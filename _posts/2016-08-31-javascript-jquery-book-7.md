---
layout: post
title: 7. jQuery
categories: javascript&jquery
tags: [book, javascript, jquery, code]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

jQuery는 일반적으로 자바스크립트를 필요로 하는 다양한 작업들을 빠르고 일관된 방법으로 처리할 수 있는 간단한 방법을 제공한다. 특히 별다른 신경을 쓰지 않아도 모든 주요 브라우저에서 동일하게 작동한다.

jQuery는 웹페이지에 포함시킬 수 있는 자바스크립트 파일이다. CSS 형식의 selector를 이용하여 요소를 찾을 수 있으며, 그런 후에는 jQuery의 메서드들을 이용하여 해당 요소에 원하는 작업을 수행할 수 있다.

## 요소 찾기

jQuery() 함수는 페이지에서 하나 이상의 요소를 찾는다. 이후 발견된 요소들에 대한 참조를 저장하는 jQuery라는 객체를 생성한다.

{% highlight js %}
// $() : 함수(jQuery 객체 생성)
// 'li.hot' : 셀렉터
$('li.hot')
{% endhighlight %}

## 요소 조작

jQuery 객체가 가지고 있는 요소나 객체들을 **Matched set**또는 **jQuery 객체집합(selection)**이라고 한다.

{% highlight js %}
// $('li.hot') : jQuery 객체
// addClass('complete') : 메서드
$('li.hot').addClass('complete');
{% endhighlight %}

## DOM과의 차이점

- 기본적으로 크로스 브라우저 환경을 지원하고 있어 대체 코드를 작성할 필요가 없다.
- 루프를 수행하지 않아도 메서드의 동작이 선택된 모든 요소에 적용된다.


## 변수에 저장

{% highlight js %}
$listItems = $('li');
{% endhighlight %}

## 루프

jQuery를 사용할때 셀렉터가 여러개의 요소를 리턴하면, 하나의 메서드만 호출해도 모든 요소의 콘텐츠를 수정할 수 있다. jQuery 객체집합 내의 모든 요소들을 수정하는 기능을 **묵시적 반복(implicit iteration)**이라고 한다. 

{% highlight js %}
$('li.hot').addClass('favorite');
{% endhighlight %}

연속된 요소로부터 정보를 얻어오고 싶다면 루프를 작성하지 않고 .each()메서드를 사용하면 된다.

## 체이닝

하나 이상의 jQuery 메서드를 동일한 요소에 적용하고 싶다면 마침표 표기법을 이용하여 아래와 같이 여러개의 메서드를 나열하면 된다.

{% highlight js %}
$('li[id != "one"]').hide().delay(500).fadeIn(1400);
{% endhighlight %}

jQuery 객체 집합을 수정하기 위한 대부분의 메서드는 체이닝이 가능하다. 그러나 DOM에서 정보를 **조회(retrieve)**하는 메서드들은 체이닝으로 연결할 수 없다.

## jQuery 메서드 예제
[예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c07_self)

## jQuery Foundation
[Download](http://jquery.com/download/) | 
[API](http://api.jquery.com)

## CDN
콘텐츠 전송 네트워크(CDN : Content Delivery Network)는 전 세계에 퍼져 있는 일련의 서버를 이용한 서비스이다. 

{% highlight html %}
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.10.2/jquery.min.js">
</script>
<script>
window.jQuery || document.write('<script src="js/jquery-1.10.2.js"></script>')
</script>
{% endhighlight %}

jQuery가 제대로 로드되었는지 확인 후, 로드되지 않았으면 웹사이트를 서비스하는 서버에서 jQuery 스크립트를 로드하도록 한다.

## 스크립트 작성위치
닫는 </body>태그 바로 앞에 스크립트 배치하는 것이 가장 이상적이다.

## 자바스크립트 라이브러리
라이브러리는 다른 스크립트가 어떤 파일에서 함수, 객체, 메서드, 속성을 구현 코드를 빌려 쓴다는 개념이다.

jQuery, Twitter Bootstrap, Angular.js, ... 


>[7장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c07)