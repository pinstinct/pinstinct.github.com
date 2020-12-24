---
layout: post
title: Froala 에디터 설치
categories: experience
tags: [python, django, project, jquery]
---

## Froala 에디터 설치

[Django Froala WYSIWYG Editor](https://www.froala.com/wysiwyg-editor/docs/framework-plugins/django) 가이드를 따라 프로젝트에 적용했다. 적용하는데 두 가지 에러가 발생했다.

1. jQuery 충돌
2. 일부 css, js 파일 로드 불가

### 1. jQuery 충돌
프로젝트에 이미 jQuery 3.2.1 버전을 적용하여 사용중이다. Froala 에디터는 jQuery 1.11.0 버전을 사용한다. 두 개의 jQuery 라이브러리가 충돌해 정상적으로 동작하지 않는다.

가이드에서 제시하는 해결책은 다음과 같다.
- 필수 플러그인을 직접 명시하는 것
- `settings.py` 파일에 `FROALA_INCLUDE_JQUERY = False` 설정하는 것

모두 해봤지만, 에러를 수정할 수 없었다.

jQuery가 충돌하지만 두 가지 버전 모두가 필요했다. 그래서 여러 버전의 jQuery를 사용하는 법을 검색했다.

> [Can I use multiple versions of jQuery on the same page?](https://stackoverflow.com/questions/1566595/can-i-use-multiple-versions-of-jquery-on-the-same-page)

[jQuery.noConflict()](https://api.jquery.com/jquery.noconflict/) API를 사용하면 된다는 것이다.

그래서 Froala 에디터를 사용하는 페이지에서는 아래의 코드를 추가해 에러를 수정했다.
```js
<script>
    jquery_3_2_1 = $.noConflict(true);
</script>
```

### 2. 일부 css, js 파일 로드 불가
위의 에러를 수정하고 `{{ form.media }}`을 통해 플러그인을 로드하고 있다. 그런데 일부 파일들을 로드하지 못하는 것이다. 확인해보니, 경로와 실제 플러그인 파일 위치가 달랐다. 그런 파일들을 경로에 맞춰 옮겼다.
