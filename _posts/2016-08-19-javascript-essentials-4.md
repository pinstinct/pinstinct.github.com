---
layout: post
title: Constructors, Scoping
categories: javascript
tags: [udemy, js, object, Constructor, method, scoping]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/javascript-essentials/">Javascript Essentials</a>강의를 듣고 정리한 것이다.</div>

## Constructors (like blueprint)
Object를 다시 한번 정리해봤다. object를 생성하는 방법 중 constructor를 이용하는 방법이 있다. 

### 1. Containing **Variables** : *{ key : value }* 형태로 선언해야 한다.
{% highlight js %}
var person = { 
	name : "Lim", 
	age : 50 
};
{% endhighlight %}

### 2. Object **Methods** : object 안에 있는 function을 method라고 함

### 3. **Creating** a JavaScript Object : Object를 생성하는 방법은 3가지가 있다.
- using an object literal
{% highlight js %}
var person = {
	name : "Lim", 
	age : 50
};
{% endhighlight %}

- keyword new
{% highlight js %}
var person = object();
person.name = "Lim";
person.age = 50;
{% endhighlight %}

- using an Object Constructor : 위의 2가지 방법은 많이 사용되지 않는다. 일반적으로 object를 만들 때 사용하는 방법이다. 1가지 타입의 많은 object 만들 수 있다.
{% highlight js %}
function person (name, age) {
	this.name = name;
	this.age = age;

	// _this_ keyword = empty object
	// It is only a substitute for the new object.
}
var myFather = new person ("Jhon", 80);
var myMother = new person ("Sally", 77);
{% endhighlight %}

### Constructors 샘플 코드
{% highlight js %}
// constructors이름의 첫자는 대문자로 함
function CosturctApple() {

	// var test = 100 + 150;
	// var obj = { PROP:"property" }

	// console.log("function run");
	// console.log(test, obj, "string", test + 100 );	

	// this = window or { empty object }
	// console.log(this);

	this.color = "red";	// window.color = "red"
	this.width = 200;
	this.height = 300;

	console.log(this);
}

function CosturctSetApple (color, width, height) {
	this.color = color;
	this.width = width;
	this.height = height;

	console.log(this);
}

CosturctApple.prototype = {
	eat : function(){ return 'eat the apple'; },
	throw : function(){ return 'Throw it'; },
	nibble : function(){ return 'nibblel like a hamster'; }
}

var apple = new CosturctApple();
var apple2 = new CosturctApple();
var apple3 = new CosturctApple();

var apple4 = new CosturctSetApple("red", 100, 100);
var apple5 = new CosturctSetApple("green", 150, 150);
var apple6 = new CosturctSetApple("orange", 200, 200);
{% endhighlight %}

### Scoping
변수에 **접근(access)**할 수 있는 범위이다. 자바스크립트는 local variables, global variables 2가지가 있다. 자세한 사항은 [JavaScript Scope](http://www.w3schools.com/js/js_scope.asp)을 참고한다.
