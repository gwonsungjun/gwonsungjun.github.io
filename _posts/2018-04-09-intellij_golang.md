---
title: 'Go 언어 개발 환경 설정 - Intellij (windows)'  
layout: post  
tags :  
- Golang
- intellij
category: Golang
---

- Intellij에서 Go 언어 개발 환경 설정하기 (windows)

---

# Go 언어 개발 환경 설정 - Intellij (windows)

hyperledger fabric (Linux Foundation project) 소스를 보기 위해 IntelliJ에 Clone 하였는데 IntelliJ는 GO 언어 개발 환경을 default로 지원하지 않았다. 따라서 IntelliJ에 Go 언어 개발 환경을 설정하였고 아래 그 과정을 설명하였다.   
크게 순서는 다음과 같다.
  1. Go lang SDK 설치
  2. 환경설정 확인 및 SDK 정상 설치 확인
  3. Intellij에 Go 언어 Plug-in 설치

## Go lang SDK 설치
  - [Go lang SDK 다운로드 링크](https://golang.org/dl/) 현재기준(2018.04.09) 1.10.1 version
  - 아래 그림에서 해당 운영체제(Windows)에 맞게 설치

  ![go1](/assets/images/usingimages/intellijGo/go1.PNG)

  - 다운로드 완료 후 해당 파일을 실행해서 설치를 완료한다.

  ![go2](/assets/images/usingimages/intellijGo/go2.PNG)

  - SDK가 설치되면, 자동으로 SDK가 설치된 디렉토리가 시스템 환경변수로 등록되긴 하지만, 정상적으로 입력되었는지 확인하는 작업 필요

## 환경설정 확인 및 SDK 정상 설치 확인

### -환경설정 확인

  - 제어판 -> 시스템 -> 고급 시스템 설정 -> 고급 -> 환경변수
  - 시스템 변수 -> Path 선택 후 -> 편집 클릭
  - Go 언어가 설치된 bin 폴더가 등록 되어 있는지 확인 ( 만약 없다면 추가하기 )

  ![go3](/assets/images/usingimages/intellijGo/go3.PNG)

### -SDK 설치 확인

  - cmd -> go version : 아래 그림과 같이 버전이 출력되는지 확인

  ![go4](/assets/images/usingimages/intellijGo/go4.PNG)

## Intellij에 Go 언어 Plug-in 설치

Intellij에서 Go 언어를 사용하기 위해서는 별도의 플러그인 설치가 필요.

  - Intellij 실행
  - File -> settings
  - Plugins -> 하단의 Brows repositories 선택
  - "Go" 검색 후 아래 그림에 있는 Go LANGUAGES 클릭
  - Install 후 재시작.

  ![go5](/assets/images/usingimages/intellijGo/go5.PNG)

## 확인 (테스트)
 - 나 같은 경우에는 Fabric 프로젝트를 분석하려고 Go 개발 환경을 설정하였다. 아래 그림과 같이 정상적으로 적용되었음. (귀여운 고퍼(Gopher) 아이콘이 보인다~)
 - 프로젝트 생성 후 Run 해서 콘솔에 찍히는지 확인하기.
 - Intellij 말고도 Go언에서 특화된 **GoLand** 라는 JetBrains의 IDE가 존재한다!

 ![go6](/assets/images/usingimages/intellijGo/go6.PNG)

## Links
[윈도우즈 Go 언어 개발환경 설정 (IntelliJ)](http://dog-paw.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B0%EC%A6%88-Go-%EC%96%B8%EC%96%B4-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95-IntelliJ)
