---
title: "fs 내장 모듈을 사용해 Node.js에서 파일 다루기"
date: 2023-08-01
last_modified_at: 2023-08-02
excerpt: "Node.js에서의 fs 모듈을 이용한 파일 조작 방식을 예시를 통해 알아보고, Sync 단어가 붙은 함수는 무엇이 다른지 Callback, Promise, Async-Await 방식을 모두 비교해봅니다. 그리고 파일을 여는 또다른 함수, open은 무엇이 다를까요?"
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

<br>
<br>

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

이제 파일을 읽고, 쓰고, 지우고, 이동하는 방법에 대해 알아보겠습니다.

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

> `fs.open()`의 경우, 해당 파일에 여러 작업을 수행하는 경우 호출합니다.
> 
> 파일을 연 후, `fs.close()`로 닫는 작업이 추가적으로 필요합니다.
> 
> `fs.readFile()`, `fs.readFileSync()`는 파일을 따로 닫지 않아도 됩니다.

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

## 이름에 ..Sync가 붙어있는 함수와의 차이점

파일 읽기의 `readFile`, `readFileSync`

파일 쓰기의 `writeFile`, `writeFileSync`

`Sync`가 붙은 함수는 동기적으로 동작합니다. 붙지 않은 함수는 비동기로 동작합니다.

### readFileSync

`readFileSync`를 사용하면 순서대로 파일을 읽을 수 있습니다. `node:fs`에서 가져옵니다.

```javascript
// readFileSync
import * as fs from 'node:fs';

const filePath1 = './file1.txt';
const filePath2 = './file2.txt';
const filePath3 = './file3.txt';
const filePath4 = './file4.txt';
const filePath5 = './file5.txt';

const data1 = fs.readFileSync(filePath1);
const data2 = fs.readFileSync(filePath2);
const data3 = fs.readFileSync(filePath3);
const data4 = fs.readFileSync(filePath4);
const data5 = fs.readFileSync(filePath5);

console.log(data1.toString()); // 1
console.log(data2.toString()); // 2
console.log(data3.toString()); // 3
console.log(data4.toString()); // 4
console.log(data5.toString()); // 5
```

```
1
2
3
4
5
```

읽은 순서대로 출력됩니다.

### readFile

`readFile`을 사용하면 다음과 같습니다. 편의상 에러 처리는 생략하겠습니다.

```javascript
// readFile
import * as fs from 'node:fs';

const filePath1 = './file1.txt';
const filePath2 = './file2.txt';
const filePath3 = './file3.txt';
const filePath4 = './file4.txt';
const filePath5 = './file5.txt';

fs.readFile(filePath1, (err, data) => console.log(data.toString())); // 1
fs.readFile(filePath2, (err, data) => console.log(data.toString())); // 2
fs.readFile(filePath3, (err, data) => console.log(data.toString())); // 3
fs.readFile(filePath4, (err, data) => console.log(data.toString())); // 4
fs.readFile(filePath5, (err, data) => console.log(data.toString())); // 5
```

```
1
2
5
4
3
```

순서대로 출력되지 않습니다.

원하는 순서대로 출력하기 위해 콜백 함수를 사용해야 합니다.

### readFile with Callback

콜백 함수를 이용해 순서대로 출력해보겠습니다.

```javascript
// readFile with Callback
import * as fs from 'node:fs';

const filePath1 = './file1.txt';
const filePath2 = './file2.txt';
const filePath3 = './file3.txt';
const filePath4 = './file4.txt';
const filePath5 = './file5.txt';

fs.readFile(filePath1, (err, data) => {
  console.log(data.toString()); // 1
  fs.readFile(filePath2, (err, data) => {
    console.log(data.toString()); // 2
    fs.readFile(filePath3, (err, data) => {
      console.log(data.toString()); // 3
      fs.readFile(filePath4, (err, data) => {
        console.log(data.toString()); // 4
        fs.readFile(filePath5, (err, data) => {
          console.log(data.toString()); // 5
        });
      });
    });
  });
});
```

```
1
2
3
4
5
```

순서대로 출력됩니다.

### readFile with Promise

이번에는 프로미스 방식으로 해결해보겠습니다. `node:fs/promises`에서 가져옴에 주의합니다.

```javascript
// readFile with Promise
import * as fs from 'node:fs/promises';

const filePath1 = './file1.txt';
const filePath2 = './file2.txt';
const filePath3 = './file3.txt';
const filePath4 = './file4.txt';
const filePath5 = './file5.txt';

fs.readFile(filePath1)
  .then((data) => {
    console.log(data.toString()); // 1
    return fs.readFile(filePath2);
  })
  .then((data) => {
    console.log(data.toString()); // 2
    return fs.readFile(filePath3);
  })
  .then((data) => {
    console.log(data.toString()); // 3
    return fs.readFile(filePath4);
  })
  .then((data) => {
    console.log(data.toString()); // 4
    return fs.readFile(filePath5);
  })
  .then((data) => {
    console.log(data.toString()); // 5
    return;
  });
```

```
1
2
3
4
5
```

### readFile with Async-Await

Async-Await 방식으로 바꿔보겠습니다.

```javascript
// readFile with Async-Await
import * as fs from 'node:fs/promises';

const filePath1 = './file1.txt';
const filePath2 = './file2.txt';
const filePath3 = './file3.txt';
const filePath4 = './file4.txt';
const filePath5 = './file5.txt';

const readAllFiles = async () => {
  const data1 = await fs.readFile(filePath1);
  console.log(data1.toString()); // 1
  const data2 = await fs.readFile(filePath2);
  console.log(data2.toString()); // 2
  const data3 = await fs.readFile(filePath3);
  console.log(data3.toString()); // 3
  const data4 = await fs.readFile(filePath4);
  console.log(data4.toString()); // 4
  const data5 = await fs.readFile(filePath5);
  console.log(data5.toString()); // 5
};

readAllFiles();
```

```
1
2
3
4
5
```

### `readFile()`을 동기식으로 쓰려고 하기 vs `readFileSync()` 그냥 쓰기

`readFile()`을 위에서 실습한 것처럼,

1. 콜백 함수
2. 프로미스
3. Async-Await

세 가지 방식으로 사용해 동기식처럼 사용할 수 있습니다.

그렇다면 파일의 입력 순서가 중요할 경우, `readFileSync()`를 사용해야 할까요 아니면 그냥 `readFile()`을 위의 세 가지 방법으로 동기식으로 사용해야 할까요?

<br>
<br>

# 참고

> [File system](https://nodejs.org/api/fs.html)

> [Node.js File System Module](https://www.w3schools.com/nodejs/nodejs_filesystem.asp)

> [How To Work with Files using the fs Module in Node.js](https://www.digitalocean.com/community/tutorials/how-to-work-with-files-using-the-fs-module-in-node-js)

> [Why is fs.readFileSync() faster than await fsPromises.readFile()?](https://stackoverflow.com/questions/63971379/why-is-fs-readfilesync-faster-than-await-fspromises-readfile)

> [what is the use of fs.open() in nodejs, what is difference between fs.readfile and fs.open()](https://stackoverflow.com/questions/48928758/what-is-the-use-of-fs-open-in-nodejs-what-is-difference-between-fs-readfile-a)

---
