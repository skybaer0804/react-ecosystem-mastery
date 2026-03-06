# React 설치 및 환경설정

## 1. Node.js 설치

Node.js는 JavaScript 런타임 환경으로, React 개발에 필수적입니다.

- **최신 LTS 버전 추천**: Node.js 18 이상 (2024년 기준)
- 설치 방법: [nodejs.org](https://nodejs.org/)에서 다운로드
- 설치 확인: 터미널에서 `node -v` 및 `npm -v` 실행

```bash
node -v  # v18.17.0 이상 권장
npm -v   # 9.0.0 이상
```

## 2. 패키지 관리자

### Yarn (권장)
빠르고 안정적인 패키지 관리자입니다.

```bash
npm install -g yarn
yarn --version
```

### 또는 pnpm
더 빠르고 효율적인 패키지 관리자 (최신 트렌드)

```bash
npm install -g pnpm
pnpm --version
```

## 3. React 프로젝트 생성

### Vite를 사용한 최신 방법 (권장)
Vite는 빠른 개발 서버와 빌드 도구를 제공합니다.

```bash
# npm 사용
npm create vite@latest my-react-app -- --template react

# yarn 사용
yarn create vite my-react-app --template react

# TypeScript 템플릿
npm create vite@latest my-react-app -- --template react-ts
```

### Create React App (기존 방법)
전통적인 방법이지만, Vite가 더 빠릅니다.

```bash
npx create-react-app my-app
npx create-react-app my-app --template typescript
```

## 4. 프로젝트 실행

```bash
cd my-react-app
npm run dev  # Vite의 경우
# 또는
npm start    # CRA의 경우
```

브라우저에서 `http://localhost:5173` (Vite) 또는 `http://localhost:3000` (CRA)로 접속

## 5. 필수 확장 및 도구

### VS Code 확장
- **ES7+ React/Redux/React-Native snippets**: 코드 스니펫 제공
- **Prettier**: 코드 포맷터
- **ESLint**: 코드 린터
- **Auto Rename Tag**: HTML/JSX 태그 자동 리네임

### 브라우저 확장
- **React Developer Tools**: React 컴포넌트 디버깅

## 6. 추가 라이브러리 설치

프로젝트 시작 시 유용한 라이브러리들:

```bash
npm install axios          # HTTP 클라이언트
npm install react-router-dom  # 라우팅
npm install @reduxjs/toolkit  # 상태 관리 (Redux)
npm install styled-components # CSS-in-JS
```

## 7. 환경 설정 팁

### ESLint 설정
`.eslintrc.js` 파일 생성:

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    sourceType: 'module',
  },
  plugins: ['react'],
  rules: {
    // 커스텀 규칙 추가
  },
};
```

### Prettier 설정
`.prettierrc` 파일 생성:

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false
}
```

이제 최신 React 개발 환경이 준비되었습니다!
