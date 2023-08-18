---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 06"
date: 2023-08-16
last_modified_at: 2023-08-18
excerpt: "MongoDB 연결, Router 분리, env 분리"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, mongodb, route, env]
---

---

<br>

# Server

mongoDB를 연결하는 코드를 작성하니, 점점 `app.js`의 코드 길이가 늘어나 슬슬 가독성이 떨어졌습니다.

mongoDB 연결 코드를 `app.js`에 다 넣었다가, 어떻게 분리할 방법이 없는지 고민했었습니다.

그래서 DB 연결은 `conn.mjs`로 분리하는 방법을 찾았습니다.

또, `app.js`에서 모든 라우트 관리를 하지 않고 따로 뺄 수 없나 고민하다가 방법을 찾게되었습니다.

그리고 환경변수도 로드하는 부분을 모듈로 따로 작성해 효율적으로 들여올 수 있게 했습니다.

### app.js

모듈은 `load-environment.mjs`에서 가져오고, 라우터는 `boardRouter`로 가져옵니다.

그리고 `app.use()`를 이용해 라우터를 연결합니다.

여기서 DB 관련된 코드는 존재하지 않습니다.

```javascript
// app.js
import express from 'express';
import cors from 'cors';
import { PORT } from './load-environment';
import boardRouter from './routes/board';

const app = express();

const corsOptions = {
  origin: 'http://localhost:5173',
  optionsSuccessStatus: 200,
};

app.use(cors(corsOptions));
app.use(express.json());
app.use('/api/board', boardRouter);
app.listen(PORT, () => console.log(`Listening on port ${PORT}`));
```

### load-environment.mjs

환경변수 세팅하는 부분을 따로 모듈로 작성했습니다.

```javascript
import dotenv from 'dotenv';

dotenv.config();

export const { PORT } = process.env;
export const { MONGODB_URI } = process.env || '';
export const { MONGODB_DB_NAME } = process.env;
```

### conn.mjs

DB에 연결하는 부분을 따로 작성합니다.

```javascript
import { MongoClient } from 'mongodb';
import { MONGODB_URI, MONGODB_DB_NAME } from './load-environment';

const client = new MongoClient(MONGODB_URI);
const db = client.db(MONGODB_DB_NAME);

export default db;
```

### routes/board.mjs

Board 관련 로직을 작성했습니다.

코드가 너무 길어져서, 핸들러는 내용을 작성하지 않았습니다.

깃허브 링크에서 누락되지 않은 코드를 확인할 수 있습니다.

```javascript
import express from 'express';
import db from '../conn.mjs';

let tempDB = ['hi'];

const router = express.Router();

// /api/board
router.get('', async (req, res) => {});

// /api/board
router.post('', (req, res) => {});

// /api/board/:boardId
router.get('/:boardId', (req, res) => {});

// /api/board/:boardId
router.put('/:boardId', (req, res) => {});

// /api/board/:boardId
router.delete('/:boardId', (req, res) => {});

export default router;
```

<br>
<br>

# Client

Server 부분만 진행했습니다.

<br>
<br>

# 느낀점

어떻게 MongoDB에 연결할 수 있을지 알아봤습니다.

`app.js`에만 코드를 몰아서 작성했을 때, 코드가 길어지면서 점점 보기가 힘들었었는데, 모듈로 분리하니 기능별로 파악이 잘 되고 관리하기가 쉬웠습니다.

DB 연결하는 부분이 비동기인지 동기인지 헷갈렸는데, 서버를 실행하면서 `console.log`를 사용하고, 공식문서를 찾아본 결과 위에서 작성했던 코드는 동기로 연결을 진행하는 것 같습니다.

`connet()`를 사용해 비동기적으로 진행하는 코드도 봤었는데, 일단 현재 코드는 동기인 것 같습니다.

에러 처리 부분은 나중에 진행하겠습니다.

에러가 발생하면 DB 서버를 꺼야하는지 궁금합니다.

코드는 진행하면서 더 다듬을 예정입니다.

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/73053e1b5e684792298e99582f9f52ecb6e2bb0a)

---
