---
layout: post
title: 6. 이벤트
categories: javascript&jquery
tags: [book, javascript, event, handling, binding, listener, handler]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

웹을 탐색하는 동안 브라우저는 여러 종류의 이벤트를 등록한다. 이벤트는 브라우저가 어떤 일이 일어났음을 알리는 방법이다. 우리는 이런 이벤트들에 반응하는 스크립트를 작성할 수 있다.

## 이벤트 종류

- UI 이벤트 : 사용자가 웹 페이지가 아닌 브라우저의 UI와 상호작용할 때 발생한다. (load, unload, error, resize, scroll)
- 키보드 이벤트 : 사용자가 키보드를 이용할 때 발생한다. (input, keydown, keyup, keypress)
- 마우스 이벤트 : 마우스를 움직이거나 버튼을 클릭했을 때 발생한다. (click, dbclick, mousedown, mouseup, mosemove, mouseover, mouseout)
- 포커스 이벤트 : 요소(링크나 폼 필드)가 포커스를 얻거나 잃을 때 발생한다. (**focus**, focusin, **blur**, focusout)
- 폼 이벤트 : 사용자가 폼(form) 요소와 상호작용할 때 발생한다. (input, change, **submit**, reset, cut, copy, paste, select)
- 변형 이벤트 : 스크립트에 의해 DOM의 구조가 변경될 때 발생한다. 변형 이벤트는 페이지의 동작이 느려지게 만들 수 있다. 이를 해결하기 위해 변형 관찰자(mutation observer)를 대신 사용할 수 있다. (DOMSubtreeModified, DOMNodeInserted, DOMNodeRemoved, DOMNodeInsertedIntoDocument, DOMNodeRemovedFromDocument)

예를 들어 사용자가 링크를 탭하면 브라우저에서는 **마우스 click 이벤트**가 **발생**한다고 표현한다. click 이벤트가 발생하면 선택된 아이템을 더욱 크게 보여주는 스크립트를 호출할 수 있다.

## 이벤트 핸들링 (event handling)

이벤트가 자바스크립트를 실행하는 방법은 다음과 같다.

1. 이벤트에 반응할 스크립트를 실행할 **요소**를 선택한다.
2. 선택된 노드에서 어떤 **이벤트**가 발생했을 때 그에 반응할 것인지를 명시한다. (바인딩, binding)
3. 이벤트가 발생했을 때 실행될 **코드**를 작성한다.

## 요소에 이벤트를 바인딩하는 세가지 방법

특정 요소에서 발생하기를 기다리는 이벤트는 이벤트 핸들러를 통해 명시한다. 이벤트 핸들러에는 다음의 세 가지 종류가 있다.

### 1. HTML 이벤트 핸들러

그다지 권장하는 방법은 아니지만, 예전코드에서는 이 방법을 사용하는 것을 볼 수 있기 때문에 알아두는 것이 좋다. 

HTML의 초기 버전은 요소(element)에 추가되어 이벤트에 대응할 수 있는 일련의 특성(attribute)을 포함하고 있다. 이 특성들은 이벤트와 동일한 이름을 사용한다. 

{% highlight html %}
<a onclick="hide()"></a>
{% endhighlight %}

HTML 이벤트 핸들러 특성(attribute)의 이름은 "이벤트 종류"에서 살펴본 이벤트의 이름과 동일하며, 앞에 **on**이라는 전치사를 덧붙여 사용한다.

### 2. 전통적인 DOM 이벤트 핸들러

DOM 이벤트 핸들러(event handler)는 DOM의 최초 명세에서 소개되었다. 이들은 HTML 이벤트 핸들러보다 나은 방법으로 평가된 이유는 HTML과 자바스크립트를 분리할 수 있었기 때문이었다. 모든 주요 브라우저에서 지원된다는 점은 이 방법의 가장 큰 장점이다. 

반면, 단점은 이벤트별로 단 하나의 함수만 바인딩 할 수 있다는 점이다. 이러한 제약 덕분에 같은 페이지에서 둘 이상의 스크립트를 사용하고 있고 이들 스크립트가 모두 같은 이벤트에 반응해야 한다면, 이 스크립트는 의도대로 동작하지 않을 수도 있다.

{% highlight js %}
// 이름이 지정된 함수를 정의
function checkUsername() {
	// code block
}

