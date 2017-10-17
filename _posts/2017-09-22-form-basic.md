---
layout: post
title: 장고 폼 기본
categories: patentstart
tags: [python, django, project]
---

# [Working with forms](https://docs.djangoproject.com/en/1.10/topics/forms/)

## HTML forms
HTML에서 폼은 `<form>...</form>`안에 있는 요소들의 모음이다. 폼은 두 가지를 지정해줘야 한다.

- where : 사용자 입력이 반환되어야 하는  **URL**
- how : **HTTP method**


## Django’s role in forms
장고의 폼 기능은 작업의 상당 부분을 단순화하고 자동화 할 수 있으며, 대부분의 프로그래머가 작성한 코드보다 더 안전하게 실행한다. 장고는 폼과 관련된 세 가지 부분을 처리한다.
- 데이터를 준비 및 재구성하여 렌더링
- 데이터로 HTML 폼 생성
- 클라이언트에서 제출한 폼 데이터 수신 및 처리

## Forms in Django
### The Django `Form` class
폼 클래스의 필드는 HTML 폼 `<input>` 요소에 매핑한다. (**ModelForm**은 모델 클래스의 필드를 `Form`을 통해 HTML 폼 `<input>` 요소에 매핑한다.)

폼 필드 자체가 클래스이다. 폼이 제출되면 폼 데이터를 관리하고 유효성 검사를 수행한다. `DateField`와 `FileField`는 매우 다른 종류의 데이터를 처리하고 그 데이터와 다른 작업을 수행해야 한다.

폼 필드는 브라우저에 HTML "widget"으로 표현한다. 각 필드 타입은 적절한 기본 `Widget class`를 가지고 있고, 필요하면 재정의 할 수 있다.

## Building a form
### Building a form in Django
#### `Form` 클래스

```python
# form.py
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label='Your name', max_length=100)
```
단일 필드(your_name)가 있는 `Form` 클래스를 정의한다. 인간에게 익숙한 레이블을 필드에 적용했다. 이 레이블은 `<label>`이 렌더링될 때 나타난다. (이 경우, 레이블을 생략 한 경우 자동으로 생성한 레이블과 지정한 레이블은 동일하다.)

필드의 최대 허용 길이는 `max_length`에 의해 정의된다. 이것은 두 가지 일을 한다. HTML `<input maxlength="100">`로 설정(브라우저가 사용자에게 설정한 수보다 많은 문자를 입력하지 못하도록 막는다)한다. 또한, 장고가 브라우저에서 폼을 받으면 데이터 길이를 확인한다.


`Form` 인스턴스는 모든 필드에 대한 유효성 검사 루틴을 실행하는 `is_valid()` 메서드가 있다. 이 메소드가 호출될 때, 모든 필드에 유효한 데이터가 들어 있으면
- `True`를 반환한다.
- 폼의 데이터를 `cleaned_data` 속성에 배치한다.

위의 폼 양식을 렌더링하면 다음과 같이 표시된다.
```html
<label for="your_name">Your name: </label>
<input id="your_name" type="text" name="your_name" maxlength="100" required />
```

주의 : 여기에는 `<form>` 태그나 **submit button**이 포함되지 않는다.

#### 뷰
장고 웹 사이트로 보내진 폼 데이터는 **뷰**에 의해 처리된다.

```python
# views.py
from django.shortcuts import render
from django.http import HttpResponseRedirect

from .forms import NameForm

def get_name(request):
    # if this is a POST request we need to process the form data
    if request.method == 'POST':
        # create a form instance and populate it with data from the request:
        form = NameForm(request.POST)
        # check whether it's valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required
            # ...
            # redirect to a new URL:
            return HttpResponseRedirect('/thanks/')

    # if a GET (or any other method) we'll create a blank form
    else:
        form = NameForm()

    return render(request, 'name.html', {'form': form})
```
이 뷰에 **GET** 요청으로 도착하면, 빈 폼 인스턴스를 생성하고 템플릿 컨텍스트에 배치한다. URL을 처음 방문 할 때 발생할 수 있는 일이다.

