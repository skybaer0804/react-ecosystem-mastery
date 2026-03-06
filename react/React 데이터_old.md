<aside>
⚠️

기본문법, API, 네트워크 로딩, 에러 처리는 삭제했습니다.

</aside>

## 1. 배열 렌더링시 key 지정이 필요한 이유

리엑트는 key로 렌더링 위치를 기억합니다. 만약 key가 없다면 스크립트 에러로 알수 있습니다.

예를 들어 key가 없다면 해당 컴포넌트에 무언가를 기록했다가 해당 컴포넌트를 삭제하면, 기록한 데이터가 엉뚱한 위치에 남게 됩니다. 엉뚱한 위치에 렌더링 되지 않도록 Key 값을 입력합니다.

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/24d55caf-4cef-4e72-9011-a2960bd70da1/Untitled.mp4)

---

## 2. 페이지네이션

한번에 많은 데이터를 가져오면 오래걸리거나, 못받을 수가 있습니다.

그래서 조금씩 데이터를 받아오는데 **책의 페이지를 조금씩 받아오는 듯하여 페이지네이션이라고 합니다.**

**오프셋 기반**

내가 몇 개 데이터 갖고있고, 10개 더 보내줘. 이런 방식입니다.

- offset : 상쇄하다. 지금까지 받아온 데이터 개수

```jsx
GET https://example.com/posts?**offset**=20&limit=10  // 20개 갖고 있음, 이후 10개 보내줘
```

- **단점 :** 서버에 데이터가 추가되거나, 삭제되거나 할때 앞으로 받아올 데이터가 소실되거나, 중복됩니다.

**커서 기반**

책갈피를 보내줍니다. 커서 데이터 이후로 10개 보내줘. 이런 방식입니다.

```jsx
GET https://example.com/posts?**cursor**=WakZec&limit=10 // WakZec 이후 10개 보내줘
```

## 3. 비동기 함수 State 변경시 콜백 함수 준수

간단히, 기존 참조값과 데이터를 콜백 파라미터로 받아서 새로운 참조값과 데이터로 setState를 해줘야합니다.

개념이 이해되면 콜백함수를 자연스럽게 사용하게됩니다.

비동기 함수를 사용할때 문제점은 잘못된 시점의 값을 사용할 수 있다는 것입니다. 변동되는 데이터 처리를 잘 해주어야 합니다. 아래는 잘못된 예시입니다.

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fd660a96-6e83-411c-b486-5b2950f9b8a9/Untitled.mp4)

```jsx
  // 문제 코드
  const handleDelete = (id) => {
    const newItems = items.filter((item) => item.id !== id);
    setItems(newItems);
  };

  const handleLoad = async (option) => {
    **const { reviews, paging } = await getReviews(option);**
    if (option.offset === 0) {
      setItems(reviews);
    } else {
      setItems([...items, ...reviews]);
    }
    setOffset(option.offset + reviews.length);
    setHasNext(paging.hasNext);
  };

  const handleLoadMore = () => {
    handleLoad({ order, offset, limit: LIMIT });
  };
```

1. handleLoadMore 함수가 실행되며 handleLoad 비동기 함수 동작 중.
2. 바로 handleDelete를 실행하면 데이터가 삭제됩니다.
하지만, handleLoad는 비동기로 실행되며 setItems([…items, …reviews]); 에서 item은 삭제되기 전 데이터를 갖고있기때문에 이 함수가 완료될때 삭제된 데이터가 복구됩니다.
- 임시로 슬로우 환경 느린 3g로 설정하면 속도 설정해보면 알 수 있습니다.
- 해결 방법은 콜백함수를 습관화해서 사용하는 것이다.

```jsx
// 콜백 함수와 새로운 참조값 준수.
const handleLoad = async (option) => {
    const { reviews, paging } = await getReviews(option);
    if (option.offset === 0) {
      setItems(reviews);
    } else {
      **setItems((prev) => [...prev, ...reviews]);**
    }
    setOffset(option.offset + reviews.length);
    setHasNext(paging.hasNext);
  };
```

콜백 함수에 들어가는 prev라는 값은는 고정된 값이 아닙니다. 이름은 상관없습니다.

