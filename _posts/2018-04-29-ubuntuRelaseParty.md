---
title: 'Ubuntu 18.04 Release Party 후기'  
layout: post  
tags : ubuntu, seminar
category: seminar
subtitle: Ubuntu 18.04 Release Party Review :)
author : sungjun
---

**Ubuntu 18.04 Release Party Review :)**{: style="display:inherit;text-align:center;"}

---
# Ubuntu 18.04 Release Party @Seoul 2018.04.28 (DCAMP)    


# Intro
Ubuntu 18.04가 Release 되었고 마침 Ubuntu 18.04 Release Party를 진행한다는 소식을 접하고 18.04에서는 어떤 변화가 생겼는지 정보를 얻기 위해 참여하게 되었다.

## 1. 미세먼지 같은 우분투 서버활용 팁 - 배준현님(코무합니다만)

### Agenda
- SELinux
- Cron
- Nohup
- Tmux or screen == BYOBU

### SELinux
- the debian administrator's handbook
- Getsebool -a
- Setsebool

### Cron
- airbnb airflow 등의 cron을 대체 할 프로젝트가 많음
- 36 2 * * 7 root /usr/local/sbin/backup.sh
  - 36 : 분
  - 2 : 시
  - 첫번째 * : 일
  - 두번째 * : 월
  - 7 : 요일
  - root : 권한
  - /usr/local/sbin/backup.sh : 아래를 실행
![cron](/assets/images/usingimages/ubuntu/cron.png)

- crontab
  - crontab을 통해 스케줄 설정이 가능
  - crontab -l
  - crontab -e
  - crontab -r
  - /var/spool/cron/USER_ID
  - /var/spool/mail/USER_ID : crontab이 돌고 나면 자신한테 메일 발송 (어떤 세팅이고, 어떤 프로시져, 기록 등을 발송)

### nohup
- Q. TTY에서 프로시저(파이썬)를 실행 하고 TTY를 끄면 프로시저가 멈춰요.
    - A. Nohup을 쓰세요.
- 세션유지
- 기본적으로 nohup.out log 생성됨
- cron에는 nohup 기능이 포함되어 있다. (run level이 다르다.)

### Tmux || screen == BYOBU
- 우분투 데스크탑 내장 BYOBU가 있음
- 보통 tmux 에서 screen 세팅을 하지만 조금 더 칼라풀하게 보여주는 BYOBU도 괜찮다.
- **AWS 인스턴스 활성화된 시간 확인 가능.**
![byobu](/assets/images/usingimages/ubuntu/byobu_screen.jpg)

## 2. What's new in Ubuntu 18.04 LTS - 한영빈님(우분투한국커뮤니티 대표)
 - LTS(Long term support) 2년에 한번 출시 (짝수년도 4월)
 - LTS - 새로운 것 보다는, 안정화 목적
 - LTS는 총 5년정도 지원받음, 3년 메인 + 2년은 보안 업데이트 정도. (17.01 부터는 거의 9개월 지원하고 있음.)

### 최소 설치
 - 새로 추가된 설치 화면
 - 최소한의 application, office & 기본 game 설치되지 않음.
 - 오피스, 영상 player 등이 없기 때문에 설치 해야 함.

### GNOME as Default
- GNOME 2.x
- Unity
- GNOME 3.x

**GNOME을 다시 쓰게된 배경**
  - GNOME 3.x가 출시되면서 GNOME의 UI가 확 바뀜.
    - 그래서 GNOME대신 Unity를 기본으로 탑재하기 시작(11.04)
  - 잘나가다가,,, 차세대 버전 Unity 버전(8) 개발 시작
    - 잘 개발하다가 디스플레이 서버를 wayland가 아닌 자체적으로 Mir 사용
    - 전환의 시작
    - 결국 프로젝트 실패
  - 다시 GNOME을 기본 데스크톱 환경으로

### 라이브패치 서비스 설정
- 중요 커널 보안 업데이트 후 재부팅 없이 적용 가능하도록

### 시스템 정보 수집
- 개인정보수집 x, 시스템 정보 (하드웨어- cpu, ram, disk ...)
- 해당 기능 끌 수 있음

### wayland가 아닌 X.Org를 기본으로 사용
- 로그인할 때 톱니바퀴 누르면 세션을 고를 수 있음.
- skype, webRTC, Google hangouts 등의 화면 공유 프로그램은 아직 X.Org에서 더 잘 돌아감.
- 다시 wayland로 돌아갈 것임.

### color Emoji support (17.10부터 지원되기 시작함)

###  Snap 패키지 지원 강화
- snapcraft
- 우분투 소프트웨어에 Snap Store 내장
- 쉬운 Snap 배포 채널 변경
- 일부 시스템 앱을 Snap 패키지로 구성
  - 라이브 패치 서비스
  - shared GNOME 3.26 Ubuntu Stack
  - 특수문자 뷰어
  - 시스템 로그 뷰어

### subiquity
- 우분투 데스크탑 설치 프로그램은 - ubiquity
- ubuntu server의 새로운 OS 설치 프로그램

### 그외
- 바닐라 버전의 GNOME Shell을 써볼 수 있다.
  - vanilla-gnome-desktop 패키지 설치 시 이용 가능하다.
- ubuntu.com에서 다운로드를 하면 이제 메인 서버가 아닌 가까운 미러서버에서 자동으로 다운로드 된다.
  - 귀찮게 미러서버 페이지를 방문할 필요가 없어졌다.

## 3. VM이랑은 무관한 컨테이너 이야기 - 윤성국님(코딩이랑 무관합니다만)

- LXC - LXD - Docker비교해 보고, 소스코드화 함께 컨테이너가 어떻게 동작하는지 그리고 Docker 이미지 레이어 버전 관리 원리에 대해 알아본다.
- VM, 컨테이너의 내부를 조금 알아보자.
- vm - 하이퍼바이저 존재. 그 위에 OS 등이 올라감.
- 컨테이너 - 하이퍼바이저 없음.

### LXC,LXD & Docker 차이
 - 목적이 다르다. 지향하는 방향이 다르다.
 - LXC
    - 가상머신의 대체재
    - LXD는 매니징 기능 강화(보안적 측면)
- Docker
    - 개발환경의 배포 및 버전관리 (쪽으로 많이 사용)
- LXC Container
    - 생명주기
    - create -> start -> attach -> stop ->detroy
- 어떻게 프로세스를 속일 수 있는가~~?
    - Namespaces
    - 컨테이너 안, 밖의 프로세스 ID는 다르다.
- (중요) chroot - change root
- cgroups - 그냥 묶기만 한다.

## 후기
- 아쉽게도 그다음 세션은 1분기 C++ 스터디 결과는 개인 사정으로 참석하지 못하였다.
- 현재 데스크탑을 우분투로 사용하고 사내 서버도 AWS Ubutu를 사용하고 있다. 따라서, 이번 18.04 LTS가 Release되면서 어떤 변화가 생겼는지 무척 궁금했는데 이런 궁금점을 없애주는 좋은 세미나였다.
