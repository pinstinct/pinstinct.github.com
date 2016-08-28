---
layout: post
title: 2. 자바스크립트의 기본 명령어
categories: javascript&jquery
tags: [book, javascript, jquery, variable, array, operator]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

## 변수 (variable)
다른 프로그래밍 언어와 달리 자바스크립트는 변수를 선언할 때 해당 변수가 저장할 테이터의 타입을 명시할 필요가 없다.

{% highlight js %}
var myAge = 10;  // var는 keyword, maAge는 식별자(id)라고 함
{% endhighlight %}

식별자가 하나 이상의 단어로 이루어진다면 주로 캐멀케이스(CamelCase) 표기법을 사용한다. 이는 첫 단어는 모두 소문자로 표기하고 그 이후의 단어들은 첫 문자만 대문자로 표기하는 방법이다. 


## 배열 
목록(list)이나 서로 **관련된** 일련의 값을 다루어야 한다면 배열의 사용을 고려해보아야 한다. 배열은 얼마나 많은 값의 목록을 처리해야 할지 알 수 없을 때 특히 유용하다.

{% highlight js %}
var colors = ['red', 'orange', 'black'];
{% endhighlight %}

## 연산자 (operator)
1. 대입 연산자 : =
2. 산술 연산자 : +, -, /, *, ++, --, % 
3. 문자열 연산자 : +
4. 비교 연산자 : ==, ===, !=, !==, >, >=, <, <=
5. 논리 연산자 : &&, &#124;&#124;, !, 

> [2장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c02)