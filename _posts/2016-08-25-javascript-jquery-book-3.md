---
layout: post
title: 3. 함수, 메서드, 객체
categories: javascript&jquery
tags: [book, javascript, jquery, function, parameter, argument, scope, property, methods, object, template, instance]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

## 함수
어떤 특정 작업을 수행하기 위해 필요한 일련의 구문들을 그룹화하기 위한 개념이다. 다른 부분에서 동일한 작업을 반복적으로 수행해야 한다면 함수를 재사용할 수 있다.
{% highlight js %}
// 함수 정의
// function은 keyword, sayHello는 식별자(id)라고 함
function sayHello() {
    /* 
        Code Block 
    */
}

// 함수 호출
sayHello();
{% endhighlight %}

간혹 함수는 자신의 작업을 수행하기 위해 추가적인 정보가 필요할 수도 있다. 이런 경우에는 함수를 선언할 때 **매개변수(parameter)**를 전달하면 된다.
{% highlight js %}
// 함수 정의
function getArea(width, height) {
    return width * height;
}
{% endhighlight %}

매개변수(parameter)를 가진 함수를 호출할 때는 함수 이름 다음의 괄호 안에 값을 명시하면 된다. 이 값들을 **인수, 인자(arguments)**라고 한다.
{% highlight js %}
// 함수 호출
getArea(3, 5);
{% endhighlight %}

- Parameter는 함수를 선언할 때의 변수의 **이름**을,
- Argument는 함수를 호출하면서 전달하는 **값**을 의미

배열을 이용해 여러 값을 전달(return)받는 함수 예
{% highlight js %}
function getSize(width, height, depth) {
    var area = width * height;
    var volume = width * height * depth;
    var sizes = [area, volume];
    return sizes;
}

var areaOne = getSize(3, 2, 1)[0];
var volumeOne = getSize(3, 2, 3)[1];
{% endhighlight %}


## 변수의 범위(scope)
1. 지역 변수 : var 키워드를 이용하여 함수 내부에 선언하면 이 변수는 해당 함수 내부에서만 사용할 수 있다. 이런 변수를 지역(local) 변수라고 한다. 지역 변수는 함수를 실행할 때 생성하고, 함수 실행이 완료되면 생성되었던 지역 변수들은 제거한다. 
- 함수가 두 번 실행되면 지역 변수들은 매번 다른 값을 갖게 된다.
- 두 개의 서로 다른 함수는 어떤 종류의 이름 충돌 없이 동일한 이름의 변수를 사용할 수 있다.
2. 전역 변수 : 함수의 바깥 부분에서 변수를 선언하면 이 변수는 스크립트의 어느 곳에서도 사용이 가능하다. 이러한 변수를 전역(global) 변수라고 한다. 전역 변수는 메모리에 지속적으로 보관되어 있다. 따라서 지역 변수보다 더 많은 메모리를 사용하며, 변수 간 이름 충돌의 위험이 있다.


## 객체
객체란 (현실 세계에서 인지하는 물체에 대한 모델을 만들기 위해) 변수와 함수를 그룹화한 것을 말한다.

|      | 객체              |
|------|-------------------|
| 변수 | 속성(property)    |
| 함수 | 메서드 (method) |


속성과 메서드 역시 **이름(id)**과 값을 가진다. 객체 내에서 이 이름은 **키(key)**라고 부른다. { key : value } 쌍을 가지고 있다.
{% highlight js %}
/* Object */
var hotel = {
    /* Property */
    name: 'Quay',
    rooms: 40,
    booked: 25,
    gym: true,
    roomTypes: ['트윈', '더블', '스위트'],

    /* Method */
    checkAvailability : function() {
        return this.rooms - this.booked;
    }

};
{% endhighlight %}

마침표 표기법(.(dot) notation)을 이용하면 객체의 속성이나 메서드에 접근이 가능하다.
{% highlight js %}
var hotelName = hotel.name;
{% endhighlight %}

**new**와 **객체 생성자**는 빈 객체를 생성한다. 그런 후에 객체에 속성과 메서드를 추가할 수 있다.
{% highlight js %}
/* Object */
var car = new Object(); // new는 키워드, Object()는 생성자 함수

/* Property */
car.make = 'BMW';  

