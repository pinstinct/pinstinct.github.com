---
layout: post
title: DCI 패턴 테스트 코드
categories: java
tags: [java, test, project]
---

### DCI 패턴(Describe 방식)

보통 given, when, then 방식(Behavior 방식)을 많이 사용하는데 주석을 사용하기 때문에 강제성이 없다. 때문에 테스트 코드에 DCI 패턴을 도입했다.

- Describe: 테스트 대상을 명시 (클래스, 메소드 이름 명시)
- Context: 테스트 대상이 놓인 상황을 설명 (파라미터 설명)
  - with, when으로 시작하도록 한다.
- It: 테스트 대상의 행동을 설명 (무엇을 리턴하는지 설명)

이어서 읽었을 때 비문이 아닌 하나의 좋은 문장이 되도록 작성하는 것이 중요하다.

> [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/)

### 적용 예시

#### controller 테스트

```java
import com.limhm.MainTests;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockMultipartFile;
import org.springframework.test.web.servlet.request.MockMultipartHttpServletRequestBuilder;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.util.MultiValueMap;

import java.time.LocalDateTime;
import java.time.ZoneId;
import java.time.format.DateTimeFormatter;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.multipart;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;

public class BannerControllerTest extends MainTests {
    String BASE_URL = "/banner";

    // multipart는 POST로만 전송가능하기 때문에 builder를 별도로 생성
    MockMultipartHttpServletRequestBuilder multipartPutBuilder(String url) {
        MockMultipartHttpServletRequestBuilder builder = multipart(url);
        builder.with(request -> {
           request.setMethod(HttpMethod.PUT.name());
           return request;
        });
        return builder;
    }

    abstract class BannerRequest {
        private String getDateTime(int days) {
            LocalDateTime now = LocalDateTime.now(ZoneId.of("Asia/Seoul"));
            LocalDateTime result = now.plusDays(days);
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
            return result.format(formatter);
        }

        MultiValueMap<String, String> getObject(int start, int end) {
            MultiValueMap<String, String> request = new LinkedMultiValueMap<>();
            request.add("banner_type", "event");
            request.add("banner_subject", "hello");
            request.add("banner_start_date", getDateTime(start));
            request.add("banner_end_date", getDateTime(end));
            return request;
        }

        MockMultipartFile getFile() {
            return new MockMultipartFile(
                    "banner_img",
                    "test.jpg",
                    MediaType.MULTIPART_FORM_DATA_VALUE,
                    "image".getBytes()
            );
        }
    }

    @DisplayName("배너 생성 테스트")
    @Nested
    class DescribeBannerRegistration {
        @DisplayName("헤더 인증정보가 없다면")
        @Nested
        class ContextWithoutAuth extends BannerRequest {
            @DisplayName("401을 리턴한다")
            @Test
            void itReturns401() throws Exception {
                mockMvc.perform(multipart(BASE_URL)
                        .file(getFile())
                        .params(getObject(0, 1)))
                        .andExpect(MockMvcResultMatchers.status().isUnauthorized())
                        .andDo(print());
            }
        }

        @DisplayName("모든 파라미터가 주어지면")
        @Nested
        class ContextWithAllParameters extends BannerRequest {
            @DisplayName("200을 리턴한다")
            @Test
            void itReturns200() throws Exception {
                mockMvc.perform(multipart(BASE_URL)
                        .file(getFile())
                        .header("JWT", "abc")
                        .params(getObject(1, 2)))
                        .andExpect(MockMvcResultMatchers.status().isOk())
                        .andDo(print());
            }
        }
    }

    @DisplayName("배너 수정 테스트")
    @Nested
    class DescribeBannerModify {
        @DisplayName("헤더 인증정보가 없다면")
        @Nested
        class ContextWithoutAuth extends BannerRequest {
            @DisplayName("401을 리턴한다")
            @Test
            void itReturns401() throws Exception {
                mockMvc.perform(multipartPutBuilder(BASE_URL)
                        .file(getFile())
                        .params(getObject(2, 3)))
                        .andExpect(MockMvcResultMatchers.status().isUnauthorized())
                        .andDo(print());
            }
        }

        @DisplayName("모든 파라미터가 주어지면")
        @Nested
        class ContextWithAllParameters extends BannerRequest {
            @DisplayName("200을 리턴한다")
            @Test
            void itReturns200() throws Exception {
                MultiValueMap<String, String> params = getObject(3, 4);
                params.add("banner_key", "1");
                mockMvc.perform(multipartPutBuilder(BASE_URL)
                        .file(getFile())
                        .header("JWT", "abc")
                        .params(params))
                        .andExpect(MockMvcResultMatchers.status().isOk())
                        .andDo(print());
            }
        }
    }
}

```

#### service 테스트

```java
import com.limhm.MainTests;

import com.limhm.dto.BannerRegistModifyDto;
import com.limhm.service.BannerService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;

import java.time.LocalDateTime;
import java.time.ZoneId;


import static org.junit.Assert.assertThat;

public class BannerServiceTest extends AdminTests {
    @Autowired
    BannerService service;

    abstract class BannerDto {
        private LocalDateTime getDateTime(int days) {
            LocalDateTime now = LocalDateTime.now(ZoneId.of("Asia/Seoul"));
            return now.plusDays(days);
        }

        BannerRegistModifyDto getObject(int start, int end) {
            return new BannerRegistModifyDto(
                    "event",
                    "hello",
                    getDateTime(1),
                    getDateTime(2)
            );
        }
    }

    @DisplayName("배너 등록")
    @Nested
    class DescribeBannerInsert {
        @DisplayName("모든 파라미터가 주어지면")
        @Nested
        class ContextWithAllParameter extends BannerDto {
            @DisplayName("배너를 저장한다")
            @Test
            void itSavesBanner() throws Exception {
                int result = service.insertBanner(getObject(1, 2));
                assertThat(result).isEqualTo(1);
            }
        }
    }
}

```
