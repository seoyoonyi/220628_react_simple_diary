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

### React Lifecycle 제어하기 - useEffect

1. 단락회로 평가
   단락회로 평가는 모든 피연산자와 연산자를 평가하지 않고서도 식의 결과가 결정되는 것을 의미한다. true or x의 경우 앞이 true니까 or 조건의 확인이 불필요하고, false and x의 경우 앞이 false니깐 뭐든 오든 false다. 때문에 and뒤의 조건 확인이 불필요하다. 이처럼 식을 계산하는 중 시의 나머지 부분을 평가하지 않아도결과가 결정되어 계산을 중지하는 것을 단락회로 평가라고 한다.
   ( https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=tlqdnjsahwk&logNo=50193854262 )

```c
  {isVisible && <UnmountTest />}
```

2. 생애주기에 대하여

- Mount(탄생): 화면에 나타나는 것 _ex) 초기화 작업_
- Update(변화): 업데이트(리렌더) _ex) 예외 처리 작업_
- Unmount(죽음): 화면에서 사라짐 _ex) 메모리 정리 작업_

### React에서 API 호출하기

1. Math.floor(Math.random() \* 5) + 1은 수학적 연산(랜덤으로 1~5까지 값을 만들어준다)

```c
      const initData = res.slice(0, 20).map((it) => {
      return {
        author: it.email,
        content: it.body,
        emotion: Math.floor(Math.random() * 5) + 1,
        created_date: new Date().getTime(),
        id: dataId.current++,
      };
    });
```

2. 다른 강의로 통해 비동기에 대해서 좀 더 공부해야겠다.

```c
  const getData = async () => {
    const res = await fetch(
      'https://jsonplaceholder.typicode.com/comments'
    ).then((res) => res.json());
```

### 최적화 1 - useMemo

1. Memoization : 이미 계산 해 본 연산 결과를 기억 해 두었다가 동일한 계산을 시키면, 다시 연산하지 않고 기억 해 두었던 데이터를 반환 시키게 하는 방법
2. 일기분석이 2번 시작된 이유

- Mount될때 data의 state가 빈 배열에서 시작되어 그 순간에 getDiaryAnalysis를 만나게 된다.
- getData가 fetch로 연결하면서 setData 안에 있는 data가 한번 바뀌게 됩니다. Rerender가 일어나면서 그안에 모든 함수가 재생성되면서 getDiaryAnalysis가 실행된다.

3. useMemo는 콜백함수를 리턴을 하는 값을 리턴받는다. 결국 getDiaryAnalysis는 값으로 사용해야한다. 함수로 사용하면 안된다.

```c
  const getDiaryAnalysis = useMemo(() => {
    console.log('일기분석시작');
    const goodCount = data.filter((it) => it.emotion >= 3).length;
    const badCount = data.length - goodCount;
    const goodRatio = (goodCount / data.length) * 100;
    return { goodCount, badCount, goodRatio };
  }, [data.length]);

  const { goodCount, badCount, goodRatio } =
    getDiaryAnalysis;
```

### 최적화 2 - React.memo

1. useMemo는 React Hook, React.memo는 HOC(고차 컴포넌트) 입니다. _React HOOk는 컴포넌트 내부에서만 사용_
2. A button는 리렌더링 안되고 B button은 리렌더링이 될까?
   A button은 props로 받은 count는 고정값으로 1을 설정했기대문에 리렌더링 안되었다.
   B button은 props로 받은 obj가 객체이고 객체의 기본 속성으로 얕은비교(객체의 주소에 의한 비교)로 시작해서 리렌더링이 안된 것 이다.
3. a={} 객체로 할당받고 a=b에 대입시키면 같은 주소로 비교되어 true값을 반환한다.
4. areEqual 함수[리액트 공식문서]
   class 컴포넌트의 `shouldComponentUpdate()` 메서드와 달리, `areEqual` 함수는 props들이 서로 같으면 true를 반환(리렌더링)하고, props들이 서로 다르면 false를 반환(리렌더링X)합니다. 이것은 `shouldComponentUpdate()`와 정반대의 동작입니다.

```c
  function MyComponent(props) {
    /* props를 사용하여 렌더링 */
  }
  function areEqual(prevProps, nextProps) {
    /*
    nextProps가 prevProps와 동일한 값을 가지면 true를 반환하고, 그렇지 않다면 false를 반환
    */
  }
  export default React.memo(MyComponent, areEqual);
```

```c
  const CounterB = ({ obj }) => {
    useEffect(() => {
      console.log(`CounterB Update - count: ${obj.count}`);
    });
    return <div>{obj.count}</div>;
  };

  const areEqual = (prevProps, nextProps) => {
    return prevProps.obj.count === nextProps.obj.count;
  };

  const MemoizedCounterB = React.memo(CounterB, areEqual);
```

### 최적화 3 - useCallback

1. Memoization은 이전 값을 메모리에 저장해 동일한 계산의 반복을 제거해 빠른 처리를 가능하게 하는 기술
2. useCallback은 함수를 memoized함.
3. useCallback을 통해 memoized된 함수는 eventhandler로 사용되며 두번째 인자인 [] 배열인 요소가 변경될때마다 새로운 함수가 생성, 두번째 인자인 배열에 담긴 값이 변경될때까지 첫번째 인자인 함수를 저장해놓고 재사용
4. 배열에 상대값을 담아 놓지 않는 경우 상태값이 변화하더라도 함수를 통한 출력값이 변화하지 않을수 있음(useEffect의 빈배열일 경우에 해당)
5. 부모 컴포넌트가 렌더링 될때 마다 부모 컴포넌트의 함수를 담고 있는 자식 컴포넌트도 렌더링(App.js에서 DiaryEditor.js한테 props로 onCreate 전달)
6. 비동기적인 방법을 해결 하기 위해서 함수형 업데이트를 사용할 수 있다. 함수형 업데이트는 useCallback과 함께 props로 전달된 함수를 최적화할 때도 유용하게 사용된다.(https://velog.io/@fltxld3/React-useCallback로-Rendering-최적화3-feat.일기장)

```c
  const onCreate = useCallback(
    (author, content, emotion) => {
      const created_date = new Date().getTime();
      const newItem = {
        author,
        content,
        emotion,
        created_date,
        id: dataId.current,
      };

      dataId.current += 1;
      setData((data) => [newItem, ...data]);
    },
    [data]
  );
```

### 최적화 4 - 최적화 완성

### 복잡한 상태 관리 로직 분리하기 - useReducer

1. 최적화는 경험이 필요하다.
2. useReducer도 경험이 필요하다.
3. Redux를 배우면서 store에 대한 개념을 배웠는데 이부분도 경험이 필요하다고 느낀다.
4. 작은 개념부터 개인프로젝트에 적용시켜봐야겠다.
