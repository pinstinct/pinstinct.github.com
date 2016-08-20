---
layout: post
title: Prototype
categories: javascript
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/javascript-essentials/" target="_blank">Javascript Essentials</a>강의를 듣고 정리한 것이다.</div>

## Prototype
Every JavaScript object has a prototype. The prototype is also an object.

{% highlight js %}
var apple = { 
	color : "red", 
	width : 200, 
	height : 350
};

var test = {
};

// create prototype object property
apple.__proto__.hello = "world";

// access prototype object
apple.hello;
// result >>> "world"

test.hello;
// result >>> "world"


// method 이름이 같을 경우
// object에서 생성한 method (or properties)가 
// prototype object의 method (or properties)보다 우선순이가 높음
var apple2 = { 
	color : "green", 
	width : 250,
	height : 450
};
var apple3 = { 
	color : "red", 
	width : 100, 
	height : 150
};

apple.hello;
// result >>> "world"
apple2.hello
// result >>> "world"
apple3.hello;
// result >>> "world"

// Added _hello_ property 
var apple = { 
	color : "red", 
	width : 200, 
	height : 350, 
	// Create
	hello : "appleHello"
};

// apple object 구조
// Object {color: "red", width: 200, height: 350, hello: "appleHello"}
// 	color: "red"
// 	height: 350
// 	hello: "appleHello"
// 	width: 200
// 	__proto__: Object


apple.hello;
// result >>> "appleHello"

apple2.hello;
// result >>> "world"
{% endhighlight %}
