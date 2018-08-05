---
title: 'Kibana, 키바나 기초'  
layout: post  
tags :  
- kibana
- 키바나
- ELK
category: ELK Stack
---

**Ubuntu에서 Kibana 실습**{: style="display:inherit;text-align:center;"}

---

# Kibana (키바나)
- [인프런 : ELK 스택 (ElasticSearch, Logstash, Kibana) 으로 데이터 분석](https://www.inflearn.com/course/elk-%EC%8A%A4%ED%83%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D/) 강의를 듣고 정리

## 1. 우분투에 키바나 설치하기
- ubuntu 16.04

### (1) Install Kibana
- 작성일 기준 가장 최신 버전 : 6.3.2(2018/08/02) 

```bash
$ wget https://artifacts.elastic.co/downloads/kibana/kibana-6.3.2-amd64.deb
$ sudo dpkg -i kibana-6.3.2.deb
```

### (2) Config
```bash
$ vi /etc/kibana/kibana.yml
```

- 아래 설정으로 변경
- elasticsearch와 kibana가 같은 서버에 있다고 간주하고 설정

```bash
#server.host: "localhost"
#elasticsearch.url: "http://localhost:9200"
```

- AWS, AZURE 등의 클라우드 서비스를 사용하는 경우 localhost가 아닌 내부 ip 설정
- Allow All Host : server.host = "0.0.0.0"

### (3) Start Kibana
```bash
sudo /usr/share/kibana/bin/kibana
```

- `localhost:5601` 접속


## 2. Kibana MANAGEMENT

### (1) SET basketball data

```bash
$ curl -XDELETE localhost:9200/basketball
$ curl -XPUT localhost:9200/basketball
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch04/basketball_mapping.json
- String -> text로 변환
$ curl -XPUT localhost:9200/basketball/record/_mappin -H 'Content-Type:application/json' -d @basketball_mapping.json
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch05/bulk_basketball.json
- 마지막 줄에 enter 값 필요
$ curl -XPOST localhost:9200/_bulk -H 'Content-Type:application/json' --data-binary @bulk_basketball.json
```


### (2) Access KIBANA
> ELASTICSEARCH 저장된 데이터 중 어떤 Index( RDB's Database )를 시각화할지 정한다.

- `localhost:5601` 접속
- Go to `Management Tab` 
- Go to `Index Patterns` 
- create index pattern의 index pattern에 `basketball` 입력
    - Next Step
- Time Filter field name에 `submit_date` 선택 후 create
- 결과 화면 : Mapping에 따라 type : string, blocks : number 등을 확인 가능.

![kibana1](/assets/images/usingimages/kibana/kibana1.png)

## 3. Kibana Discover
> ELASTICSEARCH 의 저장된 데이터를 JSON, Table 형식으로 보여준다. Filter를 이용해서 원하는 정보만 볼 수 있다.

### (1) Time filter
- Go to `Discover Tab` 
- view 오른쪽 상단에 보면 Time Range 정보가 있다.
    - Last 2 years 선택
- Time Range 아래 Quick, Relative, Absolute, Recent 등을 통해 time filter
- 결과 확인

![kibana2](/assets/images/usingimages/kibana/kibana2.png)

### (2) select item
- 해당 document를 아래로 펼치면 Table, JSON 형태로 볼 수 있음

![kibana3](/assets/images/usingimages/kibana/kibana3.png)
### (3) Filtering
- (+) 돋보기 모양으로 추가해 나갈 수 있음.

![kibana4](/assets/images/usingimages/kibana/kibana4.png)
### (4) Toggle
- 창문 모양 클릭시 toggle

![kibana5](/assets/images/usingimages/kibana/kibana5.png)

## 4. 키바나 비주얼라이즈(kibana Visualize) - 막대(bar)그래프, 파이차트
- Go to `Visualize Tab`
- click `Create a visualiztion`

![kibana6](/assets/images/usingimages/kibana/kibana6.png)

- click `Vertical Bar`

![kibana7](/assets/images/usingimages/kibana/kibana7.png)

- From a New Search, Select Index에서 basketball 선택

![kibana8](/assets/images/usingimages/kibana/kibana8.png)

- Y-Axis(y축) count는 제거하고 다른 Y-Axis에 Aggregation = Average, Field = points, custom Label = avg
- X-Axis(x축)에는 Aggregation = Terms, Field = name

![kibana9](/assets/images/usingimages/kibana/kibana9.png)

- 파이차트

![kibana10](/assets/images/usingimages/kibana/kibana10.png)

## 5. 키바나 비주얼라이즈(kibana Visualize) - 맵, 지도에 표시

```bash
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch02/classesRating_mapping.json
    - vi ./classesRating_mapping.json -- String -> text
$ curl -XPUT localhost:9200/classes/class/_mapping -H 'Content-Type:application/json' -d @classesRating_mapping.json
$ wget https://raw.githubusercontent.com/minsuk-heo/BigData/master/ch02/classes.json
$ curl -XPUT localhost:9200/_bulk?pretty -H 'Content-Type:application/json' --data-binary @classes.json
$ curl -XGET localhost:9200/classes/class/1?pretty
```

- Go to `Management Tab` -> Go to `Index Patterns` -> create index pattern의 index pattern에 `classes` 입력 -> Next Step -> Time Filter field name에 `submit_date` 선택 후 create

![kibana11](/assets/images/usingimages/kibana/kibana11.png)

- Go to `Visualize Tab`
- click `Create a visualiztion`
- select `coordinate Map` 

![kibana12](/assets/images/usingimages/kibana/kibana12.png)

### coordinate Map
- Geo Coordinates 클릭

![kibana13](/assets/images/usingimages/kibana/kibana13.png)

![kibana14](/assets/images/usingimages/kibana/kibana14.png)

## 6. 키바나 대시보드 (Dashboard)
- vertical bar chart -> classes 선택 후 아래 그림처럼 생성 & Save

![kibana15](/assets/images/usingimages/kibana/kibana15.png)

- Go to `dashboard tab`
- Create a dashboard -> Add -> Select Dashboard -> Save

![kibana16](/assets/images/usingimages/kibana/kibana16.png)


## Links 
- <https://sanghaklee.gitbooks.io/elk/content/data-science.html>

