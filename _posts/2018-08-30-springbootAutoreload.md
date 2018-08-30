---
title: 'Spring boot + LiveReload' 
layout: post  
tags : springboot, livereload
category: springboot
subtitle: Spring boot 개발 시 자동 리로드 환경 구축 
author : sungjun
---

**Intellij Spring boot 개발 시 자동 리로드 환경 구축**{: style="display:inherit;text-align:center;"}

---

# Intro
- Spring boot 개발 시 소스 변경이 일어날 때마다 매번 App을 재기동 시키는 일이 매우 번거로워 방법을 찾게 되었다.
- 크롬 확장 프로그램 `LiveReload`와 spring boot의 `devtools`를 이용하면 쉽게 자동 리로드 환경을 구축할 수 있다.
- 아래 설정 방법은 Chrome brower와 Intellij IDE를 기반으로 작성 하였다.

## 1. LiveReload 확장 프로그램 설치

![liveReload1](/assets/images/usingimages/liveReload/livereload1.PNG)

## 2. 의존성 추가

- maven
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

- Gradle
```gradle
dependencies {
    compile("org.springframework.boot:spring-boot-devtools")
}
```

## 3. Intellij Compiler 설정
- Ctrl + Shift + A : compiler 입력
- `Build project automatically 체크`-> Apply

![liveReload2](/assets/images/usingimages/liveReload/livereload2.PNG)

 ## 4. Intellij registry 설정
 - Ctrl + Shift + A : registry 입력
 - `compiler.automake.allow.when.app.running 체크` -> close

 ![liveReload3](/assets/images/usingimages/liveReload/livereload3.PNG)

## 추가 팁
- default 설정이 아닌 커스텀해서 사용하고 싶은 경우 application.yml 파일에서 수정 가능.

```yml
spring:
  devtools:
    restart:
     enabled: true
    livereload:
     enabled: true
```


## 확인
- spring boot App을 실행하고 browser에서 LiveReload extension 클릭해서 활성화(Enable) 시켜주면 변경사항이 생길 시 자동으로 리로드 해주는 것을 확인할 수 있다.

## Links
- <https://stackoverflow.com/questions/33869606/intellij-15-springboot-devtools-livereload-not-working>
- <http://haviyj.tistory.com/11>