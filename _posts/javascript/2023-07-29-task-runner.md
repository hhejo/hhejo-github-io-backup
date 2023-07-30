---
title:  "Task Runner란?"
date: 2023-07-29
last_modified_at: 2023-07-30
excerpt: "태스크 러너(작업 실행기)에 대해 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, task-runner]
---

---

<br>

# Task Runner

태스크 러너(작업 실행기)는 자동으로 명령을 실행하고 뒤에서 프로세스를 수행해, 많은 시간을 낭비하게 될 일상적이고 반복적인 작업을 수행하여 워크 플로를 자동화하는데 도움을 줍니다.

## 예시

- 개발 서버 가동(회전)
- 코드 컴파일 (SASS/SCSS -> CSS 등) 자동화
- 정적 코드 분석 (ESLint)
- 로컬 포트 환경에서 파일 제공
- 로컬 서버 hot reloading
- 번들링 등
- ..와 같은 반복적이고 지루한 특정 개발 작업들을 단순화하고 자동화

## scripts

`package.json`의 `scripts` 속성은 여러 내장 스크립트와 미리 설정된 수명 주기 이벤트 및 임의의 스크립트를 지원합니다.

<br>
<br>

# 참고

> [태스크 러너, 모듈 번들러, 모듈, 번들링, 웹팩 개념 한번에 정리!](https://im-designloper.tistory.com/78)

> [scripts](https://docs.npmjs.com/cli/v8/using-npm/scripts)

---
