---
layout: post
title: 파일과 디렉토리 조작
categories: linux
tags: [linux, command, shell, cli]
---

# 4. 파일과 디릭토리 조작

## 와일드 카드 (or 글로빙)

와일드 카드는 파일명을 명령 인자로 허용하는 명령어와 함께 사용될 수 있다.

(와일드카드가 정규표현식과 다른점은 파일명을 선택할 때 사용하는 도구라는 것이다.)

| 와일드카드         | 매칭 문자                      |
| ------------- | -------------------------- |
| `*`           | 모든 파일                      |
| ?             | 모든 하나의 문자                  |
| [characters]  | characters 문자셋에 포함된 문자     |
| [!characters] | characters 문자셋에 포함되지 않은 문자 |
| [[:class:]]   | 지정된 **문자 클래스**에 포함된 문자     |

가장 많이 사용되는 **문자 클래스**

| 문자 클래스      | 매칭 문자         |
| ----------- | ------------- |
| `[:alnum:]` | 모든 알파벳과 숫자 문자 |
| `[:alpha:]` | 모든 알파벳 문자     |
| `[:digit:]` | 모든 숫자         |
| `[:lower:]` | 모든 소문자        |
| `[:upper:]` | 모든 대문자        |


```shell
$ ls a?
aa  ac  ae  ag  ai  ak  am

$ ls a??
aa1  ac5  ae9  ah4  aj8  am3  ao7

$ ls [a]*
aa    ac9   af8   ai7   al6   ao5

$ ls [!a]*
1a  9w     Ir123    Rl123	1b  9x

$ ls [!a]*
1a  9w     Ir123    Rl123	1b  9x

$ ls [[:upper:]]*
Aa123  Dh123  Go123  Jv123  Nc123  Qj123
```



## mkdir - 디렉토리 생성

읽는 방법

- `[ ]` : 대괄호 안에 있는 건 옵션인자이다.
- `…` : 해당 인자가 반복될 수 있다.

```shell
$ mkdir --help
Usage: mkdir [OPTION]... DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.
```

> DIRECTORY 인자가 반드시 하나와야 하며, 반복해서 올 수 있다.

```shell
$ touch myfile
$ mkdir myfile
mkdir: cannot create directory ‘myfile’: File exists
# 리눅스는 모든게 파일이이다.
```



## cp - 파일 및 디렉토리 복사

```shell
# head : 앞에 10줄만 출력
$ cp --help | head
Usage: cp [OPTION]... [-T] SOURCE DEST
  or:  cp [OPTION]... SOURCE... DIRECTORY
  or:  cp [OPTION]... -t DIRECTORY SOURCE...
Copy SOURCE to DEST, or multiple SOURCE(s) to DIRECTORY.

# t 옵션 확인
$ cp --help | grep "directory"
  -t, --target-directory=DIRECTORY  copy all SOURCE arguments into DIRECTORY
  -T, --no-target-directory    treat DEST as a normal file
```

> 필수 인자를 두개 받는 명령어

### 자주 사용하는 옵션

```shell
# -a, --archive : 파일 및 디렉토리뿐만 아니라 속성까지 모두 복사
$ cp file1 normal
$ cp -a file1 a_option
$ l -ahl
total 0
drwxrwxr-x 1 lim lim  48 Apr  8 19:57 ./
drwxrwxr-x 1 lim lim 998 Apr  6 21:44 ../
-rw-rw-r-- 1 lim lim   0 Apr  6 20:55 a_option
-rw-rw-r-- 1 lim lim   0 Apr  6 20:55 file1
-rw-rw-r-- 1 lim lim   0 Apr  8 19:57 normal

# -i, --interactive : 기존 파일을 덮어쓰기 전에 확인 메시지를 보여준다.
$ cp -i file1 file2
cp: overwrite 'file2'?

# -r, --recursive : 디렉토리 안의 내용까지 복사할 때 쓰는 옵션
$ cp -r mydir/ dir5

# -u, --update : 디렉토리에서 디렉토리로 복사할 때,
# 디렉토리 없거나 최신 버전인 파일만 복사하기 위해 사용

# -v, --verbose : 메시지를 보여주는 옵션
$ cp -v file1 file2
'file1' -> 'file2'
```

### cp 사용 예제

```shell
$ touch file1
$ cp file1 file2
$ l
file1  file2

$ cp -i file1 file2
cp: overwrite 'file2'?

# 디렉토리가 미리 존재해야 한다.
$ cp file1 file2 dir1
cp: target 'dir1' is not a directory
$ mkdir dir1
$ cp file1 file2 dir1

# 디렉토리가 미리 존재해야 한다.
$ cp dir1/* dir2
cp: target 'dir2' is not a directory
$ mkdir dir2
$ cp dir1/* dir2

$ cp -r dir1 dir3
$ l
dir1/  dir2/  dir3/  file1  file2
```



