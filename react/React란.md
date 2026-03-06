# React란?

React는 사용자 인터페이스를 구축하기 위한 JavaScript 라이브러리입니다. Facebook에서 개발되었으며, 컴포넌트 기반 아키텍처를 통해 복잡한 UI를 효율적으로 관리할 수 있습니다.

## React의 핵심 개념

### 1. 컴포넌트 (Components)
React 애플리케이션은 재사용 가능한 컴포넌트들로 구성됩니다. 컴포넌트는 UI의 일부를 독립적으로 렌더링하고 관리합니다.

```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}
```

### 2. JSX (JavaScript XML)
JSX는 JavaScript에 XML을 추가한 확장 문법입니다. React에서 UI를 선언적으로 작성할 수 있게 해줍니다.

```jsx
const element = <h1>Hello, world!</h1>;
```

### 3. 가상 DOM (Virtual DOM)
React는 메모리에 가상 DOM을 유지하고, 실제 DOM과 비교하여 최소한의 변경만을 수행합니다. 이를 통해 성능을 최적화합니다.

### 4. 단방향 데이터 흐름 (One-way Data Flow)
React는 데이터가 부모 컴포넌트에서 자식 컴포넌트로 한 방향으로만 흐르는 구조를 따릅니다. 이는 데이터의 예측 가능성과 디버깅을 용이하게 합니다.

### 5. Hooks
React 16.8부터 도입된 Hooks는 함수 컴포넌트에서 상태 관리와 생명주기 기능을 사용할 수 있게 해줍니다.

```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

## React의 장점

1. **컴포넌트 재사용성**: UI를 작은 컴포넌트로 분리하여 재사용할 수 있습니다.
2. **성능 최적화**: 가상 DOM을 통해 효율적인 렌더링을 수행합니다.
3. **커뮤니티와 생태계**: 방대한 라이브러리와 도구들이 지원됩니다.
4. **크로스 플랫폼**: React Native를 통해 모바일 앱 개발도 가능합니다.

## React의 발전
React는 지속적으로 발전하고 있습니다. 최신 버전에서는 Concurrent Features, Server Components 등의 새로운 기능이 추가되었습니다. Hooks의 도입으로 함수 컴포넌트에서도 상태 관리와 생명주기 기능을 사용할 수 있게 되었습니다.

## Reconciliation (재조정)
React의 핵심 알고리즘 중 하나로, 컴포넌트의 상태 변화에 따라 UI를 효율적으로 업데이트합니다. Virtual DOM을 비교하여 실제 DOM에 최소한의 변경만을 적용합니다.
