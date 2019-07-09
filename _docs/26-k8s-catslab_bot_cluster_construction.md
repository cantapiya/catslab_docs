---
title: "04 CATS Lab Bot Cluster Construction"
permalink: /docs/k8s-catslab_bot_cluster_construction/
excerpt: "Construction of CATS Lab Bot Cluster"
last_modified_at: 2019-06-19T12:30:00
redirect_from:
  - /theme-setup/
toc: true
---

## CATS Lab Bot Cluster 구축하기  

[k8s Cluster on AWS with Kops](/catslab_docs/docs/k8s-kops_cluster/)를 통해 kops를 이용하여 클러스터 구축을 완료하였다면, 다음 명령어를 통해 클러스터의 유효성을 확인할 수 있습니다. 

```shell
$ kops validate cluster
```

![image](https://user-images.githubusercontent.com/47657715/60797272-f0064b00-a1a9-11e9-9de0-c333eb326f8a.png)


사전 준비과정을 모두 완료하였다면, 다음 과정을 통해 CATS Lab Bot Cluster를 구축할 수 있습니다. 

<br>  


### kops 클러스터 네임스페이스 생성하기  

우선 다음 명령어를 이용하여 Bot을 운용할 네임스페이스를 생성합니다. 사용자 지정 네임스페이스 이름은 `coza-bot`으로 지정합니다.  

```shell
$ kubectl create namespace coza-bot
```
![image](https://user-images.githubusercontent.com/47657715/60851961-c04e5600-a230-11e9-99d5-aa2bf41bea66.png)  

<br>  


### k8s Secret 리소스 생성하기  

[Docker Hub Private Repository](/docs/k8s-docker/)에서 설정한 Docker Hub 개인 저장소의 Docker Image를 불러오기 위해서 Kubernetes Secret 리소스를 설정이 필요합니다.  

다음 명령어를 이용해 `regcred`라는 Secret 리소스를 생성합니다.

```shell
$ kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=<Docker Hub Username> --docker-password=<Password> --docker-email=<Docker Hub E-mail>
```

![image](https://user-images.githubusercontent.com/47657715/60852302-166fc900-a232-11e9-850b-564d60099f73.png)

Docker Hub Username과 Password는 [Docker Hub Private Repository](/docs/k8s-docker/)에서 Docker Image를 빌드한 후 개인 저장소로 푸쉬하기 위해 `Docker login`에서 입력한 것과 동일하고, `Docker Hub E-mail`은 Docker Hub 사이트에서 계정을 생성할 때 입력한 E-mail과 같습니다.  

<br>  

위의 두 과정을 완료하였다면, 다음 명령어를 통해 Namespace와 Secret 리소스가 잘 생성되었는지 확인할 수 있습니다.

```shell
$ kubectl get secret
```

```shell
$ kubectl get namespace
```

![image](https://user-images.githubusercontent.com/47657715/60852503-eaa11300-a232-11e9-89f0-810a1839e8df.png)

<br>  


### 특정 네임스페이스로 Secret 리소스 export하기  

다음 명령어를 이용해 네임스페이스가 `default`로 지정된 `regcred` Secret 리소스를 `coza-bot` 네임스페이스에서도 접근할 수 있도록 `--export` 옵션을 이용하여 네임스페이스를 추가합니다.  

```shell
$ kubectl get secret regcred --namespace=default --export -o yaml | kubectl apply --namespace=coza-bot -f -
```

![image](https://user-images.githubusercontent.com/47657715/60852675-a9f5c980-a233-11e9-9292-71bcf45c7637.png)

<br>  


### Git 이용하여 Botmanager clone하기  

CATS Lab Bot Cluster를 구성하는 여러 kubernetes 리소스들을 배포하기 위해 [CATS Lab Botmanager Github](https://github.com/Derek-tjhwang/botmanager) 사이트에서 git clone을 이용하여 배포에 필요한 `yaml` 파일들을 내려받습니다.

```shell
$ git clone https://github.com/Derek-tjhwang/botmanager.git
```

![image](https://user-images.githubusercontent.com/47657715/60852995-2e951780-a235-11e9-9bcc-25de798563b6.png)

![image](https://user-images.githubusercontent.com/47657715/60853080-8b90cd80-a235-11e9-967c-f1578d585b95.png)

<br>   



### AWS에서 파일시스템 생성하기  


### CATS Lab config에 DNS 설정하기  


### ConfigMap과 deployment 수정하기  


### postgres 배포하기  


### 대시보드 접속하여 postgres 컨테이너 접속하기  



### config 수정, data 디렉터리 생성 후 마운트 경로 변경하기


### AWS 인바운드 규칙 추가하기


### 포트 추가하기 (문의)  


### postgres config 복사하기


### nginx 배포하기  



### django 배포하기  


### django 컨테이너 접속해서 migration 및 django 서버 계정 생성하기  


### celery 배포하기



### 확인 

