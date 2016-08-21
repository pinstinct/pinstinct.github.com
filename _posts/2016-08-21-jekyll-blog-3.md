---
layout: post
title: Show related posts by category
categories: jekyll
tags: [blog, code]
---
포스트 하단에 Related Posts가 최근 포스트가 출력된다. 같은 카테고리의 포스트가 출력되도록 변경했다.

## post.html 수정 

<pre>
.
+-- _includes
|	--- head.html
|	--- sidebar.html
+-- _layouts
|	--- default.html
|	--- page.html
|	--- <span style="font-weight: bold;">post.html</span>
+-- _posts
+-- css
--- config.yml
--- index.html
--- categories.html
--- tags.html
</pre>

아래의 코드를 post.html 하단에 작성한다.
<script src="https://gist.github.com/pinstinct/7f303fbdca4abd926112bacbd9313e5b.js"></script>

<cite>reference : <a href="http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/" target="_blank">zhangwenli blog</a></cite>