---
layout: post
title: Project
categories: About
---


# 1. 회원관리 기능
### 가입 (`/user/signup/`)
MyUser 테이블에 유저생성할 때, `username`, `nickname`, `password를` 필수로 입력받도록 함수를 `create_user` 함수를 재정의했습니다.

Email을 `username`으로 사용하고, 패스워드는 6자리 이상으로 입력해 가입합니다.

### 로그인 (`/user/login/`)
유저 정보의 유효성을 확인해 토큰 키를 생성합니다.
- Framework : rest_framework.authtoken


### 로그아웃 (`/user/logout/`)
유저의 권한을 확인해 토큰 키를 삭제합니다.
- Framework : rest_framework.authtoken

# 2. 책 검색 기능

### 전체 책 검색 (`/book/search/`)
데이터는 'Google Book API'와 '다음카카오 책 검색 API'를 이용합니다.


![](https://pinstinct.gitbooks.io/soobook-api/content/assets/soobook%20db.png)

검색 API를 호출하면
- 먼저 SooBook 데이터베이스에서 검색합니다.
- 데이터가 없을 경우, 외부 API에 요청을 보내 SooBook 데이터베이스에 저장합니다.
  - 최초 요청은 실시간으로 이루어집니다.
  - Framework : requests

속도 개선을 위해 다음과 같이 보완했습니다.
  - DB 쿼리문 개선
    - 여러번의 쿼리로 INSERT하는 `create`를
    - 한번의 쿼리로 INSERT하는 `bulk_create`로 변경
    - UPDATE시, WHERE절 사용
  - 비동기 처리
    - 유저가 검색했던 키워드를 저장해 외부 서버에 요청을 보내 데이터를 수집 (Celery + AWS SQS)
    - Framework : django_celery_results, django_celery_beat


### 내 책 검색 (`book/mybook/search/`)
추가한 책에 대한 검색기능을 구현했습니다. 입력받은 키워드로 `Book` 테이블의 `title`, `author`, `publisher` 컬럼을 검색해 결과를 반환합니다.
- Framework : django_filters

검색결과를 페이지네이션해서 보내줄 때, 이전 페이지와 다음페이지 주소를 `http`가 아닌 `https`로 반환하도록 정규표현식을 사용했습니다.


# 3. 책 평가 기능
## 내 책
- 추가 (`book/mybook/`)
  - 요청한 유저와 책정보의 유효성을 판단
  - `MyBook` 테이블에 INSERT
  - `status code 201` 응답을 반환
  - 특이사항 : `MyBook` 테이블에 추가한 후 해당 정보로 `BookStar` 테이블에 INSERT (기본값 0)
- 삭제 (`book/mybook/`)
  - 요청한 유저와 책정보의 유효성을 판단
  - `MyBook` 테이블에서 삭제
  - `status code 200` 응답을 반환
- 리스트 (`book/mybook/`) : 요청한 유저정보를 이용해 `MyBook` 테이블에서 검색해 최근 추가한 책부터 리스트로 반환
- 디테일 (`book/mybook/detail/`) : 요청한 유저와 책정보를 이용해 `MyBook` 테이블에서 검색해 반환


## 한줄 평가
저장한 책 1권에 대해 1개의 한줄 평가를 등록합니다.
- 추가 및 업데이트 (`book/comment/`)
  - 요청한 유저와 내 책정보의 유효성을 판단
  - `BookComment` 테이블에 INSERT
  - `status code 201` 응답을 반환
  - 특이사항 : 내 책정보가 동일하면 `BookComment` 테이블을 UPDATE한 후 `status code 200` 응답을 반환
- 삭제 (`book/comment/`)
  - 요청한 유저와 한줄 평가 정보의 유효성을 판단
  - `BookComment` 테이블에서 삭제
  - `status code 200` 응답을 반환

## 별점 평가
저장한 책 1권에 대해 1개의 별점 평가를 등록합니다.
- 업데이트 (`/book/star/`)
  - 요청한 유저와 내 책정보의 유효성을 판단
  - `BookStar` 테이블을 UPDATE
  - `status code 200` 응답을 반환
  - 특이사항 : 요청한 별점이 0에서 5사이인 double형인지 유효성체크를 합니다.
- 리스트 (`/book/star/`)
  - 요청한 유저정보의 유효성을 판단
  - `MyBook` 테이블에서 유저가 가진 책 정보를 리스트로 생성
  - `BookStar` 테이블에서 해당 책에 대한 별점 정보를 검색해 리스트로 반환

## 책속 글귀
저장한 책 1권에 대해 여러 개의 책속 글귀를 등록합니다.
- 추가 (`/book/mark/`)
  - 요청한 유저와 내 책정보의 유효성을 판단
  - `BookMark` 테이블을 INSERT
  - `status code 201` 응답을 반환
  - 특이사항 : 내 책정보가 동일하나 컨텐츠만 변경해 요청할 경우, `BookMark` 테이블을 UPDATE한 후 `status code 200` 응답을 반환
- 업데이트 (`/book/mark/`)
  - 요청한 유저와 책속 글귀 정보의 유효성을 판단
  - `BookMark` 테이블을 UPDATE
  - `status code 200` 응답을 반환
- 삭제 (`/book/mark/`)
  - 요청한 유저와 책속 글귀 정보의 유효성을 판단
  - `BookMark` 테이블에서 삭제
  - `status code 200` 응답을 반환
