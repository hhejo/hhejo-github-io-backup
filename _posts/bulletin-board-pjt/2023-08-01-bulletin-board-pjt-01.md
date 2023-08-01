---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 01"
date: 2023-08-01
last_modified_at: 2023-08-01
excerpt: "프로젝트의 구성.. 프론트엔드와 백엔드 초기 설정하기. 그리고 CORS 해결"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, vite, cors, axios]
---

---

<br>

# 프로젝트를 어떻게 구성해야하나?

간단한 게시판을 하나 만들 것입니다.

목표는 다음과 같습니다.

1. 강의에 나오는 결과물을 따라만 하지 않고 혼자서 해결하기
2. 많은 에러를 일부러라도 발생시키고 직접 해결하기
3. 배포까지 해서 다른 사람들에게 소개해보기
4. CRUD 구현
5. 인증 구현 + 소셜 로그인
6. DB 2가지 이상 사용
7. 고민중

프로젝트 루트 디렉터리 구조를 아래와 같이 잡겠습니다.

```
/client
/server
.gitignore
README.md
```

프로젝트가 진행되면서 변경될 수 있습니다.

<br>
<br>

# 백엔드 초기 설정

`nvm`으로 최신 LTS Node.js 사용

`npm init -y`로 `package.json` 초기화

작성 코드의 에러를 잡고, 방지하기 위해 `eslint` 설치

코드 형식을 다듬기 위해 `prettier` 설치

ESM 사용을 위해 `babel` 설치

그런데 ESM은 `package.json`에 `"type": "module"`을 넣으면 되는데, 이번 프로젝트에서는 그냥 babel을 설치하고 사용하겠습니다.

저장 후 서버 자동 재실행을 위해 `nodemon` 설치

일단 생각나는 패키지가 여기까지라서 프로젝트 진행 중에 필요한 다른 패키지들은 그때 설치하겠습니다.

그리고 Express로 서버를 구성할 것이기 때문에 `express` 설치

환경변수 관리를 위해 `dotenv`도 설치합니다.

설치가 끝났으니 예제 서버를 실행해봅니다.

```javascript
// server/src/app.js
import express from 'express';
import dotenv from 'dotenv';

dotenv.config();

const app = express();

const { PORT } = process.env;

app.get('/', (req, res) => res.send('Hello, World!'));

app.listen(PORT, () => console.log(`App listening on port ${PORT}`));
```

`http:localhost:3000`에 잘 접속됩니다.

<br>
<br>

# 프론트엔드 초기 설정

Vite로 React를 설정하기 위해 `npm create vite@latest`를 입력하고 선택지를 잘 고릅니다.

생성을 완료한 후, `node_modules` 폴더가 없어서 어딨는지 찾았는데, 그냥 `npm install`을 하면 생성됩니다.

Vite로 React 개발 환경을 세팅하면서 eslint가 자동으로 설치되어 있길래 따로 건드리지 않았습니다.

프론트엔드 개발이 너무 오랜만이라서 데이터를 어떻게 가져오는지 잊었습니다.

그러다가 `axios`가 생각나서 설치했습니다.

```jsx
// client/src/App.jsx
import { useState } from 'react';
import axios from 'axios';

import './App.css';

function App() {
  const [data, setData] = useState('');

  const clickHandler = () => {
    axios
      .get('http://localhost:3000/')
      .then((res) => setData(res.data))
      .catch((err) => console.error(err));
  };

  return (
    <>
      <div>
        <button onClick={clickHandler}>click</button>
      </div>
      <div>data: {data}</div>
    </>
  );
}

export default App;
```

리액트 훅도 좀 생각이 안 나지만 계속 해봐야겠습니다.

<br>
<br>

# CORS 문제

위 코드를 작성하고 프론트엔드 개발 서버 실행 후, 버튼을 눌렀는데 데이터가 CORS 문제로 오지 않았습니다.

검색을 하다가, `cors` 패키지를 설치해 백엔드에서 설정하면 해결할 수 있다고 해서 그대로 진행했습니다. 일부분만 작성하겠습니다.

```javascript
// server/src/app.js
import cors from 'cors';

app.use(cors());
```

위 코드를 추가하니 서버에서 전송한 데이터를 클라이언트에서 잘 받을 수 있었습니다.

CORS는 따로 다시 공부를 해야겠습니다.

<br>
<br>

# 요약

### 백엔드 설치 패키지

`eslint`, `prettier`, `babel`, `nodemon`, `express`, `dotenv`, `cors`

### 프론트엔드 설치 패키지

`vite`, `axios`

React와 기타 필요 패키지는 Vite 설정으로 해결됩니다.

<br>
<br>

# 다음에 할 것들

일단 서버와 DB를 연결해야겠습니다.

DB는 MongoDB, MySQL, SQLite 중에 하나를 사용해보겠습니다.

그리고 DB를 어떻게 서버와 연결할 것인지, DB 모델?은 어떻게 구성할 것인지 해보겠습니다.

그리고 이제부터 본격적인 개발이니까 커밋 메시지도 잘 쓰고 브랜치도 나누어 작업하겠습니다.

😌

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/bf13dd99e97ed17bbb35fbca0db5014bf542589e)

<br>
<br>

# 참고

> [Axios Docs](https://axios-http.com/kr/)

> [cors](https://expressjs.com/en/resources/middleware/cors.html)

> [React + Express CORS 설정하기](https://velog.io/@wiostz98kr/React-Express-CORS-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)

> [[Node.js] express cors 사용하기](https://surprisecomputer.tistory.com/32)

---
