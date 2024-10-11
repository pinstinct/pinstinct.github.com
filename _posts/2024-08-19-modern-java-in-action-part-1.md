---
layout: post
title: 모던 자바 인 액션 - Part1
categories: book
tags: [book, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987) 책을 읽고 정리한 내용입니다.

## Part1 기초

### Chapter1. 자바 8, 9, 10, 11 : 무슨 일이 일어나고 있는가?

#### 역사의 흐름은 무엇인가?

- 멀티코어 CPU 대중화와 같이 하드웨어적 변화도 자바 8에 영향을 미쳤다.
  - 자바 8 이전에는 나머지 코어를 활용하려면 스레드를 사용했다.
    - 자바 1.0에서는 스레드와 락, 메모리 모델 지원
    - 자바 5에서는 스레드 풀, 병렬 실행 컬랙션 등 도입
    - 자바 7에서는 포크/조인 프레임워크 제공
  - 자바 8에서는 병렬 연산을 지원하는 스트림 API 제공
  - 자바 9에서는 리액티브 프로그래밍이라는 병렬 실행 기법 지원

#### 왜 아직도 자바는 변화하는가?

- 프로그래밍 언어 생태계에 변화의 바람이 불었다. 프로그래머는 빅데이터라는 도전에 직면하면서 멀티코어 컴퓨팅 클러스터를 이용해서 빅데이터를 효과적으로 처리할 필요성이 커졌다. 즉, 병렬 프로세싱을 활용해야 하는 데 지금까지의 자바로는 충분히 대응할 수 없었다.
- 병렬성을 활용하는 코드, 간결한 코드를 구현할 수 있도록 자바 8에서 제공하는 기능의 모태인 세 가지 프로그래밍 개념을 소개한다.
- 스트림 처리(stream processing)
  - 스트림이란 한 번에 한 개씩 만들어지는 연속적인 데이터 항목들의 모임이다. 이론적으로 프로그램은 입력 스트림에서 데이터를 한 개씩 읽어 들이며 마찬가지로 출력 스트림으로 데이터를 한 개씩 기록한다. 즉, 어떤 프로그램의 출력 스트림은 다른 프로그램의 입력 스트림이 될 수 있다. 
- 동작 파라미터화(behavior parameterization)로 메서드에 코드 전달하기
  - 자바 8에서는 메서드를 다른 메서드의 인수로 넘겨주는 기능을 제공
  - 동작 파라미터화가 중요한 이유는 스트림 API는 연산의 동작을 파라미터화할 수 있는 코드를 전달한다는 사상에 기초하기 때문이다.
- 병렬성과 공유 가변 데이터(shared mutable data)
  - 스트림 메서드로 전달하는 코드는 다른 코드와 동시에 실행하더라도 안전하게 실행될 수 있어야 한다.
  - 안전하게 실행할 수 있는 코드를 만들려면 공유된 가변 데이터에 접근 하지 않아야 한다.
  - 이런 함수를 순수(pure) 함수, 부작용 없는(side-effect-free) 함수, 상태 없는(stateless) 함수라고 부른다.
  - synchronized를 이용해서 공유된 가변 데이터를 보호하는 규칙을 만들 수 있다. 하지만 synchronized는 시스템 성능에 악영향을 미친다. 하지만 자바 8 스트림을 이용하면 기본의 자바 스레드 API보다 쉽게 병렬성을 활용할 수 있다.

#### 자바 함수

- 프로그래밍 언어에서 함수(function)라는 용어는 메서드(method) 특히 정적 메소드(static method)와 같은 의미로 사용된다.
- 자바 8에서는 함수를 새로운 값의 형식으로 추가했다.
  - 프로그래밍 언어의 핵심은 값을 바꾸는 것이다. 프로그래밍 언어에서는 이 값을 일급(first-class)값 이라고 부른다. 일급값은 프로그램을 실행하는 동안 전달할 수 있다. 
