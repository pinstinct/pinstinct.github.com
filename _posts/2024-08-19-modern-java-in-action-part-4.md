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

### Chapter12. 새로운 날짜와 시간 API

- 자바 1.0에서는 java.util.Date 클래스 하나로 날짜와 시간 관련 기능을 제공했다. Date 클래스는 이름과 달리 특정 시점을 날짜가 아닌 밀리초 단위로 표현한다. 게다가 1900년을 기준으로 하는 오프셋, 0에서 시작하는 달 인덱스 등 모호한 설계로 유용성이 떨어졌다.
- 자바 1.1에서는 Date 클래스의 여러 메서드를 사장(deprecated)시키고 java.util.Calendar라는 클래스를 대안으로 제공했지만 이 역시 쉽게 에러를 일으키는 설계 문제를 갖고 있었다. 달의 인덱스는 여전히 0부터 시작했다. 게다가 DateFormat 같은 일부 기능은 Date 클래스에만 작동했다.
- DateFomat에도 문제가 있었다. DateFormat은 스레드에 안전하지 않다. 즉, 두 스레드가 동시에 하나의 포매터(fommater)로 날짜를 파싱할 때 예기치 못한 결과가 일어날 수 있다.
- Date와 Calendar 모두 가변 클래스다.
- 결국 자바8에서는 Joda-Time의 많은 기능을 java.time 패키지로 추가했다.

#### LocalDate, LocalTime, Instant, Duration, Period 클래스

- LocalDate와 LocalTime 사용
  - LocalDate 인스턴스는 시간을 제외한 날짜를 표현하는 불변 객체다. 특히 LocalDate 객체는 어떤 시간대 정보도 포함하지 않는다.

  - LocalDate 만들고 값 읽기
  
  ```java
  LocalDate date = LocalDate.of(2017, 9, 21);
  int year = date.getYear();
  Month month = date.getMonth();
  int day = date.getDayOfMonth();
  DayOfWeek dayOfWeek = date.getDayOfWeek();
  int len = date.lengthOfMonth();  // 9월의 일수 (30일)
  boolean leap = date.isLeapYear();  // 윤년 여부
  ```

  - 팩토리 메서드 now는 시스템 시계의 정보를 이용해서 현재 날짜 정보를 얻는다.

  ```java
  LocalDate today = LocalDate.now();
  ```

  - LocalTime 만들고 값 읽기

  ```java
  LocalTime time = LocalTime.of(13, 45, 20);    
  int hour = time.getHour();
  int minute = time.getMinute();
  int second = time.getSecond();
  ```

  - `parse` 정적 메서드를 이용해 날짜와 시간 문자열로 LocalDate와 LocalTime의 인스턴스 생성
    - 파싱할 수 없을 때 parse 메서드는 DateTimeParseException을 일으킨다.

  ```java
  LocalDate date = LocalDate.parse("2017-09-21");
  LocalTime time = LocalTime.parse("13:34:20");
  ```

  - DateTimeFormatter는 java.util.DateFormat을 대체하는 클래스다.

- 날짜와 시간 조합: LocalDateTime을 직접 만드는 방법과 날짜와 시간을 조합하는 방법

```java
LocalDate date = LocalDate.parse("2017-09-21");
LocalTime time = LocalTime.parse("13:34:20");

LocalDateTime dt1 = LocalDateTime.of(2017, SEPTEMBER, 21, 13, 45, 20);
LocalDateTime dt2 = LocalDateTime.of(date, time);
LocalDateTime dt3 = date.atTime(13, 45, 20);
LocalDateTime dt4 = date.atTime(time);
LocalDateTime dt5 = time.atDate(date);

LocalDate date1 = dt1.toLocalDate();
LocalTime time1 = dt1.toLocalTime();
```

- Instant 클래스: 기계의 날짜와 시간
  - 사람은 보통 주, 날짜, 시간, 분으로 날짜와 시간을 계산한다. 하만 기계에서는 이와 같은 단위로 시간을 표현하기가 어렵다.
  - 기계의 관점에서 연속된 시간에서 특정 지점을 하나의 큰 수로 표현하는 것이 가장 자연스러운 시간 표현 방법이다. 
  - java.time.Instant 클래스에서는 이와 같은 기계적인 관점에서 시간을 표현한다. 즉, Instant 클래스는 유닉스 에포크 시간(1970년 1월 1일 0시 0분 0초 UTC)을 기준으로 특정 시점까지의 시간을 초로 표현한다.

  ```java
  // Instant 클래스는 나노초(10억분의 1초)의 정밀도 제공
  Instant instant = Instant.ofEpochSecond(3);
  Instant instant1 = Instant.ofEpochSecond(3, 0);
  Instant instant2 = Instant.ofEpochSecond(2, 1_000_000_000);// 2초 이후의 1억 나노초(1초)
  Instant instant3 = Instant.ofEpochSecond(4, -1_000_000_000);// 4초 이전의 1억 나노초(1초)
  ```

