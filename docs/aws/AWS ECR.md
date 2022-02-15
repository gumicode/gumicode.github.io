---
layout: default
title: AWS ECR
parent: 아마존웹서비스 AWS
nav_order: 100
---

# AWS ECR

AWS ECR 이란 Elastic Container Registry의 약자로써 아마존에서 제공하는 private 컨테이너 저장소이다. 컨테이너 저장소란 컨테이너들을 다운로드할 수 있도록 원격에서 제공해 주는 서비스이다. 컨테이너 기술을 활용한 서비스는 대표적으로 로커와 쿠버네티스가 있다.

컨테이너 저장소는 크게 public 저장소와 private 저장소로 나눌 수 있는데, public 저장소의 경우 공개된 곳에서 누구나 읽고 다운로드할 수 있는 반면 private는 권한이 있어야 읽고 다운로드할 수 있다. public 저장소는 주로 오픈소스 컨테이너를 공유할 때 사용하고, private 저장소는 회사 내에서 공개해서는 안 되는 서비스를 사용할 때 사용한다.

대표적인 public 컨테이너 저장소
- docker hub
- github container registry

대표적인 private 컨테이너 저장소
- harbor
- AWS ECR (Elastic Container Registry)
- GCR (Google Container Registry)

컨테이너 기반의 쿠버네티스 CI/CD 구축을 위해서 컨테이너 저장소가 반드시 필요하지만 공개된 저장소에 컨테이너를 올릴 수 없으므로, 비공개 컨테이너 저장소를 사용할 수밖에 없다. GCP, AWS, Azure 같은 클라우드 서비스를 이용한다면 클라우드에서 제공해 주는 비공개 저장소를 사용하는 것을 권장한다. 오픈소스로 직접 구축하는 것보다 학습곡선이 적으며 요금도 저렴한 편이다.

## 생성하기

### 1. 레포지토리 생성하기 
https://ap-northeast-2.console.aws.amazon.com/ecr/create-repository

**표시여부 설정**

프라이빗과  퍼블릭을 선택할 수 있는데 프라이빗을 선택하면 된다. 퍼블릭의 경우 아마존에서 제공하는 공개 컨테이너 저장소 https://gallery.ecr.aws/ 에 공유되어 다운로드 받을수 있게 된다.

**리포지토리 이름**

하나의 리포지토리에는 image 이름이 동일하고 tag 가 다른 이미지만 업로드할 수 있으므로 식별 가능한 어플리케이션 이름을 지정하는것이 좋다. 

Docker hub 의 [Mysql Repository](https://hub.docker.com/_/mysql) 를 보면 이해할 수 있다. Mysql Repository 안에는 아래와같은 tags 가 존재한다.

- latest
- 8.0.28
- 8.0
- 8
- 5.7.37
- 5.7
- 5

즉 서비스 이름은 동일하고 tag 로 versioning 을 할 때 사용하는 단위가 레파지토리 이므로, 햇갈리지 않도록 본인이 서비스 하는 어플리케이션 이름을 입력한다. 만약 회사에서 사용하는 서비스가 5개라면 5개의 레파지토리를 생성해야 한다.

### 2. AWS CLI 설치하기

본인의 컴퓨터에서 ECR 로 이미지를 push 하기 위해서는 [AWS CLI](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/install-cliv2.html) 를 반드시 설치해야 한다. CLI 설치 이후 [credential](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) 설정 하는 부분도 잊지 않도록 하자. 

### 3. AWS IAM ECR 정책 추가

서비스 Elastic Container Registry 읽기 쓰기 권한 설정을 주어야 한다. 만약 정책 권한을 잘 모르겠다면 아래 JSON 을 참고 하면 된다. 편의상 ecr: * 을 사용했다. 
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

### 4. 푸시 명령 보기

이제 사전 준비는 끝났으므로 ECR 레파지토리를 클릭해서 안으로 접근하면 "푸시명령보기" 라는 버튼이있을 것이다. 그 버튼을 누르게 되면 터미널에서 사용해야 하는 명령어가 있으므로 그대로 진행 하면 된다. 당연히 도커 이미지는 미리 준비 하셔야 한다.


### 마무리

위 방법을 순서대로 진행 하셨다면 ECR에 정상적으로 컨테이너 이미지가 업로드 되었을 것이다. 하지만 AWS CLI 를 설치하고, 도커 이미지를 빌드하고 터미널에서 push하는 행위를 직접 하는것은 생각외로 귀찮은 일이다. 이후 AWS EKS CI/CD 편에서 다루겠지만 이 조차도 자동화 하여 사용 하는 방법을 소개할 예정이다. 