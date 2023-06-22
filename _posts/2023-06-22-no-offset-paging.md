---
layout: post
title: No Offset Paging
categories: experience
tags: [sql, project]
---

더 빠르게 페이징 처리를 할 수 있는 no offset paging 처리에 대해 알게되어서 글로 남겨둔다.

> [참고 블로그](https://jojoldu.tistory.com/528)

실제 사용예시

```sql
SELECT *
FROM (
  SELECT
    (ROW_NUMBER() OVER()) AS page
    , A.*
  FROM (
    SELECT *
    FROM post_tb
    WHERE del_yn = 'N'
    ORDER BY created_datetime DESC
  ) A
) B
WHERE B.page > 2
LIMIT 1 
```

