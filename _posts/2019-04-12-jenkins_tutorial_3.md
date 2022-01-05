---
title: '3) Jenkins Tutorial - Create AWS EC2 Instance'  
layout: post  
tags : aws, ec2
category: jenkins
subtitle: AWS EC2 Instance 생성
author : sungjun
---

AWS EC2 Instance 생성 

---

이번 시간에는 배포할 서버를 AWS EC2로 생성해보겠습니다.

## Contents

1. [Installing Jenkins and GitLab with Docker](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)
2. [Jenkins, GitLab initial setting](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_2)
3. [Create AWS EC2 Instance](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_3)
4. [Registering Jenkins items using web ui](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_4)
5. [Registering Jenkins items using pipeline](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_5)

## 3. Create AWS EC2 Instance

### 3-1. 리전 변경

먼저, AWS Console에 로그인해서 리전을 아시아 태평양(서울)로 변경해줍니다.

![aws_1](/assets/images/usingimages/jenkins_tutorial/aws_1.png)

### 3-2. EC2 인스턴스 생성

서비스 > EC2를 선택합니다.

![aws_2](/assets/images/usingimages/jenkins_tutorial/aws_2.png)

인스턴스 시작 버튼을 눌러줍니다.

![aws_3](/assets/images/usingimages/jenkins_tutorial/aws_3.png)

### 3-3. AMI(Amazon Machine Image) 선택

저는 `Ubuntu Server 18.04 LTS`를 선택하겠습니다.

![aws_4](/assets/images/usingimages/jenkins_tutorial/aws_4.png)

### 3-4. 인스턴스 유형 선택

`프리티어 사용 가능 표시`가 있는 t2.micro를 선택하고 `다음:인스턴스 세부 정보 구성` 버튼을 클릭합니다.

![aws_5](/assets/images/usingimages/jenkins_tutorial/aws_5.png)

### 3-5. 보안 그룹 구성 및 검토

인스턴스 구성, 스토리지 추가, 태그 추가, 보안 그룹 구성은 다음 버튼을 눌러 SKIP 합니다. 

![aws_6](/assets/images/usingimages/jenkins_tutorial/aws_6.png)

### 3-5. 새 키 페어 생성

인스턴스 시작을 위해 키 페어를 생성해야합니다.

![aws_7](/assets/images/usingimages/jenkins_tutorial/aws_7.png)

### 3-6. 인스턴스 생성 완료

![aws_8](/assets/images/usingimages/jenkins_tutorial/aws_8.png)

### 3-7. 인스턴스 원격 접속

- 다운로드한 키 파일(.pem)을 적당한 디렉토리로 이동시킨 후 권한 변경을 해줍니다.
    - `$ chmod 400 jenkins-demo.pem`

- $ ssh -i pem경로 user-name@public-dns-name 명령을 통해 원격 접속을 합니다.
    - Ubuntu AMI의 경우, user-name은 ubuntu 입니다.
    - public-dns-name은 aws ec2 퍼블릭 DNS(IPv4) 값을 가져옵니다.
    
    ![aws_9](/assets/images/usingimages/jenkins_tutorial/aws_9.png)
    
    - `ssh -i jenkins-demo.pem ubuntu@ec2-13-125-216-1.ap-northeast-2.compute.amazonaws.com`

![aws_welcome](/assets/images/usingimages/jenkins_tutorial/aws_welcome.png)

- windows 운영체제의 경우 putty를 이용하여 접속 할 수 있습니다.

## 마무리
이번 시간에는 간단하게 AWS EC2 인스턴스를 생성해보았습니다.     
다음 시간에는 실제 Jenkins item을 등록해보도록 하겠습니다.   
감사합니다.