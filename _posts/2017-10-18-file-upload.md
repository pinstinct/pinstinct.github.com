---
layout: post
title: 장고 파일 업로드
categories: patentstart
tags: [python, django, project]
---

## FileField

> [Model field reference - FileField](https://docs.djangoproject.com/ko/1.11/ref/models/fields/#filefield)

파일 업로드 필드이다. 두 개의 옵션 인수가 있다.

### `FileField.upload_to`
이 속성은 업로드 디렉토리와 파일 이름을 설정하는 방법을 제공하며 두 가지 방법으로 설정할 수 있다. 두 경우 모두 값은 `Storage.save()` 메서드에 전달된다.

문자열 값을 지정한다면, `strftime()` 형식을 사용할 수 있다. 이 형식은 (업로드 된 파일이 주어진 디렉토리를 채우지 않도록) 파일을 업로드한 날짜/시간으로 대체한다.

```python
class MyModel(models.Model):
    # file will be uploaded to MEDIA_ROOT/uploads
    upload = models.FileField(upload_to='uploads/')
    # or...
    # file will be saved to MEDIA_ROOT/uploads/2015/01/30
    upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
```

### `FileField.storage`
파일을 저장 및 검색하는 저장 개체입니다. 이 객체를 제공하는 방법에 대한 자세한 내용은 파일 관리를 참조하십시오.

이 필드의 기본 폼 위젯은 **ClearableFileInput**이다.

모델에서 `FileField` 또는 `ImageField`를 사용하려면 몇가지 스텝을 따라야 한다.

1. 설정 파일에서 `MEDIA_ROOT`을 정의한다. (성능을 위해 이러한 파일은 데이터베이스에 저장되지 않는다.)
2. 모델에 `FileField` 또는 `ImageField`를 추가하고 `upload_to` 옵션을 정의한다. 업로드 된 파일에 사용할 `MEDIA_ROOT`의 **하위 디렉토리**를 지정합니다.
3. 데이터베이스에 저장되는 것은 모두 파일에 대한 경로이다. `url` 속성을 사용해 간편하게 가져올 수 있다. 예를 들어 `ImageField`의 이름이 `mug_shot`인 경우 {% raw %}{{object.mug_shot.url}}{% endraw %} 템플릿을 사용하여 이미지의 절대 경로를 가져올 수 있다.

업로드 된 파일의 파일 이름 또는 크기를 검색하려면, `name` 및 `size` 속성을 각각 사용할 수 있다. (파일의 상대 경로 `path`, 파일의 절대 경로 `url`)

## [File Uploads](https://docs.djangoproject.com/ko/1.11/topics/http/file-uploads/)

장고가 파일 업로드를 처리 할 때, 파일 데이터는 `request.FILES`에 배치된다. 이 문서에서는 파일이 디스크 및 메모리에 저장되는 방법과 기본 동작을 사용자 지정하는 방법에 대해 설명한다.

### Basic file uploads
```python
# forms.py
from django import forms

class UploadFileForm(forms.Form):
    title = forms.CharField(max_length=50)
    file = forms.FileField()
```
이 폼을 처리하는 뷰는 `request.FILES`에 파일 데이터를 받는다. `request.FILES`는 폼의 각 `FileField`에 대한 키를 포함하는 딕셔너리다. 따라서 위 폼의 데이터는 `request.FILES[ 'file']`로 액세스 할 수 있다.

**주의** : 요청 메소드가 `POST`이고 `<form>`의 속성이 `enctype = "multipart/form-data"`인 경우에만 `request.FILES`에 데이터가 포함된다. 그렇지 않으면 `request.FILES`은 비어 있다.

```python
# views.py
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm

# Imaginary function to handle an uploaded file.
from somewhere import handle_uploaded_file

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILES)
        if form.is_valid():
            handle_uploaded_file(request.FILES['file'])
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})

```
폼 생성자에 `request.FILES`을 전달해야 한다. 이것은 파일 데이터가 폼에 바인딩되는 방법이다.

업로드 된 파일을 처리하는 일반적인 방법은 다음과 같습니다.
```python
def handle_uploaded_file(f):
    with open('some/file/name.txt', 'wb+') as destination:
        for chunk in f.chunks():
            destination.write(chunk)
```
`read()`를 사용하는 대신 `UploadedFile.chunks()`를 루핑하는 것이 대용량 파일이 시스템의 메모리를 소비하지 않게된다.

`UploadedFile` 객체에는 몇 가지 다른 메서드와 속성이 있다. 자세한 내용은 [UploadedFile](https://docs.djangoproject.com/ko/1.11/ref/files/uploads/#django.core.files.uploadedfile.UploadedFile)을 참조하자.

#### Handling uploaded files with a model
`FileField`가 있는 **모델**에 파일을 저장하는 경우, `ModelForm`을 사용하면이 훨씬 쉽게 처리할 수 있다. 파일 객체는 `form.save()`를 호출 할 때, 해당 `FileField`의 `upload_to` 인수로 지정된 위치에 저장된다.

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import ModelFormWithFileField

def upload_file(request):
    if request.method == 'POST':
        form = ModelFormWithFileField(request.POST, request.FILES)
        if form.is_valid():
            # file is saved
            form.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = ModelFormWithFileField()
    return render(request, 'upload.html', {'form': form})
```
객체를 수동으로 생성하는 경우, `request.FILES`의 파일 객체를 모델의 파일 ​​필드에 간단히 할당 할 수 있다.

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import UploadFileForm
from .models import ModelWithFileField

def upload_file(request):
    if request.method == 'POST':
        form = UploadFileForm(request.POST, request.FILES)
        if form.is_valid():
            instance = ModelWithFileField(file_field=request.FILES['file'])
            instance.save()
            return HttpResponseRedirect('/success/url/')
    else:
        form = UploadFileForm()
    return render(request, 'upload.html', {'form': form})
```

#### Uploading multiple files
하나의 폼 필드에 여러 파일을 업로드하려면 필드 위젯의 `multiple` 속성을 설정한다.

```python
# forms.py
from django import forms

class FileFieldForm(forms.Form):
    file_field = forms.FileField(widget=forms.ClearableFileInput(attrs={'multiple': True}))
```

그런 다음 `FormView` 하위 클래스 `post` 메서드를 재정의해 여러 파일 업로드를 처리한다.

```python
# views.py
from django.views.generic.edit import FormView
from .forms import FileFieldForm

class FileFieldView(FormView):
    form_class = FileFieldForm
    template_name = 'upload.html'  # Replace with your template.
    success_url = '...'  # Replace with your URL or reverse().

    def post(self, request, *args, **kwargs):
        form_class = self.get_form_class()
        form = self.get_form(form_class)
        files = request.FILES.getlist('file_field')
        if form.is_valid():
            for f in files:
                ...  # Do something with each file.
            return self.form_valid(form)
        else:
            return self.form_invalid(form)
```
