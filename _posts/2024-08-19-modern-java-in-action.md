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

- 일급 값은 프로그램을 실행하는 동안 전달할 수 있다. 전달할 수 없는 구조체는 이급 값이다.
  - 메서드, 람다(익명 함수)도 일급값
  - 람다가 몇 줄 이상으로 길어진다면 익명 람다 보다는 코드가 수행하는 일을 잘 설명하는 이름을 가진 메서드를 정의하고 메서드 참조를 활용하는 것이 바람직하다. 코드의 명확성이 우선시되어야 한다. 
- 프레디케이트(predicate): 수학에서 인수로 값을 받아 true나 false로 반환하는 함수를 프레디케이트라고 한다. 
- 스트림 API(java.util.stream) 병렬성 
  - 멀티스레딩은 어렵다. 각 스레드에는 동시에 공유된 데이터에 접근하고, 갱신할 수 있기 때문에 잘 제어하지 못하면 원치 않는 방식으로 데이터가 바뀔 수 있다.
  - 스트림 API는 '컬렉션을 처리하면서 발생하는 모호함과 반복적인 코드 문제' 그리고 '멀티코어 활용 어려움'이라는 두 가지 문제를 모두 해결했다. 
  - 컬렉션을 필터링할 수 있는 가장 빠른 방법은 컬렉션을 스트림으로 바꾸고, 병렬로 처리한 다음에, 리스트로 다시 복원하는 것이다.
- 디폴트 메서드: 인터페이스를 쉽게 바꾸도록 디폴트 메서드가 생겼다. 디폴트 메서드는 특정 프로그램을 구현하는 데 도음을 주는 기능이 아니라 미래에 프로그램이 쉽게 변화할 수 있는 환경을 제공하는 기능이다.
- Optional<T> 클래스로 NullPointer 예외 회피

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


