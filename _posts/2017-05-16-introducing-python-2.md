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

리스트와 다르게 튜플은 **불변**한다. 튜플은 정의한 후에는 추가, 삭제, 수정을 할 수 없다는 것을 의미한다. 그러므로 튜플은 상수의 리스트라고 할 수 있다.

### 3.3.1 튜플 생성하기: `()`

```python
>>> empty_tuple = ()
>>> empty_tuple
()

# 하나 이상의 요소가 있는 튜플을 만들기 위해서는
# 각 요소 뒤에 콤마(,)를 붙인다.
>>> one_marx = 'Groucho',
>>> one_marx
('Groucho',)

# 두 개 이상의 요소가 있을 경우,
# 마지막 요소에는 콤마를 붙이지 않는다.
>>> marx_tuple = 'Groucho', 'Chico', 'Harpo'
>>> marx_tuple
('Groucho', 'Chico', 'Harpo')

# 괄호를 적어주면, 더 명시적이다.
>>> marx_tuple = ('Groucho', 'Chico', 'Harpo')
>>> marx_tuple
('Groucho', 'Chico', 'Harpo')

# tuple() : 다른 객체를 튜플로 만든다.
>>> marx_list = ['Groucho', 'Chico', 'Harpo']
>>> tuple(marx_list)
('Groucho', 'Chico', 'Harpo')
```

튜플을 정의할 때 괄호(`()`)가 필요 없다. 뒤에 콤마가 붙는다는 것은 튜플을 정의한다는 뜻이다.

#### 튜플 언패킹(tuple unpacking)

```python
>>> a, b, c = marx_tuple
>>> a
'Groucho'
>>> b
'Chico'
>>> c
'Harpo'

# 한 문장에서 값을 교환하기 위해 임시 변수를 사용하지 않고 튜플을 사용할 수 있다.
>>> password = 'swordfish'
>>> icecream = 'tuttifrutti'
>>> password, icecream = password, icecream
>>> password
'swordfish'
>>> icecream
'tuttifrutti'
```



### 3.3.2 튜플과 리스트

튜플은 리스트의 `append()`, `insert()` 등과 같은 함수가 없고, 함수의 수가 매우 적다. 그러면 왜 튜플을 사용할까?

- 튜플은 더 적은 공간을 사용한다.
- 실수로 튜플의 항목이 손상될 염려가 없다.
- 튜플을 딕셔너리의 키로 사용할 수 있다.
- 네임드 튜플(named tuple)은 객체의 단순한 대안이 될 수 있다.
- 함수의 인자들은 튜플로 전달된다.

일반적으로 리스트와 딕셔너리를 더 많이 사용한다.



## 3.4 딕셔너리

딕셔너리(dictionary)는 리스트와 비슷하다. 다른 점은 항목의 순서를 따지지 않으며, 0또는 1과 같은 오프셋으로 항목을 선택할 수 없다. 대신 **값(value)**에 상응하는 고유한 **키(key)**를 지정한다. 딕셔너리는 변경 가능하므로 키-값 요소를 추가, 삭제, 수정할 수 있다.

> 다른 언어에서는 딕셔너리를 **associative array, hash, hashmap** 이라고 부른다.

### 3.4.1 딕셔너리 생성하기:`{}`

```python
>>> empty_dict = {}
>>> empty_dict
{}

>>> bierce = {
... "day" : "A period of twenty-four hours, mostly misspent",
... "positive" : "Mistaken at the top of one's voice",
... "misfortune" : "The kind of fortune that never misses",
... }
>>> bierce
{'day': 'A period of twenty-four hours, mostly misspent', 'misfortune': 'The kind of fortune that never misses', 'positive': "Mistaken at the top of one's voice"}
```



### 3.4.2 딕셔너리로 변환하기: `dict()`

딕셔너리의 키 순서는 임의적이다.

```python
>>> lol = [['a', 'b'], ['c', 'd'], ['e', 'f']]
>>> dict(lol)
{'c': 'd', 'a': 'b', 'e': 'f'}

>>> lot = [('a', 'b'), ('c', 'd'), ('e', 'f')]
>>> dict(lot)
{'c': 'd', 'a': 'b', 'e': 'f'}

>>> tol = (['a', 'b'], ['c', 'd'], ['e', 'f'])
>>> dict(tol)
{'c': 'd', 'a': 'b', 'e': 'f'}

>>> los = ['ab', 'cd', 'ef']
>>> dict(los)
{'c': 'd', 'a': 'b', 'e': 'f'}
```



### 3.4.3 항목 추가/변경하기: `[key]`

