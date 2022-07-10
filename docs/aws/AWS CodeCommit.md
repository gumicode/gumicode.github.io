---
layout: default
title: AWS CodeCommit
parent: Amazon Web Service
nav_order: 100
last_modified_date: 2021-02-18 00:00:00
last_modified_at: 2021-02-18 00:00:00
---

# AWS CodeCommit
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

아마존에서 제공하는 비공개 git 저장소 이다.

비공개로 git 저장소를 운영할 때 사용한다. 회사 소스코드를 관리하거나, 상업적 용도로 사용하는 서비스들의 소스코드의 외부 유출을 막기위해 비공개 저장소 용도로 사용한다.

**장점**

- 설치형 오픈소스 git 저장소를 직접 구축하여 운영하는 것보다 학습곡선이 적고, 비용이 매우 저렴한 편이다.
- AWS 인증 시스템을 그대로 가져갈 수 있어 AWS를 사용하고 있는 유저라면 통합된 경험 환경을 제공할 수 있다.
- AWS 기반에서 CI/CD를 구축할 때 편리하게 사용할 수 있다.

**단점**

- AWS 서비스 이용자만 사용할 수 있다.

## 대표적인 서비스

동일한 포지션의 대표적인 서비스는 아래와 같다. 설치형 오픈소스는 본인이 권한 설정을 어떻게 하느냐에 따라 공개용, 비공개용으로 사용할 수 있다.

- GitHub
- GitLab
- AWS CodeCommit
- GCP Cloud Source Repositories
- gogs
- gitea