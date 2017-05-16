---
layout: post
title: 숫자, 문자열, 변수
categories: python
tags: [python]
---



# Chapter2 파이재료: 숫자, 문자열, 변수

이들은 원자(atom)와 비슷하다. 3장에서는 이들을 어떻게 더 큰 분자(molecule)로 결합하는지 살펴본다.

- booleans (True or False)
- integers
- floats
- strings

## 2.1 변수, 이름, 객체

파이썬에서 **모든 것이 객체**로 구현되어 있다.

- 객체는 데이터가 담긴 투명한 플라스틱 박스와 같다. 
- 객체는 부울 혹은 정수와 같은 **타입(type)**을 가진다. 
  - 타입은 데이터와 함께 무엇을 처리할 수 있는지 결정
  - 타입은 또한 박스에 포함된 데이터**값**을 *변경할 수 있는 변수(mutable)*인지 혹은 *변경할 수 없는 상수(immutable)*인지 결정
  - 파이썬은 객체의 타입을 바꿀 수 없는 강타입(strongly typed)이다.




프로그래밍 언어에서 변수(variable)를 선언하여 사용할 수 있다. 변수는 컴퓨터 메모리에 있는 값을 참조하기 위한 이름이다.

파이썬 변수의 핵심을 살펴보자. **변수는 단지 이름(name)**일 뿐이다. 할당한다는 의미는 값을 **복사하는 것이 아니다**. 데이터가 담긴 객체에 그냥 **이름을 붙이는 것**이다. 그 이름은 객체 자신에 포함된 것이라기보다는 객체의 **참조(reference)**다. 이름을 포스트잇처럼 생각하자.

**클래스(class)**는 객체의 정의(definition)를 의미한다. 파이썬에서 'class'와 'type'은 그 의미가 거의 같다.

## 2.2 숫자

파이썬은 정수(integer)와 부동소수점(floating point)을 지원하는 기능이 내장되어 있다. 다음 표에 있는 간단한 수학 **연산자(operator)**로 결합해 계산할 수 있다.

| 연산자  | 설명                 |
| ---- | ------------------ |
| `+`  | 더하기                |
| `-`  | 빼기                 |
| `*`  | 곱하기                |
| `/`  | 부동소수점 나누기          |
| `//` | 정수 나누기 (소수점 이하 버림) |
| `%`  | 나머지                |
| `**` | 지수                 |

### 2.2.1 정수

```python
>>> 5
5
>>> 0
0
# 0을 다른 숫자 앞에 넣으면 예외 발생
>>> 05
  File "<stdin>", line 1
    05
     ^
SyntaxError: invalid token

>>> 123
123
>>> +123
123
>>> -123
-123

>>> 5 + 9
14
>>> 100 - 7
93
>>> 4 - 10
-6
>>> 5 + 9 + 3
17
>>> 6 * 7
42

# 부동소수점을 포함한 결과를 출력
>>> 9 / 5
1.8
# 부동소수점을 제외한 결과, 즉 정수 출력
>>> 9 // 5
1

# 0으로 나누면 예외 발생
>>> 5 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> 7 // 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero

>>> 9 // 5
1
>>> 9 % 5
4
# 몫과 나머지 결과를 튜플로 반환
>>> divmod(9, 5)
(1, 4)
```

### 2.2.2 우선순위

괄호를 사용하자.

### 2.2.3 진수

파이썬에서 10진수 외에도 세 가지 리터럴 정수를 표현할 수 있다.

- 2진수(binary): 0b
- 8진수(octal): 0o
- 16진수(hex): 0x

```python
>>> 10
10
>>> 0b10
2
>>> 0o10
8
>>> 0x10
16
```

### 2.2.4 형변환

다른 데이터 타입을 정수형으로 변환하려면 `int()` 함수를 사용한다.

