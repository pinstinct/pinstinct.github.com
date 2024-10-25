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
    - 에러의 근원이다: NullPointerException은 자바에서 가장 흔히 발생하는 에러다.
    - 코드를 어지럽힌다: 중첩된 null 확인 코드를 추가해야 하므로 코드 가독성이 떨어진다.
    - 아무 의미가 없다
    - 자바 철학에 위배된다: 자바는 개발자로부터 모든 포인터를 숨겼다. 하지만 예외가 있는데 그것이 바로 null 포인터다.
    - 형식 시스템에 구멍을 만든다: null은 무형식이며 정보를 포함하고 있지 않으므로 모든 참조 형식에 null을 할당할 수 있다.

#### Optional 클래스 소개

- Optional은 선택형값을 캡슐화하는 클래스다.
- Optional.empty는 Optional의 특별한 싱글턴 인스턴스를 반환하는 정적 팩토리 메서드다.
- 모든 null 참조를 Optional로 대치하는 것은 바람직하지 않다. Optional 역할은 더 이해하기 쉬운 API를 설계하도록 돕는 것이다. 즉, 메서드의 시그니처만 보고도 선택형값인지 여부를 구별할 수 있다.

#### Optional 적용 패턴 

- Optional 객체 만들기

```java
// 빈 Optional
Optional<Car> car = Optional.empty();
// result: Optional.empty

//null이 아닌 값으로 Optional 만들기
Car car = new Car();
Optional<Car> optCar = Optional.of(car);

// null 값으로 Optional 만들기
Car car = null;
Optional<Car> optCar = Optional.ofNullable(car);
// result: Optional.empty
```

- `map`으로 Optional의 값을 추출하고 변환하기
    - 스트림의 map 메서드와 개념적으로 비슷하다. 스트림의 각 요소에 제공된 함수를 적용하는 연산이다.
    - Optional 객체는 최대 요소의 개수가 한 개 이하인 데이터 컬렉션으로 생각할 수 있다. Optional이 값을 포함하면 map의 인수로 제공된 함수가 값을 바꾼다. 비어있으면 아무 일도 일어나지 않는다.

```java
Insurance insurance = new Insurance();
Optional<Insurance> optionalInsurance = Optional.ofNullable(insurance);
Optional<String> name = optionalInsurance.map(Insurance::getName);
```

- `flatMap`으로 Optional 객체 연결

    ```java
    Optional<Person> optionalPerson = Optional.of(person);
    optionalPerson.map(Person::getCar)
        .map(Car::getInsurance)  // 컴파일 에러, map의 연산결과가 Optional<Optional<Car>> 형식을 반환하기 때문
        .map(Insurance::getName);
    ```

    - 보통 map의 인수로 받은 함수를 스트림의 각 요소에 적용하면 스트림의 스트림이 만들어진다. 하지만 flatMap은 인수로 받은 함수를 적용해서 생성된 각각의 스트림에서 콘텐츠만 남긴다. 즉, 함수를 적용해서 생성된 모든 스트림이 하나의 스트림으로 병합되어 평준화된다.

    ```java
    public String getCarInsuranceName(Optional<Person> person) {
      return person.flatMap(Person::getCar)
          .flatMap(Car::getInsurance)
          .map(Insurance::getName)
          .orElse("Unknown");
    }
    ```

- 도메인 모델에 Optional을 사용했을 때 데이터를 직렬화할 수 없는 이유
  - Optional의 용도는 선택형 반환값을 지원하는 것이다.
  - Optional 클래스는 필드 형식으로 사용할 것을 가정하지 않았으므로 Serializable 인터페이스를 구현하지 않는다. 따라서 우리 도메인 모델에 Optional을 사용한다면 직렬화 모델을 사용하는 도구나 프레임워크에서 문제가 생길 수 있다.

- Optional 스트림 조작

```java
public Set<String> getCarInsuranceNames(List<Person> persons) {
  return persons.stream()
      // 사람 목록을 각 사람이 보유한 Optional<Car> 스트림으로 변환
      .map(Person::getCar)
      // flatMap 연산을 이용해 Optional<Car>을 해당 Optional<Insurance>로 변환
      .map(optCar -> optCar.flatMap(Car::getInsurance))
      // Optional<Insurance>를 해당 이름의 Optional<String>으로 매핑
      .map(optIns -> optIns.map(Insurance::getName))
      // Stream<Optional<String>>을 현재 이름을 포함하는 Stream<String>으로 변환
      .flatMap(Optional::stream)
      // 결과 문자열을 중복되지 않도록 집합으로 수집
      .collect(Collectors.toSet());
}
```

  - Optional 덕분에 이런 종류의 연산을 null 걱정없이 안전하게 처리할 수 있지만 마지막 결과를 얻으려면 빈 Optional을 제거하고 값을 언랩해야 한다는 것이 문제다.

