---
layout: post
title: 퍼미션
categories: linux
tags: [linux, command, shell, cli]
---

# 9. 퍼미션

이 장에서는 시스템 보안의 필수적인 부분을 살펴보고 다음 명령어들을 소개한다.

- `id` : 사용자 ID 정보를 표시
- `chmod` : 파일 모드를 변경
- `umask` : 기본 파일 퍼미션 설정
- `su` : 다른 사용자로 쉘을 실행
- `sudo` : 다른 사용자로 명령어를 실행
- `chown` : 파일 소유자를 변경
- `chgrp` : 파일 그룹 소유자를 변경
- `passwd` : 사용자 비밀번호를 변경



## 소유자, 그룹 멤버, 기타사용자

```shell
$ file /etc/shadow
/etc/shadow: regular file, no read permission
$ less /etc/shadow
/etc/shadow: Permission denied
```

이 오류 메시지의 원인은 이 파일을 읽을 권한이 없는 일반 사용자이기 때문이다.

유닉스 보안 모델에서 사용자는 파일과 디렉토리를 **소유**할 수 있다. 사용자가 파일 또는 디렉토리를 소유할 때, 그 사용자는 소유물의 접근을 제어한다. 또한 사용자들은 한 명 이상으로 구성된 **그룹**에 속할 수 있다.

```shell
$ id
uid=1023(lim) gid=1024(lim) groups=1024(lim),27(sudo),1001(linux-study)
```

사용자 계정이 생성되면, 사용자들은 **사용자 ID(user ID)** 또는 **uid**라 불리는 번호를 할당 받는다. 그 사용자는 **주 그룹 ID(gid)**를 할당 받고 추가로 다른 그룹에도 속할 수 있다.

이러한 정보는 어디에서 오는가? 리눅스에 존재하는 다른 것들처럼, 다수의 텍스트 파일에서 얻어온다.

```shell
# 사용자 계정
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:109::/var/run/dbus:/bin/false
lxd:x:107:65534::/var/lib/lxd/:/bin/false
uuidd:x:108:113::/run/uuidd:/bin/false
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
pollinate:x:111:1::/var/cache/pollinate:/bin/false
jehos:x:1000:1000::/home/jehos:/bin/bash
2chaes:x:1002:1003::/home/linux-study/2chaes:/bin/bash
lim:x:1023:1024::/home/linux-study/lim:/bin/bash
# ...

# 그룹
$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:
floppy:x:25:
tape:x:26:
sudo:x:27:jehos,2chaes,pildn90,across0406,hatchling13,redniche,bfhj0708,rudalson,handj310,leedg1569,skarlgus,sprtms9062,fishandcat,flzl2008,lunate24,gandalf,kdy,lifili,youngju,wt,hsim,braveek1,lim,overwatch,alphanomega,okokokk2,lovsoso,vingo,dddlee1234,leefmus,stupid42
audio:x:29:
dip:x:30:
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
systemd-journal:x:101:
systemd-timesync:x:102:
systemd-network:x:103:
systemd-resolve:x:104:
systemd-bus-proxy:x:105:
input:x:106:
crontab:x:107:
syslog:x:108:
messagebus:x:109:
netdev:x:110:
lxd:x:111:
mlocate:x:112:
uuidd:x:113:
ssh:x:114:
admin:x:115:
jehos:x:1000:
linux-study:x:1001:2chaes,pildn90,across0406,hatchling13,redniche,bfhj0708,rudalson,handj310,leedg1569,skarlgus,sprtms9062,fishandcat,flzl2008,lunate24,gandalf,kdy,lifili,youngju,wt,hsim,braveek1,lim,overwatch,alphanomega,okokokk2,lovsoso,vingo,dddlee1234,leefmus,stupid42
2chaes:x:1003:
lifili:x:1019:
youngju:x:1020:
wt:x:1021:
hsim:x:1022:
braveek1:x:1023:
lim:x:1024:
overwatch:x:1025:
# ...

# 비밀번호
$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog
tty:x:5:
disk:x:6:
lim:...
```



