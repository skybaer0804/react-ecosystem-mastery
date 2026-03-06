# React 데이터 관리

React에서 데이터를 효과적으로 관리하는 방법을 알아봅니다. 상태 관리, 데이터 페칭, 최적화 기법을 다룹니다.

## 1. 상태 관리 기본

### useState 훅
컴포넌트 내 지역 상태를 관리합니다.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>카운트: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  );
}
```

### useReducer 훅
복잡한 상태 로직을 관리할 때 유용합니다.

```jsx
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>카운트: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>
        증가
      </button>
      <button onClick={() => dispatch({ type: 'decrement' })}>
        감소
      </button>
    </div>
  );
}
```

## 2. 데이터 페칭

### useEffect를 사용한 데이터 로딩
컴포넌트 마운트 시 데이터를 가져옵니다.

```jsx
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    try {
      const response = await fetch('/api/users');
      if (!response.ok) throw new Error('데이터 로딩 실패');
      const data = await response.json();
      setUsers(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <div>로딩 중...</div>;
  if (error) return <div>에러: {error}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 커스텀 훅으로 데이터 로딩 추상화
재사용 가능한 데이터 로딩 로직을 만듭니다.

```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetchData();
  }, [url]);

  const fetchData = async () => {
    try {
      setLoading(true);
      const response = await fetch(url);
      if (!response.ok) throw new Error('데이터 로딩 실패');
      const result = await response.json();
      setData(result);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  return { data, loading, error, refetch: fetchData };
}

// 사용
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <div>로딩 중...</div>;
  if (error) return <div>에러: {error}</div>;

  return <div>{user?.name}</div>;
}
```

## 3. 데이터 캐싱과 동기화

### React Query (TanStack Query)
서버 상태 관리를 위한 강력한 라이브러리입니다.

```bash
npm install @tanstack/react-query
```

```jsx
import { QueryClient, QueryClientProvider, useQuery } from '@tanstack/react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <UserList />
    </QueryClientProvider>
  );
}

function UserList() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(res => res.json()),
  });

  if (isLoading) return <div>로딩 중...</div>;
  if (error) return <div>에러 발생</div>;

  return (
    <ul>
      {data.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

## 4. 폼 데이터 관리

### 제어되는 컴포넌트
React 상태로 폼을 제어합니다.

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

### React Hook Form
복잡한 폼을 효율적으로 관리합니다.

```bash
npm install react-hook-form
```

```jsx
import { useForm } from 'react-hook-form';

function ContactForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => {
    console.log(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('name', { required: '이름은 필수입니다' })}
        placeholder="이름"
      />
      {errors.name && <span>{errors.name.message}</span>}

      <input
        {...register('email', {
          required: '이메일은 필수입니다',
          pattern: {
            value: /^\S+@\S+$/i,
            message: '유효한 이메일 주소를 입력하세요'
          }
        })}
        placeholder="이메일"
      />
      {errors.email && <span>{errors.email.message}</span>}

      <button type="submit">제출</button>
    </form>
  );
}
```

## 5. 상태 관리 라이브러리

### Zustand
간단하고 강력한 상태 관리 라이브러리입니다.

```bash
npm install zustand
```

```jsx
import { create } from 'zustand';

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
}));

function Counter() {
  const { count, increment, decrement } = useStore();

  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
}
```

### Redux Toolkit
Redux의 현대적인 접근 방식입니다.

```bash
npm install @reduxjs/toolkit react-redux
```

```jsx
import { configureStore, createSlice } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1 },
    decrement: (state) => { state.value -= 1 },
  },
});

const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(counterSlice.actions.increment)}>
        +
      </button>
      <button onClick={() => dispatch(counterSlice.actions.decrement)}>
        -
      </button>
    </div>
  );
}

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

React에서 데이터를 효과적으로 관리하기 위해 적절한 도구를 선택하세요. 간단한 앱에서는 useState로 충분하지만, 복잡한 앱에서는 React Query나 Zustand 같은 라이브러리를 고려해보세요.