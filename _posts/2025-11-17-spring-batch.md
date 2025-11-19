---
layout: post
title: Spring Batch & Test Code
categories: java
tags: [java, spring, batch]
excerpt: 스프링 배치 기본 개념과 테스트 코드를 정리한 글이다.
---

# Spring Batch

## 스프링 배치란?

스프링 배치는 **대량의 반복적인 데이터 처리**를 위한 프레임워크로 안정적이고 구조화된 배치 처리 기능을 제공한다.

Quartz는 스케줄링, Spring Batch는 데이터 배치 처리를 담당한다. Quartz는 스프링 배치에 없는 다양한 스케줄링 기능을 지원하므로, Quartz + Batch 조합으로 사용한다.

> [1. Spring Batch 가이드 - 배치 어플리케이션이란?](https://jojoldu.tistory.com/324)

## 스프링 배치 구조

### Job > Step > Tasklet 또는 Chunk 기반 처리

- Job: 배치 작업의 최상위 단위
- Step: Job을 구성하는 하나의 실행 단위
    - `next()`로 Step을 순차적으로 연결 가능
    - `on()`, `to()`, `from()`, `end()`를 활용해 조건 분기 가능(예: step A 실행 후 성공이면 step B 실패면 step C로 수행)
- Tasklet 기반 Step: 단일 작업을 수행
- Chunk 기반 Step: `ItemReader - ItemProcessor - ItemWriter` 흐름으로 대량 데이터를 처리

### Chunk 기반 구성 요소

Chunk: 한 번에 하나씩 데이터를 읽어 Chunk라는 덩어리를 만든 뒤, Chunk 단위로 트랜잭션을 다룬다.

- ItemReader: 데이터를 하나씩 읽는다.
- ItemProcessor(선택): Reader에서 읽은 데이터를 가공한다.
- ItemWriter: 데이터를 Chunk 단위로 일괄 저장한다.

Reader와 Processor에서는 1건씩 다루고, Writer에선 Chunk 단위로 처리한다.


## 메타 데이터 테이블 구조 

스프링 배치는 총 6개의 메타데이터 테이블을 사용하며, 직접 생성해야 한다.

- BATCH_JOB_INSTANCE: `Job Parameter`(외부에서 받을 수 있는 파라미터)에 따라 생성, 같은 Batch Job이라도 `Job Parameter`가 다르면 생성
- BATCH_JOB_EXECUTION: BATCH_JOB_INSTANCE 테블과 부모-자식 관계이다. 부모 BATCH_JOB_INSTANCE가 성공/실패한 모든 내역 저장 
- BATCH_JOB_EXECUTION_PARAMS: BATCH_JOB_EXECUTION 생성될 때, 입력받은 `Job Parameter` 저장 
- BATCH_STEP_EXECUTION
- BATCH_STEP_EXECUTION_CONTEXT
- BATCH_JOB_EXECUTION_CONTEXT

이 테이블들의 스키마는 spring-batch-core 라이브러리 내부에 포함되어 있으며, IDE에서 `schema-`로 검색하여 DBMS 별 스키마 파일을 복사해 사용한다.

## 예시 1: Querydsl 기반 Paging Reader를 사용하는 Job

아래 Job은 데이터를 **페이징 방식(ItemReader)**으로 읽어, 삭제하는 배치 Job이다.

```java
@Slf4j
@Configuration
@RequiredArgsConstructor
public class DeletePhotoViewHistoryJobConfig {

  private final JobListener jobListener;
  private final EntityManagerFactory entityManagerFactory;
  private final JPAQueryFactory jpaQueryFactory;

  @Value("${batch.chunk.size}")
  private int chunkSize;

  @Bean
  public Job deletePhotoViewHistoryJob(JobRepository jobRepository,
      PlatformTransactionManager platformTransactionManager) {
    return new JobBuilder(JobNames.DELETE_PHOTO_VIEW_HISTORY, jobRepository).listener(jobListener)
        .start(deletePhotoViewHistoryStep(jobRepository, platformTransactionManager)).build();
  }

  @Bean
  @JobScope
  public Step deletePhotoViewHistoryStep(JobRepository jobRepository,
      PlatformTransactionManager platformTransactionManager) {
    return new StepBuilder(StepNames.DELETE_PHOTO_VIEW_HISTORY, jobRepository)
        // <T, T> 첫번째 T는 Reader 에 반환될 타입, 두번째 T는 Writer 에 파라미터로 넘어올 타입
        .<PhotoViewHistory, PhotoViewHistory>chunk(chunkSize, platformTransactionManager)
        .reader(deletePhotoViewHistoryReader(null))
        .writer(deletePhotoViewHistoryWriter()).build();
  }

  @Bean
  @StepScope
  public QuerydslZeroPagingItemReader<PhotoViewHistory> deletePhotoViewHistoryReader(
      @Value("#{jobParameters[current]}") LocalDateTime current) {
    log.info("current :: {}", current);
    return new QuerydslZeroPagingItemReader<>(entityManagerFactory, chunkSize,
        jpaQueryFactory -> jpaQueryFactory.select(photoViewHistory).from(photoViewHistory)
            .where(photoViewHistory.createdDatetime.before(current.minusDays(1)))
            .orderBy(photoViewHistory.historyKey.asc()));
  }

  @Bean
  @StepScope
  public DeletePhotoViewHistoryJobWriter deletePhotoViewHistoryWriter() {
    return new DeletePhotoViewHistoryJobWriter(jpaQueryFactory);
  }
}

```

### Cursor vs Paging Reader

ItemReader는 Cursor, Paging 방식이 있다.

- Cursor based Reader: 데이터베이스와 커넥션을 맺은 후, Cursor 를 한칸씩 옮기면서 지속적으로 데이터를 뽑아온다.
    - 장점: 스트리밍 방식으로 메모리 사용 효율적
    - 단점: 하나의 DB Connection을 장시간 사용하기 때문에 배치가 끝나기 전에 커넥션이 끊어질 수 있다. (장기 배치 작업에 취약)
- Paging based Reader
    - 장점: 페이지마다 새로운 쿼리 실행 (커넥션 점유 문제 없음)
    - 단점: 각 Page마다 조회 성능 고려 필요 (정렬 필수)

대부분의 실제 운영환경에서는 Paging 방식을 권장한다.

## 예시 2: 외부 서비스(AWS Cost Explorer) 데이터를 ListItemReader로 처리하는 Job

```java
@Slf4j
@Configuration
@RequiredArgsConstructor
public class InsertCostMetricsByDailyJobConfig {

  private final JobListener jobListener;
  private final CostExplorerService costExplorerService;
  private final CostMetricRepository costMetricRepository;

  @Value("${batch.chunk.size}")
  private int chunkSize;

  @Bean
  public Job insertCostMetricsByDailyJob(JobRepository jobRepository,
      PlatformTransactionManager platformTransactionManager) {
    return new JobBuilder(JobNames.INSERT_COST_METRICS_BY_DAILY, jobRepository).listener(
            jobListener).start(insertCostMetricsByDailyStep(jobRepository, platformTransactionManager))
        .build();
  }

  @Bean
  @JobScope
  public Step insertCostMetricsByDailyStep(JobRepository jobRepository,
      PlatformTransactionManager platformTransactionManager) {
    return new StepBuilder(StepNames.INSERT_COST_METRICS_BY_DAILY,jobRepository)
            .<CostMetric, CostMetric>chunk(chunkSize, platformTransactionManager)
            .reader(insertCostMetricsByDailyReader(null))
            .writer(insertCostMetricsByDailyWriter())
            .build();
  }

  @Bean
  @StepScope
  public ListItemReader<CostMetric> insertCostMetricsByDailyReader(
          @Value("#{jobParameters[date]}") LocalDate date) {  // date=20250101 형태로 외부에서 주입
    List<CostMetric> metrics = costExplorerService.getCostByDaily(date);  // AWS SDK를 통해 비용 데이터를 조회
    return new ListItemReader<>(metrics);
  }

  @Bean
  @StepScope
  public InsertCostMetricsByDailyJobWriter insertCostMetricsByDailyWriter() {
    return new InsertCostMetricsByDailyJobWriter(costMetricRepository);
  }
}

```

## ItemWriter 구현 예시

Reader/Processor에서 전달된 데이터를 Chunk 단위로 처리한다.

```java
@Slf4j
@RequiredArgsConstructor
public class InsertCostMetricsByDailyJobWriter implements ItemWriter<CostMetric> {

  private final CostMetricRepository costMetricRepository;

  @Override
  public void write(Chunk<? extends CostMetric> chunk) throws Exception {
    costMetricRepository.saveAll(chunk);
  }
}

```

## 배치 스케줄링 예시

```java
@Slf4j
@Component
@EnableScheduling
@RequiredArgsConstructor
public class BatchScheduler {

  private final JobLauncher jobLauncher;
  private final JobRegistry jobRegistry;

  /**
   * 매일 1시 실행
   */
  @Scheduled(cron = "0 0 1 * * ?")
  public void runInsertCostMetricsByDailyJob() {
    try {
      Job job = jobRegistry.getJob(JobNames.INSERT_COST_METRICS_BY_DAILY);
      jobLauncher.run(job, getJobParameter());
    } catch (NoSuchJobException | JobExecutionAlreadyRunningException | JobRestartException |
             JobInstanceAlreadyCompleteException | JobParametersInvalidException e) {
      log.error(e.toString());
    }
  }

  private JobParameters getJobParameter() {
    Map<String, JobParameter<?>> parameterMap = new HashMap<>();
    parameterMap.put("current",
        new JobParameter<LocalDateTime>(LocalDateTime.now(), LocalDateTime.class));
    parameterMap.put("now", new JobParameter<LocalDate>(LocalDate.now(), LocalDate.class));
    return new JobParameters(parameterMap);
  }
}

```

## 테스트 코드 예시

```java
@SpringBootTest
@SpringBatchTest
@ActiveProfiles("test")
@Disabled
@EnableJpaAuditing
public abstract class BaseIntegrationTest {

  @Autowired
  // 테스트 코드에서 Job을 실행할 수 있도록 지원
  protected JobLauncherTestUtils jobLauncherTestUtils;

}

```


```java
public class DeletePhotoViewHistoryTest extends BaseIntegrationTest {

  @Autowired
  private PhotoViewHistoryRepository photoViewHistoryRepository;

  @Autowired
  private Job deletePhotoViewHistoryJob;

  @BeforeEach
  public void setUp() {
    jobLauncherTestUtils.setJob(deletePhotoViewHistoryJob);

    IntStream.range(0, 1001).forEach(
        i -> photoViewHistoryRepository.save(
            PhotoViewHistory.builder().photoBoardKey(i + 1).build()));
  }

  @AfterEach
  public void tearDown() {
    photoViewHistoryRepository.deleteAll();
  }


  @Test
  public void deletePhotoViewHistoryJobTest() throws Exception {
    LocalDateTime current = LocalDateTime.now();
    JobParameters jobParameters = new JobParametersBuilder().addString("current",
        String.valueOf(current.plusDays(1))).toJobParameters();

    // JobParameters와 함께 Job 실행 
    JobExecution jobExecution = jobLauncherTestUtils.launchJob(jobParameters);
    assertThat(jobExecution.getStatus()).isEqualTo(BatchStatus.COMPLETED);

    List<PhotoViewHistory> result = photoViewHistoryRepository.findAll();
    assertThat(result).hasSize(0);
  }
}

```

> [10. Spring Batch 가이드 - Spring Batch 테스트 코드](https://jojoldu.tistory.com/455)

