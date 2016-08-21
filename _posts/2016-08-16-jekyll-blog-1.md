---
layout: post
title: Added Categories
categories: jekyll
tags: [blog, code]
---

## Template 선택
<a href="https://github.com/poole/lanyon" target="_blank">lanyon template</a>를 사용하기로 결정했으나, category 메뉴가 없었다.

## Categories 추가
가장 쉬운 방법을 찾아 헤맸다. jekyll **page layout**을 이용하는 방법이다.

### 1. Post 작성
Front Matter에 categories를 작성한다.

<pre>
// 1개의 카테고리를 작성할 경우
---
layout: post
title: Korea
categories: trip
---

// 2개 이상의 카테고리를 작성할 경우
---
layout: post
title: Seoul
categories: [trip, personal]
---
</pre>

## 2. categories.html
최상위 폴더 안에 categories.html을 추가한다.
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
|	|	--- <span style="font-weight: bold;">lanyon.css</span>을
|	|	--- poole.css
|	|	--- syntax.css
|	--- favicon.ico
--- config.yml
--- index.html
--- <span style="font-weight: bold;">categories.html</span>
</pre>

아래의 코드를 categories.html에 작성한다.
<script src="https://gist.github.com/pinstinct/d1fb2de8abc54b4a218ca0a201e49cd5.js"></script>


## 3. CSS 추가
category 페이지를 첫글자만 대문자로 출력하도록 lanyon.css 파일에 archive-group class를 작성한다.
{% highlight css %}
.archive-group {
  text-transform: capitalize;
}
{% endhighlight %}


<cite>reference : <a href="https://blog.webjeda.com/jekyll-categories/" target="_blank">https://blog.webjeda.com/jekyll-categories/</a></cite>
