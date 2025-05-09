---
layout: post
title: 하나의 API로 테이블에 상관없이 데이터 넣기 
categories: experience
tags: [java, project]
excerpt: json 데이터를 받아 db에 저장하는 단일 API 설계한 내용입니다.
---

### 문제 상황

서비스 초기에 CMS 혹은 Admin 사이트 없이 시작하는 상황이다. Json 벌크 데이터를 각 테이블에 맞게 넣을 수 있는 API를 개발한 코드를 정리한다. 

```json
// 요청 데이터 형태
// content
{
  "content": [
    {
      "id": 1,
      "description": "test"
    }, 
    {}
    // ...
  ]
}

// user
{
  "user": [
    {
      "id": 1,
      "description": "test",
      "auth": "admin"
    }, 
    {}
    // ...
  ]
}
```

### 해결 방법 


구현체와 dto만 추가하면 자동으로 API에서 인식가능하다.


```java
// controlelr
@Slf4j
@RestController
@RequiredArgsConstructor
@RequestMapping("/main")
public class MainController {

  private final MainCommandService mainCommandService;

  @PostMapping
  public void initialData(@RequestBody String body) {
    mainCommandService.loadJson(body);
  }

}

// service
@Slf4j
@Service
@RequiredArgsConstructor
@Transactional
public class MainCommandService {

  private final ObjectMapper objectMapper;
  private final List<GenericInitialDataLoader<?, ?>> dataLoaders;

  private <D> List<D> toDto(List<Map<String, Object>> json, Class<D> dto) {
    return json.stream().map(data -> objectMapper.convertValue(data, dto)).toList();
  }

  @SuppressWarnings("unchecked")
  public void processInitialData(Map<String, Object> json) {
    dataLoaders.forEach(loader -> {
      String key = loader.getSupportedKey();
      if (json.containsKey(key)) {
        List<?> dto = toDto((List<Map<String, Object>>) json.get(key), loader.getDataType());
        loader.loadData(dto);
      }
    });
  }

  public void loadJson(String content) {
    try {
      Map<String, Object> json = objectMapper.readValue(content,
          new TypeReference<Map<String, Object>>() {
          });
    } catch (IOException e) {
      log.debug(e.getMessage());
    }
  }
}

// 인터페이스
public interface GenericInitialDataLoader<D, E> {

  void loadData(List<?> data);

  Class<D> getDataType();

  Class<E> getEntityType();

  String getSupportedKey();

}

// 추상 클래스
@RequiredArgsConstructor
public abstract class AbstractInitialDataLoader<D, E> implements GenericInitialDataLoader<D, E> {

  protected final JpaRepository<E, Integer> repository;

  @Transactional
  public void createInitialData(List<D> dto) {
    List<E> entities = filterEntities(dto);
    if (!entities.isEmpty()) {
      repository.saveAll(entities);
    }
  }

  protected abstract List<E> filterEntities(List<D> dtoList);

}

// 구현체 n
@Component
public class ContentInitialDataLoader extends
    AbstractInitialDataLoader<ContentCreateRequestDto, Content> {

  private final ContentRepository contentRepository;

  public ContentInitialDataLoader(ContentRepository contentRepository) {
    super(contentRepository);
    this.contentRepository = contentRepository;
  }

  @Override
  protected List<Content> filterEntities(List<ContentCreateRequestDto> dtoList) {
    return dtoList.stream().map(dto -> contentRepository.findById(dto.id()).map(entity -> {
      entity.updateEntityForInitializeData(dto.description());
      return entity;
    }).orElseGet(() -> Content.builder().description(dto.description()).build())).toList();
  }

  @SuppressWarnings("unchecked")
  @Override
  public void loadData(List<?> data) {
    createInitialData((List<ContentCreateRequestDto>) data);
  }

  @Override
  public Class<ContentCreateRequestDto> getDataType() {
    return ContentCreateRequestDto.class;
  }

  @Override
  public Class<Content> getEntityType() {
    return Content.class;
  }

  @Override
  public String getSupportedKey() {
    return "content";
  }
}

// dto
@Builder
public record ContentCreateRequestDto(Integer id, String description) {

}

// entity
@Getter
@Builder
@Entity
@ToString
@Table(name = "content")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PROTECTED)
public class Content {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Integer id;

  @Column
  private String description;

  public void updateEntityForInitializeData(String description) {
    this.description = description;
  }
}

// repository
@Repository
public interface ContentRepository extends JpaRepository<Content, Integer> {

}

```

