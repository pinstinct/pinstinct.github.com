---
layout: post
title: 리다이렉션
categories: linux
tags: [linux, command, shell, cli]
---

# 6. 리다이렉션

### 표준 입력 재지정

#### cat - 파일 붙이기


```shell
$ type cat
cat is /bin/cat
$ man cat
NAME
       cat - concatenate files and print on the standard output

SYNOPSIS
       cat [OPTION]... [FILE]...

DESCRIPTION
       Concatenate FILE(s) to standard output.
       # 파일이 없거나 - 이면, 표준 입력을 읽는다.
       With no FILE, or when FILE is -, read standard input.
       
$ cat
hello
hello

$ cat > lim.txt
hello
# ctrl + D = EOF
$ cat lim.txt
hello

# < : 표준 입력 재지정
$ cat < lim.txt


# 파일이 없거나 - 일 때, 표준 입력을 받기 때문에
# jehos.txt 파일에서 내용을 가지고 오지 않는다.
$ cat lim.txt < jehos.txt
hello

# 위의 jehos.txt 파일을 가져오기 위해,
# - 을 추가하면 표준 입력을 받게 된다. 
$ cat lim.txt - < jehos.txt
hello world





hellooooooo


# 표준입력을 lim.txt 파일로부터 받고, 표준출력을 result.txt로 한다.
$ cat < lim.txt > result.txt
$ cat result.txt
hello
```



## 파이프라인

- `>` : 대상이 파일
- `|` : 대상이 프로세스 (단반향)

파이프라인에서 프로세스는 동시에 뜬다. 파이프는 버퍼를 가지고 있다. 앞의 명령이 출력한 내용을 파이프에 쓰고, 뒤의 명령이 입력으로 파이프의 내용을 가져온다.

```shell
# tab1
$ cat | grep hello
hello world
hello world

# tab2
$ ps | grep cat
$ ps -ef | grep cat
lim       29155  28340  0 22:53 pts/1    00:00:00 cat
lim       31554  28578  0 22:56 pts/5    00:00:00 grep --color=auto cat
$ cd /proc/29155/fd
$ ls -ahl
total 0
dr-x------ 2 lim lim  0 Apr 17 22:56 .
dr-xr-xr-x 9 lim lim  0 Apr 17 22:53 ..
lrwx------ 1 lim lim 64 Apr 17 22:56 0 -> /dev/pts/1
l-wx------ 1 lim lim 64 Apr 17 22:56 1 -> pipe:[15408341]
lrwx------ 1 lim lim 64 Apr 17 22:56 2 -> /dev/pts/1

# tab3
$ ps -ef | grep grep
lim       29156  28340  0 22:53 pts/1    00:00:00 grep --color=auto hello
lim       32229  28709  0 22:56 pts/6    00:00:00 grep --color=auto grep
$ cd /proc/29156/fd
$ ls -ahl
total 0
dr-x------ 2 lim lim  0 Apr 17 22:56 .
dr-xr-xr-x 9 lim lim  0 Apr 17 22:53 ..
lr-x------ 1 lim lim 64 Apr 17 22:57 0 -> pipe:[15408341]
lrwx------ 1 lim lim 64 Apr 17 22:57 1 -> /dev/pts/1
lrwx------ 1 lim lim 64 Apr 17 22:56 2 -> /dev/pts/1
```



### 필터

#### sort

```shell
$ man sort
NAME
       sort - sort lines of text files

SYNOPSIS
       sort [OPTION]... [FILE]...
       sort [OPTION]... --files0-from=F

DESCRIPTION
       Write sorted concatenation of all FILE(s) to standard output.
       With no FILE, or when FILE is -, read standard input.
       Mandatory arguments to long options are mandatory for short options too.  Ordering options:

$ ls /bin/ /usr/bin/ | sort | less
```



#### uniq - 중복줄 제거 및 표시

```shell
$ man uniq
NAME
       uniq - report or omit repeated lines

SYNOPSIS
       uniq [OPTION]... [INPUT [OUTPUT]]

DESCRIPTION
       Filter  adjacent matching lines from INPUT (or standard input), writing
       to OUTPUT (or standard output).

       With no options, matching lines are merged to the first occurrence.

       Mandatory arguments to long options are  mandatory  for  short  options
       too.
       
       
$ cat sample.txt | sort | uniq
```



#### wc - 라인, 단어 개수 및 파일 크기 출력