## 읽기, 쓰기, 실행

파일과 디렉토리의 접근권은 읽기 권한, 쓰기 권한, 실행 권한이란 용어로 정의된다.

```shell
$ ls -l foo.txt
-rw-r--r--  1 limhm  staff  0  5 18 20:19 foo.txt
```

처음 10개의 문자는 **파일 속성**을 나열한 것이다. 첫 문자는 **파일 종류(file type)**를 나타낸다. 나머지 9개의 문자는 **파일 모드(file mode)**를 나타낸다.

| 속성   | 파일 종류                                    |
| ---- | ---------------------------------------- |
| -    | 일반 파일                                    |
| d    | 디렉토리 (어떤 파일인지 정보를 가지고있는 특별한 파일)          |
| l    | 심볼릭 링크 (심볼릭 링크의 파일 속성은 항상 rwxrwxrwx이고 그것은 더미 값이다. 실제 파일 속성은 심볼릭 링크가 가르키는 파일의 속성이다.) |
| c    | 문자 특수 파일 (이 파일 종류는 터미널이나 모뎀같이 바이트의 열로 데이터를 처리하는 디바이스를 나타낸다.) |
| b    | 블록 특수 파일 (이 파일 종류는 하드 드라이브나 CD-ROM 드라이브같이 블록 단위의 데이터를 처리하는 디바이스를 나타낸다.) |



| 속성   | 파일                                       | 디렉토리                                     |
| ---- | ---------------------------------------- | ---------------------------------------- |
| r    | 파일 열기와 읽기를 허용한다.                         | 실행 속성이 설정되어 있으면 디렉토리의 내용물을 나용할 수 있게끔 허용한다. |
| w    | 파일 쓰기 또는 잘라내기는 허용하지만, 이름 변경이나 파일삭제는 허용하지 않는다. 파일 삭제나 파일 이름 변경은 디렉토리 속성에 의해 결정된다. | 실행 속성이 설정되어 있으면 디렉토리 내의 파일들을 생성, 삭제, 이름 변경이 가능하도록 허용한다. |
| x    | 파일이 프로그램으로 처리되고 파일이 실행되도록 허용한다.          | 디렉토리에 들어올 수 있도록 허용한다.                    |



### chmod - 파일 모드 변경

파일 또는 디렉토리의 모드를 변경하기 위해서는 `chmod` 명령어를 사용한다. 모드의 변경은 오직 파일 소유자나 슈퍼유저만이 가능하다는 것을 명심해야 한다. `chmod`는 모드 변경을 표현하는 두 방법을 제공한다.

#### 1. 8진법 표현

| 8진법  | 2진법  | 파일 모드 |
| ---- | ---- | ----- |
| 0    | 000  | `---` |
| 1    | 001  | `--x` |
| 2    | 010  | `-w-` |
| 3    | 011  | `-wx` |
| 4    | 100  | `r—`  |
| 5    | 101  | `r-x` |
| 6    | 110  | `rw-` |
| 7    | 111  | `rwx` |

```shell
$ ls -l foo.txt
-rw-r--r--  1 limhm  staff  0  5 18 20:19 foo.txt

$ chmod 600 foo.txt
$ ls -l foo.txt
-rw-------  1 limhm  staff  0  5 18 20:19 foo.txt

```



#### 2. 기호 표현

| 기호   | 의미                      |
| ---- | ----------------------- |
| u    | 파일이나 디렉토리 소유자를 의미(user) |
| g    | 그룹 소유자(group)           |
| o    | 기타 사용자(other)           |
| a    | all의 약자로, u,g,o의 조합     |

```shell
$ ls -l foo.txt
-rw-------  1 limhm  staff  0  5 18 20:19 foo.txt

$ chmod go=rw foo.txt
$ ls -l foo.txt
-rw-rw-rw-  1 limhm  staff  0  5 18 20:19 foo.txt
```
