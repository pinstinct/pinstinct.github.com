---
layout: post
title: 확장과 인용
categories: linux
tags: [linux, command, shell, cli]
---

# 확장과 인용

## 확장 복습

```shell
$ date
Tue May  2 20:31:59 KST 2017
$ man date
$ date +%Y%m%d
20170502
$ mkdir $(date +%Y%m%d)_$USER
$ ls
20170502_jehos  a10  a4  a7  passenger.gkkRTwf                  tmux-2597
20170502_lim    a2   a5  a8  snap.0_canonical-livepatch_mwcGC1
a1              a3   a6  a9  tmux-1001

$ alias mktoday='mkdir $(date +%Y%m%d_%H%M)_$USER'
$ alias
# ...
alias mktoday='mkdir $(date +%Y%m%d_%H%M)_$USER'
$ mktoday
$ ls
20170502_2041_jehos  20170502_jehos  a2  a6  passenger.gkkRTwf
20170502_2041_lim    20170502_lim    a3  a7  snap.0_canonical-livepatch_mwcGC1
20170502_2042_jehos  a1              a4  a8  tmux-1001
20170502_2042_lim    a10             a5  a9  tmux-2597
```



## 따옴표 활용(Quoting, 인용)

쉘에는 다양한 확장 방법이 많다. 그것을 제어하는 방법이 인용이다.

```shell
# echo 명령어의 인자에서 불펼요한 공백을 삭제하여 단어분할해 출력
$ echo this is a     test
this is a test

# 매개변수 확장으로 정의되지 않은 $1이 빈 문자열로 치환
# 그래서 뒤의 00.00만 출력
$ echo The total is $100.00
The total is 00.00
```

### 쌍 따옴표 기호

**쌍 따옴표**로 텍스트를 묶으면 **쉘에서 사용하는 모든 특수한 기호**들이 가진 의미가 없어지고 대신 **일반적인 문자들로 인식**된다. 

```
$, \, ` 기호는 예외다.
```

즉 단어 분할, 경로명 확장, 틸드 확장, 괄호 확장을 숨길 수 있지만 매개변수 확장, 산술확장, 명령어 치환은 그대로 실행된다.

- 공백문자는 단어분할을 해준다.
- 공백문자는 스페이스 뿐만 아니라, 엔터키, 탭 등 많다.

```shell
$ ls -l two words.txt
ls: cannot access 'two': No such file or directory
ls: cannot access 'words.txt': No such file or directory
$ ls -l "two words.txt"
-rw-rw-r-- 1 lim lim 0 May  2 22:29 two words.txt
$ mv "two words.txt" two_words.txt
```



```shell
$ echo \*
*
$ echo "******"
******

$ echo this is a      test
this is a test
$ echo this\ is \a \ \ \ \ \test
this is a     test

# 특수한 기호들이 가진 의미가 없어지고 일반적인 문자들로 인식
# 그래서 공백이 사라져 단어분할이 안되고 하나의 명령인자로 인식
$ echo "this is a test"
this is a test

$ echo "$USER $((2+2)) $(cal)"
lim 4       May 2017
Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6
 7  8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30 31

# 명령어 치환의 결과로, 
# 38개의 명령 인자를 가진 명령어로 인식
$ echo $(cal)
May 2017 Su Mo Tu We Th Fr Sa 1  2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31

$ echo "$(cal)"
      May 2017
Su Mo Tu We Th Fr Sa
    1  2  3  4  5  6
 7  8  9 10 11 12 13
14 15 16 17 18 19 20
21 22 23 24 25 26 27
28 29 30 31
```



### 홑 따옴표 기호

쌍따옴표는 세개의 예외가 있는데 홑따옴표는 모두 다 문자로 인식한다.

```shell
lim@class:/tmp$ echo '$USER $((2+2))'
$USER $((2+2))
```



### 이스케이프 문자

하나의 문자를 인용할 때, 해당 문자 앞에 백슬래시(`\`)를 추가한다. 이것을 이스케이프 문자(escape character)라고 부른다.

```shell
lim@class:/tmp$ echo "The balance for $100"
The balance for 00
lim@class:/tmp$ echo "The balance for \$100"
The balance for $100
lim@class:/tmp$ echo 'The balance for \$100'
The balance for \$100
```

