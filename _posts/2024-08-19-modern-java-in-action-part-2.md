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

- 스트림의 중간 연산은 요소를 **소비(consume)**하지 않는다. 반면 최종 연산은 스트림의 요소를 소비해서 최종 결과를 도출한다. 지금까지 최종 연산 collect, toList를 사용했다. 
- 다양한 요소 누적 방식은 `Collector` 인터페이스에 정의되어 있다. 지금부터 컬렉션(Collection), 컬렉터(Collector), collect를 헷갈리지 않도록 주의하자.

```java
// 통화별로 트랜잭션을 그룹화
// 명령형 버전
Map<Currency, List<Transaction>> transactionByCurrencies = new HashMap<>();
for (Transaction transaction : transactions) {
    Currency currency = transaction.getCurrency();
    List<Transaction> transactionsForCurrency = transactionByCurrencies.get(currency);
    if (transactionsForCurrency == null) {  // 현재 통화를 그룹화하는 맵에 항목이 없으면 항목을 생성
    transactionsForCurrency = new ArrayList<>();
    transactionByCurrencies.put(currency, transactionsForCurrency);
    }
    transactionsForCurrency.add(transaction);
}

// 함수형 버전
Map<Currency, List<Transaction>> result = transactions.stream()
    .collect(groupingBy(Transaction::getCurrency));
```

#### 컬렉터란 무엇인가?

- 다수준(multilevel)으로 그룹화를 수행할 때, 명령형 코드는 문제를 해결하는 과정에서 다중 루프와 조건문을 추가하며 가독성과 유지보수성이 크게 떨어진다. 함수형 프로그래밍에서는 필요한 컬렉터를 쉽게 추가할 수 있다.
- 고급 리듀싱 기능을 수행하는 컬렉터: 스트림에 `collect`를 호출하면 스트림의 요소에 리듀싱 연산이 수행된다. Collector 인터페이스의 메서드를 어떻게 구현하느냐에 따라 스트림에 어떤 리듀싱 연산을 수행할지 결정된다. 
- 미리 정의된 컬렉터: Collectors에서 제공하는 메서드의 기능은 크게 세 가지로 구분할 수 있다.
    - 스트림 요소를 하나의 값으로 리듀스하고 요약(summarize)
    - 요소 그룹화
    - 요소 분할(partitioning)

#### 리듀싱과 요약

- `counting()`, `count()`
- 스트림에서 최대값과 최소값: `maxBy()`, `minBy()`

```java
Long howManyDishes = menus.stream().collect(counting());
long howManyDishes2 = menus.stream().count();

Comparator<Dish> dishCaloriesComparator = Comparator.comparingInt(Dish::getCalories);
Optional<Dish> mostCalorieDish = menus.stream()
    .collect(maxBy(dishCaloriesComparator));  // maxBy(Comparator)
Optional<Dish> mostCalorieDish2 = menus.stream()
    .max(dishCaloriesComparator);
```
- 요약 연산: `summingInt()`, `averagingInt()`, `summarizingInt()`

```java
// 합계
Integer totalCalories = menus.stream().collect(summingInt(Dish::getCalories));
System.out.println(totalCalories);

// 평균
Double avgCalories = menus.stream().collect(averagingInt(Dish::getCalories));
System.out.println(avgCalories);

// 요소 수, 합계, 평균, 최댓값, 최소값 정보 수집
IntSummaryStatistics menuStatistics = menus.stream().collect(summarizingInt(Dish::getCalories));
System.out.println(menuStatistics);
```

- 문자열 연결: `joining()`

```java
String shortMenu = menus.stream()
    .map(Dish::getName).collect(joining());  // joining() 메서드는 내부적으로 StringBuilder 이용

// 연결된 두 요소 사이에 구분자 추가
String shortMenu2 = menus.stream()
    .map(Dish::getName).collect(joining(", "));
```

- 범용 리듀싱 요약 연산: 지금까지 살펴본 모든 컬렉터는 reducing 팩토리 메서드로 정의할 수 있다. 그럼에도 범용 팩토리 메소드 대신 특화된 컬렉터를 사용한 이유는 프로그래밍적 편의성 때문이다.

