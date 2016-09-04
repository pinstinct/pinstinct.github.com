---
layout: post
title: 8. Ajax와 JSON
categories: javascript&jquery
tags: [book, javascript, jquery, ajax, json]
---
<div class="message"><a href="http://www.aladin.co.kr/shop/wproduct.aspx?ItemId=55027282">
자바스크립트 & 제이쿼리 - 인터랙티브 프론트엔드 웹 개발 교과서</a>로 공부한 내용을 정리한 것이다.</div>

Ajax는 전체 페이지를 새로 고치지 않고도 페이지의 일부만을 위한 데이터를 로드하는 기법이다. 데이터는 주로 JSON(JavaScript Object Notation, 자바스크립트 객체 식)이라고 부르는 형태로 전달된다.

## Ajax란?

라이브 검색(혹은 자동 완성), 쇼핑몰 장바구니, 회원 가입 할 때 등 미처 인지하지 못했더라도 이미 Ajax를 경험했을 것이다. 웹사이트는 Ajax를 이용하여 사용자가 모르는 사이에 데이터를 로드하여 필요할 때 사용하기도 한다.

Ajax는 **비동기 처리 모델**을 사용한다. Ajax를 이용하면 브라우저는 서버에 데이터를 요청한다. **그리고 일단 데이터를 요청했으면** 페이지의 나머지를 계속해서 로드하고 페이지와 사용자의 상호작용을 처리한다. 이런 방식을 **비동기 처리 모델(asynchronous processing model)** 혹은 **논블로킹(non-blocking) 처리 모델**이라고 한다. 

데이터가 로드되는 동안 사용자는 계속해서 페이지를 사용할 수 있다. 그러다가 서버가 데이터를 전달해주면 Ajax 이벤트가 발생하여 다른 스크립트가 서버로부터 전달된 새로운 데이터를 읽고 페이지의 일부를 수정하게 된다.

(본래 Ajax는 비동기 요청을 보내는데 필요한 기술, 즉 Asynchronous Javascript And XML의 약자였으나 이후 브라우저 내에서 비동기 기능을 제공하는 모든 기법을 통칭하게 되었다.)

## Ajax 동작방식

1. 요청 : 브라우저는 서버에 정보를 요청한다. 이 요청은 서버가 필요로 하는 정보(like form)를 포함하고 있을 수도 있다.
2. (서버의 동작) : 서버는 응답으로 데이터를 전달한다. 서버에서 일어나는 일들은 Ajax 처리 과정에 포함되지 않는다.
3. 응답 : 브라우저는 콘텐츠를 처리하여 페이지에 추가한다.

브라우저는 XMLHttpRequest 객체를 이용하여 Ajax 요청을 생성한다. 서버가 브라우저의 요청에 대해 응답을 전달하면 같은 XMLHttpRequest 객체가 그 결과를 처리한다.

### 1. 요청 

{% highlight js %}
var xhr = new XMLHttpRequest();
// XMLHttpRequest 객체의 open() 메서드는 세 개의 매개 변수를 정의하고 있다.
// open(HTTP 메서드, 요청을 처리할 페이지의 URL, 요청이 비동기로 처리될 것인지 지정)
xhr.open('GET', 'data/test.json', true);
// send() 메서드는 준비된 요청을 전달하는 메서드
// 매게 변수(옵션) : 괄호 내에 서버에 전달될 추가 정보를 지정
xhr.send('search=arduino');
{% endhighlight %}



### 2. 응답

{% highlight js %}
// 브라우저가 서버로부터 응답을 받으면 onload 이벤트 발생
// 이벤트가 발생하면 함수 호출
xhr.onload = function () {
    if (xhr.status === 200) {
        // code block
    }
};
{% endhighlight %}



## 데이터

Ajax 요청의 응답은 주로 HTML, XML, JSON 중 한 가지 형식으로 전달된다.

### 데이터 타입

- HTML 
  - 장점 : 코드의 작성 및 요청을 처리하고 응답을 표시하기가 간편하다.
  - 단점 : **데이터 이식성(data portability)**이 좋다고 할 수 없다. 또한 요청이 반드시 같은 도메인으로부터 이루어져야 한다.
