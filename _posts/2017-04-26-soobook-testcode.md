---
layout: post
title: Team Project - Soobook Test Code
categories: wps
tags: [fastcampus, project]
---

# 테스트 코드
Unit 테스트를 주로 성공케이스에 대해 작성했습니다.


## Mixin
자주 사용하는 기능은 Mixin(상속관계 없이 다른 클래스에서 사용되기 위해 메소드를 포함한 클래스)으로 구현해 코드를 재사용할 수 있도록 했습니다.
- `APIAuthMixin` : 유저생성 후 로그인하여 토큰 키를 반환
- `APIBookMixin` : 입력받은 수만큼 책을  생성하여 책 리스트를 반환


## 회원관리
- `MemberModelTestCase` : MyUser 테이블에 유저생성 결과를 row 개수와 평가
- `TestUser` : 필수 파라미터를 포함해 API를 요청한 결과를 status code와 평가
  - `test_create_user` : 회원가입 API 테스트
  - `test_login` : 로그인 API 테스트
  - `test_logout` : 로그아웃 API 테스트



## 책
필수 파라미터를 포함해 API를 요청한 결과를 status code와 평가

- `MyBookSearchAPITestCase`
  - `test_apis_url_exists` : API 주소가 존재하는지 확인
  - `test_book_search` : 전체 책 검색 API 테스트


## 내 책장
필수 파라미터를 포함해 API를 요청한 결과를 status code와 평가

### 내 책
- `MyBookAPITestCase`
  - `test_mybook_list_one_data` : 책 한권에 대해
    - 내 책 추가 API 테스트
    - 내 책 삭제 API 테스트
    - 내 책 리스트 API 테스트
  - `test_mybook_list_many_data` : 여러권의 책에 대해
    - 내 책 추가 API 테스트
    - 내 책 삭제 API 테스트
    - 내 책 리스트 API 테스트
  - `test_mybook_detail` : 책 한권에 대해
    - 내 책 디테일 API 테스트

### 한줄 평가
- `MyBookCommentTestCase`
  - `create_dummy_mybook` : 유저와 책 데이터를 받아 MyBook 테이블에 row를 추가
  - `test_comment_add` : 한줄 평가 추가 API 테스트
  - `test_comment_detete` : 한줄 평가 삭제 API 테스트

### 별점 평가
실패 케이스에 대해서도 작성했습니다.
- `MyBookStarAPITestCase`
  - `create_dummy_mybook` : 유저와 책 데이터를 받아 MyBook 테이블에 row를 추가
  - `test_star_add` : 별점 평가 추가 API 테스트
  - `test_star_add_validate_fail` : 별점 유효성 테스트
  - `test_star_add_field_requeired_fail` : 필수 파라미터 테스트
  - `test_star_add_field_invalid_mybook_fail` : 값에 대한 유효성 테스트


### 책속 글귀
- `MyBookMarkTestCase`
  - `create_dummy_mark` : 유저와 책 데이터를 받아 MyBook 테이블에 row를 추가
  - `test_mark_add` : 책속 글귀 추가 API 테스트
  - `test_mark_update` : 미완성
  - `test_comment_detete` : 책속 글귀 삭제 API 테스트
