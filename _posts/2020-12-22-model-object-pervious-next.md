---
layout: post
title: 모델 객체의 이전, 다음 객체 가져오기  
categories: experience
tags: [python, django, project]
excerpt:  
---

`obj`는 `User` 모델 객체입니다.

```python
# previous object
User.objects.filter(pk__lt=obj.pk).order_by("-pk").first()
```


```python
# next object
User.objects.filter(pk__gt=obj.pk).order_by("pk").first()
```