- Duration과 Period 정의
  - LocalTime, LocalDate, LocalDateTime, Instant 클래스는 Temporal 인터페이스를 구현하는데, Temporal 인터페이스는 특정 시간을 모델링하는 객체의 값을 어떻게 읽고 조작할지 정의한다.
  - Duration 클래스의 정적 팩토리 메서드 between으로 두 시간 객체 사이의 지속시간을 만들 수 있다.
  - LocalDateTime과 Instant는 서로 혼합할 수 없다.
  - Duration 클래스는 초와 나노초로 시간 단위를 표현하므로 between 메서드에 LocalDate를 전달할 수 없다. 대신 Period 클래스를 사용한다.

  ```java
  LocalTime time1 = LocalTime.of(13, 45, 20);
  LocalTime time2 = LocalTime.of(13, 45, 20);
  Duration d1 = Duration.between(time1, time2);

  LocalDateTime dateTime1 = LocalDateTime.now();
  LocalDateTime dateTime2 = LocalDateTime.of(2013, 2, 3, 13, 22, 11);
  Duration d3 = Duration.between(dateTime1, dateTime2);

  Instant instant1 = Instant.ofEpochSecond(44 * 365 * 86400);
  Instant instant2 = Instant.now();
  Duration d2 = Duration.between(instant1, instant2);

  // 년, 월, 일로 시간을 표현할 때는 Period 클래스 사용 
  LocalDate date1 = LocalDate.of(2017, 9, 11);
  LocalDate date2 = LocalDate.of(2017, 9, 21);
  Period days = Period.between(date1, date2);

  // 두 시간 객체를 사용하지 않고 생성
  Duration threeMinutes = Duration.ofMinutes(3);
  Duration threeMinutes1 = Duration.of(3, ChronoUnit.MINUTES);

  Period tenDays = Period.ofDays(10);
  Period threeWeeks = Period.ofWeeks(3);
  Period twoYearsSixMonthsOneDay = Period.of(2, 6, 1);
  ```

- 간격을 표현하는 날짜와 시간 클래스의 공통 메서드

|메서드|정적|설명|
|-----|---|----|
|between|네|두 시간 사이의 간격을 생성함|
|from|네|시간 단위로 간격을 생성함|
|of|네|주어진 구성 요소에서 간격 인스턴스를 생성함|
|parse|네|문자열을 파싱해서 간격 인스턴스를 생성함|
|addTo|아니오|현재값의 복사본을 생성한 다음에 지정된 Temporal 객체에 추가함|
|get|아니오|현재 간격 정보값을 읽음|
|isNegative|아니오|간격이 음수인지 확인함|
|isZero|아니오|간격이 0인지 확인함|
|minus|아니오|현재값에서 주어진 시간을 뺀 복사본을 생성함|
|multipliedBy|아니오|현재값에 주어진 값을 곱한 복사본을 생성함|
|negated|아니오|주어진 값의 부호를 반전한 복사본을 생성함|
|plus|아니오|현재값에 주어진 시간을 더한 복사본을 생성함|
|subtractFrom|아니오|지정된 Temporal 객체에서 간격을 뺌|

- 지금까지 살펴본 모든 클래스는 불변이다. 불변 클래스는 함수형 프로그래밍 그리고 스레드 안전성과 도메인 모델의 일관성을 유지하는 데 좋은 특징이다.

#### 날짜 조정, 파싱, 포매팅

- withAttribute 메서드로 기존의 LocalDate를 바꾼 버전을 직접 간단하게 만들 수 있다.

```java
// 절대적인 방식으로 LocalDate의 속성 바꾸기
LocalDate date1 = LocalDate.of(2017, 9, 2);
LocalDate date2 = date1.withYear(2011);  // 2011-09-02
LocalDate date3 = date2.withDayOfMonth(25);  // 2011-09-25
LocalDate date4 = date3.with(ChronoField.MONTH_OF_YEAR, 2);  // 2011-02-25
```

- 마지막 with 메서드는 get 메서드(`int year = LocalDate.of(2024, 1, 1).get(ChronoField.YEAR);`)와 쌍을 이룬다. 
  - get과 with 메서드로 Temporal 객체의 필드값을 읽거나 고칠 수 있다.
  - with 메서드는 기존의 Temporal 객체를 바꾸는 것이 아니라 필드를 갱신한 복사본을 만든다. 이런 과정을 함수형 갱신이라고 부른다.

