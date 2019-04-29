---
title: "Package Installation"
permalink: /docs/package-installation/
excerpt: "CATS Lab Package Installation Document"
last_modified_at: 2019-04-25T12:00:00
redirect_from:
  - /theme-setup/
toc: true
---


## CATS Lab Package 설치


우선 CATS Lab 패키지를 설치하기 위해 [Git Download](https://git-scm.com/downloads)에서 Git을 다운로드합니다.

![Git 설치 완료](https://user-images.githubusercontent.com/47657715/56722755-ab5e3c80-6782-11e9-980a-9f91bbea76e4.png)  

  


[CATS Lab Github](https://github.com/Derek-tjhwang/CATS-LAB) 사이트에서 git clone을 이용하여 설치에 필요한 파일들을 내려받습니다.

-----------------------------  
`~$ git clone https://github.com/Derek-tjhwang/CATS-LAB.git`  
  
-----------------------------  

![git clone [REPOSITORY]](https://user-images.githubusercontent.com/47657715/56722802-c3ce5700-6782-11e9-93f0-5e94b15e03a7.png)  

  


CATS-LAB 디렉토리로 이동 후 다음 명령어를 실행시킵니다.  
(python ver. 3.6.4과 pip, 그리고, 아래 명령어 실행을 위한 python package인 setuptools이 설치된 환경을 전제로 합니다.) 
 
-----------------------------  
`~$ cd CATS-LAB`  
`~$ python setup.py sdist`  

-----------------------------  

![](https://user-images.githubusercontent.com/47657715/56723358-c41b2200-6783-11e9-8f52-395d1ce103b2.png)  
  
  

  

새로 생성된 dist 디렉토리로 이동 후 다음 명령어를 입력하여 CATS Lab package를 설치합니다.

-----------------------------  
`~$ cd dist`  
`~$ pip install coza-0.2.tar.gz`  

-----------------------------  
![CATS Lab Package 설치](https://user-images.githubusercontent.com/47657715/56723116-4d7e2480-6783-11e9-870e-7bfdfe618cd8.png)  



  
## CATS Lab Sign Up

CATS Lab 패키지를 사용하기 위해서는 CATS Lab UUID와 사용할 BOT의 BOT ID가 필요합니다.

이를 발급받기 위해 우선 [CATS Lab](https://coza.me/home) 사이트에서 로그인 후 UUID와 BOT ID를 발급 받습니다.




## 환경변수 설정  

CATS Lab 패키지를 설치한 후 패키지에서 사용할 환경변수를 설정합니다. 

필요한 환경변수로는 CATS Lab API Server의 Host와 Secret Key, 위의 과정에서 발급받은 UUID와 BOT ID입니다.

다음과 같이 환경변수를 등록 후 CATS Lab 패키지를 사용할 수 있습니다.  

-----------------------------  
`~$ export COZA_HOST="http://api.coza.me"`  
`~$ export COZA_SECRET=SECRET_KEY`  
`~$ export COZA_USER_UUID=USER_UUID`  
`~$ export COZA_BOT_ID=BOT_ID`  

-----------------------------  









