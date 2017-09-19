---
layout: post
title: django-allauth 설치
categories: patentstart
tags: [python, django, project]
---


## [django-allauth 설치](https://django-allauth.readthedocs.io/en/latest/installation.html)

> [Is using the django.contrib.sites framework necessary?](https://github.com/pennersr/django-allauth/issues/57)

라이브러리를 사용하기 위해 `sites` 프레임워크 사용이 필수이다.
- 소셜 앱 설정을 `SocialApp` 테이블에 저장 (예: Facebook app ID/secret)
    - provider
    - name
    - client_id
    - secret
    - key
    - sites (ManyToMany)
- 소셜 앱 설정은 일반적으로 실행중인 도메인과 연결되어 있으므로 도메인 당 앱이 필요
- 단일 사이트의 경우라도 두 개의 도메인이 필요 (development, production)

```python
INSTALLED_APPS = [
    # The following apps are required:
    'django.contrib.sites',
]

SITE_ID = 1
```

### 에러 해결

#### 문제
`SITE_ID`를 설정하지 않고 `localhost:8000/admin/` 페이지 접속하니, `Site matching query does not exist.` 에러가 발생한다.

#### 왜?
`django.contrib.sites`를 설정 후 DB를 마이그레이션 하면 `django_site`라는 테이블이 생긴다. 이 모델은 웹사이트의 `domain`과 `name` 속성을 저장한다.
- `domain` : 웹사이트와 관련된 정규화 된 도메인 이름 (기본: **www.example.com**)
- `name` : 사림이 읽기 편한 웹사이트 "verbose" 이름

`SITE_ID` 설정은 `Site` 객체의 데이터베이스 ID를 지정한다. 만약 이 설정을 생략하면, `request.get_host()` 메서드가 가져온 호스트 이름의 **domain**과 비교하여 `get_current_site()` 함수가 현재 사이트를 가져오려고 시도한다.

그런데 `Site` 테이블에 `localhost`가 없기 때문에 현재 사이트를 가져올 수 없어 에러가 발생한다.
`SITE_ID=1`로 지정하고 `localhost:8000/admin/` 페이지 접속하면 정상적으로 동작한다. admin 페이지에 로그인 후, sites 메뉴에서 `localhost`를 등록한다. 그 후 `SITE_ID=1`를 제거하면 정상적으로 동작한다.

> https://docs.djangoproject.com/ko/1.11/ref/contrib/sites/
