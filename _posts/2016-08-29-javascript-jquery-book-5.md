---
layout: post
title: 5. 문서 객체 모델
categories: javascript&jquery
tags: [book, javascript, jquery, if, switch, loop, for, while]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

문서 객체 모델(DOM, Document Object Model)은 브라우저가 HTML 페이지의 모델을 생성하고 이 페이지가 브라우저 창에 보여지는 동안 자바스크립트를 이용하여 웹 페이지의 내용에 접근하고 수정하기 위한 방법을 명시하고 있다.

## DOM 트리

브라우저는 웹 페이지를 로드할 때 해당 페이지에 대한 모델을 생성한다. 이 모델을 DOM 트리라고 부르며, 이 모델은 브라우저의 메모리에 저장된다. 또한, 이 모델은 네 종류의 노드로 구성된다.

<img src="/image/js-dom-model.gif" alt="DOM Tree">


- 문서 노드(document node) : 트리의 최상위에는 문서노드가 존재하며, 이 노드는 전체 페이지를 표시한다. 요소, 특성, 텍스트 노드에 접근하려면 문서 노드를 통해야만 한다. 따라서 DOM 트리를 조회하기 위한 **시작점 역할**을 한다.
- 요소 노드(element node) : **DOM 트리에 접근**하려면 일단 요소를 탐색해야 한다. 원하는 요소를 찾은 후에야 비로소 필요에 따라 그 요소의 텍스트와 특성 노드에 접근하여 값을 변경할 수 있다.
- 특성 노드(attribute node) : HTML element의 여는 태그에 특성을 지정할 수 있으며, 이런 특성들은 DOM 트리 내에서 특성 노드로 표현된다.
- 텍스트 노드(text node) : 텍스트 노드는 자식노드를 가질 수 없다. 텍스트 노드는 DOM 트리 내에서 항상 새로운 노드로 표현되며, 그 하위에는 더 이상의 노드가 존재하지 않는다.

각각의 노드는 메서드와 속성을 가진 객체이다. 스크립트는 DOM 트리에 접근하여 그 내용을 수정한다. DOM 트리에 가해진 모든 변경 사항은 브라우저에 반영된다.


## DOM 트리 활용

요소(elements)와 요소 노드(element nodes)라는 단어는 동의어로 사용되기도 하지만, 사람들이 DOM을 통해 요소를 조작한다고 말할 때는 실제로 요소를 표현하는 노드(element node)를 이용한다는 뜻이다.

### 1. 요소 접근

#### DOM 쿼리(DOM Query)

DOM 쿼리는 하나의 요소를 리턴할 수도 있지만, 노드의 모음(collection)인 NodeList를 리턴할 수도 있다.

- 하나의 요소 노드를 리턴하는 메서드 : getElementById('id'), querySelector('css selector')
- 노드의 모음인 NodeList를 리턴하는 메서드 : getElementsByClassName('class name'), getElementsByTagName('tag name'), querySelectorAll('css selector')

NodeList 객체에서 요소를 선택하는 방법은 두 가지가 있다. 배열 문법은 item() 메서드보다 빠르게 동작하기 때문에 더 보편적으로 사용되는 방법이다. 또한, NodeList 객체에서 노드를 선택하기에 앞서 발견된 노드가 존재하는지를 먼저 확인하는 것이 좋다.

- item() 메서드
- 배열의 요소 참조 문법

{% highlight js %}
var elements = document.getElementsByClassName('hot');
if (elements.length >= 1) {
  	// item() 메서드
	var firstItem = elements.item(0);
  
	// 배열의 요소 참조 문법
	var firstItem = elements[0];
}
{% endhighlight %}


#### DOM 탐색(DOM Traversing)

페이지에 요소 노드가 존재한다면 다음 5가지의 속성을 이용하여 다른 요소들을 선택할 수 있다. 이런 과정을 DOM 탐색이라고 한다.

- parentNode : 현재 요소 노드의 부모 노드를 찾는다.
- previousSibling, nextSibling : 현재 요소 노드의 이전 혹은 다음 노드를 찾는다.
- firstChild, lastChild : 현재 요소의 첫 번째 혹은 마지막 자식 요소를 찾는다.

이 속성들을 이용할 때, 탐색한 요소가 없을 경우에는 **null**이 리턴된다. 또한, 이 속성들은 읽기 전용 속성이다.

DOM을 탐색하다 보면 간혹 어려움을 겪게 되는 이유 중 하나는 일부 브라우저가 요소 사이사이의 공백을 텍스트 노드로 추가하기 때문이다. 이 문제를 다루는 가장 대중적인 방법은 이런 문제를 알아서 해결해주는 jQuery같은 자바스크립트 라이브러리를 사용하는 것이다.



