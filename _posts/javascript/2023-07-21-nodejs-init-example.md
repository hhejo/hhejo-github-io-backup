---
title:  "Node.js 프로젝트 초기 세팅"
date: 2023-07-21
last_modified_at: 2023-07-21
excerpt: "Node.js 프로젝트를 간단하게 시작하기 위해 필요한 것들을 정리했습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, babel, nodemon]
---

---

<br>

폴더부터 생성하겠습니다.

```bash
mkdir my-pjt
cd my-pjt
```

nvm을 이용해 LTS 버전의 Node.js를 사용하겠습니다. nvm이 없다면 설치하거나 그냥 Node.js만 설치하면 됩니다. 여기서는 다루지 않겠습니다.

```bash
nvm use --lts
```

package.json 파일을 생성합니다. `-y` 옵션은 자유롭게 사용합니다.

```bash
npm init -y
```

프로젝트의 진입점이 될 파일을 생성합니다.

```bash
mkdir src
cd src
touch index.js
```

index.js 파일에 코드를 작성합니다.

```javascript
console.log('hello world');
```

프로젝트 루트 디렉터리로 다시 이동해 코드를 실행합니다.

```bash
cd ..
node src/index.js
```

터미널에서 다음 출력을 확인할 수 있습니다.

```
hello world
```

package.json에 실행 스크립트를 추가해보겠습니다.

```json
  ...,
  "scripts": {
    "start": "node src/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

이제 `npm`으로 실행할 수 있습니다.

```bash
npm run start
```

```
> my-pjt@1.0.0 start
> node src/index.js

hello world
```

디렉터리 구조는 아래와 같습니다.

```
/my-pjt
---/src
    ---index.js
---package.json
```

<br>
<br>

우리가 현재 개발을 하면, 코드를 작성하고 수정하게 되는데, 이를 저장하고 다시 실행해야 합니다.

코드 한줄 고치고 저장한 후 `npm run start` 입력하고 결과를 확인하고, 다시 코드를 수정하고 `npm run start`를 입력한 후 출력을 확인하고, 이것이 반복적인 작업이기 때문에 자동화를 하는 것이 편리합니다.

이를 위해 `nodemon`을 설치하고 현재 프로젝트에 적용해보겠습니다.

다음 명령어를 터미널에 입력해 nodemon을 설치합니다. 개발의존성에 넣고 싶기 때문에 `--save-dev` 옵션을 추가합니다.

```bash
npm install --save-dev nodemon
```

npm 스크립트로 관리하고 싶기 때문에 package.json에 npm 스크립트를 추가합니다. `"dev": "nodemon src/index.js"`라고 입력합니다.

```json
  ...,
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

새로 작성한 스크립트 명령어를 입력합니다.

```bash
npm run dev
```

```
[nodemon] 3.0.1
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,cjs,json
[nodemon] starting `node src/index.js`
hello world
[nodemon] clean exit - waiting for changes before restart
```

index.js 파일의 내용을 수정하고 저장하면, 바뀐 내용이 적용되어 자동으로 재실행되는 것을 확인할 수 있습니다.

```javascript
console.log('hello word');
console.log('wow'); // 새로 추가된 코드
```

```
[nodemon] restarting due to changes...
[nodemon] starting `node src/index.js`
hello world
wow
[nodemon] clean exit - waiting for changes before restart
```

그런데 Node.js v18.11.0 부터 nodemon 없이 변경 사항을 추적하고 재시작하는 기능이 새로 추가되었습니다.

```bash
node --watch src/index.js
```

```json
  ...,
  "scripts": {
    "start": "node src/index.js",
    "dev": "node --watch src/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

```bash
npm run dev
```

```
(node:16201) ExperimentalWarning: Watch mode is an experimental feature and might change at any time
(Use `node --trace-warnings ...` to show where the warning was created)
hello world
wow
Completed running 'src/index.js'
```

하지만 여기서는 nodemon을 이용하도록 하겠습니다.

<br>
<br>

`babel`을 설치해보겠습니다.

```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

```bash
npm install @babel/node
```

babel, nodemon 같이 하는 부분 작업중..

<br>
<br>

# 참고

> [nodemon - npm](https://www.npmjs.com/package/nodemon)

> [The minimal Node.js with Babel Setup](https://www.robinwieruch.de/minimal-node-js-babel-setup/)

---
