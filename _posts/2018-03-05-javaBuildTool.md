---
title: 'Java Build Tool'  
layout: post  
tags : java, Build Tool, ant, maven, gradle
category: java
subtitle: Ant, Maven, Gradle 특징 설명 + Groovy 간단 정리
author : sungjun
---

**Ant, Maven, Gradle 특징 설명 + Groovy 간단 정리** 

---

사내에서 java 빌드 툴 Gradle에 대해서 발표를 진행하였고   
발표자료와 아래 내용을 같이 참조해서 보면 많이 도움 될 것 같아 작성하였다.  
- **발표자료는 [slideshare](https://www.slideshare.net/SungjunGwon1/gradle-89730981) < 클릭!**

### Build

- **빌드란 ?** 소스 코드 파일을 동작하는 독립적인 소프트웨어 산출물로 만드는 과정. 개발하는 어플리케이션을 완성 시켜주는 마지막 단계.
- **빌드 툴(Build Tool) ?** 새로운 버전의 프로그램을 빌드할때 사용하는 툴
- **일반적인 빌드 툴이 제공하는 기능** : 전처리, 컴파일, 패키징, 테스팅, 배포
- **빌드 자동화란?** 개발자가 매일 진행하는 일을 자동화 하는 행동들, 빌드툴들이 이런 자동화를 도와준다. 소스 코드를 바이너리 코드로 컴파일, 바이너리 코드 패키징, 테스트 수행, 실서비스 시스템 배포, 도큐먼트 및 릴리즈 노트 생성
- 빌드를 잘하는 것은 훌륭한 애플리케이션을 만드는데 중요한 과정. 좋은 빌드 툴을 선택하는 것은 개발의 기본 요건이다.

### Ant

-  Ant의 가장 큰 장점을 들자면 개발자가 자유롭게 빌드 단위(Ant에서는 target이라고 한다.)를 지정하고 빌드 단위 간의 의존 관계를 자유롭게 설정할 수 있다는 것, 하지만 자유도가 높다는 것은 잘 활용할 경우 좋은 도구가 될 수 있지만 그렇지 않을 경우 애물단지로 전락할 가능성이 있다.
- 개발자 친화적, 정해진 규칙이나 프로세스가 없고 모든것을 개발자가 정의한다. (Task 중심)
- Ant 자체는 XML이나 Remote Repository(Dependency)를 사용할 수 없다. 그래서 + ivy

### Maven

- Maven의 가장 큰 장점은 Convention Over Configuration 전략에 따라 프로젝트 빌드 과정에 대한 많은 부분이 이미 관례로 정해져 있다는 것, 따라서 Maven 기반 프로젝트를 경험한 개발자는 Maven을 기반으로 하고 있는 새로운 프로젝트에서도 쉽게 적용할 수 있다는 것. 하지만 관례가 항상 좋은 것은 아니며, 특수항 상황이 발생하는 경우에는 맞지 않는 경우도 종종 발생.
- 규칙, 프로세스가 뚜렷하게 정의, 그러나 맞춤 로직을 구현하기 힘듬.(Maven의 핵심은 의존성+라이프사이클+플러그인(모조)+pom.xml)
- 도구 내에서 자체적으로 지원하는 관례에 벗어나는 것에만 설정을 해주면 된다. 그 외에는 도구에 포함된 관례에 맞춰서 개발한다면 설정이 필요없다!

### Gradle (ant + ivy + maven + gant의 합)

> ant : flexibility / full control / chaining of targets   
 ivy : dependency management   
 maven : convention over configuration / multimodule projects / extensibility via plugins   
 gant : Groovy DSL(Domain specific language) on top of Ant   

- Gradle Wrapper - 이미 존재하는 프로젝트를 새로운 환경에 설치할때 별도의 설치나 설정 과정없이 곧 바로 빌드할 수 있게 하기 위함.
- gradlew - 유닉스용 실행 스크립트 (>gradle build x, >./gradlew build) - wrapper 사용
- gradlew.bat - 윈도우용 실행 배치 스크립트
- build.gradle - 의존성, 플러그인 설정 등을 위한 스크립트 파일
- setting.gradle - 프로젝트의 구성 정보 기록, 어떤 하위 프로젝트들이 어떤 관계로 구성되어 있는지 기술(gradle은 이 파일에 기술된대로 프로젝트를 구성)

### Groovy

도메인 특화 언어 Domain-specific language - DSL : 특정한 도메인을 적용하는데 특화된 언어. (GPL- General-purpose language 와는 반대의 개념)
- Groovy - 자바와의 상호 연동 + 기존 자바개발자의 경우 학습 비용이 적음 + 동적 타이핑 언어(컴파일x 바로 실행가능) + 클로저 지원
- 자바 버추얼 머신을 위한 기민(agile)하고 동적인 언어
- 자바의 강점으로 만들었지만 파이썬, 루비, 스몰토크와 같은 언어의 추가적인 특징도 가지고 있음
- 최근의 프로그래밍 특징 기반으로 만들어서 기존의 프로그래머는 거의 새로 배울 필요가 없음
- 도메인 특성 언어와 단축 문법을 지원하여 가독성과 유지보수하기 쉬움
- 기본적으로 강력한 처리력, 객체지항 기능, Ant DSL로 쉘과 스크립트를 쉽게 만들 수 있음
- 웹, GUI, DB, 콘솔 어플리케이션을 개발할 때 scaffolding 코드 감소로 개발 생산성이 오름
- 유닛 테스트와 블랙박스(out-of-the-box) 모방으로 테스트가 간단함
- 기존의 모든 자바 객체와 라이브러리를 통합할 수 있음
- 자바 바이트코드로 바로 컴파일해서 자바를 사용하는 어디에도 사용할 수 있음

### 용어
- CoC : Convention over configuration : 명확한 관습으로 인해 더 편해진다는 의미
- 이행적 종속성 :  A->B이고 B->C일 때 A->C를 만족하는 관계

### Links
- <http://kurapa.com/2008/03/30/%EA%B7%B8%EB%A3%A8%EB%B9%84%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/>
- <https://gradle.org/>
- <https://zeroturnaround.com/rebellabs/java-tools-and-technologies-landscape-2016/>
- <https://www.slideshare.net/ihoneymon/gradle-27152839>
- <https://www.slideshare.net/sup2rior/gradle-guide-v-01-31412469>
- <https://www.slipp.net/wiki/pages/viewpage.action?pageId=11632748>
- <https://www.slideshare.net/ssuser59a869/java-build-tool>
- <https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09>
