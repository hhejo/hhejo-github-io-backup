---
title:  "Linter와 Formatter"
date: 2023-07-30
last_modified_at: 2023-07-30
excerpt: "Linter와 Formatter에 대해 알아보고, 각각의 대표적인 유틸리티인 ESLint와 Prettier를 사용해보겠습니다."
categories:
  - JavaScript
tags:
  - [javascript, nodejs, linter, eslint, formatter, prettier]
---

---

<br>

# Linter

린터는 코드를 분석하고 버그, 구문 오류, 문체 불일치 및 의심스러운 구조를 발견하고 개선하는 데 사용되는 도구입니다.

linter는 컴파일된(compiled) 언어에만 국한되지 않지만, 개발 시간에 오류를 감지하는 컴파일러가 없는 해석된(interpreted) 언어에 더 가치가 있습니다.

JavaScript용 린터에는 ESLint, JSLint, JSHint 등이 있습니다.

## Linter의 장점

- production 환경에서 적은 오류
- 규칙을 적용해 일관되고 팀이 읽기 쉽고 편리한 유지보수
- 코드 검토(review) 중 코드 스타일 및 미적 선택에 관한 논의, 논쟁 감소
- 코드 품질의 객관적 측정
- 보다 안전하고 성능이 뛰어난 코드
- 코드 품질에 대해 배울 수 있는 기회

## 타입 체크(Type Checks)

- 구문 오류(syntax errors) 확인
- 코드 표준 준수로 일관된 코딩 스타일 유지
- 잠재적인 문제 코드 감지
  - 너무 긴 함수
  - 너무 복잡한 코드
- 보안 검사
- 

## 정적 분석(Static Analysis)

정적 분석을 사전 실행 디버그로 볼 수 있습니다.

정적 분석은 오류, 규칙 또는 규칙 준수 부족 또는 기타 잠재적인 문제를 찾는 소스 코드를 분석하는 도구를 사용하는 것으로 구성됩니다.

## 린터의 예

### 정적 분석을 위한 린터

StandardJS

### 보안을 위한 린터

LGTM

### 코딩 규칙, 코딩 서식 지정을 위한 린터

Prettier

<br>
<br>

# ESLint

ESLint는 자바스크립트에서 가장 인기있는 오픈 소스 린팅(linting) 유틸리티입니다.

ESLint를 사용해 특정 독립 실행형 규칙 세트를 사용해 코딩 표준을 적용할 수 있어 특정 스타일 지침을 준수하지 않는 문제가 있는 패턴이나 코드를 찾을 수 있습니다.

자바스크립트는 동적이고 느슨한 유형의 언어이기 때문에 개발자 오류가 발생하기 쉽습니다. ESLint를 이용해 코딩 표준에 대한 지침을 제시하고 이러한 오류를 최소화할 수 있습니다. ESLint와 같은 린팅 도구를 사용하면 개발자가 자바스크립트 코드를 실행하지 않고도 문제를 발견할 수 있습니다.

다음 명령어를 통해 개발 의존성으로 eslint를 설치합니다.

```bash
npm install --save-dev eslint
```

다음 명령어로 초기 설정합니다.

```bash
npx eslint --init
```

본인의 프로젝트에 맞게 선택지를 고르면서 설정합니다.

```bash
You can also run this command directly using 'npm init @eslint/config'.
Need to install the following packages:
  @eslint/create-config@0.4.6
Ok to proceed? (y) 
✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · none
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ What format do you want your config file to be in? · JavaScript
A config file was generated, but the config file itself may not follow your linting rules.
Successfully created .eslintrc.cjs file in /Users/hejo/Documents/my-pjt
```

완료하면, 프로젝트 루트 폴더 내 `.eslintrc.cjs` 파일이 생성됩니다.

```javascript
module.exports = {
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": "eslint:recommended",
    "overrides": [
        {
            "env": {
                "node": true
            },
            "files": [
                ".eslintrc.{js,cjs}"
            ],
            "parserOptions": {
                "sourceType": "script"
            }
        }
    ],
    "parserOptions": {
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "rules": {
    }
}
```

이전에 작성했던 파일을 검사해보겠습니다.

```javascript
// src/index.js
import 'dotenv/config';

const { PASSWORD } = process.env;
console.log(`PASSWORD: ${PASSWORD}`);
```

아래 명령어로 해당 파일을 검사합니다.

```bash
npx eslint src/index.js
```

그런데 다음과 같은 오류가 발생합니다.

```bash
/Users/hejo/Documents/my-pjt/src/index.js
  7:22  error  'process' is not defined  no-undef

✖ 1 problem (1 error, 0 warnings)
```

