---
layout: post
title: Change hamburger button color
categories: jekyll
tags: [blog, code]
---

템플릿에 color themes를 제공해 아래와 같이 적용했다.

{% highlight css %}
<!-- default.html -->
<body class="theme-base-0a">
	...
</body>
{% endhighlight %}

그런데 햄버거 버튼이 기본색이랑 섞여 제대로 동작을 안한다. 이리저리 css 파일을 수정하며 테스트해 고쳤다. 

## lanyon.css 수정
lanyon.css 파일에서 sidebar-toggle class를 수정해주면 된다. #555를 찾아 #f4bf75로 모두 변경한다. (참고로 #555 sidebar-toggle class에만 있다.)
<pre>
.
+-- _includes
+-- _layouts
+-- _posts
+-- css
+-- public
|	+-- css
|	|	--- <span style="font-weight: bold;">lanyon.css</span>을
|	|	--- poole.css
|	|	--- syntax.css
|	--- favicon.ico
--- config.yml
--- index.html
--- categories.html
--- tags.html
</pre>


