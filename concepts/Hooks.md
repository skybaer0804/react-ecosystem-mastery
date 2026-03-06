# Hooks

Hooks는 React 16.8에서 도입된 기능으로, 함수형 컴포넌트에서 상태 관리와 생명주기 기능을 사용할 수 있게 해줍니다.

## 기본 Hooks
- **useState**: 컴포넌트 상태 관리
- **useEffect**: 사이드 이펙트 처리 (componentDidMount, componentDidUpdate, componentWillUnmount 대체)
- **useContext**: Context API 사용

## 추가 Hooks
- **useReducer**: 복잡한 상태 로직 관리
- **useCallback**: 함수 메모이제이션
- **useMemo**: 값 메모이제이션
- **useRef**: DOM 참조 또는 값 유지

## 규칙
- 최상위 레벨에서만 호출
- React 함수 내에서만 사용
- 조건문이나 반복문 안에서 사용하지 않음

## 장점
- 클래스 컴포넌트 불필요
- 코드 재사용성 향상
- 로직 분리 용이