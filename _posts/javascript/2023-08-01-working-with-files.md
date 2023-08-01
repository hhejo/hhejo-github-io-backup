---
title:  "Node.js에서 파일을 다루기"
date: 2023-08-01
last_modified_at: 2023-08-01
excerpt: "Node.js에서의 fs 모듈을 이용한 파일 조작 방식을 예시를 통해 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, fs]
---

---

<br>

# Node.js의 파일 시스템

Node.js에서는 `fs`(file system) 모듈을 사용해 파일을 조작할 수 있습니다.

`fs` 모듈은 동기식, 비동기식 또는 스트림을 통한 파일과의 상호작용을 지원합니다.

CommonJS, ES Modules 방식 모두 지원합니다.

# fs

ES Modules 방식으로 살펴보겠습니다. promise 기반의 API를 사용하기 위해 아래 선언문을 작성합니다.

```javascript
// index.js
import * as fs from 'node:fs/promises';
```

만약 동기식/콜백 API를 사용하고 싶다면, 아래 선언문을 작성합니다.

```javascript
import * as fs from 'node:fs';
```

프로미스 기반 작업의 예시는 아래와 같습니다. 비동기 작업이 완료되면 프로미스를 반환합니다.

```javascript
import { unlink } from 'node:fs/promises';

try {
  await unlink('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (error) {
  console.error('there was an error:', error.message);
}
```

콜백 형식의 예시는 아래와 같습니다. 작업을 비동기적으로 호출합니다.

```javascript
import { unlink } from 'node:fs';

unlink('/tmp/hello', (err) => {
  if (err) throw err;
  console.log('successfully deleted /tmp/hello');
});
```

동기식 작업의 예시는 아래와 같습니다. 해당 작업이 완료될 때까지 Node.js 이벤트 루프와 추가 JavaScript 실행을 차단합니다. 예외는 즉시 발생합니다.

```javascript
import { unlinkSync } from 'node:fs';

try {
  unlinkSync('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (err) {
  // handle the error
}
```

## 1. 파일 읽기

`readFile()`을 사용해 파일을 읽습니다.

파일을 읽으면, `buffer` 객체를 반환합니다. `toString()` 메서드를 이용해 텍스트로 변환합니다.

### 예시

읽어들일 텍스트 파일을 하나 생성합니다. `hello.txt`를 생성하겠습니다.

```
Hello, World!
```

ESM 방식을 사용하기 위해 `npm init -y`를 실행한 후, `package.json`에 `"type": "module"`을 추가합니다.

```javascript
// index.js
import * as fs from 'node:fs/promises';

const filePath = './hello.txt';

try {
  const data = await fs.readFile(filePath);
  console.log(data.toString());
} catch (err) {
  console.error(err.message);
}
```

`index.js` 파일을 실행하면, `Hello, World!`가 잘 출력됩니다.

## 2. 파일 쓰기

`writeFile()`을 사용해 파일에 씁니다.

### 예시

```javascript
import * as fs from 'node:fs/promises';

const filePath = './book.csv';

try {
  const csvHeader = 'title,author,price';
  await fs.writeFile(filePath, csvHeader);
} catch (err) {
  console.error(err.message);
}
```

csv 파일이 없다면 새로 생성됩니다.

```javascript
import * as fs from 'node:fs/promises';

const filePath = './book.csv';

try {
  const csvLine = '\nthisIsTitle,iAmAuthor,4000';
  await fs.writeFile(filePath, csvLine, { flag: 'a' });
} catch (err) {
  console.error(err.message);
}
```

`{ flag: 'a' }`로 덮어쓰지 않고 추가합니다.

`cat book.csv`로 내용을 출력해 확인할 수 있습니다.

## 3. 파일 삭제

`unlink()`를 사용해 파일을 지웁니다.

### 예시

```javascript
import * as fs from 'node:fs/promises';

const filePath = './hello.txt';

try {
  await fs.unlink(filePath);
  console.log(`Deleted ${filePath}`);
} catch (err) {
  console.error(err.message);
}
```

`hello.txt` 파일이 삭제됩니다.

## 4. 파일 이동(이름 변경)

`rename()`을 사용해 파일을 이동하거나 이름을 변경합니다.

```javascript
import * as fs from 'node:fs/promises';

const source = './hello.txt';
const destination = './hello2.txt';

try {
  await fs.rename(source, destination);
  console.log(`Moved file from ${source} to ${destination}`);
} catch (err) {
  console.error(err.message);
}
```

<br>
<br>

# 참고

> [File system](https://nodejs.org/api/fs.html)

> [Node.js File System Module](https://www.w3schools.com/nodejs/nodejs_filesystem.asp)

> [How To Work with Files using the fs Module in Node.js](https://www.digitalocean.com/community/tutorials/how-to-work-with-files-using-the-fs-module-in-node-js)

---
