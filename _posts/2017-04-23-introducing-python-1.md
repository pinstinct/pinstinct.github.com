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

시퀀스 (Sequence) 타입 : list, tuple, range, binary data, text strings

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

