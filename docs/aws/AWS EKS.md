---
layout: default
title: AWS EKS
parent: 아마존 웹서비스 AWS
nav_order: 105
last_modified_date: 2021-02-24 02:50:00
last_modified_at: 2021-02-24 02:50:00
---

# AWS EKS
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

**Elastic Kubernetes Service (EKS)** 아마존에서 쿠버네티스 클러스터를 구축하기 위한 서비스

쿠버네티스 서비스를 이용하는 방법에는 **클라우드 서비스를 이용**하는것과 **직접 구축**하여 사용 할 수 있는데, 쿠버네티스를 직접 구축하여 사용하는것은 고도의 하드웨어 인프라 지식이 필요하므로 클라우드 관리자가 아닌 이상 대게로 클라우드 서비스를 이용하게 된다. 학습 용도로 간단한 기능들은 직접 구축하여 사용해 볼 수 있지만 기능이 제한이므로 완전한 쿠버네티스기능을 모두 사용해 보고자 한다면 클라우드 서비스를 이용하는것이 좋다. 참고로 구글 클라우드의 경우 쿠버네티스 엔진을 3개월동안 무료로 사용할 수 있다.

## 대표적인 서비스

동일한 포지션의 대표적인 서비스는 아래와 같다.

- kubernetes
- GKS (Google Kubernetes Engine) : 구글 클라우드 쿠버네티스 서비스
- AKS (Azure Kubernetes Service) : 애저 클라우드 쿠버네티스 서비스

## 클러스터 생성하기

**AWS CLI** 를 사용하여 EKS 클러스터를 생성할 것이다. AWS 사이트 콘솔에서 생성하지 않는 이유는 터미널에서 kubectl 를 사용하기 위한 권한 설정이 매우 까다롭기 때문이다.

### 1. IAM 역할 생성 하기

![eks_01.png](/meta/docs/aws/eks_01.png)

AWS IAM -> 역할 -> AWS 서비스 -> EKS Cluster 생성 역할을 생성한다. 역할 이름을 기억해 둔다.

### 2. CLI를 사용할 사용자에게 권한 부여

AWS CLI 를 통하여 쿠버네티스를 사용하기 위해서는 1번에서 생성한 역할을 컨트롤 할 수 있는 권한과, EKS 를 사용할 수 있는 권한을 부여해야 한다. 

![eks_02.png](/meta/docs/aws/eks_02.png)

**EKS 사용 권한 부여**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        }
    ]
}
```

**role 사용 권한 부여**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::108429674457:role/*"
        }
    ]
}
```

### 3. EKS 클러스터용 VPC 생성하기

아마존에서 기본으로 생성된 VPC는 EKS 클러스러를 생성하기에 알맞지 않다. 

[AWS EKS 클러스터용 VPC 생성](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/creating-a-vpc.html)를 참고하여 별도의 VPC 를 생성하자.

### 4. AWS CLI 으로 EKS 클러스터 생성

자세한 가이드 문서는 [AWS EKS 클러스터 생성](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/create-cluster.html) 을 참고

커맨트명령어에 아래 명령어를 적절히 수정하여 입력한다.

```
aws eks create-cluster \
   --region ap-northeast-2 \ 
   --name {클러스터이름} \ 
   --kubernetes-version 1.21 \
   --role-arn arn:aws:iam::{AWS계정아이디}:role/{IAM역할} \
   --resources-vpc-config subnetIds={서브넷ID},{서브넷ID},securityGroupIds={보안그룹ID}
```

- region : 리전
- name : 원하는 클러스터 이름 입력 
- kubernetes-version : 쿠버네티스 버전
--role-arn : {AWS계정아이디}는 숫자로 된 본인의 AWS 아이디 이다. {IAM역할}은 1번에서 생성한 역할의 이름을 입력한다.
--resources-vpc-config : 마스터노드의 {서브넷ID}와 {보안그룹ID}을 설정한다.

여기서 주의할 점은 <code>--resources-vpc-config</code> 의 경우 마스터 노드 영역 이기 때문에 서브넷의 경우 public이 아닌 private 서브넷을 지정하는것이 좋다. 관련 옵션 내용은 [여기](https://docs.aws.amazon.com/cli/latest/reference/eks/create-cluster.html#options)를 참고하길 바란다. [AWS EKS 클러스터 생성](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/create-cluster.html) 옵션을 을 따랐다면 자동으로 생성된 private 서브넷 2개를 지정하면 된다. (반드시 2개 이상을 선택해야 한다.) 보안그룹 또한 자동으로 생성된 보안그룹을 지정한다.

만약 <code>An error occurred (AccessDeniedException) when calling the CreateCluster operation:</code> 와 같이 클러스터 생성 권한이 없다는 메세지가 발생 할 경우 2번을 정상적으로 수행 했는지 다시한번 확인하자. IAM 권한 추가의 경우 실제 적용되는데 몇분이 걸리므로 적용한지 얼마 되지 않았다면 잠깐 기다렸다가 진행 해 보자.

### 5. aws-auth ConfigMap 적용

AWS Console 에서 생성한 EKS 클러스터를 선택하면 **현재 사용자 또는 역할이 이 EKS 클러스터에 있는 Kubernetes 객체에 액세스할 수 없습니다.** 와 같은 경고 문구를 볼 수 있다. 이는 사용자 계정으로 클러스터를 생성했기 때문에 루트 계정 콘솔로 조회가 안되기 때문에 역할 권한을 추가해 주어야 한다.

[configMap 추가](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/add-user-role.html#aws-auth-configmap) 링크를 참고하도록 하자.


### 6. EKS 클러스터 터미널 연결

바로 생성 직후라면 자동으로 연결되어 있겠지만, 세션이 만료 되었을 경우 아래처럼 재접속 할 수 있다.

```
aws eks --region ap-northeast-2 update-kubeconfig --name {클러스터이름}
```

**중요** EKS 는 노드를 생성하지 않더라도, 클러스터 생성시 시간당 0.1$ 고정 비용이 발생 하므로 실제 운영할 계획이 없다면 반드시 삭제하도록 하자