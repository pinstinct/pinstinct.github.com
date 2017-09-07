---
layout: post
title: 코드 구조
categories: python
tags: [python]
---



# Chapter4 파이 크러스: 코드 구조

파이썬은 프로그램의 구조를 정의하기 위해 **공백**을 사용하는 흔치 않은 언어이다.

## 4.1 코멘트 달기: `#`

```python
In [1]: # 60 sec/min * 60 min/hr * 24 hr/day
In [2]: seconds_per_day = 86400

In [3]: seconds_per_day = 86400 # 60 sec/min * 60 min/hr * 24hr/day
    
# 파이썬에는 여러라인을 처리하는 주석이 없다.
# 명시적으로 각 행이나 부분에 #을 붙여야 한다.
In [4]: # I can say anything here, even if Python doesn't like it,
In [5]: # because I'm protected by the awesome
In [6]: # octothorpe.

# # 문자가 문자열안에 있다면, 코멘트가 아닌 평범한 문자로 돌아가게 된다.
In [7]: print("No comment: quotes make the # harmless.")
No comment: quotes make the # harmless.
```

`#` 문자는 *hash, sharp, pound, octo-thorpe*라는 많은 이름이 있다.



## 4.2 라인 유지하기: `\`

한 라인에 권장한느 최대 문자수는 80자다. 이 길이 안에 넣고 싶은 코드를 모두 입력할 수 없다면 백슬래지(`\`) 문자를 입력한 후 다음 라인에 계속 입력한다.

```python
In [1]: alphabet = ''
In [2]: alphabet += 'abcdefg'
In [3]: alphabet += 'hijklmnop'
In [4]: alphabet += 'qrstuv'
In [5]: alphabet += 'wxyz'

In [6]: alphabet = 'abcdefg' + \
   ...:     'hijklmnop' + \
   ...:     'qrstuv' + \
   ...:     'wxyz'

In [7]: 1 + 2 +
  File "<ipython-input-7-91fbedf112a5>", line 1
    1 + 2 +
           ^
SyntaxError: invalid syntax

In [8]: 1 + 2 + \
   ...: 3
Out[8]: 6
```



## 4.3 비교하기: `if`, `elif`, `else`

```python
In [10]: if disaster:
    ...:     print("Woe!")
    ...: else:
    ...:     print("Whee!")
    ...:
Woe!

# 여러 단계에 걸쳐 조건 테스트
In [14]: if furry:
    ...:     if small:
    ...:         print("It's a cat.")
    ...:     else:
    ...:         print("It's a bear!")
    ...: else:
    ...:     if small:
    ...:         print("It's a skink!")
    ...:     else:
    ...:         print("It's a human. Or a hairless bear.")
    ...:
It's a cat.

# 두 개 이상의 조건 테스트
In [15]: color = "puce"
In [16]: if color == "red":
    ...:     print("It's a tomato")
    ...: elif color == "green":
    ...:     print("It's a green pepper")
    ...: elif color == "bee purple":
    ...:     print("I don't know what it is, but only bees can see it")
    ...: else:
    ...:     print("I've never heard of the color", color)
    ...:
I've never heard of the color puce
```

파이썬 **비교 연산자**는 다음과 같다.

| 비교 연산자 | 의미        |
| ------ | --------- |
| `==`   | 같다        |
| `!=`   | 다르다       |
| `<`    | 보다 작다     |
| `<=`   | 보다 작거나 같다 |
| `>`    | 보다 크다     |
| `>=`   | 보다 크거나 같다 |
| `in`   | 멤버십       |



```python
In [17]: x = 7

In [18]: x == 5
Out[18]: False

In [19]: x == 7
Out[19]: True

In [20]: x < 5
Out[20]: False

In [21]: x < 10
Out[21]: True

In [23]: (x > 5) and (x < 10)
Out[23]: True

In [24]: 5 < x < 10
Out[24]: True
```



### 4.3.1 True와 False

다음은 모두 `False`로 간주한다.

| 요소      | False |
| ------- | ----- |
| null    | None  |
| 정수 0    | 0     |
| 부동소수점 0 | 0.0   |
| 빈 문자열   | ''    |
| 빈 리스트   | []    |
| 빈 튜플    | ()    |
| 빈 딕셔너리  | {}    |
| 빈 셋     | set() |

이 외의 다른 것들은 True로 간주된다.



## 4.4 반복하기: `while`

한 번 이상 뭔가 실행하려 할 때는 **루프(loop)**가 필요하다.

```python
In [25]: count = 1

In [26]: while count <= 5:
    ...:     print(count)
    ...:     count += 1
    ...:
1
2
3
4
5
```



### 4.4.1 중단하기: `break`

어떤 일이 일어날 때까지 반복하고 싶지만, 어떤 일이 언제 일어날지 확실하지 않다면 **무한루프** 속에 `break` 문을 사용한다.

```python
In [27]: while True:
    ...:     stuff = input("String to capitalize [type q to quit]: ")
    ...:     if stuff == "q":
    ...:         break
    ...:     print(stuff.capitalize())
    ...:
String to capitalize [type q to quit]: test
Test
String to capitalize [type q to quit]: hey, it works
Hey, it works
String to capitalize [type q to quit]: q
```



### 4.4.2 건너뛰기: `continue`

반복문을 중단하고 싶지는 않지만 몇몇 이유로 다음 루프로 건너뛰고 싶을 때 사용한다.

```python
In [28]: while True:
    ...:     value = input("Integer, please [q to quit]: ")
    ...:     if value == 'q':    # quit
    ...:         break
    ...:     number = int(value)
    ...:     if number % 2 == 0:    # an even number
    ...:         continue
    ...:     print(number, "squared is", number * number)
    ...:
Integer, please [q to quit]: 1
1 squared is 1
Integer, please [q to quit]: 2
Integer, please [q to quit]: 3
3 squared is 9
Integer, please [q to quit]: 4
Integer, please [q to quit]: 5
5 squared is 25
Integer, please [q to quit]: q
```

### 4.4.3 break 확인하기: `else`

`break`는 어떤 것을 체크하여 그것을 발견했을 경우 종료하는 `while` 문을 작성할 때 사용한다. `while` 문이 모두 실행되었지만 발견하지 못했을 경우에는 `else`가 실행된다.

```python
In [32]: while position < len(numbers):
    ...:     number = numbers[position]
    ...:     if number % 2 == 0:
    ...:         print('Found even number', number)
    ...:         break
    ...:     position += 1
    ...: else:    # break가 호출되지 않았다.
    ...:     print('No even number found')
    ...:
No even number found
```



## 4.5 순회하기(iterate): `for`

파이썬에서 **iterator**는 자주 유용하게 쓰인다.

자료구조가 얼마나 큰지, 어떻게 구현되었는지에 관계없이 자료구조를 순회할 수 있도록 해준다. 심지어 바로 생성되는 데이터도 순회할 수 있다. 데이터가 메모리에 맞지 않더라도 데이터 **스트림**을 처리할 수 있도록 허용해준다.

