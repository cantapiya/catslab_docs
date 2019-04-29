---
title: "02 k8s Cluster on AWS with Kops"
permalink: /docs/k8s-kops_cluster/
excerpt: "Creation of k8s Cluster on AWS with Kops"
last_modified_at: 2019-04-26T12:10:00
redirect_from:
  - /theme-setup/
toc: true
---

## Kops 이용하여 Cluster 생성하기  

[AWS Settings and Installation](/catslab_docs/docs/k8s-AWS_settings/)를 통해 AWS 환경설정 및 kops, kubectl, AWS CLI의 설치가 완료되었으면 kops를 이용하여 클러스터 구축을 진행합니다.  


### S3 버킷 생성

클러스터를 생성하기 위해 S3 버킷을 생성하고, 환경 변수를 설정 및 SSH Key Pair를 생성 후 클러스터를 생성합니다.  

먼저 클러스터의 정보를 저장할 저장소가 필요한데, kops는 S3를 사용하므로 다음 명령어로 bucket을 생성하고 Versioning 기능도 활성화합니다.  

-----------------------------  
`~$ aws s3api create-bucket --bucket <bucket name> --region ap-northeast-2 --create-bucket-configuration LocationConstraint=ap-northeast-2`  
  
`~$ aws s3api put-bucket-versioning --bucket <bucket name> --versioning-configuration Status=Enabled`  

-----------------------------  

