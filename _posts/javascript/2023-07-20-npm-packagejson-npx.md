---
title:  "npm과 package.json, 그리고 npx"
date: 2023-07-20
last_modified_at: 2023-07-20
excerpt: "Node.js에서 사용되는 npm과 프로젝트 마다 항상 보게 되는 package.json에 대해 알아봅시다. 그리고 npx는 무엇일까?"
categories:
  - JavaScript
tags:
  - [javascript, nodejs, npm, npx, packagejson]
---

---

<br>

# npm (Node Package Manager)

Node.js로 만든 패키지(모듈)를 설치하고 관리하는 표준 패키지 관리자입니다.

예전에는 따로 설치했었지만, 현재는 Node.js에 내장되어 있기 때문에 Node.js만 설치해도 `npm` 명령어를 사용해 npm을 사용할 수 있습니다.

npm을 이용해서 몇 줄의 명령어로 기존에 공개된 모듈들을 설치하고 활용할 수 있으며, 패키지 업데이트와 버전 관리를 손쉽게 합니다.

설치된 모듈은 CJS 방식이나 ESM 방식으로 가져와 사용하면 됩니다.

로컬에 설치하면 현재 패키지의 루트 디렉터리의 `./node_modules`에 설치되며, 전역 설치하면 `/usr/local` 또는 노드가 설치된 곳에 설치됩니다.

다른 패키지 관리자로는 `yarn`, `pnpm`이 있습니다.

## npm CLI

### 초기화

```bash
npm init -y
```

Node.js가 설치된 상태에서, 위 명렁어를 터미널에 입력해 `package.json` 파일을 생성할 수 있습니다. `-y` 옵션을 이용해 package.json 파일에 기본 사항을 자동적으로 입력해 생성할 수 있습니다. 해당 옵션은 필수가 아니기 때문에 자유롭게 입력할 수 있습니다.

### 패키지 설치

```bash
npm install
```

하나의 프로젝트에 package.json 파일이 존재할 때, 위 코드로 package.json 파일에 작성된 의존성 패키지들을 `/node_modules` 폴더에 설치합니다. 해당 폴더가 존재하지 않는다면, `node_modules` 폴더를 새로 생성한 후 진행합니다. `npm i`로 줄여 입력할 수도 있습니다.

```bash
npm install <package-name>
```

위 명령어를 이용해 원하는 패키지를 원격으로 설치할 수도 있습니다. 패키지를 설치하면, npm 5 버전 이후부터는 자동적으로 `dependencies` 항목에 추가합니다. 그러므로 `--save` 옵션을 추가할 필요가 없습니다.

```bash
npm install <package-name>@<version>
```

위 명령어로 특정 버전의 패키지를 설치할 수 있습니다.

옵션을 통해 특정 목표를 달성할 수 있습니다.

1. `-D`, `-dev`, `--save-dev` : 개발할 때는 해당 패키지를 사용하나, 개발 이후에는 사용되지 않음
2. `-g` : 패키지 전역 설치

### 지난 버전 확인

```bash
npm outdated
```

설치된 모든 종속성을 확인하고 현재 버전을 npm 레지스트리의 최신 버전과 비교합니다. 모든 패키지가 최신 버전이면, 아무것도 출력하지 않습니다.

### 업데이트

```bash
npm update
```

위 명령어로 npm은 자동으로 패키지들의 최신 버전을 탐색하고, 최신 버전이 있다면 업데이트합니다.

### 스크립트 실행

Node.js에서 npm 스크립트는 서버 시작, 프로젝트 빌드 및 테스트 실행을 위해 사용됩니다.

웹 개발 및 배포에는 많은 반복 작업이 필요하기 때문에, 이를 스크립트를 이용해 자동화할 수 있습니다.

```bash
npm run <task-name>
```

package.json에 실행할 작업을 명시해 명령어로 지정할 수 있습니다.

