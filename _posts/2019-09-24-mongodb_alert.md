---
title: 'MongoDB, ALERT: Query Targeting: Scanned Objects / Returned has gone above 1000 해결'  
layout: post  
tags : mongodb
category: mongodb
subtitle:  MongoDb Scanned Objects ALERT을 해결해보자
author : sungjun
---

4월 이후에 정말 오랜만에 블로깅을 한다.   
그동안 많은 변화가 있어서 적응하느라 블로깅을 못 했는데(변명이지만...) 이제 좀 적응되어 그동안 쌓아왔던 이슈들을 하나씩 풀어보려 한다.
   
현재 MongoDB 클라우드 매니지드 서비스인 Atlas를 Production 환경에서 사용 중이다.
   
몽고디비를 접한 시기도 얼마 되지 않았고 실제 프로덕션 환경에서의 운영을 처음 해보기 때문에 간단한 Alrert이 mail로 오면 꽤 당황하곤 한다. 익숙함의 문제보다 아직 모르는 게 많아서 그런 거 같다.
   
이번 포스팅에서 소개할 이슈는 아래와 같다.
   
`ALERT : Query Targeting: Scanned Objects / Returned has gone above 1000`
   
먼저 `Scanned Objects`이 무엇을 의미하는지부터 찾아보았다.
   
Atlas Document에 자세히 나와 있지만 간단하게 설명하면 Query Targeting Metric은 2개의 information을 보여주고 있다.
- scanned / returned: 실제 리턴 된 문서 수와 비교하여 쿼리를 수행하기 위해 검사 된 인덱스 키 수 간의 비율로 사용자 정의 임계 값을 초과하거나 초과하는 경우 발생
    - 기본적으로 활성화되어 있지 않음
- Scanned Objects / Returned: 반환된 실제 문서 수와 비교하여 쿼리를 수행하기 위해 검사한 문서 수간의 비율로 사용자 정의 임계 값을 초과하거나 초과하는 경우 발생
    - 기본 임계 값은 1000. 즉, 쿼리 하나에 1000개 이상의 문서를 스캔한 경우 경고를 알림
   
무슨 말이지? 고민하며 경고 메일 본문을 다시 읽어보는데 힌트가 눈에 들어왔다. 
   
`which typically suggests that un-indexed queries are being run`: index가 안 걸려 있구나.
   
근데 해당 mail이 전송되고 `Scanned Objects` value가 다시 1000 이하로 내려가면 해당 이슈가 자동적으로 close 되면서 어떤 collections에서 문제가 발생 중인지 확인하기가 어려웠다.
   
다행히 최근 들어 많이 사용되는 collection이 있었는데 인덱스가 걸려있지 않은 것을 확인하였고 mongoose로 schema를 생성해 사용 중이었기 때문에 `indxe:true` 옵션을 추가 및 배포하여 해결하였다.
   
소스 단에서 mongoose 등을 이용해 스키마를 생성하지 않았다면 직접 shell 또는 gui (mogodb compass)에 접속하여 index를 생성하면 해결될 것이다.
   
문제 해결 후 Qury Targeting Metric을 확인해보면 문제가 되는 지점에서는 1k까지 치솟았었는데 index 설정 후 확 떨어지는 것을 확인할 수 있다.
   
![atlas-query-targeting-metric](/assets/images/usingimages/atlas-query-targeting-metric.png)
      
참고로 index 설정 시 주의할 점
- 모든 도큐먼트를 스캔해야 되는 비효율성을 줄여 주지만  write 및 update operation을 성능을 떨어뜨리므로 읽기 위주인 경우에만 유용

### Link
- [https://docs.atlas.mongodb.com/reference/alert-resolutions/query-targeting/](https://docs.atlas.mongodb.com/reference/alert-resolutions/query-targeting/)
