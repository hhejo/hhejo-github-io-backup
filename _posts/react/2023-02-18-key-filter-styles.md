---
title:  "key와 조건 필터링, Styled Components와 CSS Modules"
date: 2023-02-18
last_modified_at: 2023-02-18
excerpt: "key, filter, Styled Components, CSS Modules"
categories:
  - React
tags:
  - [React, Styled Components, CSS Modules]
---

---

<br>

# `key` props

- 아이템을 추가하면 맨 위에 추가되는데, HTML에서는 맨 아래에 추가됐다고 표시됨 (근데 다시 업데이트로 위로 이동)
- React는 모든 목록을 체크해서 업데이트 -> 버그 발생할 수 있고, 성능 저하됨
- `key` 속성을 사용해서 해결 (map 메서드의 index를 줄 수도 있지만, 권장하지 않음)

<br>

# filter (조건 필터링)

`&&` 연산자를 이용해, 조건을 만족할 때 렌더링할 JSX content를 `&&` 뒤에 배치

```jsx
isEditing && <Component />
!isEditing && <AnotherComponent />
```

<br>

# Conditional & Dynamic Styles

style을 태그에 주는 방법은 내용이 길어지기 때문에 좋지 않음

동적으로 클래스를 지정할 수 있음

- ```jsx
  className={form-control ${!isValid ? "invalid" : ""}}
  ```

<br>

# Styled Components

```sh
npm install --save styled-components
```

```jsx
// Button.js
import styled from "styled-components";

const Button = styled.button`
  width: 100%;
  font: inherit;
  padding: 0.5rem 1.5rem;
  border: 1px solid #8b005d;
  color: white;
  background: #8b005d;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.26);
  cursor: pointer;

  @media (min-width: 768px) {
    width: auto;
  }

  &:focus {
    outline: none;
  }

  &:hover,
  &:active {
    background: #ac0e77;
    border-color: #ac0e77;
    box-shadow: 0 0 8px rgba(0, 0, 0, 0.26);
  }
`;

export default Button;
```

tagged template literal syntax 사용하고, props 전달 가능, media query 적용 가능

`&`로 대체해서 사용

<br>

# CSS Modules

해당 클래스의 이름을 고유하게 바꿔 파일 어딘가에서 겹치지 않게 할 수 있음

`css` 파일 이름에 `module`을 추가한다.

- ```jsx
  import styles from "./???.module.css"
  ```

```jsx
// CourseInput.js
import React, { useState } from "react";

import Button from "../../UI/Button/Button";

import styles from "./CourseInput.module.css"; // styles는 객체

const CourseInput = (props) => {

  // ...

  return (
    <form onSubmit={formSubmitHandler}>
      <div
        className={`${styles["form-control"]} ${!isValid && styles.invalid}`}
      >
      </div>
    </form>
  );
};

export default CourseInput;
```

<br>

# 그 외 팁

## 간단한 숫자 변환

문자열 숫자 앞에 `+`를 붙이면 자동으로 변환

<br>

## 문자열 변환

숫자 옆에 문자열 더하기로 붙이면 자동으로 문자열로 변환

<br>

## style 속성

`style={{}}` 중괄호 두 개 겹치게 넣어야 함 (객체를 넣어야 하기 때문)

속성 이름 중간에 `-`가 들어가면, 카멜 케이스로 변경

---
