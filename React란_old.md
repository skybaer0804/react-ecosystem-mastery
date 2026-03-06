## 1. React ?

React는 JavaScript로 HTML 태그를 만들고 렌더링 하는데 가상DOM을 활용해 데이터가 바뀌어도 전체를 렌더링하는게 아니라 바뀐 데이터 부분만 리렌더링 해준다.

## 2. 왜 리액트를 쓰는게 좋을까? 리액트의 이해

자바스크립트는 단순 웹 브라우저 연산이나 시각적인 효과를 주는 단순한 스크립트 언어를 벗어나 웹앱(애플리케이션) 의 핵심 역할을 하게 됨.

최근 몇 년는 웹 브라우저 영역에서 더 확장되어 서버 사이드, 모바일, 데스크톱 에서도 많이 쓰이고 있음.

예를 들어 슬랙, 아톰, VS Code 등 데스크톱 앱도 일렉트론(자바스크립로 데스크톱 앱을 만들 수 있는 프레임워크)로 개발함.

모바일도 페이스북, 디스코드, 페이팔, 이베이등 공룡 기업과 중소기업이 자바스크립트로 크로스 플랫폼 앱을 개발 할 수 있는 여러 프레임워크들(React Native, Ionic, Titanium, NativeScript 등)로 개발함.

대규모 애플리케이션 중 프론트엔드 사이드에서 돌아가는 앱의 구조를 관리할때 순수 자바스크립트로는 어려움이 많아서
다양한 관점으로 이를 해결하려고 노력해서 다양한 프레임워크, 라이브러리가 나오게 됨.
(Angular, Backbone.js, Derby.js, Ember.js, Ext.js, Vue.js, PureMVC, …)

이 프레임워크들은 주로 MVC 아키텍쳐, MVVM( Model - View - View - Controllor ) 아키텍쳐를 사용함. 
Angular의 경우 MVW( Model - View - Whatever) 아키텍쳐로 구조화함.

이 아키텍쳐의 공통점은 Model 과 View 임.

모델은 데이터를 관리하는 영역이고, 뷰는 사용자에게 보여주는 부분임.
사용자가 작업을 하면 컨트롤러가 모델을 조회하거나 수정하고 변경된 사항을 뷰에 반영함.

이 뷰에 반영하는 부분은 애플리케이션의 규모가 크면 복잡해지고 성능도 떨어질 수 있음.

페이스북 개발팀은 이를 해결하려고 아이디를 고안해 냈는데, 어떤 데이터가 변할 때마다 어떤 변화를 줄지 고민하는 것이 아니라
그냥 기존 뷰를 날려 버리고 처음부터 새로 렌더링 하는 방식임.

이렇게 하면 애플리케이션 구조가 매우 간단하고, 작성할 코드의 양도 많이 줄어들게 됨.

더 이상 어떻게 변화줄지 신경 쓸 필요가 없고, 그저 뷰가 어떻게 생길지 선언하며, 데이터에 변화가 있으면 기존에 있던 것은 버리고 정해진 규칙에 따라 새로 렌더링 하면 됨.

하지만 이 방식대로하면 CPU 점유율이 높게되고, DOM은 느림, 메모리도 많이 사용할 거고, 사용자가 input 박스에 텍스트를 입력할 때마다 기존에 렌더링된 것은 사라지고 새로 렌더링하면 끊김 현상이 발생하게 됨.

그래서 페이스북 개발 팀은 앞서 설명한 방식으로 최대한 성능을 아끼고 편안한 사용자 경험을 제공하면서 구현하고자 개발한 것이 바로 리액트임.

구조가 MVC, MVW 등인 프레임워크와 달리 리액트는 자바스크립트 라이브러리로 오직 뷰만 신경 씀.

리액트는 특정 부분이 어떻게 생길지 정하는 선언체가 있는데, 이를 컴포넌트(Component)라고 함.

컴포넌트는 단순 템플릿 개념이 아닌 좀 더 복합적인 개념임.

