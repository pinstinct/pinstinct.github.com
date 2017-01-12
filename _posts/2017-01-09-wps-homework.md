---
layout: post
title: Day1 - HW
categories: wps
tags: [fastcampus, emmet, html]
---

# Emmet 문서 정리

[Emmet Abbreviations Syntax](http://docs.emmet.io/abbreviations/syntax/)를 정리한 문서이다.

## Elements
div, p와 같이 **요소의 이름**을 이용해 HTML 태그를 생성한다. 예를 들어 `div`라고 입력하면 `<div></div>`와 같이 HTML 태그로 변환한다.

## Nesting operators

### 1. Child: />

`di>ul>li`

```html
<div>
	<ul>
		<li></li>
	</ul>
</div>
```

### 2. Sibling: +

`div+p+bq`

```html
<div></div>
<p></p>
<blockquote></blockquote>
```

### 3. Climb-up: ^

`div+div>p>span+em^bq`

```html
<div></div>
<div>
    <p><span></span><em></em></p>
    <blockquote></blockquote>
</div>
```

`div+div>p>span+em^^^bq`

```html
<div></div>
<div>
    <p><span></span><em></em></p>
</div>
<blockquote></blockquote>
```

### 4. Multiplication: *

`ul>li*5`

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

### 5. Grouping: ()

`div>(header>ul>li*2>a)+footer>p`

```html
<div>
    <header>
        <ul>
            <li><a href=""></a></li>
            <li><a href=""></a></li>
        </ul>
    </header>
    <footer>
        <p></p>
    </footer>
</div>
```

## Attribute operators

### 1. ID and CLASS
**elem#id**로 표시해 element의 id 특성 값을 지정하고, **elem.class**로 표시해 element의 class 특성 값을 지정한다.

`div#header+div.page+div#footer.class1.class2.class3`

```html
<div id="header"></div>
<div class="page"></div>
<div id="footer" class="class1 class2 class3"></div>
```

### 2. Custom attributes
**[attr]**로 표시해 element의 custom 속성을 추가한다.

`td[title="Hello world!" colspan=3]`

```html
<td title="Hello world!" colspan="3"></td>
```

* 대괄호 안에 원하는 만큼 속성을 넣을 수 있다.
* 속성의 값을 지정할 필요가 없다. `td[colspan title]`은 `<td colspan="" title="">`을 생성한다.
* 속성 값은 작음 따옴표 혹은 큰 따옴표를 사용할 수 있다.
* 속성 값에 공백이 없다면, 따옴표를 사용하지 않아도 된다. `td[title=hello colspan=3]`

### 3. Item numbering: $

`ul>li.item$*5`

```html
<ul>
    <li class="item1"></li>
    <li class="item2"></li>
    <li class="item3"></li>
    <li class="item4"></li>
    <li class="item5"></li>
</ul>
```

`ul>li.item$$$*5`

```html
<ul>
    <li class="item001"></li>
    <li class="item002"></li>
    <li class="item003"></li>
    <li class="item004"></li>
    <li class="item005"></li>
</ul>
```

#### Changing numbering base and direction
**@** modifier로 오름차순, 내림차순 그리고 시작 값을 바꿀 수 있다.

`ul>li.item$@-*5`

```html
<ul>
    <li class="item5"></li>
    <li class="item4"></li>
    <li class="item3"></li>
    <li class="item2"></li>
    <li class="item1"></li>
</ul>
```

`ul>li.item$@3*5`

```html
<ul>
    <li class="item3"></li>
    <li class="item4"></li>
    <li class="item5"></li>
    <li class="item6"></li>
    <li class="item7"></li>
</ul>
```

`ul>li.item$@-3*5`

```html
<ul>
    <li class="item7"></li>
    <li class="item6"></li>
    <li class="item5"></li>
    <li class="item4"></li>
    <li class="item3"></li>
</ul>
```

## Text: {}

`a{Click me}`

```html
<a href="">Click me</a>
```

주의사항 : `{text}`는 **div, p**와 같이 separate element에서 사용하지만, element 바로 오른쪽 옆에서 사용하면 특별한 의미를 가진다.

```html
<!-- a{click}+b{here} -->
<a href="">click</a><b>here</b>

<!-- a>{click}+b{here} -->
<a href="">click<b>here</b></a>
```

```html
<!-- p>{Click }+a{here}+{ to continue} -->
<p>Click <a href="">here</a> to continue</p>
```

```html
<!-- p{Click }+a{here}+{ to continue} -->
<p>Click </p>
<a href="">here</a> to continue
```

## Notes on abbreviation formatting
`(header > ul.nav > li*5) + footer`와 같이 읽기 쉽게 작성한다면, 동작하지 않는다.  Emmet에서 공백은 *stop symbol*이기 때문이다.



>[cheat-sheet](http://docs.emmet.io/cheat-sheet/)
