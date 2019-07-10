---
title: "05 CATS Lab Bot Cluster Construction"
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



### AWS에서 파일 시스템 생성하기  

클러스터에서 사용할 파일 시스템을 생성하기 위해 `AWS 서비스` 탭 에서 `스토리지-EFS`로 접근하여 파일 시스템 생성을 진행합니다.  

![image](https://user-images.githubusercontent.com/47657715/60854110-fcd27f80-a239-11e9-8e7f-2ad7be960ab9.png)

![image](https://user-images.githubusercontent.com/47657715/60854206-49b65600-a23a-11e9-94e5-abd284469008.png)  


파일 시스템 생성의 1단계에서는 생성할 파일 시스템의 VPC를 설정하는데, `보안 그룹` 탭에서 생성한 클러스터의 `VPC ID`를 확인 후 선택합니다.  

가용 영역은 클러스터 가용 영역에서와 마찬가지로 `ap-northeast-2c` (서울)로 지정한 후 다음 단계를 선택합니다.


![image](https://user-images.githubusercontent.com/47657715/60854488-6acb7680-a23b-11e9-97bf-cc166bc17599.png)

![image](https://user-images.githubusercontent.com/47657715/60854824-897e3d00-a23c-11e9-860e-896c6abbd3d4.png)

<br>  


2단계, 3단계에서는 변경사항없이 다음 단계를 진행합니다. 

![image](https://user-images.githubusercontent.com/47657715/60855452-bf242580-a23e-11e9-9155-2ff13919410c.png)
![image](https://user-images.githubusercontent.com/47657715/60855495-e2e76b80-a23e-11e9-8b61-d93486e03a8b.png)

![image](https://user-images.githubusercontent.com/47657715/60855533-0a3e3880-a23f-11e9-919d-d4eff12bd4c8.png)

<br>  


파일 시스템 생성을 완료한 뒤, 생성된 파일 시스템 정보를 조회하여 `DNS 이름` 항목의 주소를 복사해 둡니다.

![image](https://user-images.githubusercontent.com/47657715/60855701-bd0e9680-a23f-11e9-857b-e908f1f0ffba.png)
![image](https://user-images.githubusercontent.com/47657715/60855705-bed85a00-a23f-11e9-948a-811791381e78.png)
![image](https://user-images.githubusercontent.com/47657715/60855708-c13ab400-a23f-11e9-9ba5-32c197fc50af.png)

<br>  

파일 시스템 인바운드 규칙을 수정하기 위해 `보안 그룹`으로 이동하여 다음과 같이 규칙을 수정합니다.  


생성된 파일 시스템의 `VPC ID` 를 확인한 뒤, 보안 그룹의 이름을 구분하기 위해 `NFS Storage` (혹은 구분하기 위한 이름)으로 변경합니다.


인바운드 규칙 편집(마우스 우 클릭을 하여 `인바운드 규칙 편집` 클릭)에서 유형은 `NFS`로 지정하고, 소스 항목에는 masters.<Cluster Name> .k8s.local로 생성된 보안 그룹과 nodes.<Cluster Name> .k8s.local로 생성된 보안 그룹의 `그룹 ID` 를 각각 복사하여 추가합니다. 

![image](https://user-images.githubusercontent.com/47657715/60856453-360eed80-a242-11e9-8b04-f5df9b53e814.png)

<br>  




### DB storage, deployment, service 배포하기  

생성한 파일 시스템의 인바운드 규칙을 변경하고, DNS 주소를 복사해 두었다면 다시 인스턴스로 접속하여 클러스터 생성에 필요한 리소스들의 배포를 진행합니다.  

yaml 파일을 수정하기 위해 vim을 설치합니다.

```shell
$ sudo apt update  
$ sudo apt install vim -y
```
![image](https://user-images.githubusercontent.com/47657715/60859267-ea157600-a24c-11e9-86da-e8469ddaf236.png)
![image](https://user-images.githubusercontent.com/47657715/60859275-f4377480-a24c-11e9-857b-cdb29a101574.png)



앞서 clone한 `botmanager` 디렉터리에서 `db` 디렉터리로 이동한 후 `postgres-storage.yaml`과 `postgres-deployment.yaml`파을을 수정합니다.



![image](https://user-images.githubusercontent.com/47657715/60857017-09f46c00-a244-11e9-8c29-35a29bf1db58.png)

`postgres-storage.yaml`의 `nfs - server`에는 파일 시스템을 생성한 후 복사해 둔 `DNS 주소`를 입력합니다.  

`nfs - path`에는 다음과 같이 `"/"`로 입력합니다.

![image](https://user-images.githubusercontent.com/47657715/60859896-2a75f380-a24f-11e9-9bc6-27be7e0bd64c.png)

<br>  

`postgres-deployment.yaml`의 `volumeMounts - mountPath` 항목을 다음과 같이 `/data`로 변경하고, `envFrom` 항목들을 모두 주석처리 합니다.  

![image](https://user-images.githubusercontent.com/47657715/60862562-e1c33800-a258-11e9-997e-5cf4b1a3114c.png)

<br>  


yaml 파일을 수정 후 다음 명령어를 통해 각각의 yaml 파일을 배포합니다.

```shell
$ kubectl apply -f postgres-storage.yaml
$ kubectl apply -f postgres-deployment.yaml
```


![image](https://user-images.githubusercontent.com/47657715/60860330-fef40880-a250-11e9-82c2-2555e2cf2609.png)
![image](https://user-images.githubusercontent.com/47657715/60860337-03202600-a251-11e9-8630-9cd92ee3f9eb.png)

<br>  

`postgres-storage.yaml`을 배포하면 다음과 같이 `kubectl get pv` 명령어를 통해 `postgres-pv-volume`이 생성된 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/47657715/60860335-00bdcc00-a251-11e9-87f6-fcf7c9a04950.png)

<br>  


두 개의 리소스를 배포한 후 [k8s Dashboard](/docs/k8s-dashboard/)에서 설치한 대시보드에 접속하면 다음과 같이 디플로이먼트, 파드, 레플리카 셋들이 배포된 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/47657715/60861312-f56c9f80-a254-11e9-9a4c-3bfdd71fd9cd.png)

파드의 `postgres-xxx`를 클릭하면 파드 상세보기 페이지로 이동하고, `EXEC` 버튼을 이용하여 postgres 컨테이너의 터미널로 접속할 수 있습니다.  

![image](https://user-images.githubusercontent.com/47657715/60861388-41b7df80-a255-11e9-96b0-8d51b0877e94.png)
![image](https://user-images.githubusercontent.com/47657715/60861395-4aa8b100-a255-11e9-86e9-aa9b50d8f967.png)


postgres 컨테이너 터미널로 접속하였다면, root 디렉터리에서 `mkdir` 명령어를 이용하여 다음과 같이 하위 디렉터리를 생성합니다.  

`/data/data/db/postgresql`  
`/data/data/config`  


![image](https://user-images.githubusercontent.com/47657715/60861445-7b88e600-a255-11e9-8389-7b8899c710f0.png)
![image](https://user-images.githubusercontent.com/47657715/60862684-454d6580-a259-11e9-94af-6628c41fa1c3.png)  



하위 디렉터리를 생성한 뒤, 다시 ubuntu 터미널에서 다음 명령어를 이용하여 설치한 kubernetes 클러스터의 config 파일을 postgres 컨테이너로 복사합니다.  

`<Postgres Pod Name>`에는 postgres 파드를 클릭하였을 때 파드 상세 페이지에서 확인할 수 있는 파드 이름을 입력합니다.

```shell
$ kubectl cp /home/ubuntu/.kube/config default/<Postgres Pod Name>:/data/data/config/kube_config  
```

![image](https://user-images.githubusercontent.com/47657715/60862238-ee935c00-a257-11e9-81d3-d8a437625789.png)


config 파일을 복사한 뒤 postgres 터미널에서 확인하면 다음과 같이 kube_config 파일로 복사된 것을 확인할 수 있습니다.  

![image](https://user-images.githubusercontent.com/47657715/60861778-7710fd00-a256-11e9-91cf-cd7178c8a7c9.png)  

<br>  

config 파일을 postgres 컨테이너로 복사한 후 

`postgres-storage.yaml`파일과 `postgres-deployment.yaml`파일을 다음과 같이 다시 수정합니다.  


![image](https://user-images.githubusercontent.com/47657715/60866514-7fbc0000-a263-11e9-836b-27364c3a17f7.png)

![image](https://user-images.githubusercontent.com/47657715/60866534-8d718580-a263-11e9-8597-9efd3b6d8c10.png)



수정 후 `kubectl delete` 명령어를 이용하여 배포한 리소스들을 삭제한 후 다시 배포합니다.




같은 디렉터리의 `redis-deployment.yaml`과 `redis-service.yaml`도 함께 배포합니다.  

![image](https://user-images.githubusercontent.com/47657715/60874366-dd0b7d80-a272-11e9-9ec6-3c9b6aec79ec.png)

`redis`까지 배포를 완료하면, kubernetes 대시보드에서 다음과 같이 배포중인 디플로이먼트, 파드, 레플리카 셋, 서비스, 퍼시스턴스 볼륨 클레임, 시크릿 등과 같은 리소스를 확인할 수 있습니다.  


![image](https://user-images.githubusercontent.com/47657715/60874434-fad8e280-a272-11e9-9155-a7398cdb8827.png)

![image](https://user-images.githubusercontent.com/47657715/60874437-fc0a0f80-a272-11e9-9efd-b5c2f82bf5c1.png)






### nginx 배포하기  

`storage.yaml`과 `deployment.yaml`을 수정하고 배포한 후 nginx 배포를 진행합니다.


postgres 컨테이너 터미널로 접속하여 다음과 같은 설정을 진행합니다.  

```shell
$ su postgres
$ psql 
```

```shell
postgres=# ALTER USER postgres with encrpted password '<Password>';
postgres=# create database <Database Name>;
postgres=# \l  
```

`<Database Name>` 에는 생성할 database의 이름을 입력합니다.  

`create database` 명령어를 실행 후 database가 잘 생성되었는지 확인하기 위해 `\l` 명령어를 입력하여 database 목록을 확인합니다.  

![image](https://user-images.githubusercontent.com/47657715/60872090-088c6900-a26f-11e9-8bdf-68f9282b6aab.png)


postgres database를 생성하고 vim을 설치한 후 `/var/lib/postgresql/data` path로 이동한 후 `pg_hba.conf` 파일에 아래와 같은 내용을 추가합니다.  


![image](https://user-images.githubusercontent.com/47657715/60872152-2b1e8200-a26f-11e9-8cbf-23db6483eaea.png)
![image](https://user-images.githubusercontent.com/47657715/60872159-2d80dc00-a26f-11e9-817e-6cc93a84d453.png)


![image](https://user-images.githubusercontent.com/47657715/60872181-383b7100-a26f-11e9-8efd-f3f7f9ee45fc.png)

![image](https://user-images.githubusercontent.com/47657715/60872497-b861d680-a26f-11e9-994d-c040152a0f0c.png)


아래와 같이 `catslab-configmap.yaml`의 기본 템플릿에서 각 부분을 수정합니다.  

![image](https://user-images.githubusercontent.com/47657715/60872948-8e5ce400-a270-11e9-9fc9-54229a121652.png)

`K8S_BOT_IMAGE`에는 Docker Hub 개인 저장소에 푸시한 Docker Image를 입력합니다.  

`K8S_BOT_NAMESPACE`에는 Secret을 export한 네임스페이스인 `coza-bot`으로 입력합니다.  

`K8S_IMG_PULL_SECRET`에는 생성한 Secret 리소스의 이름을 입력합니다.  

`POSTGRES_DB`에는 postgres 컨테이너 터미널에서 생성한 database의 이름을 입력합니다.

`POSTGRES_USER`에는 `postgres`를 입력하고, `POSTGRES_PASSWORD`에는 database를 생성할 때 입력한 비밀번호를 입력합니다.

입력한 예시는 다음과 같습니다. 

![image](https://user-images.githubusercontent.com/47657715/60873613-a3864280-a271-11e9-9232-c95d7fe62163.png)
   

`catslab-configmap.yaml` 파일을 수정한 뒤 다음 명령어를 이용하여 배포를 진행합니다.  

```shell
$ kubectl apply -f catslab-configmap.yaml
```





### django 배포하기  

위의 과정까지 완료하였다면, django deployment와 service를 배포하기 위한 준비가 완료되었습니다.  

django 디렉터리로 이동하여 다음과 같이 yaml 파일을 수정한 후 배포를 진행합니다.  



배포가 정상적으로 완료되었다면 다시 kubernetes 대시보드에서 django 컨테이너 터미널로 접속할 수 있습니다.  







### django 컨테이너 접속해서 migration 및 django 서버 계정 생성하기  

django 컨테이너의 터미널로 접속하여 다음과 같은 과정을 진행합니다.  



### celery 배포하기

django migration과 superuser까지 잘 생성했다면, 나머지 celery 관련 리소스의 배포를 진행합니다.


### 확인  

모든 리소스의 배포가 정상적으로 진행되었다면, kubernetes 대시보드에서 다음과 같이 배포된 리소스들을 확인할 수 있습니다.



### 포트 추가하기 (문의)  




### bot 실행  



