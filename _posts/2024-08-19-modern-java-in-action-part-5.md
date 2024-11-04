---
layout: post
title: 모던 자바 인 액션 - Part5
categories: book
tags: [book, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987) 책을 읽고 정리한 내용입니다.

> [Repository](https://github.com/pinstinct/java-in-action)

## Part5 개선된 자바 동시성

### Chapter15. CompletableFuture와 리액티브 프로그래밍 컨셉의 기초

- 최근 소프트웨어 개발 방법을 획기적으로 뒤집는 두 가지 추세가 있다.
  - 멀티코어 프로세서가 발전하면서 애플리케이션의 속도는 멀티코어 프로세서를 얼마나 잘 활용할 수 있도록 소프트웨어를 개발하는가에 따라 달라질 수 있음을 확인했다.
  - 하나의 거대한 애플리케이션 대신 작은 서비스로 애플리케이션을 나누는 것이다. 서비스가 작아진 대신 네트워크 통신이 증가한다.
    - 이런 애플리케이션을 구현하려면 인터네승로 여러 웹 서비스에 접근해야 한다. 하지만 이들 서비스의 응답을 기다리는 동안 연산이 블록되거나 귀중한 CPU 클록 사이클 자원을 낭비하고 싶진 않다. 포크/조인 프레임워크와 병렬 스트림은 병렬성의 귀중한 도구다.
  - 반면 병렬성이 아니라 동시성을 필요로 하는 상황 즉 조금씩 연관된 작업을 같은 CPU에서 동작하는 것 또는 애플리케이션의 생산성을 극대화할 수 있도록 코어를 바쁘게 유지하는 것이 목표라면, 원격 서비스나 데이터베이스 결과를 기다리는 스레드를 블록함으로 연산 자원을 낭비하는 일은 피해야 한다. 
    - 자바는 이런 환경에서 사용할 수 있는 도구를 제공한다. Future 인터페이스로 CompletableFuture 구현(자바 8)은 간단하고 효율적인 문제 해결사다. 최근 자바 9에 추가된 발행 구독 프로토콜에 기반한 리액티브 프로그래밍 개념을 따르는 플로 API는 조금 더 정교한 프로그래밍 접근 방법을 제공한다.
  
#### 동시성을 구현하는 자바 지원의 진화 

- 처음에는 Runnable과 Thread를 동기화된 클래스와 메서드를 이용해 잠갔다.
- 자바 5는 스레드 실행과 태스크 제출을 분리하는 ExecutorService 인터페이스를 지원
- 자바 7에서는 분할 그리고 정복 알고리즘의 포크/조인 구현을 지원하는 java.util.concurrent.RecursiveTask 추가
- 자바 8에서는 스트림과 람다 지원에 기반한 병렬 프로세싱 추가
- 자바 9에서는 분산 비동기 프로그래밍을 명시적으로 지원
- CompletableFuture와 java.util.concurrent.Flow의 궁극적인 목표는 가능한한 동시에 실행할 수 있는 독립적인 태스크를 가능하게 만들면서 멀티코어 또는 여러 기기를 통해 제공되는 병렬성을 쉽게 이용하는 것이다.
- Executor와 스레드 풀
  - 자바 5는 Executor 프레임워크와 스레드 풀을 통해 스레드 힘을 높은 수준으로 끌어올리는 즉 자바 프로그래머가 태스크 제출과 실행을 분리할 수 있는 기능을 제공했다.
  - 스레드의 문제: 자바 스레드는 직접 운영체제 스레드에 접근한다. 운영체제 스레드를 만들고 종료하려면 비싼 비용을 치러야 하며 더욱이 운영체제 스레드의 숫자는 제한되어 있는 것이 문제다.
  - 스레드 풀 그리고 스레드 풀이 더 좋은 이유: ExecutorService는 태스크를 제출하고 나중에 결과를 수집할 수 있는 인터페이스를 제공한다. newFixedThreadPool 같은 팩토리 메서드 중 하나를 이용해 스레드 풀을 만들어 사용할 수 있다. 이 메서드는 워커 스레드라 불리는 nThreads를 포함하는 ExecutorService를 만들고 이들을 스레드 풀에 저장한다. 스레드 풀에서 사용하지 않는 스레드로 제출된 태스크를 먼저 온 순서대로 실행한다. 이들 태스크 실행이 종료되면 스레드를 풀로 반환한다. 이 방식의 장점은 하드웨어에 맞는 수의 태스크를 유지함과 동시에 수 천개의 테스크를 스레드 풀에 아무 오버헤드 없이 제출할 수 있다는 점이다.
  - 스레드 풀 그리고 스레드 풀이 나쁜 이유: 거의 모든 관점에서 스레드를 직접 사용하는 것보다 스레드 풀을 이용하는 것이 바람직하지만 두 가지 "사항"을 주의해야 한다.
    - k 스레드를 가진 스레드 풀은 오직 k만큼의 스레드를 동시에 실행할 수 있다. 초과로 제출된 태스크는 큐에 저장되며 이전에 태스크 중 하나가 종료되기 전까지는 스레드에 할당하지 않는다. I/O를 기다리는 태스크가 있다면 주의해야 한다. 이들 태스크가 워커 스레드에 할당된 상태를 유지하지만 아무 작업도 하지 않게 된다. 핵심은 블록(자거나 이벤트를 기다리는)할 수 있는 태스크는 스레드 풀에 제출하지 말야아 한다는 것이지만 항상 이를 지킬 수 있는 것은 아니다.
    - 프로그램을 종료하기 전에 모든 스레드 풀을 종료하는 습관을 갖는 것이 중요하다. 자바는 이런 상황을 다룰 수 있도록 Thread.setDaemon 메서드를 제공한다.
  - 스레드의 다른 추상화: 중첩되지 않은 메서드 호출
    - 엄격한 포크/조인: 태스크나 스레드가 메서드 호출 안에서 시작되면 그 메서드 호출은 반환하지 않고 작업이 끝나기를 기다린다.
    - 여유로운 방식의 포크/조인: 시작된 태스크를 내부 호출이 아니라 외부 호출에서 종료하도록 기다린다.
      - 사용자의 메서드 호출에 의해 스레드가 생성되고 메서드를 벗어나 계속 실행되는 동시성 형태에 초점을 둔다. 이런 종류, 특히 메서드 호출자에 기능을 제공하도록 메서드가 반환된 후에도 만들어진 태스크 실행이 계속되는 메서드를 비동기 메서드라 한다.
      - 비동기 메서드를 사용할 때 따르는 위험성
        - 스레드 실행은 메서드를 호출환 다음의 코드와 동시에 실행되므로 데이터 경쟁 문제를 일으키지 않도록 주의해야 한다.
        - 기존 실행 중이던 스레드가 종료되지 않은 상황에서 자바의 main() 메서드가 반환되면 어떻게 될까? 다음과 같은 두 가지 방법이 있는데 어느 방법도 안전하지 못하다.
          - 애플리케이션을 종료하지 못하고 모든 스레드가 실행을 끝낼 때까지 기다린다.
          - 애플리케이션 종료를 방해하는 스레드를 강제종료(kill) 시키고 애플리케이션을 종료한다.
        - 자바 스레드는 setDaemon() 메서드를 이용해 **데몬(daemon)** 또는 비데몬으로 구분시킬 수 있다. 데몬 스레드는 애플리케이션이 종료될 때 강제 종료되므로 디스크의 데이터 일관성을 파괴하지 않는 동작을 수행할 때 유용하게 활용할 수 있는 반면, main() 메서드는 모든 비데몬 스레드가 종료될 때까지 프로그램을 종료하지 않고 기다린다.
  - 스레드는 무엇을 바라는가? 일반적으로 모든 하드웨어 스레드를 활용해 병렬성의 장점을 극대화하도록 프로그램 구조를 만드는 것 즉, 프로그램을 작은 태스크 단위로 구조화하는 것이 목표다. 하지만 태스크 변환 비용을 고려해 너무 작은 크기는 아니어야 한다.

#### 동기 API와 비동기 API

- 자바 8 스트림을 이용해 명시적으로 병렬 하드웨어를 이용할 수 있다. 외부 반복(명시적 for 루프)을 내부 반복(스트림 메서드 사용)으로 바꿔야 한다. 그리고 스트림에 parallel() 메서드를 이용해 자바 런타임 라이브러리가 복잡한 스레드 작업을 하지 않고 병렬로 요소가 처리되도록 할 수 있다.
- f와 g를 실행하는데 오랜 시간이 걸린다고 가정하자. 별도의 스레드로 f와 g를 실행해 이를 구현할 수 있다. 의도는 좋지만 단순했던 코드가 복잡하게 변한다.
- 문제의 해결은 비동기 API라는 기능으로 API를 바꿔서 해결할 수 있다.
  - 동기 API는 보통 결과가 나올 때까지 물리적 반환을 지연시킴으로 **블로킹** API로도 알려져있다. I/O 작업이 가장 흔한 예다. 반면 비동기 API는 블록하지 않는 I/O를 구현한다. 비동기 API는 보통 결과를 기다리지 않고 I/O 작업을 시작시킨다. 
  - Future 형식 API
  - 리액티브 형식 API
  - 두 대안 모두 코드를 복잡하게 만든다고 생각할 것이다. 하지만 API는 명시적으로 스레드를 처리하는 코드에 비해 사용 코들르 더 단순하게 만들어주며 높은 수준의 구조를 유지할 수 있게 도와준다.
  - 또한 계산이 오래 걸리는 메서드(수 밀리초 이상), 네트워크나 사람의 입력을 기다리는 메서드에 이들 API를 잘 활용하면 애플리케이션의 효율성이 크게 향상된다.
- 잠자기(그리고 기타 블로킹 동작)는 해로운 것으로 간주: 스레드는 잠들어도 여전히 시스템 자원을 점유한다. 스레드가 많아지고 그 중 대부분이 잠을 잔다면 문제가 심각해진다. 스레드 풀에서 잠을 자는 태스크는 다른 태스크가 시작되지 못하게 막으므로 자원을 소비한다는 사실을 기억하자.
- 현실성 확인: 블록할 수 있는 모든 동작을 비동기 호출로 구현한다면 병렬 하드웨어를 최대한 활용할 수 있다. 현실적으로는 '모든 것은 비동기'라는 설계 원칙을 어겨야 한다.

#### CompletableFuture와 콤비네이터를 이용한 동시성

- 상황에 따라 get()을 기다리는 스레드가 큰 문제가 되지 않으므로 기존 자바 8의 Future를 이용한 방식도 해결 방법이 될 수 있다. 하지만 어떤 상황(예를 들어 서비스에 여러 질의를 처리하는 상황)에서는 많은 수의 Future를 사용해야 한다. 
- 이런 상황에서는 CompletableFuture와 콤비네이터를 이용해 get()에서 블록하지 않을 수 있고 그렇게 함으로 병렬 실행의 효율성을 높이고 데드락은 피하는 최상의 해결책을 구현할 수 있다.

#### 발행-구독 그리고 리액티브 프로그래밍

- 프로그램이 스트림 모델에 잘 맞는 상황이라면 가장 좋은 구현이 될 수 있다. 하지만 보통 리액티비 프로그래밍 패러다임은 비싼 편이다. 스트림 패러다임은 두 개의 파이프라인으로 값을 분리 (포크처럼)하기 어려우며 두 개의 분리된 스트림에서 다시 결과를 합치기도 (조인처럼) 어렵다. 스트림은 선형적인 파이프라인 처리 기법에 알맞다.
- 자바 9에서는 java.util.concurrent.Flow의 인터페이스에 발행-구독 모델(또는 줄여서 pub-sub이라 불리는 프로토콜)을 적용해 리액티브 프로그래밍을 제공한다.
- 살펴볼 세 가지 플로 API  
  - **구독자**가 구독할 수 있는 **발행자**
  - 이 연결을 **구독(subscription)**이라 한다.
  - 이 연결을 이용해 **메시지**(또는 **이벤트**로 알려짐)를 전송한다.
- 데이터가 발행자(생산자)에서 구독자(소비자)로 흐름에 착안해 개발자는 이를 **업스트림(upstream)** 또는 **다운스트림(downstream)**이라 부른다.
- 플로 인터페이스의 개념을 복잡하게 만드는 두 가지 기능은 압력과 역압력이다. 처음에는 이 두 기능이 별로 중요해 보이지 않을 수 있지만 스레드 활용에서 이들 기능은 필수다.
- 역압력
  - Publisher에서 Subscriber로 정보를 전달한다. 정보의 흐름 속도를 역압력(흐름 제어)으로 제어 즉 Subscriber에서 Publisher로 정보를 요청해야 할 필요가 있을 수 있다.
  - Publisher는 여러 Subscriber를 갖고 있으므로 역압력 요청이 한 연결에만 영향을 미쳐야 한다는 것이 문제가 될 수 있다.
  - Subscription 객체는 cancel, request처럼 Subscriber와 Publisher와 통신할 수 있는 메서드를 포함한다.
  - Publisher는 Subscription 객체를 만들어 Subscriber로 전달하면 SubScriber는 이를 이용해 Publisher로 정보를 보낼 수 있다.

#### 리액티브 시스템 vs 리액티브 프로그래밍

- 리액티브 시스팀(reactive system): 런타임 환경이 변화에 대응하도록 전체 아키텍처가 설계된 프로그램
  - 리액티브 시스템이 가져야할 공식적인 속성은 [Reactive Manifesto](https://www.reactivemanifesto.org/)에서 확인할 수 있다. 
  - 반응성(responsive): 큰 작업을 처리하느라 간단한 질의 응답을 지연하지 않고 실시간으로 입력에 반응하는 것
  - 회복성(resillent): 한 컴포넌트의 실패로 전체 시스템이 실패하지 않아야 함
  - 탄력성(elastic): 시스템이 자신의 작업 부하에 맞게 적응하며 작업을 효율적으로 처리함을 의미
  - 메시지 주도(message-driven): 박스와 채널 모델에 기반한 API를 갖고 있는데 여기서 컴포넌트는 처리할 입력을 기다리고 결과를 다른 컴포넌트로 보내면서 시스템이 반응한다.
-  여러 가지 방법으로 이런 속성을 구현할 수 있지만 java.util.concurrent.Flow 관련된 자바 인터페이스에서 제공하는 **리액티브 프로그래밍** 형식을 이용하는 것도 주요 방법 중 하나다. 

### Chapter16. CompletableFuture: 안정적 비동기 프로그래밍

#### Future의 단순 활용

- 자바 5부터는 미래의 어느 시점에 결과를 얻는 모델에 활용할 수 있도록 Future 인터페이스를 제공한다. 비동기 모델링하는 데 Future를 이용할 수 있으며, Future는 계산이 끝났을 때 결과에 접근할 수 있는 참조를 제공한다. 시간이 걸릴 수 있는 작업을 Future 내부로 설정하면 호출자 스레드가 결과를 기다리는 동안 다른 유용한 작업을 수행할 수 있다.

```java
// 스레드 풀에 태스크를 제출하려면 ExecutorService를 만들어야 함
ExecutorService executor = Executors.newCachedThreadPool();

// Callable을 ExecutorService로 제출
Future<Double> future = executor.submit(new Callable<Double>() {
  @Override
  public Double call() throws Exception {
    // 시간이 오래 걸리는 작업은 다른 스레드에서 비동기적으로 실행
    return doSomeLongComputation();
  }
});

// 비동기 작업을 수행하는 동안 다른 작업 수행
doSomethingElse();

try {
  // 비동기 작업 결과를 가져옴 (결과가 준비되어 있지 않으면 호출 스레드가 블록됨)
  // 하지만 최대 1초까지만 대기
  Double result = future.get(1, TimeUnit.SECONDS);
  System.out.println(result);
} catch (ExecutionException e) {
  // 계산 중 예외 발생
} catch (InterruptedException e) {
  // 현재 스레드에서 대기 중 인터럽트 발생
} catch (TimeoutException e) {
  // Future가 완료되기 전에 타임아웃 발생
}
```

- Future 제한: 간결한 동시 실행 코드를 구현하기 충분하지 않다. 예를 들어 여러 Future의 결과가 있을 때 이들의 의존성을 표현하기 어렵다. 즉, '오래 걸리는 A라는 계산이 끝나면 그 결과를 다른 오래 걸리는 계산 B로 전달하시오. 그리고 B의 결과가 나오면 다른 질의의 결과와 B의 결과를 조합하시오' 같은 요구사항을 Future로 구현하는 것은 쉽지 않다.
- Stream과 CompletableFuture는 비슷한 패턴, 즉 람다 표현식과 파이프라이닝을 활용한다. 따라서 Future와 CompletableFuture의 관계를 Collection과 Stream의 관게에 비유할 수 있다.

#### 비동기 API 구현 

- 예산을 줄일 수 있도록 여러 온라인 상점 중 가장 저렴한 가격을 제시하는 상점을 찾는 애플리케이션을 구현하는 예제
- 동기 메서드를 비동기 메서드로 변환 

  ```java
  /* 동기 메서드 */
  public double getPrice(String product) {
    // 상점의 데이터베이스를 이용해서 가격 정보를 얻는 동시에 다른 외부 서비스에도 접근
    return calculatePrice(product);
  }

  /* 비동기 메서드 */
  public Future<Double> getPriceAsync(String product) {
    // 계산 결과를 포함할 CompletableFuture 생성
    CompletableFuture<Double> futurePrice = new CompletableFuture<>();

    new Thread(() -> {
      // 다른 스레드에서 비동기적으로 계산을 수행
      double price = calculatePrice(product);
      // 오랜 시간이 걸리는 계산이 완료되면 Future에 값을 설정
      futurePrice.complete(price);
    }).start();
    // 계산 결과가 완료되길 기다리지 않고 Future 반환
    return futurePrice;
  }

  /* 클라이언트 코드 */
  Shop shop = new Shop("BestShop");
  Future<Double> futurePrice = shop.getPriceAsync("my favorite product");

  // 제품의 가격을 계산하는 동안 다른 작업 수행
  doSomethingElse();

  try {
    // Future가 결과값을 가지고 있다면 Future에 포함된 값을 읽거나
    // 아니면 값이 계산될 때까지 블록
    Double price = futurePrice.get();
    System.out.printf("Price is %.2f%n", price);
  } catch (InterruptedException e) {
    throw new RuntimeException(e);
  } catch (ExecutionException e) {
    throw new RuntimeException(e);
  }
  ```

  - 클라이언트는 특정 제품의 가격 정보를 상점에 요청한다. 상점은 비동기 API를 제공하므로 즉시 Future를 반환한다. 클라이언트는 반환된 Future를 이용해서 나중에 결과를 얻을 수 있다.
  - 그 사이 클라이언트는 다른 작업을 처리하고 특별히 할일이 없으면 Future의 get 메서드를 호출한다. 이 때 Future가 결과값을 가지고 있다면 Future에 포함된 값을 읽거나 아니면 값이 계산될 때까지 블록한다.
- 에러 처리 방법
  - 만약, 위의 코드에서 가격을 계산하는 동안 에러가 발생한다면 클라이언트는 get 메서드가 반환될 때까지 영원히 기다리게 될 수도 있다.

  ```java
  public Future<Double> getPriceAsyncWithException(String product) {
    CompletableFuture<Double> futurePrice = new CompletableFuture<>();
    new Thread(() -> {
      try {
        double price = calculatePrice(product);
        // 계산이 정상적으로 종료되면 Future에 가격 정보를 저장하고 종료
        futurePrice.complete(price);
      } catch (Exception e) {
        // 도중에 문제가 발생하면 발생한 에러를 포함시켜 Future 종료
        futurePrice.completeExceptionally(e);
      }
    }).start();
    return futurePrice;
  }
  ```

  - 팩토리 메서드 supplyAsync로 CompletableFutre 만들기
    - supplyAsync 메서드는 Supplier를 인수로 받아 CompletableFuture 반환
    - CompletableFuture는 Supplier를 실행해서 비동기적으로 결과를 생성 
    - 위의 에러 처리 같은 방법으로 에러를 관리 

    ```java
    public Future<Double> getPriceAsync(String product) {
        return CompletableFuture.supplyAsync(() -> calculatePrice(product));
      }
    ```

#### 비블록 코드 만들기 

- 지금부터 위에 구현한 API를 제어할 권한이 우리에게 없는 상황이며 모든 API는 동기 방식의 블록 메서드라고 가정한다.
- 블록 메서드를 사용할 수밖에 없는 상황에서 비동기적으로 여러 상점에 질의하는 방법, 즉 요청의 응답을 기다리며 블록하는 상황을 피해 최저가격 검색 어플리케이션의 성능을 높일 수 있는 방법을 살펴보자.
- 병렬 스트림으로 요청 병렬화하기

```java
public static List<String> findPricesParallel(String product) {
  return shops.parallelStream()
      .map(shop -> format("%s price is %s", shop.getName(), shop.getPrice(product)))
      .toList();
}
```

- CompletableFuture로 비동기 호출 구현하기
  - CompletableFuture에 join을 호출해서 모든 동작이 끝나기를 기다린다.
  - CompletableFuture 클래스의 join 메서드는 Future 인터페이스의 get 메서드와 같은 의미를 갖는다. 다만 join은 아무 예외도 발생시키지 않는다는 점이 다르다.
  - 두 map 연산을 하나의 스트림 파이프라인으로 처리하지 않고 두 개의 스트림 파이프라인으로 처리했다는 사실에 주목하자. 스트림 연산은 게으른 특성이 있으므로 하나의 파이프라인으로 연산을 처리했다면 모든 가격 정보 요청 동작이 동기적, 순차적으로 이루어지는 결과가 된다.

  ```java
  public static List<String> findPricesWithCF(String product) {
    List<CompletableFuture<String>> priceFutures = shops.stream()
        .map(shop -> CompletableFuture.supplyAsync( // CompletableFuture로 각각의 가격을 비동기적으로 계산
            () -> format("%s price is %s", shop.getName(), shop.getPrice(product))
        ))
        .toList();
    return priceFutures.stream()
        .map(CompletableFuture::join)  // 모든 비동기 동작이 끝나기를 기다림 
        .toList();
  }
  ```

  - 코드를 실행하는 기기가 네 개의 스레드를 병렬로 실행할 수 있는 기기라면, 더 많은 상점을 처리하는 상황이어야 향상된 결과를 얻을 수 있다.

- 더 확장성이 좋은 해결 방법: 다섯 번째 상점이 추가되었다면 어떻게 될까? 순차 버전에서는 1초 정도 늘어난다. 병렬 스트림 버전에는 네 개의 상점을 검색하느라 네 개의 모든 스레드(일반적으로 스레드 풀에서 제공하는 스레두 수는 4개)가 사용된 상황이므로 다섯 번째 상점을 처리하는데 추가로 1초 이상 소요한다. CompletableFuture 버전은 병렬 스트림 버전보다 아주 조금 빠르다. 두 가지 버전 모두 내부적으로 Runtime.getRuntime().availableProcessors()가 반환하는 스레드 수를 사용하면서 비슷한 결과가 된다. 결과적으로 비슷하지만 CompletableFuture는 병렬 스트림 버전에 비해 작업에 이용할 수 있는 다양한 Executor를 지정할 수 있다는 장점이 있다.

#### 커스텀 Executor 사용하기 

- 애플리케이션이 실제로 필요한 작업량을 고려한 풀에서 관리하는 스레드 수에 맞게 Executor를 만드는게 좋다.
- 스레드 풀 크기 조절: 스레드 풀이 너무 크면 CPU와 메모리 자원을 서로 경쟁하느라 시간을 낭비할 수 있다. 반면 스레드 풀이 너무 작으면 CPU의 일부 코어는 활용되지 않을 수 있다. 
  - 공식: 스레드 수 = (Runtime.getRuntime().availableProcessors()가 반환하는 코어 수) * (0과 1사이의 값을 갖는 CPU 활용 비율) * (대기시간과 계산시간의 비율)

```java
private static final Executor executor = Executors.newFixedThreadPool(Math.min(shops.size(), 100),
    // 상점 수만큼 스레드를 갖는 풀을 생성(0과 100사이)
    new ThreadFactory() {
      @Override
      public Thread newThread(Runnable r) {
        Thread t = new Thread(r);
        // 프로그램 종료를 방해하지 않는 데몬 스레드를 사용
        t.setDaemon(true);
        return t;
      }
    });

public static List<String> findPriceWithExecutor(String product) {
  List<CompletableFuture<String>> priceFutures = shops.stream().map(
          shop -> CompletableFuture.supplyAsync(
              () -> format("%s price is %s", shop.getName(), shop.getPrice(product)), executor))
      .toList();
  return priceFutures.stream().map(CompletableFuture::join).toList();
}
```

- 위의 코드에서 생성된 풀은 데몬 스레드를 포함한다. 자바에서 일반 스레드가 질생 중이면 자바 프로그램은 종료되지 않는다. 따라서 어떤 이벤트를 한없이 기다리면서 종료되지 않는 일반 스레드가 있으면 문제가 될 수 있다. 반면 데몬 스레드는 자바 프로그램이 종료될 때 강제로 실행이 종료될 수 있다. 두 스레드의 성능은 같다.
- 스트림 병렬화와 CompletableFuture 병렬화
  - CompletableFuture를 이용하면 전체적인 계산이 블록되지 않도록 스레드 풀의 크기를 조절할 수 있다.
  - 다음을 참고해 어떤 병렬화 기법을 사용할 것인지 선택하는데 참고하자
    - I/O가 포함되지 않은 계산 중심의 동작을 실행할 때는 스트림 인터페이스가 가장 구현하기 간단하며 효율적일 수 있다. 모든 스레드가 계산 작업을 수행하는 상황에서 프로세서 코어수 이상의 스레드를 가질 필요가 없다.
    - 반면 작업이 I/O를 기다리는 작업을 병렬로 실행할 때는 CompletableFuture가 더 많은 유연성을 제공하며 대기/계산 비율에 적합한 스레드 수를 설정할 수 있다. 특히 스트림의 게으른 특성 때문에 스트림 I/O를 실제로 언제 처리할지 예측하기 어려운 문제도 있다.

#### 비동기 작업 파이프라인 만들기

- 여러 상점에서 가격 정보를 얻어오고, 결과 문자열을 파싱하고, 할인 서버에 질의를 보낸다. 할인 서버에서 할인율을 확인해서 최종 가격을 계산할 수 있다.
- 가장 간단한 구현

  ```java
  public static List<String> findPrices(String product) {
    return shops.stream()
        .map(shop -> shop.getPriceString(product))  // 각 상점에서 할인 전 가격 얻기
        .map(Quote::parse)  // 상점에서 반환한 문자열을 Quote 객체로 변환
        .map(Discount::applyDiscount)  // Discount 서비스를 이용해서 각 Quote에 할인 적용
        .toList();
  }
  ```

  - 성능 최적화와는 거리가 멀다. 병렬 스트림을 이용하면 성능을 쉽게 개선할 수 있다는 사실을 확인했다. 하지만 병렬 스트림에서는 스트림이 사용하는 스레드 풀의 크기가 고정되어 있어서 상점 수가 늘어났을 때처럼 검색 대상이 확장되었을 때 유연하게 대응할 수 없다는 사실도 확인했다. 따라서 CompletableFuture에서 수행하는 태스크를 설정할 수 있는 커스텀 Executor를 정의함으로써 CPU 사용을 극대화 할 수 있다.

- 동기 작업과 비동기 작업 조합하기

  ```java
  // 비동기적으로 재구현 
  public static List<String> findPricesAsync(String product) {
    List<CompletableFuture<String>> priceFutures = shops.stream()
        .map(shop -> CompletableFuture.supplyAsync(() -> shop.getPriceString(product), executor))
        .map(future -> future.thenApply(Quote::parse))
        .map(future -> future.thenCompose(
            quote -> CompletableFuture.supplyAsync(() -> applyDiscount(quote), executor))).toList();
    return priceFutures.stream().map(CompletableFuture::join)  // 값 추출
        .toList();
  }
  ```

  - 세 개의 map 연산을 적용한다.
    - 가격 정보 얻기: supplyAsync에 람다 표현식을 전달해 비동기적으로 상점에서 정보를 조회한다. 반환 결과는 `Stream<CompletableFuture<String>>`이다.
    - Quote 파싱하기: 원격 서비스나 I/O가 없으므로 지연 없이 동작 수행가능하다. thenApply 메서드는 CompletableFuture가 끝날 때까지 블록하지 않는다. 즉, CompletableFuture가 동작을 완전히 완료한 다음에 thenApply 메서드로 전달된 람다 표현식을 적용할 수 있다. 따라서 `CompletableFuture<String>`을 `CompletableFuture<Quote>`로 변환할 것이다.
    - CompletableFuture를 조합해서 할인된 가격 계산: thenCompose(Fuction) 메서드는 첫 번째 연산의 결과를 두 번째 연산으로 전달한다. Fuction은 첫 번째 CompletableFuture 반환 결과를 인수로 받고 두 번째 CompletableFuture를 반환하는데, 두 번째 CompletableFuture는 첫 번째 CompletableFuture의 결과를 계산의 입력으로 사용한다. thenCompose 메서드도 Async로 끝나는 버전이 존재한다. Async로 끝나지 않는 메서드는 이전 작업을 수행한 스레드와 같은 스레드에서 작업을 실행함을 의미하며 Async로 끝나는 메서드는 다음 작업이 다른 스레드에서 실행되도록 스레드 풀로 작업을 제출한다. 여기서 두 번째 CompletableFuture의 결과는 첫 번째 CompletableFuture에 의존하므로 두 CompletableFuture를 하나로 조합하든 Async 버전의 메서드를 사용하든 최종 결과나 실행시간에는 영향을 미치지 않는다. 따라스 스레드 전환 오버헤드가 적게 발생하면서 효율성이 좀 더 좋은 thenCompose를 사용했다.
- 독립 CompletableFuture와 비독립 CompletableFuture 합치기
  - 독립적으로 실행된 두 개의 CompletableFuture 결과를 합쳐야 하는 상황에서는 thenCombine 메서드를 사용한다. 
  - thenCombine 메서드는 BiFunction을 두 번째 인수로 받는다. BiFuction은 두 개의 CompletableFuture 결과를 어떻게 합칠지 정의한다. thenCombine 메서드도 Async 버전이 존재한다.
  - 한 상점에서 유로 가격 정보를 제공하는데, 고객에게 항상 달러 가격을 보여줘야 한다. 주어진 상품의 가격을 상점에 요청하는 한편 원격 환율 교환 서비스를 이용해 현재 환율을 비동기적으로 요청해야 한다.
  
  ```java
  public static List<String> findPricesInUSD(String product) {
    List<CompletableFuture<String>> futures = shops.stream().map(
        // 제품가격 정보를 요청하는 첫 번째 태스크 생성 (Executor 스레드 1)
        shop -> CompletableFuture.supplyAsync(() -> shop.getPrice(product))
            // USD, EUR의 환율 정보를 요청하는 독립적인 두 번째 태스크 생성 (Executor 스레드 2)
            .thenCombine(CompletableFuture.supplyAsync(() -> getRate(Money.EUR, Money.USD)),
                (price, rate) -> price * rate)
            .thenApply(price -> shop.getName() + " price is " + price)).toList();
    return futures.stream().map(CompletableFuture::join).toList();
  }
  ```

- 타임아웃 효과적으로 사용하기
  - 무한적 대기하는 상황이 발생할 수 있으므로 블록하지 않는 것이 좋다. 
  - orTimeout 메서드는 지정된 시간이 지난 후에 CompletableFuture를 TimeoutException으로 완료하면서 또 다른 CompletableFuture를 반환할 수 있도록 내부적으로 ScheduledThreadExecutor를 활용한다.

  ```java
  public static List<String> findPricesInUSD(String product) {
    List<CompletableFuture<String>> futures = shops.stream().map(
        // 제품가격 정보를 요청하는 첫 번째 태스크 생성 (Executor 스레드 1)
        shop -> CompletableFuture.supplyAsync(() -> shop.getPrice(product))
            // USD, EUR의 환율 정보를 요청하는 독립적인 두 번째 태스크 생성 (Executor 스레드 2)
            .thenCombine(CompletableFuture.supplyAsync(() -> getRate(Money.EUR, Money.USD)),
                (price, rate) -> price * rate)
            // 3초 뒤에 작업이 완료되지 않으면 TimeoutException 발생
            .orTimeout(3, TimeUnit.SECONDS)  
            .thenApply(price -> shop.getName() + " price is " + price)).toList();
    return futures.stream().map(CompletableFuture::join).toList();
  }
  ```

  - 일시적으로 서비스를 이용할 수 없는 상황에서 꼭 서버에서 얻은 값이 아닌 미리 지정된 값을 사용할 수 있는 상황도 있다. 
  - completeOnTimeout 메서드를 이용하면 쉽게 구현할 수 있다. orTimeout 메서드처럼 CompletableFuture를 반환하므로 이 결과를 다른 CompletableFuture 메서드와 연결할 수 있다.

  ```java
  public static List<String> findPricesInUSD(String product) {
    List<CompletableFuture<String>> futures = shops.stream().map(
        // 제품가격 정보를 요청하는 첫 번째 태스크 생성 (Executor 스레드 1)
        shop -> CompletableFuture.supplyAsync(() -> shop.getPrice(product))
            // USD, EUR의 환율 정보를 요청하는 독립적인 두 번째 태스크 생성 (Executor 스레드 2)
            .thenCombine(CompletableFuture.supplyAsync(() -> getRate(Money.EUR, Money.USD)),
                (price, rate) -> price * rate)
            // 환전 서비스가 1초 안에 결과를 제공하지 않으면 기본 환율값을 사용
            .completeOnTimeout(DEFAULT_RATE, 1, TimeUnit.SECONDS)  
            .thenApply(price -> shop.getName() + " price is " + price)).toList();
    return futures.stream().map(CompletableFuture::join).toList();
  }
  ```

#### CompletableFuture의 종료에 대응하는 방법

- get이나 join은 CompletableFuture가 완료될 때까지 블록한다. 다른 방식으로 CompletableFuture의 종료에 대응하는 방법을 알아보자.
- 지금까지는 findPrices 메서드 구현은 모든 상점에서 가격 정보를 가져온 다음에 그것을 사용할 수 있다. 이제 모든 상점에서 가격 정보를 제공할 때까지 기다리지 않고 상점에서 가격 정보를 제공할 때마다 즉시 보여줄 수 있도록 리팩터링 해본다.
  - 상점에 필요한 일련의 연산 실행 정보를 포함하는 CompletableFuture의 스트림을 직접 제어해야 한다.

  ```java
  public static Stream<CompletableFuture<String>> findPricesStream(String product) {
    return shops.stream()
        .map(shop -> CompletableFuture.supplyAsync(() -> shop.getPriceString(product), executor))
        .map(future -> future.thenApply(Quote::parse))
        .map(future -> future.thenCompose(quote -> CompletableFuture.supplyAsync(() -> applyDiscount(quote), executor)));
  }

  findPricesStream("myPhone")
    // thenAccept: CompletableFuture에 동작을 등록
    // 등록된 동작은 CompletableFuture의 계산이 끝나면 값을 소비
    .map(f -> f.thenAccept(System.out::println));
  ```

  - thenAccept 메서드는 연산 결과를 소비하는 Consumer를 인수로 받는다. 또한 thenCompose, thenCombine 메서드와 마찬가지로 thenAcceptAsync라는 Async 버전이 존재한다. 불필요한 콘텍스트 변경은 피하는 동시에 CompletableFuture가 완료되는 즉시 응답하는 것이 좋으므로 thenAcceptAsync를 사용하지 않는다. 오히려 새로운 스레드를 이용할 수 있을 때까지 기다려야 하는 상황이 일어날 수 있다.
  - thenAccept 메서드는 CompletableFuture가 생성한 결과를 어떻게 소비할지 미리 지정했으므로 `CompletableFuture<Void>`를 반환한다.
  - 가장 느린 상점에서 응답을 받아서 반환된 가격을 출력할 기회를 제공하는 방법

  ```java
  CompletableFuture[] future = findPricesStream("myPhone")
      .map(f -> f.thenAccept(System.out::println))
      .toArray(CompletableFuture[]::new);  // 배열에 추가하고 실행 결과 대기
  CompletableFuture.allOf(future).join();
  ```
  
  - 팩토리 메서드 allOf는 CompletableFuture 배열을 입력받아 `CompletableFuture<Void>`를 반환한다. 전달된 모든 CompletableFuture가 완료되야 `CompletableFuture<Void>`가 완료된다. 따라서 allOf 메서드가 반환하는 CompletableFuture에 join을 호출하면 원래 스트림의 모든 CompletableFuture의 실행 완료를 기다릴 수 있다.
  - 배열의 CompletableFuture 중 하나의 작업이 끝나길 기다리는 상황에는 anyOf를 사용한다. anyOf 메서드는 CompletableFuture 배열을 입력으로 받아서 `CompletableFuture<Object>`를 반환한다. `CompletableFuture<Object>`는 처음으로 완료한 CompletableFuture의 값으로 동작을 완료한다.

