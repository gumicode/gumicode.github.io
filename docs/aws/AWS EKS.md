---
layout: default
title: AWS EKS
parent: 아마존 웹서비스 AWS
nav_order: 105
last_modified_date: 2021-03-02 06:15:00
last_modified_at: 2021-03-02 06:15:00
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

## 생성하기

### 1. VPC 생성하기

[AWS EKS 클러스터 VPC 생성](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/creating-a-vpc.html)를 참고하여 EKS에 알맞은 VPC 를 생성한다.

### 2. CreateCluster 역할 생성

클러스터를 생성하기 위해서 IAM 역할을 생성해야 한다. 생성 방법은 아래와 같다.

AWS IAM -> 역할 -> AWS 서비스 -> EKS Cluster 생성 역할을 생성한다.

![eks_01.png](/meta/docs/aws/eks_01.png)

### 3. 클러스터 생성

[AWS EKS](https://console.aws.amazon.com/eks)를 선택하여 클러스터 생성하기 버튼을 누른다.

**1단계 클러스터 구성**

- **이름** : 적당한 클러스터 이름을 선택한다.
- **Kubernetes 버전** : 적당한 버전을 선택한다. 기본으로 선택되는 버전을 권장
- **클러스터 서비스 역할** : 2번에서 생성했던 역할을 선택한다.

그 외에는 기본값으로 지정한다.

**2단계 네트워크 지정**

- **VPC** : 1번에서 생성한 VPC를 선택한다.
- **서브넷** : private 서브넷 2개를 선택한다. 해당 옵션은 쿠버네티스를 관리하는 master 노드를 위치할 서브넷을 선택 하는것이다. 실제 워커노드를 배치할 서브넷을 선택하는것이 아니므로 혼동하지 않도록 하자
- **보안그룹** : 자동으로 생성되었던 보안그룹을 선택한다. 관련 내용은 [참고](https://docs.aws.amazon.com/eks/latest/userguide/sec-group-reqs.html)

그 외에는 기본값으로 지정한다. 생성에는 약 10분 이상 소요된다.

### 4. Amazon VPC CNI 설정

기본적으로 EKS 에서는 Amazon VPC CNI 를 사용한다. 하지만 보안설정이 되어 있어 추가 설정을 하지 않으면, 포드간의 통신이 불가능하므로 기능이 정상적으로 동작하지 않는다.

#### 1. OpenID Connect 공급자URL 복사

[AWS EKS](https://console.aws.amazon.com/eks)로 이동하여 생성한 클러스터를 선택후 구성, 세부정보로 들어간다.

![eks_02.png](/meta/docs/aws/eks_02.png)

**OpenID Connect 공급자 URL**을 그대로 복사한다.

#### 2. 자격증명공급자 생성

IAM -> 자격증명 공급자 탭에 들어가서 공급자 추가를 누른다. OpenID Connect 를 선택한다.

![eks_03.png](/meta/docs/aws/eks_03.png)

이후 복사한 URL 를 입력하고 대상에 <code>https://sts.amazonaws.com/</code> 을 추가한다.

#### 3. CNI 역할 생성

IAM 역할 생성에 들어간다. 

![eks_04.png](/meta/docs/aws/eks_04.png)

웹 자격증명을 선택후 방금 생성한 자격증명 공급자를 선택하고 다음을 선택후, <code>AmazonEKS_CNI_Policy</code> 권한을 추가한뒤 역할 생성 버튼을 누른다.

**신뢰관계 편집**

방금 생성한 역할을 선택하여 신뢰정책 편집 버튼을 누른다.

<code>StringEquals</code> 아래에 있는 key value 를 아래와 같이 수정한다.

**수정전**
```
"oidc.eks.ap-northeast-2.amazonaws.com/id/094C0CE2730BED0260CA1C7674DA43A9:aud": "https://sts.amazonaws.com"
```

**수정후**
```
"oidc.eks.ap-northeast-2.amazonaws.com/id/094C0CE2730BED0260CA1C7674DA43A9:sub": "system:serviceaccount:kube-system:aws-node"
```

#### 4. EKS CNI 역할 변경

[AWS EKS](https://console.aws.amazon.com/eks)로 이동하여 생성한 클러스터를 선택후 구성, 추가기능에 들어간다 .

이후 vpc-cni 를 선택후 편집을 눌러 방금 생성한 역할로 변경 한다.

![eks_05.png](/meta/docs/aws/eks_04.png)

이후 업데이트를 진행하면 CNI 권한 설정은 완료된다.

## 워커노드 역할 생성

[워커노드 역할생성 가이드](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html#create-worker-node-role)를 참고하여 생성한다.

3개의 정책을 추가하면 된다. <code>AmazonEKSWorkerNodePolicy</code>, <code>AmazonEC2ContainerRegistryReadOnly</code>, <code>AmazonEKS_CNI_Policy</code>

**자주 발생하는 오류**
```
aws eks docker: network plugin is not ready: cni config uninitialized
```

이 문구는 대체로 AWS 에서 지원하는 CNI 를 사용했을 때 발생하는데, 포드가 배포된 VPC 보안그룹 에서 443 포트를 허용시켜야 한다. 그래도 동작하지 않는다면 **4. EKS CNI 역할 변경** 부분이 정상적으로 적용됬는지 확인해야 한다.


## AWS CLI 으로 kubectl 접근하기

AWS Console 로 EKS 를 생성했다면, root 계정으로 쿠버네티스 마스터 권한이 있으므로 해당 계정으로 접근해야 한다.

IAM 대시보드에서 루트 사용자 **액세스 키 관리**에 들어가여 루트 사용자의 키를 획득후 AWS CLI에 접근한다.


**명령어를 입력하여 ARN 이 root로 정상적으로 잡혀 있는지 확인 한다.**
```
aws sts get-caller-identity
```

**EKS 에 접근한다.**
```
aws eks --region ${region} update-kubeconfig --name ${cluster_name}
```

만약 권한이 없다는 메세지가 나온다면, 루트 계정으로 접근한건지 다시한번 확인하자.


## 다른 사용자에게 kubectl 권한 부여하기

아마존 쿠버네티스 클러스터는 생성한 유저가 마스터 권한을 가지며, 이 외의 계정은 접근을 할 수 없으므로 권한을 부여해야 한다. 일단 EKS 를 생성한 루트계정으로 kubectl 에 접근해야 한다.

cinfigmap 에 접근한다.
```
kubectl edit -n kube-system configmap/aws-auth
```

만약 <code>Error from server (NotFound): configmaps "aws-auth" not found</code> 와 같은 에러가 난다면 [AWS cinfigmap 적용](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/add-user-role.html) 페이지를 참고하여 추가하면 된다.


mapRoles: 과 같은 라인에 아래 문구 추가

```
  mapUsers: |
    - userarn: {사용자 ARN}
      username: bs
      groups:
        - system:masters
```

특정 사용자에게도 마스터 권한이 주어져 kubectl 를 접근할 수 있게 된다.