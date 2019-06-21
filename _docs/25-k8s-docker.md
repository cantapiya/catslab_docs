---
title: "04 Docker Hub Private Repository"
permalink: /docs/k8s-docker/
excerpt: "Docker Hub Private Repository and Docker Image Build"
last_modified_at: 2019-06-19T12:20:00
redirect_from:
  - /theme-setup/
toc: true
---

## Docker Hub Private Repository 생성 및 Docker Image 빌드 후 푸시하기  

CATS Lab Bot 클러스터를 구축하여 전략을 운용할 때의 장점은 사용자가 구현한 전략을 CATS Lab 서버에 전략의 소스코드를 공개 및 저장하지 않고 사용할 수 있다는 것입니다.  

사용자가 도커 허브의 개인 저장소(private repository)에 미리 생성한 도커 이미지를 이용하여 하나의 독자적인 서버를 운용할 수 있습니다.  

CATS Lab Bot 클러스터를 구축하기 이전에 Docker Hub 계정을 생성합니다.  




### Docker Hub 계정 생성하기  

[도커 허브(Docker Hub)](https://hub.docker.com/) 사이트에서 `Sign up for Docker Hub`를 이용하여 계정을 생성합니다.  
(이미 계정을 보유하고 있거나, 개인 저장소를 이용하고 계신다면 이 과정을 건너 뛰어도 좋습니다.)  

![image](https://user-images.githubusercontent.com/47657715/59890881-ee98fc80-940d-11e9-91fe-7df66c72958a.png)



### Private Repository 생성하기  

계정 생성을 진행한 뒤 개인 저장소 생성을 진행합니다.  

`Create a Repository`를 이용하여 저장소를 생성합니다.

![image](https://user-images.githubusercontent.com/47657715/59891225-3ff5bb80-940f-11e9-9cfe-d199992f589d.png)


`Name` 필드에 저장소 명칭을 지정하고 Visibility 옵션은 `Private`으로 설정한 뒤 `Create`를 이용하여 저장소를 생성합니다.  

(Docker Hub에서 제공하는 Private Repository는 1개까지 무료로 이용가능합니다.)


![image](https://user-images.githubusercontent.com/47657715/59891364-daee9580-940f-11e9-8586-29e83ea2d43d.png)


다음과 같이 생성된 저장소를 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/47657715/59892629-dd072300-9414-11e9-994d-295a47ae7965.png)

<br>  



### Git 설치하기  


### CATS Lab Package 클론하기  
Git 이용하여 CATS Lab Package 클론하기



### Docker Image 빌드하기

Dockerfile 이용하여 docker image 빌드하기  



### Docker Image 푸시하기

빌드한 docker image를 생성한 Private Repository에 푸시하기  




