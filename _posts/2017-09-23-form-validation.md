---
layout: post
title: 장고 폼 유효성
categories: experience
tags: [python, django, project]
---

# [Form and field validation](https://docs.djangoproject.com/en/1.10/ref/forms/validation/)

폼 유효성 검사는 데이터 정리시 발생한다. 이 프로세스를 사용자 정의하려면, 변경할 수 있는 다양한 장소가 있으며 각각 다른 용도로 사용한다. 세 가지 유형의 **cleaning 메소드**가 폼 처리 중 실행된다. 일반적으로 폼에서 `is_valid()` 메소드가 호출될 때 실행한다. cleaning과 validation을 시작할 수 있는 다른 방법(`errors` 속성에 접근 or `full_clean()`을 직접 호출)도 있으나, 일반적으로 필요하지 않는다.

보통 **cleaning 메소드**는 처리하는 동안 문제가 있으면 `ValidationError`를 발생시킨다. 만약 `ValidationError`가 발생하지 않는다면, 메소드는 python 객체로 된 **cleaned data**를 리턴한다.

대부분의 유효성 검사는 `validators`를 사용하여 수행할 수 있다. `validators`는 단일 인수를 사용하여 잘못된 입력에 대해 `ValidationError`를 발생시키는 단순한 함수이다. `validators`는 필드의 `to_python`과 `validate` 메소드가 호출된 후 실행된다.

- 필드의 `to_python()` 메소드는 모든 유효성 검사의 첫 번째 단계이다. 값을 올바른 데이터타입으로 강제 변환하고 이것이 불가능하면, `ValidationError`를 발생시킨다. 이 메소드는 위젯에서 raw 값을 허용하고 변환된 값을 리턴한다. 예를 들어 `FloatField`는 데이터를 Python `float`로 변환하거나 `ValidationError`를 발생시킨다.
- 필드의 `validate()` 메소드는 validator에 적합하지 않은지 필드별 유효성 검사를 처리합니다. 올바른 데이터타입으로 변환된 값을 취하고, 에러가 있으면 `ValidationError`를 발생시킨다. 이 메소드는 아무것도 리턴하지 않으므로 값을 변경하면 안된다. 유효성 검사 로직을 다루려면 이 메소드를 재정의해야 한다.
- 필드의 `run_validators()` 메소드는 모든 필드의 유효성 validators를 실행하고 모든 오류를 종합해 단일 `ValidationError`를 발생시킨다. 이 메소드를 재정의 할 필요는 없다.
- 필드 하위 클래스의 `clean()` 메소드는 `to_python()`, `validate()`, `run_validators()`를 올바른 순서로 실행하고, 에러를 전파할 책임이 있다. (언제든지 어떤 메소드에서 `ValidationError`가 발생한다면, 유효성 검사가 중지되고 해당 오류가 발생한다.) 이 메소드는 **clean data**를 반환하고 폼의 `cleaned_data` 사전에 삽입한다.
- `clean_<fieldname>()` 메소드는 폼의 하위 클래스에서 호출된다. `<fieldname>`은 폼 필드 속성의 이름으로 대체된다. 이 메소드는 모든 정리를 수행한다. 이 메소드는 매개 변수를 전달하지 않는다. 필드의 `self.cleaned_data`에 있는 값을 찾아보고, 폼에 제출된 원래 문자열이 아닌 python 객체라는 것을 기억해야 한다. (위의 일반 필드 `clean()` 메서드가 이미 데이터를 한 번 정리 했으므로 `cleaned_data`에 있음)
  - 예를 들어, **serialnumber**라는 `CharField`의 내용이 고유한지 확인하려는 경우, `clean_serialnumber()`가 이 작업을 수행 할 올바른 장소이다. 특정 필드(`CharField`)는 필요하지 않지만, 폼 필드에 특정한 '유효성 검사/cleaning/normalizing'을 원할 경우이다.
  - 이 메소드의 반환 값은 `cleaned_data`의 기존 값을 대체하므로, 필드의 `cleaned_data` 값이거나 새로 정리 된 값이어야 한다.
