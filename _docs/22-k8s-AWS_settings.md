---
title: "01 AWS Settings & Installation"
permalink: /docs/k8s-AWS_settings/
excerpt: "AWS Settings & Installation"
last_modified_at: 2019-04-26T12:00:00
redirect_from:
  - /theme-setup/
toc: true
---

## AWS 인스턴스 생성 및 구동하기  
먼저 AWS 루트 사용자 로그인을 진행합니다.  
로그인 후 인스턴스 생성을 위해 다음 과정을 진행합니다.  

- "AWS Management Console - 솔루션 구축 : EC2 사용하여"를 진행합니다.  

![시작: 솔루션 구축](https://user-images.githubusercontent.com/47657715/56702818-0111f500-6741-11e9-8686-64f4a0b74c6a.png)

  


### 단계 1: Amazon Machine Image(AMI) 선택  
- "Ubuntu Server 18.04 LTS (HVM), SSD Volume Type -ami- (64비트(x86))"를 선택합니다.

![단계 1: AMI 선택](https://user-images.githubusercontent.com/47657715/56702865-2999ef00-6741-11e9-906a-7cc6ca81b5f5.png)

  


### 단계 2: 인스턴스 유형 선택  

- "t2.micro" 유형을 선택 후 "검토 및 시작"을 진행합니다.

![단계 2: 인스턴스 유형 선택](https://user-images.githubusercontent.com/47657715/56702902-636af580-6741-11e9-8e28-c06131186a03.png)

  


### 단계 7: 인스턴스 시작 검토  

- 선택 사항을 확인 후 시작하기를 통해 다음 단계를 진행합니다.

![단계 7: 인스턴스 시작 검토](https://user-images.githubusercontent.com/47657715/56703073-2fdc9b00-6742-11e9-8053-d57944fbb32c.png)

  


- 기존 키 페어 선택 또는 새 키 페어 생성을 진행합니다.

- 기존에 사용하는 키 페어가 없는 경우 새 키 페어 생성 후 키 페어 다운로드를 진행합니다. 

- 키 페어 이름을 설정한 뒤 프라이빗 키 파일(*.pem)을 다운로드 후 인스턴스를 시작합니다.

![기존 키 페어 선택 또는 새 키 페어 생성](https://user-images.githubusercontent.com/47657715/56703160-a2e61180-6742-11e9-8039-5f32696a4949.png)

  


- 인스턴스 시작 후 인스턴스 탭에서 생성된 인스턴스의 정보를 확인하고, 인스턴스 보기를 진행하여 생성한 인스턴스를 확인합니다.

![인스턴스 정보 확인](https://user-images.githubusercontent.com/47657715/56703219-d7f26400-6742-11e9-82f1-e5c5156da25d.png)

  


- 인스턴스가 생성이 되어 시작 중(pending) 상태에서 사용할 준비가 되면 실행 중(running) 상태로 변경됩니다.

![인스턴스 보기](https://user-images.githubusercontent.com/47657715/56703279-0a03c600-6743-11e9-86e7-aa75f519a05e.png)

  


- 생성한 인스턴스의 IPv4 퍼블릭 IP을 이용하여 인스턴스에 접속이 가능합니다. (pem키를 생성하여 보관, pem키를 통하여 접속)

![IPv4 퍼블릭 IP 확인](https://user-images.githubusercontent.com/47657715/56703330-3c152800-6743-11e9-83b8-814bdac5cf1a.png)

  


- 다음 명령어를 통해 생성한 인스턴스로 접속할 수 있습니다.  

-----------------------------  
```shell
$ sudo ssh -i [KEY_PAIR_NAME].pem ubuntu@[IPv4 퍼블릭 IP]  
```
-----------------------------  

![인스턴스 접속](https://user-images.githubusercontent.com/47657715/56703430-b5147f80-6743-11e9-877e-2b769593d779.png)

  



## kops 설치 및 kubectl 설치  


### kops 설치  
kops는 Kubernetes 클러스터를 만들어 주는 CLI 도구이며, 실행 파일 하나만 다운 받으면 됩니다.  

-----------------------------  
```shell
$ wget -O kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64  
$ chmod +x ./kops  
$ sudo mv ./kops /usr/local/bin/  
```  
-----------------------------  

![kops 설치](https://user-images.githubusercontent.com/47657715/56703639-cc07a180-6744-11e9-91d8-3d8377ca6a66.png)

  


### kubectl 설치  
kubectl은 이미 만들어져 있는 Kubernetes 클러스터를 관리하기 위한 도구이며, 다음을 실행하여 실행 파일 하나만 다운 받으면 됩니다.  

-----------------------------  

```shell
$ wget -O kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl  
$ chmod +x ./kubectl  
$ sudo mv ./kubectl /usr/local/bin/kubectl  
```
-----------------------------  


![kubectl 설치](https://user-images.githubusercontent.com/47657715/56703670-eccff700-6744-11e9-9edb-52eb8ad429ff.png)

  


## IAM 유저 생성  
kops가 동작하기 위해서는 AWS 권한이 필요하고 따라서 적당한 권한을 가지고 있는 IAM 유저를 생성해야 합니다.  

- AWS 웹 콘솔에서 서비스 페이지를 열고 "보안, 자격 증명 및 규정 준수" 탭에서 "IAM"을 선택하여 IAM 유저 생성을 진행합니다.  


![IAM 유저 생성 01](https://user-images.githubusercontent.com/47657715/56703795-6cf65c80-6745-11e9-85a6-f0d0d699fe68.png)

  

- "사용자" 탭에서 "사용자 추가"를 진행합니다.  


![IAM 유저 생성 02](https://user-images.githubusercontent.com/47657715/56703833-a7f89000-6745-11e9-8026-f1784b026778.png)

  

- kops라는 사용자 이름을 추가하고 그룹에 사용자 추가를 진행하여 다음 권한을 가지고 있는 kops라는 그룹을 생성합니다.

![IAM 유저 생성 03](https://user-images.githubusercontent.com/47657715/56704144-fce8d600-6746-11e9-8f0a-98662c01f6be.png)

![IAM 유저 생성 04](https://user-images.githubusercontent.com/47657715/56704158-0c681f00-6747-11e9-8b12-76aea3b616df.png)

  

검색창에 다음 정책들을 검색하여 권한을 설정합니다.  

-----------------------------  
AmazonEC2FullAccess  
AmazonRoute53FullAccess  
AmazonS3FullAccess  
IAMFullAccess  
AmazonVPCFullAccess  

-----------------------------  

![IAM 유저 생성 05](https://user-images.githubusercontent.com/47657715/56704208-3cafbd80-6747-11e9-9f52-1c91591c9734.png)



- kops 그룹에 속한 kops라는 유저를 만들고 Access key ID와 Secret access key를 저장해 둡니다.

![IAM 유저 생성 06](https://user-images.githubusercontent.com/47657715/56704242-5a7d2280-6747-11e9-9c08-a18f0d3f7bc8.png)


![IAM 유저 생성 07](https://user-images.githubusercontent.com/47657715/56704292-85677680-6747-11e9-8f89-3f189cb746fd.png)

  


- 사용자 만들기를 진행하여 액세스 키 ID와 비밀 액세스 키를 확인 후 저장해 둡니다.

![IAM 유저 생성 08](https://user-images.githubusercontent.com/47657715/56704330-ab8d1680-6747-11e9-9880-a34c59cf5297.png)


![IAM 유저 생성 09](https://user-images.githubusercontent.com/47657715/56704438-10e10780-6748-11e9-853a-0f54ea699a33.png)

  

- 다음과 같이 kops 그룹에 kops 사용자가 추가되어 있는 것을 확인할 수 있습니다.

![IAM 유저 생성 10](https://user-images.githubusercontent.com/47657715/56704462-29512200-6748-11e9-9ea9-1e83d9ce0787.png)

![IAM 유저 생성 11](https://user-images.githubusercontent.com/47657715/56704465-2d7d3f80-6748-11e9-8ed0-04580845fd95.png)



## AWS CLI 설치  
클러스터를 생성하는 과정에서 AWS CLI가 필요합니다.  

AWS CLI 설치를 위해 다음을 실행하여 설치합니다.  

-----------------------------  
```shell
$ sudo apt update  
$ sudo apt install -y python-pip  
$ pip install awscli  
```
-----------------------------  


설치 후 aws 명령을 찾지 못한 경우 다시 로그인해 봅니다. (인스턴스 재접속)

![AWS CLI 설치](https://user-images.githubusercontent.com/47657715/56705214-0d9b4b00-674b-11e9-818f-0cd41c951930.png)  

  

aws 환경 설정을 위해 aws configure를 입력 후 필요한 항목들을 입력합니다.  

AWS Access Key ID와 AWS Secret Access Key에는 IAM 그룹 및 사용자 설정 시 발급받은 Access Key와 Secret Access Key를 입력합니다.  

-----------------------------  
```shell
$ aws configure  
$ AWS Access Key ID [None]: <Your access key id>  
$ AWS Secret Access Key [None]: <Your secret access key>  
$ Default region name [None]: ap-northeast-2  
$ Default output format [None]:  
```
-----------------------------  


![AWS 환경설정](https://user-images.githubusercontent.com/47657715/56705288-505d2300-674b-11e9-9e27-8ccf26f10a57.png)  

  

환경설정이 제대로 되었는지 다음과 같은 명령어를 통해 확인해 볼 수 있습니다.  

-----------------------------  
```shell
$ aws ec2 describe-instances  
$ aws iam list-users  
```
-----------------------------  

  

![AWS 환경설정 확인 01](https://user-images.githubusercontent.com/47657715/56705373-95815500-674b-11e9-87b7-8eed8984bdc7.png)

![AWS 환경설정 확인 02](https://user-images.githubusercontent.com/47657715/56705377-97e3af00-674b-11e9-8bdf-355ceba7dc78.png)  

  
















