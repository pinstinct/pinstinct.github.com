---
layout: post
title: urls.py에서 템플릿 연결
categories: patentstart
tags: [python, django, project]
---

## 뷰 없이 템플릿 렌더링하는 법
소개 페이지는 템플릿으로 전달할 컨텍스트도 없고, 모델과 뷰도 필요하지 않다.  그래서 검색해보니 다음과 같이 뷰에서 `render_to_response()`를 사용하는 방법이 많았다.

```python
from django.shortcuts import render_to_response

def some_view(request):
   return render_to_response('sometemplate.html')
```

더 찾아보니, 장고 1.8 버전 이상에서는 `urls.py`에서 바로 템플릿으로 연결해주는 방법이 있어 이 방법을 적용했다.

```python
from django.views.generic import TemplateView

urlpatterns = [
    url(r'^$', TemplateView.as_view(template_name="your_static.html"), name='whatever'),
]
```

> [Is there a way to render a html page without view model?](https://stackoverflow.com/questions/2799443/is-there-a-way-to-render-a-html-page-without-view-model)