- 메서드와 람다를 일급 시민으로
  - 메서드 참조(method reference): `::`, 이 메서드를 값으로 사용하라는 의미
    - 기존에 객체 참조(object reference, new로 객체 참조를 생성함)를 이용해서 객체를 이리저리 주고받았던 것처럼 자바 8에서는 메서드 참조를 만들어 전달할 수 있게 되었다.
  - 람다(또는 익명 함수)
    - 자바 8에서는 (기명, named) 메서드를 일급값으로 취급할 뿐 아니라 람다(또는 익명 함수)를 포함하여 함수도 값으로 취급할 수 있다.

#### 스트림

- 거의 모든 애플리케이션은 컬렉션을 만들고 활용한다. 
- 스트림 API를 이용하면 컬렉션 API와는 상당히 다른 방식으로 데이터를 처리할 수 있다.
  - 컬렉션에서는 for-each 루프를 이용해 반복 과정을 직접 처리해야 한다. 이런 방식의 반복을 외부 반복이라고 한다.
  - 반면 스트림 API를 이용하면 루프를 신경 쓸 필요가 없다. 스트림 API에서는 라이브러리 내부에서 모든 데이터가 처리된다. 이와 같은 반복을 내부 반복이라고 한다.
  - 컬렉션을 이용했을 때 다른 문제도 생길 수 있다. 예를 들어 많은 요소를 가진 목록을 반복한다면 오랜 시간이 걸릴 수 있다.
- 멀티스레딩 제공
  - 스레드 API로 멀티스레딩 코드를 구현해서 병렬성을 이용하는 것은 쉽지 않다.
  - 자바 8은 스트림 API(java.util.stream)로 '컬렉션을 처리하면서 발생하는 모호함과 반복적인 코드 문제' 그리고 '멀티코어 활용 어려움'이라는 두 가지 문제를 모두 해결했다.
  - 컬렉션은 어떻게 데이터를 저장하고 접근할지에 중점을 두는 반면 스트림은 데이터에 어떤 계산을 할 것인지 묘사하는 것에 중점을 둔다.
  - 컬렉션을 필터링할 수 있는 가장 빠른 방법은 컬렉션을 스트림으로 바꾸고, 병렬로 처리(`parallelStream()`)한 다음에, 리스트로 다시 복원하는 것이다.
  - 라이브러리에서 분할을 처리한다. 즉, 큰 스트림을 병렬로 처리할 수 있도록 작은 스트림으로 분할한다. 

#### 디폴트 메서드와 자바 모듈

- 자바 9의 모듈 시스템은 모듈을 정의하는 문법을 제공하므로 이를 이용해 패키지 모음을 포함하는 모듈을 정의할 수 있다.
- 자바 8에서는 인터페이스를 쉽게 바꿀 수 있도록 디폴트 메서드를 지원한다. 디폴트 메서드를 프로그래머가 직접 구현하는 상황은 흔치 않다. 디폴트 메서드는 특정 프로그램을 구현하는 데 도움을 주는 기능이 아니라 미래에 프로그램이 쉽게 변화할 수 있는 환경을 제공하는 기능이다.
  - 컬렉션 API의 인터페이스를 구현하는 많은 컬렉션 프레임워크가 존재한다. 인터페이스에 새로운 메서드를 추가한다면 인터페이스를 구현하는 모든 클래스는 새로 추가된 메서드를 구현해야 한다. 자바 8은 구현 클래스에서 구현하지 않아도 되는 메서드를 인터페이스에 추가할 수 있는 기능을 제공한다. 디폴트 메서드를 이용하면 기존의 코드를 건드리지 않고도 원래의 인터페이스 설계를 자유롭게 확장할 수 있다.

#### 함수형 프로그래밍에서 가져온 다른 유용한 아이디어

- 지금까지 살펴본 자바에 포함된 함수형 프로그램의 핵심적인 두 아이디어
  - 메서드와 람다를 일급값으로 사용하는 것
  - 가변 공유 상태가 없는 병렬 실행을 이용해 효율적이고 안전하게 함수나 메서드 호출
- Null 회비 기법
  - 자바 8에서는 NullPointer 예외를 피할 수 있도록 도와주는 `Optional<T>` 클래스를 제공


### Chapter2. 동작 파라미터화 코드 전달하기

