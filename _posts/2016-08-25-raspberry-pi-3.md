---
layout: post
title: LED, Temperature&Humidity Sensor
categories: raspberrypi
tags: [udemy, raspberrypi, python, gpio, led, temperature, humidity, sensor, vim]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/rpi-full-stack">Raspberry Pi: Full Stack</a>강의를 듣고 정리한 것이다.</div>

## LED Blink

아래와 같이 LED를 연결한다.

<img src="/image/rbpi-led-blinky.png" alt="LED Blink">

RPi.GPIO module을 설치후 파이썬을 실행한다.

{% highlight sh %}
$cd /var/working
$apt-get install python-pip
$pip install rpi.gpio

$. venv/bin/activate
$python
{% endhighlight %}

코드 파일을 작성하기 전에 터미널에서 직접 파이썬을 조작해본다.

{% highlight py %}
>>> import RPi.GPIO as GPIO
>>> import time
>>> pin = 7
>>> GPIO.setmode(GPIO.BOARD)
>>> GPIO.setup(pin, GPIO.OUT)
>>> GPIO.output(pin, GPIO.HIGH)
>>> GPIO.output(pin, GPIO.LOW)
>>> GPIO.cleanup()
{% endhighlight %}

코드 파일 작성을 위해 vim 에디터를 설치한다. 설치 후 vim 명령어를 통해 blinky.py파일을 생성한다.
{% highlight sh %}
$apt-get install vim
$vim blinky.py
{% endhighlight %}

vim 에디터가 열리면 **i**를 눌러 입력모드로 전환한다.
{% highlight py %}
import RPi.GPIO as GPIO         ## Import GPIO Library
import time                     ## Import 'time' library (for 'sleep')

pin = 7                         ## We're working with pin 7
GPIO.setmode(GPIO.BOARD)        ## Use BOARD pin numbering
GPIO.setup(pin, GPIO.OUT)       ## Set pin 7 to OUTPUT

for i in range(0,20):           ## Repeat 20 times
    GPIO.output(pin, GPIO.HIGH) ## Turn on GPIO pin (HIGH)
    time.sleep(1)               ## Wait 1 second
    GPIO.output(pin, GPIO.LOW)  ## Turn off GPIO pin (LOW)
    time.sleep(1)               ## Wait 1 second

GPIO.cleanup()
{% endhighlight %}

위의 코드를 입력 후 **esc + :w**를 눌러 저장한다. **esc + :q**를 눌러 종료한다.

> [vim 명령어](http://vim.wikia.com/wiki/Tutorial) 


## Temperature&Humidity Sensor

[Adafruit Python DHT Sensor Library](https://github.com/adafruit/Adafruit_Python_DHT)를 이용해 간단하게 작동해본다. 우선, 아래와 같이 센서를 연결한다.

<img src="/image/rbpi-temperature-humidity-sensor.png" alt="Temperature&Humidity Sensor">

라이브러리를 다운받기 위해 git을 설치 후 기본 설정을 한다.

*user.name*과 *user.email*에는 자신의 이름과 메일을 입력한다. *config --list*명령어를 통해 입력한 것을 확인한다.

{% highlight sh %}
$apt-get install git-core
$git config --global user.name "pinstinct"
$git config --global user.email gusals3407@gmail.com
$git config --list
{% endhighlight %}

라이브러리를 다운로드 한 후 해당 디렉토리로 이동한다. *setup.py*를 실행해 설치한다.

{% highlight sh %}
$git clone https://github.com/adafruit/Adafruit_Python_DHT.git
$cd Adafruit_Python_DHT/
$python setup.py install
{% endhighlight %}

설치가 완료되면 *exampes*폴더로 이동하여 *AdafruitDHT.py*을 실행한다.

{% highlight sh %}
$cd examples/
$python AdafruitDHT.py 2302 17
{% endhighlight %}

[AdafruitDHT.py](https://github.com/adafruit/Adafruit_Python_DHT/blob/master/examples/AdafruitDHT.py)파일에 sensor 객체가 아래와 같이 정의 되어 있다.

{%  highlight py%}
sensor_args = { '11': Adafruit_DHT.DHT11,
                '22': Adafruit_DHT.DHT22,
                '2302': Adafruit_DHT.AM2302 }
{% endhighlight %}

센서에 적힌 것을 확인하여 *$python AdafruitDHT.py **2302** 17* 호출한다.

> [DHT22/AM2302 Digital Temperature And Humidity Measurement Sensor 구입처](http://storefarm.naver.com/mundolino/products/338805918?NaPm=ct%3Disea1vd9%7Cci%3Dcheckout%7Ctr%3Dco%7Ctrx%3D%7Chk%3D685454b0019e7af86726f1fbca8a6f870b89a171)

