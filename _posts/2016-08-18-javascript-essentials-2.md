---
layout: post
title: Object, Array, Function
categories: javascript
tags: [udemy, js, object, function, array, method]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/javascript-essentials/" target="_blank">Javascript Essentials</a>강의를 듣고 정리한 것이다.</div>

## Object 특징
1. properties : describe the object (like Nouns)
2. functions : performing action (like Verbs)
3. hierarchy : grouping data, objects within objects (like Encapsulation)

{% highlight js %}
myMac : 
{
// properties
width : 1400,
height : 600,
color : "space gray",
keyboardSize: "13inch",

// functions
typeText : function(){ ... },
smash : function(){ ... },

// hierarchy
camera : {
          quality : "HD",
          record : function(){ ... },  
        }
screen : {
          type : "retina",
          size : "13inch",
          backlight : "LED",
        }
keyboard : {
          quality : "en-UK",
          functional : true,
          typeIn : function(){ ... },   
        }
}
{% endhighlight %}

## array
{% highlight js %}
// create
var myArray = [
  value1,
  value2,
  value3,
  ...
];

// access
myArray[0];
{% endhighlight %}

## object
{% highlight js %}
// create
var myObject = {
  key1 : value1,
  key2 : value2,
  key3 : value3,
  ...
};

// access
myObject.key1;
myObject[key2];
{% endhighlight %}

## function
{% highlight js %}
// argument : temporary storage
// function myFunction (argument) {
//   execution context
// }

// create
function sayHello () {
  return "Hello"
}
function verb (a, b) {
  return a + b;
} 
var obj = {
  // object 안에 있는 fuction은 method라고 함
  myMethod : function (a, b) {
    return a + b;
  }
};

// access
sayHello();
verb(5, 10);  //argument에 넣은값은 parameter라고 함
obj.myMethod(10, 15);
{% endhighlight %}