```python
>>> int(True)
1
>>> int(False)
0

# 부동소수점을 정수로 변환하면 소수점을 버리고 정수로 출력
>>> int(98.6)
98
>>> int(1.0e4)
10000

# 문자열을 정수로 변환
>>> int('99')
99
>>> int('-23')
-23

# int() 함수에서 숫자가 아닌 다른 뭔가를 변환하면 예외 발생
>>> int('99 bottles of beer on the wall')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: '99 bottles of beer on the wall'
      
# 소수점 혹은 지수를 포함하는 문자열을 변환하면 예외 발생
>>> int('98.6')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: '98.6' 
>>> int('1.0e4')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: '1.0e4'
```

### 2.2.5 int의 크기

파이썬 2에서 int의 크기는 32비트로 제한되었다. long은 그보다 더 많은 공간인 64비트까지 허용한다.

파이썬 3에서는 long이 사라지고, int의 크기가 유연해졌다.

```python
>>> googol = 10**100
>>> googol
10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
>>> googol * googol
100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
```

### 2.2.6 부동소수점수

부동소수점수(파이썬에서 float이라고 한다)는 소수점이 있다.

```python
>>> float(True)
1.0
>>> float(False)
0.0
>>> float(98)
98.0
>>> float('99')
99.0
```

## 2.3 문자열

파이썬 3에서는 유니코드 표준에 대한 지원으로 전 세계에서 쓰이는 언어들의 많은 기호를 담을 수 있다.

파이썬에서는 문자열이 불변(immutable)이다.

시퀀스(Sequence) 타입 : list, tuple, range, binary data, text strings

### 2.3.1 인용 부호로 문자열 생성

```python
>>> 'Snap'
'Snap'
>>> "Cracle"
'Cracle'
>>> "'Nay, ' said the naysayer."
"'Nay, ' said the naysayer."
>>> 'The rare double quote in captivity: ".'
'The rare double quote in captivity: ".'
>>> 'A "two by four" is actualy 1 1/2" x 3 1/2".'
'A "two by four" is actualy 1 1/2" x 3 1/2".'
>>> "There's the man that shot my paw!' cried the limping bound."
"There's the man that shot my paw!' cried the limping bound."

>>> '''Boom!'''
'Boom!'
>>> """Eek!"""
'Eek!'

# 일반적으로 여러개의 문자열에 사용
>>> poem = '''There was a Young Lady of Norway,
... Who casually sat in a doorway;
... When the door squeezed her flat,
... She exclaimed, "What of that?"
... This courageous Young Lady of Norway.'''
    
>>> poem2 = '''I do not like thee, Doctor Fell.
...     The reason why, I cannot tell.
...     But this I know, and know full well:
...     I do not like thee, Doctor Fell.
... '''

# print()의 출력 결과와 인터프리터의 자동 출력 결과가 다르다.
>>> print(poem2)
I do not like thee, Doctor Fell.
    The reason why, I cannot tell.
    But this I know, and know full well:
    I do not like thee, Doctor Fell.
>>> poem2
'I do not like thee, Doctor Fell.\n    The reason why, I cannot tell.\n    But this I know, and know full well:\n    I do not like thee, Doctor Fell.\n'

>>> print(99, 'bottles', 'would be enough.')
99 bottles would be enough.

# 빈 문자열
>>> ''
''
>>> ""
''
>>> ''''''
''
>>> """"""
''

>>> bottles = 99
>>> base = ''
>>> base += 'current inventory: '
>>> base += str(bottles)
>>> base
'current inventory: 99'
```



### 2.3.2 데이터 타입 변환: `str()`

```python
>>> str(98.6)
'98.6'
>>> str(1.0e4)
'10000.0'
>>> str(True)
'True'
```



### 2.3.3 이스케이프 문자: `/`