- 동작 파라미터화: 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다. 이 코드 블록은 나중에 프로그램에서 호출한다. 즉, 코드 블록의 실행은 나중으로 미뤄진다. 변화하는 요구사항에 유연하게 대응할 수 있다.
- 동작 파라미터화를 추가하려면 쓸데없는 코드가 늘어난다. 자바 8은 람다 표현식으로 이 문제를 해결한다.
- 예를 들어 컬렉션을 처리할 때 다음과 같은 메서드를 구현한다고 가정하자.
  - 리스트의 모든 요소에 대해서 '어떤 동작'을 수행할 수 있음
  - 리스트 관련 작업을 끝낸 다음에 '어떤 다른 동작'을 수행할 수 있음
  - 에러가 발생하면 '정해진 어떤 다른 동작'을 수행할 수 있음

#### 변화하는 요구사항에 대응하기

- 예제: 녹색 사과 필터링 --> 색을 파라미터화 --> 무게를 파라미터화 --> 가능한 모든 속성을 필터링

#### 동작 파라미터화

- 위의 예제와 같이 파라미터를 추가하는 방법이 아닌 변화하는 요구사항에 좀 더 유연하게 대응할 수 있는 방법이 절실하다.
  - 사과의 어떤 속성에 기초해서 불리언값을 반환(예를 들어 사과가 녹색인가? 150그램 이상인가?)하는 방법이 있다.
  - 참 또는 거짓을 반환하는 함수를 프레디케이트라고 한다. 
- 전략 패턴: 각 알고리즘(전략이락 불리는)을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에 런타임에 알고리즘을 선택하는 기법이다.

```java
interface ApplePredicate {  // 사과 선택 전략을 캡슐화(알고리즘 패밀리)

  boolean test(Apple a);

}

// 다양한 전략들
static class AppleWeightPredicate implements ApplePredicate {

  @Override
  public boolean test(Apple apple) {
      return apple.getWeight() > 150;
  }
}

static class AppleColorPredicate implements ApplePredicate {

  @Override
  public boolean test(Apple apple) {
      return apple.getColor() == Color.GREEN;
  }
}
```

- 프레디케이트 객체를 받아 사과의 조건을 검사하도록 메소드를 수정한다. 이렇게 동작 파라미터화, 즉 메서드가 다양한 동작(또는 전략)을 받아서 내부적으로 다양한 동작을 수행할 수 있다.

```java
// ApplePredicate 객체를 인수로 받아 조건을 검사
public static List<Apple> filter(List<Apple> inventory, ApplePredicate p) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple : inventory) {
      if (p.test(apple)) {
        result.add(apple);
      }
  }
  return result;
}

List<Apple> heavyApples = filter(inventory, new AppleWeightPredicate());
System.out.println(heavyApples);
```

- 한 개의 파라미터, 다양한 동작: 컬렉션 탐색 로직과 각 항목에 적용할 동작을 분리할 수 있다는 것이 동작 파라미터화의 강점이다.
- 한 메서드가 다른 동작을 수행하도록 재활용할 수 있다. 따라서 유연한 API를 만들 때 동작 파라미터화가 중요한 역할을 한다.
- 지금까지 동작을 추상화해서 변화하는 요구사항에 대응할 수 있는 코드를 구현하는 방법을 살펴봤다.

#### 복잡한 과정 간소화

- 새로운 동작을 전달하려면 ApplePredicate 인터페이스를 구현하는 여러 클래스를 정의한 다음에 인스턴스화해야 한다.
- 자바는 클래스의 선언과 인스턴스화를 동시에 수행할 수 있도록 익명 클래스라는 기법을 제공한다. 익명 클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 할 수 있다. 즉, 즉석에서 필요한 구현을 만들어서 사용할 수 있다.
  - 하지만, 익명클래스는 많은 공간을 차지한다. 코드의 장황함(verbosity)은 나쁜 특성이다. 장황한 코드는 구현하고 유지보수하는 데 시간이 오래 걸릴 뿐 아니라 읽는 즐거움을 빼앗는 요소이다.
- 람다 표현식 사용

```java
// 람다 표현식 사용
List<Apple> heavyApples2 = filterApples(inventory, (Apple a) -> a.getWeight() > 150);
```

- 리스트 형식으로 추상화 