리스트와 달리 딕셔너리를 할당할 때는 인덱스의 범위 지정이 벗어났다는 예외에 대해 걱정할 필요가 없다.

```python
>>> pythons = {
... 'Chapman': 'Graham',
... 'Cleese': 'John',
... 'Idle': 'Eric',
... 'Jones': 'Terry',
... 'Palin': 'Michael',
... }
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Palin': 'Michael', 'Jones': 'Terry'}

>>> pythons['Gilliam'] = 'Gerry'
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Palin': 'Michael', 'Gilliam': 'Gerry', 'Jones': 'Terry'}

>>> pythons['Gilliam'] = 'Terry'
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Palin': 'Michael', 'Gilliam': 'Terry', 'Jones': 'Terry'}
```

딕셔너리 키들은 반드시 **유일**해야 한다. 만약 같은 키를 두 번 이상 사용하면 마지막 값이 승리한다.

```python
>>> some_python = {
... 'Graham': 'Chapman',
... 'John': 'Cleese',
... 'Eric': 'Idle',
... 'Terry': 'Gilliam',
... 'Michael': 'Palin',
... 'Terry': 'Jones',
... }
>>> some_python
{'Eric': 'Idle', 'John': 'Cleese', 'Michael': 'Palin', 'Graham': 'Chapman', 'Terry': 'Jones'}
```



### 3.4.4 딕셔너리 결합하기: `update()`

`update()` 함수는 한 딕셔너리의 키와 값들을 복사해서 다른 딕셔너리에 붙여준다.

```python
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Palin': 'Michael', 'Gilliam': 'Terry', 'Jones': 'Terry'}
>>> others = {'Marx': 'Groucho', 'Howard': 'Moe'}
>>> pythons.update(others)
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Howard': 'Moe', 'Marx': 'Groucho', 'Palin': 'Michael', 'Gilliam': 'Terry', 'Jones': 'Terry'}

>>> first = {'a': 1, 'b': 2}
>>> second = {'b': 'platypus'}
>>> first.update(second)
>>> first
{'a': 1, 'b': 'platypus'}
```



### 3.4.5 항목 삭제하기: `[key]`와 `del`

```python
>>> del pythons['Marx']
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Howard': 'Moe', 'Palin': 'Michael', 'Gilliam': 'Terry', 'Jones': 'Terry'}

>>> del pythons['Howard']
>>> pythons
{'Idle': 'Eric', 'Cleese': 'John', 'Chapman': 'Graham', 'Palin': 'Michael', 'Gilliam': 'Terry', 'Jones': 'Terry'}
```



### 3.4.6 모든 항목 삭제하기: `clear()`

```python
>>> pythons.clear()
>>> pythons
{}

>>> others = {}
>>> others
{}
```



### 3.4.7 키(key)를 위한 테스트: `in`

딕셔너리에 키가 존재하는지 알고 싶다면 in을 사용한다.

```python
>>> pythons = {
... 'Chapman': 'Graham',
... 'Cleese': 'John',
... 'Jones': 'Terry',
... 'Palin': 'Michael'
... }
>>> 'Chapman' in pythons
True
>>> 'Palin' in pythons
True
>>> 'Gilliam' in pythons
False
```



### 3.4.8 항목 얻기: `[key]`

```python
>>> pythons['Cleese']
'John'

# 딕셔너리에 키가 존재하지 않으면 예외발생
>>> pythons['Marx']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Marx'
```

위의 문제를 피하는 방법이 있다. 첫 번째 `in`으로 키에 대해 테스트를 실행하는 것이다. 두 번째 방법은 딕셔너리의 `get()` 함수를 사용하는 것이다. 이 함수는 딕셔너리, 키, 옵션값을 사용한다.

```python
# 키가 존재할 경우, 그 값을 얻는다.
>>> pythons.get('Cleese')
'John'

# 키가 존재하지 않을 경우, 옵션값을 지정해 이를 출력
>>> pythons.get('Marx', 'Not a Python')
'Not a Python'
# 옵션값을 지정하지 않으면 None을 얻는다.
>>> pythons.get('Marx')
>>>
```



### 3.4.9 모든 키 얻기: `keys()`

딕셔너리의 모든 키를 가져오기 위해서는 `keys()`를 사용한다.

```python
>>> signals = {'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
>>> signals.keys()
dict_keys(['green', 'yellow', 'red'])
```

> 순회가능한(iterable) `dict_keys()`를 반환한다. 이것은 아주 큰 딕셔너리에 유용하다. 사용되지 않을 리스트를 생성하고 저장하기 위한 메모리와 시간을 소비하지 않기 때문이다.
>
> 이것을 실제 리스트로 쓰고 싶을 때가 있다. 파이썬 3에서는 `list()`를 호출해서 dict_keys 객체를 리스트로 변환할 수 있다.
>
> ```python
> >>> list(signals.keys())
> ['green', 'yellow', 'red']
> ```