```python
# /n : 줄바꿈
>>> palindrome = 'A man, \nA plan, \nA canal:\nPanama.'
>>> print(palindrome)
A man,
A plan,
A canal:
Panama.

# /t : tab
>>> print('\tabc')
	abc
>>> print('a\tbc')
a	bc
>>> print('ab\tc')
ab	c
>>> print('abc\t')
abc

# \", \' : 단일 혹은 이중 인용 부호
>>> testimony = "\"I did nothing!\" he said. \"Not that either! Or the other thing.\""
>>> print(testimony)
"I did nothing!" he said. "Not that either! Or the other thing."
>>> fact = "The world's largest rubber duck was 54'2\" by 65'7\" by 105'"
>>> print(fact)
The world's largest rubber duck was 54'2" by 65'7" by 105'

# \\ : 백슬래시
>>> speech = 'Today we honor our friend, the backslash: \\.'
>>> print(speech)
Today we honor our friend, the backslash: \.
```



### 2.3.4 결합: `+`

```python
# 리터럴 문자열 or 문자열 변수를 결합할 수 있다.
>>> 'Release the kraken! ' + 'At once!'
'Release the kraken! At once!'

# 리터럴 문자열을 다음과 같이 차례대로 결합할 수 있다.
>>> "My word! " "A gentleman caller!"
'My word! A gentleman caller!'

>>> a = 'Duck.'
>>> b = a
>>> c = 'Grey Duck!'

# 파이썬은 문자열을 결합할 때 공백을 자동으로 붙이지 않는다.
>>> a + b + c
'Duck.Duck.Grey Duck!'

# print()는 각 인자 사이에 공백을 붙인다. 그리고 마지막에 줄바꿈 문자를 붙인다.
>>> print(a, b, c)
Duck. Duck. Grey Duck!
```



### 2.3.5 복제: `*`

```python
>>> start = 'Na ' * 4 + '\n'
>>> middle = 'Hey ' * 3 + '\n'
>>> end = 'Goodbye.'
>>> print(start + start + middle + end)
Na Na Na Na
Na Na Na Na
Hey Hey Hey
Goodbye.
```



### 2.3.6 문자 추출: `[]`

문자열에서 한 문자를 얻기 위해서는 문자열 이름 뒤에 대괄호(`[]`)와 **오프셋**을 지정한다.

```python
>>> letters = 'abcdefghijklmnopqrstuvwxyz'
>>> letters[0]
'a'
>>> letters[1]
'b'

>>> letters[-1]
'z'
>>> letters[-2]
'y'

# 오프셋을 문자열의 길이 혹은 이상으로 지정하는 경우 다음과 같은 예외 발생
>>> print(len(letters))
26
>>> letters[26]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
>>> letters[100]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
```

문자열은 불변하기 때문에 특정 인덱스에 문자를 삽입하거나 변경할 수 없다.

```python
>>> name = 'Henny'
>>> name[0]
'H'
>>> name[0] = 'P'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
    
# 대신 replace() 혹은 slice와 같은 문자열 함수를 사용할 수 있다.
>>> name.replace('H', 'P')
'Penny'
>>> 'H' + name[1:]
'Henny'
```



### 2.3.7 슬라이스: `[start:end:step]`

슬라이스를 사용하여 한 문자열에서 **문자열의 일부(substring)**를 추출할 수 있다.

```python
>>> letters
'abcdefghijklmnopqrstuvwxyz'
>>> letters[:]
'abcdefghijklmnopqrstuvwxyz'
>>> letters[20:]
'uvwxyz'
>>> letters[10:]
'klmnopqrstuvwxyz'

# 파이썬은 마지막 오프셋은 포함하지 않는다.
>>> letters[12:15]
'mno'
>>> letters[-3:]
'xyz'
>>> letters[18:-3]
'stuvw'
>>> letters[-6:-2]
'uvwx'
>>> letters[::7]
'ahov'
>>> letters[4:20:3]
'ehknqt'
>>> letters[::-1]
'zyxwvutsrqponmlkjihgfedcba'
```



### 2.3.8 문자열 길이: `len()`

파이썬의 내장 함수

```python
>>> len(letters)
26
>>> empty = ""
>>> len(empty)
0
```



