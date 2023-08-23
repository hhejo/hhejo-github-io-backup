---
title: "Node.js의 HTTP 모듈 정리"
date: 2023-08-23
last_modified_at: 2023-08-23
excerpt: "HTTP 모듈에서 자주 쓰는 함수와 메서드에 대해 예제와 함께 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, http]
---

---

<br>

# HTTP Module

`http` 모듈은 Node.js 내장 모듈이기 때문에 별도의 설치 없이 활용할 수 있습니다.

```javascript
const http = require('node:http');
```

## 서버 생성

`createServer([options][, requestListener])`

서버 인스턴스를 생성합니다.

```javascript
const server = http.createServer();
```

## 서버 연결

`listen([options][, listeningListener])`

```javascript
server.listen(3000, () => { console.log('Listening on port 3000'); });
```

### 서버 연결 방식 1

`listen()`을 함께 써줍니다.

```javascript
http.createServer((req, res) => {
  // 요청을 처리하고 응답하기
}).listen(3000);
```

### 서버 연결 방식 2

`listen()`을 따로 쓰고, `on()`을 이용해 요청을 처리합니다.

```javascript
const server = http.createServer();
server.on('request', (req, res) => {
  // 요청을 처리하고 응답하기
});
server.listen(3000, () => { console.log('Listening..'); });
```

## 요청과 응답 작업하기

`writeHead()`를 이용해 응답 헤더를 설정합니다.

`setHeader()`를 사용할 수도 있습니다.

`write()`를 이용해 응답 스트림에 씁니다.

`end()`를 이용해 응답을 전송합니다.

```javascript
http
  .createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ data: 'Hello, world!' }));
  })
  .listen(3000, () => { console.log('Listening on port 3000') } );
```

### writeHead와 setHeader의 차이

`writeHead()`는 상태 코드, 여러 헤더 프로퍼티를 설정할 수 있습니다.

```javascript
res.writeHead(200, {
  'Content-Type': 'text/plain;charset=UTF-8',
  'Content-Language': 'ko',
  'Access-Control-Allow-Origin': '*',
});
```

`setHeader()`는 하나의 헤더 프로퍼티를 설정합니다.

```javascript
res.setHeader('Content-Type', 'application/json');
res.setHeader('Content-Language': 'ko');
res.setHeader('Access-Control-Allow-Origin', '*');
res.statusCode = 200;
```

## 요청 URL에 따라 작업하기

`request.url`을 이용해 특정 URL에 따라 작업할 수 있습니다.

```javascript
if (req.url === '/') {
  // / 관련 작업
} else if (req.url === '/api') {
  // /api 관련 작업
}
```

<br>
<br>

# 참고

> [HTTP](https://nodejs.org/api/http.html)

> [Difference between response.setHeader and response.writeHead?](https://stackoverflow.com/questions/28094192/difference-between-response-setheader-and-response-writehead)

---
