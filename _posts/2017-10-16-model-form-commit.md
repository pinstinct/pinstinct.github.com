---
layout: post
title: 장고 모델폼 commit 키워드 인수
categories: patentstart
tags: [python, django, project]
---

## `commit` 키워드 인수
Froala를 적용하고 실제 기능을 구현하다 보니, 폼 저장시 `IntegrityError` 에러가 발생한다.

`foreign key`로 지정해둔 필드에 데이터가 없기 때문이다. 모델폼을 사용하고 있으므로, `commit=False`를 사용하는 방법이 적절해보였다.

> [Django: multiple models in one template using forms](https://stackoverflow.com/questions/569468/django-multiple-models-in-one-template-using-forms/575133#575133)

```python
if request.POST():
    a_valid = formA.is_valid()
    b_valid = formB.is_valid()
    c_valid = formC.is_valid()
    # we do this since 'and' short circuits and we want to check to whole page for form errors
    if a_valid and b_valid and c_valid:
        a = formA.save()
        b = formB.save(commit=False)
        c = formC.save(commit=False)
        b.foreignkeytoA = a
        b.save()
        c.foreignkeytoB = b
        c.save()
```

### [모델 폼 `save()` 메소드](https://docs.djangoproject.com/ko/1.11/topics/forms/modelforms/#the-save-method)

`save()` 메소드는 `commit` 키워드 인수를 제공한다. `save(commit=False)`를 호출하면, 아직 데이터베이스에 저장되지 않은 객체를 반환한다. 이 경우 `save()` 메소드를 호출하는 것은 너에게 달려있다. 저장하기 전에 처리해줘야할 객체가 있다면 유용한 방법이다. 디폴트는 `commit=True`이다.

다대다 모델에서 `commit=False`를 사용할 경우, 사이드 이펙트가 있다.
