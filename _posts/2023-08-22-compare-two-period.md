---
layout: post
title: 두개의 기간이 겹치는지 체크하는 로직
categories: experience
tags: [java, project]
---

### 문제 상황

기존에 등록된 배너들과 새로운 배너들의 기간이 겹치지 않도록 추가/수정하고 싶다는 요구사항이 발생했다. 

위의 요구사항을 해결하기 위해 기간 A와 기간 B가 주어졌을 때, 겹치는지 확인하는 로직이 필요했다.

### 두 기간 비교 방법 

기간 B가 기간 A보다 앞서는 경우 (startA > endB)
- |--- B ---|   |--- A ---|

기간 B가 기간 A보다 뒤에 있는 경우 (endA < startB)
- |--- A ---|   |--- B ---|

위의 두 경우를 제외한 모든 경우가 겹치는 기간이다. 

여기에 드모르간 법칙을 이용하면,
- not (A or B) <-> not A and not B

다음과 같이 겹치는 기간을 표현할 수 있다.
- startA <= endB and EndA >= startB

> [Determine Whether Two Date Ranges Overlap](https://stackoverflow.com/questions/325933/determine-whether-two-date-ranges-overlap/325964#325964)


### 해결 방법

위의 원리에 따라 자바의 LocalDateTime을 사용해 함수를 작성했다.

```java
public boolean isOverlap(LocalDateTime startA, LocalDateTime endA, LocalDateTime startB, LocalDateTime endB) {
  return ((startA.isBefore(endB) || startA.isEqual(endB))) && ((startB.isBefore(endA)) || (startB.isEqual(endA)));
}
```
