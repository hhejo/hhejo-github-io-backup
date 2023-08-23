---
title: fs 모듈을 이용해 폴더 조작하기
date: 2023-08-02 00:00:00 +0900
last_modified_at: 2023-08-02 00:00:00 +0900
categories: [JavaScript]
tags: [javascript, nodejs, fs]
---

`mkdir`, `mkdirSync`, `rmdir`, `rmdirSync`를 이용해 폴더를 생성하고 삭제해봅시다.

## 폴더 조작

`fs` 모듈을 이용해 폴더를 생성하고, 삭제할 수 있습니다.

## 폴더 생성

`mkdir()`을 이용합니다.

`new-dir` 이름의 폴더가 생성됩니다.

```javascript
import fs from "fs/promises";
import { dirname } from "path";
import { fileURLToPath } from "url";

const __dirname = dirname(fileURLToPath(import.meta.url));
const folderPath = `${__dirname}/new-dir`;

fs.mkdir(folderPath);
```

`mkdirSync()`는 동기 방식으로 동작하며, `fs` 모듈에서 가져옵니다.

```javascript
import fs from "node:fs";
import { dirname } from "path";
import { fileURLToPath } from "url";

const __dirname = dirname(fileURLToPath(import.meta.url));
const folderName = `${__dirname}/new-dir`;

try {
  if (!fs.existsSync(folderName)) {
    fs.mkdirSync(folderName);
  } else {
    console.log(`Already exists.`);
  }
} catch (err) {
  console.error(err);
}
```

## 폴더 삭제

`rmdir()` 혹은 `rmdirSync()`를 이용해 폴더를 삭제합니다.

```javascript
import fs from "node:fs";
import { dirname } from "path";
import { fileURLToPath } from "url";

const __dirname = dirname(fileURLToPath(import.meta.url));

const folderName = `${__dirname}/new-dir`;

try {
  if (fs.existsSync(folderName)) {
    fs.rmdirSync(folderName);
    console.log("Removed.");
  } else {
    fs.mkdirSync(folderName);
    console.log("Created.");
  }
} catch (err) {
  console.error(err);
}
```

## 참고

> [Working with folders in Node.js](https://nodejs.dev/en/learn/working-with-folders-in-nodejs/)

> [How To Use \_\_dirname in Node.js](https://www.digitalocean.com/community/tutorials/nodejs-how-to-use__dirname)
