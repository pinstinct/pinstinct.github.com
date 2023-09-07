---
layout: post
title: 개월 수 구하기(Duration vs Period vs ChronoUnit)
categories: experience
tags: [java, project]
---

### 문제 상황

오늘부터 입력받은 날짜까지의 개월 수를 구하는 레거시 코드에서 버그가 발견됐다. 해당 로직은 SimpleDateFormat과 Calender 라이브러리를 사용중이었다. 오늘날짜를 제대로 가져오지 못하고, 개월 수 계산도 제대로 이루어지지 않는 상태였다. 

### Duration vs Period vs ChronoUnit

- Duration: 두 시간 사이의 초, 나노 초 단위 간격을 나타낸다.

```java
LocalTime start = LocalTime.of(10, 35, 40);
LocalTime end = LocalTime.of(10, 36, 50, 800);

Duration duration = Duration.between(start, end);

System.out.println("Seconds: " + duration.getSeconds());  // Seconds: 70
System.out.println("Nano Seconds: " + duration.getNano());  // Nano Seconds: 800
```

- Period: 두 날짜 사이의 간격을 년, 월, 일 단위로 나타낸다.

```java
LocalDate startDate = LocalDate.of(1939, 9, 1);
LocalDate endDate = LocalDate.of(1945, 9, 2);

Period period = Period.between(startDate, endDate);

System.out.println("Years: " + period.getYears());  // Years: 6
System.out.println("Months: " + period.getMonths());  // Months: 0
System.out.println("Days: " + period.getDays());  // Days: 1
```

- ChronoUnit: 객체를 생성하지 않고 간편하게 간격을 나타낸다.

```java
LocalDate startDate = LocalDate.of(1939, 9, 1);
LocalDate endDate = LocalDate.of(1945, 9, 2);

long months = ChronoUnit.MONTHS.between(startDate, endDate);
long weeks = ChronoUnit.WEEKS.between(startDate, endDate);
long days = ChronoUnit.DAYS.between(startDate, endDate);

System.out.println("Months: " + months);  // Months: 72
System.out.println("Weeks: " + weeks);  // Weeks: 313
System.out.println("Days: " + days);  // Days: 2193

LocalTime startTime = LocalTime.of(10, 35, 40);
LocalTime endTime = LocalTime.of(10, 36, 50, 800);

long hours = ChronoUnit.HOURS.between(startTime, endTime);
long minutes = ChronoUnit.MINUTES.between(startTime, endTime);
long seconds = ChronoUnit.SECONDS.between(startTime, endTime);

System.out.println("Hours: " + hours);  // Hours: 0
System.out.println("Minutes: " + minutes);  // Minutes: 1
System.out.println("Seconds: " + seconds);  // Seconds: 70
```

> [[Java8 Time API] Duration과 Period 사용법 (+ChronoUnit)](https://www.daleseo.com/java8-duration-period/)


### 해결 방법 

Period 이용한 방법

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
LocalDate requestDate = LocalDate.parse("2022-02-02", formatter);
ZonedDateTime kst = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));

Period period = Period.between(requestDate, kst.toLocalDate());
int result = period.getYears() * 12 + period.getMonths();
```

ChronoUnit 이용한 방법

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
LocalDate requestDate = LocalDate.parse("2022-02-02", formatter);
ZonedDateTime kst = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));

long result = ChronoUnit.MONTHS.between(kst.toLocalDate(), requestDate);
```

ChronoUnit이 더 효율적이기 때문에 ChronoUnit을 사용한다.

> [Duration.between vs ChronoUnit.between](https://stackoverflow.com/a/66248485)
