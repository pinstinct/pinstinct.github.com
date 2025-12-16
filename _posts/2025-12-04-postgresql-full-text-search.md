---
layout: post
title: 검색품질 고도화
categories: experience
tags: [posgresql, fts, search]
excerpt: like 검색으로 검색 정확도 문제가 발생해 고도화 방법을 검토한 내용을 정리합니다.
---

# 문제 상황

회사에서 검색 품질을 개선해야 하는 상황이 생겼다. 평소라면 Elasticsearch로 넘어가는 방법을 생각했겠지만, 현재 인프라를 마음대로 바꿀 수 없고 비용도 최대한 아껴야 한다. 그래서 RDB에서 할 수 있는 고도화 방법을 찾아보기로 했다.

문제는 다음과 같다. 
- 검색 결과에 대한 가중치 조절이 힘들다. SQL `CASE WEHN`으로 가중치 정렬을 흉내낼 수 있지만, 단순 패턴 매칭 및 수동 순서 지정이므로 검색 정확도가 낮다.

따라서 기존 인프라와 비용 제약 내에서 성능과 정확도를 동시에 개선할 수 있는 방법을 찾고자 PostgreSQL의 FTS(Full-Text Search) 기능을 검토해봤다.

> [PostgreSQL의 고급 검색 기능을 사용한 구직 검색 엔진 구축](https://aws.amazon.com/ko/blogs/tech/building-a-job-search-engine-with-postgresqls-advanced-search-features/)


## 검색이란?

### [전체 텍스트 검색(Full-Text Search)](https://cloud.google.com/discover/what-is-full-text-search?hl=ko)

키워드 검색을 넘어 문서 콘텐츠를 분석하여 사용자의 검색어에 따라 관련성 있는 결과를 식별하는 기술이다. 

#### 색인 생성 (`tsvector`)

- 토큰화(tokenization): 텍스트를 개별 단어 또는 토큰이라는 단위로 분해한다. 토큰의 단위와 분해 규칙은 언어와 파서 설정에 따라 달라진다.
- 정규화: 'running'을 'run'과 같이 어근 형태로 추출한다. 이렇게 하면 검색 시 동일한 단어의 변형이 하나의 용어로 취급한다.
- 불용어(stop words) 삭제: 검색에서 의미 없는 'the', 'a', 'is'와 같은 단어 삭제한다. 색인 크기를 줄이고 검색 속도를 높일 수 있다.
- 색인 구축: 키워드를 문서 내 위치로 매핑하는 데이터 구조를 생성한다. 검색 시 문서를 빠르게 찾을 수 있다.

#### 검색 (`tsquery`)

색인을 이용해 관련 키워드가 포함된 문서를 식별한다.

#### 검색 방법

FTS에는 다양한 접근 방식이 있다. 

- 기본 검색: 키워드의 순서나 근접도에 관계없이 문서 내 키워드 일치
- [퍼지 검색](https://cloud.google.com/discover/what-is-fuzzy-search?hl=ko): 철차, 오타와 같은 변형을 허용하는 보다 유용한 방법
  - 주로 n-gram 유사도 검색을 사용한다. `LIKE` 보다 인덱스 활용 측면에서 효율적이다.
  - PostgreSQL에서는 `pg_trgm`, `pg_bigm`을 확장 모듈로 제공(별도 설치 필요)한다.
    - 확장모듈 설치법: `create extension if not exists pg_trgm;`
    - 설치된 확장 모듈 확인: `select * from pg_extension;`
  
    항목 | `pg_trgm` | `pg_bigm`
    -- | -- | --
    인덱스 단위 | 3글자 | 2글자
    PostgreSQL 내장 여부 | 내장 | 외부 프로젝트
    한글 검색 | 보통 | 우수

- 근접 검색


### [의미론적 검색(Semantic search)](https://www.elastic.co/kr/what-is/semantic-search) (`pgvector`)

검색자의 의도와 검색 컨텍스트에서 단어를 이해하는 것을 포함하는 일련의 검섹 엔진 기능이다. 시맨틱 검색은 머신 러닝 및 인공 지능과 같은 기술의 도움을 받아 검색 의도를 의미론적인 의미와 일치시킴으로써 이를 달성한다.

**벡터 검색**을 통해 제공되며, 벡터 검색은 시맨틱 검색이 컨텍스트 관련성과 의도 관련성을 기반으로 콘텐츠를 제공하고 순위를 매길 수 있도록 한다.

- 임베딩: 텍스트, 이미지, 오디오 등 복잡한 데이터를 컴퓨터가 이해할 수 있는 숫자의 배열(벡터)로 변환하는 과정 또는 결과물 

### FTS vs 의미론적 검색 비교

FTS는 키워드 일치에 중점을 두는 반면, 시맨틱 검색은 검색어의 의미와 사용자 의도를 이해하는 데 중점을 둔다.

특징 | FTS 검색 (역 인덱스)	| 시멘틱 검색 (벡터 인덱스)
-- | -- | --
색인 구조 |	역 인덱스 (단어 -> 문서 ID) |	벡터 인덱스 (임베딩 벡터 -> 문서 ID)
검색 원리 |	키워드 및 구문 일치 |	의미 및 문맥 이해, 의미적 유사성
알고리즘	| BM25 등 통계 기반 알고리즘 | 신경망 모델 기반 임베딩, 벡터 유사도 검색
장점	| 정확한 키워드 검색, 빠른 조회 속도	| 사용자 의도 파악, 관련 콘텐츠 발견 용이

실무에서는 FTS로 후보를 좁히고(필터링), 벡터로 재정렬(재랭킹)하는 하이브리드 검색이 흔히 사용된다.

## PostgreSQL FTS

> [Chapter 12. Full Text Search](https://www.postgresql.org/docs/current/textsearch.html)

### 서론

#### `LIKE`, `ILIKE` 연산자들의 문제점

- 파생어 처리가 어렵다. (satisfy, satisfies, ...)
- 검색 결과에 대한 우선 순위(ranking)를 제공하지 않으므로 일치하는 문서가 수천 개 나온다.
- 인덱스 지원이 없어 속도가 느리다.

#### FTS 전처리

- 문서를 토큰으로 파싱 (PostgreSQL은 표준 파서를 제공)
- 토큰을 *lexeme(어휘소)*으로 변환
- 검색에 최적화된 전처리 문서를 저장

전처리된 문서를 저장하기 위한 `tsvector` 데이터 타입과 처리된 쿼리를 표현하는 `tsquery` 제공한다.

#### Basic Text Matching

전체 텍스트 검색은 일치 연산자 `@@`를 기반으로 한다. 이 연산자는 `tsvector`(문서)가 `tsquery`(쿼리)와 일치하면 true를 반환한다.


```sql
select '아빠가 방에 들어가신다.'::tsvector @@ '아빠가 & 방에'::tsquery;
-- result: true

select to_tsvector('아빠가 방에 들어가신다.') @@ to_tsquery('아빠가 & 방에');
-- result: true
```

`tsquery`는 검색어를 포함하는데, 검색어는 이미 정규화된 어휘소여야 하며, AND, OR, NOT, FOLLOWED BY 연산자를 사용하여 여러 검색어를 결합할 수 있다.

- `&` (AND 연산자): 인수가 모두 문서에 나타나야 일치 
- `|` (OR 연산자): 인수 중 적어도 하나가 나타나야 일치 
- `!` (NOT 연산자): 인수가 나타나지 않아야 일치 
- `<->` (FOLLOWED BY 연산자): 인수와 일치하는 항목이 인접하고 지정된 순서대로 있는 경우 일치 

  ```sql
  select to_tsvector('fatal error') @@ to_tsquery('fatal <-> error');
  -- result: true

  -- 위와 동일한 쿼리, <->와 <1>은 동일
  select to_tsvector('fatal error') @@ to_tsquery('fatal <1> error'); 
  -- result: true

  select to_tsvector('error is not fatal') @@ to_tsquery('fatal <-> error');
  -- result: false
  ```

  - `phraseto_tsquery` 함수는 일부 단어가 불용어일 때 여러 단어로 구성된 구(phrase)를 일치시킬 수 있는 `tsquery` 생성 

    ```sql
    select phraseto_tsquery('cats ate rats');
    -- result: 'cat' <-> 'ate' <-> 'rat'

    select phraseto_tsquery('the cats ate the rats');
    -- result: 'cat' <-> 'ate' <2> 'rat'
    ```

연산자 우선순위는 괄호가 없으면, `!` > `&`, `<->` > `|` 순으로 적용된다.

### 테이블과 인덱스

#### 테이블 검색

인덱스 없이도 텍스트 검색을 수행할 수 있다. 인덱스 없이도 작동하지만, 속도가 느리다. 임시 검색을 하는 경우를 제외하고, 텍스트 검색을 실제로 사용하려면 인덱스를 생성해야 한다.

```sql
select content, to_tsvector('simple', content)
from question
where to_tsvector('simple', content) @@ to_tsquery('simple', '보험:*')
```

#### 인덱스 생성

`default_text_search_config`: 텍스트 검색 함수 변형에 사용되는 구성을 선택한다. 기본 설정은 `pg_catalog.simple`이다.

```sql
-- tsvector 컬럼 생성 
alter table question add column content_tsv tsvector generated always as (to_tsvector('simple', content)) stored;

-- 인덱스 생성
create index content_tsv_idx on question using gin (content_tsv);

-- FTS 검색
select content, content_tsv
from question
where content_tsv @@ to_tsquery('보험:*');
```

`to_tsvector` 호출을 다시 실행할 필요가 없으므로 검색 속도가 빨라진다.

### 텍스트 검색 제어

#### Parsing Documents

PostgreSQL은 문서를 `tsvector` 데이터 유형으로 변환하기 위한 `to_tsvector` 함수를 제공한다.

```
to_tsvector([config reconfig, ] document text) returns tsvector
```

`to_tsvector`는 텍스트 문서를 토큰으로 파싱하고, 토큰을 어휘소로 변환 후 어휘소와 문서 내 위치를 나열하는 `tsvector`를 반환한다.

```sql
select to_tsvector('english', 'a fat cat sat on a mat - it ate a fat rats');
-- result: 'ate':9 'cat':3 'fat':2,11 'mat':7 'rat':12 'sat':4
-- a, on, it, - 는 사라지고, rats는 rat으로 변경

```

`setweight` 함수는 `tsvector` 항목에 주어진 **가중치**를 부여하여 레이블을 지정하는 데 사용한다. 가중치는 A, B, C, D 중 하나이다. 이 함수는 일반적으로 제목과 본문처럼 문서의 여러 부분에서 온 항목을 표시하는데 사용한다. 나중에 이 정보는 검색 결과 순위를 매기는데 사용할 수 있다.

#### Parsing Queries

PostgreSQL은 쿼리를 `tsquery` 데이터 유형으로 변환하기 위한 `to_tsquery`, `plainto_tsquery`, `phraseto_tsquery` 및 `websearch_to_tsquery` 함수를 제공한다.

```sql
to_tsquery([config reconfig, ] querytext text) returns tsquery
```

`tsquery`는 토큰을 액면 그대로 받아들이는 반면, `to_tsquery`는 구성을 사용하여 토큰화, 정규화, 불용어 삭제를 한다.

```sql
plainto_tsquery([config regconfig, ] querytext text) return tsquery

select plainto_tsquery('english', 'The Fat Rats');
-- result: 'fat' & 'rat'
```

`plainto_tsquery`: 서식이 지정되지 않은 텍스트를 tsquery 값으로 변환한다. `to_tsquery`와 마찬가지로 토큰화, 정규화, 불용어 삭제 후 남은 단어 사이에 `&` 연산자가 삽입된다.

```sql
phraseto_tsquery([config reconfig, ] querytext text) return tsquery
```

`phraseto_tsquery`는 `plainto_tsquery`와 유사하게 동작하지만, `&` 연산자 대신 남아 있는 단어 사이에 `<->` 연산자를 삽입한다.

```sql
websearch_to_tsquery([config reconfig, ] querytext text) return tsquery
```

`websearch_to_tsquery`는 다음 구문이 지원된다.

- unquoated text: 따옴표로 묶이지 않은 텍스트는 `plainto_tsquery` 처럼 `&` 연산자로 구분된 용어로 변환
- "quoted text": 따옴표 안의 텍스트는 `phraseto_tsquery` 처럼 `<->` 연산자로 구분된 용어로 변환
- or: `|` 연산자로 변환
- -: 대시(-)는 `!` 연산자로 변환

```sql
select websearch_to_tsquery('english', 'The fat rats');
-- result: 'fat' & 'rat'

select websearch_to_tsquery('english', '"supernovae stars" -crab');
-- result: 'supernova' <-> 'star' & !'crab'

select websearch_to_tsquery('english', '"sad cat" or "fat rat"');
-- result: 'sad' <-> 'cat' | 'fat' <-> 'rat'

select websearch_to_tsquery('english', 'signal - "segmentation fault"');
-- result: 'signal' & !( 'segment' <-> 'fault' )
```

#### Ranking Search Results

랭킹은 검색 쿼리와 문서의 관련성을 측정하여 일치하는 항목이 많을 때 가장 관련성이 높은 문서를 먼저 표시한다. PostgreSQL은 어휘, 근접성 및 구조적 정보를 고려하는 두 가지 랭킹 함수를 제공한다.

```sql
-- 일치하는 어휘소의 빈도를 기준으로 벡터의 순위를 매긴다.
ts_rank([weights float4[], ] vector tsvector, query tsquery [, normalization integer]) returns float4

-- ts_rank 순위와 유사하지만, 일치하는 어휘소 간의 근접성이 고려된다.
ts_rank_cd([weights float4p[], ] vector tsvector, query tsquery [, normalization integer]) returns float4
```

`weights` 인수는 가중치를 더하거나 덜 적용할 수 있는 기능을 제공한다. 일반적으로 가중치는 제목이나 초기 초록과 같은 문서의 특정 영역에 있는 단어를 표시하는 데 사용되므로 본문에 있는 단어보다 더 중요하거나 덜 중요하게 처리할 수 있다.

```
{D-weight, C-weight, B-weight, A-weight}
{0.1, 0.2, 0.4, 1.0}
```

문서가 길수록 검색 쿼리 용어가 포함될 가능성이 더 높으므로 문서 크기를 고려하는 것이 합리적이다. 랭킹 함수는 문서 길이가 순위에 영향을 미치는지 여부와 그 영향을 지정하는 정수 `normalization` 옵션이 있다. 정수 옵션은 여러 동작을 제어하므로, bit mask입니다. `|` 사용하여 하나 이상의 동작을 지정할 수 있다. (예: `2|4`) 두 개 이상의 플래그 비트가 지정된 경우 나열된 순서대로 적용된다.

- 0 (default): 문서 길이를 무시
- 1: 문서 길이의 로그 + 1로 나눔
- 2: 순위를 문서 길이로 나눔
- 4: 범위 간 평균 거리로 나눔(`ts_rank_cd`에서만 구현)
- 8: 문서의 고유 단어 수로 순위를 나눔 
- 16: 문서의 고유 단 수의 로그 + 1로 나눔
- 32: 그 자체로 나눈 값 + 1

랭킹은 각 매칭 문서의 `tsvector`를 참조해야 하므로 비용이 많이 들며, I/O에 의존하므로 결과적으로 속도가 느려질 수 있다. 

#### Highlighting Results

검색 결과를 표시하려면 각 문서의 일부와 해당 문서가 검색 쿼리와 어떻게 연관있는지 보여주는 것이 좋다. PostgreSQL은 이 기능을 `ts_headline` 함수로 제공한다.

```sql
ts_headline([config reconfig, ] document text, query tsquery [, options text]) returns text
```

`ts_headline` 쿼리와 함께 문서를 입력받아 쿼리의 용어가 강조 표시된 문서의 발췌문을 반환한다. 문서 구문 분석에 사용할 구성은 `config`로 지정할 수 있다. 생략하면, `default_text_search_config` 구성이 사용된다.

- `MaxWords`, `MinWords` (Integer): 출력할 가장 긴 헤드라인과 가장 짧은 헤드라인을 결정 (기본값 35, 15)
- `StartSel`, `StopSel` (String): 문서에 나타나는 쿼리 단어를 구분하는 문자열로, 다른 발췌 단어와 구분하기 위해 사용한다. (기본값 `<b>`, `</b>`)
- ...

```sql
select ts_headline(
	'simple', '아빠가 방에 들어가신다.',
	to_tsquery('simple', '방:*'),
	'StartSel=<mark>, StopSel=</mark>'
);
-- result: 아빠가 <mark>방에</mark> 들어가신다.
```

`ts_headline`은 `tsvector` 원본 문서를 사용하므로 느릴 수 있으므로 주의해서 사용해야 한다.



## [PostgreSQL Full-Text Search: A Powerful Alternative to Elasticsearch for Small to Medium Applications](https://iniakunhuda.medium.com/postgresql-full-text-search-a-powerful-alternative-to-elasticsearch-for-small-to-medium-d9524e001fe0) 번역

여러 필드(제목, 콘텐츠, 저자, 약력, 태그)에 걸쳐 수백만 개의 기사를 검색하고, 맞춤법 오류를 매끄럽게 처리하고, 관련성 순으로 결과를 정렬해야 했다. `LIKE` 연산자가 너무 부족하게 느껴졌다.

첫 번째 본능은 Elasticsearch를 사용하는 것이다. 하지만 구현 과정을 살펴보면 놀라운 사실을 발견했다. PostgreSQL에 정교한 전체 텍스트 검색 기능이 있었다. PostgreSQL은 완전한 텍스트 검색 엔진을 제공한다.

패턴 매칭이 효과적으로 처리할 수 없는 검색 요구사항은 다음과 같다.

- 다른 가중치를 갖는 다양한 필드 검색
- 단어 변형 처리 (예: run을 검색하면 running, ran, runs가 검색되어야 함)
- 철차 오류 처리
- 관련성 기반 순위 구현
- 성능 저하 없이 수백만 개의 레코드로 확장 가능

인덱싱을 고려하면 단순 패턴 매칭의 한계가 더욱 분명해진다. 'word%'와 같은 패턴을 가진 `LIKE` 쿼리에 대해서는 B트리 인덱스를 생성할 수 있지만, '%word%' 패턴에는 전체 테이블 스캔이 발생한다.

```sql
SELECT title, ts_rank(search_vector, query) as rank
FROM articles, to_tsquery('english', 'keyboard') query
WHERE search_vector @@ query
ORDER BY rank DESC;
```

위의 쿼리는 다음을 수행할 수 있다.

- 빠른 검색을 위한 GIN 인덱스 사용 가능
- 단어 어휘소 자동으로 처리 
- 상자 밖에서 관련 순위 제공
- 데이터 중가에 따른 효과적인 확장

그렇다면 "왜 그냥 Elasticsearch를 사용하면 안 되는 걸까?" 

- 유지 관리를 위한 추가 인프라
- 기본 데이터베이스와 검색 인덱스 간의 복잡한 동기화
- 추가 운영 비용
- 더 복잡한 배포 및 모니터링
- 추가 전문 지식

Elasticsearch는 훌륭한 도구이지만, 중소 규모 애플리케이션에 종종 과한 경우가 많다. 

### Understanding PostgreSQL Full-Text Search Basics

#### 1. Document (문서)

PostgreSQL FTS에서 Document(문서)는 검색하려는 콘텐츠 단위이다. 물리적 문서와 달리 PostgreSQL 문서는 다음 요소로 구성될 수 있다.

- 단일 텍스트 column
- 결합된 여러 columns
- 관련 테이블들의 데이터
- 러타임에 생성된 동적 콘텐츠

#### 2. tsvector

사전 처리된 검색 가능한 콘텐츠

```sql
select to_tsvector('english', 'The qucick brown foxes are jumping over lazy dogs');
-- result: 'brown':3 'dog':9 'fox':4 'jump':6 'lazi':8 'qucick':2

select to_tsvector('english', 'Running and running and running throught the forest');
-- result: 'forest':8 'run':1,3,5 'throught':6

select setweight(to_tsvector('english', title), 'A') ||
	setweight(to_tsvector('english', content), 'B') as weighted_document
from (
	select 'PostgreSQL search Guide' as title,
		'Learn how to search in PostgreSQL effectively' as content
);
-- result: 'effect':10B 'guid':3A 'learn':4B 'postgresql':1A,9B 'search':2A,7B
```

#### 3. tsquery

검색을 수행하는 데 사용되는 쿼리 타입

```sql
with sample_queries as (
	select 
		to_tsquery('english', 'postgresql & database') as q1,
		to_tsquery('english', 'posgtresql <-> database') as q2,
		to_tsquery('english', 'fast & !slow & database') as q3
)
select 
	'PosgreSQL is a database' as text,
	to_tsvector('english', 'PostgreSQL is a database') @@ q1 as matches_and,
	to_tsvector('english', 'PostgreSQL is a database') @@ q2 as matches_phrase,
	to_tsvector('english', 'PostgreSQL is a fast database') @@ q3 as matches_not
from sample_queries;
```

#### 4. lexemes (어휘소)

PostgreSQL의 텍스트 검색은 다음과 같은 방법으로 색인을 생성한다.

```sql
with example as (
	select unnest(array[
		'running',
		'runs',
		'ran',
		'PostgreSQL''s',
		'databases',
		'faster!'
	]) as word
)
select
	word as original_word,
	to_tsvector('english', word) as lexeme
from example;
-- result:
-- original_word | lexeme
-- running      | 'run':1
-- Runs         | 'run':1
-- ran          | 'ran':1
-- PostgreSQL's | 'postgresql':1
-- databases    | 'database':1
-- faster!      | 'faster':1
```


## 적용 예시

```sql
select 
	question.content , answer.content, 
	to_tsvector(question.content) as question_v,
	to_tsvector(answer.content) as answer_v,
	ts_rank(
		setweight(to_tsvector(question.content), 'A') ||
		setweight(to_tsvector(answer.content), 'B'),
		q
	) as rank,
	question.id,
	ts_headline('simple', (question.content || answer.content), q, 'StartSel=<mark>, StopSel=</mark>, HighlightAll=true') as highlight
from to_tsquery('암보험:*') as q, exp_question as question
left join exp_answer as answer on question.id = answer.id
where (to_tsvector(question.content) || to_tsvector(answer.content)) @@ q
order by rank desc
;
```
