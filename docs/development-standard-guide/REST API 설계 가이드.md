---
layout: default
title: REST API 설계 가이드
parent: 개발 표준 가이드 Development Standard Guide
nav_order: 100
last_modified_date: 2021-04-25 03:24:00
last_modified_at: 2021-04-25 03:24:00
---

# REST API 설계 가이드
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

클라이언트와 서버간에 통신하기 위한 규약

## URL 규칙

### URL HOST 작성 규칙

여러개의 도메인 사용을 지양하고, 서브도메인으로 각 서비스를 구분하여 사용한다.

| service name | url |
| auth | **auth**.gumicode.com |
| discovery | **discovery**.gumicode.com |
| config | **config**.gumicode.com |
| blog | **blog**.gumicode.com |

### URL ENDPOINT 네이밍 규칙

| 내용 | 샘플 |
| 기본 Resource는 <code>복수형 명사</code> 사용  | gumicode.com/**accounts** |
| 가급적 명사를 사용하되 <code>컨트롤자원</code>의 경우 예외적으로 허용  | gumicode.com/posts/**duplicate** |
| 두 단어 이상의 연결은 <code>-</code> 사용 | gumicode.com/**dormant-accounts** |
| 개별 리소스를 지정할때 <code>PathVariable</code> 사용 | gumicode.com/accounts/**{accountId}** |
| 개별 리소스에 추가 액션을 지정 할때 <code>/</code> 후 작성  | gumicode.com/accounts/{accountId}/**friends** |
| 중첩 리소스가 <code>3개 이상</code> 은 분리 하는것을 권장  | <code>권장안함 X</code> gumicode.com/**order/{orderId}/items/list** |
| URL 리소스에는 <code>소문자</code> 만 사용한다.  | <code>권장안함 X</code> gumicode.com/**dormantAccounts** |
| 마지막에는 <code>/</code> 를 포함하지 않는다.  | <code>권장안함 X</code> gumicode.com/**accounts/** |
| verion은 url Path 에 포함한다. 미 포함시 <code>최신버전</code> 사용 | gumicode.com/**v1/accounts** |
| 조회 필터링 사용시 <code>QueryParm</code> 활용 | gumicode.com/**accounts?name=abc** |
| 조회 정렬 사용시 <code>QueryParm</code> 활용 | gumicode.com/**accounts?sort=name,desc** |
| 조회 페이징 사용시 <code>QueryParm</code> 활용 | gumicode.com/**accounts?page=1&size=1** |

## Method 사용시 규칙

### Method 종류와 특징

| 메소드 | operation | 멱등성 여부 |
| GET | read | must |
| POST | create | must not |
| PUT | replace, state change | must |
| PATCH | update | should |
| DELETE | delete | must | 

#### 리소스 수정시 무엇을 사용해야 할까 ?

리소스 수정시 PATCH 를 사용 한다.

다만 HTTP 초창기에는 GET, POST 만 있었기 때문에 현재도 POST 를 사용하는 곳이 많다. 오래된 시스템의 경우 GET, POST 만 지원하는 경우도 있으므로 적절하게 사용하면 된다. POST 를 사용했다고 해서 표준에 위반되는것은 아니다. 그렇다고 해서 굳이 오래된 규약을 사용할 필요는 없다.

#### PUT 은 언제 사용할까 ?

리소스를 덮어씌울 때 사용 한다.

PATCH 와 POST 는 리소스를 일부분 수정할 때 사용하지만, PUT 은 완전히 덮어씌울 때 사용한다. 그렇기에 리소스가 없는 상태에서 PUT 을 사용할 경우 생성도 할 수 있다. 일반적으로 기존 리소스를 삭제하고 새로 업로드 해야 할 때 많이 사용한다. 예를 들어 프로필사진 변경시 단순히 이미지만 교체하는것이 아니라 기존 데이터를 삭제후 이미지를 변경 한다.

### collection

컬렉션 규약에 따라 아래처럼 메소드를 사용한다.

| 조건 | 메소드 | URL |
| 회원 목록 | GET | /members |
| 회원 등록 | POST | /members |
| 회원 개별 조회 | GET | /members/{id} |
| 회원 수정 | PATCH | /members/{id} |
| 회원 덮어쓰기 | PUT | /members/{id} |
| 회원 삭제 | DELETE | /members/{id} |


### data foramt

| 메소드 | 내용 |
| GET | body 데이터를 사용하지 않는다. QueryParm 만 활용한다. 응답은 json body 를 사용한다. |
| POST,PUT,PATCH,DELETE | QueryParm 사용을 지양한다. body 를 적극 사용한다. 응답은 json body 를 사용한다. |

### GET 예시

GET 을 사용할 땐 request body 에 값을 입력하지 않는다. 조건을 추가할려면 queryParam 을 추가하여 작성한다.

```
GET gumicode.com/accounts?name=abc
```

### POST 예시

POST 을 사용할 땐 request body 에 json 형태의 body를 넣을 수 있다. 대신 url 에는 qeuryParam 사용을 지양한다.

```
POST gumicode.com/accounts
Content-Type: application/json
{
    "name":"abc"
}
```

## Http Header

| 조건 | 설명 | 허용 |
| Content-Type | 클라이언트가 요청하는 포맷 | application/json, multipart/fom-data 만 허용한다. |
| Accept | 클라이언트가 받을 수 있는 응답 포맷 | application/json 만 허용 |