- 폼의 하위 클래스 `clean()` 메소드는 여러 폼 필드에 접근이 필요한 유효성 검사를 수행할 수 있다. 여기에 "필드 A가 제공되면, 필드 B는 반드시 유효한 이메일 주소를 포함해야 한다"와 같은 확인을 넣을 수 있다. 이 메소드는 원한다면 완전히 다른 딕셔너리를 리턴할 수 있으며, 이는 `cleaned_data`로 사용된다.
  - 필드 유효성 검사 메소드는 `clean()`이 호출 될 때 실행되기 때문에, 각 필드의 정리로 발생하는 모든 에러가 포함된 폼 `errors` 속성에도 접근할 수 있다.
  - `Form.clean()` 재정의로 인해 발생하는 오류는 모든 필드와 관련되지 않는다. 필요한 경우 `non_field_errors()` 메소드를 통해 액세스 할 수 있는 특수한 "필드"(`__all__`)로 이동한다. 폼의 특정 필드에 오류를 첨부하려면 `add_error()`를 호출해야합니다.

이러한 메소드들은 위에서 설명한 순서대로 한 번에 한 필드 씩 실행된다. 즉, 폼의 각 필드에 대해 `Field.clean()` 메소드(or its override)가 실행 된 다음 `clean_<fieldname>()`이 실행된다. 각 필드에 대해 두 메서드를 실행하고 나면, 이전 메서드에서 오류가 발생했는지 여부와 관계없이 `Form.clean()` 메서드(or its override)가 실행된다.

이 메소드들 중 하나를 사용하면 `ValidationError`를 발생시킬 수 있다. 어떤 필드에서 `Field.clean()` 메서드가 `ValidationError`를 발생시키면, 해당 필드 관련 cleaning 메소드가 호출되지 않는다. 그러나 나머지 필드의 cleaning 메소드는 계속 실행된다.

## Using validation in practice
### Using validators
장고의 폼(및 모델) 필드는 `validators(유효성 검사기)`라고하는 간단한 유틸리티 함수 및 클래스의 사용을 지원한다. 유효성 검사기는 값을 취해 값이 유효한 경우 아무 것도 반환하지 않거나, 유효하지 않은 경우 `ValidationError`를 발생시키는 **호출 가능한 객체 또는 함수** 일뿐입니다. 필드의 validator 인수를 통해 필드의 생성자(constructor)에 전달하거나, `default_validators` 특성을 사용하여 **Field** 클래스 자체에 정의 할 수 있습니다.

간단한 유효성 검사기는 필드 내에서 값의 유효성 검사를 할 수 있다. 다음 장고의 `SlugField`를 살표보자.

```python
slug = forms.CharField(validators=[validators.validate_slug])
```

이메일이나 정규표현식에 대한 일반적인 유효성 확인과 같은 경우 장고에서 사용할 수있는 기존의 validator 클래스를 사용하여 처리 할 수 ​​있다. 예를 들어, `validators.validate_slug`는 첫 번째 인수가 `^[-a-zA-Z0-9_]+$` 패턴인 `RegexValidator` 인스턴스이다. 사용 가능한 유효성 검사기의 리스트를 보려면 [validators](https://docs.djangoproject.com/en/1.10/ref/validators/)를 참조하라.

### Form field default cleaning
콤마로 구분된 이메일 주소가 포함된 문자열을 입력으로 받는 유효성 검사를 하는 커스텀 폼 필드를 만들어보자.

```python
from django import forms
from django.core.validators import validate_email

class MultiEmailField(forms.Field):
    def to_python(self, value):
        """Normalize data to a list of strings."""
        # Return an empty list if no input was given.
        if not value:
            return []
        return value.split(',')

    def validate(self, value):
        """Check if value consists only of valid emails."""
        # Use the parent's handling of required fields, etc.
        super(MultiEmailField, self).validate(value)
        for email in value:
            validate_email(email)
```
이 필드를 사용하는 모든 폼은 필드의 데이터로 다른 작업을 수행하기 전에 이러한 메소드를 실행한다.

이 필드를 사용하는 방법을 보여주기 위해 간단한 `ContactForm`을 작성해 보자.
```python
class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField()
    sender = forms.EmailField()
    recipients = MultiEmailField()
    cc_myself = forms.BooleanField(required=False)
```
다른 폼 필드와 마찬가지로 `MultiEmailField`를 사용하기만 하면 된다. 폼의 `is_valid()` 메서드가 호출되면, `MultiEmailField.clean()` 메서드가 cleaning 프로세스의 일부로 실행되고, 차례로 사용자 지정 `to_python()` 및 `validate()` 메서드가 호출된다.
