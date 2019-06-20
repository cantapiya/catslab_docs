---
title: "03 k8s Dashboard"
permalink: /docs/k8s-kops_dashboard/
excerpt: "Kubernetes Dashboard"
last_modified_at: 2019-06-19T12:20:00
redirect_from:
  - /theme-setup/
toc: true
---

# Kubernetes Dashboard 배포 및 연결하기  

이 문서의 내용은 [자습서: Kubernetes 웹 UI 배포(대시보드)](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/dashboard-tutorial.html)를 기반으로 합니다.

## 대시보드 배포 사전 준비  

[k8s Cluster on AWS with Kops](/catslab_docs/docs/k8s-kops_cluster/)를 통해 kops를 이용하여 클러스터 구축을 완료한 후, 다음 명령어를 이용하여 클러스터를 확인할 수 있습니다.  

-----------------------------  
```shell
$ kops validate cluster
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59811904-de6a1a00-9346-11e9-9e9a-5e854713990a.png)



### 대시보드 배포  

대시보드를 배포하기 위해 아래 단계를 차례대로 수행하여 사전에 구축한 클러스터에 배포합니다.


### Kubernetes Dashboard 배포  

먼저, 구축한 클러스터에 kubernetes 대시보드를 배포합니다.

-----------------------------  
```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml 
```
-----------------------------  

![image](https://user-images.githubusercontent.com/47657715/59812168-f8582c80-9347-11e9-97b2-9efc384b3232.png)

  

### heapster 배포  

heapster를 배포하여 컨테이너 클러스터 모니터링 및 클러스터의 성능 분석 활성화합니다.  

-----------------------------  
```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/heapster.yaml
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59812930-aebd1100-934a-11e9-9ae8-f420c3b356de.png)



### influxdb 백엔드 배포  

heapster에 대한 influxdb 백엔드를 클러스터에 배포합니다.

-----------------------------  
```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/influxdb/influxdb.yaml
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59813010-f0e65280-934a-11e9-9038-fcff6867c34c.png)



### heapster 클러스터 역할 바인딩 생성  

대시보드에 대한 heapster 클러스터 역할 바인딩을 생성합니다.  

-----------------------------  
```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/heapster/master/deploy/kube-config/rbac/heapster-rbac.yaml
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59813155-766a0280-934b-11e9-82cb-08888cbde1d5.png)




## eks-admin 서비스 계정 및 클러스터 역할 바인딩 생성  

기본적으로 Kubernetes 대시보드 사용자의 권한은 제한되어 있기 때문에, 서비스 계정 및 클러스터 역할 바인딩 생성이 필요합니다.  

이를 사용하여 관리자 수준 권한으로 대시보드에 보안 연결을 할 수 있습니다.  

자세한 내용은 Kubernetes 문서의 [Managing Service Accounts(서비스 계정 관리)](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/)를 참조하십시오.  

eks-admin 서비스 계정 및 클러스터 역할 바인딩을 생성하려면 다음 과정이 필요합니다.


### 매니페스트 생성  

아래 텍스트가 포함된 eks-admin-service-account.yaml이라는 파일을 생성합니다. 이 매니페스트는 eks-admin이라는 서비스 계정 및 클러스터 역할 바인딩을 정의합니다.  

__eks-admin-service-account.yaml__  

-----------------------------  
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: eks-admin
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: eks-admin
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: eks-admin
  namespace: kube-system
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59813658-46236380-934d-11e9-9be7-9300224a1178.png)  
![image](https://user-images.githubusercontent.com/47657715/59813660-49b6ea80-934d-11e9-9f6a-e86693ba960e.png)  



### 매니페스트 적용  

생성한 매니페스트를 이용하여 서비스 계정 및 클러스터 역할 바인딩을 클러스터에 적용합니다.  

-----------------------------  
```shell
$ kubectl apply -f eks-admin-service-account.yaml
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59814028-bc749580-934e-11e9-8ddb-aeb3d2fc3972.png)  




## 대시보드에 연결  

클러스터에 Kubernetes 대시보드가 배포되었고, 클러스터를 보고 제어하는 데 사용할 수 있는 관리자 서비스 계정을 가졌기 때문에 이제 이 서비스 계정을 사용하여 대시보드에 연결할 수 있습니다.  

Kubernetes 대시보드에 연결하기 위해 다음 과정을 수행합니다.  


### eks-admin 서비스 계정에 대한 인증 토큰을 조회  

eks-admin 서비스 계정에 대한 인증 토큰을 조회합니다. 출력에서 <authentication_token> 값을 복사합니다. 이 토큰을 사용하여 대시보드에 연결합니다.  

-----------------------------  
```shell
$ kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59814697-2c841b00-9351-11e9-8e6f-0b9fdd0b6396.png)


### kubectl proxy 시작  

kubectl proxy를 시작합니다. 이미 proxy node가 생성 되어있는 경우 건너뛸 수 있습니다.

-----------------------------  
```shell
$ kubectl proxy
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59814783-78cf5b00-9351-11e9-994a-4e15ab42dbda.png)  


### 대시보드 엔드포인트로 액세스하기  

웹 브라우저로 다음 링크를 열어 대시보드 엔드포인트에 액세스합니다.  
```
http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login  
```  

또는  
```
https://<DNS 이름>/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login
```  

AWS 로드밸런싱 - 로드밸런서 페이지에서 구축한 클러스터의 DNS 이름을 복사하여 `<DNS 이름>` 자리에 입력하여 액세스합니다.

![image](https://user-images.githubusercontent.com/47657715/59815040-5558e000-9352-11e9-9205-1eb3cbf74953.png)


위의 엔드포인트로 액세스하면, 다음과 같이 로그인 과정이 필요합니다.

![image](https://user-images.githubusercontent.com/47657715/59815172-c6989300-9352-11e9-90fb-5c17fa7c5b08.png)

다음 명령어를 실행하여 `사용자이름`과 `비밀번호`를 확인합니다.  

-----------------------------  
```shell
$ kubectl config view
```
-----------------------------

![image](https://user-images.githubusercontent.com/47657715/59815551-dbc1f180-9353-11e9-994b-cd0216b96f63.png)

  
`사용자이름`은 `admin`으로 입력하고, `비밀번호`는 위의 명령어 실행 결과에서 `password`를 복사하여 입력합니다.  

![image](https://user-images.githubusercontent.com/47657715/59815949-2d1eb080-9355-11e9-83ab-c0f6bad01bcd.png)

__eks-admin 서비스 계정에 대한 인증 토큰을 조회__  에서 조회한 토큰을 이용하여 토큰 필드에 복사한 다음 `SIGN IN`을 선택합니다.  


## 대시보드 사용  

![image](https://user-images.githubusercontent.com/47657715/59816104-c8178a80-9355-11e9-8652-d0cc2648c1a9.png)  

Kubernetes 클러스터 대시보드에 연결한 이후 eks-admin 서비스 계정을 사용하여 클러스터를 보고 제어할 수 있습니다.  

대시보드 사용에 대한 자세한 내용은 [GitHub 프로젝트 문서](https://github.com/kubernetes/dashboard)를 참조하십시오.  








