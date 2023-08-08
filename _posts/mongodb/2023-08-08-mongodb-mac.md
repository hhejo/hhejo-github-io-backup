---
title: "macOS에서 MongoDB Community Edition을 설치하고 실행하기"
date: 2023-08-08
last_modified_at: 2023-08-08
excerpt: "MongoDB를 설치하고 실행하겠습니다. 그리고 MongoDB Atlas란 무엇일까요?"
categories:
  - MongoDB
tags:
  - [mongodb, mongosh, brew]
---

---

<br>

# MongoDB Community Edition 설치

현재 프로젝트의 DB로 MongoDB를 사용하기 위해 MongoDB Community Edition을 설치하겠습니다.

공식문서를 참고하면서 진행했습니다. 버전은 현재 최신 버전인 `v6.0`입니다.

Homebrew를 이용해 설치하겠습니다.

먼저, Xcode 명령줄 도구를 설치하는데, 저는 이미 설치되어 있어서 넘기겠습니다.

```bash
brew tap mongodb/brew
brew update
brew install mongodb-community@6.0
```

<br>
<br>

# 실행, 중단, 확인 명령어

MongoDB 서비스를 아래 명령어로 실행합니다.

```bash
brew services start mongodb-community@6.0
```

중단하려면 아래 명령어를 사용합니다.

```bash
brew services stop mongodb-community@6.0
```

서비스가 실행되고 있는지 확인하려면 아래 명령어를 사용합니다.

```bash
brew services list
```

실행되고 있지 않을 경우, 아래처럼 나타납니다.

```bash
Name              Status User File
mongodb-community none
```

정상적으로 실행되는 중이라면, 아래처럼 나타납니다.

```bash
❯ brew services start mongodb-community@6.0
==> Successfully started `mongodb-community` (label: homebrew.mxcl.mongodb-community)
❯ brew services list
Name              Status  User File
mongodb-community started hejo ~/Library/LaunchAgents/homebrew.mxcl.mongodb-community.plist
```

## 에러 발생

제가 예전에 다른 프로젝트 때문에 MongoDB를 설치한 적이 있어서인지 잘 모르겠지만, 한 에러가 있었습니다.

`brew services start mongodb-community@6.0`으로 실행하고, `mongosh`로 사용하려는데 에러가 발생했습니다.

분명히 서비스는 잘 시작되었다고 뜨는데, 서비스 리스트에서 확인해보면 상태가 에러로 나와있었습니다.

```bash
❯ brew services list
Name              Status  User File
mongodb-community error   hejo ~/Library/LaunchAgents/homebrew.mxcl.mongodb-community.plist
```

어떻게 해결해야 할지 잘 몰라서 찾아보다가, 아래 글에서 해결 방법을 찾았습니다.

> [HELP: Brew mongodb-community@5.0 error [MacOS]](https://www.mongodb.com/community/forums/t/help-brew-mongodb-community-5-0-error-macos/125648)

권한 이슈일 수 있어서 해당 파일을 지우고 실행하면 된다고 합니다.

```bash
> ls -l /tmp/mongodb-27017.sock
srwx------  1 hejo  wheel  0  8  8 16:39 /tmp/mongodb-27017.sock
> sudo rm -rf /tmp/mongodb-27017.sock
> brew services start mongodb-community@6.0
```

잘 해결 되었습니다.

<br>
<br>

# MongoDB 사용

MongoDB 서비스를 실행한 후, MongoDB를 사용하려면 아래 명령어를 입력합니다.

```bash
mongosh
```

이제부터 mongoDB shell로 조작할 수 있습니다.

```bash
Current Mongosh Log ID: ________________________
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+1.10.3
Using MongoDB:          6.0.6
Using Mongosh:          1.10.3

For mongosh info see: https://docs.mongodb.com/mongodb-shell/

------
   The server generated these startup warnings when booting
   2023-08-08T16:39:18.427+09:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
------

test> 
```

<br>
<br>

# MongoDB Atlas?

MongoDB 홈페이지에서 설정을 시작하려고 문서를 보면, MongoDB Atlas를 사용하라고 나와있는데, 이것이 뭔지 찾아보다가 알게 된 내용입니다.

MongoDB Atlas는 클라우드 데이터베이스와 데이터 서비스가 통합된 제품군으로, 데이터를 사용해 빠르고 손쉽게 구축할 수 있다고 합니다.

클라우드 상에서 CLI만이 아닌 GUI를 이용해 DB가 어떤 상태인지 잘 볼 수 있게 해주고, 데이터 조작도 손쉽게 할 수 있는 도구인 것 같습니다.

하지만 저는 mongoDB CLI에 좀 더 집중하고 싶어서 나중으로 미뤘습니다.

서비스 사진들을 보면 확실히 쓰기 좋아보이긴 합니다.

<br>
<br>

# 참고

> [Install MongoDB Community Edition on macOS](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-os-x/)

---
