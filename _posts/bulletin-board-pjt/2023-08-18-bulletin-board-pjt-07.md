---
title: React, Vite, Node.js, Express를 이용해 게시판 만들기 07
date: 2023-08-18 00:00:00 +0900
last_modified_at: 2023-08-18 00:00:00 +0900
categories: [BulletinBoardPjt]
tags:
  [bulletin-board-pjt, javascript, nodejs, express, react, router, controller]
---

Router와 Controller 분리, 백엔드, 프론트엔드 코드 다듬기

## Server

Router에서 Controller를 또 따로 모듈로 작성했습니다.

그래서 좀 더 각각의 로직에 집중할 수 있게 했습니다.

코드도 다듬어 정확하게 작성했습니다.

```javascript
// app.js
import express from "express";
import cors from "cors";
import { PORT } from "./load-environment";
import boardRouter from "./routes/board-router";

const app = express();

const corsOptions = {
  origin: "http://localhost:5173",
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
app.use(express.json());

app.use("/api/board", boardRouter);

app.listen(PORT, () => console.log(`Listening on port ${PORT}`));
```

```javascript
// board-router.mjs
import { Router } from "express";
import * as controller from "../controllers/board-controller";

const router = Router();

router.get("/", controller.getBoardList);
router.post("/", controller.createBoard);
router.get("/:boardId", controller.getBoardDetail);
router.put("/:boardId", controller.updateBoard);
router.delete("/:boardId", controller.deleteBoard);

export default router;
```

```javascript
// board-controller.mjs
import { ObjectId } from "mongodb";
import db from "../conn";
import { generateCreatedAt } from "../generate-created-at";

const boards = db.collection("boards");

const getBoardList = async (req, res) => {
  const cursor = await boards.find({});
  const boardList = await cursor.toArray();
  return res.send(boardList);
};

const createBoard = async (req, res) => {
  const { title, content } = req.body;
  const { createdDate, createdTime } = generateCreatedAt();
  const newData = { title, content, createdDate, createdTime };
  const createResult = await boards.insertOne(newData);
  return res.json(createResult);
};

const getBoardDetail = async (req, res) => {
  const { boardId } = req.params;
  const query = { _id: new ObjectId(boardId) };
  const boardDetail = await boards.findOne(query);
  return res.json(boardDetail);
};

const updateBoard = async (req, res) => {
  const { boardId } = req.params;
  const { title, content } = req.body;
  const filter = { _id: new ObjectId(boardId) };
  const updateDocument = { $set: { title, content } };
  const updateResult = await boards.updateOne(filter, updateDocument);
  return res.json(updateResult);
};

const deleteBoard = async (req, res) => {
  const { boardId } = req.params;
  const query = { _id: new ObjectId(boardId) };
  const deleteResult = await boards.deleteOne(query);
  return res.json(deleteResult);
};

export { getBoardList, createBoard, getBoardDetail, updateBoard, deleteBoard };
```

`findOne`을 사용할 때, `_id`를 찾는 방법을 몰라서 시간이 걸렸습니다.

mongosh로 확인할 때, `_id`는 `ObjectId`라고 하는데, 이것을 코드에서 비교할 때 어떻게 사용할지 몰랐습니다.

`mongodb.ObjectId`를 이용할 수 있었습니다.

`new` 연산자를 이용하는 것도 잊지 않아야 합니다.

## Client

MongoDB에 맞춰 코드를 다시 작성하고, 리팩터링을 진행했습니다.

## 느낀점

`findOne`에서 `ObjectId`에 막혀 시간이 좀 걸렸었습니다.

모듈로 나누고 코드를 개선하니 깔끔해지고 좋았습니다.

다음에는 미들웨어를 이용해 코드를 좀 더 개선하고, 에러도 처리하겠습니다.

## 현재까지 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/e2ea0ad48ce980d03bd79b324765a5b6c6c720cb)