```json
{
  "scripts": {
    "start-dev": "node lib/server-development",
    "start": "node lib/server-production",
    "watch": "webpack --watch --progress --colors --config webpack.conf.js",
    "dev": "webpack --progress --colors --config webpack.conf.js",
    "prod": "NODE_ENV=production webpack -p --config webpack.conf.js"
  }
}
```

위 예시를 아래와 같이 간단하게 실행할 수 있습니다.

```bash
npm run watch
npm run dev
npm run prod
```

다른 npm 스크립트 내에 npm 스크립트를 사용할 수도 있습니다.

```json
{
  "name": "example",
  "scripts": {
    "start": "npm run lite",
    "lite": "lite-server" 
  }
}
```

위 스크립트를 `npm run start`로 실행해 또다른 명령을 실행할 수 있습니다.

### 기타 명령어

```bash
npm ls
```

의존성 그래프 분석 명령어입니다.

```bash
npm ls -g --depth=0
```

전역 설치 패키지를 확인하는 명령어입니다.

```bash
npm -v
```

npm 버전을 확인하는 명령어입니다.

```bash
npm root -g
```

전역 패키지 설치 폴더를 확인하는 명령어입니다.

```bash
npm view <package-name>
```

패키지 정보를 참조하는 명령어입니다.

```bash
npm help
npm help <command>
npm <command> -h
```

도움말 관련 명령어입니다.

```bash
npm list depth <number>
```

depth 0은 패키지 이름, 1은 dependency, 2는 그 다음 dependency로 특정 depth의 패키지의 리스트를 확인합니다.

<br>
<br>

# npx

npm의 5.2.0부터 추가된 도구로, npm 패키지를 설치하지 않고(임시 설치) 실행할 수 있어 npm을 더 편리하게 사용할 수 있게 합니다. Node.js로 빌드하고 npm registry를 통해 게시된 코드를 일회성으로 실행할 수 있습니다.

package.json에 스크립트를 작성하고 npm을 이용해 실행하는 번거로운 작업 대신 npx를 이용해 로컬에 설치된 패키지를 바로 실행해볼 수 있습니다.

또, npx로 Node.js의 특정 버전을 사용해 실행 가능하기 때문에, 프로젝트의 Node.js 버전 별 실행 환경을 확인할 때 유용합니다. 불필요한 패키지 설치를 방지하고 버전 관리, 종속성 문제에서 벗어날 수 있습니다.

npm으로는 패키지를 설치한 후, 해당 폴더로 진입해 파일을 실행하지만, npx는 한번에 가능합니다. 기본적으로 실행되어야 할 패키지가 경로에 있는지 확인한 후, 경로에 존재한다면 실행합니다. 존재하지 않는다면 패키지가 설치되지 않은 것이므로, npx가 최신 버전의 패키지를 설치하고(실행 후 없어짐) 실행합니다.

짧고 간단한 코드, 메모 등을 기록 또는 공유하는 `Github gist`에 게시된 코드를 실행할 수도 있습니다.

```bash
npx <github-gist-주소>
```

<br>
<br>

# package.json