- XML : HTML과 비슷하지만 태그의 이름이 다르다.
  - 장점 : 복잡한 구조의 데이터도 표현이 가능하다.
  - 단점 : 실제 데이터 외에도 추가로 많은 문자들이 사용되기 때문에 더 많은 양의 대역폭을 소비한다. 또한 HTML과 마찬가지로 요청이 반드시 도메인으로부터 이루어져야 한다.
- JSON : 객체 표현식과 유사한 문법을 사용하여 데이터를 표현한다.
  - 장점 : 다른 도메인에서도 요청을 보낼 수 있다. HTML/XML보다는 간결하다.
  - 단점 : 문법에 예민하다. 또한,  XSS 위험이 있다.
  - JSON.stringigy() : 자바스크립트 객체를 JSON 형식의 문자열로 변환
  - JSON.parse() : JSON 데이터를 자바스크립트 객체로 변환

### 데이터 로드

서버가 HTML, XML, JSON 중 무엇을 리턴하든 Ajax 요청을 설정하고 파일이 동작할 준비가 되었는지 확인하는 과정은 동일하다. 바뀌는 것은 리턴된 데이터를 다루는 부분뿐이다.

서버가 요청에 응답을 할 때는 반드시 상태 메시지를 전달하여 요청이 제대로 처리되었는지를 알려야 한다. 상태 메시지는 다음과 같다.

<table>
  <tr>
    <td>200</td>
    <td>서버가 응답을 완료했으며 아무런 문제가 없다.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>응답 내용이 이전 응답 내용과 동일하다.</td>
  </tr>
  <tr>
    <td>404</td>
    <td>페이지를 찾을 수 없다.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>서버 내부에서 오류가 발생했다.</td>
  </tr>
</table>

#### 1. HTML
HTML은 Ajax를 이용하여 페이지에 추가하기가 가장 손쉬운 데이터 타입이다. 브라우저는 다른 HTML을 처리할 때와 마찬가지 방법으로 이를 처리한다.

{% highlight js %}
// 1. XMLHttpRequest 객체 생성
var xhr = new XMLHttpRequest();

// 4. 서버로부터 응답을 받으면 객체의 onload 이벤트 발생 
xhr.onload = function () {
if (xhr.status === 200) {
    document.getElementById('content').innerHTML = xhr.responseText;
}
};

// 2. 요청 준비
xhr.open('GET', '../data/data.html', true);
// 3. 요청 전송
xhr.send(null);
{% endhighlight %}

#### 2. XML
XML 데이터를 요청하는 것은 HTML 데이터를 요청하는 것과 매우 유사하다. 그러나 리턴된 데이터를 처리하는 것이 상대적으로 더 복잡한데, 그 이유는 XML을 페이지에 보여질 수 있는 HTML로 변환해야 하기 때문이다.

