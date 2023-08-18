---
title: "Express에서 MongoDB Driver와 연결하고 모듈로 분리하기"
date: 2023-08-18
last_modified_at: 2023-08-18
excerpt: "Express와 MongoDB Driver를 어떻게 연결하고, 환경변수와 함께 모듈로 분리해 효율적으로 다룰 수 있을지 알아보겠습니다."
categories:
  - ExpressJS
tags:
  - [express, nodejs, mongodb, env]
---

---

<br />

# env

아래 코드처럼 환경변수를 다룰 수 있는 모듈 파일을 생성합니다.

```javascript
// load-environment.mjs
import dotenv from 'dotenv';

dotenv.config();

export const { MONGODB_URI } = process.env || '';
export const { MONGODB_DB_NAME } = process.env;
```

위처럼 모듈로 분리해, 깔끔하게 환경변수를 다른 곳에서 가져올 수 있고, 관리하기도 쉬워집니다.

<br />
<br />

# MongoClient

`app.js`에 DB를 연결하는 코드를 작성하면 난잡해지기 때문에, 따로 모듈로 분리해 관리해줄 수 있습니다.

`mongodb.MongoClient`를 이용해 MongoDB에 연결할 수 있습니다.

MongoDB 서버는 실행중이어야 합니다.

```javascript
// conn.mjs
import { MongoClient } from 'mongodb';
import { MONGODB_URI, MONGODB_DB_NAME } from './load-environment';

const client = new MongoClient(MONGODB_URI);
const db = client.db(MONGODB_DB_NAME);

export default db;
```

## 다른 파일에서 사용

이제 아래 코드를 작성해 다른 파일에서 가져올 수 있습니다.

```javascript
import db from '../conn';
```

## Collection 선택

아래 코드처럼 작성해 collection을 선택할 수 있습니다.

```javascript
const boards = db.collection('boards');
```

<br />
<br />

# 참고

> [Connect to MongoDB](https://www.mongodb.com/docs/drivers/node/current/quick-start/connect-to-mongodb/)

---
