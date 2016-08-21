---
layout: post
title: CSS 1
categories: css
tags: [udemy, css]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/build-your-first-website-in-1-week/" target="_blank">Build Your First Website in 1 Week with HTML5 and CSS3</a> 강의를 듣고 정리한 것이다.</div>

## Basic
CSS(Cascading Style Sheets) : a language that describes the style of an HTML document.

## 3 ways to include CSS

### 1. External style (like custom.css)
{% highlight css %}
/* custom.css */
h1 {
    color: red;
}
{% endhighlight %}

{% highlight html %}
<!-- html file -->
<head>
	<link rel="stylesheet" type="text/css" href="custom.css">
</head>
{% endhighlight %}

### 2. Internal style
defined within the **style** element, inside the **head** section of an HTML page.
{% highlight html %}
<head>
	<style>
	h1 {
	    color: red;
	} 
	</style>
</head>
{% endhighlight %}

### 3. Inline style
using the style attribute within the element’s opening tag.
{% highlight html %}
<h1 style="color: red;">Hello</h1>
{% endhighlight %}

### Order
Inline style has the highest priority.
External and Internal have the same priority. Cascading, read last will be the CSS rule that takes place.

1. Inline style
2. External and Internal style
3. Browser default


