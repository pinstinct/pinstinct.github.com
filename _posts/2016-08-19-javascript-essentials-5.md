---
layout: post
title: Conditional operator
categories: javascript
tags: [udemy, js, operator, polymorphism]
---
<div class="message">Udemy에서 <a href="https://www.udemy.com/javascript-essentials/" target="_blank">Javascript Essentials</a>강의를 듣고 정리한 것이다.</div>


### Conditional operator
{% highlight js %}
/* 다형성(polymorphism, 폴리모피즘) : 프로그래밍 언어의 자료형 체계의 성질을 나타내는 것으로, 프로그램 언어의 각 요소들(상수, 변수, 식, 오브젝트, 함수, 메소드 등)이 다양한 자료형(type)에 속하는 것이 허가되는 성질을 가리킨다. 반댓말은 단형성(monomorphism)으로, 프로그램 언어의 각 요소가 한가지 형태만 가지는 성질을 가리킨다.
출처: wikipedia */
// 다형성 때문에 아래의 3개 연산 결과는 true

20 == "20"
// result >>> true

false == 0
// result >>> true

null == undefined
// result >>> true


/* 때문에 === 를 사용하는 것이 좋음 */

20 === "20"
// result >>> false

false === 0
// result >>> false

null === undefined
// result >>> false


/* ==, != 는 다형성을 허용 */

"answer" != "correct answer"
// result >>> true

"correct answer" != "correct answer"
// result >>> false

20 != "20"
// result >>> false

20 !== "20"
// result >>> true

20 !== 20
// result >>> false

{% endhighlight %}