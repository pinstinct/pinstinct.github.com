---
layout: post
title: 컨테이너 네트워크 작동 방식(도커 브릿지 네트워크)
categories: docker
tags: [docker, network, kubernetes]
excerpt: 리눅스 환경에서 실습을 통해 도커 네트워크 동작 방식에 대해 설명합니다.
---

[How Container Networking Works: a Docker Bridge Network From Scratch](https://labs.iximiuz.com/tutorials/container-networking-from-scratch) 포스트를 읽고 정리한 내용입니다.

> 전체 실습 구성은 [Repository](https://github.com/pinstinct/container-network-study)에 있습니다.

## 컨테이너 네트워킹 작동 방식: 처음부터 시작하는 Docker Bridge 네트워크


표준 리눅스 도구를 사용해 학습한다. 컨테이너 네트워킹은 기본적은 리눅스 기능의 조합에서 나온다.

- Network namespaces (netns)
  - 네트워크 네임스페이스는 논리 네트워크 스택의 사본이며 자체 라우트, 방화벽 규칙, 네트워크 장치가 있다.
  - 실습 스크립트: [1-main-network.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/1-main-network.sh), [2-network-namespace.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/2-network-namespace.sh)
  - ![](/image/linux-network-environment.png)
- Virtual Ethernet devices (veth)
  - 리눅스는 네트워크 네임스페이스들을 연결하기 위한 가상 이더넷 장치(veth)를 제공한다. 가상 이더넷 장치는 다른 네임스페이스에 있는 물리적 네트워크 장치에 브릿지를 생성해 네트워크 네임스페이스 간의 터널 역할을 한다. 또한, 독립형 네트워크 장치로도 사용할 수 있다.
  - 가상 인터넷 장치는 항상 쌍으로 존재한다.
  - 실습 스크립트: [3-virtual-ethernet-devices.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/3-virtual-ethernet-devices.sh)
  - ![](/image/veth.png)
  - 하지만 같은 IP 네트워크 대역에 두 개의 컨테이너가 있다면, 제대로 동작하지 않는다. 여러 컨테이너가 하나의 IP 네트워크를 사용하는 것은 흔한 사용 케이스이다.
  - 실습 스크립트: [4-second-container.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/4-second-container.sh)
- Virtual network switches (bridge)
  - 리눅스는 위의 문제 해결방안으로 bridge라는 기능을 가지고 있다. 리눅스 브릿지 장치는 네트워크 스위치처럼 동작한다. 브릿지는 연결된 인터페이스간 패킷을 전달한다. 그리고 라우터가 아닌 스위치이기 때문에 연결된 장치들의 IP 주소를 신경쓸 필요가 없다. 브릿지는 L2(예: Ethernet) 레벨에서 작동하기 때문이다.
  - ![](/image/bridge-vs-router.png)
  - 실습 스크립트: [11-virtual-network-switch.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/11-virtual-network-switch.sh)
  - ![](/image/bridge.png)

- IP routing and network address translation (NAT)
  - 컨테이너가 패킷을 외부 세계로 보내면, 도착지 서버는 컨테이너의 IP 주소가 비공개이기 때문에 컨테이너로 다시 패킷을 전송할 수 없다. 즉, 해당 IP에 대한 라우팅 규칙을 로컬 네트워크만 알고 있다. 
  - 이 문제에 대한 해결책은 네트워크 주소 변환(NAT, Network Address Translation) 이다. 외부 네트워크로 나가기 전에, 컨테이너에서 생성된 패킷은 source IP 주소를 호스트의 외부 인터페이스 주소로 바꾼다. 또한, 호스트는 모든 기존 매핑을 추적하고 도착 시 패킷을 컨테이너로 다시 전달하기 전에 IP 주소를 복원한다.
  - 실습 스크립트: [12-ip-routing.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/12-ip-routing.sh), [13-port-publishing.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/13-port-publishing.sh)
  - ![](/image/router.png)

### Docker 네트워크 유형(드라이버) 이해

- host
  - 독립된 네트워크 네임스페이스를 사용하지 않고, 단순히 main 네트워크 네임스페이스에서 작동한다. 그리고 네트워크 컨텍스트를 호스트 머신과 공유한다.

```shell
docker run -it --rm --network host alpine ip link list
```

- none
  - 단일 loopback 네트워크 인터페이스만 있다.

```shell
docker run --rm -it --network none alpine ip link list
```

- bridge
  - 도커 네트워크의 기본 모드이다. 위의 실습에서 재현하려고 했던 것이다.
  - 많은 쿠버네티스 배포판이 bridge 네트워크를 사용해 같은 노드의 파드들(pods)과 서로 연결한다.

```shell
docker run --rm -it --network bridge alpine ip link list
```
