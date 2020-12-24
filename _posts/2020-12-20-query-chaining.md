---
layout: post
title: 쿼리 필터 체이닝 
categories: experience
tags: [python, django, project]
excerpt: 필터 조건에 리스트를 넣어 쿼리를 만듭니다.  
---


여러 개의 값이 있는 리스트를 필터에 넣어 검색하고 싶었습니다.

```python
User.objects.filter(first_name__contains=['x','y','z'])
```

또한 최대한 간결한 코드로 작성하기 위해 검색해보니 다음과 같은 문서를 찾을 수 있었습니다.

> [Django: Query using contains each value in a list](https://stackoverflow.com/questions/4824759/django-query-using-contains-each-value-in-a-list)

```python
import operator
from functools import reduce

from django.db.models import Q

User.objects.filter(reduce(operator.and_, (Q(first_name__contains=x) for x in ['x', 'y', 'z'])))
```

- 우선 [Comprehension](https://mingrammer.com/introduce-comprehension-of-python/)을 사용해 `Q`객체를 만듭니다.
- [reduce](https://docs.python.org/3.0/library/functools.html#functools.reduce)를 사용해 `Q`객체와 [and_](https://docs.python.org/3/library/operator.html#operator.and_) 연산자를 누적합니다.
- 쿼리 필터에 위의 연산결과를 적용합니다.




