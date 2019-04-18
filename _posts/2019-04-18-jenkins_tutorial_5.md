---
title: '5) Jenkins Tutorial - Registering Jenkins items using pipeline'  
layout: post  
tags : jenkins
category: jenkins
subtitle:  Jenkins pipeline을 이용해 아이템 등록 하기
author : sungjun
---

**Jenkins pipeline을 이용해 아이템 등록 하기.** 

---

이번 시간에는 Jenkins Web UI가 아닌 Jenkins pipeline을 이용해서 item을 등록해보도록 하겠습니다.   
전체적인 흐름은 저번 시간과 같습니다. 참고해주시기 바랍니다.

![jenkins_flow](/assets/images/usingimages/jenkins_tutorial/jenkins_flow.png)

## Contents

1. [Installing Jenkins and GitLab with Docker](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)
2. [Jenkins, GitLab initial setting](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_2)
3. [Create AWS EC2 Instance](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_3)
4. [Registering Jenkins items using web ui](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_4)
5. [Registering Jenkins items using pipeline](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_5)

## 5. Registering Jenkins items using pipeline

### 5-1. SSH Agent Plugin 설치

Jenkins 관리 > 플러그인 관리로 이동해서 아래와 같이 `SSH Agent` 플러그인을 설치합니다.
- '지금 다운로드하고 재시작 후 설치하기' > '설치가 끝나고 실행 중인 작업이 없으면 Jenkins 재시작' 버튼 체크

![jenkins_ssh_agent](/assets/images/usingimages/jenkins_tutorial/jenkins_ssh_agent.png)

### 5-2. 새로운 Item 등록

Jenkins > 새로운 Item을 클릭하고 아래와 같이 `item 이름` 지정, `Pipeline`를 선택한 뒤 `OK` 버튼을 누릅니다.

![jenkins_new_item_pipeline](/assets/images/usingimages/jenkins_tutorial/jenkins_new_item_pipeline.png)

### 5-3. Pipeline 설정

item 설정 화면 맨 밑 `Pipeline` 부분부터 설정해보겠습니다.

- `Definition` : Pipeline script from SCM 선택
- `SCM` : Git 선택
- `Repository URL` : GitLab project http URL을 입력합니다.
    - 현재는 gitlab URL을 docker private ip로 등록해야 합니다. (이전 편에서 말씀드린 것 처럼 실제 상용에서는 이렇게 사용하시면 안됩니다!)
    - gitlab.example.com 부분을 `docker inspect gitlab | grep "IPAddress"` 명령을 통해 나오는 IP로 바꿔줍니다.
- `Credentials`
    - 혹시 [4. Registering Jenkins items using web ui](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_4)에서 등록하신분들은 이전에 등록한 Credentials를 선택해줍니다.
    - 등록 안하신 분들은 아래의 순서로 등록해줍니다.
        - (1) Add 버튼 클릭 > jenkins 선택
        - (2) Credentials 생성
            - Domain : Global credentials
            - Kind : Username with password
            - Scope : Global
            - Username : gitlab id
            - password : gitlab password
            - ID : 젠킨스 내 식별 ID
            - Description : Credentials 부연 설명
        - (3) 생성한 Credentials 선택 > Error 없어지는 것을 확인합니다.
- `Branch Specifier (blank for 'any')` : `*/**` 입력 (모든 브랜치로부터 event를 받게됩니다.)
- `Repository browser` : 자동
- `Script Path` : `Jenkinsfile.groovy` 입력 `(.groovy를 입력해주셔야 합니다!)`
- `Lightweight checkout` : 체크 해제

![jenkins_pipeline_setting](/assets/images/usingimages/jenkins_tutorial/jenkins_pipeline_setting.png)

### 5-4. General 설정

General에는 해당 item을 간단하게 설명해줍니다.   
그리고 `이 빌드는 매개변수가 있습니다`를 체크해줍니다.

![jenkins_pipeline_general](/assets/images/usingimages/jenkins_tutorial/jenkins_pipeline_general.png)

### 5-5. 매개변수 추가

`매개변수 추가` 버튼을 눌러 아래와 같이 3개의 `String Parameter`를 추가해줍니다.   
프로젝트별 상이한 정보들을 매개변수로 추출해서 등록하는 과정입니다.

- `GIT_URL` : 깃 저장소 주소
    - 위 5-3에서 등록했던 git 주소를 똑같이 입력합니다.
- `CREDENTIALIS_ID` : 바로 위 Username with password로 등록한 credentials의 `ID`를 입력합니다.
- `SLACK_CHANNEL` : 슬랙 채널명을 입력합니다.

![jenkins_pipeline_parameter](/assets/images/usingimages/jenkins_tutorial/jenkins_pipeline_parameter.png)

### 5-6. Build Triggers 설정

빌드 유발은 `Build when a change is pushed to GitLab` 을 선택해줍니다.      
그리고 고급 버튼을 눌러 Secret token의 `Generate 버튼`을 클릭합니다.

