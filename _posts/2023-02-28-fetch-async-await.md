---
title:  "fetch, async, await"
date: 2023-02-28
last_modified_at: 2023-02-28
excerpt: "HTTP 요청 관련 fetch, async, await에 대해 알아보자"
categories:
  - React
tags:
  - [React, JavaScript, fetch, async, await]
---

---

<br>

# Section 14 HTTP Requests, DB

## Interact with DB

## Sending HTTP Requests, Using Responses

## Handling Errors, Loading State

브라우저에서 실행되는 JavaScript 코드가 DB와 직접 통신하면 안 됨

클라이언트 내부에서 DB에 직접 연결을 하면 DB의 인증 정보를 노출하게 됨

React App은 일반적으로 백엔드 서버 (백엔드 API)라고 불리는 서로 다른 URL로의 요청을 전송하는 서버와 통신

인증 정보는 백엔드 앱에 저장하고, 백엔드 앱과의 통신은 보안에 관련된 세부 사항이 필요 없기 때문에 DB와 안전하게 통신을 주고 받을 수 있음

## `fetch` API

promise 객체를 반환하므로 `then()`, `catch()`로 이어줌

`fetch(URL, {요청 옵션...})`

```javascript
// App.js
// ...
function App() {
  const [movies, setMovies] = useState([]);

  function fetchMoviesHandler() {
    fetch("https://swapi.dev/api/films/")
      .then((response) => {
        return response.json();
      })
      .then((data) => {
        const transformedMovies = data.results.map((movieData) => {
          return {
            id: movieData.episode_id,
            title: movieData.title,
            openingText: movieData.opening_crawl,
            releaseDate: movieData.release_date,
          };
        });
        setMovies(transformedMovies);
      });
  }
  // const dummyMovies = [
  //   {
  //     id: 1,
  //     title: 'Some Dummy Movie',
  //     openingText: 'This is the opening text of the movie',
  //     releaseDate: '2021-05-18',
  //   },
  //   {
  //     id: 2,
  //     title: 'Some Dummy Movie 2',
  //     openingText: 'This is the second opening text of the movie',
  //     releaseDate: '2021-05-19',
  //   },
  // ];

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        <MoviesList movies={movies} />
      </section>
    </React.Fragment>
  );
}

export default App;
```

버튼을 누르면 백엔드 앱에 요청하고, 백엔드 앱은 DB 서버에 요청해서 다시 React App으로 응답

## `async`, `await`

함수 앞에 `async` 예약어 작성

promise를 반환하는 작업 앞에 `await` 예약어 사용

비교해보기

```javascript
// App.js
// ...
function App() {
  const [movies, setMovies] = useState([]);

  async function fetchMoviesHandler() {
    // async 추가
    const response = await fetch("https://swapi.dev/api/films/"); // then 삭제, await 추가
    const data = await response.json(); // then 삭제, await 추가

    const transformedMovies = data.results.map((movieData) => {
      return {
        id: movieData.episode_id,
        title: movieData.title,
        openingText: movieData.opening_crawl,
        releaseDate: movieData.release_date,
      };
    });
    setMovies(transformedMovies);
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>
        <MoviesList movies={movies} />
      </section>
    </React.Fragment>
  );
}

export default App;
```

## `try`, `catch`

로딩 기능도 추가해보기

- fetch를 시작하기 전에 `isLoading` 상태를 true로 변경

- fetch가 끝나면 `isLoading` 상태를 false로 변경

- `isLoading`의 상태에 따라 조건문으로 출력할 내용 렌더링

- 혹시 내용이 없다면 배열의 길이가 0인 것이므로 그에 따라 출력할 내용이 없다고 렌더링하기

- 오류가 있는 경우, 그에 따른 에러 출력하기

- axios의 경우 에러를 만들어 주지만, fetch는 우리가 만들어야 함 (try-catch 사용)

