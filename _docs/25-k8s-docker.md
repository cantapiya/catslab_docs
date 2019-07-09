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

[k8s Cluster on AWS with Kops](/catslab_docs/docs/k8s-kops_cluster/)의 과정을 따라 클러스터를 설치했다면, git이 자동으로 설치됐을 것입니다. (인스턴스 생성 초기부터 설치돼 있습니다.)  

다음 명령어를 통해 Git이 제대로 설치되었는지를 확인합니다.  

```shell
$ git --version
```

제대로 설치되지 않은 경우는 다음 명령어를 통해 설치할 수 있습니다.

```shell
$ apt-get install git  
```



### CATS Lab Package 클론하기  

Git 설치가 정상적으로 완료되었다면, Private Docker Image를 생성하기 위해 CATS Lab Package 클론을 진행합니다.  

[CATS Lab Github](https://github.com/Derek-tjhwang/CATS-LAB) 사이트에서 git clone을 이용하여 CATS Lab 패키지를 내려받습니다.

-----------------------------  
```shell
$ git clone https://github.com/Derek-tjhwang/CATS-LAB.git
```    
-----------------------------  

![git clone [REPOSITORY]](https://user-images.githubusercontent.com/47657715/56722802-c3ce5700-6782-11e9-93f0-5e94b15e03a7.png) 





### Docker Image 빌드하기  

[Package Installation](/catslab_docs/docs/package-installation/)과 같이 내려 받은 CATS Lab 패키지를 정상적으로 설치했다면 CATS-LAB 디렉터리 하위에 `scripts`라는 디렉터리를 확인할 수 있습니다.  

이 디렉터리 내부에 배포하고자하는 전략을 작성하여 추가합니다. 이때 파일이름의 형태는 `<Strategy Name>_entrypoint.py`과 같이 지정합니다.  

배포하기 위한 전략을 추가하였다면, Dockerfile 이용하여 docker image 빌드한 후 Dockerhub private repository로 푸쉬합니다.  

 
Dockerfile을 이용한 Docker Image 빌드 명령어는 다음과 같습니다. Dockerfile-coza라는 파일이 있는 디렉터리로 이동하여 다음 명령어를 실행합니다.

```shell
$ docker build -t <Private Repository> ./ -f Dockerfile-coza
```  

`<Private Repository>`에는 `<Docker Hub Username>/<Repository Name>:<tagname>` 형태로 지정합니다.  

`<tagname>`을 지정하지 않는 경우 자동으로 latest로 지정됩니다.  



### Docker 로그인하기  

위에서 빌드한 Docker Image를 개인 저장소에 푸시하기 위해서는 Docker 로그인 과정이 필요합니다.  

Docker 로그인을 위한 명령어는 다음과 같습니다.  

```shell
$ sudo docker login
```

![image](https://user-images.githubusercontent.com/47657715/60795789-4d4ccd00-a1a7-11e9-9860-debd9d7df6ab.png)

`Username`와 `Password`에는 각각 Docker Hub 사용자 이름과 비밀번호를 입력합니다. 




### Docker Image 푸시하기

Docker 로그인을 완료한 뒤, 위의 과정에서 빌드한 Docker Image를 Private Repository에 푸시하는 명령어는 다음과 같습니다.

```shell
$ docker push <Dockerhub Username>/<Repository Name>:<tagname>
```

Private Repository 페이지의 Docker commands 항목에서도 확인할 수 있습니다.  

마찬가지로 `<tagname>`을 지정하지 않는 경우 자동으로 latest로 지정됩니다.  






