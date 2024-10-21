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
