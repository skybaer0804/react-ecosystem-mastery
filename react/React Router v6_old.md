<aside>
⚠️

너무 길어서 가독성을 해치는 코드문서를 줄였습니다. 오래된 응용 컴포넌트는 삭제하고 개념만 남겼습니다.

</aside>

## 1. 리액트 라우터 v6 ?

### 1.1 리액트 라우터란?

리엑트 **컴포넌트로 페이지를 나누고 이동하게 해주는 라이브러리입니다.**

- Router
    
    리엑트 라우터에서 사용하는 데이터를 모두 갖고있는 컴포넌트입니다. 현재 주소, 페이지 기록 데이터를 갖고 있습니다. Context.Provider 개념으로 데이터를 내려주고 보통 최상위 컴포넌트에서 감싸주는 형태로 사용합니다. (프로젝트 전체에서 라우터를 사용하기 위함)
    
    ```jsx
    import {BroswserRouter} from 'react-router-dom';
    
    function Main() {
    	return <BrowserRouter> ... < /BrowserRouter>;
    }
    
    export default Main;
    ```
    
- Routes : witch 개념입니다.
- Route : url 경로와 맞는 컴포넌트를 보여줍니다.
    
    ```jsx
    <Routes>
    	<Route path="/" element={<Hompage />} />
    	<Route path="/courses" element={<CourseListPage />} />
    	<Route path="/courses/1" element={<CoursePage />} />
    	<Route path="*" element={<NotFoundPage />} />
    </Routes>
    ```
    
- Link  - Route 안에서 a 태그 대신 사용합니다.
    
    ```jsx
    <Link to="/">홈페이지</Link>
    <Link to="/courses">수업 탐색</Link>
    ```
    

### 1.2 v5 와 달라진점

- <Switch /> ⇒ <Routes /> 네이밍 변경됨
- <Route /> 에서 컴포넌트 렌더링 속성명이 component  ⇒ element로 네이밍 변경됨
- <Redirect /> ⇒ <Navigate /> 네이밍 변경됨

https://velog.io/@poseassome/Router-%EC%A0%91%EA%B7%BC-%EC%A0%9C%ED%95%9C-%EA%B5%AC%ED%98%84

---

## 2. 페이지 이동과 Link 이동

### 2.1 페이지 이동

- <BrouserRouter>로 감싸고 안에 <Routes> - <Route> 태그로 페이지 이동을 할 수 있습니다.
- swich문과 비슷해서 주소 경로로 들어오게 되면 path를 하나씩 비교하고 맞는 Route의 element 프롭 안에 있는 컴포넌트 페이지로 이동하게 된다.
- 프레그먼트 태그와 비슷하게 흔적은 남지 않습니다.
- path 값에 "/"를 붙이면 절대 경로를 뜻합니다. 안붙이면 현재 주소 뒤에 path경로가 붙습니다
- element 안에는 컴포넌트가 들어갑니다.

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
...
function Main() {
  return (
    <BrowserRouter>
      <App>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/courses" element={<CourseListPage />} />
          <Route
            path="/courses/react-front-development"
            element={<CoursePage />}
          />
          <Route path="/wishlist" element={<WishlistPage />} />
        </Routes>
      </App>
    </BrowserRouter>
  );
}

export default Main;
```

### 2.2 NavLink로 네비게이션 구분

- Link 와 동일하나 한가지 특징은 style 프롭을 넣을 수 있다.
- 해당 링크에 들어가 있을경우 표시해줍니다.

```jsx
import { Link, NavLink } from "react-router-dom";    // NavLink 추가
...
import styles from "./Nav.module.css";

function getLinkStyle({ isActive }) {  // 파라미터로 객체를 받는데  isActive 라는 불린형이 있는데, 현재페이지의 경로가 네비게이션의 링크에 해당하면 이게 참이됨.
  return {
    textDecoration: isActive ? "underline" : undefined, // 인라인 스타일 객체를 리턴
  };
}

function Nav() {
  return (
    <div className={styles.nav}>
      <Container className={styles.container}>
        <Link to={"/"}>
          <img src={logoImg} alt="Codethat Logo" />
        </Link>
        <ul className={styles.menu}>
          <li>
            <NavLink to={"/courses"} style={getLinkStyle}>   //NavLink로 바꾸고 getLinkStyle 함수를 넣어줌
              카탈로그
            </NavLink>
          </li>
        </ul>
      </Container>
    </div>
  );
}

export default Nav;
```

### 2.3 useNavigate 로 페이지 이동하기 : 코드로 이동

- 코드를 통해 페이지 이동이 가능한 훅입니다.

```jsx
...
import { Navigate, useNavigate, useParams } from "react-router-dom";

function CoursePage() {
  const { courseSlug } = useParams();
  **const navigate = useNavigate();** // useNavigate
  const course = getCourseBySlug(courseSlug);

  if (!course) {
    return <Navigate to="/courses" />;
  }
  
  const handleAddWishlistClick = () => {
    addWishlist(course?.slug);
    **navigate("/wishlist"); // 코드실행, 변수로 경로 전달**
  };
	...
}

