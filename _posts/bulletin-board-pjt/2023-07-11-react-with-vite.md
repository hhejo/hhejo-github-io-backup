---
title: "게시판 프로젝트 02 - React, Vite 초기 설정"
date: 2023-07-11 00:00:00 +0900
last_modified_at: 2023-07-11 00:00:00 +0900
categories: [BulletinBoardPjt]
tags: [bulletin-board-pjt, react, vite, babel, webpack, cra, npm]
---

프론트엔드 작업 설정하기. CRA가 아닌 Vite를 이용해 React를 시작하자

## [React](https://react.dev)

- 리액트 공식문서의 [Add React to an Existing Project](https://react.dev/learn/add-react-to-an-existing-project) 여기를 참고해서 진행했다.
- 그런데 Babel, Webpack 같은 것도 써야 할 것 같아서 어떻게 해야할지 고민했다.
- 뭔가 자세히는 모르겠는데 여기저기 검색해보니 써야 할 것 같았다.
- 그리고 공식문서를 원래 https://ko.legacy.reactjs.org/ 여기를 참고했었는데, 새로운 사이트에서 업데이트 하는 것 같다.

## [Babel](https://babeljs.io/)

- JavaScript Compiler
- 최신 문법의 자바스크립트를 구 버전에서도 호환될 수 있도록 변환 해준다.

## [Webpack](https://webpack.kr/)

- JavaScript 애플리케이션을 위한 정적 모듈 번들러

## CRA (Create React App)

- `npx create-react-app [애플리케이션 이름]`
- 예전에 리액트로 프론트엔드 작업을 하려면 이 명령어로 시작했었다.
- 리액트로 작업하기 위해 필요한 설정을 전부 자동으로 해준다.

## [Vite](https://ko.vitejs.dev/)

- 프론트엔드 빌드 툴
- Webpack 보다 빠르다! 인기가 많다

## Vite로 React 시작하기

### 1.

```sh
npm create vite@latest
```

진행하면 선택지를 고르는데, 나는 React, JavaScript를 각각 골랐다.

프로젝트 이름은 어떻게 해야 할 지 몰라서 일단 frontend로 했다.

frontend 디렉터리에 넣을 예정이라 그렇게 했는데 나중에 수정해야 될지도 모르겠다.

### 2.

해당 디렉터리 안으로 들어가서

```sh
npm i
```

실행하고, 이후

```sh
npm run dev
```

이렇게 하니 서버가 실행되었다. (포트 번호가 좀 이상함)

## 에러 모음

`vite`, `npm vite`, `npm run vite` 등등 명령어가 다 안 먹혀서 왜 안 되나 한참 찾았다.

공식문서에서 명령어 본 대로 썼는데 안 돼서 계속 찾아봤었다. `npm run dev`로 해야 실행이 된다.

그리고 `npm i`를 한번 실행해줘야 하는 것 같다.

## 현재까지 디렉터리 구조

```
/bulletin-board-pjt
--- /backend
--- /frontend
README.md
```

일단 이렇게 진행해야겠다.
