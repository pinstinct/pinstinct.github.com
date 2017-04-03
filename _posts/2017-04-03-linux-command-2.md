---
layout: post
title: 시스템 살펴보기, 파일과 디렉토리 조작
categories: linux
tags: [linux, command, shell, cli]
---

# 3. 시스템 살펴보기

`coreutils`라고 불리는 명령어들이다.

- `ls` : 디렉토리 내용 나열
- `file` : 파일 타입 확인
- `less` : 파일 내용 표시

## ls 명령어

```shell
$ ls
bin  public_html  testdir

$ ls public_html/
index.html

$ ls ~ public_html/
/home/linux-study/lim:
bin  public_html  testdir

public_html/:
index.html

# -l : 자세한 속성확인
$ ls -l
total 2
drwxrwxr-x 2 lim lim 2 Apr  3 21:10 bin
drwxrwxr-x 2 lim lim 3 Mar 26 00:14 public_html
drwxrwxr-x 2 lim lim 2 Mar 30 21:43 testdir
```

### 명령어 옵션과 명령 인자

보통 명령어를 다음과 같이 입력하게 된다.

`명령어 -옵션 명령인자`

옵션에는 "short 옵션"과 "long 옵션"이 있다.

- short 옵션 : 대시(`-`)와 함께 사용, 여러옵션을 한 명령어에 연이어 사용가능
- long 옵션 : 대시 대시(`--`)와 함께 사용, 명시적으로 사람이 읽을 수 있게 할 때 주로 사용

```shell
# ls 주요 옵션

# 숨김 파일까지 표시
$ ls -a
.   .bash_history  .bashrc  .byobu  .kshrc    public_html  .viminfo
..  .bash_logout   bin      .cache  .profile  testdir

# 해당 디렉토리가 아닌 디렉토리 내용을 확인
$ ls public_html/
index.html
# 해당 디렉토리 정보 확인
$ ls -d public_html/
public_html/
# 해당 디렉토리 정보를 자세히 확인
$ ls -ld public_html/
drwxrwxr-x 2 lim lim 3 Mar 26 00:14 public_html/
```

### long 포맷으로 출력 결과 보기

```shell
$ ls -l
total 2
drwxrwxr-x 2 lim lim 2 Apr  3 21:10 bin
drwxrwxr-x 2 lim lim 3 Mar 26 00:14 public_html
drwxrwxr-x 2 lim lim 2 Mar 30 21:43 testdir
```

- drwxrwxr-x : 파일 접근 권한 정보
  - 첫 번째 문자 : 파일 형식 (일반적 파일 -, 디렉토리 d, ...)
  - 다음 세 문자 : 파일 **소유자**의 접근 권한
  - 다음 세 문자 : 파일 **그룹**에 대한 권한
  - 다음 세 문자 : 그 외 사용자(**others**)에 대한 권한
    - r : read
    - w : write
    - x : excute
- 2 : 하드 링크 수
- lim : 파일 소유자 이름
- lim : 파일 소유 그룹 이름
- 파일크기 (바이트)
- 마지막 **수정** 날짜
- 파일명

## file 명령어

어떤 파일인지 확인하는 명령어이다. 헤더에 있는 매직코드로 확장자를 결정한다.

```shell
$ file index.html
index.html: HTML document, ASCII text
```

*nix 운영체제에서는 "모두 다 파일이다".

## less 명령어

텍스트 파일을 **읽을 때** 사용하는 프로그램이다. (**pager**라는 프로그램의 일종) 텍스트 파일을 확인해야 하는 이유는 대부분이 텍스트 형식으로 저장되어있기 때문이다. 이를 통해 시스템 동작 방식을 이해할 수 있기 때문이다.

```shell
$ less index.html
```

(vim에서 사용하는 명령어를 거의 그대로 사용할 수 있는데, 이런 것을 vim 바인딩이라고 한다. vim 명령어를 익혀두는 것이 좋다.)

## 리눅스 파일시스템 계층 표준(FHS)

Linux Filesystem Hierarchy Standard에 따라 대부분의 리눅스 배포판이 설계되어있다. FHS를 알면 대략적으로 어떤 역할을 하는 것들인지 알 수 있다.

[Filesystem Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)

## 심볼링 링크 (like 통로, 추상화)

하드 링크는 두 가지 치명적 단점이 있다. 

- 디렉토리는 링크로 가질 수 없다.
- 링크를 지우면 원본도 지워진다. (그러므로 unlink를 해줘야 한다.)

그래서 심볼링 링크가 생겨났다.

```shell
lrwxrwxrwx 1 jehos       jehos            5 Apr  3 21:39 hello_sym -> hello
```

- 첫 번째 문자가 `l` : 심볼릭 링크를 나타 냄
- 모든 권한이 허용 : 타고 넘어갈 수 있느냐에 대한 권한이므로 모든 권한이 주어진다. 수정도 불가능하다.

# 4. 파일과 디렉토리 조작

## 와일드 카드

명령어에 강력함을 더해주는 **쉘의 특성**이 있다. **쉘**은 특수한 문자들을 지원한다. 이러한 특수문자를 **와일드카드** 혹은 **글로빙**이라고 한다.

- `*` : 모든 파일(문자)

쉘은 `*`을 현재 작업위치의 모든 파일들로 치환(*매크로*)하여 인자로 전달한다. (특정문자열을 다른문자열로 치환하는 작업을 *매크로*라고 한다.)