`process`가 정의되지 않았다고 합니다.

그 이유는, `.eslintrc.cjs`의 파일에서 확인할 수 있습니다. 일부분을 표시하겠습니다.

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
};
```

`browser: true`를 삭제하고 `node: true`를 추가합니다. eslint를 설정할 때 browser 환경이냐, node 환경이냐 선택할 때 실수로 browser로 선택했기 때문에 저렇게 되었습니다. process 객체는 node 환경에만 존재하기 때문에 이런 오류가 발생했습니다.

저장하고 다시 실행하면 오류가 발생하지 않습니다.

```bash
npx eslint src/index.js
```

`package.json` 스크립트로 지정해 편리하게 사용할 수도 있습니다.

```json
  "scripts": {
    "lint": "eslint src/index.js",
  },
```

VSCode를 사용한다면 extensions로 ESLint를 다운받아 더욱 편리하게 개발중에 사용할 수 있습니다.

더 많은 설정을 할 수 있지만 여기까지 해보겠습니다.

<br>
<br>

# Formatter

Formatter는 작성한 코드를 설정된 규칙에 의해 코드 스타일을 보기 좋게 정렬하거나 수정해줍니다.

## Linter와 Formatter의 차이

Linter로 코드를 정적으로 분석하여 코드의 잠재적인 오류와 버그, 의심스러운 구조를 발견해 수정하거나 개선합니다.

Formatter로 코드 스타일(간격, 줄바꿈, 주석 등)을 수정하거나 통일해 서식을 일관되게 유지합니다.

<br>
<br>

# Prettier

아래 명령어로 Prettier를 설치합니다.

```bash
npm install --save-dev --save-exact prettier
```

빈 config 파일인 `.prettierrc.json`을 생성해 현재 사용하는 에디터와 다른 도구들에 Prettier를 사용함을 알립니다.

```
echo {}> .prettierrc.json
```

`.prettierrc.json` 파일을 아래와 같이 변경해줍니다. 물론 설정은 자유롭게 할 수 있습니다.

```json
{
  "tabWidth": 2,
  "useTabs": false,
  "endOfLine": "lf",
  "printWidth": 80,
  "semi": true,
  "singleQuote": true
}
```

`.prettierignore` 파일을 생성해 포매팅하지 않을 파일을 알려줍니다.

```
# Ignore artifacts:
build
coverage
```

이제 아래 명령을 사용해 포매팅합니다.

```bash
npx prettier . --write
```

포매팅 규칙으로 덮어쓰지 않고 체크만 하고 싶다면 아래 명령을 사용합니다.

```bash
npx prettier . --check
```

## ESLint와 Prettier 같이 사용하기

`prettier-eslint`: prettier를 실행한 후 eslint를 실행

`eslint-plugin-prettier`: prettier를 eslint의 규칙으로 동작시킴 (포매팅 문제도 오류로 출력되기 때문에 오류 메시지가 지나치게 많아지며 느려짐)

`eslint-config-prettier`: 불필요하거나 Prettier와 충돌할 수 있는 모든 규칙 해제 (권장)

```bash
npm install --save-dev eslint-config-prettier
```

`.eslintrc.cjs`의 `extends` 속성에 `'prettier'`를 맨 마지막으로 추가합니다.

```javascript
{
  extends: ['eslint:recommended', 'prettier'],
}
```

<br>
<br>

# 참고

> [What Is a Linter? Here’s a Definition and Quick-Start Guide](https://www.testim.io/blog/what-is-a-linter-heres-a-definition-and-quick-start-guide/)

> [eslint.org](https://eslint.org/)

> [ESLint: What, Why, When, How](https://dev.to/shivambmgupta/eslint-what-why-when-how-5f1d)

> [[JS] ESLint 적용하기](https://ingg.dev/eslint/)

> [내 프로젝트에 ESLint & Prettier 도입하기 (VSCode)](https://seogeurim.tistory.com/15)

> [What's the difference between code linters and formatters?](https://nono.ma/linter-vs-formatter)

> [Code Formatter와 Linter의 차이](https://empty-cloud.tistory.com/86)

> [Why Prettier?](https://prettier.io/docs/en/why-prettier.html)

> [Prettier vs. Linters](https://prettier.io/docs/en/comparison.html)

> [[VSCode] Prettier - 코드를 깔끔하게 정리하기](https://zzemal.tistory.com/86)

> [eslint-config-prettier - GitHub](https://github.com/prettier/eslint-config-prettier)

> [prettier와 eslint를 구분해서 사용하자](https://yrnana.dev/post/2021-03-21-prettier-eslint/)

---