```java
Integer totalCalories = menus.stream()
    // reducing(시작값 또는 반환값, 변환 함수, BinaryOperator)
    .collect(reducing(0, Dish::getCalories, (i, j) -> i + j));

Optional<Dish> mostCalorieDish = menus.stream()
    // reducing(자신을 반환하는 항등 함수): 스트림의 첫 번째 요소가 시작값이다.
    .collect(reducing((d1, d2) -> d1.getCalories() > d2.getCalories() ? d1 : d2));
```

- 컬렉션 프레임워크 유연성: 같은 연산도 다양한 방식으로 수행할 수 있다.

```java
Integer totalCalories = menus.stream()
    .collect(reducing(0,  // 초기값
        Dish::getCalories,  // 합계 함수
        Integer::sum));  // 변환 함수
```

- 자신의 상황에 맞는 최적의 해법 선택: 지금까지 살펴본 예제는 함수형 프로그래밍에서는 하나의 연산을 다양한 방법으로 해결할 수 있음을 보여준다. 또한 스트림 인터페이스에서 직접 제공하는 메서드를 이용하는 것에 비해 컬렉터를 이용하는 코드가 더 복잡하다는 사실도 보여준다. 코드가 좀 더 복잡한 대신 재사용성과 커스터마이즈 가능성을 제공하는 높은 수준의 추상화와 일반화를 얻을 수 있다.

```java
Integer totalCalories = menus.stream()
    .map(Dish::getCalories)
    .reduce(Integer::sum).get();

// 간결하고 가독성이 좋으며, IntStream 덕분에 자동 언박싱 과정을 회피하므로 성능도 좋다.
int totalCalories = menus.stream().mapToInt(Dish::getCalories).sum();
```

#### 그룹화 

- 팩토리 메서드 `Collectors.groupingBy`를 이용해서 쉽게 메뉴를 그룹화할 수 있다.

```java
Map<Type, List<Dish>> dishesByType = menus.stream().collect(groupingBy(Dish::getType));  // 분류 함수
```
- 위 코드는 groupingBy 메서드로 함수를 전달했다. 이 함수를 기준으로 스트림이 그룹화되므로 이를 **분류 함수(classification function)**라고 부른다.
- 단순한 속성 접근자 대신 더 복잡한 분류 기준이 필요한 상황에서는 메서드 참조 대신 람다 표현식으로 필요한 로직을 구현할 수 있다.

```java
enum CaloricLevel {DIET, NORMAL, FAT}
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = menus.stream()
    .collect(groupingBy(dish -> {
    if (dish.getCalories() <= 400)
        return CaloricLevel.DIET;
    else if (dish.getCalories() <= 700)
        return CaloricLevel.NORMAL;
    else
        return CaloricLevel.FAT;
    }));
```

- 그룹화된 요소 시작
  - groupingBy 오버로드
  - filtering 메서드
  - mapping 메서드
  - flatMapping 메서드

```java
Map<Type, List<Dish>> caloricDishesByType = menus.stream()
    .collect(
        groupingBy(Dish::getType,
            filtering(dish -> dish.getCalories() > 500, toList())));

Map<Type, List<String>> dishNamesByType = menus.stream()
    .collect(groupingBy(Dish::getType, mapping(Dish::getName, toList())));

Map<String, List<String>> dishTags = new HashMap<>();
dishTags.put("pork", asList("greasy", "salty"));
// ...
Map<Type, Set<String>> dishNamesByType = menus.stream()
    .collect(groupingBy(Dish::getType,
        flatMapping(dish -> dishTags.get(dish.getName()).stream(), toSet())));  // 집합으로 그룹화(toSet)해 중복 태그 제거
```

- 다수준 그룹화: groupingBy는 일반적인 분류 함수와 컬렉터를 인수로 받는다. 바깥쪽 groupingBy 메서드에 스트림의 항목을 분류할 두 번째 기준을 정의하는 내부 groupingBy를 전달해서 두 수준으로 스트림의 항목을 그룹화 할 수 있다.

```java
// 외부 맵은 'Type(fish, map, other)'를 키 값으로 갖는다.
// 내부 맵은 'CaloricLevel(diet, normal, fat)'를 키 값으로 갖는다.
Map<Type, Map<CaloricLevel, List<Dish>>> dishesByTypeCaloricLevel = menus.stream()
    .collect(groupingBy(Dish::getType,  // 첫 번째 수준의 분류 함수
        groupingBy(dish -> {  // 두 번째 수준의 분류 함수
            if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
            } else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
            } else {
            return CaloricLevel.FAT;
            }
        })
    ));
```