## mv - 파일 이동과 이름 변경

`mv` 명령어는 파일을 이동할 수도 있고 **파일명을 수정**할 수도 있다.

```shell
$ mv --help | head
Usage: mv [OPTION]... [-T] SOURCE DEST
  or:  mv [OPTION]... SOURCE... DIRECTORY
  or:  mv [OPTION]... -t DIRECTORY SOURCE...
Rename SOURCE to DEST, or move SOURCE(s) to DIRECTORY.
```

### mv 사용 예제

```shell
$ l
dir1/  dir2/  dir3/  file1  file2
$ mv file1 file2
$ l
dir1/  dir2/  dir3/  file2

$ cp file2 file1
$ l
dir1/  dir2/  dir3/  file1  file2
$ mv -i file1 file2
mv: overwrite 'file2'? no
$ l
dir1/  dir2/  dir3/  file1  file2

$ mv file1 file2 dir1
$ l
dir1/  dir2/  dir3/

$ mv dir1 dir2
$ l
dir2/  dir3/
$ cd dir2
/dir2$ l
dir1/  file1  file2
```

## rm

```shell
$ rm --help | head
Usage: rm [OPTION]... [FILE]...
Remove (unlink) the FILE(s).
```

룰렛 게임
```shell
$ touch {a..z}{1..9}
$ alias roulette='[ $[ $RANDOM % 6 ] == 0 ] && echo "rm -rf" $(shuf -n1 -e *) && echo "BOOM" || echo *Click*'
```

### rm 사용 예제

```shell
$ l
dir1/  file1  file2
$ rm file1
$ l
dir1/  file2

$ rm -i file2
rm: remove regular empty file 'file2'? yes
$ l
dir1/

$ touch file1
$ l
dir1/  file1
$ rm -r file1 dir1
$ l

# -r과 -rf의 차이
# 파일이나 디렉토리가 존재하지 않아도 실행
$ l
dir2/  dir3/
$ rm -r file1 dir2
rm: cannot remove 'file1': No such file or directory
$ rm -rf file1 dir2
$ l
dir3/
```

## ln - 링크 생성
```shell
$ ln --help | head -n20
Usage: ln [OPTION]... [-T] TARGET LINK_NAME   (1st form)
  or:  ln [OPTION]... TARGET                  (2nd form)
  or:  ln [OPTION]... TARGET... DIRECTORY     (3rd form)
  or:  ln [OPTION]... -t DIRECTORY TARGET...  (4th form)
In the 1st form, create a link to TARGET with the name LINK_NAME.
In the 2nd form, create a link to TARGET in the current directory.
In the 3rd and 4th forms, create links to each TARGET in DIRECTORY.
Create hard links by default, symbolic links with --symbolic.
By default, each destination (name of new link) should not already exist.
When creating hard links, each TARGET must exist.  Symbolic links
can hold arbitrary text; if later resolved, a relative link is
interpreted in relation to its parent directory.
```

### 하드 링크
하드 링크에는 치명적인 약점이 두 가지 있다.
- 파일시스템 외부에 있는 파일을 참조할 수 없다. 다시 말해서 하드 링크는 같은 디스크 파티션에 있는 파일이 아니면 참조할 수 없다.
- 하드 링크는 디렉토리를 참조할 수 없다.

하드 링크에 대해 고민할 때, 파일이 두 부분으로 만들어진다고 생각하면 조금 도움이 된다. 즉 파일은 파일 내용을 담고 있는 데이터 영역과 파일 이름을 갖고 있는 이름 영역, 이 두가지다. 시스템은 **아이노드(inode)**라고 불리는 디스크 블록 체인을 하나 할당하고 이것은 이름 영역과 연결된다. 결국 각 하드링크가 파일 내용을 담고 있는 각각의 아이노드를 참조하게 되는 것이다.

### 심볼릭 링크
하드 링크의 한계를 극복하기 위해서 탄생되었다. 심볼릭 링크는 참조될 파일이나 디렉토리를 가르키는 텍스트 포인터가 포함된 특수한 파일을 생성한다.

## 놀이터를 만들어 보자

### 디렉토리 생성
```shell
$ mkdir playground
$ cd playground/
$ mkdir dir1 dir2
$ l
dir1/  dir2/
```

