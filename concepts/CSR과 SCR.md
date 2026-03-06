---

update : 2023-11

### 💡 웹 애플리케이션의 역사

MPA(Muti Page Application)

: 다중 페이지로 이뤄져있어 변경사항이 있을 때마다 서버로 페이지를 요청

초기 방식으로 서버로부터 새로운 html 파일을 받으면서 새로고침시 깜빡임 발생.

사진, 영상, 인터랙션이 많이 생기며 복잡도가 생기며 성능 문제 발생

AJAX

필요한 부분만 리로드 할 수 있게 해줌

SPA(Single Page Application)

: 단일 페이지로 이루어져 있어 갱신될 부분에 대해서만 데이터를 요청

### CSR(Client Side Rendering)

클라이언트 측에서 렌더링을 담당하는 것

첫 로딩에 빈 html 파일에 필요한 번들파일을 모두 다운받는 방식.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a9a4786b-322e-4eda-85ff-d941ae073b9c/78f05fc2-039c-4082-a0f1-660364352dcb/Untitled.png)

👍 TTFB (Time to First Byte)

요청을 보내고 응답을 첫번째 바이트가 동작하기 까지의 시간이 좋음

👎 FCP (First Contentful Paint)

사용자가 화면에서 콘텐츠를 볼 수 있는 페이지 로드 타임라인의 첫 번째 지점이 안좋음

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a9a4786b-322e-4eda-85ff-d941ae073b9c/b60f6e96-0408-43bc-bccd-432781547078/Untitled.png)

### SSR(Server Side Rendering)

서버 측에서 렌더링 될 페이지를 그려서 클라이언트에 내려주는 방식

요청 시 즉시 HTML 파일을 만드느냐 아니면 미리 HTML 파일을 만들어 두다가 제공하냐에 따라 방식이 나뉜다.

SSG (Static Site Generation) 

여러 사용자가 동일한 컨텐츠를 제공하는 정적 페이지일 경우 사용하면 적합하다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a9a4786b-322e-4eda-85ff-d941ae073b9c/78e23512-0e9e-4479-b408-845d68728cbd/Untitled.png)

👍 FCP (First Contentful Paint)

👎 TBT (Total Blocking Time)

FCP로부터 TTI(Time To Interact) 사이의 시간을 의미하는데

TTI는 사용자가 페이지에서 상호작용을 가능하기까지의 시간을 의미한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a9a4786b-322e-4eda-85ff-d941ae073b9c/33480d63-0299-40cb-a61a-90454e8836a8/Untitled.png)

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

CSR + SSR (Universal Rendering)

초기 렌더링은 SSR 방식 사용하고 다른 페이지를 이동할 때는 CSR 방식을 활용하는것이 좋다.

### ✅ 출처

[[10분 테코톡] 타미의 CSR과 SSR](https://www.youtube.com/watch?v=TXzwuaXQN2U&list=PLN55uUeRUitkrUJI6L7GuTghLp8YyDXnZ)