컴포넌트는 재상요이 가능한 API로 수많은 기능들을 내장하고 있으며, 컴포넌트 하나에서 해당 컴포넌트의 생김새와 작동 방식을 정의함.

사용자 화면에 뷰를 보여 주는 것을 렌더링이라고함.

리액트는 어떻게 렌더링 하길래 데이터가 변할 때마다 새롭게 리렌더링 하면서 성능을 아끼고 최적의 사용자 경험을 제공할까?

이를 알려면 컴포넌트가 최초로 실행한 '초기 렌더링'과 '컴포넌트의 데이터 변경으로 다시 실행되는 '리렌더링' 개념을 이해 해야함.

### 2.1 초기 렌더링

어떠한 UI 프레임워크, 라이브러리든 맨 처음 어떻게 보일지를 정하는 초기 렌더링이 필요함.
리액트에서는 이를 다루는 render 함수가 있음.

```
render() { ... }
```

이 함수는 컴포넌트가 어떻게 생겼는지 정의하는 역할을 함.

이 함수는 html 형식의 문자열을 반환하지 않고, 뷰가 어떻게 생겼고 어떻게 작동하는지에 대한 정보를 지닌 객체를 반환함.

컴포넌트 내부에 또 다른 컴포넌트들이 들어갈 수 있음.

이때 render 함수를 실행하면 그 내부에 있는 컴포넌트들도 재귀적으로 렌더링함.

이렇게 최상위 컴포넌트를 렌더링 하는 작업이 끝나면 지니고 있는 정보들을 사용해 HTML, 마크업을 만들고, 이를 우리가 정하는 실제 페이지의 dom 요소 안에 주입함.

컴포넌트를 실제 페이지에 렌더링할 때는 분리된 두 가지 절차를 따르는데, 먼저 문자열 형태의 HTML 코드를 생성한 후 특정 DOM에 해당 내용을 주입하면 이벤트가 적용됨.

### 2.2조화 과정(reconciliation)

그래서 리액트에서 업데이트를 어떻게 진행하는가?

우선, 리액트에서는 업데이트 보다는, 조화과정을 거친다라는 표현이 더 맞음.

컴포넌트에서 데이터에 변화가 있을 때 우리가 보기에는 변화에 따라 뷰가 변형되는 것처럼 보이지만,

사실은 새로운 요소로 갈아 끼우기 때문임.

이 작업도 render 함수가 함. 

데이터를 업데이트 했을 때 단순히 업데이트한 값을 수정하는 것이 아니라, 새로운 데이터를 가지고 render 함수를 또 다시 호출함.

그럼 업데이트가 된 데이터를 가진 뷰가 생성됨.

이 때 곧바로 DOM 에 반영되지 않고, 이 전 render 함수가 만들었던 컴포넌트 정보와 현재 render 함수가 만든 컴포넌트 정보를 비교함.

자바스크립트로 두 가지 뷰의 차이점을 최소한의 연산으로 비교해서 알아낸 다음

최소한의 연산으로 DOM 트리를 업데이트 하는 것임.

마치 방식 자체는 루트 노드부터 시작해서 전체 컴포넌트를 처음부터 다시 렌더링하는 것처럼 보일 뿐

사실 최적의 자원을 수행해서 이를 수행하는 것임.

### 2.3 Virtual DOM

먼저 DOM이 느린지 부터 말해보면,

DOM 자체는 빠름. 단, 웹 브라우저 단에서 DOM에 변화가 일어나면 웹 브라우저가 CSS를 다시 연산하고, 레이아웃을 구성하고, 페이지를 리페인트하는 이 과정이 느리거임.

돌아가서

리액트는 DOM을 최소한으로 조작하여 작업하기 위해 Virtual DOM 방식을 사용하여 DOM 업데이트를 추상화함으로써 DOM 처리 횟수를 최소화하고 효율적으로 진행함.
리액트가 웹 브라우저에 실제 DOM을 업데이트 할 때는 3가지 절차를 밟음.

1. 데이터를 업데이트하면 전체 UI를 Virtual DOM에 리렌더링함.
2. 이전 Virtual DOM에 있던 내용과 현재 내용을 비교함.
3. 바뀐 부분만 실제 DOM에 적용함.

