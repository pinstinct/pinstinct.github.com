---
layout: post
title: swagger-ui를 통해 요청할 때 발생하는 415 (unsupported media type) 에러 해결
categories: experience
tags: [java, project]
---

### 문제 상황

@RequestPart로 MultipartFile과 DTO를 받는 controller에 spring swagger ui를 통해 요청할 경우, 다음과 같은 에러가 발생합니다.

```shell
ERROR c.e.a.global.advice.ExceptionAdvice.defaultException - Content-Type 'application/octet-stream' is not supported
```

하지만 크롬 개발자 도구를 통해 요청 헤더와 페이로드를 확인해도 'application/octet-stream'는 찾아볼 수 없었습니다.

![](/image/swagger-ui-request-headers.png)
![](/image/swagger-ui-form-data.png)

### contentType이 null인 경우 

stackTrace를 하나씩 따라가며 디버깅했습니다.

```shell
ERROR c.e.a.global.advice.ExceptionAdvice.defaultException - Content-Type 'application/octet-stream' is not supported
org.springframework.web.HttpMediaTypeNotSupportedException: Content-Type 'application/octet-stream' is not supported 
at org.springframework.web.servlet.mvc.method.annotation.AbstractMessageConverterMethodArgumentResolver.readWithMessageConverters(AbstractMessageConverterMethodArgumentResolver.java:213)
at org.springframework.web.servlet.mvc.method.annotation.RequestPartMethodArgumentResolver.resolveArgument(RequestPartMethodArgumentResolver.java:141)
# ...
```

AbstractMessageConverterMethodArgumentResolver 클래스의 readWithMessageConverters 메서드를 첫번째 브레이크 포인트로 잡았습니다.

#### AbstractMessageConverterMethodArgumentResolver 클래스

HttpMessageConverter를 사용하여 request body에서 메서드 인수 값을 읽어 해결하는 기본 클래스

> [Class AbstractMessageConverterMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/AbstractMessageConverterMethodArgumentResolver.html)

#### readWithMessageConverters 메서드 

주어진 HttpInputMessage에서 메시지 컨버터를 사용하여 메서드 인수의 값을 읽어오는 메서드로 다음과 같은 작업을 합니다.

- 주어진 parameter, targetType을 기반으로 targetClass 및 contextClass 확인
- inputMessage(request)의 contentType을 확인하고, 없는 경우 기본값으로 `MediaType.APPLICATION_OCTET_STREAM` 설정
- inputMessage(request)의 httpMethod를 가져옴
- messageConverters 목록을 반복하며 contextClass, targetClass, contentType에 맞는 메시지 컨버터를 찾아 요청 본문을 읽어 반환

여기서 두번째 항목에 대한 코드를 자세히 보겠습니다.

```java
   @Nullable
    protected <T> Object readWithMessageConverters(HttpInputMessage inputMessage, MethodParameter parameter, Type targetType) throws IOException, HttpMediaTypeNotSupportedException, HttpMessageNotReadableException {
        Class<?> contextClass = parameter.getContainingClass();
        Class<T> targetClass = (targetType instanceof Class clazz ? clazz : null);
        // ...
        if (contentType == null) {
            noContentType = true;
            contentType = MediaType.APPLICATION_OCTET_STREAM;
        }
        // ...
}
```

디버깅하여 살펴보니, **contentType**이 null로 들어옵니다. 때문에 contentType이 `application/octet-stream`으로 설정됩니다. 하지만 스프링은 해당 타입을 지원하지 않기 때문에 에러가 발생하게 됩니다.

> [[Spring] Swagger + RequestPart를 통해 파일, Dto 동시 요청 시 발생 에러 핸들링](https://one-armed-boy.tistory.com/entry/Swagger-RequestPart%EB%A5%BC-%ED%86%B5%ED%95%B4-%ED%8C%8C%EC%9D%BC-Dto-%EB%8F%99%EC%8B%9C-%EC%9A%94%EC%B2%AD-%EC%8B%9C-%EB%B0%9C%EC%83%9D-%EC%97%90%EB%9F%AC)

#### (+) RequestPartMethodArgumentResolver 클래스

다음 메서드 인수를 해결하는 클래스
  - @RequestPart로 어노테이션 지정된 경우
    - 해당 파트의 내용은 request 'Content-Type'을 고려하여 HttpMessageConverter를 통해 메서드 인수를 해결합니다. 이는 일반적인 요청의 내용을 기반으로 인수를 해결하는 @RequestBody가 하는 것과 유사합니다.
  - Spring의 MultipartResolver 추상화와 함께 MultipartFile 유형
  - Servlet 멀티파트 요청과 함께 jakarta.servlet.http.Part 유형

[
Class RequestPartMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/mvc/method/annotation/RequestPartMethodArgumentResolver.html)

### 해결 방법 

문제 해결을 위해 찾아보니 다음과 같이 공식 repository에서 open 이슈인 것을 확인했습니다.

> [multipart/form-data should support custom content-type selection. #6462
](https://github.com/swagger-api/swagger-ui/issues/6462)


그래서 댓글들을 활용해 프로젝트에 적용했습니다.

```java
public SwaggerConfig(MappingJackson2HttpMessageConverter converter) {
        /**
         * content-type이 null인 경우 spring에서는 application/octet-stream으로 인식하여 처리하게 됨
         * 하지만 application/octet-stream에 대한 기본 컨버터가 없기 때문에 MappingJackson2HttpMessageConverter에 application/octet-stream을 추가 함
         */
        List<MediaType> supportMediaTypes = new ArrayList<>(converter.getSupportedMediaTypes());
        supportMediaTypes.add(new MediaType("application", "octet-stream"));
        converter.setSupportedMediaTypes(supportMediaTypes);
    }
```

swagger 관련 설정 클래스가 생성될 때, 지원하는 contentType에 'application/octet-stream'을 추가해 문제를 해결했습니다.