/* Method */
car.changeSpeed = function() {
    // code block
};
{% endhighlight %}

객체의 속성을 제거하려면 delete 키워드를 사용한다.
{% highlight js %}
delete hotel.name;
{% endhighlight %}

단지 속성 값만을 지우려면 빈 문자열을 대입하면 된다.
{% highlight js %}
hotel.name = '';
{% endhighlight %}

비슷한 모델을 표현하기 위해 여러 개의 객체를 생성해야 할 경우가 있다. 객체 생성자는 함수를 마치 객체 생성을 위한 **템플릿(template)**처럼 사용한다.
{% highlight js %}
// 생성자 함수의 이름은 주로 대문자로 표기
function Hotel(name, rooms, booked) {
    // 현재 사용 중인 객체에 정의 된 것임을 표현하기 위해 객체의 이름 대신 this 키워드 사용

    /* Property */
    this.name = name;
    this.rooms = rooms;
    this.booked = booked;

    /* Method */
    this.checkAvailability = function() {
        return this.rooms - this.booked;
    }
}

/* Object */
var quayHotel = new Hotel('Quay', 40, 25);
var parkHotel = new Hotel('Park', 120, 77);
{% endhighlight %}

생성자 함수를 사용하면 객체의 **인스턴스(instance)**를 생성할 수 있다. 그러려면 **new 키워드** 다음에 새로운 객체를 생성하는 함수를 호출하면 된다.


### 객체 생성 법 복습
{% highlight js %}
/*  객체를 생성한 후 속성과 메서드를 추가하는 방법 */
// 축약형 표기법
var hotel = {}

hotel.name = 'Quay';
hotel.rooms = 40;
hotel.booked = 25;

hotel.checkAvailability = function() {
    return this.rooms - this.booked;
};
// 객체 생성자 표기법
var hotel = new Object();

hotel.name = 'Quay';
hotel.rooms = 40;
hotel.booked = 25;
hotel.checkAvailability = function() {
    return this.rooms - this.booked;
};


/*  속성과 메서드를 사전에 정의하여 객체 생성하기 */
// 축약형 표기법
var hotel = {
    name: 'Quay',
    rooms: 40,
    booked: 25,
    checkAvailability: function() {
        return this.rooms - this.booked;
    }
};
// 객체 생성자 표기법
function Hotel(name, rooms, booked) {
    this.name = name;
    this.rooms = rooms;
    this.booked = booked;
    this.checkAvailability = function() {
        return this.rooms - this.booked;
    };
}
var quayHotel = new Hotel('Quay', 40, 25);
var parkHotel = new Hotel('Park', 120, 77);
{% endhighlight %}

### 배열도 객체다
배열은 실제로 특별한 형식의 객체이다. 배열은 (객체와 마찬가지로) 서로 관련이 있는 **키/값** 쌍의 집합을 저장하지만 각 값의 **키**로는 **인덱스 번호**가 사용된다.

복잡한 데이터 구조를 표현하기 위해서는 배열과 객체를 혼합하여 사용할 수도 있다. 배열은 일련의 객체(와 그 순서)를 저장할 수 있다. 물론, 객체 역시 (하나의 속성에 대한 값으로서) 배열을 저장할 수도 있다.

### 객체 내의 배열
<table>
  <tr>
    <th>속성</th>
    <th>값</th>
  </tr>
  <tr>
    <td>room1</td>
    <td>items[420, 40, 10]</td>
  </tr>
  <tr>
    <td>room2</td>
    <td>items[460, 20, 20]</td>
  </tr>
  <tr>
    <td>room3</td>
    <td>items[230, 0, 0]</td>
  </tr>
  <tr>
    <td>room4</td>
    <td>items[620,150, 60]</td>
  </tr>
</table>

room1의 첫 번째 숙박비를 가져오려면 다음과 같은 구문을 사용한다.
{% highlight js %}
costs.room1.items[0];
{% endhighlight %}

### 배열 내의 객체
<table>
  <tr>
    <th>번호</th>
    <th>값</th>
  </tr>
  <tr>
    <td>0</td>
    <td>{accom: 420, food: 40, phone: 10}</td>
  </tr>
  <tr>
    <td>1</td>
    <td>{accom: 460, food: 20, phone: 20}</td>
  </tr>
  <tr>
    <td>2</td>
    <td>{accom: 230, food: 0, phone: 0}</td>
  </tr>
  <tr>
    <td>3</td>
    <td>{accom: 620, food: 150, phone: 60}</td>
  </tr>
