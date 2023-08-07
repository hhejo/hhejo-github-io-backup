---
title:  "React, Vite, Node.js, Express를 이용해 게시판 만들기 05"
date: 2023-08-07
last_modified_at: 2023-08-07
excerpt: "Intl, 컴포넌트 분할, 뒤로가기"
categories:
  - BulletinBoardPjt
tags:
  - [pjt, javascript, nodejs, express, react, intl]
---

---

<br>

# Server

## Intl

JavaScript 내장 모듈 `Intl`을 사용했습니다.

날짜를 다루는 것은 `Date`를 이용해도 되지만, 더 편하게 쓸 수 있고 내장도 되어 있기 때문에 연습해볼 겸 사용했습니다.

```javascript
app.post('/api/board', (req, res) => {
  const id = new Date().getTime();
  const createdAt = new Intl.DateTimeFormat('ko', {
    year: '2-digit',
    month: '2-digit',
    day: '2-digit',
    hourCycle: 'h24',
    hour: '2-digit',
    minute: '2-digit',
  }).format(new Date());
  const createdDate = createdAt.slice(0, 10).split('. ').join('/');
  const createdTime = createdAt.slice(12, 17);
  const { title, content } = req.body;
  const newData = { id, title, content, createdDate, createdTime };
  console.log('newData', newData);
  tempDB.push(newData);
  return res.json(newData);
});
```

<br>
<br>

# Client

## 컴포넌트 분할

Board 페이지, CreateBoard 페이지, DetailBoard 페이지의 컴포넌트를 만들고 나눴습니다.

### Board

```javascript
const Board = () => {
  // ...

  return (
    <Container>
      <PageTitle pageTitle={'BOARD'} />
      <BoardList boardList={boardList} />
      <Button type="button" onClick={() => navigate('/board/create')}>
        작성하기
      </Button>
    </Container>
  );
};
```

`PageTitle` 컴포넌트는 페이지의 큰 제목을 나타냅니다.

`BoardList` 컴포넌트는 게시글 목록을 나타내고, `BoardListItem`을 포함합니다.

`Button` 컴포넌트는 재사용을 많이 하기 때문에 따로 컴포넌트로 만들고, 타입과 핸들러와 내용물을 전달할 수 있게 했습니다.

### CreateBoard

```javascript
const CreateBoard = () => {
  // ...

  return (
    <Container>
      <PageTitle pageTitle={isModify ? 'MODIFY' : 'CREATE'} />
      <Form submitHandler={submitHandler}>
        <Input
          type="text"
          htmlFor="title"
          labelContent="제목"
          id="title"
          name="title"
          placeholder="제목을 입력하세요"
          value={title || ''}
          onChange={titleHandler}
        />
        <TextArea
          htmlFor="content"
          labelContent="내용"
          id="content"
          name="content"
          placeholder="내용을 입력하세요"
          value={content || ''}
          onChange={contentHandler}
        />
        <div className="flex items-center justify-between">
          <Button type="submit">{isModify ? '수정' : '작성'}</Button>
          <div
            className="inline-block align-baseline font-bold text-sm text-sky-500 hover:text-sky-800 cursor-pointer transition"
            onClick={() => navigate(-1)}
          >
            뒤로가기
          </div>
        </div>
      </Form>
    </Container>
  );
};
```

`Form`을 컴포넌트로 만들고, children을 전달할 수 있게 했습니다.

`Input` 컴포넌트와 `TextArea` 컴포넌트도 따로 만들고, 속성을 전달할 수 있게 했습니다.

### DetailBoard

```javascript
const DetailBoard = () => {
  // ...

  return (
    <Container>
      <PageTitle pageTitle={'DETAIL'} />
      <div className="space-y-4">
        <div className="flex justify-between border-b">
          <div className="text-xl font-bold">{board.title}</div>
          <div className="flex space-x-2 text-sm">
            <div className="">(닉네임)</div>
            <div className="text-gray-500">
              {board.createdDate} {board.createdTime}
            </div>
          </div>
        </div>
        <div>{board.content}</div>
        <div className="flex items-center justify-between">
          <div className="space-x-4">
            <Button type="button" onClick={modifyHandler}>
              수정하기
            </Button>
            <Button type="button" onClick={deleteHandler}>
              삭제하기
            </Button>
          </div>
          <div
            className="inline-block align-baseline font-bold text-sm text-sky-500 hover:text-sky-800 cursor-pointer transition"
            onClick={() => navigate(-1)}
          >
            뒤로가기
          </div>
        </div>
      </div>
    </Container>
  );
};
```

아직 어떻게 변경될 지 몰라 컴포넌트로 많이 나누지 않았습니다.

## 뒤로가기

특정 링크를 누르면 뒤로가기를 구현했습니다. `-1` 값을 넣어주면 뒤로가기가 됩니다.

```javascript
const navigate = useNavigate();
const someHandler = () => navigate(-1);
```

<br>
<br>

# 느낀점

클라이언트 쪽이 정말 시간이 많이 걸렸습니다.

특히 컴포넌트로 정리하고, 어디를 어떻게 컴포넌트로 설계하고 데이터 흐름을 어떻게 둬야 할지 많이 생각하게 됩니다.

컴포넌트로 잘 정리하면 코드가 깔끔해져서 보기가 아주 좋았습니다.

<br>
<br>

# 현재까지의 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/bfeb6b8d66f5958a2dd4f0e2ca14df2016667dec)

<br>
<br>

# 참고

---
