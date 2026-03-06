## **1. Image**

### **v13 이전 (Pages Router)**

- **특징**: <Image> 컴포넌트는 next/image에서 가져와 사용하며, 정적 또는 외부 이미지 최적화를 지원했습니다.
- **설정**: next.config.js에서 도메인 설정(images.domains) 필요.
- **동작 방식**: 클라이언트 사이드에서 이미지 최적화 및 로딩 처리.

### **예제 코드**

```jsx
*// pages/index.js*
import Image from "next/image";

export default function Home() {
  return (
    <div>
      <Image src="/example.jpg" alt="Example" width={500} height={500} />
    </div>
  );
}
```

### **v13 이후 (App Router)**

- **변경점**:
    - <Image> 컴포넌트는 여전히 next/image에서 가져오지만, App Router에서 서버 컴포넌트와 통합되어 사용 가능.
    - next/image가 개선되어 더 빠른 이미지 로딩과 빌드 타임 최적화 제공.
    - unoptimized 속성으로 최적화 비활성화 가능.
- **도메인 설정**: next.config.js에서 images.remotePatterns으로 외부 도메인 설정 방식 변경.

### **예제 코드**

```jsx
*// app/page.js*
import Image from "next/image";

export default function Page() {
  return (
    <div>
      <Image src="/example.jpg" alt="Example" width={500} height={500} priority />
    </div>
  );
}
```

### **next.config.js**

```jsx
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "example.com",
      },
    ],
  },
};
```

### **주요 차이**

- 설정 방식이 domains에서 remotePatterns으로 더 세밀해짐.
- 서버 컴포넌트에서 기본적으로 실행되므로 성능 향상.

---

## **2. Head**

### **v13 이전 (Pages Router)**

- **특징**: next/head에서 가져와 페이지별로 <head> 태그를 동적으로 수정.
- **사용**: 주로 메타 태그, 타이틀, 링크 등을 설정.

### **예제 코드**

```jsx
*// pages/index.js*
import Head from "next/head";

export default function Home() {
  return (
    <div>
      <Head>
        <title>My Page Title</title>
        <meta name="description" content="This is my page" />
      </Head>
      <h1>Welcome</h1>
    </div>
  );
}
```

### **v13 이후 (App Router)**

- **변경점**:
    - next/head는 여전히 사용 가능하지만, App Router에서는 metadata 객체를 사용해 정적 메타데이터를 설정하는 방식이 권장됨.
    - 동적 메타데이터는 generateMetadata 함수로 처리.
- **장점**: 서버 컴포넌트와 통합되어 빌드 타임에 최적화.

### **예제 코드 (정적 메타데이터)**

```jsx
*// app/page.js*
export const metadata = {
  title: "My Page Title",
  description: "This is my page",
};

export default function Page() {
  return <h1>Welcome</h1>;
}
```

### **예제 코드 (동적 메타데이터)**

```jsx
*// app/post/[id]/page.js*
export async function generateMetadata({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`).then(res => res.json());
  return {
    title: post.title,
    description: post.summary,
  };
}

export default function Post({ params }) {
  return <h1>Post ID: {params.id}</h1>;
}
```

### **주요 차이**

- Head 컴포넌트 대신 metadata 객체로 간소화.
- 동적 데이터 기반 메타데이터 설정이 generateMetadata로 더 체계적.

---

## **3. 구글 폰트 적용**

### **v13 이전 (Pages Router)**

- **특징**: <link> 태그를 Head에 추가하거나, 외부 스타일시트로 폰트를 수동으로 적용.
- **방법**: 구글 폰트 URL을 직접 삽입하거나 CSS로 처리.

### **예제 코드**

```jsx
*// pages/index.js*
import Head from "next/head";

export default function Home() {
  return (
    <div>
      <Head>
        <link
          href="https://fonts.googleapis.com/css2?family=Roboto&display=swap"
          rel="stylesheet"
        />
      </Head>
      <h1 style={{ fontFamily: "Roboto, sans-serif" }}>Hello World</h1>
    </div>
  );
}
```

### **v13 이후 (App Router)**

- **변경점**:
    - @next/font 모듈이 추가되어 구글 폰트를 쉽게 가져오고 최적화 가능.
    - 빌드 시 폰트를 자동으로 다운로드해 자체 호스팅하며, CLS(Cumulative Layout Shift)를 방지.
- **사용**: next/font/google에서 폰트를 가져와 변수로 적용.

### **예제 코드**

```jsx
*// app/page.js*
import { Roboto } from "next/font/google";

const roboto = Roboto({
  weight: "400",
  subsets: ["latin"],
});

export default function Page() {
  return <h1 className={roboto.className}>Hello World</h1>;
}
```

### **레이아웃에 적용**

```jsx
*// app/layout.js*
import { Roboto } from "next/font/google";

const roboto = Roboto({
  weight: "400",
  subsets: ["latin"],
});

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={roboto.className}>
      <body>{children}</body>
    </html>
  );
}
```

### **주요 차이**

- <link> 태그 대신 @next/font로 간편하고 최적화된 적용.
- 폰트 자체 호스팅으로 성능 개선 및 외부 요청 감소.

---

## **정리**

Next.js v13 이후(App Router)에서 Image, Head, 구글 폰트 적용의 주요 변화는 다음과 같습니다:

1. **Image**:
    - images.domains → images.remotePatterns으로 설정 방식 변경.
    - 서버 컴포넌트 통합으로 성능 향상.
2. **Head**:
    - next/head → metadata 객체 또는 generateMetadata로 전환.
    - 정적/동적 메타데이터 관리가 더 체계적.
3. **구글 폰트**:
    - 수동 <link> 태그 → @next/font/google으로 최적화된 적용.
    - 자체 호스팅으로 로딩 속도 개선.

이러한 변화는 App Router의 서버 중심 설계와 성능 최적화를 반영하며, 더 간결하고 효율적인 개발 경험을 제공합니다. 위 예제 코드를 참고해 v13 이후 방식으로 적용해보세요! 추가 질문이 있다면 말씀해주세요.

---

[v13 이전](https://www.notion.so/v13-1bcd68f80cf68057849cd38301880e9c?pvs=21)