![S3 버킷 생성 01](https://user-images.githubusercontent.com/47657715/56706446-fad74500-674f-11e9-8d80-bee33bb67dc9.png)

![S3 버킷 생성 02](https://user-images.githubusercontent.com/47657715/56706448-fc087200-674f-11e9-971d-16489148fb55.png)  


  

### 환경 변수 설정  

kops가 AWS API를 쓰기 위한 환경 변수를 설정합니다. 

-----------------------------  
`~$ export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)`  
`~$ export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)`  

-----------------------------  

![환경 변수 설정 01](https://user-images.githubusercontent.com/47657715/56706497-29552000-6750-11e9-9952-53f623d5dc1b.png)  

  
작업을 편리하게 하기 위해 아래 환경 변수도 생성합니다. 

Gossip 기반 클러스터를 사용할 계획이기 때문에 클러스터 이름은 .k8s.local로 입력합니다. (예: myfirstcluster.k8s.local)
bucket name에는 위의 S3 버킷 생성 과정에서 입력한 bucket name을 입력합니다.

![환경 변수 설정 02](https://user-images.githubusercontent.com/47657715/56706513-35d97880-6750-11e9-9d77-8b5156e12996.png)  

  


### SSH Key Pair 생성  

생성해둔 SSH Key Pair가 없다면 클러스터 생성을 시작하기 전에 ssh-keygen 을 통해 미리 생성해 둡니다.

-----------------------------  
`~$ ssh-keygen`  

-----------------------------  

![SSH Key Pair 생성](https://user-images.githubusercontent.com/47657715/56706590-851fa900-6750-11e9-9a0a-e84582783097.png)  

  

### 클러스터 생성  

다음 명령어를 이용하여 클러스터를 생성합니다. 클러스터를 생성한 Availability Zone을 지정해야 하는데 ap-northeast-2c (Seoul)만 지정합니다. 

이 명령어를 입력하면 클러스터가 바로 생성되는 것은 아니고 생성을 위한 설정들이 만들어집니다.

-----------------------------  
`~$ kops create cluster --zones ap-northeast-2c ${NAME}`  

-----------------------------  


![](https://user-images.githubusercontent.com/47657715/56706660-c0ba7300-6750-11e9-887e-382ce80c33ab.png)
...
![클러스터 설정 생성](https://user-images.githubusercontent.com/47657715/56706673-ce6ff880-6750-11e9-9d3a-0c3a9a879500.png)  

  
클러스터 설정을 확인하거나 수정하고 싶으면 다음 명령어를 입력합니다.  

-----------------------------  
`~$ kops edit cluster ${NAME}`  

-----------------------------  


다음 명령어로 생성될 인스턴스 그룹을 확인할 수 있습니다.  

-----------------------------  
`~$ kops get ig --name ${NAME}`  

-----------------------------  


![인스턴스 그룹 확인](https://user-images.githubusercontent.com/47657715/56706758-168f1b00-6751-11e9-9d8c-950284d4bfe4.png)  

기본 설정은 c4.large 타입 마스터 노드 1개와 t2.medium 워커 노드 2개가 생성되게 되어있습니다. CATSLAB 서비스에 가동하기 위해 t2.medium 타입 마스터 노드 1개와 t2.medium 타입 워커 노드 2개를 생성합니다.  

다음 명령어를 통해 마스터 노드와 워커 노드의 설정을 변경합니다.  

-----------------------------  
`~$ kops edit ig master-ap-northeast-2c --name ${NAME}`  
`~$ kops edit ig nodes --name ${NAME}`  

-----------------------------  

  
![인스턴스 그룹 설정 변경 01](https://user-images.githubusercontent.com/47657715/56706793-3d4d5180-6751-11e9-9b11-5268a7e5746d.png)

![인스턴스 그룹 설정 변경 02 - master node](https://user-images.githubusercontent.com/47657715/56706828-6241c480-6751-11e9-9830-3e259e861956.png)

![인스턴스 그룹 설정 변경 03 - worker node](https://user-images.githubusercontent.com/47657715/56706856-784f8500-6751-11e9-811b-36d9e890ddd7.png)  

  
  
생성할 클러스터의 설정을 변경한 후 다음 명령어를 통해 클러스터 생성합니다.  

-----------------------------  
`~$ kops update cluster ${NAME} --yes`  

-----------------------------  


![클러스터 생성](https://user-images.githubusercontent.com/47657715/56706917-b2208b80-6751-11e9-9a15-d1215e4e946f.png)  


  
클러스터 생성이 완료될 때까지는 어느 정도의 시간이 소요됩니다.

클러스터가 잘 생성되었는지 확인하기 위해 다음 명령어들을 이용하여 클러스터 상태를 확인합니다.

-----------------------------  
`~$ kops validate cluster            # 클러스터 상태 확인`  
`~$ kubectl get nodes --show-labels  # 노드 목록 가져오기`  
`~$ kubectl -n kube-system get po    # kube-system 네임스페이스 안의 Pod 목록`  

-----------------------------  


인스턴스가 실행 중으로 바뀌고 노드들 간에 결합이 완료되면 다음과 같이 cluster가 잘 생성된 것을 확인할 수 있습니다.  


![생성된 cluster 확인](https://user-images.githubusercontent.com/47657715/56706960-edbb5580-6751-11e9-8e38-a7ab1551da6c.png)

![생성된 node 확인](https://user-images.githubusercontent.com/47657715/56706963-f1e77300-6751-11e9-8d5e-613a0ea1b0db.png)

![생성된 pod 확인](https://user-images.githubusercontent.com/47657715/56706990-0f1c4180-6752-11e9-939e-d38abdb9a0c7.png)  

![AWS 인스턴스 생성 확인](https://user-images.githubusercontent.com/47657715/56707006-252a0200-6752-11e9-8629-7fb7d2b12307.png)  

  

클러스터가 생성되면 다음과 같은 명령어로 생성한 마스터 노드에 접속이 가능합니다.  

-----------------------------  
`~$ ssh admin@[Master Node IP]`  

-----------------------------  
  



### 클러스터 삭제  

다음 명령어를 통해 클러스터를 삭제할 수 있습니다.  

-----------------------------  
`~$ kops delete cluster --name ${NAME} --yes`  

-----------------------------  
  

kops를 이용하여 생성한 클러스터는 자동 복구(self-healing) 메커니즘으로 가동되기 때문에 구동 중인 노드가 다운되거나 삭제되더라도 자동으로 인스턴스를 교체해 줍니다. 그렇기 때문에 확실하게 클러스터의 가동을 중단시키기 위해서는 클러스터를 구성한 kops 마스터 노드에서 위의 명령어를 통해 클러스터를 삭제하는 것이 안전합니다.  

kops 설치 및 cluster를 생성한 master 노드 역시 다음 명령어를 통해 종료할 수 있습니다.  

-----------------------------  
`~$ sudo systemctl poweroff`  

-----------------------------  

  




  
