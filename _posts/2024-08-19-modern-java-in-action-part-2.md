---
layout: post
title: 모던 자바 인 액션 - Part2
categories: book
tags: [book, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987) 책을 읽고 정리한 내용입니다.

> [Repository](https://github.com/pinstinct/java-in-action)

## Part2 함수형 데이터 처리

### Chapter4. 스트림 소개

#### 스트림이란 무엇인가?

- 스트림(Streams)을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있다. 또한 스트림을 이용하면 멀티스레드 코드를 구현하지 않아도 데이터를 병렬로 처리할 수 있다.
- 스트림의 새로운 기능이 소프트웨어공학적으로 주는 이득
    - 선언형으로 코드를 구현할 수 있다.
    - filter, sorted, map, collect 같은 여러 빌딩 블록 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있다. 여러 연산을 파이프라인으로 연결해도 여전히 가독성과 명확성이 유지된다.
- filter(또는 sorted, map, collect) 같은 연산은 **고수준 빌딩 블록(high-level building block)**으로 이루어져 있으므로 특정 스레딩 모델에 제한되지 않고 자유롭게 어떤 상황에서든 사용할 수 있다. 또한 이들은 내부적으로 단일 스레드 모델에 사용할 수 있지만 멀티코어 아키텍처를 최대한 투명하게 활용할 수 있게 구현되어 있다. 결과적으로 데이터 처리 과정을 병렬화하면서 스레드와 락을 걱정할 필요가 없다.
- 스트림 API의 특징
    - 선언형: 더 간결하고 가독성이 좋아진다.
    - 조립할 수 있음: 유연성이 더 좋아진다.
    - 병렬화: 성능이 좋아진다.

#### 스트림 시작하기

- 자바 8 컬렉션에는 스트림을 반환하는 stream 메서드가 추가됐다.
- 스트림이란 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소(Sequence of elements)'로 정의할 수 있다.
    - 연속된 요소: 컬렉션과 마찬가지로 스트림은 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공한다. 컬렉션은 자료구조이므로 (예를 들어 ArrayList를 사용할 것인지 아니면 LinkedList를 사용할 것인지에 대한) 컬렉션에서는 시간과 공간의 복잡성과 관련된 요소 저장 및 접근 연산이 주를 이룬다. 반면 스트림은 표현 계산식이 주를 이룬다. 즉, 컬렉션의 주제는 데이터고 스트림의 주제는 계산이다.
    - 소스: 스트림은 컬렉션, 배열, I/O 자원 등의 데이터 제공 소스로부터 데이터를 소비한다.
    - 데이터 처리 연산: 스트림은 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터베이스와 비슷한 연산을 지원한다.
- 스트림에 중요한 두 가지 특징
    - 파이프라이닝(Pipelining): 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록 스트림 자신을 반환한다.
    - 내부 반복: 반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원한다.

```java
// 데이터 소스: menus
List<String> threeHighCaloricDishNames = menus.stream()
    .filter(dish -> dish.getCalories() > 300)  
    .map(Dish::getName)
    .limit(3)
    .collect(toList());  // 파이프라인을 처리해 결과 반환(collect 를 호출하기 전까지 menu 에서 무엇도 선택되지 않으며 출력 결과도 없다.)
System.out.println(threeHighCaloricDishNames);
```

- filter: 람다를 인수로 받아 스트림에서 특정 요소를 제외시킨다.
- map: 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추찰한다.
- limit: 정해진 개수 이상의 요소가 스트림에 저장되지 못하게 스트림 크기를 축소한다.
- collect: 스트림을 다른 형식으로 변환한다.

#### 스트림과 컬렉션 

- 자바의 컬렉션과 스트림 모두 연속된 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공한다.
    - '연속된(sequenced)'이라는 표현은 순서와 상관없이 아무 값에나 접속하는 것이 아니라 순차적으로 값에 접근한다는 것을 의미한다.
- 데이터를 언제 계산하느냐가 컬렉션과 스트림의 가장 큰 차이다. 컬렉션은 현재 자료구조가 포함하는 **모든** 값을 메모리에 저장하는 자료구조다. 즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야 한다. 
- 반면 스트림은 이론적으로 **요청할 때만 요소를 계산**하는 고정된 자료구조다. 스트림에 요소를 추가하거나 제거할 수 없다. 결과적으로 스트림은 생산자(producer)와 소비자(consumer) 관계를 형성한다. 또한 스트림은 게으르게 만들어지는 컬렉션과 같다. 즉, 사용자가 데이터를 요청할 때만 값을 계산한다. 
- 딱 한 번만 탐색할 수 있다.
     - 반복자와 마찬가지로 스트림도 한 번만 탐색할 수 있다. 즉, 탐색된 스트림의 요소는 소비된다. 스트림은 단 한 번만 소비할 수 있다는 점을 명심하자.
- 외부 반복과 내부 반복
    - 외부 반복(external iteration): 사용자가 직접 요소를 반복
    - 내부 반복(internal iteration): 반복을 알아서 처리하고 결과 스트림값을 알아서 어딘가에 저장
      - 내부 반복이 좋은 이유: 작업을 병렬로 처리하거나 더 최적화된 다양한 순서로 처리할 수 있다.

#### 스트림 연산

- 연결할 수 있는 스트림 연산을 **중간 연산(intermediate operation)**이라고 하며, 스트림을 닫는 연산을 **최종 연산(terminal operation)**이라고 한다.
- 중간 연산: 단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다는 것, 즉 게으르다(lazy)는 것이다. 중간 연산을 합친 다음에 합쳐진 중간 연산을 최종 연산으로 한 번에 처리하기 때문이다.
- 최종 연산: 스트림 파이프라인에서 결과를 도출한다.
- 스트림 이용하기
    - 스트림 이용 과정은 다음과 같이 세 가지로 요약할 수 있다.
        - 질의를 수행할 (컬렉션 같은) 데이터 소스
        - 스트림 파이프라인을 구성할 중간 연산 연결
        - 스트림 파이프라인을 실행하고 결과를 만들 최종 연산
    - 스트림 파이프라인의 개념은 빌더 패턴(builder pattern)과 비슷하다.

- 중간 연산

|연산|형식|반환 형식|연산의 인수|함수 디스크립터|
|----|----|------|-----------|----------|
|filter|중간 연산|`Stream<T>`|`Predicate<T>`|T -> boolean|
|map|중간연산|`Stream<R>`|`Function<T, R>`|T -> R|
|limit|중간연산|`Stream<T>`|||
|sorted|중간연산|`Stream<T>`|`Comparator<T>`|(T, T) -> int|
|distinct|중간연산|`Stream<T>`|||

- 최종 연산

|연산|형식|반환 형식|목적|
|---|----|--------|---|
|forEach|최종연산|void|스트림의 각 요소를 소비하면서 람다를 적용한다.|
|count|최종연산|long(generic)|스트림의 요소 개수를 반환한다.|
|collect|최종연산||스트림을 리듀스해서 리스트, 맵, 정수 형식의 컬렉션을 만든다.|


### Chapter5. 스트림 활용

#### 필터링

- 프레디케이트로 필터링: `filter` 메서드는 프레디케이트(불리언을 반환하는 함수)를 인수로 받아서 프레디케이트와 일치하는 모든 요소를 포함하는 스트림을 반환한다.
- 고유 요소 필터링: 고유 요소로 이루어진 스트림을 반환하는 `distinct` 메서드도 지원한다. 고유 여부는 스트림에서 만든 객체의 hashCode, equals로 결정된다.

```java
List<Dish> vegetarianMenu = menus.stream()
    .filter(Dish::isVegetarian)
    .toList();

List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
numbers.stream()
    .filter(i -> i % 2 == 0)
    .distinct()
    .forEach(System.out::println);
```

#### 스트림 슬라이싱

- 스트림의 요소를 선택하거나 스킵하는 다양한 방법을 설명한다.
- 프레디케이트를 이용한 슬라이싱: 이미 **정렬 된 리스트**에서 `takeWhile`, `dropWhile` 메서드를 이용해 조건을 만족하는 경우(프레디케이트) 반복 작업을 중단할 수 있다. takeWhile과 dropWhile은 정반대의 작업을 수행한다. dropWhile은 프레디케이트가 처음으로 거짓이 되는 지점까지 발견된 요소를 버린다. 프레디케이트가 거짓이 되면 그 지점에서 작업을 중단하고 남은 요소를 반환한다.
- 스트림의 축소: 주어진 값 이하의 크기를 갖는 새로운 스트림을 반환하는 `limit(n)` 메서드를 지원한다. 소스가 정렬되어 있지 않다면 limit의 결과도 정렬되지 않은 상태로 반환된다.
- 요소 건너뛰기: 처음 n개 요소를 제외한 스트림을 반환하는 `skip(n)` 메서드를 지원한다. 

```java
List<Dish> dishes = specialMenu.stream()
    .filter(dish -> dish.getCalories() > 300)
    .limit(3)
    .toList();
```

#### 매핑

- 스트림의 각 요소에 함수 적용하기: 함수를 인수로 받는 `map` 메서드를 지원한다.
- 스트림 평면화: `flatMap` 메서드는 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 수행한다.

```java
List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
List<Integer> wordLengths = words.stream()
    .map(String::length)
    .toList();

String[] arrayOfWords = {"Hello", "World"};
List<String> unique = Arrays.stream(arrayOfWords)
    .map(word -> word.split(""))  // 각 단어를 개별 문자를 포함하는 배열로 변환
    .flatMap(Arrays::stream)  // 생성된 스트림을 하나의 스트림으로 평면화
    .distinct()
    .toList();  
// 결과: [H, e, l, o, W, r, d]
```

#### 검색과 매칭

- 프레디케이트가 적어도 한 요소와 일치하는지 확인 : 주어진 스트림에서 적어도 한 요소와 일치하는지 확인할 때 `anyMatch` 메서드 사용
- 프레디케이트가 모든 요소와 일치하는지 검사: `allMatch` 스트림의 모든 요소가 주어진 프레디케이트와 일치하는지 검사한다.
    - `noneMatch`: allMatch와 반대 연산을 수행한다. 주어진 프레디케이트와 일치하는 요소가 없는지 확인한다.
    - anyMatch, allMatch, noneMatch 세 메서드는 스트림 **쇼트서킷** 기법 연산을 활용한다.
    - 쇼트서킷 평가: 모든 스트림의 요소를 처리하지 않고도 결과를 반환할 수 있다. 원하는 요소를 찾았으면 즉시 결과를 반환할 수 있다.
- 요소 검색: `findAny` 메서드는 현재 스트림에서 임의의 요소를 반환한다. findAny는 아무 요소도 반환하지 않을 수 있으므로 `Optional<T>` 클래스를 반환한다.
    - 첫 번째 요소 찾기: `findFirst`
    - findFirst와 findAny는 언제 사용하나? 병렬 실행에서는 첫 번째 요소를 찾기 어렵다. 따라서 요소의 반환 순서가 상관없다면 병렬 스트림에서는 제약이 적은 findAny를 사용한다.

```java
boolean result = menus.stream().anyMatch(Dish::isVegetarian);
boolean result = menus.stream().allMatch(dish -> dish.getCalories() < 1000);
Optional<Dish> dish = menus.stream()
    .filter(Dish::isVegetarian)
    .findAny();  // 결과를 찾는 즉시 실행 종료
```

#### 리듀싱

- 리듀스(reduce) 연산을 이용해서 '메뉴의 모든 칼로리의 합계를 구하시오', '메뉴에서 칼로리가 가장 높은 요리는?' 같이 스트림 요소를 조합해서 더 복잡하게 질의를 표현하는 방법을 설명한다. 이런 질의를 수행하려면 Integer 같은 결과가 나올 때까지 스트림의 모든 요소를 반복적으로 처리해야 한다. 이런 질의를 **리듀싱 연산**(모든 스트림 요소를 처리해서 값으로 도출하는)이라고 한다. 함수형 프로그래밍 언어 용어로는 이 과정이 마치 종이를 작은 조각이 될 때까지 반복해서 접는 것과 비슷하다는 의미로 폴드(fold)라고 부른다.
- 요소의 합: `reduce` 메서드는 리스트에 하나의 숫자가 남을 때까지 reduce 과정을 반복한다.
- 최대값과 최솟값: reduce를 활용할 수 있다.
- reduce 메서드의 장점과 병렬화: reduce를 이용하면 내부 반복이 추상화되면서 내부 구현에서 병렬로 reduce를 실행할 수 있게 된다. 반복적인 합계에서 sum 변수를 공유해야 하므로 쉽게 병렬화하기 어렵다. 가변 누적자 패턴은 병렬화와 거리가 너무 먼 기법이기 때문이다.

```java
Optional<Integer> max = numbers.stream().reduce(Integer::max);
```

#### 스트림 연산: 상태 없음과 상태 있음

- 스트림을 이용해서 원하는 모든 연산을 쉽게 구현할 수 있으며, 컬렉션을 스트림으로 만드는 stream 메서드를 parallelStrema로 바꾸는 것만으로도 별다른 노력 없이 병렬성을 얻을 수 있다.
- map, filter 등은 입력 스트림에서 각 요소를 받아 0 또는 결과를 출력 스트림으로 보낸다. 따라서 이들은 보통 상태가 없는, 즉 내부 상태를 갖지 않는 연산(stateless operation)이다.
- reduce, sum, max 같은 연산은 결과를 누적할 내부 상태가 필요하다. 스트림에서 처리하는 요소 수와 관계없이 내부 상태의 크기는 한정(bounded)되어 있다.
- 반면 sorted, distinct 같은 연산은 fiter나 map과 다르다. 스트림의 요소를 정렬하거나 중복을 제거하려면 과거의 이력을 알고 있어야 한다. 연산을 수행하는 데 필요한 저장소 크기는 정해져있지 않다. 따라서 데이터 스트림의 크기가 크거나 무한이라면 문제가 생길 수 있다. 이러한 연산을 **내부 상태를 갖는 연산(stateful operation)**이라 한다.

#### 숫자형 스트림 

- 숫자 스트림에 박싱 비용이 숨어있다. 스트림 API는 숫자 스트림을 효율적으로 처리할 수 있도록 **기본형 특화 스트림(primitive stream specialization)**을 제공한다.
- 기본형 특화 스트림: 박싱 비용을 피할 수 있도록 세 가지 기본형 특화 스트림을 제공한다. IntStream, DoubleStream, LongStream을 제공한다. 각각의 인터페이스는 자주 사용하는 숫자 관련 리듀싱 연산 수행 메서드를 제공한다. 특화 스트림은 오직 박싱 과정에서 일어나는 효율성과 관련 있으며 추가 기능을 제공하지는 않는다.
- 스트림을 특화 스트림으로 변환할 때는 `mapToInt`, `mapToDouble`, `mapToLong` 세 가지 메서드를 가장 많이 사용한다. 이들 메서드는 map과 정확히 같은 기능을 수행하지만, `Stream<T>` 대신 특화된 스트림을 반환한다.

```java
int calories = menus.stream()
    .mapToInt(Dish::getCalories)  // IntStream 반환
    .sum();
```

- 객체 스트림으로 복원하기: `boxed` 메서드를 이용해서 특화 스트림을 일반 스트림으로 변환할 수 있다.

```java
IntStream intStream = menus.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed();
```

- 기본값(OptionalInt): OptionalInt, OptionalDouble, OptionalLong 세 가지 기본형 특화 스트림 버전도 제공한다. 이것을 이용해서 최대값이 없는 상황에서 사용할 기본값을 명시적으로 정의할 수 있다.
- 숫자 범위: `range`와 `rangeClosed`라는 두 가지 정적 메서드를 제공한다. 두 메서드 모두 첫 번째 인수로 시작값을, 두 번째 인수로 종료값을 갖는다. range 메서드는 시작값과 종료값이 결과에 포함되지 않는 반면 rangeClosed는 시작값과 종료값이 결과에 포함된다는 점이 다르다.

```java
IntStream evenNumbers = IntStream.rangeClosed(1, 100).filter(n -> n % 2 == 0);
```

#### 스트림 만들기

- 일련의 값, 배열, 파일, 심지어 함수를 이용한 무한 스트림 만들기 등 다양한 방식으로 스트림을 만드는 방법을 설명한다.
- 값으로 스트림 만들기: 정적 메서드 `Stream.of`를 이용해서 스트림을 만들 수 있다.

```java
Stream<String> stream = Stream.of("Modern", "Java", "In", "Action");
stream.map(String::toUpperCase).forEach(System.out::println);
```

- null이 될 수 있는 객체로 스트림 만들기: `Stream.empty()`, `Stream.ofNullable()`
- 파일로 스트림 만들기: 파일을 처리하는 등의 I/O 연산에 사용하는 자바의 NIO API(비블록 I/O)도 스트림 API를 활용할 수 있도록 업데이트되었다. `java.nio.file.Files`의 많은 정적 메서드가 스트림을 반환한다.
- 함수로 무한 스트림 만들기: `Stream.iterate`와 `Stream.generate`를 제공한다. 두 연산을 이용해서 무한 스트림(infinite stream)을 만들 수 있다. 하지만 보통 무한한 값을 출력하지 않도록 limit(n) 함수를 함께 연결해서 사용한다.
    - 무한 스트림을 언바운드 스트림(unbounded stream)이라고 표현한다. limit이 없다면 스트림은 언바운드 상태가 된다.

```java
Stream.iterate(0, n -> n + 2)  // iterate(초기값, 람다)
    .limit(10)
    .forEach(System.out::println);

Stream.generate(Math::random)  // generate(Supplier<T>)
    .limit(5)
    .forEach(System.out::println);
```

### Chapter6. 스트림으로 데이터 수집


#### 컬렉터란 무엇인가?
