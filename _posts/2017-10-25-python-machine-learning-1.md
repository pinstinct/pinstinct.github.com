---
layout: post
title: Docker 환경 구성, 웹에서 데이터 가져오기
categories: MachineLearning
tags: [python, docker]
---

Youtube에서 [머신러닝/딥러닝 실전 입문](https://www.youtube.com/playlist?list=PLBXuLgInP-5m_vn9ycXHRl7hlsd1huqmS) 강의를 듣고 정리한 것이다.

## Docker 환경 구성

```shell
$ docker pull continuumio/miniconda3
$ docker run -it continuumio/miniconda3 /bin/bash
root@4c3004998c8a:/$ python3 -c "print(3+5)"
8
root@4c3004998c8a:/$ pip install beautifulsoup4
root@4c3004998c8a:/$ pip install requests
root@4c3004998c8a:/$ exit

# 이미지 저장 : docker commit <CONTAINER_ID> <IMAGE_NAME>:<IMAGE_TAG>
$ docker commit 48dea47e9b58 mlearn:init
$ docker run -it mlearn:init
 ```

### 로컬 디렉토리를 마운트해 이미지 실행
바인딩 할 폴더와 파일을 생성한다.
```shell
$ mkdir sample
$ cd sample
$ touch test.txt
```

폴더를 마운트해서 이미지 실행한다.
```shell
# docker run -it -v <LOCAL_DIRECTORY>:<CONTAINER_DIRECTORY> <IMAGE_NAME>:<IMAGE_TAG>
$ docker run -it -v /Users/hmlim/sample/:/sample mlearn:init
root@9192dfaba9f6:/sample$ python3 download-png1.py
```


## 웹에서 데이터 가져오기
URL을 다루는 모듈의 집합인 [urllib](https://docs.python.org/3/library/urllib.html) 라이브러리를 사용한다. `urllib.request`는 URL을 열고 읽기 위한 모듈이다.

```python
import urllib.request

# 다운받을 네트워크 위치
url = "http://uta.pw/shodou/img/28/214.png"

# 저장할 로컬 위치
savename = "test1.png"

# urlretrieve(url, filename=None, reporthook=None, data=None)
# url의 네트워크 오브젝트를 로컬 파일로 복사
urllib.request.urlretrieve(url, savename)
```

위의 방법은 파일을 바로 로컬에 저장한다. 네트워크 오브젝트를 파이썬 코드내에서 활용하고 싶다면 `urlopen()` 메서드를 사용한다.

```python
# 저장할 로컬 위치
savename = "test2.png"

# urlopen(): 문자열 혹은 request 객체로 된 url을 open
obj = urllib.request.urlopen(url).read()

# 파일저장
# open(filename, mode) : 파일 오브젝트를 리턴
# mode : r(읽기), w(쓰기), a(추가), r+(읽기+쓰기)
# 이미지 파일이기 때문에 rb(바이너리로 쓰기) 모드 사용
with open(savename, mode='wb') as f:
    f.write(obj)
    print ("save!")
```

이번엔 웹 페이지를 크롤링해보자.
```python
url = 'http://api.aoikujira.com/ip/ini'
obj = urllib.request.urlopen(url).read()
print(obj)
print('-')
print (obj.decode('utf-8'))
```
위의 코드를 실행한 결과는 다음과 같다.
처음 결과는 바이너리형태로 출력됐고, 두번째 결과는 utf-8형식으로 디코딩되어 출력됐다.
```shell

root@9192dfaba9f6:/sample# python3 download-png1.py
b'[ip]\nAPI_URI=http://api.aoikujira.com/ip/get.php\nREMOTE_ADDR=61.82.155.171\nREMOTE_HOST=61.82.155.171\nREMOTE_PORT=63549\nHTTP_HOST=api.aoikujira.com\nHTTP_USER_AGENT=Python-urllib/3.6\nHTTP_ACCEPT_LANGUAGE=\nHTTP_ACCEPT_CHARSET=\nSERVER_PORT=80\nFORMAT=ini\n\n'
-
[ip]
API_URI=http://api.aoikujira.com/ip/get.php
REMOTE_ADDR=61.82.155.171
REMOTE_HOST=61.82.155.171
REMOTE_PORT=63549
HTTP_HOST=api.aoikujira.com
HTTP_USER_AGENT=Python-urllib/3.6
HTTP_ACCEPT_LANGUAGE=
HTTP_ACCEPT_CHARSET=
SERVER_PORT=80
FORMAT=ini
```
