---
layout: post
title: Javascript 3
categories: javascript
---
## Prototype, Constructors, Scoping, Conditional operator
Udemy에서 <a href="https://www.udemy.com/javascript-essentials/" target="_blank">Javascript Essentials</a>강의를 듣고 정리한 것.


### Prototype
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


### Constructors (like blueprint)
Object를 다시 한번 정리해봤다. object를 생성하는 방법 중 constructor를 이용하는 방법이 있다. 

#### 1. Containing **Variables** : *{ key : value }* 형태로 선언해야 한다.
{% highlight js %}
var person = { 
	name : "Lim", 
	age : 50 
};
{% endhighlight %}

#### 2. Object **Methods** : object 안에 있는 function을 method라고 함

#### 3. **Creating** a JavaScript Object : Object를 생성하는 방법은 3가지가 있다.
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
변수에 **접근(access)**할 수 있는 범위이다. 자바스크립트는 local variables, global variables 2가지가 있다. 자세한 사항은 <a href="http://www.w3schools.com/js/js_scope.asp" target="_blank">JavaScript Scope</a>을 참고한다.

### Conditional operator
{% highlight js %}
/* 다형성(polymorphism, 폴리모피즘) : 프로그래밍 언어의 자료형 체계의 성질을 나타내는 것으로, 프로그램 언어의 각 요소들(상수, 변수, 식, 오브젝트, 함수, 메소드 등)이 다양한 자료형(type)에 속하는 것이 허가되는 성질을 가리킨다. 반댓말은 단형성(monomorphism)으로, 프로그램 언어의 각 요소가 한가지 형태만 가지는 성질을 가리킨다.
출처: wikipedia */
// 다형성 때문에 아래의 3개 연산 결과는 true

20 == "20"
// result >>> true

false == 0
// result >>> true

null == undefined
// result >>> true


/* 때문에 === 를 사용하는 것이 좋음 */

20 === "20"
// result >>> false

false === 0
// result >>> false

null === undefined
// result >>> false


/* ==, != 는 다형성을 허용 */

"answer" != "correct answer"
// result >>> true

"correct answer" != "correct answer"
// result >>> false

20 != "20"
// result >>> false

20 !== "20"
// result >>> true

20 !== 20
// result >>> false

{% endhighlight %}