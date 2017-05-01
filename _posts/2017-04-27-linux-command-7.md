---
layout: post
title: 확장과 인용
categories: linux
tags: [linux, command, shell, cli]
---

# 확장과 인용
## 확장
무엇이든 입력하면 쉘이 그것을 처리하기 전에 다른 무언가로 확장한다. (like 치환)

```shell
# echo : 표준출력으로 프린트
$ echo this is a test
this is a test
$ echo *
aaa bin lim lim.txt ls-error.txt ls-output-err.txt ls-output.txt ls-test.txt playground public_html result.txt testdir test.py
```

### 경로명 확장
**와일드 카드**로 동작하는 방식을 경로명 확장이라고 한다.

```shell
$ echo /usr/*/share
/usr/local/share
```

### 틸트(~) 확장
물결표(`~`) 기호는 현재 사용자의 홈 디렉토리명을 나타낸다.

```shell
$ echo ~
/home/linux-study/lim

# 사용자 이름을 지정할 경우
$ echo ~jehos
/home/jehos
```

### 산술 확장
`$((expression))`

산술 확장은 정수만을 허용한다.
```shell
$ echo $((2+2))
4

# 중첩해서 사용 가능
$ echo $(( $((5**2)) * 3 ))
75

# 괄호 사용 가능
$ echo $(( (5**2) * 3 ))
75
```


### 중괄호 확장
```shell
$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back

$ echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5

$ echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A

$ echo {2009..2011}-0{1..9} {2009..2011}-{10..12}
2009-01 2009-02 2009-03 2009-04 2009-05 2009-06 2009-07 2009-08 2009-09 2010-01 2010-02 2010-03 2010-04 2010-05 2010-06 2010-07 2010-08 2010-09 2011-01 2011-02 2011-03 2011-04 2011-05 2011-06 2011-07 2011-08 2011-09 2009-10 2009-11 2009-12 2010-10 2010-11 2010-12 2011-10 2011-11 2011-12
```

### 매개변수 확장
```shell
$ echo $USER
lim

$ echo USER
USER

# 잘못된 변수명을 입력하면, 빈 문자열을 반환
$ echo $ABC

# 사용가능한 변수 목록
$ printenv | less
```

### 명령어 치환
```shell
$ ls -l $(which bash)
-rwxr-xr-x 1 root root 1037528 Jun 25  2016 /bin/bash
```
