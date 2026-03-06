# React useEffect와 이벤트 처리

React에서 사이드 이펙트를 관리하고 이벤트를 처리하는 방법을 알아봅니다.

## 1. useEffect 기본

useEffect는 컴포넌트의 생명주기 동안 사이드 이펙트를 수행합니다.

### 기본 구조

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // 사이드 이펙트 실행
  console.log('컴포넌트가 마운트되거나 업데이트됨');

  return () => {
    // 정리 함수 (cleanup)
    console.log('컴포넌트가 언마운트되거나 업데이트되기 전');
  };
}, [dependencies]); // 의존성 배열
```

### 의존성 배열에 따른 동작

```jsx
// 1. 의존성 배열이 없는 경우 - 매 렌더링마다 실행
useEffect(() => {
  console.log('매번 실행');
});

// 2. 빈 배열 - 마운트 시 한 번만 실행
useEffect(() => {
  console.log('마운트 시만 실행');
}, []);

// 3. 의존성 있는 경우 - 의존성이 변경될 때 실행
useEffect(() => {
  console.log('count가 변경될 때 실행');
}, [count]);
```

## 2. 일반적인 사용 사례

### 데이터 페칭

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUser = async () => {
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        setUser(data);
      } catch (error) {
        console.error('사용자 데이터 로딩 실패:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // userId가 변경될 때마다 재실행

  if (loading) return <div>로딩 중...</div>;
  return <div>{user?.name}</div>;
}
```

### 이벤트 리스너 등록

```jsx
import { useEffect } from 'react';

function WindowSizeTracker() {
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
  }, []); // 빈 배열: 마운트/언마운트 시만 실행

  return (
    <div>
      창 크기: {size.width} x {size.height}
    </div>
  );
}
```

### 타이머 관리

```jsx
import { useEffect, useState } from 'react';

function CountdownTimer({ initialTime }) {
  const [time, setTime] = useState(initialTime);

  useEffect(() => {
    if (time <= 0) return;

    const timer = setInterval(() => {
      setTime(prev => prev - 1);
    }, 1000);

    return () => clearInterval(timer); // 정리 함수
  }, [time]); // time이 변경될 때마다 재설정

  return <div>남은 시간: {time}초</div>;
}
```

## 3. useEffectEvent (React 18.3+)

useEffectEvent는 useEffect의 의존성 문제를 해결하기 위한 실험적 훅입니다.

### 문제점: 불필요한 의존성

```jsx
function ChatRoom({ roomId, theme }) {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const connection = createConnection(roomId, theme); // theme도 의존성에 포함됨
    connection.connect();

    return () => connection.disconnect();
  }, [roomId, theme]); // theme이 변경될 때마다 재연결됨 (원하지 않는 동작)

  // ...
}
```

### 해결: useEffectEvent

```jsx
import { useEffect, useEffectEvent } from 'react';

function ChatRoom({ roomId, theme }) {
  const [messages, setMessages] = useState([]);

  const onConnected = useEffectEvent((connectedRoomId) => {
    showNotification('Connected to ' + connectedRoomId, theme);
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on('connected', () => onConnected(roomId));

    connection.connect();
    return () => connection.disconnect();
  }, [roomId]); // roomId만 의존성으로

  // ...
}
```

## 4. 커스텀 이벤트 훅

재사용 가능한 이벤트 처리 로직을 만듭니다.

### useEventListener 훅

```jsx
import { useEffect } from 'react';

function useEventListener(eventName, handler, element = window) {
  useEffect(() => {
    const eventListener = (event) => handler(event);

    element.addEventListener(eventName, eventListener);

    return () => {
      element.removeEventListener(eventName, eventListener);
    };
  }, [eventName, handler, element]);
}

// 사용
function MyComponent() {
  const [key, setKey] = useState('');

  useEventListener('keydown', (event) => {
    setKey(event.key);
  });

  return <div>마지막으로 누른 키: {key}</div>;
}
```

### useLocalStorage 훅

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// 사용
function App() {
  const [name, setName] = useLocalStorage('name', 'John');

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
    </div>
  );
}
```

## 5. 이벤트 처리 모범 사례

### 이벤트 핸들러 최적화

```jsx
import { useCallback } from 'react';

function TodoList({ todos, onToggle }) {
  // useCallback으로 이벤트 핸들러 메모이제이션
  const handleToggle = useCallback((id) => {
    onToggle(id);
  }, [onToggle]);

  return (
    <ul>
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={handleToggle}
        />
      ))}
    </ul>
  );
}
```

### 폼 이벤트 처리

```jsx
import { useState } from 'react';

function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('제출된 데이터:', formData);
    // 폼 제출 로직
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="이름"
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="이메일"
      />
      <textarea
        name="message"
        value={formData.message}
        onChange={handleChange}
        placeholder="메시지"
      />
      <button type="submit">제출</button>
    </form>
  );
}
```

## 6. 주의사항

1. **무한 루프 방지**: useEffect 내에서 상태를 업데이트할 때는 의존성을 올바르게 설정
2. **메모리 누수 방지**: 정리 함수를 사용하여 이벤트 리스너, 타이머 등 정리
3. **불필요한 실행 방지**: 의존성 배열을 정확히 지정
4. **useEffectEvent**: 아직 실험적 기능이므로 프로덕션에서 사용 시 주의

useEffect를 효과적으로 사용하면 React 애플리케이션의 사용자 경험을 크게 향상시킬 수 있습니다.