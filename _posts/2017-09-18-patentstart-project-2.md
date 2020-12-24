---
layout: post
title: Authentication backends/Facebook Social Login
categories: experience
tags: [python, django, project]
---

# [Customizing authentication in Django](https://docs.djangoproject.com/en/1.10/topics/auth/customizing/)
username/password 방식이 아닌 다른 방식을 사용할 때, 확장하거나 대체할 수 있는 `Authentication backends` 제공한다.

## Other authentication sources
장고 인증 시스템은 다른 인증 소스를 쉽게 사용할 수 있다. 장고의 기본 데이터베이스 스킴을 재정의 하거나, 기본 시스템과 다른 시스템을 앞뒤로 연결하여 사용할 수 있다.

### Specifying authentication backends
장고는 인증을 체크하는 **authentication backends** 리스트를 유지한다. `django.contrib.auth.authenticate()`를 호출하면, 장고는 **authentication backends** 리스트에 있는 모든 인증을 시도한다. 만약 첫번째 인증 방법이 실패하면 장고는 모든 **backends**를 시도할 때 까지 두번째, 그 다음 시도를 한다.

**authentication backends** 리스트는 `AUTHENTICATION_BACKENDS` 설정에 지정한다.

```python
# 기본 인증 백엔드
['django.contrib.auth.backends.ModelBackend']
```

`AUTHENTICATION_BACKENDS` 순서는 중요하다. 만약 같은 username/password가 여러 **backends**에서 유효하다면, 장고는 처음 매치된 시점에서 처리를 멈춘다.


# Facebook Social Login
django의 대표적인 social login 라이브러리로 **django-allauth**와 **social-app-django**가 있다.

## [django-allauth](https://django-allauth.readthedocs.io/en/latest/)
3rd party 계정의 인증/등록/계정 관리를 다루는 장고 통합 세트

- 이메일 검증 지원
- 로컬 인증과 소셜 인증을 모두 허용하는 완전히 통합된 인증 지원
- 거의 대부분의 소셜 로그인 지원 (네이버, 카카오 지원)

## [social-app-django](http://python-social-auth.readthedocs.io/en/latest/configuration/django.html)
- MongoEngine ORM 제공
- 파이프라인을 통한 기능 추가 및 제거


## 결론
여러가지 Provider를 지원하고, 라이브러리 유지가 활발히 되고 있는 django-allauth를 사용하기로 결정
