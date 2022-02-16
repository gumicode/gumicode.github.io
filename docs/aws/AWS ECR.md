---
layout: default
title: AWS ECR
parent: 아마존 웹서비스 AWS
nav_order: 101
last_modified_date: 2021-02-16
---

# AWS ECR
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

Elastic Container Registry 아마존 컨테이너 저장소

주로 비공개 컨테이너 저장소로 사용한다. 공개 저장소로 사용할 수는 있으나 docker hub 같은 훨씬 방대한 서비스가 있으므로 해당 용도로는 자주 사용되진 않는다. AWS ECS 혹은, AWS EKS (아마존 쿠버네티스) 를 사용 중이며 CI/CD 구축을 하고자 한다면 컨테이너 저장소로 사용할 수 있다. harbor 같은 오픈소스 컨테이너 저장소를 직접 구축해서 사용해도 되지만 AWS 서비스를 사용하고 있다면 클라우드에서 제공해 주는 컨테이너 저장소를 사용하는 것이 편리하다.

## 대표적인 서비스

동일한 포지션의 대표적인 서비스는 아래와 같다. 

- docker hub
- github container registry
- GCR (Google Container Registry)
- harbor

## 생성 하기

### 1. 레포지토리 생성하기 
[https://ap-northeast-2.console.aws.amazon.com/ecr/create-repository](https://ap-northeast-2.console.aws.amazon.com/ecr/create-repository)

![ecr_01.png](/meta/docs/aws/ecr_01.png)

**표시여부 설정**

프라이빗과  퍼블릭을 선택할 수 있는데 프라이빗을 선택하면 된다. 퍼블릭의 경우 아마존에서 제공하는 공개 컨테이너 저장소 [AWS ECR Gallery](https://gallery.ecr.aws/) 에 공유되어 다운로드 받을수 있게 된다. 

**리포지토리 이름**

하나의 리포지토리에는 image 이름이 동일하고 tag 가 다른 이미지만 업로드할 수 있으므로 식별 가능한 어플리케이션 이름을 지정해야 한다.

Docker hub 의 [Mysql Repository](https://hub.docker.com/_/mysql) 를 보면 이해할 수 있다. Mysql Repository 안에는 아래와같은 tags 가 존재한다.

- latest
- 8.0.28
- 8.0
- 8
- 5.7.37
- 5.7
- 5

즉 서비스 이름은 동일하고 tag 로 versioning 을 할 때 사용하는 단위가 레파지토리 이므로, 햇갈리지 않도록 본인이 서비스 하는 어플리케이션 이름을 입력한다. 만약 업데이트 하고자 하는 서비스가 5개라면 각각 5개의 레파지토리를 생성해야 한다.

### 2. AWS CLI 설치 및 인증정보 설정

AWS ECR 에 컨테이너 이미지를 업로드 하기 위해 AWS CLI를 설치해야 한다. 설치가 완료 되었다면 IAM 인증 정보도 함께 설정해야 한다.

**CLI 설치 방법**

[AWS CLI](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/install-cliv2.html) 링크

**IAM 인증 정보 설정 방법**

[AWS Credential](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) 링크

### 3. AWS IAM 정책 추가

IAM에서 사용자를 생성하여 인증정보를 설정했다고 하더라도 아마존은 각각의 서비스마다 권한을 따로 추가해 주어야 하므로 ECS를 사용하기 위해 추가적으로 권한을 부여해야 한다. 물론 IAM에서 모든 관리자 권한을 주면 제약 없이 사용할 수 있지만 보안상 좋은 방법은 아니므로 직접 권한을 추가하는 것이 좋다.

만약 정책 권한을 잘 모르겠다면 아래 JSON 을 참고 하면 된다. 편의상 <code>ecr: *</code> 을 사용했다. IAM JSON 정책에서 사용할 수 있다.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "ecr:*",
            "Resource": "*"
        }
    ]
}
```

### 4. Push 하기

생성한 레포지토리를 클릭하여 상세 페이지로 접속한다.

![ecr_02.png](/meta/docs/aws/ecr_02.png)

**푸시명령보기**

해당 버튼을 클릭하면 각 단계별로 업로드 하는 방법에 대해 자세하게 나와있다.

1. AWS CLI 를 이용하여 패스워드를 가져온뒤 docker 에 로그인 한다.

해당 터미널을 성공적으로 실행하기 위해서는 당연히 도커를 실행중 상태이어야 하며, 2번~3번을 정상적으로 수행한 상태이어야 한다.

2. 소스코드에 루트에 있는 <code>Dockfile</code> 에 정의한 대로 컨테이너를 생성한다.
3. ECR에 업로드 하기 위하여 컨테이너 이미지 이름을 변경한다. <code>~~~.dkr.ecr~.com</code> 이 부분이 ecr private 저장소 주소다.
4. 컨테이너를 push 한다.

## 자동화 CI/CD 

1~4번 행위를 매번 반복하는 행위는 힘든 일이다. 이걸 자동화 해주는 방법이 바로 AWS pipeline 이라는 기능이다. 단, 파이프라인을 사용하기 위해서는 AWS CodeCommit, AWS CodeBuild, AWS CodeDeploy 에 대한 기초 사용법을 알아야 하므로 다음 편에서 다루도록 하겠다.