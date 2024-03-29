---
title: Express에서 Router를 이용해 코드 분리하기
date: 2023-08-15 00:00:00 +0900
last_modified_at: 2023-08-18 00:00:00 +0900
categories: [Express]
tags: [express, nodejs, router]
---

모든 코드를 app.js에 작성할 수 없기 때문에 Router를 이용해 코드를 모듈로 분리해 효율적으로 관리해보겠습니다.

## Router

Express로 서버 개발을 진행하면서, 기능이 점점 늘어남에 따라 모든 코드를 `app.js`에만 작성하기에는 불편했습니다.

한 파일의 코드가 길어지면서 가독성이 떨어지면서 관리가 힘들어졌습니다.

그래서 Express에서는 어떻게 파일을 나눌 수 없나 찾아보다가, Router를 이용하면 목적을 달성할 수 있다는 것을 알게 되었습니다.

```javascript
// app.js
app.get("/api/board", (req, res) => {});
app.post("/api/board", (req, res) => {});
app.get("/api/board/:boardId", (req, res) => {});
app.update("/api/board/:boardId", (req, res) => {});
app.delete("/api/board/:boardId", (req, res) => {});
```

위 예시를 이용하겠습니다.

## express.Router

Express에서는 `express.Router`를 이용해 라우터를 다른 모듈로 분리할 수 있습니다.

```javascript
// board-router.mjs
import { Router } from "express";

const router = Router();

router.use((req, res, next) => {
  console.log("before /api/board");
  return next();
});

router.get("", (req, res) => {});
router.post("", (req, res) => {});
router.get("/:boardId", (req, res) => {});
router.put("/:boardId", (req, res) => {});
router.delete("/:boardId", (req, res) => {});

export default router;
```

`express.Router` 클래스를 이용해 마운트 가능한 모듈러식 라우트 핸들러를 생성합니다.

`Router` 인스턴스는 완전한 미들웨어 및 라우팅 시스템이 됩니다.

`router.use()`를 이용해 해당 라우터의 고유한 미들웨어를 사용합니다.

`router.메서드()`를 이용해 `app.메서드()` 처럼 사용할 수 있습니다.

앞의 URL은 필요한 만큼 생략합니다. (`'/api/board/:boardId'` -> `'/:boardId'`)

```javascript
// app.js
import express from "express";
import boardRouter from "./routes/board-router";

app.use("/api/board", boardRouter);

app.listen(PORT, () => console.log(`Listening on port ${PORT}`));
```

이제 위 코드처럼 `app.js`에서 해당 라우터 모듈을 불러와 `app.use()`에 연결합니다.

## app.route()

```javascript
app
  .route("/api/board")
  .get((req, res) => {})
  .post((req, res) => {})
  .put((req, res) => {})
  .delete((req, res) => {});
```

위 코드처럼 `app.route()`를 이용해 그냥 `app.메서드()`를 사용하는 것보다 코드를 좀 더 줄일 수도 있습니다.

## 참고

> [Routing](https://expressjs.com/en/guide/routing.html)

> [[node.js] 라우팅 설정 및 Controller분리](https://velog.io/@ysg81/node.js-%EB%9D%BC%EC%9A%B0%ED%8C%85-%EC%84%A4%EC%A0%95-%EB%B0%8F-Controller%EB%B6%84%EB%A6%AC)
