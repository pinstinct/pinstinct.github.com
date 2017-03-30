---
layout: post
title: 쉘이란 무엇인가, 파일시스템 탐색
categories: linux
tags: [linux, command, shell, cli]
---

# 1. 쉘이란 무엇인가?

- shell : 문자열 해석기
- bash : 프로그램
- 터미널 에뮬레이터 : shell에 접근할 수 있게 해준다.

## 첫 번째 키 입력

### 명령어 히스토리
입력했던 명령어를 기억하고 있다. 메모리에 있던 명령어를 보관하기 위해 **설정파일**로 보조기억장치에 저장한다. 확인하려면 아래와 같이 명령어를 입력한다.

```shell
$ ls -alh
$ cat .bash_history
```

### 커서 이동
리눅스는 이전부터 `ctrl + c`를 다른 의미로 사용하고 있었다. 때문에 리눅스에서 복사 붙여넣기를 하기 위해서는 `ctrl + shift + c/v`를 사용한다.

- ctrl + c : 작업취소 (취소하는 방법은 프로그램마다 다름)
- 터미널 세션 종료법
     - exit
     - ctrl + d : EOF (End Of File)


### 간단한 명령어 실행하기

```shell
$ locale
LANG=en_US.UTF-8
LANGUAGE=en_US:en
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"

# 환경 변수 참조
$ date
Thu Mar 30 20:49:17 KST 2017

$ cal
     March 2017
Su Mo Tu We Th Fr Sa
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30 31

$ LC_TIME=ko_KR.UTF-8 cal
      3월 2017
일 월 화 수 목 금 토
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30 31
```

# 2. 파일시스템 탐색
메모리 용량을 줄이기 위해 명령어를 짧게 사용하게 됐다.

- pwd : print working directory
- cd : change directory
- ls : list directory contents

```shell
$ man ls
```

## 파일시스템 트리 구조의 이해
- 트리가 재귀의 가장 좋은 예이다.
- 파일시스템은 트리 구조이다.
- 파일시스템의 최상위 디렉토리를 **루트(root) 디렉토리**라고 한다.
     - 윈도우는 디스크마다 루트를 가진다. (c드라이브, d드라이브, ...)
     - 유닉스/리눅스 파일시스템은 루트를 하나만 가진다.
          - 새로운 디스크를 연결하려면, 파일시스템으로 포맷하고
          - 특정 디렉토리로 **마운트**를 해야 한다.

![](https://pinstinct.github.io/image//images/linux-shell.jpg)

## 디렉토리
사용자 계정마다 고유의 *홈 디렉토리*를 갖는데, 일반 사용자로 시스템을 사용할 때 **파일 쓰기 권한이 부여된 유일한 공간**이다.

```shell
# 현재 작업 디렉토리 표시
$ pwd

# 현재 작업 디렉토리에 있는 파일과 하위 디렉토리 표시
$ ls
```

## 현재 작업 디렉토리 변경
`cd` 다음에 변경하고자 하는 디렉토리 **경로명**을 입력한다.

### 절대 경로명
- 루트(/) 디렉토리에서 시작하는 경로

### 상대 경로명
- 루트(/)로 시작하지 않는 경로
- 현재 작업디렉토리가 시작점
- `.` : 현재 작업 디렉토리
- `..` : 현재 작업 디렉토리의 상위 디렉토리


### 유용한 단축 표현들
- `cd -` : 작업 디렉토리를 이전 작업 디렉토리로 변경
- `cd ~` : 홈 디렉토리로 작업 디렉토리를 변경

확장 개념
```shell
$ echo ~lim
/home/linux-study/lim
$ cd ~lim
$ pwd
/home/linux-study/lim
```

### 파일명에 관한 중요한 사실
- 마침표(.)로 시작하는 파일명을 가진 파일들은 보이지 않는다.
- 파일명과 명령어는 대소문자를 구별한다.
- 파일 확장자 개념이 없다. (헤더에 **매직코드**라고 불리는 비트코드를 통해 확장자를 식별)
- 파일명에 공백을 포함하지 않는다.
