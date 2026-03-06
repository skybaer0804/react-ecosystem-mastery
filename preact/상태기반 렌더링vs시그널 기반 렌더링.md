> 원문: [State-based vs Signal-based rendering](https://jovidecroock.com/blog/state-vs-signals/)
> 

프런트엔드 프레임워크에서 상태 관리를 떠올리면, 우리는 흔히 훅(hooks), 옵저버블(observables), 시그널(signals) 같은 API의 형태에 집중하곤 합니다. 하지만 더 근본적인 변화가 일어나고 있습니다. 바로 `렌더링이 어디서 일어나느냐` 에 대한 관점입니다. 기존의 리액트 훅 기반 상태 관리는 상태를 생성한 지점에서 렌더링이 발생하는 반면, Preact나 Solid.js 같은 시그널 기반 접근 방식에서는 상태를 소비하는 지점에서만 렌더링이 발생합니다.

즉, "상태를 만든 곳에서 렌더링" 하던 방식에서 "상태를 사용하는 곳에서 렌더링"하는 방식으로의 전환은 성능, 코드 구조, 그리고 사고방식 전반에 큰 변화를 가져옵니다.

## [**핵심적인 차이**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%ED%95%B5%EC%8B%AC%EC%A0%81%EC%9D%B8%20%EC%B0%A8%EC%9D%B4-1)

기존 리액트 훅 기반의 상태 관리에서는 `useState`를 호출하면, 그 상태가 업데이트될 때 해당 컴포넌트뿐 아니라 모든 하위 컴포넌트까지 함께 리렌더링이 일어납니다. 하위 컴포넌트가 실제로 그 상태를 사용하고 있는지는 중요하지 않습니다. 단지 상태를 가진 컴포넌트의 자식이라는 이유만으로 렌더링의 파도에 휩쓸리게 되는 것이죠.

```
const Parent = () => {
  const [count, setCount] = useState(0);
  return (
    <>
      {/* count 값을 사용하지 않더라도 리렌더가 됨 */}
      <ChildA />
      <ChildB />
      {/* 실제로 count 값을 사용하고 리렌더가 됨 */}
      <ChildC count={count} />
    </>
  );
};
```

**시그널 기반 렌더링에서는 이 패러다임이 완전히 뒤집힙니다. 시그널은 스스로의 의존성을 추적하는 반응형(reactive) 기본 단위입니다. 시그널을 생성하더라도, 그 시점에서 렌더링이 발생하지는 않습니다. 대신 해당 시그널의 값에 실제로 `접근`하는 컴포넌트에서만 렌더링이 일어납니다.**

```
const Parent = () => {
  const count = useSignal(0);
  return (
    <>
      {/* 리렌더가 되지 않음 */}
      <ChildA />
      <ChildB />
      {/* count 값을 읽는 경우에만 리렌더 됨 */}
      <ChildC count={count} />
    </>
  );
};
```

이처럼 세밀한 반응성(granular reactivity) 덕분에, 시그널이 업데이트되더라도 정확히 그 시그널에 의존하는 컴포넌트만 리렌더링됩니다. 이제 사고방식도 바뀝니다. "불필요한 리렌더링을 막자"에서 "필요한 곳에서만 리렌더링이 일어난다"로 전환되는 것이죠.

## [**컨텍스트(Context): 패러다임 전환이 더 극명해지는 순간**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8(Context)%3A%20%ED%8C%A8%EB%9F%AC%EB%8B%A4%EC%9E%84%20%EC%A0%84%ED%99%98%EC%9D%B4%20%EB%8D%94%20%EA%B7%B9%EB%AA%85%ED%95%B4%EC%A7%80%EB%8A%94%20%EC%88%9C%EA%B0%84-1)

이 차이는 컨텍스트 API를 사용할 때 더욱 뚜렷하게 드러납니다. 리액트에서는 상태를 컨텍스트로 전달하고 그 값을 업데이트하면, 그 컨텍스트를 구독하고 있는 `모든 컴포넌트`가 일괄적으로 리렌더링됩니다. 문제는, 그 컴포넌트들이 실제로 변경된 값을 읽고 있든 아니든 상관없이 모두 리렌더링이 일어난다는 점입니다.

```jsx
const CountContext = createContext();

const Provider = ({ children }) => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");
  return <CountContext.Provider value={{ count, name, setCount, setName }}>{children}</CountContext.Provider>;
};

const ComponentA = () => {
  const { name } = useContext(CountContext);
// 오직 name 값만 사용하고 있지만,// count가 변경되었을때 리렌더 됩니다.return <div>{name}</div>;
};
```

시그널을 컨텍스트와 함께 사용하면, 반응성은 마치 외과 수술처럼 정교해집니다. 컨텍스트 안에 시그널을 저장할 수 있고, 그 시그널의 `.value`를 실제로 호출하는 컴포넌트만 업데이트에 구독하게 됩니다. 즉, 시그널을 읽는 곳만 다시 렌더링되는 것이죠.

```jsx
const CountContext = createContext();

const Provider = ({ children }) => {
  const count = useSignal(0);
  const name = useSignal("");
  return <CountContext.Provider value={{ count, name }}>{children}</CountContext.Provider>;
};

const ComponentA = () => {
  const { name } = useContext(CountContext);
// count가 변경될 때는 영향이 없고,// name이 바뀔 때만 리렌더링됩니다.return <div>{name.value}</div>;
};
```

이 접근 방식은 대규모 애플리케이션에서 컨텍스트를 통해 상태를 여러 컴포넌트에 분산시킬 때 완전히 판을 바꾸는 개념입니다. 더 이상 불필요한 리렌더링을 막기 위해 컨텍스트를 여러 개로 쪼개거나, 복잡한 최적화 패턴을 적용할 필요가 없습니다.

이제 컨텍스트는 상태를 배포하고 렌더링을 조율하는 수단이 아니라, 단순히 의존성을 주입하는(Dependency Injection) 도구로만 사용됩니다.

## [**렌더링 전파(Rendering Propagation)**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EB%A0%8C%EB%8D%94%EB%A7%81%20%EC%A0%84%ED%8C%8C(Rendering%20Propagation)-1)

이제 컴포넌트 트리를 기준으로 리렌더링이 어떻게 전파되는지 시각적으로 살펴보겠습니다.

### [**상태 기반 렌더링 (리액트 훅)**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%83%81%ED%83%9C%20%EA%B8%B0%EB%B0%98%20%EB%A0%8C%EB%8D%94%EB%A7%81%20(%EB%A6%AC%EC%95%A1%ED%8A%B8%20%ED%9B%85)-1)

상태 기반 렌더링에서는 상태가 업데이트될 때, 그 상태가 생성된 지점부터 시작해 해당 하위 트리 전체가 리렌더링됩니다. 이로 인해 불필요한 연산이 발생하기 쉬우며, 이를 방지하려면 `React.memo`, `shouldComponentUpdate`, `useMemo`, `useCallback` 같은 수동 최적화 기법을 직접 적용해야 합니다.

![](https://blog.kakaocdn.net/dna/KCO2W/dJMcaaDBeWW/AAAAAAAAAAAAAAAAAAAAAIpObMWMTYfv0NskNcXsQeICnBbiGxlzqyUse3GxEcp-/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1764514799&allow_ip=&allow_referer=&signature=3lk0UmRs6sFmqVudurirR36jn0M%3D)

모든 하위 컴포넌트(빨간색으로 표시된 부분)가, 그 상태를 실제로 사용하지 않더라도 전부 리렌더링됩니다. 사실상 업데이트가 필요한 것은 `GC 2` 단 하나뿐이지만, `Child 1`, `Child 2`, `Child 3`, `GC 1`, `GC 3`까지 모두 불필요하게 다시 렌더링되는 것이죠.

### [**시그널 기반 렌더링 (Preact Signals / Solid.js)**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%8B%9C%EA%B7%B8%EB%84%90%20%EA%B8%B0%EB%B0%98%20%EB%A0%8C%EB%8D%94%EB%A7%81%20(Preact%20Signals%20%2F%20Solid.js)-1)

시그널 기반 렌더링에서는 실제로 시그널의 값을 읽는 컴포넌트만 리렌더링됩니다. 컴포넌트 계층 구조는 전혀 중요하지 않습니다. 핵심은 `컴포넌트가 어디에 위치하느냐`가 아니라, `어떤 데이터에 의존하느냐` 입니다.

![](https://blog.kakaocdn.net/dna/beq4kA/dJMcagqhx6c/AAAAAAAAAAAAAAAAAAAAAKtDEteQgbyEp-Q1ggW4Gajdn1984uVBXN-hbRld5_9B/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1764514799&allow_ip=&allow_referer=&signature=IL%2FUwRB37dnjeM7PrYiSCqJuzUY%3D)

`signal.value`를 실제로 참조하는 `GC 2`(초록색으로 표시된 부분)만 리렌더링됩니다. 같은 컴포넌트 트리 안에 있더라도 다른 컴포넌트들(회색으로 표시된 부분)은 전혀 변경되지 않습니다.

## [**제어 흐름을 통한 세밀한 렌더링 제어**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%A0%9C%EC%96%B4%20%ED%9D%90%EB%A6%84%EC%9D%84%20%ED%86%B5%ED%95%9C%20%EC%84%B8%EB%B0%80%ED%95%9C%20%EB%A0%8C%EB%8D%94%EB%A7%81%20%EC%A0%9C%EC%96%B4-1)

Preact는 `Show`, `For`와 같은 제어 흐름용 컴포넌트를 통해 이 개념을 한 단계 더 확장합니다. 이런 컴포넌트들은 반응성의 범위를 훨씬 더 정밀하게 한정(scope) 하여, 필요한 부분만 최소 단위로 다시 렌더링할 수 있게 해줍니다.

```
const items = signal([]);
// items 시그널이 업데이트 되었을 때, 오직 영향을 끼치는 items만 리렌더됩니다.
<For each={items}>
  {(item) => (
    <div>
      {/* item.value가 변경되었을 때 오직 이 아이템만 리렌더됩니다. */}
      <span>{item.name.value}</span>
      <button onClick={() => item.count.value++}>{item.count.value}</button>
    </div>
  )}
</For>;
```

기존의 훅 기반 방식에서는 리스트의 한 항목이 변경되면, 그 항목뿐 아니라 형제 컴포넌트, 부모 컴포넌트, 심지어 다른 자식 컴포넌트들까지 리렌더링이 전파될 수 있습니다. 이 문제를 막으려면 모든 부분을 꼼꼼히 메모이제이션 처리해야 하죠.

반면, For나 Show 같은 제어 흐름 컴포넌트는 시그널의 리렌더링 범위를 해당 JSX 블록 내부로만 제한합니다. 즉, 파생된 computed 시그널이든 단순한 시그널이든, 그 값을 사용하는 JSX 자식만 다시 렌더링되는 것입니다.

## [**성능에 미치는 영향**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%84%B1%EB%8A%A5%EC%97%90%20%EB%AF%B8%EC%B9%98%EB%8A%94%20%EC%98%81%ED%96%A5-1)

이러한 패러다임의 전환은 단순한 개념 변화에 그치지 않습니다. 실제 성능 측면에서도 분명한 차이를 만들어냅니다.

- `연산량 감소`: 리렌더링되는 컴포넌트가 줄어들면, 실행되는 자바스크립트의 양도 그만큼 감소합니다. 상태 변경과 무관한 컴포넌트에서는 render() 함수 호출, 가상 DOM 비교(diffing), useEffect 재실행 등이 전혀 일어나지 않습니다.
- `번들 크기 감소`: `React.memo`, `shouldComponentUpdate`, `useMemo`, `useCallback`**같은 메모이제이션 헬퍼들이 필요하지 않습니다. 프레임워크 자체의 반응형 시스템이 이미 자동으로 최적화를 처리하니까요. 결과적으로 애플리케이션 코드가 간결해지고 번들 크기도 작아집니다.**
- `예측 가능한 성능`: **어떤 부분이 다시 렌더링될지는 컴포넌트 트리 구조가 아니라, 시그널이 읽히는 위치에 의해 결정됩니다. 즉, 성능의 변동성이 줄어들고 디버깅이 훨씬 쉬워집니다. "어떤 컴포넌트가 업데이트되었는가?"를 알고 싶다면 시그널이 어디에서 읽혔는지만 추적하면 됩니다.**
- `prop drilling이 사라짐`: **시그널은 컨텍스트를 거쳐 전달하거나, 심지어 직접 import 해서 사용할 수도 있습니다. 이 과정에서 불필요한 리렌더링이 발생하지 않습니다. 따라서 리액트에서 흔히 겪는 복잡한 Provider 피라미드도 만들 필요가 없습니다.**

## [**언제 상태 기반 렌더링이 더 적합한가**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EC%96%B8%EC%A0%9C%20%EC%83%81%ED%83%9C%20%EA%B8%B0%EB%B0%98%20%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4%20%EB%8D%94%20%EC%A0%81%ED%95%A9%ED%95%9C%EA%B0%80-1)

물론 상태 기반 렌더링이 본질적으로 나쁘다는 뜻은 아닙니다. 규모가 작은 컴포넌트나, 리렌더링 비용이 거의 없는 애플리케이션이라면 훅 기반 모델만으로도 충분히 간단하고 실용적입니다. 수십 개 정도의 컴포넌트가 리렌더링되는 정도라면, 그 비용은 대부분의 경우 거의 무시할 수준이죠.

이 트레이드오프는 다음과 같은 상황에서 확연히 두드러집니다.

- 규모가 크고 중첩이 깊은 컴포넌트 트리
- 업데이트 빈도가 높은 애플리케이션 — 예: 애니메이션, 실시간 데이터 처리
- 상태가 여러 컨텍스트나 전역 상태로 복잡하게 분산된 애플리케이션의 경우

## [**결론**](https://imnotadevleoper.tistory.com/374?utm_source=substack&utm_medium=email#%EA%B2%B0%EB%A1%A0-1)

상태 기반 렌더링에서 시그널 기반 렌더링으로의 전환은 단순한 성능 최적화 이상의 의미를 가집니다. 이는 반응성을 바라보는 사고방식 자체의 전환입니다. 이제 우리는 리렌더링을 메모이제이션으로 '막는' 것이 아니라, `필요한 곳에서만 '발생시키는' 방식`으로 사고해야 합니다.

이러한 전환, 즉 "상태를 생성하는 곳에서 렌더링"에서 "상태를 사용하는 곳에서 렌더링"으로의 전환은 코드를 실제 데이터 흐름에 더 가깝게 만듭니다. 이 접근 방식은 기본적으로 애플리케이션을 더 빠르게 만들고, 개발자가 이해해야 하는 정신적 모델도 훨씬 단순화시킵니다. 시그널을 읽는다면, 그 값이 바뀔 때 리렌더링됩니다. 읽지 않는다면, 아무 일도 일어나지 않습니다.

Preact Signals와 Solid.js 같은 프레임워크가 보여주듯, 이는 단순한 이론상의 개선이 아니라 실질적인 생산성 향상으로 이어지는 변화입니다. 프론트엔드 반응성의 미래는 정교한 단위 제어에 있으며, 그 미래는 이미 우리 앞에 와 있습니다.

## React, Next.js 프레임워크에서 preact 사용

useSignal은 React에서 기본적으로 제공하는 API가 아닙니다. useSignal은 주로 Preact, Qwik, Solid.js 같은 리액티브 프레임워크에서 제공되는 개념입니다. 리액트에서 상태 관리는 useState, useReducer 등 기존의 훅을 통해 이루어지지만, 시그널(signal) 기반 패러다임은 리액트 생태계에서는 아직 기본 탑재가 아닙니다.

단, Preact 팀이 제공하는 "@preact/signals-react" 같은 별도 라이브러리를 통해 리액트에서 시그널 패턴을 사용할 수 있도록 지원하는 방식이 존재합니다. 이런 경우에도 이는 리액트 자체의 기본 훅은 아니며, 별도의 외부 라이브러리를 설치해서 사용하는 것입니다.

따라서 useSignal은 React의 내장 API가 아닌, 외부 라이브러리(Preact Signals 등)에서 제공하는 별도의 기능입니다.

React와 Next.js 프레임워크 환경에서 Preact가 제공하는 @preact/signals-react 라이브러리를 설치해 사용하는 경우, 기본적으로 충돌 문제는 없으나 몇 가지 주의점과 제한사항이 있습니다.

- @preact/signals-react는 React 환경에서 시그널 기반 상태 관리를 구현하기 위한 별도의 라이브러리로, React의 기본 상태 관리 훅과는 별개입니다. 리액트 컴포넌트 내에서 시그널을 사용하려면 이 라이브러리를 설치해야 하며, Preact의 내장 시그널을 직접 사용하는 것은 React와 호환되지 않습니다.
- 일부 Next.js 버전(특히 appDir 사용하는 Next.js 13 이상)에서는 클라이언트 컴포넌트와 시그널 사용 간 호환 이슈가 보고되며 완벽한 작동을 위해 별도의 설정이나 제약이 있을 수 있습니다. 예를 들어, Next.js에서 여러 개의 클라이언트 컴포넌트가 존재할 때 시그널 상태 동기화에 문제가 발생하는 사례가 있습니다.
- React와 Next.js는 내부적으로 React 자체 상태 관리와 렌더링 최적화 로직을 갖고 있기 때문에 @preact/signals-react 사용 시에도 React 컴포넌트 트리와 상태 동기화에 충돌은 없으나, 시그널 상태를 직접 가변값으로 다룰 때 발생할 수 있는 타입 문제나 업데이트 지연 등의 문제를 코드로 적절히 처리해야 합니다.

요약하면, @preact/signals-react는 React·Next.js 환경에서 설치해 쓸 수 있는 공식 라이브러리이며, 호환성 문제는 크지 않지만 Next.js 특정 버전 및 구조에서는 제한이 있을 수 있으므로 테스트 후 도입하는 것을 권장합니다. 충돌 방지를 위해 React용 공식 시그널 라이브러리를 반드시 따로 설치해서 사용해야 하며, 직접 Preact 시그널 코어를 쓰는 방식을 피하는 것이 좋습니다.

즉, React와 Next.js에서 @preact/signals-react 설치 후 적절히 사용하면 큰 충돌은 없지만, Next.js의 최신 기능(appDir) 사용 시 주의가 필요합니다.

### npmtrends에서 preact 추이

https://npmtrends.com/preact
