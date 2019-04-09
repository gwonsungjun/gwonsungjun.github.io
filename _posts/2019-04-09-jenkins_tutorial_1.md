---
title: '1) Jenkins Tutorial - Installing Jenkins and GitLab with Docker'  
layout: post  
tags : jenkins, docker, gitlab
category: jenkins
subtitle: 도커를 이용해 젠킨스, 깃랩 설치하기.
author : sungjun
---

**도커 컴포즈를 이용해 젠킨스, 깃랩 설치하기.** 

---

이번 시리즈 글은 수많은 삽질을 통해 젠킨스를 사내에 구축하며 얻은 유용한 팁을 젠킨스를 처음 접하는 분들께 도움이 되고자 작성하게 되었습니다.   
Tutorial은 블로그에 단계별로 나눠 포스팅할 예정이고 pipeline script 외 기타 필요한 코드들을 [GitHub Repo](https://github.com/gwonsungjun/jenkins_tutorial)에 커밋할 것입니다.   
Git Hosting service는 깃헙이 아닌 깃랩을 직접 설치해서 사용할 것입니다. (깃헙과 깃랩은 거의 유사하기 때문에 필요한 호스팅 서비스를 사용하면 될 것 같습니다.)


## 개발환경

- IDE : Intellij IDEA Ultimate
- OS : Mac OS X
- Spring boot 2.1.3.RELEASE
- Java8
- Gradle
- docker 18.09.2

## Contents

1. [Installing Jenkins and GitLab with Docker](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)
2. Jenkins initial setting
3. Create AWS EC2 Instance
4. Registering Jenkins items using web ui
5. Registering Jenkins items using pipeline

## 1. Installing Jenkins and GitLab with Docker

### 1-1. Docker 설치

운영체제별로 설치 방법이 다르므로 자신의 환경에 맞게 설치합니다.

####  Windows
- [Windows 10 : Pro, Enterprise or Education](https://docs.docker.com/docker-for-mac/install/)
- Windows 10 : Home
    - 이전에 작성한 [Windows 10 Home에서 Docker 설치 하기](https://gwonsungjun.github.io/articles/2018-01/DockerInstall) 글을 참조

#### MacOS
- <https://docs.docker.com/docker-for-mac/install/> 참고

#### Linux

```shell
curl -fsSL https://get.docker.com/ | sudo sh
sudo usermod -aG docker $USER # sudo 없이 사용, 현재 접속중인 사용자에게 권한주기
    
# 재로그인 시 적용됨
docker -v
```

### 1-2. Docker compose 설치
Windows와 Mac의 경우는 docker 설치 시 docker compose가 기본적으로 포함되어 있을 것입니다.

#### Linux
- <https://github.com/docker/compose/releases> 접속하여 Latest release 버전을 확인합니다.
- 2019.04.09 기준 1.24.0 최신 버전 (최신 버전 확인 후 아래 curl 명령 download 뒤에 버전을 변경)

```shell
curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Access 권한 설정
chmod +x /usr/local/bin/docker-compose
    
# test
docker-compose version
```

### 1-3. Jenkins Dockerfile 작성

- dockerfile과 docker-compose 차이는 간단하게 설명하면 아래와 같고 자세한 설명은 [docker compose 공식 문서](https://docs.docker.com/compose/overview/)를 참고합니다.
    - dockerfile : 이미지를 만드는 도구
    - docker-compose : 컨테이너를 만드는 도구
- 나중에 설명할 것이지만 배포를 위해 zip, aws 명령이 필요합니다.

`vi Dockerfile-jenkins`

```dockerfile
# 1. Jenkins Long Term Support(LTS) 이미지 생성
FROM jenkins/jenkins:lts
    
# 2. 명령을 실행할 사용자 설정
USER root
    
# 3. Jenkins build 시 zip command install
RUN apt-get update
RUN apt-get install -y zip
    
# 4.Jenkins build 시 필요한 awscli command install
RUN apt-get install -y python-pip
RUN pip install awscli
RUN pip install --upgrade pip
```

### 1-4. docker-compose.yml 작성

- version : <https://docs.docker.com/compose/compose-file/>
- image : 도커 컨테이너의 기반이 되는 베이스 이미지를 지정
- build : Dockerfile에 이미지 구성을 저장하고 이를 자동으로 build 하여 베이스 이미지로 지정
- port : YAML은 xx:yy형식을 시간으로 인식하므로 포트 번호를 설정할 때 다음과 같이 꼭 쌍따옴표("")안에서 문자열을 입력해야 함
- volumes : 컨테이너의 볼륨을 마운트

Dockerfile-jenkins와 동일 디렉토리에서 `vi docker-compose.yml`

```yml
version: "3.7"

services:
	jenkins:
		build:
			context: .
			dockerfile: Dockerfile-jenkins
		container_name: jenkins
		restart: always
		user: root
		ports:
			- "8080:8080"
		volumes:
			- "/home/jenkins/jenkins_home:/var/jenkins_home"
	gitlab:
		image: "gitlab/gitlab-ce:latest"
		container_name: gitlab
		restart: always
		hostname: "gitlab.example.com"
		environment:
			GITLAB_OMNIBUS_CONFIG: |
				external_url = "gitlab.example.com"
		ports:
			- "80:80"
			- "443:443"
			- "22:22"
		volumes:
			- "/srv/gitlab/config:/etc/gitlab"
			- "/srv/gitlab/logs:/var/log/gitlab"
			- "/srv/gitlab/data:/var/opt/gitlab"
```

### 1-5. docker-compose 실행

```shell
docker-compose up -d # 백그라운드로 실행
...
docker ps # 컨테이너 정상 구동 확인
    
# +) 각 서비스 버전 업데이트
docker-compose pull [service...] # update
docker-compose up -d [service...] # 재실행
```

### 1-6. Jenkins & GitLab 접속

아래와 같은 화면이 보이면 설치 성공!

- Jenkins 접속 : <http://localhost:8080>

![jenkins-main](/assets/images/usingimages/jenkins_tutorial/jenkins-main.png)

- gitlab 접속 : <http://localhost:80>

![gitlab-main](/assets/images/usingimages/jenkins_tutorial/gitlab-main.png)


### 마무리

docker-compose를 이용해 간편하게 Jenkins와 GitLab을 설치하고 실행시켜 보았습니다.   
다음 시간에는 Jenkins, GitLab login 및 기본적인 Jenkins Setting에 대해 진행하겠습니다.   
감사합니다.
