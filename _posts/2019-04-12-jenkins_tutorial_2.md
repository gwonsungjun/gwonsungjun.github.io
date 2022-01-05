---
title: '2) Jenkins Tutorial - Jenkins, GitLab initial setting'  
layout: post  
tags : jenkins, docker, gitlab
category: jenkins
subtitle: 젠킨스, 깃랩 초기 설정
author : sungjun
---

젠킨스, 깃랩 초기 설정 

---

지난 [1편](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)에서 젠킨스와 깃랩을 설치해보았습니다.      
이번 시간에는 젠킨스와 깃랩의 기본 설정 작업을 진행해보도록 하겠습니다. 

## Contents

1. [Installing Jenkins and GitLab with Docker](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)
2. [Jenkins, GitLab initial setting](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_2)
3. [Create AWS EC2 Instance](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_3)
4. [Registering Jenkins items using web ui](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_4)
5. [Registering Jenkins items using pipeline](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_5)

## 2. Jenkins, GitLab initial setting

### 2-1. Jenkins 로그인

젠킨스를 설치 후 처음 접속하게 되면 지난 1편 마지막에 보인 이미지처럼 Unlock Jnekins 화면이 보입니다.   
따라서, lock 해제를 위해 젠킨스 컨테이너 안의 bash 셸로 연결하고 아래 cat 명령을 통해 Administrator password를 확인합니다.

```shell
docker exec -it jenkins /bin/bash
$ cat /var/jenkins_home/secrets/initialAdminPassword
6a85c6fa37644529a7426863a701784d
```

- 출력되는 password를 jnekins password 입력창에 copy & paste 합니다.

