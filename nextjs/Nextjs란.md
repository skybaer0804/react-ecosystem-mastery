<aside>
💡

Next.js는 React 프레임워크입니다. 흐름제어가 사용자가 아닌 Next.js가 갖습니다.

</aside>

## 1. Next.js 키워드

- **SEO**(검색 엔진 최적화)
- **SSR**(서버 사이드 렌더링) : 페이지를 서버에서 렌더링 하여 초기 로딩 속도 향상
- **SSG**(정적 사이트 생성) : 빌드 시점에 페이지를 사전 렌더링하여 성능을 향상 시킴.

## 2. Next.js 특징

### **사전 렌더링(Pre-Rendering)**

- 미리 사전 페이지를 만들어놓아서, 클라이언트에서 서버에 요청했을때, 미리 만든 HTML을 바로 줍니다.
- 렌더링 속도가 매우 빨라짐. SEO에도 큰 도움이 됩니다.

### **자동 코드 분할(Code-Splitting)**

- 각 페이지의 코드 조각을 작은 청크로 프로그램의 번들을 분할하는 과정. 페이지를 실행하는데 꼭 필요한 파일만 가져오도록 초기 로딩 시간을 단축시킵니다.
- 코드를 레이지로딩(지연로딩) 시킬 수 있기에 사용자에게 훨씬 향상된 성능을 제공합니다.
- 즉, 불필요한 코드가 페이지에 로드되지 않습니다.

### **API Routes**

- JS 만으로 API 를 만들 수 있습니다. 백엔드 없이도 데이터 통신할 수 있는 앱 제작이 가능합니다.

### **자동 최적화(Built - in Optimization)**

- 이미지, 폰트, 스크립트 등 여러 기능을 최적화해줍니다.
- 즉, 따로 설정안해도 지연 로딩, 성능 최적화를 해줍니다.
- 개발 생산성 향상되고 더 나은 유저 경험을 제공할 수 있습니다.

### **미들웨어(Middleware) 내장**

- 특정 요청을 하기전에 어떤 행동을 하고 싶을때 미들웨어로 손쉽게 제어할 수 있습니다.
- 예를 들면 특정 페이지는 로그인된 사용자만 접근 가능하다면 미들웨어 인증이 필요한 페이지 경로만 간단히 정해주면 됩니다.

### **Routing & Layout**

- 편리한 Routing, Layout 기능이 있어 별도 라이브러리를 설치하고 설정하지 않아도 됩니다.
- **파일 시스템을 기반으로 Routing**을 합니다. 즉, 폴더경로에 따라 페이지 경로가 결정됩니다.
- Layout 파일만 설정해주면 전체 적용됩니다. 즉, 각 페이지 마다 공통된 레이아웃을 따로 설정하지 않아도 됩니다.

## 3. Next.js 장점과 단점

### **장점**

- **SSR, SEO 지원**
    
    Next.js는 SSR과 SEO를 쉽게 구현할 수 있어 **초기 로딩 속도**와 **검색 엔진 최적화(SEO)**를 개선 할 수 있습니다.
    
- **SEO 최적화**
    
    SSR로 검색엔진은 페이지를 쉽게 색인화할 수 있으며, 메타태그 및 제목 태그 관리 용이합니다.
    
- **개발 생산성 향상**
    
    간단한 프로젝트 구조와 라우팅 설정, 자동 코드 분할 등의 기능으로 개발 생산성 향상됩니다.
    
- **리액트 호환성**
    
    React 기반으로 작동되며, React 내장 컴포넌트와 라이브러리를 쉽게 통합할 수 있습니다.
    

### **단점**

- **학습 곡선**
    
    Next.js는 초기에는 복잡할 수 있고, 기존 React 개발자는 새로운 개념을 익혀야 합니다.
    
- **서버 자원 소모**
    
    SSR은 매 요청마다 서버에서 페이지를 렌더링하므로 서버 자원을 많이 소모할 수 있습니다.
    
- **그 외**
    
    느린 네트워크에 영향이 있을 수 있음, 복잡한 애플리케이션 구현시 복잡도 커짐
    

