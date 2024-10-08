---
layout: post
title: 모던 자바 인 액션
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
  - 동적 파라미터화가 중요한 이유는 스트림 API는 연산의 동작을 파라미터화할 수 있는 코드를 전달한다는 사상에 기초하기 때문이다.
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

- 동적 파라미터화: 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미
- 전략 패턴: 각 알고리즘(전략이락 불리는)을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에 런타임에 알고리즘을 선택하는 기법이다.

```java
interface ApplePredicate {  // 선택 조건을 결정하는 인터페이스 정의(알고리즘 패밀리)

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

- 동작 파라미터화를 통해 메서드가 다양한 동작(또는 전략)을 받아서 내부적으로 다양한 동작을 수행할 수 있다.
- 컬렉션 탐색 로직과 각 항목에 적용할 동작을 분리할 수 있다는 것이 동작 파라미터화의 강점이다.

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

- 복잡한 과정 간소화를 위해 람다 표현식 사용

```java
// 람다 표현식 사용
List<Apple> heavyApples2 = filterApples(inventory, (Apple a) -> a.getWeight() > 150);

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
List<Apple> result = new ArrayList<>();
for (Apple apple : inventory) {
    if (p.test(apple)) {
    result.add(apple);
    }
}
return result;
}
```

- 리스트 형식으로 추상화 

```java
public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> result = new ArrayList<>();
    for (T e: list) {
      if (p.test(e)) {
        result.add(e);
      }
    }
    return result;
}

List<Apple> redApples = filter(inventory, (Apple apple) -> Color.RED.equals(apple.getColor()));
List<Integer> evenNumbers = filter(List.of(1, 2, 3, 4, 5), (Integer i) -> i % 2 == 0);
```

### Chapter3. 람다 표현식 

- 람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다. 람다 표현식에 이름은 없지만 파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트를 가질 수 있다.
- 람다의 특징
  - 익명
  - 함수: 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다.
  - 전달: 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
  - 간결성: 익명 클래스 처럼 많은 자질구레한 코드를 구현할 필요가 없다.