### 3.4.10 모든 값 얻기: `values()`

```python
>>> list(signals.values())
['go', 'go faster', 'smile for the camera']
```

### 3.4.11 모든 쌍의 키-값 얻기: `items()`

```python
# 각 키와 값은 튜플로 반환
>>> list(signals.items())
[('green', 'go'), ('yellow', 'go faster'), ('red', 'smile for the camera')]
```

### 3.4.12 할당: `=`, 복사: `copy()`

```python
>>> signals
{'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
>>> save_signals = signals
>>> signals['blue'] = 'confuse everyone'
>>> save_signals
{'green': 'go', 'yellow': 'go faster', 'blue': 'confuse everyone', 'red': 'smile for the camera'}

>>> signals = {'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
>>> original_signals = signals.copy()
>>> signals['blue'] = 'confuse everyone'
>>> signals
{'green': 'go', 'yellow': 'go faster', 'blue': 'confuse everyone', 'red': 'smile for the camera'}
>>> original_signals
{'green': 'go', 'yellow': 'go faster', 'red': 'smile for the camera'}
```



## 3.5 셋

**셋(set)**은 값은 버리고 키만 남은 딕셔너리와 같다. 딕셔너리와 마찬가지로 각 키는 유일해야 한다. 어떤 것이 존재하는지 여부만 판단하기 위해서는 셋을 사용한다. 그리고 키에 어떤 정보를 첨부해서 그 결과를 얻고 싶으면 딕셔너리를 사용한다.

### 3.5.1 셋 생성하기: `set()`

딕셔너리의 키와 마찬가지로 셋은 순서가 없다.

```python
>>> empty_set = set()
>>> empty_set
set()
>>> even_number = {0, 2, 4, 6, 8}
>>> even_number
{8, 0, 2, 4, 6}
>>> odd_number = {1, 3, 5, 7, 9}
>>> odd_number
{9, 1, 3, 5, 7}
```

> `{}`는 빈 딕셔너리를 생성한다. 이것이 인터프리터가 빈 셋을 `{}` 대신 `set()`으로 출력하는 이유이기도 하다. 왜 그럴까? 파이썬에서 딕셔너리가 먼저 등장해서 중괄호를 이미 차지하고 있었기 때문이다.

### 3.5.2 데이터 타입 변환하기: `set()`

리스트, 문자열, 튜플, 딕셔너리로부터 중복된 값을 버린 셋을 생성할 수 있다.

```python
>>> set('letters')
{'l', 't', 'e', 'r', 's'}
>>> set(['Dasher', 'Dancer', 'Prancer', 'Mason-Dixon'])
{'Dasher', 'Prancer', 'Dancer', 'Mason-Dixon'}
>>> set(('Ummagumma', 'Echoes', 'Atom Heart Mother'))
{'Ummagumma', 'Echoes', 'Atom Heart Mother'}
>>> set({'apple': 'red', 'orange': 'orange', 'cherry': 'red'})
{'cherry', 'orange', 'apple'}
```

### 3.5.3 `in`으로 값 멤버십 테스트하기

이것은 일반적으로 사용되는 셋의 용도다.

```python
# 딕셔너리 안에 여러개의 키-값 쌍들이 있다.
# 셋은 값들의 시퀀스이다.
>>> drinks = {
... 'martini': {'vodka', 'vermouth'},
... 'black russian': {'vodka', 'kahlua'},
... 'white russian': {'cream', 'kahlua', 'vodka'},
... 'manhattan': {'rye', 'vermouth', 'bitters'},
... 'screwdriver': {'orange juice', 'vodka'}
... }

>>> for name, contents in drinks.items():
...     if 'vodka' in contents:
...         print(name)
...
screwdriver
black russian
white russian
martini
```

### 3.5.4 콤비네이션과 연산자

셋값의 **조합(combination)**을 어떻게 확인할까? 

**셋 교집합 연산자(set intersection operator)**인 **앰퍼샌드(`&`)**를 사용한다.

```python
>>> for name, contents in drinks.items():
...     if contents & {'vermouth', 'orange juice'}:
...         print(name)
...
manhattan
screwdriver
martini
```

`&` 연산자 결과가 없다면, False로 간주되는 빈 셋을 반환한다.

