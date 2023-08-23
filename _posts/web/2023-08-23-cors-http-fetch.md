---
title: "CORS 에러를 어떻게 해결하는지 알아보기"
date: 2023-08-23
last_modified_at: 2023-08-23
excerpt: "HTTP 모듈, Fetch API를 이용해 CORS 에러를 만들어보고, 어떻게 해결할 수 있을지 알아보겠습니다."
categories:
  - Web
tags:
  - [web, cors, http, fetch]
---

---

<br>

# CORS

fetch로 요청을 보낼 사이트가 현재 접속 사이트와 다르면 요청이 실패할 수 있습니다.

도메인, 프로토콜, 포트 3가지가 전부 같아야 같은 Origin이 됩니다.

하나라도 다른 곳에 요청을 보내는 것은 Cross-Origin Request(COR)가 됩니다.

브라우저는 Same-Origin Policy(SOP)를 따르기 때문에, 자바스크립트가 실행한 요청을 서버로 전송한 후, 서버로부터 오는 응답을 SOP에 의해 차단합니다.

## 예시

```javascript
// server.js
// http://localhost:3000
const http = require('node:http');

const server = http.createServer();

server.on('request', (req, res) => {
  const statusCode = 200;
  const headers = {
    'Content-Type': 'application/json;charset=UTF-8',
    'Content-Language': 'ko',
  };
  res.writeHead(statusCode, headers);

  const data = { hello: 'Hi, hi' };
  const jsonData = JSON.stringify(data);
  res.end(jsonData);
});

server.listen(3000, () => {
  console.log(`Server listening on port ${3000}`);
});
```

```html
<!-- index.html -->
<!-- http://localhost:5500 -->
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <h1>Index.html</h1>
  </body>
  <script>
    const runFetch = async () => {
      const url = 'http://localhost:3000';
      const headers = {
        Accept: 'application/json',
        'Accept-Language': 'ko',
      };
      const options = { method: 'GET', headers };
      const response = await fetch(url, options);
    };
    runFetch();
  </script>
</html>
```

위 과정은 실패합니다.

서로 다른 주소.. 즉, 브라우저(`http://127.0.0.1:5500`)에서 서버(`http://localhost:3000`)로, 안전하지 않은 요청(`application/json`)을 보냈기 때문입니다.

```
Access to fetch at 'http://localhost:3000/' from origin 'http://127.0.0.1:5500' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

<br />
<br />

# Safe request

Cross-Origin Request는 안전한 요청(safe request)과 그 이외의 안전한 요청이 아닌 요청으로 나뉩니다.

안전한 요청은 다음과 같은 두 가지 조건 모두를 충족하는 요청입니다.

1. Safe method: GET, POST, HEAD를 사용한 요청
2. Safe header: 다음에 속하는 헤더
   - `Accept`
   - `Accept-Language`
   - `Content-Language`
   - `Content-Type`: `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`

두 가지 조건 모두를 충족하지 못하는 요청은 안전하지 않은 요청(unsafe request)가 됩니다.

<br>
<br>

# 과정과 해결

Cross-Origin 요청을 보낼 경우, 브라우저는 `Origin`이라는 헤더를 추가합니다.

위 예시에서 브라우저의 Origin은 `http://localhost:5500`이 됩니다.

요청이 전송되면, 서버는 요청의 헤더에 있는 `Origin`을 검사하고, 요청을 받아들인다면 `Access-Control-Allow-Origin` 헤더를 응답에 추가합니다.

이 헤더에는 허가된 Origin의 정보나 `*`이 명시됩니다.

응답 헤더 `Access-Control-Allow-Origin`에 Origin 정보나 `*`이 들어있다면 응답은 성공하고, 그렇지 않다면 실패합니다.

브라우저는 Cross-Origin 요청시에 `Origin` 값이 제대로 설정되었는지 확인하고, 이후 서버로부터 받은 응답 헤더에 `Access-Control-Allow-Origin`이 있는지 확인합니다.

그리고 해당 값이 Origin과 일치하거나, `*`인 경우 성공합니다.

위 예시에서, 아래와같이 헤더를 설정하면 성공적으로 응답을 받아옵니다.

```javascript
// server.js
// http://localhost:3000
const http = require('node:http');

const server = http.createServer();

server.on('request', (req, res) => {
  const statusCode = 200;
  const headers = {
    'Content-Type': 'application/json;charset=UTF-8',
    'Content-Language': 'ko',
    'Access-Control-Allow-Origin': 'http://127.0.0.1:5500', // 추가
    // 'Access-Control-Allow-Origin': '*', // 이것도 가능
  };
  res.writeHead(statusCode, headers);

  const data = { hello: 'Hi, hi' };
  const jsonData = JSON.stringify(data);
  res.end(jsonData);
});

server.listen(3000, () => {
  console.log(`Server listening on port ${3000}`);
});
```

서버에서 `Access-Control-Allow-Origin` 헤더를 설정하면 성공적으로 응답을 받아올 수 있습니다.

<br>
<br>

# 참고

> [CORS](https://ko.javascript.info/fetch-crossorigin)

---
