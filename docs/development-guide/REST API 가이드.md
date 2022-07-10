---
layout: default
title: REST API 가이드
parent: Development Guide
nav_order: 100
last_modified_date: 2021-04-27 01:55:00
last_modified_at: 2021-04-27 01:55:00
---

# REST API 가이드
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 개요

협업을 위한 REST API 가이드

## URL 규칙

### URL HOST 작성 규칙

여러개의 도메인 사용을 지양하고, 서브도메인으로 각 서비스를 구분하여 사용한다.

| service name | url |
|:--|:--|
| auth | **auth**.gumicode.com |
| discovery | **discovery**.gumicode.com |
| config | **config**.gumicode.com |
| blog | **blog**.gumicode.com |

### URL ENDPOINT 네이밍 규칙

| 내용 | 샘플 |
|:--|:--|
| 기본 Resource는 <code>복수형 명사</code> 사용  | gumicode.com/**accounts** |
| 명사를 사용하되 <code>컨트롤 자원</code>의 경우 예외적으로 동사 허용  | gumicode.com/posts/**duplicate** |
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
|:--|:--|:--|
| GET | read | must |
| POST | create | must not |
| PUT | replace, state change | must |
| PATCH | update | should |
| DELETE | delete | must | 

**리소스 수정시 PATCH 를 사용 한다.**

다만 HTTP 초창기에는 GET, POST 만 있었기 때문에 현재도 POST 를 사용하는 곳이 많다. 오래된 시스템의 경우 GET, POST 만 지원하는 경우도 있으므로 적절하게 사용하면 된다. POST 를 사용했다고 해서 표준에 위반되는것은 아니다. 그렇다고 해서 굳이 오래된 규약을 사용할 필요는 없다.

**리소스를 덮어씌울 때 PUT 을 사용 한다.**

PATCH 와 POST 는 리소스를 일부분 수정할 때 사용하지만, PUT 은 완전히 덮어씌울 때 사용한다. 그렇기에 리소스가 없는 상태에서 PUT 을 사용할 경우 생성도 할 수 있다. 일반적으로 기존 리소스를 삭제하고 새로 업로드 해야 할 때 많이 사용한다. 예를 들어 프로필사진 변경시 단순히 이미지만 교체하는것이 아니라 기존 데이터를 삭제후 이미지를 변경 한다.

### collection

컬렉션 규약에 따라 아래처럼 메소드를 사용한다.

| 조건 | 메소드 | URL |
|:--|:--|:--|
| 회원 목록 | GET | /members |
| 회원 등록 | POST | /members |
| 회원 개별 조회 | GET | /members/{id} |
| 회원 수정 | PATCH | /members/{id} |
| 회원 덮어쓰기 | PUT | /members/{id} |
| 회원 삭제 | DELETE | /members/{id} |

### GET 예시

GET 을 사용할 땐 요청 body 에 값을 추가하지 않는다. (응답 body 는 무관) 만약 요청시 조건이 필요하다면 queryParam 사용한다.

```
GET gumicode.com/accounts?name=abc
```

응답 받을 수 있는 포맷은 <code>applicaion/json</code> 으로 통일한다.

### POST 예시

POST 요청 body 에 json 형태의 body를 넣을 수 있다. query String 및 PathVariable 를 모두 사용하지 않는다.

```
POST gumicode.com/accounts
Content-Type: application/json
{
    "name":"abc"
}
```

사용 가능한 Content-Type 은 아래 두개로 제한한다.
- <code>application/json</code>
- <code>multipart/fom-data</code>

응답 받을 수 있는 포맷은 <code>applicaion/json</code> 으로 통일한다.

### PUT 예시

PUT을 사용할 땐 요청 body 에 json 형태의 body를 넣을 수 있다. query String 및 PathVariable 를 모두 사용하지 않는다.
PUT 의 경우에는 해당 entity 에 사용하는 모든 값을 넣어야 하며, 키를 생략했을 경우 null 값이 강제로 할당된다. PUT 의 개념은 "덮어쓰기" 이다.
memberId 가 123가 존재 한다면 덮어쓰고, 존재하지 않으면 생성한다.

```
PUT gumicode.com/accounts
Content-Type: application/json
{
    "id" : 123,
    "name":"abc",
    "password" : "abcd1234!"
}
```

사용 가능한 Content-Type 은 아래 두개로 제한한다.
- <code>application/json</code>
- <code>multipart/fom-data</code>

응답 받을 수 있는 포맷은 <code>applicaion/json</code> 으로 통일한다.

### PATCH 예시

PATCH 요청 body 에 json 형태의 body를 넣을 수 있다.  query String 사용을 지양하고 PathVariable 를 사용한다. 
PATCH 의 경우 해당 entity 에 사용하는 모든 값을 넣지 않아도 된다. 키를 생략할 경우 생략된 값은 변경하지 않는다. PATCH 의 개념은 "수정하기" 이다.

```
PATCH gumicode.com/accounts/123
Content-Type: application/json
{
    "name":"gumi"
}
```

사용 가능한 Content-Type 은 아래 두개로 제한한다.
- <code>application/json</code>
- <code>multipart/fom-data</code>

응답 받을 수 있는 포맷은 <code>applicaion/json</code> 으로 통일한다.

### DELETE 예시

DELETE 를 사용할땐 요청 body 에 값을 추가하지 않는다. query String 사용을 지양하고 PathVariable 를 사용한다. 

```
DELETE gumicode.com/accounts/123
```

응답 받을 수 있는 포맷은 <code>applicaion/json</code> 으로 통일한다.

## 인증 프로토콜

인증 프로토콜은 JWT(Json Web Token)을 사용한다. HTTP key value 는 다음과 같다.

| key | value |
|:--|:--|
| Authorization | Bearer {access_token} |

## Response Body

- 응답 데이터는 <code>JSON</code> 으로 응답 한다.
- key 네이밍 포맷은 <code>camel case</code> 를 사용 한다.
- value 값이 null 이거나 빈값인 경우는 최소화 한다. 필요하다면 사용 한다. value 타입이 배열인 경우 null이 아닌 [] 값을 응답 한다.
- 응답 데이터 포맷은 아래와 같은 규칙을 따른다.

### 응답데이터가 없는 경우

```json
{
    "success": true
}
```

### 단일 데이터가 있는 경우
```json
{
    "success": true,
    "data": {
        "id":1,
        "name":"gumi"
    }
}
```

### 데이터가 리스트 인 경우
```json
{
    "success": true,
    "data": [
        {
        "id":1,
        "name":"gumi"
        },
        {
        "id":2,
        "name":"gumi2"
        }
    ]
}
```

### 요청에 실패한 경우(errors가 없는경우)

```json
{
    "success": false,
    "code": "USER_001",
    "message": "이미 중복된 닉네임 입니다."
}
```

### 요청에 실패한 경우(errors가 있는경우)

```json
{
    "success": false,
    "code": "COMMON_001",
    "errors": [
        {
            "field": "username",
            "reason": "username 은(는) 12자 이상 입력해야 합니다."
        },
        {
            "field": "password",
            "reason": "password 은(는) 12자 이상 입력해야 합니다."
        }
    ]
    "message": "요청값이 잘못되었습니다."
}
```

### 오류코드

오류코드는 아래와 같이 문서로 함께 제공하여 사용자가 인지하기 쉽도록 한다.

| code | status | 설명 |
|:--|:--|:--|
| USER_001 | 400 | 회원가입 진행중 중복된 닉네임으로 가입을 시도할 때 |
| COMMON_001 | 400 | API 요청시 올바르지 않은 (정규식에 위배된) 값을 입력한 경우 |

### 날짜 포맷

날짜 포맷은 <code>yyyy-MM-dd HH:mm:ss</code>을 따릅니다.

```json
{
    "success": true,
    "data": {
        "now": "2022-01-01 23:59:59"
    }
}
```

## HTTP Status Code

아래 지정된 status code 만 사용 한다. 

| code | 설명 |
|:--|:--|
| 200 | OK |
| 201 | Created | 
| 204 | No content |
| 304 | Not Modified |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |
| 502 | Bad Gateway |
| 503 | Service Unavailable |

## 개발 전 권고사항

- 프론트엔드에 제공해야 하는 API 는 Mock API 로 작성하여 선 제공한다.
- API 문서는 <code>Spring Rest Docs</code>를 사용하여 제공한다.