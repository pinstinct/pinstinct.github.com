---
layout: post
title: Codecademy Python
categories: wps
tags: [Codecademy, basic, python, code]
toc: true
---


## A. String

### 1. String Method
dot notation 은 오직 strings 형에서만 동작한다. 바꿔말하면 len(), str()은 data 형에서도 동작한다는 뜻이다.

```python
your_string = abc
pi = 3.14

len(your_string)
your_string.lower()
your_string.upper()
str(pi)
```


### 2. String Formatting with %

```python
name = raw_input("What is your name?")
quest = raw_input("What is your quest?")
color = raw_input("What is your favorite color?")

print "Ah, so your name is %s, your quest is %s, and your favorite color is %s." % (name, quest, color)
```

### 3. Date and Time

```python
from datetime import datetime
now = datetime.now()
print '%s/%s/%s %s:%s:%s' % (now.month, now.day, now.year, now.hour, now.minute, now.second)
```

## B. Function

### 1. Function Junction

1. header
2. comment
3. body

```python
def square(n):	# header
	""" print to the console """
	print "Eggs!"	#body
```

### 2. Parameter & argument

```python
def square(n):	# parameter
	"""Returns the square of a number."""
	squared = n**2
	print "%d squared is %d." % (n, squared)
	return squared

square(10)	# argument
```

### 3. Importing Modules

#### 1. Generic Import

```python
import math
print math.sqrt(25)
```

#### 2. Fuction Improt (with **From** keyword)

```python
from math import sqrt
print sqrt(25)
```

#### 3. Universal Import

```python
from math import *
print sqrt(25)
```

#### 4. 사용가능한 모듈 확인

```python
import math            # Imports the math module
everything = dir(math) # Sets everything to a list of things from math
print everything       # Prints 'em all!
```

### 4. Built-in Function

#### 1. max()

```python
maximum = max(1, 2.456, 0)
print maximum
```

#### 2. min()

```python
minimum = min("test", 1, 0, -1)
print minimum
```

#### 3. abs()

```python
absolute = abs(-42)
print absolute
```

#### 4. type()

```python
print type(1)
print type(3.12)
print type("python")

""" Result
<type 'int'>
<type 'float'>
<type 'str'> """
```

## C. Lists & Dictionaries

### 1. List Function

#### 1.  append(), slice

```python
suitcase = []
suitcase.append("sunglasses")

# append()
suitcase.append("tie")
suitcase.append("pen")
suitcase.append("paper")

# length
list_length = len(suitcase)

print "There are %d items in the suitcase." % (list_length)
print suitcase

# Slice
first  = suitcase[0:2]
last = suitcase[2:]
```

#### 2. index(), insert()

```python
animals = ["aardvark", "badger", "duck", "emu", "fennec fox"]

# index(item)
duck_index = animals.index("duck")

# insert(index, item)
animals.insert(duck_index, "cobra")

print animals
""" Result
['aardvark', 'badger', 'cobra', 'duck', 'emu', 'fennec fox'] """
```

#### 3. for loop

```python
my_list = [1,9,3,8,5,7]

# for VARIABLE_NAME in LIST_NAME:
for number in my_list:
    print 2 * number
```

#### 4. sort()

```python
start_list = [5, 3, 1, 2, 4]
square_list = []

for x in start_list:
    square_list.append(x**2)

""" NOTE!
.sort() modifies the list rather than returning a new list. """
square_list.sort()
print square_list
```

### 2. Dictionaries

#### 1. del

```python
# key - animal_name : value - location
zoo_animals = { 'Unicorn' : 'Cotton Candy House',
'Sloth' : 'Rainforest Exhibit',
'Bengal Tiger' : 'Jungle House',
'Atlantic Puffin' : 'Arctic Exhibit',
'Rockhopper Penguin' : 'Arctic Exhibit'}

# del dictionry_name[key_name]
del zoo_animals['Unicorn']
del zoo_animals['Sloth']
del zoo_animals['Bengal Tiger']

zoo_animals['Rockhopper Penguin'] = 'LA'

print zoo_animals
```

