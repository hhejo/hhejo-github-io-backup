---
title: React, Vite, Node.js, Express를 이용해 게시판 만들기 04
date: 2023-08-06 00:00:00 +0900
last_modified_at: 2023-08-06 00:00:00 +0900
categories: [BulletinBoardPjt]
tags:
  [bulletin-board-pjt, javascript, nodejs, express, react, cors, tailwindcss]
---

CORS, TailwindCSS, Component 분리

## Server

### CORS option

클라이언트의 origin 헤더를 특정해서 값을 주었습니다.

```javascript
const corsOptions = {
  origin: "http://localhost:5173",
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

## Client

### TailwindCSS

CSS는 없이 진행하려고 했지만 작업물을 보는 것이 고통스러워서 빠르게 CSS를 입히기 위해 TailwindCSS를 선택했습니다.

> [Install Tailwind CSS with Vite](https://tailwindcss.com/docs/guides/vite)

### Container

따로 Container 컴포넌트를 생성한 후, `props.children`을 감싸는 방식을 사용했습니다.

좀 더 재사용성이 증가한 것 같습니다.

```javascript
const Container = ({ children }) => {
  return <div className="container mx-auto">{children}</div>;
};

export default Container;
```

### Button

Button은 여기저기 많이 쓰일 것 같아서, 따로 컴포넌트를 작성했습니다.

```javascript
const Button = ({ children }) => {
  return (
    <button className="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded transition">
      {children}
    </button>
  );
};

export default Button;
```

### 보드 조회 컴포넌트명 수정

`BoardDetail.jsx`에서 `DetailBoard.jsx`로 수정했습니다.

전자는 `Board.jsx` 페이지의 컴포넌트 같은 느낌이 들어서, 후자와 같이 수정했습니다.

### PageTitle 컴포넌트 생성

페이지 Home, Board, DetailBoard 마다 화면에 페이지의 제목을 출력하는데, 컴포넌트로 만들면 좋을 것 같아서 `PageTitle.jsx` 컴포넌트로 만들었습니다.

```javascript
const PageTitle = ({ pageTitle }) => {
  return <div className="container mb-2 text-2xl">{pageTitle}</div>;
};

export default PageTitle;
```

### Board

원래 Board에서 `BoardList` 컴포넌트에 하나의 게시글을 클릭하면 디테일로 이동하는 핸들러를 달았었는데, 여기서 말고 BoardList에서 핸들러를 생성해서 달았습니다.

게시글 목록 데이터는 현재 Board에서 받고, 세부적인 핸들러는 아래의 컴포넌트에서 받는 것이 괜찮을 것 같았습니다.

```javascript
const Board = () => {
  const [boardList, setBoardList] = useState([]);

  useEffect(() => {
    (async () => {
      const res = await axios.get(`${API_URL}/board`);
      setBoardList(res.data);
    })();
  }, []);

  return (
    <Container>
      <PageTitle pageTitle={"BOARD"} />
      <BoardList boardList={boardList} />
      <Button>
        <Link to="/board/create">작성하기</Link>
      </Button>
    </Container>
  );
};
```

```javascript
const BoardList = ({ boardList }) => {
  const navigate = useNavigate();
  const goDetailHandler = (boardId) => navigate(`/board/${boardId}`);

  return (
    <div className="container mb-4">
      {boardList.map((el) => (
        <BoardListItem
          key={el.id}
          onClick={() => goDetailHandler(el.id)}
          title={el.title}
          content={el.content}
          createdDate={el.createdDate}
          createdTime={el.createdTime}
        />
      ))}
    </div>
  );
};
```

## 현재까지 진행 사항의 소스 코드

> [bulletin-board-pjt](https://github.com/hhejo/bulletin-board-pjt/tree/c117410cccded1c680ebef9e6a4c913bd0c36199)