// DOM 요소 노드 선택
var elUserName = document.getElementById('username');

// 이벤트 핸들러를 바인딩
// DOM요소.이벤트핸들러 = 함수이름(괄호는 생략)
// 함수 이름 뒤에 괄호를 지정하면 자바스크립트 해석기는 "이 코드를 지금 실행하라"는 의미로 해석
elUserName.onblur = checkUsername;
{% endhighlight %}



### 3. DOM 레벨 2 **이벤트 리스너**

**이벤트 리스너(event listener)**는 DOM 명세의 개정판에서 소개되었다. 현재는 이 방법을 가장 많이 사용한다. 문법에는 상당한 차이가 있으며, 전통적인 이벤트 핸들러와 달리 이 새로운 이벤트 리스너들은 하나의 이벤트로 여러 개의 함수를 실행할 수 있다. 그 결과, 같은 페이지에서 동작하는 서로 다른 스크립트 간 충돌이 거의 없어졌다.

이 방법은 IE8(및 그 이전 버전)에서는 동작하지 않는다. 

{% highlight js %}
// 이름이 지정된 함수를 정의
function checkUsername() {
	// code block
}

// DOM 요소 노드 선택
var elUserName = document.getElementById('username');

// 이벤트 리스너를 이용하여 요소에 이벤트를 바인딩
// DOM요소.addEventListener('이벤트', 함수이름(괄호생략), boolean);
elUserName.addEventListener('blur', checkUsername, false);
{% endhighlight %}



### 매개변수를 가진 이벤트 핸들러와 리스너

이벤트 핸들러나 리스너를 지정할 때, 함수 이름 다음에 괄호를 사용할 수 없으므로 인수를 전달해야 하는 경우에 일종의 우회 방법이 필요하다. 이벤트 핸들러나 리스너가 호출하는 함수에 인수를 전달해야 한다면 해당 함수를 익명함수(anonymous function)로 감싸야 한다.

{% highlight js %}
el.addEventListner ('blur', function(){
  checkUsername(5);
}, false);
{% endhighlight %}



### 구 버전의 IE 지원하기

IE5부터 IE8까지의 브라우저는 addEventListner() 메서드를 지원하지 않는다. 그 대신 동일한 기능을 수행하지만 IE에서만 동작하는 attachEvent()라는 고유의 함수를 제공한다.

if else 구문을 이용하여 addEventListner() 메서드를 지원하는지를 알 수 있다. if 구문의 조건이 true를 리턴한다면 이 브라우저는 addEventListner() 메서드를 지원하는 것이다.

{% highlight js %}
if (el.addEventListener) {
	el.addEventListener('blur', function () {
	checkUsername(5);
	}, false);
} else {
  	// attachEvent 메서드를 사용할 때, 이벤트 이름 앞에 'on'을 덧붙임
	el.attachEvent('onblur', function(){
		checkUsername(5)
	});
}
{% endhighlight %}

IE8(혹은 그 이하) 버전을 지원해야 한다면, 모든 이벤트에 대한 우회코드를 작성하는 것보다는 적절한 이벤트 핸들러를 생성하는 함수를 직접 작성(이런 메서드들을 헬퍼메서드(helper method)라고 한다)하는 것이 좋다.

### 이벤트의 흐름

HTML 요소는 다른 요소의 내부에 중첩된다. 그래서 링크에 마우스를 가져가거나 클릭하면, 그 부모 요소에도 마우스를 가져가거나 클릭한 셈이 된다.

1. 이벤트 버블링(event bubbling) : 바깥쪽으로 전파되어 나가는 방식이다. 대부분의 브라우저가 기본적으로 지원하는 방식이다.
2. 이벤트 캡처링(event capturing) : 안쪽으로 전파되어 들어오는 방식이다. IE8 혹은 그 이전 버전에서는 지원되지 않는 방식이다.

전통적인 DOM 이벤트 핸들러(및 HTML 이벤트 핸들러)를 사용하는 경우, 현재의 모든 브라우저들은 기본적으로 이벤트 버블링을 사용한다. 이벤트 리스너를 사용하는 경우, 마지막 매개변수에 설정하는 값에 따라 발생한 이벤트의 전파 방향을 선택할 수 있다. 

