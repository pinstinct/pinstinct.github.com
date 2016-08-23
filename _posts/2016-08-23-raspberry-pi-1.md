---
layout: post
title: Install&Setting
categories: raspberrypi
tags: [udemy, raspberrypi]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/rpi-full-stack">Raspberry Pi: Full Stack</a>강의를 듣고 정리한 것이다.</div>

## OS 선택
작은 용량의 SD Card에도 적합한 [MINIBIAN](https://minibianpi.wordpress.com/)을 설치한다.

## SD card 설정
다운로드 받은 MINIBIAN img 파일을 SD card에 Mount 한다. SD card에 Raspberry pi OS를 쉽게 마운트할 수 있는 [ApplePi-Baker](http://www.tweaking4all.com/news/applepi-baker-v1-6-update/)를 이용한다. 

작업이 완료되면 Raspberry pi에 SD card를 장착한다.

## SSH 접속
기본적으로 ```ID : root , password : raspberry``` 설정되어 있다.
{% highlight sh %}
$ssh root@127.0.0.1
{% endhighlight %}

## 비밀번호 변경
{% highlight sh %}
$passwd root
{% endhighlight %}

## apt-get 업데이트
apt-get은 software package를 쉽게 관리해주는 유틸리티이다. 
{% highlight sh %}
$apt-get update
{% endhighlight %}

## Raspeberry Pi 설정
raspi-config는 Raspeberry Pi의 기본설정을 쉽게 관리해주는 유틸리티이다.  유틸리티를 이용해 기본 설정을 한다.
{% highlight sh %}
$apt-get install raspi-config
$raspi-config
{% endhighlight %}

- Expand filesystem을 선택 후 OK > Finish > Reboot
- Advanced Options에서 >  SPI, I2C, Serial 3개를 차례대로 Enable


