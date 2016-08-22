---
layout: post
title: DOM
categories: javascript
tags: [js, dom]
---
HTML DOM 컴파일러와 함께, 자바스크립는 HTML 문서의 모든 element를 접근&변경 할 수 있다.

## HTML DOM (Document Object Model)
웹 페이지가 로딩될 때, 브라우저는 페이지의 **D**ocument **O**bject **M**odel을 만든다.

HTML DOM 모델은 **Objects** Tree를 만든다.

<pre>
Document
Root element: &lt;html>
+-- element: &lt;head>
|	+-- element: &lt;title>
|	|	+-- text: "Title"
+-- element: &lt;body>
|	+-- element: &lt;h1>
|	|	+-- text: "Header"	
|	+-- element: &lt;a>
|	|	+-- text: "Link"
|	+-- attribute: "href"
</pre>

object 모델과 함께, 자바스크립트는 동적인 HTML을 만들기 위해 필요한 모든 권한을 얻는다.

예를 들어 자바스크립트는 다음과 같은 작업을 할 수 있다.

- 페이지에 있는 모든 HTML elements 변경
- 페이지에 있는 모든 HTML attributes 변경
- 페이지에 있는 모든 CSS styles 변경
- 존재하는 HTML elements&attributes 제거
- 새로운 HTML elements&attributest 추가
- 페이지에 있는 모든 HTML events 반응하여 동작
- 페이지에 새로운 HTML events 추가

## 3 different part of DOM
1. Core DOM - standard model for all document types
2. XML DOM - standard model for XML documents
3. HTML DOM - standard model for HTML documents

## What is the HTML DOM?
HTML DOM은 HTML elements를 얻고, 변경하고, 추가하고, 삭제하는 방법을 위한 표준이다.

- The HTML elements as **objects**
- The **properties** of all HTML elements
- The **methods** to access all HTML elements
- The **events** for all HTML elements 

> reference : [w3schools.com](http://www.w3schools.com/js/js_htmldom.asp)
