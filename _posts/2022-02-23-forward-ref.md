---
title:  "Forward Ref란?"
date: 2022-02-23
last_modified_at: 2022-02-24
excerpt: "Forward Ref의 개념에 대해"
categories:
  - React
tags:
  - [React, forward ref]
---

---

<br>

## Input 재사용

`Input` 폴더 만들고 `Input.js` 만들기

```jsx
// Input.js
import React from "react";

import classes from "./Input.module.css";

const Input = (props) => {
  return (
    <div
      className={`${classes.control} ${
        props.isValid === false ? classes.invalid : ""
      }`}
    >
      <label htmlFor={props.id}>{props.label}</label>
      <input
        type={props.type}
        id={props.id}
        value={props.value}
        onChange={props.onChange}
        onBlur={props.onBlur}
      />
    </div>
  );
};

export default Input;
```

```jsx
// Login.js
// ...
const Login = () => {
  // ...
  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <Input
          id="email"
          label="E-Mail"
          type="email"
          isValid={emailIsValid}
          value={emailState.value}
          onChange={emailChangeHandler}
          onBlur={validateEmailHandler}
        />
        <Input
          id="password"
          label="Password"
          type="password"
          isValid={passwordIsValid}
          value={passwordState.value}
          onChange={passwordChangeHandler}
          onBlur={validatePasswordHandler}
        />
      </form>
    </Card>
  );
};
```

# Forward Refs

ref를 사용자 커스텀 input에 넘길 수 있음

예시) 제출 버튼을 클릭할 때, 양식이 비어있다면 자동 포커스하는 기능

이메일 양식, 비밀번호 양식이 순서대로 있을 때, 제출했을 때 맞지 않는 제일 처음의 양식에 포커스 주기

일반 input이라면 ref로 하면 되지만, 우리가 생성한 Input 컴포넌트에는?

```jsx
// Login.js
// ...
const Login = () => {
  const emailInputRef = useRef();
  const passwordInputRef = useRef();
  // ...
  const submitHandler = (event) => {
    event.preventDefault();
    if (formIsValid) {
      authCtx.onLogin(emailState.value, passwordState.value);
    } else if (!emailIsValid) {
      emailInputRef.current.activate();
    } else {
      passwordInputRef.current.activate();
    }
  };
  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <Input
          ref={emailInputRef}
          id="email"
          label="E-Mail"
          type="email"
          isValid={emailIsValid}
          value={emailState.value}
          onChange={emailChangeHandler}
          onBlur={validateEmailHandler}
        />
        <Input
          ref={passwordInputRef}
          id="password"
          label="Password"
          type="password"
          isValid={passwordIsValid}
          value={passwordState.value}
          onChange={passwordChangeHandler}
          onBlur={validatePasswordHandler}
        />
      </form>
    </Card>
  );
};
```

`props.ref`는 없음. props 객체에서 ref를 받아들이지 않음. 작동시키려면?

위 코드에서 Input 컴포넌트에서 activate나 focus를 호출할 수 있다면, Input 컴포넌트를 내장형처럼 사용할 수 있게 됨

`useImperativeHandle(forwardRef로 감싸진 ref, 객체를 반환하는 함수)` 훅

객체를 반환할 때 객체는 내부 함수 또는 내부 변수 또는 무엇이든 그 이름을 통해 외부에서 접근할 수 있어야 하는 것을 가리킴

```jsx
React.forwardRef()
```

```jsx
const Input = forwardRef((props, ref) => {
  const inputRef = useRef();
  const activate = () => inputRef.current.focus();

  useImperativeHandle(ref, () => {
    return {
      focus: activate,
    };
  });
});
```

이제 Login.js에서 `emailInputRef.current.focus();` 호출 가능. focus란 이름으로 activate가 호출될 것

---
