---
title:  "Effects란? Side Effects와 useEffect 훅"
date: 2022-02-20
last_modified_at: 2022-02-22
excerpt: "Effects, Side Effects, useEffect, Clean-up function"
categories:
  - React
tags:
  - [React, useEffect]
---

---

<br>

# Effects & Side Effects

## Effects

리액트가 하는 일

- UI를 렌더링하고, 사용자 입력, 이벤트에 반응
- JSX를 평가, 계산하고 렌더링, State & Props 관리
- State & Prop이 변하면 컴포넌트 재평가

<br>

## Side Effects

- 리액트가 하는 일을 제외한 나머지들
- 브라우저 스토리지에 데이터 저장
- 백엔드 서버에 HTTP 요청 송신
- 타이머 설정, 관리

이 작업들은 보통의 컴포넌트 평가의 밖에서 일어나야 한다.

HTTP 요청에 대한 응답으로 어떤 state를 변경한다면, 무한 루프에 빠질 수 있음 -> 함수가 다시 실행될 때마다 요청을 보내면, 요청에 대한 응답으로 이 함수를 다시 트리거하는 state를 변경하게 되기 때문

그래서 사이드 이펙트는 직접적으로 컴포넌트 함수에 들어가서는 안 되기 때문에, 특별한 리액트 훅 `useEffect`를 사용한다.

<br>

# `useEffect()` Hook

```jsx
useEffect(() => { ... }, [ dependencies ]);
```

```
() => { ... }
```
- 모든 컴포넌트 평가 후에 실행되어야 하는 함수 (지정된 의존성이 변경된 경우)

```
[ dependencies ]
```
- 지정된 의존성으로 구성된 배열
- 지정한 의존성이 변경된 경우에만 함수 실행 (컴포넌트가 다시 렌더링될 때는 실행되지 않음)
- 데이터 가져오기는 한 번만 실행되어야 함
- 배열이 비어있다면 컴포넌트 함수가 처음으로 실행될 때 의존성이 변경된 것으로 간주 (의존성이 없었기 때문) -> 단 한 번만 실행. 이후로 의존성은 절대 변경되지 않기 때문

첫 번째 함수에 어떤 사이드 이펙트 코드라도 넣을 수 있음. 해당 코드는 지정한 의존성이 변경된 경우에만 실행. 컴포넌트가 다시 렌더링될 때는 실행되지 않음

```jsx
useEffect(() => {
  const isLoggedIn = localStorage.getItem("isLoggedIn");
  if (isLoggedIn === "1") {
    setIsLoggedIn(true);
  }
}, []); // 빈 의존성 배열 (단 한 번만 실행)
```

```jsx
useEffect(() => {}); // 배열이 없으면 렌더링마다 계속 재실행
```

- 의존성 배열이 useEffect에 아예 없으면 컴포넌트가 재평가될 때마다 실행
- 그러면 useEffect의 콜백 함수가 컴포넌트 함수 바로 안으로 이동한 것과 같음
- 의존성이 없기 때문에 해당 코드는 컴포넌트가 다시 렌더링될 때마다 다시 실행
- 그 코드에 state를 설정하는 setState 함수가 있었다면, 재 렌더링 주기 자체를 트리거 -> 무한루프 발생
- 모든 컴포넌트 렌더링 주기 이후에 실행되기 때문


```jsx
useEffect(() => {
  setFormIsValid(
    enteredEmail.includes("@") && enteredPassword.trim().length > 6
  );
}, [setFormIsValid, enteredEmail, enteredPassword]);
// setFormIsValid 함수는 리액트에 의해 절대 변경되지 않도록 보장되기 때문에 생략 가능
```

<br>

## 종속성으로 추가할 항목 및 추가하지 않을 항목

effect 함수에서 사용하는 모든 것을 종속성으로 추가해야 함 -> 거기에서 사용하는 모든 상태 변수와 함수를 포함

### 예외

- 상태 업데이트 기능을 추가할 필요 (ex)setFormIsValid) (해당 함수는 리액트가 절대 변경되지 않도록 보장하기 때문에 추가할 필요 없음)
- 내장 API 또는 함수를 추가할 필요 없음(ex)fetch(), localStorage()) (브라우저 API, 전역 기능은 리액트 구성 요소 렌더링 주기와 관련이 없으며 변경되지 않음)
- 변수, 함수를 추가할 필요 없음. 아마도 구성 요소 외부에서 정의했을 것

구성 요소(또는 일부 상위 구성 요소)가 다시 렌더링되어 이러한 것들이 변경될 수 있는 경우에 종속성으로 추가

### 예시

```jsx
import { useEffect, useState } from "react";

let myTimer;

const MyComponent = (props) => {
  const [timerIsActive, setTimerIsActive] = useState(false);

  const { timerDuration } = props;

  useEffect(() => {
    if (!timerIsActive) {
      setTimerIsActive(true);
      myTimer = setTimeout(() => {
        setTimerIsActive(false);
      }, timerDuration);
    }
  }, [timerIsActive, timerDuration]);
};
```

1. `timerIsActive` : 구성 요소가 변경될 때 변경될 수 있는 구성 요소 상태이기 때문에 종속성으로 추가 (예) 상태가 업데이트)
2. `timerDuration` : 해당 구성 요소의 prop 값이기 때문에 종속성으로 추가 -> 상위 구성 요소가 해당 값을 변경하면 변경될 수 있음 (이 MyComponent 구성 요소도 다시 렌더링되도록 함)
3. `setTimerIsActive` : 예외 조건이기 때문에 종속성으로 추가하지 않음. 상태 업데이트 기능을 추가할 수 있지만 React는 기능 자체가 절대 변경되지 않음을 보장하기 때문
4. `myTimer` : 구성 요소 내부 변수가 아니기 때문에 종속성으로 추가하지 않음. (즉, 어떤 상태나 prop 값이 아님) -> 구성 요소 외부에서 정의되고 이를 변경함(어디에서든). 구성 요소가 다시 평가되도록 하지 않음
5. `setTimeout` : 그것은 내장 API이기 때문에 종속성으로 추가하지 않음. (브라우저에 내장) - React 및 구성 요소와 독립적이며 변경되지 않음

<br>

## Clean-Up function

useEffect는 return할 때 클린업 할 수 있음 (함수 자체를 반환 -> 클린업 함수 반환)

클린업은 모든 새로운 사이드 이펙트 함수가 실행되기 전에 실행됨

```jsx
useEffect(() => {
  const identifier = setTimeout(() => {
    setFormIsValid(
      enteredEmail.includes("@") && enteredPassword.trim().length > 6
    );
  }, 500);

  return () => {
    console.log("CLEANUP");
    clearTimeout(identifier);
  };
}, [enteredEmail, enteredPassword]);
```

<br>

## Debouncing (그룹화)

사용자의 입력을 잠시 기다렸다가 유효성 검사를 수행하면 더 효율적 (입력할 때마다 state 변경하면 비효율적)

사용자가 타이핑을 일시 중지했을 때 수행 (Form 유효성 검사)

---
