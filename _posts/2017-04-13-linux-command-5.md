---
layout: post
title: 리다이렉션
categories: linux
tags: [linux, command, shell, cli]
---



# 6. 리다이렉션

프로그램을 실행하면 결과는 항상 **표준출력**으로 간다. 프로그램의 입력은 별도의 지정이 없다면, **표준입력**에서 받는다.

프로그램은 표준출력과 표준입력을 바라보고 있고, 표준출력이나 표준입력은 모니터나 키보드로 연결된다.

부모 프로세스가 자식 프로세스를 만들 때, 자기를 복제(`fork`)하고 복제한 프로세스 공간에 실행하고자 하는 프로그램을 덮어써서 프로세스를 만든다.

그렇기 때문에 bash에서 `sleep`이라 입력하면 bash가 `sleep`이라는 프로세스를 포크한다. 덮어써질 때, 몇가지는 그대로 남게되는데 표준출력과 표준입력을 부모와 동일하게 쓴다. 

리다이렉션을 이용해 파이프라인을 만들기 위해서 필요한 명령어들을 연결할 수 있다.

> 파이프라인은 프로세스와 프로세스를 연결해주는 '빨대'같은 것이다.
>
> 파이프라인과 리다이렉션의 차이는 *프로세스*를 다루냐, *파일*을 다루냐이다.



## 표준 입출력과 표준오류

- 표준 입력 (stdin)
- 표준 출력 (stdout)
- 표준 오류 (stderr)

많은 프로그램들이 표준 입력이라고 부르는 곳에서 입력 내용을 가겨오고 그것은 기본적으로 키보드와 연결되어 있다. 

표준 출력을 파일로 방향을 지정하면 출력결과는 파일로 나온다.

```shell
$ echo abc
abc
$ echo abc > /dev/stdout
abc

# echo는 printf를 이용해 표준 출력(stdout)을 한다.
# 그 표준출력의 방향을 aaa로 변경한다.
$ echo abc > aaa
```



### 표준 출력 재지정

```shell
# > : 표준출력 재지정 (overwrite)
# 표준출력으로 나와는 ls 명령을 텍스트 파일로 출력
$ ls -l /usr/bin/ > ls-output.txt
$ ls -l ls-output.txt
-rw-rw-r-- 1 lim lim 156026 Apr 13 20:55 ls-output.txt

# 표준 출력만 재지정했기 때문에 표준 오류는 화면에 나타난다.
$ ls -l /bin/usr > ls-output.txt
ls: cannot access '/bin/usr': No such file or directory

# 파일은 항상 처음부터 다시 작성되므로, 파일크기가 0이다.
$ ls -l ls-output.txt
-rw-rw-r-- 1 lim lim 0 Apr 14 20:20 ls-output.txt

# >> : append
$ ls -l /usr/bin/ >> ls-output.txt
$ ls -lh ls-output.txt
-rw-rw-r-- 1 lim lim 305K Apr 13 21:06 ls-output.txt
$ ls -l /usr/bin/ >> ls-output.txt
$ ls -lh ls-output.txt
-rw-rw-r-- 1 lim lim 458K Apr 13 21:06 ls-output.txt
```

### 표준 오류 재지정

파일 디스크립터(fd)는 파일을 다루는 핸들 같은 것으로, 인트형 자료형이다. 쉘은 내부적으로 표준 입출력과 표준 오류를 0, 1, 2번 **파일 디스크립터**로 표현한다.

- 0 : 표준 입력
- 1 : 표준 출력
- 2 : 표준 오류

````shell
$ ls -l /bin/usr 2> ls-error.txt
$ cat ls-error.txt
ls: cannot access '/bin/usr': No such file or directory
````



### 표준 출력과 표준 오류를 한 파일로 재정의

```shell
# 두 번의 리다이렉션이 이루어진다.
# 표준 출력이 lim.txt 파일로 재지정되고,
# 파일디스크립터 2(표준오류)가 파일디스크립터 1(표준출력)로 재지정된다.

# 2> : 표준오류를 재지정한다.
# & : 파일 디스크립터
# 1 : 표준출력
$ ls -l abdczdfadf > lim.txt 2>&1

# 표준 오류는 표준출력으로 재지정되고,
# (에러는 화면에 출력)
# 표준 출력은 텍스트 파일로 재지정된다.
$ ls -l abdczdfadf  2>&1 >> lim.txt

# 위의 방법은 헷갈리니, 다음과 같은 기호가 나왔다.
lim@class:~$ ls -l dfdfdf  &> lim.txt
lim@class:~$ ls -l dfdfdf  &>> lim.txt
```



### 원치 않는 출력 제거

시스템은 `dev/null`이라는 특수한 파일이 있다. 이 파일은 **비트 버킷(bit bucket)**이라고 불리는 시스템 장치로 입력을 받고 아무것도 수행하지 않는다.

```shell
$ ls -l /bin/usr 2> /dev/null

$ man -k null
BIO_f_null (3ssl)    - null filter
BIO_s_null (3ssl)    - null data sink
DBM_Filter::null (3perl) - filter for DBM_Filter
EVP_enc_null (3ssl)  - EVP cipher routines
EVP_md_null (3ssl)   - EVP digest routines
Net::DNS::RR::NULL (3pm) - DNS NULL resource record
null (4)             - data sink
RSA_null_method (3ssl) - select RSA method

$ man 4 null
NULL(4)                
NAME
       null, zero - data sink

DESCRIPTION
       Data  written  to  the /dev/null and /dev/zero special files is discarded.
```





### 프로세스

 : 프로그램이 실행되면 커널이 자원을 할당해 메모리에 올리면, 프로세스가 된다.

> 프로세스 주인 / 프로세스 아이디(PID)
>
> 프로그램이 인스턴스화 된 것

```shell
$ cd /dev/
$ ls std*
stderr  stdin  stdout

# proc : 현재 프로세스를 파일로 노출한 공간 (관리하기 쉬우라고)
# self : pid, 자기자신의 프로세스 공간
# fd : 파일디스크립터
$ ls -ahl std*
lrwxrwxrwx 1 root root 15 Apr  9 05:17 stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Apr  9 05:17 stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Apr  9 05:17 stdout -> /proc/self/fd/1

$ cd /proc/self/fd
$ ls -ahl
total 0
dr-x------ 2 lim lim  0 Apr 14 15:07 .
dr-xr-xr-x 9 lim lim  0 Apr 14 15:07 ..
lrwx------ 1 lim lim 64 Apr 14 15:07 0 -> /dev/pts/4
lrwx------ 1 lim lim 64 Apr 14 15:07 1 -> /dev/pts/4
lrwx------ 1 lim lim 64 Apr 14 15:07 2 -> /dev/pts/4
lrwx------ 1 lim lim 64 Apr 14 16:32 255 -> /dev/pts/4
```

