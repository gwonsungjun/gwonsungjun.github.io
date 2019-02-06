---
title: 'Windows 10 Home에서 Docker 설치 하기'  
layout: post  
tags : How to install, docker
category: How to install
subtitle: Windows 10 Home - Docker 설치
author : sungjun
---

**Windows 10 Home - Docker 설치** 

---

### Windows 10 Home에서 Docker 설치 하기    
Docker Community Edition for Windows는 Windows 10 home에서 사용할 수 없다.(Windows 10 pro 이상에서 가능)
이유는 Windows에서 Docker를 사용하려면  Hyper-V 가 필요한데 Windows 10 home에는  Hyper-V (ms에서 만든 가상화 SW이며 Docker를 Windows 환경에서 VirtualBox 없이 Native하게 돌아가도록 해준다) 를 지원하지 않기 때문이다.  

도커는 리눅스에서만 사용 가능한 가상화 컨테이너이다. 따라서 윈도우나 맥 OS를 사용하는 경우에는 오라클 버추얼박스(VirtualBox) 등의 저수준 가상화 소프트웨어로 리눅스 운영체제를 가진 가상 컴퓨터를 만들어야지만 도커를 쓸 수 있다.  

그래도 방법이 있다. Docker Toolbox(Legacy desktop solution)를 설치해서 사용하면 된다.  

<https://docs.docker.com/toolbox/toolbox_install_windows/> 링크로가서 toolbox 다운로드를 받으면 된다.  

Virtualization은 반드시 사용가능 상태이어야 한다.(Virtualization은 Hyper-V와 다른 것)  
아래 사진에 **가상화:사용** 으로 되어있는 것을 확인할 수 있다.

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall8.PNG)

**setUp 파일을 실행한다.**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall1.PNG)

**Next>**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall2.PNG)

**virtual box가 설치되어있다면 체크를 해제하고 설치되어 있지 않다면 체크를 해줘야한다.(Git도 마찬가지) - Next>**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall3.PNG)

**Next>**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall4.PNG)

**Finish**  

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall5.PNG)

**Run docker - quickstart terminal을 실행하면 Virtualbox용 이미지 Boot2Docker를 다운로드한다.**  

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall6.PNG)

**위 화면을 확인할 수 있다. Virtualbox내에 default라는 vm이 아래와 같이 돌고 있으면 된다.**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall7.PNG)

**docker run hello-world 명령어 입력하고 위와 같이 나오면 성공!**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall9.PNG)

**docker version 확인**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall10.PNG)

**Docker Toolbox에 GUI툴 Kitematic이 있다. 클릭 몇번으로 이미지를 다운로드 받아서 컨테이너 생성까지 정말 간편하게 사용하고 로그도 출력되며 웹포트가 열려있으면 웹도 쉽게 띄울수있다.  
docker hub는 docker 이미지들을 등록하고 공유할 수 있는 서비스 이다. docker hub를 회원가입하고 로그인을 진행한다.**

![Virtualization은](/assets/images/usingimages/DockerInstallImage/dockerInstall11.PNG)

**그리고 Kitematic를 확인하면 방금 실행한 Docker container가 활성화 되어 있는 것을 확인할 수 있다.**  

이상 Windows 10 Home에서 Docker 설치를 마친다.

### Link
<http://pseg.or.kr/pseg/infoinstall/6076>  
<http://sukill.tistory.com/1>  
<https://docker.gnupark.com/bbs/board.php?bo_table=docker&wr_id=69>  
