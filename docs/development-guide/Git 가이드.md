---
layout: default
title: Git 가이드
parent: Development Guide
nav_order: 200
last_modified_date: 2021-04-28 01:31:00
last_modified_at: 2021-04-28 01:31:00
---

# Git 가이드
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

협업을 위한 Git 가이드

## Git 원격 저장소 도구

[Github](https://github.com) 를 사용한다. 상황에 따라 다른 저장소를 사용할 수 있다.

## 저장소 이름 규칙

- 저장소 이름은 **소문자**만 사용한다. 
- 두개 이상의 단어로 연결 될 경우 <code>-</code> 를 사용한다. 
- 가능하다면 **한단어** 사용을 권장한다.

**ex)** shopping-live
{: .fs-5 .ls-10 .code-example }

- **팀명** 혹은 **부서명** 이 있다면 가장 앞 자리에 배치 한다.

**ex)** teamstudy-shopping
{: .fs-5 .ls-10 .code-example }

## commit 가이드

- commit 앞에는 명사로 구성된 유형을 **접두어**로 붙입니다.
    - <code>feat:</code> 새로운 기능 추가
    - <code>fix:</code> 버그 픽스
    - <code>docs:</code> 사이트 / 문서 업그레이드
    - <code>refactor:</code> 리팩토링
    - <code>style:</code> 코드 포맷팅
    - <code>test:</code> 테스트 코드 추가
    - <code>merge:</code> 브랜치 병합
    - <code>chore:</code> 기타 변경사항, 다른 목적
- commit 제목은 ticket id, 접두어, 간략한 내용으로 구성합니다.
- commit 내용은 생략해도 됩니다. 필요시 작성은 자유입니다.
- 다른 ticket id 의 경우에는 commit 을 분리하여 체계적으로 작업하도록 합니다.

**ex)** ABC-10 feat: 사용자 생성 API 추가
{: .fs-5 .ls-10 .code-example }

**ex)** ABC-16 refactor: 올바르지 않은 변수명 변경
{: .fs-5 .ls-10 .code-example }

## branch 가이드

실무에서 사용하는 서버 운영 전략은 아래 3개의 형태가 존재한다. 이 형태에 따라서 Branch 전략이 나뉠 수 있다.

- **develop** : 말 그대로 개발용 서버 환경이다. 승인 없이 마음껏 서버를 내리고 올리거나 테스트 할 수 있다.
- **staging** : 실제 프로덕션 환경과 완벽하게 동일하지만, 프로덕션 환경은 아니다. 프로덕션으로 배포하기 전 서비스가 이상 없는지 테스트 하기 위한 서버 환경이다. 여러 팀과 협업중이라면 본인이 담당하고 있는 서버를 함부로 내릴경우 상호작용에 문제가 생기므로 마음대로 내려서는 안된다.  
- **production** : 실제 유저가 접근하고 상호작용하는 운영 환경이다. 

아래에 설명하겠지만 CI/CD 구축 자동화를 통해 **병합 한다는것 자체가 곧 배포**를 의미한다.

### (공통) 개인 branch 네이밍 룰