이다음 절차는 이전에 제가 작성한 [Ubuntu 16.04 Jenkins 설치](https://gwonsungjun.github.io/articles/2018-04/jenkinsInstall) 글을 참조하여 플러그인 설치 및 계정 생성을 통해 로그인하도록 합니다.

### 2-2. Jenkins 플러그인 설치

Jenkins 첫 메인 화면에서 Jenkin 관리 > 플러그인 관리로 이동합니다.   
설치 가능 탭을 선택하고 `gitlab`을 검색한 뒤 아래 그림과 같이 `GitLab`을 선택합니다.

![jenkins_plugin_gitlab](/assets/images/usingimages/jenkins_tutorial/jenkins_plugin_gitlab.png)

다음 `slack`을 검색해서 `Slack Notification`을 선택한 뒤 "지금 다운로드하고 재시작 후 설치하기"를 클릭합니다.
- Slack Notification은 차후에 젠킨스 빌드 결과를 slack으로 전송할 것이기 때문에 미리 설치합니다.
    
![jenkins_plugin_slack](/assets/images/usingimages/jenkins_tutorial/jenkins_plugin_slack.png)

마지막으로 `설치가 끝나고 실행 중인 작업이 없으면 Jenkins 재시작`을 체크한 뒤 설치가 완료되면 jenkins가 재시작되도록 합니다.

![jenkins_plugin_installing](/assets/images/usingimages/jenkins_tutorial/jenkins_plugin_installing.png)

젠킨스에 재접속한 뒤 플러그인 관리의 설치된 플러그인 목록을 보면 GitLab와 Slack Notification 플러그인이 설치되어 있을 것입니다.

### 2-3. Jenkins Jenkins CI Apps 설치

slack worksapce는 테스트로 생성해준 뒤 채널 목록부분 아래 Apps 옆에 +를 클릭합니다.   
jenkins를 검색하고 Install를 눌려주면 Jenkins CI 설치를 위한 브라우저 창이 열립니다.

![slack_jenkins_install_1](/assets/images/usingimages/jenkins_tutorial/slack_jenkins_install_1.png)

![slack_jenkins_install_2](/assets/images/usingimages/jenkins_tutorial/slack_jenkins_install_2.png)

Jenkins 빌드 결과 받을 채널을 생성해준 뒤 선택하고 "Add Jenkins CI integration" 버튼을 클릭합니다.

![slack_jenkins_install_3](/assets/images/usingimages/jenkins_tutorial/slack_jenkins_install_3.png)

마지막으로 "Setup Instr/uctions" 페이지를 확인할 수 있는데 잠시 켜둔 채 다시 젠킨스로 이동합니다.

### 2-4. Jenkins Slack 연동

Jenkins 관리 > 시스템 설정 > 맨 아래 `Global Slack Notifier Settings`으로 이동합니다.   
Integration Token Credential ID 옆에 `Add` 버튼을 클릭해서 2-3 마지막에 켜둔 Setup Instr/uctions 페이지의 Step 3 Integration Token을 아래와 같이 secret 부분에 입력합니다.

![jenkins_slack_integration_1](/assets/images/usingimages/jenkins_tutorial/jenkins_slack_integration_1.png)

Step 3 Base URL을 Slack compatible app URL에 입력하고 Channel or Slack ID는 젠킨스 빌드 결과를 받기 위해 생선한 slack 채널명을 입력합니다.   
Test Connection 버튼을 클릭했을 때 success가 떨어지면 저장하도록 합니다.

![jenkins_slack_integration_2](/assets/images/usingimages/jenkins_tutorial/jenkins_slack_integration_2.png)

- (+) 여러개의 슬랙 채널을 사용하는 방법 : comma separated(,)를 이용해서 등록 가능합니다.

### 2-5. GitLab 로그인 및 프로젝트 생성

GitLab으로 접속하여 패스워드를 설정해주고 로그인합니다.
- default ID는 root입니다.

![gitlab_login](/assets/images/usingimages/jenkins_tutorial/gitlab_login.png)

"Create a project"를 클릭하여 프로젝트를 새롭게 생성합니다.

![gitlab_create_project](/assets/images/usingimages/jenkins_tutorial/gitlab_create_project.png)

프로젝트 이름 등 정보를 입력하고 "Create project"를 클릭합니다.

![gitlab_new_project](/assets/images/usingimages/jenkins_tutorial/gitlab_new_project.png)

데모 프로젝트는 [GitHub Sample project](https://github.com/gwonsungjun/demo-springboot)를 내려받아서 사용하시면 됩니다.
- 간단하게 hello world를 출력하는 spring boot로 작성된 프로젝트입니다.

데모 프로젝트를 내려받고 아래 명령을 통하여 GitLab에 Push를 합니다.

```shell
cd existing_folder
git init
git remote add origin http://localhost/root/test.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

- 아래와 같이 프로젝트 소스가 정상적으로 올라오면 성공입니다.

![gitlab_project](/assets/images/usingimages/jenkins_tutorial/gitlab_project.png)

### 2-6. GitLab Access Token 생성

Gitlab > User settings > Access Tokens로 이동합니다.   
Name, Expires at, Scopes를 선택 및 입력하고 Create personal access token 클릭하여 상단에 생성된 토큰을 복사합니다.

![gitlab_accesstoken](/assets/images/usingimages/jenkins_tutorial/gitlab_accesstoken.png)

### 2-7. Jenkins Credentials 등록

왼쪽 메뉴에서 Credentials > System > Global credentials > Add Credentials를 클릭해서 2-6에서 복사한 GitLab API token을 등록해줍니다.

![jenkins_credential](/assets/images/usingimages/jenkins_tutorial/jenkins_credential.png)
![jenkins_credential_gitlab](/assets/images/usingimages/jenkins_tutorial/jenkins_credential_gitlab.png)

- Kind : GitLab API token
- Scope : Global
- 방금 위에서 GitLab에서 복사한 Access Token, API token에 붙여넣기
- ID, Description 입력 후 OK


### 2-8. Jenkins GitLab Connection

왼쪽 메뉴 Jenkins 관리 > 시스템 설정 > GitLab에서 GitLab connections를 설정합니다.

![jenkins_gitlab_connect](/assets/images/usingimages/jenkins_tutorial/jenkins_gitlab_connect.png)

- Connection name : 식별할 수 있는 값 입력
- GitLab Host URL
    - `docker inspect gitlab | grep "IPAddress"` 명령을 입력해서 나오는 IP를 입력한다.
    - 도커 컨테이너의 private IP는 유동적으로 바뀌기 때문에 매번 바꿔줘야 하는 불편함이 있다. 따라서 로컬 테스트일 경우만 사용하고 실제 상용 서비스일 경우 gitlab의 도메인 주소를 입력해주면 된다.
- Credentials : 위 2-7에서 생성한 Credentials를 등록
- Test Connection > Success 확인되면 SAVE

## 마무리

이번 시간에는 젠킨스와 깃랩의 기본 설정을 해보았습니다.   
젠킨스와 깃랩을 처음 접하시는 분들은 조금 복잡해 보일 수도 있다고 생각합니다. 궁금한 점이 있으면 언제나 댓글에 질문을 남겨주세요.   
다음 시간에는 배포할 서버(AWS EC2)를 생성해보도록 하겠습니다.
감사합니다.

