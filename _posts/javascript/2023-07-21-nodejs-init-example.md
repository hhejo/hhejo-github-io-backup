---
title:  "nodemon과 Babel로 Node.js 프로젝트 초기 세팅"
date: 2023-07-21
last_modified_at: 2023-07-22
excerpt: "Node.js 프로젝트를 간단하게 시작하기 위해 필요한 것들을 정리했습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, nodemon, babel]
---

---

<br>

# Node.js

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

# nodemon

우리가 현재 개발을 하면, 코드를 작성하고 수정하게 되는데, 이를 저장하고 다시 실행해야 합니다.

코드 한줄 고치고 저장한 후 `npm run start` 입력하고 결과를 확인하고, 다시 코드를 수정하고 `npm run start`를 입력한 후 출력을 확인하고, 이것이 반복적인 작업이기 때문에 자동화를 하는 것이 편리합니다.

이를 위해 `nodemon`을 설치하고 현재 프로젝트에 적용해보겠습니다.

다음 명령어를 터미널에 입력해 nodemon을 설치합니다. 개발의존성에 넣고 싶기 때문에 `--save-dev` 옵션을 추가합니다.

```bash
npm install --save-dev nodemon
```

npm 스크립트로 관리하고 싶기 때문에 package.json에 npm 스크립트를 추가합니다. `"dev": "nodemon src/index.js"`라고 입력합니다.

```json
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

# Babel

Node.js에서 최신 JS 문법을 사용하면서, 구버전의 브라우저에서도 호환이 되는 코드를 작성하려면, Babel을 사용합니다. Babel을 사용해 CommonJS 방식이 기본인 Node.js에 ES Modules 방식을 적용할 수도 있습니다.

`babel`을 설치해보겠습니다.

```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env @babel/node
```

`@babel/core` : babel을 사용하기 위한 핵심

`@babel/cli` : babel CLI 제공 (터미널에서 babel 명령어 사용을 가능하게 함)

`@babel/preset-env` : babel 플러그인의 모음

`@babel/node` : Node.js CLI와 동일하게 동작하는 CLI

babel은 다음과 같이 실행할 수 있습니다.

```bash
npx babel src --out-dir dist --presets=@babel/preset-env
```

`babel` 명령어로 `src` 폴더 안의 파일들을 트랜스파일해 `dist` 폴더에 저장합니다. `--presets`로 프리셋을 지정해주지 않으면 babel은 전혀 바뀌지 않은 파일을 출력하기 때문에 프리셋을 (혹은 플러그인을) 지정해야 원하는 결과물을 얻을 수 있습니다.

항상 위의 명령줄을 작성하는 것은 번거롭기 때문에, 프로젝트 루트 폴더에 `babel.config.json` 파일을 생성하고 다음을 작성합니다. `.babelrc` 파일로 작성해도 상관 없습니다.

```json
{
  "presets": ["@babel/preset-env"]
}
```

`.babelrc`, `babel.config.json`, `babel.config.js`의 설정 파일로 babel을 설정할 수 있습니다.

plugin은 babel이 코드를 어떤 식으로 변환할지에 대해 알려줍니다. plugin들의 모음이 preset이 됩니다.

이제 babel은 해당 프리셋을 적용하기 때문에, 명령줄에 프리셋 없이 명령을 입력해도 프리셋이 적용된 결과물을 얻을 수 있습니다.

```bash
npx babel src --out-dir dist
```

더 편하게 하기 위해 npm 스크립트로 작성하겠습니다.

```json
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon --exec babel-node src/index.js",
    "build": "babel src --out-dir dist",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

`npm run dev` 명령을 입력하게 되면 nodemon이 `babel-node` 명령을 실행해 src 폴더의 index.js 파일을 실행합니다. `babel-node`는 Node.js의 CLI와 동일하게 동작하며 babel 사전 설정으로 컴파일할 수 있기 때문에 ESM을 사용할 수 있습니다.

```javascript
// src/index.js
import { a } from './a';

console.log(a);
```

```javascript
// src/a.js
export const a = 10;
```

아래는 에러가 발생합니다. 

```bash
node src/index.js
```

```
SyntaxError: Cannot use import statement outside a module
```

아래는 성공적으로 실행됩니다.

```bash
npx babel-node src/index.js
```

```
10
```

스크립트를 아래와 같이 정리합니다.

```json
    "start": "node src/index.js",
    "dev": "nodemon --exec babel-node src/index.js",
    "build": "babel src --out-dir dist",
```

`npm run dev`로 파일을 babel-node로 실행하면서 파일이 수정되면 nodemon이 자동으로 재시작합니다.

`npm run build`를 이용해 babel로 트랜스파일을 합니다. 결과물은 dist 폴더에 저장됩니다.

디렉터리 구조는 아래와 같습니다.

```
/my-pjt
---/dist
    ---a.js
    ---index.js
---/node_modules
---/src
    ---a.js
    ---index.js
---babel.config.json
---package-lock.json
---package.json
```

<br>
<br>

# 결론

파일을 수정하고 다시 시작하는 번거로움을 없애기 위해 nodemon을 사용해 파일 수정시 실행 재시작을 자동화했고, 최신 JS 문법 (ESM 등) 적용을 위해 babel을 사용해 개발 중에는 최신 문법을 사용하고 결과물은 구 버전 브라우저 등에 호환 되도록 트랜스파일했습니다.

또는 babel 없이 package.json에 아래 줄을 작성하면 Node.js에 ESM 사용이 가능합니다.

```json
"type": "module"
```

단, `import` 문 작성시에 파일 확장자까지 명시해야합니다. (브라우저와의 호환을 위해) 또한, 모듈 파일의 확장자는 `.mjs`로 명시함으로써 해당 파일이 모듈로 동작한다는 것을 알리는 것이 권장됩니다.

```javascript
import { a } from './a.mjs';
```

<br>
<br>

# 참고

> [nodemon - npm](https://www.npmjs.com/package/nodemon)

> [The minimal Node.js with Babel Setup](https://www.robinwieruch.de/minimal-node-js-babel-setup/)

---
