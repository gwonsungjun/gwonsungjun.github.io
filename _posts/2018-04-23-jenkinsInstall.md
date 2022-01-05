---
title: 'Ubuntu 16.04 Jenkins 설치'  
layout: post  
tags : jenkins
category: jenkins
subtitle: Ubuntu 16.04 Jenkins Installation process
author : sungjun
published : false
---

**Ubuntu 16.04 Jenkins Installation process** 

---

# Ubuntu 16.04 Jenkins 설치

## Installation

```js
- sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
- sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
- sudo apt-get update
- sudo apt-get install -y jenkins
```

## Upgrade
- sudo apt-get update
- sudo apt-get install jenkins

## 기본 설정 파일
- /etc/default/jenkins
- 젠킨스 기본 포트는 8080이기 때문에 해당 포트가 사용 중이라면 위의 설정 파일에서 HTTP_PORT= 부분을 수정해주면 된다

## 로그 파일
- /var/log/jenkins/jenkins.log

## 데몬 실행 파일 위치
- /etc/init.d/jenkins

## 젠킨스 Start (실행)
- sudo service jenkins start
- http://localhost:8080 (ip:port)으로 접속하면 아래 그림이 출력됨을 확인할 수 있다.

![jenkins1](/assets/images/usingimages/jenkins/jenkins1.PNG)

## Administrator pssword
- sudo vi /var/lib/jenkins/secrets/initialAdminPassword 파일을 열어서 초기 암호를 copy & paste

## Install suggested plugins
- 추천되는 Plugin 설치를 진행
- 설치하고 싶은 plugins를 선택하려면 두번째 항목을 클릭

![jenkins2](/assets/images/usingimages/jenkins/jenkins2.PNG)

## 플러그인 설치 화면 (설치 진행중)
![jenkins3](/assets/images/usingimages/jenkins/jenkins3.PNG)

## 관리자 계정 생성
![jenkins4](/assets/images/usingimages/jenkins/jenkins4.PNG)

## Start using Jenkins
![jenkins5](/assets/images/usingimages/jenkins/jenkins5.PNG)

## 설치 후 초기화면!
![jenkins6](/assets/images/usingimages/jenkins/jenkins6.PNG)

## Links
- [Ubuntu 16.04 에서 Jenkins 설치](https://www.fun25.co.kr/blog/jenkins-ubuntu-16-04-install/?category=004)
- [Jenkins 시작하기 (on Ubuntu)]( https://kanziw.github.io/tools/jenkins/2017/01/16/jenkins-install.html)