### CSR vs SSR vs SSG 비교

|  | CSR | SSR(서버사이드렌더링) | SSG(정적사이트생성)  |
| --- | --- | --- | --- |
| 장점 | - 서버 부하 감소 : 서버는 초기 빈 HTML만 제공하고 이후에는 클라이언트에서 데이터를 로드함. 초기 로딩 이후 구동 속도가 빠름
- 상호 작용성 : 초기로딩은 느려도 이후 클라이언트에서 페이지를 렌더링 하므로 사용자와의 상호작용이 빠르게 이루어짐 | - 초기 로딩속도가 빠르고 CSR보다 사용자 경험이 좋음.
- SEO가 쉬움 | - **빌드시점**에서 파일을 생성하고 직접 제공하기에 SSR보다 초기 로딩속도가 더 빠름.
- Next.js는 기본적으로 SSG.
- 서버부하가 낮음.
- SEO 쉬움
-ISR 기법을 잘 사용하면 특정시간(초) 이후마다 업데이트할 수 있음. |
| 단점 | - 초기 구동 속도가 느림 : 로딩 후 콘텐츠 표시까지 시간이 걸리는 문제, 성능문제, JS 의존하는 문제..등
- 데이터가 복잡하고, 양이 클 경우 성능에 문제가 크게됨. 특히 느린 네트워크일 경우 이 문제가 부각됨.
- SEO가 어려움 : 초기 빈 HTML로 콘텐츠가 없으므로 검색 엔진 최적화를 구현하기 어려움. 자바스크립트를 로드하고 콘텐츠가 생성됨. | - 매번 요청을 보내기 때문에 서버가 과부화 될 수 있음.
- CSR보다 많지 않으며, api 호출 최적화
- 필요한 페이지만 SSR 적용. | - 정적 데이터를 사용하므로 동적 콘텐츠에는 제한됨
- 업데이트된 데이터에 대한 재빌드 필요 |
| 서비스 예시 | SPA, 대시보드 및 관리 패털, 소셜 미디어 플랫폼, 라이브 스트리밍 및 실시간 업데이트 서비스 | 주로 동적 콘텐츠를 포함하는 뉴스 웹사이트, 블로그, 전자상거래 플랫폼 | - 주로 정적인 블로그, 포트폴리오 웹사이트, 회사 홈페이지, FAQ |

## 3. Next build

![image.png](attachment:c2a66b2f-a6ec-4d40-8ebb-b56342f87d66:image.png)

Next.js의 빌드 프로세스에서 여러 최적화 기법들이 적용되어 First Load JS 크기가 줄어듭니다.

### **Next.js가 수행하는 주요 최적화 기법들**

**코드 분할 (Code Splitting)**

- 페이지별로 필요한 코드만 로드
- 불필요한 코드는 별도 청크로 분리

**트리 쉐이킹 (Tree Shaking)**

- 사용하지 않는 코드 제거
- 번들 크기 감소

**자동 청크 최적화**

- 공통 모듈을 별도 청크로 분리
- 이미지에서 보이는 chunks/framework와 chunks/main이 이에 해당

**이미지 최적화**

- 이미지 압축
- 적절한 포맷 변환

**미니파이케이션 (Minification)**

- 공백, 주석 제거
- 변수명 축소

### **최적화 이점**

- 초기 로딩 시간 단축
- 네트워크 대역폭 절약
- 전반적인 성능 향상

이미지를 보면 Static 콘텐츠와 Dynamic 콘텐츠가 구분되어 있어, 서버 사이드 렌더링과 정적 생성을 통한 최적화도 이루어지고 있음을 알 수 있습니다.

## 3. Next.js app 맛보기

```jsx
npx create-next-app .
```

package.json

```jsx
"scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
```

### 디렉토리

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a9a4786b-322e-4eda-85ff-d941ae073b9c/5c14aebc-7c7d-480c-837d-5cb03edcc089/Untitled.png)