그러나 Virtual DOM을 쓴다고 무조건 빠른것은 아님.

리액트를 사용하지 않아도 코드를 최적화 하거나, 또 작업이 매우 간단할 때( 예로 단순 라우팅 정도만 있는 정적 페이지)는 오히려 리액트를 사용하지 않는 편이 더 나은 성능을 보임.

즉, 리액트와 Virtual DOM이 제공하는건 업데이트 처리의 간결성임.

앞서 리엑트는 뷰만 신경쓰는 라이브러리라 설명함.

그래서 다른 프레임워크와 달리 기타 기능은 직접 구현해야함.

Ajax, 데이터 모델링, 라우팅 등.. But 걱정할 필요는 없음 React를 위한 라이브러리가 무수히 많음.

즉, 라우팅에는 리액트 라우터, Ajax 처리에는 axios나 fetch, 상태 관리에는 리덕스나 MobX를 사용하면 됨.

리액트의 다른 장점은 다른 웹 프레임워크나 라이브러리와 혼용할 수도 잇음.

---

## 3. 오래된 방식 React 렌더링, 이벤트 추가(참고)

```jsx
//root 컴포넌트 생성 및 렌더링되는 코드 살펴보기

import ReactDOM from "react-dom/client";
import App from "./components/App";

const root = ReactDOM.createRoot(document.getElementById("root"));

root.render(
  <>
    <App />
  </>
);
```

### 3.1 React 컴포넌트 렌더링

<aside>
💡 const test = React.createElement( 태그, {속성}, 컨텐츠(내용) )

</aside>

- 오래된 방식, 현재 사용하지 않음
- JSX문법을 사용하겠지만 결국 React에 의해서 아래 표현식으로 바뀌게 됨.

```jsx
<body>
	<div id="root"></div>
</body>
<script>
//root 아이디를 갖은 element를 const root에 저장.
	const root = document.getElementbyId("root");

//span 태그의 id는 'sexy-span' 이고 내용은 '섹시스펜'인 React element를 만들어
Component에 저장.
  const Component = React.createElement("span", {id:"sexy-span", }, "섹시스펜");

//위 Componet를 root 요소 안에 넣어 화면에 표시
	ReactDOM.render(Component, root)
</script>

// React => HTML 결과
<body>
	<div id='root'>
	  <span id='sexy-span'>섹시스펜</span>
	</div>
</body>

------------------------------------------------------------------------------------

// 여러개의 컴포넌트
<body>
	<div id="root"></div>
</body>
<script>
  const Component = React.createElement("span", {id:"sexy-span"}, "섹시스펜");
  const btn = React.createElement("botton", null, "버튼");
  const Container = React.createElement("div", null, [Component, btn]);
	
	ReactDOM.render(Container , root)
</script>

// React => HTML

<div id='root'>
	<div>
	  <span id='sexy-span'>섹시스펜</div>
    <botton>버튼</botton>
	</div>
</div>
```

Container 라는 div태그를 생성해 그 안 컨텐츠에 컴포넌트들의 배열을 넣어주었다.

이 Container를 root안에서 렌더링 한다.

### 3.2 이벤트 리스너 추가해보기

- 오래된 방식, 현재 사용하지 않음

```jsx
<body>
	<div id="root"></div>
</body>
<script>
  const Component = React.createElement("span", {id:"sexy-span"}, "섹시스펜");
  const btn = React.createElement(
			"botton", 
			{ onClick: () => console.log("addEventLisner!") }
			 "버튼");
  const Container = React.createElement("div", null, [Component, btn]);
	
	ReactDOM.render(Container , root)
</script>
```

---

## 4. JSX 문법

### 4.1 오래된 방식을 JSX문법으로 바꿔보기

```jsx
<body>
	<div id="root"></div>
</body>
<script>
  const Component = React.createElement("span", {id:"sexy-span"}, "섹시스펜");

// JSX문법
  const btn = (
		<botton onClick={() => console.log("addEventLisner!")}>버튼</botton>
	);
  const Container = React.createElement("div", null, [Component, btn]);
	
	ReactDOM.render(Container , root)
</script>
```

