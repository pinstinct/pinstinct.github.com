# 엘라스틱 서치(Elastic Search)

> [시작하세요! 엘라스틱서치](http://wikibook.co.kr/elasticsearch/) 정리한 글 입니다.

## 데이터 처리

### 데이터 구조

인덱스(index) > 타입(type) > 도큐먼트(document) 단위로 이루어 진다. 서로 다른 인덱스와 타입들은 다른 매핑 구조로 구성되며 각각 다른 공간에 저장되고 처리된다. `_all` 명령어나 와일드카드 질의 등으로 여러 개의 인덱스를 한꺼번에 묶어 검색하거나 처리할 수 있다.

관계형 DB | 엘라스틱 서치
--|--
데이터베이스(Database) | 인덱스(Index)
테이블(Table) | 타입(Type)
열(Row) | 도큐먼트(Document)
행(Column) | 필드(Field)
스키마(Schema) | 매핑(Mapping)

엘라스틱 서치의 REST API를 이용한 curl 명령은 다음과 같은 형식으로 사용한다.

> `curl -X(메서드) http://host:port/{인덱스}/{타입}/{도큐먼트id} -d '{JSON 데이터}'`

HTTP Method | CRUD | SQL
--|--|--
GET | Read | Select
PUT | Update | Update
POST | Create | Insert
DELETE | Delete | Delete

### 데이터 입력

`PUT` 메서드를 이용해 입력한다.
```bash
$ curl -XPUT 'http://localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
"title":"Elasticsearch Guide",
"author":"Kim",
"date":"2014-05-01",
"pages":250
}'
{"_index":"books","_type":"book","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "date" : "2014-05-01",
    "pages" : 250
  }
}
```

`POST` 메서드를 이용해 도큐먼트 id를 생략하고 입력할 수도 있다. 이 경우 자동으로 임의의 id(*UtHje2cBF4s6Kti3QweG*)가 생성된다.
```bash
$ curl -XPOST 'localhost:9200/books/book' -H 'Content-Type: application/json' -d '
{
  "title":"Elasticsearch Guide",
  "author":"Kim",
  "started":"2014-05-1",
  "pages":250
}'
{"_index":"books","_type":"book","_id":"UtHje2cBF4s6Kti3QweG","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":2}
```

다시 한번 `PUT` 메서드를 이용해 업데이트한다. 데이터가 수정됐으므로 `_version` 필드의 값이 증가한다. 도큐먼트가 업데이트 될 때 `_version` 필드에서 변경된 횟수를 파악할 수 있다. 도큐먼트 버전은 확인할 수 있지만, 기존 도큐먼트는 보관되지 않고 업데이트할 때마다 삭제되고 새로 쓰인다. 한 번 업데이트한 도큐먼트는 이전 버전으로 되돌릴 수 없으므로 중요한 데이터를 다룰 때에는 업데이트에 주의해야 한다.
```bash
$ curl -XPUT 'http://localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
"title":"Elasticsearch Guide",
"author":["Kim", "Lee"],
"date":"2014-05-01",
"pages":300
}'
{"_index":"books","_type":"book","_id":"1","_version":4,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":3,"_primary_term":2}
```

실제로 입력한 JSON 데이터는 도큐먼트의 `_source` 필드에 JSON 형식의 문자열로 저장된다. 메타 정보를 제외한 `_source` 필드의 값만 보려면 도큐먼트 URI 뒤에 `_source`를 추가한다.
```bash
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 4,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : [
      "Kim",
      "Lee"
    ],
    "date" : "2014-05-01",
    "pages" : 300
  }
}

$ curl -XGET 'localhost:9200/books/book/1/_source?pretty'
{
  "title" : "Elasticsearch Guide",
  "author" : [
    "Kim",
    "Lee"
  ],
  "date" : "2014-05-01",
  "pages" : 300
}
```

### 데이터 삭제

삭제는 `DELETE` 메서드를 이용한다. 도큐먼트를 삭제한다.
```bash
$ curl -XDELETE 'localhost:9200/books/book/1' -H 'Content-Type: application/json'
{"_index":"books","_type":"book","_id":"1","_version":5,"result":"deleted","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":4,"_primary_term":2}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "found" : false
}
```

도큐먼트 데이터를 삭제하더라도 도큐먼트의 메타 정보는 여전히 남아있다. 위와 동일한 URI에 데이터를 입력해보면 `_version` 값이 변하는 것을 확인할 수 있다. 도큐먼트 삭제는 도큐먼트가 실제로 삭제되는 것이 아니라 도큐먼트의 `_source`에 입력된 데이터 값이 빈 값으로 업데이트되고 검색되지 않게 상태가 변경되는 것으로 이해하는 것이 편하다.
```bash
$ curl -XPOST 'localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
  "title":"Elasticsearch Guide",
  "author":["Kim","Lee"],
  "started":"2014-05-1",
  "pages":300
}'
{"_index":"books","_type":"book","_id":"1","_version":3,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":7,"_primary_term":2}
```

`DELETE` 메서드를 사용한 삭제 명령은 인덱스 단위로도 사용할 수 있다. 인덱스 단위로 삭제하면 해당 인덱스에 포함된 타입, 도큐먼트가 모두 삭제되며 **URI에 접근이 불가능**하게 된다. 인덱스 삭제 후 도큐먼트 데이터를 조회하면 인덱스를 찾을 수 없다는 메시지가 출력된다.
```bash
$ curl -XDELETE 'localhost:9200/books' -H 'Content-Type: application/json'
{"acknowledged":true}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "error" : {
    "root_cause" : [
      {
        "type" : "index_not_found_exception",
        "reason" : "no such index",
        "resource.type" : "index_expression",
        "resource.id" : "books",
        "index_uuid" : "_na_",
        "index" : "books"
      }
    ],
    "type" : "index_not_found_exception",
    "reason" : "no such index",
    "resource.type" : "index_expression",
    "resource.id" : "books",
    "index_uuid" : "_na_",
    "index" : "books"
  },
  "status" : 404
}
```

### 데이터 업데이트(`_update`) API

도큐먼트 데이터의 업데이트는 `_update` API의 두 개의 매개 변수 `doc`와 `script`를 이용해서 데이터를 제어할 수 있다. 우선 `doc` 매개변수를 사용해서 필드와 필드의 값을 추가해보자.
```bash
# 데이터 생성
$ curl -XPOST 'localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
  "title":"Elasticsearch Guide",
  "author":"Kim",
  "started":"2014-05-1",
  "pages":250
}'
{"_index":"books","_type":"book","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "started" : "2014-05-1",
    "pages" : 250
  }
}

# 데이터 업데이트
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '
{
"doc":{"category":"ICT"}
}'
{"_index":"books","_type":"book","_id":"1","_version":2,"result":"updated","_shards":{"total":2,"successful":1,"fai
led":0},"_seq_no":1,"_primary_term":1}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 2,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "started" : "2014-05-1",
    "pages" : 250,
    "category" : "ICT"
  }
}
```

`_update` API는 도큐먼트의 구조를 변경하는 것이 아니라 실제로는 `GET` 메서드로 저장된 도큐먼트의 값을 가져와서 입력한 명령을 토대로 새로 변경된 도큐먼트 내용을 만들고 다시 그 내용을 기존 도큐먼트에 입력하는 방식으로 동작한다.

이번에는 `doc` 매개 변수를 사용해서 기존 필드에 저장된 값을 변경해보자.
```bash
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '
{
"doc":{"author":"Lee"}
}'
{"_index":"books","_type":"book","_id":"1","_version":3,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":2,"_primary_term":1}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 3,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Lee",
    "started" : "2014-05-1",
    "pages" : 250,
    "category" : "ICT"
  }
}
```

`script` 매개 변수를 사용하면 `_source`에 있는 도큐먼트의 내용에 다양한 연산을 적용할 수 있다. `_source`에 있는 도큐먼트 내용을 변경하려면 `script`의 `ctx._source` 명령을 사용한다.
```bash
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '
{
  "script": {
    "source": "ctx._source.pages+=5"
  }
}'
{"_index":"books","_type":"book","_id":"1","_version":4,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":3,"_primary_term":1}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 4,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Lee",
    "started" : "2014-05-1",
    "pages" : 300,
    "category" : "ICT"
  }
}
```

`script` 매개 변수를 사용해서 문자 필드를 수정해보자. 먼저 추가하려는 값을 특정 변수에 저장하고 연산을 이용해 추가한다.
```bash
# 1. 문자열 추가
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '{
"script":{
    "source":"ctx._source.author+=params.new_author",
    "params":{
        "new_author":"Kim"
        }
    }
}'
{"_index":"books","_type":"book","_id":"1","_version":8,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":7,"_primary_term":1}

# 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 8,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "LeeKim",
    "started" : "2014-05-1",
    "pages" : 305,
    "category" : "ICT"
  }
}

# author 필드를 배열로 변경
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '
{
"doc":{"author":["Lee"]}
}'
{"_index":"books","_type":"book","_id":"1","_version":5,"result":"updated","_shards":{"total":2,"successful":1,"fai
led":0},"_seq_no":4,"_primary_term":1}

# 2. 배열 추가
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '{
"script":{
    "source":"ctx._source.author.add(params.new_author)",
    "params":{
        "new_author":"Kim"
        }
    }
}'
{"_index":"books","_type":"book","_id":"1","_version":11,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":10,"_primary_term":1}

# 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 11,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : [
      "Lee",
      "Kim"
    ],
    "started" : "2014-05-1",
    "pages" : 300,
    "category" : "ICT"
  }
}
```

이번에는 `if` 문을 이용한 조건문을 처리해보자. author 필드의 값에 "Kim"이 포함된 경우 pages 필드 값을 100으로, 포함되지 않은 경우 200으로 바꾸는 명령이다.
```bash
$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d '{
"script":{
  "source":"if(ctx._source.author.contains(params.auth)) {ctx._source.pages=100} else {ctx._source.pages=200}",
  "params": {
    "auth":"Kim"
    }
  }
}'
{"_index":"books","_type":"book","_id":"1","_version":12,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":11,"_primary_term":1}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 12,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : [
      "Lee",
      "Kim"
    ],
    "started" : "2014-05-1",
    "pages" : 100,
    "category" : "ICT"
  }
}
```

### 파일을 이용한 데이터 처리

입력할 데이터의 내용을 미리 파일에 기록한 뒤 다음과 같이 `@{파일명}`을 지정하는 형식으로도 입력할 수 있다.

> `curl -X(메서드) http://host:port/{인덱스}/{타입}/{도큐먼트id} -d @{파일명}`

```bash
$ pwd
~/Project
$ mkdir start-elasticsearch
$ cd start-elasticsearch

# 파일생성
$ echo '{
  "title":"Elasticsearch Guide",
  "author":["Kim", "Lee"],
  "date":"2014-05-01",
  "pages":300
}' > book1

# 파일을 이용해 데이터 생성
$ curl -XPUT 'http://localhost:9200/books/book/1' -H 'Content-Type: application/json' -d @book1
{"_index":"books","_type":"book","_id":"1","_version":3,"result":"updated","_shards":{"total":2,"successful":1,"fai
led":0},"_seq_no":2,"_primary_term":1}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 3,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : [
      "Kim",
      "Lee"
    ],
    "date" : "2014-05-01",
    "pages" : 300
  }
}
```

이번에는 파일과 `_update` API를 이용해 데어터를 업데이트해보자.
```bash
# 파일 생성
$ echo '{
"doc": {
  "category":"ICT"
}}' > update_1

$ curl -XPOST 'localhost:9200/books/book/1/_update' -H 'Content-Type: application/json' -d @update_1
{"_index":"books","_type":"book","_id":"1","_version":4,"result":"updated","_shards":{"total":2,"successful":1,"fai
led":0},"_seq_no":3,"_primary_term":1}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 4,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : [
      "Kim",
      "Lee"
    ],
    "date" : "2014-05-01",
    "pages" : 300,
    "category" : "ICT"
  }
}
```

### 벌크(`_bulk`) API를 이용한 배치 작업
여러 명령을 한꺼번에 실행할 수 있는 배치 작업을 위한 벌크(`_bulk`) API를 제공한다. 입력할 데이터를 모아 한꺼번에 처리하므로 데이터를 각각 처리하고 결과를 반환할 때보다 속도가 매우 빠르다.

> `curl -XPOST http://host:port/{인덱스}/{타입}/_bulk -d '{JSON 데이터}' 또는 @{파일명}`

> `curl -XPOST http://host:port/{인덱스}/_bulk -d '{JSON 데이터}' 또는 @{파일명}`

> `curl -XPOST http://host:port/_bulk -d '{JSON 데이터}' 또는 @{파일명}`

벌크 API는 index, create, delete, update의 4가지 동작을 처리할 수 있다. delete를 제외한 index, create, update 동작은 **실행 메타 정보(action meta data)와 요청 데이터(request body)**가 각각 한 쌍씩 묶여 동작한다. delete 동작은 삭제할 도큐먼트의 **실행 메타 정보만 필요**로 하므로 요청 데이터는 입력하지 않는다.

실행 메타 정보는 `{동작 {인덱스, 타입, 도큐먼트 id}}` 형식으로 입력한다. id는 생략할 수 있으며 생략하면 임의의 문자열이 id로 저장된다. 주의할 점은 실행 메타 정보와 요청 데이터는 한줄로 입력해야 하며 각 입력이 끝날 때마다 반드시 줄 바꿈을 해야 한다.

```json
{"index": {"_index":"books", "_type":"book", "_id":1}}  //index 실행 메타 정보
{"field":"value"}  // index 요청 데이터

{"delete": {"_index":"books", "_type":"book", "_id":1}}  // delete 실행 메타 정보

{"create": {"_index":"books", "_type":"book", "_id":1}}  // create 실행 메타 정보
{"field":"value"}  // create 요청 데이터

{"update": {"_index":"books", "_type":"book", "_id":1}}  // update 실행 메타 정보
{"doc": {"field":"value"}}  // update 요청 데이터
```

index와 create 모두 데이터를 입력하는 명령이다. 다만 도큐먼트가 이미 존재할 때 create 명령을 사용하면 입력 오류가 발생한다. index 명령은 기존에 도큐먼트가 존재하면 입력하는 데이터로 기존 도큐먼트를 덮어쓰게 된다.

```bash
$ curl -XPOST 'localhost:9200/_bulk' -H 'Content-Type: application/json' -d '
{"index":{"_index":"books", "_type":"book", "_id":"1"}}
{"title":"Elasticsearch Guide", "author":"Kim", "pages":250}
{"index":{"_index":"books", "_type":"book", "_id":"2"}}
{"title":"Elasticsearch Easy Guide", "author":"Lee", "pages":300}
'
{"took":40,"errors":false,"items":[{"index":{"_index":"books","_type":"book","_id":"1","_version":5,"result":"updated","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":4,"_primary_term":1,"status":200}},{"index":{"_index":"books","_type":"book","_id":"2","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1,"status":201}}]}

$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 5,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "pages" : 250
  }
}

$ curl -XGET 'localhost:9200/books/book/2?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "2",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Easy Guide",
    "author" : "Lee",
    "pages" : 300
  }
}
```

처리할 동작이 많을 때는 명령을 일일이 입력하는 방법보다는 파일에 입력해 놓고 벌크를 이용해 처리하는 방법이 훨씬 효과적이다. 벌크 파일은 바이너리로 처리해야 하므로 데이터 옵션을 `-d`가 아닌 `--data-binary`로 해야 한다.
```bash
$ vi bulk_1
{"delete":{"_index":"books", "_type":"book", "_id":"1"}}
{"update":{"_index":"books", "_type":"book", "_id":"2"}}
{"doc":{"date":"2014-05-01"}}
{"create":{"_index":"books", "_type":"book", "_id":"3"}}
{"title":"Elasticsearch Guide 2", "author":"Park", "pages":400}

$ curl -XPOST 'localhost:9200/_bulk?pretty' -H 'Content-Type: application/json' --data-binary @bulk_1
{
  "took" : 72,
  "errors" : false,
  "items" : [
    {
      "delete" : {
        "_index" : "books",
        "_type" : "book",
        "_id" : "1",
        "_version" : 6,
        "result" : "deleted",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 5,
        "_primary_term" : 1,
        "status" : 200
      }
    },
    {
      "update" : {
        "_index" : "books",
        "_type" : "book",
        "_id" : "2",
        "_version" : 2,
        "result" : "updated",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 1,
        "_primary_term" : 1,
        "status" : 200
      }
    },
    {
      "create" : {
        "_index" : "books",
        "_type" : "book",
        "_id" : "3",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 0,
        "_primary_term" : 1,
        "status" : 201
      }
    }
  ]
}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "found" : false
}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/2?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "2",
  "_version" : 2,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Easy Guide",
    "author" : "Lee",
    "pages" : 300,
    "date" : "2014-05-01"
  }
}

# 데이터 확인
$ curl -XGET 'localhost:9200/books/book/3?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "3",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide 2",
    "author" : "Park",
    "pages" : 400
  }
}
```
벌크 API를 사용할 때 URI에 인덱스, 타입 수준까지 명시하면 벌크 데이터의 실행 메타 정보에 인덱스, 타입을 생략할 수 있다. 실행 메타 정보에 타입과 id만 명시하면 된다.
```bash
$ curl -XPOST 'localhost:9200/books/book/_bulk?pretty' -H 'Content-Type: application/json' -d '
{"index": {"_id":"1"}}
{"title":"Elasticsearch Guide", "author":"Kim", "pages":250}
'
{
  "took" : 25,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "books",
        "_type" : "book",
        "_id" : "1",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 6,
        "_primary_term" : 1,
        "status" : 201
      }
    }
  ]
}

# 확인
$ curl -XGET 'localhost:9200/books/book/1?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "pages" : 250
  }
}

# id 없이 생성
$ curl -XPOST 'localhost:9200/books/book/_bulk?pretty' -H 'Content-Type: application/json' -d '
{"index": {}}
{"title":"Elasticsearch Guide", "author":"Kim", "pages":250}
'
{
  "took" : 54,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "books",
        "_type" : "book",
        "_id" : "yzN9nWcBBtTGuTMCz_Q6",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 2,
        "_primary_term" : 2,
        "status" : 201
      }
    }
  ]
}

# 확인
$ curl -XGET 'localhost:9200/books/book/yzN9nWcBBtTGuTMCz_Q6?pretty'
{
  "_index" : "books",
  "_type" : "book",
  "_id" : "yzN9nWcBBtTGuTMCz_Q6",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Elasticsearch Guide",
    "author" : "Kim",
    "pages" : 250
  }
}
```
벌크 API를 이용한 배치 작업의 성능과 한계는 엘라스틱서치가 설치된 시스템 하드웨어의 영향을 받는다. 통상적으로 1,000~5,000개 정도의 작업을 한 배치로 실행하는 것이 바람직하며 10,000개 이상의 작업을 배치로 실행하면 오류가 발생할 확률이 높다.