- pages 폴더는 Next.js에서 명시된 폴더명으로 이 폴더 안에 만든 js 파일이 곧 페이지입니다.
- 파일기반 라우팅입니다.
- import 할때 @를 사용합니다.(scr디렉토리 기준 파일경로 입력)
- 전역 속성을 설정할 수 있는 _app.js, _document.js이 있습니다.
- 다이나믹 라우츠
    - products 폴더내에 대괄호 [id].js 로 파일을 생성하면 변수를 받을 수 있습니다.
    - [id]를 param 이라고 부르며, 폴더명도 대괄호를 사용해 파람으로 생성 가능합니다.

### **쿼리 사용하기**

`router.query` 값을 사용하면 페이지 주소에서 Params 값이나 쿼리스트링 값을 참조할 수 있습니다.

예를 들면 `pages/products/[id].js` 페이지에서 `router.query['id']` 값으로 Params `id`에 해당하는 값을 가져올 수 있었습니다.

pages/products/[id].js

```jsx

import { useRouter } from 'next/router';

export default function Product() {
  const router = useRouter();
  const id = router.query['id'];

  return <>Product #{id} 페이지</>;
}

```

`/search?q=티셔츠`와 같은 주소로 들어왔을 때 `router.query['q']` 값으로 쿼리스트링 `q`에 해당하는 값을 가져올 수 있었습니다.

pages/search.js

```jsx

import { useRouter } from 'next/router';

export default function Search() {
  const router = useRouter();
  const q = router.query['q'];

  return <>{q} 검색 결과</>;
}

```

### router.push로 페이지 이동

- SearchFrom을 만들면서 router 이동을 이해

```jsx
import { useRouter } from "next/router";
import { useState } from "react";

export default function SearchForm({ init = "" }) {
  const [value, setValue] = useState(init);

  const router = useRouter();

  const handleChangeValue = (e) => {
    setValue(e.target.value);
  };

  const handleSearch = (e) => {
    e.preventDefault(); //form 태그의 기본동작을 막고 Next.js의 라우터로 페이지를 이동하기 위함.

    if (!value) {
      router.push("/");
      return;
    }

    router.push(`search?q=${value}`);
  };

  return (
    <form onSubmit={handleSearch}>
      <input name="q" value={value} onChange={handleChangeValue} />
      <button>검색</button>
    </form>
  );
}
```

### 리다이렉트

`next.config.js` 파일을 수정하면 특정 주소에 대해서 리다이렉트할 주소를 지정할 수 있습니다. 예를 들어서 `/products/:id`라는 주소로 들어오면 `/items/:id`라는 주소로 이동시켜 줄 수 있었죠.

이때 `permanent`라는 속성으로 상태 코드를 정할 수 있었는데요. `permanent: false`로 하면 307 Temporary Redirect를 하고, `permanent: true`로 하면 브라우저에 리다이렉트 정보를 저장하는 308 Permanent Redirect를 할 수 있었습니다.

```jsx

/** @type {import('next').NextConfig} */
const nextConfig = {
  async redirects() {
    return [
      {
        source: '/products/:id',
        destination: '/items/:id',
        permanent: true,
      },
    ];
  },
}

module.exports = nextConfig;

```

- 업데이트로 url을 바꾸었을때 ex) products → items
- 사용자가 products로 왔을때 items로 리다리렉트

**next.config.js**

- next.js 서버를 설정하는 파일
- next.js는 서버를 실행하는 것이기때문에 이런 처리가 가능
- 공식문서 참고