- 렌더링되어 브라우저에 표시될때는 위에 오래된 React형식으로 표시된다.

### 4.2 JSX 문법 컴포넌트 생성 2가지 방법

- 컴포넌트 이름의 첫글자는 대문자이다.
React가 html 태그로 판단하지 않고 컴포넌트임을 인지한다.

```jsx
<body>
	<div id="root"></div>
</body>
<script>

// 1번째 방법
  function Title() = {
		return (
			<h3 id="title" onMouseEnter={()=> console.info("마우스 오버됨")} >
				제목 입니다
			</h3>
		);
	}

// 2번째 방법
	const Button = () => (
		<botton 
			style={{backgroundColor:"tomato"}}
			onClick={() => console.log("click!")}
		>
			버튼
		</botton>
	);

  const Container = (
			<div>
				<Title />
				<Button />
			<div>
	);
	
	ReactDOM.render(Container , root)
</script>
```

### 4.3 JSX 규칙?

- JS 에 html을 섞어쓰는 확장된 문법.
- html에서 class 속성을 쓰려면 JSX에서는 className으로 사용한다.
- html에서 label 태그안에 for 속성을  쓰려면 JSX 에서는 'htmlFor'으로 사용한다.
- 이벤트 핸들러의 이름은 on 다음 카멜케이스로 작성한다.

```jsx
<input id="name" onBlur="" onFocus="" onMouseDown=""/>
```

- JSX 문법상 render에 요소들은 하나의 태그로 감싸주어야 에러가 발생하지 않는다.

```jsx
root.render{
 <div>
	<p>안녕</p>
	<p>리엑트</p>
 <div>
}
```

- 다른 방법은 < Fragment > 태그로 감싸주면 된다.
- 이 방법을 쓰면 <div> 없이 들어가기 때문에  불필요한 div 태그라면 이를 없애 줄 수 있다.
- import { Frgment } from 'react'; 가 추가된다.

```jsx
import { Frgment } from 'react';

// 1번째 방법
root.render{
 <Fragment>
	<p>안녕</p>
	<p>리엑트</p>
 </Fragment>
}

// 2번째 방법
root.render{
 <>
	<p>안녕</p>
	<p>리엑트</p>
 </>
}
```

### 4.4 JSX 에서 자바스크립트 사용하기

- 중괄호 안에서는 자바스크립는 표현식을 사용할 수 있다
- 단, IF문, FOR문,  함수 선언문 등 의 자바스크립트 문장은 쓸 수 없다.
- 이벤트 예시

```jsx
import ReactDOM from 'react-dom/client';

const name = "의진";
const pictureUrl = "https://res.klook.com/image/upload/c_fill,w_843,h_474/q_80/w_80,x_15,y_15,g_south_west,l_Klook_water_br_trans_yhcmh3/activities/ys5jqwvg6pgtpacb5kqb.webp";

function clickEvent(){
  alert("달려갈게!");
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <>
  <h1 id="title">우리 {name}이 사랑한다.</h1>
  <img src={pictureUrl}></img>
  <button className="test" onClick={clickEvent}>눌르면</button>
  </>
);
```

[Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b72c9d2-5a5b-4388-9d9a-2509ad849e74/Untitled.mp4)

https://ko.reactjs.org/docs/dom-elements.html#differences-in-attributes

### 4.5 children을 잘 활용해서 컴포넌트를 렌더링.

```jsx
Button.js

function Button({childeren}){
	return(
		<button>
			{childeren}
		</button>
	)
}

export default Button

App.js

import Button from './Button.js';

function App(){
	return (
		<div>
			<Button>버튼이름</Button>
		</div>
	);
}
```

---

## 5. State

```jsx
const data = React.useState( 초기값 );
```

React.useState를 통해 초기값을 넘겨주었다면 data 는 배열을 받게된다.

```jsx
data = [ 초기값, 변경함수 ];
```