이것은 **리엑트 set함수의 파라미터로, 리엑트가 현재 시점의 state 값을 리턴해 줍니다.**

수정된 코드는 콜백함수를 사용해 현재시점 state를 가져와 새로운 참조값 배열을 생성해서 setState에 적용됬습니다. **콜백 형태를 사용하는 습관을 들이는게 좋습니다.**

## 4. JS 모던한 표기법

```jsx
// 모던한 프로퍼티 표기 1
const title = "NWJ"; 
const birth = "2023-01-01";
const job= "Programer";

const user ={
  title,
  birth,
  job
}
```

```jsx
// 모던한 프로퍼티 표기 2
function getFullName() {
  return `${this.firstName} ${this.lastName}`;
}

const user ={
  firstName: "No",
  lastName: "WonJae",
  getFullName
}
```

```jsx
// 모던한 프로퍼티 표기 3

function getFullName() {
  return `${this.firstName} ${this.lastName}`;
}

const user ={
  firstName: "No",
  lastName: "WonJae",
  getFullName(){
    return `${this.firstName} ${this.lastName}`;
  }
}

```

```jsx
// 계산된 속성(computed property name)
const user = {
      [표현명] : 값,
}

// 계산된 속성명 사용 예
const propertyName = 'birth';
const getJob = () => 'job';

const codeit- {
  ['topic' + 'Name']: 'Mordern JavaScript',
  [propertyName]: 2017,
  [getJob()]: '프로그래밍 강사'
}
```

## 5. 제어 컴포넌트와 비제어 컴포넌트

- 제어컴포넌트 (controlled component)
    - input의 value 값을 리엑트에서 지정합니다.
    - 리엑트에서 사용하는 값과 실제 인풋 값이 항상 일치합니다.
    - 주로 권장되는 방법입니다.
- 비제어 컴포넌트 (uncontrolled component)
    - 인풋 value 값을 리엑트에서 지정하지 않습니다.
    - **파일 인풋의 경우 비제어 컴포넌트입니다.**

```jsx
// 제어 컴포넌트
import { useState } from './react';

function Mycomponent() {
  const [value, setValue] = useState('');
  
	**// 실제 작성할때 소문자로 작성해도 대문자로 표시됩니다. 
	// 컴포넌트의 value값 또한 당연히 대문자입니다.
  const handleChange = (e) => {
    const nextValue = e.target.value.toUpperCase();
    setValue(nextValue);
  }**
  **return <input value={value} onChange={handleChange} />**   
}

// 비제어 컴포넌트
import { useState } from './react';

function Mycomponent() {
  const [value, setValue] = useState('');
  
  **// 실제 작성할때 소문자작성시 소문자 표시됨
  // 컴포넌트의 value값은 대문자로 저장됩니다.
  const handleChange = (e) => {
    const nextValue = e.target.value.toUpperCase();
    setValue(nextValue);
  }

  return <input onChange={handleChange} />** 
}
```

### 5.1 File을 제어 컴포넌트로 작성시 **에러가 나는 이유**

**DOM**에서 발생한 에러입니다. 파일 인풋에서 value는 파일 이름을 받는데 자바 스크립트로는 빈 문자열로 밖에 설정 할 수 없다고 나옵니다. 이는 해커가 내 파일에 조작을 못하도록 하는 보안 문제로 HTML에서 파일 인풋은 반드시 사용자만 바꿀 수 있고 자바 스크립트에서는 바꿀 수 없게금 되어있습니다.

- **웹 브라우저는 파일의 경로를 숨겨줍니다.**

이러한 특징 때문에 파일 인풋은 비제어 컴포넌트로 만들어야 합니다.

**비제어 형식으로 진행**

- 파일 인풋에서 state 대신에 props를 바꿔서 사용합니다. 이를 **State rebooting** 이라고합니다.

```jsx
import { useState } from "react";
import "./ReviewForm.css";

export default function ReviewForm() {
	...
  return (
    <form className="ReviewForm" onSubmit={handleSubmit}>
      <FileInput
        name="imgFile"
        value={values.imgFile}
        onChange={handleChange}
      />
      ...
      <button type="submit"> 보내기 </button>
    </form>
  );
}

function FileInput({ name, value, onChange }) {
  const handleChange = (e) => {
    const nextValue = e.target.files[0];
    onChange(name, nextValue);
  };

  return <input type="file" onClick={handleChange} />;
```

