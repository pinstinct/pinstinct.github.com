---
layout: post
title: 9. API
categories: javascript&jquery
tags: [book, javascript, api, google, mvc, angularjs]
---

<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

애플리케이션 프로그래밍 인터페이스(API: Application Programming Interface)는 프로그램들이 서로 대화할 수 있는 방법을 제공한다.

스크립트나 프로그램이 어떻게 기능을 구현하고 있는지에 대해 반드시 알아야 할 필요는 없다. 특정 작업의 수행을 요청하는 방법이나 응답을 처리하는 방법만 알면 될 뿐이다. 다른 스크립트나 프로그램에 요구할 수 있는 것과 그에 대한 응답 형식이 API를 구성하는 요소들이다.

## 지리위치 API

웹 페이지에서 지리 위치를 활용하려면 세 개의 객체를 사용해야 한다. 

- geolocation 객체
- Position 객체
- PositionError 객체

## 웹 저장소 API

웹 저장소(Web Stoarage, HTML 5 저장소라고도 불린다)는 브라우저 내에 데이터를 저장하기 위한 공간이다. 저장소는 크게 로컬(local) 저장소와 세션(session) 저장소의 두 종류가 제공된다.

HTML 5 이전에는 브라우저 내에 정보를 저장하기 위한 주요 매커니즘으로 쿠키(cookie)가 사용되었다. 그러나 쿠키는 다음과 같은 제약이 있다.

- 많은 양의 데이터를 저장할 수 없다.
- 동일한 도메인에 페이지를 요청할 때마다 서버로 함께 전송된다.
- 보안에 취약하다.

그래서 HTML 5에서는 저장소 **객체(storage object)**를 정의하고 있다. 저장소 객체는 localStorage 객체와 sessionStorage 객체의 두 종류가 제공된다. 이 두 객체는 모두 window 객체에 구현되어 있기 때문에 이름 앞에 뭔가를 덧붙일 필요가 없다. 둘 사이의 차이점은 데이터를 저장하는 기간과 모든 탭들이 저장된 데이터를 공유할 수 있는지에 있다.

<table>
  <tr>
    <th></th>
    <th>로컬</th>
    <th>세션</th>
  </tr>
  <tr>
    <td>창이나 탭을 닫아도 데이터가 보존되는가?</td>
    <td>O</td>
    <td>X</td>
  </tr>
  <tr>
    <td>현재 열려 있는 모든 창 또는 탭이 데이터를 롱유하는가?</td>
    <td>O</td>
    <td>X</td>
  </tr>
</table>


일반적으로 브라우저는 저장소 객체에 도메인당 5MB의 공간을 할당한다. 사이트가  5MB 이상의 데이터를 저장하려고 하면 브라우저가 사용자에게 더 많은 데이터를 저장하는 것을 허용할 것인지를 묻는다.

데이터는 저장소 객체의 속성으로 저장되며(키/값 쌍을 이용한다), 값은 항상 문자열로 표현된다.



## 히스토리 API

어떤 페이지로부터 다른 페이지로 이동하게 되면 브라우저의 히스토리는 사용자가 어떤 페이지를 방문했었는지를 기억한다. 그러나 Ajax 애플리케이션은 새로운 페이지를 로드하지 않기 때문에 위치 막대와 히스토리를 수정하기 위해 **history** API를 사용해야 한다.

**히스토리 스택(history stack)**이라고 불리는 저장소에 저장된다. history 객체는 window 객체의 자식 객체이므로 직접 사용할 수 있다.

- 히스토리 객체에 정보 추가 : pushState(), replaceState()
- 히스토리 객체에서 정보 조회 : 사용자가 이전 혹은 다음 버튼을 클릭했을 때 올바른 콘텐츠를 보여주기 위해 **사용자가 새로운 페이지를 요청할 때마다 onpopstate 이벤트**가 발생한다.



## API를 제공하는 스트립트

### Modernizr [(https://modernizr.com)](https://modernizr.com)

브라우저가 HTML, CSS, 자바스크립트 기능을 지원하는지를 판단하는 기능을 제공한다.

### jQuery UI [(https://jqueryui.com)](https://jqueryui.com)

### AngularJS [(https://angularjs.org)](https://angularjs.org)

AngularJS는 웹 앱(Web apps)을 편리하게 구현할 수 있는 프레임워크이다. 특히 서버의 데이터를 쓰고, 읽고, 수정하고, 삭제하는 앱을 구현하는 데 유용하다. Angular는 **모델-뷰-컨트롤러(MVC, Model-View-Controller)** 패턴이라고 불리는 소프트웨어 개발 접근법을 바탕으로 하고 있다. jQuery와 마찬가지로 페이지에 angular.js 파일을 추가하면 Angular가 제공하는 다양한 도구들을 사용할 수 있다.

- **뷰(View)**는 사용자가 보게 될 어떤 것을 의미한다. 사용자가 뷰 값을 변경하면 모델을 수정하기 위한 **명령( command)**이 전송된다.
- **컨트롤러(Controller)** 혹은 뷰 모델(View Model)은 뷰를 수정하는 역할을 담당한다. 또한, 뷰에서 변경이 발생하면 모델을 수정하기도 한다. 이 둘 사이 값의 변화를 동기화하는 과정을 데이터**바인딩(data binding)**이라고 한다.
- **모델(Model)**은 주로 데이터베이스에 저장되며, 모델에 접근하여 수정할 수 있는 서버 측 코드에 의해 관리된다. 모델이 수정되면 **변경 알림(change notification)**이 뷰 모델에 전달된다. 이 정보는 변경 사항을 적용하기 위해 다시 뷰에 전달된다.

### Google MAP [(https://developers.google.com/maps/documentation/javascript/?hl=ko)](https://developers.google.com/maps/documentation/javascript/?hl=ko)

>[9장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c09)