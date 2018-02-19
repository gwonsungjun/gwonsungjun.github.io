---
title: 'Jekyll, Google Analytics 사용법'  
layout: post  
tags :  
- Google Analytics
category: jekyll
---

- Jekyll, Google Analytics

---

### Google Analytics

지킬 블로그를 사용하며, 사용자들이 내 블로그를 방문하는지? 방문한다면 어떤 경로로 방문하는지 등에 대해서 궁금했다. 그래서 방법을 찾던 중 구글 애널리틱스(Google Analytics)를 알게 되었다. 블로그를 운영한다면 웹 분석은 필수(?)인데 많은 웹 분석 서비스가 존재하지만 나는 구글 애널리틱스를 선택하였다. 어렵지 않은 작업이기 때문에 간단하게 작성을 하였다.

### 1. Google analytics 가입
google 계정이 있다면 사용가능하다.

![Google analytics1](/assets/images/usingimages/GoogleAnalytics/1.png)

### 2. 아래와 같이 작성 한다.

![Google analytics2](/assets/images/usingimages/GoogleAnalytics/2.png)

### 3. 거주 국가 설정 후 서비스 약관에 동의

![Google analytics3](/assets/images/usingimages/GoogleAnalytics/3.png)

### 4. 가입 완료 후 관리 -> 추적 정보 -> 추적코드에서 추적ID 확인.

![Google analytics4](/assets/images/usingimages/GoogleAnalytics/4.png)

### 5. 자신의 Jekyll 프로젝트에서 config.yml파일의 Google analytics 부분에 방금 전에 확인한 추적ID를 입력, 그 후 변경사항 push.

![Google analytics5](/assets/images/usingimages/GoogleAnalytics/5.png)

### 6. Google analytics 접속해서 확인.

![Google analytics6](/assets/images/usingimages/GoogleAnalytics/6.png)

- 이제부터 해당 블로그에 접속한 사용자들을 분석할 수 있게 되었다.   
- 다음번에는 Google Search Console 설정과 Google Analytics와의 연동 방법을 포스팅할 것이다.

### Link
<https://rextarx.github.io/jekyll/2017/02/03/Applying_Google_Analytics_to_a_blog_using_Jekyll/>
