---
layout: post
title: 모던 자바 인 액션 - Part3
categories: book
tags: [book, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[모던 자바 인 액션](https://www.yes24.com/Product/Goods/77125987) 책을 읽고 정리한 내용입니다.

> [Repository](https://github.com/pinstinct/java-in-action)

## Part3 스트림과 람다를 이용한 효과적 프로그래밍

### Chapter8. 컬렉션 API 개선

#### 컬렉션 팩토리

- 자바 9에서 작은 컬렉션 객체를 쉽게 만들 수 있는 방법을 제공한다.
- 리스트 팩토리: 변경할 수 없는 리스트가 만들어진다. 이런 제약은 컬렉션이 의도치 않게 변하는 것을 막을 수 있다. 또한 null 요소는 금지하므로 의도치 않은 버그를 방지한다.

```java
List<String> friends = List.of("Raphael", "Olivia", "Thibaut");

// UnsupportedOperationException 발생
friends.set(0, "kim");  // 요소 수정 불가능
friends.add("lee");  // 요소 추가 불가능
```
- 오버로딩 vs 가변인수
    - 내부적으로 가변 인수 버전은 추가 배열을 할당해서 리스트로 감싼다. 따라서 배열을 할당하고 초기화하면 나중에 가비지 컬렉션 비용을 지불해야 한다.
    - 고정된 숫자의 요소(최대 열개까지)를 API로 정의하므로 이런 비용을 제거할 수 있다.
    - `List.of`로 열 개 이상의 요소를 가진 리스트를 만들 수 있지만 이 때는 가변 인수를 이용하는 메서드가 사용된다.
    - `Set.of`, `Map.of`에서도 이와 같은 패턴이 등장한다.
- 집합 팩토리

```java
Set<String> friends = Set.of("Raphael", "Olivia", "Thibaut");

// 중복 요소가 있으면, IllegalArgumentException 발생
Set<String> friends2 = Set.of("Raphael", "Olivia", "Olivia");
```

- 맵 팩토리

```java
// 10개 이하인 경우
Map<String, Integer> ageOfFriends = Map.of("Raphael", 30, "Olivia", 25, "Thibaut", 26);

// 10개 초과인 경우
// Map.entry<K, V> 객체를 인수로 받으며 가변 인수로 구현된 Map.ofEntries 팩토리 메서드 이용
Map<String, Integer> ageOffFriends = Map.ofEntries(
    entry("Raphael", 30),
    entry("Olivia", 25),
    entry("Thibaut", 26)
);
```

#### 리스트와 집합 처리

- 자바 8에서는 List, Set 인터페이스에 다음과 같은 메서드를 추가했다. 이 메서드들은 새로운 결과를 만드는 대신 호출한 컬렉션 자체를 바꾼다.
    - removeIf: 프레디케이트를 만족하는 요소를 제거
    - replaceAll: 리스트에 이용할 수 있는 기능으로 UnaryOperator 함수를 이용해서 요소를 바꾼다.
    - sort: 리스트를 정렬

#### 맵 처리

- 자바 8에서는 Map 인터페이스에 몇 가지 디폴트 메서드를 추가했다. 자주 사용되는 패턴을 개발자가 직접 구현할 필요가 없도록 이들 메서드를 추가했다.
- forEach 메서드

```java
ageOffFriends.forEach((friend, age) -> 
    System.out.println(friend + " is " + age + " years old"));
```

- 정렬 메서드
    - `Entry.comparingByValue`
    - `Entry.comparingByKey`

```java
favoriteMovies.entrySet().stream()
    .sorted(Entry.comparingByKey())
    .forEachOrdered(System.out::println);
```

- getOrDefault 메서드

```java
favoriteMovies.getOrDefault("Olivia", "Matrix");
```

- 계산 패턴
    - computeIfAbsent: 제공된 키에 해당하는 값이 없으면(값이 없거나 null), 키를 이용해 새 값을 계산하고 맵에 추가한다.
        - 정보를 캐시할 때 활용 
    - computeIfPresent: 제공된 키가 존재하면 새 값을 계산하고 맵에 추가한다.
    - compute: 제공된 키로 새 값을 계산하고 맵에 저장한다.

```java
Map<String, List<String>> friendsToMovies = new HashMap<>();
friendsToMovies.computeIfAbsent("Raphael", name -> new ArrayList<>())
    .add("Star wars");
```

- 삭제 패턴

```java
favoriteMovies.remove(key, value);
```

- 교체 패턴
    - replaceAll: BiFunction을 적용한 결과로 각 항목의 값을 교체한다.
    - repalce: 키가 존재하면 맵의 값을 바꾼다. 키가 특정 값으로 매핑되었을 때만 값을 교체하는 오버로드 버전도 있다.
    
```java
favoriteMovies.replaceAll((friend, movie) -> movie.toUpperCase());
```

- 합침: 두 개의 맵에 값을 합치거나 수정한다.
    - putAll: 중복된 키가 없는 경우
    - merge: 중복된 키를 어떻게 합칠지 결정하는 BiFunction을 인수로 받는다. 널값과 관련된 상황도 처리한다.

```java
// 중복된 키가 없는 경우
Map<String, String> everyone = new HashMap<>(family);
everyone.putAll(friends);

// 중복된 키가 있는 경우
Map<String, String> everyone = new HashMap<>(family);
friends.forEach((k, v) -> everyone.merge(k, v,
    (movie1, movie2) -> movie1 + " & " + movie2));  // 중복된 키가 있으면 두 값을 연결

// merge를 이용한 초기화 검사
Map<String, Long> moviesToCount = new HashMap<>();
String movieName = "JamesBond";
moviesToCount.merge(movieName, 1L, (key, count) -> count + 1L);  // merge(key, 초기화 값, BiFunction)
```

#### 개선된 ConcurrentHashMap

- ConcurrentHashMap 클래스는 동시성 친화적이며 최신 기술을 반영한 HashMap 버전이다. ConcurrentHashMap은 내부 자료구조의 특정 부분만 잠궈 동시 추가, 갱신 작업을 허용한다. 따라서 동기화된 Hashtable 버전에 비해 읽기 쓰기 연산 성능이 월등하다. 참고로, 표준 HashMap은 비동기로 동작한다.
- 리듀스와 검색
    - 스트림과 비슷한 종류의 세 가지 새로운 연산 지원
        - `forEach`: 각 (키, 값) 쌍에 주어진 액션을 실행
        - `reduce`: 모든 (키, 값) 쌍을 제공된 리듀스 함수를 이용해 결과로 합침
        - `search`: 널이 아닌 값을 반환할 때까지 (키, 값) 쌍에 함수를 적용
    - 다음과 같은 네 가지 연산 형태 지원
        - 키, 값으로 연산: forEach, reduce, search
        - 키로 연산: forEachKey, recudeKeys, searchKeys
        - 값으로 연산: forEachValue, reduceValues, serchValues
        - Map.Entry 객체로 연산: forEachEntry, reduceEntries, searchEntries
    - 이들 연산에 제공한 함수는 계산이 진행되는 동안 바뀔 수 있는 객체, 값, 순서 등에 의존하지 않아야 한다.
    - 또한 이들 연산에 병렬성 기준값(threshold)을 지정해야 한다. 맵의 크기가 주어진 기준값보다 작으면 순차적으로 연산을 실행한다. 기준값을 1로 지정하면 공통 스레드 풀을 이용해 병렬성을 극대화한다.
    - int, long, double 등의 기본값에는 전용 each reduce 연산이 제공되므로, reduceValuesToInt, reduceKeysToLong 등을 이용하면 박싱 작업을 할 필요가 없다.

```java
ConcurrentHashMap<String, Long> map = new ConcurrentHashMap<>();
long parallelismThreshold = 1;
Optional<Long> maxValue = Optional.ofNullable(map.reduceValues(parallelismThreshold, Long::max));
```

- 계수
    - `mappingCount`: 맵의 매핑 개수를 반환한다. 기존의 `size` 메서드 대신 int를 반환하는 `mappingCount`를 사용하는 것이 좋다.
- 집합뷰
    - `keySet`: ConcurrentHashMap을 집합 뷰로 반환하는 메서드.


### Chapter9. 리팩터링, 테스팅, 디버깅

- 람다 표현식으로 전략(strategy), 템플릿 메서드(template method), 옵저버(observer), 의무 체인(chain of responsibility), 팩토리(factory) 등의 객체지향 디자인 패턴을 어떻게 간소화할 수 있는지 살펴본다.

#### 가독성과 유연성을 개선하는 리팩터링

- 람다, 메서드 참조, 스트림 등의 기능을 이용해서 더 가독성이 좋고 유연한 코드로 **리팩터링**하는 방법을 설명한다.
- 코드 가독성 개선
    - 코드 가독성이 좋다는 것은 '어떤 코드를 다른 사람도 쉽게 이해할 수 있음'을 의미한다.
    - 코드 가독성을 높이려면 코드의 문서화를 잘하고, 표준 코딩 규칙을 준수하는 등의 노력을 기울여야 한다.
- 람다, 메서드 참조, 스트림을 활용해서 코드 가독성을 개선할 수 있는 간단한 세 가지 리팩토링 예제
    - 익명 클래스를 람다 표현식으로 리팩터링하기: 하나의 추상 메서드를 구현하는 익명 클래스는 람다 표현식으로 리팩터링 할 수 있다.
    - 람다 표현식을 메서드 참조로 리팩터링하기
    - 명령형 데이터 처리를 스트림으로 리팩터링하기: 반복자를 이용한 기존의 모든 컬렉션 처리 코드를 스트림 API로 변경한다. 
- 코드 유연성 개선: 람다 표현식을 이용하면 동작 파라미터화를 쉽게 구현할 수 있다.
    - 함수형 인터페이스 적용: 람다 표현식을 이용하려면 함수형 인터페이스가 필요하다.
    - 조건부 연기 실행(conditional defeered execution)
        - 객체 상태를 자주 확인하거나, 객체의 일부 메서드를 호출하는 상황이라면 내부적으로 객체의 상태를 확인한 다음에 메서드를 호출하도록 새로운 메서드를 구현하는 것이 좋다. 그러면 코드 가독성이 좋아질 뿐 아니라 캡슐화도 강화된다.

    ```java
    Logger logger = Logger.getLogger("tester");
    // 람다를 이용하면, 특정 조건(FINER)에서만 메시지가 생성될 수 있도록 메시지 생성 과정을 연기(defer)한다.
    logger.log(Level.FINER, () -> "Problem: " + generateDiagnostic());
    ```

    - 실행 어라운드(execute around): 매번 같은 준비, 종료 과정을 반복적으로 수행하는 코드가 있다면 이를 람다로 변환할 수 있다. 준비, 종료 과정을 처리하는 로직을 재사용함으로써 코드 중복을 줄일 수 있다.

#### 람다로 객체지향 디자인 패턴 리팩터링하기

- 디자인 패턴(design pattern): 공통적인 소프트웨어 문제를 설계할 때 재사용할 수 있는, 검증된 청사진을 제공한다.
    - 예를 들어 구조체와 동작하는 알고리즘을 서로 분리하고 싶을 때 방문자 디자인 패턴(visitor design pattern)을 사용할 수 있다. 또 다른 예제로 싱글턴 패턴(singleton pattern)을 이용해서 클래스 인스턴스화를 하나의 객체로 제한할 수 있다.
- 람다를 이용하면 이전에 디자인 패턴으로 해결하던 문제를 더 쉽고 간단하게 해결할 수 있다.
- 전략(strategy)
    - 전략 패턴은 한 유형의 알고리즘을 보유한 상태에서 런타임에 적절한 알고리즘을 선택하는 기법이다.
    - 전략 패턴은 세 부분으로 구성
        - 알고리즘을 나타내는 인터페이스(Strategy 인터페이스)
        - 다양한 알고리즘을 나타내는 한 개 이상의 인터페이스 구현(ConcreateStrategyA, ConcreateStrategyB, ... 같은 구체적인 구현 클래스)
        - 전략 객체를 사용하는 한 개 이상의 클라이언트

    ```java
    public interface ValidationStrategy {
    boolean execute(String s);
    }

    public class Validator {

    private final ValidationStrategy strategy;

    public Validator(ValidationStrategy strategy) {
        this.strategy = strategy;
    }

    public boolean validate(String s) {
        return strategy.execute(s);
    }
    }

    // 전략을 구현하는 새로운 클래스를 구현할 필요 없이 람다 표현식을 직접 전달
    Validator numericValidator = new Validator(s -> s.matches("\\d+"));
    boolean b1 = numericValidator.validate("aaaa");
    assertThat(b1).isFalse();

    Validator lowerCaseValidator = new Validator(s -> s.matches("[a-z]+"));
    boolean b2 = lowerCaseValidator.validate("bbb");
    assertThat(b2).isTrue();
    ```

- 템플릿 메서드(template method)
    - 템플릿 메서드는 '이 알고리즘을 사용하고 싶은데 그대로는 안 되고 조금 고쳐야 하는' 상황에 적합하다.

    ```java
    public class OnlineBankingLambda {

    /**
     * 온라인 뱅킹 알고리즘이 해야 할 일
     */
    public void processCustomer(int id, Consumer<Customer> makeCustomerHappy) {
        Customer c = Database.getCustomerWithId(id);
        makeCustomerHappy.accept(c);
    }

    // 더미 Customer 클래스
    static public class Customer {}

    // 더비 Database 클래스
    static public class Database {

        static Customer getCustomerWithId(int id) {
        return new Customer();
        }
    }
    }

    new OnlineBankingLambda().processCustomer(122, customer -> System.out.println(customer));
    ```

- 옵저버(observer)
    - 어떤 이벤트가 발생했을 때 한 객체(주제, subject)가 다른 객체 리스트(옵저버, observer)에 자동으로 알림을 보내야 하는 상황에서 옵저버 디자인 패턴을 사용한다.

    ```java
    public interface Observer {
    void notify(String tweet);
    }

    public interface Subject {
    void registerObserver(Observer observer);
    void notifyObservers(String tweet);
    }

    public class Feed implements Subject {

    private final List<Observer> observers = new ArrayList<>();

    @Override
    public void registerObserver(Observer observer) {
        this.observers.add(observer);
    }

    @Override
    public void notifyObservers(String tweet) {
        observers.forEach(observer -> observer.notify(tweet));
    }
    }

    Feed f = new Feed();

    // 옵저버를 명시적으로 인스턴스화하지 않고 람다 표현식으로 직접 전달해서 실행할 동작 지정
    // 이 예제에서는 실행해야 할 동작이 비교적 간단하므로 람다 표현식으로 불필요한 코드를 제거하는 것이 바람직
    f.registerObserver(tweet -> {
    if (tweet != null && tweet.contains("money")) {
        System.out.println("Breaking news in NY| " + tweet);
    }
    });
    f.registerObserver(tweet -> {
    if (tweet != null && tweet.contains("queen")) {
        System.out.println("Yet more news from London..." + tweet);
    }
    });
    f.notifyObservers("The queen said her favourite book is Modern Java in Action!");
    ```

- 의무 체인(chain of responsibility): 한 객체가 어떤 작업을 처리한 다음에 다른 객체로 결과를 전달하고, 다른 객체도 해야 할 작업을 처리한 다음에 또 다른 객체로 전달하는 식이다.

    ```java
    // 함수 체인과 비슷
    UnaryOperator<String> headerProcessing = (String text) -> "From Raoul, Mario and Alan: "
        + text;  // 첫 번째 작업 처리 객체
    UnaryOperator<String> spellCheckerProcessing = (String text) -> text.replaceAll("labda", "lambda");  // 두 번째 작업 처리 객체
    Function<String, String> pipeline = headerProcessing.andThen(spellCheckerProcessing);  // 동작 체인으로 두 함수를 조합
    String result = pipeline.apply("Aren't labdas really sexy?!!");
    ```

- 팩토리(factory): 인스턴스화 로직을 클라이언트에 노출하지 않고 객체를 만들 때 팩토리 디자인 패턴을 사용한다.

```java

public class ProductFactoryLambda {

  public static Product createProduct(String name) {  // 여러 인수를 전달하는 상황에서는 이 기법을 사용하기 어려움
    Supplier<Product> p = map.get(name);
    if (p != null) return p.get();
    throw new IllegalArgumentException("No such product " + name);
  }

  static interface Product {}
  static class Loan implements Product {}
  static class Stock implements Product {}
  static class Bond implements Product {}

  final static Map<String, Supplier<Product>> map = new HashMap<>();
  static {
    map.put("loan", Loan::new);
    map.put("stock", Stock::new);
    map.put("bond", Bond::new);
  }
}

Product p = ProductFactoryLambda.createProduct("loan");
```

#### 디버깅

- 정보 로깅
    - `forEach`를 호출하는 순간 전체 스트림이 소비된다.
    - `peek`은 스트림의 각 요소를 소비한 것처럼 동작을 실행한다. 하지만 forEach처럼 실제로 스트림의 요소를 소비하지 않는다.

```java
List<Integer> result = numbers.stream()
    .peek(x -> System.out.println("from stream: " + x))  // 소스에서 처음 소비한 요소를 출력
    .map(x -> x + 17)
    .peek(x -> System.out.println("after map: " + x))  // map 동작 실행 결과를 출력
    .filter(x -> x % 2 == 0)
    .peek(x -> System.out.println("after filter: " + x))  // filter 동작 후 선택된 숫자를 출력
    .limit(1)
    .peek(x -> System.out.println("after limit: " + x))  // limit 동작 후 선택된 숫자를 출력
    .toList();
```
