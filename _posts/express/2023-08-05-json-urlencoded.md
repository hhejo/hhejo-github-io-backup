---
title: Express에서 클라이언트의 요청 데이터 처리하기
date: 2023-08-05 00:00:00 +0900
last_modified_at: 2023-08-18 00:00:00 +0900
categories: [Express]
tags: [express, nodejs, middleware, payload, mime]
---

요청 데이터를 처리하는 Express 미들웨어 json(), urlencoded(), text(), raw() 에대해 알아보겠습니다.

## Request Payload

클라이언트에서 Express 서버로 전송하는 요청에 데이터를 넣는 경우가 있습니다.

대표적으로 `POST` 요청이 있을 것입니다.

클라이언트에서 전송한 요청의 페이로드를 서버에서 구문 분석하기 위해, Express의 내장 미들웨어 기능을 사용해야 합니다. 이 기능들은 `body-parser`를 기반으로 합니다.

해당 기능을 사용하지 않으면, `req.body`의 값은 `undefined`이 나오게 됩니다.

```javascript
import express from "express";

const app = express();
```

```javascript
app.post(API_URL, (req, res) => {
  console.log(req.body); // undefined
});
```

> **Middleware**
>
> 공통 서비스 및 기능을 애플리케이션에 제공하여 개발자와 운영자가 애플리케이션을 더욱 효율적으로 구축하고 배포하도록 돕는 소프트웨어입니다.

> **Payload**
>
> 전송의 근본적인 목적이 되는 데이터의 일부분으로, 그 데이터와 함께 전송되는 헤더와 메타데이터와 같은 부분은 제외합니다.
>
> ```json
> {
>   "status": "OK",
>   "data": {
>     "message": "Hello, world!"
>   }
> }
> ```
>
> 여기서 `"Hello, world!"`가 클라이언트가 관심을 가지는 페이로드가 됩니다.

> **MIME 타입**
>
> 클라이언트에게 전송된 문서의 다양성을 알려주기 위한 메커니즘입니다.
>
> 웹에서 파일의 확장자는 별 의미가 없으므로, 각 문서와 함께 올바른 MIME 타입을 전송하도록 서버가 정확히 설정하는 것이 중요합니다.
>
> `type/subtype`으로 표현합니다. 대소문자를 구분하지 않지만, 전통적으로 소문자를 씁니다.
>
> `text/plain`, `text/html`, `image/png`, `application/json`, `application/octet-stream`, `application/xml`, `application/x-www-urlencoded`, `multipart/form-data` 등이 있습니다.

## 1. express.json()

`express.json([options])`

`Content-Type` 헤더와 `type` 옵션이 일치하는 요청만 처리하고, JSON만 파싱하는 미들웨어를 리턴합니다.

## 2. express.urlencoded()

`express.urlencoded([options])`

`Content-Type` 헤더와 `type` 옵션이 일치하는 요청만 처리하고, urlencoded된 body만 파싱하는 미들웨어를 리턴합니다.

### form 태그의 Content-Type

> application/x-www-form-urlencoded

`&`으로 분리되고, `=` 기호로 값과 키를 연결하는 key-value tuple로 인코딩되는 값입니다.

HTML `form` 태그의 `enctype` 속성을 따로 명시하지 않으면 기본값으로 설정됩니다.

> multipart/form-data

모든 문자를 인코딩하지 않습니다.

x파일, 이미지를 서버로 전송할 때 주로 사용합니다.

> text/plain

공백 문자(space)는 `+`로 변환하지만, 나머지 문자는 모두 인코딩하지 않습니다.

## 3. express.text()

`express.text([option])`

`Content-Type` 헤더와 `type` 옵션이 일치하는 요청만 처리하고, 문자열만 파싱하는 미들웨어를 리턴합니다.

## 4. express.raw()

`express.raw([option])`

`Content-Type` 헤더와 `type` 옵션이 일치하는 요청만 처리하고, Buffer만 파싱하는 미들웨어를 리턴합니다.

## 참고

> [express()](https://expressjs.com/en/4x/api.html#express)

> [express.js 에서 POST 데이터 처리하기](https://kasterra.github.io/handle-POST-data-in-express/)

> [페이로드 (컴퓨팅)](<https://ko.wikipedia.org/wiki/%ED%8E%98%EC%9D%B4%EB%A1%9C%EB%93%9C_(%EC%BB%B4%ED%93%A8%ED%8C%85)>)

> [MIME 타입](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

> [POST](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods/POST)

> [HTTP: Content-Type 에 대해 알아보자](https://jw910911.tistory.com/117)

> [HTTP multipart/form-data 란?](https://velog.io/@shin6403/HTTP-multipartform-data-%EB%9E%80)
