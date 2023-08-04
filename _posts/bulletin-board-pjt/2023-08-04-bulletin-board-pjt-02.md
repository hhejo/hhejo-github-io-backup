---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 02"
date: 2023-08-04
last_modified_at: 2023-08-04
excerpt: "express.json(), req.params, redirect, react-router-dom, nav"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, react-router-dom]
---

---

<br>

# Server

## express.json()

요청 데이터를 받기 위해 미들웨어를 설정했습니다.

```javascript
app.use(express.json());
```

위 문장이 없으면, `req.body`가 빈 객체로 나타납니다.

> [express.json([options])](https://expressjs.com/ko/api.html#express.json)

`express.json()`은 내장 미들웨어로, `body-parser`를 기반으로 JSON 페이로드 요청을 구문 분석합니다.

## URL 변경

기본 URL을 변경했습니다.

```
/api
```

## req.params

요청에 들어오는 파라미터를 `req.params`로 잡을 수 있다는 것을 배웠습니다.

## 리다이렉트 문제

클라이언트에서 게시글을 작성하고 서버로 데이터를 보내면, 서버에서 데이터를 처리(DB에 저장)하고 클라이언트의 화면을 게시글 작성 화면에서 작성한 해당 게시글 화면으로 넘겨야 했습니다.

`res.redirect()`를 사용했더니, 클라이언트 주소는 `http://localhost:5173`이고, 서버 주소는 `http://localhost:3000`이어서 에러가 발생했습니다.

CORS 문제였던 것 같은데, 해결 방법을 모르겠어서 방식을 변경했습니다.

서버는 그냥 `200` 상태 코드와 작성한 게시글의 정보를 주고, 클라이언트는 해당 정보를 받아서 클라이언트에서 리다이렉트를 직접 진행해서 해결했습니다.

<br>
<br>

# Client

클라이언트가 저한테는 제일 어려웠습니다.

## Router

예전에 사용했던 라우터와 다른 최신의 방식을 공식 문서를 참고해 사용했습니다.

```javascript
// main.jsx
// ...
import { RouterProvider } from 'react-router-dom';
import { router } from './router';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

`<RouterProvider router={router} />`에 라우터가 들어갑니다.

```javascript
// router.jsx
import { createBrowserRouter } from 'react-router-dom';
// ...

export const router = createBrowserRouter([
  {
    path: '/',
    element: <App />,
    children: [
      {
        path: '/',
        element: <Home />,
      },
      {
        path: 'board',
        element: <Board />,
      },
      {
        path: 'board/:boardId',
        element: <BoardDetail />,
      },
      {
        path: 'board/create',
        element: <CreateBoard />,
      },
    ],
  },
]);
```

내비게이션 바 때문에 `children` 속성에 페이지들을 넣었습니다.

## Navigation Bar

```javascript
<React.StrictMode>
  <RouterProvider router={router} />
</React.StrictMode>
```

위 처럼 라우터가 저렇게 묶여서 내비게이션 바를 어떻게 해야 할지 잘 몰랐습니다.

고민하고 검색을 해본 결과, 루트 경로에 `children`으로 페이지들을 넣어서 해결했습니다.

`<Outlet />` 컴포넌트에 대해 배웠습니다.

```javascript
import { Outlet } from 'react-router-dom';
// ...

const App = () => {
  return (
    <>
      <Nav />
      <br></br>
      <Outlet />
    </>
  );
};
```

맞는 방법인지 잘 모르겠지만 일단 진행해보고 나중에 변경할 필요가 생기면 그렇게 하겠습니다.

## Redirect

공식문서에 `redirect()` 메서드가 있길래 사용해봤는데 작동하지 않아서 헤맸습니다.

그래서 `useNavigate()` 훅을 사용해 `navigate(url, { replace: true })`로 해결했습니다.

게시글을 생성하고, 생성이 완료되면 생성된 해당 게시글의 id를 서버로부터 받아 클라이언트에서 리다이렉트합니다.

## useParams()

React에서 파라미터를 전달받을 수 있습니다.

```javascript
const { boardId } = useParams();
```

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/c2698cd57b59d1b0373756ba39fda6f355c06840)

<br>
<br>

# 참고

> [[React] Router v6 리다이렉트 및 경로 이동 처리(새로고침X): 404에러 페이지 렌더링 활용](https://curryyou.tistory.com/476)

---
