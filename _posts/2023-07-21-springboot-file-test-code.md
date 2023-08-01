---
layout: post
title: 파일 관련 테스트 코드 작성
categories: experience
tags: [web, springboot, project]
---

자바&스프링부트 프로젝트에서 CSV 파일을 파싱하고, 테스트하는 코드를 작성하는 법을 기록으로 남겨둔다.

### CSV 파일 파싱

```java
import org.springframework.web.multipart.MultipartFile;

import java.io.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class FileParser {

    private ArrayList<Map<String, String>> processingData(BufferedReader bufferedReader) throws IOException {
        ArrayList<Map<String, String>> data = new ArrayList<>();

        String headerLine = bufferedReader.readLine();
        String[] headers = headerLine.split(",");

        String line;
        while ((line = bufferedReader.readLine()) != null) {
            String[] rows = line.split(",");
            if (rows.length == headers.length) {
                Map<String, String> map = new HashMap<>();
                for (int i = 0; i < rows.length; i++) {
                    String key = headers[i].trim();
                    String value = rows[i].trim();

                    map.put(key, value);
                }
                data.add(map);
            }
        }
        return data;
    }

    // http 요청으로 오는 스트림 파일 처리 (오버로딩)
    public ArrayList<Map<String, String>> convertCsvFile(MultipartFile file) {
        try (BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(file.getInputStream()))) {
            // InputStreamReader: 보조 스트림&문자 스트림 (바이트 단위로 읽은 자료를 문자로 변환해주는 보조스트림)
            // BufferedReader: 네트워크 읽거나 쓸 때 사용하는 스트림 (이유: 바이트 자료만 입력되는 스트림이기 때문)
            return processingData(bufferedReader);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    // 파일 위치 정보가 주어질 경우 처리 (오버로딩)
    public ArrayList<Map<String, String>> convertCsvFile(String file) {
        try (BufferedReader bufferedReader = new BufferedReader(new FileReader(file))) {
            // BufferedReader: 보조 스트림 (스트림 버퍼를 제공해 빠르게 처리 가능)
            // FileReader: 기반 스트림&문자 스트림 (한글은 바이트스트림 대신 문자스트림 사용해야)
            return processingData(bufferedReader);
        } catch (FileNotFoundException e) {
            throw  new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}

```

### 파일 PATH 테스트

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.web.servlet.MockMvc;

@ActiveProfiles("test")
@SpringBootTest
@AutoConfigureMockMvc
public class MainTests {
    @Autowired
    protected MockMvc mockMvc;

    @Autowired
    protected ObjectMapper mapper;
}

```

```java
import com.limhm.AdminTests;
import com.limhm.common.FileParser;
import org.junit.Test;
import org.junit.jupiter.api.DisplayName;

import java.util.ArrayList;
import java.util.Map;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

public class FileParserTest extends AdminTests {
    @DisplayName("csv 파일 파싱 테스트")
    @Test
    public void success() {
        FileParser fileParser = new FileParser();
        ArrayList<Map<String, String>> result = fileParser.convertCsvFile("./src/test/resources/test.csv");

        Map<String, String> map1 = result.get(0);
        assertThat(map1.get("영어")).isEqualTo("abc");
        assertThat(map1.get("한글")).isEqualTo("가나다");
        assertThat(map1.get("숫자")).isEqualTo("010");

        Map<String, String> map2 = result.get(1);
        assertThat(map2.get("영어")).isEqualTo("xyz");
        assertThat(map2.get("한글")).isEqualTo("마바사");
        assertThat(map2.get("숫자")).isEqualTo("234");
    }
}

```

### HTTP 요청 Controller 테스트 

```java
import com.limhm.AdminTests;

import org.junit.Test;
import org.junit.jupiter.api.DisplayName;
import org.springframework.http.MediaType;
import org.springframework.mock.web.MockMultipartFile;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.util.MultiValueMap;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.multipart;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;

public class FileControllerTest extends AdminTests {
    @DisplayName("파일 테스트")
    @Test
    public void success() throws Exception {
        String PATH = "/registrationFile";
        MockMultipartFile file = new MockMultipartFile(
                "parameter_name",
                "sample.csv",
                MediaType.TEXT_PLAIN_VALUE,
                ("name,item\n" + "kim,gift1\n" + "lee,gift2\n").getBytes()
        );

        MultiValueMap<String, String> dto = new LinkedMultiValueMap<>();
        dto.add("key", "1000001");

        mockMvc.perform(multipart(PATH)
                        .file(file)
                        .header("JWT", "ey")
                        .params(dto))
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andDo(print());
    }
}
```