![jenkins_build_trigger](/assets/images/usingimages/jenkins_tutorial/jenkins_build_trigger.png)

잠깐 GitLab에 접속해서 `Admin area > settings > network > Outbound requests`로 이동하여 `Allow request to the local network from hooks and services`를 체크해준 뒤 저장합니다.
- 이 설정을 해주지 않으면 아래 Integrations 설정 시 "Requests to the local network are not allowed" 에러가 발생합니다. (로컬 테스트 시만 체크하시고 상용환경에서는 생략하시고 넘어가면 됩니다.)

![gitlab_outbound](/assets/images/usingimages/jenkins_tutorial/gitlab_outbound.png)

다음, Gitlab 해당 Project > settings > Integrations로 이동하겠습니다.   
그리고 URL은 위에서 체크했던 `Build when a change is pushed to GitLab. GitLab webhook URL:` 부분의 url 부분만 복사해서 넣습니다.
- url에서 localhost 부분은 `docker inspect jenkins | grep "IPAddress"`를 입력해서 나오는 IP로 바꿔줍니다.
secret token 역시 젠킨스에서 생성한 token을 입력합니다.    
적절한 Trigger를 선택해주면 되는데 현재는 Push events만 선택하겠습니다.

![gitlab_integrations](/assets/images/usingimages/jenkins_tutorial/gitlab_integrations.png)

여기까지가 item 등록 끝이고 이대로 저장을 하시면 pipeline item 등록이 완료됩니다.

### 5-7. Jenkinsfile.groovy 파일 생성

먼저 아래와 같이 demo 프로젝트 루트에 Jenkinsfile.groovy 파일을 생성해줍니다.

![intellij_jenkinsfile](/assets/images/usingimages/jenkins_tutorial/intellij_jenkinsfile.png)

### 5-8. (+) 젠킨스 파이프라인 문법
- 젠킨스 파이프라인의 경우 2가지 문법을 지원합니다.
    - Scripted : Groovy 문법을 사용하고 Declarative 문법보다 더 유연하게 사용 가능.
    - Declarative : 간단하지만 고정된 방식.
