---
layout: post
title: 리스트, 튜플, 딕셔너리, 셋
categories: python
tags: [python]
---



# Chapter3 파이 채우기: 리스트, 튜플, 딕셔너리, 셋

이번 장에서 다룰 자료구조(data structure)는 분자(molecule)다. 이 말은 기본 타입들이 복잡한 형태로 결합된다는 것을 의미한다.

- booleans (True or False)
- integers
- floats
- strings



## 3.1 리스트와 튜플

이전 장에서 파이썬의 **문자열**은 문자의 시퀀스라는 것을 배웠다. 이번 장에서 리스트는 모든 것의 시퀀스라는 것을 알게 될 것이다.

파이썬에는 두 가지 다른 시퀀스 구조가 있다. **튜플(tuple)**과 **리스트(list)**다. 문자열과는 달리, 이들 항목은 다른 타입이 될 수 있다. 다시 말해 각 요소는 **어떤** 객체도 될 수 있다.

파이썬은 왜 리스트와 튜플 모두를 포함하고 있을까? 튜플은 **불변(immutable)**한다. 튜플에 항목을 할당하고 나서, 이를 바꿀 수 없다. 리스트는 **변경 가능(mutable)**하다. 항목을 할당하고, 자유롭게 수정하거나 삭제할 수 있다.



## 3.2 리스트

리스트는 데이터를 순차적으로 파악하는 데 유용한다. 특히 내용의 순서가 바뀔 수 있다는 점에서 유용하다. 문자열과 달리 리스트는 변경 가능하다.



### 3.2.1 리스트 생성하기: `[]` 또는 `list()`

리스트는 0 혹은 그 이상의 요소로 만들어진다. 콤마(`,`)로 구분하고, 대괄호(`[]`)로 둘러싸여 있다.

```python
>>> empty_list = []
>>> weekdays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday']
>>> big_birds = ['emu', 'ostrich', 'cassowary']
first_names = ['Graham', 'John', 'Terry', 'Terry', 'Michael']

# list() 함수로 빈 리스트 할당
>>> another_empty_list = list()
>>> another_empty_list
[]
```

요소들이 순서는 상관없이 유일한 값으로만 유지될 필요가 있다면, 리스트보다 **셋(set)**을 사용하는 것이 더 좋다.



### 3.2.2 다른 데이터 타입을 리스트로 변환하기: `list()`

```python
>>> list('cat')
['c', 'a', 't']

# 튜플을 리스트로 변환
>>> a_tuple = ('ready', 'fire', 'aim')
>>> list(a_tuple)
['ready', 'fire', 'aim']

# split()은 문자열을 구분자로 나누어 리스트로 변환
>>> birthday = '1/6/1952'
>>> birthday.split('/')
['1', '6', '1952']

>>> splitme = 'a/b//c/d///e'
>>> splitme.split('/')
['a', 'b', '', 'c', 'd', '', '', 'e']
>>> splitme.split('//')
['a/b', 'c/d', '/e']
```



### 3.2.3 항목 얻기: `[offset]`

문자열과 마찬가지로 리스트는 오프셋으로 하나의 특정 값을 추출할 수 있다.

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes[0]
'Groucho'
>>> marxes[1]
'Chico'
>>> marxes[2]
'Harpo'
>>> marxes[-1]
'Harpo'
>>> marxes[-2]
'Chico'
>>> marxes[-3]
'Groucho'

# 오프셋의 위치가 리스트의 범위를 벗어날 경우 예외 발생
>>> marxes[5]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```



### 3.2.4 리스트의 리스트

리스트는 리스트뿐만 아니라 다른 타입의 요소도 포함할 수 있다.

```python
>>> small_birds = ['hummingbird', 'finch']
>>> extinct_birds = ['dodo', 'passenger pigeon', 'Norwegian Blue']
>>> carol_birds = [3, 'French hens', 2, 'turtledoves']
>>> all_birds = [small_birds, extinct_birds, 'macaw', carol_birds]

