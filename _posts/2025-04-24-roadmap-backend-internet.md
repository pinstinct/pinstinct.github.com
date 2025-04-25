---
layout: post
title: Backend Roadmap - Internet
categories: backend
tags: [backend, internet, network]
excerpt: https://roadmap.sh/backend를 따라 학습한 내용을 정리합니다.
---

[Backend Developer Roadmap](https://roadmap.sh/backend) 포스트를 읽고 정리한 내용입니다.


# Internet

request webpage -> send a data packet -> Internet Service Provider(ISP) -> DNS sever -> IP address -> (routers and switches) -> destination sever -> send back the response

## How does the Internet Work?

### [How does the Internet Work?](https://cs.fyi/guide/how-does-internet-work)

#### Introduction to the Internet

인터넷이 무엇인지 배우기 전에, 네트워크가 무엇인지 이해해야 한다. 네트워크는 서로 연결된 컴퓨터들 또는 다른 장치들의 집합이다. 예를 들어, 당신의 집에 컴퓨터들과 장치들의 네트워크가 있다. 당신의 이웃도 비슷하게 기계들의 네트워크가 있다. 이 모든 네트워크들이 서로 연결되면 인터넷을 만들다.

#### How the Internet Works: An Overview

프로토콜은 장치들 사이에서 어떻게 정보를 교환할지 정의하고, 데이가 안정적이고 안전하게 전송되도록 보장한다. 

인터넷의 핵심은 서로 연결된 라우터들로 구성된 네트워크로, 서로 다른 장치들과 시스템들 간의 트래픽을 전달하는 역할을 한다. 인터넷을 통해 데이터를 전송할 때, 데이터는 작은 패킷으로 분할되어 장치에서 라우터로 전송된다. 라우터는 패킷을 검사하여 목적지로 향하는 경로에 있는 다음 라우터로 전달한다. 이 과정은 패킷이 최종 목적지에 도착할 때까지 계속된다,

패킷이 올바르게 송수신되도록 인터넷은 인터넷 프로토콜(IP)과 전송 제어 프로토콜(TCP)을 포함한 다양한 프로토콜을 사용한다. IP는 패킷을 올바른 목적지로 라우팅하는 역할을 하고, TCP는 패킷이 안정적으로 올바른 순서로 전송되도록 보장한다.

#### Basic Concepts and Terminology

- Packet: 인터넷을 통해 전송되는 작은 데이터 단위
- Router: 서로 다른 네트워크 사이에서 데이터 패킷들을 전송하는 장치
- IP Address: 네트워크의 각 장치에 할당된 고유 식별자로, 데이터를 올바른 목적지로 라우팅하는데 사용
- Domain Name: google.com과 같이 웹 사이트를 식별하는데 사용하는 사람이 읽을 수 있는 이름
- DNS(Domain Name System): 도메인 이름을 IP 주소로 변환하는 역할을 하는 시스템 
- HTTP(Hypertext Transfer Protocol): 클라이언트와 서버 간에 데이터를 전송하는데 사용
- HTTPS: 클라이언트와 서버 간의 안전한 통신을 제공하는데 사용
- SSL(Secure Sockets Layer)/TLS(Transport Layer Security): 인터넷을 통해 안전한 통신을 제공하는데 사용

#### The Role of Protocols in Internet

IP는 데이터 패킷을 올바른 목적지로 라우팅하는 역할을 하며, TCP와 UDP는 패킷을 안정적이고 효율적으로 전송되도록 보장한다. DNS는 도메인 이름을 IP 주소로 변환하는 데 사용되며, HTTP는 클라이언트와 서버 간의 데이터 전송에 사용된다.

#### Introduction to HTTP and HTTPS

HTTPS는 HTTP의 보안성이 강화된 버전으로 클라이언트와 서버 간에 전송 되는 데이터를 SSL/TLS 암호화를 사용하여 암호화한다.

#### Building Applications with TCP/IP

TCP/IP는 대부분의 인터넷 기반 애플리케이션과 서비스에 사용되는 기본 통신 프로토콜이다. 서로 다른 장치들에서 실행되는 애플리케이션 간에 안정적이고, 순서에 맞고 오류 검사를 한 데이터를 전달한다.

TCP/IP를 사용하여 애플리케이션을 개발할 때, 이해해야 할 몇 가지 핵심 개념
- Ports: 장치에서 실행되는 애플리케이션이나 서비스를 식별하는데 사용한다. 각 애플리케이션이나 서비스는 고유한 포트 번호가 할당되어 데이터를 올바른 목적지로 전송할 수 있다.
- Sockets: 소켓은 IP 주소와 포트 번호의 조합으로 통신을 위한 특정 endpoint를 나타낸다.
- Connections: 두 장치가 서로 통신하려고 할 때 두 소켓의 연결이 성립된다. 연결이 성립되는 동안, 장치들은 최대 세그먼트 사이즈와 윈도우 사이즈와 같은 다양한 파라미터를 협상한다. 연결을 통해 데이터가 전송되는 방식을 결정한다.
- Data transfer: 연결이 성립되면 각 장치에서 실행 중인 애플리케이션 간에 데이터를 전송할 수 있다. 데이터는 일반적으로 세그먼트 단위로 전송되며, 각 세그먼트에는 안정적인 전송을 위한 시퀀스 번호와 기타 메타데이터가 포함된다.

#### Securing Internet Communication with SSL/TLS

- Certificates: SSL/TLS 인증서는 클라이언트와 서버 간의 신뢰를 구축하는 데 사용한다. 이 인증서에는 서버의 신원 정보가 포함되어 있으며, 신뢰할 수 있는 제 3자(인증 기관)에서 서명하여 진위 여부를 검증한다.
- Handshake: SSL/TLS 핸드셰이크가 진행되는 동안 클라이언트와 서버는 보안 연결을 위한 암호화 알고리즘 및 기타 매개변수를 협상하기 위해 정보를 교환한다.
- Encryption: 보안 연결이 설정되면 합의된 알고리즘을 사용하여 데이터가 암호화되고 클라이언트와 서버 간에 안전하게 전송될 수 있다.

### [How does the Internet Work?](https://web.stanford.edu/class/msande91si/www-spr04/readings/week1/InternetWhitepaper.htm)

#### Where to Begin? Internet Addresses

인터넷 서비스 제공업체(IPS)를 통해 인터넷에 연결하는 경우, 일반적으로 전화 접속 세션 동안 임시 IP 주소가 할당된다. 근거리 통신만(LAN)을 통해 인터넷에 연결하는 경우, 컴퓨터에 영구 IP 주소가 할당되거나 DHCP(동적 호스트 구성 프로토콜) 서버에서 임시 IP 주소를 받을 수 있다. 어 떤 경우든 인터넷에 연결되어 있으면 컴퓨터에 고유한 IP 주소가 부여된다.

```sh
ping www.yahoo.com
```

#### Protocol Stacks and Packets

모든 컴퓨터는 인터넷에서 통신하기 위해 프로토콜 스택이 필요하며, 일반적으로 컴퓨터 운영 체제(예: Windows, Unix 등)에 내장되어 있다. 인터넷에서 사용되는 프로토콜 스택은 TCP/IP 프로토콜 스택이다.

- Application Protocols Layer: WWW, e-mail, FTP와 같이 애플리케이션에 특화된 프로토콜
- Transmission Control Protocol Layer: TCP는 포트 번호를 사용하여 패킷을 컴퓨터의 특정 애플리케이션으로 전송
- Internet Protocol Layer: IP 주소를 사용하여 패킷을 특정 컴퓨터로 전송
- Hardware Layer: 이진 패킷 데이터를 네트워크 신호로 변환하고, 반대로도 변환 (예: 이더넷 네트워크 카드, 전화선용 모뎀 등)

![](/image/protocol-stack-and-packet.gif)

1. 전송할 메시지가 길면, 메시지가 통과하는 각 스택 계층이 메시지를 더 작은 데이터 청크로 나누는데 이러한 데이터 청크를 **패킷**이라고 한다.
2. 패킷은 애플리케이션 계층을 거쳐 TCP 계층으로 전달된다. 각 패킷에는 포트 번호가 할당된다.
3. TCP 계층을 거친 패킷은 IP 계층으로 이동한다.
4. 하드웨어 계층은 메시지가 포함된 패킷을 전자 신호로 변환하여 전화선을 통해 전송한다.
5. 전화선 반대편에는 ISP가 인터넷에 직접 연결되어 있다. ISP **라우터**는 각 패킷의 목적지 주소를 검사하여 어디로 보낼지 결정한다.
6. 수많은 라우터를 거쳐 패킷은 목적지에 도달한다. 패킷이 스택을 따라 올라가면서 라우팅 데이터(예: IP 주소 및 포트 번호)가 패킷에서 제거된다. 스택의 맨 위에 도달하면 패킷은 원래 형태로 다시 조립된다.

#### Networking Infrastructure

![](/image/networking-infra.gif)

ISP는 전화 접속 고객을 위해 모뎀 풀을 관리한다. 이 풀은 백본 또는 전용 회선 라우터로 데이터 흐름을 제어하는 특정 형태의 컴퓨터(일반적으로 전용 컴퓨터)에 의해 관리된다. 이러한 설정은 네트워크 접속을 '제공'하므로 포트 서버라고 한다. 요금 및 사용량 정보도 일반적으로 이 풀에서 수집된다.

패킷은 전화망과 ISP의 로컬 장비를 거친 후, ISP의 백본망으로 라우팅된다. 패킷은 일반적으로 여러 라우터와 백본망, 전용선 그리고 기타 네트워크를 거쳐 목적지에 도착한다.

```sh
traceroute www.yahoo.com
```

#### Internet Infrastructure

인터넷 백본은 서로 상호 연결된 수많은 대규모 네트워크로 구성된다. 이러한 대규모 네트워크를 Network Service Provider(NSP)라고 한다. 이러한 네트워크는 서로 피어링하여 패킷 트래픽을 교환한다. 각 NSP는 세 개의 Network Access Point(NAP)에 연결해야 한다. NAP에서 패킷 트래픽은 한 NSP의 백본에서 다른 NSP의 백본으로 이동할 수 있다. NSP 또는 Metropolitan Area Exchanges(MAE)에도 상호 연결된다. MAE는 NAP와 동일한 목적을 수행하지만 개인 소유이다. 

![](/image/internet-infra.gif)

#### The Internet Routing Hierarchy

어떤 컴퓨터도 다른 컴퓨터의 위치를 알지 못하며, 패킷이 모든 컴퓨터로 전송되는 것도 아니다. 패킷을 목적지까지 전달하는 데 사용되는 정보는 인터넷에 연결된 각 라우터가 관리하는 라우팅 테이블에 저장된다.

**라우터는 패킷 스위치이다.** 라우터는 일반적으로 네트워크 간에 연결되어 패킷을 라우팅한다. 각 라우터는 자신의 하위 네트워크와 사용하는 IP 주소를 알고 있다.


![](/image/internet-routing-hierarchy.gif)

패킷이 라우터에 도착하면 라우터는 발신 컴퓨터의 IP 프로토콜 계층에서 지정한 IP 주소를 검사한다. 라우터는 라우팅 테이블을 확인해 IP 주소가 포함된 네트워크가 발견되면 해당 네트워크로 전성한다. 발견되지 않으면 라우터는 기본 경로(일반적으로 백본 계층을 따라 다음 라우터까지)를 통해 패킷을 전송한다. 다음 라우터가 패킷을 어디로 보내야 할지 모른다면, 패킷은 NSP 백본에 도달할 때까지 위쪽으로 라우팅된다. NSP 백본에 연결된 라우터는 가장 큰 라우팅 테이블을 보유하고 있으며, 여기서 패킷은 올바른 백본으로 라우팅되고, 그곳에서 점점 더 작은 네트워크를 통해 '아래쪽'으로 이동을 시작하여 목적지를 찾는다.

#### Application Protocols: HTTP(Hypertext Transfer Protocol) and the World Wide Web

인터넷에서 가장 널리 사용되는 서비스 중 하나는 월드 와이드 웹(WWW)이다. 웹을 작동시키는 애플리케이션 프로토콜은 **HTTP**이다. HTTP는 웹 브라우저와 웹 서버가 인터넷을 통해 서로 통신하는 데 사용하는 프로토콜이다. 

HTTP는 연결 없는 텍스트 기반 프로토콜이다. 서버가 요청을 처리하면 클라이언트와 서버 간의 연결이 끊어진다. 각 요청마다 새로운 연결을 설정해야 한다.

웹 브라우저에 URL을 입력하면 다음과 같은 일이 발생한다.
1. URL에 도메인 이름이 포함되어 있으면 브라우저는 먼저 DNS에 연결하여 해당 웹 서버의 IP 주소를 검색한다.
2. 웹 브라우저는 웹 서버에 연결하여 원하는 웹 페이지에 대한 HTTP 요청을 보낸다.
3. 웹 서버는 요청을 수신하고 원하는 페이지를 확인한다. 존재하면 해당 페이지를 전송하고, 없으면 HTTP 404 오류 메시지를 전송한다.
4. 웹 브라우저가 페이지를 받고 연결이 끊어진다.
5. 그런 다음 부라우저는 페이지를 분석하여 웹 페이지를 완성하는 데 필요한 다른 페이지 요소를 찾는다.
6. 브라우저는 필요한 각 요소에 대해 추가 연결과 HTTP 요청을 만든다.

#### Transmission Control Protocol

**TCP는 연결 지향적이고 안정적인 바이트 스트림 서비스이다.** 연결 지향이란 TCP를 사용하는 두 애플리케이션이 데이터를 교환하기 전에 먼저 연결을 설정해야 함을 의미한다. 

#### Internet Protocol

TCP와 달리 **IP는 신뢰할 수 없는 비연결형 프로토콜**이다. IP는 패킷이 목적지에 도착하는지 여부에 신경 쓰지 않는다. **IP 역할은 패킷을 다른 컴퓨터로 보내고 라우팅하는 것이다.** IP 패킷은 순서가 어긋나거나 전혀 도착하지 않을 수 있다. TCP 역할은 패킷이 올바른 순서로 도착하는지 확인하는 것이다.


## What is HTTP?

HTTP는 메시지의 형식과 전송 방식 그리고 웹 서버와 브라우저가 다양한 명령에 응답하는 방식을 정의한다. 클라이언트가 웹 페이지나 파일과 같은 리소스에 대한 HTTP 요청을 서버에 보내면 서버는 요청된 콘텐츠와 요청 결과를 나타내는 HTTP 상태 코드로 응답한다. HTTP는 상태 비저장(stateless) 프로토콜로, 클라이언트에서 서버로 보내는 각 요청은 독립적이며 이전 상호작용에 대한 정보를 유지하지 않습니다.

### [What is HTTP?](https://www.cloudflare.com/en-gb/learning/ddos/glossary/hypertext-transfer-protocol-http/)

#### What is in an HTTP request?

일반적인 HTTP 요청에는 다음이 포함된다.

- HTTP versio type
- a URL
- an HTTP method
- HTTP request headers
- Optional HTTP body

#### What is in an HTTP response?

일반적인 HTTP 응답에는 다음이 포함된다.

- an HTTP status code
  - 1xx Informational
  - 2xx Success
  - 3xx Redirection
  - 4xx Client Error
  - 5xx Server Error
- HTTP response headers
- Optional HTTP body

#### Can DDoS(Distributed Denial of Service) attacks be launched over HTTP?

> [DDoS 공격이란?](https://www.cloudflare.com/ko-kr/learning/ddos/what-is-a-ddos-attack/)

인터넷 트래픽 폭주로 공격 대상이나 그 주변 인프라를 압도하여 공격 대상 서버나 서비스, 네트워크의 정상적인 트래픽을 방해하는 악의적인 시도를 말한다. 

공격자는 맬웨어를 이용하여 감염된 컴퓨터 및 기타 기기들을 원격으로 제어할 수 있다. 제어되는 개별 기기는 봇(bot, 또는 좀비)이라고 지칭되며 봇으로 이루어진 그룹은 봇넷(botnet)이라고 부른다. 봇넷이 피해자의 서버나 네트워크를 공격 대상으로 삼으면 각 봇은 대상의 IP 주소로 요청을 보내고 이는 해당 서버나 네트워크에 잠재적 과부하를 일으켜 정상적인 트래픽에 대한 서비스 거부(denial-of-service)로 이어진다. 각 봇은 합법적인 장치이기 때문에 공격 트랙픽을 정상적인 트래픽과 분리하는 것이 어려울 수 있다.

- DDos 공격의 일반적인 유형
  - 애플리케이션 계층(7계층) 공격
    - 대상의 리소스를 소진함으로써 서비스 거부 상태를 만드는 것
    - 한 번에 많은 컴퓨터에서 웹 브라우저의 새로 고침을 누르는 것과 유사하며 많은 수의 HTTP 요청이 서버를 폭주시켜 서비스 거부를 초래
  - 프로토콜 공격
    - 상태 고갈 공격이라고도 하며 네트워크 장비 리소스를 과도하게 소비하여 서비스 중단을 유발한다. 3계층과 4계층에 존재하는 약점을 활용한다.
    - SYN 폭주
- DDoS 공격을 완화하기 위한 프로세스
  - 레이트 리미팅(Rate limiting): 서버가 특정 시간대에 수락할 요청 수를 제한하는 것 
  - 웹 애플리케이션 방화벽(WAF): 7계층 DDos 공격을 완하는데 도움을 줄 수 있는 도구이다. 인터넷과 서버 사이에 WAF를 배치하면 WAF가 리버스 프록시 역할을 수행하므로 특정 유횽의 악의적 트래픽으로부터 서버를 보호할 수 있다. 


> [리버스 프록시란?](https://www.cloudflare.com/ko-kr/learning/cdn/glossary/reverse-proxy/)

- 리버스 프록시는 일반적으로 보안, 성능, 안정성을 향상시키기 위해 구현된다.

![](/image/forward-proxy-flow.png)

- 프록시 서버: 클라이언트가 웹 서버에 요청하면 프록시 서버가 요청을 가로채 중개자처럼 해당 클라이언트를 대신해 웹 서버와 통신한다.
- 사용하는 이유
  - 검색 제한을 피하기 위해: 프록시를 사용하면 사용자가 방문하는 사이트에 직접 연결하지 않고 프록시에 연결할 수 잇으므로 이러한 제한을 피할 수 있다.
  - 특정 콘텐츠에 대한 액세스 차단: 반대로 사용자 그룹이 특정 사이트에 엑세스 하는 것을 차단할 수도 있다.
  - 온라인에서 자신의 신원을 보호하기 위해

![](/image/reverse-proxy-flow.png)

- 리버스 프록시: 하나 이상의 웹 서버 앞에 위치하여 클라이언트의 요청을 가로채는 서버이다. 정방향 프록지는 클라이언트 앞에 위치하며 원본 서버가 해당 특정 클라이언트와 직접 통신하지 못하도록 하는 것이다. 반면에 리버스 프록시는 원본 서버 앞에 위치하여 어떤 클라이언트도 원본 서버와 직접 통신하지 못하도록 한다.
- 사용하는 이유
  - 부하 분산: 트래픽을 여러 서버에 고르게 분산하는 부하 분산 솔루션을 제공할 수 있다. (하지만, 부하 분산은 로드밸런서로 하는게 좋음)
  - 공격으로부터 보호: 원본 서버의 IP 주소를 공개할 필요가 없다. 
  - 전역 서버 부하 분산: 클라이언트를 지리적으로 가장 가까운 서버로 보낸다. 그러면 요청과 응답이 이동해야 하는 거리가 줄어들어 로드 시간이 최소화된다.
  - 캐싱: 리버스 프록시도 콘텐츠를 캐시할 수 있으므로 성능이 향상된다.
  - SSL 암호화: 들어오는 모든 요청을 해독하고 나가는 모든 응답을 암호화하여 원본 서버의 리소스를 확보


### [Overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)

![](/image/http-layers.svg)

#### Components of HTTP-based systems

HTTP는 클라이언트-서버 프로토콜이다.

- 프록시: 애플리케이션 계층에서 작동하는 기기는 일반적으로 프록시라고 한다.
  - 프록시가 수행 가능한 기능: 캐싱, 필터링, 부하 분산, 인증, 로깅

#### Basic aspects of HTTP

- HTTP는 간단하다.
- HTTP는 확장가능하다.
- HTTP는 상태 비저장(stateless)이지만 세션 비저장은 아니다. 
  - HTTP 쿠키는 상태 저장 세션을 사용할 수 있다. 헤더 확장성을 활용하여 HTTP 쿠키가 워크플로에 추가되어 각 HTTP 요청에서 동일한 컨텍스트 또는 상태를 공유하는 세션을 생성할 수 있다.
- HTTP 및 연결(Connection)
  - 연결은 전송 계층에서 제어되므로 근본적으로 HTTP의 범위를 벗어난다. HTTP는 기본 전송 프로토콜이 연결 기반일 것을 요구하지 않습니다. 단지 신뢰할 수 있거나 메시지 손실이 없는 것만 요구한다. HTTP는 연결 기반인 TCP 표준을 사용한다.
  - HTTP/1.0의 기본 동작은 각 HTTP 요청/응답 쌍에 대해 별도의 TCP 연결을 여는 것이다. 이는 여러 요청이 연달아 전송될 때 단일 TCP 연결을 공유하는 것보다 효율적이지 않다.
  - 이러한 결함을 완화하기 위해 HTTP/1.1은 파이프라이닝과 지속 연결을 도입했다. HTTP/2는 한 걸음 더 나아가 단일 연결을 통해 메시지를 다중화하여 연결의 안정성과 효율성을 높였다. 

#### HTTP flow

1. TCP 연결 열기
2. HTTP 메시지 전송: HTTP 메시지는 사람이 읽을 수 있다. HTTP/2에서 메시지가 프레임으로 캡슐화되어 직접 읽을 수 없지만, 원리는 동일하다.
3. 서버에서 응답 보내기
4. 추가 요청을 위해 연결을 닫거나 재사용

HTTP 파이프라이닝을 활성화하면 첫 번째 응답이 완전히 수신될 때까지 기다리지 않고 여러 요청을 전송할 수 있다.

#### APIs based on HTTP

HTTP 기반에서 가장 일반적으로 사용되는 API는 `Fetch API`로 자바스크립트에서 HTTP 요청을 하는 데 사용할 수 있다. 또 다른 API인 서버 전송 이벤트(Server-Sent Events)는 서버가 HTTP를 전송 메커니즘으로 사용하여 클라이언트에 이벤트를 전송할 수 있도록 하는 단방향 서비스이다. 클라이언트는 `EventSource` 인터페이스를 사용하여 연결하고 이벤트 핸드러를 설정한다.


### [HTTP/3 From A To Z: Core Concepts](https://www.smashingmagazine.com/2021/08/http3-core-concepts-part1/)

## What is Domain Name?

### [What is a Domain Name?](https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Web_mechanics/What_is_a_domain_name)

#### Structure of domain names

![](/image/domain-name-structure.png)

- TLD(Top-Level Domain): 최상위 도메인
- 라벨(또는 구성요소)

```sh
whois mozilla.org
```

### [What is a Domain Name? | Domain name vs. URL](https://www.cloudflare.com/en-gb/learning/dns/glossary/what-is-a-domain-name/)

#### What is the difference between a domain name and a URL?

URI(Uniform Resource Locator)에는 사이트의 도메인 이름뿐 아니라 프로토콜과 경로를 포함한 기타 정보가 포함된다. 예를 들어, URL `https://cloudflare.com/learning/`에서 `cloudflare.com`은 도메인 이름이고, `https`는 프로토콜이며, `/learning/`은 웹사이트의 특정 페이지로 가는 경로이다.


## Browsers and how they work?

웹 브라우저는 사용자가 월드 와이드 웹(WWW)의 정보에 접근하고, 검색하고, 탐색할 수 있도록 하는 소프트웨 애플리케이션이다. HTML, CSS, JavaScript를 해석하고 표시하여 웹 페이지를 렌더링한다. 웹 콘텐츠를 처리하는 렌더링 엔진(예: Blink, Gecko, WebKit)과 코드 실행을 위한 JavaScript 엔진을 통합한다. 또한 브라우저는 샌드박싱, HTTPS 적용, 팝업 차단 등의 기능을 통해 보안을 관리한다. HTML5, CSS3, 웹 API 등 다양한 웹 표준과 기술을 지원하여 풍부하고 인터랙티브한 웹 경험을 제공한다.  

### [How Browsers Work](https://www.ramotion.com/blog/what-is-web-browser/)

#### How a Web Browser Works?

![](/image/outlook-of-how-a-web-browser-works.jpg)

렌더링 엔진: 웹 브라우저의 주요 구성 요소로 웹사이트 코드를 웹 페이지에 표시되는 시각적 요소로 변환한다. 

- Blink: 구글 크롬에 구동되며, 빠른 속도와 효율성으로 유명
- WebKit: Safari 및 기타 부라우저에서 사용되며, 복잡한 웹 페이지를 렌더링하기 위한 강력한 기능을 제공
- Gecko: Mozilla Firefox 엔진은 오픈 소스 개발과 표준 준수를 우선시한다.

### [Populating the Page: How Browsers Work](https://developer.mozilla.org/ko/docs/Web/Performance/Guides/How_browsers_work)

#### Navigation

- DNS Lookup: 한 번도 방문한 적이 없는 사이트를 요청하면, 브라우저는 DNS 조회를 요청한다. 이는 최종적으로 이름 서버에 의해 처리되고, IP 주소로 응답한다.
- TCP Handshake: IP 주소를 알고난 후에는, 브라우저는 서버와 TCP 3방향 핸드셰이크를 통해 연결을 설정한다. TCP의 3방향 핸드셰이크 기술은 "SYN-SYN-ACK" (더 정확히는 SYN, SYN-ACK, ACK)로 불린다. 이는 요청이 보내지기 전에 3개의 추가적인 메시지가 컴퓨터 사이에 주고 받아진다는 의미앋.
- TLS Negotiation: HTTPS를 이용한 보안성 있는 연결을 위해서는 또 다른 "핸드셰이크"가 필요하다. 이를 위해서 자원에 대한 실제 요청 전에 클라이언트에서 서버로 3번 더 왕복해야 한다.

![](/image/ssl.jpg)

- 8번의 왕복이 있은 후에 브라우저는 마침내 요청을 할 수 있다.

#### Response

서버가 요청을 받으면, 관련 응답 헤더와 함께 HTML의 내용을 응답하게 된다.

- 혼잡 제어(Congestion control) / TCP 슬로우 스타트(TCP Slow Start)
  - CP 패킷은 전송 중에 세그먼트로 분할된다. TCP는 패킷의 순서를 보장하기 때문에 서버는 일정 개수의 세그먼트를 전송한 후 클라이언트로부터 ACK 패킷 형태로 승인을 받아야 한다.
  - 서버가 각 세그먼트마다 ACK을 기다린다면 클라이언트로부터 빈번한 ACK이 발생하고, 이는 저부하 네트워크 상황에서도 전송 시간을 증가시킬 수 있다.
  - 반면, 한 번에 너무 많은 세그먼트를 보내면 사용량이 많은 네트워크는 클라이언트가 세그먼트를 받을 수 없어 계속 ACK만 응답하게 되고, 서버는 세그먼트를 계속 재전송해야 하는 문제가 발생할 수 있다.
  - 전송되는 세그먼트 수의 균형을 맞추기 위해 TCP 슬로우 스타트 알고리즘을 사용하여 최대 네트워크 대역폭이 결정될 때까지 전송되는 데이터의 양을 점차적으로 늘리고 네트워크 부하가 높은 경우 전송되는 데이터의 양을 줄인다.

#### Parsing(구문 분석)

구문 분석은 브라우저가 네트워크를 통해 받은 데이터를 DOM이나 CSSOM으로 바꾸는 단계이다. 이는 렌더러가 화면에 페이지를 그리는데 사용된다.

브라우저는 마크업을 내부적으로 DOM으로 표현한다. DOM은 공개되어있고 Javascript의 다양한 API를 통해 조작할 수 있다.

- DOM 트리 구축(Building the DOM tree): 브라우저가 DOM 트리를 만드는 프로세스는 메인 쓰레드를 차지한다.
- 프리로드 스캐너(Preload scanner): 사용 가능한 컨텐츠를 분석하고 CSS, Javascript, 웹폰트 같이 우선순위가 높은 자원을 요청한다.
- CSSOM 구축(Building the CSSOM)

#### 다른 작업들(Other PRocesses)

- Javascript 컴파일(JavaScript Compilation): 자바스크립트 해석, 컴파일, 구문 분석, 실행
- 접근성 트리 구축(Building the Accessibility Tree)

#### 렌더(Render)

- Style: DOM과 CSSOM을 합쳐 렌더 트리를 만드는 것
- Layout
- Paint
- Composition