### 2.3.9 문자열 나누기: `split()`

`len()`과는 달리 문자열에 특정한 함수도 있다.  문자열 함수는 `string.function(arguments)` 형태로 입력하여 사용한다.

어떤 **구분자(separator)**를 기준으로 하나의 문자열을 작은 문자열 **리스트**로 나누기 위해서 문자열 내장 함수 `split()`을 사용한다.

```python
>>> todos = 'get gloves,get mask,give cat vitamins,call ambulance'
>>> todos.split(',')
['get gloves', 'get mask', 'give cat vitamins', 'call ambulance']

>>> todos.split()
['get', 'gloves,get', 'mask,give', 'cat', 'vitamins,call', 'ambulance']
```

만약 구분자를 지정하지 않으면 `split()`는 문자열에 등장하는 공백문자(줄바꿈, 스페이스, 탭)를 사용한다.



### 2.3.10 문자열로 결합하기: `join()`

`join()` 함수는 `split()` 함수와 반대다. `join()` 함수는 문자열 리스트를 하나의 문자열로 결합한다. `string.join(list)` 형태로 결합한다.

```python
>>> crypto_list = ['Yeti', 'Bigfoot', 'Loch Ness Monster']
>>> crypto_string = ', '.join(crypto_list)
>>> print('Found and signing book deals:', crypto_string)
Found and signing book deals: Yeti, Bigfoot, Loch Ness Monster
```



### 2.3.11 문자열 다루기

```python
>>> poem  = '''All that doth flow we cannot liquid name
... Or else would fire and water be the same;
... But that is liquid which is moist and wet
... Fire that property can naver get.
... Then 'tis not cold that doth the fire put out
... But 'tis the wet that makes it die, no doubt.'''
>>> poem[:13]
'All that doth'
>>> len(poem)
250

# All로 시작하는가?
>>> poem.startswith('All')
True

# That's all, folks!로 끝나는가?
>>> poem.endswith('That\'s all, folks!')
False

>>> word = 'the'
# 첫 번째로 the가 나오는 오프셋은?
>>> poem.find(word)
73
# 마지막으로 the가 나오는 오프셋은?
>>> poem.rfind(word)
214

>>> poem.count(word)
3
# 글자와 숫자로만 이루어져 있는가?
>>> poem.isalnum()
False
```



### 2.3.12 대소문자와 배치

```python
>>> setup = 'a duck goes into a bar...'

# 문자열은 불변하기 때문에,
# 다음의 어느 예제에서도 setup 문자열을 바꿀 수 없다.
# 함수를 수행한 뒤 새로운 문자열로 결과를 반환한다.
>>> setup.strip('.')
'a duck goes into a bar'
>>> setup.capitalize()
'A duck goes into a bar...'
>>> setup.title()
'A Duck Goes Into A Bar...'
>>> setup.upper()
'A DUCK GOES INTO A BAR...'
>>> setup.swapcase()
'A DUCK GOES INTO A BAR...'

# 문자열 정렬
# 문자열을 지정한 공간(여기서는 30)에서 정렬

# 중앙 배치
>>> setup.center(30)
'  a duck goes into a bar...   '
# 왼쪽 배치
>>> setup.ljust(30)
'a duck goes into a bar...     '
# 오른쪽 배치
>>> setup.rjust(30)
'     a duck goes into a bar...'
```



### 2.3.13 대체하기: `replace()`

인자로 바꿀 문자열, 대체할 새 문자열, 바꿀 문자열에 대한 횟수를 입력한다. 마지막 인자를 생략하면 첫 번째 인스턴스만 바꾼다.

```python
>>> setup.replace('duck', 'marmoset')
'a marmoset goes into a bar...'
>>> setup.replace('a ', 'a famous ', 100)
'a famous duck goes into a famous bar...'
>>> setup.replace('a', 'a famous', 100)
'a famous duck goes into a famous ba famousr...'
```