>>> all_birds
[['hummingbird', 'finch'], ['dodo', 'passenger pigeon', 'Norwegian Blue'], 'macaw', [3, 'French hens', 2, 'turtledoves']]
>>> all_birds[0]
['hummingbird', 'finch']
>>> all_birds[1]
['dodo', 'passenger pigeon', 'Norwegian Blue']
>>> all_birds[1][0]
'dodo'
```



### 3.2.5 항목 바꾸기: `[offset]`

```python
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> marxes[2] = 'Wanda'
>>> marxes
['Groucho', 'Chico', 'Wanda']
```

리스트의 오프셋은 리스트에서 유효한 위치여야 한다.

문자열은 이러한 방식으로 변경할 수 없다. 왜냐하면 문자열은 불변한다. 리스트는 변경 가능하다. 리스트의 항목 수와 항목 내용을 바꿀 수 있다.



### 3.2.6 슬라이스로 항목 추출하기

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> marxes[0:2]
['Groucho', 'Chico']
>>> marxes[::2]
['Groucho', 'Harpo']
>>> marxes[::-2]
['Harpo', 'Groucho']
>>> marxes[::-1]
['Harpo', 'Chico', 'Groucho']
```



### 3.2.7 리스트의 끝에 항목 추가하기: `append()`

`append()`는 리스트의 끝에 새 항목을 추가한다. 리스트는 변경 가능하기 때문에 이것을 추가할 수 있다.

```python
>>> marxes.append('Zeppo')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo']
```



### 3.2.8 리스트 병합하기: `extend()` 또는 `+=`

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> others = ['Gummo', 'Karl']
>>> marxes.extend(others)
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo', 'Gummo', 'Karl']

>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes += others
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo', 'Gummo', 'Karl']

# append()를 사용하면 항목을 병합하지 않고, 하나의 리스트로 추가
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.append(others)
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo', ['Gummo', 'Karl']]
```



### 3.2.9 오프셋과 `insert()`로 항목 추가하기

`append()` 함수는 단지 리스트의 끝에 항목을 추가한다. 그러나 `insert()` 함수는 원하는 위치에 항목을 추가할 수 있다. 리스트의 끝을 넘는 오프셋은 `append()`처럼 끝에 항목을 추가한다. 예외를 던질 거라는 걱정은 하지 않아도 된다.

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.insert(3, 'Gummo')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
>>> marxes.insert(10, 'Karl')
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo', 'Karl']
```



### 3.2.10 오프셋으로 항목 삭제하기: `del`

```python
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo', 'Karl']
>>> del marxes[-1]
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Gummo', 'Zeppo']
>>> marxes[2]
'Harpo'
>>> del marxes[2]
>>> marxes
['Groucho', 'Chico', 'Gummo', 'Zeppo']
>>> marxes[2]
'Gummo'
```

> `del`은 리스트 함수가 아니라 파이썬 **구문**이다. `del`은 할당(=)의 반대다. 이것은 객체로부터 이름을 분리하고, 객체의 메모리를 비워준다.



### 3.2.11 값으로 항목 삭제하기: `remove()`

리스트에서 삭제할 항목의 위치를 모르는 경우, `remove()`와 값으로 그 항목을 삭제할 수 있다.

```python
>>> marxes
['Groucho', 'Chico', 'Gummo', 'Zeppo']
>>> marxes.remove('Gummo')
>>> marxes
['Groucho', 'Chico', 'Zeppo']
```



### 3.2.12 오프셋으로 항목을 얻은 후 삭제하기: `pop()`

`pop()`은 리스트에서 항목을 가져오는 동시에 그 항목을 삭제한다. 오프셋과 함께 `pop()`을 호출했다면 그 오프셋의 항목이 반환된다. 인자가 없다면 -1을 사용한다. `pop(0)`은 리스트의 시작(head)을 반환한다. 그리고 `pop()` 혹은 `pop(-1)`은 리스트의 끝(tail)을 반환한다.

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.pop()
'Zeppo'
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> marxes.pop(1)
'Chico'
>>> marxes
['Groucho', 'Harpo']
```

> `append()`로 새로운 항목을 끝에 추가한 뒤 `pop()`으로 다시 마지막 항목을 제거했다면, **LIFO(Last In First Out, 후입선출)** 자료구조인 **스택(stack)**을 구현한 것이다.
>
> 그리고 `pop(0)`을 사용했다면 **FIFO(First In First Out, 선입선출)** 자료구조인 **큐(queue)**를 구현한 것이다.



### 3.2.13 값으로 항목 오프셋 찾기: `index()`

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> marxes.index('Chico')
1
```



