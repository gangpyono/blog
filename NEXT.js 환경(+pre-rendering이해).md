
원본 링크 : https://yess.tistory.com/4

pre-rendering에 대해 공부하다 환경에 대해 이해하고 좀 더 와닿아서 공유해보려 한다. 우선 환경에는 다음과 같은 차이점들이 있다.

## 1\. 실행 방법 차이

실행방법으로는 다음과같은 차이가 있다.

```
// development모드 실행
npm run next dev 

// production모드 실행
npm run next build 
npm run next start
```

우선 development환경에서는 별도의 빌드 과정이 생략되어있다. 그렇다고 빌드 결과물인. next폴더가 생성되지 않는 건 아닌데, 폴더를 구성하는 파일이 다르다. 파일 구성에 대해 선 밑에서 다루겠다. 

## 2\. 지원하는 기능차이

###  development 환경

development환경은 말 그대로 개발자가 개발을 편하게 할 수 있도록 해주는 환경을 의미한다. 따라서 최적화가 개발자를 위해 구성되어있는데, Fast Refresh가 대표적이다.

#### Fast Refresh란

컴포넌트 단위에서의 수정사항이 발생했을 때 빠른 속도로 수정된 사항이 컴포넌트의 상태를 유지한 채로 뷰에 반영되는 것을 말한다. (리액트 프로젝트를 cra환경에서 작업을 할 때 파일 사이즈가 커질수록 점점 수정사항이 뷰에 반영되는 시간이 오래 걸렸는데 이 부분을 해결해 줄 수 있을 것 같다)

###  production환경

production환경은 실제 유저가 사용하기에 최적화되어있는 환경으로써, 배포 전의 build결과물을 실행해보는 환경을 의미한다. 최적화 과정으로 다음의 과정을 거치게 된다.

\- compiled : JSX문법, 최신 JS문법, 타입 스크립트 등이 브라우저가 해석할 수 있는 수준의 JS로 변환하는 과정이다.

\- bundled : 브라우저의 요청 횟수를 줄이기 위해 여러 파일을 하나로 합치며 최적화시키는 과정.

\- minified : 파일에 작성된 코드들의 기능적인 부분을 유지한 채로 해석하는데 불필요한 정보들(공백, 띄어쓰기, 주석 등등)을 없애주는 과정.

\- code split : 초기 로딩 속도를 높이기 위해 현 페이지에 필요한 정보만을 담은 파일(chunk.js)로 하나의 bundle로부터 분리시키는 과정.

이 모든 최적화를 빌드 과정을 통해 지원해준다.(npm run next build) 이후 실행단계를 거쳐 실제로 확인해 볼 수 있다.

## 3\. 빌드 폴더 구성 차이( + pre-rendering원리)

우선 왼쪽부터 기본 폴더구조, development모드 빌드 결과물, production모드 빌드결과물  순이다.

물론 다른 폴더도 있고 파일들도 있지만, pre-rendering을 이해하는데 도움이 되었던 폴더들만 소개해보겠다.
<img width="792" alt="스크린샷 2022-03-10 오후 10 46 11" src="https://user-images.githubusercontent.com/58588011/157674646-e97066aa-2923-4906-83d1-45664b7586df.png">



### 1). next/server/pages  –  pre-rendering을 진행할 js파일 & pre-rendering이 완료된 HTML 파일로 구성.

\-> next.js에선 기본적으로 pages폴더 경로에 있는 페이지(index.js, list 1.js, list 2.js)들을 pre-rendering 시킨다. 지원하는 방식으로는 SSG, SSR이 있는데, 이두가지 방식의 차이점은 빌드 과정에서 pre-rendering을 진행하는지(SSG), 아니면 매 요청 시에 pre-rendering을 진항하는지의 차이다.(SSR)

#### development 환경

하지만 production환경과 달리 development환경에선 빌드 결과물의. next/server/pages경로에  \_app.js,\_document.js,\_error.js 외엔 어떠한 파일도 존재하지 않는 걸 볼 수 있는데, 이는 개인적인 생각이지만 development환경 특성에 맞게(개발자 위주)  빌드 과정을 최소화하여 빠르게 화면을 띄울 수 있게 하기위함인것같다. 이는 development모드를 실행후 index.js 페이지에 접속하게되면, 그떄서야 SSR방식처럼 index.js파일이 생성되는걸 볼 수 있다. 이렇게 내가 원하는 페이지에대해서만 빌드과정을 적용하는 과정으로, 훨씬 더 개발자에게 맞춰진 환경으로 생각할 수 있을 것 같다.

#### production 환경

빌드 결과물의. next/server/pages경로로 HTML 파일(index.html, list 1.html)을 생성했다는 것은 이미 pre-rendering을 진행했다는 의미이므로 두 페이지는 SSG방식을 선택했음을 알 수 있고, list 2페이지는 그대로 list 2.js파일로 빌드되었으므로, 이는 SSR방식을 선택했음을 알 수 있다.

### 2). next/static/chunks/pages – 각 페이지를 구성하고 있는 js파일.

#### development 환경

첫 접속 시 생성하게 되는데 위와 동일한 이유라 생각된다.

#### production 환경

index페이지를 예로 들자면, index페이지를 이루고 있는 js코드들을 담고 있다(난독화 적용된 상태).

\- 버튼에적용되어있는 함수, 기타 js코드들..

## 4\. 결론

development환경은 개발자를 위한 환경, production환경은 사용자를 위한 환경이다. 따라서 pre-rendering 되는 과정이 각 환경에 따라 다르게 동작한다.
