---
title: 'Logstash,로그스태시 기본 개념' 
layout: post  
tags :  
- Logstash
- 로그스태시
- ELK
category: ELK Stack
---

**Ubuntu에서 Logstash 실습**{: style="display:inherit;text-align:center;"}

---

# Logstash (로그스태시)
- [인프런 : ELK 스택 (ElasticSearch, Logstash, Kibana) 으로 데이터 분석](https://www.inflearn.com/course/elk-%EC%8A%A4%ED%83%9D-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D/) 강의를 듣고 정리

![elk-stack](/assets/images/usingimages/kibana/elk-stack.png) [그림참조](https://sanghaklee.gitbooks.io/elk/content/data-science.html)
- ELK 스택에서 로그스태시는 Input을 담당.(다양한 형태의 데이터를 받아들여서 사용자가 지정한 형식에 맞게 필터링 가능.)
- 로그스태시에서 받은 Input은 변환되어 엘라스틱서치에 들어가게 되고 
- 키바나는 그 데이터를 Visualize

## 1. 우분투에 로그스태시 설치하기
- ubuntu 16.04

### (1) Install Logstash
- elasticsearch와 마찬가지로 jdk 필요.
- 작성일 기준 가장 최신 버전 : 6.3.2(2018/08/02) 

```bash
$ wget https://artifacts.elastic.co/downloads/logstash/logstash-6.3.2.deb
$ sudo dpkg -i logstash-6.3.2.deb
```

- Install path: /usr/share/logstash

### (2) Config Logstash
- config file 필요
- config file : input, output, filter 3개로 구성

```bash
$ vi logstash-simple.conf
```

```shell
input {
        stdin { }
}
output {
        stdout { }
}
```

### (3) Run Logstash
```bash
$ sudo /usr/share/logstash/bin/logstash -f ./logstash-simple.conf
```

## Links 
- <https://sanghaklee.gitbooks.io/elk/content/data-science.html>