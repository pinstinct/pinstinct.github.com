# 엘라스틱 서치(Elastic Search) 01

> [시작하세요! 엘라스틱서치](http://wikibook.co.kr/elasticsearch/) 정리한 글 입니다.

## 특징

- 아파치 루씬 기반
- 실시간 분석(Near Realtime)
- 전문 검색(Full Text Search)
    - indexing : 데이터를 검색할 수 있는 구조로 변경하기 위해 원본 문소를 변환하여 저장하는 일련의 과정
    - index : indexing을 거친 결과물
    - searcing : index에 들어있는 토큰을 기준으로 해당하는 토큰이 포함된 문서를 찾는 과정
    - query : 사용자가 원하는 결과를 출력하기 위해 입력하는 검색어
    - 엘라스틱 서치에서는 indexing 절차를 거쳐 입력된 데이터를 토큰으로 변환해 inverted index 구조로 저장
- JSON 문서

## 설치

[엘라스틱 서치 설치 가이드](https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html)를 따라 설치한다. (tar 파일로 설치로 진행)

```bash
# 엘라스틱 서치는 JAVA가 필요
$ java -version

$ mkdir es
$ cd es

$ curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.1.tar.gz
$ tar -xvf elasticsearch-6.5.1.tar.gz
$ cd elasticsearch-6.5.1/bin
$ ./elasticsearch
Java HotSpot(TM) 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
[2018-12-04T12:04:31,345][INFO ][o.e.e.NodeEnvironment    ] [YxXdoFR] using [1] data paths, mounts [[/ (/dev/disk1s1)]], ...
```

`YxXdoFR` 노드명으로 실행됐다. 노드의 이름을 별도로 설정하지 않으면 임의로 생성된다. `curl` 명령어를 사용해 REST API를 사용할 수 있다. 아래의 명령으로 실행 중인 엘라스틱 서치 프로세스 정보를 조회할 수 있다.
```bash
$ curl -XGET 'localhost:9200'
{
  "name" : "YxXdoFR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "sj6eYlcpR9yEvXWRaVY_VQ",
  "version" : {
    "number" : "6.5.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "8c58350",
    "build_date" : "2018-11-16T02:22:42.182257Z",
    "build_snapshot" : false,
    "lucene_version" : "7.5.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

다음과 같이 프로세스 ID를 이용해 엘라스틱서치를 종료한다.
- `-d` : 백그라운드 실행
- `-p <file>` : 프로세스 ID를 file에 저장
```bash
$ ps -ef | grep elasticsearch
501 59861 58164   0 12:04PM ttys000    1:19.85 /usr/bin/java -Xms1g -Xmx1g -XX:+UseConcMarkSweepGC ....
$ kill 59861

$ bin/elasticsearch -d -p es.pid
Java HotSpot(TM) 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future
# pid 파일 생성 확인
$ ls
LICENSE.txt    README.textile config         es.pid         logs           plugins
NOTICE.txt     bin            data           lib            modules

$ cat es.pid
62262
$ ps -ef | grep elasticsearch
501 62262     1   0  1:12PM ttys000    0:59.61 /usr/bin/java -Xms1g -Xmx1g -XX:+UseConcMarkSweepGC ...

$ kill `cat es.pid`

# shell 파일로 만들어 실행
$ echo 'bin/elasticsearch -d -p es.pid' > start.sh
$ echo 'kill `cat es.pid`' > stop.sh
$ chmod 755 start.sh stop.sh
$ ./stop.sh
```

### 환경설정

엘라스틱 서치는 자바 가상 머신 위에서 실행되는 프로그램이다. 엘라스틱 서치가 실행되는 옵션 중 `-Xms1g`, `-Xmx1g` 옵션은 엘라스틱 서치가 실행될 때 사용하는 자바 메모리를 최소 1GB, 최대 1GB까지 사용할 수 있게 설정하는 옵션이다. 자바 메모리 옵션은 메모리 용량이 변경되는 불필요한 오버헤드를 방지하기 위해 **최소 메모리와 최대 메모리를 동일하게** 지정해서 사용하는 것을 권장하고 있다.
```bash
$ ps -ef | grep elasticsearch
501 62262     1   0  1:12PM ttys000    0:59.61 /usr/bin/java -Xms1g -Xmx1g -XX:+UseConcMarkSweepGC ...
```

관련된 설정은 `config/jvm.options` 파일에서 변경 할 수 있다.
```bash
$ vi config/jvm.options
## JVM configuration

################################################################
## IMPORTANT: JVM heap size
################################################################
##
## You should always set the min and max JVM heap
## size to the same value. For example, to set
## the heap to 4 GB, set:
##
## -Xms4g
## -Xmx4g
##
################################################################

# Xms represents the initial size of total heap space
# Xmx represents the maximum size of total heap space

-Xms1g
-Xmx1g

################################################################
```

#### `config/elasticsearch.yml`

##### 클러스터

클러스터 이름은 엘라스틱 서치 시스템 전체를 구분 짓는 이름이므로 처음 설치했을 때 반드시 바꾸는 것이 좋다.
```yml
cluster.name: es-test
```
```bash
$ curl -XGET 'localhost:9200/_cluster/stats?pretty'
{
  "_nodes" : {
    "total" : 1,
    "successful" : 1,
    "failed" : 0
  },
  "cluster_name" : "es-test",
  "cluster_uuid" : "sj6eYlcpR9yEvXWRaVY_VQ",
  ...
}
```

##### 노드

엘라스틱 서치는 하나 이상의 노드로 구성된다. **하나의 실행된 엘라스틱 서치 프로세스를 = 하나의 노드**라 한다. 각 노드가 연결된 전체 시스템을 엘라스틱 서치 클러스터라고 한다.
```yml
node.name: node-1
```

##### 경로

디렉토리 경로를 변경한다. `path.data` 경로는 쉼표(`,`)로 구분해 여러 경로를 지정할 수 있다. 이 방법으로 색인 된 데이터를 여러 개의 드라이브에 나눠서 저장할 수도 있다.
```yml
path.data: /var/lib/elasticsearch
path.logs: test_log
```

##### 메모리

자바 가상 머신 위에서 실행 중인 엘라스틱서치가 점유하는 메모리를 고정(lock)시킨다. 이렇게 해서 자바 가상 머신이 엘라스틱 서치가 사용하지 않는 메모리를 다른 자바 프로그램으로 돌리는(swap) 것을 방지할 수 있다. 앞서 `-Xms1g`, `-Xmx1g` 옵션을 동일한 크기로 설정하고 `true` 옵션을 설정해 메모리가 부족하지 않도록 방지할 수 있다. 엘라스틱 서치가 설치된 시스템의 메모리 용량을 충분히 고려해 너무 많은 메모리를 엘라스틱 서치에 할당하지 않도록 적절한 조절이 필요하다. 시스템 전체 메모리의 50%가 넘지 않도록 한다.
```yml
bootstrap.memory_lock: true
```

##### 플러그인

옵션 뒤에 플러그인 이름을 명시하면, 필수 플러그인으로 검사하여 해당 플러그인이 설치돼 있어야만 엘라스틱 서치를 실행할 수 있다.
```yml
plugin.mandatory: analysis-icu,lang-js
```

##### 네트워크

> [Network Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html)

같은 서버에서는 별도의 네트워크 설정 없이도 엘라스틱 서치 노드를 여러 개 실행하면 바로 바인딩(biding)된다. 같은 서버가 아닌 서로 다른 네트워크에 있는 엘라스틱 서치의 노드를 연결하려면 각 노드에 대한 네트워크 설정이 필요하다.

서버가 방화벽이나 인터넷 공유기 등을 통해 내부 네트워크에 위치에 있는 경우를 가정해보자. 서버의 내부 주소는 `192.168.0.40`이고 방화벽 외부의 인터넷 주소는 `121.131.44.64`인 경우 아래와 같이 설정한다.
```yml
network.bind_host: 192.168.0.40
network.publish_host: 121.131.44.64
```

기본적으로 REST API를 서비스하기 위해 엘라스틱 서치는 9200~9299 범위의 HTTP 통신 포트를 사용한다. 서버에서 엘라스틱서치 노드를 처음 실행하면 9200 포트로 실행된다. 같은 서버에서 새로운 엘라스틱 서치 노드를 또다시 실행하면 두 번째 노드는 9201번 포트로 실행된다.
```yml
http.port: 9200
```

또한, 하나의 엘라스틱 서치 노드가 다른 노드와 바인딩되어 데이터 교환을 위해 통신하는 포트는 9300~9399 범위의 HTTP 통신 포트를 사용한다. 같은 서버에서 두 개 이상의 엘라스틱 서치 노드가 실행된 경우 9300, 9301과 같이 차례대로 포트 번호를 늘려가며 실행된다.
```yml
transport.tcp.port: 9300
```

##### 게이트웨이

> [Local Gateway](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-gateway.html)

게이트웨이는 엘라스틱 서치의 전체 클러스터 상태를 저장하는 저장소이다. 엘라스틱 서치의 전체 클러스터가 종료된 후 재실행 될 때 게이트웨이에 저장된 상태 값을 읽어 들여 노드와 인덱스 등에 대해 새로 설정하는데, 이 과정을 리커버리(recovery)라 한다.

##### 디스커버리

> [Discovery settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/discovery-settings.html)

디스커버리는 원격 네트워크에 있는 노드와 바인딩 설정하는 데 필요하다.

##### 슬로우로그

> [Slow Log](https://www.elastic.co/guide/en/elasticsearch/reference/6.5/index-modules-slowlog.html)

엘라스틱 서치는 log4j 라이브러리를 사용해서 로그를 기록한다. 엘라스틱 서치에서 수행하는 `query`, `fetch`, `indexing` 활동에 대해 설정할 수 있다.

#### `config/log4j2.properties`

> [Logging Configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/logging.html)

### 플러그인 설치

```bash
$ bin/elasticsearch-plugin install analysis-icu
$ bin/elasticsearch-plugin list
$ bin/elasticsearch-plugin remove analysis-icu
```

## 시스템 구조

가장 큰 시스템 단위인 클러스터와 프로세스 단위인 노드, 그리고 색인 된 데이터를 저장하는 메커니즘인 샤드와 복사본을 살펴본다.

### 클러스터와 노드

- 엘라스틱 서치의 가장 큰 시스템 단위 : 클러스터(Cluster)
- 하나의 클러스터는 여러 개의 노드(Node)로 구성
- 서로 다른 클러스터는 데이터의 접근이나 교환을 할 수 없는 독립적인 시스템

여러 대의 서버가 하나의 클러스터를 구성할 수 있으며, 반대로 하나의 물리적 서버에 여러개의 클러스터가 존재할 수도 있다.

- 노드는 하나의 엘라스틱 서치 프로세스로써 실행

같은 시스템, 또는 네트워크 바인딩이 되도록 설정한 다른 시스템에서 같은 클러스터 이름으로 설정된 엘라스틱 서치를 실행하면 새로운 노드가 실행되면서 두 개의 노드는 하나의 클러스터로 바인딩 되어 묶인다. 클러스터 이름을 서로 다르게 설정한 노드 두 개를 실행하면 바인딩 되지 않고 각각 다른 클러스터를 구성한다.

엘라스틱 서치는 클러스터 이름만 같게 유지하고 프로세스만 실행하면 시스템 확장이 끝난다.

#### 노드 바인딩

새로운 디렉토리를 구성하고, 엘라스틱 서치를 실행해보자.

```bash
$ cd c1_n1

~/Project/start-elasticsearch/c1_n1
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test
```

```bash
$ cd c1_n2

~/Project/start-elasticsearch/c1_n2
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test
```

```bash
~/Project/start-elasticsearch/c1_n1
$ bin/elasticsearch
[2018-12-05T14:59:20,752][INFO ][o.e.n.Node               ] [Q7hCteh] initialized
[2018-12-05T14:59:20,752][INFO ][o.e.n.Node               ] [Q7hCteh] starting ...
[2018-12-05T14:59:20,981][INFO ][o.e.t.TransportService   ] [Q7hCteh] publish_address {127.0.0.1:9300}, bound_addresses {[::1]:9300}, {127.0.0.1:9300}
[2018-12-05T14:59:24,069][INFO ][o.e.c.s.MasterService    ] [Q7hCteh] zen-disco-elected-as-master ([0] nodes joined), reason: new_master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true}
[2018-12-05T14:59:24,074][INFO ][o.e.c.s.ClusterApplierService] [Q7hCteh] new_master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true}, reason: apply cluster state (from master [master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true} committed version [1] source [zen-disco-elected-as-master ([0] nodes joined)]])
[2018-12-05T14:59:24,100][INFO ][o.e.x.s.t.n.SecurityNetty4HttpServerTransport] [Q7hCteh] publish_address {127.0.0.1:9200}, bound_addresses {[::1]:9200}, {127.0.0.1:9200}
[2018-12-05T14:59:24,101][INFO ][o.e.n.Node               ] [Q7hCteh] started
```
먼저 `Q7hCteh` 노드이름으로 엘라스틱 서치가 실행됐음을 확인할 수 있다.
- `publish_address {127.0.0.1:9300}, bound_addresses {[::1]:9300}, {127.0.0.1:9300}` : 9300번 포트를 사용해서 데이터 통신을 하고 있음을 의미
- `zen-disco-elected-as-master ([0] nodes joined), reason: new_master {Q7hCteh}` : 현재 실행된 노드가 마스터 노드로 선출됐음을 의미
- `publish_address {127.0.0.1:9200}, bound_addresses {[::1]:9200}, {127.0.0.1:9200}` : http 통신, 즉 REST API가 9200 포트를 통해서 실행되고 있음을 의미

```bash
~/Project/start-elasticsearch/c1_n2
$ bin/elasticsearch
[2018-12-05T15:00:15,251][INFO ][o.e.n.Node               ] [zHVF9t8] initialized
[2018-12-05T15:00:15,251][INFO ][o.e.n.Node               ] [zHVF9t8] starting ...
[2018-12-05T15:00:15,482][INFO ][o.e.t.TransportService   ] [zHVF9t8] publish_address {127.0.0.1:9301}, bound_addresses {[::1]:9301}, {127.0.0.1:9301}
[2018-12-05T15:00:18,950][INFO ][o.e.c.s.ClusterApplierService] [zHVF9t8] detected_master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true}, added {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true},}, reason: apply cluster state (from master [master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true} committed version [4]])
[2018-12-05T15:00:19,531][INFO ][o.e.x.s.t.n.SecurityNetty4HttpServerTransport] [zHVF9t8] publish_address {127.0.0.1:9201}, bound_addresses {[::1]:9201}, {127.0.0.1:9201}
```
`zHVF9t8` 노드이름으로 엘라스틱 서치가 실행됐다.
- `publish_address {127.0.0.1:9301}, bound_addresses {[::1]:9301}, {127.0.0.1:9301}` : 9301번 포트를 통해 데이터 통신을 하고 있음을 의미
- `detected_master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}` : `Q7hCteh`인 마스터 노드를 찾았고, 이 노드와 9300번 포트를 통해 바인딩 됐음을 의미
- `publish_address {127.0.0.1:9201}, bound_addresses {[::1]:9201}, {127.0.0.1:9201}` : http 통신, 즉 REST API가 9201 포트를 통해서 실행되고 있음을 의미

```bash
~/Project/start-elasticsearch/c1_n1
[2018-12-05T15:00:18,900][INFO ][o.e.c.s.MasterService    ] [Q7hCteh] zen-disco-node-join[{zHVF9t8}{zHVF9t8XRtCvpHijMFglfA}{ASz4xmTdSxWrD_9C5mKoxw}{127.0.0.1}{127.0.0.1:9301}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true}], reason: added {zHVF9t8}{zHVF9t8XRtCvpHijMFglfA}{ASz4xmTdSxWrD_9C5mKoxw}{127.0.0.1}{127.0.0.1:9301}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true},}
[2018-12-05T15:00:19,474][INFO ][o.e.c.s.ClusterApplierService] [Q7hCteh] added {zHVF9t8}{zHVF9t8XRtCvpHijMFglfA}{ASz4xmTdSxWrD_9C5mKoxw}{127.0.0.1}{127.0.0.1:9301}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true},}, reason: apply cluster state (from master [master {Q7hCteh}{Q7hCtehFT3uwEUJvLetUkw}{rSP198_yQ_SYKNz1WgVgYw}{127.0.0.1}{127.0.0.1:9300}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true} committed version [4] source [zen-disco-node-join[{zHVF9t8}{zHVF9t8XRtCvpHijMFglfA}{ASz4xmTdSxWrD_9C5mKoxw}{127.0.0.1}{127.0.0.1:9301}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true}]]])
```
첫 번째로 실행했던 `Q7hCteh` 노드의 터미널로 돌아 실행 로그를 확인해보자. 위와 같은 로그가 추가된 것을 확인할 수 있다. `zen-disco-node-join[{zHVF9t8}{zHVF9t8XRtCvpHijMFglfA}{ASz4xmTdSxWrD_9C5mKoxw}{127.0.0.1}{127.0.0.1:9301}`에서 `zHVF9t8` 노드가 추가됐고, 9301 포트를 통해 통신하고 있음을 확인할 수 있다.

하나의 서버에서 여러 개의 엘라스틱 서치 노드가 실행될 때 노드 사이의 바인딩을 위한 통신 포트는 9300부터 차례대로 생성되고, REST API를 위한 http 통신 포트는 9200부터 차례대로 생성됐다.

```bash
$ curl -XGET 'localhost:9200'
{
  "name" : "Q7hCteh",
  "cluster_name" : "es-test",
  "cluster_uuid" : "67MRzbn2SQ6XsbLtTEg6gQ",
  "version" : {
    "number" : "6.5.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "8c58350",
    "build_date" : "2018-11-16T02:22:42.182257Z",
    "build_snapshot" : false,
    "lucene_version" : "7.5.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}

$ curl -XGET 'localhost:9201'
{
  "name" : "zHVF9t8",
  "cluster_name" : "es-test",
  "cluster_uuid" : "67MRzbn2SQ6XsbLtTEg6gQ",
  "version" : {
    "number" : "6.5.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "8c58350",
    "build_date" : "2018-11-16T02:22:42.182257Z",
    "build_snapshot" : false,
    "lucene_version" : "7.5.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

9200번 포트에 도큐먼트를 입력하고, 9201번 포트로 입력된 도큐먼트를 조회해보자. 두 개의 노드는 서로 바인딩 돼 하나의 시스템을 이루고 있으므로 어떤 경로로도 입력하거나 조회할 수 있다.
```bash
$ curl -XPUT 'http://localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
"title":"Elasticsearch Guide",
"author":"Kim",
"date":"2014-05-01",
"pages":250
}'
{"_index":"books","_type":"book","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}

$ curl -XGET 'localhost:9201/books/book/1?pretty'
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

헤드 플러그인으로 현재 구성된 엘라스틱 서치 시스템의 구조와 데이터를 확인해보자. `Q7hCteh`와 `zHVF9t8` 두 개의 노드가 실행되고 있다. `Q7hCteh` 노드의 별 표시는 마스터 노드임을 뜻한다.

![](/image/elastic-search-1.png)

`Q7hCteh`와 `zHVF9t8` 노드가 바인딩 된 이유는 클러스터명이 같기 때문이다. 클러스터명이 다르면 노드들은 서로 바인딩 되지 않고 서로 다른 엘라스틱 서치 시스템을 구성하게 된다. 클러스터 명이 `es-test-2`인 노드를 하나 더 실행해보자.

```bash
$ cd c2_n1

~/Project/start-elasticsearch/c2_n1
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test-2
```

```bash
~/Project/start-elasticsearch/c2_n1 26s
$ bin/elasticsearch
[2018-12-05T15:09:16,327][INFO ][o.e.n.Node               ] [fe9Fs1a] initialized
[2018-12-05T15:09:16,327][INFO ][o.e.n.Node               ] [fe9Fs1a] starting ...
[2018-12-05T15:09:16,584][INFO ][o.e.t.TransportService   ] [fe9Fs1a] publish_address {127.0.0.1:9302}, bound_addresses {[::1]:9302}, {127.0.0.1:9302}
[2018-12-05T15:09:16,779][WARN ][o.e.d.z.UnicastZenPing   ] [fe9Fs1a] [1] failed send ping to {[::1]:9300}{m5Ckin7QTc-nvhrk7XNv5Q}{0:0:0:0:0:0:0:1}{[::1]:9300}
java.lang.IllegalStateException: handshake failed, mismatched cluster name [Cluster [es-test]] - {[::1]:9300}{m5Ckin7QTc-nvhrk7XNv5Q}{0:0:0:0:0:0:0:1}{[::1]:9300}
...
[2018-12-05T15:09:16,779][WARN ][o.e.d.z.UnicastZenPing   ] [fe9Fs1a] [1] failed send ping to {[::1]:9301}{9oB9NCNLQX-4hKa93BfKLg}{0:0:0:0:0:0:0:1}{[::1]:9301}
...
[2018-12-05T15:09:19,636][INFO ][o.e.c.s.MasterService    ] [fe9Fs1a] zen-disco-elected-as-master ([0] nodes joined), reason: new_master {fe9Fs1a}{fe9Fs1alQ_quXF9qrGj5RA}{J97hTnoSR6-kqN1dtR72Fw}{127.0.0.1}{127.0.0.1:9302}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true}
[2018-12-05T15:09:19,641][INFO ][o.e.c.s.ClusterApplierService] [fe9Fs1a] new_master {fe9Fs1a}{fe9Fs1alQ_quXF9qrGj5RA}{J97hTnoSR6-kqN1dtR72Fw}{127.0.0.1}{127.0.0.1:9302}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true}, reason: apply cluster state (from master [master {fe9Fs1a}{fe9Fs1alQ_quXF9qrGj5RA}{J97hTnoSR6-kqN1dtR72Fw}{127.0.0.1}{127.0.0.1:9302}{ml.machine_memory=17179869184, xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true} committed version [1] source [zen-disco-elected-as-master ([0] nodes joined)]])
[2018-12-05T15:09:19,673][INFO ][o.e.x.s.t.n.SecurityNetty4HttpServerTransport] [fe9Fs1a] publish_address {127.0.0.1:9202}, bound_addresses {[::1]:9202}, {127.0.0.1:9202}
```
`zen-disco-elected-as-master ([0] nodes joined), reason: new_master {fe9Fs1a}{fe9Fs1alQ_quXF9qrGj5RA}{J97hTnoSR6-kqN1dtR72Fw}{127.0.0.1}{127.0.0.1:9302}`에서 노드명이 `fe9Fs1a`인 새 노드가 9302번 포트로 통신하며 `fe9Fs1a` 노드가 현재 마스터 노드로 선출됐음을 확인할 수 있다.

![](/image/elastic-search-1-1.png)

새로 생성한 `fe9Fs1a` 노드는 기존 `es-test` 클러스터에 바인딩 되지 않고 `es-test-2`라는 독립적인 시스템으로 실행됐다. 서로 다른 클러스터지만 한 서버에서 실행되고 있으므로 `fe9Fs1a` 노드의 http 포트는 9202, 데이터 통신 포트는 9302 포트로 실행돼 기존의 노드와 충돌이 일어나지 않는다. 서로 다른 클러스터이므로 데이터 교환도 일어나지 않는다.

#### 마스터 노드와 데이터 노드

엘라스틱 서치에 다음과 같은 종류의 노드가 있다.

- 마스터 노드 : 전체 클러스터의 상태에 대한 메타정보를 관리하는 노드다. 기존의 마스터 노드가 종료되는 경우 새로운 마스터 노드가 선출된다. `config/elasticsearch.yml` 설정 파일에서 `node.master` 속성이 `true`인 노드는 마스터 노드로 선출될 수 있다(선출될 수 있는 것이지 반드시 마스터 노드가 되는 것은 아니다). `node.master` 속성을 `false`로 지정하면 해당 노드는 마스터 노드의 선출 후보로 선정되지 않는다.
- 데이터 노드 : 색인된 데이터를 실제로 저장하는 노드다. `node.data` 속성을 `false`로 지정하면 해당 노드는 데이터를 저장하지 않는다.

앞서 실행했던 노드들을 종료하고 두 개의 디렉토리에 엘라스틱 서치를 설치해보자.


```bash
~/Project/start-elasticsearch/c1_n1
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test

node.name: node-1
node.master: true
node.data: false
```

```bash
~/Project/start-elasticsearch/c1_n2
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test

node.name: node-2
node.master: false
node.data: true
```
엘라스틱 서치를 각각 실행하고, 데이터를 입력한다.

```bash
$ curl -XPUT 'http://localhost:9200/books/book/1' -H 'Content-Type: application/json' -d '
{
"title":"Elasticsearch Guide",
"author":"Kim",
"date":"2014-05-01",
"pages":250
}'
{"_index":"books","_type":"book","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```

별 표시가 된 `node-1` 마스터 노드와 `node-2` 노드를 각각 확인할 수 있다. `node-1` 노드는 `node.data: false`로 설정했기 때문에 데이터가 저장되지 않았다. *Unassigned*라는 항목이 보이고 상단에 클러스터 상태(cluster health)가 yellow로 됐다. 엘라스틱 서치에 색인된 데이터들은 샤드(shard)와 복사본(replica)로 구성해 저장하는데, 현재 데이터를 저장하는 노드가 `node-2` 하나밖에 없어서 복사본의 저장이 이뤄지지 않았다. 따라서 복사본 데이터는 현재 미할당(Unassigned) 상태로 돼 있고, 미할당 상태의 데이터가 존재하므로 클러스터 상태는 yellow다.

![](/image/elastic-search-2.png)

노드를 하나 더 실행해보겠다.

```bash
~/Project/start-elasticsearch/c2_n1 28s
$ vi config/elasticsearch.yml
```

```yml
cluster.name: es-test

node.name: node-3
node.master: false
node.data: true
```

![](/image/elastic-search-3.png)

`node-3`인 데이터 노드가 추가로 생성됐다. 기존에 미할당 상태였던 데이터들이 `node-3`에 할당되면서 클러스터 상태도 다시 green으로 변경됐다.

> 마스터 노드와 데이터 노드가 반드시 상호 배타적인 관계는 아니다. 마스터 노드가 false이면서 동시에 데이터 노드도 false인 노드도 존재할 수 있다. 이러한 노드는 색인과 검색을 위한 명령과 결과를 전달하는 역할로만 존재한다. 엘라스틱 서치 시스템을 구성할 때 보통 마스터 노드는 명령 수행을 전담하는 기능을 하며 데이터 노드는 http 통신 기능을 막아 놓아 REST API 접근을 차단하고 데이터를 저장하는 역할만을 전담하도록 설정하는 것이 일반적이다.


### 샤드와 복사본

엘라스틱 서치에 색인된 데이터는 여러 개의 샤드로 분할돼 저장되는데, 기본적으로 인덱스당 5개의 샤드와 5개의 복사본으로 분리된다. 샤드와 복사본 개수를 설정할 때 말고는 사용자가 직접 샤드에 접근하는 경우는 없다.

처음 데이터가 색인돼 저장되는 공간을 최초 샤드(Primary Shard)라고 한다. 최초 샤드에 데이터가 색인되고 나면 다시 동일한 최초 샤드 수만큼 복사본을 생성한다. 엘라스틱 서치가 복사본을 사용하는 이유는 크게 두 가지가 있다.

1. 데이터 손실(Fail over)을 방지하는 목적으로, 최초 샤드가 유실되는 경우 복사본을 최초 샤드로 승격시켜 시스템의 무결성을 유지한다.
2. 성능 향상을 목적으로 최초 샤드와 복사본을 동시에 검색해 더 빠르게 대이터를 찾을 수 있다.

기본적으로 같은 데이터 블록의 최초 샤드와 복사본은 서로 다른 노드에 저장된다. 이렇게 함으로써 한 노드가 실패하더라도 최초 샤드나 복사본 중 하나가 생존 상태에 있도록 한다. 실행 중인 노드가 하나인 경우 복사본은 생성되지 않고 최초 샤드만 존재한다. 이 경우 장애가 발생하면 데이터 무결성을 보장할 수 없으므로 노드는 항상 2개 이상을 유지하는 것이 좋다.

```bash
❯ curl -XPUT 'http://localhost:9200/magazines' -H 'Content-Type: application/json' -d '
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 0
  }
}'
{"acknowledged":true,"shards_acknowledged":true,"index":"magazines"}
```

magazines 인덱스를 생성하는데 샤드 개수를 2개, 복사본은 0으로 설정했다. 주의할 점은 이미 생성된 인덱스의 샤드 설정은 변경할 수 없다. 항상 데이터를 색인하기 전에 샤드와 복사본 개수를 설정해야 한다.

![](/image/elastic-search-4.png)

magazines 인덱스는 복사본 없이 최초 샤드만 2개가 존재한다. 여기서 새로운 노드를 하나 더 실행해보자.

```yml
cluster.name: es-test

node.name: node-4
node.master: false
node.data: true
```

books 인덱스는 복사본이 있으므로 node-2, node-3, node-4 중 어떤 노드가 실패해도 0~4의 샤드들이 손실없이 유지된다. 하지만 magazines 인덱스는 node-2, node-3 노드가 실패하면 데이터의 손실이 생긴다. node-3 노드를 종료해보자.

![](/image/elastic-search-5.png)

node-3 종료 시 books 인덱스에는 1, 3, 4 샤드가 유실된다. 하지만 node-2에 0, 2, 3, 4 샤드가 있고 node-4에 0, 1, 2 샤드가 있어서 0~4까지 빠진 샤드 없이 전체 데이터가 유지된다. 하지만 node-3에 있던 magazines 인덱스의 1 샤드는 복사본이 없으므로 미할당 상태가 된다.

![](/image/elastic-search-6.png)

복사본은 데이터 무결성을 위해서 반드시 필요하다. 샤드와 복사본의 개수는 노드 수와 데이터 용량을 고려해 적절하게 조절하는 것이 좋다. 색인된 데이터의 크기가 5GB고 해당 인덱스가 5개의 샤드와 1쌍의 복사본으로 구성됐다고 가정하면 1개의 샤드 용량은 각 1GB고 전체 합은 최초샤드와 복사본을 합한 10개의 샤드, 총 10GB가 된다.

