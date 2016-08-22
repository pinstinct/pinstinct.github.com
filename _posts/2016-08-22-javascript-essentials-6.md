---
layout: post
title: Loop
categories: javascript
tags: [udemy, js, for, loop, array, object]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/javascript-essentials/">Javascript Essentials</a>강의를 듣고 정리한 것이다.</div>

유용한 예제가 나와서 남겨둔다. for문과 array를 이용해 object 값(value)을 출력하는 예제이다. 
{% highlight js %}
var obj = { 
	color: "red", 
	width:200, 
	height:300 
};

var array = Object.keys( obj );

for( var i = 0; i < array.length; i++ ){    
    console.log( obj[ array[i] ] );
}

// result >>>
// red
// 200
// 300
{% endhighlight %}