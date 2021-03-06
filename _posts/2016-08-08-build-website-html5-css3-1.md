--- 
layout: post
title: HTML
categories: html
tags: [udemy, html, structure, div, span]
--- 
<div class="message">Udemy에서 <a href="https://www.udemy.com/build-your-first-website-in-1-week/">Build Your First Website in 1 Week with HTML5 and CSS3</a> 강의를 듣고 정리한 것이다.</div>


## Basic
HTML(Markup language) : It controls the structure of the site's code. 

## Structure
{% highlight html %}
<!DOCTYPE html>
<html>
	<!-- <head></head> section is background info,
	 include behind the scenes info -->
	<head>
		<title> My Blog </title>
	</head>
	<!-- <body></body> section is everything
	 you see on the screen -->
	<body>
		<h1> Hello World </h1>
		<!-- h1 : element
		<h1> : opening tag
		</h1> : closing tag -->
		<a href="http://google.com"> Go google </a>
		<!-- href : attributes,
		always defined name/value pair -->
	</body>
</html>
{% endhighlight %}

## Path
{% highlight html %}
<!-- folder directory -->
.first-site
+-- css
|	--- bootstrap.min.css
|	--- custom.css
+-- fonts
+-- js
--- index.html

<!-- index.html -->
<link rel="stylesheet" type="text/css" href="css/bootstrap.min.css">
<!-- href="
css : The browser to look for the "css" folder in the same directory
/ : Once "css" folder is found, go into it
bootstrap.min.css : Then find "bootstrap.min.css"
" -->

{% endhighlight %}

## DIV
element는 Block element와 Inline element가 있다.

1. Block element : start and end with a new line when displayed in browser. (examples : <div> <li>, <h1>, <p>)
2. Inline element : are displayed without starting a new line. (examples : &lt;img&gt;, &lt;a&gt;, &lt;strong&gt;)

**DIV** is used to **group block elements for styling purpose**. DIV also block element.

## Span
group inline elements in a document.
{% highlight html %}
<strong>strong <span style="color:red">red</span> text.</strong>
{% endhighlight %}