```java
// 상대적인 방식으로 LocalDate의 속성 바꾸기
LocalDate date1 = LocalDate.of(2017, 9, 21);
LocalDate date2 = date1.plusWeeks(1);  // 2017-09-28
LocalDate date3 = date2.minusYears(6);  // 2011-09-28
LocalDate date4 = date3.plus(6, ChronoUnit.MONTHS);  // 2012-03-28
```

- 특정 시점을 표현하는 날짜 시간 클래스의 공통 메서드

|메서드|정적|설명|
|-----|---|----|
|from|네|주어진 Temporal 객체를 이용해서 클래스의 인스턴스를 생성함|
|now|네|시스템 시계로 Temporal 객체를 생성함|
|of|네|주어진 구성 요소에서 Temporal 객체의 인스턴스를 생성함|
|parse|네|문자열을 파싱해서 Temporal 객체를 생성함|
|atOffset|아니오|시간대 오프셋과 Temporal 객체를 합침|
|atZone|아니오|시간대 오프셋과 Temporal 객체를 합침|
|format|아니오|지정된 포매터를 이용해서 Temporal 객체를 문자열로 변환함(Instatnt는 지원하지 않음)|
|get|아니오|Temporal 객체의 상태를 읽음|
|minus|아니오|특정 시간을 뺀 Temporal 객체의 복사본을 생성함|
|plus|아니오|특정 시간을 더한 Temporal 객체의 복사본을 생성함|
|with|아니오|일부 상태를 바꾼 Temporal 객체에서 복사본을 생성함|

- TemporalAdjusters 사용하기
  - 좀 더 복잡한 날짜 조정 기능이 필요할때는 오버로드된 버전의 with 메서드에 좀 더 다양한 동작을 수행할 수 있도록 하는 기능을 제공하는 TemporalAdjuster를 전달하는 방법으로 문제를 해결할 수 있다.
  - 날짜와 시간 API는 다양한 상황에서 사용할 수 있도록 다양한 TemporalAdjuster를 제공한다.  
    - TemporalAdjuster는 인터페이스이며, TemporalAdjusters는 여러 TemporalAdjuster를 반환하는 정적 팰토리 메서드를 포함하는 클래스이므로 혼돈하지 않도록 주의하자. 둘 다 java.time.temporal 패키지에 포함되어 있다.

  ```java
  // TemporalAdjusters에서 정의한 정적 팩토리 메서드 
  LocalDate date1 = LocalDate.of(2014, 3, 18);
  LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY));
  LocalDate date3 = date2.with(lastDayOfMonth());
  ```

  - TemporalAdjusters 클래스의 팩토리 메서드
    - dayOfWeekInMonth: n번째 요일의 날짜를 반환하는 TemporalAdjuster를 반환
    - firstDayOfMonth: 현재 달의 첫 번째 날짜를 반환하는 TemporalAdjuster를 반환
    - firstDayOfNextMonth: 다음 달의 첫 번째 날짜를 반환하는 TemporalAdjuster를 반환
    - firstDayOfNextYear: 내년의 첫 번째 날짜를 반환하는 TemporalAdjuster를 반환 
    - firstDayOfYear: 올해의 첫 번째 날짜를 반환하는 TemporalAdjuster를 반환
    - firstInMonth: 현재 달의 첫 번째 요일에 해당하는 날짜를 반환
    - lastDayOfMonth: 현재 달의 마지막 날짜를 반환하는 TemporalAdjuster를 반환
    - lastDayOfYear: 올해의 마지막 날짜를 반환하는 TemporalAdjuster를 반환
    - lastInMonth: 현재 달의 마지막 날짜를 반환하는 TemporalAdjuster를 반환
    - previous, next: 현재 날짜 이전/이후로 지정한 요일이 처음으로 나타나는 날짜를 반환하는 TemporalAdjuster를 반환
    - previousOrSame, nextOrSame: 현재 날짜 이전/이후로 지정한 요일이 처음으로 나타나는 날짜를 반환하는 TemporalAdjuster를 반환(현재 날짜도 포함)
  - 필요한 기능이 정의되어 있지 않을때 쉽게 커스텀 TemporalAdjuster 구현을 만들 수 있다.

  ```java
  @FunctionalInterface
  public interface TemporalAdjuster {
    Temporal adjustInto(Temporal temporal);
  }
  ```

