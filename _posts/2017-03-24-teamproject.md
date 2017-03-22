---
layout: post
title: TeamProject
categories: wps
tags: [fastcampus, code]
---


# 수북(soobook)

## 1. 목표

1. 회원 관리 : 가입, 로그인, 로그아웃
2. 책 검색 및 저장 기능 : 검색, 저장, 삭제, 리스트, 디테일
3. 평가 작성 기능 : 코멘트 작성 및 삭제, 평점 작성 및 삭제



## 2. 서버 구성

![](https://pinstinct.github.io/image/wps-team-server.png)

### 기술 스펙

- python - django
- nginx
- uwsgi



## 3. 데이터베이스 구축

![](https://pinstinct.github.io/image/wps-team-flow.png)



### 데이터 베이스 구성

![](https://pinstinct.github.io/image/wps-team-db.jpg)



## 4. API

### member

- signup(id, password)
- login(id, password)
- logout(id, password)

### my_page

- list_from_my_book(user_id)
- detail_from_my_book(user_id, book_id)
- search_from_my_book_list(keyword)
- add_comment(*args)
- remove_comment(comment_id)
- add_star(*args)
- remove_star(star_id)

### search

- search_from_google_book(q)
- save_book(book_id)
- remove_book(book_id)
