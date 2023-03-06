---
title:  "Custom React Hooks"
date: 2023-03-02
last_modified_at: 2023-03-03
excerpt: "커스텀 리액트 훅이란?"
categories:
  - React
tags:
  - [React, hooks]
---

---

<br>

# Custom React Hooks

## Custom Hooks

- 결국은 정규 함수. 안에 상태를 설정할 수 있는 로직을 포함한 함수
- 정규 함수와는 다르게 다른 커스텀 훅을 포함한 다른 리액트 훅 사용 가능
- 커스텀 훅을 통해 다른 컴포넌트에서 사용할 수 있는 로직을 커스텀 훅으로 아웃소싱할 수 있고 이를 통해 다양한 컴포넌트에서 호출이 가능
- 로직 재사용이 가능

생성할 때 반드시 이름은 `use`로 시작해야 함

`components` 폴더와 같은 위치에 `hooks` 폴더 생성하고 안에 파일 작성

`setInterval()`, `clearInterval()`

```jsx
// hooks/use-counter.js
import { useState, useEffect } from "react";

const useCounter = (forwards = true) => {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      if (forwards) {
        setCounter((prevCounter) => prevCounter + 1);
      } else {
        setCounter((prevCounter) => prevCounter - 1);
      }
    }, 1000);

    return () => clearInterval(interval);
  }, [forwards]);

  return counter;
};

export default useCounter;
```

커스텀 훅을 사용하는 것은 내장 훅을 사용하는 것과 동일. 함수 같이 호출 (함수이기 때문)

매개변수 받을 수 있고, 함수도 받을 수 있음.

의존성 추가해야 함

```jsx
// components/ForwardCounter.js
import Card from "./Card";
import useCounter from "../hooks/use-counter";

const ForwardCounter = () => {
  const counter = useCounter();

  return <Card>{counter}</Card>;
};

export default ForwardCounter;
```

```jsx
// components/BackwardCounter.js
import Card from "./Card";
import useCounter from "../hooks/use-counter";

const BackwardCounter = () => {
  const counter = useCounter(false);

  return <Card>{counter}</Card>;
};

export default BackwardCounter;
```

HTTP 요청 관련에서 커스텀 훅으로 만들면 좋을 때가 있음

## 사용자 정의 커스텀 HTTP 훅


---
