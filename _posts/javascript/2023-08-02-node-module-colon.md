---
title: 모듈의 node:는 무엇일까요?
date: 2023-08-02 00:00:00 +0900
last_modified_at: 2023-08-02 00:00:00 +0900
categories: [JavaScript]
tags: [javascript, nodejs, fs]
---

모듈 중에 node:가 이름 앞에 붙는 경우가 있는데 무엇인지 알아보겠습니다.

## `fs`, `node:fs`

코딩을 하던 중에 궁금해졌습니다.

```javascript
import 'fs' from 'fs';
```

```javascript
import 'fs' from 'node:fs';
```

위와 아래 코드의 차이점은 `node:`가 붙어있느냐 없느냐 입니다.

그렇다면 둘은 다를까요?

찾아 본 바에 의하면..

Node.js의 핵심 모듈은 Node.js 소스 내에서 정의되며, 폴더(`lib/`)에 존재합니다.

이 핵심 모듈들은 접두사 `node:`를 이용해 식별할 수 있으며, 이것은 `require`의 캐시를 우회합니다.

예를 들어, `require.cache` 엔트리에 해당 이름이 있더라도, `require('node:http')`는 항상 내장 HTTP 모듈을 반환합니다.

## 참고

> [What are the `node:fs`, `node:path` etc. modules?](https://stackoverflow.com/questions/67554506/what-are-the-nodefs-nodepath-etc-modules)

> [Core modules](https://nodejs.org/api/modules.html#core-modules)