export default CoursePage;
```

---

## 3. 경로 구분, 상세 페이지, 쿼리 검색

### 3.1 하위 페이지 나누기

path가 길어지고 복잡해지면 하나의 Route로 하기에는 어려움이 있습니다. 그래서 Route를 중첩해서 하위 Route를 사용할 수 있습니다.

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
...
function Main() {
  return (
    <BrowserRouter>
      <App>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="courses">  // 이렇게 공통 path를 밖에서 Route로 묶어줌. 중첩
            <Route index element={<CourseListPage />} />  // index는 부모의 path를 의미
            <Route
              path="/react-front-development" // path에 있던 courses/... 를 제거
              element={<CoursePage />}
            />
          </Route>
          <Route path="/wishlist" element={<WishlistPage />} />
        </Routes>
      </App>
    </BrowserRouter>
  );
}

export default Main;
```

- 장점은 Route가 많아졌을때 경로나, 용도에 따라 분류 가능. path 단순하고, 부모 Route의 path를 추후 별도로 바꿀 수도 있습니다.

### 3.2 Outlet 컴포넌트.

- 공통적인 레이아웃을 보여주고 싶을때 사용하면 좋습니다.
- 먼저, Routes 안에는 Route 컴포넌트나 프레그먼트만 들어갈 수 있습니다.
- Routes 안에 Route 특정 경로에 element로 내려줍니다.

Main.js

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
...
function Main() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<App />}>  // App 컴포넌트를 element로 내려줌
          <Route index element={<HomePage />} />
					...
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default Main;
```

App.js

```jsx
import { Outlet } from 'react-router-dom';
...
function App() {  // childern  프롭 삭제
  return (
    <>
      <Nav className={styles.nav} /> //레이아웃
      <div className={styles.body}>
	      <**Outlet** />
      </div> // Outlet 컴포넌트 추가
      <Footer className={styles.footer} /> //레이아웃
    </>
  );
}

export default App;
```

- 이렇게 Outlet을 활용하면, Routes에서 렌더링되는 부분은 이 Outlet 부분에서 렌더링 된다.

### 3.3 동적인 경로 만들기 : 상세페이지

특정 상세 페이지는 수십가지 이상이 되고, 또 추가 될수 있습니다. 이때 Route 에서 제공하는 커스텀 훅인 useParam을 사용합니다.

먼저, Route에 동적인 경로를 주려면, 파라미터로 전달 합니다. Route에서 파라미터는 ": 변수이름"  식으로 작성합니다.

<aside>
💡 <Route path=":courseSlug" element={<CoursePage />} />

</aside>

Main.js

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
...
function Main() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<App />}>
          <Route index element={<HomePage />} />
          <Route path="courses">
            <Route index element={<CourseListPage />} />
            **<Route path=":courseSlug" element={<CoursePage />} />   // 요렇게**
          </Route>
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default Main;
```

- **useParams**는 내장 커스텀 훅으로 **리턴하는 객체에는 현재 경로의 파라미터들이 저장**되어 있습니다.

CoursePage.js

```jsx
...
import { useParams } from "react-router-dom";

function CoursePage() {
  **const { courseSlug } = useParams();  // 디스럭쳐링으로 courseSlug만 선언
  ...**
}
export default CoursePage;
```

### 3.4 없는 페이지 처리와 리다이렉트

- Routes 맨 마지막줄에 처리합니다. 하나하나 읽다가 없는 url들(*)은 리다이렉트 처리하거나 NotFoundPage 컴포넌트를 보여줍니다.

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
...

