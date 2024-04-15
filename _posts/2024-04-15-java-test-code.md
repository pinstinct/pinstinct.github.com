---
layout: post
title: 자바 프로젝트에서 테스트 코드 
categories: java
tags: [java, test, project]
---

### 테스트 장단점

#### 장점

- 코드 품질 향상
- 문서화
- 리팩토링을 안심하고 진행 가능

#### 단점

- 개발 시간 증가
- 유지 보수 비용
- 학습 곡선 

> [테스트 코드는 왜 만들까?](https://yozm.wishket.com/magazine/detail/1964/)

### 테스트 종류

- 단위 테스트(Unit Test): 일반적으로 함수, 메서드 단위
- 통합 테스트(Integration Test): 서로 다른 모듈간의 상호작용을 테스트
- E2E 테스트(End to End Test): 사용자 시나리오를 검증한다. 예를 들어 회원가입 과정을 전체적으로 테스트 하는 것이다.

테스트를 이론적으로 3가지 종류로 분류한다. 하지만 E2E 테스트를 코드로 작성하는 것은 과도한 시간과 노력이 필요하기 때문에 단위 테스트와 통합 테스트에 대해서만 다룬다. 

통합 테스트는 의존성이 있는 기능에 대한 테스트인데, 실제 테스트 코드를 작성하다 보면 단위 테스트와 통합 테스트의 경계가 모호해진다. 의존성을 제거하기 위해 스프링부트에서 제공하는 기능을 사용하지 않고 테스트를 작성하다 보면, 내가 작성한 코드를 테스트하는건지, 스프링부트 자체를 테스트하는건지 모호해지기 때문이다.

그래서 개인적으로 Endpoint(controller)에 대한 테스트 코드를 통합 테스트로 정의하고, 그 외의 테스트들을 단위 테스트로 정의하고 테스트 코드를 작성한다.

> [스프링부트 테스트](https://brunch.co.kr/@springboot/207)

### 내가 테스트코드를 작성하는 이유

- 모든 케이스를 포스트맨으로 테스트하기 귀찮아서
- 리포트 받은 버그들이 기록으로 남길 수 있어서
- 수정 사항으로 인해 다른 코드에 영향을 끼치지 않는다는 확신을 갖고 싶어서
- hotfix 반영 시, 빠르게 수정하고 싶어서


### 테스트하기 좋은 코드

- 테스트 코드를 작성하는데 겪는 어려움은 경험 부족 혹은 숙련도 때문이 아니다. 테스트를 작성하기 어려운 구현체 때문이다.
- 테스트 코드 작성이 쉽게 구현된 코드라면 별도의 mock 라이브러리의 도움 없이 테스트 코드 작성이 쉽다.
- 좋은 디자인으로 구현된 코드는 대부분 테스트하기 쉽다.

> [1. 테스트하기 좋은 코드 - 테스트하기 어려운 코드](https://jojoldu.tistory.com/674)

### 테스트 코드를 작성하는 나만의 철학

- 테스트하고자 하는 대상이 한눈에 보이도록 최대한 간단하게 작성
- 하나의 테스트에 최소한의 검증(assert)
- 과도한 추상화 금지  
- 성공 케이스 반드시는 작성
- 경계값에 대한 테스트 작성
- 외부 의존성이 있는 경우가 아니라면, 과도한 mock test 지양
  - 의존성 없는 완벽한 단위 테스트를 작성하고 있다면 상관없는 항목이다. 

### Mock

- Test Double: 테스트를 진행하기 어려운 경우, 테스트를 진행할 수 있도록 만들어 주는 객체
  - 상태 기반
    - Dummy Object: 인스턴스화 할 수 있는 수준의 객체
    - Test Stub: dummy object가 실제로 동작하는 것처럼 보이게 만든 객체
    - Fake Object: stub과 유사하나 stub은 하나의 인스턴스를 대표하고, fake는 여러개의 인스턴스를 대표
    - Test Spy: 테스트에 사용되는 객체, 메소드의 사용 여부 및 정상 호출 여부를 기록하고 알려줌 
  - 행위 기반 
    - Mock Object: 행위를 검증하기 위해 사용되는 객체

> [Mock 이란?](https://velog.io/@ejung803/Mock-%EC%9D%B4%EB%9E%80)

> [Mock 객체란 무엇일까? 왜 써야될까?](https://happy-coding-day.tistory.com/entry/Mock-%EA%B0%9D%EC%B2%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C-%EC%99%9C-%EC%8D%A8%EC%95%BC%EB%90%A0%EA%B9%8C)

#### 장점

- 테스트 시간 단축
- 불필요한 리소스 사용을 감소
- 제어할 수 없는 영역 대체 가능

#### 단점

- 테스트 코드 유지에 복잡성 증가
  - 의존한 코드들이 조금이라도 변경되면 테스트 코드가 모두 깨져, 모두 수정해야 함
- 실제 객체로 작동했을 때, 작동하지 않을 수 있음
  - mock은 동작하길 기대하는 것 일뿐 실제 구현체는 의도한대로 동작하지 않을 수 있음

> [Mock 객체 남용은 테스트 코드를 망친다.](https://medium.com/@chanhyeonglee/mock-%EA%B0%9D%EC%B2%B4-%EB%82%A8%EC%9A%A9%EC%9D%80-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C%EB%A5%BC-%EB%A7%9D%EC%B9%9C%EB%8B%A4-f38129e5d40a)

> [Mockito, 이대로 괜찮은가?](https://tecoble.techcourse.co.kr/post/2020-10-16-is-ok-mockito/)



### BDDBehavior Driven Development) 스타일의 테스트 코드

#### Given - When - Then 패턴

- Given : 시나리오 진행에 필요한 값을 설정한다.
- When : 시나리오를 진행하는데 필요한 조건을 명시한다.
- Then : 시나리오를 완료했을 때 보장해야 하는 결과를 명시한다.

> [[Spring Boot + Mockito] 09. BDD (Behavior Driven Development) 스타일로 Mockito 활용하기](https://wiki.yowu.dev/ko/dev/Mockito/Spring-Boot-Mockito-Series/9-Using-Mockito-in-BDD-style)

> [Given-When-Then Pattern](https://brunch.co.kr/@springboot/292)

#### Describe - Context - It 패턴

상황 설명보다 테스트 대상을 주인공 삼아 설명하는데 적합한 패턴으로 다음과 같은 장점이 있다.

- 테스트 코드를 계층 구조로 만들어 준다.
- 테스트 코드를 추가하거나 읽을 때 스코프 범위만 신경쓰면 된다.

```java
// 작성 예시
@DisplayName("휴대폰 / 이메일 점유인증 요청")  // controller operation summary
@Nested
class DescribeAddOccupancyAuth {  // Describe + 메서드 이름

   @DisplayName("모든 파라미터가 주어지면")
   @Nested
   class ContextWithAllParameter extends RelatedObject {  // Context + with/when+ *

       @DisplayName("회원 인증 정보를 저장하고 결과를 반환한다")
       @Test
       void itSaveMemberAuth() throws Exception {  // it + *
           AddOccupancyAuthRequestDto dto = getAddOccupancyAuthRequestDtoObj(AuthType.EMAIL,
               "");
           AddOccupancyAuthResponseDto result = memberCommandService.addOccupancyAuth(dto);
           assertThat(result).isNotNull();
       }
   }
}
```

> [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/)


### 내가 DCI 패턴의 테스트 코드를 작성하는 이유

- 하나의 테스트 대상에 케이스가 많아지면 가독성이 떨어져, 계층 구조가 필요했다.
- Given-When-Then 패턴은 주석이 증가하고, 패턴을 강제할 수가 없다. 
- given이 길어져 테스트 내용이 잘 보이지 않는다.
