---
layout: post
title: related posts 수정
categories: jekyll
tags: [blog, code]
---

예전 포스트에서 related posts가 노출되지 않는 문제를 발견했다. _site 폴더에서 변환된 html 파일을 보니 related posts 가져오는 부분이 다 빈값이었다. 처음엔 예전 포스트에 적용이 잘 안 됐나 싶어 _site 폴더를 삭제하고 다시 빌드해봤지만 여전히 안됐다. 그래서 YAML front matter가 잘못됐나 싶어 다시 써봤지만 마찬가지였다. 

결국, 코드를 살펴봤는데 for문 조건이 **site.related_posts**로 되어있었다. 조건을 전체 포스트인 **site.posts**로 바꾸니 모든 포스트에서 related posts가 정상적으로 보인다.

> [코드 보기](https://github.com/pinstinct/pinstinct.github.com/commit/843908721022fd417110fbbce22dccf18dc84f82?diff=unified)