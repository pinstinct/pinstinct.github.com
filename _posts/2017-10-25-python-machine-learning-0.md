---
layout: post
title: Docker 이해
categories: MachineLearning
tags: [python, docker]
---


![](/image/docker-image-vs-containers.jpg)

도커 이미지는 **분리된 환경**이다.

![](/image/docker-image.png)

도커에서 실제 실행되는 것은 이미지가 아니다. 도커 이미지는 도커 컨테이너를 만드는데 사용하는 템플릿이다.

이미지를 생성하는 방법은 3가지가 있다.
- `docker pull`
- `docker commit`
- Dockerfile
  - RUN : 쉘 명령어 실행
  - ENV : 환경 변수 설정
  - EXPOSE : 오픈 port
  - CMD : 실행 컨테이너에 제공할 기본 값

이미지 삭제는 `docker rmi` 명령으로 할 수 있다.
