# 10분 넥스트

getting started 따라가며 nextjs 찍먹하기.

### ToC

- Create a Next.js App
- Navigate Between Pages
- Assets, Metadata, and CSS
- Pre-rendering and Data Fetching
- Dynamic Routes
- API Routes
- Deploying Your Next.js App

## Create a Next.js App

넥스트는 리액트 프레임워크이고, 많은 문제를 해결하기 위한 방법을 제공한다.

- 페이지 기반의 직관적인 라우팅
- 페이지 기반의 프리렌더링 기능 ( SSG, SSR )
- 빠른 페이지 로드를 위한 코드 스플리팅
- 최적화된 프리페칭을 이용한 클라이언트 사이드 라우팅
- 내장된 CSS, Sass 기능과, CSS-in-JS에 대한 지원.
- 패스트 리프레시를 포함한 개발환경
- 서버리스 함수를 위한 엔드포인트를 만들 수 있는 API 라우팅
- 엄청난 확장성

### 설치하기

create-next-app 으로 설치하고 dev 명령어로 로컬 실행

```bash
npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter"

cd nextjs-blog

yarn dev
```

## 페이지 네비게이션

넥스트에서는 `Link` 컴포넌트로 페이지 간 네비게이션을 지원한다.

```jsx
import Link from "next/link";

...
    <h1 className="title">
        Learn
        <Link href="/posts/first-post">
            <a className="some-class">Next.js!</a>
        </Link>
    </h1>
...

```

이렇게 `a` 태그를 `Link`로 래핑해주면 된다.

그냥 `a` 태그를 사용할 때와 비교해서 `Link`를 사용 했을때 얻을 수 있는 이점이 몇가지 있다.

- `a` 태그는 새로 앱에 접근하게 되어서 처음 js 번들부터 리소스를 다시 불러와야하지만, `Link`는 변화한, 필요한 부분만 불러오면 된다.
- 그래서 화면 전환도 빠르다
- 넥스트 자체에서 `Link`가 뷰포트에 드러나면 해당 페이지를 프리페칭 하기 때문에 더더욱 빠른 전환이 가능하다.

## 애셋, 메타데이터, CSS

정적 데이터는 `public` 디렉토리 하위에 위치시킨다.

### 이미지

img 또한 Next가 알아서 optimize를 제공하는 Image 컴포넌트가 존재한다.

### 메타데이터

`head`의 내용을 수정하기 위해 넥스트에서 `Head` 컴포넌트를 제공하고, 이는 페이지별로 사용해서 오버라이딩 할 수도 있는 듯. 그리고 `_documents` 라고 전역적으로 설정하는 방법도 있는 듯.

### 서드파티 사용하기

서드파티를 사용하는 스크립트 또한 `Head` 속에 작성할 수 있는데, 이또한 로드를 최적화하기 위한 `Script` 컴포넌트를 제공한다.

### CSS

넥스트는 기본적으로 CSS, Scss 그리고 CSS-in-JS 인 `styled-jsx` 를 지원한다. 원한다면 styled-component나 emotion 등도 이용할 수 있다.

### 레이아웃 컴포넌트

여기저기 공통 컴포넌트를 만들고, 스타일을 적용하더라도, 넥스트 CSS 모듈이 className에 프리픽스를 만들어서 유니크할 수 있게 만들어준다.

### 글로벌 스타일

넥스트 CSS 모듈 시스템은 `[component].module.css` 라는 네이밍 컨벤션을 가지는 모듈기반 css 를 지원하며 그 외의 글로벌 css는 오직 `pages/_app.js` 에서만 import 할 수 있다.

### 스타일링 팁

...

## 프리렌더링과 데이터페칭

### | 프리렌더링

프리렌더링이란 넥스트가 이미 렌더 된 HTML을 내려준다는 뜻이다. 그래서 플레인 리액트 앱과 달리 JS 를 disable 하더라도 빈 화면을 보여주는게 아니라, 만들어진 HTML을 볼 수있다. 그 후에 js가 실행되면서 화면을 인터랙티브하게 만드는게 hydration 이다.

### 두가지 프리렌더링 방식

Static Site Generation과 Server Side Rendering 두가지.

SSG는 빌드타임에 HTML을 만들어서 매 요청때 재사용하는거고, SSR은 매 요청때마다 HTML을 만들어서 내려주는 것. ( 개발 환경에서는 모든 페이지가 SSR로 프리렌더링 된다.)

넥스트는 사용자가 자유롭게 둘 중 선택하도록 해줌.

빌드된 HTML을 서빙하는 편이 당연히 퍼포먼스상에서 우위에 있으므로 가능하다면, SSG 를 선택하는걸 추천한다. 사용자의 요청 전에 페이지를 만들 수 있다면 SSG를 그렇지 않다면 SSR을 사용하면 된다?

### SSG

대부분의 페이지들은 외부의 데이터를 불러올 필요가 없고, 이런 페이지들은 빌드타임에 생성된다. 간혹 외부 데이터 없이는 최초의 HTML 렌더가 어려운 경우를 위해 넥스트는 빌드타임에 외부 데이터를 참조하기위한 도구를 제공한다. 페이지에서 컴포넌트 뿐만 아니라 `getStaticProps`라는 async function을 export하면 넥스트가 빌드타임에 이를 실행하고, 페이지에 그 결과를 props로 넘긴다.

이 `getStaticProps`는 빌드타임에 실행되고 클라이언트에게는 전달되지 않는다. 그래서 디비 조회도 해도 되고, api 콜도 해도 되고 하는데, 다만 동적으로 쿼리 파라미터를 받는다던가 그런걸 위한 용도가 아니다. 블로그 포스트 느낌으로다가....

### SSR

동적으로 데이터를 받아서 프리렌더를 하고싶다면 SSR을 사용해야한다. 이때는 `getServerSideProps`를 export 하면 된다. `getStaticProps` 는 매 요청마다 실행되고, 해당 요청의 컨텍스트를 인자로 갖는다. 매 요청마다 실행되기 때문에 TTFB가 SSG에 비해 느리고 결과 값도 CDN에서 캐싱되지 않는다.

## 다이나믹 라우트
