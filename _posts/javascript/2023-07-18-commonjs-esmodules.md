---
title:  "CommonJS, ES Modules"
date: 2023-07-18
last_modified_at: 2023-07-18
excerpt: "CommonJS와 ES Modules에 대해 알아보자"
categories:
  - JavaScript
tags:
  - [javascript, nodejs, commonjs, esmodules, amd, requrejs]
---

---

<br>

# Module

개발하는 애플리케이션의 크기가 커지면서, 여러 개의 파일(코드)로 분리하게 되는데, 이 분리된 파일 각각을 모듈이라고 부릅니다. 하나의 클래스 혹은 함수로 구성되거나, 복수의 클래스, 함수로 구성된 라이브러리로 만들어질 수도 있습니다.

자바스크립트는 탄생 초기에 짧은 코드, 작고 단순한 기능을 위해 사용되었기 때문에 모듈 관련 표준 문법이 존재하지 않았습니다. 하지만 스크립트의 크기가 점차 커지고 기능도 복잡해지며, 사용하는 곳이 늘어나자 모듈 관련 시스템을 만들 필요성이 생겼습니다.

## AMD (Asynchronous Module Definition)

자바스크립트의 사양으로, 코드 모듈과 해당 종속성(dependency)을 정의하는 API를 정의하고 원하는 경우 비동기식으로 로드합니다.

AMD는 더 작은 자바스크립트 파일을 로드한 후, 필요할 때만 로드할 수 있어 웹사이트 성능을 개선할 수 있습니다.

개발자는 AMD 구현을 통해 모듈이 실행되기 전에 로드해야 하는 종속성을 정의할 수 있으므로 모듈은 아직 사용할 수 없는 외부 코드를 사용하려고 시도하지 않기 때문에 페이지 오류가 적습니다.

`exports`, `require()`, `define()` 구문을 이용합니다.

RequireJS



## UMD

## CommonJS

웹 브라우저 외부(웹 서버, 데스크탑 애플리케이션)에서 자바스크립트용 모듈 생태계를 표준화하는 프로젝트입니다.

CommonJS의 모듈 작동 방식은 Node.js를 사용하는 서버 측 자바스크립트에 사용됩니다.

브라우저 측 자바스크립트에도 사용되지만, 브라우저가 CommonJS를 지원하지 않기 때문에 해당 코드를 트랜스파일러로 변환해야 합니다.

ESM 모듈 사양과 더불어 널리 쓰이는 모듈 시스템입니다.

`require()`와 `module.exports` 구문을 이용합니다.

## ECMAScript Modules



<br>
<br>

# 참고

> [모듈 소개](https://ko.javascript.info/modules-intro)

> [CommonJS](https://en.wikipedia.org/wiki/CommonJS)

> [자바스크립트 모듈 시스템: ESM, CommonJS, AMD, UMD](https://www.youdad.kr/js-module-system/)

> [RequireJS - AMD의 이해와 개발](https://d2.naver.com/helloworld/591319)

> [JavaScript 표준을 위한 움직임: CommonJS와 AMD](https://d2.naver.com/helloworld/12864)

---