```shell
$ man wc
NAME
       wc - print newline, word, and byte counts for each file

SYNOPSIS
       wc [OPTION]... [FILE]...
       wc [OPTION]... --files0-from=F

DESCRIPTION
       Print newline, word, and byte counts for each FILE, and a total line if
       more than one FILE is specified.  A word is a non-zero-length  sequence
       of characters delimited by white space.
       
# 라인의 개수 / 단어의 개순 / 파일 크기
$ cat sample.txt | sort | uniq | wc
      8       8      91
      
# -l : 라인의 수만 출력
$ cat sample.txt | sort | uniq | wc -l
8
```



#### **grep** - 패턴과 일치하는 라인 출력

```shell
$ man grep
NAME
       # 패턴에 맞는 줄을 출력
       grep, egrep, fgrep, rgrep - print lines matching a pattern
       
SYNOPSIS
       grep [OPTIONS] PATTERN [FILE...]
       grep [OPTIONS] [-e PATTERN]...  [-f FILE]...  [FILE...]
       
DESCRIPTION
       grep searches the named input FILEs for lines containing a match to the
       given PATTERN.  If no files are specified, or if the file “-” is given,
       grep  searches  standard  input.   By default, grep prints the matching
       lines.
```



```shell
# 파일이 없거나 - 일 경우, 표준 입력을 받는다.
$ grep hello
adfadfal

adfjlafdj

adfjl


hello
hello


adjldafsdf
asdfjladsf

hellowordl
hellowordl

wowow

$ echo "hello world" | grep hello
hello world

# -i : 대소문자 구분하지 않고 검색
$ cat | grep -i hello
hello
hello

Hello
Hello
HELLO
HELLO
helLo
helLo

# -v : 패턴과 일치하지 않는 라인만 출력
$ cat | grep -v hello
hello
HEllo
HEllo
hi
hi
```





#### head / tail - 파일의 처음/끝 부분 출력

```shell
$ man head
NAME
       head - output the first part of files

SYNOPSIS
       head [OPTION]... [FILE]...

DESCRIPTION
       # 각 FILE의 처음 10줄을 표준출력으로 출력
       Print  the  first  10  lines of each FILE to standard output.  With more than one FILE, precede each
       with a header giving the file name.
       With no FILE, or when FILE is -, read standard input.
       Mandatory arguments to long options are mandatory for short options too.
       
$ cat sample.txt | head
myfile
myfile
kdlfjaskldjf
kdlfjaskldjf
myfile
myfile
asldjflkaj
askldjfakslkdjs
asldjflkaj
asldjflkaj

# -n : 길이를 조정
$ cat sample.txt | head -n 5
myfile
myfile
kdlfjaskldjf
kdlfjaskldjf
myfile
```





```shell
$ man tail
NAME
       tail - output the last part of files

SYNOPSIS
       tail [OPTION]... [FILE]...

DESCRIPTION
       Print  the  last  10  lines of each FILE to standard output.  With more
       than one FILE, precede each with a header giving the file name.

       With no FILE, or when FILE is -, read standard input.

       Mandatory arguments to long options are  mandatory  for  short  options
       too.

       -c, --bytes=[+]NUM
              output  the  last  NUM  bytes; or use -c +NUM to output starting
              with byte NUM of each file
              
       # 증가되는 데이터를 더해 출력
       # 로그볼때 많이 사용
       -f, --follow[={name|descriptor}]
              output appended data as the file grows;
              
              
$ cat auth.log* | grep "lim" | wc -l
162

$ grep "Accepted password for lim" auth.log* | wc -l
25

# -f : 지속적으로 로그 파일을 감시하고 새 내용이 추가될 때 곧바로 그 내용을 표시
# ctrl + c를 입력하면 종료
$ tail -f /api/ access.log
tail: cannot open '/api/' for reading: No such file or directory
==> access.log <==
172.17.0.1 - - [17/Apr/2017:14:21:26 +0000] "GET / HTTP/1.1" 200 553 "-" "ELB-HealthChecker/2.0"
172.17.0.1 - - [17/Apr/2017:14:21:26 +0000] "GET / HTTP/1.1" 200 553 "-" "ELB-HealthChecker/2.0"
```



#### tee - 표준 입력에서 데이터를 읽고, 표준 출력과 파일에 출력

작업이 진행되고 있을 때, 중간 지점의 파이프라인에 있는 내용을 알고 싶을때 유용한다.

```shell
$ ls /usr/bin/ | tee ls.txt | grep zip
```