**POST** 요청을 사용하여 폼을 제출하면, 뷰에서 폼 인스턴스를 다시 생성하고 요청(request)의 데이터로 채운다.
```python
form = NameForm (request.POST)
```
"폼 데이터 바인딩"이라고 한다. (지금부터 *bound* 폼이라 한다.)

폼의 `is_valid()` 메소드를 호출한다. `True`가 아니라면 폼이 있는 템플릿으로 돌아간다. 이번에는 폼이 더 이상 비어 있지 않으므로(*unbound*) HTML 폼은 이전에 제출 된 데이터로 채워지며, 필요에 따라 편집 및 수정할 수 있다.

`is_valid()`가 `True`이면, 유효성이 검사 된 폼 데이터를 `cleaned_data` 속성에서 찾을 수 있다. 이 데이터를 사용하여 데이터베이스를 업데이트하거나 브라우저로 HTTP 리디렉션을 보내기 전에 다른 처리를 수행한다.

#### 템플릿
```html
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit" />
</form>
```
모든 폼의 필드와 속성은 장고의 템플릿 언어인 `{{ form }}`에 의해 HTML 마크업으로 풀린다.

## More about Django Form classes
모든 폼 클래스는 `ModelForm`을 포함하여 `django.forms.Form`의 서브 클래스로 생성된다.

> 모델과 폼 : 실제로 폼을 장고 모델을 직접 추가하거나 편집하는데 사용한다면 ModelForm을 사용하면 많은 시간, 노력 및 코드를 절약 할 수 있다. 왜냐하면 Model 클래스에서 적절한 필드와 속성과 함께 폼을 작성하기 때문이다.

### Bound and unbound form instances
바운드/언바운드 폼을 구분하는 것은 중요하다.
- 언바운드 폼은 연결된 데이터가 없다. 사용자에게 렌더링될 때, 비어 있거나 기본값을 포함한다.
- 바운드 폼은 제출된 데이터를 가지고, 그 데이터가 유효한지 판별하는데 사용할 수 있다. 유효하지 않은 바운드 폼이 렌더링되면, 사용자에게 수정해야 할 데이터를 알려주는 인라인 에러 메시지를 포함할 수 있다.

폼의 `is_bound` 속성은 폼 데이터가 바인드되어 있는지 여부를 알려준다.

### More on fields

```python
# forms.py
from django import forms

class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField(widget=forms.Textarea)
    sender = forms.EmailField()
    cc_myself = forms.BooleanField(required=False)
```
이 경우 폼에는 `subject`, `message`, `sender`, `cc_myself`라는 네 개의 필드가 있다. `CharField`, `EmailField`, `BooleanField`는 사용 가능한 필드 유형 중 세 가지이다.

#### Widgets
각 폼 필드에 해당하는 `Widget Class`가 있고, `<input type="text">`와 같은 HTML 폼 위젯에 해당한다.

#### Field data
폼과 함께 제출된 데이터가 무엇이든간에 `is_valid()`를 호출하여 유효성을 검사를 하고 (`is_valid()`가 `True`를 반환하면) 유효성 검사가 된 폼 데이터는 `form.cleaned_data` 딕셔너리에 저장된다. 이 데이터는 파이썬 유형으로 잘 변환 될 것이다.

> 주의 : `request.POST`에서 유효성을 검사하지 않은 데이터에 직접 액세스 할 수는 있지만, 검증 된 데이터가 더 좋다.

이 폼 데이터를 뷰에서 처리하는 방법은 다음과 같다.
```python
# views.py
from django.core.mail import send_mail

if form.is_valid():
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['message']
    sender = form.cleaned_data['sender']
    cc_myself = form.cleaned_data['cc_myself']

    recipients = ['info@example.com']
    if cc_myself:
        recipients.append(sender)

    send_mail(subject, message, sender, recipients)
    return HttpResponseRedirect('/thanks/')

```
일부 필드 유형은 추가 처리가 필요하다. 예를 들어, 폼을 통해 업로드 된 파일은 다르게 처리해야 한다. (`request.POST`보다 `request.FILES`에서 찾을 수 있다.) 파일 업로드를 처리하는 방법에 대한 자세한 내용은 [Binding uploaded files to a form](https://docs.djangoproject.com/en/1.11/ref/forms/api/#binding-uploaded-files)을 참조한다.