{% highlight js %}
 // 1. XMLHttpRequest 객체 생성
 var xhr = new XMLHttpRequest();

 // 4. 서버로부터 응답을 받으면 객체의 onload 이벤트 발생 
 xhr.onload = function () {
    if (xhr.status === 200) {
        // 4. XML 데이터 처리
        var response = xhr.responseXML();   // 서버에서 XML 데이터 리턴 
        var events = response.getElementsByTagName('event');

        for (var i = 0; i < events.length; i++) {
            // XML 데이터를 변환 
    }
 };

 // 2. 요청 준비
 xhr.open('GET', '../data/data.html', true);
 // 3. 요청 전송
 xhr.send(null);
{% endhighlight %}


#### 3. JSON
문자열을 자바스크립트 객체로 변환하는 것을 **역직렬화(deserializing)**라고 한다. 역직렬화는 JSON이라는 내장객체의 parse() 메서드를 이용하면 된다. 이 객체는 전역 객체이므로 인스턴스를 만들 필요없이 사용하면 된다.

{% highlight js %}
 // 1. XMLHttpRequest 객체 생성
 var xhr = new XMLHttpRequest();

 // 4. 서버로부터 응답을 받으면 객체의 onload 이벤트 발생 
 xhr.onload = function () {
    if (xhr.status === 200) {
        // 4. JSON 데이터 처리
        responseObject = JSON.parse(xhr.responseText);

        // 5. JSON 데이터로 새로운 콘텐츠 생성 
        var newContent = '';
        for (var i = 0; i < responseObject.events.length; i++) { // Loop through object
          newContent += '<div class="event">';
          newContent += '<img src="' + responseObject.events[i].map + '" ';
          newContent += 'alt="' + responseObject.events[i].location + '" />';
          newContent += '<p><b>' + responseObject.events[i].location + '</b><br>';
          newContent += responseObject.events[i].date + '</p>';
          newContent += '</div>';
        }
        document.getElementById('content').innerHTML = newContent;
 };

 // 2. 요청 준비
 xhr.open('GET', '../data/data.html', true);
 // 3. 요청 전송
 xhr.send(null);
{% endhighlight %}

Ajax는 보안상의 이유로 다른 도멘으로부터의 Ajax 응답은 로드할 수 없다. (이를 크로스 도메인 요청이라고 한다.) 이 경우 다음과 같은 세 가지 우회 방법이 있다.

1. 웹 서버의 프록시 파일 : 내 서버에 원격 서버로부터 데이터를 수집하는 파일을 생성(서버측 언어 이용)하는 것이다. 이후 사이트에서 이 파일에 데이터를 요청한다(그러면 이 파일이 원격 서버에서 데이터를 가져와 리턴한다). 이를 **프록시(proxy)**라고 하는데, 그 이유는 다른 페이지의 대행자처럼 동작하기 때문이다.
2. JSONP (JSON with Padding) : JSON 데이터를 로드하는 &lt;script> 요소를 다른 서버로부터 페이지에 추가하는 방법이다.
3. 크로스 오리진 리소스 공유 : 브라우저는 서버와 통신할 때마다 서로에 대한 정보를 HTTP 헤더를 이용해 전달한다. **크로스 오리진 리소스 공유(CORS: Cross-Origin Resource Sharing)**는 HTTP 헤더에 추가적으로 정보를 추가하여 브라우저와 서버가 서로 통신을 해야 한다는 사실을 알게 한다. 하지만 최신 브라우저에서만 동작한다.

{% highlight html %}
<!-- data-jsonp.html -->
<html>
<head></head>
  <body>
    <!-- code block -->
    <!-- 데이터를 -->
    <script src="js/data-jsonp.js"></script>
    <!-- 원격 서버로부터 JSON 데이터 요청 -->
    <!-- 간혹 데이터를 리턴할 때 이를 처리할 함수의 이름을 지정할 수도 있다. -->
    <!-- 함수의 이름은 주로 URL 쿼리 문자열로 전달 -->
    <script src="http://htmlandcssbook.com/js/jsonp.js?callback=showEvents"></script>
  </body>
</html>
{% endhighlight %}


{% highlight js %}
// data-jsonp.js
function showEvents(data) {                           // Callback when JSON loads
  var newContent = '';                                // Variable to hold HTML
 
    // BUILD UP STRING WITH NEW CONTENT (could also use DOM manipulation)
    for (var i = 0; i < data.events.length; i++) {    // Loop through object
      newContent += '<div class="event">';
      newContent += '<img src="' + data.events[i].map + '" ';
      newContent += 'alt="' + data.events[i].location + '" />';
      newContent += '<p><b>' + data.events[i].location + '</b><br>';
      newContent += data.events[i].date + '</p>';
      newContent += '</div>';
    }

    // Update the page with the new content
    document.getElementById('content').innerHTML = newContent;
}
{% endhighlight %}



## jQuery & Ajax

### 요청

- .load() : 요소에 HTML 코드를 로드한다.
- $.get() :  HTTP GET 메서드를 이용하여 데이터를 로드한다.
- $.post() : HTTP POST 메서드를 이용하여 데이터를 로드한다.
- $.getJSON() : GET 요청을 이용하여 JSON 데이터를 로드한다. 서버가 JSON 데이터를 리턴할 때 사용한다.
- $.getScript() : GET 요청을 이용하여 스크립트를 로드하고 실행한다. 자바스크립트 데이터(JSONP)를 로드할 때 사용한다.
- $.ajax() : 모든 요청을 수행할 수 있다.

### 응답 

load() 메서드를 이용하면 서버로부터 전달받은 HTML은 jQuery 객체집합에 적용된다. 다른 메서드의 경우에는 jqXHR 객체를 통해 리턴된 데이터를 어떻게 처리할 것인지를 직접 정의해야 한다.

- jqXHR 속성
  - responseText : 서버로부터 리턴된 텍스트 데이터를 가져온다.
  - responseXML : 서버로부터 리턴된 XML 데이터를 가져온다.
  - status : 상태코드를 가져온다.
  - statusText : 상태 설명 문자열을 가져온다.

- jqXHR 메서드
  - .done() : 요청이 성공적으로 처리되었을 때 실행될 코드를 지정한다.
  - .fail() : 요청이 실패했을 때 실행될 코드를 지정한다.
  - .always() : 요청의 성공/실패 여부와 관계없이 항상 실행될 코드를 지정한다.
  - .abort() : 서버와 커뮤니케이션을 취소한다.


{% highlight js %}
$('nav a').on('click', function (e) {
    e.preventDefault();
    var url = this.href;

    $('nav a.current').removeClass('current');
    $(this).addClass('current');

    $('#container').remove();
    // jQUery객체.load('페이지URL 셀릭터')
    $('#container').load(url, ' #content').hide().fadeIn('slow');
});
{% endhighlight %}


### 약식 메서드

다른 jQuery 메서들과는 달리 객체집합을 대상으로 실행되지 않는다. jQuery 객체 집합 대신 $ 기호만을 사용하는 이유는 바로 이 때문이다. 이 메서드들은 주로 페이지가 로드되거나 사용자와 페이지의 상호작용(예를 들면, 링크를 클릭하거나 폼을 전송하는 등)으로 인해 이벤트가 발생했을 때 호출한다.

- $.get(url, data, callback, type) : HTTP GET 요청으로 데이터를 보낸다.
- $.post(url, data, callback, type) : HTTP POST 방식을 이용하여 서버의 데이터를 수정한다.
- $.getJSON(url, data, callback) : GET 요청을 이용하여 JSON 데이터를 로드한다.
- $.getScript(url, callback) : GET 요청을 이용하여 자바스크립트를 로드하고 실행한다.


<table>
  <tr>
    <td>url</td>
    <td>데이터를 가져올 경로를 지정</td>
  </tr>
  <tr>
    <td>data</td>
    <td>서버로 전송될 추가 정보를 제공</td>
  </tr>
  <tr>
    <td>callback</td>
    <td>서버로부터 데이터가 전달되었을 때 호출될 콜백을 지정</td>
  </tr>
  <tr>
    <td>type</td>
    <td>클라이언트가 원하는 서버의 응답 형식을 지정</td>
  </tr>
</table>

{% highlight js %}
$('#selector a').on('click', function (e) {
    e.preventDefault();
    var queryString = 'vote=' + $(e.target).attr('id');
    $.get('votes.php', queryString, function (data) {
        $('#selector').html(data);
    })
})
{% endhighlight %}



### Ajax 폼 전송

- .post() : 주로 폼 데이터를 서버로 **전송**하는 용도로 사용한다.
- .serialize() : 폼 데이터를 수집한다.

{% highlight js %}
$('#register').on('submit', function (e) {
    e.preventDefaulte();
    // 폼 데이터를 수집
    var details = $('#register').serialize();
    $.post('register.php', details, function (data) {
        $('#register').html(data);
    });
});
{% endhighlight %}



### JSON과 오류

$.getJSON() 메서드를 이용하면 JSON 데이터를 로드할 수 있다. 또한, 서버가 요청에 대한 처리에 실패한 경우에 대한 응답을 처리할 수 있는 메서드들도 제공한다.

- JSON 로드 : 같은 서버로부터 JSON 데이터를 조회할 경우에는 $.getJson() 메서드를 사용한다. JSONP의 경우에는 $.getScript() 메서드를 사용한다.
- 성공/실패
  - .done() : 요청이 성공적으로 처리되었을 때 발생하는 이벤트 메서드
  - .fail() : 요청이 실패했을 때 발생하는 이벤트 메서드
  - .always() : 요청에 대한 처리가 성공/실패 여부와 관계없이 완료되었을 때 발생하는 이벤트 메서드

{% highlight js %}
$('#exchangerates').append('<div id="rates"></div><div id="reload"></div>');

function loadRates() {
  $.getJSON('data/rates.json')
  // 요청이 성공한 경우
  .done( function(data){                                
    var d = new Date();                                  
    var hrs = d.getHours();                              
    var mins = d.getMinutes();                           
    var msg = '<h2>Exchange Rates</h2>';                 
    $.each(data, function(key, val) {                    
      msg += '<div class="' + key + '">' + key + ': ' + val + '</div>';
    });
    msg += '<br>Last update: ' + hrs + ':' + mins + '<br>'; 
    $('#rates').html(msg);      
    // 요청이 실패한 경우
  }).fail( function() {                                 
    $('#rates').text('Sorry, we cannot load rates.');   
    // 항상 실행
  }).always( function() {                               
     var reload = '<a id="refresh" href="#">';           
     reload += '<img src="img/refresh.png" alt="refresh" /></a>';
     $('#reload').html(reload);                          
     $('#refresh').on('click', function(e) {             
       e.preventDefault();                               
       loadRates();                                      
     });
  }); 
}

loadRates();   
{% endhighlight %}



### 요청 제어

$.ajax() 메서드를 이용하면 Ajax 요청을 완벽하게 제어할 수 있다. 이 메서드는 30개 이상의 설정을 통해 Ajax 요청을 보다 상세하게 제어할 수 있는 기능을 제공한다. 이 설정들은 객체 표현식을 통해 제공하면 된다.

- type : 요청을 HTTP GET 또는 POST 방식으로 전송하기 위해 GET 또는 POST라는 값을 지정한다.
- url : 요청을 보낼 페이지의 경로를 지정한다.
- data : 요청과 함께 서버로 전달될 데이터를 지정한다.
- success : Ajax 요청이 성공적으로 처리되었을 때 호출할 함수를 지정한다. 
- error : Ajax 요청의 처리가 실패했을 때 호출될 함수를 지정한다.
- beforeSend : Ajax 요청을 전달하기 전에 실행될 함수를 지정한다.
- complete : 성공/실패 이벤트를 처리한 다음에 실행된다.
- timeout : 요청을 실패한 것으로 처리하기까지 기다릴 시간을 밀리초로 지정한다.

{% highlight js %}
$('nav a').on('click', function(e) {
  e.preventDefault();
  var url = this.href;                                 
  var $content = $('#content');                        

  $('nav a.current').removeClass('current');           
  $(this).addClass('current');
  $('#container').remove();                            

  // Ajax 요청 상세 제어
  $.ajax({
    type: "GET",    
    url: url,       
    timeout: 2000,  
    // Ajax 요청을 전달하기 전에 실행될 함수
    beforeSend: function() {               
      $content.append('<div id="load">Loading</div>');
    },
    // 성공, 실패 이벤트를 처리한 다음에 실행
    complete: function() {                                  
      $('#load').remove();                                  
    },
    // 요청이 성공적으로 처리되었을 경우
    success: function(data) {   
      $content.html( $(data).find('#container') ).hide().fadeIn(400);
    },
    // 요청의 처리가 실패했을 경우
    error: function() {                                     
      $content.html('<div class="container">Please try again soon.</div>');
    }
  });
});
{% endhighlight %}

>[8장 최종 예제](https://github.com/pinstinct/front-end-web-js-jquery/tree/master/c08)

>[최종 예제 결과](http://devlim.woobi.co.kr/c08/example.html)
