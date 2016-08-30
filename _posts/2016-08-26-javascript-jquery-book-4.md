---
layout: post
title: 4. 의사결정과 루프
categories: javascript&jquery
tags: [book, javascript, if, switch, loop, for, while]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

## 조건문 (conditional statements)
if, switch 구문

### 비교 연산자
비교 연산자의 가장 기본적인 사용법은 두 값을 비교하여 true나 false 값을 얻어내는 것이다.

==, ===, !=, !==, >, >=, <, <=

### 논리 연산자
비교 연산자는 주로 true나 false 중 하나의 값을 리턴한다. 논리 연산자는 둘 이상의 비교 연산자의 결과를 비교하기 위해 사용한다.

&&, &#124;&#124;, !


- 단축 평가(short-circuit evaluation) : 두 개 이상의 조건을 평가할 때 첫 번째 조건을 평가한 결과가 전체 평가 결과를 좌우한다면 두 번째 조건을 평가하지 않는 동작 방식을 말한다. 예를 들면 **false && 표현식** 혹은 **true &#124;&#124; 표현식**인 경우 다른 결과를 계속 판단해야 할 이유가 없다.

### if 구문
if 구문은 조건식을 평가(혹은 검사)하는 구문이다. 만일 조건식이 true라고 판단되면 그 이후의 코드 블록 내의 코드들이 실행된다.

### if-else 구문
조건식을 검사하여 true로 판단되면 if 코드 블록이 실행된다. false로 판단되면 else 코드 블록이 실행된다.

### switch 구문
switch 구문은 스위치 값(switch value)이라고 부르는 변수와 함께 시작한다. 각각의 case 구문은 이 변수가 가질 수 있는 값을 가르키며, 코드는 변수 값이 일치할 때만 실행된다.

{% highlight js %}
switch (level) {
  case 'One':
    title = 'Level 1';
    break;
  case 2:
    title = 'Level 2';
    break;
  default:
    title ='Test';
    break;
}
{% endhighlight %}

### 타입 강제와 약한 형식화
*자바스크립트*는 내부적으로 데이터 타입을 변환하여 작업을 완료할 수 있다. 이를 **타입 강제(type coercion)**라고 한다.

자바스크립트는 **약한 형식화(weak typing)**를 사용한다고 말할 수 있는데, 그 이유는 값의 데이터 타입이 바뀔 수 있기 때문이다. 강력한 형식화(string typing)를 사용하는 일부 다른 프로그래밍 언어들은 각각의 변수가 어떤 데이터 타입을 사용할 것인지를 명시하도록 규정하고 있다.

### true 혹은 false로 취급되는 값들
false로 취급될 수 있는 값들

{% highlight js %}
var highScore = false;      // 전형적인 불리언 false
var highScore = 0;          // 숫자 0
var highScore = '';         // NaN(Not a Number, 숫자가 아님)
var highScore = 10/'score'; // 빈 값
var highScore;              // 아직 값을 대입하지 않은 변수
{% endhighlight %}

이를 제오한 거의 모든 값들은 true로 취급할 수 있다.

{% highlight js %}
var highScore = true;       // 전형적인 불리언 true
var highScore = 1;          // 0이 아닌 다른 숫자
var highScore = 'carrot';   // 내용을 가진 문자열
var highScore = 'true'      // 문자열로 표현된 true
var highScore = 'false'     // 문자열로 표현된 false
var highScore = '0'         // 문자열로 표현된 0
var highScore = 10/5;       // 계산된 숫자
{% endhighlight %}

### 단축 평가 값
논리 연산자들은 왼쪽 오른쪽 순서로 실행된다. 이 연산자들은 결과를 얻게 되는 순간 단축 평가(즉, 평가의 중단)를 실행한다. 그러나 그 결과값은 (반드시 true 또는 false가 아니라)평가를 중단하게 된 계기가 된 값을 리턴한다.

개발자들은 이런 특징을 창의적으로 활용한다. 예를 들어, 변수 값을 대입하거나 혹은 새로운 객체를 생성한다.

{% highlight js %}
var nickName = '';
var nickNameA = (nickName || '손님');

var nickName = '';
var nickNameA = (nickName || {});
{% endhighlight %}

다음 예제를 살펴보자. 세 개의 값을 정의한다. 만일 이 세 값 중 어느 하나라도 true로 취급될 수 있으면 if 구문 내의 코드가 실행될 것이다. valueB를 만나게 되면, 숫자 1은 true로 취급될 수 있기 때문에 단축 평가가 실행되어 연산이 종료되고 if 구문 내의 코드 블록이 실행된다.

{% highlight js %}
valueA = 0;
valueB = 1;
valueC = 2;

if (valueA || valueB || valueC){
  // code block
}
{% endhighlight %}

논리 연산자를 사용할 때 다음과 같은 내용을 고려하면 좋다.

- OR 연산자를 사용할 때는 true를 리턴할 가능성이 높은 코드를 첫 번째 조건으로 작성하고, AND 연산자를 사용할 때는 false를 리턴할 가능성이 높은 코드를 첫 번째 조건으로 작성한다.
- 평가를 수행할 때 비용이 많이 필요한 코드는 가능한 나중에 평가되도록 작성하여 코드가 실행되지 않도록 한다.


## 루프
루프는 조건을 검사하여 true이면 코드블록을 실행한다. false를 리턴할 때까지 계속해서 반복된다.

- for : 일정한 횟수만큼 코드를 반복 실행해야 한다면 for 루프가 가장 적합
- while : 반복 실행할 횟수를 정확히 알지 못한다면 while 루프를 사용하는 것이 좋다.
- do while : while 루프와 비슷하지만 한 가지 차이점이 있다. 조건식이 false를 리턴하더라도 우선 코드 블록을 한 번 실행한 후 루프를 시작한다.

> [4장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c04)