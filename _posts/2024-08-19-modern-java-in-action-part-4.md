---
layout: post
title: 모던 자바 인 액션 - Part4
categories: book
tags: [book, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987) 책을 읽고 정리한 내용입니다.

> [Repository](https://github.com/pinstinct/java-in-action)

## Part4 매일 자바와 함께

### Chapter11. null 대신 Optional 클래스

#### 값이 없는 상황을 어떻게 처리할까?

- 아래와 같은 데이터 모델이 있다.

```java
public class Person {

  private Car car;

  public Car getCar() {
    return car;
  }
}

public class Car {

  private Insurance insurance;

  public Insurance getInsurance() {
    return insurance;
  }
}

public class Insurance {

  private String name;

  public String getName() {
    return name;
  }
}
```

- 아래의 코드는 어떤 문제가 발생할까?
    - 코드에 문제가 없는 것처럼 보이지만, 차를 소유하지 않은 사람도 많다. 이 때, getCar()를 호출하면 NullPointException이 발생한다.런타임에 NullPointException이 발생하면 프로그램 실행이 중단된다.

```java
public String getCarInsuranceName(Person person1) {
return person.getCar().getInsurance().getName();
}
```

- 보수적인 자세로 NullPointException 줄이기: 대부분의 프로그래머는 필요한 곳에 다양한 null 확인 코드를 추가해서 null 예외 문제를 해결하려 할 것이다. 하지만 중첩된 if가 추가되면서 코드 들여쓰기 수준이 증가한다. 이와 같은 반복 패턴(recurring pattern) 코드를 깊은 의심(deep doubt)이라고 부른다.
- null 때문에 발생하는 문제
    - 

