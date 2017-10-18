---
layout: post
title: AWS EC2 서버 설정과 배포
categories: patentstart
tags: [python, django, project, aws, ec2, deploy, uwsgi, nginx]
---

## 리눅스 서버 설정
AWS EC2 인스턴스를 생성해 작업했다.

```shell
$ sudo apt-get install language-pack-ko
$ sudo locale-gen ko_KR.UTF-8
$ sudo apt-get update

$ sudo apt-get install python-pip

# zsh
$ sudo apt-get install zsh
$ sudo curl -L http://install.ohmyz.sh | sh
$ sudo chsh ubuntu -s /usr/bin/zsh

# pyenv
$ sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils
$ curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
$ vi ~/.zshrc
```

```vim
# .zshrc
export PATH="/home/ubuntu/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

```shell
$ source ~/.zshrc
$ sudo apt-get dist-upgrade
$ sudo shutdown -r now
```

## 장고 프로젝트
### 1. 디렉토리 생성
```shell
$ cd /
$ l
drwxr-xr-x   2 root root 4.0K Jan 13 14:20 srv

$ sudo chown -R ubuntu:ubuntu /srv/
$ l
drwxr-xr-x   2 ubuntu ubuntu 4.0K Jan 13 14:20 srv

$ cd srv
$ mkdir app
```
### 2. 서버 전송
```shell
$ l
total 24
drwxr-xr-x   9 limhm  staff   306B  3  6 11:40 .
drwxr-xr-x  21 limhm  staff   714B  3  6 11:17 ..
drwxr-xr-x   4 limhm  staff   136B  3  6 11:51 .conf-secret
drwxr-xr-x  12 limhm  staff   408B  3  6 12:09 .git
-rw-r--r--   1 limhm  staff   2.7K  3  6 11:40 .gitignore
drwxr-xr-x   8 limhm  staff   272B  3  6 12:23 .idea
-rw-r--r--   1 limhm  staff    11B  3  6 11:21 .python-version
drwxr-xr-x   6 limhm  staff   204B  3  6 11:40 django_app
-rw-r--r--   1 limhm  staff    31B  3  6 11:29 requirements.txt


$ scp -r -i ~/.ssh/YOUR_KEY_PAIR.pem . ubuntu@YOUR_PublicDNS:/srv/app/
```

### 3. 서버 프로젝트 설정
```shell
$ pyenv install 3.6.0
$ pyenv virtualenv 3.6.0 ps
$ pip install  -r requirements.txt
```

## uWSGI
### 1. 설치
```shell
$ pip install uwsgi
```

### 2. `.ini` 파일 작성

```shell
$ sudo mkdir /etc/uwsgi
$ sudo mkdir /etc/uwsgi/sites
$ sudo vi /etc/uwsgi/sites/app.ini
```

```ini
[uwsgi]
chdir = /srv/app/django_app
module = config.wsgi
home = /home/ubuntu/.pyenv/versions/ps

uid = ubuntu
gid = ubuntu

socket = /tmp/app.sock
chmod-socket = 666
chown-socket = ubuntu:ubuntu

enable-threads = true
master = true
vacuum = true
pidfile = /tmp/app.pid
```

### 3. 서비스 파일 작성

```shell
$ sudo vi /etc/systemd/system/uwsgi.service
```

```service
[Unit]
Description=uWSGI Emperor service
After=syslog.target

[Service]
ExecPre=/bin/sh -c 'mkdir -p /run/uwsgi; chown ubuntu:ubuntu /run/uwsgi'
ExecStart=/home/ubuntu/.pyenv/versions/ps/bin/uwsgi --uid ubuntu --gid ubuntu --master --emperor /etc/uwsgi/sites

Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```

### 4. uWSGI 재실행
```shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart uwsgi

$ cd /tmp
$ l
# app.pid, app.sock 확인
-rw-r--r--  1 ubuntu ubuntu    6 Oct 17 04:37 app.pid
srw-rw-rw-  1 ubuntu ubuntu    0 Oct 17 04:37 app.sock
```

## Nginx
### 1. 설치
```shell
$ sudo apt-get install nginx
```

### 2. 서버 설정 파일 작성

```shell
$ cd /etc/nginx/sites-available
$ touch app
$ sudo vi app
```

```nginx
server {
  listen 80;
  server_name *.compute.amazonaws.com;
  charset utf-8;
  client_max_body_size 128M;

  location / {
    uwsgi_pass unix:///tmp/app.sock;
    include uwsgi_params;
  }
}
```

### 3. 심볼릭 링크 생성
```shell
$ cd /etc/nginx/sites-enabled
$ sudo ln -s ../sites-available/app .
```

### 4. Nginx 재실행
```shell
$ sudo systemctl restart nginx
```
