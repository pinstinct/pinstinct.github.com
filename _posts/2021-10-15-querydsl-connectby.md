---
layout: post
title: QueryDsl에서 connect by 사용하기
categories: querydsl
tags: [java, spring, querydsl]
excerpt: QueryDsl에서 기능을 확인하고 적용하는 방법을 예(connect by)를 이용해 정리합니다.
---


## Oracle, Hierarchical Queries 

> https://docs.oracle.com/cd/B19306_01/server.102/b14200/queries003.htm


```sql
SELECT *
FROM A
CONNECT BY LEVEL <= 3
```

## QueryDsl, connectBy

`CONNECT BY` 쿼리가 오라클의 기능인 것을 확인하고 아래의 테스트 코드를 참고했다.

> https://github.com/querydsl/querydsl/blob/master/querydsl-sql/src/test/java/com/querydsl/sql/oracle/OracleQueryTest.java

또한, 구글에서 `connectby site: https://querydsl.com/static/querydsl/4.4.0` 검색어를 이용해 QueryDsl 자바 문서를 찾았다.

그런데, `connectBy()` 메소드가 호출이 안 되는 것이다.
[QueryDsl 자바 문서](https://querydsl.com/static/querydsl/4.4.0/apidocs/com/querydsl/sql/oracle/AbstractOracleQuery.html#connectBy-com.querydsl.core.types.Predicate-)를 살펴보면 `OracleQuery` 객체를 사용해 호출하면 되는데 현재 프로젝트에서는 `OracleQueryFactory` 객체를 사용하고 있다.

그렇기 때문에 아래와 같이 사용해야 `connectBy()` 메소드를 호출할 수 있었다.

```java
private final OracleQueryFactory queryFactory;
import static com.querydsl.sql.oracle.OracleGrammar.level;  // level pseudocolumn

queryFactory
  .query()  // 쿼리를 먼저 호출
  .select(all)
  .from(A)
  .connectBy(level.loe(3))
```