- 저희는 Scripted 문법을 사용해서 Jenkinsfile을 작성할 것입니다. 따라서, Groovy SDK를 설치하고 intellij에 설정하는 것을 권합니다.
- 파이프라인 문법에 대해 조금 더 상세한 내용은 [젠킨스 파이프라인 정리 - 2. Scripted 문법 소개](https://jojoldu.tistory.com/356?category=777282)를 참조해주시기 바랍니다.

MacOS 기준 groovy sdk 설치 및 intellij 설정법입니다.

```shell
$ brew install groovysdk
$ export GROOVY_HOME=/usr/local/opt/groovy/libexec
Point IntelliJ to the installed directory, e.g.:/usr/local/Cellar/groovysdk/2.4.7/libexec
```

### 5-9. SSH Agent 설정

Remote Server SSH Private key를 등록합니다.   
먼저, 이전에 생성해둔 AWS EC2 Instance 키페어가 저장된 위치로 이동합니다.
아래와 같이 cat 명령을 통해 pem file을 출력하고 출력된 문자열을 복사합니다.
- 복사하실 때는 —–BEGIN RSA PRIVATE KEY—–부터 —–END RSA PRIVATE KEY—–까지 모두 복사해야 합니다.

![cat_pem](/assets/images/usingimages/jenkins_tutorial/cat_pem.png)

왼쪽 메뉴에서 Credentials > System > Global credentials > Add Credentials를 클릭해서 복사한 pem file을 등록해줍니다.   

- `Kind` : SSH Username with private key
- `Scope` : Global
- `ID` : 젠킨스 내 식별 ID (잘 기억해둡니다! jenkinsfile 작성 시 사용할 것 입니다.)
- `Description` : 부연 설명
- `Username` : ssh 접속 아이디
- `private key > Enter directly > key` : 복사한 pem file 문자열 붙여넣기 

![jenkins_pipeline_credentials](/assets/images/usingimages/jenkins_tutorial/jenkins_pipeline_credentials.png)

### 5-10. Jenkinsfile.groovy 작성

5-7에서 생성한 groovy 파일에 아래 스크립트를 작성해줍니다.   
각 단계별로 간단하게 설명을 써놨는데 혹시 이해안가거나 부족한 부분이 있으면 질문 부탁드립니다!

```groovy
node {
    def scmVars = checkout scm
    def values = scmVars.GIT_BRANCH.tokenize('/')
    String branchName = values[1] // Gitlab으로 Push한 branch의 이름을 얻어 올 수 있습니다.

    try {
        gitCheckout(branchName)
        srcBuild()
        unitTest()
        deploy()
        notifySlack('SUCCESS')
    } catch (env) {
        notifySlack('FAIL')
        throw env
    }
}

// gitlab으로 부터 Source를 checkout 해옵니다.
// {CREDENTIALIS_ID}, ${GIT_URL}은 jenkins item 등록 시 추가해줬던 매개변수들이 대입됩니다.
def gitCheckout(String branch) {
    stage('Git Checkout') {
        echo 'Checkout ' + branch
        git branch: branch, credentialsId: "${CREDENTIALIS_ID}", url: "${GIT_URL}"
    }
}

// build를 진행합니다.
def srcBuild() {
    stage('Build') {
        sh "./gradlew clean build"
    }
}

// 단위 테스트를 진행합니다.
def unitTest() {
    stage('Unit tests') {
        sh "./gradlew test"
        if (currentBuild.result == "UNSTABLE") {
            notifySlack('UNSTABLE')
            sh "exit 1"
        }
    }
}

// SSH Agent 플러그인을 통해 배포를 진행합니다.
// credentials는 5-9에서 설정한 ID를 넣어줍니다.
def deploy() {
    stage('Deploy') {
        sshagent(credentials: ['aws-ec2-key']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@15.164.104.228 uptime'
            sh 'scp -r build/libs/*.jar ubuntu@15.164.104.228:/home/ubuntu/demo'
            sh 'ssh ubuntu@15.164.104.228 "sh /home/ubuntu/start.sh"'
        }
    }
}

// 슬랙으로 노티를 보낼때 사용되는 함수입니다.
def notifySlack(String buildStatus) {

    def color

    if (buildStatus == 'SUCCESS') {
        color = '#1a9367'
    } else if (buildStatus == 'UNSTABLE') {
        color = '#FFFE89'
    } else {
        color = '#ff0000'
    }

    def msg = "${buildStatus}: `${env.JOB_NAME}` #${env.BUILD_NUMBER}:\n${env.BUILD_URL}"
    slackSend(channel: "${SLACK_CHANNEL}", color: color, message: msg)
}
```

### 5-11. Deploy server(aws ec2) 설정

잠깐 배포할 서버인 aws ec2로 ssh 접속해서 /home/ubuntu 밑에 `테스트 sh 파일`과 `demo 디렉토리`만 생성하겠습니다.
- sh 파일은 `echo "test"`만 작성한 의미 없는 shell 파일입니다.
- 이번 시간에는 jenkins에서 빌드 후 결과물이 배포할 서버에 제대로 delivery 되는지만 체크하기 위해 의미 없는 값을 넣었습니다.
- 실제 상용에서는 실행 중인 프로세스를 멈추고 새 빌드 결과물로 기동을 하는 등의 스크립트 파일을 넣어 줄 수 있을 것입니다.

![ec2_mkdir](/assets/images/usingimages/jenkins_tutorial/ec2_mkdir.png)

### 5-11. Git Push

Jenkinsfile.goorvy 파일이 생성 및 작성된 상태로 gitlab으로 push하면 jenkins에서 자동으로 빌드를 수행하는 것을 확인할 수 있습니다.
- (테스트하느라 빌드 번호가 6번인데 원래는 1번일 것입니다!)

![jenkins_pipeline_build_ing](/assets/images/usingimages/jenkins_tutorial/jenkins_pipeline_build_ing.png)

### 5-13. Result

빌드가 성공하고 해당 인스턴스로 ssh 접속해보면 빌드 결과물인 jar 파일이 있는 것을 확인 하실 수 있습니다.   

![build_result_file_pipeline](/assets/images/usingimages/jenkins_tutorial/build_result_file_pipeline.png)

또한, 슬랙으로 성공 메시지가 전송될 것 입니다.

## 마무리

이번 시간에는 파이프라인 스크립트를 이용해서 젠킨스 item을 등록해보았습니다.   
Web UI를 통해 item을 등록하는 방식에 비해 훨씬 간단하지 않으셨나요?   
이렇게 공통 매개 변수를 빼고 동일한 Jenkinsfile.groovy 파일을 사용한다면 수정 및 추가 사항이 있을 때 UI를 이용해 등록하는 방식보다 훨씬 간단할 것입니다.   
또한, 최근에는 인프라에 관한 설정들이 소스 코드 단으로 내려오는 추세이기도 하고 젠킨스에서도 pipeline을 이용한 방식을 추천하고 있기 때문에 파이프라인 사용을 적극 추천드립니다.   
Jenkinsfile.groovy 파일은 각자의 요구사항에 맞게끔 수정해서 사용하면 될 것 같은데 소스에 대한 설명이 조금 빈약해서 혹시 궁금하신 점이 있으면 댓글 남겨주시기 바랍니다.   
지금까지 총 5편의 Jenkins tutorial을 작성해보았는데 제가 한 방식이 정답은 아니라고 생각합니다. 더 좋고 유연한 방식이 많을 것인데 그중 하나라고 봐주시면 좋을 것 같습니다.   
읽어주셔서 감사합니다 :)






