---
title:  "Node.js의 환경변수 관리"
date: 2023-07-22
last_modified_at: 2023-07-29
excerpt: "Node.js에서 환경변수를 관리하기 위한 process, .env, dotenv에 대해 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, process, env, dotenv]
---

---

<br>

# Command Line Applications

명령줄에서 실행되는 애플리케이션을 의미합니다.

CLI (Command Line Interface) App으로도 불립니다.

사용자는 터미널 명령어로 클라이언트와 상호작용합니다.

동일한 작업(배포, 테스트 등)을 반복 수행하는 경우, 스크립트를 사용해 해당 단계를 자동화해 시간을 절약할 수 있습니다.

Node.js로 CLI App을 작성할 수 있습니다.

<br>
<br>

# 환경변수 (Environment Variable)

개발, 테스트, 운영 등의 여러 환경에 애플리케이션을 배포할 때, 각 환경에 따라 다르게 설정되어야 하는 값들을 환경변수에 설정합니다.

또, API 키, 데이터베이스 비밀번호와 같은 외부로부터 노출되지 않아야 하는 데이터를 환경 변수로 설정해 코드 외부에 위치시킵니다.

Node.js는 코어 모듈, 즉 프로세스가 시작될 때 설정된 모든 환경변수를 호스팅하는 `process` 아래에 `env` 속성을 제공합니다.

`process.env`는 전역 객체로, 런타임 중에 주입됩니다. 환경변수를 설정하면 런타임 중에 `process.env`에 로드되고 이후에 접근할 수 있습니다.

그리고 환경변수는 항상 문자열의 값을 가집니다. 다른 자료형식으로 사용하고 싶다면 직접 변환해야 합니다.

Node.js 인터프리터로 값을 확인해보겠습니다.

```bash
$ nvm use --lts
$ node
```

```bash
> process.env.PWD
'/Users/hejo/Documents/my-pjt'
> process.env.PASSWORD
undefined
```

`process.env.PASSWORD`는 제가 임의로 지정한 속성이기 때문에 `undefined`가 나옵니다.

이제 환경변수를 설정해보겠습니다.

```bash
❯ PASSWORD=abcd1234 node
Welcome to Node.js v18.16.1.
Type ".help" for more information.
> process.env.PASSWORD
'abcd1234'
```

지정하고싶은 환경변수의 값을 지정하고 그 뒤에 `node`로 인터프리터를 실행합니다.

`process.env.PASSWORD`의 값이 잘 나오는 것을 볼 수 있습니다.

또는 인터프리터 실행 중에 값을 대입해도 됩니다.

```bash
> process.env.PASSWORD=1234
1234
> process.env.PASSWORD
'1234'
```

`delete`를 이용해 지울 수도 있습니다.

```bash
> delete process.env.PASSWORD
true
> process.env.PASSWORD
undefined
```

하지만 CLI를 종료하고 `node`로만 실행하면 환경변수 값은 사라져있습니다. 해당 환경변수 값은 지정했을 때만 유효하기 때문에 매번 장황하게 명령줄에 작성하고 실행하는 것은 번거롭습니다.

```bash
$ node
```

```bash
Welcome to Node.js v18.16.1.
Type ".help" for more information.
> process.env.PASSWORD
undefined
```

`export` 명령어를 사용해 프로세스가 종료된 후 다시 실행해도 지정했던 환경변수의 값을 얻을 수 있습니다.

```bash
$ export PASSWORD=1234
$ node
```

```bash
Welcome to Node.js v18.16.1.
Type ".help" for more information.
> process.env.PASSWORD
'1234'
```

그러나 명령줄을 실행했던 터미널을 종료하고 다시 똑같이 반복하면, `PASSWORD`의 값이 `undefined`가 나오게 됩니다.

`.env` 파일과 `dotenv` 패키지를 사용해 환경변수를 관리하는 방법에 대해 알아보겠습니다.

<br>
<br>

# dotenv

`.env` 파일을 사용해 프로젝트의 소스 코드에서 접근할 수 있도록 Node.js의 환경변수를 설정할 수 있습니다.

그러나 Node.js는 기본적으로 `.env`의 파일을 로드하지 않으므로 `dotenv` 패키지를 사용해 해당 파일을 로드하여 환경변수 값에 접근해야 합니다.

아래 명령을 입력해 설치합니다.

```bash
npm install dotenv
```

프로젝트 루트 폴더에 `.env` 파일을 생성합니다.

```
PASSWORD="1234"
```

## CJS

```javascript
require('dotenv').config();

const { PASSWORD } = process.env;

console.log(`PASSWORD: ${PASSWORD}`);
```

## ESM

```javascript
import dotenv from 'dotenv';

dotenv.config();

const { PASSWORD } = process.env;

console.log(`PASSWORD: ${PASSWORD}`);
```

`dotenv.config()`를 환경변수에 접근하기 전에 빨리 호출해야 합니다.

혹은 아래와 같이 작성합니다.

```javascript
import 'dotenv/config';
```

해당 파일을 모듈로 작동시킬 때, `dotenv`의 `config`가 선행되도록 해야합니다.

## 주의

`.env` 파일은 `.gitignore` 파일에 작성해 깃허브에 올라가지 않게 하는 것이 좋습니다.

```
.env
```

## 예시

아래 예시처럼 `.env` 파일을 구성할 수 있습니다.

```
PORT = 8000
NODE_ENV = development
CLIENT_URL = "http://localhost:3000"
DATABASE_URL = "..."
```

<br>
<br>

# 참고

> [Node.js에서 환경 변수 다루기 (process.env)](https://www.daleseo.com/js-node-process-env/)

> [dotenv로 환경 변수를 .env 파일로 관리하기](https://www.daleseo.com/js-dotenv/)

> [The minimal Node.js with Babel Setup](https://www.robinwieruch.de/minimal-node-js-babel-setup/)

> [Reading Environment Variables From Node.js](https://www.geeksforgeeks.org/reading-environment-variables-from-node-js/)

> [Node Environment Variables: Process env Node](https://www.knowledgehut.com/blog/web-development/node-environment-variables)

> [Node.js process.env Property](https://www.geeksforgeeks.org/node-js-process-env-property/)

> [Dotenv - npm](https://www.npmjs.com/package/dotenv)

---