[Next.js by Vercel - The React Framework](http://nextjs.org)

### 커스텀  404 페이지 만들기

존재하지 않는 주소로 들어올 경우에 Next.js에서는 기본적으로 404 페이지를 보여 줍니다. 내가 원하는 404 페이지를 보여주려면 `pages/404.js` 파일을 만들고 일반적인 페이지처럼 구현하면 됩니다.

- pages 폴더 아래 404.js를 만든다

```jsx
import ButtonLink from "@/components/ButtonLink";
import Container from "@/components/Container";
import Header from "@/components/Header";
import styles from "@/styles/NotFound.module.css";

export default function NotFound() {
  return (
    <>
      <Header />
      <Container>
        <div className={styles.notFound}>
          <div className={styles.content}>
            찾을 수 없는 페이지입니다.
            <br />
            요청하신 페이지가 사라졌거나, 잘못된 경로를 이용하셨어요 :)
          </div>
          <ButtonLink className={styles.button} href="/">
            홈으로 이동
          </ButtonLink>
        </div>
      </Container>
    </>
  );
}
```

### 커스텀 App과 Document

- Header와 Contain 태그를 모든 페이지의 **공통 레이아웃**을 설정할때 App안에 적용해줌
- Document
    - 서버에서 렌더링해서 넘겨줄때만 동작하며, 클라이언트에서는 동작하지 않음
    - React에서 벗어나는 html을 속성 지정.
    - useState, useEffect, onClick 등 훅 동작하지 않음.

**커스텀 App**

모든 페이지에 공통적으로 코드를 적용하고 싶다면 커스텀 `App` 컴포넌트를 수정하면 됩니다. 

`pages/_app.js` 파일에 있는 컴포넌트인데요. 이 컴포넌트에 사이트 전체에서 보여 줄 컴포넌트나 전체적으로 적용할 리액트 컨텍스트를 적용할 수 있었습니다. 그리고 사이트 전체에 적용할 CSS 파일도 여기서 임포트할 수 있죠.

커스텀 `App` 컴포넌트의 Props는 `Component`와 `pageProps`가 있는데요. 우리가 만든 페이지들이 `Component` Prop으로 전달되고 여기에 내부적으로 필요한 Props는 `pageProps`라는 값으로 전달됩니다.

pages/_app.js

```jsx

import Header from '@/components/Header';
import { ThemeProvider } from '@/lib/ThemeContext';
import '@/styles/globals.css';

export default function App({ Component, pageProps }) {
  return (
    <ThemeProvider>
      <Header />
      <Component {...pageProps} />
    </ThemeProvider>
  );
}

```

**커스텀 Document**

`pages/_document.js` 파일에 있는 `Document` 컴포넌트는 HTML 코드의 뼈대를 수정하는 용도로 사용합니다. 코드는 React 컴포넌트이지만 일반적인 컴포넌트처럼 동작하지는 않기 때문에 `useState`나 `useEffect`처럼 브라우저에서 실행이 필요한 기능들은 사용할 수 없습니다.

pages/_document.js

```jsx

import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html lang="ko">
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

- _app.js

```jsx
import Header from "@/components/Header";
import { ThemeProvider } from "@/lib/ThemeContext";
import "@/styles/global.css";

export default function App({ Component, pageProps }) {
  return (
    <ThemeProvider>
      <Header />
      <Component {...pageProps} />
    </ThemeProvider>
  );
}
```

- setting.js

```jsx
import Dropdown from "@/components/Dropdown";
import { useTheme } from "@/lib/ThemeContext";
import styles from "@/styles/Setting.module.css";

export default function Setting() {
  const { theme, setTheme } = useTheme();

  return (
    <div>
      <h1 className={styles.title}>설정</h1>
      <section className={styles.section}>
        <h2 className={styles.sectionTitle}>테마 설정</h2>
        <Dropdown
          className={styles.input}
          name="theme"
          value={theme}
          onChange={(name, value) => setTheme(value)}
          options={[
            { label: "라이트", value: "light" },
            { label: "다크", value: "dark" },
          ]}
        />
      </section>
    </div>
  );
}
```

- global.css

```jsx
body.dark {
  background-color: #000000;
  color: #ffffff;
}

body.dark a {
  color: #ffffff;
}

body.light {
  background-color: #ffffff;
  color: #000000;
}

body.light a {
  color: #000000;
}

a {
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
```

- Header.module.css

```jsx
.header {
  border-bottom: 1px solid #1f1f1f;
}

	:global(.light) .header {  // module이 제공하는 기능.(이렇게 작성하면 됨)
  border-color: #e1e1e1;
}

.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 26px 0;
}
```
