---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 03"
date: 2023-08-05
last_modified_at: 2023-08-05
excerpt: "게시글 임시 id, 게시글 작성 날짜와 시간, 게시글 조회, 수정, missing in props validation"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, date]
---

---

<br>

# Server

## id

임시 id 값을 주기 위해 `new Date().getTime()`을 사용했습니다.

```javascript
const id = new Date().getTime();
```

## 게시글 작성 날짜, 시간

`new Date().toISOString()`을 사용해 작성 날짜와 시간을 가져왔습니다.

```javascript
const tempDateArr = new Date().toISOString().split('T');
const createdDate = tempDateArr[0];
const createdTime = tempDateArr[1].split('.')[0];
```

## 게시글 조회

목록에서 게시글을 클릭하거나, 작성하거나 수정하고 게시글 상세 화면으로 넘어갈 때 임시 DB 배열에서 `find()` 메서드를 이용해 찾았습니다.

```javascript
const matchingData = tempDB.find((el) => el.id === +boardId);
```

## 게시글 수정

수정할 게시글을 임시 DB 배열에서 찾기 위해 `findIndex()` 메서드를 사용했습니다.

```javascript
const idx = tempDB.findIndex((el) => el.id === +boardId);
```

<br>
<br>

# Client

## missing in props validation

클라이언트 서버 실행은 잘 되는데, 코드 상에서 위 에러(경고?)가 발생해서 해결 방법을 찾아봤습니다.

`.eslintrc.cjs` 파일의 `rules` 속성에 다음 값을 추가합니다.

```javascript
rules: {
  'react/prop-types': 'off',
}
```

다른 방법으로 prop type을 체크해주는 방법이 있습니다.

## 게시글 수정

`/board/create`의 POST 요청은 form에 작성한 게시글 생성 요청으로 만들었습니다.

게시글 수정할 때의 URL이 고민됐습니다.

`board/:boardId/modify`를 생각해봤는데, 좀 다른 방법을 사용하고 싶었습니다.

참고할 것을 찾아보니, Velog 서비스에서는 작성할 때는 `/write`, 수정할 때는 `/write/?id=1234` 같은 방식으로 하고 있었습니다.

코드를 자세히 본 것은 아니고 브라우저 주소창으로 확인했습니다.

게시글 생성 페이지에 진입했을 때, `useEffect()`를 사용했습니다. 여기서 쿼리 스트링 값이 없다면 새로운 글 생성이 되도록 했고, 쿼리 스트링에서 수정할 게시글의 id 값을 가져와 서버에 게시글의 정보를 요청합니다. 데이터를 응답받으면 그것으로 양식을 채웁니다.

그에 따라 버튼의 값도 생성/수정 둘 중 하나가 나오게 하고, 제출시 어디로 요청을 보낼지도 다르게 했습니다. 아래는 코드 일부분입니다.

```javascript
useEffect(() => {
  if (searchParams.size == 0) {
    return;
  }
  const getOriginalData = async () => {
    setModifyMode(true);
    const boardId = searchParams.get('boardId');
    const res = await axios.get(`${API_URL}/board/${boardId}`);
    console.log('res:', res);
    setTitle(res.data.title);
    setContent(res.data.content);
  };
  getOriginalData();
}, [searchParams]);
```

잘 설계한 것인지 잘 모르겠지만, 계속 다듬어보겠습니다.

## Board, BoardList, BoardListItem

페이지는 Board, 컴포넌트는 BoardList, BoardListItem으로 나눴습니다.

props를 이용해 데이터와 핸들러를 내렸습니다.

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt]()

<br>
<br>

# 참고

---
