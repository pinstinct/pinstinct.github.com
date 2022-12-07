---
layout: post
title: sql optimizer
categories: experience
tags: [sql, mysql, sqlalchemy]
excerpt: sql을 분석해 쿼리 속도를 향상시킨 작업을 기록합니다.
---

## SQL 옵티마이저 실행 계획 및 최적화

`EXPLAIN` 키워드를 `SELECT` 앞에 붙여 실행 계획을 살펴볼 수 있습니다. 

### `EXPLAIN` 컬럼

`EXPLAIN` 키워드를 붙여서 실행하면 테이블 형식의 결과를 확인 할 수 있다. 다음은 각 컬럼에 대한 설명이다.

- id
- select_type
    - SIMPLE: 단순 SELECT 쿼리(UNION 또는 subqury를 사용하지 않음)
    - PRIMARY: 가장 바깥쪽 SELECT
    - DERIVED: SELECT 쿼리에서 파생(FROM 절에 있는 subquery), 메모리나 디스크에 임시 테이블을 만드는 경우를 의미
- table: 결과 열이 참조하는 테이블
    - `<>`: 임시 테이블
- partitions: 어떤 파티션을 읽었는지 알려주는 정보
- type: 적절하게 인덱스를 참조했는지 확인하는 컬럼, 성능이 좋은 순서대로 나열
    - system: 하나의 열만 가지는 경우
    - const: WHERE 절에 PRIMARY KEY 또는 UNIQUE 인덱스를 상수 값과 비교하는 경우
    - eq_ref: 가장 최선의 JOIN 타입. 이 때, 인덱스는 PRIMARY KEY 또는 UNIQUE 인덱스
    - ref: 테이블에서 읽어온 각각의 열을 조합하기 위해 매칭되는 인덱스 값을 가진 모든 열을 읽어 오는 경우(eq_ref 보다 느리지만 엄청 빠른 인덱스)
    - fulltext: FULLTEXT 인덱스를 사용하는 경우
    - ref_or_null: ref와 같은데 NULL 비교가 추가되는 경우
    - index_merge: 인덱스 병합 최적화가 사용되었음을 의미
    - unique_subquery
    - index_subquery
    - range: 주어진 범위에 들어 있는 열만 추출하는 경우
    - index: ALL과 동일하지만, 인덱스 트리만을 스캔하는 점이 다름
    - ALL: 전체 테이블을 스캔. 가장 나쁜 방법이지만 잘못된 인덱스를 타는 경우보다는 나음
- possible_keys: 후보 인덱스 목록
    - NULL: 연관된 인덱스가 존재하지 않는 경우
- key: 실제 사용 예정인 인덱스
    - possible_keys 컬럼에 있는 인덱스를 사용하거나 무시하기 위해 `FORCE INDEX`, `USE INDEX`, `IGNORE INDEX`를 쿼리에서 사용
- key_len: 키의 길이
- ref
- rows
- filtered 
- Extra
    - Using filesort: `ORDER BY`를 처리하기 위해 적절한 인덱스를 사용하지 못하는 경우, 소트 버퍼에서 레코드를 복사해서 정렬하는 비효율적인 작업을 하는 것을 의미 (튜닝 대상)
    - Using index: 인덱스 트리에 있는 정보만을 가지고 컬럼 정보 추출(극강 효율)
    - Using temporary: 쿼리를 처리하는 동안 중간 결과를 담아 두는 임시 테이블을 생성. 쿼리가 컬럼을 서로 다르게 목록화 하는 `GROUP BY`, `ORDER BY` 구문을 가지고 있는 경우 발생 (튜닝 대상)
    - Using where

> [MySQL 8.0 EXPLAIN Output Format](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html)

> [MySQL 5.0 EXPLAIN을 사용해서 쿼리 최적화 하기](http://www.mysqlkorea.com/sub.html?mcode=manual&scode=01&m_no=21444&cat1=7&cat2=217&cat3=227&lang=k)

> [DB의 꽃 옵티마이저의 실행 계획 수립](https://jeong-pro.tistory.com/243)


### Join 고려 사항

- JOIN 순서: A 테이블은 C 테이블에 없는 A_ID를 모두 가지고 있다. 두 테이블을 조인 할 때, A 테이블을 기준으로 찾는다면 A 테이블을 모두 조회하게 된다. 그렇기 때문에 반대로 C 테이블을 기준으로 A 테이블을 조인한다.
- STRAIGHT_JOIN: MySQL 내부 적으로 join 순서를 바꾸는 경우, `STRAIGHT_JOIN`문을 명시해준다.

> [[MySql] JOIN 속도를 빠르게! EXPLAIN, STRAIGHT_JOIN](https://marobiana.tistory.com/35)

### Order by 최적화

> [MySQL : ORDER BY 최적화 (번역)](https://blog.naver.com/ez_/140129818967)


## ORM 수정

sqlalchemy ORM의 raw 쿼리를 `EXPLAIN` 으로 확인해보니 Extra 컬럼에 "Using index condition; Using temporary; Using filesort" 출력되었습니다. 그래서 불필요하게 사용중인 `ORDER BY`, `GROUP BY` 구문을 제거했습니다. Extra 컬럼에 "Using index condition"만 남았습니다.

또한 추가적으로 다음과 같이 쿼리를 수정했습니다. 쿼리 수행 후, 결과를 순회하는 로직이 있어 `COUNT()` 집계 함수를 제거하고 해당 로직에서 계산하게 수정했습니다.

수정 작업 후 쿼리 실행 속도가 약간 개선됐습니다. (평균 2500ms → 평균 2000ms)

raw 쿼리 속도는 2600ms에서 158ms으로 엄청나게 빨라지나, ORM 수행 속도는 400ms 정도밖에 줄어들지 않았습니다. ORM join의 문제인 것 같은데, DB 구조가 FK 연결이 없는 구조라 ORM을 수정할 수 있는 부분이 더 없었습니다.
