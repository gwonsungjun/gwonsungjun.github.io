---
title: 'Oracle Developer Meetup - Serverless Computing'  
layout: post  
tags :  
- seminar
- oracle
category: seminar
---

**2018 [3rd] Oracle Developer Meetup, Serverless Computing 세미나**{: style="display:inherit;text-align:center;"}

---

### [3rd] Oracle Developer Meetup | Serverless Computing과 친해지기 세미나에 다녀왔습니다.

코드가 실행되는 시스템에 대한 생각을 접고 창의적인 코드 개발에만 집중하자. 최근 가장 뜨거운 화두인 ‘Serverless Computing’ 의 장점과 아키텍쳐, 실제 활용 사례를 알아보고, 실습까지 한번에 하는 시간을 가졌습니다.

### 13:00-13:50 | [Overview] Serverless Computing 친해지기 (한국오라클 김종규 컨설턴트)

**서버리스란 ?**
- 기준은 개발자다.
- 개발자가 서버에 대해 신경을 안 쓰는 환경
- 애플리케이션 개발(코드)만 신경 쓰고 나머지 서버는 몇 개가 필요하고 컨테이너가 몇 개 필요한지에 대한 생각 없이
- 애플리케이션 개발을 위한 새로운 아키텍처
- 시스템에 관해 신경 쓰지 않음
- 서비스 준비시간이 매우 빠름
- 사용되는 시간만 지불

> **일반 환경**
애플리케이션
라이브러리
컨테이너
실행환경
운영시스템

> **클라우드 환경**
애플리케이션
라이브러리
컨테이너

> **서버리스 환경**
애플리케이션

<br/>

**서버리스 아키텍처의 중심 - Function**
- 애플리케이션 아키텍쳐 - Monolithic application -> Microservice application -> function (가장 작은 단위의 서비스)
- function 단위로 서비스 ( oracle 제외 서버리스 아키텍처의 중심 - 다른 벤더들도 다 같음)

**Function as a Service (FaaS)**
- 서버리스 아키텍처의 중심
- function
  - 그 자체로 일을 수행할 수 있는 작은 단위
  - 어떤 입력을 받고 기능을 수행한 다음 아웃풋을 내는 형태
- Function as a Service (FaaS)
  - Function 하나가 Faas 플랫폼의 하나의 유닛으로 배포
  - 프로비져닝, 배포하고, 스케일 업/다운, 빌링, 인증, 인가, 보안 등을 제공
  - 매우 빠르고 스케일도 매우 클 수 있으며 어떤 언어나 플랫폼에서도 수행 가능

**다른 벤더의 serverless 대표적인 예 : aws lamda**

**서버리스의 중심**
- 코드(개발자가 코드에만 집중)
- 유기적인 확장 (리퀘스트가 갑작스럽게 몰리더라도 스케일업/다운-로드밸런싱 가능)
- 저렴한 비용(리퀘스트가 있을떄만)
- 하나의 기능을 만들면 Function 단위로 도커 이미지 생성
- 각 기능(function)을 서로 다른 언어로 만들어도 Function의 조립이 가능하다.


### 14:00-15:20 | [Special Session] 개발자를 위한 JavaOne 2017 업데이트 2탄 - Enhancement 중심으로 (양수열 Java Champion, Oracle ACE)

- 모든 나라 중 개발자 컨퍼런스 비용이 가장 값싼 곳이 우리나라다.(많이 참여하자!)
- javaOne 2017 컨퍼런스에 대한 소개  - ex)1,화성탐사도 자바로..., 2,주제 - Monolithic  vs MicroServices 를 똥에 비유하며 재밌게 묘사하기도..(해외 컨퍼런스는 국내의 컨퍼런스의 딱딱함과 다르게 재미있게 진행한다는 말)

**java 9**

- JVM의 변화
- JAVA는 플랫폼과 랭귀지 두 가지 측면으로 볼 수 있다.
- 웹으로 넘어오면서 상당수의 엔터프라이즈 솔루션이 자바로 구현 많이 됬었는데 조금 씩 다른 곳으로 넘어가면서 자바가 랭귀지 보다 플랫폼 쪽(즉, JVM)에 좀 더 신경을 쓸 것이다.
- 자바만 하는 사람이 별로 없다. 언어적 특성, 목적에 따라 분화가 많이 됨.
- GC에 대한 개선이 많이 됨 (JEP248, JEP250...), code cache, logging 등..
- JDK/JRE File Structure 변경 - java 8과 java9는 아예 다르다. ( 폴더 구조의 재편, rt.jar 등이 로딩 되지 않음 ), 9에서 만든것을 8에서 돌릴 수 없음..
- 컴파일러 최적화에서도 개선이 많이 됨.
- Api document 검색 가능
- nashon - ECMAScript 5.1 spec 지원
- Certificates(인증서), SHA-1 -> SHA-256으로 변경
- Deprecate the Applet API (웹 표준이 대두되고 있음..)
- com.sun.sercurity.auth. ~  Six deprecated APIs
- JRE Version selection command line option Remove
- Demos and Samples Remove
- Remove the jhat tool

**요약**
- 상당히 많은 큰 변화를 겪음
- 장단점이 많지만.. 하위호환성을 유지하기 힘들어져서 논란이 많다고 생각함.
- 7->8은 랭귀지 적인 이슈가 많이 컸음. 8->9로 간다해서 람다 function 제한이라던 가는 없고 증분이라고 생각하라.

### 15:30-16:30 | [Hands-On] Fn Project를 이용한 Serverless Computing 실습 (한국오라클 김종규 컨설턴트)

: Docker 기반의 Serverless 개발 플랫폼인 Fn Project를 통해 Serverless Computing의 처음 단계를 실습하는 시간.

- [fnproject](https://github.com/shiftyou/fnproject)
- [fnproject 실습을 위한 한글 문서](https://github.com/fnproject/fn)
- fnproject 실습을 위한 한글문서에 적힌 내용을 순서대로 실습하는 시간을 가짐.

### 후기

serverless가 뜨거운 감자인 건 알았지만 어떤 것을 의미하는지 의문만 품고 있었습니다. 하지만 이번 세미나를 통해 완벽하게는 아니지만 serverless가 어떤 의미 인지는 대략적으로 감을 잡을 수 있었던 좋은 기회였던 것 같습니다. 또한, java9에 대해 좋은 정보를 얻게 돼서 기쁩니다.
이 세미나를 통해 도커와 서버리스, java9 등에 대해서 좀 더 공부를 해보고 싶다는 마음이 들었습니다.
주니어의 입장에서는 받아들이기 쉽진 않았지만 그래도 좋은 강의를 볼 수 있게 해주신 한국 오라클에 감사합니다. 피자도 맛있게 잘 먹었습니다:)<br/>
*본문의 내용은 제가 이해한 대로 작성하였기 때문에 틀린 부분이 있다면 댓글로 피드백을 부탁드리겠습니다. 감사합니다.
