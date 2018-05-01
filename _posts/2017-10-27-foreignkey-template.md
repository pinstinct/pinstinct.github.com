---
layout: post
title: 외래키 관계 객체 템플릿으로 출력
categories: patentstart
tags: [python, django, project]
---

> [Query Django Object Lists of ForeignKey](https://stackoverflow.com/questions/42403375/query-django-object-lists-of-foreignkey)

외래키 관계 모델의 데이터를 아래와 같은 구조로 출력하려고 한다.
```
reporter.first_name
    article.headline
    article.headline
    article.headline
reporter.first_name
    article.headline
    article.headline
    article.headline
    ...
```

```python
# models.py
from django.db import models

class Reporter(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
    email = models.EmailField()

class Article(models.Model):
    headline = models.CharField(max_length=100)
    pub_date = models.DateField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)
```

```python
# views.py
context = {
    'repoter_article': Reporter.objects.all()
}
```

템플릿에서 다음과 같이 출력한다.

<script src="https://gist.github.com/pinstinct/b127944c3ef7158d2e6147b3c7c15736.js"></script>
