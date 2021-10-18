---
layout: post
title: QueryDsl에서 from 혹은 join에 subQuery 
categories: querydsl
tags: [java, spring, querydsl]
excerpt: QueryDsl에서 sub query를 하는 법을 정리합니다.
---

### sub query


`SubQueryExpression`은 sub query를 표현합니다. 아래와 같이 subQuery를 선언합니다.


```java
import static com.querydsl.sql.SQLExpressions.select;

import com.querydsl.core.types.SubQueryExpression;

QSurvey survey = QSurvey.survey;

SubQueryExpression<String> subQuery = select(survey.name)
  .from(survey);

```

그리고 sub query의 별명을 사용하기 위해 아래와 같이 선언합니다.

```java
import static com.querydsl.core.types.dsl.Expressions.stringPath;

import com.querydsl.core.types.dsl.StringPath;


final StringPath subQueryAlias = stringPath("subQueryAlias"); 

```

```java
QEmployee employee = QEmployee.employee;

return queryFactory
  .select(employee.id)
  .from(employee)
  .join(subQuery, subQueryAlias)
  .on(employee.name.eq(stringPath(subQueryAlias, "name")))
  .fetch();

```


### multi table union sub query 


```java
import static com.querydsl.sql.SQLExpressions.unionAll;

QEmployee employee = QEmployee.employee;
QSurvey survey = QSurvey.survey;
QPerson person = Qperson.person;

final String id = "id";
final String name = "name";

final StringPath unionQueryAlias = stringPath("unionQueryAlias");

SubQueryExpression<Tuple> employeeQuery = select(
    http://employee.id.as(id),
    http://employee.name.as(name))
    .from(employee);

SubQueryExpression<Tuple> surveyQuery = select(
    http://survey.employeeId.as(id),
    http://survey.name.as(name))
    .from(survey);

SubQueryExpression<Tuple> unionQuery = unionAll(employeeQuery, surveyQuery);

return queryFactory
  .select(
    numberPath(Long.class, unionQueryAlias, id),
    stringPath(unionQueryAlias, name))
  .from(unionQuery, unionQueryAlias)
  .join(person)
  .on(numberPath(Long.class, unionQueryAlias, id).eq(person.id))
  .fetch();

```