### 2. 요소 조작

#### 텍스트 조작

- nodeValue : **텍스트 노드**를 선택하면 nodeValue 속성을 이용하여 그 내용을 조회하거나 변경할 수 있다.
- textContent : 자식 요소를 포함한 상위 요소(및 그 자식 요소)의 텍스트를 가져오거나 변경할 수 있다. 마크업은 무시한채 텍스트만 리턴한다.
- (innerText : 사용을 권장하지 않는다.)

#### HTML 콘텐츠 조작

DOM 트리에 콘텐츠를 추가하거나 제거하는 방법은 크게 두 가지가 있다. 하나는 innerHTML 속성을 이용하는 방법이고, 다른 하나는 DOM을 조작하는 방법이다.

- innerHTML의 속성 
  - 요소의 자식 노드를 포함한 모든 콘텐츠에 접근 및 수정이 가능하다.
  - 요소의 모든 컨텐츠를 하나의 긴 문자열로 가져올 수 있다. 이 문자열에는 해당 요소가 가지고 있는 모든 마크업이 포함되어 있다.
  - 보안에 취약하다.

- DOM 조작
  - DOM 트리 내의 개별 노드를 쉽게 조작할 수 있는 방식이다.
  - DOM 조작으로 요소 추가
    1. 요소 생성 : createElement( )
    2. 콘텐츠 지정 : createTextNode( )
    3. 생성된 요소를 DOM에 추가 : appendChild( )
  - DOM 조작으로 요소 제거
    1. 제거할 요소를 변수에 저장
    2. 해당 요소의 부모 요소를 변수에 저장
    3. 부모 요소로부터 요소를 제거 : removeChild( )


<table>
  <tr>
    <th>element.innerHTML</th>
    <th>DOM 조작 방식</th>
  </tr>
  <tr>
    <td>innerHTML 속성을 이요아여 특정 요소의 전체 콘텐츠(마크업을 포함하여)를 문자열로 가져오거나 수정할 수 있다.</td>
    <td>DOM 조작 방식은 요소와 텍스트 노드에 접근하거나 생성 혹은 수정하기 위해 일련의 메서드와 속성을 이용하는 방식이다.</td>
  </tr>
  <tr>
    <td>장점<br>- DOM 조작 방식에 비하면 더 적은 양의 코드로 더 많은 양의 새로운 마크업을 추가할 수 있다.<br>- 웹 페이지에 많은 양의 새 요소들을 추가해야 할 때 DOM 조작 방식에 비해 더 빠르게 동작한다.<br>- 비교적 간편하게 하나의 요소에서 콘텐츠를 모두 제거할 수 있다(빈 문자열을 대입하면 된다).</td>
    <td>장점<br>- 이웃한 요소가 많은 DOM 내에서 특정 요소를 변경하고자 할 때 적합하다.<br>- 이벤트 핸들러에 아무런 영향을 미치지 않는다.<br>- 스크립트를 통해 점진적으로 요소를 추가하기가 쉽다.</td>
  </tr>
  <tr>
    <td>단점<br>- 사용자로 전달받은 콘텐츠를 추가할 때 사용해서는 안된다. 매우 중요한 보안 이슈 때문이다.<br>- DOM의 일부로부터 수정하고자 하는 하나의 요소를 구분하기가 어렵다.<br>- 이벤트 핸들러가 의도한 대로 동작하지 않는다.</td>
    <td>단점<br>- 대량의 콘텐츠를 변경해야 할 때 innerHTML 속성보다 느리게 동작한다.<br>- innerHTML 속성을 이용하는 방법과 비교할 때 동일한 작업을 수행하기 위해 더 많은 코드를 작성해야 한다.</td>
  </tr>
</table>


### 3. 특성 노드

요소 노드를 생성했으면 해당 요소의 특성 노드에 접근 또는 수정을 위해 다른 속성과 메서드를 사용할 수 있다.

- hasAttribute( )
- getAttribute( )
- setAttribute( )
- removeAttribute( )
- className
- id

## DOM 쿼리 캐싱

DOM 트리에서 요소를 찾아내는 메서드들을 DOM 쿼리라고 한다. 어떤 요소를 한 번 이상 조작할 필요가 있다면 요소를 조회한 결과를 저장하기 위한 변수를 사용한다. 이렇게 하면 브라우저가 같은 요소를 탐색하기 위해 DOM 트리를 여러 번 조회하는 것을 방지할 수 있다. 이런 방법을 캐싱(caching)이라고 한다.

변수에 요소를 저장할 때 **요소의 위치**를 변수에 저장하는 것이다.


> [5장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c05)

