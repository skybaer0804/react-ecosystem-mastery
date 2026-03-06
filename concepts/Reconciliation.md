# Reconciliation

Reconciliation은 React가 Virtual DOM의 변경 사항을 실제 DOM에 적용하는 과정을 말합니다. 이를 통해 React는 UI를 효율적으로 업데이트합니다.

## 과정
1. **Diffing**: 이전 Virtual DOM과 새로운 Virtual DOM을 비교
2. **Reconciliation**: 변경된 부분을 찾아 실제 DOM에 적용
3. **Commit**: 변경 사항을 실제 DOM에 반영

## 알고리즘
- 같은 타입의 요소는 속성만 비교
- 다른 타입의 요소는 완전히 교체
- 리스트 항목은 key를 사용하여 효율적으로 비교

## 최적화
- React Fiber: Reconciliation을 비동기로 처리
- 메모이제이션: 불필요한 재렌더링 방지