---
layout: post
title: 12. 폼 확장
categories: javascript&jquery
tags: [book, javascript, form, helper, html5]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

폼(Form)은 사용자로부터 정보를 모을 수 있으며, 자바스크립트를 이용하여 사용자들이 올바른 정보를 입력하도록 유도할 수 있다.

### 헬퍼 함수 (Helper function)

순수한 자바스크립트를 사용하며 jQuery는 사용하지 않는다. 크로스 브라우저 문제를 해결하기 위한 자바스크립트 파일을 작성한다. 이처럼 재사용 가능한 함수들을 헬퍼함수라고도 한다.

{% highlight js %}
/*
크로스 브라우저를 지원하는 "이벤트 핸들러 생성" 함수 
el : 이벤트를 추가하거나 제거할 DOM 노드를 표현하는 요소를 지정
event : 핸들러를 등록하고자 하는 이벤트를 지정
callback : 요소에서 지정한 이벤트가 발생했을 때 실행될 함수 지정 
*/
function addEvent(el, event, callback) {
    if ('addEventListener' in el) {
        el.addEventListener(event, callback, false);
    } else {
        el['e' + event + callback] = callback;  // (1)callback 함수를 el 요소의 메서드로 만든다.
        el[event + callback] = function () {
            el['e' + event + callback](window.event);   // (1)에서 작성한 메서드에 event 객체를 전달한다.
        };
        el.attachEvent('on' + event, el[event + callback]);
    }
}
{% endhighlight %}



## 폼 확장

폼 컨트롤을 표현하는 DOM 노드는 지금까지 살펴본 다른 요소들과는 달리 전혀 다른 속성과 메서드, 이벤트를 제공한다.

### 폼 요소

- action : 폼을 전송할 URL을 지정한다.
- method : 폼을 전송할 방식을 GET 또는 POST 중 하나로 지정한다.
- name : 거의 사용하지 않는다. id 특성 값을 이용해 폼을 선택하는 방법이 더 일반적으로 사용된다.
- elements : 사용자가 상호작용할 수 있는 폼 내 요소들의 집합이다. 인덱스 번호나 요소의 name 특성 값을 이용해 각 요소에 접근할 수 있다. 

### 폼 메서드

- submit() : 폼의 전송 버튼을 클릭하는 것과 마찬가지 역할을 수행한다.
- reset() : 폼을 페이지가 처음 로드되었을 때의 초기 상태로 되돌린다.

### 폼 이벤트

- submit : 폼을 전송할 때 발생한다.
- reset : 폼이 재설정될 때 발생한다.

### 폼 컨트롤 속성

- value : 텍스트 입력 상자에서 사용자가 입력한 텍스트를 리턴한다. (그 외의 컨트롤에서는 value 특성 값을 리턴한다.)
- type : &lt;input> 요소를 이용하여 폼 컨트롤을 생성할 때 폼 요소의 종류를 정의한다. (text, password, radio, checkbox 등)
- name : name 특성 값을 리턴하거나 지정한다.
- defaultValue : 텍스트 상자나 여러 줄 텍스트 상자가 페이지에 출력될 때의 기본값을 리턴하거나 지정한다.
- form : 현재 폼 컨트롤이 속한 폼을 리턴한다.
- disabled : 폼 요소의 비활성화 여부를 리턴하거나 혹은 비활성화한다.
- checked : 체크박스나 라디오 버튼이 체크되어 있는지를 리턴한다.
- defaultChecked : 페이지가 로드될 때 체크박스나 라디오 버튼이 체크되어 있어야 하는지를 지정한다.
- selected : 선택 상자의 아이템이 선택되어 있는지를 리턴한다.

### 폼 컨트롤 메서드

- focus() : 요소에 포커스를 지정한다.
- blur() : 요소에서 포커스를 제거한다.
- select() : 요소의 텍스트 콘텐츠를 선택하고 하이라이트한다.
- click() : 버튼, 체크박스, 파일 업로드 컨트롤에서 click 이벤트를 발생시킨다.

