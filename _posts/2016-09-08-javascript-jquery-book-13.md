---
layout: post
title: 13. 유효성 검사 
categories: javascript&jquery
tags: [book, javascript, form, validate]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

유효성 검사는 사용자가 입력한 값이 특정 조건을 만족하는지를 검사하는 과정이다. 이후 사용자에게 입력한 값이 어떻게 잘못되었는지 알려주고 이를 수정하여 다시 전송하도록 안내할 수 있다. 

## 기본 유효성 검사

폼의 모든 요소에 대해 루프를 실행하면서 두 종류의 기본 유효성을 검사한다.

- 요소의 required 특성을 가지고 있는가? 만일 그렇다면 값이 입력되어 있는가?
- 입력된 값이 type 특성에 적합한가? 예를 들면, email 입력 창에 메일 주소가 입력되어 있는가?

## 모든 요소 검색

폼의 elements 컬렉션(폼 내의 모든 요소에 대한 참조를 저장하는 속성)을 이용해 폼 내의 모든 요소를 검사한다. 예제의 경우 elements 컬렉션은 다음과 같은 폼 컨트롤들을 저장하고 있다.

<table>
  <tr>
    <th>인덱스</th>
    <th>요소</th>
    <th>필수 여부</th>
  </tr>
  <tr>
    <td>0</td>
    <td>elements.name</td>
    <td>필수</td>
  </tr>
  <tr>
    <td>1</td>
    <td>elements.email</td>
    <td>필수</td>
  </tr>
  <tr>
    <td>2</td>
    <td>elements.password</td>
    <td>필수</td>
  </tr>
  <tr>
    <td>3</td>
    <td>elements.conf-password</td>
    <td>필수</td>
  </tr>
  <tr>
    <td>4</td>
    <td>elements.birthday</td>
    <td>필수</td>
  </tr>
  <tr>
    <td>5</td>
    <td>elements.parent-consent</td>
    <td>13세 미만이면 필수</td>
  </tr>
  <tr>
    <td>6</td>
    <td>elements.bio</td>
    <td>선택</td>
  </tr>
</table>


## 추가 유효성 검사

기본 유효성 검사를 마치고 나면 폼의 개별 요소에 대한 검사를 다시 수행한다. 

- 비밀번호가 일치하는가?
- 자기소개가 140자를 넘지 않는가?
- 사용자가 13세 미만인 경우 부모 동의 체크박스가 체크되어 있는가?

## 유효한 요소 추적

오류를 추적하기 위해 valid라는 이름의 객체를 하나 생성한다. valid 객체에 각 요소를 속성으로 저장한다.

- 요소의 id 특성 값을 속성의 이름으로 사용한다.
- 속성의 값은 불리언이다. 요소에서 오류가 발견될 때마다 이 속성 값에 false를 설정한다.

<table>
  <tr>
    <th>valid 객체의 속성들</th>
  </tr>
  <tr>
    <td>valid.name</td>
  </tr>
  <tr>
    <td>valid.email</td>
  </tr>
  <tr>
    <td>valid.password</td>
  </tr>
  <tr>
    <td>valid.conf-password</td>
  </tr>
  <tr>
    <td>valid.birthday</td>
  </tr>
  <tr>
    <td>valid.parents-consent</td>
  </tr>
  <tr>
    <td>valid.bio</td>
  </tr>
</table>


## 오류 처리

오류가 발견되면 스크립트는 폼의 전송을 취소하고 사용자에게 무엇을 해야 할지를 알려준다. 스크립트는 각 요소를 검사하면서 오류가 발견되면 아래의 두 가지 작업을 수행한다.

- valid 객체의 해당 속성 값을 수정하여 콘텐츠가 유효하지 않음을 표시한다.
- setErrorMessage() 함수를 호출한다. 이 함수는 jQuery의 .data() 메서드를 이용하여 요소에 필요한 데이터를 저장한다. 따라서 오류 메시지는 문제가 발생한 요소와 함께 메모리에 저장된다.

## 폼 전송

