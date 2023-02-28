---
title:  "npm과 package.json"
date: 2023-02-11
last_modified_at: 2023-02-11
excerpt: "package.json에 대해 자세히 알아보자"
categories:
  - JavaScript
tags:
  - [JavaScript, npm, package.json]
---

---

# npm

`Node Package Manager`

software registry

Node.js로 만든 패키지(모듈) 설치하고 관리해주는 프로그램

몇 줄의 명령어로 기존에 공개된 모듈들을 설치하고 활용 가능

패키지 업데이트, 버전 관리가 쉬워짐

패키지란 어떤 사람이 업로드한 노드 모듈

JavaScript를 위한 패키지 관리자

예전에는 따로 설치했지만 현재는 Node.js에 내장

`npm install ...`, `npm i ...`

`npm init`(`npm init -y`)을 사용해 `package.json` 파일 생성

## package.json

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

Node.js 프로젝트에서는 많은 패키지를 사용하게 되고 각각의 버전도 빈번하게 업데이트 되기 때문에 현재 프로젝트가 의존하고 있는 패키지를 일괄적으로 관리할 필요가 있음. `package.json` 파일을 통해 프로젝트 정보와 패키지의 의존성을 관리

개발자가 배포한 패키지에 대해 다른 사람들이 관리하고 설치하기 쉽게 하기 위한 문서

npm에 패키지를 배포하고 npm registry에 올리기 위해 반드시 필요한 문서

자신의 프로젝트가 의존하는 패키지의 리스트, 프로젝트의 버전 명시

npm이라는 오픈 소스 패키지 생태계를 사용하기 위한 명세이자 프로젝트의 의존성 관리를 위한 명세. 또 이 생태계로의 배포를 위한 명세

`scripts`

`run` 명령어를 통해 실행할 것들을 작성 `npm run ...`

`dependencies`

설치할 모듈들

package.json이 있다면 `npm install` 명령어로 해당 프로그램에 사용한 모듈들을 간단히 내려받을 수 있음

field들 확인

`name`, `version`

반드시 있어야 하는 fields. 해당 패키지의 이름과 버전

`description`

패키지에 대한 설명

`main`

패키지의 진입점(entry point)이 되는 모듈의 ID. 사용자가 foo라는 이름의 패키지를 설치하고 require("foo")를 통해 모듈을 import하면, "main"으로 지정한 모듈의 exports 객체 반환. 패키지 root의 상대경로로 지정해야 함. 지정하지 않은 경우 root 폴더의 index.js가 기본값

`scripts`

command를 alias를 통해 지정해 둘 수 있는 디렉터리

`keywords`

키워드를 문자열 배열로 설명

`author`

배포자 한 사람을 위한 필드. 다수의 사람을 표시하려면 `contributors`로 작성

`license`

배포한 패키지에 대해 패키지 사용자가 패키지를 사용하는 데 어떤 권한과 제한 사항이 있는지 명시

`dependencies`, `devDependencies`

패키지의 이름에 해당 패키지의 버전 범위를 매핑한 형태의 객체로 지정

해당 패키지, 프로젝트가 어떤 외부 라이브러리에 의존성(dependency)을 가지고 있는지 프로젝트가 의존하고 있는 패키지를 일괄적으로 관리하기 위한 필드 -> 해당 프로젝트가 어떤 라이브러리를 갖고 있어야 제대로 구동될수 있는지에 대한 명세

`devDependencies`는 개발시에만 필요한 의존 패키지들을 의미

### package-lock.json

version range 형태로 표시하는 `package.json`보다 모듈들의 버전이 정확하게 표시

`package-lock.json`이 존재하면 `npm install`은 `package.json`가 아닌 `package-lock.json`를 사용하여 `node_modules`를 생성

---

## 버전

npm은 Semantic Version에 따라 패키지를 관리

Major, Minor, Patch 세 가지 숫자를 조합해 버전 관리

1. Major Version : 기존 버전과 호환되지 않게 변경
2. Minor Version : 기존 버전과 호환되면서 기능 추가, 개선
3. Patch Version : 기존 버전과 호환되면서 버그 수정
4. `~` : 마이너 버전이 명시되어 있으면 패치 버전을 변경
5. `^` : 정식 버전에서 마이너와 패치 버전을 변경

---

## 명령어

`install`

`view`

`run`

---

## 옵션

`-D`, `-dev`, `--save-dev`

개발할 때는 해당 패키지 사용, 개발 이후에는 사용되지 않음

`-g`

패키지 전역 설치

`--save`

install로 설치시 package.json의 dependencies 부분 업데이트 해주는데 이 옵션을 붙이면 자동적으로 package.json을 업데이트 해줌. npm 5부터는 기본 적용이기 때문에 굳이 쓰지 않아도 됨

---

## 기타

`./node_modules`

로컬 설치시 현재 패키지의 루트 디렉터리의 `./node_modules`에 설치

글로벌 설치시 `/usr/local` 또는 노드가 설치된 곳에 설치

`require()`를 한다면 로컬에 설치

커맨드 라인에서 실행한다면 글로벌 설치

`npm ls`

그래프 분석 명령어

`npm ls -g --depth=0`

전역 설치 패키지 확인

`npm uninstall <package-name>`

`npm update <package-name>`

`npm outdated`

업데이트 필요한 모듈 확인 (`current`, `wanted`)

`npm -v`

npm 버전 확인

`npm start`

`npm run <script-name>`

`npm root -g`

전역 패키지 설치 폴더 확인

`npm view <package-name>`

패키지 정보 참조

`npm help`, `npm help <command>`, `npm <command> -h`

`npm list depth <number>`

depth 0은 패키지 이름, 1은 dependency, 2는 그 다음 dependency로 특정 depth의 패키지의 리스트 확인

## cache

패키지 설치 후 삭제하고 재설치할 때 꼬이는 문제를 방지하기 위해 알아두자

보통 `/npm-cache/_cache` 폴더에 저장

`npm cache clean --force`

npm의 cache 전부 삭제

`npm cache verify`

cache에서 꼬인 부분을 체크 및 해결 (추천)

## npx

npm의 5.2.0부터 추가된 도구. npm을 더 편리하게 사용

npm 패키지를 설치하지 않고(임시 설치) 실행하는 용도

react는 설치해도 실행이 안 되기 때문에 `create-react-app` 명령어를 붙여 사용

1. 패키지 임시 설치 및 실행
2. npm으로 설치할 명령어 축소
3. 다른 버전의 노드 실행 가능
4. gist에 스크립트 공유 가능

기본적으로 실행되어야 할 패키지가 경로에 있는지 먼저 확인. 경로에 있다면 실행. 없다면 패키지가 설치되지 않은 것이므로 npx가 최신 버전의 패키지를 설치하고 실행 (임시로 설치하고 실행 후 설치된 것이 사라짐)

npx로 Node.js의 특정 버전을 사용해 js 파일 실행 가능 (프로젝트의 Node.js 버전 별 실행환경 확인시 유용)

npm으로 패키지 설치 -> 해당 폴더로 들어가 실행 파일 실행

npx는 한 번에 가능

`Github gist`는 짧은 코드, 메모 등을 기록 또는 공유 목적으로 사용하는 서비스 (간단한 코드)

`npx [github gist 주소]`를 이용해 실행 가능 (실행 후 설치된 것이 지워지기 때문에 편리)
