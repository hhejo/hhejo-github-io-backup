---
title:  "Node.js의 환경변수 관리"
date: 2023-07-22
last_modified_at: 2023-07-22
excerpt: "Node.js에서 환경변수를 관리하기 위한 .env와 process.env에 대해 알아보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, process, env]
---

---

<br>

# 환경변수 (Environment Variable)

개발, 테스트, 운영 등의 여러 환경에 애플리케이션을 배포할 때, 각 확경에 따라 다르게 설정되어야 하는 값들을 환경변수에 설정합니다. 또, 개인 API 키, 데이터베이스 비밀번호와 같은 외부로부터 노출되지 않아야 하는 데이터를 환경 변수로 설정합니다.

`.env` 파일을 사용해 프로젝트의 소스 코드에서 접근할 수 있도록 Node.js의 환경변수를 설정할 수 있습니다.

환경변수 파일을 생성합니다.

```bash
touch .env
```





<br>
<br>

# 참고

> [Node.js에서 환경 변수 다루기 (process.env)](https://www.daleseo.com/js-node-process-env/)

> [The minimal Node.js with Babel Setup](https://www.robinwieruch.de/minimal-node-js-babel-setup/)

---
