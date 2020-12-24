---
layout: post
title: Pagination, File Storage System
categories: experience
tags: [python, django, project]
---


# 페이지네이션 적용

> [장고 Pagination](https://docs.djangoproject.com/en/1.11/topics/pagination/)

> [django paginator - how to show all page numbers available](https://stackoverflow.com/questions/19751806/django-paginator-how-to-show-all-page-numbers-available)

<script src="https://gist.github.com/hyunminlim/d4bbd6010b0fbe5881220d92f6eb5a29.js"></script>


# 파일 저장

## [File storage](https://docs.djangoproject.com/en/1.11/topics/files/#file-storage)
장고는 파일을 어디에 어떻게 저장하는지에 대한 결정을 **파일 저장 시스템**에 위임한다.

장고의 기본 파일 저장소는 `DEFAULT_FILE_STORAGE` 설정한다. 명시적으로 스토리지 시스템을 제공하지 않으면, 이 스토리지 시스템을 사용한다.

내장된 기본 파일 저장 시스템에 대한 자세한 내용은 아래를 참조하고, 자신의 파일 저장 시스템을 작성하고 싶다면 [Writing a custom storage system](https://docs.djangoproject.com/en/1.11/howto/custom-file-storage/) 참조한다.

### Storage objects
대부분의 경우 `File` 객체를 사용하려고 하지만, 파일 저장 시스템을 직접 사용할 수 있다. *custom storage system* 클래스 혹은 기본 저장소 시스템을 사용해 인스턴스를 만들 수 있다.

```python
>>> from django.core.files.storage import default_storage
>>> from django.core.files.base import ContentFile

>>> path = default_storage.save('/path/to/file', ContentFile('new content'))
>>> path
'/path/to/file'

>>> default_storage.size(path)
11
>>> default_storage.open(path).read()
'new content'

>>> default_storage.delete(path)
>>> default_storage.exists(path)
False
```
더 자세한 [File storage API - save(), url()](https://docs.djangoproject.com/en/1.11/ref/files/storage/#django.core.files.storage.Storage.save)는 문서를 참조한다.

## 상황
프로젝트는 디버그 설정에 따라 **기본 저장 시스템**과 **커스텀 저장 시스템**을 사용한다. 뷰에서 모델 인스턴스의 `save()` 메소드를 이용해 파일을 저장하고 있다. 이러다 보니 원하는 경로에 저장되지 않고, `url`도 작동하지 않는다.

## 해결
뷰에서 **저장 시스템의 인스턴스**를 생성해 저장하도록 변경했다.

```python
def file_upload(request):
    if 'file' in request.FILES:
        the_file = request.FILES['file']

        upload_to = 'your/file/path/'
        path = default_storage.save(os.path.join(upload_to, the_file.name), the_file)
        link = default_storage.url(path)
```
