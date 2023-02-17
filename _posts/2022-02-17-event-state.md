---
title:  "Event와 State"
date: 2022-02-17
last_modified_at: 2022-02-17
excerpt: "Event, State, useState, 사용자 입력, 여러 개의 state, state 업데이트, Lifting up"
categories:
  - React
tags:
  - [React, event, state]
---

---

<br>

# Event

```jsx
const clickHandler = () => {};
// ...
<button onClick={clickHandler}>Click Me</button>;
// ...
<ExpenseForm onSaveExpenseData={saveExpenseDataHandler} />
```

리액트는 JSX 코드를 평가할 때마다 컴포넌트 함수들을 호출하고, 컴포넌트 함수들은 모든 평가된 JSX 코드를 반환한다.

더 이상 함수가 남아있지 않을 때까지, 리액트는 계속해서 JSX에서 마주치는 컴포넌트 함수들을 호출함

리액트는 응용프로그램이 처음 렌더링되었을 때 그 모든 과정을 실행하고 끝난다.

<br>

# useState

리액트에게 어떤 것이 변경되었고 특정 컴포넌트가 재평가되어야 한다고 알려주려면 `state`를 사용

컴포넌트 함수에 변수를 갖고 있고 그것이 변경되었다 해도 리액트는 무시하고 다시 실행되지 않기 때문에 `useState()` hook을 사용해야 함

훅들은 리액트 컴포넌트 함수 안에서만 호출할 수 있다. 중첩된 함수나 컴포넌트 함수 밖에서는 불가능

```jsx
import React, { useState } from "react";
```

```jsx
const [현재상태값, 그것을업데이트하는함수] = useState(초깃값);
const [state, setState] = useState("");
```

`state`가 변하면, 업데이트 함수를 호출함

JSX 코드에서 그것을 출력하기 위해 상태값을 사용하고 싶을 때마다 첫 번째 요소를 사용(현재 상태 값)

리액트는 상태가 변할 때마다 **컴포넌트 함수를 다시 실행**하고 JSX 코드를 다시 평가함

`useState()`는 여러 개 사용할 수 있음

<br>

# 사용자 입력

사용자 입력은 `input` 태그에 `onChange` 속성에 함수를 연결하면 됨

양방향 바인딩 이용하려면 `value` 속성 넣기

핸들러 함수에서 `event`를 받을 수 있고, `event.target.value`로 사용

```jsx
const [inputValue, setInputValue] = useState("");

const changeHandler = (event) => console.log("value changed");

<input type="text" onChange={changeHandler} value={inputValue} />;
```

<br>

# 여러 개의 `state`를 하나의 `state`로 관리하기

객체를 이용

```jsx
// 여러 개의 state: enteredTitle, enteredAmount, enteredDate
const [enteredTitle, setEnteredTitle] = useState("");
const [enteredAmount, setEnteredAmount] = useState("");
const [enteredDate, setEnteredDate] = useState("");

// 하나의 state: userInput
const [userInput, setUserInput] = useState({
  enteredTitle: "",
  enteredAmount: "",
  enteredDate: "",
});
```

<br>

# 이전 `state`에 의존하는 `state` 업데이트

그냥 값으로 덮어 쓰는 것은 좋지 않은 방법이다.

- ```jsx
  setUserInput({ ...userInput, enteredTitle: event.target.value }); // 좋지 않음
  ```

대신 아래처럼 함수를 전달한다.

- ```jsx
  setUserInput((prevState) => {
    return { ...prevState, enteredTitle: event.target.value };
  }); // 옳은 방법
  ```

<br>

## `form` 기본 동작 막기

`event.preventDefault()`

<br>

## Lifting State Up (state 끌어올리기)

`props`로 부모 컴포넌트가 준 함수를 받은 후에 매개변수로 값을 넣어줌

```jsx
props.onSaveExpenseData(expenseData); // 부모에게 전달
```

```jsx
// NewExpense.js
import React from "react";

import ExpenseForm from "./ExpenseForm";
import "./NewExpense.css";

const NewExpense = (props) => {
  // 자식이 넘긴 데이터를 enteredExpenseData로 받음
  const saveExpenseDataHandler = (enteredExpenseData) => {
    props.onAddExpense(expenseData); // 부모 App.js의 함수를 이용해 생성 데이터 전달
  };

  return (
    <div className="new-expense">
      {/* 자식에게 데이터를 받기 위해 함수 전달 */}
      <ExpenseForm onSaveExpenseData={saveExpenseDataHandler} />
    </div>
  );
};

export default NewExpense;
```

<br>

`Presentational Component` v `Stateful Component`

`Stateless Component` v `Stateful Component`

`Dumb Component` v `Smart Component`


---
