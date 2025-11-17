---
layout: post
title: AI API gateway 구현
categories: ai
tags: [ai, python]
excerpt: AI API gateway 개발하면서 배운 것들을 정리한다.
---

# AI API gateway

회사에서 구현한 AI API gateway 목표는 "AI 모델을 호출하기 위한 단일 진입점을 HTTP 기반으로 제공"하는 것이다.
이를 통해 AWS, Google, MS 등 주요 클라우드 벤더사의 자격 증명 처리를 게이트웨이에서 일원화함으로써, AI 기능이 필요한 다른 프로젝트들은 인증이나 인프라 구성에 신경 쓰지 않고 오직 서비스 로직 구현에만 집중할 수 있다.

> [생성형 AI 서비스: 게이트웨이로 쉽게 시작하기](https://techblog.woowahan.com/19915/)

## 사용 기술

- Python 3.13
- Fast API 
- Langchain 0.3.14
- Locust

## 응답 형식

### Json 응답 구조

AI API Gateway는 **Global 및 Business 예외를 포함한 모든 응답에 표준화된 응답 형식**을 제공합니다.
이를 통해 클라이언트는 일관된 구조로 성공/실패 여부를 판별할 수 있습니다.

```json
{
    "success": false,
    "code": "NOT_FOUND",
    "message": "요청한 페이지를 찾을 수 없습니다.",
    "aid": "4c60ce57-145b-4406-afce-d60eca63c2d9",
    "data": null
}
```


### SSE(Server-Sent Events)

SSE는 서버가 클라이언트로 데이터를 **단반향으로 실시간 전송**할 수 있도록 하는 웹 기술이다. HTTP 프로토콜 기반으로 동작하며, `text/event-stream` MIME 타입을 사용한다.

웹소켓(WebSocket)에 비해 구현이 간단하고, 양방향 통신이 필요하지 않은 실시간 스트리밍 환경에 적합하다. 이번 프로젝트에서 Chat 응답을 실시간으로 내려주기 위해 SSE를 채택했습니다.

#### SSE 메시지 형식

1. Data only messages: `data` 필드만 포함된 단순한 메시지 구조

```shell
data: some text

data: another message
data: with two lines
```

2. Named events: `event` 필드와 `data` 필드를 함께 사용하는 구조로, 이벤트 유형을 명시할 수 있다.

```shell
event: userconnect
data: {"username": "bobby", "time": "02:33:48"}

event: usermessage
data: {"username": "bobby", "time": "02:34:11", "text": "Hi everyone."}

event: userdisconnect
data: {"username": "bobby", "time": "02:34:23"}

event: usermessage
data: {"username": "sean", "time": "02:34:36", "text": "Bye, bobby."}
```

3. Mixing and matching: 상황에 따라 위의 두 가지 방식을 혼합하여 사용

> [Using server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events#event_stream_format)

#### 프로젝트 적용 방식

이번 프로젝트에서는 Data only messages와 Named events 두 가지 형식을 사용했다.

- Data only messages: LangChain으로부터 받은 실시간 `content` 데이터를 그대로 스트리밍 전송할 때 사용
- Named events: 표준화된 [Json 응답 구조](#Json-응답-구조)에 맞춰 최종 응답을 전달할 때 사용


```python
async for chunk in response:
    if not hasattr(chunk, "content"):
        continue
    
    content = parser.parse(chunk)

    # data only messages
    if bypass:
        yield "".join(f"data: {line}\n" for line in content.splitlines()) + "\n"
        continue
    
    # named events
    elif not bypass:
        usage_metadata = getattr(chunk, "usage_metadata", None)
        response_metadata = getattr(chunk, "response_metadata", {})

        is_last = any(
            key in response_metadata for key in ["stop_reason", "finish_reason"]
        )
        yield get_named_event(content, aid, usage_metadata, is_last)

def get_named_event(
    content: str, aid: str, usage_metadata: Optional[dict], is_last: bool
) -> str:
    data = {
        "text": content,
        "success": is_last if is_last else "",
        "code": SUCCESS_CODE if is_last else "",
        "message": SUCCESS_MESSAGE if is_last else "",
        "aid": aid,
        "usage_metadata": usage_metadata,
    }
    return f"event: message\ndata: {json.dumps(data, ensure_ascii=False)}\n\n"

```


## Context 변수

`contextvars`는 파이썬 3.7 이상에서 제공되는 표준 라이브러리 모듈로, 비동기 및 동시성 환경에서 실행 컨텍스별로 격리된 상태(context-local state)를 저장하고 관리하는데 유용하다.

여기서 '컨텍스트(context)'는 다음과 같은 의미로 사용한다.
- 코드 실행 중에 사용되는 변수와 현재의 값
- 현재 실행 중인 코드의 위치, 실행 경로, 그리고 호출 스택의 상태
- 환경 구성 및 설정
- 실행 중인 코드가 액세스하는 파일, 네트워크 연결, 데이터베이스 연결 등의 리소스
- 비동기 프로그래밍에서, 각 비동기 작업(코루틴 등)의 상태 (작업이 중단되었다가 다시 시작될 때 이전 상태를 유지하는데 필요)

과거에는 `threading.local()` 같은 스레드 로컬 저장소를 사용했지만, 비동기 환경에서는 하나의 스레드에서 여러 태스크가 실행되기 때문에 값이 섞일 위험이 있었다. `contextvars`는 이러한 문제를 해결하기 위해 도입된 비동기 친화적 상태 저장 메커니즘이다.

### 프로젝트 적용 예시 — 요청/응답 Trace ID 추적

아래 예시는 `ContextVar`를 사용해 요청 단위로 `trace_id`를 관리하고, 이를 로깅 시스템에 자동으로 포함시키는 방식이다.
비동기 기반 API 서버에서는 여러 요청이 동시에 처리되기 때문에, 각 요청의 trace 정보를 섞지 않고 독립적으로 유지하는 것이 중요하다.

1. `ContextVar` 기반 trace_id 선언 및 로거 설정

```python
import logging
import os
from contextvars import ContextVar
from logging import Logger
from logging.handlers import RotatingFileHandler

trace_id_var: ContextVar[str] = ContextVar("trace_id", default="N/A")
MAX_LOG_SIZE = 5 * 1024 * 1024  # 5MB
BACKUP_COUNT = 5


class CustomFormatter(logging.Formatter):

    def format(self, record):
        if not hasattr(record, "trace_id"):
            record.trace_id = "N/A"
        return super().format(record)


class ContextFilter(logging.Filter):
    def filter(self, record: logging.LogRecord):
        record.trace_id = str(trace_id_var.get())
        return True


def log_directory(log_dir):
    try:
        os.makedirs(log_dir)
    except FileExistsError:
        pass


def setup_logger(name: str = "app") -> Logger:
    logger = logging.getLogger(name)
    logger.setLevel(os.getenv("LOG_LEVEL", "INFO").upper())

    # 로그 파일 경로
    base_dir = os.path.dirname(
        os.path.dirname(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))
    )
    log_dir = os.path.join(base_dir, "log")
    log_file = os.path.join(log_dir, "app.log")

    # 디렉토리 생성
    log_directory(log_dir)

    log_format = "%(asctime)s %(name)s %(levelname)s [%(process)d] [%(threadName)s] [trace_id=%(trace_id)s] %(message)s"

    console_handler = logging.StreamHandler()
    console_handler.setFormatter(CustomFormatter(log_format))

    file_handler = RotatingFileHandler(
        log_file, maxBytes=MAX_LOG_SIZE, backupCount=BACKUP_COUNT
    )
    file_handler.setFormatter(CustomFormatter(log_format))

    logger.addHandler(console_handler)
    logger.addHandler(file_handler)
    logger.addFilter(ContextFilter())

    return logger


logger = setup_logger()

```

2. 요청 단위 Trace ID 생성 및 ContextVar 저장

```python
from app.config.logging.config import trace_id_var  # 위의 파일

# 미들웨어에서 trace_id 생성해 ContextVar에 저장하는 코드
trace_id = str(uuid.uuid4())
scope["trace_id"] = trace_id
trace_id_var.set(trace_id)

```

이렇게 설정하면, 같은 스레드나 이벤트 루프에서 여러 요청이 동시에 처리되더라도 `ContextVar`는 각 요청별 trace_id를 안전하게 유지하여 로그에 자동으로 포함시킬 수 있다.

## ASGI 미들웨어 구현

### 미들웨어(middleware)


HTTP 요청과 응답이 처리되기 전에 실행되는 함수를 말한다. 즉, HTTP 요청이 엔드포인트 함수로 전달되기 전이나, 응답이 외부로 반환되기 전에 실행된다. 예를 들면 다음과 같은 것들이다.

- 요청 데이터 처리
- 요청/응답 데이터 형식화
- 인증/인가
- 로깅 및 모니터링
- 캐싱
- 오류 처리
- API 속도 제한

FastAPI는 기본적으로 다음과 같은 미들웨어를 제공한다.

- CORSMiddleware
- GzipMiddleware: HTTP 응답을 Gzip으로 압축한다.
- HTTPSRedirectMiddleware: HTTP 요청을 HTTPS로 리디렉션한다.
- TrustedHostMiddleware: 특정 호스트에서만 요청을 허용한다.

그 외에도 다른 ASGI 미들웨어나 스탈렛이 제공하는 미들웨어를 사용할 수 있다.

> 주의사항: 주입된 의존성이 `yield`를 사용한다면 미들웨어 이후에 종료코드가 실행된다. 예를 들어 데이터베이스 연결을 닫거나 자원을 정리하는 코드가 있다면, 이 코드는 미들웨어가 수행된 후 실행된다.

`BackgroundTasks` 는 모든 미들웨어가 수행된 이후에 실행된다.


제한없는 기능 구현을 위해 ASGI 미들웨어 구현

> [Pure ASGI Middleware](https://www.starlette.dev/middleware/#pure-asgi-middleware)



## Langchain


#### Streaming 컨셉

완전한 응답이 준비되기 전이라도 점진적으로 응답 출력(실시간 응답 출력)
사용자의 대기 시간을 축소

#### Streaming APIs

stream: 동기
astream: 비동기

#### 결론

응답 시간 축소 및 처리량 증가를 위해 astream api를 사용해 비동기로 작동하도록 구현

## 성능 테스트

Locust를 이용한 라운드 트립 응답속도 테스트

- AWS bedrock (private link를 통해 통신, 네트워크 홉 발생 X)
- MS Azure open ai (송도 IDC 거치므로 네트워크 홉 증가)
- 서버 EC2 instance (m7i.xlarge, k8s pod 2개)
- 동시 접속 처리량이 10명도 안되서
- stream api는 비동기 처리하도록 langcahin의 astream 함수사용
- 하지만 cps 마다 quotas(할당량 존재)
https://learn.microsoft.com/ko-kr/azure/ai-foundry/openai/quotas-limits?tabs=REST#gpt-4o-standard

최종: 분당 요청수 1900, 분당 토큰 350,0000 처리 가능 