### 5.2 useRef로 DOM 노드 가져오기

ref 는 React에 있는 특별한 prop 입니다. ref 는 특정 시점의 DOM 노드에 접근 할 수 있게 도와줍니다.

- DOM 노드는 반드시 렌더링이 끝나야 생겨납니다.
- ref는 DOM 노드가 생겨나야 하는데, 조건부 렌더링 일 경우 DOM에서 노드가 없을 수 있습니다.
- 그래서 반드시 current  값이 있는지 확인하는 **조건문 작성 필요합니다**.
- 초기값을 안줘도 에러로그가 나오니. null이라도 주는게 좋습니다.

```jsx
import { useEffect, useRef } from "react";

function FileInput({ name, value, onChange }) {
  const inputRef = useRef(null);

  const handleChange = (e) => {
    const nextValue = e.target.files[0];
    onChange(name, nextValue);
  };

  useEffect(() => {
    if (inputRef.current) {
      console.log(inputRef.current);
    }
  });

  return <input type="file" onClick={handleChange} ref={inputRef}/>;
}

export default FileInput;
```

### 5.3 File input 초기화 - ref 활용

파일 인풋 의  value 속성은 사용자만 바꿀 수있고, 자바스크립트에서 바꿀 때는 빈문자열로만 바꿀 수 있는 방법을 위에서 말씀드렸습니다. 이 방법을 통해 **초기화 하는 방법**입니다.

```jsx
import { useEffect, useRef } from "react";

function FileInput({ name, value, onChange }) {
  const inputRef = useRef();

  const handleChange = (e) => {
    const nextValue = e.target.files[0];
    onChange(name, nextValue);
  };

  useEffect(() => {
    if (inputRef.current) {
      console.log(inputRef.current);
    }
  }, [value]);

  const handleClearClick = () => {
    const inputNode = inputRef.current;  // ref로 DOM 노드에 값이 있는지 확인
    if (!inputNode) return; //없으면 리턴

    inputNode.value = ""; // 있으면 value 빈문자열로 바꿈(초기화)
    onChange(name, null); // State 변경
  };

  return (
    <div>
      <input type="file" onClick={handleChange} ref={inputRef} />  //ref Prop 추가해둠. DOM 노드 확인용.
      {value && <button onClick={handleClearClick}>X</button>}
    </div>
  );
}

export default FileInput;
```

### 5.4 사이드 이펙트

**URL.createObject로 URL을** 만들면 웹브라우저는 메모리를 할당하고, 파일에 해당하는 주소를 만들어줍니다. 

- 이 파일 인풋 컴포넌트는 렌더링하는 과정에서 리엑트 외부의 상태를 바꾸게 됩니다. **이런 식으로 컴포넌트 상태에서 외부의 상태를 바꾸는걸 '사이드 이펙트'라고 합니다. 네트워크 리퀘스트도 일종의 사이드 이펙트입니다.**

### 5.5 사이드 이펙트 정리하기

- 위에 이미지를 계속 추가하면 메모리가 계속 할당되어 낭비가 되므로, 이를 해제해 줘야 합니다.
- **revokeObjectURL** 이라는 함수로 메모리 할당을 해제하여 사이드이펙트를 정리합니다.

```jsx
useEffect(() => {
    if (!value) return;

    const nextPreview = URL.createObjectURL(value);
    setPreview(nextPreview);

    return () => {
      setPreview();
      **URL.revokeObjectURL(nextPreview);**
    };
  }, [value]);
```

