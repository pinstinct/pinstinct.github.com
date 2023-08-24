---
layout: post
title: SimpleDateFormat vs DateTimeFormatter
categories: java
tags: [java]
---

### SimpleDateFormat

SimpleDateFormat: 날짜를 파싱(text -> date) 또는 포매팅(date -> text)할 때 사용하는 locale-sesitive 클래스이다.

하지만 멀티스레드 환경에서 Thread-safe를 보장하지 않는다. 내부적으로 Calender 클래스를 인스턴스화해서 사용하는데 멀티쓰레드 충돌을 방지해주는 장치가 없기 때문이다.

`new` 연산자를 통해 새로운 인스턴스를 생성해 해결할 수 있다. SimpleDateFormat은 비싼 객체로 CPU 자원을 많이 사용한다.

> [[Java]SimpleDateFormat을 쓰면 안된다고? (feat.Thread-Safe)](https://woonys.tistory.com/entry/JavaSimpleDateFormat%EC%9D%84-%EC%93%B0%EB%A9%B4-%EC%95%88%EB%90%9C%EB%8B%A4%EA%B3%A0-featThread-Safe)


### DateTimeFormatter

JDK8 부터 DateTimeFormatter 사용을 권장한다. 

- LocalDate: yyyy.MM.dd
- LocalTime: HH.mm.ss
- LocalDateTime: yyyy.MM.dd HH.mm.ss (+나노 초 가능)

```java
// 날짜 생성
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

LocalDateTime today = LocalDateTime.now();
String today2 = today.format(formatter);

// 날짜 연산
LocalDateTime afterHour = today.plusDays(1);

// 날짜 비교
boolean a = today.isBefore(afterHour);
boolean b = today.isAfter(afterHour);
boolean c = today.isEqual(afterHour);

// String -> LocalDateTime
String dateTime = "2023-08-21";
LocalDateTime localDateTime = LocalDateTime.parse(dateTime);
```

> [DateTimeFormatter 기본 사용법](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=pajamasi&logNo=220996273157)
