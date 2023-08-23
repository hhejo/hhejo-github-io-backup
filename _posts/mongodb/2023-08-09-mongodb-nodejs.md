---
title: MongoDB를 Node.js 환경에서 사용하기
date: 2023-08-09 00:00:00 +0900
last_modified_at: 2023-08-09 00:00:00 +0900
categories: [MongoDB]
tags: [mongodb, mongosh, nodejs]
---

MongoDB에 연결하고 해당 데이터베이스에 접근하는 코드를 살펴보겠습니다.

## MongoDB Node Driver

애플리케이션에 드라이버를 추가하여 JavaScript 또는 TypeScript에서 MongoDB를 사용할 수 있습니다.

Node.js 드라이버는 MongoDB에 연결하고 통신하는 데 사용할 수 있는 함수들의 라이브러리입니다.

MongoDB Atlas를 사용하지 않고 로컬 배포된 MongoDB를 사용하겠습니다.

### 설치

프로젝트 폴더에 이미 `package.json`이 존재하고 `npm` 설정을 한 상태라고 가정하겠습니다.

```bash
npm install mongodb
```

### 연결과 실습

MongoDB는 실행중이어야 합니다.

```bash
brew services start mongodb-community@6.0
```

```javascript
import { MongoClient } from "mongodb";

const uri = "mongodb://localhost:27017";

const client = new MongoClient(uri);

async function run() {
  try {
    const database = client.db("boardpjt");
    const boards = database.collection("boards");

    const query = { title: "first board" };
    const board = await boards.findOne(query);

    console.log("board:", board);
  } finally {
    await client.close();
  }
}

run().catch(console.dir);
```

```bash
board: {
  _id: new ObjectId("64d38a672db20e5710ca6527"),
  title: 'first board',
  content: 'this is first board.',
  createdDate: '23/08/09',
  createdTime: '21:45',
  id: 1691417458371
}
```

CRUD 예제에 관한 내용들은 아래 링크에서 볼 수 있습니다.

> [Usage Examples](https://www.mongodb.com/docs/drivers/node/current/usage-examples/)

## 참고

> [MongoDB Node Driver](https://www.mongodb.com/docs/drivers/node/current/)
