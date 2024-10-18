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

- 교체 패턴
    - replaceAll: BiFunction을 적용한 결과로 각 항목의 값을 교체한다.
    - repalce: 키가 존재하면 맵의 값을 바꾼다. 키가 특정 값으로 매핑되었을 때만 값을 교체하는 오버로드 버전도 있다.
    