#### 2.  vs  remove, pop

```python
backpack = ['xylophone', 'dagger', 'tent', 'bread loaf']

# del(list_name[index])
del(backpack[1])
# remove(item)
backpack.remove("dagger")
# pop(index)
backpack.pop(0)
```

#### 3. Unordered

```python
webster = {
	"Aardvark" : "A star of a popular children's cartoon show.",
    "Baa" : "The sound a goat makes.",
    "Carpet": "Goes on the floor.",
    "Dab": "A small amount."
}

""" NOTE
dictionaries are unordered, meaning that any time you loop through a dictionary, you are not guaranteed to get them in any particular order.
"""
for a in webster:
    print webster[a]
```

#### 4.  같은 키

```python
prices = {
    "banana": 4,
    "apple": 2,
    "orange": 1.5,
    "pear": 3}

stock = {
    "banana": 6,
    "apple": 0,
    "orange": 32,
    "pear": 15
    }

# prices and stock dictionary have the same keys
for a in prices:
    print a
    print "price: " + str(prices[a])
    print "stock: " + str(stock[a])
```

## D. Lists and Functions

### 1. range

```python
range(6) # => [0,1,2,3,4,5]
range(1,6) # => [1,2,3,4,5]
range(1,6,3) # => [1,4]

"""
range() function returns a list of numbers
"""
```

### 2. Two ways of  iterating

#### 1. Method 1

```python
for item in list:
    print item
```

#### 2. Method 2

```python
for i in range(len(list)):
    print list[i]
```

### 3. join

```python
letters = ['a', 'b', 'c', 'd']
print " ".join(letters) #a b c d
print "---".join(letters) #a---b---c---d
```

### 4. randint

```python
from random import randint
coin = randint(0, 1)
dice = randint(1, 6)
```

### 5. raw_input

```python
number = raw_input("Enter a number: ")
if int(number) == 0:
    print "You entered 0"
```

## E. Loops

### 1. while loops

```python
count = 0
while count < 10:
    print "Hello, I am a while and count is", count
    count += 1
```

#### while/else

if/else와 달리 loop 조건문이 "False"로 평가되면, else block은 실행된다.

```python
from random import randint

# Generates a number from 1 through 10 inclusive
random_number = randint(1, 10)

guesses_left = 3
# Start your game!
while guesses_left > 0:
    guess = int(raw_input("Your guess: "))
    if guess == random_number:
        print "You win!"
        break
    guesses_left = guesses_left - 1
else:
    print "You lose."
```

### 2. for loops

```python
for i in range(20):
    print i
```

```python
word = "Marble"
for char in word:
    print char,
    # The , character after our print statement means that our next print statement keeps printing on the same line.
```

#### enumerate

```python
choices = ['pizza', 'pasta', 'salad', 'nachos']

print 'Your choices are:'
for index, item in enumerate(choices):
    print index + 1, item
```

#### zip

```python
list_a = [3, 9, 17, 15, 19]
list_b = [2, 4, 8, 10, 30, 40, 50, 60, 70, 80, 90]

"""
zip will create pairs of elements when passed two lists, and will stop at the end of the shorter list.
zip can handle three or more lists as well
"""
for a, b in zip(list_a, list_b):
    if a > b:
        print a
    elif a < b:
        print b
    else:
        print 0
```

## F. Advanced

### 1. Dictionaries

**Dictionaries have no specific order!!!**

```python
"""  items() function doesn't return key/value pairs in any specific order. """
my_dict = {
    "name" : "kim",
    "age" : 20,
    "BDFL" : True
    }

print my_dict.items()  # [('BDFL', True), ('age', 20), ('name', 'kim')]
print my_dict.keys()  # ['BDFL', 'age', 'name']
print my_dict.values()  # [True, 20, 'kim']

""" BDFL True
age 20
name kim
"""
for key in my_dict:
    print key, my_dict[key]

```

### 2. list comprehension

 List comprehensions are a powerful way to generate lists using the **for/in and if** keywords.