- 브런치 이름은 **jira ticket** 이름을 접두어로 붙어야 한다. 
- **소문자**만 입력한다. 
- ticket 이름에 대문자가 포함되어 있다면 **소문자로 변경**한다.
- 가장 앞에는 **feature/** 를 붙인다. 
- ticket 이름 뒤에는 **간단한 작업 내용**을 추가 한다.

**ex)** abc-123 ( X )
{: .fs-5 .ls-10 .code-example }

**ex)** feature/abc-123 ( X )
{: .fs-5 .ls-10 .code-example }

**ex)** feature/ABC-123-user-api-update ( X )
{: .fs-5 .ls-10 .code-example }

**ex)** feature/abc-123-user-api-update ( O )
{: .fs-5 .ls-10 .code-example }

### 축소형 ( 1단계 )

production 환경 1개를 운영할 경우 사용하는 전략이다.

- 실제 서비스가 아닌 프로젝트에서 사용하는 브랜치 전략이다. 
- 스터디나 팀 프로젝트 같이 규모가 작은 경우 선택하기에 적합하다. 
- 실무 프로젝트가 아니기에 production 환경에 문제가 생겨도 큰 문제가 없을때 사용한다.
- 가장 합리적인 비용으로 사용할 수 있다.

#### 공용 branch 

| branch | 설명 | 병합 방법 |
|:--|:--|:--|
| main | production branch | PR 코드리뷰 이후 |

#### branch commit 순서

1. 최신버전의 <code>main branch</code> 를 내려 받는다.
2. jira 에서 본인이 받은 ticket ID로 브런치를 만들고 앞에 feature 를 붙인다. **예시** : <code>feature/ABC-10-api-update</code>
3. 코드를 다 작성한 후 <code>pull request</code> 를 요청한다.
4. 팀원들의 코드리뷰를 받고 승인을 받으면 <code>main branch</code> 에 병합한다. 
5. 성공적으로 병합된 브런치는 리모트에서 제거한다.

### 중간형 ( 2단계 )

dev 환경, production 환경 2개를 운영할 때 사용하는 전략이다. 

- 스타트업 초창기 형태에서 사용하기에 적합하다. 
- 실무에서 사용할 수 있다. 
- 서로 상호작용 할만한 다른 팀이 없을때 사용하기 적합하다. 

#### 공용 branch 전략

| branch | 설명 | 병합 방법 |
|:--|:--|:--|
| main | production branch | QA 이후 |
| develop | develop branch | PR 코드리뷰 이후 |

#### 개인 branch 전략

1. 최신버전의 <code>develop branch</code> 를 내려 받는다.
2. jira 에서 본인이 받은 ticket ID로 브런치를 만들고 앞에 feature 를 붙인다. **예시** : <code>feature/ABC-10-api-update</code>   
3. 코드를 다 작성한 후 <code>pull request</code> 를 요청한다.
4. 팀원들의 코드리뷰를 받고 승인을 받으면 <code>develop branch</code>  에 병합한다. 
5. 성공적으로 병합된 브런치는 리모트에서 제거한다.
6. QA 이후 <code>develop branch</code> 에서 <code>main branch</code> 으로 병합 한다. 

### 대형 (3단계)

dev 환경, staging 환경, producrtion 환경 3개를 운영할 때 사용하는 전략이다.

- 규모가 있는 회사에서 사용하기 적합하다.
- 안정적으로 서비스를 운영할 수 있다.

#### 공용 branch 전략

| branch | 설명 | 병합 방법 |
|:--|:--|:--|
| main | production branch | QA 이후 |
| staging | staging branch | 팀 채널에 통보 이후 |
| develop | develop branch | PR 코드리뷰 이후 |

#### 개인 branch 전략

1. 최신버전의 <code>develop branch</code> 를 내려 받는다.
2. jira 에서 본인이 받은 ticket ID로 브런치를 만들고 앞에 feature 를 붙인다. **예시** : <code>feature/ABC-10-api-update</code>   
3. 코드를 다 작성한 후 <code>pull request</code> 를 요청한다.
4. 팀원들의 코드리뷰를 받고 승인을 받으면 <code>develop branch</code>  에 병합한다. 
5. 성공적으로 병합된 브런치는 리모트에서 제거한다.
6. staging 배포가 필요하다면 팀 채널에 통보후 <code>develop branch</code> 에서 <code>staging branch</code> 으로 병합 한다. 
7. QA 이후 <code>staging branch</code> 에서 <code>main branch</code> 으로 병합 한다. 

## 코드리뷰

코드리뷰란 공용 branch 에 코드를 병합 하기 전에 PR 을 등록하여 리뷰를 받는 행위

코드리뷰는 귀찮더라도 반드시 진행하는것이 좋다. 코드리뷰 단계에서 서로 몰랐던것을 알게 되거나 새로운 지식을 습득하는 경우가 많다. 다만 팀원이 항상 대기하고 있는 상태가 아니므로 PR 이후 슬랙을 통하여 리뷰 요청 메세지를 전달하는것이 좋다.

### 예시 이미지

![code-review](/meta/docs/development-guide/pull-request.png)

github 페이지에 보면 Pull Reuqest 라는 메뉴가 있다. 줄여서 **PR** 이라고 한다. 
내가 생성한 branch 혹은 fork branch 에 code 를 commit 한 이후 본 프로젝트에 병합하고자 할 때 PR 을 통해 권한이 있는 사람에게 승인을 받고 코드를 병합할 수 있다. 
회사나 팀에서 협업으로 프로젝트를 할 경우에는 같은 소속의 팀원의 몇명 이상에게 승인을 받고 병합하거나 팀장에게 승인을 받은후 병합되는 룰을 가져가는 경우가 많다.

### 코드리뷰 양식

PR 양식은 정해진 것은 없다. 어떤 작업을 했는지 읽기 편하게 정리하여 작성하면 된다. 

### 코드 병합시

pull request 로 코드 병합시 <code>merge</code> 대신 <code>rebase</code> 사용을 권장한다.

## version 정책

- major.minor.patch 형태의 버전 규칙을 따른다. 

**ex)** v1.1.5
{: .fs-5 .ls-10 .code-example }

- 버전마다 최대 2자리까지 가질 수 있다. 99가 넘어간다면 앞 자리 버전을 올린다.

**ex)** v2.99.99
{: .fs-5 .ls-10 .code-example }

- 서비스가 완전히 개편될때 **major** 버전을 올린다.
- 새로운 기능이 추가될때 **minor** 버전을 올린다.
- 기능을 수정하거나 버그를 수정한 경우 **patch** 버전을 올린다.
- 기능 추가와 버그를 수정을 동시에 배포한 경우 큰 버전의 규칙을 따라 간다. 그러나 동시에 진행하지않고 별도로 버전을 분기하여 업데이트 하는것을 권장 한다.
- 버전은 prodcutin 환경 ( main branch ) 를 배포할때 **tag** 를 함께 진행하여 배포한다.