- 서브그룹으로 데이터 수집
    - groupingBy(f)는 사실 groupingBy(f, toList())의 축약형이다.
    - 두번째 인수로 다른 컬렉터를 전달할 수 있다.
        - `counting`
        - `summingInt`
        - `mapping`: 스트림의 인수를 변환하는 함수와 변환 함수의 결과 객체를 누적하는 컬렉터를 인수로 받는다.
  - collectingAndThen: 컬렉터 결과를 다른 형식에 적용


```java
// 메뉴에서 Type별 요리의 수
Map<Type, Long> typesCount = menus.stream()
    .collect(groupingBy(Dish::getType, counting()));

// 메뉴에서 Type별 가장 높은 칼로리를 가진 요리 
Map<Type, Optional<Dish>> mostCaloricByType = menus.stream()
    .collect(groupingBy(Dish::getType, maxBy(Comparator.comparingInt(Dish::getCalories))));

// 컬렉터 결과를 다른 형식에 적용하기 (위의 결과에 Optional 제거)
Map<Type, Dish> mostCaloricByType = menus.stream()
    .collect(groupingBy(Dish::getType,  // 분류 함수
        collectingAndThen(
            maxBy(Comparator.comparingInt(Dish::getCalories)),  // 감싸인 컬렉터
            Optional::get  // 반환 함수
        )
    ));

// mapping
Map<Type, Set<CaloricLevel>> caloricLevelsByType = menus.stream()
    .collect(
        groupingBy(Dish::getType, mapping(dish -> {
            if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
            } else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
            } else {
            return CaloricLevel.FAT;
            }
        }, toSet()))  // toSet 컬렉터로 중복 제거
    );
```

#### 분할 

- 분할은 **분할 함수(partitioning function)**라 불리는 프레디케이트를 분류 함수로 사용하는 특수한 그룹화 기능이다. 분할 함수는 불리언을 반환하므로 맵의 키 형식은 Boolean이다.

```java
Map<Boolean, List<Dish>> partitionedMenu = menus.stream()
    .collect(partitioningBy(Dish::isVegetarian));  // 분할 함수
``

- 분할의 장점: 분할 함수가 반환하는 참, 거짓 두 가지 요소의 스트림 리스트를 모두 유지한다는 것이 분할의 장점이다.

```java
// 컬렉터를 두 번째 인수로 전달할 수 있는 오버로드 된 partitioningBy
Map<Boolean, Map<Type, List<Dish>>> vegetarianDishesByType = menus.stream()
    .collect(partitioningBy(Dish::isVegetarian,  // 분할함수
        groupingBy(Dish::getType))  // 두 번째 컬렉터
    );