```python
even_squares = [x*x for x in range(1, 12) if x % 2 == 0]
print even_squares  # [4, 16, 36, 64, 100]
```

### 3. list slicing

```python
""" start describes where the slice starts (inclusive),
end is where it ends (exclusive),
stride describes the space between items in the sliced list
"""
list[start:end:stride]

# Reversing a List
my_list = range(1, 11)

backwards = my_list[::-1]
print backwards
```

### 4. Lambdas

* 함수 이름을 지정할 필요가 없다.
* 그래서 익명함수라고 불린다.
* filter를 사용할 때, 첫 번째 인수는 필터링 대상, 두 번재 인수는 범위다.

```python
my_list = range(16)
print filter(lambda x: x % 3 == 0, my_list)
# [0, 3, 6, 9, 12, 15]
```

```python
squares = [x*x for x in range(1, 11)]
print filter(lambda x: 30 <= x <= 70, squares)
```

### 5. Bitwise

* 숫자를 `0b` 로 시작하면 이진형식이 된다.

```python
print 0b1,    #1
print 0b10,   #2
print 0b11,   #3
print 0b100  #4
```

#### bin()
```python
# integer를 입력하면 2진 표현을 문자열로 리턴
print bin(1)
print oct(1)  # 8진 표현
print hex(1)  # 16진 표현
```

#### int("number_string", x)

x진수의 number_stirng을 10진수로 변경한다.

```python
print int("1",2)  # 1
print int("10",2)  # 2
print int("111",2)  # 7
print int("0b100",2)  # 4
print int(bin(5),2)  # 2
```

#### Operator

##### shift

정수에 대한 비트 연산만 실행할 수 있다.

```python
# Left Bit Shift (<<)
0b000001 << 2 == 0b000100  # (1 << 2 = 4)
0b000101 << 3 == 0b101000  # (5 << 3 = 40)

# Right Bit Shift (>>)
0b0010100 >> 3 == 0b000010  # (20 >> 3 = 2)
0b0000010 >> 2 == 0b000000  # (2 >> 2 = 0)
```

##### and

- 같은 자리수 숫자를 비교한다.
  - 0 & 0 = 0
  - 0 & 1 = 0
  - 1 & 0 = 0
  - 1 & 1 = 1
- 두 값 중 작은 값 보다 작거나 같은 숫자만 반환된다.

```python
print bin(0b1110 & 0b101)
```

##### or

- 같은 자리수 숫자를 비교한다.
  - 0 &#124; 0 = 0
  - 0 &#124; 1 = 1 
  - 1 &#124; 0 = 1
  - 1 &#124; 1 = 1
- 두 값 중 큰 값 보다 크거나 같은 숫자만 반환된다.

```python
print bin(0b1110 | 0b101)
```

#####  xor

- 같은 자리수 숫자를 비교한다.
  - 0 ^ 0 = 0
  - 0 ^ 1 = 1
  - 1 ^ 0 = 1
  - 1 ^ 1 = 0

```python
print bin(0b1110 ^ 0b101)
```

##### not

숫자 하나를 더한 후 음수로 만드는 것.

```python
print ~1  # -2
print ~2  # -3
print ~3  # -4
print ~42  #-43
print ~123  # -124
```

##### mask

비트 마스크는 특정 비트를 켜거나, 다른 비트를 끄거나, 비트가 켜지거나 꺼지는 데이터를 수집하는데 도움이 된다.

```python
def check_bit4(num):
    mask = 0b1000
    desired = num & mask

    if desired > 0:
        return "on"
    else:
        return "off"

print check_bit4(0b1000)
```

## G. Class

- 객체지향 프로그래밍이다.

- an object = single data structure

  - methods
  - attributes



```python
# 사용자 정의 class의 이름은 대문자로 시작
class NewClass(object):
    pass  # placeholder
```

### 1. Basics

#### `__init__(self)`

* 객체를 초기화 하기 위해 사용한다.
* 항상 `self`라는 적어도 한 개의 인수를 취한다.
  * `self`자체는 특별한 것이 없다.
  * `self`를 사용하는 것은 다른 사람들이 내 코드를 이해하기 위한 규칙이다.
