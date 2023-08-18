---
title:  "Express.js의 응답 res.send(), res.json(), res.end()의 차이점"
date: 2023-08-04
last_modified_at: 2023-08-18
excerpt: "세 메서드는 각각 어떤 차이가 있을까요?"
categories:
  - ExpressJS
tags:
  - [express, nodejs]
---

---

<br>

# Express.js의 응답

```javascript
app.get('/api/test', (req, res) => {
  // ... do something ...
});
```

객체 `res`는 Node.js에 있는 응답 객체의 향상된 버전입니다.

Express를 사용하여 응답을 보내는 방법에는 여러 가지가 있습니다.

# res.send()

`res.send([body])`

`body` 파라미터는 Buffer 객체, 문자열, 객체, 불리언, 배열이 될 수 있습니다.

> [res.send([body])](https://expressjs.com/en/4x/api.html#res.send)

이 메서드는 전달된 인수를 기반으로 응답 헤더(response header)의 `Content-Type`을 자동으로 설정합니다.

자동으로 설정하는 것을 방지하려면, `res.set()`으로 따로 설정해줍니다.

```javascript
res.set('Content-Type', 'text/html');
res.send(Buffer.from('<p>some html</p>'));
```

JSON 데이터 전송을 위해 body에 객체를 넣으면, 응답 헤더의 `Content-Type`은 올바르게 `application/json`이 설정됩니다.

```javascript
res.send({ hello: 'world'}); // 응답 헤더
```

```
Content-Type: application/json; charset=utf-8
```

그렇다면 `res.json()`은 쓰지 않아도 될까요?

<br>
<br>

# res.json()

이 메서드는 JSON 응답을 전송합니다.

> [res.json([body])](https://expressjs.com/en/4x/api.html#res.json)

```javascript
res.send({ hello: 'world'});
```

`res.send()`와 `res.json()`의 주요 차이점은 비 객체를 응답으로 전달할 때 나타납니다.

`res.json()`은 유효한 JSON이 아닌 비 객체(null, undefined 등)도 변환하지만, `res.send()`는 변환하지 않습니다.

또한, `res.json()`은 json replacer와 json space 설정을 사용합니다.

<br>
<br>

# res.end

데이터를 제공하지 않고 응답을 종료하려면 `res.end()`를 사용할 수 있습니다.

> [res.end([data] [, encoding])](https://expressjs.com/en/4x/api.html#res.end)

예로, 404 페이지에 유용합니다.

```javascript
res.status(404).end();
```

일부 응답 데이터를 보내고 싶다면 `res.send()`, `res.json()` 등을 사용합니다. 둘은 응답을 종료하기도 하기 때문에 `res.end()`를 명시적으로 호출할 필요가 없습니다.

<br>
<br>

# 결론

응답에 데이터를 보낼 때, `res.send()` 혹은 `res.json()`을 사용합니다.

`res.send()`로 응답 데이터를 전송하면, 담긴 데이터의 종류에 따라 응답 헤더의 `Content-Type`이 다르게 설정됩니다. JSON 데이터도 문제 없이 보낼 수 있습니다.

`res.json()`은 JSON 데이터를 명시적으로 보내고 싶을 때 사용합니다. 자동으로 응답 헤더의 `Content-Type`의 값을 `application/json`으로 변경합니다.

`res.end()`는 보낼 데이터는 없지만 응답을 종료하고 싶을 때 사용합니다. 위 두 방식 중 하나로 응답을 보낸다면, 이 메서드는 사용할 필요가 없습니다.

<br>
<br>

# 참고

> [Difference between res.json vs res.send vs res.end in Express.js](https://medium.com/gist-for-js/use-of-res-json-vs-res-send-vs-res-end-in-express-b50688c0cddf)

> [Express res.json과 res.send 비교](https://haeguri.github.io/2018/12/30/compare-response-json-send-func/)

> [Express res.send() vs res.json() vs res.end() 비교](https://yohanpro.com/posts/nodejs/express-response)

> [res.json() vs res.send() vs res.end() in Express](https://tpiros.dev/blog/res-json-vs-res-send-vs-res-end-in-express/)

---
