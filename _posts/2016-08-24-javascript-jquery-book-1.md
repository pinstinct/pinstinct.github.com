---
layout: post
title: 1. 프로그래밍의 기초
categories: javascript&jquery
tags: [book, javascript, object, property, event, method]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

## 스크립트란?
스크립트는 명령어의 모음이다. 컴퓨터가 원하는 목적을 달성하도록 만들기 위한 명령어 모음이다.

1. 목표설정
2. 스크립트 디자인 (흐름도, flowchart)
<img src="/image/js-flowchart.png">
3. 각 단계 코드로 표현


## 컴퓨터는 어떻게 세상과 어떻게 소통하는가?

### 객체 , Object (물체) 
1. 속성, properties
2. 이벤트, events
3. 메서드, methods

#### 1. 속성 (특징)
- 이름, name
- 값, value

#### 2. 이벤트
이벤트(event)는 컴퓨터가 "이봐, 방금 전에 이런 일이 일어났었어!"라고 말하는 방법이다.

프로그래머는 자신이 대응해야 할 이벤트를 선택한다. 특정 이벤트가 발생하면 이 이벤트는 그에 대응하기 위한 일련의 코드를 실행하는 데 사용한다.

#### 3. 메서드
객체를 사용하여 하고자 하는 일을 표현한다. 즉, 이벤트는 메서드를 실행하고 메서드는 객체의 속성을 알아내거나 변경한다.

{% highlight js %}
objectHotel {
    properties {
        name : Quay,
        rating : 4,
        rooms : 42,
        bookings : 22,
        gym : false,
        pool : true
    }
    event {
        book // 예약이 이루어졌을 때, makeBooking() 메서드를 호출
        cancel // 예약이 취소되었을 때, cancelBooking() 메서드를 호출
    }
    method {
        makeBooking() // bookings 속성 값을 증가시킨다.
        cancelBookin() // bookings 속성 값을 감소시킨다.
        checkAvailability() // rooms 속성 값에서 bookings 속성 값을 빼서 예약 가능한 방의 개수를 리턴한다.
    }
}
{% endhighlight %}

### 웹 브라우저와 객체
1. window 객체 : 웹 브라우저는 window 객체를 이용해 브라우저 창(or 탭)을 표현한다.
2. document 객체 : 웹 페이지는 각 브라우저 창에 로드되며, document 객체를 통해 모델로서 표현된다.

### 브라우저가 웹 페이지를 보는 방법
1. 브라우저가 HTML 페이지를 다운로드한다. 
2. 페이지의 모델을 만들고 메모리에 저장한다. 이 모델의 최상위에 있는 객체가 바로 document 객체이다. document 객체 아래에 있는 각 상자들을 노드(node)라고 부른다.
3. 내장 엔진을 이용하여 페이지를 화면에 표시한다.