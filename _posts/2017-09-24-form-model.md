---
layout: post
title: 장고 모델폼
categories: experience
tags: [python, django, project]
---

# [Creating forms from models](https://docs.djangoproject.com/ko/1.11/topics/forms/modelforms)

## `ModelForm`
데이터베이스 기반 앱을 만드는 경우, 장고 모델과 밀접하게 매핑되는 폼을 사용할 수 있다. 이 경우 모델에 필드를 이미 정의했기 때문에, 폼에 필드 유형을 정의하는 것은 불필요하다. 이런 이유로, 장고는 모델에서 `From` 클래스를 만들 수 있는 헬퍼 클래스를 제공한다.

```python
>>> from django.forms import ModelForm
>>> from myapp.models import Article

# Create the form class.
>>> class ArticleForm(ModelForm):
...     class Meta:
...         model = Article
...         fields = ['pub_date', 'headline', 'content', 'reporter']

# Creating a form to add an article.
>>> form = ArticleForm()

# Creating a form to change an existing article.
>>> article = Article.objects.get(pk=1)
>>> form = ArticleForm(instance=article)
```

### Field types
생성한 `Form` 클래스에는 모든 모델 필드에 대한 폼 필드가 있다. 또한, 생성된 폼 필드는 다음과 같은 속성을 가진다.
- 모델 필드가 `blank=True`면, 폼 필드에 **required**가 `False`로 설정된다.
- 폼 필드의 `label`은 모델 필드의 `verbose_name`로 설정된다.
- 폼 필드의 `help_text`는 모델 필드의 `help_text`로 설정된다.
- 모델 필드가 `choices` 세트를 가지고 있다면, 폼 필드의 **widget**은 `select`로 설정된다.

### A full example
`ModelFrom`과 `Form`의 유일한 차이점은 `save()` 메소드이다.

### Validation on a ModelForm
ModelForm 유효성을 확인에는 두 가지 주요 단계가 있다.
1. Validating the form
2. Validating the model instance

일반 폼 유효성 검사와 마찬가지로 모델 폼 유효성 검사도 `is_valid()`를 호출하거나, `errors` 속성에 액세스하고 명시적으로 `full_clean()`을 호출할 때 트리거된다.

`Model` 유효성 검사(`Model.full_clean()`)는 폼의 `clean()` 메소드가 호출된 후 폼 유효성 검사 단계에서 트리거된다.

#### Overriding the clean() method
모델 폼에서 `clean()` 메서드를 재정의하여 추가 유효성 검사를 제공 할 수 있다.

모델 객체가 결부된 모델 폼은 **instance** 속성을 포함한다. 인스턴스 속성은 해당 모델 인스턴스에 접근할 수 있는 메소드를 제공한다.

#### Considerations regarding model's error_messages
**폼 필드** 레벨 또는 **폼 메타** 레벨에 정의 된 오류 메시지는 항상 **모델 필드** 레벨에 정의 된 오류 메시지보다 우선한다.

**모델 필드**에 정의 된 오류 메시지는 **모델 유효성 검사** 단계에서 `ValidationError`가 발생하고 해당 오류 메시지가 폼 수준에서 정의되지 않은 경우에만 사용된다.

```python
from django.forms import ModelForm
from django.core.exceptions import NON_FIELD_ERRORS

class ArticleForm(ModelForm):
    class Meta:
        error_messages = {
            NON_FIELD_ERRORS: {
                'unique_together': "%(model_name)s's %(field_labels)s are not unique.",
            }
        }
```

### The `save()` method
모든 `ModelForm`에는 `save()` 메소드도 있다. 이 메소드는 폼에 바인딩 된 데이터에서 데이터베이스 개체를 만들고 저장한다. `ModelForm`의 하위 클래스는 기존 모델 인스턴스를 키워드 인수 **instance**로 사용할 수 있다. **instance**가 제공되면, `save()`는 그 인스턴스를 갱신 할 것이다. 제공되지 않으면 `save()`는 지정된 모델의 새 인스턴스를 만든다.

```python
>>> from myapp.models import Article
>>> from myapp.forms import ArticleForm

# Create a form instance from POST data.
>>> f = ArticleForm(request.POST)

# Save a new Article object from the form's data.
>>> new_article = f.save()

# Create a form to edit an existing Article, but use
# POST data to populate the form.
>>> a = Article.objects.get(pk=1)
>>> f = ArticleForm(request.POST, instance=a)
>>> f.save()
```
