---
title: 'ElasticSearch, 엘라스틱서치 기초'  
layout: post  
tags :  
- elasticsearch
- 엘라스틱서치
- ELK
category: ELK Stack
---

**Ubuntu에서 Elasticsearch 실습**{: style="display:inherit;text-align:center;"}

---

# ElasticSearch
- [인프런 : ELK 스택 (ElasticSearch, Logstash, Kibana) 으로 데이터 분석](https://www.inflearn.com/course/elk-%EC%8A%A4%ED%83%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D/) 강의를 듣고 정리
### ELK 스택
- ElasticSerach
    - [Lucene](https://en.wikipedia.org/wiki/Apache_Lucene) 기반 검색 엔진. HTTP 웹 인터페이스와 JSON 문서 객체를 이용해 분산된 multitenant 가능한 검색을 지원한다.
- LOGSTASH 
    - 어떤 데이터든지 ElasticSearch에 수집을 해주는 역할
    - server-side 처리 파이프라인, 다양한 소스에서 동시에 Data를 수집-변환 후 Stash로 전송
- Kibana : Data Visualization
    - ELASTICSEARCH data를 시각화하고 탐색을 지원

## 1. 우분투에 엘라스틱서치 설치하기
- ubuntu 16.04

### (1) install java
- elasticsearch는 jvm 위에서 동작, 따라서 jdk 설치.

```bash
$ sudo add-apt-repository -y ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get -y install oracle-java8-installer
$ java -version
```

### (2) install Elasticsearch 
- 작성일 기준 가장 최신 버전 : 6.3.2(2018/08/01) 

```bash
$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.3.2.deb
$ sudo dpkg -i elasticsearch-6.3.2.deb
    - install 완료 시 install, config, init file path
    1. Install path: /usr/share/elasticsearch
    2. Config file: /etc/elasticsearch
    3. Init script: /etc/init.d/elasticsearch
$ sudo systemctl enable elasticsearch.service
    - automatic : elasticsearch를 안전하게 끄고 켜기 위한 setting
```

### (3) Elasticsearch Start & Stop

```bash
$ sudo service elasticsearch start
$ sudo service elasticsearch stop
$ curl -XGET 'localhost:9200'
 ```

 ```json
 - 시작되었을 시 아래 출력

{
  "name" : "XncFy-t",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "mudpoGJuR1SG8fJJTx5xKw",
  "version" : {
    "number" : "6.3.2",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "053779d",
    "build_date" : "2018-07-20T05:20:23.451332Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

## 2. 엘라스틱서치 기본 개념
```json
# 교수님이 어떤 강의를 가르친다는 정보를 저장
- doc1
"class" : {
    "name" : "database"
    "professor" : "john"
}
- doc2
 "class" : {
     "name" : "algorithm"
     "professor" : "john"
 }
- doc3
 "class" : {
     "name" : "database"
     "professor" : "tom"
 }
```
- 해당 정보는 indexing 되어 아래와 표와 같이 저장된다.
- 해당 keyword가 어떤 documet에 있는지 저장.
- Better, faster when you `search` text!

| text | Document |
| ---- | -------- |
| john | doc1, doc2 |
| database | doc1, doc3 |
| ... | ... |

- 관계형 데이터 베이스와 저장되는 방식이 다름을 알 수 있다. (아래 표 참고)
- 관계형 데이터 베이스는 전부를 저장.

| document | context |
| ---- | -------- |
| doc1 | "class" : {"name" : "database" "professor" : "john"} |
| doc2 | "class" : {"name" : "database" "professor" : "john"} |
| doc3 | "class" : {"name" : "database" "professor" : "john"} |

### (1) elasticsearch와 RDB
- 참조 : <http://d2.naver.com/helloworld/273788>

|RDB	|elasticsearch|
|------| ------------|
|Database|	Index|
|Table|	Type|
|Row|	Document|
|Column|	Field|
|Schema|	Mapping|
|Index|	Everything is indexed|
|SQL|	Query|

### (2) elasticsearch = REST API 사용

|RDB	|elasticsearch|
|------| ------------|
| select | get |
| update | put |
| insert | post |
| delete | delete |

## 3. Elasticsearch CREATE, READ, DELETE 

|RDB	|elasticsearch| CRUD |
|------| ------------| ----- |
| select | GET | Read|
| update | PUT | Update|
| insert | POST | Create|
| delete | DELETE | Delete|

### (1) Verify Index
- index를 만들기 전에 해당 index가 있는지 조회

```bash
$ curl -XGET localhost:9200/classes
$ curl -XGET localhost:9200/classes?pretty
- 결과는 "status" : 404
```

- ?pretty : JSON formatting

### (2) Create Index
```bash
$ curl -XPUT localhost:9200/classes
- success 결과 : {"acknowledged":true,"shards_acknowledged":true, "index":"classes"}
```

### (3) Delete Index
```bash
$ curl -XDELETE localhost:9200/classes
```

### (4) Create Document
- 6.0 이후 버전 부터는 Content-Type checking이 엄격하게 들어가므로 `-H 'Content-Type:application/json'` 옵션 추가 필요
- 물론 `http.content_type.required` configuration을 통해 설정 가능함. default는 true

```bash
$ curl -XPOST localhost:9200/classes/class/1/ -H 'Content-Type:application/json' -d '{"title":"Algorithm", "professor":"John"}'
```

- success 결과 : {"_index":"classes","_type":"class","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}


### (5) Create INDEX, TYPE, DOCUMENT FROM FILE
```bash
$ curl -XPOST localhost:9200/classes/class/1/ -H 'Content-Type:application/json' -d @oneclass.json
```

```json
onclass.json

{
  "title" : "Machine Learning",
  "Professor" : "Minsuk Heo",
  "major" : "Computer Science",
  "semester" : ["spring", "fall"],
  "student_count" : 100,
  "unit" : 3,
  "rating" : 5
}

```

## 4. 엘라스틱서치 데이터 업데이트(UPDATE)
- Document(Row)를 수정

```bash
$ curl -XPOST localhost:9200/classes/class/1/ -H 'Content-Type:application/json' -d '{"title":"Algorithm", "professor":"John"}'
```

- classes : Index ( RDB's Database )
- class : Type ( RDB's Table )
- 1 : Document ( RDB's Row )

### (1) Add Field (RDB's Column)

```bash
$ curl -XPOST localhost:9200/classes/class/1/_update -H 'Content-Type:application/json' -d '{"doc":{"unit":1}}'
$ curl -XGET localhost:9200/classes/class/1?pretty
```

|before	|after|
|------| ------------|
| {"title":"Algorithm", "professor":"John"} | {"title":"Algorithm", "professor":"John", "unit": 1} |

- Script를 이용한 방법 (unit field의 값에 5 더함)

```bash
$ curl -XPOST localhost:9200/classes/class/1/_update -H 'Content-Type:application/json' -d '{"script":"ctx._source.unit +=5"}'
```

## 5. 엘라스틱서치 벌크(Bulk)
- File로 된 여러개의 Document를 한 번에 저장
- classes file
    - Bulk : 2개의 Line으로 구성
        - 첫줄 = meta information : 어떤 index에, 어떤 type에, id 어떤 걸로 해서 이 document를 넣어라.

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch02/classes.json
```

```bash
$ curl -XPOST localhost:9200/_bulk?pretty -H 'Content-Type:application/json' --data-binary @classes.json
```

## 6. 엘라스틱서치 매핑(Mapping)
- 매핑은 RDB의 스키마와 같은 개념
- 지금까지는 매핑없이 사용했다.(매핑없이 데이터 삽입 가능하다는 뜻)
- 하지만 실무에서는 매핑을 사용하면 위험.
- 예를들어 Date 타입을 넣어야하는데 매핑이 없다면 날짜인지 모르니깐 단순히 문자열로 저장할 수도 있음.
- 잘 못 지정된 타입은 계산, Kibana에 보여줄 때(시각화) 할 때 어려워진다.

### (1) Create Index
```bash
$ curl -XDELETE localhost:9200/classes
$ curl -XPUT localhost:9200/classes
```

```json
$ curl -XGET localhost:9200/classes?pretty

{
  "classes" : {
    "aliases" : { },
    "mappings" : { },
    "settings" : {
      "index" : {
        "creation_date" : "1493388598544",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "N2GHTYgFQqOy_DoP9zuISA",
        "version" : {
          "created" : "5030199"
        },
        "provided_name" : "classes"
      }
    }
  }
}
```
- 현재 mapping이 비어있음.


### (2)Create Mapping
- Get classesRating_mapping.json
- 해당 파일을 가져온 후 편집기를 통해 String -> text로 전부 변경

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch02/classesRating_mapping.json
```

- Mapping

```bash
$ curl -XPUT localhost:9200/classes/class/_mapping -H 'Content-Type:application/json' -d @classesRating_mapping.json
```
- verify

```json
$ curl -XGET localhost:9200/classes?pretty

결과
{
  "classes" : {
    "aliases" : { },
    "mappings" : {
      "class" : {
        "properties" : {
          "major" : {
            "type" : "text"
          },
          "professor" : {
            "type" : "text"
          },
          "rating" : {
            "type" : "integer"
          },
          "school_location" : {
            "type" : "geo_point"
          },
          "semester" : {
            "type" : "text"
          },
          "student_count" : {
            "type" : "integer"
          },
          "submit_date" : {
            "type" : "date",
            "format" : "yyyy-MM-dd"
          },
          "title" : {
            "type" : "text"
          },
          "unit" : {
            "type" : "integer"
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1533113500660",
        "number_of_shards" : "5",
        "number_of_replicas" : "1",
        "uuid" : "1Pt13zuPQm6lIv-DEXNmAw",
        "version" : {
          "created" : "6030299"
        },
        "provided_name" : "classes"
      }
    }
  }
}
```

```bash
$ curl -XPOST localhost:9200/_bulk?pretty -H 'Content-Type:application/json' --data-binary @classes.json
$ curl -XGET localhost:9200/classes/class/1/?pretty
```

```json
- 결과 확인해보면 데이터 타입에 맞게 잘 들어옴.
{
  "_index" : "classes",
  "_type" : "class",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : {
    "title" : "Machine Learning",
    "Professor" : "Minsuk Heo",
    "major" : "Computer Science",
    "semester" : [
      "spring",
      "fall"
    ],
    "student_count" : 100,
    "unit" : 3,
    "rating" : 5,
    "submit_date" : "2016-01-02",
    "school_location" : {
      "lat" : 36.0,
      "lon" : -120.0
    }
  }
}
```

## 7. 엘라스틱서치 Search
- Get simple_basketball.json

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/simple_basketball.json
```

- Add Document(Bulk)

```bash
$ curl -XPOST localhost:9200/_bulk -H 'Content-Type:application/json' --data-binary @simple_basketball.json
```

### (1) Search

```bash
$ curl -XGET localhost:9200/basketball/record/_search?pretty
```

```json
- 조회 결과

{
  "took" : 40,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "basketball",
        "_type" : "record",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "team" : "Chicago Bulls",
          "name" : "Michael Jordan",
          "points" : 20,
          "rebounds" : 5,
          "assists" : 8,
          "submit_date" : "1996-10-11"
        }
      },
      {
        "_index" : "basketball",
        "_type" : "record",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "team" : "Chicago Bulls",
          "name" : "Michael Jordan",
          "points" : 30,
          "rebounds" : 3,
          "assists" : 4,
          "submit_date" : "1996-10-11"
        }
      }
    ]
  }
}
```

### (2) Search - URI (옵션)

```bash
$ curl XGET localhost:9200/basketball/record/_search?q=points:30&pretty
```

- q (쿼리는) point:30 (포인트가 30인 것만 조회)

### (3) Search - REQUEST BODY
```bash
curl XGET localhost:9200/basketball/record/_search -d '{
  "query": {
    "term": {
      "points": 30
    }
  }
}'
```

- Request Body의 경우 여러 옵션이 존재
- <https://www.elastic.co/guide/en/elasticsearch/reference/5.3/search-request-body.html> 참조

## 8. 엘라스틱서치 메트릭 어그리게이션(Metric Aggregation)
- 어그리게이션 : 엘라스틱서치 안에 있는 Document 안에서 조합을 통해서 어떠한 값을 도출할때 사용하는 방법
- 메트릭 어그리게이션 : 산술할때 사용 (합계, 평균, 최대, 최소값 등)

### (1) Add Documents
```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/simple_basketball.json
$ curl -XPOST localhost:9200/_bulk -H 'Content-Type:application/json' --data-binary @simple_basketball.json
```

### (2) Average
```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/avg_points_aggs.json
```

```json
{
  "size": 0,
  "aggs": {
    "avg_score": {
      "avg": {
        "field": "points"
      }
    }
  }
}
```
- aggreations = aggs

```bash
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @avg_points_aggs.json
```

### (3) MAX
```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/max_points_aggs.json
```

```json
{
  "size": 0,
  "aggs": {
    "max_score": {
      "max": {
        "field": "points"
      }
    }
  }
}
```

```bash
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @max_points_aggs.json
```

### (4) Min
```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/min_points_aggs.json
```

```json
{
  "size": 0,
  "aggs": {
    "min_score": {
      "min": {
        "field": "points"
      }
    }
  }
}
```

```bash
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @min_points_aggs.json
```

### (5) SUM
```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/sum_points_aggs.json
```

```json
{
  "size": 0,
  "aggs": {
    "sum_score": {
      "sum": {
        "field": "points"
      }
    }
  }
}
```

```bash
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @sum_points_aggs.json
```

### (6) STATS
- 평균, 합계, 최소, 최대 값을 한 번에 도출

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch03/stats_points_aggs.json
```

```json
{
  "size": 0,
  "aggs": {
    "stats_score": {
      "stats": {
        "field": "points"
      }
    }
  }
}
```

```bash
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @stats_points_aggs.json
```

## 9. 엘라스틱서치 버켓 어그리게이션(Bucket Aggregation)
- 버켓 어그리게이션 : GROUP BY (데이터를 일정 기준으로 묶어서 결과를 도출)

### (1) Create Basketball Index

```bash
curl -XPUT localhost:9200/basketball
```

### (2) Mapping Basketball

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch04/basketball_mapping.json
$ curl -XPUT localhost:9200/basketball/record/_mapping -H 'Content-Type:application/json' -d @basketball_mapping.json
```

### (3) Add Basketball Documents

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch04/twoteam_basketball.json
$ curl -XPOST localhost:9200/_bulk -H 'Content-Type:application/json' --data-binary @twoteam_basketball.json
```

### (4) TERM aggs - Group by team

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch04/terms_aggs.json
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @terms_aggs.json
```

```json
{
  "took" : 10,
  "timed_out" : false,
  "_shards" : {
    "total" : 10,
    "successful" : 10,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 28,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "players" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "chicago",
          "doc_count" : 2
        },
        {
          "key" : "la",
          "doc_count" : 2
        }
      ]
    }
  }
}
```

- chicago : 2, la : 2 확인 가능

### (5) STATS aggs - Group by team
- 각 팀별로 그룹한 것 중 분석 통계(stats)

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch04/stats_by_team.json
$ curl -XGET localhost:9200/_search?pretty -H 'Content-Type:application/json' --data-binary @stats_by_team.json
```

## Links 
- <https://sanghaklee.gitbooks.io/elk/content/data-science.html>

