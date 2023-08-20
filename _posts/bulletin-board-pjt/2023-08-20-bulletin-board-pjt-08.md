---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 08"
date: 2023-08-20
last_modified_at: 2023-08-20
excerpt: "Controller와 Validator 나누기, Express App 생성과 설정 나누고 클래스의 인스턴스로 구체화하기"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, controller, validator]
---

---

<br>

# Server

좀 더 유지보수하기 쉽게 하기 위해 구조를 변경했습니다.

의존성 주입이 무엇인지, 어떤 식으로 express에 적용할 수 있는지에 대한 글을 좀 봤습니다.

완벽히 이해할 수는 없었지만, 추상화를 통해 코드를 효율적으로 사용할 수 있는 것 같습니다.

## Express App 생성, 설정

기존에 express 앱에 미들웨어를 붙이고, 라우터를 붙이고, DB를 설정하던 코드들을 전부 클래스로 모았습니다.

### app.js

여기서는 정말 간단하게 서버를 동작시키는 것을 보여줍니다.

```javascript
// app.js
import Server from './server';
import { PORT } from './load-environment';

const server = new Server();

server.listen(PORT);
```

### server.js

express 앱에 필요한 모든 것들을 클래스로 만들어, `app.js`에서 인스턴스로 활용할 수 있게 했습니다.

클래스를 이용해 나중에 인스턴스를 생성할 경우, 자동으로 미들웨어를 설정하고 라우터를 적용할 수 있게 메서드를 만들었습니다.

`CORS`를 적용하는 부분도 따로 모듈로 빼서, 여기서 가져와 적용하는 것으로 했습니다.

DB를 초기화하는 작업도 만들고 싶었지만, MongoDB는 `MongoClient`를 생성해서 연결하는데, 연결하는 방법도 한 가지가 아니고, 어떻게 연결만 할 수 있을지 고민해도 답이 나오지 않아 일단 비워뒀습니다.

```javascript
import express from 'express';
import cors from './cors';
import { PORT } from './load-environment';
import boardRouter from './routes/board';

export default class Server {
  constructor() {
    this.app = express();
    this.initializeDb();
    this.useMiddleware();
    this.useRoutes();
  }

  initializeDb() {}

  useMiddleware() {
    const { app } = this;
    app.use(cors);
    app.use(express.json());
  }

  useRoutes() {
    const { app } = this;
    app.use('/api/board', boardRouter);
  }

  listen(port) {
    const { app } = this;
    app.listen(port);
    console.log(`Listening on port ${PORT}`);
  }
}
```

### cors.js

CORS 설정 관련 코드를 모았습니다.

나중에 인증 관련 헤더 설정 코드도 들어갈 것 같습니다.

```javascript
import cors from 'cors';

const corsOptions = {
  origin: 'http://localhost:5173',
  optionsSuccessStatus: 200,
};

export default cors(corsOptions);
```

### db.js

DB 관련 코드를 넣었습니다.

일단 `db`와 `collection`을 따로 추출할 수 있게 작성했습니다.

```javascript
import { MongoClient } from 'mongodb';
import { MONGODB_URI, MONGODB_DB_NAME } from './load-environment';

const client = new MongoClient(MONGODB_URI);
const db = client.db(MONGODB_DB_NAME);
const boards = db.collection('boards');

export { boards };

export default db;
```

## Controller

라우터를 나누는 것은 좋았는데, 라우터를 나누고도 함수에서 유효성 검사, 비즈니스 로직 구현, 에러 처리 등을 하면 관리하기 힘들어질 것 같아 더 분리하게 되었습니다.

일단 라우터에 controller를 두고, 해당 controller에서 필요하다면 미들웨어로서 validator나 에러를 처리하는 것을 넣을 예정입니다.

### board.js

라우터에 validator와 controller를 추가합니다.

일단 게시글(board) 작성, 수정할 때만 유효성 검사를 진행했습니다.

에러 처리는 기능 구현을 더 한 후에 추가하도록 하겠습니다.

