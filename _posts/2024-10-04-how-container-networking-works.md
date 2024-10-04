---
layout: post
title: 컨테이너 네트워크 작동 방식(도커 브릿지 네트워크)
categories: docker
tags: [docker, network, kubernetes]
excerpt: 리눅스 환경에서 실습을 통해 도커 네트워크 동작 방식에 대해 설명합니다.
---

[How Container Networking Works: a Docker Bridge Network From Scratch](https://labs.iximiuz.com/tutorials/container-networking-from-scratch) 포스트를 읽고 정리한 내용입니다.

## 컨테이너 네트워킹 작동 방식: 처음부터 시작하는 Docker Bridge 네트워크

표준 리눅스 도구를 사용해 학습한다. 컨테이너 네트워킹은 기본적은 리눅스 기능의 조합에서 나온다.

- Network namespaces (netns)
- Virtual Ethernet devices (veth)
- Virtual network switches (bridge)
- IP routing and network address translation (NAT)

### 주요 네트워크 환경 구성 요소(장치, 라우팅 테이블, 방화벽 규칙)

![](/image/linux-network-environment.png)

[1-main-network.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/1-main-network.sh)

### 네트워크 네임스페이스(netns)를 사용하여 첫 번째 컨테이너 만들기

네트워크 네임스페이스는 논리 네트워크 스택의 사본이며 자체 라우트, 방화벽 규칙, 네트워크 장치가 있다.

[2-network-namespace.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/2-network-namespace.sh)

### 가상 이더넷 장치(veth)를 사용하여 컨테이너를 호스트에 연결

리눅스는 네트워크 네임스페이스들을 연결하기 위한 가상 이더넷 장치(veth)를 제공한다. 가상 이더넷 장치는 다른 네임스페이스에 있는 물리적 네트워크 장치에 브릿지를 생성해 네트워크 네임스페이스 간의 터널 역할을 한다. 또한, 독립형 네트워크 장치로도 사용할 수 있다.

가상 인터넷 장치는 항상 쌍으로 존재한다.

[3-virtual-ethernet-devices.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/3-virtual-ethernet-devices.sh)

![](/image/veth.png)

### 동일한 단계를 반복하여 두 번째 컨테이너 생성

[4-second-container.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/4-second-container.sh)

### 가상 네트워크 스위치(브리지)를 사용하여 컨테이너 상호 연결

리눅스는 위의 문제 해결방안으로 `bridge` 라는 기능을 가지고 있다.
리눅스 브릿지 장치는 네트워크 스위치처럼 동작한다. 브릿지는 연결된 인터페이스간 패킷을 전달한다. 그리고 라우터가 아닌 스위치이기 때문에 연결된 장치들의 IP 주소를 신경쓸 필요가 없다. 브릿지는 L2(예: Ethernet) 레벨에서 작동하기 때문이다.

![](/image/bridge-vs-router.png)

[11-virtual-network-switch.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/11-virtual-network-switch.sh)

![](/image/bridge.png)

### 외부 세계로의 접근(IP 라우팅 및 위장)

[12-ip-routing.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/12-ip-routing.sh)

![](/image/router.png)

### 외부 세계가 컨테이너에 접근할 수 있도록 함(포트 퍼블리싱)

[13-port-publishing.sh](https://github.com/pinstinct/container-network-study/blob/8a156b623932269eb6fbee9a54d4231b2af8bea4/script/13-port-publishing.sh)

### Docker 네트워크 유형(드라이버) 이해

#### `--network host` 모드로 실행

도커 `host` 모드는 독립된 네트워크 네임스페이스를 사용하지 않고, 단순히 main 네트워크 네임스페이스에서 작동합니다. 그리고 네트워크 컨텍스트를 호스트 머신과 공유합니다.

```shell
docker run -it --rm --network host alpine ip link list
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: bond0: <BROADCAST,MULTICAST400> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 1a:ce:e8:6d:ed:3c brd ff:ff:ff:ff:ff:ff
3: dummy0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 8e:8e:e5:c0:dc:58 brd ff:ff:ff:ff:ff:ff
4: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 65535 qdisc pfifo_fast state UP qlen 1000
    link/ether ea:9b:d8:a3:1c:83 brd ff:ff:ff:ff:ff:ff
5: teql0: <NOARP> mtu 1500 qdisc noop state DOWN qlen 100
    link/void
6: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
7: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
8: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1462 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
9: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
10: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
11: ip6_vti0@NONE: <NOARP> mtu 1428 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
12: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/sit 0.0.0.0 brd 0.0.0.0
13: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
14: ip6gre0@NONE: <NOARP> mtu 1448 qdisc noop state DOWN qlen 1000
    link/[823] 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
15: services1@ip6_vti0: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
    link/ether 46:ac:62:c8:59:e4 brd ff:ff:ff:ff:ff:ff
16: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 65535 qdisc noqueue state UP
    link/ether 02:42:eb:e1:54:ca brd ff:ff:ff:ff:ff:ff
17: br-595b5ae3b7bd: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 65535 qdisc noqueue state DOWN
    link/ether 02:42:e3:44:29:4e brd ff:ff:ff:ff:ff:ff
18: br-8be3aa999979: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN
    link/ether 02:42:6c:07:83:95 brd ff:ff:ff:ff:ff:ff
82: veth9bb36e2@if81: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 65535 qdisc noqueue master docker0 state UP
    link/ether 46:d0:f3:ba:a1:92 brd ff:ff:ff:ff:ff:ff
```

#### `--network none` 모드로 실행

단일 loopback 네트워크 인터페이스만 있다.

```shell
docker run --rm -it --network none alpine ip link list
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
3: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
4: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1462 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
5: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
6: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
7: ip6_vti0@NONE: <NOARP> mtu 1428 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
8: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/sit 0.0.0.0 brd 0.0.0.0
9: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
10: ip6gre0@NONE: <NOARP> mtu 1448 qdisc noop state DOWN qlen 1000
    link/[823] 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
```

#### `--network bridge` 모드로 실행

도커 네트워크의 기본 모드이다. 위의 실습에서 재현하려고 했던 것이다. 게다가 많은 쿠버네티스 배포판이 `bridge` 네트워크를 사용해 같은 노드의 파드들(pods)과 서로 연결한다.

```shell
docker run --rm -it --network bridge alpine ip link list
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: tunl0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
3: gre0@NONE: <NOARP> mtu 1476 qdisc noop state DOWN qlen 1000
    link/gre 0.0.0.0 brd 0.0.0.0
4: gretap0@NONE: <BROADCAST,MULTICAST> mtu 1462 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
5: erspan0@NONE: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN qlen 1000
    link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
6: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
7: ip6_vti0@NONE: <NOARP> mtu 1428 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
8: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN qlen 1000
    link/sit 0.0.0.0 brd 0.0.0.0
9: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN qlen 1000
    link/tunnel6 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
10: ip6gre0@NONE: <NOARP> mtu 1448 qdisc noop state DOWN qlen 1000
    link/[823] 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00 brd 00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
83: eth0@if84: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 65535 qdisc noqueue state UP
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
```
