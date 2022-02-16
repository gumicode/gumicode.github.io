---
layout: default
title: AWS CodeBuild
parent: 아마존 웹서비스 AWS
nav_order: 102
last_modified_date: 2021-02-17
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

CI/CD 중 Continuous Integeration (CI:지속적 통합) 을 담당하는 부분으로, 주로 소스코드를 컨테이너 이미지로 패키징을 자동화 할 때 사용한다. 해당 서비스는 단독으로 사용할 수 없으며 주로 [AWS CodeCommit](AWS%20CodeCommit) 과 [AWS ECR](AWS%20ECR) 을 연동하여 사용한다.

## 대표적인 서비스

동일한 포지션의 대표적인 서비스는 아래와 같다. 

- jenkins

## AWS CodeCommit 과 AWS ECR 과 연동하기

아마존 쿠버네티스 서비스를 구축하고자 한다면 해당 방법으로 주로 사용될 것이므로 AWS EKS 