- true : 발생한 이벤트가 캡처된다.
- false : 발생한 이벤트가 버블링된다. IE8및 이전 버전에서는 지원되지 않기 때문에 대부분 false를 기본값으로 사용한다.

## event 객체

event 객체는 이벤트가 발생했을 때 이벤트를 발생시킨 요소와 발생한 이벤트에 대한 정보를 제공한다. 이 객체가 제공하는 정보는 다음과 같다.

- 이벤트를 발생시킨 요소
- keypress 이벤트가 어떤 키에 의해 발생했는지에 대한 정보
- 사용자가 뷰포트 내의 어떤 요소를 클릭해서 click 이벤트가 발생했는지에 대한 정보

event 객체는 이벤트 핸들러나 리스너로 지정된 함수에 전달한다. 

- 속성
  - target : 이벤트가 발생한 요소
  - type : 발생한 이벤트 종류
  - cancelable : 요소의 기본 동작을 취소할 수 있는지 여부

- 메서드
  - preventDefault() : 취소가 가능할 경우 이벤트의 기본 동작을 취소한다.
  - stopPropagation() : 이벤트의 캡처링이나 버블링을 중단한다.

{% highlight js %}
  // 매개변수가 없는 이벤트 리스너
  function checkUsername(e) {	// event 객체를 전달받기 위해 매개변수
    var target = e.target;	// 이벤트가 발생한 요소
  }

  var el = document.getElementById('username');
  el.addEventListener('blur', checkUsername, false);
  /* 특별한 작업을 하지 않더라도 event 객체에 대한 참조는
  이벤트 핸들러가 함수를 호출할 때 자동으로 전달 */


  // 매개변수를 가진 이벤트 리스너
  function checkUsername(e, minLength) {	
    var target = e.target;	// 이벤트가 발생한 요소
  }

  var el = document.getElementById('username');
  // event 객체에 대한 참조는 익명함수에 자동으로 전달
  el.addEventListener('blur', function(e){
    checkUsername(e, 5);
  }, false);
{% endhighlight %}



## 이벤트 위임

이벤트 리스너를 지정하는 요소가 많은면 많을수록 페이지의 실행 속도는 느려진다. 이벤트는 이벤트를 포함하고 있는 부모 요소에도 영향을 미치기 때문에, 자식 요소를 포함할 수 있는 요소에 이벤트 핸들러를 지정하고 event 객체의 target 속성을 이용하여 정확히 어떤 요소에서 이벤트가 발생했는지를 판단하면 된다.

{% highlight js %}
// 4. 이벤트 발생 요소
function getTarget(e) {
	if (!e) {
		e = window.event;
	}
	return e.target || e.srcElement;
}

// 5. itemDone 함수 생성
function itemDone(e) {
	// 6. 변수 선언
	var target, elParent, elGrandparent;

	target = getTarget(e);
	elParent = target.parentNode;
	elGrandparent = target.parentNode.parentNode;

	// 7. <ul> 요소에서 <li> 요소 제거
	elGrandparent.removeChild(elParent);

	// 8. preventDefault() 메서드 지원 여부 확인
	if (e.preventDefault) {
		// 8-1. 지원할 경우
		e.preventDefault();
	} else {
		// 8-2. 지원하지 않을 경우 (for IE)
		e.returnValue = false;
	}
}


// 1. <ul> 요소
var elUl = document.getElementById('shoppingList');

// 2. addEventListener() 메서드 지원 여부 확인
if (addEventListener) {
	// 3-1. 지원할 경우
	elUl.addEventListener('click', function (e) {
		itemDone(e)	
	}, false);
} else {
	// 3-2. 지원하지 않을 경우 (for IE)
	elUl.attachEvent('onclick', function (e) {
		itemDone(e)
	});
}
{% endhighlight %}



## 이벤트가 발생한 지점

event 객체는 이벤트가 발생했을 때의 커서의 위치에 대한 정보를 제공한다.

- screenX, screenY : 모니터 화면 전체를 기준으로 커서의 위치를 알려준다.
- pageX, pageY : 전체 페이지를 기준으로 커서의 위치를 알려준다.
- clientX, clientY : 브라우저의 뷰포트 기준으로 커서의 위치를 알려준다.


> [6장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c06)