```java
// 리스트 형식으로 추상화
interface Predicate<T> {
  boolean test(T t);
}

// 이제 바나나, 오렌지, 정수, 문자열 등의 리스트에 필터 메서드를 사용할 수 있다.
static <T> List<T> filter(List<T> list, Predicate<T> p) {
  List<T> result = new ArrayList<>();
  for (T e : list) {
    if (p.test(e)) {
      result.add(e);
    }
  }
  return result;
}

List<Apple> redApples = filter(inventory, apple -> RED.equals(apple.getColor()));
System.out.println(redApples);

List<Integer> evenNumbers = filter(Arrays.asList(1, 2, 3, 4, 5), number -> number % 2 == 0);
System.out.println(evenNumbers);
```

#### 실전 에제

- 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리 등을 포함한 다양한 동작으로 파라미터화 할 수 있다.
- Comparator로 정렬하기
  - 자바 8의 List에는 sort 메서드가 포함되어 있다. 물론 Collections.sort도 존재한다. 다음과 같은 인터페이스를 갖는 `java.util.Comparator` 객체를 이용해서 sort의 동작을 파라미터화 할 수 있다.
- Runnalbe로 코드 블록 실행하기

```java
// Runnable로 코드 블록 실행하기
Thread thread = new Thread(new Runnable() {

  @Override
  public void run() {
    System.out.println("Hello world");
  }
});
thread.start();

// Runnable로 코드 블록 실행하기 with 람다
Thread thread = new Thread(() -> System.out.println("Hello world"));
thread.start();
```

- Callable을 결과로 반환하기: Callable 인터페이스를 이용해 결과를 반환하는 태스크를 만든다.

```java
ExecutorService executorService = Executors.newCachedThreadPool();
Future<String> threadName = executorService.submit(new Callable<String>() {
  @Override
  public String call() throws Exception {
    return Thread.currentThread().getName();
  }
});
  
// 람다
ExecutorService executorService = Executors.newCachedThreadPool();
Future<String> threadName = executorService.submit(() -> Thread.currentThread().getName());
```

### Chapter3. 람다 표현식 

- 더 깔끔한 코드로 동작을 구현하고 전달하는 자바 8의 새로운 기능인 람다 표현식을 설명한다.
- 또한, 람다 표현식과 함께 위력을 발휘하는 새로운 기능인 메서드 참조를 설명한다.

#### 람다란 무엇인가?

- 람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다. 람다 표현식에 이름은 없지만 파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트를 가질 수 있다.
- 람다의 특징
  - 익명: 이름이 없다.
  - 함수: 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다.
  - 전달: 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
  - 간결성: 익명 클래스 처럼 많은 자질구레한 코드를 구현할 필요가 없다.
- 람다 표현식 구성
  - 파라미터 리스트: Comparator의 compare 메서드 파라미터(사과 두개)
  - 화살표: 람다 파라미터와 바디를 구분
  - 람다 바디: 람다의 반환값에 해당하는 표현식(두 사과의 무게 비교) 

```java
// 람다 파라미터 -> 람다 바디
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

- 람다 문법

```java
// 표현식 스타일(expresssion style)
(parameters) -> expression

// 블록 스타일(block style)
(parameters) -> { statements; }
```

#### 어디에, 어떻게 람다를 사용할까?

- 함수형 인터페이스라는 문맥에서 람다 표현식을 사용할 수 있다.
- 함수형 인터페이스
  - 정확히 **하나의 추상 메서드**를 지정하는 인터페이스다. 지금까지 살펴본 자바 API의 함수형 인터페이스로 Comparator, Runnable 등이 있다.
  - 람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 **전체 표현식을 함수형 인터페이스의 인스턴스로 취급**할 수 있다.
  - `@FunctionalInterface`: 함수형 인터페이스임을 가리키는 어노테이션
- 함수 디스크립터(function descriptor)
  - 함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가르킨다. 람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 부른다.
- 람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메서드로 전달할 수 있으며, 함수형 인터페이스의 추상 메서드와 같은 시그니처를 갖는다는 사실을 기억하자.

#### 람다 활용 : 실행 어라운드 패턴 

- 람다와 동작 파라미터화로 유연하고 간결한 코드를 구현하는 데 도움을 주는 실용적인 예제
  - 자원 처리(예: 데이터베이스의 파일 처리)에 사용하는 순환 패턴(Circular Reference)은 자원을 열고, 처리한 다음에 자원을 닫는 순서로 이루어진다. 즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는다. 이런 형식의 코드를 **실행 어라운드 패턴**(execute around pattern)이라고 부른다.

```java
public class ExecuteAroundPattern {

