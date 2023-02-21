---
title:  "useReducer란?"
date: 2022-02-21
last_modified_at: 2022-02-21
excerpt: "React의 useReducer에 대해 알아봅시다. 그리고 useState와의 차이점은?"
categories:
  - React
tags:
  - [React, useReducer, useState]
---

---

<br>

# `useReducer` Hook

한 함수 내에서 두 개 이상의 state를 보는데 최신 스냅샷이 아닌 경우

```jsx
const emailChangeHandler = (event) => {
  setEnteredEmail(event.target.value);
  setFormIsValid(
    event.target.value.includes("@") && enteredPassword.trim().length > 6
  ); // 이메일을 검사하는데 가장 최근에 입력한 암호가 포함되지 않을 수 있음 (리액트가 state 업데이트를 스케줄링하는 방식 때문)
};
```

그래서 setState 함수에서는 콜백 함수로 관리해야 함 (prevState => {} 이 방법)

최근 스냅샷이 아닌 state에 의존할 수 있기 때문

그러나 두 개의 다른 state에 의존하고 있기 때문에 사용할 수 없는 방법

그렇다면? -> `useReducer` 사용

복잡한 State를 관리할 때 `useState` 대신 사용

다른 state를 기반으로 하는 state를 업데이트하면 하나의 state로 병함하는 것도 좋음 (useReducer 없이)

```jsx
const [state, dispatchFn] = useReducer(reducerFn, initialState, initFn);
```

- `state` : 최신 state 스냅샷
- `dispatchFn` : state를 업데이트하는 함수. 새로운 state 값을 설정하는 대신 action을 dispatch
- `reducerFn` : dispatch된 action을 가져 오는 함수. 최신 state 스냅샷을 자동으로 가져오고 새로운 업데이트된 state 반환 `(prevState, action) => newState`
- dispatch 함수에는 객체가 들어감. type을 키로 지정할 수 있고, 페이로드 또한 넣을 수 있음

## example

```jsx
// Login.js
// password 부분 제외 (전체 코드 참고)
const emailReducer = (state, action) => {
  if (action.type === "USER_INPUT") {
    return { value: action.val, isValid: action.val.includes("@") };
  }
  if (action.type === "INPUT_BLUR") {
    return { value: state.value, isValid: state.value.includes("@") };
  }
  return { value: "", isValid: false };
};

const Login = (props) => {
  // const [enteredEmail, setEnteredEmail] = useState("");
  // const [emailIsValid, setEmailIsValid] = useState();
  const [formIsValid, setFormIsValid] = useState(false);

  const [emailState, dispatchEmail] = useReducer(emailReducer, {
    value: "",
    isValid: null,
  });

  useEffect(() => {
    console.log("EFFECT RUNNING");

    return () => {
      console.log("EFFECT CLEANUP");
    };
  }, []);

  // 구조분해할당 with alias
  // 문자만 변했는데도 검사하는 것을 막기
  const { isValid: emailIsValid } = emailState;

  useEffect(() => {
    const identifier = setTimeout(() => {
      console.log("Checking form validity!");
      setFormIsValid(emailIsValid && passwordIsValid); // 여기도 emailState -> emailIsValid, passwordState -> passwordIsValid로 변경
    }, 500);

    return () => {
      console.log("CLEANUP");
      clearTimeout(identifier);
    };
  }, [emailIsValid, passwordIsValid]); // emailState 또는 passwordState가 변경될 때마다 실행 -> 막으려면?
  // emailState에서 isValid만 빼서 쓰면 됨. 어차피 그 변수만 사용하기 때문에 (password도 똑같이)
  // emailState -> emailIsValid, passwordState -> passwordIsValid로 변경

  const emailChangeHandler = (event) => {
    // setEnteredEmail(event.target.value);
    dispatchEmail({ type: "USER_INPUT", val: event.target.value });

    // setFormIsValid(event.target.value.includes("@") && passwordState.isValid);

    // 원래 setFormIsValid() 함수에서 위 처럼 사용하면 업데이트되지 않은 이전 state를 사용할 수 있음
    // 리액트가 보장하는 최신 state를 사용해야 안전하기 때문에, 위 useEffect() 처럼 사용하면 됨
    // 해당 의존성 배열에 있는 값으로 리액트가 잡아내 state를 최신화하기 때문
    // 따라서 useEffect() 안에서 최신 state 값을 보장하고, 해당 값을 이용해 setFormIsValid() 함수를 사용
    // 그러니까 여기서 setState같은 함수 사용은 ㄴㄴ
  };

  const validateEmailHandler = () => {
    // setEmailIsValid(emailState.isValid);
    dispatchEmail({ type: "INPUT_BLUR" });
  };

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

useEffect에는 전체 개체 대신 특정 속성을 종속성으로 전달 가능

```jsx
[someObject.someProperty]; // 가능
```

`useEffect`를 사용할 때는 전체 개체 대신 특정 속성을 종속성으로 전달하는 것이 좋음. 그렇지 않으면 전체 개체의 모든 속성이 변경될 때마다 재실행 됨

## `useState` v `useReducer`

- `useState`는 주요 state 관리 도구
- 개별 state및 data를 다루기에 적합 (간단한 state)
- state 업데이트가 쉽고 몇 종류 안 되는 경우, state가 잘 변하지 않는 경우
- `useReducer`는 state로서의 객체가 있는 경우, 복잡한 state가 있는 경우 고려
- 더 강력하기 때문에 좋음
- state가 연관되어 있을 때, 복잡한 state 업데이트

---
