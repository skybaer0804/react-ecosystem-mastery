`Period` 2025/11/11 - `Last Update`2025/11/11

---

## Preact 소개 및 특징

- Preact는 React의 경량 대체제로, React와 유사한 API를 제공하면서 크기가 매우 작고 빠릅니다. (3kb gzipped)
- Uber, Lyft 같은 대형 기업에서 Preact를 사용해 모바일 퍼포먼스를 향상시킨 사례가 있습니다.
- 주요 장점은 빠른 렌더링 속도, 적은 메모리 사용, React 프로젝트와의 높은 호환성입니다.
- Preact는 Progressive Web App(PWA)에 적합하며, 빠른 초기 로딩과 상호작용 시간을 제공합니다.
- React와 달리 별도의 빌드 툴이 필요 없으며, JSX 대안으로 htm이라는 경량 문법을 사용합니다.

## React와 Preact 주요 차이점

- Preact는 React의 synthetic event 시스템 대신 브라우저 기본 이벤트 시스템을 사용해 크기와 성능을 최적화합니다.
- 클래스 네임 대신 HTML의 class 속성을 주로 사용합니다.
- Props와 State를 렌더 함수의 인자로 전달해 코드가 간결합니다.
- React의 대부분 기능과 호환되지만, PropTypes와 Synthetic Event 같은 일부 기능은 없습니다.
- React 코드 대부분을 수정 없이 Preact로 전환 가능하며, preact/compat를 이용해 호환성을 높일 수 있습니다.

## 초기 세팅 방법

- npm에서 preact-cli를 설치해 프로젝트를 생성하고, 개발 서버를 실행합니다.
- 기존 React 프로젝트는 preact/compat 설치로 쉽게 Preact로 전환 가능합니다.

## 주요 성능 차트 및 벤치마크 (2025년 기준)

| **라이브러리** | **미니파이 크기** | **gzip 크기** | **LCP (최대 콘텐츠 표시 시간)** | **INP (입력 지연 시간)** |
| --- | --- | --- | --- | --- |
| React 16.14.0 | 133.16 kB | 41.92 kB | 1.83초 | 48ms |
| React 17.0.2 | 134.15 kB | 43.45 kB | 1.82초 | 48ms |
| React 18.3.1 | 143.87 kB | 46.34 kB | 1.88초 | 48ms |
| React 19.0.0 | 186.49 kB | 58.96 kB | 1.94초 | 40ms |
| Preact 10.25.4 | 16.26 kB | 6.84 kB | 1.71초 | 16ms |

- Preact의 번들 크기와 로딩 성능이 React보다 현저히 우수함을 보여줍니다.
- Preact는 가벼운 앱에서 빠른 렌더링과 낮은 메모리 사용으로 특히 빛을 발합니다.
- React는 복잡한 대규모 애플리케이션에서 고급 최적화 기능으로 강점을 가지지만, 초기 로딩 속도는 Preact에 비해 다소 느립니다.
