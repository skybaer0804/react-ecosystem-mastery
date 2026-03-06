# Virtual DOM

Virtual DOM은 React의 핵심 개념 중 하나로, 실제 DOM의 가상 표현입니다. React는 Virtual DOM을 사용하여 UI 변경 사항을 효율적으로 관리합니다.

## 주요 특징
- 메모리에 존재하는 실제 DOM의 복사본
- 변경 사항을 배치 처리하여 성능 최적화
- Reconciliation 과정에서 사용됨

## 작동 방식
1. 컴포넌트 상태 변경 시 Virtual DOM 생성
2. 이전 Virtual DOM과 비교 (Diffing)
3. 실제 DOM에 최소한의 변경만 적용

## 장점
- 빠른 UI 업데이트
- 효율적인 메모리 사용
- 플랫폼 독립성