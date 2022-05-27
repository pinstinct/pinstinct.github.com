---
layout: post
title: Jekyll blog 환경 설정
categories: jekyll
tags: [blog, code]
excerpt: jekyll 환경 설정을 정리합니다.
---

## Jekyll blog를 위한 환경 설정

맥을 새로 사고 블로그 글을 올리려 보니, jekyll 환경 설정이 필요해 다시 하면서 정리합니다.

### rbenv 설치

루비 환경을 관리하기 위해 [rbenv](https://github.com/rbenv/rbenv)를 설치합니다.

```shell
$ brew install rbenv ruby-build
$ rbenv init
# Load rbenv automatically by appending
# the following to ~/.zshrc:

eval "$(rbenv init - zsh)"
```

터미널 창을 껐다 다시 켭니다.

```shell
$ rbenv install -l  # list latest stable versions
$ rbenv install 2.7.6  # jekyll은 3.0.0 버전보다 높으면 별도의 설정이 필요
$ rbenv rehash  # 루비 새 버전을 설치 후 실행
$ cd ~/blog_directory
$ rbenv local 2.7.6
```

### jekyll 설치

```shell
$ gem install jekyll bundler
$ bundle update --bundler
$ bundle exec jekyll serve  # 로컬 서버 실행
```

[http://localhost:4000](http://localhost:4000)에서 블로그를 확인합니다.