```

- groupingBy 컬렉터와 마찬가지로 partitioningBy 컬렉터도 다른 컬렉터와 조합해서 사용할 수 있다. 특히 두 개의 partitioningBy 컬렉터를 이용해서 다수준 분할을 수행할 수 있다.

- Collectors 클래스의 정적 팩토리 메서드

| 팩토리 메서드     | 반환 형식                                      | 설명                                                                                                                         | 활용 예                                                                                               |
|-------------------|------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| toList            | `List<T>`                                      | 스트림의 모든 항목을 리스트로 수집                                                                                           | `List<Dish> dishes = menuStream.collect(toList());`                                                   |
| toSet             | `Set<T>`                                       | 스트림의 모든 항목을 중복이 없는 집합으로 수집                                                                               | `Set<Dish> dishes = menuStream.collect(toSet());`                                                     |
| toCollection      | `Collection<T>`                                | 스트림의 모든 항목을 발행자가 제공하는 컬렉션으로 수집                                                                       | `Collection<Dish> dishes = menStream.collect(toCollection(), ArrayList::new);`                        |
| counting          | Long                                           | 스트림의 항목 수 계산                                                                                                        | `long howManyDishes = menuStream.collect(counting());`                                                |
| summingInt        | Integer                                        | 스트림의 항목에서 정수 프로퍼티값을 더함                                                                                     | `int totalCalories = menuStream.collect(summingInt(Dish::getCalories));`                              |
| averagingInt      | Double                                         | 스트림 항목의 정수 프로퍼티의 평균값 계산                                                                                    | `double avgCalories = menuStream.collect(averagingInt(Dish::getCalories));`                           |
| summarizingInt    | IntSummaryStatistics                           | 스트림 내 항목의 최대값, 최소값, 합계, 평균 등의 정수 정보 통계 수집                                                         | `IntSummaryStatistics menuStatistics = menuStream.collect(summarizingInt(Dish::getCalories));`        |
| joining           | String                                         | 스트림의 각 항목에 toString 메서드를 호출한 결과 문자열 연결                                                                 | `String shortMenu = menuStream.map(Dish::getName).collect(joining(", "));`                            |
| maxBy             | `Optional<T>`                                  | 주어진 비교자를 이용해서 스트림의 최대값 요소를 Optional로 감싼 값을 반환. 스트림에 요소가 없을 때는 Optional.empty() 반환   | `Optional<Dish> fattest = menuStream.collect(maxBy(comparingInt(Dish::getCalories)));`                |
| minBy             | `Optional<T>`                                  | 주어진 비교자를 이용해서 스트림의 최소값 요소를 Optional로 감싼 값을 반환. 스트림에 요소가 없을 때는 Optional.empty() 반환   | `Optional<Dish> lightest = menuStream.collect(minBy(comparingInt(Dish::getCalories)));`               |
| reducing          | The type produced by the reduction operation   | 누적자를 초기값으로 설정한 다음에 BinaryOperator로 스트림의 각 요소를 반복적으로 누적자와 합쳐 스트림을 하나의 값으로 리듀싱 | `int totalCalories = menuStream.collect(reducing(0, Dish::getCalories, Integer::sum));`               |
| collectingAndThen | The type returned by the transforming function | 다른 컬렉터를 감싸고 그 결과에 변환 함수 적용                                                                                | `int howManyDishes = menuStream.collect(collectingAndThen(toList(), List::size));`                    |
| groupingBy        | `Map<K, List<T>>`                              | 하나의 프로퍼티값을 기준으로 스트림의 항목을 그룹화하며 기준 프로퍼티값을 결과 맵의 키로 사용                                | `Map<Dish.Type, List<Dish>> dishesByType = menuStream.collect(groupingBy(Dish::getType));`            |
| partitioningBy    | `Map<Boolean, List<T>>`                        | 프레디케이트를 스트림의 각 항목에 적용한 결과로 항목 분할                                                                    | `Map<Boolean, List<Dish>> vegetarianDishes = menuStream.collect(partitioningBy(Dish::isVegetarian));` |

#### Collector 인터페이스

- Collector 인터페이스는 리듀싱 연산(즉, 컬렉터)을 어떻게 구현할지 제공하는 메서드 집합으로 구성된다. 지금까지 toList나 groupingBy 등 Collector 인터페이스를 구현하는 많은 컬렉터를 살펴봤다.
- 즉, Collector 인터페이스를 직접 구현해서 더 효율적으로 문제를 해결하는 컬렉터를 만드는 방법을 살펴본다.

```java
// Collector 인터페이스

/**
 * T: 수집될 스트림 항목(제네릭 형식)
 * A: 누적자(중간 결과를 누적하는 객체)
 * R: 수집 연산 결과 객체의 형식(대게 컬렉션 형식)
*/
public interface Collector<T, A, R> {
    // collect 메서드에서 실행하는 함수를 반환
    Supplier<A> supplier();   // 새로운 결과 컨테이너 만들기 
    BiConsumer<A, T> accumulator();  // 결과 컨테이너에 요소 추가하기
    Function<A, R> finisher();  // 최종 변환값을 결과 컨테이너로 적용하기
    BinaryOperator<A> combiner();  // 두 결과 컨테이너 병합
    
