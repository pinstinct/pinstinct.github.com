---
layout: post
title: 동시성과 Virtual Thread
categories: Java
tags: [java, jvm, thread, concurrency]
excerpt: 동시성, 쓰레드에 대한 내용을 바탕으로 java virtual thread에 대해 정리합니다. 
---

## 병렬성과 동시성

- 병렬성(Parallelism): 물리적 동시 처리
    - CPU 연산이 많이 필요한 작업에 유용하다. (예: 오디오/비디오 처리, 컴퓨터 비전, 머신러닝, 딥러닝, ...)
- 동시성(Concurrency): 작업을 동시 처리하는 그 자체 혹은 그렇게 보이는 것 (time-sharing), 순차적이 아님
    - 대기 시간이 필요한 작업에는 더 유용하다. (예: 웹 어플리케이션, ...)

## 쓰레드

- 쓰레드: CPU를 여러 작업이 번갈아 쓰는 경우, 이 때 작업을 쓰레드라고 부름
    - 문제점 
        - 컨텍스트 스위칭 비용 발생
            - 컨텍스트 스위칭 비용을 줄이기 위한 스케줄링
                - (1)선점형 스케줄링 
                - (2)협력적 스케줄링
                    - (3)Continuation Passing Style
                    - (4)코루틴
        - 스택 사이즈
            - 하나의 쓰레드는 하나의 스택을 의미 
            - 리눅스에서 하나의 스택은 2MB 정도 메모리 차지, 쓰레드가 1,000개 되면 메모리 4GB 차지
- 사용자 스레드: 사용자 공간에서 구현된 스레드 라이브러리 

### 컨텍스트 스위칭 비용을 줄이기 위한 스케줄링

- (1)선점형 스케줄링: 각 스레드의 실행 시간을 시스템이 할당한다. 우선순위를 통해 운영체제에 실행 시간을 '권고' 가능하다.
- (2)협력적 스케쥴링: 스레드 실행 시간을 스레드 스스로 제어한다. 따라서 일을 마친 스레드는 다른 스레드로 전환되도록 시스템에 적극적으로 알려야 한다.
    - 장점: 일반적으로 동기화 문제가 발생하지 않음, 구현하기 쉬움  
    - 단점: 스레드 실행 시간을 제어할 수 없음  

### 협력적 스케줄링을 구현하기 위한 방법

- (3)Continuation Passing Style, CPS
    - Continuation = Callback
    - 콜백 함수를 전달(반환)하는 방식의 스케쥴링
    - 우리는 Callback 함수를 잘 스케쥴링 하기만 하면 됨
    - 예: 이전 Node.js 이벤트 루프(I/O Loop)
        -  Node.js의 모든 I/O 처리는 이벤트 루프(I/O Loop)라는 내부 프로시저에서 처리 
        - 동시성 처리를 위한 협력적 스케줄링 + 작업을 I/O 대기가 일어나는 구간으로 분할
        - 모든 콜백 구간이 I/O 대기를 피해 나누어져 있어, 개발자는 콜백 함수를 잘 사용하기만 하면 됨
- (4)코루틴
    - 코루틴은 여러 진입점+여러 진출점을 가진 프로그램 조각 
    - 아래 코드의 코루틴의 실행 과정을 보면 하나의 작업(generator)이 `yield`를 기준으로 여러 단위로 나뉨 
    - 코루틴을 효과적으로 스케줄링하면 협력적 스케줄링을 하는 가상의 쓰레드 생성 가능, 이를 User-level Thread 혹은 Green Thread라고 부름

    ```python
    def some_generator():
        name = yield 1
        age = yield 2
        print(name, age)
        yield 3
    ```


## JVM Virtual Thread
- 예전에 JVM 스레드 모델은 Kernel-level Thread와 User-level Thread를 1:1 매핑하여 사용
- JDK21 이후의 JVM의 가상 스레드 Kernel-level Thread, Platform Thread, User-level Thread의 N:M 구조로 구성
    - JVM이 자체적으로 Virtual Thread를 관리/처리, Spring MVC&Tomcat의 단점이 많이 상쇄됨 
    - 개발자가 신경쓰지 않고(코드에 async/await 작성), 동시성 활용 가능 
    - 하지만 아직 모든게 지원되는 것은 아니다. Java에 남아있는 synchronized 제거 해야하고, 그 외에 사용하는 라이브러리가 지원해줘야하는 문제점이 있음 


> ref: [백엔드 개발자들이 알아야할 동시성 6 — Coroutine](https://choi-geonu.medium.com/%EB%B0%B1%EC%97%94%EB%93%9C-%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%93%A4%EC%9D%B4-%EC%95%8C%EC%95%84%EC%95%BC%ED%95%A0-%EB%8F%99%EC%8B%9C%EC%84%B1-6-coroutine-9eecdbeb2d2d), [Virtual Thread의 기본 개념 이해하기](https://d2.naver.com/helloworld/1203723), [[Project Loom] Virtual Thread에 봄(Spring)은 왔는가](https://tech.kakaopay.com/post/ro-spring-virtual-thread/), [Java의 미래 Virtual Thread](https://techblog.woowahan.com/15398/)


> [download](/image/D2Coding-Ver1.zip)
