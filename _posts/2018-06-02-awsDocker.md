# AWSKRUG Container Hands-On #1 - 모두의 Docker
- awskrug(aws Community group) container 모임 3회 중 첫 번째 세션 모두의 Docker 밋업에 참석하게 되었다.
- 평소에 Docker에 관심이 많았고 이미 흥했지만 앞으로 더 흥할 Docker를 알아가고자 참석하게 되었다.

## 사전 준비
- <https://hub.docker.com> 개인 계정
- <https://github.com> 개인 계정
- AWS EC2 Instance 생성(Amazon Linux)
- SSH 접속 툴 준비
- 실습 문서 download

## 목차
- 1. Docker ??
- 2. Docker 주요 개념
- 3. Docker Hands-on

## 1. Docker ??
- 하드웨어의 발전으로 더 많은 효율성을 내기 위해 가상화 사용
- 게스트 OS 사용 - 하드웨어에 의존적인 명령어 사용 : 성능 저하 불러일으킴
- 도커는 게스트 OS 가 없음
- 따라서, 기존의 가상 시스템보다 퍼포먼스가 좋다.
- 아래 그림은 보통 docker를 설명할 때 보여주는 그림과 도커 아키텍처
![docker](/assets/images/usingimages/awsDocker/docker.jpg)
![Docker_Architecture](/assets/images/usingimages/awsDocker/Docker_Architecture.png)

## 2. Docker 주요개념
- images
  - docker 이미지는 레이어의 형태
  - 변경되는 부분만 따로 이미지 생성
  - 생성된 이미지는 부모의 이미지 바라봄
  - 상대적으로 경량화 가능
- container
   - 도커의 이미지가 실행된 상태
- overlay Network
   - 네트워크 추상화
   - underlay network (물리적 L2, L4...) - 기존 네트웍 방식
   - underlay위에 존재하게됨.
- union file system
- Server orchestration
  - 도커 호스트들을 관리하기 위한 툴들
  - docker SWARM
  - 쿠버네티스 등

## 3. Docker Hands-on
- ec2 생성 (Amazon linux)
  - security group : all trafic
  - 원활한 실습을 위해 (실습 끝난 후에는 반드시 삭제, 과금 때문)
- ssh -i awskrug-docker-default-keypair.pem ec2-user@public-ip
  - 나는 ubuntu에서 실습 진행
- awskrug-docker-default-keypair.pem 실행권한 있어야함 (chmod)

### Docker 설치
- sudo yum update -y
- sudo yum install -y docker
- sudo usermod -a -G docker ec2-user
  - 적용 위해 터미널 재접속 필요
- sudo docker service start
- docker version

### Docker hub 사용하기
- docker login
  - docker hub 계정으로 login 하기
- docker search ubuntu
  - OFFICIAL - 공식 이미지
  - STARS - 즐겨찾기 받은 횟수

### Docker Container 만들어 보기
- docker pull ubuntu:14.04
  - 태그 정보는 도커 허브 접속해서 볼 수 있음(예를들어 ubuntu 검색 후 detail)
- docker images
  - size가 엄청 작음을 확인할 수 있음
- docker run -i -t ubuntu:14.04 /bin/echo hello
- docker ps
  - 실행중인 컨테이너 목록만
- docker ps -a
  - 정지된 컨테이너 목록까지 확인
- docker run -i -t --name ubuntu-awskrug ubuntu:14.04
  - 위 명령은 ubuntu:14.04 이미지로 ubuntu-awskrug 이름을 가지는 container 만들고 쉘을 통해 attach 하라는 뜻.
  - 프롬프트가 변경되면서 container 내부로 들어온 것을 확인.
  - 프로세스 살아 있는 상태로 빠져나올 때는 : ctrl+p+q
  - 다시 접속 할때는 : docker attach [name or container id]
  - exit 입력시 프로세스 죽게됨

### Docker Container 환경으로 APM 구축하기
- docker run -itd -p 80:80 --name web-demo ubuntu:14.04
- docker exec -it web-demo /bin/bash
- apt-get install -y apache2 php5 mysql-server php5-mysql php5-curl git
- cd /var/www/html
- git clone https://github.com/blueice123/web-demo
- service mysql start
- service apache2 start
- mysql -u root -p
  - mysql> CREATE DATABASE web_demo;
  - mysql> CREATE USER 'username'@'%' IDENTIFIED BY 'password';
  - mysql> GRANT ALL PRIVILEGES ON web_demo.* TO 'username'@'%';
  - mysql> quit
- cd /var/www/html/web-demo/
- mysql -u username -p web_demo < web_demo.sql
- Enter password: password
- vi ./config.php   
  - // Database connection - parameters  <-- DB connection 설정 부분 변경
  - $db_hostname = "localhost";
  - $db_database = "web_demo";
  - $db_username = "username";
  - $db_password = "password";
- chown -R www-data:www-data uploads/
- service apache2 restart
- http://<Docker Host Public IP>/web-demo/
- 웹 접속하면 화면 출력 확인 가능 but.아키텍처 변경이 필요함!

### Docker image 만들기
- docker commit -a "test-user" -m "AWSKRUG" web-demo web-demo:0.1
- docker images

### docker file을 이용하면 더 유용함
- dockerfile 생성
- build
  - docker build --tag web-demo:0.2 ~/AWSKRUG-Docker/
  - docker build --tag {image_tag:version} {docker_file 파일위치}
- 특정 디렉토리에 불필요한 파일이 있으면 dockerignore 작성

### Docker image push
- Docker Repository 를 생성 후 repository name에 맞게 이미지를 다시 빌드.
- Image name을 다음과 같이 정의해주셔야 DockerHub에 push 된다. DockerHubID/DockerHubReponame:TAG
- docker commit -a "test-user" -m "AWSKRUG" web-demo kwen5600/web-demo:0.3
- docker build --tag kwen5600/web-demo:0.3 ~/AWSKRUG-Docker/
- docker push kwen5600/web-demo:0.3
- docker stop -f $(docker ps -a -q)

## 계속해서...
- 계속해서 Docker Compose 로 Wordpress 만들기 실습을 진행하였고
- jenkins를 사용하여 container를 배포하는 실습을 진행하였다.
- 모든 것을 담고 싶었지만 내용이 너무 많아서 작성하지 못했다.
- 하지만, 실습 진행했던 내용을 개인적으로 테스트해보며 블로그에 글을 작성해볼 예정이다.
- 막상 혼자 시작하기에는 쉽지 않았던 도커를 실습해본 정말 좋은 경험이었다.
- 기회가 된다면 2번째, 3번째 밋업도 참석하고 싶다!
- 발표자분들과 awskrug 구성원분들께 매우 감사하다. :)
