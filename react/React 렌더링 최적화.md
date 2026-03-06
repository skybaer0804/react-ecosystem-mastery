# React 렌더링 최적화

React 애플리케이션의 성능을 향상시키기 위한 렌더링 최적화 기법들을 알아봅니다.

## 1. 렌더링 과정 이해

React의 렌더링은 두 단계로 나뉩니다:

### Render Phase
- 컴포넌트를 호출하여 React Element를 생성
- Virtual DOM을 업데이트하고 변경 사항을 계산

### Commit Phase
- 실제 DOM에 변경 사항을 적용
- 브라우저가 화면을 다시 그리게 함

## 2. 불필요한 리렌더링 방지

### React.memo
컴포넌트의 props가 변경되지 않으면 리렌더링을 방지합니다.

```jsx
import { memo } from 'react';

const UserCard = memo(function UserCard({ user }) {
  console.log('UserCard 렌더링');
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
});

// props 비교 커스터마이징
const UserCard = memo(function UserCard({ user }) {
  return (
    <div>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
}, (prevProps, nextProps) => {
  // name과 email이 같으면 리렌더링하지 않음
  return prevProps.user.name === nextProps.user.name &&
         prevProps.user.email === nextProps.user.email;
});
```

### useCallback
함수 재생성을 방지하여 자식 컴포넌트의 불필요한 리렌더링을 막습니다.

```jsx
import { useCallback, useState } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log('버튼 클릭');
  }, []); // 의존성 배열이 빈 경우, 함수는 한 번만 생성

  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={() => setCount(count + 1)}>증가</button>
      <ChildComponent onClick={handleClick} />
    </div>
  );
}

const ChildComponent = memo(function ChildComponent({ onClick }) {
  console.log('ChildComponent 렌더링');
  return <button onClick={onClick}>클릭</button>;
});
```

### useMemo
복잡한 계산 결과를 메모이제이션하여 불필요한 재계산을 방지합니다.

```jsx
import { useMemo, useState } from 'react';

function ExpensiveCalculation({ numbers }) {
  const [multiplier, setMultiplier] = useState(1);

  // numbers가 변경될 때만 계산 수행
  const result = useMemo(() => {
    console.log('복잡한 계산 수행');
    return numbers.reduce((acc, num) => acc + num, 0) * multiplier;
  }, [numbers, multiplier]);

  return (
    <div>
      <p>결과: {result}</p>
      <button onClick={() => setMultiplier(multiplier + 1)}>
        곱하기 증가
      </button>
    </div>
  );
}
```

## 3. 리스트 렌더링 최적화

### Key prop의 중요성
React가 리스트 아이템을 효율적으로 추적할 수 있게 합니다.

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}> {/* 고유한 key 사용 */}
          {user.name}
        </li>
      ))}
    </ul>
  );
}
```

### 가상화 (Virtualization)
대량의 데이터를 효율적으로 렌더링합니다.

```bash
npm install react-window
```

```jsx
import { FixedSizeList as List } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );

  return (
    <List
      height={400}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </List>
  );
}
```

## 4. 코드 분할 (Code Splitting)

### React.lazy와 Suspense
컴포넌트를 동적으로 로드하여 초기 번들 크기를 줄입니다.

```jsx
import { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function App() {
  return (
    <Suspense fallback={<div>로딩 중...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### 라우트 기반 코드 분할

```jsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./pages/Contact'));

function App() {
  return (
    <Suspense fallback={<div>페이지 로딩 중...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </Suspense>
  );
}
```

## 5. 상태 구조 최적화

### 상태 정규화
중복된 데이터를 피하고 업데이트를 효율적으로 만듭니다.

```jsx
// 비효율적인 구조
const state = {
  posts: [
    { id: 1, title: 'Post 1', author: { id: 1, name: 'John' } },
    { id: 2, title: 'Post 2', author: { id: 1, name: 'John' } },
  ]
};

// 정규화된 구조
const state = {
  posts: {
    1: { id: 1, title: 'Post 1', authorId: 1 },
    2: { id: 2, title: 'Post 2', authorId: 1 },
  },
  authors: {
    1: { id: 1, name: 'John' },
  }
};
```

## 6. 프로파일링 도구

### React Developer Tools Profiler
컴포넌트 렌더링 성능을 분석합니다.

```jsx
import { Profiler } from 'react';

function App() {
  const onRender = (id, phase, actualDuration, baseDuration, startTime, commitTime) => {
    console.log(`${id} 컴포넌트 렌더링:`, {
      phase,
      actualDuration,
      baseDuration
    });
  };

  return (
    <Profiler id="App" onRender={onRender}>
      <MyComponent />
    </Profiler>
  );
}
```

## 7. 최적화 체크리스트

1. **컴포넌트 메모이제이션**: React.memo, useMemo, useCallback 적절히 사용
2. **불필요한 리렌더링 방지**: props와 state 변경 최소화
3. **코드 분할**: 큰 번들을 작은 청크로 분할
4. **이미지 최적화**: lazy loading과 적절한 포맷 사용
5. **상태 구조 최적화**: 정규화와 효율적인 데이터 구조 사용
6. **프로파일링**: 실제 성능 병목 지점 파악

성능 최적화는 실제 성능 문제가 발생했을 때 수행하세요. premature optimization은 코드를 복잡하게 만들 뿐입니다.