```javascript
// App.js
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false); // 로딩중
  const [error, setError] = useState(null); // 에러 (null)

  async function fetchMoviesHandler() {
    setIsLoading(true);
    setError(null);

    try {
      const response = await fetch("https://swapi.dev/api/films/");
      // response의 ok 필드를 사용
      // status 필드를 사용해도 됨
      if (!response.ok) {
        throw new Error("Something went wrong!"); // 이게 message가 됨
      }
      const data = await response.json();
      const transformedMovies = data.results.map((movieData) => {
        return {
          id: movieData.episode_id,
          title: movieData.title,
          openingText: movieData.opening_crawl,
          releaseDate: movieData.release_date,
        };
      });
      setMovies(transformedMovies);
    } catch (error) {
      setError(error.message); // throw한 것을 error로 catch하고 setError로 error 설정
    }
    setIsLoading(false);
  }

  // 로딩중인지, 에러인지, 결과가 없는지 나타낼 내용
  let content = <p>Found no movies.</p>;
  if (movies.length > 0) {
    content = <MoviesList movies={movies} />;
  }
  if (error) {
    content = <p>{error}</p>;
  }
  if (isLoading) {
    content = <p>Loading...</p>;
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>{content}</section>
    </React.Fragment>
  );
}

export default App;
```

## `useEffect()`, `useCallback()`과 함께 사용하기

페이지가 렌더링 되자마자 fetch를 실행하려면, `useEffect()` 훅을 사용

```javascript
// App.js
import React, { useState, useEffect, useCallback } from "react";
// ...
function App() {
  const [movies, setMovies] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);

  // useEffect에 아래 함수를 의존성 목록에 올리는데, 컴포넌트가 재렌더링될 때마다 함수가 변경됨
  // 함수 내용은 같아도 함수 포인터는 다름
  // 그래서 의존성으로 추가하면 무한 루프 발생 !!
  // 해결책으로 의존성 배열에서 제거하면 무한 루프 없이 원하는 결과가 나오지만, 해당 함수가
  // 외부 상태를 사용한다면 의도치 않은 버그가 발생할 수 있음
  // 따라서 useCallback을 사용해 함수를 감싸줌
  // 함수에 있는 모든 의존성을 나열해야 하지만, 이 함수에는 외부 의존성이 없음
  // fetch API는 글로벌 브라우저 API이기 때문에 의존성 아님
  // 내부에 setState 관련 함수는 리액트에서 변경이 절대 없을 것이라 보장하기 때문에 의존성 아님
  // useCallback 안에 async 사용 가능
  const fetchMoviesHandler = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    try {
      // ...
    } catch (error) {
      setError(error.message);
    }
    setIsLoading(false);
  }, []);
  //////////
  // useEffect 사용
  useEffect(() => {
    fetchMoviesHandler();
  }, [fetchMoviesHandler]);
  //////////
  let content = <p>Found no movies.</p>;
  if (movies.length > 0) {
    content = <MoviesList movies={movies} />;
  }
  if (error) {
    content = <p>{error}</p>;
  }
  if (isLoading) {
    content = <p>Loading...</p>;
  }

  return (
    <React.Fragment>
      <section>
        <button onClick={fetchMoviesHandler}>Fetch Movies</button>
      </section>
      <section>{content}</section>
    </React.Fragment>
  );
}

export default App;
```

## Firebase

Firebase 콘솔로 이동

새로운 프로젝트 추가 (구글 통계는 제외해도 됨)

-> Realtime Database 사용 (Cloud Firestore가 강력하지만 이것도 충분)

Start in Test Mode

URL 뒤에 `/movies.json`을 추가

`JSON.stringify()` : json 형태로 만들어 줌

`.json()` : json 형태 가져오기

POST 요청 fetch API로 보내기

```javascript
const response = await fetch("URL/movies.json", {
  method: "POST",
  body: JSON.stringify(movie), // JSON.stringify()
  headers: {
    "Content-Type": "application/json",
  },
});
const data = await response.json(); // .json()
```

코드는 따로 참고

---
