---
layout: post
title: form-data와 null 처리 
categories: experience
tags: [web, springboot, project]
---


## 문제 상황

스프링부트 프로젝트 controller에서 이미지를 다룰 때 발생한 이슈이다. DTO 클래스 MultipartFile 필드를 통해 이미지를 매핑할 때 발생한 문제이다.

```java
@RequestMapping(value = "/uploadImage", method = RequestMethod.POST)
@RequestBody
public ResponseEntity uploadImage(HttpServletRequest request, @Valid ImageDto dto) throws Exception {
  // ...
  return new ResponseEntity("", HttpStatus.OK);
}
```

```java
@NoArgsConstructor
@Getter
@Setter
public class ImageDto {
  // ...
  private String image_description;
  private MultipartFile image;
}
```

클라이언트에서 image를 `null`로 보내게 되면, 파일 타입이 아닌 텍스트 타입으로 요청이 오게 된다. 때문에 서버에서는 타입이 맞지 않게 되어 400 에러가 발생한다.

### form 태그 enctype 속성

form 데이터가 서버로 제출될 때, 데이터가 인코딩되는 방법을 명시하는 속성이다.

- application/x-www-form-urlencoded: 기본값, 모든 문자들을 서버로 보내기 전에 인코딩함을 명시
- text/plain: 공백 문자(space)는 "+" 기호로 변환하지만, 나머지 문자는 모두 인코딩하지 않음을 명시
- multipart/form-data: 모든 문자를 인코딩하지 않음을 명시, 파일이나 이미지를 서버로 전송할 때 사용

> http://www.tcpschool.com/html-tag-attrs/form-enctype

### @ModelAttribute

- @RequestMapping과 함께 사용
- 보안 이슈가 있으므로 Spring Web MVC, Spring WebFlux 매뉴얼을 참고

### @RequestParam

- 요청 파라미터(request parameter)가 메소드 파라미터(method parameter)와 바인딩되야 함을 나타내는 어노테이션
- Spring MVC, Spring WebFlux에서 지원 
  - Spring MVC에서 요청 파라미터(request parameter)
    - 쿼리 파라미터, 폼 데이터, 멀티파트 요청
  - Spring WebFlux에서 요청 파라미터(request paramenter)
    - only 쿼리 파라미터
    - 쿼리 파라미터, 폼 데이터, 멀티파트 요청 모두에서 작동하게 하려면 @ModelAttribue 어노테이션을 사용한다.
- 메소드 파라미터(method parameter) 타입은 Map

### @RequestBody

- HTTP 요청의 body를 자바 객체로 매핑하는 어노테이션
- POST, PUT 요청 본문에 json, xml 등의 데이터를 자바 객체로 변환

## 해결 방법 

Controller에서 DTO 클래스로 매핑하던 파일을 `@RequestParam`으로 매핑할 수 있도록 수정했다.

```java
@RequestMapping(value = "/uploadImage", method = RequestMethod.POST)
@RequestBody
public ResponseEntity uploadImage(HttpServletRequest request
    , @Valid ImageDto dto
    , @RequestParam image) throws Exception {
  // ...
  return new ResponseEntity("", HttpStatus.OK);
}
```

```java
@NoArgsConstructor
@Getter
@Setter
public class ImageDto {
  // ...
  private String image_description;
  // private MultipartFile image;
}
```