  static String processFile() throws IOException {
    File file = new File(
        ExecuteAroundPattern.class.getClassLoader().getResource("data.txt").getFile());
    try (BufferedReader br = new BufferedReader(new FileReader(file))) {
      return br.readLine();  // 실제 필요한 작업을 하는 코드 (한 줄만 읽기)
    }
  }
}
```

- 1단계: 동작 파라미터화
  - 현재 코드는 한 번에 한 줄만 읽을 수 있다. 한 번에 두 줄을 읽거나 다른 요구 사항이 생기면 어떻게 해야 할까? 동작을 파라미터화하는 것이다.
  - 예제에서는 BufferedReader를 인수로 받아서 String을 반환하는 람다가 필요하다.
- 2단계: 함수형 인터페이스를 이용해서 동작 전달
  - 함수형 인터페이스 자리에 람다를 사용할 수 있다.
  - 따라서 BufferedReader를 --> String과 IOException을 던질 수 있는 시그니처와 일치하는 함수형 인터페이스를 만든다.

```java
@FunctionalInterface
public interface BufferedReaderProcessor {

  // BufferedReader -> String & IOException 던질 수 있는 시그니처
  String process(BufferedReader b) throws IOException;
}

public class ExecuteAroundPattern {

  // 함수형 인터페이스 자리에 람다 사용 할 수 있도록 수정
  static String processFile(BufferedReaderProcessor p) throws IOException {
    File file = new File(
        ExecuteAroundPattern.class.getClassLoader().getResource("data.txt").getFile());
    try (BufferedReader br = new BufferedReader(new FileReader(file))) {
      return p.process(br);  
    }
  }
}
```
  - 3단계: 람다 전달

```java
String oneLine = processFile((BufferedReader br) -> br.readLine());  // 한 번에 한 줄

