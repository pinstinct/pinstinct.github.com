---
layout: post
title: spring security
categories: java
tags: [java, spring]
---

## Spring Security, Servlet Application

> [Architecture](https://docs.spring.io/spring-security/reference/servlet/architecture.html)

### 구조

서블릿 기반 애플리케이션에서의 Spring Security의 고수준 아키텍처에 대해 다룹니다.

#### Filters

스프링 시큐리티의 서블릿은 서블릿 필터에 기반하고 있기 때문에 일반적으로 필터의 역할을 살펴보는게 유용합니다. 다음은 단일 HTTP 요청에 대한 핸들러의 전형적 계층 구조를 보여줍니다.

![](/image/filterchain.png)

클라이언트가 애플리케이션에 요청을 보내면 컨테이너는 FilterChain을 생성합니다. 이 FilterChain에는 요청 URI의 경로를 기반으로 HttpServletRequest를 처리해야 할 서블릿과 Filter 인스턴스가 포함됩니다. Spring MVC 애플리케이션에서 서블릿은 DispatcherServlet의 인스턴스입니다. 하나의 Servlet이 하나의 HttpServletRequest 및 HttpServletResponse를 처리할 수 있습니다. 그러나 하나 이상의 Filter를 사용하여 다음과 같은 작업을 수행할 수 있습니다.

- 하위 Filter 인스턴스 또는 Servlet의 호출을 방지합니다. 이 경우, Filter는 일반적으로 HttpServletResponse를 작성합니다.
- 하위 Filter 인스턴스 및 Servlet이 사용하는 HttpServletRequest 또는 HttpServletResponse를 수정합니다.

Filter의 강력한 기능은 전달되는 FilterChain에서 나옵니다.

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
	// do something before the rest of the application
    chain.doFilter(request, response); // invoke the rest of the application
    // do something after the rest of the application
}
```

필터가 영향을 미치는 대상은 하위 필터 인스턴스와 서블릿뿐이기 때문에 각 필터가 호출되는 순서는 매우 중요합니다.