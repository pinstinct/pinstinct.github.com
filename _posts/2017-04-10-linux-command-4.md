---
layout: post
title: 명령어와 친해지기
categories: linux
tags: [linux, command, shell, cli]
---


# 5. 명령어와 친해지기

명령어 옵션, 메뉴얼 살펴보는 법을 확인한다.



## 명령어란 구체적으로 무엇인가?

명령어는 네 가지 중 하나이다.

- 명령어란 **실행 프로그램**을 말한다. 
  - 실행 프로그램 : **바이너리 형식**의 파일 혹은 쉘/펄/파이썬/루비와 같은 **스크립트 언어**로 만든 프로그램
- 명령어란 **쉘에 내장되어 있는 명령**어이다.
  - 쉘-빌트인 (shell builtins) : `cd`, ..
- 명령어란 **쉘 함수**이다.
- 명령어란 **별칭**이다.



## 명령어 확인

명령어의 네 가지 정의 중 어떠한 종류의 명령어인지 알아두는 것이 좋다.

### type - 명령어 타입 표시

```shell
# 경로가 나오면, 
# 실행 프로그램
$ type zsh
zsh is /usr/bin/zsh

# 쉘 내장 명령어
$ type cd
cd is a shell builtin

# 별칭
$ type ls
ls is aliased to `ls --color=auto'

# 쉘 함수
$ type workon
workon is a function
workon ()
{
    virtualenvwrapper_load;
    workon "$@"
}
```



### which - 실행 파일의 위치 표시

**현재 환경**에서 내가 실행하는 명령어가 **"어느 것"**인지 알려주는 명령어이다. `which` 명령어는 오직 **실행 프로그램만**을 대상으로 한다.

환경이라는 말이 중요하다. 시스템에 하나의 실행 프로그램이 여러 버전으로 설치되곤 한다. 어떤 유저는 기본 버전의 `ls` 명령어를 사용할 수 있고, 어떤 유저는 최신 `ls` 명령어를 설치해 사용할 수 있다. 

환경이 중요하다. 유저마다 명령어를 ls



## 명령어 도움말 보기

### help - 쉘 빌트인 도움말 보기

```shell
# 쉘에 포함된 명령
$ type help
help is a shell builtin

# 패턴 (like 검색어)
$ help help
help: help [-dms] [pattern ...]
    Display information about builtin commands.

    Displays brief summaries of builtin commands.  If PATTERN is
    specified, gives detailed help on all commands matching PATTERN,
    otherwise the list of help topics is printed.


$ help cd | head -n 20

# | 둘 중 하나의 항목만 사용한다는 것을 뜻한다.
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
    # 명령어가 다 옵션이면 default가 있을 확률이 높다.

	# cd만 입력할경우 default는 홈디렉토리다.
    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.

    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.

    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.
```



### `-—help`  - 사용법 표시

 실행프로그램 도움말

```shell
$ type mkdir
mkdir is /bin/mkdir  # 바이너리에 있으면 실행프로그램

$ mkdir --help
Usage: mkdir [OPTION]... DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.
```



### `man`- 프로그램 매뉴얼 페이지 표시

프로그램 설명서

````shell
$ type man
man is hashed (/usr/bin/man)
$ man man
1번섹션 프로그램에 대한 내용
````

man 페이지는 less, more와 같은 pager이다.

#### man 페이지 구조

| 섹션   | 내용                     |
| ---- | ---------------------- |
| 1    | 사용자 명령어                |
| 2    | 커널 시스템 콜 API           |
| 3    | C 라이브러리 API            |
| 4    | 장치 노드 및 드라이버와 같은 특수 파일 |
| 5    | 파일 포맷 및 설정파일           |
| 6    | 스크린세이버와 같은 게임이나 미디어 파일 |
| 7    | 그 외 여러 종류              |
| 8    | 시스템 관리용 명령어            |

만약 특정 섹션 번호를 지정하지 않으면 항상 일치하는 첫 번째 섹션 아마도 섹션 1번의 매뉴얼을 얻게 될 것이다. 섹션 번호를 지정하는 방법은 다음과 같다.

```shell
$ man 5 passwd
```

그런데 섹션 번호를 어떻게 알 수 있을까?

```shell
$ man man
# 확인해서 -k 옵션을 살펴보자.

$ man -k ls
# 너무 많이 나온다.

$ man -k ^ls
ls (1)               - list directory contents
lsattr (1)           - list file attributes on a Linux second extended file s...
lsb_release (1)      - print distribution-specific information
lsblk (8)            - list block devices
lscgroup (1)         - list all cgroups
lscpu (1)            - display information about the CPU architecture
lsearch (3)          - linear search of an array
lseek (2)            - reposition read/write file offset
lseek64 (3)          - reposition 64-bit read/write file offset
lshw (1)             - list hardware
lsinitramfs (8)      - list content of an initramfs image
lsipc (1)            - show information on IPC facilities currently employed ...
lslocks (8)          - list local system locks
lslogins (1)         - display information about known users in the system
lsmod (8)            - Show the status of modules in the Linux Kernel
lsof (8)             - list open files
lspci (8)            - list all PCI devices
lspgpot (1)          - extracts the ownertrust values from PGP keyrings and l...
lssubsys (1)         - list hierarchies containing given subsystem
lstat (2)            - get file status
lstat64 (2)          - get file status
lsusb (8)            - list USB devices
```

- `-k` 옵션 : 매뉴얼 페이지의 요약본을 검색해준다. apropos(1)

#### apropos - 적합한 명령어 찾기

```shell
$ apropos ls
```

man 명령어를 -k 옵션과 함께 사용하면 apropos 명령과 동일한 기능을 한다.



#### whites - 간략한 명령어 정보 표시

man 페이지에 대하여 그 이름과 한 줄의 간략한 정보를 보여준다.



## 별칭으로 나만의 명령어 만들기

세미콘론(`;`)으로 명령어를 구분할 수 있다.

```shell
$ type test
test is a shell builtin

lim@class:~$ help alias
alias: alias [-p] [name[=value] ... ]
    Define or display aliases.

    Without arguments, `alias' prints the list of aliases in the reusable
    form `alias NAME=VALUE' on standard output.

    Otherwise, an alias is defined for each NAME whose VALUE is given.
    A trailing space in VALUE causes the next word to be checked for
    alias substitution when the alias is expanded.

    Options:
      -p	Print all defined aliases in a reusable format

    Exit Status:
    alias returns true unless a NAME is supplied for which no alias has been
    defined.
    
# 사용자 환경에 정의된 모든 별칭 확인
$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'


$ alias foo='cd /usr;ls;cd -'
$ alias foo
alias foo='cd /usr;ls;cd -'

$ type foo
foo is aliased to `cd /usr;ls;cd -'

$ foo
bin  games  include  lib  local  sbin  share  src
/home/linux-study/lim

# 별칭 삭제
$ unalias foo
$ type foo
```

쉘 세션이 종료될 때 별칭도 사라진다. (`~/.bashrc`에 설정해두면 쉘이 실행 될 때마다 환경을 설정한다.)

