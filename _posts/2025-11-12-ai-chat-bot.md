---
layout: post
title: AI API gateway 구현
categories: ai
tags: [ai, python]
excerpt: AI API gateway 개발하면서 배운 것들을 정리한다.
---

# AI API gateway 란

회사에서 AI API gateway를 구현하면서 배운 것들을 정리하려고 한다. 구현한 AI API gateway 목표는 "AI 모델을 호출하기 위한 단일 진입점을 HTTP 기반으로 제공"하는 것이다. 이를 통해 AWS, Google, MS 등 주요 클라우드 벤더사의 자격 증명 처리를 게이트웨이에서 일원화함으로써, AI 기능이 필요한 다른 프로젝트들은 인증이나 인프라 구성에 신경 쓰지 않고 오직 서비스 로직 구현에만 집중할 수 있다.

> [생성형 AI 서비스: 게이트웨이로 쉽게 시작하기](https://techblog.woowahan.com/19915/)

## 사용 기술

- Python 3.13
- Fast API 
- Langchain 0.3.14
- Locust

## 응답 형식

### Json 응답 구조

AI API Gateway는 **Global 및 Business 예외를 포함한 모든 응답에 표준화된 응답 형식**을 제공한다. 이를 통해 클라이언트는 일관된 구조로 성공/실패 여부를 판별할 수 있다.

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

웹소켓(WebSocket)에 비해 구현이 간단하고, 양방향 통신이 필요하지 않은 실시간 스트리밍 환경에 적합하다. 이번 프로젝트에서 AI Chat 응답을 실시간으로 내려주기 위해 SSE를 채택했다.

#### SSE 메시지 형식

- Data only messages: `data` 필드만 포함된 단순한 메시지 구조

```shell
data: some text

data: another message
data: with two lines
```

- Named events: `event` 필드와 `data` 필드를 함께 사용하는 구조로, `event` 필드를 사용해 이벤트 유형을 명시할 수 있다.

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

- Mixing and matching: 상황에 따라 위의 두 가지 방식을 혼합하여 사용

> [Using server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events#event_stream_format)

#### 프로젝트 적용 방식

이번 프로젝트에서는 Data only messages와 Named events 두 가지 형식을 사용했다.

- Data only messages: LangChain으로부터 받은 실시간 `content` 데이터를 그대로 스트리밍 전송할 때 사용
- Named events: 표준화된 [Json 응답 구조](#json-응답-구조)에 맞춰 최종 응답을 전달할 때 사용


```python

async def completions():
    # ...
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
비동기 기반 API 서버에서는 여러 요청이 동시에 처리되기 때문에, 각 요청의 정보를 섞지 않고 독립적으로 유지하는 것이 중요하다.

#### `ContextVar` 기반 trace_id 선언 및 로거 설정

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

#### 요청 단위 Trace ID 생성 및 ContextVar 저장

```python
from app.config.logging.config import trace_id_var  # 위의 파일

# 미들웨어에서 trace_id 생성해 ContextVar에 저장하는 코드
trace_id = str(uuid.uuid4())
scope["trace_id"] = trace_id
trace_id_var.set(trace_id)

```

이렇게 설정하면, 같은 스레드나 이벤트 루프에서 여러 요청이 동시에 처리되더라도 `ContextVar`는 각 요청별 `trace_id`를 안전하게 유지하여 로그에 자동으로 포함시킬 수 있다.

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


### 미들웨어 주의사항

- 주입된 의존성(`Dependencies`)이 `yield`를 사용한다면 미들웨어 이후에 종료코드가 실행된다. 예를 들어 데이터베이스 연결을 닫거나 자원을 정리하는 코드가 있다면, 이 코드는 미들웨어가 수행된 후 실행된다.
- `BackgroundTasks` 는 모든 미들웨어가 수행된 이후에 실행된다.

> [Dependencies with yield](https://fastapi.tiangolo.com/tutorial/dependencies/dependencies-with-yield/)

> [Background Tasks](https://fastapi.tiangolo.com/tutorial/background-tasks/#background-tasks)

```scss

        ┌───────────────────────────────────────────┐
        │                 HTTP Request              │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │            [ASGI Middleware 1]            │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │            [ASGI Middleware 2]            │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │        FastAPI Routing & Endpoint         │
        │     - Dependencies (yield 이전 실행)        │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │       Response 객체 생성 (Response)         │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │         [ASGI Middleware 2 종료]           │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │         [ASGI Middleware 1 종료]           │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │            Response 전송 완료              │
        └───────────────────────────┬───────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │     Dependency cleanup (yield 이후 실행)    │
        └───────────────────────────────────────────┘
                                    ▼
        ┌───────────────────────────────────────────┐
        │            BackgroundTasks 실행            │
        └───────────────────────────────────────────┘

```


### 고급 기능 구현을 위한 ASGI 미들웨어

Starlette의 `Pure ASGI Middleware` 패턴을 사용하면, 프레임워크에 종속되지 않고 **ASGI 프로토콜 레벨에서 요청/응답 메시지를 직접 처리**할 수 있다.

또한, body 가로채기 등 고급 기능을 자유롭게 구현할 수 있어 ASGI 미들웨어를 사용했다.

> [Pure ASGI Middleware](https://www.starlette.dev/middleware/#pure-asgi-middleware)


#### 프로젝트 적용 방식

아래 코드는 Access Log를 남기기 위해 직접 구현한 ASGI 미들웨어 예시이다.

- 특정 경로/정적 파일은 로깅 제외
- request body 로깅
- response body 로깅
- trace_id 생성 및 헤더 주입
- 수행 시간 측정 

```python
import time
import uuid
from urllib.parse import unquote

from starlette.datastructures import MutableHeaders
from starlette.requests import Request
from starlette.types import ASGIApp, Message, Receive, Scope, Send

from app.config.logging.config import logger, trace_id_var


class AccessLoggerMiddleware:
    def __init__(self, app: ASGIApp) -> None:
        self.app = app
        self.excluded_paths = [
            "/docs",
            "/swagger-ui",
            "/swagger-ui-bundle",
            "/openapi.json",
            "/web",
        ]
        self.excluded_prefixes = ["/swagger-ui"]
        self.excluded_extensions = (".js", ".css", ".png", ".svg", ".ico", ".map")

    def _is_excluded(self, path: str) -> bool:
        return (
            path in self.excluded_paths
            or any(path.startswith(prefix) for prefix in self.excluded_prefixes)
            or path.endswith(self.excluded_extensions)
        )

    async def __call__(self, scope: Scope, receive: Receive, send: Send):
        if scope["type"] != "http":
            await self.app(scope, receive, send)
            return

        trace_id = str(uuid.uuid4())
        scope["trace_id"] = trace_id
        trace_id_var.set(trace_id)

        path = scope.get("path")
        if self._is_excluded(path):
            await self.app(scope, receive, send)
            return

        request = Request(scope)
        start_time = time.perf_counter()

        async def wrapped_receive() -> Message:
            message = await receive()
            if message["type"] == "http.request":
                body = message.get("body", b"")
                try:
                    payload = body.decode("utf-8", errors="replace")
                except Exception:
                    payload = "<decode error>"

                logger.info(
                    f"Request: {request.method} {path} {unquote(str(request.query_params))} "
                    f"Payload={payload}"
                )
            return message

        async def wrapped_send(message: Message) -> None:
            aid = request.scope.get("trace_id")
            if message["type"] == "http.response.start":
                headers = MutableHeaders(scope=message)
                headers.append("X-Trace-ID", aid)

                status_code = message.get("status", 0)
                logger.info(f"Response: {status_code} {message.get('headers')} ")
            elif message["type"] == "http.response.body":
                body = message.get("body", b"") or b""
                try:
                    decoded_body = body.decode("utf-8", errors="replace")
                except Exception:
                    decoded_body = "<decode error>"

                elapsed = time.perf_counter() - start_time
                logger.info(f"Elapsed Time: {elapsed: .4f}s")
                logger.info(f"Response Body={decoded_body}")
            await send(message)

        await self.app(scope, wrapped_receive, wrapped_send)

```

## Langchain

### Streaming 컨셉

LangChain의 Streaming은 모델의 전체 응답이 생성되기 전에 토큰 단위로 결과를 순차적으로 전달받는 방식이다. 다음과 같은 장점이 있다.

- 완전한 응답이 준비되기 전이라도 점진적으로 응답 출력(실시간 응답 출력)
- 사용자의 체감 대기 시간 감소

`stream`, `astream`을 통해 스트리밍 방식으로 모델의 토큰을 소비할 수 있다.

### Streaming APIs

- stream: 동기 방식으로 토큰 스트림을 제공
- astream: 비동기 방식으로 토큰 스트림을 제공 

### 결론

`astream` 사용 시 비동기 처리로 더 높은 처리량을 달성할 수 있다. 동시 접속자 증가 또는 응답 지연 문제를 해결하기 위해서 비동기 스트리밍(`astream`) 방식으로 전환했다.

## 성능 테스트

### 테스트 환경

성능(라운드 트립 응답속도, 처리량 등)은 Locust를 사용해 측정했다.

- AWS Bedrock 
    - PrivateLink로 통신 → 네트워크 홉 없음, 안정적 지연 시간
- MS Azure OpenAI
    - 회사 IDC를 거쳐야 하므로 네트워크 홉 증가 → 지연 시간 다소 증가 가능
- 서버 인프라
    - EC2 m7i.xlarge
    - Kubernetes POD 2개로 서비스 구성

### 튜닝 포인트 

테스트 결과, 동시 접속자가 10명을 넘는 지점에서 LangChain `stream` 함수는 병렬성이 부족해 병목이 발생했다. 이를 해결하기 위해 기존의 **동기 스트리밍(`stream`)에서 비동기 스트리밍(astream)으로 전환**했다.

하지만 모델별로 [quotas(할당량)](https://learn.microsoft.com/ko-kr/azure/ai-foundry/openai/quotas-limits?tabs=REST#gpt-4o-standard)이 존재 하기 때문에, 비동기로 처리량을 늘려도 할당량을 초과하면 더 이상 수용되지 않는 구조다.

최종적으로 시스템은 다음 수준의 처리량을 안정적으로 달성했다. (초당 100 유저)
- 분당 약 1,000 요청 처리
- 분당 약 200,000 토큰 처리 


