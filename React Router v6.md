# React Router v6

React Router는 React 애플리케이션에서 클라이언트 사이드 라우팅을 구현하는 라이브러리입니다. v6 버전은 더욱 직관적이고 강력한 API를 제공합니다.

## 1. 기본 개념

### Router
라우팅 데이터를 제공하는 컴포넌트입니다. 보통 최상위 컴포넌트에서 감쌉니다.

```jsx
import { BrowserRouter } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      {/* 라우팅 컴포넌트들 */}
    </BrowserRouter>
  );
}
```

### Routes와 Route
URL 경로에 따라 컴포넌트를 렌더링합니다.

```jsx
import { Routes, Route } from 'react-router-dom';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/contact" element={<Contact />} />
    </Routes>
  );
}
```

## 2. 네비게이션

### Link 컴포넌트
페이지 이동을 위한 링크입니다.

```jsx
import { Link } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <Link to="/">홈</Link>
      <Link to="/about">소개</Link>
      <Link to="/contact">연락처</Link>
    </nav>
  );
}
```

### NavLink 컴포넌트
현재 활성 링크에 스타일을 적용할 수 있습니다.

```jsx
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <NavLink
        to="/"
        className={({ isActive }) => isActive ? 'active' : ''}
      >
        홈
      </NavLink>
    </nav>
  );
}
```

### 프로그래밍적 네비게이션
useNavigate 훅을 사용하여 코드에서 페이지 이동을 수행합니다.

```jsx
import { useNavigate } from 'react-router-dom';

function LoginButton() {
  const navigate = useNavigate();

  const handleLogin = () => {
    // 로그인 로직
    navigate('/dashboard');
  };

  return <button onClick={handleLogin}>로그인</button>;
}
```

## 3. 동적 라우팅

### URL 파라미터
동적인 경로를 정의하고 파라미터를 추출합니다.

```jsx
// 라우트 정의
<Route path="/users/:id" element={<UserProfile />} />

// 컴포넌트에서 파라미터 사용
import { useParams } from 'react-router-dom';

function UserProfile() {
  const { id } = useParams();
  return <div>사용자 ID: {id}</div>;
}
```

### 쿼리 파라미터
URLSearchParams 또는 useSearchParams를 사용합니다.

```jsx
import { useSearchParams } from 'react-router-dom';

function SearchResults() {
  const [searchParams] = useSearchParams();
  const query = searchParams.get('q');

  return <div>검색어: {query}</div>;
}
```

## 4. 중첩 라우팅

### Outlet 컴포넌트
부모 라우트 내에서 자식 라우트를 렌더링합니다.

```jsx
// App.js
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="about" element={<About />} />
      </Route>
    </Routes>
  );
}

// Layout.js
import { Outlet } from 'react-router-dom';

function Layout() {
  return (
    <div>
      <header>헤더</header>
      <main>
        <Outlet /> {/* 자식 라우트가 여기 렌더링됨 */}
      </main>
      <footer>푸터</footer>
    </div>
  );
}
```

## 5. 보호된 라우트

인증이 필요한 라우트를 구현합니다.

```jsx
import { Navigate } from 'react-router-dom';

function ProtectedRoute({ children }) {
  const isAuthenticated = checkAuth(); // 인증 확인 로직

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return children;
}

// 사용
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  }
/>
```

## 6. 에러 처리

존재하지 않는 경로를 처리합니다.

```jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}

function NotFound() {
  return <div>페이지를 찾을 수 없습니다.</div>;
}
```

## 7. 최신 기능

### Data Router (v6.4+)
서버 사이드 데이터 로딩을 지원합니다.

```jsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />,
    loader: async () => {
      // 데이터 로딩 로직
      return fetchData();
    },
  },
]);

function App() {
  return <RouterProvider router={router} />;
}
```

React Router v6는 더욱 강력하고 유연한 라우팅 솔루션을 제공합니다. 공식 문서를 참고하여 최신 기능을 활용하세요!