```json
{
  "name": "PROJECT_DIRECTORY",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

Node.js 프로젝트에서는 많은 패키지를 사용하게 되고, 각각의 버전도 빈번하게 업데이트 되기 때문에 현재 프로젝트가 의존하고 있는 패키지를 일괄적으로 관리할 필요가 있습니다.

`package.json` 파일을 통해 프로젝트 정보와 패키지의 의존성을 관리합니다.

npm에 패키지를 배포하고 npm registry에 올리기 위해 반드시 필요한 문서로, 개발자가 배포한 패키지에 대해 다른 사람들이 관리하고 쉽게 설치할 수 있습니다.

여기에는 자신의 프로젝트가 의존하는 패키지의 리스트와 프로젝트의 버전을 명시합니다.

npm이라는 오픈 소스 패키지 생태계를 사용하기 위한 명세이자 프로젝트의 의존성 관리를 위한 명세이며, 해당 생태계로의 배포를 위한 명세입니다.

### scripts

`npm run` 명령어를 통해 실행할 스크립트를 작성합니다. alias를 통해 디렉터리를 지정할 수 있습니다.

### dependencies, devDependencies

해당 프로젝트에서 설치할 모듈들입니다.

package.json이 있다면 `npm install` 명령어로 해당 프로그램에 사용한 모듈들을 간단히 내려받을 수 있습니다.

패키지의 이름에 해당 패키지의 버전 범위를 매핑한 형태의 객체로 지정합니다.

해당 패키지, 프로젝트가 어떤 외부 라이브러리에 의존성(dependency)을 가지고 있는지 프로젝트가 의존하고 있는 패키지를 일괄적으로 관리하기 위한 필드 -> 해당 프로젝트가 어떤 라이브러리를 갖고 있어야 제대로 구동될수 있는지에 대한 명세입니다.

`devDependencies`는 개발시에만 필요한 의존 패키지들을 의미합니다.

### name, version

반드시 있어야 하는 필드로, 해당 패키지의 이름과 버전을 나타냅니다.

### description

패키지에 대한 설명을 나타냅니다.

### main

패키지의 진입점(entry point)이 되는 모듈의 ID. 사용자가 foo라는 이름의 패키지를 설치하고 require("foo")를 통해 모듈을 import하면, "main"으로 지정한 모듈의 exports 객체 반환. 패키지 root의 상대경로로 지정해야 함. 지정하지 않은 경우 root 폴더의 index.js가 기본값

### keywords

키워드를 문자열 배열로 설명합니다.

### author

배포자 한 사람을 위한 필드입니다. 다수의 사람을 표시하려면 `contributors`로 작성합니다.

### license

배포한 패키지에 대해 패키지 사용자가 패키지를 사용하는 데 어떤 권한과 제한 사항이 있는지 명시합니다.

<br>
<br>

# package-lock.json

version range 형태로 표시하는 `package.json`보다 모듈들의 버전이 정확하게 표시됩니다.

`package-lock.json`이 존재하면 `npm install`은 `package.json`가 아닌 `package-lock.json`를 사용하여 `node_modules`를 생성합니다.

<br>
<br>

# 버전 표현

npm은 Semantic Version에 따라 패키지를 관리합니다.

Major, Minor, Patch 세 가지 숫자를 조합합니다.

1. Major Version : 기존 버전과 호환되지 않게 변경
2. Minor Version : 기존 버전과 호환되면서 기능 추가, 개선
3. Patch Version : 기존 버전과 호환되면서 버그 수정
4. `~` : 마이너 버전이 명시되어 있으면 패치 버전을 변경
5. `^` : 정식 버전에서 마이너와 패치 버전을 변경

<br>
<br>

# cache

패키지 설치 후 삭제하고 재설치할 때 꼬이는 문제를 방지하기 위해 알아두면 좋습니다.

보통 `/npm-cache/_cache` 폴더에 저장됩니다.

```bash
npm cache clean --force
```

npm의 cache 전부 삭제합니다.

```bash
npm cache verify
```

cache에서 꼬인 부분을 체크 및 해결합니다. (추천)


<br>
<br>

# 참고

> [Global objects | Node.js v20.4.0 Documentation](https://nodejs.org/api/globals.html#global)

> [An introduction to the NPM package manager](https://nodejs.dev/en/learn/an-introduction-to-the-npm-package-manager/)

> [How to Update NPM Dependencies](https://www.freecodecamp.org/news/how-to-update-npm-dependencies/)

> [Updating packages downloaded from the registry](https://docs.npmjs.com/updating-packages-downloaded-from-the-registry)

> [Running scripts](https://riptutorial.com/node-js/example/4592/running-scripts)

> [Introduction to NPM scripts](https://www.geeksforgeeks.org/introduction-to-npm-scripts/)

> [Introducing npx: an npm package runner](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)

> [npm vs npx — What’s the Difference?](https://www.freecodecamp.org/news/npm-vs-npx-whats-the-difference/)

---