### 폼 컨트롤 이벤트 

- blur : 사용자가 폼 요소에서 다른 폼 요소로 이동할 때 발생한다.
- focus : 사용자가 폼 요소로 이동할 때 발생한다.
- click : 사용자가 요소를 클릭할 때 발생한다.
- change : 요소의 값이 변경될 때 발생한다.
- input : &lt;input>또는 &lt;textarea> 요소의 값이 변경될 때 발생한다.
- keydown, keyup, keypress : 사용자가 어떤 요소에서 키보드를 이용할 때 발생한다.



## 선택상자

&lt;select> 요소는 다른 폼 컨트롤에 비해 조금 복잡하다. 이 컨트롤을 표현하는 DOM 노드는 더 많은 속성과 메서드를 제공한다. 사용자가 선택할 수 있는 값은 &lt;option> 요소가 가지고 있다.

### 선택상자 속성

- options : 모든 &lt;option> 요소의 컬렉션을 리턴한다.
- selectedIndex : 현재 선택되어 있는 옵션의 인덱스 번호를 리턴한다.
- length : 전체 옵션의 개수를 리턴한다.
- multiple : 사용자가 선택 상자에서 여러 개의 옵션을 선택할 수 있는지 여부를 리턴하거나 지정한다. (거의 사용되지 않음)
- selectedOptions : 선택된 &lt;option> 요소의 컬렉션을 리턴한다.

### 선택상자 메서드

- add(option, before) : 목록에 아이템을 추가한다. 첫 번째 매개변수는 추가할 옵션이며, 두 번째 매개변수는 새로운 아이템이 어느 아이템 앞에 추가되어야 하는지를 지정한다. 두 번째 매개변수에 값을 지정하지 않으면 새 아이템은 목록의 가장 마지막에 추가된다.
-  remove(index) : 목록에서 아이템을 제거한다. 제거할 아이템의 인덱스 번호를 지정하는 하나의 매개변수를 사용한다.



## HTML5 폼 요소

HTML5는 지금까지 자바스크립트를 통해 구현했던 기능들을 실행하는 **폼 요소와 특성**들을 제공한다.

- autofocus : 페이지가 로드되면 해당 요소에 포커스를 자동으로 지정한다.
- placeholder : &lt;input> 입력 상자에 특성 앖으로 지정한 문자열이 힌트처럼 보인다.
- required : 해당 요소가 값을 가지고 있는지 검사한다.
- min :  입력 가능한 숫자의 최소값
- max : 입력 가능한 숫자의 최대값
- step : 숫자를 증가 혹은 감소할 때의 증가치/감소치
- value : 페이지가 로드될 때 컨트롤에 나타날 기본값
- autocomplete : 기본적으로 활성화 된다. 예전에 입력했던 값의 목록을 보여준다.
- pattern : 값의 유효성 검사를 위한 정규 표현식(regular expression)을 지정한다.
- novalidate : &lt;form> 요소가 HTML5 내장 유효성 검사를 사용하지 않도록 한다.

사용자의 브라우저들이 이런 새로운 기능을 제공하기 전까지 개발자들은 같은 기능을 일관되게 구현하기 위해 새로운 기능 사용을 조심스럽게 생각해야 한다.

- 대체 방안 : 구 버전 브라우저들은 새로운 HTML5 요소를 지원하지 않기 때문에 polyfil을 이용하여 동일한 경험/기능을 사용할 수 있다.  다양한 기능에 대한 대체 기능들은 [http://html5please.com/](http://html5please.com/)에서 찾아볼 수 있다.
- 기능 탐지 : 기능 탐지(feature detection)는 브라우저가 특정 기능을 지원하는지 여부를 확인하는 것이다. Modernizr는 대체 기능 스크립트가 필요치 않은 브라우저를 위해 **조건식 로드(conditional loader)**를 제공한다. 이 로더는 스크립트가 필요로 하는 기능이 지원되지 않는 경우에만 대체 스크립트를 로드하도록 한다.

> [12장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c13)
