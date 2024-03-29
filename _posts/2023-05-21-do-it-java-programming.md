---
layout: post
title: 자바 프로그래밍 입문
categories: book
tags: [book, software, java]
excerpt: 책을 읽고 정리한 내용입니다.
---

[Do it! 자바 프로그래밍 입문](http://www.yes24.com/Product/Goods/62281686) 책을 읽고 정리한 내용입니다.

## 2. 변수와 자료형 

- 변수는 컴퓨터 내부의 메모리 공간에 저장된다.
- 즉 변수를 선언한다는 것은 선언한 변수 이름으로 어떤 위치에 있는 메모리를 얼마만큼의 크기로 사용하겠다 뜻이다.
- 자바에서 제공하는 자료형은 기본자료형과 참조자료형(클래스형)이 있다.
- 기본자료형은 자바 라이브러리에서 기본으로 제공 (byte, boolean, short, char, int, float, long, double) 
- 문자는 항상 작은따옴표를 사용한다. 문자를 여러개 이은 문자열 큰따옴표를 사용하고 기본 자료형으로 표현할 수 없다.
- 문자열의 끝에는 항상 널 문자('\0')가 있다. 자바에서 문자열을 다룰 때 String 클래스를 사용한다.
- 자료형 없이 변수 선언하기 
    ```java
    package chapter2;

    public class TypeInference {

        public static void main(String[] args) {
            /*
            * 자료형 없이 변수 선언하기(자바 10부터 생긴 문법)
            * 지역 변수 자료형 추론(local variable type inference)
            * 지역 변수만 가능
            * */
            var i = 10;  // int i = 10으로 컴파일 됨
            var j = 10.0;  // double j = 10.0으로 컴파일 됨
            var str = "hello";  // String str = "hello"로 컴파일 됨
            
            System.out.println(i);
            System.out.println(j);
            System.out.println(str);
            
            str = "test";
            //str = 3;  // 에러, str 변수는 String형으로 먼저 사용되었기 때문에 정수 값을 넣을 수 없음 
        }

    }

    ```

- 변하지 않는 값 상수(constant)를 자바에서 `final` 예약어를 사용해 선언한다. 상수 이름은 대문자를 주로 사용한다.
- 리터럴은 오른쪽에 사용한 문자와 숫자, 논리값을 일컫는 말이다. 리터럴은 프로그램이 시작할 때 시스템에 같이 로딩되어 특정 메모리 공간인 상수 풀(constant pool)에 놓인다.
- 끊임없이 돌아가는 시스템을 데몬(daemon)이라고 부른다.

## 4. 제어 흐름 이해하기
- continue문은 반복문을 계속 수행하지만 특정 조건에서 수행문을 생략하는 경우에 사용하고, break문은 반복문을 더 이상 수행하지 않고 빠져나올 때 사용한다.

## 5. 클래스와 객체 1
- 객체 지향 프로그램은 먼저 객체를 만들고 객체 사이에 일어나는 일을 구현하는 것이다.
- 클래스
  - 클래스 이름은 대문자
  - 멤버 변수: 클래스 속성(변수로 선언)
  - 멤버 변수를 선언할 때 int형, double형 같은 기본 자료형(primitive data type)으로 선언할 수도 있고, 또 다른 클래스형으로 선언할 수도 있다. 클래스형이란 다른말로 객체 자료형 또는 참조 자료형이라고 한다. 참조 자료형으로 사용하는 클래스는 String, Date와 같이 이미 JDK에서 제공하는 것일 수도 있고, 개발자가 직접 만든 Student나 Person 같은 클래스가 다른 클래스에서 사용하는 멤버 변수의 자료형이 될 수도 있다.
  - 멤버 함수 또는 메서드: 클래스 내부에서 멤버 변수를 사용하여 클래스를 구현한 것
  - 패키지란 클래스 파일의 묶음
  - 클래스 이름이 같다고 해도 패키지 이름이 다르면 클래스 전체 이름이 다른 것이므로 다른 클래스가 됩니다.
- 메서드
  - `void`: 반환할 값이 없을 때 사용하는 예약어
  - `return;`: 함수 수행을 종료하는 목적일 때는 return 뒤에 반환값을 적지 않는다.
  - 함수 호출과 스택 메모리
    - 함수를 호출하면 그 함수만을 위한 메모리 공간이 할당되는데, 이 메모리 공간을 스택(stack)이라고 부른다.
    - 함수 내부에서만 사용하는 변수를 지역 변수라고 한다. 지역 변수는 스택 메모리에 생성된다.
- 클래스와 인스턴스
  - main()함수는 자바 가상머신(JVM)이 프로그램을 시작하기 위해 호출하는 함수입니다.
  - 자바에서 클래스를 생성할 때는 `new` 예약어로 클래스를 생성한다.
    - `Student kim = new Student();`
    - 클래스가 생성된다는 것은 클래스를 실제 사용할 수 있도록 메모리 공간(힙 메모리)을 할당 받는다는 뜻이다. 이렇게 실제로 사용할 수 있도록 생성된 클래스를 '인스턴스'라고 한다. 그리고 인스턴스를 가리키는 클래스형 변수를 '참조 변수(스택 메모리에 할당, `kim`)'라고 한다.
    - 힙 메모리란? 프로그램에서 사용하는 동적 메모리(dynamic memory) 공간을 말한다. 일반적으로 프로그램은 스택, 힙, 데이터 이렇게 세 영역을 사용하는데, 객체가 생성될 때 사용하는 공간이 힙이다. 힙은 동적으로 할당되며 사용이 끝나면 메모리를 해제해 주어야 한다. 자바에서는 가비지 컬렉터(GC)가 자동으로 메모리를 해제해 준다.
    - 클래스가 생성될 때마다 인스턴스는 다른 메모리 공간을 차지한다.
    - 참조 변수에는 힙 메모리에 생성된 인스턴스의 메모리 주소(참조 값)가 저장된다. 주소 값은 다른 말로 해시코드(hash code) 값이라고도 한다.
  - 참조 변수를 사용하면 인스턴스의 멤버 변수와 메서드를 참조할 수 있다. 이 때 `.` 연산자를 사용한다.
- 생성자(constructor)
  - 디폴트 생성자: 생성자 이름은 클래스 이름과 같고, 반환값이 없다. 생성자가 없는 클래스는 클래스 파일을 컴파일할 때 자바 컴파일러에서 자동으로 생성자를 만들어 준다.

    ```java
    package chapter2;

    public class Person {
        String name;
        float height;
        float weight;
        
        // public Person() {}  // 디폴트 생성자: 자바 컴파일러가 자동으로 제공
    }

    ```

  - 생성자 만들기: 인스턴스가 생성됨과 동시에 멤버 변수의 값을 지정하고 인스턴스를 초기화하기 위해 생성자를 직접 구현해 사용한다. 자바 컴파일러는 생성자가 하나도 없는 경우에만 디폴트 생성자를 제공한다.

    ```java
    package chapter2;

    public class Person {
        String name;
        float height;
        float weight;

        public Person() {}
        
        public Person(String name) {
            this.name = name;
        }
    }

    ```
  - 생성자 오버로드: 메서드 이름이 같고 매개변수만 다른 경우를 '오버로드'라고 한다. 클래스에 생성자가 두 개 이상 제공되는 경우를 생성자 오버로드라고 한다.
  - 어떤 멤버 변수들은 외부 클래스에서 값을 지정하지 못하는 경우도 있다. 따라서 매개변수가 있는 생성자를 구현하고 이를 사용하는 것이 편리한 경우가 많다.
- 참조 자료형
  - 크기가 정해진 기본 자료형(int, char, float, double 등)으로 선언하는 변수가 있고, 클래스 자료형으로 선언하는 참조 자료형 변수가 있다. 
  - 참조 자료형은 프로그래머가 필요에 의해 만든 클래스를 사용할 수도 있고, JDK에서 제공하는 클래스를 사용할 수도 있다.
- 정보 은닉
  - 접근 제어자(access modifier)
    - public: 외부 클래스 어디에서나 접근할 수 있다.
    - protected: 같은 패키지 내부와 상속 관계의 클래스에서만 접근할 수 있고 그 외 클래스에서는 접근할 수 없다.
    - 아무것도 없는 경우: default이며 같은 패키지 내부에서만 접근할 수 있다.
    - priavate: 같은 클래스 내부에서만 접근할 수 있다.
  - get(), set() 메서드
    - private으로 선언한 변수를 사용할 수 있도록 public 메서드를 제공해야 한다. 이때 사용할 수 있는 것이 get(), set() 메서드이다. 즉 외부 클래스에서 private 변수에 직접 접근할 수 는 없지만, public 메서드를 통하면 private 변수에 접근할 수 있다.
  - 멤버 변수를 public으로 선언하면 접근이 제한되지 않으므로 정보의 오류가 발생할 수 있습니다. 이런 경우에는 오류가 나더라도 그 값이 해당 변수에 대입되지 못하도록 변수를 private으로 바꾸고 public 메서드를 별도로 제공해야 한다. 이것을 객체 지향에서는 정보은닉(information hiding)이라고 한다. 자바에서는 접근 제어자를 사용하여 정보 은닉을 구현한다. 필요한 경우에는 private으로 선언하여 오류를 막을 수 있다.
    ```java
    package chapter2;

    public class MyDate {
        private int day;
        private int month;
        private int year;
        
        public void setDay(int day) {
            if(month == 2) {
                if (day < 1 || day > 28) {
                    System.out.println("오류입니다.");
                } else {
                    this.day = day;
                }
            }
        }    
    }

    ```

## 6. 클래스와 객체 2

- `this` 예약어: 생성된 인스턴스 스스로를 가리키는 예약어
- `static`: 클래스에서 공통으로 사용하는 변수를 'static 변수'로 선언한다.
  - static 변수란 다른 용어로 '정적 변수'라고 한다. 혹은 '클래스 변수(class variable)'라고 한다.
  - static 변수는 클래스 내부에 선언하지만, 다른 멤버 변수처럼 인스턴스가 생성될 때마다 새로 생성되는 변수가 아니다. static 변수는 프로그램이 실행되어 메모리에 올라갔을 때 딱 한번 메모리 공간이 할당된다. 그리고 그 값은 모든 인스턴스가 공유한다.
  - 데이터 영역(static 변수) <- 스택 메모리(참조 변수1, 참조 변수 2, ...) -> 힙메모리(인스턴스A, 인스턴스A, ...)
  - static 변수는 같은 클래스에서 생성된 인스턴스들이 같은 값을 공유할 수 있으므로, 인스턴스 간에 공통으로 사용할 값이 필요한 경우 유용하게 사용할 수 있다.
  - static 변수는 인스턴스가 아닌 클래스 이름으로도 참조하여 사용할 수 있다. (`Student.number`)
  - 클래스 메서드: static 메서드를 위한 메서드를 'static 메서드' 또는 '클래스 메서드'라고 한다. 클래스 메서드 내부에서는 인스턴스 변수를 사용할 수 없다.
- 변수 유효 범위
  - 지역 변수 = 로컬 변수: 함수나 메서드 안에서만 사용
    - 스택 메모리에 생성, 함수가 호출될 때 생성되고 함수가 반환되면 메모리 공간이 해제
  - 멤버 변수 = 인스턴스 변수: 클래스 안에서 사용
    - 힙 메모리에 생성, 가비지 컬렉터에 의해 수거
  - static 변수 = 클래스 변수: 여러 인스턴스에서 공통으로 사용
    - 데이터 영역에 생성, 데이터 영역은 프로그램이 실행될 때 메모리에 상주한다. 이 영역에는 상수나 문자열, static 변수가 생성된다. static 변수는 프로그램이 시작할 때부터 끝날 때까지 메모리에 상주하므로 너무 큰 변수를 static으로 선언하는 것은 좋지 않다.
- static 응용(싱글톤 패턴)
  - 객체 지향 프로그램에서 인스턴스를 단 하나만 생성하는 디자인 패턴을 싱글톤 패턴(singleton pattern)이라고 한다. 살펴볼 싱글톤 패턴은 static을 응용하여 프로그램 전반에서 사용하는 인스턴스를 하나만 구현하는 방식이다.
  - 예: 회사 객체 하나에 직원은 여러 명
    ```java
    package chapter2;

    public class Company {
        
        /*
        * 2. 
        * 클래스 내부에 static으로 유일한 인스턴스 생성 (프로그램 전체에서 유일한 인스턴스)
        * 외부에서 인스턴스에 접근하지 못하도록 private으로 선언
        */
        private static Company instance = new Company();
        
        /*
        * 1
        * 접근 제어자가 private인 생성자 추가
        * 외부 클래스에서 마음대로 Company 인스턴스를 생성할 수 없게 됨
        * 즉, Company 클래스 내부에서만 이 클래스 생성을 제어할 수 있음 
        */
        private Company() {}

        /*
        * 3.
        * 외부에서 참조할 수 있는 public 메서드 만들기
        * 유일한 인스턴스를 외부에서도 사용할 수 있도록 public 메서드 생성 
        * 이때 static으로 선언해야 getInstance() 메서드를 인스턴스 생성과 상관없이 호출 가능
        */
        public static Company getInstance() {
            if (instance == null) {
                instance = new Company();
            }
            return instance;
        } 
        

    }

     ```

## 7. 배열과 ArrayList
- 배열이란?
  - 배열 선언 예: `int[] studentIds = new int[10];`
    - 배열을 선언하면 선언한 자료형과 배열 길이에 따라 메모리가 할당
    - 배열의 물리적 위치와 논리적 위치는 같다.
    - 인덱스 연산자(`[]`): 배열 요소가 저장된 메모리 위치를 찾아주는 역할
  - 객체 배열 선언하기: 참조 자료형 변수도 여러 개를 배열로 사용할 수 있다.
    - 예: `Book[] library = new Book[5];`
    - 위의 예시는 Book 인스턴스 5개를 생성하는 것이 아니다. Book 인스턴스 주소 값을 담을 공간 5개를 생성하는 것인다.
  - 배열 복사하기 
    - 기존 배열과 배열 길이가 같거나 더 긴 배열을 만들고 for문을 사용하여 각 요소 값을 반복해서 복사하는 방법
    - `System.arraycopy(src, srcPos, dest, destPos, length)` 메서드 사용
    - src: 복사할 배열 이름
    - srcPos: 복사할 배열의 첫 번째 위치
    - dest:  복사해서 붙여넣을 대상 배열 이름
    - destPos: 복사해서 대상 배열에 붙여 넣기를 시작할 첫 번째 위치
    - length: src에서 dest로 자료를 복사할 요소 개수
  - 얕은 복사(shallow copy): 주소 값만 복사
    - 객체 배열의 요소에 저장된 값은 인스턴스 자체가 아니고 인스턴스의 주소 값이다. 따라서 객체 배열을 복사할 때 인스턴스를 따로 생성하는 게 아니라 기존 인스턴스이 주소 값만 복사한다. 결국 같은 인스턴스를 가리키고 있으므로 복사되는 배열의 인스턴스 값이 변경되면 두 배열 모두 영향을 받는다.
  - 깉은 복사(deep copy): **객체 배열을 복사하면 항상 인스턴스 주소**가 복사된다. 인스턴스를 따로 관리하고 싶다면 직접 인스턴스를 만들고 그 값을 복사한다. 이를 깊은 복사라 한다.
  
  ```java
  package array;

  public class ObjectCopy {

        public static void main(String[] args) {
            Book[] bookArray1 = new Book[3];
            Book[] bookArray2 = new Book[3];
            
            bookArray1[0] = new Book("태백산맥", "조정래");
            bookArray1[1] = new Book("데미안", "헤르만 헤세");
            bookArray1[2] = new Book("어떻게 살 것인가", "유시민");
            
            bookArray2[0] = new Book();
            bookArray2[1] = new Book();
            bookArray2[2] = new Book();
            
            for (int i = 0; i < bookArray1.length; i++) {
                bookArray2[i].setName(bookArray1[i].getName());
                bookArray2[i].setAuthor(bookArray1[i].getAuthor());
            }
            
            for (int i = 0; i < bookArray2.length; i++) {
                bookArray2[i].showBookInfo();
            }
            
            bookArray1[0].setName("나목");
            bookArray1[0].setAuthor("박완서");
            
            System.out.println("=== bookArray1 ===");
            for (int i = 0; i < bookArray1.length; i++) {
                bookArray1[i].showBookInfo();
            }
            System.out.println("=== bookArray2 ===");
            for (int i = 0; i < bookArray2.length; i++) {
                bookArray2[i].showBookInfo();
            }
        }
    }

  ```

- 향상된 for문과 배열
  - `for (변수 : 배열)`
  - 자바 5부터 제공되는 향상된 for문(enhanced for loop)은 배열의 처음부터 끝까지 모든 요소를 참조할 때 사용하면 편리한 반복문이다. 

    ```java
    package array;

    public class EnhancedForLoop {

        public static void main(String[] args) {

            String[] strArray = {"java", "python", "C", "javascript", "android"};
            
            for (String lang : strArray) {
                System.out.println(lang);
            }
        }
    }

    ```

- ArrayList 클래스 사용하기
  - 기존 배열은 프로그램에서 사용하려면 항상 배열 길이를 정하고 시작한다. 그래서 자바는 객체 배열을 좀 더 쉽게 사용하도록 객체 배열 클래스 ArrayList를 제공한다.
  - ArrayList 외에도 배열을 쉽게 사용할 수 있도록 제공되는 클래스가 있지만 효율이 가장 높기 때문에 많이 사용한다.
  - ArrayList 주요 메서드
    - boolean add(E e): 요소 하나를 배열에 추가
    - int size(): 배열에 추가된 요소 전체 개수 반환
    - E get(int index): 배열의 index 위치에 있는 요소 값 반환
    - E remove(int index): 배열의 index 위치에 있는 요소 값을 제거하고 그 값을 반환
    - boolean isEmpty(): 배열이 비어 있는지 확인
  - 기본형식: `ArrayList<E> 배열이름 = new ArrayList<E>();`
    - `<E>`와 같은 형태를 제네릭(generic) 자료형이라고 한다.
    - `< >`안에 사용할 객체의 자료형을 쓰면 된다.


## 8. 상속형과 다형성
- 상속(inheritance)이란?
  - B 클래스가 A 클래스를 상속받으면 B클래스는 A 클래스의 멤버 변수와 메서드를 사용할 수 있다.
  - 자바 문법으로 상속을 수현할 때는 `extends` 예약어를 사용
  - 하위 클래스에서 private 변수를 수정하려고 하면 오류가 발생한다. 상위 클래스에 작성한 변수나 메서드 중 외부 클래스에서 사용할 수 없지만 하위 클래스에서는 사용할 수 있도록 지정하는 예약어가 바로 `protected`이다. 상속받은 하위 클래스에서는 public처럼 사용할 수 있다. 즉 protected는 상속된 하위 클래스를 제외한 나머지 외부 클래스에서는 private과 동일한 역할을 한다.
- 상속에서 클래스 생성과 형 변환
  - 하위 클래스가 생성될 때는 상위 클래스의 생성자가 먼저 호출된다. 그리고 상위 클래스 생성자가 호출 될 때 상위 클래스의 멤버 변수가 메모리에 생성된다.
  - `super` 예약어: 하위 클래스에서 상위 클래스로 접근할 때 사용한다. 하위 클래스는 상위 클래스의 주소, 즉 참조 값을 알고 있다. 이 참조 값을 가지고 있는 예약어가 바로 super이다.
  - 하위 클래스 생성자만 호출했는데 상위 클래스 생성자가 호출되는 이유는 하위 클래스 생성자에서 `super()`를 자동으로 호출하기 때문이다. `super()`를 호출하면 상위 클래스의 디폴트 생성자가 호출된다.
  ```java
  // ...
  public BClass() {  // 하위 클래스 생성자
    // super();  // 컴파일러가 자동으로 추가하는 코드
    customerGrade = "VIP";
    bonusRatio = 0.05;
  }
  ```
  - 명시적으로 상위 클래스 생성자 호출하기
  ```java
  // ...
  public BClass() {
    super(customerId, CustomerName);  // 상위 클래스 생성자 호출
    customerGrade = "VIP";
    bonusRatio = 0.05;
  }
  ```
  - 업캐스팅(upcasting)
    - `AClass variable = new Bclass();`
    - 모든 하위 클래스는 상위 클래스 자료형으로 형변환 될 수 있다.
    - 형 변환된 `varialbe`은 AClass의 멤버 변수와 메서드에만 접근할 수 있다.
      - 만약 하위 클래스에서 오버라이딩한 메서드가 있다면, 해당 메서드는 BClass의 메서드를 호출한다. (가상 메서드)
- 메서드 오버라이딩
  - 상위 클래스에 정의한 메서드가 하위 클래스에서 구현할 내용과 맞지 않을 경우에 하위 클래스에서 이 메서드를 재정의 할 수 있습니다. 이를 메서드 오버라이딩(method overriding)이라고 한다. 오버라이딩을 하려면 반환형, 메서드 이름, 매개 변수 개수, 매개변수 자료형이 반드시 같아야 합니다.
  - `@Override` 애노테이션은 '이 메서드는 재정의된 메서드'라고 컴파일러에 명확히 알려주는 역할을 한다.
    - 자바에서 애노테이션은 컴파일러에게 특정한 정보를 제공해 주는 역할을 한다.
    - `@FuctionalInterface`: 함수형 인터페이스라는 정보 제공
    - `@Deprecated`: 이후 버전에서 사용되지 않을 수 있는 변수, 메서드에 사용
    - `@SuppressWarnings`: 특정 경고가 나타나지 않도록 함
  - 상속에서 상위 클래스와 하위 클래스에 **같은 이름의 메서드가 존재할 때** 호출되는 메서드는 인스턴스에 따라 결정된다. 다시 말해 선언한 클래스형이 아닌 **생성된 인스턴스의 메서드**를 호출하는 것이다. 이렇게 인스턴스의 메서드가 호출되는 기술을 '가상 메서드(virtual method)'라고 한다.
    - 자바의 모든 메서드는 가상 메서드다.
    - 가상 메서드의 경우에는 '가상 메서드 테이블'이 만들어진다. 가상 메서드 테이블은 각 메서드 이름과 실제 메모리 주소가 짝을 이루고 있다. 어떤 메소드가 호출되면 이 테이블에서 주소 값을 찾아서 해당 메서드의 명령을 수행한다.
    - 변수가 사용하는 메모리와 메서드가 사용하는 메모리는 다르다. 변수는 인스턴스가 생성될 때마다 새로 생성되지만, 메서드는 실행해야 할 명령 집합이기 때문에 인스턴스가 달라도 같은 로직을 수행한다. 즉 같은 객체의 인스턴스를 여러개 생성한다고 해서 메서드도 여러개 생성되지 않는다.
    - 메서드의 명령 집합은 메서드 영역(코드 영역)에 위치한다.
- 다형성(polymorphism)
  - 다형성이란 하나의 코드가 여러 자료형으로 구현되어 실행되는 것을 말한다.
  - 다형성은 추상 클래스, 인터페이스에서 구현된다.
  - 상속 관계에 있는 상위 클래스와 하위 클래스는 같은 상위 클래스 자료형으로 선언되어 생성할 수 있지만 재정의된 메서드는 각각 호출될 뿐만 아니라 같은 메서드가 서로 다른 역할을 구현하고 있다. 
- 다형성 활용하기
  - 배열을 함께 사용해 여러 하위 클래스 자료형을 상위 클래스 자료형으로 한꺼번에 관리할 수 있다.
  - 예: 사용할 클래스는 Customer, GoldCustomer, VIPCustomer 세 종류이다.
    - 배열의 자료형을 Customer로 지정하고, VIPCustomer 클래스와 GoldCustomer 클래스 모두 Customer에서 상속받은 클래스이므로 Customer형으로 선언한다. 이렇게 선언하면 이 배열에는 Customer, GoldCustomer, VIPCustomer 모두 사용할 수 있다. 그리고 이 배열에 Customer 하위 클래스 인스턴스가 추가될 때 모두 Customer형으로 묵시적 형 변환이 된다.
    - `ArrayList<Customer> customerList = new ArrayList<Customer>();`
  - 상속과 다형성을 잘 활용하면 복잡한 코드를 간결하게 줄일 수 있고 확장성 있는 프로그램을 구현할 수 있다.
  - 다중 상속: 여러 클래스를 상속받는 것, 자바는 다중 상속을 지원하지 않는다.
    - 다중 상속으로 인한 모호성때문이다. 객체 지향에서 다중 상속의 모호성에 대한 예가 다이아몬드 문제(diamond problem)이다.
- 다운 캐스팅과 instanceof
  - 앞에서 상위 클래스로 형 변환이 묵시적으로 이루어지는 과정을 살펴보았다. 이제부터 다시 하위 클래스로 형 변환이 되는 과정을 살펴보자.
  - 다운 캐스팅(down casting): 상위 클래스로 형 변환되었던 하위 클래스를 다시 원래 자료형으로 형 변환하는 것
  - `instanceof` 예약어: 다운 캐스팅을 하기 전에 상위 클래스로 형 변환된 인스턴스의 원래 자료형을 확인하는 예약어

    ```java
    Animal hAnimal = new Human();
    if (hAnimal instanceof Human) {  // hAnimal 인스턴스 자료형이 Human형인지 확인
      Human human = (Human)hAnimal;  // 명시적으로 자료형을 써야 함
    }
    ```

## 9. 추상 클래스
- 추상 클래스
  - 구체적이지 않은 클래스라는 뜻이다. 추상 클래스를 영어로 표현하면 abstract class이고, 추상 클래스가 아닌 클래스는 concrete class라고 한다. 우리가 지금까지 만든 클래스는 모두 concrete class이다.
  - 추상 클래스는 항상 추상 메서드를 포함한다. 추상 메서드는 구현 코드가 없다.
  - `abstract int add(int x, int y);`
  - 예: Computer 추상 클래스를 상속받아 DeskTop 클래스와 NoteBook 추상 클래스를 만든다. 그리고 NoteBook 추상 클래스를 상속받아 MyNoteBook 클래스를 만든다.

  ```java
    package abstractex;

    public abstract class Computer {

        // 하위 클래스에 따라 구현이 달라질 수 있다.
        // 추상 메서드 구현에 대한 책임을 상속받는 클래스에 위임
        // 하위 클래스마다 다르게 구현할 메서드는 추상 메서드로 선언 
        public abstract void display();
        public abstract void typing();
        
        // 하위 클래스도 공통으로 사용할 메서드는 구현
        public void turnOn() {
            System.out.println("전원을 켭니다.");
        }
        public void turnOff() {
            System.out.println("전원을 끕니다.");
        }
    }

  ```

  ```java
    package abstractex;

    public class DeskTop extends Computer {

        @Override
        public void display() {
            System.out.println("DeskTop display");  // 추상 메서드의 몸체 코드 작성
        }

        @Override
        public void typing() {
            System.out.println("DeskTop typing");  // 추상 메서드의 몸체 코드 작성
        }

    }

  ```

  ```java
    package abstractex;

    public abstract class NoteBook extends Computer {
        // 추상 메서드 중 하나라도 구현하지 않는다면, 추상 메서드를 포함하고 있기 때문에 추상 클래스가 된다.
        @Override
        public void display() {
            System.out.println("NoteBook display()");
        }
    }

  ```

  ```java
    package abstractex;

    public class MyNoteBook extends NoteBook {

        @Override
        public void typing() {
            System.out.println("MyNoteBook typing()");
        }

    }

  ```
  - 추상 클래스는 인스턴스로 생성할 수 없다.

  ```java
    package abstractex;

    public class ComputerTest {

        public static void main(String[] args) {
            // Computer c1 = new Computer();  // 오류
            Computer c2 = new DeskTop();
            // Computer c3 = new NoteBook();  // 오류
            Computer c4 = new MyNoteBook();
        }

    }

  ```
  
- 템플릿 메서드
  - 템플릿 메서드는 디자인 패턴의 한 방법으로, 추상 클래스를 사용하여 구현할 수 있다.

  ```java
    package template;

    public abstract class Car {

        public abstract void drive();
        
        public abstract void stop();
        
        public void startCar() {
            System.out.println("시동을 켭니다");
        }
        
        public void turnOff() {
            System.out.println("시동을 끕니다");
        }
        
        // 템플릿 메서드 
        final public void run() {
            startCar();
            drive();
            stop();
            turnOff();
        }
    }

  ```

  - 템플릿 메서드의 역할은 메서드 실행 순서와 시나리오를 정의하는 것이다. 템플릿 메서드에서 호출하는 메서드가 추상 메서드라면 차종에 따라 구현 내용이 바뀔 수 있다.
  - 상위 클래스를 상속받은 하위 클래스에서 템플릿 메서드를 재정의하면 안된다. 그래서 템플릿 메서드는 `final` 예약어를 사용해 선언한다. final을 사용하면 상속받은 하위 클래스가 메서드를 재정의할 수 없습니다.
- 템플릿 메서드 응용하기
  - [예시 코드](https://github.com/easyspubjava/JAVA_LAB/tree/master/Chapter9/src/gamelevel)
- final 예약어
  - 변수: 상수를 의미
  - 메서드: 하위 클래스에서 재정의할 수 없다.
  - 클래스: 상속할 수 없다.
  - 여러 파일에서 공유하는 상수

  ```java
    package finalex;

    public class Define {
        public static final int MIN = 1;
        public static final int MAX = 99999;
        public static final double PI = 3.14;
        public static final String GOOD = "good!";
    }

  ```

  - 사용할때는 `Define.MIN`과 같이 클래스 이름으로 참조 가능하다.

## 10. 인터페이스
- 인터페이스란?
  - 클래스 혹은 프로그램이 제공하는 기능을 명시적으로 선언하는 역할을 한다.
  - 인터페이스는 추상 메서드와 상수로만 이루어져 있다.

  ```java
    package interfaceex;

    public interface Calc {
        // 인터페이스에서 선언한 변수는 컴파일 과정에서 상수로 변환됨 
        // public static final 예약어를 쓰지 않아도 무조건 상수로 인식 
        double PI = 3.14;
        int ERROR = -99999999;
        
        // 인터페이스에서 선언한 메서드는 컴파일 과정에서 추상 메서드로 변환됨
        // public abstract 예약어를 명시적으로 쓰지 않아도 컴파일 과정에서 자동으로 추상 메서드로 변환 
        int add(int num1, int num2);
        int substract(int num1, int num2);
        int times(int num1, int num2);
        int divide(int num1, int num2);
    }

  ```

  - 인터페이스에 선언한 기능을 클래스가 구현한다는 의미로 `implements` 예약어를 사용한다.
  - 인터페이스 역할: 인터페이스는 클라이언트 프로그램에 어떤 메서드를 제공하는지 미리 알려주는 명세(specification)이다.
  - 형 변환
    - 예: interface Calc <- abstract class Calculator(추상 메서드 일부만 구현) <- class CompleteCalc
    - 인터페이스도 상속관계와 마찬가지로 묵시적 형 변환할 수 있다.
    - `Calc calc = new CompleteCalc();`
    - 인터페이스를 구현한 클래스가 있을 때 그 클래스는 해당 인터페이스형으로 묵시적 형 변환이 이루어지며, 형변환되었을 때 사용할 수 있는 메서드는 인터페이스에서 선언한 메서드 뿐이다.
  - [스케쥴러 예시](https://github.com/easyspubjava/JAVA_LAB/tree/master/Chapter10/src/scheduler)
    - Scheduler 인터페이스
    - RoundRobin 클래스 implement Scheduler
    - LeastJob 클래스 implement Scheduler
    - PriorityAllocation 클래스 implement Scheduler
    - ... (향후 추가되는 정책을 반영한 클래스)
    - 실행 main 함수: 입력 문자에 따라 해당 정책의 클래스 생성 후 인터페이스에서 선언한 메서드 호출 
- 인터페이스 요소 살펴보기
  - 디폴트 메서드와 정적 메서드: 자바 7까지는 인터페이스에서 추상 메서드와 상수, 이 두 가지 요소만 선언해서 사용할 수 있었다. 자바 8부터 인터페이스 활용성을 높이기 위해 디폴트 메서드와 정적 메서드 기능을 제공한다. 
    - 디폴트 메서드: 인터페이스에서 구현하지만, 이후 인터페이스를 구현한 클래스가 생성되면 그 클래스에서 사용할 기본 기능이다. `default` 예약어를 사용한다.
    - 디폴트 메서드 재정의 하기: `@Override`를 통해 재정의 할 수 있다.
    - 정적 메서드: `static` 예약어를 사용하여 선언하며 클래스 생성과 무관하게 사용할 수 있다. 정적 메서드를 사용할 때는 인터페이스 이름으로 직접 참조하여 사용한다.
    - private 메서드: 자바 9부터 인터페이스에 private 메서드를 구현할 수 있다. private 메서드는 인터페이스를 구현한 클래스에서 사용하거나 재정의할 수 없습니다. 따라서 기존에 구현된 코드를 변경하지 않고 인터페이스를 구현하면 코드 재사용성을 높일 수 있다.
- 인터페이스 활용하기
  - 한 클래스가 여러 클래스를 상속받으면 메서드 호출이 모호해지는 문제가 발생할 수 있다. 하지만 인터페이스는 한 클래스가 여러 인터페이스를 구현할 수 있다. 두 인터페이스에 이름이 같은 메서드가 선언되었다고 해도 구현은 클래스에서 이루어지므로, 어떤 메서드를 호출해야 하는지 모호하지 않다.
  - 예: Customer 클래스가 Buy, Sell 두 인터페이스를 구현(Buy는 buy메서드만 있고, Sell은 sell메서드만 있다.)
  
  ```java
  package interfaceex;

  public class CustomerTest {
    public static void main(String[] args) {
      Customer customer = new Customer();

      Buy buyer = customer;  // Customer 클래스형인 customer를 Buy 인터페이스형인 buyer에 대입하여 형 변환
      buyer.buy();  // buyer는 Buy 인터페이스의 메서드만 호출 가능

      Sell seller = customer;  // Customer 클래스형인 customer를 Sell 인터페이스형인 seller에 대입하여 형 변환
      seller.sell();  // seller는 Sell 인터페이스의 메서드만 호출 가능

      if (seller instanceof Customer) {
        Customer customer2 = (Customer) seller;  // seller를 하위 클래스인 Customer로 다시 형 변환
        customer2.buy();
        customer2.sell();
      }
    }
  }
  ```

  - 두 인터페이스의 디폴트 메서드가 중복되는 경우: 위의 예에서 Buy와 Sell 인터페이스에 똑같은 pay() 정적 메서드가 있다면 Buy.pay(), Sell.pay()로 특정하여 호출할 수 있기 때문에 문제가 되지 않는다. 그런데 디폴트 메서드는 인스턴스를 생성해야 호출할 수 있는 메서드이기 때문에 이름이 같은 디폴트 메서드가 두 인터페이스에 있으면 문제가 된다. 이런 경우 Custom 클래스에서 디폴트 메서드를 재정의해야 한다. 재정의 하면 Customer 클래스를 생성하여 사용할 때 재정의된 메서드가 호출된다.
  - 인터페이스 간에 상속도 가능하다. `extends` 예약어를 사용한다.
  - 한 클래스에서 클래스 상속과 인터페이스 구현을 모두 할 수 있다.
  - 실무에서 인터페이스를 사용하는 경우: UserInfoWeb -- 사용 --> UserInfoDao 인터페이스 <-- implements -- OracleDao, MysqlDao, MssqlDao
    - UserInfoDao 인터페이스: 사용자 정보를 데이터베이스에 입력하거나 업데이트하거나 삭제하는 기능을 정의
    - 그리고 여러 데이터베이스에 맞게 구현하는 것은 각 클래스가 담당

## 11. 기본 클래스 (java.lang 패키지 속한 클래스들)
- Object 클래스
  - Object 클래스는 모든 자바 클래스의 최상위 클래스이다. 다시 말하면 모든 클래스는 Object 클래스로부터 상속을 받는다. 컴파일 과정에서 `extends Object`가 자동으로 쓰인다.
  - toString() 메서드: 객체 정보를 문자열(String)으로 바꾼다. Object 클래스의 toString() 메서드 원형은 '클래스 이름@해시 코드 값'이다.
  - equals() 메서드: 두 인스턴스의 주소 값을 비교하여 boolean 값(true/false)을 반환해주는 것이다.
    - String 클래스의 equals() 메서드는 같은 문자열의 경우 true, 그렇지 않은 경우 false를 반환하도록 재정의 됨
    - Integer: 클래스의 경우도 정수값이 같은 경우 ture를 반환하도록 equals() 메서드가 재정의 됨

    ```java
    package object;

    public class StringEquals {
      public static void main(String[] args) {
        String str1 = new String("abc");
        String str2 = new String("abc");

        System.out.println(str1 == str2);  // 주소 값 비교이므로 false
        System.out.println(str1.equals(str2));  // 문자열 값을 비교하므로 true
      }
    }
    ```

    - Student 클래스에서 equals() 메서드 직접 재정의하기

    ```java
    package object;

    class Student {

      //...

      @Override
      public boolean equals(Object obj) {
        if (obj instanceof Student) {
          Student std = (Student) obj;
          if (this.studentId == std.studentId) {  // 논리적으로 동일 
            return true;
          else return false;
          }
        }
        return false;
      }
    }
    ```

  - hashCode() 메서드: 해시(hash)는 정보를 저장하거나 검색할 때 사용하는 자료구조이다. 자바에서 인스턴스를 힙 메모리에 생성하여 관리할 때 해시 알고리즘을 사용한다. 참조 변수를 출력할 때 본 16진수 숫자 값이 '해시 코드 값'이고 이 값은 자바 가상 머신이 힙 메모리에 저장한 '인스턴스의 주소 값'이다. 즉 자바에서 두 인스턴스가 같다면 hashCode() 메서드에서 반환하는 해시 코드 값이 같아야 한다. equals() 메서드를 재정의했다면 hashCode() 메서드도 재정의해야 한다.

  ```java
    package object;

    class Student {

      //...

      @Override
      public boolean hashCode() {
        return studentId;  // 멤버 변수 활용 
      }
    }
    ```

  - clone() 메서드: 객체를 복제하여 동일한 멤버 변수 값을 가진 새로운 인스턴스를 생성한다.
- String 클래스
  - 생성자의 매개변수로 문자열 생성
    - `String str1 = new String("abc");`
    - 메모리가 할당되고 새로운 객체가 생성
  - 문자열 상수를 가르키는 방식
    - `String str2 = "test";`
    - 기존에 만들어져 있던 "test"라는 문자열 상수의 메모리 주소를 가리키게 됨 
    - test, 10, 20 등과 같이 프로그램에서 사용되는 상수값을 저장하는 공간을 상수 풀(constant pool)이라고 한다.
  - 한 번 생성된 문자열은 변경되지 않는다. 이런 문자열의 특징을 '문자열은 불변(immutable)한다'라고 한다. 두 개의 문자열을 연결하면 둘 중 하나의 문자열이 변경되는 것이 아니라 두 문자열이 연결된 새로운 문자열이 생성된다.
  - StringBuffer와 StringBuilder 클래스 활용하기
    - 문자열을 변경하거나 연결해야 할때 String 클래스는 한번 생성되면 그 내부의 문자열이 변경되지 않기 때문에 String 클래스를 사용하여 문자열을 계속 연결하거나 변경하는 프로그램을 작성하면 메모리가 많이 낭비된다. 이 문제를 해결하기 위한 것이 바로 StringBuffer와 StringBuilder 클래스이다.
    - StringBuffer와 StringBuilder는 내부에 변경 가능한(final이 아닌) char[] 변수를 가지고 있다. 이 두 클래스를 사용하여 문자열을 연결하면 기존에 사용하던 char[] 배열이 확장되므로 추가 메모리를 사용하지 않는다. 
    - 두 클래스의 차이는 여러 작업(스레드)이 동시에 문자열을 변경하려 할 때 문자열이 안전하게 변경되도록 보장해주는가 차이다. StringBuffer 클래스는 문자열이 안전하게 변경되도록 보장하지만, StringBuilder 클래스는 보장되지 않는다. 프로그램에서 따로 스레드를 생성하는 멀티스레도 프로그램이 아니라면 StringBuilder를 사용하는 것이 실행 속도가 좀 더 빠르다.
- Wrapper 클래스: 기본 자료형처럼 사용할 수 있는 클래스
  - boolean | Boolean
  - byte | Byte
  - char | Character
  - short  | Short
  - int | Integer
  - long | Long
  - float | Float
  - double | Double
  - Integer 클래스의 메서드
    - intValue() 메서드: int 자료형 값을 가져오기

    ```java
    Integer iValue = new Integer(100);
    int myValue = iValue.intValue(); 
    ```

    - valueOf() 정적 메서드: 생성자를 사용하지 않고 정수나 문자열을 바로 Integer 클래스로 반환
   
    ```java
    Integer number1 = Integer.valueOf("10");
    Integer number2 = Integer.valueOf(100);
    ```

    - parseInt() 메서드: 문자열이 어떤 숫자를 나타낼 때, 문자열에서 int 값을 가져와서 반환
    
    ```java
    int num = Integer.parseInt("100");
    ```
  - 오토박싱과 언박싱: 자바 5 이전에는 기본 자료형과 Wrapper 클래스형을 함께 연산하기 위해 둘 중 하나의 형태로 일치시켜야 했다. 하지만 자바 5부터 변환없이 사용 가능하다.
    - 기본형을 객체형으로 바꾸는 것을 오토박싱(autoboxing), 객체형을 기본형으로 꺼내는 것을 언박싱(unboxing)이라고 한다.
    - 이는 자바 연산 방식이 변경된 것이 아니라 컴파일러가 변경하는 것이다.
- Class 클래스 
  - 자바의 모든 클래스와 인터페이스는 컴파일 되고 나면 class 파일로 생성된다. Class 클래스는 컴파일된 class 파일에 저장된 클래스나 인터페이스 정보를 가져오는데 사용한다.
  
  ```java
  // 1. Object 클래스의 getClass() 메서드 사용하기
  String s = new String();
  Class c1 = s.getClass();

  // 2. 클래스 파일 이름을 Class 변수에 직접 대입하기
  Class c2 = String.Class;

  // 3. ClassforName("클래스 이름") 메서드 사용하기
  Class c3 = Class.forName("java.lang.String");
  ```

  - Class 클래스를 활용해 그 클래스 정보를 가져오고, 이 정보를 활용하여 인스턴스를 생성하거나 메서드를 호출하는 방식을 리플렉션(reflection)이라고 한다. 사실 우리가 자바로 프로그램을 구현할 때 리플렉션 프로그래밍을 해야 하는 경우는 많지 안힉 때문에, 이런 방법이 있다는 것 정도만 알아 두면 된다. 리플렉션 프로그래밍은 컴파일 시점에 알 수 없는 클래스, 즉 프로그램 실행 중에 클래스를 메모리에 로딩하거나 객체가 다른 곳에 위치해서 원격으로 로딩하고 생성할 때 사용합니다.
  - `Class.forName()`을 사용해 동적 로딩하기: 프로그램 실행 이후 클래스의 로딩이 필요한 경우 클래스의 '동적 로딩(dynamic loading)' 방식을 사용한다.
    - 예: 개발한 시스템이 여러 종류의 데이터베이스를 지원한다. 그렇다고 이 시스템을 컴파일할 때 모든 데이터베이스 라이브러리(드라이버)를 같이 컴파일할 필요는 없다. 시스템을 구동할 때 어떤 데이터베이스와 연결할지만 결정된다면 해당 드라이버만 로딩하면 된다. 
    - 동적 로딩을 통해 Class 클래스를 가져올 수 있다면 리플렉션 프로그래밍으로 객체를 생성하고 활용할 수 있다.

## 12. 컬렉션 프레임워크 (자료구조를 구현한 다양한 인터페이스와 클래스)
- 제네릭
  - 어떤 값이 하나의 참조 자료형이 아닌 여러 참조 자료형을 사용할 수 있도록 프로그래밍하는 것을 '제네릭(Generic) 프로그래밍'이라고 한다. 제네릭 프로그램은 참조 자료형이 변환될 때 이에 대한 검증을 컴파일러가 하므로 안정적이다.
  - 제네릭의 필요성: 3D 프린터는 재료를 가지고 입체 모양을 만드는 일을 한다. 프린터에 쓰이는 재료는 여러가지가 있다.
  
  ```java
  public class ThreeDPrinter {
    private Powder material;  // 재료가 파우더인 경우

    public void setMaterial(Powder material) {
      this.material = material;
    }

    public Powder getMaterial() {
      return material;
    }
  }
  ```

  ```java
  public class ThreeDPrinter {
    private Plastic material;  // 재료가 플라스틱인 경우

    public void setMaterial(Plastic material) {
      this.material = material;
    }

    public Plastic getMaterial() {
      return material;
    }
  }
  ```

    - 재료만 바뀌었을 뿐 프린터 기능이 동일하다면 프린터 클래스를 두 개 만드는 것은 비효율적이다. 이런 경우 어떤 재료든 쓸 수 있도록 material 변수의 자료형을 Object로 사용할 수 있다.
    - 하지만 어떤 변수가 여러 참조 자료형을 사용할 수 있도록 Object 클래스를 사용한다면 다시 원래 자료형으로 반환해 주기 위해 매번 형 변환을 해야 하는 번거로움이 있다. 이런 경우에 사용하는 프로그래밍 방식이 제네릭이다.
    - 여러 참조형이 쓰일 수 있는 곳에 특정한 자료형을 지정하지 않고, 클래스나 메서드를 정의한 후 사용하는 시점에 어떤 자료형을 사용할 것인지 지정하는 방식이다.
  - 제네릭 클래스 정의하기
    
    ```java
    public class GenericPrinter<T> {  // 제네릭 클래스, T 자료형 매개변수(type parameter)
      private T material;

      public void setMaterial(T material) {
        this.material = material;
      }

      public T getMaterial() {
        return material;
      }
    }
    ```

    - 다이아몬드 연산자 `<>`: 자바 7부터는 제네릭 자료형의 클래스를 생성할 때 생성자에 사용하는 자료형을 명시하지 않을 수 있다. `ArrayList<String> list = new ArrayList<>();` 선언된 자료형을 보고 생략된 부분이 String임을 컴파일러가 유추할 수 있기 때문에 생성 부분에서는 생략할 수 있다.
    - static 변수나 메서드는 인스턴스를 생성하지 않아도 클래스 이름으로 호출할 수 있다. 그런데 T의 자료형이 정해지는 순간은 제네릭 클래스의 인스턴스가 생성되는 순간이다. 따라서 static 변수의 자료형이나 static 메서드 내부 변수의 자료형으로 T를 사용할 수 없다.
    - 자료형 매개변수로 T외에 다른 문자도 사용할 수 있다. E는 element, K는 key, V는 value를 의미한다. 하지만 A, B 등 아무 문자나 사용해서 정의할 수 있다.
    - 자바 10부터 지역변수에 한해 자료형 추론이 가능하다. 이는 제네릭에도 적용된다. 
      - `ArrayList<String> list = new ArrayList<String>();` -> `var list = new ArrayList<String>();`
  - 제네릭 클래스 사용하기
    
    ```java
    GenericPrinter<Powder> powderPrinter = new GenericPrinter<Powder>();
    powderPrinter.setMaterial(new Powder());
    powder powder = poderPrinter.getMaterial();  // 명시적 형 변환을 하지 않음  
    ```
    
    - Powder형을 '대입된 자료형'이라 하고, GenericPrinter<Powder>를 '제네릭 자료형(Generic type)' 또는 '매개변수화된 자료형(parameterized type)'이라 한다.
    - 메서드 선언부나 매개변수로 자료형 매개변수 T를 사용하는 메서드를 제네릭 메소드라고 한다.
  - T 자료형에 사용할 자료형을 제한하는 `<T extends 클래스>`
    - 만약 아무 제약이 없으면 물을 재료로 쓸수있게 된다. 이런 일을 방지하기 위해 `extends` 예약어를 사용한다. T에 대입된 자료형으로 사용할 재료 클래스를 추상클래스에서 상속받는다.
    - Material <- Powder, Plastic
    
    ```java
    package generics;

    public abstract class Material {
      public abstract void doPrinting();
    }
    ```

    ```java
    package generics;

    public class Powder extends Material {
      public void doPrinting() {
        System.out.println("Powder 재로로 출력합니다.");
      }

      public String toString() {
        return "재료는 Powder입니다";
      }
    }
    ```

    ```java
    package generics;

    public class GenericPrinter<T extends Material> {
      private T material;

      // ...
    }
    ```

    - `<T extends 클래스>`로 선언하면 제네릭 클래스를 사용할 때 상위 클래스 Material에서 선언한 메서드도 사용할 수 있다.
  - 제네릭 메서드 활용하기
    - 제네릭 클래스가 아니라도 내부에 제네릭 메서드를 구현할 수 있다.
    - public <자료형 매개변수> 반환형 메서드이름 (자료형 매개변수 ...) {}
    - `public static <T, V> double makeRectagle(Point<T, V> p1, Point<T, V> p2) {}`
  - 컬렌셕 프레임워크에서 사용하는 제네릭: 컬렉션 프레임워크에서도 다양한 자료형을 관리하기 위해 제네릭을 자주 사용한다.
    - 예를 들어 ArrayList의 정의는 다음과 같다.
    
    ```java
    public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAcess, Clonealbe, java.io.Serializable { ... }
    ```
  
- 컬렉션 프레임워크
  - 컬렉션 프레임워크란? 자바에서 필요한 자료구조를 미리 구현하여 java.util 패키지에서 제공하고 있는데, 이를 컬렉션 프레임워크(collection framework)라고 한다.
  - Collection 인터페이스: 하나의 자료를 모아서 관리하는데 필요한 기능을 제공
    - List: 순차적인 자료를 관리하는데 사용
      - ArrayList, Vector, LinkedList
    - Set: 집합(집합은 순서와 상관없이 중복을 허용하지 않는다.)
      - HashSet, TreeSet
  - Collection 대표 메서드
    - boolean add(E e): Collection에 객체를 추가
    - void clear(): Collection에 모든 객체를 제거
    - Iterator<E> iterator: Collection을 순환할 반복자(iterator)를 반환
    - boolean remove(Object o): Collection에 매개변수에 해당하는 인스턴스가 존재하면 제거
    - int size(): Collection에 있는 요소의 개수를 반환
  - Map 인터페이스: 쌍(pair)으로 된 자료들을 관리하는데 유용한 기능을 제공(키는 중복 불가)
    - Hashtable: Properties
    - HashMap
    - TreeMap
  - Map 대표 메서드
    - V put(K key, V value): key에 해당하는 value 값을 map에 넣는다.
    - V get(K key): key에 해당하는 value 값을 반환
    - boolean isEmpty(): Map이 비었는지 여부를 반환
    - boolean containsKey(Object key): Map에 해당 key가 있는지 여부를 반환
    - boolean containsValue(Object value): Map에 해당 value가 있는지 여부를 반환
    - Set keyset(): key 집합을 Set으로 반환(중복 안되므로 Set)
    - Collection values(): value를 Collection으로 반환(중복 무관)
    - V remove(key): key가 있는 경우 삭제
    - boolean remove(Object key, Object value): key가 있는 경우 key에 해당하는 value가 매개변수와 일치할 때 삭제
- List 인터페이스
  - ArrayList 클래스: Collection 인터페이스와 그 하위 List 인터페이스를 구현
    - 디폴트 용량(DEFAULT_CAPACITY)이 10으로 정의되어 있다. `add()`나 `insert()` 등의 메서드는 용량이 부족하면 큰 용량의 배열을 새로 만들고 기존 항목을 복사한다.
  - ArrayList와 Vector 클래스: 가장 큰 차이는 동기화 지원 여부이다. 동기화(synchronization)란 두 개 이상의 스레드가 동시에 Vector를 사용할 때 오류가 나지 않도록 실행 순서를 보장하는 것이다.
    - 스레드란 간단히 말하면 작업 단위이다. 프로그램이 메모리에서 수행되려면 스레드 작업이 생성되어야 한다. 이때 하나의 스레드만 수행되면 단일 스레드(single thread)라고 하고 두 개 이상의 스레드가 동시에 실행되는 경우를 멀티스레드(multi-thread)라고 한다. 두 개 이상의 스레드가 동시에 실행되면 같은 메모리 공간(리소스)에 접근하기 때문에 변수 값이나 메모리 상태에 오류가 생길 수 있다. 이 때 메모리에 동시에 접근하지 못하도록 순서를 맞추는 것이 동기화이다.
    - 멀티스레드 환경이 아닌 경우에는 ArrayList를 사용하도록 권장한다. 왜냐하면 동기화를 구현하기 위해서는 동시에 작업이 이루어지는 자원에 대해 잠금(lock)을 수행하기 때문이다. 즉 메서드를 호출할 때 배열 객체에 잠금을 하고, 메서드 수행이 끝나면 잠금을 해제한다. 이렇게 Vector의 모든 메서드는 호출될 때마다 잠금과 해제가 일어나므로 ArrayList보다 수행 속도가 느리다.
    - ArrayList를 사용해 구현했는데 나중에 프로그램에서 동기화가 필요하다면 Vector로 바꾸지 않고 다음과 같이 ArrayList를 생성해서 쓰면 된다.
    - `Collections.synchronizedList(new ArrayList<String>());`
  - LinkedList 클래스: 배열은 처음 배열을 생성할 때 정적 크기로 선언하고, 물리적 순서와 논리적 순서가 동일하다. 배열은 중간에 자료를 삽입하거나 삭제할 때 나머지 자료를 이동시켜 빈 공간을 만들지 않고 연속된 자료 구조를 구현한다. 또한 처음 선언한 배열 크기 이상으로 요소가 추가되는 경우에는 크기가 더 큰 배열을 새로 생성하여 각 요소를 복사해야 하는 번거로움이 있다. 이런 점을 개선한 자료 구조를 linked list라고 한다. 자바의 LinkedList 클래스가 이를 구현하고 있다.
    - 링크드 리스트의 각 요소는 다음 요소를 가리키는 주소 값을 가진다. 따라서 물리적인 메모리는 떨어져 있어도 논리적으로는 앞뒤 순서가 있다.
    - ArrayList에 비해 중간에 자료를 넣고 제거하는데 시간이 적게 걸리다는 장점이 있다.
    - 하지만 배열이 효과적인 경우도 있다. 배열은 물리적으로 연결된 자료구조이므로 i 번째 요소 메모리 위치를 바로 계산할 수 있다. 그리고 배열이 링크드 리스트보다 구현하기도 쉽다.
    - 따라서 사용하는 자료의 변동(삽입, 삭제)이 많은 경우에는 링크드 리스트를, 자료 변동이 거의 없는 경우에는 배열을 사용하는 것이 효율적이다.
    - LinkedList 클래스에서만 제공하는 메서드
      - addFirst(), addLast(), removeFirst(), removeLast() 등의 메서드가 있어 스택(stack)이나 큐(queue)에서 다양하게 활용 가능하다.
    - stack: 가장 최근에 검색한 단어 찾기, 지역 변수가 사용하는 메모리 구조
      - 자료 추가를 push(), 자료를 꺼내는 것을 pop(), 스택에 가장 최근에 추가된 자료의 위치를 top라고 한다.
    - queue: 선착순
      - 자료 추가를 enqueue(), 자료를 꺼내는 것을 dequeue()라고 한다.
  - Collection 요소를 순회하는 Iterator
    - Iterator는 Collection 인터페이스를 구현한 객체에서 미리 정의되어 있는 iterator() 메서드를 호출하여 참조한다.
    - `Iterator ir = memberArrayLlist.iterator();`
    - Iterator를 순회할 때 사용하는 메서드
      - boolean hashNext(): 이후에 요소가 더 있는지 체크하는 메서드, 요소가 있다면 true 반환
      - E next(): 다음 요소를 반환 
    - 순서가 없는 클래스(ex: Set 인터페이스)도 Iterator를 사용하면 요소를 순회할 수 있다.
- Set 인터페이스
  - HashSet 클래스

    ```java
    package collection.hashset;

    import collection.Member;

    public class MemberHashSetTest {

      public static void main(String[] args) {

        MemberHashSet memberHashSet = new MemberHashSet();
        
        Member memberLee = new Member(1001, "이지원");
        Member memberSon = new Member(1002, "손민국");
        Member memberPark = new Member(1003, "박서훤");
        
        
        memberHashSet.addMember(memberLee);
        memberHashSet.addMember(memberSon);
        memberHashSet.addMember(memberPark);
        memberHashSet.showAllMember();
        
        Member memberHong = new Member(1003, "홍길동");  //1003 아이디 중복 
        memberHashSet.addMember(memberHong);
        memberHashSet.showAllMember();
      }
    }
    ```

    - 기본적으로 인스턴스 주소가 같으면 같은 객체이다. Object 클래스에서 논리적으로 같은 객체를 구현하기 위해 equals() 메서드와 hashCode() 메서드를 재정의해서 회원 아이디가 같으면 같은 회원임을 구현해줘야 아이디가 같은 회원이 추가되지 않는다.
  - TreeSet 클래스
    - 자바의 Collection 혹은 Map 인터페이스를 구현한 클래스 중 Tree로 시작하는 클래스는 데이터를 추가한 후 결과를 출력하면 결과 값이 정렬된다. TreeSet는 자료의 중복을 허용하지 않으면서 출력 결과 값을 정렬하는 클래스이다.
    - 자바는 정렬을 구현하기 위해 '이진 트리(binary tree)'를 사용한다.
      - 트리 자료 구조에서 각 자료가 들어가는 공간을 노드라고 한다.
      - 위아래로 연결된 노드의 관계를 '부모-자식 노드(parent-child node)'라고 한다.
      - 이진 검색 트리(Binary Search Tree; BST)는 노드에 저장되는 자료의 중복을 허용하지 않고, 부모가 가지는 자식 노드의 수가 2개 이하이다.
      - 왼쪽에 있는 자식 노드는 부모 노드보다 항상 작은 값을 가진다.
      - 오른쪽에 있는 자식 노드는 부모 노드보다 항상 큰 값을 가진다.
    - 자신이 만든 Member 클래스를 TreeSet 요소로 추가할 때 Member 클래스에 Comparable 인터페이스를 구현하지 않으면 오류가 발생한다.
      - Member 클래스가 가진 회원아이디를 기준으로 오름차순 정렬할 것이다. Comparable 인터페이스와 Comparator 인터페이스는 이러한 정렬을 구현할 수 있게 해주는 인터페이스다.
      - `public class Member implements Comparable<member> {}`
        - Comparable 인터페이스에는 compareTo() 추상 메서드가 포함되어 있다. 따라서 Member 클래스에서 compareTo() 메서드를 구현해야 한다.
        
        ```java
        public class Member implements Comparable<Member> {
          private int memberId;
          private String memberName;

          // ...

          @Override
          public int compareTo(Member member) {
            return (this.memberId - member.memberId);
          }
        }
        ```
        
        - 새로 추가한 회원 아이디와 compareTo() 메서드의 매개변수로 전달된 회원 아이디를 비교
        - 두 값을 비교하여 추가한 회원 아이디가 더 크면 양수, 그렇지 않으면 음수, 같으면 0을 반환한다. 이렇게 구현하면 오름차순으로 정렬된다.
        - compareTo()는 프로그래머가 호출하는 메서드가 아닌 객체가 TreeSet에 요소를 추가할 때마다 호출되는 메서드이다. 
        - compareTo() 메서드처럼 프로그래머가 작성하지만 시스템이나 자바 컬렉션 프레임워크가 호출하는 메서드를 콜백(callback) 메서드라고 한다.
        - `return (this.memberId - member.memberId) * (-1);`로 하면 내림차순으로 정렬된다.
      - `public class Member2 implements Comparator<Member2> {}`
        - Comarator 인터페이스는 compare() 메서드를 구현해야 한다.
        
        ```java
        public class Member2 implements Comparator<Member2> {
          private int memberId;
          private String memberName;

          // ...

          @Override
          public int compare(Member2 mem1, Member2 mem2) {
            return mem1.getMemberId() - mem2.getMemberId();
          }
        }
        ```

        - 첫 번째 매개변수가 더 클 때 양수를 반환하여 오름차순으로 정렬된다.
        - Comparator를 사용할 때 TreeSet 생성자에 Comparator를 구현한 객체를 매개변수로 전달해야 한다. 
        - `TreeSet<Member2> treeSet = new TreeSet<Member2>(new Member2());`
        - 일반적으로 Comparator 인터페이스보다 Comparable 인터페이스를 더 많이 사용한다.
- Map 인터페이스
  - Map 인터페이스를 구현한 클래스는 내부적으로 해시 알고리즘에 의해 구현되어 있다.
  - HashMap 클래스
    - 가장 많이 사용하는 클래스이다. 자료를 관리하는 방식은 해시 방식이다.
    - 자료 추가 속도나 검색 속도가 상당히 빠르다는 장점이 있다.
    - 서로 다른 키 값에 같은 index가 반환되는 충돌(collision)이 발생하는 경우가 있다. 자바는 해시 테이블에 데이터를 꽉 채우지 않고 75%까지만 사용하고 컴파일러가 자동으로 메모리를 확장한다.
    - Map 인터페이스에서 사용하는 key 값은 중복될 수 없으므로 equals(), hashcode() 메서드를 재정의하여 사용하는 것이 좋다.
    - HashMap과 Hashtable: 두 클래스 모두 쌍으로 이루어진 자료를 관리하는데 사용합니다. Hashtable 클래스는 자바 1부터 사용했고, Vector 클래스와 마찬가지로 멀티스레드를 위한 동기화를 제공합니다. 멀티스레드 환경이 아니라면, Hashtable보다 HashMap을 사용하는 것을 권장한다.
  - TreeMap 클래스
    - key 값으로 자료를 정렬하려면 TreeMap을 사용한다. TreeSet과 마찬가지로 이진 검색 트리로 구현되어 있다. key 값으로 정렬하므로 key 값에 해당하는 클래스에 Comparable이나 Comparator 인터페이스를 구현해야 한다.
    - key 값은 Integer 형이므로 Comparable 인터페이스가 구현되어 있다. 이럴 경우에는 따로 구현하지 않아도 된다.

## 13. 내부 클래스, 람다식, 스트림
- 내부 클래스(inner class)
  - 클래스 내부에 선언한 클래스
  - 내부에 클래스를 선언한 이유는 대게 이 클래스와 외부 크래스가 밀접한 관련이 있기 때문이다. 또한 다른 클래스와 협력할 일이 없는 경우에 내부 클래스로 선언해서 사용한다.
  - 4가지 유형
    - 클래스 내부에 선언하는 변수 유형과 유사(인스턴스 변수, 정적 변수, 지역 변수)
      - 인스턴스 내부 클래스
      - 정적(static) 내부 클래스
      - 지역(local) 내부 클래스
    - 클래스 이름 없이 선언하고 바로 생성하여 사용할 수 있는
      - 익명(anonymous) 내부 클래스
  
  ```java
  class ABC {
    int n1;  // 인스턴스 변수
    static int n2;  // 정적 변수

    public void abc() {
      int i;  // 지역 변수
    }
  }
  ```

  ```java
  class ABC {  // 외부 클래스
    class In {  // 인스턴스 내부 클래스
      static class SIn {  // 정적 내부 클래스
      }
    }

    public void abc() {  // 메서드
      class Local {  // 지역 내부 클래스
      }
    }
  }
  ```

  - 인스턴스 내부 클래스(instance inner class): 외부 클래스 내부에서만 생성하여 사용하는 객체을 선언할 때 선언할 때 사용한다. 인스턴스 내부 클래스는 외부 클래스 생성 후 생성된다.
    - 인스턴스 내부 클래스에 정적 변수, 정적 메서드 선언 불가능하다. 인스턴스 내부 클래스는 외부 클래스를 생성한 이후에 사용해야 하기 때문이다. 따라서 클래스의 생성과 상관없이 사용할 수 있는 정적 변수는 인스턴스 내부 클래스에서 선언할 수 없다.
  - 정적 내부 클래스(static inner calss): 내부 클래스가 외부 클래스 생성과 무관하게 사용할 수 있어야 하고 정적 변수도 사용할 수 있어야 한다면  정적 내부 클래스를 사용하면 된다. `static` 에약어를 함께 사용한다.
    - 정적 메서드에서는 인스턴스 변수를 사용할 수 없다. 따라서 정적 내부 클래스에서도 외부 클래스의 인스턴스 변수는 사용할 수 없다.
  - 지역 내부 클래스: 지역 변수처럼 메서드 내부에 클래스를 정의하여 사용하는 것이다. 따라서 이 클래스는 메서드 안에서만 사용할 수 있다.
    - 지역 내부 클래스에서 사용하는 메서드의 지역 변수는 모두 상수로 바뀐다.
  - 익명 내부 클래스: 이름을 사용하지 않는 클래스
    - 익명 내부 클래스는 변수에 직접 대입하는 경우도 있고 메서드 내부에서 인터페이스나 추상 클래스를 구현하는 경우도 있다. 
    - 익명 내부 클래스는 예전에 자바 UI에서 이벤트 처리할 때 많이 사용했다. 현재는 안드로이드 프로그래밍에서 위젯의 이벤트를 처리하는 핸들러를 구현할 때 사용한다.
- 람다식
  - 자바는 객체를 기반으로 프로그램을 구현한다. 어떤 기능이 필요하다면 클래스를 먼저 만들고, 클래스 안에 기능을 구현한 메서드를 만든 후 그 메서드를 호출한다.
  - 함수의 구현과 호출만으로 프로그램을 만들 수 있는 프로그램 방식을 '함수형 프로그래밍(fucntional programming; fp)'이라고 한다. 자바 8부터 함수형 프로그래밍을 지원한다. 자바에서 제공하는 함수형 프로그래밍 방식을 '람다식(Lambda expression)'이라고 한다.
  - 람다식 구현하기 
    - 람다식은 함수 이름이 없는 익명 함수를 만드는 것이다.
    - `(매개변수) -> {실행문;}`
    
    ```java
    int add(int x, int y) {
      return x + y;
    }
    ```
    - 위의 함수를 람다식으로 변환: `(int x, int y) -> {return x + y;}`
  - 람다식 문법
    - 매개변수 자료형과 괄호 생략하기: 매개변수 자료형을 생략할 수 있다. 매개변수가 하나인 경우에는 괄호도 생략할 수 있다.
    
    ```java
    str -> {System.out.println(str);}
    x, y -> {System.out.println(x + y);}  // 매개변수가 2개이므로 괄호 생략 불가, 잘못된 형식
    ```
    - 중괄호 생략하기: 구현 부분이 한 문장인 경우 중괄호 생략 가능
    
    ```java
    str -> System.out.println(str);
    str -> return str.length();  // 한 문장ㅇ이더랃도 return 문은 중괄호 생략 불가, 잘못된 형식
    ```
    - return 생략하기: 중괄호 안의 구현부분이 return문 하나라면 중괄호와 return문 모두 생략하고 식만 쓴다.
    
    ```java
    (x, y) -> x + y
    str -> str.length()
    ```
  - 람다식 사용하기: 람다식을 구현하기 위해서 먼저 인터페이스를 만들고, 인터페이스에 람다식으로 구현할 메서드를 선언한다. 이를 함수형 인터페이스라고 한다.
  
  ```java
  package lambda;

  public interface MyNumber {
    int getMax(int num1, int num2);  // 추상 메서드 선언
  }
  ```

  ```java
  package lambda;

  public class TestMyNumber {
    public static void main(String[] args) {
      MyNumber max = (x, y) -> (x >= y) ? x : y;  // 람다식을 인터페이스 max 변수에 대입
      System.out.println(max.getMax(10, 20));  // 인터페이스형 변수로 메서드 호출
    }
  }
  ```

  - 함수형 프로그래밍은 순수 함수(pure function)를 구현하고 호출함으로써 외부 자료에 부수적인 영향(side effect)을 주지 않도록 구현하는 방식이다. 순수 함수란 매개변수만을 사용하여 만드는 함수이다. 즉 함수 내부에서 함수 외부에 있는 변수를 사용하지 않아 함수가 수행되더라도 외부에 영향을 주지 않는다. 함수가 입력받은 자료 이외에 외부 자료에 영향을 미치지 않기 때문에 여러 자료 동시에 처리하는 병렬 처리에 적합하며, 안정되고 확장성 있는 프로그램을 개발할 수 있다는 장점이 있다. 또 순수 함수로 구현된 함수형 프로그램은 함수 기능이 자료에 독립적일 수 있도록 보장한다. 즉 동일한 입력에 대해서 동일한 출력을 보장하고, 다양한 자료에 같은 기능을 수행할 수 있다.
  - 함수형 인터페이스: 람다식은 메서드 이름이 없고 메서드를 실행하는데 필요한 매개변수와 매개변수를 활용한 실행 코드를 구현하는 것이다. 함수형 언어에서는 함수만 따로 호출할 수 있지만, 자바에서는 참조 변수 없이 메서드를 호출할 수 없다. 그러므로 람다식을 구현하기 위해 함수형 인터페이스를 만들고, 인터페이스에 람다식으로 구현할 메서드를 선언하는 것이다. 람다식은 오직 하나의 메서드만 선언한 인터페이스를 구현할 수 있다.
    - `@FunctionalInterface` 애노테이션:메서드를 하나 이상 선언하면 오류를 발생 시킴
  - 익명 객체를 생성하는 람다식:  람다식은 객체 없이 인터페이스의 구현만으로 메서드를 호출할 수 있었다. 자바는 객체 생성 없이 메서드 호출이 일어날 수 없는데 어떻게 호출되는 것을까? 익명 내부 클래스는 클래스 이름없이 인터페이스 자료형 변수에 바로 메서드 구현부를 생성하여 대입할 수 있다. 즉 람다식으로 메서드를 구현해서 호출하면 컴퓨터 내부에서 다음처럼 익명 클래스가 생성되고 이를 통해 익명 객체가 생성된다.
  
  ```java
  StringConcat concat3 = new StringConcat() {
    @Override
    public void makeString(String s1, String s2) {
      System.out.println(s1 + ", " + s2);
    }
  };
  ```
  - 람다식은 함수의 구현부를변수에 대입하고, 매개변수로 전달하고, 함수의 반환값으로 사용할 수 있다. 마치 변수처럼 사용할 수 있는 것이다. 이는 함수형 프로그래밍의 특징 중 하나이다.
- 스트림
  - 자료가 모여있는 배열이나 컬렉션 또는 특정 범위 안에 있는 일련의 숫자를 처리하는 기능이 미리 구현되어 있다면 프로그램의 코드가 훨씬 간결해지고 일관성 있게 다룰 수 있다. 예를 들어 배열 요소를 특정 기준에 따라 정렬(sorting)하거나, 요소 중 특정 값은 제외하고 출력하는(filter)기능처럼 말이다. 이렇게 여러 자료의 처리에 대한 기능을 구현해 놓은 클래스가 스트림(stream)이다. 스트림을 활용하면 배열, 컬렉션 등의 자료를 일관성 있게 처리할 수 있다. 자료에 따라 기능을 각각 새로 구현하는 것이 아니라 처리해야 하는 자료가 무엇인지와 상관없이 같은 방식으로 메서드를 호출할 수 있기 때문이다. 다른 말로 자료를 추상화했다고 표현한다.
  
  ```java
  int[] arr = {1, 2, 3, 4, 5};
  for (int i = 0; i < arr.length; i++) {
    System.out.println(arr[i]);
  }

  // 스트림을 생성하여 출력
  int[] arr = {1, 2, 3, 4, 5};
  Arrays.stream(arr).forEach(n -> System.out.println(n));
  ```

  - 스트림 연산: 중간 연산과 최종 연산 두 가지가 있다. 중간 연산은 자료를 거르거나 변경하여 또 다른 자료를 내부적으로 생성한다. 최종 연산은 생성된 내부 자료를 소모해 가면서 연산을 수행한다. 따라서 최종 연산은 마지막에 한번만 호출된다. 그리고 최종 연산이 호출되어야 중간 연산의 결과가 만들어 진다. 
    - 중간 연산: `filter()`, `map()`
      - `filter()`: 조건을 넣고 조건에 맞는 경우 참을 추출하는 경우에 사용
      
      ```java
      // 문자열의 길이가 5 이상인 경우만 출력
      sList.stream().filter(s -> s.length() >=5).forEach(s -> System.out.println(s));
      ``` 
      - `map()`: 클래스가 가진 자료 중 이름만 출력하는 경우 사용할 수 있다. 또는 요소들을 순회하여 다른 형식으로 변환하기도 한다.
      
      ```java
      customerList.stream().map(c -> c.getName()).forEach(s -> System.out.println(s));
      ```
    - 최종 연산: `forEach()`, `count()`, `sum()`, `reduce()`
      
      ```java
      int[] arr = {1, 2, 3, 4, 5};
      int sumVal = Arrays.stream(arr).sum();
      int count = (int) Arrays.stream(arr).count();  // 반환 값이 long형이므로 형 변환 
      ```
    - collection에서 스트림 생성하고 사용하기
    
    ```java
    package stream;

    import java.util.ArrayList;
    import java.util.List;
    import java.util.stream.Stream;

    public class ArrayListStreamTest {

      public static void main(String[] args) {

        List<String> sList = new ArrayList<String>();
        sList.add("Tomas");
        sList.add("Edward");
        sList.add("Jack");
        
        Stream<String> stream = sList.stream();  // 스트림 생성
        stream.forEach(s->System.out.print(s + " "));  // 배열의 요소 하나씩 출력
        System.out.println();
        
        sList.stream().sorted().forEach(s->System.out.print(s+ " "));  // 스트림 새로 생성
      //	sList.stream().map(s->s.length()).forEach(n->System.out.println(n));
      //	sList.stream().filter(s->s.length() >= 5).forEach(s->System.out.println(s));
        
      }
    }

    ``` 
      - forEach() 메서드는 내부적으로 반복문이 수행된다.
      - forEach() 메서드가 수행되면 자료가 소모되므로, 스트림을 새로 생성해야 한다.
  - 스트림의 특징
    - 자료의 대상과 관계없이 동일한 연산을 수행: 스트림은 컬렉션의 여러 자료 구조에 대해 일관성 있게 처리할 수 있는 메서드를 제공
    - 한번 생성하고 사용한 스트림은 재사용 불가
    - 스트림의 연산은 기존 자료를 변경하지 않는다. 스트림 연산을 위해 사용하는 메모리 공간이 별도로 존재한다.
    - 스트림의 연산은 중간연산과 최종연산이 있다.
  - 프로그래머가 기능을 지정하는 `reduce()` 연산
    - `reduce()` 연산은 내부적으로 스트림의 요소를 하나씩 소모하면서 프로그래머가 직접 지정한 기능을 수행한다.
    - 모든 요소의 합을 구하는 예시: `Arrays.stream(arr).reduce(0, (a, b) -> a + b);`
    - `T reduce(T identify, BinaryOperator<T> accumulator)`
      - 첫 번째 매개변수 `T identify`는 초기값을 의미
      - 두 번째 매개변수 `BinaryOperator<T> accumulator`는 수행해야 할 기능이다. 이 때 BinaryOperator 인터페이스를 구현한 람다식을 직접 써도 되고, 람다식이 길면 인터페이스를 구현한 클래스를 생성하여 대입해도 된다. 또한 BinaryOperator 인터페이스는 함수형 인터페이스로 `apply()` 메서드를 반드시 구현해야 한다. `apply()` 메서드는 두 개의 매개변수와 한 개의 반환 값을 가지는데, 세 개 모두 같은 자료형이다.
      
      ```java
      package stream;

      import java.util.Arrays;
      import java.util.function.BinaryOperator;

      class CompareString implements BinaryOperator<String>{

        @Override
        public String apply(String s1, String s2) {
          if (s1.getBytes().length >= s2.getBytes().length) return s1;
          else return s2;
        }
      }

      public class ReduceTest {

        public static void main(String[] args) {

          String[] greetings = {"안녕하세요~~~", "hello", "Good morning", "반갑습니다^^"};
          
          System.out.println(Arrays.stream(greetings).reduce("", (s1, s2)-> 
                                    {if (s1.getBytes().length >= s2.getBytes().length) 
                                                return s1;
                                    else return s2;})); 
          
          String str = Arrays.stream(greetings).reduce(new CompareString()).get(); //BinaryOperator를 구현한 클래스 이용
          System.out.println(str);
                                    
        }
      }
      ```

## 14. 예외 처리
- 예외 클래스
  - 오류란? 프로그램에서 오류가 발생하는 상황은 두 가지이다. 하나는 프로그램 코드 작성 중 실수로 발생하는 컴파일 오류(compile error)이고, 다른 하나는 실행 중인 프로그램이 의도하지 않은 동작을 하거나 프로그램이 중지되는 실행 오류(runtime error)이다. 실행 오류 중 프로그램을 잘못 구현하여 의도한 바와 다르게 실행되어 생기는 오류를 버그(bug)라고 한다. 프로그램 실행 중 발생하는 오류는 예측하기 어려운 경우가 많고, 프로그램이 비정상 종료되면서 갑자기 멈춘다. 자바는 이러한 비정상 종료를 최대한 줄이기 위해 다양한 예외 처리 방법을 가지고 있다. 예외 처리를 하는 목적은 일단 프로그램이 비정상 종료되는 것을 방지하기 위한 것이다. 그리고 예외가 발생했을 때 로그를 남겨 두면 예외 상황을 파악하고 버그를 수정하는데 도움을 받을 수 있다.
  - 오류와 예외: 자바 가상머신에서 발생하는 시스템 오류(error)이고 다른 하나는 예외(exception)이다. 시스템 오류의 예로는 사용 가능한 동적 메모리가 없는 경우나 스택 메모리의 오버 플로우가 발생한 경우 등이 있다. 이러한 시스템 오류는 프로그램에서 제어할 수 없다. 반면 '예외'는 프로그램에서 제어할 수 있다.
  - Throwable <- Error, Exception
  - 오류 클래스는 모두 Throwable 클래스에서 상속받는다. Error 클래스의 하위 클래스는 시스템에서 발생하는 오류를 다루며 프로그램에서 제어하지 않는다. 프로그램에서 제어하는 부분은 Exception 클래스와 그 하위에 있는 예외 클래스다.
  - Exception
    - IOException
      - FileNotFoundException
      - SocketException
      - ...
    - RuntimeException
      - ArithmeticException
      - IndexOutofBoundsException
      - ...
- 예외 처리하기
  - `try-catch` 문: 가장 기본 문법
    
    ```java
    try {
      예외가 발생할 수 있는 코드 부분
    } catch(처리할 예외 타입 e) {
      try 블록 안에서 예외가 발생했을 때 예외를 처리하는 부분
    }
    ```
  - `try-catch-finally` 문 
    - 리소스는 프로그램이 종료되면 자동으로 해제된다. 하지만 끝나지 않고 계속 수행되는 서비스는 리소스를 여러 번 반복해서 열기만 하고 닫지 않는다면 문제가 발생한다. 시스템에서 허용되는 자원은 한계가 있기 때문이다. 따라서 사용한 리소스는 사용한 후 반드시 `close()` 메서드로 닫아주어야 한다. 이때 항상 수행되야 하는 부분을 추가할 수 있는 `try-catch-finally` 문을 사용한다.
    
    ```java
    try {
      예외가 발생할 수 있는 코드 부분
    } catch(처리할 예외 타입 e) {
      예외 처리 부분
    } finally {
      항상 수행되는 부분
    }
    ```

  - `try-with-resources` 문
    - 시스템 리소스를 사용하고 해제하는 코드는 다소 복잡하다. 자바 7부터 `try-with-resources`문을 제공하여 `close()` 메서드를 명시적으로 호출하지 않도록 try 블록 내에서 열린 리소스를 자동으로 닫도록 만들 수 있다. try-with-resources 문법을 사용하려면 해당 리소스가 AutoCloseable 인터페이스를 구현해야 한다.
    
    ```java
    try (A a = new A(); B b = new B()) {  // 괄호안에 리소스를 선언, 리소스가 여러개인 경우 세미 콜론(;)으로 구분
      // ...
    } catch(Exception e) {
      // ...
    }
    ```
    - try-with-resources 문을 사용하면 close() 메서드를 명시적으로 호출하지 않아도 정상 종료된 경우와 예외가 발생한 경우 모두 리소스가 잘 해제된다.
    - 향상된 try-with-resources문 (자바 9에서 추가된 문법): try문의 괄호안에 외부에서 선언한 변수도 쓸 수 있다.
    
    ```java
    AutoClsoeObj obj = new AutoCloseObj();
    try (obj) {
      // ...
    } catch(Exception e) {
      // ...
    }
    ```
  - 예외 처리 미루기
    - 예외를 해당 메서드에서 처리하지 않고 미룬 후 메서드를 호출하여 사용하는 부분에서 예외를 처리하는 방법이다.
    
    ```java
    public String A(String fileName) throws FileNotFoundException {  // 호출 될 때 처리하도록 미룸
      // ...
    }
    ```
    - 예외가 발생한 메서드에서 그 예외를 바로 처리할 것인지 아니면 미루어서 그 메서드를 호출하여 사용하는 부분에서 처리할 것인지는 만들고자 하는 프로그램 상황에 따라 다르다. 만약 어떤 메서드가 다른 여러 코드에서 호출되어 사용된다면 호출하는 코드의 상황에 맞게 로그를 남기거나 예외 처리하는 것이 더 좋다. 따라서 이런 경우에는 메서드를 호출하는 부분에서 예외 처리를 하도록 미루는 것이 합리적이다.
  - 다중 처리 예외: 어떤 예외가 발생할지 미리 알 수 없지만 모든 예외 상황을 처리하고자 한다면 맨 마지막 부분에 Exception 클래스를 활용하여 catch 블록을 추가한다.
    - Exception 클래스는 모든 예외 클래스의 최상위 클래스이다. 따라서 catch 블록에 선언한 것 이외의 예외가 발생하더라도 Exception 클래스로 자동 형 변환된다.
    - 예외는 catch문을 선언한 순서대로 검사한다. 따라서 맨 위에 `catch(Exception e)` 문장을 쓰면 발생하는 모든 예외 클래스는 Exception 상위 클래스로 자동 형 변환되어 오류가 발생한다. Exception 클래스 블록은 여러 예외 처리 블록의 가장 아래에 놓여야 한다.
- 사용자 정의 예외
  - JDK에서 제공하는 예외 클래스 중 가장 유사한 클래스를 상속받는 것이 좋다. 잘 모르겠다면 가장 상위 클래스인 Exception 클래스를 상속받는다.
  
  ```java
  package exception;

  public class IDFormatException extends Exception{
    
    public IDFormatException(String message){
      super(message);
    }
  }
  ```

  ```java
  package exception;

  public class IDFormatTest {

    private String userID;
    
    public String getUserID(){
      return userID;
    }
    
    public void setUserID(String userID) throws IDFormatException{  // 메서드가 호출할 때 예최 처리하도록 미룸
      
      if(userID == null){
        throw new IDFormatException("아이디는 null 일 수 없습니다");  // 강제 예외 발생
      }
      else if( userID.length() < 8 || userID.length() > 20){
        throw new IDFormatException("아이디는 8자 이상 20자 이하로 쓰세요");  // 강제 예외 발생
      }
      this.userID = userID;
    }
    
    public static void main(String[] args) {

      IDFormatTest test = new IDFormatTest();
      String userID = null;
      try {
        test.setUserID(userID);
      } catch (IDFormatException e) {
        System.out.println(e.getMessage());
      }
      
      userID = "1234567";
      try {
        test.setUserID(userID);
      } catch (IDFormatException e) {
        System.out.println(e.getMessage());
      }
    }
  }
  ```

  - 예외 처리할때는 로그를 잘 남기자!

## 15. 자바 입출력
- 자바 입출력과 스트림
  - 스트림: 자바에서 모든 입출력은 스트림(stream)을 통해 이루어진다. 스트림이란 네트워크에서 유래된 용어이다. 자료 흐름이 물의 흐름과 같다는 의미에서 사용했다. 입출력 장치는 매우 다양하기 때문에 장치에 따라 입출력 부분을 일일이 다르게 구현을 하면 프로그램 호환성이 떨어질 수 밖에 없다. 이런 문제를 해결하기 위해 자바는 입출력 장치와 무관하고 일관성 있게 프로그램을 구현할 수 있도록 일종의 가상 통로인 스트림을 제공하는 것이다.
  - 스트림을 세 가지 기준에 따라 분류
    - 입력 스트림과 출력 스트림: 어떤 대상으로부터 자료를 읽어 들일 때 사용하는 스트림이 입력 스트림이다. 파일에 저장할 때는 출력 스트림을 사용한다. 스트림은 단반향으로 자료가 이동하기 때문에 입력과 출력을 동시에 할 수 없다.
      - 입력 스트림: FileInputStream, FileReader, BufferedInputStream, BufferedReader 등
      - 출력 스트림: FileOutputStream, FileWriter, BufferedOutputStream, BufferedWriter 등
    - 바이트 단위 스트림과 문자 단위 스트림: 자바의 스트림은 바이트(byte) 단위로 자료의 입출력이 이루어진다. 그런데 자바에서 하나의 문자를 나타내는 char형은 2바이트이기 때문에 1바이트만 읽으면 한글 같은 문자는 깨진다. 따라서 문자를 위해 문자 스트림을 별도로 제공한다.
      - Stream으로 끝나는 경우 바이트 단위를 처리하는 스트림이다.
      - Reader, Writer로 끝나는 이름은 문자를 위한 스트림 클래스다.
      - 바이트 스트림: FileInputStream, FileOutputStream, BufferedInputStream, BufferedOutputStream 등
      - 문자 스트림: FileReader, FileWriter, BufferedReader, BufferedWriter 등
    - 기반 스트림과 보조 스트림: 스트림이 자료를 직접 읽거나 쓰는 기능을 제공하는 스트림이면 기반 스트림, 직접 읽거나 쓰는 기능은 없어 다른 스트림에 부가 기능을 제공하면 보조 스트림
      - 기반 스트림: FileInputStream, FileOutputStream, FileReader, FileWriter 등
      - 보조 스트림: InputStreamReader, OutputStreamWriter, BufferedInputStream, BufferedOutputStream 등
- 표준 입출력
  - System.out: 표준 출력용 스트림
  - System.in: 표준 입력용 스트림
  - System.err: 오류 메시지를 출력
  - System 클래스를 생성하지 않고 System.out을 사용할 수 있었던 이유는 out, in, err 모두 정적(static) 변수이기 때문이다.
  - 그 외 입력 클래스
    - Scanner 클래스: java.util 패키지에 있는 입력 클래스이다. 문자뿐 아니라 정수, 실수 등 다른 자료형도 읽을 수 있다. 또한 콘솔 화면뿐 아니라 파일이나 문자열을 생성자의 매개변수로 받아 자료를 읽어 올 수 있다. Scanner 클래스는 System.in으로 입력받는 것보다 다양한 메서드를 활용할 수 있기 때문에 자주 사용하는 클래스이다.
    - Console 클래스: System.in을 사용하지 않고 간단히 콘솔 내용을 읽을 수 있다. 
- 바이트 단위 스트림
  - InputStream: 바이트 단위로 읽는 스트림 중 최상위 스트림이다. InputStream은 추상 메서드를 포함한 추상 클래스로 하위 스트림 클래스가 상속받아 각 클래스 역할에 맞게 추상 메서드 기능을 구현한다.
  - 주로 사용하는 하위 클래스
    - FileInputStream: 파일에서 바이트 단위로 자료를 읽는다.
    - ByteArrayInputStream: Byte 배열 메모리에서 바이트 단위로 자료를 읽는다.
    - FilterInputStream: 기반 스트림에서 자료를 읽을 때 추가 기능을 제공하는 보조 스트림의 상위 클래스이다.
  - InputStream은 바이트 자료를 읽기 위해 다음 메서드를 제공
    - `int read()`: 입력 스트림으로부터 한 바이트의 자료를 읽는다.
    - `int read(byte b[])`: 입력 스트림으로부터 b[] 크기의 자료를 b[]에 읽는다.
    - `int read(byte b[], int off, int len)`
    - `void close()`
  - InputStream 중 가장 많이 사용하는 FileInputStream 클래스
    - 파일에서 바이트 단위로 자료를 읽어 들일 때 사용하는 스트림 클래스
    - 생성자
      - FileInputStream(String name): 파일이름 또는 경로를 매개변수로 받아 입력 스트림 생성
      - FileInputStream(File f): File 클래스 정보를 매개변수로 받아 입력 스트림 생성
      - `FileInputStream fis = new FileInputStream("input.txt")`
      - 예외 처리를 하면 프로그램이 중단되거나 멈춘 것이 아니라 마지막 부분까지 실행이 된다. 프로그램 수행을 중단시키지 않는 예외 처리의 중요성을 알 수 있다.
  - OutputStream: 바이트 단위로 쓰는 스트림 중 최상위 스트림이다.
    - FileOutputStream: 바이트 단위로 파일에 자료를 쓴다.
    - ByteArrayOutputStream: Byte 배열에 바이트 단위로 자료를 쓴다.
    - FilterOutputStream: 기반 스트림에서 자료를 쓸 때 추가 기능을 제공하는 보조 스트림의 상위 클래스이다.
    - OutputStream에서 제공하는 메서드는 다음과 같다.
      - `void write(int b)`: 한 바이트를 출력
      - `void write(byte[] b)`: b[] 배열에 있는 자료를 출력
      - `void write(byte b[], int off, int len)`
      - `void flush()`: 출력을 위해 잠시 자료가 머무르는 출력 버퍼를 강제로 비워 자료를 출력
      - `void close()`
    - FileOutputStream: 파일에 바이트 단위 자료를 출력하기 위해 사용하는 스트림
      - 생성자
        - FileOutputStream(String name)
        - FileOutputStream(String name, boolean append): append값이 true이면 파일 스트림을 닫고 다시 생성할 때 파일의 끝에 이어쓴다. 디폴트는 false.
        - FileOutputStream(File f,)
        - FileOutputStream(File f, boolean append)
- 문자 단위 스트림
  - Reader : 문자 단위로 읽는 스트림 중 최상위 스트림이다.
    - FileReader: 파일에서 문자 단위로 읽는 스트림 클래스
    - InputStreamReader: 바이트 단위로 읽은 자료를 문자로 변환해주는 보조 스트림
    - BufferedReader: 문자로 읽을 때 배열을 제공하여 한꺼번에 읽을 수 있는 기능을 제공해주는 보조 스트림
  - Reader 제공 메서드
    - `int read()`: 파일로부터 한 문자를 읽고 읽은 값을 반환
    - `int read(char[] buf)`: 파일로부터 buf 배열에 문자를 읽는다.
    - `int read(char[] buf, int off, int len)`
    - `void close()`
    - 바이트 단위로 읽어오면 문자가 깨지기 때문에 문자를 입출력할 때는 문자 스트림을 사용해야 한다.
  - Writer: 문자 단위로 출력하는 최상위 스트림이다.
    - FileWriter: 파일에 문자 단위로 출력하는 스트림 클래스
    - OutputStreamWriter: 파일에 바이트 단위로 출력한 자료를 문자로 변환해 주는 보조 스트림
    - BufferedWriter: 문자로 쓸 때 배열을 제공하여 한꺼번에 쓸 수 있는 기능을 제공해주는 보조 스트림
  - Writer 제공 메서드
    - `void write(int c)`: 한 문자를 파일에 출력
    - `void write(char[] buf)`: 문자 배열 buf 내용을 파일에 출력
    - `void write(char[] buf, int off, int len)`
    - `void write(String str)`: 문자열 str를 파일에 출력 
    - `void write(String str, int off, int len)`
    - `void flush()`
    - `void close()`
- 보조 스트림
  - 보조 스트림은 입출력 대상이 되는 파일이나 네트워크에 직접 쓰거나 읽는 기능은 없다. 보조 기능을 추가하는 스트림이다. 보조 스트림은 Wrapper 스트림이라고도 한다. 스스로는 입출력 기능이 없기 때문에 생성자의 매개변수로 다른 스트림을 받게 되면 자신이 갑싸고 있는 스트림이 읽거나 쓰는 기능을 수행할 때 보조 기능을 추가한다.
  - FiltereInputStream과 FilterOutputStream: 보조 스트림의 상위 클래스이다.
    - 생성자
      - protected FilterInputStream(InputStream in): 생성자의 매개변수로 InputStream을 받는다.
      - public FilterOutputStream(OutputStream out): 생성자의 매개변수로 OutputStream을 받는다.
    - 직접 생성하여 사용하는 경우는 거의 없고 이를 상속한 하위 클래스를 프로그램에서 많이 사용한다.
    - 하나의 기반 스트림에 여러 보조 스트림 기능이 추가된다.
  - InputStreamReader와 OutputStreamWriter: 바이트 단위로 자료를 읽으면 한글 같은 문자는 깨진다. 그래서 문자는 Reader나 Writer에서 상속받은 스트림을 사용해서 자료를 읽거나 써야 한다. 하지만 바이트 자료만 입력되는 스트림도 있다. 대표적으로 표준 입출력 System.in 스트림이다. 또한 네트워크에서 소켓이나 인터넷이 연결되었을 때 읽거나 쓰는 스트림은 바이트 단위인 InputStream과 OutputStream이다. 이렇게 생성된 바이트 스트림을 문자로 변환해 주는 보조 스트림이 InputStreamReader와 OutputStreamWriter이다.
    - 생성자
      - InputStreamReader(InputStream in)
      - InputStreamReader(InputStream in, Charset cs)
      - InputStreamReader(InputStream in, CharsetDecoder dec)
      - InputStreamReader(InputStream in, String charsetName)
      - 매개변수로 바이트 스트림과 문자 세트를 매개변수로 지정한다. 문자 세트는 문자를 표현하는 인코딩 방식이다. 바이트 자료가 문자로 변환될 때 지정된 문자 세트가 적용된다. 적용할 문자 세트를 명시하지 않으면 시스템이 기본으로 사용하는 문자 세트가 적용된다.
      - 대표적으로 자바에서 사용하는 UTF-16 문자 세트는 유니코드를 나타내는 문자 세트다.
    - 채팅 프로그램을 만든다고 할 때 바이트 단위로 사용하면 영어로만 채팅해야 한다. 이럴 때 읽어 들인 자료를 InputStreamReader나 OutputStreamWriter를 활용해 문자로 변환해 사용한다.
  - Buffered 스트림: 입출력이 한 바이트나 문자 단위로 이루어지면 그만큼 프로그램 수행 속도가 느려진다. Buffered 스트림은 내부적으로 8,912 바이트 크기의 배열을 가지고 있으며 이미 생성된 스트림에 배열 기능을 추가해 더 빠르게 입출력을 실행할 수 있는 버퍼링 기능을 제공한다. 한 바이트나 한 문자 단위로 처리할 때보다 **훨씬 빠르게 처리**할 수 있다.
    - 버퍼링 기능을 제공하는 스트림 클래스
      - BufferedInputStream: 바이트 단위로 읽는 스트림에 버퍼링 기능을 제공
      - BufferedOutputStream: 바이트 단위로 출력하는 스트림에 버퍼링 기능을 제공
      - BufferedReader: 문자 단위로 읽는 스트림에 버퍼링 기능을 제공
      - BufferedWriter: 문자 단위로 출력하는 스트림에 버퍼링 기능을 제공
    - 생성자
      - BufferedInputStream(InputStream in)
      - BufferedInputStream(InputStream in, int size): size는 버퍼 크기
  - 소켓 통신에서 스트림 사용하기
    - 자바는 통신을 위한 여러 클래스를 제공한다. 가장 간단한 소켓(통신에 사용하는 네트워크 연결 리소스) 통신을 한다고 가정하자. 소켓 통신을 위해 자바는 Socket 클래스를 제공한다.
    
    ```java
    Socket socket = new Socket();
    InputStream is = socket.getInputStream();
    ```
    InputStream은 바이트 단위 스트림이므로 한글을 쓰면 깨진다. 따라서 이를 문자로 변환해야 한다. 그리고 여기에 버퍼링 기능을 추가해 더 빠르게 읽고 쓸 수 있다. 

    ```java
    Socket socket = new Socket();
    BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
    BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
    ```
  - DataInputStream과 DataOutputStream: 지금까지 살펴본 스트림은 사람이 읽고 쓰는 텍스트 형식의 자료를 다루었다. DataInputStream과 DataOutputStream은 메모리에 저장된 0, 1 상태를 그대로 읽거나 쓴다. 자료형을 그대로 읽고 쓰는 스트림이기 때문에 같은 정수라도 자료형에 따라 다르게 처리한다. 따라서 자료를 쓸 때 사용한 메서드와 같은 자료형의 메서드로 읽어야 한다.
- 직렬화
  - 직렬화와 역직렬화: 클래스의 인스턴스가 생성되면 인스턴스의 상태, 즉 인스턴스 변수 값은 마치 생명체처럼 계속 변한다. 그런데 인스턴스를 어느 순간 상태를 그대로 저장하거나 네트워크를 통해 전송할 일이 있습니다. 이를 직렬화(serialization)이라고 한다. 그리고 저장된 내용이나 전송받은 내용을 다시 복원하는 것을 역직렬화(deserialization)라 한다. 다시 말해 직렬화란 인스턴스 내용을 연속 스트림으로 만드는 것이다. 자바에서 보조 스트림인 ObjectInputStream과 ObjectOutputStream을 사용하여 좀 더 쉽게 구현할 수 있다.
    - 생성자
      - ObjectInputStream(InputStream in)
      - ObjectOutputStream(OutputStream out)
  - Person 클래스를 하나 만들어 인스턴스로 생성 후 파일에 썼다가 복원하는 예제

  ```java
  package stream.serialization;

  import java.io.FileInputStream;
  import java.io.FileOutputStream;
  import java.io.IOException;
  import java.io.ObjectInputStream;
  import java.io.ObjectOutputStream;
  import java.io.Serializable;

  class Person implements Serializable{  // 직렬화하겠다는 의도 표시
    
    private static final long serialVersionUID = -1503252402544036183L;  // 버전 관리를 위한 정보

    String name;
    transient String job;
    
    
    public Person() {}

    public Person(String name, String job) {
      this.name = name;
      this.job = job;
    }
    
    public String toString()
    {
      return name + "," + job;
    }
  }

  public class SerializationTest {

    public static void main(String[] args) throws ClassNotFoundException {

      Person personAhn = new Person("안재용", "대표이사");
      Person personKim = new Person("김철수", "상무이사");
      
      try(FileOutputStream fos = new FileOutputStream("serial.out");
          ObjectOutputStream oos = new ObjectOutputStream(fos)){
        
        oos.writeObject(personAhn);  // 직렬화
        oos.writeObject(personKim);  // 직렬화
      
      }catch(IOException e) {
        e.printStackTrace();
      }
        
      try(FileInputStream fis = new FileInputStream("serial.out");
        ObjectInputStream ois = new ObjectInputStream(fis)){
        
        Person p1 = (Person)ois.readObject();  // 역직렬화
        Person p2 = (Person)ois.readObject();  // 역직렬화 
        
        System.out.println(p1);
        System.out.println(p2);
      }catch (IOException e) {
        e.printStackTrace();
      }
    }
  }
  ```
  - Serializable 인터페이스: 직렬화는 인스턴스 내용이 외부로 유출되는 것이므로 프로그래머가 직렬화를 하겠다는 의도를 표시해야 한다. 
  - transient 예약어: 직렬화 대상이 되는 클래스는 모든 인스턴스 변수가 직렬화되고 복원된다. 그런데 직렬화 될 수 없는 클래스(Socket 클래스)가 인스턴스 변수로 있다거나 직렬화하고 싶지 않은 변수가 있을 수 있다. 그럴 때 사용한다. 그러면 해당 변수 정보는 그 자료형의 기본값으로 저장된다. 
    - `transient String job;`
  - serialVersionUID를 사용한 버전 관리: 객체를 역직렬화할 때, 직렬화할 때의 클래스와 상태가 다르면 오류가 발생한다. 따라서 직렬화할 때 자동으로 serialVersionUID를 생성하여 정보를 저장한다. 그리고 역직렬화 할때 serialVersionUID를 비교하는데 만약 클래스 내용이 변경되었다면 클래스 버전이 맞지 않는다는 오류가 발생한다. 그런데 작은 변경에도 클래스 버전이 계속 바뀌면 네트워크로 서로 객체를 공유해서 일하는 경우에 매번 클래스를 새로 배포해야 하는 번거로움이 있다. 이런 경우 클래스의 버전 관리를 개발자가 할 수 있다. 자바가 제공하는 자바 설치 경로의 bin\serialver.exe 사용하거나 IDE에서 제공하는 기능을 사용하면 된다.
  - Externalizable 인터페이스: Serializable 인터페이스는 자료를 읽고 쓰는 데 필요한 부분을 프로그래머가 따로 구현하지 않는다. 하지만 Externalizable 인터페이스는 프로그래머가 구현해야 할 메서드가 있다. 객체의 직렬화와 역직렬화를 프로그래머가 직접 세밀하게 제어하고자 할 때, 메서드에 그 내용을 구현한다.
- 그 외 입출력 클래스
  - File 클래스: 별도의 입출력 기능은 없지만 파일 자체의 경로나 정보를 알 수 있고 파일을 생성할 수 있다.
  - RandomAccessFile 클래스: 입출력 클래스 중 유일하게 파일 입출력을 동시에 할 수 있는 클래스이다. 또한 지금까지 배운 스트림은 처음부터 아례대로 자료를 읽었지만 RandomAccessFile은 임의의 위치로 이동하여 자료를 읽을 수 있다. 스트림을 생성하지 않고 간단하게 파일에 자료를 쓰거나 읽을 때 사용하면 유용하다.
  - [예시 코드](https://github.com/easyspubjava/JAVA_LAB/blob/master/Chapter15/src/stream/others/RandomAccessFileTest.java)