```python
>>> bruss = drinks['black russian']
>>> wruss = drinks['white russian']
>>> a = {1, 2}
>>> b = {2, 3}

# 교집합(intersection) : 양쪽 셋에 모두 들어있는 멤버
>>> a & b
{2}
>>> a.intersection(b)
{2}
>>> bruss & wruss
{'kahlua', 'vodka'}

# 합집합(union) : 각 셋의 멤버 모두
>>> a | b
{1, 2, 3}
>>> a.union(b)
{1, 2, 3}
>>> bruss | wruss
{'kahlua', 'cream', 'vodka'}
>>> bruss.union(wruss)
{'kahlua', 'cream', 'vodka'}

# 차집합(difference) : 첫 번째 셋에는 있지만 두 번째 셋에는 없는 멤버
>>> a - b
{1}
>>> a.difference(b)
{1}
>>> bruss - wruss
set()
>>> wruss - bruss
{'cream'}

# 대칭 차집합(exclusive) : 한 쪽 셋에는 들어 있지만 양쪽 모두에 들어 있지 않은 멤버
>>> a ^ b
{1, 3}
>>> a.symmetric_difference(b)
{1, 3}
>>> bruss ^ wruss
{'cream'}

# 부분집합(subset)
>>> a <= b
False
>>> a.issubset(b)
False
>>> bruss <= wruss
True
# 모든 셋은 자신의 서브셋이다.
>>> a <= a
True
>>> a.issubset(a)
True

# 진부분집합(proper subset) : 첫 번째 셋이 두 번째 셋의 진부분집합이 되려면,
# 두 번째 셋에는 첫 번째 셋의 모든 멤버를 포함한 그 이상의 멤버가 있어야 한다.
>>> a < b
False
>>> a < a
False
>>> bruss < wruss
True

# superset : subset의 반대이다.
>>> a >= b
False
>>> a.issuperset(b)
False
>>> wruss >= bruss
True
# 모든 셋은 자신의 슈퍼셋이다.
>>> a >= a
True
>>> a.issuperset(a)
True

# proper superset: 첫 번째 셋이 두 번째 셋의 프로퍼 슈퍼셋이 되려면,
# 첫 번째 셋에는 두 번째 셋의 모든 멤버를 포함한 그 이상의 멤버가 있어야 한다.
>>> a > b
False
>>> wruss > bruss
True
# 모든 셋은 자신의 프로퍼 슈퍼셋이 될 수 없다.
>>> a > a
False
```



## 3.6 자료구조 비교하기

- `[]` : 리스트
- `,` : 튜플
- `{}` : 딕셔너리

```python
>>> marx_list = ['Groucho', 'Chico', 'Harpo']
>>> marx_tuple = ('Groucho', 'Chico', 'Harpo')
>>> marx_dict = {'Harpo': 'harp', 'Chico': 'piano', 'Groucho': 'banjo'}
>>> marx_list[2]
'Harpo'
>>> marx_tuple[2]
'Harpo'
>>> marx_dict['Harpo']
'harp'
```

리스트와 튜플은 대괄호에 들어가는 값이 정수 **오프셋**이고, 딕셔너리의 경우에는 **키**다.



## 3.7 자료구조를 더 크게

내장된 자료구조를 결합해서 더 크고 복잡한 자료구조를 만들 수 있다.

```python
>>> marxes = ['Groucho', 'Chico', 'Harpo']
>>> pythons = ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin']
>>> stooges = ['Moe', 'Curly', 'Larry']

# 튜플의 각 요소의 리스트다.
>>> tuple_of_lists = marxes, pythons, stooges
>>> tuple_of_lists
(['Groucho', 'Chico', 'Harpo'], ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin'], ['Moe', 'Curly', 'Larry'])

# 세 리스트를 한 리스트에 포함
>>> list_of_lists = [marxes, pythons, stooges]
>>> list_of_lists
[['Groucho', 'Chico', 'Harpo'], ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin'], ['Moe', 'Curly', 'Larry']]

# 리스트의 딕셔너리
>>> dict_of_lists = {'Pythons': pythons, 'Stooges': stooges, 'Marxes': marxes}
>>> dict_of_lists
{'Pythons': ['Chapman', 'Cleese', 'Gilliam', 'Jones', 'Palin'], 'Stooges': ['Moe', 'Curly', 'Larry'], 'Marxes': ['Groucho', 'Chico', 'Harpo']}
```

제한사항이 있다면 데이터 타입 그 자체다. 예를 들어 딕셔너리의 키는 불변하기 때문에 리스트, 딕셔너리, 셋은 다른 딕셔너리의 키가 될 수 없다. 그러나 튜플은 딕셔너리의 키가 될 수 있다.

```python
>>> houses = {
...:     (44.79, -93.14, 285): 'My House',
...:     (38.89, -77.03, 13): 'The White House'
...: }
```