    // collect 메서드가 어떤 최적화를 이용해서 리듀싱 연산을 수행할 것인지 결정하도록 돕는 힌트 특성 집합 제공
    Set<Characteristics> characteristics();
}
```

- supplier 메서드: 새로운 결과 컨테이너 만들기, 빈 결과로 이루어진 Supplier를 반환
- accumulator 메서드: 결과 컨테이너에 요소 추가하기, 리듀싱 연산을 수행하는 함수 반환
- finisher 메서드: 최종 변환값을 결과 컨테이너로 적용하기, 스트림 탐색을 끝내고 누적자 객체를 최종 결과로 변환하면서 누적 과정을 끝낼 때 호출할 함수를 반환해야 한다.
- combiner 메서드: 두 결과 컨테이너 병합, 스트림의 서로 다른 서브파트를 병렬로 처리할 때 누적자가 이 결과를 어떻게 처리할지 정의한다.
- characteristics 메서드: 컬렉터의 연산을 정의하는 Characteristics 형식의 불변 집합을 반환한다.
    - UNORDERED: 리듀싱 결과는 스트림 요소의 방문 순서나 누적 순서에 영향을 받지 않는다.
    - CONCURRENT: 다중 스레드에서 accumulator 함수를 동시에 호출할 수 있으며 이 컬렉터는 스트림의 병렬 리듀싱을 수행할 수 있다.
    - IDENTITY_FINISH: finisher 메서드가 반환하는 함수는 단순히 identity를 적용할 뿐이므로 이를 생략할 수 있다. 리듀싱 과정의 최종 결과로 누적자 객체를 바로 사용할 수 있다. 또한 누적자 A를 결과 R로 안전하게 형변환할 수 있다.
- 커스텀 컬렉터 구현 예시

```java
public class PrimeNumberCollector implements
    // 1단계: Collector 클레스 시그니처 정의 (스트림 요소의 형식, 중간 결과를 누적하는 객체의 형식, 최종 결과 형식)
    Collector<Integer, Map<Boolean, List<Integer>>, Map<Boolean, List<Integer>>> {

  // 2단계: 리듀싱 연산 구현
  @Override
  public Supplier<Map<Boolean, List<Integer>>> supplier() {
    return () -> new HashMap<Boolean, List<Integer>>() {{
      put(true, new ArrayList<Integer>());
      put(false, new ArrayList<Integer>());
    }};
  }

  // 2단계: 리듀싱 연산 구현
  @Override
  public BiConsumer<Map<Boolean, List<Integer>>, Integer> accumulator() {
    return (acc, candidate) -> {
      acc.get(isPrime(acc.get(true), candidate))  // isPrime 결과에 따라 소수 리스트와 비소수 리스트 생성
          .add(candidate);  // candidate를 알맞은 리스트에 추가
    };
  }

  // 3단계: 병렬 실행할 수 있는 컬렉터 만들기(가능하다면)
  @Override
  public BinaryOperator<Map<Boolean, List<Integer>>> combiner() {
    return (map1, map2) -> {
      // 알고리즘 자체가 순차적이어서 병렬로 사용 불가
      // 따라서 combiner 메서드는 호출될 일이 없으므로 빈 구현으로 남겨둘 수 있다.
      // 또는 UnsupportedOperationException 을 던지도록 구현하는 방법도 좋다.
      // 학습 목적으로 구현
      map1.get(true).addAll(map2.get(true));
      map1.get(false).addAll(map2.get(false));
      return map1;
    };
  }

  // 4단계: finisher 메서드와 컬렉터의 characteristics 메서드
  @Override
  public Function<Map<Boolean, List<Integer>>, Map<Boolean, List<Integer>>> finisher() {
    // accumulator 메서드의 형식은 컬렉터 결과 형식과 같으므로 변환 과정이 필요 없다.
    // 따라서 항등 함수 identity를 반환하도록 구현
    return Function.identity();
  }

  // 4단계: finisher 메서드와 컬렉터의 characteristics 메서드
  @Override
  public Set<Characteristics> characteristics() {
    return Collections.unmodifiableSet(EnumSet.of(Characteristics.IDENTITY_FINISH));
  }
}
```


### Chapter7. 병렬 데이터 처리와 성능

- 자바 7이 등장하기 전에는 데이터 컬렉션을 병렬로 처리하기 어려웠다. 우선 데이터를 서브파트로 분할해야 한다. 그리고 분할된 서브파트를 각각의 스레드로 할당한다. 스레드로 할당한 다음에는 의도치 않은 레이스 컨디션(race condition)이 발생하지 않도록 적절한 동기화를 추가해야 하며, 마지막으로 부분 결과를 합쳐야 한다.
- 자바 7은 **포크/조인 프레임워크(fork/join framework)** 기능을 제공한다.
- 스트림으로 데이터 컬렉션 관련 동작을 쉽게 병렬로 실행할 수 있게 되었다.

#### 병렬 스트림

- 컬렉션에 `parallelStream`을 호출하면 **병렬 스트림(parallel stream)**이 생성된다. 병렬 스트림이란 각각의 스레드에서 처리할 수 있도록 스트림 요소를 여러 청크로 분할한 스트림이다.
- 순차 스트림을 병렬 스트림으로 변환: `parallel` 메서드 호출
    - 순차 스트림에 parallel을 호출해도 스트림 자체에는 아무 변화도 일어나지 않는다. 내부적으로 parallel을 호출하면 이후 연산이 병렬로 수행해야 함을 의미하는 불리언 프래그가 설정된다. 반대로 sequential로 병렬 스트림을 순차 스트림으로 바꿀 수 있다.
- 병렬 스트림에서 사용하는 스레드 풀 설정
    - 병렬 스트림은 내부적으로 ForkJoinPool을 사용한다. 기본적으로 ForkJoinPool은 프로세스 수(`Runtime.getRuntime().availableProcessors()`)가 반환하는 값에 상응하는 스레드를 갖는다.
    - 현재는 하나의 병렬 스트림에 사용할 수 있는 특정한 값을 지정할 수 없다. 일반적으로 기기의 프로세서 수와 같으므로 특별한 이유가 없다면 ForkJoinPool의 기본값을 그대로 사용할 것을 권장한다.

```java
public class CustomSum {

