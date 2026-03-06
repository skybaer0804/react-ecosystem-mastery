# React useEffect와 Event 처리

## 1. useEffect란?

useEffect는 리액트 함수 컴포넌트에서 사이드 이펙트(Side Effect)를 수행할 수 있게 해주는 훅입니다.

### 1.1 useEffect의 기본 구조

```jsx
useEffect(() => {
  // 사이드 이펙트 실행
  return () => {
    // 정리 함수 (cleanup function)
  };
}, [dependencies]); // 의존성 배열
```

### 1.2 의존성 배열에 따른 동작

**1. 의존성 배열이 없을 때**
```jsx
useEffect(() => {
  console.log('매번 렌더링될 때마다 실행');
});
```
- 컴포넌트가 렌더링될 때마다 실행

**2. 빈 배열일 때**
```jsx
useEffect(() => {
  console.log('마운트될 때만 실행');
}, []);
```
- 컴포넌트가 마운트될 때만 한 번 실행

**3. 의존성이 있을 때**
```jsx
useEffect(() => {
  console.log('count가 변할 때만 실행');
}, [count]);
```
- count가 변할 때마다 실행

---

## 2. useEffect 활용 예제

### 2.1 데이터 가져오기

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      });
  }, [userId]); // userId가 변할 때마다 다시 데이터 가져오기

  if (loading) return <div>로딩 중...</div>;
  return <div>{user.name}</div>;
}
```

### 2.2 이벤트 리스너 등록과 정리

```jsx
import { useState, useEffect } from 'react';

function WindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    window.addEventListener('resize', handleResize);

    // 정리 함수: 컴포넌트 언마운트 시 이벤트 리스너 제거
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []); // 마운트/언마운트 시만 실행

  return <div>가로: {size.width}, 세로: {size.height}</div>;
}
```

### 2.3 타이머 설정

```jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // 정리 함수: 타이머 제거
    return () => clearInterval(timer);
  }, []); // 마운트/언마운트 시만 실행

  return <div>경과 시간: {seconds}초</div>;
}
```

### 2.4 로컬스토리지에 데이터 저장

```jsx
import { useState, useEffect } from 'react';

function Theme() {
  const [theme, setTheme] = useState('light');

  // 마운트 시 로컬스토리지에서 가져오기
  useEffect(() => {
    const savedTheme = localStorage.getItem('theme');
    if (savedTheme) {
      setTheme(savedTheme);
    }
  }, []);

  // theme이 변할 때 로컬스토리지에 저장
  useEffect(() => {
    localStorage.setItem('theme', theme);
  }, [theme]);

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      현재 테마: {theme}
    </button>
  );
}
```

---

## 3. Event 처리

### 3.1 기본 이벤트 핸들러

```jsx
function Button() {
  const handleClick = () => {
    console.log('버튼 클릭');
  };

  return <button onClick={handleClick}>클릭</button>;
}
```

### 3.2 이벤트 객체 접근

```jsx
function Input() {
  const handleChange = (e) => {
    console.log('입력값:', e.target.value);
  };

  return <input onChange={handleChange} />;
}
```

### 3.3 여러 이벤트 처리

```jsx
function Form() {
  const handleSubmit = (e) => {
    e.preventDefault(); // 기본 동작 방지
    console.log('폼 제출');
  };

  const handleFocus = (e) => {
    console.log('포커스:', e.target.name);
  };

  const handleBlur = (e) => {
    console.log('포커스 해제:', e.target.name);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input 
        name="email"
        onFocus={handleFocus}
        onBlur={handleBlur}
      />
      <button type="submit">제출</button>
    </form>
  );
}
```

### 3.4 이벤트 위임 (Event Delegation)

```jsx
function List() {
  const handleClick = (e) => {
    if (e.target.tag === 'BUTTON') {
      console.log('클릭된 아이템:', e.target.textContent);
    }
  };

  return (
    <ul onClick={handleClick}>
      <li><button>항목 1</button></li>
      <li><button>항목 2</button></li>
      <li><button>항목 3</button></li>
    </ul>
  );
}
```

---

## 4. useEffect와 Event의 조합

### 4.1 키보드 이벤트 감지

```jsx
import { useState, useEffect } from 'react';

function SearchBox() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (query.length === 0) {
      setResults([]);
      return;
    }

    const handleSearch = () => {
      // API 호출
      console.log('검색:', query);
    };

    const debounceTimer = setTimeout(handleSearch, 500);

    return () => clearTimeout(debounceTimer);
  }, [query]);

  const handleKeyDown = (e) => {
    if (e.key === 'Enter') {
      console.log('검색 실행');
    }
  };

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        onKeyDown={handleKeyDown}
        placeholder="검색..."
      />
      <ul>
        {results.map((result, i) => (
          <li key={i}>{result}</li>
        ))}
      </ul>
    </div>
  );
}
```

### 4.2 마우스 이벤트와 useEffect

```jsx
import { useState, useEffect } from 'react';

function Movable() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);

    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []);

  return (
    <div>
      마우스 위치: X={position.x}, Y={position.y}
    </div>
  );
}
```

---

## 5. 흔한 실수와 해결 방법

### 5.1 의존성 배열 누락

**❌ 나쁜 예**
```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(count + 1); // count가 항상 0
  }, 1000);
});
// 매번 렌더링할 때마다 새로운 타이머 생성
```

**✅ 좋은 예**
```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(prev => prev + 1); // 콜백 함수 사용
  }, 1000);

  return () => clearInterval(timer);
}, []); // 마운트/언마운트 시만 실행
```

### 5.2 정리 함수 누락

**❌ 나쁜 예**
```jsx
useEffect(() => {
  window.addEventListener('resize', handleResize);
  // 정리 함수 없음 - 메모리 누수 발생
}, []);
```

**✅ 좋은 예**
```jsx
useEffect(() => {
  window.addEventListener('resize', handleResize);

  return () => {
    window.removeEventListener('resize', handleResize);
  };
}, []);
```

### 5.3 이벤트 핸들러에서의 동기화 문제

**❌ 나쁜 예**
```jsx
const handleClick = () => {
  setItems([...items, newItem]);
  // items는 이전 값
};
```

**✅ 좋은 예**
```jsx
const handleClick = () => {
  setItems(prev => [...prev, newItem]);
  // prev는 최신 값
};
```

---

## 6. 정리

| 개념 | 설명 |
|------|------|
| **useEffect** | 렌더링 후 부수 효과를 처리하는 훅 |
| **의존성 배열** | 언제 useEffect를 실행할지 결정 |
| **정리 함수** | useEffect 실행 전 이전 effect를 정리 |
| **Event Handler** | 사용자 상호작용에 응답하는 함수 |
| **e.preventDefault()** | 기본 동작 방지 |
| **e.stopPropagation()** | 이벤트 전파 방지 |

---

## 참고 자료

- [React 공식 문서 - useEffect](https://react.dev/reference/react/useEffect)
- [React 공식 문서 - 이벤트 처리](https://react.dev/learn/responding-to-events)
