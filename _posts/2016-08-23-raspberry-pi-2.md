---
layout: post
title: Python, GPIO
categories: raspberrypi
tags: [udemy, raspberrypi, python, gpio]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/rpi-full-stack">Raspberry Pi: Full Stack</a>강의를 듣고 정리한 것이다.</div>

## Python install
[virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/) 독립적인 파이썬 환경을 제공한다.

{% highlight sh %}
$apt-get install python-dev
$apt-get install python-virtualenv

$cd /var
$mkdir working
$cd working/
$virtualenv venv

$. venv/bin/activate

$deactivate
{% endhighlight %}

## GPIO Basic

<img src="/image/rbpi-gpio-layout.png" alt="GPIO Layout">

1. Physical numbering (like 1, 2, 3, ... )
    - GPIO.BOARD : set pins to physical numbering system
2. BCM : Broadcom SOC channel (like GPIO**2**, GPI**3** , ...)
    - GPIO.BCM : set pins to BCM numbering system

> [Detail Guid GPIO Header and Pins](http://pinout.xyz/pinout/ground)

