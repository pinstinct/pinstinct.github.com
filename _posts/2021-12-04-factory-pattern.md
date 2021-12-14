---
layout: post
title: 팩토리 패턴 
categories: querydsl
tags: [java, design pattern]
excerpt: 결제로직을 팩토리 패턴을 이용해 리팩토링하기 위해 정리합니다.
---


## [디자인 패턴, Factory Method](https://refactoring.guru/design-patterns/factory-method)

## 취지

팩토리 메소드는 슈퍼클래스에서 객체를 생성하기 위한 인터페이스를 제공하지만, 서브클래스가 생성될 객체의 유형(type)을 변경할 수 있도록 하는 생성 디자인 패턴이다.

### 상속 - 슈퍼클래스, 서브클래스 

슈퍼클래스(=기반클래스, 부모클래스)로부터 **속성과 동작**을 상속받을 수 있다. 그 결과로 생기는 클래스를 서브클래스(=파생클래스, 자식클래스)라고 한다.

자바에서 상속은 다음과 같이 한다.

```java
class SubClass extends SuperClass
```

#### 상속 불가 클래스, 메소드

`final` 키워드를 통해 클래스 혹은 메소드가 상속 불가하다고 선언할 수 있다.

#### 접근 제한자

상속은 일방향성을 가진다. 슈퍼클래스에서 public이면, 서브클래스에서도 public이다. 반대로 슈퍼클래스는 서브클래스에 접근 할 수 없다.

- private : 해당 클래스에서만 사용 가능 
- protected : 슈퍼클래스와 서브클래스만 사용 가능
- public : 모든 클래스에서 사용 가능 

### 문제

물류 관리 프로그램을 만들고 있다고 상상해보자. 앱의 첫 번째 버전은 오직 트럭 운송만 다루기 때문에 대부분의 코드는 `Truck` 클래스 내에 있다.

앱이 꽤 유명해진다. 매일 해상 운송 회사로부터 해상 물류를 포함해 달라는 수십 개의 요청을 받는다.

좋은 소식이지? 하지만 코드는 어떤가? 현재 대부분의 코드는 `Truck` 클래스와 연결되 있다. 앱에 `Ships` 추가하는 것은 전체 코드를 수정해야만 한다. 게다가 다른 종류의 교통 수단을 추가한다면, 아마 이러한 모든 변경이 필요할지도 모른다.

### 해결

팩토리 메소드 패턴은 (`new` 연산자 사용하여) 직접 객책 생성자를 호출하는 대신 특별한 팩토리 메소드를 호출할 것을 제안한다. 객체는 여전히 `new` 연산자를 통해 생성되지만, 팩토리 메소드 내에서 호출된다. 팩토리 메소드가 반환한 객체는 *products*라고 한다.

