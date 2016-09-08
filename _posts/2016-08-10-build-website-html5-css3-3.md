--- 
layout: post
title: CSS 2
categories: css
tags: [udemy, css, id, class, margin, padding, grid]
--- 
<div class="message">Udemy에서 <a href="https://www.udemy.com/build-your-first-website-in-1-week/">Build Your First Website in 1 Week with HTML5 and CSS3</a> 강의를 듣고 정리한 것이다.</div>

## Box Model
- margin : outside of the border
- border
- padding : within the border
- (html element)

## Selectors

### 1. element selector
{% highlight css %}
p {
	text-align: center;
	color: red;
}
{% endhighlight %}

### 2. id selector
{% highlight css %}
#uniquemyid {
	text-align: center;
	color: red;
}
{% endhighlight %}

### 3. class selector
{% highlight css %}
.jumbotron {
	text-align: center;
	color: red;
}
{% endhighlight %}

## ID vs Class
- ID : **unique**, link to take a user to that section.
- IDs are more specific than classes so they will take priority.

## Grid System ([@bootstrap](http://getbootstrap.com/css/#grid-options))
> reference : [http://lim-web-staging.getforge.io/grid.html](http://lim-web-staging.getforge.io/grid.html)
 