  // 반복문
  static long iterativeSum(long n) {
    long result = 0;
    for (long i = 1L; i <= n; i++) {
      result += i;
    }
    return result;
  }

  // 순차 리듀싱
  static long sequentialSum(long n) {
    return Stream.iterate(1L, i -> i + 1)  // 무한 자연수 스트림 생성
        .limit(n)  // n개 이하로 제한
        .reduce(0L, Long::sum);  // 모든 숫자를 더하는 스트림 리듀싱 연산
  }

  // 병렬 리듀싱
  static long parallelSum(long n) {
    return Stream.iterate(1L, i -> i + 1)
        .limit(n)
        .parallel()  // 스트림을 병렬 스트림으로 변환
        .reduce(0L, Long::sum);
  }

}
```

- 스트림 성능 측정
    - 성능을 최적화할 때는 세 가지 황금 규칙을 기억해야 한다. 첫째도 측정, 둘째도 측정, 셋째도 측정!
    - 자바 마이크로벤치마크 하니스(Java Microbenchmark Harness, JMH)라는 라이브러릴 사용해 성능을 측정한다.
    - 테스트 결과: iterativeSum, sequentialSum, parallelSum 순서대로 속도가 빠르다.
    - 전통적인 for 루프를 사용해 반복하는 방법이 더 저수준으로 동작할 뿐 아니라 특히 기본값을 박싱하거나 언박싱할 필요가 없으므로 더 빠르다. 테스트 결과 순차적 스트림보다 거의 4배 빠르다.
    - 하지만 병렬 스트림을 사용하는 함수가 순차 보다 5배나 더 느리다.
    - 문제점
        - 반복 결과로 박싱된 객체가 만들어지므로 숫자를 더하려면 언박싱을 해야 한다.
        - 반복 작업은 병렬로 수행할 수 있는 독립 단위로 나누기가 어렵다.
    - 병렬 프로그램은 까다롭고 이해하기 어려운 함정이 숨어있다. 따라서 내부적으로 어떤일이 일어나는지 꼭 이해해야 한다.
    - 더 특화된 메서드 사용: rangeClosed 메서드는 iterate에 비해 다음과 같은 장점을 제공한다.
        - 기본형 long을 직접 사용하므로 박싱과 언박싱 오버헤드가 사라진다.
        - 청크로 쉽게 분할할 수 있는 숫자 범위를 생산한다.
    - 상황에 따라서는 어떤 알고리즘을 병렬화하는 것보다 적절한 자료구조를 선택하는 것이 더 중요하다는 사실을 단적으로 보여준다. 올바른 자료구조를 선택해야 병렬실행도 최적의 성능을 발휘할 수 있다.
    - 병렬화는 공짜가 아니다. 병렬화를 이용하려면 스트림을 재귀적으로 분할해야 하고, 각 서브 스트림을 서로 다른 스레드의 리듀싱 연산으로 할당하고 이 결과를 하나의 값으로 합쳐야 한다. 멀티코어 간의 데이터 이동은 생각보다 비싸다. 
    - 스트림을 병렬화해서 코드 실행 속도를 빠르게 하고 싶으면 항상 병렬화를 올바르게 사용하고 있는지 확인해야 한다.
- 병렬 스트림의 올바른 사용법: 병렬 스트림과 병렬 계산에서는 공유된 가변 상태를 피해야 올바로 동작한다.
- 병렬 스트림 효과적으로 사용하기
    - 확신이 서지 않으면 직접 측정하라. 순차 스트림을 병렬 스트림으로 쉽게 바꿀 수 있다. 하지만 무조건 병렬 스트림으로 바꾸는 것이 능사가 아니다. 따라서 순차 스트림과 병렬 스트림 중 어떤 것이 좋을지 모르겠다면 적절한 벤치마그로 직접 성능을 측정하는 것이 바람직하다.
    - 박싱을 주의하라. 자동 박싱과 언박싱은 성능을 크게 저하시킬 수 있는 요소다. 따라서 되도록이면 기본형 특화 스트림(IntStream, LongStream, DoubleStream)을 사용하는 것이 좋다.
    - 순차 스트림보다 병렬 스트림에서 성능이 떨어지는 연산이 있다. 특히 limit, findFirst 처럼 요소의 순서에 의존하는 연산을 병렬 스트림에서 수행하려면 비싼 비용을 치러야 한다. 스트림에 N개 요소가 있을 때 요소의 순서가 상관없다면 비정렬된 스트림에 limit을 호출하는 것이 더 효율적이다.
    - 스트림에서 수행하는 전체 파이프라인 연산 비용을 고려하라. 처리해야 할 요소 수가 N이고 하나의 요소를 처리하는데 드는 비용을 Q라 하면 전체 스트림 파이프라인 처리 비용을 N*Q로 예상할 수 있다. Q가 높아진다는 것은 병렬 스트림으로 성능을 개선할 수 있는 가능성이 있음을 의미한다.
    - 소량의 데이터에서는 병렬 스트림이 도움 되지 않는다.
    - 스트림을 구성하는 자료구조가 적절한지 확인하라. 예를 들어 ArrayList는 LinkedLit보다 효율적으로 분할할 수 있다. 또한 range 팩토리 메서드로 만든 기본형 스트림도 쉽게 분해할 수 있다.
    - 스트림의 특성과 파이프라인의 중간 연산이 스트림의 특성을 어떻게 바꾸는지에 따라 분해 과정의 성능이 달라질 수 있다.
    - 최종 연산의 병합 과정 비용을 살펴보라.
- 다양한 스트림 소스의 병렬화 친밀도

|소스|분해성|
|---|------|
|ArrayList|훌륭함|
|LinkedList|나쁨|
|IntStream.range|훌륭함|
|Stream.iterate|나쁨|
|HashSet|좋음|
|TreeSet|좋음|

- 병렬 스트림 처리는 포크/조인 프레임워크로 처리된다.

#### 포크/조인 프레임워크

- 포크/조인 프레임워크는 병렬화할 수 있는 작업을 재귀적으로 작은 작업으로 분할한 다음에 서브태스크 각각의 결과를 합쳐서 전체 결과를 만들도록 설계되었다.
- 포크/조인 프레임워크에서는 서브태스크를 스레드 풀(ForkJoinPool)의 작업자 스레드에 분산 할당하는 ExecutorService 인터페이스를 구현한다.
- RecursiveTask 활용
    - 스레드 풀을 이용하려면 `RecursiveTask<R>`의 서브클래스를 만들어야 한다. 여기서 R은 병렬화된 태스크가 생성하는 결과 형식 또는 결과가 없을 때는 recursiveAction 형식이다. 
    - RecursiveTask를 정의하려면 추상 메서드 compute를 구현해야 한다.
    - compute 메서드는 태스크를 서브태스크로 분할하는 로직과 더 이상 분할할 수 없을 때 개별 서브태스크의 결과를 생상할 알고리즘을 정의한다.
    - 의사코드로 표현하면 다음과 같다.

    ```java
    if (태스크가 충분히 작거나 더 이상 분할할 수 없으면) {
        순차적으로 태스크 계산
    } else {
        태스크를 두 서브태스크로 분할
        태스크가 다시 서브태스크로 분할되도록 이 메서드를 재귀적으로 호출
        모든 서브태스크의 연산이 완료될 때까지 기다림
        각 서브태스크의 결과를 합침
    }
    ```

    - 이 알고리즘은 분할 후 정복(divide-and-conquer) 알고리즘의 병렬화 버전이다.
    - 포크/조인 과정
        - 포크: 각각의 서브태스크의 크기가 충분히 작아질 때까지 태스크를 재귀적으로 포크
        - 모든 서브태스크를 병렬로 수행
        - 조인: 부분 결과를 조합
- 포크/조인 프레임워크를 제대로 사용하는 방법
    - join 메서드를 태스크에 호출하면 태스크가 생산하는 결과가 준비될 때까지 호출자를 블록시킨다. 따라서 두 서브태스크가 모두 시작된 다음에 join을 호출해야 한다. 그렇지 않으면 대기가 발생하며 원래 순차 알고리즘보다 느리고 복잡한 프로그램이 된다.
    - RecursiveTask 내에서는 ForkJoinPool의 invoke 메서드를 사용하지 말아야 한다. 대신 compute나 fork 메서드를 직접 호출할 수 있다. 순차 코드에서 병렬 계산을 시작할 때만 invoke를 사용한다.
    - 서브태스크에 fork 메서드를 호출해서 ForkJoinPool의 일정을 조절할 수 있다. 한쪽 작업에는 fork를 호출하고 다른쪽 작업에는 compute를 호출하는 것이 효율적이다.
    - 포크/조인 프레임워크를 이용하는 병렬 계산은 디버깅하기 어렵다.
    - 순차처리보다 무조건 빠를 거라는 생각은 버려야 한다.
- 작업 훔치기
    - 코어 개수와 관계없이 적절한 크기로 분할된 많은 태스크를 포킹하는 것이 바람직하다.
    - 포크/조인 프레임워크에서는 **작업 훔치기(work stealing)**라는 기법으로 이 문제를 해결한다.
    - 작업 훔치기 기법에서는 ForkJoinPool의 모든 스레드를 거의 공정하게 분할한다. 각각의 스레드는 자신에게 할당된 태스크를 포함하는 이중 연결 리스트(doubly linked list)를 참조하면서 작업이 끝날 때마다 큐의 헤드에서 다른 태스크를 가져와 작업을 처리한다.
    - 할일이 없어진 스레드는 유휴 상태로 바뀌는 것이 아니라 다른 스레드 큐의 꼬리에서 작업을 훔쳐온다. 따라서 태스크의 크기를 작게 나누어야 작업자 스레드 간의 작업부하를 비슷한 수준으로 유지할 수 있다.

#### Spliterator 인터페이스

- Spliterator는 '분할할 수 있는 반복자'라는 의미다.
- Iterator처럼 Spliterator는 소스의 요소 탐색 기능을 제공하는 점은 같지만 Spliterator는 병렬 작업에 특화되어 있다.
- 분할 과정
    - trySplit의 결과가 null이면 재귀 분할 과정이 종료된다.
    - Spliterator 특성
        - ORDERED: 리스트처럼 요소에 정해진 순서가 있으므로 Spliterator는 요소를 탐색하고 분할할 때 이 순서에 유의해야 한다.
        - DISTINCT: x, y 두 요소를 방문했을 때 x.equals(y)는 항상 false를 반환한다.
        - SORTED: 탐색된 요소는 미리 정의된 정렬 순서를 따른다.
        - SIZED: 크기가 알려진 소스(예: Set)로 Spliterator를 생성했으므로 estimatedSize()는 정확한 값을 반환한다.
        - NON-NULL: 탐색하는 모든 요소는 null이 아니다.
        - IMMUTABLE: 이 Spliterator의 소스는 불변이다. 즉, 요소를 탐색하는 동안 추가/삭제/수정 할 수 없다.
        - CONCURRENT: 동기화 없이 Spliterator의 소스를 여러 스레드에서 동시에 고칠 수 있다.
        - SUBSIZED: 이 Spliterator 그리고 분할되는 모든 Spliterator는 SIZED 특성을 갖는다.

