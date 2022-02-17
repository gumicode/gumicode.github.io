---
layout: default
title: AWS CodeBuild
parent: 아마존 웹서비스 AWS
nav_order: 102
last_modified_date: 2021-02-18
---

# AWS CodeBuild
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

정해진 규칙에 따라 소스 코드를 빌드하기 위한 서비스이다. 

CI/CD 중 Continuous Integeration (CI:지속적 통합) 을 담당하는 부분으로, 주로 소스코드를 컨테이너 이미지로 패키징을 자동화 할 때 사용한다. 해당 서비스는 단독으로 사용할 수 없으며 주로 [AWS CodeCommit](AWS%20CodeCommit){: target="_blank"} 과 [AWS ECR](AWS%20ECR){: target="_blank"} 을 연동하여 사용한다.

## 대표적인 서비스

동일한 포지션의 대표적인 서비스는 아래와 같다. 

- jenkins

## AWS CodeCommit 과 AWS ECR 과 연동하기

CodeBuild는 심플하게 생각하면, git 레파지토리 (github, CodeCommit 등.. ) 에서 소스코드를 가져와 내가 사전에 입력한 명령어를 그대로 실행해주는 서비스이다. 쿠버네티스를 사용중이라면 아래와 같은 일련의 과정의 목적으로 사용한다.

1. git 에서 소스코드를 clone 한다.
2. docker 빌드 명령어를 실행한다. 이때 소스코드에 Dockfile 를 미리 정의해 두어야 한다.
3. 생성된 컨테이너 이미지를 컨테이너 저장소에 업로드한다.

이번 장에서 소개할 내용은, git 저장소를 AWS CodeCommit 을 사용하고 컨테이너 저장소를 AWS ECR 를 사용하는 방법이다.

### 1. 예제 소스 코드 준비

[aws-code-build-example](https://github.com/gumicode/aws-code-build-example){: target="_blank"}

소스코드를 내려받아 .git 정보를 지우고 본인의 [AWS CodeCommit](AWS%20CodeCommit){: target="_blank"} 에 업로드 한다.

### 2. 예제 소스 코드 분석

예제 소스코드는 Java Spring Boot 프로젝트이다. 코드는 아래와 같이 매우 단순한 프로젝트 이다. 단 여기서 중요한 부분은 <code>buildspec.yml</code> 파일이다.

**HomeController.java**
```java 
@RestController
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "success home";
    }
}
```

**buildspec.yml**
```yml 
version: 0.2

env:
  variables:
    AWS_ACCESS_KEY_ID: # AWS IAM 에서 발급한 KEY ID 입력
    AWS_SECRET_ACCESS_KEY: # AWS IAM 에서 발급한 SECERT 입력
    AWS_ACCOUNT_ID: # 숫자로 이루어진 계정 ID,  https://console.aws.amazon.com/billing/home?#/account 접속후 가장 상단에 있는 계정 ID
    AWS_REGION: ap-northeast-2 # 리전 정보 , 서울리전의 경우 ap-northeast-2
    IMAGE_NAME: aws-code-build-example # 컨테이너 이미지 이름
    TAGS: latest # 컨테이너 이미지 tags

phases:
  pre_build:
    commands:
      - echo Entered the pre_build phase...
      - export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
      - export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
      - aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
    finally:
      - echo This always runs even if the login command fails
  build:
    commands:
      - echo Entered the build phase...
      - ./gradlew bootBuildImage --imageName=${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}:${TAGS}
    finally:
      - echo This always runs even if the install command fails
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`
      - docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}:${TAGS}

cache:
  paths:
    - '/root/.m2/**/*'