```javascript
import { Router } from 'express';
import * as controller from '../controllers/board-controller';
import * as validator from '../validators/board-validator';

const { getBoardList, createBoard, getBoardDetail, updateBoard, deleteBoard } =
  controller;
const { boardValidator } = validator;

const router = Router();

router.get('/', getBoardList);
router.post('/', boardValidator, createBoard);
router.get('/:boardId', getBoardDetail);
router.put('/:boardId', boardValidator, updateBoard);
router.delete('/:boardId', deleteBoard);

export default router;
```

### board-validator.js

일단 제목, 내용이 0자 이상, 각각 40자와 40,000자 미만으로 작성할 수 있게 제한했습니다.

`next()`를 이용해 제어를 controller로 넘겨줍니다.

```javascript
const boardValidator = async (req, res, next) => {
  const { title, content } = req.body;
  const trimTitle = title.trim();
  const trimContent = content.trim();
  if (trimTitle.length === 0) {
    throw new Error('title의 공백을 제외하고 길이가 0입니다.');
  }
  if (trimTitle.length > 40) {
    throw new Error('title의 공백을 제외하고 길이가 40을 넘습니다.');
  }
  if (trimContent.length === 0) {
    throw new Error('content의 공백을 제외하고 길이가 0입니다.');
  }
  if (trimContent.length > 40_000) {
    throw new Error('content의 공백을 제외하고 길이가 40,000을 넘습니다.');
  }
  return next();
};

export { boardValidator };
```

### board-controller.js

유효성 검사를 했다면 해당 controller로 진입합니다.

여기서 service 부분을 나누고 싶어 분리를 했다가, 아직은 너무 모듈만 많아지고 효율적이지 않은 것 같아서 다시 controller에 붙였습니다.

비즈니스 로직을 service로 나누어 작성하는 것 같은데, 아직 잘 모르겠어서 이 부분은 더 공부할 예정입니다.

또, 에러 처리도 향후 추가할 것입니다.

```javascript
import { ObjectId } from 'mongodb';
import db from '../db';
import { generateCreatedAt } from '../generate-created-at';

const boards = db.collection('boards');

const getBoardList = async (req, res) => {
  const cursor = await boards.find({});
  const boardList = await cursor.toArray();
  return res.send(boardList);
};

const createBoard = async (req, res) => {
  const { title, content } = req.body;
  const { createdDate, createdTime } = generateCreatedAt();
  const newData = { title, content, createdDate, createdTime };
  const result = await boards.insertOne(newData);
  return res.json(result);
};

const getBoardDetail = async (req, res) => {
  const { boardId } = req.params;
  const query = { _id: new ObjectId(boardId) };
  const board = await boards.findOne(query);
  return res.json(board);
};

const updateBoard = async (req, res) => {
  const { boardId } = req.params;
  const { title, content } = req.body;
  const filter = { _id: new ObjectId(boardId) };
  const updateData = { $set: { title, content } };
  const result = await boards.updateOne(filter, updateData);
  return res.json(result);
};

const deleteBoard = async (req, res) => {
  const { boardId } = req.params;
  const query = { _id: new ObjectId(boardId) };
  const result = await boards.deleteOne(query);
  return res.json(result);
};

export { getBoardList, createBoard, getBoardDetail, updateBoard, deleteBoard };
```

<br>
<br>

# Client

서버에만 집중했습니다.

<br>
<br>

# 느낀점

백엔드 부분을 작업하면서, 코드가 점점 길어지게 되니 모듈로 분리를 해야겠다는 생각이 저절로 들었습니다.

Express로 서버를 다루는 것은 처음이라, 모듈을 어떻게 나누고, 어떤 부분을 Express에 있는 기능으로 나눠야 할지 많이 고민됐습니다.

일단 Middleware를 이용하고, 개념적인 Router, Controller를 구체화해 나눴습니다.

비즈니스 로직은 따로 Service로 나눠야 한다는데, 코드가 짧아서 그런지 아직은 괜찮았습니다.

이 작업들과 연관된 것이 소프트웨어 아키텍처인지 모르겠지만, 필요성이 느껴졌습니다.

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/30fa8ee524151547837443d75e9f97b77293e2cd)

---
