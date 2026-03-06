### 1. Node.js 설치

- node.js는 웹브라우저 바깥에서 js를 사용하게 만들어준다.
그러나 프론트엔드에서는 보조 역할만 한다.
- Node.js 설치시 npm(node package manager) 같이 설치된다. 하지만 yarn이 훨씬 빠르기에 yarn을 추천한다.
- 크롬 웹스토어에서 **React Developer Tools** 설치

https://nodejs.org/ko/

### 2. yarn

- 패키지 관리자 도구인 yarn은 npm을 대체할 수 있는 도구로 npm보다 더 빠르며 효율적인 캐시 시스템과 기타 부가 기능을 제공한다.
    
    ```
    npm istall --global yarn
    yarn --version
    ```
    

### 3. React  프로젝트 생성

- 프로젝트 생성
    
    ```
    //case1. 현재 위치한 폴더에 프로젝트를 생성
    npm init react-app .
    
    //case2. typescript 템플릿 설치
    npx create-react-app my-app --template typescript
    
    //case3. 기존 프로젝트에 TypeScript 추가
    npm install typescript @types/react @types/react-dom @types/node
    
    **//case4. yarn으로 설치(추천)**
    **yarn create react-app [프로젝트 이름] --template typescript**
    ```
    
- 프로젝트 실행은 scripts를 참고하고 패키지관리 도구를 사용해 실행.
    
    ```
    // package.json 파일에 scripts 참고
    npm run start   OR  npm start 도 됨. OR yarn start
    ```
    
- 프로젝트 디렉토리가 정상적이고 실행시 웹페이지가 보이면 정상 설치된걸로 판단하면 된다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/05e4f6e6-9ea3-450e-9798-efb894083a99/Untitled.png)
    
- package.json에는 설치한 라이브러리 항목들이 있는데. yarn install 혹은 npm install을 통해 변경된 라이브러리들을 한번에 받아올 수 있다. 협업할때 사용하자.
- npm을 install 하는 이유는보통 Node.js로 만든 프로젝트를 공유할 때는 
용량이 많은 node_modules 이 폴더를 제외한다, node_module은 직접 설치 할 수 있다.
- **기본 라이브러리 설치**
    - CRA(CreateReactApp) 일때는 아래와 같은 라이브러리를 설치해두면 편하다.
    - 참고용으로 둔다.
    
    ```
    1. Node.js 설치
        - https://nodejs.org/en
        - 2023-03-25 기준 18.14.1 LTS 버전
        - 설치 버전 확인 cmd -> 'node -v'
    
    2. node-module 설치
        - 설치 명령어 터미널 'npm install'
        - 실행 명령어 터미널 'npm start' 
    
    3. 리액트 라우터v6 설치
        - 리액트 컴포넌트로 페이지를 나누고 이동하게 해주는 라이브러리
        - 타입스크립트 개념이 포함되있기에 별도 typeScript 버전 설치할 필요없음
        - 설치 명령어 터미널 'npm install react-router-dom@6'
        - 설치 확인
        package.json 파일에 dependencies 아래에 'react-router-dom' 이란 게 추가되고
        node_modules 폴더에 'react-router-dom'이라는 폴더 생성
    
    4. ESLint 익스텐션 설치
        - 코드 작성할 때 실수시 에러 혹은 경고메시지를 vscode 에디터에서 확인할 수 있게해줌.
    
    5. Prettier - Code formatter 익스텐션 설치
        - JSX 작성시 코드의 가독성을 위해 들여쓰기를 자동으로 해줍니다.
        - 코드 스타일 커스터마이징 가능합니다.(현재 기본설정)
        - 설정 옵션 확인 : https://prettier.io/docs/en/options.html 참고
    
    6. 프로시 설정
        - package.json 폴더에 "proxy": "http://localhost:9080" api 서버
        - 개발 환경상 백엔드 서버 localhost:9080에서 실행되고 있고, 
        React 프론트엔드 서버 localhost:3000번으로 실행됩니다. 
        그러다보니까 CORS( cross-origin requests) 가 발생하게 되는데, 
        이 문제를 해결 하기 위해서는 Proxy를 프론트쪽에서 잡아주어야합니다.
        - 리액트 공식 문서 : https://create-react-app.dev/docs/proxying-api-requests-in-development/
    ```
    
- **Proxy설정 및 서버 CROS 설정**
    - 웹팩의 Proxy 설정을 활용하면 origin을 바꿔서 api서버에 통신하므로 CROS를 피할 수 있다.
    - package.json
    
    ```jsx
    //example
    "proxy": "http://192.168.0.12:8000",//server cros
    "proxy": "http://localhost:9080",//server cros example
    ```
    
    - 또는 api서버에서 CROS로 내 origin이나 맞춘 origin을 설정하면된다.