```

[buildspec 빌드 사양 참조](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html){: target="_blank"}

AWS CodeBuild 의 핵심은 바로 <code>buildspec.yml</code> 이다. 이 파일은 내가 입력한 명령을 CodeBuild 에서 실행 해 주는 역할을 수행한다. 위 파일은 아마존 공식 문서를 보고 직접 작성 하였다.

- **version** : buildspec 의 버전이다. 현재 최신버전은 <code>0.2</code>
- **env.variables** : 터미널 명령에 사용할 환경변수를 지정할 수 있다. 위 설명을 읽고 변수에 값을 채우도록 하자.
- **pre_build.commands** : 빌드를 실행하기 전 실행할 명령어를 입력한다. 주로 docker 에 업로드 하기 전 로그인 하기 위해 사용한다.
- **build.commands** : 실제로 docker 를 빌드하는 명령어를 입력한다. 일반적으로 Dockfile 를 정의하여 사용하지만, spring boot 2.3 버전 이후부터는 Dockfile 을 만들지 않아도 빌드하는 방법을 제공한다. gradle 명령어는 <code>bootBuildImage</code> 이다.
- **post_build.commands** : 주로 빌드가 완료된 컨테이너 이미지를 컨테이너 저장소로 업로드할 때 사용한다.

### 3. CodeBuild 프로젝트 만들기

[AWS CodeBuild](https://ap-northeast-2.console.aws.amazon.com/codesuite/codebuild/start?region=ap-northeast-2){: target="_blank"} 를 선택하여 프로젝트 만들기를 선택한다.

![codebuild_01.png](/meta/docs/aws/codebuild_01.png)

**프로젝트 이름**

프로젝트 이름을 입력한다.

**소스 공급자**

AWS CodeCommit 을 선택후 내가 등록한 레포지토리를 선택한다. 참조 유형의 경우 원하는것을 선택하면 되는데, 현재 학습용도로 소개할 것이기 때문에 master 브런치로 선택하여 진행한다.

**환경**

CodeBuild 를 내부적으로 동작시킬 운영체제 환경을 설정하는 부분이다. 나는 무난하게 Ubuntu 를 진행했다. 스프링부트 프로젝트를 빌드하기 위해선 Java Runitme 이 필요한데, 아마존에서 제공해주는 운영체제 이미지에는 이미 자바 런타임이 설치된 채로 배포되고 있어서 따로 추가적인 설정을 하지 않았다. docker 또한 내장되어 있었다. 어떤 프로그램이 미리 설치되어 있는지 확인하고 싶다면 아래 링크를 눌러 확인하면 된다.

[아마존에서 제공하는 관리형 이미지 목록](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-available.html){: target="_blank"}

내가 직접 세팅한 운영체제에서 빌드하고 싶다면 사용자 지정 이미지 기능도 제공한다. 우리는 기본 아마존 관리형 이미지에서 도커로 빌드할 예정이기 때문에 도커 이미지 빌드 권한 부분을 반드시 체크하고 진행해야 한다.

**Buildspec 이름**

핵심적인 부분이다. 소스코드 root 디렉토리에 <code>buidspect.yml</code> 이 존재해야 한다. 기본값으로 사용할 경우 따로 입력하지 않아도 된다. 

이후 프로젝트 만들기를 진행한다.

### 4. 빌드 시작

생성한 프로젝트 상세보기에 들어간다.

![codebuild_02.png](/meta/docs/aws/codebuild_02.png)

**빌드시작**

빌드시작 버튼을 누르게 되면 자동으로 빌드가 시작된다. 그러면 아래와 같이 결과가 표시된다. 이후 AWS ECR 저장소에 가면 이미지가 업로드 된 것을 확인할 수 있다. 빌드가 실패했다면, 내가 사용하는 IAM 권한이 충분한지 다시 확인하도록 하자.


## 빌드 자동화 방법

CodeBuild를 실행하기 위해선 '수동'으로 빌드 시작 버튼을 눌러야 한다. 

물론 배치를 통해 특정 시간대마다 동작할 수 있도록 설정할 수는 있지만, 대부분의 사람들은 이 기능을 원한 게 아니라 소스코드를 git에 push 하면 webhook으로 자동 빌드가 시작되는 형태를 원하셨을 것이다. 

이는 AWS CodePipeline에서 지원해 주는 기능이다. Git Commit, Git CodeBuild 세팅이 끝났다면 PipeLine에서 연결만 해주면 간편하게 끝난다. 관련 내용은 [AWS CodePipeline](AWS%20CodePipeline) 페이지를 참고하면 된다.