폼을 전송하기에 앞서, 스크립트는 폼에 오류가 없는지 확인한다. 만일 폼에 오류가 있으면 스크립트는 폼의 전송을 취소한다. 폼에 입력된 값에 오류가 있는지 검사하기 위해 isFormValid라는 변수를 선언하고 그 값을 true로 설정한다. 이후 valid 객체의 각 속성에 대해 루프를 실행하면서 오류가 있는지(즉, 객체의 속성 중 값이 false인 속성이 있는지) 검사한다. 이후 폼에 오류가 있으면 isFormValid 변수 값을 false로 설정한다. 따라서 isFormValid 변수는 일종의 **플래그(flag)**처럼 사용한다.

##정규 표현식

정규 표현식은 지정된 패턴의 문자를 찾는다. 또한 이 문자들은 다른 문자로 교체할 수도 있다. 아래 기호들은 정규 표현식에서 사용 가능한 빌딩 블록(building block)들이다. 

<table>
  <tr>
    <td> . </td>
    <td>모든 단일 문자 (새 줄 문자 제외)</td>
  </tr>
  <tr>
    <td>[ ]</td>
    <td>괄호 내에 나열된 문자 중 하나</td>
  </tr>
  <tr>
    <td>[^ ]</td>
    <td>괄호 내에 나열되지 않은 문자 중 하나</td>
  </tr>
  <tr>
    <td>^</td>
    <td>줄의 시작 위치</td>
  </tr>
  <tr>
    <td>$</td>
    <td>줄의 끝 위치</td>
  </tr>
  <tr>
    <td>( )</td>
    <td>하위 표현식(블록 또는 캡쳐링 그룹이라고도 부른다)</td>
  </tr>
  <tr>
    <td>*</td>
    <td>이전 요소가 0개 또는 그 이상 반복됨</td>
  </tr>
  <tr>
    <td>\n</td>
    <td>n번째 하위 표현식</td>
  </tr>
  <tr>
    <td>{m,n}</td>
    <td>이전 요소가 최소 m번, 최대 n번까지 반복됨</td>
  </tr>
  <tr>
    <td>/d</td>
    <td>숫자</td>
  </tr>
  <tr>
    <td>\D</td>
    <td>숫자가 아닌 모든 문자</td>
  </tr>
  <tr>
    <td>/s</td>
    <td>공백 문자</td>
  </tr>
  <tr>
    <td>/S</td>
    <td>공백 문자가 아닌 모든 문자</td>
  </tr>
  <tr>
    <td>/w</td>
    <td>알파벳 문자 및 숫자(A~Z, a~z, 0~9)</td>
  </tr>
  <tr>
    <td>/W</td>
    <td>알파벳 문자 및 숫자가 아닌 모든 문자(_는 제외)</td>
  </tr>
</table>


### 일반적인 정규 표현식
<table>
  <tr>
    <td>/^\d+$</td>
    <td></td>>숫자 검색</td>
  </tr>
  <tr>
    <td>^[ \s]+</td>
    <td>공백으로 시작하는 줄</td>
  </tr>
  <tr>
    <td>/[^@]+@[^@]+/</td>
    <td>메일 주소</td>
  </tr>
  <tr>
    <td>/^#[a-fA-F0-9]{6}$/</td>
    <td>16진수 색상 코드</td>
  </tr>
  <tr>
    <td>/^(/d{2}\/\d{2}\/\d{4})|(\d{4}-\d{2}-\d{2})$/</td>
    <td>yy-mm-dd 형식의 날짜</td>
  </tr>
</table>

## 코드 의존성과 재사용
어떤 프로젝트든 같은 작업을 수행하는 코드를 두 번 작성하는 것은 피해야 할 일이다. 또한, 프로젝트 간에 코드를 재사용할 수 있도록 해야 한다. 그럴 경우 코드에 **의존성(dependency)**이 생기게 된다.

### 의존성 
다른 스크립트에 의존하는 스크립트를 작성할 때 해당 스크립트에 의존성이 있다고 표현한다. 이런 경우 주석을 통해 의존성을 명시하여 다른 사람이 분명히 알 수 있도록 하는 것이 좋다.

### 코드 재사용과 코드 중복
만일 같은 작업을 수행하는 코드를 두번 작성했다면 이를 코드 중복(code duplication)이라고 한다. 대체로 코드 중복은 기피해야 할 현상이다. 코드 중복의 반대 개념인 코드 재사용(code reuse)은 같은 코드가 스크립트의 다른 곳에서 한 번 이상 사용되는 것을 말한다. 

> [13장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c13_validation)
