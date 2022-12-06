---
layout: post
title: date query optimization
categories: experience
tags: [python, sqlalchemy, project]
---

## 쿼리 튜닝

쿼리 속도 개선 작업을 기록합니다. (1584ms → 166ms)

### 원인

기존 sqlalchemy orm 쿼리는 sub_max, sub_old를 구하는 subquery를 이용해 result 쿼리를 실행할 때마다 수행됩니다. 

```python
sub_max = session.query(func.MAX(table.date).label("max")).subquery()
if not recent:
    sub_old = (
        session.query(func.MAX(table.date).label("old"))
        .filter(table.date < sub_max.c.max)
        .subquery()
    )

result = (
    session.query(table)
    .filter(
        table.id.in_(ids),
        table.date == sub_max.c.max if recent else table.date == sub_old.c.old,
    )
    .all()
)

```

### 결론

dates 쿼리를 통해 sub_max, sub_old의 값을 구합니다. 이 때, dates 쿼리의 실행 속도를 개선하기 위해 id 필터를 추가합니다.

```python
dates = (
    session.query(table.date.label("date"))
    .filter(table.id.in_(ids))
    .group_by(table.date.desc())
    .limit(2)
    .all()
)
sub_max = dates[0]
if not recent:
    sub_old = dates[1]
result = (
    session.query(table)
    .filter(
        table.id.in_(ids),
        table.date == sub_max.date if recent else table.date == sub_old.date,
    )
    .all()
)

```