### 파일 복사
```shell
$ cp /etc/passwd .
$ ls -l
total 18
drwxrwxr-x 2 lim lim     2 Apr  6 21:56 dir1
drwxrwxr-x 2 lim lim     2 Apr  6 21:56 dir2
-rw-r--r-- 1 lim lim 49721 Apr  8 20:59 passwd

# 경고 없이 그대로 덮어쓴다.
$ cp -v /etc/passwd .
'/etc/passwd' -> './passwd'

$ cp -i /etc/passwd .
cp: overwrite './passwd'? n
```


### 파일 이동 및 이름 변경
```shell
$ mv passwd fun
$ l
dir1/  dir2/  fun

$ mv fun dir1
$ mv dir1/fun dir2
$ mv dir2/fun .
$ l
dir1/  dir2/  fun

$ mv fun dir1
$ mv dir1 dir2

$ ls -l dir2
total 1
drwxrwxr-x 2 lim lim 3 Apr  8 21:04 dir1

$ ls -l dir2/dir1/
total 17
-rw-r--r-- 1 lim lim 49721 Apr  8 21:00 fun

$ mv dir2/dir1/ .
$ mv dir1/fun .
$ l
dir1/  dir2/  fun
```

### 하드 링크 생성
```shell
$ ln fun fun-hard
$ l
dir1/  dir2/  fun  fun-hard

$ ln fun dir1/fun-hard
$ ln fun dir2/fun-hard
$ ls -l dir1
total 17
-rw-r--r-- 3 lim lim 49721 Apr  8 21:00 fun-hard

$ ls -l
total 34
drwxrwxr-x 2 lim lim     3 Apr  8 21:13 dir1
drwxrwxr-x 2 lim lim     3 Apr  8 21:14 dir2
-rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun
-rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun-hard

# fun과 fun-hard 파일이 같은 아이노드 번호를 공유하고 있다.
# 즉, 둘은 동일한 파일이다.
$ ls -li
total 34
3169296 drwxrwxr-x 2 lim lim     3 Apr  8 21:13 dir1
3169297 drwxrwxr-x 2 lim lim     3 Apr  8 21:14 dir2
3242229 -rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun
3242229 -rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun-hard
```

### 심볼릭 링크 생성
```shell
$ ln -s fun-sym
$ ln -s ../fun dir1/fun-sym
$ ln -s ../fun dir2/fun-sym
$ ls -l dir1
total 17
-rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun-hard
lrwxrwxrwx 1 lim lim     6 Apr  8 21:22 fun-sym -> ../fun

# 절대 경로로 사용할 수 있지만,
# 상대 경로로 사용하는 것이 바람직하다.
$ ln -s /home/linux-study/lim/playground/fun dir2/fun-sym-abs
$ ls -l dir2
total 18
-rw-r--r-- 4 lim lim 49721 Apr  8 21:40 fun-hard
lrwxrwxrwx 1 lim lim     6 Apr  8 21:43 fun-sym -> ../fun
lrwxrwxrwx 1 lim lim    36 Apr  8 21:44 fun-sym-abs -> /home/linux-study/lim/playground/fun

$ ls -l
total 35
drwxrwxr-x 2 lim lim     4 Apr  8 21:22 dir1
drwxrwxr-x 2 lim lim     4 Apr  8 21:27 dir2
lrwxrwxrwx 1 lim lim     4 Apr  8 21:31 dir-sym -> dir1
-rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun
-rw-r--r-- 4 lim lim 49721 Apr  8 21:00 fun-hard
lrwxrwxrwx 1 lim lim     7 Apr  8 21:22 fun-sym -> fun-sym
```

### 파일 및 디렉토리 삭제
```shell
$ rm fun-hard
$ ls -l
total 19
drwxrwxr-x 2 lim lim     4 Apr  8 21:22 dir1
drwxrwxr-x 2 lim lim     4 Apr  8 21:27 dir2
lrwxrwxrwx 1 lim lim     4 Apr  8 21:31 dir-sym -> dir1
-rw-r--r-- 3 lim lim 49721 Apr  8 21:00 fun
lrwxrwxrwx 1 lim lim     7 Apr  8 21:22 fun-sym -> fun-sym

$ rm -i fun
rm: remove regular file 'fun'? y
$ ls -l
total 2
drwxrwxr-x 2 lim lim 4 Apr  8 21:22 dir1
drwxrwxr-x 2 lim lim 4 Apr  8 21:27 dir2
lrwxrwxrwx 1 lim lim 4 Apr  8 21:31 dir-sym -> dir1
lrwxrwxrwx 1 lim lim 7 Apr  8 21:22 fun-sym -> fun-sym

$ cd dir1
$ less fun-sym
fun-sym: No such file or directory
$ rm fun-sym dir1-sym

$ cd
$ rm -r playground
```