</table>

세번째 방의 전화 사용료를 가져오려면 다음과 같은 구문을 사용한다.
{% highlight js %}
costs[2].phone
{% endhighlight %}

## 내장 객체

우리가 생성하는 객체들은 주로 필요에 따라 만들어진다. 또한, 이 객체들은 필요한 기능을 포함하거나 내부적으로 필요한 데이터를 모델화하는 데 사용된다. 반면, 내장 객체들은 많은 스크립트들이 필요로 하는 공통의 기능을 제공한다.

1. 브라우저 객체 모델 (Browser Object Model) 
2. 문서 객체 모델 (Document Object Model) 
3. 전역 자바스크립트 객체 (Global Javascript Objects)

(* 객체 모델이란, 객체의 집합이다.)


### 1. 브라우저 객체 모델
브라우저 탭 혹은 창의 모델을 생성한다. 최상의 객체는 window 객체이다. 이 객체의 자식 객체들은 브라우저의 다른 기능들을 표현한다.
<pre>
Window : 현재 브라우저 창이나 탭
+-- document : 현재 로드된 웹 페이지
+-- History : 브라우저 히스토리에 기록된 웹 페이지들
+-- Location : 현재 페이지의 URL
+-- Navigator : 브라우저와 관련된 정보
+-- Screen : 장치의 디스플레이 정보
</pre>

screen 객체의 width 속성은 현재 사용 중인 장치 화면의 가로 픽셀 수를 알려준다.
{% highlight js %}
window.screen.width;
{% endhighlight %}

### 2. 문서 객체 모델
문서 객체 모델(DOM)은 현재 웹 페이지의 모델을 생성한다. 최상의 객체는 document 객체이며, 전체 페이지를 표현한다. 자식 객체로는 페이지의 다른 요소들을 표현하는 객체들이 사용된다.

<img src="/image/js-dom-model.gif" alt="DOM Model" />


### 3. 전역 자바스크립트 객체
전역 객체는 어떤 특정 모델을 표현하지는 않는다. 이 객체들은 자바스크립트 언어의 각기 다른 부분을 담당하는 관련 객체들의 집합이다.

다음의 객체들은 기본적인 데이터 타입을 표현한다.

- String : 문자열 값들을 다룬다.
- Number : 숫자 값들을 다룬다.
- Boolean : 불리언 값들을 다룬다.

다음 객체들은 실제 세계의 개념들을 다루기 위한 것들이다.

- Date : 날짜를 표현하며 조작한다.
- Math : 숫자 값을 다루며 이들을 이용한 다양한 연산을 수행한다.
- RegEx : 텍스트를 구성하는 문자열 내의 패턴 처리를 담당한다.

예제
{% highlight js %}
hotel.toUpperCase();
Math.PI;
{% endhighlight %}

#### 전역 객체 : String 객체
 문자열 값을 다룰 때는 주로 String 객체의 속성이나 메서드를 사용하게 된다. 이런 속성과 메서드들은 주로 변수나 객체에 저장된 문자열을 다루기 위한 용도로 제공된다.

 String 객체가 전역 객체(global object)로서 제공되기 때문에 스크립트의 어느 곳에서도 사용이 가능한 것 이다. 또한, 문자열 자체가 마치 String 객체인 것처럼 동작하기 때문에 래퍼 객체(wrapper object)라고 부르기도 한다. 따라서 모든 문자열 값을 통해 String 객체의 속성과 메서드를 실행할 수 있다.

{% highlight js %}
var saying = 'Home sweet home ';
var msg = '<p> 길이: ' + saying.length + '</p>';
{% endhighlight %}

#### 예약된 데이터 타입
- String
- Number
- Boolean
- Undefined : 변수가 선언은 되었지만 아무런 값이 대입되지 않은 상태일 때 갖는 값
- Null : 변수 값이 없는 경우, 한때 어떤 값을 가지고 있었더라도 지금은 아무런 값도 갖지 않은 상태
- Object

> [3장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c03)