- 날짜와 시간 객체 출력과 파싱
  - 포매팅과 파싱 전용 패키지인 java.time.format이 새로 추가되었다. 이 패키지에서 가장 중요한 클래스는 DateTimeFormatter다.

  ```java
  LocalDate date = LocalDate.of(2014, 3, 18);

  // DateTimeFormatter 이용해 날짜와 시간을 특정 형식의 문자열로 만들기
  String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE);
  String s2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE);

  // 날짜와 시간을 표현하는 문자열을 파싱해 날짜 객체로 만들기
  LocalDate date1 = LocalDate.parse("20140318", DateTimeFormatter.BASIC_ISO_DATE);
  LocalDate date2 = LocalDate.parse("2014-03-18", DateTimeFormatter.ISO_LOCAL_DATE);
  ```

  - 기존의 java.util.DateFormat 클래스와 달리 모든 DateTimeFormatter는 스레드에서 안전하게 사용할 수 있는 클래스다.

  ```java
  // 패턴으로 DateTimeFormatter 만들기
  DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
  LocalDate date1 = LocalDate.of(2014, 3, 18);
  String formatted = date1.format(formatter);
  LocalDate date2 = LocalDate.parse(formatted, formatter);

  // 지역화된 DateTimeFormatter 만들기
  DateTimeFormatter formatter = DateTimeFormatter.ofPattern("d. MMMM yyyy", Locale.ITALIAN);
  LocalDate date1 = LocalDate.of(2014, 3, 18);
  String formatted = date1.format(formatter);
  LocalDate date2 = LocalDate.parse(formatted, formatter);

  // DateTimeFormatterBuilder 클래스로 복합적인 포매터를 정의
  DateTimeFormatter formatter = new DateTimeFormatterBuilder()
    .appendText(ChronoField.DAY_OF_MONTH)
    .appendLiteral(". ")
    .appendText(ChronoField.MONTH_OF_YEAR)
    .appendLiteral(" ")
    .appendText(ChronoField.YEAR)
    .parseCaseInsensitive()
    .toFormatter(Locale.ITALIAN);
  ```

#### 다양한 시간대와 캘린더 활용 방법

- 기존의 java.util.TimeZone을 대체할 수 있는 java.time.ZoneId 클래스가 새롭게 등장했다. 새로운 클래스를 이용하면 서머타임(Daylight Saving Time) 같은 복잡한 사항이 자동으로 처리된다.
- 날짜와 시간 API에서 제공하는 다른 클래스와 마찬가지로 ZoneId는 불변 클래스다.
- 시간대 사용하기
  - 표준 시간이 같은 지역을 묶어서 시간대(time zone) 규칙 집합을 정의한다.
  - ZoneId 객체를 얻은 다음에 LocalDate, LocalDateTime, Instant를 이용해서 ZonedDateTime 인스턴스로 변환할 수 있다.
  - (LocalDate 또는 LocalTime 또는 LocalDateTime) + ZoneId = ZonedDateTime

  ```java
  // IANA Time Zone Database에서 제공하는 지역 집합 정보 사용
  ZoneId romeZone = ZoneId.of("Europe/Rome");  // "지역/도시" 형식

  // 기존의 TimeZone 객체를 ZoneId 객체로 변환
  ZoneId zoneId = TimeZone.getDefault().toZoneId();

  // 특정 시점에 시간대 적용
  LocalDate date = LocalDate.of(2014, Month.MARCH, 18);
  ZonedDateTime zdt1 = date.atStartOfDay(romeZone);
  LocalDateTime dateTime = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45);
  ZonedDateTime zdt2 = dateTime.atZone(romeZone);
  Instant instant = Instant.now();
  ZonedDateTime zdt3 = instant.atZone(romeZone);
  ```

#### UTC/Greenwich 기준의 고정 오프셋

- 때로는 UTC(Universal Time Coordinated, 협정 세계시)/GMT(Greenwich Mean Time, 그리니치 표준시)를 기준으로 시간대를 표현하기도 한다. 예를 들어 '뉴욕은 런던보다 5시간 느리다'라고 표현할 수 있다. 

```java
// ZoneOffset으로는 서머타임을 제대로 처리할 수 없으므로 권장하지 않는 방식
ZoneOffset newYorkOffset = ZoneOffset.of("-05:00");

// OffsetDateTime: ISO-8601 캘린더 시스템에서 정의하는 UTC/GMT와 오프셋으로 날짜와 시간을 표현
LocalDateTime dateTime = LocalDateTime.of(2014, MARCH, 18, 13, 45);
OffsetDateTime offsetDateTime = OffsetDateTime.of(dateTime, newYorkOffset);
```
