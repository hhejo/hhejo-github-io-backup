---
title:  "git push 시 GitHub Authentication Error 해결 방법"
date: 2023-03-01
last_modified_at: 2023-03-01
excerpt: "git push를 시도했을 때 fatal: Authentication failed for ... 와 같은 오류 발생시 처리하는 방법을 알아보겠습니다."
categories:
  - GitHub
tags:
  - [GitHub, git, authentication, token, error]
---

---

<br>

# git push 에러 발생

평소과 같이 git `add`, `commit` 후 `push`를 진행해서 잔디를 심으려고 했습니다.

그런데 다음과 같은 에러가 발생했습니다.. 

```sh
fatal: Authentication failed for 'https://github.com/.../.git/'
```

알고보니 저의 깃허브 인증 토큰이 오늘 만료되어 발생한 일이었습니다.

그래서 토큰을 재발급해서 해결했습니다.

1. 우측 상단 본인의 프로필 이미지를 클릭하고 Settings 클릭
2. 좌측 메뉴 맨 아래 Developer Settings 클릭
3. 좌측 메뉴 맨 아래 Personal access tokens 클릭
4. 클릭하면 나오는 Tokens (classic) 클릭
5. 상단 우측 Generate new token 클릭하고 Generate new token (classic) 클릭
6. Note에는 설명을 자유롭게 작성하고 Expiration도 자유롭게 (저는 90일 선택)
7. 아래 Select scopes에서 repo 체크
8. 맨 아래 초록색 버튼 Generate token 클릭
9. 중간에 나오는 토큰(랜덤한 문자열)을 복사해서 어디에 적어두기 (다시 볼 수 없음)

토큰은 아래처럼 생겼습니다.

```
ghp_KX97nFtPA...
```

다시 터미널에서 git push를 하면 (git add, commit 다 된 상태)

```sh
git push origin master
Username for 'https://github.com': 
Password for 'https://...@gmail.com@github.com': 
```

`Username`에는 깃허브 이메일을 작성하고, `Password`에는 받은 토큰을 쓰면 됩니다.

이상입니다.

---