변경 함수는 React에서 제공해주는데 이 data의 값을 변경하게 해주는 함수이다.

<aside>
💡 const [name, setName] = React.useState(초기값);

</aside>

이렇게 한줄로 끝내면 const data, setData를 쓸수 있게된다.

```jsx
const [data, setData] = React.useState(0);
console.log(data) // 0
setData(10);
console.log(data) // 10
setData(data +1);
console.log(data) // 11
```

<aside>
💡 리렌더링 : 데이터가 변경되면 React는 이를 감지해서 '변화된 부분만 렌더링해준다!!'

</aside>

데이터가 바뀔때마다 UI를 refresh 해준다.

<aside>
💡 데이터를 바꿀때. 현재 상태값을 통해 다음 상태값을 바꿔야할때는 위 방법보다
아래 방법으로 한다.

</aside>

```jsx
// setState(current + 1) BAD!
setSate((current)=>current +1);
```

함수형태로 하는것이 훨씬 안전하다.

예상치 못한 업데이트가 어디선가 발생하더라도 이 것이 혼동을 방지해준다.(버그 방지)

### 5.1 참조형 State

State 데이터가 하면 컴포넌트는 리렌더링한다.
**참조형 데이터(object, Array) 의 경우, 새로운 데이터로 복제, 생성해야 참조값 주소가 달라진다.**
즉, 참조형 데이터는 원본데이터를 복제해 수정 후 State에 저장해줘야 한다.

**즉, 반드시 새로운 참조형 값을 만들어서 state를 변경 해한다.**

```jsx
import {useState} from 'react';

const [someArray, setSomeArray] = useState([1, 2, 3]);
const [someObject, setSomeObject] = useState({});

const nextArray = [...someArray] // 배열타입으로 someArray를 복사해줌.
nextArray.push(4);
setSomeArray(nextArray);

// Spred 문법
setSomeArray([...someArray, 4]);
// 장점. 불필요 변수 제거, 바로 복제, 추가 생성 후 set 해줌. 
```

### 5.2 이벤트 헨들러

- onChange  : input 핸들러의 oninput 이벤트 핸들러로 동작함.

```jsx
const handleBetChange = (e) => { // event 핸들러
    const num = Number(e.target.value); // 사용자가 입력한 값 1 ~ 9
    if (num >9) num %= 10; // 나머지 할당 연산자. 1 ~9 사이 숫자로 만들어줌
    if (num <1) num = 1; // 
    num = Math.floor(num);
    setBet(num);
  };
...

      <div>
        <input type="number" value={bet} min={1} max={9} onChange={handleBetChange}></input>
      </div>
```

### 5.3 컴포넌트

- 부품, 재사용 가능 개념으로 함수로 볼 수 있다.
- State 리부트 : 자식컴포넌트의 State를 부모 State로 올리는것을 의미한다.

### 5.4 React 렌더링 방식

- State 값이 변경 되면 Rendering 변경 한다.
- React는 Virtual DOM 에서 구성을하고
- DOM 과 비교해서 바뀐 node만 변경한다.
- Virtual은 적당한 양의 변경사항을 모아서 DOM과 비교, 바꾼다.
- 장점1: 때문에, 자원을 효율적으로 쓸 수 있다.
- 장점2: 개발자가 node를 신경쓸 필요없고, 오직 어떻게 보여줄 것인지만 신경쓰면됨, 깔끔해진다.

---

## 6. Style 적용해보기

- React 에서 Style 은 객체로 만들어서 적용해 볼 수도 있다.
- Prop Style 에 적용한다.
- **"-" 하이픈이 들어간 명칭은 카멜케이스로 작성해야 적용된다.**

```jsx
const style = {
	backgroundColor: "red" // 하이픈 명칭은 카멜케이스로 바꿈.
}

<Button style={style} >버튼</Button>

```

- Style 속성 중 공통 사용되는 속성은 아래와 같이 만들 수 있다.