### 3.2.14 존재여부 확인하기:  `in`

```python
>>> marxes
['Groucho', 'Chico', 'Harpo', 'Zeppo']
>>> 'Groucho' in marxes
True
>>> 'Bob' in marxes
False
```



### 3.2.15 값 세기: `count()`

```python
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> marxes.count('Harpo')
1
>>> marxes.count('Bob')
0

>>> snl_skit = ['cheeseburger', 'cheeseburger', 'cheeseburger']
>>> snl_skit.count('cheeseburger')
3
```



### 3.2.16 문자열로 변환하기: `join()`

```python
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> ', '.join(marxes)
'Groucho, Chico, Harpo'
```

`join()`은 문자열 메서드지, 리스트 메서드가 아니다. `join()`의 인자는 문자열 혹은 반복가능한(iterable) 문자열의 시퀀스다. 그리고 결과로 반환되는 값은 **문자열**이다.

```python
>>> friends = ['Harry', 'Hermione', 'Ron']
>>> separator = ' * '

>>> joined = separator.join(friends)
>>> joined
'Harry * Hermione * Ron'

>>> separated = joined.split(separator)
>>> separated
['Harry', 'Hermione', 'Ron']
>>> separated == friends
True
```



### 3.2.17 정렬하기: `sort()`

값을 이용하여 리스트를 정렬한다. 파이썬은 두 가지 함수를 제공한다.

- `sort()` : 리스트 자체를 **내부적**으로 정렬한다.
- `sorted()` : 리스트의 정렬된 **복사본**을 반환한다.

리스트의 항목이 숫자인 경우, 기본적으로 오름차순으로 정렬한다. 문자열인 경우, 알파벳순으로 정렬한다.

```python
>>> marxes
['Groucho', 'Chico', 'Harpo']
>>> sorted_marxes = sorted(marxes)
>>> sorted_marxes
['Chico', 'Groucho', 'Harpo']
# 원본 리스트는 변하지 않는다.
>>> marxes
['Groucho', 'Chico', 'Harpo']

>>> marxes.sort()
>>> marxes
['Chico', 'Groucho', 'Harpo']

>>> numbers = [2, 1, 4.0, 3]
>>> numbers.sort()
>>> numbers
[1, 2, 3, 4.0]

# 내림차순 정렬
>>> numbers.sort(reverse=True)
>>> numbers
[4.0, 3, 2, 1]
```



### 3.2.18 항목 개수 얻기: `len()`

```python
>>> marxes = ['Chico', 'Groucho', 'Harpo']
>>> len(marxes)
3
```



### 3.2.19 할당: `=`, 복사: `copy()`

```python
>>> a = [1, 2, 3]
>>> a
[1, 2, 3]
>>> b = a
>>> a[0] = 'surprise'
>>> a
['surprise', 2, 3]

# 변수는 단지 이름(name)일 뿐이다. 
# 할당한다는 의미는 값을 복사하는 것이 아니다. 그 이름은 객체 자신에 포함된 것이라기보다는 객체의 참조(reference)다. 

# b는 단지 같은 리스트 객체의 a를 참조,
# 그러므로 두 변수 모두에 변경사항이 반영
>>> b
['surprise', 2, 3]

>>> b[0] = 'I hate surprise'
>>> b
['I hate surprise', 2, 3]
>>> a
['I hate surprise', 2, 3]
```

다음과 같은 방법을 이용하여 한 리스트를 새로운 리스트로 **복사**할 수 있다.

- `copy()` 함수
- `list()` 변환 함수
- 슬라이스 `[:]`

```python
>>> a = [1, 2, 3]
>>> b = a.copy()
>>> c = list(a)
>>> d = a[:]

# b, c, d는 a의 복사본이다. 
# 이들은 자신만의 값을 가진 새로운 객체다.

>>> a[0] = 'integer lists are boring'
>>> a
['integer lists are boring', 2, 3]
>>> b
[1, 2, 3]
>>> c
[1, 2, 3]
>>> d
[1, 2, 3]
```



## 3.3 튜플