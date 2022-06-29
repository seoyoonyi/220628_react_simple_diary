## 한입 크기로 잘라 먹는 리액트 <간단 일기장 프로젝트> 새롭게 알게된 사실 적기

### React에서 사용자 입력 처리하기

1. useState를 객체를 내보낼수 있다는 것
2. 동적으로 key값을 받을수 있다는 것

```c
  const [state, setState] = useState({
    author: '',
    content: '',
    emotion: 1,
  });
```

```c
  const handleChangeState = (e) => {
    console.log(e.target.name);
    setState({
      ...state,
      [e.target.name]: e.target.value,
    });
  };
```

```c
  return(
  <input ref={authorInput} name="author" value={state.author} onChange={handleChangeState}/>
  <textarea ref={contentInput} name="content" value={state.content} onChange={handleChangeState}></textarea>
  )
}
```

### React에서 DOM 조작하기 - useRef

1. useRef를 이용해서 DOM을 조작해 focus를 만든 것

```c
  const handleSubmit = () => {
    if (state.author.length < 1) {
      authorInput.current.focus();
      return;
    }
    if (state.content.length < 5) {
      contentInput.current.focus();
      return;
    }
    alert('저장성공');
  };
```

### React에서 배열 사용하기 1 - 리스트 렌더링(조회)

1. prop를 사용해 diaryList.length 값이 없는 경우 defaultProps로 처리

```c
DiaryList.defaultProps = {
  diaryList: [],
};
```

2. map으로 전개연산자를 사용해 하위컴포넌트에 prop을 넘김

```c
      <div>
        {diaryList.map((it) => (
          <DiaryItem key={it.id} {...it} />
        ))}
      </div>
```

3. new Date().getTime()은 ms(milliseconds)로 날짜를 표현한것을 환산해 가져옴
4. new Date(created_date).toLocaleString()은 밀리세컨트를 가져온것을 현지화 시간으로 교체
   \*created_date는 이미 밀리세컨트를 표현됨
   \?여기서 드는 의문 값을 미리 toLocalString으로 먼저 가져오면 어떻게 될까??

```c
    const dummyList = [
      {
        id: 1,
        author: '이정환',
        content: '하이 1',
        emotion: 1,
        created_date: new Date().getTime(),
      }
    ]
```

```c
    <span className="date">{new Date(created_date).toLocaleString()}</span>
```