- **사이드 이펙트**
    
    # **사이드 이펙트(Side Effect)란?**
    
    사이드 이펙트는 한국어로는 '부작용'이라는 뜻입니다.
    
    일상생활에서는 '약의 부작용'처럼 사용하는 단어인데요.
    
    예를 들면 감기약을 먹었을 때
    
    감기 증상은 없어졌지만 (작용)
    
    **피부가 붉게 올라오면 (부작용)**
    
    이 약에는 부작용이 있다고 할 수 있죠.
    
    일상생활에서는 주로 안 좋은 것들을 부작용이라고 부르지만
    
    프로그래밍에선 말 그대로 외부에 부수적인 작용을 하는 걸 말하는데요.
    
    어떤 것인지 간단한 자바스크립트 함수를 예시로 알아봅시다.
    
    ```
    let count = 0;
    
    function add(a, b) {
      const result = a + b;
      count += 1; // 함수 외부의 값을 변경
      return result;
    }
    
    const val1 = add(1, 2);
    const val2 = add(-4, 5);
    
    ```
    
    위 코드에서 `add` 함수를 봅시다.
    
    이 함수는 `a`, `b` 를 파라미터로 받아서 더한 값을 리턴하는데요.
    
    함수 코드 중에서 함수 바깥에 있는 `count` 라는 변수의 값을 변경하는 코드가 있죠?
    
    `add` 함수는 실행하면서 함수 외부의 상태(count 변수)가 바뀌기 때문에,
    
    이런 함수를 **"사이드 이펙트가 있다"**고 합니다.
    
    우리에게 친숙한 예로는 `console.log` 함수가 있는데요.
    
    `console.log` 함수를 사용하면 값을 계산해서 리턴하는 게 아니라
    
    웹 브라우저 콘솔 창에 문자열을 출력하죠?
    
    외부 상태를 변경해서 문자열을 출력하는 겁니다.
    
    이렇게 함수 안에서 함수 바깥에 있는 값이나 상태를 변경하는 걸 '사이드 이펙트'라고 부릅니다.
    
    # **사이드 이펙트와 useEffect**
    
    `useEffect` 는 리액트 컴포넌트 함수 안에서
    
    **사이드 이펙트를 실행하고 싶을 때** 사용하는 함수입니다.
    
    예를들면 DOM 노드를 직접 변경한다거나,
    
    브라우저에 데이터를 저장하고,
    
    네트워크 리퀘스트를 보내는 것처럼 말이죠.
    
    주로 **리액트 외부에 있는 데이터나 상태를 변경할 때** 사용하는데요.
    
    간단한 예시들을 보면서 어떤 식으로 활용할 수 있는지 가볍게 살펴봅시다.
    
    ## **페이지 정보 변경**
    
    ```
    useEffect(() => {
      document.title = title; // 페이지 데이터를 변경
    }, [title]);
    
    ```
    
    ## **네트워크 요청**
    
    ```
    useEffect(() => {
      fetch('https://example.com/data') // 외부로 네트워크 리퀘스트
        .then((response) => response.json())
        .then((body) => setData(body));
    }, [])
    
    ```
    
    ## **데이터 저장**
    
    ```
    useEffect(() => {
      localStorage.setItem('theme', theme); // 로컬 스토리지에 테마 정보를 저장
    }, [theme]);
    
    ```
    
    참고: `localStorage` 는 웹 브라우저에서 데이터를 저장할 수 있는 기능입니다.
    
    ## **타이머**
    
    ```
    useEffect(() => {
      const timerId = setInterval(() => {
        setSecond((prevSecond) => prevSecond + 1);
      }, 1000); // 1초마다 콜백 함수를 실행하는 타이머 시작
    
      return () => {
        clearInterval(timerId);
      }
    }, []);
    
    ```
    
    참고: `setInterval` 이라는 함수를 쓰면 일정한 시간마다 콜백 함수를 실행할 수 있습니다.
    
    # **useEffect를 쓰면 좋은 경우**
    
    여기서 한 가지 의문점이 들 수도 있습니다.
    
    앞에서 우리는 데이터 불러오는 기능 만들 때
    
    처음에는 `onclick` 이벤트 핸들러에서 네트워크 리퀘스트를 보냈고,
    
    페이지가 열리자마자 데이터를 불러오기 위해서 `useEffect` 를 사용하는 걸로 바꿨습니다.
    
    하지만 만약 둘 다 가능한 경우라면 핸들러 함수를 써야 하는 걸까요?
    
    아니면 `useEffect` 를 써야 하는 걸까요?
    
    여기에 정해진 규칙은 없습니다.
    
    하지만 `useEffect` 는 쉽게 말해서 '동기화'에 쓰면 유용한 경우가 많은데요.
    
    여기서 동기화는 컴포넌트 안에 데이터와 리액트 바깥에 있는 데이터를 일치시키는 걸 말합니다.
    
    이게 어떤 의미인지 간단한 예시를 통해 살펴봅시다.
    
    아래 `App` 컴포넌트는 인풋 입력에 따라 페이지 제목을 바꾸는 컴포넌트인데요.
    
    ## **핸들러 함수만 사용한 예시**
    
    ```
    import { useState } from 'react';
    
    const INITIAL_TITLE = 'Untitled';
    
    function App() {
      const [title, setTitle] = useState(INITIAL_TITLE);
    
      const handleChange = (e) => {
        const nextTitle = e.target.value;
        setTitle(nextTitle);
        document.title = nextTitle;
      };
    
      const handleClearClick = () => {
        const nextTitle = INITIAL_TITLE;
        setTitle(nextTitle);
        document.title = nextTitle;
      };
    
      return (
        <div>
          <input value={title} onChange={handleChange} />
          <button onClick={handleClearClick}>초기화</button>
        </div>
      );
    }
    
    export default App;
    
    ```
    
    `handleChange` 함수와 `handleClearClick` 함수가 있습니다.
    
    모두 `title` 스테이트를 변경한 후에 `document.title` 도 함께 변경해주고 있는데요.
    
    여기서 `document.title` 값을 바꾸는 건 외부의 상태를 변경하는 거니까 사이드 이펙트입니다.
    
    만약 새로 함수를 만들어서 `setTitle` 을 사용하는 코드를 추가할 때마다
    
    `document.title` 값도 변경해야 한다는 걸 기억해뒀다가 관련된 코드를 작성해야 한다는 점이 아쉽습니다.
    
    동료 개발자가 나중에 컴포넌트를 수정하거나 1년 뒤의 내가 컴포넌트를 수정할 때 빠뜨리기 좋겠죠?
    
    ## **useEffect를 사용한 예시**
    
    ```
    import { useEffect, useState } from 'react';
    
    const INITIAL_TITLE = 'Untitled';
    
    function App() {
      const [title, setTitle] = useState(INITIAL_TITLE);
    
      const handleChange = (e) => {
        const nextTitle = e.target.value;
        setTitle(nextTitle);
      };
    
      const handleClearClick = () => {
        setTitle(INITIAL_TITLE);
      };
    
      useEffect(() => {
        document.title = title;
      }, [title]);
    
      return (
        <div>
          <input value={title} onChange={handleChange} />
          <button onClick={handleClearClick}>초기화</button>
        </div>
      );
    }
    
    export default App;
    
    ```
    
    `useEffect` 를 사용한 예시에서는 `document` 를 다루는 사이드 이펙트 부분만 따로 처리하고 있는데요.
    
    이렇게 하니 `setTitle` 함수를 쓸 때마다 `document.title` 을 변경하는 코드를 신경 쓰지 않아도 되니까 편리합니다.
    
    게다가 처음 렌더링 되었을 때 'Untitled'라고 페이지 제목을 변경하는 효과까지 낼 수 있죠.
    
    그리고 이 코드를 본 사람이라면 누구든 이 컴포넌트는 `title` 스테이트 값을 가지고 항상 `document.title` 에 반영해줄 것이라고 쉽게 예측할 수 있는데요.
    
    이렇게 `useEffect` 는 리액트 안과 밖의 데이터를 일치시키는데 활용하면 좋습니다.
    
    `useEffect` 를 사용했을 때 반복되는 코드를 줄이고, 동작을 쉽게 예측할 수 있는 코드를 작성할 수 있기 때문이죠.
    
    # **정리 함수 (Cleanup Function)**
    
    ```
    useEffect(() => {
      // 사이드 이펙트
    
      return () => {
        // 사이드 이펙트에 대한 정리
      }
    }, [dep1, dep2, dep3, ...]);
    
    ```
    
    `useEffect` 의 콜백 함수에서 사이드 이펙트를 만들면 정리가 필요한 경우가 있습니다.
    
    이럴 때 콜백 함수에서 리턴 값으로 정리하는 함수를 리턴할 수 있었는데요.
    
    리턴한 정리 함수에서는 사이드 이펙트에 대한 뒷정리를 합니다.
    
    예를 들면 이미지 파일 미리보기를 구현할 때 Object URL을 만들어서 브라우저의 메모리를 할당(`createObjectURL`) 했는데요. 정리 함수에서는 이때 할당한 메모리를 다시 해제(`revokeObjectURL`)해줬었죠.
    
    ## **정리 함수가 실행되는 시점**
    
    쉽게 말해서 **콜백을 한 번 실행했으면, 정리 함수도 반드시 한 번 실행된다**고 생각하면 됩니다.
    
    정확히는 새로운 콜백 함수가 호출되기 전에 실행되거나 (앞에서 실행한 콜백의 사이드 이펙트를 정리), 컴포넌트가 화면에서 사라지기 전에 실행됩니다 (맨 마지막으로 실행한 콜백의 사이드 이펙트를 정리).
    
    ## **예시: 타이머**
    
    ```
    import { useEffect, useState } from 'react';
    
    function Timer() {
      const [second, setSecond] = useState(0);
    
      useEffect(() => {
        const timerId = setInterval(() => {
          console.log('타이머 실행중 ... ');
          setSecond((prevSecond) => prevSecond + 1);
        }, 1000);
        console.log('타이머 시작 🏁');
    
        return () => {
          clearInterval(timerId);
          console.log('타이머 멈춤 ✋');
        };
      }, []);
    
      return <div>{second}</div>;
    }
    
    function App() {
      const [show, setShow] = useState(false);
    
      const handleShowClick = () => setShow(true);
      const handleHideClick = () => setShow(false);
    
      return (
        <div>
          {show && <Timer />}
          <button onClick={handleShowClick}>보이기</button>
          <button onClick={handleHideClick}>감추기</button>
        </div>
      );
    }
    
    export default App;
    
    ```
    
    일정한 시간 간격마다 콜백 함수를 실행하는 `setInterval` 이라는 함수도 정리가 필요한 사이드 이펙트입니다.
    
    매번 타이머를 시작하기만 하면 타이머가 엄청나게 많아지겠죠?
    
    이 컴포넌트는 렌더링이 끝나면 타이머를 시작하고, 화면에서 사라지면 타이머를 멈춥니다.
    
    코드를 보면서 동작을 살펴볼까요?
    
    사용자가 '보이기' 버튼을 눌렀을 때 `show` 값이 참으로 바뀌면서 다시 렌더링 됩니다.
    
    조건부 렌더링에 의해서 `Timer` 컴포넌트를 렌더링 하겠죠?
    
    `Timer` 컴포넌트에서는 `useEffect` 에서 타이머를 시작하고, 정리 함수를 리턴합니다.
    
    콘솔에는 '타이머 시작 🏁'이 출력됩니다.
    
    다시 사용자가 '감추기' 버튼을 누르면 `show` 값이 거짓으로 바뀌면서 다시 렌더링 됩니다.
    
    조건부 렌더링에 의해서 이제 `Timer` 컴포넌트를 렌더링 하지 않겠죠?
    
    그럼 리액트에선 마지막으로 앞에서 기억해뒀던 정리 함수를 실행해줍니다.
    
    타이머를 멈추고 콘솔에는 '타이머 멈춤 ✋'이 출력됩니다.
    
    이런 식으로 정리 함수를 리턴하면 사이드 이펙트를 정리하고 안전하게 사용할 수 있다는 것.
    
    꼭 기억해주세요!
    

---

## 6. Hook을 반복문에서 사용하면 안되는 이유

hook은 반드시 함수의 최상위에서 실행되어야 합니다.

즉, 반복문이나 조건문 안에서 쓰면 안됩니다. 리엑트에서는 훅이 실행된 순서대로 기억합니다.

실행한 순서대로 값을 연결해 두는 건데 만약 반복문이나 조건문을 쓰게되면 상황에 따라서 이 순서가 바뀌거나 건너 뛰게됩니다. 그래서 반복문 조건문에서는 훅을 사용할 수 없습니다.

![리액트는 훅이 실행된 순서를 기억합니다.](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fdf6a03c-dbac-4e7a-a265-8bae4e955a91/Untitled.png)

리액트는 훅이 실행된 순서를 기억합니다.

---
