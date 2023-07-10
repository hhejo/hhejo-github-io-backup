---
title:  "게시판 프로젝트 01 - nvm, npm, express"
date: 2023-07-10
last_modified_at: 2023-07-10
excerpt: "게시판 프로젝트의 시작. 프로젝트 초기 설정 하는 방법"
categories:
  - BulletinBoardPjt
tags:
  - [nvm, npm, node, express]
---

---

<br>

# nvm (node version manager)

- `Node.js`의 버전 매니저
- 여러 버전의 노드를 설치하고, 활성화할 수 있다.
- 특정 버전의 노드를 사용하다가 다른 버전의 노드를 사용할 수 있다.
- ```sh
  nvm ls
  nvm install --lts
  nvm uninstall --lts
  nvm use --lts
  nvm deactivate
  ```

# npm (node package manager)

- javascript 런타임 환경 `Node.js`의 기본 패키지 관리자
- ```sh
  npm init
  ```
- 위 명령어로 `package.json`, `package-lock.json` 파일이 생성된다.
- ```json
  {
    "dependencies": {
      "express": "^4.18.2"
    },
    "name": "node-practice",
    "description": "",
    "version": "1.0.0",
    "main": "app.js",
    "devDependencies": {},
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC"
  }
  ```
- `"main"` 부분의 값은 `"app.js"`로 수정했다. `index.js`도 상관 없지만 `express`의 예제와 통일성을 위해 바꿨다.
- 나머지는 개발을 진행하면서 수정하려고 한다.

# Express

- Node.js 웹 애플리케이션 프레임워크
- 나의 게시판 프로젝트 백엔드를 위해 사용하기로 했다.

## 설치

- `npm install express`
- `--save`는 빼고 작성해도 dependencies 목록에 추가된다.

## 예제

```javascript
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

- `node app.js`로 서버를 실행한다.
- `http:localhost:3000/` 에서 응답을 확인한다.

---
