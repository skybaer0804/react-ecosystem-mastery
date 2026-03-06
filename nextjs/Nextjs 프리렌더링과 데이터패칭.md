## 1. 프리렌더링 개요

<aside>
💡 프리렌더링은 웹페이지 렌더링 전에 이루어지는 렌더링을 말합니다.

</aside>

- 서버로부터 HTML 코드를 받아오기 이전에 프리렌더링이 진행됩니다.
- 빈 화면이 아니라 어떤 화면이 보여집니다.

### **프리렌더링 방식 : SSG, SSR**

**정적생성(SSG)**

- 빌드 시점에서 생성됩니다.(단, 개발자 모드에서는 항상 생성됩니다.)
- 빌드 될때 html 파일을 생성 하고 요청이 있을때 파일을 바로 보내줍니다.
- 그 후 js 코드를 로딩해서 연결한(**Hydration**) 후 실행하면 화면에 보여집니다.
- 정적이므로 변하지 않는 페이지를 SSG를 하면 좋습니다.
- 데이터가 업데이트 됬다면 다시 빌드해야하므로 동적 콘텐츠에는 적합하지 않습니다.
- 기본적으로 Next.js에서는 모든 페이지를 정적 생성합니다.

**서버사이드 렌더링(SSR)**

- **매 요청이 서버로 들어올때마다 렌더링**해서 보내줍니다.
    - cash-controller 헤더가 구성된 경우에만 캐싱이 됩니다. 아닌 경우에는 계속 새로운 데이터를 가져오게 됩니다.
- 동적 콘텐츠 페이지에 적합합니다.
- 그러나 만약 데이터 요청 중에 데이터를 렌더링할 필요가 없는 경우, 클라이언트에서 useEffect를 사용해 데이터를 가져오거나 **getStaticProps**를 사용하는걸 고려해보는게 좋습니다.

**장점**

- 초기 로딩이 빠릅니다.(CSR 보다 빠름)
- 검색 엔진 최적화됩니다.
    
    로봇 프로그램들이 텅빈 html이 아니라 렌더링된 html을 볼 수 있기에 사이트를 파악할 수 있습니다.
    

## **2. Next.js v13 이후 프리렌더링 방식**

Next.js v13부터는 **App Router**가 도입되며, React Server Components(RSC)를 기본으로 사용해 프리렌더링이 간소화되었습니다. 주요 특징과 사용법은 다음과 같습니다.

### **1. App Router의 기본 구조**

- **라우팅**: app/ 디렉토리 기반으로 파일 시스템을 통해 정의됩니다.
- **프리렌더링**: getStaticProps나 getServerSideProps 같은 별도 함수 없이, 페이지 컴포넌트 내에서 fetch로 데이터를 가져옵니다.
- **캐싱 옵션**:
    - **cache: "force-cache"**: 정적 렌더링(SSG와 유사).
    - **cache: "no-store"**: 동적 렌더링(SSR와 유사).

### **예제 코드**

```jsx
*// app/page.js*
export default async function Page() {
  const data = await fetch("https://api.example.com/data", { cache: "force-cache" }).then(res => res.json());
  return <div>{data.message}</div>;

```

---

### **2. 동적 라우팅**

- **동적 경로**: app/post/[id]/page.js처럼 파일 구조로 정의합니다.
- **정적 경로 생성**: generateStaticParams 함수를 사용해 빌드 시 동적 경로를 미리 생성합니다.
- **데이터 페칭**: 페이지 컴포넌트 내에서 fetch로 데이터를 직접 가져옵니다.

### **예제 코드**

```jsx
*// app/post/[id]/page.js*
export async function generateStaticParams() {
  const posts = await fetch("https://api.example.com/posts").then(res => res.json());
  return posts.map(post => ({ id: post.id.toString() }));
}

export default async function Post({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`).then(res => res.json());
  return <div>{post.title}</div>;
}
```

---

### **3. ISR (Incremental Static Regeneration)**

- **설정**: fetch에 next: { revalidate: 초 } 옵션을 추가해 ISR을 구현합니다.
- **동작**: 지정된 시간 후 페이지를 자동으로 재생성합니다.

### **예제 코드**

```jsx
*// app/post/[id]/page.js*
export default async function Post({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`, {
    next: { revalidate: 10 }, *// 10초 후 재생성*
  }).then(res => res.json());
  return <div>{post.title}</div>;
}
```

---

### **4. 서버 컴포넌트와 클라이언트 컴포넌트**

- **서버 컴포넌트 (RSC)**: 기본적으로 서버에서 렌더링됩니다.
- **클라이언트 컴포넌트**: "use client" 지시어로 명시하며, 클라이언트에서 실행됩니다.

### **예제 코드**

```jsx
*// app/page.js (Server Component)*
export default async function Page() {
  const data = await fetch("https://api.example.com/data").then(res => res.json());
  return (
    <div>
      <p>Server Data: {data.message}</p>
      <ClientComponent />
    </div>
  );
}

*// app/ClientComponent.js (Client Component)*
"use client";
import { useState } from "react";

export default function ClientComponent() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

---

### **정리**

Next.js v13 이후 App Router는 프리렌더링을 더 직관적이고 유연하게 만듭니다:

- **fetch와 캐싱 옵션**으로 정적/동적 렌더링을 쉽게 제어.
- *generateStaticParams**로 동적 경로를 사전 생성.
- *next: { revalidate }**로 ISR을 간단히 설정.
- **서버/클라이언트 컴포넌트** 구분으로 성능을 최적화.

---

[v13 이전](https://www.notion.so/v13-1bcd68f80cf6808f8f81c2b661afa60a?pvs=21)