- 디폴트 액션과 Optional 언랩
  - `get()`: 가장 간단한 메서드이면서 가장 안전하지 않은 메서드다.
  - `orElse(T other)`: Optional이 값을 포함하지 않을 때 기본값을 제공
  - `orElseGet(Supplier<? extends T>)`: `orElse` 메서드의 게으른 버전 메서드다. Optional 값이 없을 때만 Supplier가 실행되기 때문이다.
  - `orElseThrow(Supplier<? extends X> exceptionSupplier)`: Optional이 비어있을 때 예외를 발생시킨다.
  - `ifPresent(Consumer<? super T> consumer)`: 값이 존재할 때 인수로 넘겨준 동작을 실행한다. 값이 없으면 아무 일도 일어나지 않는다.
  - `ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)`: Optional이 비었을 때 실행할 수 있는 Runnalbe을 인수로 받는다는 점이 ifPresent와 다르다.

- 두 Optional 합치기

```java
public Optional<Insurance> nullSafeFindCheapestInsurance(Optional<Person> person, Optional<Car> car) {
  // null 확인 코드와 크게 다른 점이 없다.
  if (person.isPresent() && car.isPresent()) {
    return Optional.of(findCheapestInsurance(person.get(), car.get()));
  } else {
    return Optional.empty();
  }
}

// 위의 nullSafeFindCheapestInsurance 메서드 리팩토링
public Optional<Insurance> nullSafeFindCheapestInsurance2(Optional<Person> person, Optional<Car> car) {
  return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
}
```

- 필터로 특정값 거르기

```java
Insurance insurance = new Insurance();
if (insurance != null && "CambridgeInsurance".equals(insurance.getName())) {
  System.out.println("ok");
}

// 위의 코드 Optional 객체로 구현
Optional<Insurance> optionalInsurance = Optional.of(new Insurance());
optionalInsurance.filter(ins -> "CambridgeInsurance".equals(ins.getName()))
    .ifPresent(x -> System.out.println("ok"));
```

- Optional 클래스의 메서드
  - empty: 빈 Optional 인스턴스 반환
  - filter: 값이 존재하며 프레디케이트와 일치하면 값을 포함하는 Optional을 반환하고, 값이 없으면 빈 Optional을 반환
  - flapMap: 값이 존재하면 인수로 제공된 함수를 적용한 결과 Optional을 반환하고, 값이 없으면 빈 Optional을 반환
  - get: 값이 존재하면 Optional이 감싸고 있는 값을 반환하고, 값이 없으면 NoSuchElementException 발생
  - isPresent: 값이 존재하면 true를 반환하고, 값이 없으면 false를 반환
  - map: 값이 존재하면 제공된 매핑 함수를 적용
  - of: 값이 존재하면 값을 감싸는 Optional을 반환하고, 값이 null이면 NullPointerException을 반환
  - ofNullable: 값이 존재하면 값을 감싸는 Optional을 반환하고, 값이 null이면 빈 Optional을 반환
  - or: 값이 존재하면 같은 Optional을 반환하고, 값이 없으면 Supplier에서 만든 Optional을 반환
  - stream: 값이 존재하면 존재하는 값만 포함하는 스트림을 반환하고, 값이 없으면 빈 스트림 반환

#### Optional을 사용한 실용 예제

- 잠재적으로 null이 될 수 있는 대상을 Optional로 감싸기: 기존의 자바 API에서는 null을 반환하면서 요청한 값이 없거나 어떤 문제로 계산에 실패했음을 알린다. 예를 들어 Map의 get 메서드는 요청한 키에 대흥하는 값을 찾지 못했을 때 null을 반환한다.

```java
Object value = map.get("key");
// result: null

Optional<Object> value = Optional.ofNullable(map.get("key"));
// result: Optional.empty
```

- 예외와 Optional 클래스: 자바 API는 어떤 이유에서 값을 제공할 수 없을 때 null을 반환하는 대신 예외를 발생시킬 때도 있다. 예를 들어 문자열을 정수로 변환하는 정적 메서드 Integer.pareseInt(String)다. 이 메서드는 문자열을 정수로 바꾸지 못할 때 NumberFormatException을 발생시킨다. 기존에 값이 null일 수 있을 때는 if문으로 null 여부를 확인했지만 예외를 발생시키는 메서드에서는 try/catch 블록을 사용해야 한다는 점이 다르다.

- 기본형 Optional을 사용하지 말아야 하는 이유: 스트림처럼 Optional도 기본형으로 특화된 OptionalInt, OptionalLong, OptionalDouble 등의 클래스를 제공한다. Optional의 최대 요소 수는 한 개이므로 Optional에서는 기본형 특화 클래스로 성능을 개선할 수 없다. 또한, 앞서 살펴본 Optional 클래스의 유용한 메서드 map, flatMap, filter등을 지원하지 않으므로 기본형 특화 Optional을 사용할 것을 권장하지 않는다.