String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());  // 한 번에 두 줄
```

#### 함수형 인터페이스 사용

- 함수형 인터페이스는 오직 하나의 추상 메서드를 지정한다. 함수형 인터페이스의 추상 메서드는 람다 표현식의 시그니처를 묘사한다. 함수형 인터페이스의 추상 메서드 시그니처를 **함수 디스크립터**라고 한다.
- 다양한 람다 표현식을 사용하려면 공통의 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다. 자바 API는 Comparable, Runnable, Callable 등의 다양한 함수형 인터페이스를 포함하고 있다. 자바 8 라이브러리 설계자들은 `java.util.function` 패키지에 여러 가지 새로운 함수형 인터페이스를 제공한다.
- Predicate
  - `java.util.function.Predicate<T>` 인터페이스는 `test`라는 추상 메서드를 정의하며 제네릭 형식 T의 객체를 인수로 받아 불리언을 반환한다.

```java
public static <T> List<T> filter(List<T> list, Predicate<T> p) {
  List<T> result = new ArrayList<>();
  for (T t: list) {
    if (p.test(t)) {
      result.add(t);
    }
  }
  return result;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(new ArrayList<>(), nonEmptyStringPredicate);
```

- Consumer
  - `java.util.function.Consumer<T>` 인터페이스는 제너릭 형식 T 객체를 받아서 void를 반환하는 `accept`라는 추상 메서드를 정의한다.
  - T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있다.

```java
public static <T> void forEach(List<T> list, Consumer<T> c) {
  for (T t : list) {
    c.accept(t);
  }
}

forEach(Arrays.asList(1, 2, 3, 4, 5), (Integer i) -> System.out.println(i));
```

- Function
  - `java.util.function.Function<T, R>` 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 `apply`를 정의한다.
  - 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있다.

```java
public static <T, R> List<R> map(List<T> list, Function<T, R> f) {
  List<R> result = new ArrayList<>();
  for (T t : list) {
    result.add(f.apply(t));
  }
  return result;
}

List<Integer> result = map(Arrays.asList("lambdas", "in", "action"), (String s) -> s.length());
```

- 기본형 특화
  - 자바의 모든 형식은 참조형(reference type: Byte, Integer, Object, List, ...) 아니면 기본형(primitive type: int, double, byte, char)에 해당한다. 제너릭 파라미터(예를 들면 Cusumer<T>의 T)에는 참조형만 사용할 수 있다.
  - 박싱(boxing): 기본형을 참조형으로 변환
  - 언방식(unboxing): 참조형을 기본형으로 변환
  - 오토박싱(autoboxing): 박싱과 언박싱이 자동으로 이루어지는 기능
  - 하지만 이런 변환 과정은 비용이 소모된다. 박싱한 값은 기본형을 감싸는 래퍼며 힙에 저장된다. 따라서 박싱한 값은 메모리를 더 소비하며 기본형을 가져올 때도 메모리를 탐색하는 과정이 필요하다.
  - 자바 8에서는 기본형을 입출력으로 사용하는 상황에서 오토박싱 동작을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공한다. 일반적으로 특정 형식을 입력으로 받는 함수형 인터페이스 이름 앞에는 IntPredicate, DoublePredicate, IntConsumer, LongBinaryOperator, IntFunction처럼 형식명이 붙는다.
  - [Package java.util.function](https://docs.oracle.com/en/java/javase/23/docs/api/java.base/java/util/function/package-summary.html)

```java
// 1000은 기본적으로 int 타입

IntPredicate evenNumbers = (int i) -> i % 2 == 0;
System.out.println();
boolean result = evenNumbers.test(1000);  // 1000이라는 값을 박싱하지 않음

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
boolean result2 = oddNumbers.test(1000);  // 1000이라는 값을 Integer 객체로 박싱 
```

> 자바 8에 추가된 함수형 인터페이스

|함수형 인터페이스|함수 디스크립터|기본형 특화|
|--------------|-------------|---------|
|`Predicate<T>`|T -> boolean|IntPredicate, LongPredicate, DoublePredicate|
|`Consumer<T>`|T -> void|IntConsumer, LongConsumer, DoubleConsumer|
|`Function<T, R>`|T -> R|`IntFunction<R>`, IntToDoubleFunction, IntToLongFunction, `LongFunction<R>`, LongToDoubleFunction, LongToIntFunction, `DoubleFunction<R>`, DoubleToIntFunction, DoubleToLongFunction, `ToIntFunction<T>`, `ToDoubleFunction<T>`, `ToLongFunction<T>`|
|`Supplier<T>`|() -> T|BooleanSupplier, IntSupplier, LongSupplier, DoubleSupplier|
|`UnaryOperator<T>`|T -> T|IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator|
|`BinaryOperator<T>`|(T, T) -> T|IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator|
|`BiPrediate<L, R>`|(T, U) -> boolean|
|`BiConsumer<T, U>`|(T, U) -> void|`ObjIntConsumer<T>`, `ObjLongConsumer<T>`, `ObjDoubleConsumer<T>`|
|`BiFunction<T, U, R>`|(T, U) -> R|ToIntBiFunction<T, U>, ToLongBiFunction<T, U>, ToDoubleBiFunction<T, U>|

> 람다와 함수형 인터페이스 예제

|사용사례|람다 예제|대응하는 함수형 인터페이스
|-------|--------|-------------|
|불리언 표현|`(List<String> list) -> list.isEmpty()`|`Predicate<List<String>>`|
|객체 생성|`() -> new Apple(10)`|`Supplier<Apple>`|
|객체에서 소비|`(Apple a) -> System.out.println(a.getWeight())`|`Consumer<Apple>`|
|객체에서 선택/추출|`(String s) -> s.length()`|`Function<String, Integer>` 또는 `ToIntFunction<String>`|
|두 값 조합|`(int a, int b) -> a * b`|`IntBinaryOperator`|
|두 객체 비교|`(Apple a1, Apple a2) -> a1.getWeight.compareTo(a2.getWeight())`|`IntBinaryOperator`, `Comparator<Apple>`, `BiFunction<Apple, Apple, Integer>`, `ToIntBiFunction<Apple, Apple>`

- 형식 검사, 형식 추론, 제약
  - 람다로 함수형 인터페이스의 인스턴스를 만들 수 있다. 람다 표현식 자체에는 람다가 어떤 함수형 인터페이스를 구현하는지 정보가 포함되어 있지 않다.
  - 형식 검사: 람다가 사용되는 콘텍스트(context)를 이용해서 람다의 형식(type)을 추론할 수 있다. 어떤 콘텍스트에서 기대되는 람다 표현식의 형식을 대상 형식(target type)이라고 부른다.
  - 형식 추론: 자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 즉 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다. 결과적으로 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생랼할 수 있다.

```java
// 형식을 추론하지 않음
Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

// 형식을 추론함
Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
```
  
  - 지역 변수 사용: 지금까지 살펴본 모든 람다 표현식은 인수를 자신의 바디 안에서만 사용했다. 하지만 람다 표현식에서는 익명 함수가 하는 것처럼 자유 변수(free variable, 파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있다. 이와 같은 동작을 람다 캡쳐링(capturing lambda)이라고 부른다.
    - 명시적으로 final로 선언되어 있어야 하거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 한다. 즉, 람다 표현식은 한 번만 할당할 수 있는 지역 변수를 캡쳐할 수 있다.
  - 지역 변수 제약
    - 내부적으로 인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치한다.
    - 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다. 따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것이다.
    
#### 메소드 참조

- 특정 람다 표현식을 축약한 것
- 하나의 메서드를 참조하는 람다를 편리하게 표현할 수 있는 문법 

```java
// 위, 아래 동일한 코드 (메서드 참조 여부 차이)
inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
inventory.sort(Comparator.comparing(Apple::getWeight));

Function<Apple, Integer> function = (Apple apple) -> apple.getWeight();
Function<Apple, Integer> getWeight = Apple::getWeight;

Thread.dumpStack();
Runnable stack = Thread::dumpStack;

BiFunction<String, Integer, String> function1 = (String str, Integer i) -> str.substring(i);
BiFunction<String, Integer, String> function2 = String::substring;
```

- 생성자 참조: `ClassName::new`처럼 클래스명과 new 키워드를 이용한다.

```java
Supplier<Apple> s = () -> new Apple();
Apple a2 = s.get();

Supplier<Apple> supplier = Apple::new;
Apple a1 = supplier.get();

BiFunction<Integer, Colors, Apple> f = (weight, color) -> new Apple(weight, color);
Apple a4 = f.apply(20, RED);

BiFunction<Integer, Colors, Apple> function = Apple::new;
Apple a3 = function.apply(10, GREEN);
```

#### 람다 표현식을 조합할 수 있는 유용한 메서드

- 자바 8 API의 몇몇 함수형 인터페이스는 다양한 유틸리티 메서드를 포함한다. 예를 들어 Comparator, Function, Predicate 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있도록 유틸리티 메서드를 제공한다.
  - 예를 들어 두 프레디케이트를 조합해서 두 프레디케이트의 or 연산을 수행하는 커다란 프레디케이트를 만들 수 있다. 또한 한 함수의 결과가 다른 함수의 입력이 되도록 두 함수를 조합할 수 있다.
  - 함수형 인터페이스는 디폴트 메소드(default method)를 이용해 유틸리티 메서드를 제공한다.
- Comparator 조합: revresed, thenComparing
- Predicate 조합: negate(반전), and, or
- Function 조합: andThen, compose

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
// g(f(x))
Function<Integer, Integer> h = f.andThen(g);
int result = h.apply(1);
System.out.println(result);  // 4 출력

// f(g(x))
Function<Integer, Integer> h = f.compose(g);
int result = h.apply(1);
System.out.println(result);  // 3 출력
```

## Part2 함수형 데이터 처리

### Chapter4. 스트림 소개

#### 스트림이란 무엇인가?

- 스트림(Streams)을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있다. 또한 스트림을 이용하면 멀티스레드 코드를 구현하지 않아도 데이터를 병렬로 처리할 수 있다.
