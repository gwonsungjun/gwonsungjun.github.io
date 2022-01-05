---
title: '4) Jenkins Tutorial - Registering Jenkins items using web ui'  
layout: post  
tags : jenkins
category: jenkins
subtitle:  Jenkins UI를 이용해 아이템 등록 하기
author : sungjun
---

Jenkins UI를 이용해 아이템 등록 하기 

---

이번 시간에는 Jenkins Web UI를 이용해서 실제 item을 등록해보도록 하겠습니다.   
전체적인 흐름은 다음과 같습니다. 참고해주시기 바랍니다.

![jenkins_flow](/assets/images/usingimages/jenkins_tutorial/jenkins_flow.png)


## Contents

1. [Installing Jenkins and GitLab with Docker](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_1)
2. [Jenkins, GitLab initial setting](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_2)
3. [Create AWS EC2 Instance](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_3)
4. [Registering Jenkins items using web ui](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_4)
5. [Registering Jenkins items using pipeline](https://gwonsungjun.github.io/articles/2019-04/jenkins_tutorial_5)

## 4. Registering Jenkins items using web ui

### 4-1. Publish Over SSH Plugin 설치

Jenkins 관리 > 플러그인 관리로 이동해서 아래와 같이 `Publish Over SSH` 플러그인을 설치합니다.
- '지금 다운로드하고 재시작 후 설치하기' > '설치가 끝나고 실행중인 작업이 없으면 Jenkins 재시작' 버튼 체크

![jenkins_publish-over-ssh](/assets/images/usingimages/jenkins_tutorial/jenkins_publish-over-ssh.png)

### 4-2. Jenkins Publish Over SSH 설정

먼저, 이전에 생성해둔 AWS EC2 Instance 키페어가 저장된 위치로 이동합니다.       
아래와 같이 cat 명령을 통해 pem file을 출력하고 출력된 문자열을 복사합니다.
- 복사하실 때는 -----BEGIN RSA PRIVATE KEY-----부터 -----END RSA PRIVATE KEY-----까지 모두 복사해야 합니다. 

![cat_pem](/assets/images/usingimages/jenkins_tutorial/cat_pem.png)

다음, Jenkins 관리 > 시스템 설정 > Publish Over SSH 항목으로 이동합니다.     
Key에는 방금 복사한 문자열(pem file)을 붙여넣고 `추가` 버튼을 눌러 아래와 같이 자신에게 맞는 정보를 입력합니다.
- 실제 상용에서는 지금처럼 사용하면 키값을 그대로 노출할 수 있어 많이 취약한 방법입니다.
- 해당 키를 적절한 디렉토리(/var/lib/jenkins/.ssh/)에 이동시키고 path 설정을 통해 키를 읽도록 하는 것이 가장 안전합니다.

![jenkins-over-ssh-setting](/assets/images/usingimages/jenkins_tutorial/jenkins-over-ssh-setting.png)

- Name: Job에서 표시될 이름 지정
- Hostname: IP Address
- Username: SSH 접근 계정
- Remote Directory: 업로드될 디렉토리 (여러개가 있다면 상위만 지정)

Test Configuration을 눌러 `Success`가 떨어지면 저장버튼을 눌러 적용시킵니다.

### 4-3. 새로운 Item 등록

Jenkins > 새로운 Item을 클릭하고 아래와 같이 `item 이름` 지정, `Freestyle project`를 선택한 뒤 `OK` 버튼을 누릅니다.

![jenkins_new_item](/assets/images/usingimages/jenkins_tutorial/jenkins_new_item.png)

### 4-4. General 설정
 
General에는 해당 item을 간단하게 설명해줍니다.

![jenkins_general](/assets/images/usingimages/jenkins_tutorial/jenkins_general.png)

### 4-5. 소스 코드 관리 설정

소스 코드 관리는 `Git`을 선택해줍니다.      
그리고 Credentials 셀렉트박스 옆에 `Add` 버튼을 클릭하고 `jenkins`를 선택합니다.      
아래와 같이 Kind는 `Username with password`를 선택하고 Username과 Password에 gitlab 아이디 패스워드를 입력합니다.      
ID는 Jenkins 내에서 식별할 아이디, Description은 해당 ID에 대한 설명을 입력해줍니다.

![jenkins_add_credential](/assets/images/usingimages/jenkins_tutorial/jenkins_add_credential.png)

Credentials를 추가하였다면 해당 Credentials를 선택해주고 `Reposiroty URL`에는 gitlab 프로젝트 주소를 입력합니다.   
현재는 gitlab URL을 docker private ip로 등록해야 합니다. (실제 상용에서는 이렇게 사용하시면 안됩니다!)
- 2편에서 보셨던것 처럼 `docker inspect gitlab | grep "IPAddress"` 명령을 통해 나오는 IP를 입력합니다.
- 프로젝트 URL의 gitlab.example.com 부분을 172.18.0.2와 같은 Docker private IP로 변경해줍니다.
또한, 현재 브랜치는 master 브랜치 밖에 없기 때문에 기본 설정(*/master) 그대로 유지합니다. (여러 브랜치에서의 설정은 나중에 설명드리도록 하겠습니다.)

![jenkins_source_code](/assets/images/usingimages/jenkins_tutorial/jenkins_source_code.png)

### 4-6. 빌드 유발 설정

빌드 유발은 `Build when a change is pushed to GitLab` 을 선택해줍니다.      
그리고 고급 버튼을 눌러 Secret token의 `Generate 버튼`을 클릭합니다.

![jenkins_build_trigger](/assets/images/usingimages/jenkins_tutorial/jenkins_build_trigger.png)

잠깐 GitLab에 접속해서 `Admin area > settings > network > Outbound requests`로 이동하여 `Allow request to the local network from hooks and services`를 체크해준 뒤 저장합니다.
- 이 설정을 해주지 않으면 아래 Integrations 설정 시 "Requests to the local network are not allowed" 에러가 발생합니다. (로컬 테스트 시만 체크하시고 상용환경에서는 생략하시고 넘어가면 됩니다.)

![gitlab_outbound](/assets/images/usingimages/jenkins_tutorial/gitlab_outbound.png)

다음, Gitlab 해당 Project > settings > Integrations로 이동하겠습니다.   
그리고 URL은 위에서 체크했던 `Build when a change is pushed to GitLab. GitLab webhook URL:` 부분의 url 부분만 복사해서 넣습니다.
- url에서 localhost 부분은 `docker inspect jenkins | grep "IPAddress"`를 입력해서 나오는 IP로 바꿔줍니다. (매번 수정해 넣기 불편하긴 하네요. 테스트하기 쉽게 젠킨스와 깃랩을 로컬에서 도커로 띄워서 어쩔 수 없을 것 같습니다. 😥)
secret token 역시 젠킨스에서 생성한 token을 입력합니다.    
적절한 Trigger를 선택해주면 되는데 현재는 Push events만 선택하겠습니다.

![gitlab_integrations](/assets/images/usingimages/jenkins_tutorial/gitlab_integrations.png)

### 4-7. 빌드 환경 설정

다시 젠킨스로 돌아와 빌드 환경의 `Send files or execute commands over SSH after the build runs`를 선택합니다.

- Name: Jenkins 시스템 설정에서 등록한 SSH 서버 중 배포할 서버를 선택합니다.
- Sources files	: jar 혹은 war가 빌드된 위치를 적습니다. ex) build/libs/*.jar
- Remove prefix	: 파일 앞부분에 경로부분을 적습니다. ex) build/libs
- Remote directory : 업로드될 경로입니다. 
    - 주의할 것은 위의 4-2 Publish Over SSH 설정에서 Remote Directory 내의 디렉토리를 적어야 합니다.
    - 예를들어, 최종 경로가 /hom/ubuntu/demo 라면 => 이미 위에서 /home/ubuntu를 적었기에 여기에는 demo 만 적어야 합니다.
- Exec command : 실행할 명령어를 적으면 됩니다. ex) sh start.sh

![jenkins_build_env](/assets/images/usingimages/jenkins_tutorial/jenkins_build_env.png)

### 4-8. 빌드 설정

빌드는 Add build step > `Execute shell`을 선택하고 command에 `./gradlew build`를 넣어줍니다.

![jenkins_build](/assets/images/usingimages/jenkins_tutorial/jenkins_build.png)

### 4-9. 빌드 후 조치 설정

빌드 결과를 슬랙으로 전송받기 위해 마지막으로 빌드 후 조치 추가 > `Slack notifications`를 선택해 설정을 해줍니다.

![slack_notify](/assets/images/usingimages/jenkins_tutorial/slack_notify.png)

슬랙 Noti 설정까지 끝내고 저장을 하시면 드디어 하나의 item 등록이 완료 되었습니다.

### 4-10. Deploy server(aws ec2) 설정

잠깐 배포할 서버인 aws ec2로 ssh 접속해서 /home/ubuntu 밑에 `테스트 sh 파일`과 `demo 디렉토리`만 생성하겠습니다.
- sh 파일은 `echo "test"`만 작성한 의미 없는 shell 파일입니다.
- 이번 시간에는 jenkins에서 빌드 후 결과물이 배포할 서버에 제대로 delivery 되는지만 체크하기 위해 의미 없는 값을 넣었습니다.
- 실제 상용에서는 실행 중인 프로세스를 멈추고 새 빌드 결과물로 기동을 하는 등의 스크립트 파일을 넣어 줄 수 있을 것입니다.

![ec2_mkdir](/assets/images/usingimages/jenkins_tutorial/ec2_mkdir.png)

### 4-11. Git Push

소스를 조금 수정하고 push하면 jenkins에서 자동으로 빌드를 수행하는 것을 확인할 수 있습니다.
- (여러 번 테스트하느라 빌드 번호가 12번인데 원래는 1번일 것입니다!)

![jenkins_build_ing](/assets/images/usingimages/jenkins_tutorial/jenkins_build_ing.png)

### 4-12. Result

빌드가 성공하고 해당 인스턴스로 ssh 접속해보면 빌드 결과물인 jar 파일이 있는 것을 확인 하실 수 있습니다.   

![build_result_file](/assets/images/usingimages/jenkins_tutorial/build_result_file.png)

또한, 슬랙으로 아래와 같이 성공 메시지가 전송될 것 입니다.

![success_slack_message](/assets/images/usingimages/jenkins_tutorial/success_slack_message.png) 

## 마무리

이번 시간에는 실제 젠킨스 item을 등록해보았습니다.   
실제 item을 등록해보니 어떤 신가요? 사용하는데 번거롭거나 불편하진 않으셨나요?               
Web UI를 통해 item을 등록하는 방식은 사용자가 배포할 서버를 추가하거나 빌드 스크립트에 수정 사항이 있는 등 변경 사항이 있을 때 마다 웹에 접속하여 일일이 바꿔줘야 합니다. 여러 item이 공통적인 내용을 담고 있다면 더 곤란해질 것입니다.   
이를 해결하고자 jenkins pipeline을 사용해볼 것 인데요. 자세한 것은 다음 시간에 소개하도록 하겠습니다.   
감사합니다. 궁금한 점이 있으시거나 잘못된 점이 있다면 댓글 남겨 주시기 바랍니다. 

## Links
- <https://hreeman.tistory.com/m/136>
- <https://yookeun.github.io/tools/2018/04/14/jenkins-remote/>