```jsx
const baseButtonStyle = {
	padding: '14px 27px',
  borderRadius: '30px',
  backgroundColor: 'red',
  cursor: 'point',
  fontSize: '17px'
}

const blueButtonStyle = {
	...baseButtonStyle, //Spred 문법으로 펼침.
  color: '#000'

}
```

### 6.1 Style 적용 업그레이드 (className 사용)

- css를 분리 관리한다. 그래서 코드가 간결해진다.
- 분리된 css 파일을 적용할때는 import 를 사용한다.(파일경로)
- prop은 style이 아니라 className으로 지정.

```jsx
import 문자열 파일 경로

ex) import './index.css';
```

```jsx
// button.css

.Button {
	padding: 14px 27px;
  borderRadius: 30px;
  backgroundColor: red;
  cursor: point;
  fontSize: 17px;
}

.Button.blue {
	color: '#000'
}

.Button.red {
	color: '#fff'
}

// someComponent.js

import Button from './style/button.css'
...

function Button({ children, onClick, color }) {
	const classNames = `Button ${color}`;  // 클래스 이름별로 띄어쓰기. 주의
	return (
		<button className={classNames} onClick={onClick}>
			{children}
		</button>

	);
}

export default Button;
```

### 6.2 Style 적용 업그레이드( className 편하게 사용하기)

**템플릿 문자열 사용**

```jsx
function Button({ isPending, color, size, invert, children }) {
  const classNames = `Button ${isPending ? 'pending' : ''} ${color} ${size} ${invert ? 'invert' : ''}`;
  return <button className={classNames}>{children}</button>;
}

export default Button;
```

**배열을 사용**

```jsx
function Button({ isPending, color, size, invert, children }) {
  const classNames = [
    'Button',
    isPending ? 'pending' : '',
    color,
    size,
    invert ? 'invert' : '',
  ].join(' ');
  return <button className={classNames}>{children}</button>;
}

export default Button;
```

### 6.3 Style Module.css

- 전역적인 css를 적용하는게 아닌
- css를 모듈화해서 컴포넌트 별로 필요한 css를 적용하는 구조
- **최장점 : 같은 클래스 이름의 다른 style의 css를 만들어도 된다!**
- 이유 : 리엑트가 렌더링시 알아서 뒤에 랜덤한 이름을 붙여준다
- css를 모듈화하는것.
- 파일이름은 뒤에 .module.css를 붙여준다.

https://create-react-app.dev/docs/adding-a-css-modules-stylesheet/

---

## 7. Props 와 렌더링 최적화

- 부모 컴포넌트로 부터 자식 컴포넌트로 데이터, 이벤트, 함수 등을 전달해줄 수 있다.
- 이를 통해 비슷한 컴포넌트 통합해서 이를 "재활용" 할 수 있다.
- 컴포넌트에 내려주는 Props의 key는 무엇이든 상관없다. style이라고해도 된다.
하지만 style prop이 진짜 style을 바꿔주진 않는다. 태글 생성하는 쪽에 직접 '전달'해야 한다.
- 자식 컴포넌트는 유일한 인자 props를 받는다. 이는 객체이다. 전달하는 모든 정보를 갖고있는다.

```
function Btn(props){
	return <button style={props.style}>{props.text}</button>
}

function App(){
	return (
		<div>
			<Btn style={color:red} text={"Save" />
			<Btn style={color:blue} text={"Change"} />
		</div>
 )
}
```

- Btn 컴포넌트에서 property를 꺼낼때 아래 shortcut 방식을 쓴다

```
function Btn({style, text}){
	return <button style={style}>{text}</button>
}

function App(){
	return (
		<div>
			<Btn style={color:red} text={"Save" />
			<Btn style={color:blue} text={"Change"} />
		</div>
 )
}
```

- Prop으로는 문자, 숫자, 불린값, 함.. 등등 뭐든 내려줄수 있고 그에 따라 자식컴포넌트를 컨트롤 할 수 있다.

### 7.1 Prop으로 이벤트 전달

- 부모에서 정의된 이벤트 함수를 자식에게 Prop으로 내려준다.
- 자식에서 실행된 이벤트로 부모의 State가 바뀌게되면 부모의 모든속성은 다시 렌더링 된다.
- 자식이 부모를 컨트롤하는 부분

