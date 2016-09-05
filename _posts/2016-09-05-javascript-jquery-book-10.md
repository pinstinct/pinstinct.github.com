---
layout: post
title: 10. 오류 처리와 디버깅
categories: javascript&jquery
tags: [book, javascript, debug]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

## 실행

### 실행 컨텍스트(execution context)
스크립트의 모든 구문은 아래 세 가지 중 하나의 컨텍스트 내에 존재한다.

- 전역 컨텍스트(global context) : 스크립트 중 함수가 아닌 영역에 존재하는 코드는 이 컨텍스트에서 실행된다. 모든 페이지를 통틀어 전역 컨텍스트는 단 하나뿐이다. 
- 함수 컨텍스트(function context) : 함수 내에서 실행되는 코드들은 이 컨텍스트 내에 존재한다. 각 함수는 각자의 함수 컨텍스트를 보유하고 있다.
- 평가 컨텍스트(eval context) : 내부 함수인 eval() 함수에 의해 코드처럼 실행되는 텍스트는 이 컨텍스트 내에 존재한다.


### 스택
자바스크립트 해석기는 한 번에 한 줄의 코드를 실행한다. 어떤 구문이 다른 함수로부터 얻을 수 있는 데이터가 필요하면 새로운 함수를 스택의 위에 쌓는다.

### 스코프 이해하기
자바스크립트 해석기 내에서 각 실행 컨텍스트는 각자의 variables 객체를 갖는다. 이 객체는 변수, 함수, 사용 가능한 매개변수를 모두 그 안에 저장한다. 또한 각 실행 컨텍스트는 부모의 variables 객체에도 접근이 가능하다.

만일 현재 실행 컨텍스트의 variables 객체에서 변수를 찾지 못하면 부모 실행 컨텍스트의 variables 객체를 이용한다. 다만, 상위 객체의 스택에 대한 접근은 성능에 영향을 미치므로 변수는 사용할 함수 내에서 선언하는 것이 가장 이상적이다.


## 오류
자바스크립트 구문이 오류를 발생시키면 예외(exception)가 발생한다. 이 시점에서는 해석기의 실행이 중단되고 예외 처리 코드를 탐색한다.

만일 코드 중의 어딘가에서 오류가 발생할 것이라고 예상되면 오류를 처리(handle)할 수 있는 구문을 사용할 수 있다. 

예외가 발생하면 해석기는 실행을 중단하고 현재 실행 컨텍스트에서 예외를 처리하는 코드를 탐색한다. 함수에서 오류가 발생했지만 그 함수에 예외 처리 코드가 존재하지 않으면 해석기는 해당 함수를 호출한 코드로 이동한다. 여기서도 예외 처리 코드를 찾지 못하면 다음 수준으로 이동하여 해당 실행 컨텍스트에 예외 처리 코드가 있는지를 계속 확인한다. 전역 실행 컨텍스트까지 도달하면 스크립트의 실행은 종료되고 Error 객체를 생성한다.

### 자바스크립트 7가지 오류 객체
<table>
  <tr>
    <th>객체</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>Error</td>
    <td>일반적인 오류 - 다른 오류들은 모두 이 오류를 기반으로 한다.</td>
  </tr>
  <tr>
    <td>SyntaxError</td>
    <td>문법에 오류가 있을 경우에 발생한다.</td>
  </tr>
  <tr>
    <td>ReferenceError</td>
    <td>선언되지 않았거나 현재 스코프 안에 없는 변수를 참조하려 했을 때 발생한다.</td>
  </tr>
  <tr>
    <td>TypeError</td>
    <td>예상하지 못한 데이터 타입이 리턴되었으며 원하는 타입으로 변환이 불가능한 경우에 발생한다.</td>
  </tr>
  <tr>
    <td>RangeError</td>
    <td>숫자가 허용 범위를 벗어난 경우에 발생한다.</td>
  </tr>
  <tr>
    <td>URIError</td>
    <td>encodeURI(), decodeURI() 및 기타 유사한 함수를 잘못 사용했을 때 발생한다.</td>
  </tr>
  <tr>
    <td>EvalError</td>
    <td>eval() 함수를  잘못 사용했을 때 발생한다.</td>
  </tr>
</table>

### Error 객체 속성
<table>
  <tr>
    <th>속성</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>name</td>
    <td>실행 종류</td>
  </tr>
  <tr>
    <td>message</td>
    <td>오류에 대한 설명</td>
  </tr>
  <tr>
    <td>fileName</td>
    <td>자바스크립트 파일의 이름</td>
  </tr>
  <tr>
    <td>lineNumber</td>
    <td>오류가 발생한 코드의 줄 번호</td>
  </tr>
</table>

### 오류 처리법 
오류를 처리하는 방법은 크게 두 가지가 있다.

1. 디버깅 : 브라우저가 제공하는 개발자 도구 이용
2. 예외 처리 : try, catch, throw, finally 구문 이용 


#### 1. 디버깅

1. 오류 메시지를 통해 **발생지점** 파악
2. 문제가 발생한 부분에 **중단점(breakpoint)**을 설정
3. 그 주변의 변수들이 올바른 값을 가지고 있는지 확인

##### 콘솔 출력
콘솔을 가지고 있는 브라우저는 console 객체를 가지고 있다. 이 객체는 스크립트를 통해 콘솔에 데이터를 출력할 수 있는 몇 가지 메서드를 제공한다. 객체는 Console API에 문서화되어 있다. 

{% highlight js %}
console.group('계산기'); // 메시지 그룹화
    console.log('더하기') // 로그 출력
    console.info('빼기'); // 정보 출력
    console.warn('곱하기'); // 경고성 메시지 출력
    console.error('나누기'); // 오류를 출력 
console.groupEnd();

// 객체
// (배열 or 객체를 가진)배열 
// 표 형식으로 출력 
console.table(contacts);

// 조건 출력
// 해당 조건이 false일 때만 콘솔에 값을 출력
console.assert(this.value < 10);
{% endhighlight %}

##### 중단점
중단점을 이용하면 어느 곳에서든 스크립트의 실행을 중단할 수 있다. 이후 그 시점에 변수에 저장된 값을 확인할 수 있다.

##### 디버깅 팁

- 숫자 더하기 : 콘솔에 숫자를 출력하여 어떤 값들이 출력되는지 확인한다. 이 방법을 통해 코드가 중단되기 전에 스크립트가 얼마나 실행되는지 확인할 수 있다.
- 코드 플레이그라운드 : [JSbin](http://jsbin.com/)
- 유효성 도구 : [JShint](http://jshint.com), [JSlint](http://jslint.com)
- undefined는 null과 다르다. null은 객체를 위한 것이지만 undefined는 속성, 메서드, 변수를 위한 것이다.

#### 2. 예외 처리

{% highlight js %}
try{
    // 이 부분 코드를 실행
} catch {
    // 예외가 발생하면 이 부분 코드 실행
} finally {
    // 이 코드는 항상 실행
}

// 오류 전달
throw new Error('message');
{% endhighlight %}



