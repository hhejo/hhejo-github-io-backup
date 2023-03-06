---
title:  "useReducer란?"
date: 2023-02-21
last_modified_at: 2023-02-22
excerpt: "React의 useReducer에 대해 알아봅시다. 그리고 useState와의 차이점은?"
categories:
  - React
tags:
  - [React, useReducer, useState]
---

---

<br>

# `useReducer` Hook

예를 들어, 한 컴포넌트 함수 내에서 두 개 이상의 state를 보는데 최신 스냅샷이 아닌 경우

```jsx
const emailChangeHandler = (event) => {
  setEnteredEmail(event.target.value);
  setFormIsValid(
    event.target.value.includes("@") && enteredPassword.trim().length > 6
  ); // 이메일을 검사하는데 가장 최근에 입력한 암호가 포함되지 않을 수 있음 (리액트가 state 업데이트를 스케줄링하는 방식 때문)
};
```

최근 스냅샷이 아닌 state에 의존할 수 있기 때문에 `setState` 함수에서는 콜백 함수로 관리해야 함 (`prevState => {}` 이 방법)

그러나 위 예시에서는 두 개의 다른 state에 의존하고 있기 때문에 위 방법은 사용할 수 없기 때문에 `useReducer`를 사용하는 것이 좋다.

복잡한 state를 관리할 때는 `useState` 대신 `useReducer`를 사용한다.

다른 state를 기반으로 하는 state를 업데이트하면 하나의 state로 병함하는 것도 좋음 (useReducer 없이)

<br>

```jsx
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn);
```

- `state` : 최신 state 스냅샷
- `dispatchFn` : state를 업데이트하는 함수. 새로운 state 값을 설정하는 대신 action을 dispatch
- `reducerFn` : dispatch된 action을 가져 오는 함수. 최신 state 스냅샷을 자동으로 가져오고 새로운 업데이트된 state 반환 `(prevState, action) => newState`
- dispatch 함수에는 객체가 들어감. type을 키로 지정할 수 있고, 페이로드 또한 넣을 수 있음

<br>

## 예시

```jsx
// Login.js
// ...
const emailReducer = (state, action) => {
  if (action.type === "USER_INPUT") {
    return { value: action.val, isValid: action.val.includes("@") };
  }
  if (action.type === "INPUT_BLUR") {
    return { value: state.value, isValid: state.value.includes("@") };
  }
  return { value: "", isValid: false };
};

const passwordReducer = (state, action) => {
  if (action.type === "USER_INPUT") {
    return { value: action.val, isValid: action.val.trim().length > 6 };
  }
  if (action.type === "INPUT_BLUR") {
    return { value: state.value, isValid: state.value.trim().length > 6 };
  }
  return { value: "", isValid: false };
};

const Login = (props) => {
  const [formIsValid, setFormIsValid] = useState(false);
  const [emailState, dispatchEmail] = useReducer(emailReducer, {
    value: "",
    isValid: null,
  });
  const [passwordState, dispatchPassword] = useReducer(passwordReducer, {
    value: "",
    isValid: null,
  });

  const { isValid: emailIsValid } = emailState;
  const { isValid: passwordIsValid } = passwordState;

  useEffect(() => {
    const identifier = setTimeout(() => {
      setFormIsValid(emailIsValid && passwordIsValid);
    }, 500);
    return () => clearTimeout(identifier);
  }, [emailIsValid, passwordIsValid]); // 해당 state들에서 isValid만 빼오면, emailState나 passwordState가 변경될 때마다 useEffect가 실행되지 않음

  const emailChangeHandler = (event) => {
    dispatchEmail({ type: "USER_INPUT", val: event.target.value });
    // setFormIsValid(event.target.value.includes("@") && passwordState.isValid);
  };

  const passwordChangeHandler = (event) => {
    dispatchPassword({ type: 'USER_INPUT', val: event.target.value });
    // setFormIsValid(emailState.isValid && event.target.value.trim().length > 6);
  };

  const validateEmailHandler = () => dispatchEmail({ type: "INPUT_BLUR" });

  const validatePasswordHandler = () => dispatchPassword({ type: "INPUT_BLUR" });

  const submitHandler = (event) => {
    event.preventDefault();
    props.onLogin(emailState.value, passwordState.value);
  };

  return (
    <Card className={classes.login}>
      <form onSubmit={submitHandler}>
        <div
          className={`${classes.control} ${
            emailState.isValid === false ? classes.invalid : ""
          }`}
        >
          <label htmlFor="email">E-Mail</label>
          <input
            type="email"
            id="email"
            value={emailState.value}
            onChange={emailChangeHandler}
            onBlur={validateEmailHandler}
          />
        </div>
        <div
          className={`${classes.control} ${
            passwordState.isValid === false ? classes.invalid : ""
          }`}
        >
          <label htmlFor="password">Password</label>
          <input
            type="password"
            id="password"
            value={passwordState.value}
            onChange={passwordChangeHandler}
            onBlur={validatePasswordHandler}
          />
        </div>
        <div className={classes.actions}>
          <Button type="submit" className={classes.btn} disabled={!formIsValid}>
            Login
          </Button>
        </div>
      </form>
    </Card>
  );
};
```

useEffect에는 전체 개체 대신에 특정 속성만을 종속성으로 전달 가능하고, 그것이 좋다. 그렇지 않으면 전체 개체의 모든 속성이 변경될 때마다 재실행된다.

- ```jsx
  [someObject.someProperty];
  ```

## `useState` v `useReducer`

- `useState`는 주요 state 관리 도구
- 개별 state및 data를 다루기에 적합 (간단한 state)
- state 업데이트가 쉽고 몇 종류 안 되는 경우, state가 잘 변하지 않는 경우
- `useReducer`는 state로서의 객체가 있는 경우, 복잡한 state가 있는 경우 고려
- 더 강력하기 때문에 좋음
- state가 연관되어 있을 때, 복잡한 state 업데이트

---
