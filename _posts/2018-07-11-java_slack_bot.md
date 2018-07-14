---
title: 'JAVA - SimpleSlackAPI를 활용하여 간단한 슬랙 봇 메시지 보내기'  
layout: post  
tags :  
- java
- 슬랙봇
- slack bot
category: java
---

**SimpleSlackAPI를 활용하여 간단한 슬랙 봇 메시지 보내기, OAuth Access Tocken 생성부터 JAVA Test Code까지**{: style="display:inherit;text-align:center;"}

---

## Intro

- 사내 배치 프로그램 문자 메세지 전송이 부담되어 Slack 메세지 전송으로 부분적 대체하기 위해 테스트를 해보았다.
- 더 좋은 방법이 많지만 가장 쉽게 테스트할 수 있는 방법이라 생각하여 [SimpleSlackAPI](https://github.com/Ullink/simple-slack-api)를 선택하게 되었다.

## Get Slack OAuth Access Tocken 

- [Slack API](https://api.slack.com/) 사이트에서 `Start Building`

![slackbot1](/assets/images/usingimages/slackbot/slackbot1.PNG)

- Create a Slack App 모달 창이 바로 뜨면 App name & Workspace 선택 후 `Create App`
- 모달 창이 바로 뜨지 않으면 Create an App 클릭 후 App 생성

![slackbot2](/assets/images/usingimages/slackbot/slackbot2.PNG)

 - Bot Users 메뉴 선택 후 Displayname, Default username 입력 후 `Add Bot User`

 ![slackbot3](/assets/images/usingimages/slackbot/slackbot3.PNG)

 - OAuth & Permissions 메뉴 선택 후 `Install App to Workspace(team)` 클릭 -> `Authorize`

 ![slackbot4](/assets/images/usingimages/slackbot/slackbot4.PNG)

 - `생성된 Token(Bot User OAuth Access Token)`을 확인하고 복사해두기

 ![slackbot5](/assets/images/usingimages/slackbot/slackbot5.PNG)

## JAVA Code Test

- Token 값은 공개저장소에 노출되지 않도록 주의.
- JAVA Client API는 여러 가지가 있지만 간단한 메세지만 전송하면 되므로 [SimpleSlackAPI](https://github.com/Ullink/simple-slack-api)를 사용하여 테스트를 진행하였다.
- Maven, Gradle을 이용하고 있다면 [MVN Repository : Simple Slack API >> 1.2.0](https://mvnrepository.com/artifact/com.ullink.slack/simpleslackapi/1.2.0) 확인 후 설정을 해준다.


```java
@Test
public void sendMsgTest() {

    SlackSession session = SlackSessionFactory.createWebSocketSlackSession("slack-bot-auth-token");
    try {
        session.connect();
    } catch (IOException e) {
        e.printStackTrace();
    }
    SlackChannel channel = session.findChannelByName("channel-name");
    session.sendMessage(channel, "message" );

 }
```
- 결과
![slackbot6](/assets/images/usingimages/slackbot/slackbot6.PNG)

 ### Links

 - [Slack API](https://api.slack.com/community)
 - [Django - Slacker를 활용하여 간단하게 슬랙 봇 메시지 보내기](https://wayhome25.github.io/django/2017/09/03/django-slack-bot/)
 - [Slack 업무용 메신제 JAVA API](http://ddakker.tistory.com/329)