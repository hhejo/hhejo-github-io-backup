---
title: Express에서 Router, Controller 분리하기
date: 2023-08-18 00:00:00 +0900
last_modified_at: 2023-08-18 00:00:00 +0900
categories: [Express]
tags: [express, nodejs, mongodb, router, controller]
---

Router와 Controller를 분리해 현재의 로직에만 집중할 수 있게 해보겠습니다.

## Router, Controller

저번 글에서 Router를 app에서 분리했지만, Router에서 또 Controller를 따로 분리해 좀 더 효율적으로 모듈을 구성할 수 있습니다.

```javascript
router.get("/", (req, res) => {});
router.post("/", (req, res) => {});
router.get("/:boardId", (req, res) => {});
router.put("/:boardId", (req, res) => {});
router.delete("/:boardId", (req, res) => {});
```

위 코드에서, `(req, res) => {}` 부분을 따로 controller로 구성해보겠습니다.

```javascript
// board-controller.mjs
import db from "../conn";

const boards = db.collection("boards");

const getBoardList = async (req, res) => {};
const createBoard = async (req, res) => {};
const getBoardDetail = async (req, res) => {};
const updateBoard = async (req, res) => {};
const deleteBoard = async (req, res) => {};

export { getBoardList, createBoard, getBoardDetail, updateBoard, deleteBoard };
```

```javascript
// board-router.mjs
import { Router } from "express";
import * as controller from "../controllers/board-controller";

const router = Router();

// /api/board
router.get("/", controller.getBoardList);
router.post("/", controller.createBoard);
// /api/board/:boardId
router.get("/:boardId", controller.getBoardDetail);
router.put("/:boardId", controller.updateBoard);
router.delete("/:boardId", controller.deleteBoard);

export default router;
```

이렇게 Router와 Controller를 분리할 수 있습니다.

## 참고

> [[nodejs] express 라우터기능/ 미들웨어 나누기로 파일 쪼개기](https://kong-dev.tistory.com/132)

> [[express] 라우터, 컨트롤러, 미들웨어로 나누어 관리하기](https://velog.io/@hiro2474/express-router-controller-middleware)
