## Next.js v13 이후 라우팅 방식

Next.js v13 이후에는 app/ 디렉토리를 기반으로 한 파일 시스템 라우팅이 사용됩니다. 

이 방식은 기존 pages/ 디렉토리 대신 더 유연하고 직관적인 구조를 제공하며, 서버 컴포넌트와 클라이언트 컴포넌트를 혼합해 사용할 수 있습니다. 주요 특징과 사용법을 아래에 정리했습니다.

---

### **1. 기본 라우팅**

- **설명**: app/ 디렉토리 내의 page.js 파일이 각 경로에 대응됩니다.
- **v13 이전과의 차이**: pages/about.js → app/about/page.js
- **경로 예시**:
    - app/page.js → / (홈 페이지)
    - app/about/page.js → /about

### **예제 코드**

```jsx
*// app/page.js*
export default function Home() {
  return <h1>Welcome to Next.js!</h1>;
}

*// app/about/page.js*
export default function About() {
  return <h1>About Us</h1>;
}
```

---

### **2. 동적 라우팅**

- **설명**: 동적 경로는 [slug]와 같은 디렉토리명을 사용하며, params props로 동적 매개변수를 받습니다.
- **v13 이전과의 차이**: pages/post/[id].js → app/post/[id]/page.js
- **정적 경로 생성**: generateStaticParams 함수를 사용해 빌드 시 동적 경로를 미리 생성할 수 있습니다.

### **예제 코드**

```jsx
*// app/post/[id]/page.js*
export default function Post({ params }) {
  return <h1>Post ID: {params.id}</h1>;
}

*// 정적 경로 생성 (선택적)*
export async function generateStaticParams() {
  const posts = await fetch("https://api.example.com/posts").then(res => res.json());
  return posts.map(post => ({ id: post.id.toString() }));
}
```

---

### **3. 레이아웃과 중첩 라우팅**

- **설명**:
    - 전역 레이아웃은 app/layout.js에서 설정합니다.
    - 특정 경로에 중첩 레이아웃을 추가하려면 해당 경로에 layout.js를 정의합니다.
    - children props를 통해 하위 페이지나 레이아웃을 렌더링합니다.
- **v13 이전과의 차이**: _app.js와 _document.js → app/layout.js

### **예제 코드**

```jsx
*// app/layout.js (전역 레이아웃)*
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <header>Header</header>
        {children}
        <footer>Footer</footer>
      </body>
    </html>
  );
}

*// app/dashboard/layout.js (중첩 레이아웃)*
export default function DashboardLayout({ children }) {
  return (
    <div>
      <aside>Sidebar</aside>
      <main>{children}</main>
    </div>
  );
}

*// app/dashboard/page.js*
export default function Dashboard() {
  return <h1>Dashboard Page</h1>;
}
```

- **결과**: /dashboard 경로는 RootLayout과 DashboardLayout이 중첩되어 렌더링됩니다.

---

### **4. 데이터 페칭**

- **설명**:
    - 서버 컴포넌트에서 fetch를 사용해 데이터를 가져옵니다.
    - 캐싱 옵션으로 렌더링 방식을 제어합니다:
        - cache: "force-cache": 정적 렌더링(SSG).
        - cache: "no-store": 동적 렌더링(SSR).
        - next: { revalidate: 초 }: ISR(Incremental Static Regeneration).
- **v13 이전과의 차이**: getStaticProps, getServerSideProps → fetch 기반 데이터 페칭.

### **예제 코드**

```jsx
*// app/page.js (정적 렌더링)*
export default async function Page() {
  const data = await fetch("https://api.example.com/data", { cache: "force-cache" }).then(res => res.json());
  return <div>{data.message}</div>;
}

*// app/post/[id]/page.js (ISR)*
export default async function Post({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`, {
    next: { revalidate: 10 }, *// 10초 후 재생성*
  }).then(res => res.json());
  return <div>{post.title}</div>;
}
```

---

### **5. 클라이언트 컴포넌트**

- **설명**: "use client" 지시어를 추가해 클라이언트에서 실행되는 컴포넌트를 정의합니다. 서버 컴포넌트와 혼합 사용 가능.
- **v13 이전과의 차이**: 별도 지시어 없이 클라이언트 로직이 혼재 → 명확한 구분.

### **예제 코드**

```jsx
*// app/ClientComponent.js*
"use client";
import { useState } from "react";

export default function ClientComponent() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

*// app/page.js (서버 컴포넌트에서 사용)*
import ClientComponent from "./ClientComponent";

export default function Page() {
  return (
    <div>
      <h1>Server Rendered Page</h1>
      <ClientComponent />
    </div>
  );
}
```

---

### **6. 기타 기능**

- **에러 핸들링**: error.js 파일로 경로별 에러 페이지를 정의.
- **로딩 상태**: loading.js 파일로 로딩 UI 설정.
- **메타데이터**: metadata 객체로 페이지 메타데이터 설정.

### **예제 코드**

```jsx
*// app/page.js*
export const metadata = {
  title: "Home Page",
  description: "Welcome to our website",
};

export default function Page() {
  return <h1>Home</h1>;
}

*// app/error.js*
export default function Error({ error, reset }) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

---

## **정리**

Next.js v13 이후 App Router의 주요 특징은 다음과 같습니다:

- **app/ 디렉토리**: 파일 시스템 기반 라우팅.
- **page.js**: 각 경로의 페이지 정의.
- **[slug]**: 동적 라우팅, generateStaticParams로 정적 경로 생성.
- **layout.js**: 전역 및 중첩 레이아웃 설정.
- **fetch와 캐싱 옵션**: 데이터 페칭과 렌더링 제어.
- **"use client"**: 클라이언트 컴포넌트 명시.

이러한 변화로 라우팅과 데이터 페칭이 더 직관적이고 유연해졌으며, 서버와 클라이언트의 역할이 명확히 구분됩니다. 위 예제 코드를 참고해 v13 이후 방식으로 프로젝트를 구성해보세요!

---

[v13 이전 ](https://www.notion.so/v13-1bcd68f80cf6800d946feecbdcb0bc0b?pvs=21)
