---
layout: post
title: Added tags
categories: jekyll
tags: [blog, code]
---

Categories와 동일한 방법으로 tags 기능을 추가했다.

## 1. Post 작성
Front Matter에 tags를 작성한다.

<pre>
// 1개의 태그를 작성할 경우
---
layout: post
title: Korea
categories: trip
tags: weekend
---

// 2개 이상의 태그를 작성할 경우
---
layout: post
title: Seoul
categories: [trip, personal]
tags: [weekend, river]
---
</pre>

## 2. tags.html
최상위 폴더 안에 tags.html을 추가한다.
<pre>
.
+-- _includes
|	--- head.html
|	--- sidebar.html
+-- _layouts
|	--- default.html
|	--- page.html
|	--- post.html
+-- _posts
+-- css
+-- public
|	+-- css
|	|	--- <span style="font-weight: bold;">lanyon.css</span>
|	|	--- poole.css
|	|	--- syntax.css
|	--- favicon.ico
--- config.yml
--- index.html
--- categories.html
--- <span style="font-weight: bold;">tags.html</span>
</pre>

아래의 코드를 tags.html에 작성한다.
<script src="https://gist.github.com/pinstinct/da98c532cf5119bcd83b2c5643f547f8.js"></script>

## 3. CSS 추가
tag를 소문자로 출력하도록 lanyon.css 파일에 tag-group class를 작성한다.
{% highlight css %}
.tag-group {
  text-transform: lowercase;
}
{% endhighlight %}