function Main() {
  return (
    <BrowserRouter>
      <Routes>
        ...
          <Route path="*" element={<NotFoundPage />} /> // 요기, Routes 맨 마지막에 path * 로 내려줌, 모든경로 
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
export default Main;
```

### 3.5 쿼리 검색 useSearchParams

리액트 라우터의 커스텀 훅으로 이 훅을 통해서 "쿼리 파라미터"를 가져올 수 있습니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdcecae8-b1e3-4e5a-8725-f6f5a30353e0/Untitled.png)

```jsx
...
import { useSearchParams } from "react-router-dom";

function CourseListPage() {
  const [searchParams, setSearchParams] = useSearchParams(); // useSearchParams
  console.log(searchParams.get('keyword'));
	...
}

export default CourseListPage;
```

---

## **4. Link, Navigate, useNavigate는 언제 쓰는게 좋을까?**

### **4.1 Link**

**사용자가 클릭해서 페이지를 이동하도록 할 때** 사용하면 됩니다. 하이퍼링크 텍스트나 페이지를 이동하는 버튼, 이미지 등에 사용하면 좋습니다. 대부분의 경우 `Link` 만으로도 충분합니다.

### **4.2 Navigate**

특정 경로에서 **렌더링 시점에 다른 페이지로 이동시키고 싶을 때** 사용하면 됩니다.

**예시:**

- 쇼핑몰의 회원 전용 페이지에 로그인없이 들어와서 로그인 페이지로 리다이렉트하는 경우
- 쇼핑몰의 상품 상세 페이지에서 제품이 품절되었거나 삭제되어서 다른 페이지로 이동시키는 경우

### **4.3 useNavigate**

**특정한 코드의 실행이 끝나고 나서 페이지를 이동시키고 싶을 때** 사용하면 됩니다.

**예시:**

- 쇼핑몰에서 장바구니에 담기를 눌렀을 때 리퀘스트를 보내고 장바구니 페이지로 이동시키는 경우
- 쇼핑몰에서 결제하기 버튼을 누르고 나서 모든 결제가 완료된 후에 페이지를 이동시키는 경우
- 리다이렉트된 로그인 페이지에서 로그인을 완료한 후에 처음 진입했던 페이지로 돌아가는 경우

---

## 5. SPA, 리액트를 렌더링하는 여러 방식

### 5.1 싱글 페이지 애플리케이션(SPA)

왜 웹브라우저의 기본기능이 있는데, <a> 태그가 아닌 <Link> 태그를 사용했을까요? 바로 싱글 페이지 애플리케이션 이기 때문입니다. 

localhost:3000을 검색해서 들어갔을때, /course로 들어갔을때 request 받는 것들 중 localhost를 살펴보면 똑같은 html 입니다.

그럼, 왜 여러 페이지가 있는것처럼 보이는 건 리엑트가 라우터가 경로를 읽어서 일치하는 컴포넌트를 렌더링하기 때문입니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8b99e35-bef5-41eb-9ec0-be1a0e6da845/Untitled.png)

### 5.2 리액트를 렌더링하는 여러방식

클라이언트 사이드 렌더링이란 웹 브라우저에서 자바스크립트로 HTML을 만들고, 이걸로 화면을 보여주는 거였는데요. 이걸 꼭 웹 브라우저에서 할 필요는 없습니다.

리액트 팀에서는 렌더링을 자유롭게 할 수 있도록 리액트를 개발했는데요. 리퀘스트를 받으면 서버가 렌더링 해서 리스폰스로 보내줄 수도 있고, 아예 미리 HTML 파일로 만들어 두었다가 리스폰스로 보내줄 수도 있습니다.

심지어는 리액트 코드를 HTML이 아니라 모바일 애플리케이션의 화면을 렌더링 하는 데 사용할 수도 있습니다.

### **5.3 대표적인 렌더링의 종류**

**클라이언트사이드 렌더링(Client-side Rendering)**

— 웹 브라우저에서 자바스크립트로 HTML을 만드는 것

리액트로 할 수 있는 가장 기본적인 방식의 렌더링입니다. 리액트로 작성한 코드는 자바스크립트로 변환이 가능한데요. 참고로 이런 변환을 트랜스파일링이라고 부릅니다.

클라이언트사이드 렌더링은 **자바스크립트로 변환된 리액트 코드를 웹 브라우저에서 실행해서 HTML을 만드는** 걸 말합니다.

**서버사이드 렌더링(Server-side Rendering)**

— 서버에서 HTML을 만들고 리스폰스로 보내주는 것

백엔드 서버에서 리퀘스트를 받으면 상황에 맞는 HTML을 만들어서 리스폰스로 보내주는 방식을 '서버사이드 렌더링'이라고 합니다. 서버에서 HTML을 만든다는 뜻이죠.

이렇게 하면 이미 렌더링 된 것이 웹 브라우저에 도착하니까 훨씬 빨리 화면을 띄워줄 수 있고,검색 엔진에서 좋은 점수를 받아서 검색했을 때 사이트가 잘 노출될 수 있다는 장점이 있습니다.

**정적 사이트 생성(Static Site Generation)**

— 미리 HTML 파일을 만들어서 서버를 배포하는 것

서버에서 렌더링 하는 것도 좋지만, 데이터가 거의 바뀌지 않는다면 매번 새로 만드는 건 낭비입니다. 그래서 미리 HTML 파일로 만들고 이걸 서버를 배포하는 방법을 사용하는데, 이런 방식을 '정적 사이트 생성'이라고 합니다. 서버에서는 리퀘스트가 들어오면 HTML 파일을 읽어서 리스폰스로 보내주는 건데요.

'정적 사이트 생성'에서 정적이라는 말의 의미는 HTML을 파일로 만든다는 겁니다. 개발자가 새로 배포하지 않는다면 서버에서 보내주는 HTML이 달라지지 않는다는 의미죠. 용어가 생소해 보이지만, 쉽게 생각해서 리액트 코드로 HTML 파일을 만든다고 생각하면 됩니다.

물론 자바스크립트를 쓸 수 있기 때문에 정적으로 생성된 사이트에서도 동적인 데이터를 가져와 페이지를 보여줄 수 있습니다.
