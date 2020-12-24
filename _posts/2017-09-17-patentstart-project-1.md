---
layout: post
title: User 모델 설정
categories: experience
tags: [python, django, project]
---


> [How to Extend Django User Model]( https://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html) 정리한 것이다.


## 기존 User 모델을 확장하는 방법
일반적으로 기존 User 모델을 확장하는데 4가지 방법이 있다.

### Option 1. Proxy 모델 사용
무엇인가?
- 데이터베이스에 새 테이블을 만들지 않는 **모델 상속**이다.
- 기존 데이터베이스 스키마에 영향을 주지 않는다.
- 기존 모델의 동작을 변경하는데 사용한다. (예: 기본 순서 지정, 새 매소드 추가)

언제 사용하는가?
- 추가 정보를 데이터베이스에 저장할 필요가 없을 때
- 단순히 새 메소드를 추가 하거나 모델 쿼리 매니저를 변경할 때

### Option 2. User 모델과 일대일 링크 사용
무엇인가?
- 자신의 데이터베이스 테이블을 가진 **일반 장고 모델**이다.
- OneToOneField를 통해 기존 User 모델과 일대일 관계를 유지한다.

언제 사용하는가?
- 인증 프로세스와 관련없는 추가 정보를 저장할 때

### Option 3. AbstractBaseUser를 확장한 사용자 정의 User 모델 생성
무엇인가?
- AbstractBaseUser를 상속받은 **완전히 새로운 User 모델**이다.
- 특별한 주의가 필요하며, `settings.py`를 통해 일부 레퍼런스를 업데이트한다.
- 데이터베이스 스키마에 엄청난 영향을 주기 때문에 프로젝트의 시작할 때 완료해야 한다.

언제 사용하는가?
- 인증 프로세스 관련한 특정 요구가 있을 때 (예: username 대신 이메일을 id로 사용할 경우)

### Option 4. AbstractUser를 확장한 사용자 정의 User 모델 생성
무엇인가?
- AbstractUser를 상속받은 **새로운 User 모델**이다.
- 특별한 주의가 필요하며, `settings.py`를 통해 일부 레퍼런스를 업데이트한다.
- 데이터베이스 스키마에 엄청난 영향을 주기 때문에 프로젝트의 시작할 때 완료해야 한다.

언제 사용하는가?
- 장고의 인증 프로세스에 완벽히 만족하고 User 모델에 직접 추가 정보를 저장할 때

### 결론
우리 서비스를 사용하기 위해 가입할 때, 필요한 필수 User 정보는 다음과 같다.
 - `username`
 - `password`

인증 시스템은 그대로 사용할 것이다. 하지만 추가 정보를 저장할 가능성은 열어두고 싶다. 그렇다면 User 모델 확장을 위해 **옵션 2**와 **옵션 4** 중 하나를 선택할 수 있다. **옵션 2**의 방법은 추가 쿼리 혹은 `join`이 발생하기 때문에 **옵션 4**를 선택했다.

```python
# config/settings.py
AUTH_USER_MODEL = 'account.User'
INSTALLED_APPS = [
    # ...
    'account'
]

# account/models.py
from django.db import models
from django.contrib.auth.models import AbstractUser


# 추가 정보를 저장하기 위해 AbstractUser 상속
class User(AbstractUser):
    pass
```


> [사용자 인증 (django.contrib.auth)](https://wayhome25.github.io/django/2017/05/18/django-auth/)