* 특별한 것은 `__init__()`의 첫 번째 파라미터이다.
  * 첫 번째 파라미터가 생성된 객체를 가르킨다.
  * 그렇기 때문에 `self`라고 불리는 것이다. 이 파라미터가 생성되는 객체에 identity를 부여하기 때문이다.
* 클래스가 생성하는 각 객체를 "부팅"하는 함수라고 생각할 수 있다.

```python
class Animal(object):

    def __init__(self):
        pass

```

#### Instance

```python
class Animal(object):
    """ __init__의 first argument는 instance를 가르킨다.
    additional arguments는 Animal class instance를 가르킨다.
    """
    def __init__(self, name):
        self.name = name


zebra = Animal("Jeffrey")
print zebra.name
```

#### Scope

```python
class Animal(object):

    is_alive = True
    health = "good"

    def __init__(self, name, age):
        self.name = name
        self.age = age
    # Add your method here!
    def description(self):
        print self.name
        print self.age

hippo = Animal("Tim", 2)
sloth = Animal("Kim", 4)
ocelot = Animal("Lee", 10)

hippo.description()
print hippo.health
print hippo.health
print ocelot.health
```

#### Inheritance

**is-a** relationship. For example, a Panda **is a** bear, so a Panda class could inherit from a Bear class. 

```python
class Customer(object):

    def __init__(self, customer_id):
        self.customer_id = customer_id

    def display_cart(self):
        print "I'm a string that stands in for the contents of your shopping cart!"


class ReturningCustomer(Customer):

    def display_order_history(self):
        print "I'm a string that stands in for your order history!"

monty_python = ReturningCustomer("ID: 12345")
monty_python.display_cart()
monty_python.display_order_history()
```

#### override

calculate_wage_underline 메소드를 만드는 것보다 calculate_wage를 override하는 것이 좋다.

```python
class Employee(object):

    def __init__(self, employee_name):
        self.employee_name = employee_name

    def calculate_wage(self, hours):
        self.hours = hours
        return hours * 20.00


class PartTimeEmployee(Employee):

    def calculate_wage(self, hours):
        return hours * 12.00

    # super() : superclassd의 메소드나 속성에 접근
    def full_time_wage(self, hours):
        return super(PartTimeEmployee, self).calculate_wage(hours)


kim = Employee("kim")
milton = PartTimeEmployee("lee")

print kim.calculate_wage(8)
print milton.calculate_wage(8)
print milton.full_time_wage(8)
```

## H. File Input/Output

### open()

- w : write-only mode
- r : read-only mode
- r+ : read and write mode
- a : append mode

```python
# open "output.txt" in "w" mode
my_file = open("output.txt", "r+")
```

### write()

- string을 인수로 취한다.
- 파일을 닫아야한다.

```python
my_list = [i**2 for i in range(1,11)]
my_file = open("output.txt", "r+")

for i in my_list:
    my_file.write(str(i) + "\n")

my_file.close()
```

### reading()

```python
my_file = open("output.txt", "r")
print my_file.read()

my_file.close()
```

### readline()

```python
my_file = open("text.txt", "r")
print my_file.readline()
my_file.close()
```

### `with`, `as` keyword

- 파일을 자동으로 close하는 방법
  - file object는 `__enter__()`와 `__exit__()`메소드가 있다.
  - `__exit__()`가 호출될 때, 자동으로 파일을 닫는다.
  - `with`, `as` 키워드와 함께 `__exit__()`메소드를 호출할 수 있다.

```python
with open("text.txt", "w") as textfile:
	textfile.write("Success!")
```

### closed 속성

- file object는 closed라는 속성을 가지고 있다.
- my_file.closed 값은 True or False로 반환된다.
  - True : 파일이 닫혀있다.
  - False : 파일이 닫혀있지 않다.

```python
with open("text.txt", "w") as my_file:
    my_file.write("wow, amazing")

if my_file.closed == False:
    my_file.close()

print my_file.closed
```
