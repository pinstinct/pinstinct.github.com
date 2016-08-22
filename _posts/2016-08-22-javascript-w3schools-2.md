---
layout: post
title: DOM methods
categories: javascript
tags: [js, dom, method, property]
---

## The DOM Programming Interface

자바스크립트(or 다른 프로그래밍 언어)로 HTML DOM에 접근할 수 있다. 

DOM에서 모든 HTML elements는 **objects**로 정의되어 있다.

프로그램 인터페이스는 각 object의 **properties**와 **methods**이다.

- property : 설정하거나 변경할 수 있는 HTML Elements의 **value** (like HTML element의 내용(content)을 변경하는 것)
- method : HTML Elements에서 수행할 수 있는 **action** (like HTML element를 추가하거나 삭제하는 것)

{% highlight html %}
<!-- id="demo"인 <p> element의 내용을 변경하는 예 -->
<!DOCTYPE html>
<html>
	<head>
		<title>DOM Tutorial</title>
	</head>
	<body>
		<p id="demo"></p>
	</body>
	<script>
	// getElementById is a method, 
 	// innerHTML is a property.
	document.getElementById("demo").innerHTML = "Hello world"
	</script>
</html>

{% endhighlight %}