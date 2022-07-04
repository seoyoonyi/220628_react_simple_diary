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

### React에서 배열 사용하기 2 - 데이터 추가하기

1. React 단방향으로만 데이터가 흐른다. (event는 역방향으로 흐른다)
2. 저장할때 흐름이 중요하다. UX의 힘이 여기서 실행된다.

```c
      const handleSubmit = () => {

        /* 작가 글자수에 대한 제한 */
        if (state.author.length < 1) {
          authorInput.current.focus();
          return;
        }

        /* 일기 본문 글자수에 대한 제한 */
        if (state.content.length < 5) {
          contentInput.current.focus();
          return;
        }

        /* 새로추가 */
        onCreate(state.author, state.content, state.emotion);

        /* 저장이 잘 되었다는 안심을 주기 위해 alert 사용 */
        alert('저장성공');

        /* 새로 적기 위해 일기장 에디터는 초기화 */
        setState({
          author: '',
          content: '',
          emotion: 1,
        });
  };
```

### React에서 배열 사용하기 3 - 데이터 삭제하기

1. id값 비교로 데이터 삭제가 가능하다.
2. 그 값이 다르면 화면에 보여지고 같으면 화면에 제외됨

```c
      const onRemove = (targetId) => {
      console.log(`${targetId}가 삭제 되었습니다.`);
      const newDiaryList = data.filter((it) => it.id !== targetId);
      setData(newDiaryList);
  };
```

### React에서 배열 사용하기 4 - 데이터 수정하기

1. 수정할때 여러가지 경우의 수를 생각해야할 것
2. 수정화면일때 버튼의 내용, 완료화면일때 버튼의 내용, 취소일때 버튼의 내용
3. 다시 공부할때 흐름을 잘 확인할 것

```c
        <div className="content">
          {isEdit ? (
            <>
              <textarea
                ref={localContentInput}
                value={localContent}
                onChange={(e) => setLocalContent(e.target.value)}
              />
            </>
          ) : (
            <>{content}</>
          )}
        </div>
        {isEdit ? (
          <>
            <button onClick={handleQuitEdit}>수정취소</button>
            <button onClick={handleEdit}>수정완료</button>
          </>
        ) : (
          <>
            <button onClick={handleRemove}>삭제하기</button>
            <button onClick={toggleIsEdit}>수정하기</button>
          </>
        )}
      </div>
```