![_](https://refactoring.guru/images/patterns/diagrams/factory-method/solution2-en.png?id=db5de848c1d490b83566)

언뜻 보기에는 이 변경이 무의미해 보일 수 있다. 방금 생성자 호출을 이쪽에서 저쪽으로 옮겼다. 그러나 이제 서브클래스에서 팩토리 메소드를 재정의(오버라이드)할 수 있고 메소드에 의해 생성되는 *products* 클래스를 변경할 수 있다.

여기에 약간의 제한이 있다. 서브클래스는 이러한 *products*에 공통 기본 클래스 혹은 인터페이스가 있는 경우에만, 다른 유형(type)의 *products*를 반환할 것이다. 또한, 기본 클래스의 팩토리 메소드는 인터페이스에 선언된 반환 유형을 가져야만 한다.

![_](https://refactoring.guru/images/patterns/diagrams/factory-method/solution2-en.png?id=db5de848c1d490b83566)

예를 들어, `Truck`과 `Ship` 클래스는 `deliver`라는 메소드를 선언한 `Transport` 인터페이스를 구현해야 한다. 각 클래스는 `deliver` 메소드를 다르게 구현한다. 트럭은 육로로 배달하고, 선박은 해상으로 배달한다. `RoadLogistics` 클래스 안에 있는 팩토리 메소드는 `truck` 객체를 반환하고, `SeaLogistics` 클래스 안에 있는 팩토리 메소드는 `ships`을 반환한다.

![_](https://refactoring.guru/images/patterns/diagrams/factory-method/solution3-en.png?id=b6f53911fc0d56f9ef99)

팩토리 메소드를 사용하는 코드(*클라이언트* 코드라고도 불림)는 다양한 서브클래스로 반환된 실제 *products*의 차이를 알수 없다. *클라이언트*는 모든 *products*를 추상적으로 취급한다. *클라이언트*는 모든 `transport` 객체가 `deliver` 메소드를 지원해야 한다는 것을 알지만, 정확히 어떻게 동작하는지 중요하지 않다.

### 구조

![_](https://refactoring.guru/images/patterns/diagrams/factory-method/structure-indexed.png?id=4c603207859ca1f939b1)

1. `Product`, 생성자와 서브클래스에 의해 만들어진 모든 객체의 공통 인터페이스를 선언
2. `Concrete Products`, `product` 인터페이스의 구현
3. `Creator` 클래스는 새로운 `product` 객체를 반환하는 팩토리 메소드를 선언. 이 메소드의 반환 유형(type)이 `product` 인터페이스와 일치하는 것이 중요하다.

팩토리 메소드를 추상으로 선언하여 모든 서브클래스가 자신의 메소드 버전을 구현하도록 강제할 수 있다. 대신, 기본 팩토리 메소드는 기본 `product` 유형을 반환할 수 있다.

주의, `product` 생성이라는 이름에도 불구하고 `creator`의 주요 의무가 아니다. 대게, `creator` 클래스는 *products*와 관련된 주요 비지니스 로직을 가지고 있다. 팩토리 메소드는 이 로직을 구체적인 제품 클래스(`concrete product`)에서 분리하는데 도움이 된다. 


4. `Concrete Creators` 기본 팩토리 메소드를 재정의이 하므로 다른 유형의 `product`를 반환

팩토리 메소드가 항상 새로운 인스턴스를 생성하지 않음을 주의하라. 캐시, 객체 풀(pool), 다른 소스에서 기존 객체를 반환할 수 있다.

### 의사코드 

예는 클라이언트 코드를 UI 클래스들과 연결하지 않고, 크로스 플랫폼 UI 요소들을 생성하기 위해 어떻게 팩토리 메소드를 사용할 수 있는지 보여준다.

![_](https://refactoring.guru/images/patterns/diagrams/factory-method/example.png?id=67db9a5cb817913444ef)

기본 dialog 클래스는 다른 UI 요소들을 사용해 창을 만든다. 다양한 운영체제에서 이러한 요소들은 조금 다르게 보이지만 일관되게 작동한다. 윈도우 버튼은 여전히 리눅스 버튼이다.(?)

팩토리 메소드가 작동하면, 각 운영체제마다 dialog 로직을 다시 작성할 필요가 없다. 만약 기본 dialog 클래스 안에서 버튼들을 생성하는 팩토리 메소드를 선언하면, 우리는 윈도우 스타일 버튼을 반환하는 팩토리 메소드를 가진 dialog 서브 클래스를 나중에 만들 수 있다. 서브클래스는 기본 클래스에서 대부분의 dialog의 코드를 상속하지만 팩토리 메소드 덕분에 화면에 윈도우 모양 버튼을 렌더링 할 수 있다.

이 패턴이 작동하려면, 기본 dialog 클래스는 추상 버튼(모든 버튼이 따르는 기본 클래스 또는 인터페이스)과 함께 동작해야만 한다. 

물론, 이런 접근을 다른 UI 요소들에 적용할 수 있다. 그러나, 각각 새로운 팩토리 메소드와 함께 dialog를 추가할 수 있다. 점점 추상 팩토리 패턴가 가까워진다. 두려워 말라, 이 패턴은 나중에 다룰것이다.

```java
// The creator class declares the factory method that must
// return an object of a product class. The creator's subclasses
// usually provide the implementation of this method.
class Dialog is
    // The creator may also provide some default implementation
    // of the factory method.
    abstract method createButton():Button

    // Note that, despite its name, the creator's primary
    // responsibility isn't creating products. It usually
    // contains some core business logic that relies on product
    // objects returned by the factory method. Subclasses can
    // indirectly change that business logic by overriding the
    // factory method and returning a different type of product
    // from it.
    method render() is
        // Call the factory method to create a product object.
        Button okButton = createButton()
        // Now use the product.
        okButton.onClick(closeDialog)
        okButton.render()


// Concrete creators override the factory method to change the
// resulting product's type.
class WindowsDialog extends Dialog is
    method createButton():Button is
        return new WindowsButton()

class WebDialog extends Dialog is
    method createButton():Button is
        return new HTMLButton()


// The product interface declares the operations that all
// concrete products must implement.
interface Button is
    method render()
    method onClick(f)

// Concrete products provide various implementations of the
// product interface.
class WindowsButton implements Button is
    method render(a, b) is
        // Render a button in Windows style.
    method onClick(f) is
        // Bind a native OS click event.

class HTMLButton implements Button is
    method render(a, b) is
        // Return an HTML representation of a button.
    method onClick(f) is
        // Bind a web browser click event.


class Application is
    field dialog: Dialog

    // The application picks a creator's type depending on the
    // current configuration or environment settings.
    method initialize() is
        config = readApplicationConfigFile()

        if (config.OS == "Windows") then
            dialog = new WindowsDialog()
        else if (config.OS == "Web") then
            dialog = new WebDialog()
        else
            throw new Exception("Error! Unknown operating system.")

    // The client code works with an instance of a concrete
    // creator, albeit through its base interface. As long as
    // the client keeps working with the creator via the base
    // interface, you can pass it any creator's subclass.
    method main() is
        this.initialize()
        dialog.render()
```

### 적용 가능성

- 정확한 유형들(types)과 객체들의 의존성을 미리 모르는 경우, 팩토리 메소드를 사용하라.

팩토리 메소드는 product 생성 코드를 product를 실제 사용하는 코드와 분리한다. 따라서 다른 코드들로 부터 독립적으로 product 생성 코드를 확장하기 쉽다. 예를 들어, 앱에 새로운 product 유형(type)을 추가한다면 새로운 생성자 서브클래스를 만들고 그 안에 팩토리 메소드를 재정의하면 된다.

- 라이브러리나 프레임워크의 사용자에게 내부 구성요소를 확장하는 방법을 제공할 경우, 팩토리 메소드를 사용하라. 

상속은 라이브러리나 프레임워크에서 기본 동작을 확장하는 가장 쉬운 방법이다. 그러나 프레임워크가 표준 구성요소 대신 서브클래스를 사용한다는 것을 어떻게 인식할 수 있는가?

해결책은 프레임워크 구조 구성요소 코드를 단일 팩토리 메소드로 줄이는 것이다. 그리고 누구나 이 메소드를 재정의하거나 구성요소를 확장할 수 있도록 한다. 

어떻게 동작하는지 봅시다. 오픈 소스 UI 프레임워크를 사용해 앱을 만든다고 가정하자. 앱은 둥근 버튼이 필요하지만, 프래임워크는 오직 사각 버튼만 제공한다. `RoundButton` 서브클래스로 표준 `Button` 클래스를 확장한다. 그러나 이제 메인 `UIFramework` 클래스에게 기본 클래스 대신 새 버튼 서브클래스를 사용한다고 알려야 한다.

이를 달성하기 위해, 기본 프레임워크 클래스로 부터 `UIWithRoundButtons` 서브클래스를 생성하고 `createButton` 메소드를 재정의한다. 기본 클래스의 메소드가 `Button` 객체를 반환하는 반면에, 서브클래스는 `RoundButton` 객체를 반환한다. 이제 `UIFramework` 대신 `UIWithRoundButtons` 클래스를 사용하면 된다.

- 매번 새로 만드는 대신 기존 객체를 재사용해 시스템 자원을 절약하고 싶다면 팩토리 메소드를 사용하라.

데이터베이스 연결, 파일시스템, 네트워크 자원과 같은 자원을 많이 사용하는 객체를 다룰 때, 이런 필요를 종종 경험한다.

기존 객체를 재사용하는 법을 생각해보자.
1. 생성된 모든 객체를 추적하기 위해 저장소를 생성한다.
2. 객체에 대한 요청이 들어올 때, 프로그램은 풀에서 사용 가능한 객체를 찾는다.
3. 그리고 그 객체를 클라이언트 코드에 반환한다.
4. 만약, 사용 가능한 객체가 없다면, 프로그램은 새로운 객체를 생성하고 풀에 추가한다.

코드가 많아 진다. 그리고 중복 코드로 프로그램을 오염하지 않도록 이 코드가 모두 한 장소에 위치해야 합니다. 

아마 이 코드가 위치할 가장 명백하고 편한 곳은 재사용하려는 객체를 가진 클래스 생성자이다. 하지만 생성자는 항상 정의된 **새로운 객체**를 반환한다. 기존(existing) 객체를 반환할 수 없다.

그러므로, 새로운 객체를 생성할 수 있고 기존 객체를 재사용할 수 있는 적당한 메소드가 필요하다. 이건 팩토리 메소드와 매우 흡사하다.


### 구현 방법

1. 모든 *products*가 같은 인터페이스를 따르게 한다. 이 인터페이스는 모든 *product*에 의미가 있는 메소드를 선언해야 한다.
2. 생성자 클래스 안에 빈 팩토리 메소드를 추가한다. 메소드의 반환 유형(type)은 공통 *product* 인터페이스와 일치해야 한다.
3. 생성자 코드에서 *product* 생성자에 대한 모든 참조를 찾는다. 하나씩 *product* 생성 코드를 팩토리 메소드로 추출하면서, 팩토리 메소리로 호출하는 것으로 교체한다.

반환 *product*의 유형을 제어하기 위해 팩토리 메소드에 임시 파라미터를 추가해야 할 수도 있다.

이 시점에서, 팩토리 메소드의 코드는 꽤 보기 흉할 수 있다. 인스턴스화 할 *product* 클래스를 선택하는 `switch` 연산자를 가질수도 있다. 그러나 걱정하지 마라, 곧 고칠 것이다.

4. 이제, 팩토리 메소드에 나열된 각 *product* 유형의 생성자 서브클래스 세트를 만든다. 서브클래스의 팩토리 메소드를 재정의하고, 기본 메소드에서 적절한 크기의 생성자 코드로 추출한다.

5. 만약 너무 많은 *product* 유형이 있고 모든 서브클래스를 만드는 것이 합리적이지 않은 경우, 베이스 클래스에서 제어 파라미터를 재사용할 수 있다.

예를 들어, 다음과 같은 클래스 구조가 있다고 가정해보자. `AirMail`, `GroundMail` 같은 몇개의 서브크래스를 가진 기본 `Mail` 클래스. `Transport` 클래스는 `Plane`, `Truck`, `Train`이다. `AirMail` 클래스는 오직 `Plane` 객체를 사용하는 반면, `GroundMail`은 `Truck`과 `Train` 객체와 작동합니다. 두 가지 경우를 처리하기 위해 `TrainMail`이라는 새로운 서브클래스를 생성할 수 있지만, 다른 옵션이 있다. 클라이언트 코드가 `GroundMail` 클래스의 팩토리 메소드에게 인수를 넘겨 받기 원하는 *product*을 제어할 수 있다.

6. 모든 추출이 끝난다면, 기본 팩토리 메소드는 비어 추상화할 수 있다. 남아 있는 것이 있다면, 메소드의 기본 동작으로 만들 수 있다.

### 장점과 단점

#### 장점

- 생성자와 구체적인 *products* 간의 긴밀한 결합을 피한다.
- 단일 책임 원칙. *prodcut* 생성 코드를 프로그램의 한 곳으로 이동할 수 있고, 코드를 더 쉽게 지원할 수 있다.
- 개방/폐쇄 원칙. 클라이언트 코드에 손상없이 프로그램에 *products*의 새로운 유형을 도입할 수 있다.


#### 단점

- 패턴을 구현하기 위해 많은 새로운 서브클래스를 도입해야 하므로, 코드가 더 복잡해질 수 있다. 베스트 케이스 시나리오는 생성자 클래스들의 기존 계층구조에 패턴을 도입하는 경우다.


### 다른 패턴과의 관계

- 많은 디자인은 팩토리 메소드(덜 복잡하고 하위 클래스를 통해 사용자 정의가능)를 사용하여 시작하고 추상 팩토리, 프로토타입 또는 빌더(더 유연하지만 복잡한)로 발전한다.

- 추상 팩토리 클래스는 종종 팩토리 메소드 세트를 기반으로 하지만, 이 클래스들의 메소드를 구성하기 위해 프로토타입을 사용할 수도 있다.

- 이터레이터와 함께 팩토리 메소드를 사용하여 컬렉션 서브클래스가 컬렉션과 호환되는 다른 유형의 이터레이터를 반환하도록 할 수 있다.

- 프로토타입은 상송 기반이 아니므로 단점이 없다. 반면에 프로토타입은 복제된 객체의 복잡한 초기화가 필요하다. 팩토리 메소드는 상속 기반이지만, 초기화 단계가 필요없다.

- 팩토리 메소드는 템플릿 메소드의 전문화이다. 동시에, 팩토리 메소드는 커다란 템플릿 메소드를  제공한다.