```
function Btn({style, text, changeClick }){
  console.info("value:", value);
	return <button style={style} onClick={changeClick} >{text}</button>
}

function App(){
  const [value, setValue] = useState("Save Change");
  const handleValeChange = () => setValue("Revert Change");
	return (
		<div>
			<Btn style={color:red} text={value} changeClick={handleValeChange} />
			<Btn style={color:blue} text={"Change"} />
		</div>
 )
}
```

### 7.2 React.memo()

- 그런데 위에 보면 부모의 상태값이 변경되어 부모의 모든 것들(속한 모든 컴포넌트)이 렌더링 된다.
- 만약 부모 컴포넌트가 1000개가 넘는다면 느려질 수 있다.
- memo를 통해 상태값이 변하지 않는 것은 렌더링이 안되도록 할 수 있다.

```
function Btn({style, text, changeClick }){
  console.info("value:", value);
	return <button style={style} onClick={changeClick} >{text}</button>
}

const Memorize = React.memo(Btn);
function App(){
  const [value, setValue] = useState("Save Change");
  const handleValeChange = () => setValue("Revert Change");
	return (
		<div>
			<Memorize style={color:red} text={value} changeClick={handleValeChange} />
			<Memorize style={color:blue} text={"Change"} />
		</div>
 )
} 
```

- 버튼을 클릭했을때 첫번째 버튼만 변경되고 두번재 버튼은 콘솔에 찍히지 않는걸 확인할 수 있다.
- 우리의 컴포넌트 렌더링할때 이를 컨트롤 할 수 있다.

---

## 8.3 PropTypes

<aside>
💡

TypeScript를 쓰면 타입체크는 PropTypes와 동일하게 체크해준다.

</aside>

### PropTypes 필요성

- 컴포넌트에 필요로 하는 Prop을 안빼뜨리고 이상하게 넣어주지 않기 위해서 → 에러 발생.  해결 찾는데 시간 소요 감소
- 어떤 타입의 prop을 받는지 체크해준다.
- 문법상 오류가 없더라도 체크를 통해 오류로그를 찍어줘서 실수를 줄여준다. 협업할때 좋다.
- 타입을 체크하는 것은 옵셔널이라 프롭 전달을 아예안하면 체크가 안된다.
- 그래서 특정 값은 필수로 들어오는지 체크하고 싶다면 isRequired를 쓰면된다.
- 특정 값은 옵셔널이 아니라 필수로 체크할 수 있다.

```
Btn.propTypes = {
	text: PropTypes.string,
	  fontSize: PropTypes.number.isRequired,
  only: PropTypes.oneOf(['News', 'Photos']),   // 딱 이 안에 있는 문자 두개중 하나여야만 
}
```

- 이 라이브러리는 설치를 필요로함
- 설치

```
npm i prop-types
```

- 설치확인은 package.json에서 확인
- 사용은 import PropTypes from "prop-types"

---

## 9. 배포 및 빌드

### 9.1 배포하기

- JSX 문법등은 웹브라우저가 사용하기 어려우니 → JS로 변환한다.
- build 하기

```jsx
npm run build
```

- build 파일 생성
- 먼저, 로컬 환경에서 웹서버 실행으로 테스트한다.
- 다음, npm 저장소에서 serve 프로그램 다운받고 방금 생성한 build라는 폴더에서 서버를 실행.

```jsx
npx serve build
```

- 서브 설치할거냐고 물어보면 y
- 잘 실행되면 주소 알려준다. 접속해서 잘 동작하는지 테스트 한다.
- 같은 공유기의 접속자에게는 url 주소공유가 가능하다.

### 9.2 build 살펴보기

- build 시 JSX 문법으로 작성된 JS 파일들은  transpiling 되어 웹이 읽을 수 있는 JS 파일로 변환된다.
- 변환된 JS 파일들은 Bundling을 통해 여러개의 파일이 하나의 파일로 묶이게 된다.

---
