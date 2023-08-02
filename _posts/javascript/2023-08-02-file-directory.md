---
title: "Node.js에서 파일 경로 찾기"
date: 2023-08-02
last_modified_at: 2023-08-02
excerpt: "__dirname, __filename, path.dirname(), process.cwd()가 각각 무엇을 의미하는지 비교해보며 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, dirname, filename, path, cwd]
---

---

<br>

# __dirname, __filename

`__dirname`은 현재 실행중인 파일이 포함된 디렉터리의 절대 경로를 리턴합니다.

`__filename`은 현재 파일의 경로까지 포함해 리턴합니다.

```javascript
console.log(__dirname);
console.log(__filename);
```

```
/Users/hejo/Documents/my-pjt
/Users/hejo/Documents/my-pjt/index.js
```

단, `__dirname`과 `__filename`은 CommonJS 방식에서 유효합니다. `"type": "module"`이 작성된 경우, 아래 에러가 발생합니다.

```
ReferenceError: __dirname is not defined in ES module scope
```

## ES modules에서 __dirname, __filename 사용하기

`path` 모듈의 `dirname()`과 `url` 모듈의 `fileURLToPath()`를 사용합니다.

```javascript
import path from 'path';
import { fileURLToPath } from 'url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

console.log(import.meta.url);
console.log(`__filename: ${__filename}`);
console.log(`__dirname: ${__dirname}`);
```

```
file:///Users/hejo/Documents/my-pjt/index.js
__filename: /Users/hejo/Documents/my-pjt/index.js
__dirname: /Users/hejo/Documents/my-pjt
```

`import.meta.url`을 통해 파일 URL을 얻고, `fileURLToPath()`를 이용해 경로로 변경합니다.

`dirname()`을 통해 파일 이름을 제외한 경로만 얻습니다.

<br>
<br>

# path

`path` 모듈은 Node.js의 내장 모듈로 파일 및 디렉터리 경로 작업을 위한 유틸리티를 제공합니다.

```javascript
import path from 'path';
```

Unix 계열 운영체제와 Windows 운영체제의 시스템 파일 경로는 서로 다르기 때문에 `path` 모듈을 사용하는 것이 좋습니다.

전자는 `/users/hhejo/hello.txt`와 같이 경로를 나타내지만, 후자는 `C:\users\hhejo\hello.txt`와 같은 구조를 가집니다.

터미널에 `pwd`를 입력하면 현재 위치의 경로를 알 수 있습니다(Windows 제외).

## 파일 정보 가져오기

`dirname()`은 파일의 디렉터리를 반환합니다.

`basename()`은 파일의 이름을 반환합니다.

`extname()`은 파일의 확장자 이름을 반환합니다.

```javascript
import path from 'path';

const fileName = 'hello.txt';
const filePath = `${process.cwd()}/${fileName}`;

console.log(`filePath: ${filePath}`);
console.log(`dirname(): ${path.dirname(filePath)}`);
console.log(`basename(): ${path.basename(filePath)}`);
console.log(`extname(): ${path.extname(filePath)}`);
```

```
filePath: /Users/hejo/Documents/my-pjt/hello.txt
dirname(): /Users/hejo/Documents/my-pjt
basename(): hello.txt
extname(): .txt
```

## 경로 다루기

`join()`을 이용해 경로의 두 개 이상 부분을 연결할 수 있습니다.

```javascript
import path from 'path';

const filePath = path.join('/', 'Users', 'hhejo', 'my-pjt', 'hello.txt');
console.log(filePath);
```

```
/Users/hhejo/my-pjt/hello.txt
```

`resolve()`를 이용해 상대 경로를 절대 경로로 계산해 얻을 수 있습니다.

```javascript
import path from 'path';

const filePath = path.resolve('hello.txt');
console.log(filePath);
```

```
/Users/hejo/Documents/my-pjt/hello.txt
```

<br>
<br>

# process.cwd()

`process.cwd()` 메서드는 Node.js 프로세스의 현재 작업 디렉터리를 반환합니다.

```javascript
import { cwd } from 'node:process';

console.log(cwd()); // /Users/hejo/Documents/my-pjt
console.log(process.cwd()); // /Users/hejo/Documents/my-pjt
```

## process.cwd()와 __dirname의 차이점

`process.cwd()`는 Node.js 프로세스를 실행한 디렉터리의 이름을 반환합니다.

`__dirname`은 현재 스크립트의 디렉터리 이름을 반환합니다. 전역이 아니라 각 모듈에 대해 로컬입니다.

예시를 보겠습니다.

```
/my-pjt
--- index.js
--- /lib
    --- script.js
```

```javascript
// main.js
require('./lib/script.js');

console.log('>>> main.js <<<'); // >>> main.js <<<
console.log(process.cwd()); // /Users/hejo/Documents/my-pjt
console.log(__dirname); // /Users/hejo/Documents/my-pjt
console.log(__dirname === process.cwd()); // true
```

```javascript
// lib/script.js
console.log('>>> script.js <<<'); // >>> script.js <<<
console.log(process.cwd()); // /Users/hejo/Documents/my-pjt
console.log(__dirname); // /Users/hejo/Documents/my-pjt/lib
console.log(__dirname === process.cwd()); // false
```

```bash
node main.js
```

```
>>> script.js <<<
/Users/hejo/Documents/my-pjt
/Users/hejo/Documents/my-pjt/lib
false
>>> main.js <<<
/Users/hejo/Documents/my-pjt
/Users/hejo/Documents/my-pjt
true
```

`process.cwd()`는 Node.js 프로세스를 실행하는 디렉터리의 값을 반환하고,

`__dirname`은 현재 실행중인 파일이 있는 디렉터리의 값을 반환합니다.

<br>
<br>

# 참고

> [The module scope](https://nodejs.org/docs/latest/api/modules.html#__dirname)

> [How To Use __dirname in Node.js](https://www.digitalocean.com/community/tutorials/nodejs-how-to-use__dirname)

> [Path](https://nodejs.org/api/path.html#path)

> [Node.js File Paths](https://nodejs.dev/en/learn/nodejs-file-paths/)

> [Node.js의 path 모듈로 경로 다루기](https://www.daleseo.com/js-node-path/)

> [What's the difference between process.cwd() vs __dirname?](https://stackoverflow.com/questions/9874382/whats-the-difference-between-process-cwd-vs-dirname)

> [import.meta](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import.meta)

---
