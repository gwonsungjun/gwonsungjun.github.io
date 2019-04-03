---
title: 'Gradle Multi Module Project 구성하기'  
layout: post  
tags : java, gradle, springboot
category: java
subtitle: Gradle Multi Module Project 구성 방법을 알아본다.
author : sungjun
---

**Gradle Multi Module Project 구성 방법을 알아본다.** 

---

## 환경

spring boot 버전별로 설정 시 약간의 차이가 있으므로 주의.

- spring boot 2.1.3 (2019.04.03 기준 최신 버전)
- gradle 4.10.3
- jdk 1.8

## 1. Create Root Project

- (1) New Project > Gradle > Additional Libraries and Frameworks > java 선택 > Next
- (2) GroupId, ArtifactId 입력 > Next
- (3) Use auto-import 체크 해제되어있는지 확인 > Next > Finish

## 2. Create Sub Project

필요한 서브 프로젝트 개수만큼 생성

- (1) File > New > Module
- (2) Gradle > Additional Libraries and Frameworks > java 선택 > Next
- (3) Group, Version Inherit 체크 되어있는지 확인
- (4) ArtifactId 입력 > Next
- (5) Sub Project는 Root Project 바로 아래에 모듈이 생성되어야 함. Content root 경로 잘 확인하기 > Finish

## 3. Project 기본 골격

- 프로젝트 루트 밑에 src 폴더가 생성되었다면 Delete
- 빌드는 항상 root 프로젝트를 기준으로 할 것이기 때문에 sub project의 gradle 폴더, gradlew, gradlew.bat, settings.gradle 등의 파일은 필요 없다. src 폴더와 build.gradle만 존재하도록 한다.
    - 서브 프로젝트를 Gradle project로 생성하였다면 build.gradle만 존재하지만 spring Initializr로 생성하면 gradle 폴더, gradlew 등이 존재.
- Project 기본 골격은 아래 그림과 같다. (admin, api, common 서브 프로젝트 생성)

![gradle-multi-module-scaffolding](/assets/images/usingimages/gradle-multi-module/gradle-multi-module-scaffolding.png)


## 4. Root Project와 Sub Project 관계 설정
- (자동으로 등록되어 있지 않다면) Root Project의 settings.gradle에 아래와 같은 형식으로 sub project name을 include.
- sample-multi-module 프로젝트가 하위 프로젝트들을 관리하겠다는 의미.

```gradle
rootProject.name = 'sample-multi-module'
include 'sample-api'
include 'sample-admin'
include 'sample-common'
```

## 5. Root build.gradle 수정

```gradle
buildscript {
    ext {
        springBootVersion = '2.1.3.RELEASE'
    }
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
        classpath "io.spring.gradle:dependency-management-plugin:1.0.6.RELEASE"
    }
}

allprojects {
    group 'com.sungjun'
    version '1.0-SNAPSHOT'
}

subprojects {
    apply plugin: 'java'
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'

    sourceCompatibility = 1.8

    repositories {
        mavenCentral()
    }

    dependencies {
        testCompile group: 'junit', name: 'junit', version: '4.12'
    }
}

project(':sample-api') {
    dependencies {
        compile project(':sample-common')
    }
}

project(':sample-admin') {
    dependencies {
        compile project(':sample-common')
    }
}
```

## 6. 각 Sub 프로젝트의 build.gradle 수정

- [각 프로젝트 별](https://github.com/gwonsungjun/gradle-multi-module/blob/master/sample-api/build.gradle) 필요한 dependencies만 설정하면 된다.

```gradle
dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

## 7. common 프로젝트 build.gradle 설정
- 예제 project >> [깃헙](https://github.com/gwonsungjun/gradle-multi-module) 확인
- 위의 예제 project의 Common 프로젝트의 처럼 main 메소드가 없는 경우
    - 아래와 같이 bootJar, jar enabled 설정을 해야한다.

``` gradle
bootJar {
    enabled = false
}
jar {
    enabled = true
}
dependencies {
    compile('org.springframework.boot:spring-boot-starter-data-jpa')
    runtime('com.h2database:h2')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

## 8. 다른 Project에서 common project를 사용 할 경우

다른 project에서 common project의 Entity 클래스와 Repsoitory를 사용하기 위해서는 `@EntityScan("com.sungjun.*")`, `@EnableJpaRepositories("com.sungjun.*")` 2개의 어노테이션을 설정 해줘야 한다.

- [api project의 SampleApiApplication class](https://github.com/gwonsungjun/gradle-multi-module/blob/master/sample-api/src/main/java/com/sungjun/api/SampleApiApplication.java) 참조



## Links
- [Gradle 멀티 프로젝트(모듈) 관리](https://okky.kr/article/375833)
- [gradle로 멀티 프로젝트 구성하기](https://github.com/hantomato/gradle-multi-proj)
- [Gradle 에서 Multi 프로젝트 만들기](https://yookeun.github.io/java/2017/10/07/gradle-multi/)