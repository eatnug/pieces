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

정적 데이터는 `/public` 디렉토리 아래에 위치시키면 된다. 해당 디렉토리의 파일들은 `/pages` 디렉토리의 파일들과 같이, 어플리케이션 루트에서부터 참조할 수 있다. (`import image from '/images/some.jpg`)


### 이미지

이미지를 일반적인 `img` 태그로 다루려면 화면 크기에 대한 대응, 서드파티 최적화, 로드 시점 등을 직접 핸들링 해줘야하지만 `next/image` 의 `Image` 컴포넌트는 이런 작업들을 알아서 해결해준다.

### 메타데이터

`head`의 내용을 수정하기 위해서는 `next/head`의 `Head` 컴포넌트를 사용하면 된다. 

### JS 불러오기

서드파티를 위해 사용하는 스크립트를 불러오는 부분도 `Head` 속에 작성할 수 있는데, 이때도 최적화를 위한 `next/script`의 `Script` 컴포넌트를 쓸 수 있다.

### CSS

넥스트는 기본적으로 CSS-in-JS 라이브러리 `styled-jsx` 를 지원한다. 원한다면 `styled-component`나 `emotion` 등도 이용할 수 있다. 그리고 css, scss 를 기본적으로 지원하기 때문에 그냥 import 해서 사용할 수 있다.

### 레이아웃 컴포넌트 ( CSS 모듈 )

레이아웃 컴포넌트를 만들수도 있는데, 이렇게 되면 앱 전반에 걸쳐 className이 겹칠수도 있다. 이를 위해 넥스트는 컴포넌트레벨의 스타일시트를 만들 수 있도록 CSS 모듈을 제공한다. `[componentName].moudle.css` 이런 이름의 css 파일을 만들고, 특정 컴포넌트에서 해당 파일을 import 하면 css 파일을 모듈처럼 사용할 수 있고 (`<Component style={styleSheet.container} />`) 넥스트에서 모듈별로 유니크한 className을 만들어준다.

### 글로벌 스타일

넥스트의 CSS 모듈 시스템은 `[component].module.css` 라는 네이밍 컨벤션을 기반으로 동작하고, 컴포넌트 레벨에서 동작하기 때문에 전역적으로 다른 컴포넌트에 영향을 끼칠 수 없다. 글로벌 스타일을 선언하기 위해서는 넥스트가 컨벤셔널하게 사용하는 `pages/_app.js` 에 스타일을 import 해야한다. 이렇게 그냥 CSS 모듈의 컨벤션을 따르지 않는 스타일은 오직 `pages/_app.js` 에서만 사용 가능하다.

### 스타일링 팁

은 관심없어서 넘어간다.

## 프리렌더링과 데이터페칭

### | 프리렌더링

프리렌더링이란 말그대로 미리 렌더 한다는 뜻이다. 즉 클라이언트에게 전달되기 전에 먼저 HTML을 만든다는 뜻이다. 

클라이언트가 어떤 페이지를 요청할 때 넥스트는 텅 빈 HTML에 화면 전체를 다 그려야 하는 리액트 코드를 주는게 아니라, 어느정도 채워진 HTML에, 나머지 화면만 그리면 되는 리액트 코드를 준다. 이 나머지 화면을 그리는 과정을 hydration 이라고 한다.


### 두가지 프리렌더링 방식

프리렌더링에는 Static Site Generation과 Server Side Rendering 두가지 방법이 있다.  넥스트는 사용자가 자유롭게 둘 중 선택하도록 해주는데,
아무래도 빌드된 HTML을 서빙하는 편이 당연히 퍼포먼스상에서 우위에 있으므로 가능하다면, SSG를 선택하는걸 추천한다. 따라서 만약 사용자의 요청 전에 페이지를 만들 수 있다면 SSG를 사용하는 걸 우선으로하고 그렇지 않을 경우에 SSR을 선택하면 된다.

### SSG

SSG는 빌드타임에 말 그대로 static한 HTML을 생성하는 것이고, 그 페이지들을 매 요청때마다 재사용한다. ( 사실 개발환경에서는 매번 만든다. )

외부의 데이터에 의존하지 않아서 먼저 만들어질 수 있는 페이지들은 넥스트가 알아서 SSG 를 선택해 빌드타임에 HTML을 만들어준다.

만약 외부 데이터에 의존해, 이 데이터 없이는 최초 HTML 렌더가 어려울 경우에는 `getStaticProps` 를 사용하면 된다. 페이지에서 이 함수를 export 하면 빌드타임에 넥스트가 해당 함수의 반환값을 페이지의 props로 넘겨준다.

주의할 부분은 빌드타임에 이루어지고, 따라서 클라이언트에게 전달되지도 않고, 동적으로 유저의 사용 맥락에 따른 데이터를 활용할수는 없다는 점.

빌드타임에 마크다운을 읽어서 블로그 포스트 페이지를 만드는걸 생각해보자.

### SSR

만약 유저의 사용 맥락에 따른 데이터가 필요한 페이지가 있다면 어떻게 해야할까? SSR 을 이용하면 된다. 유저의 요청이 들어올 때 그 맥락에서의 데이터를 획득한 다음 해당 페이지를 만들어서 내려보내줄 수 있다.

이때는 `getServerSideProps`를 `getStaticProps`와 유사하게 사용하면 되는데, 다만 이때 사용자의 요청정보(context)를 인자로 받는다. 물론 SSR은 요청이 들어올 때 화면을 만들기 때문에 TTFB가 SSG에 비해 느리고 결과 페이지가 CDN에 캐싱되지도 않는다. 

( 그리고 사실 그냥 CSR를 써도 되긴 한다. )

## 다이나믹 라우트

페이지 이름을 `/pages/someRoute/[dynamicValue].js` 이렇게 지정하면 `/someRoute/somePage` 에 매치되어서 동적인 라우트를 만들 수 있다. 그리고 이를 SSG와 함께 적용하기 위해서 `getStaticPath` 라는 함수를 export 할수도 있다.

`getStaticPath`는 `[dynamicValue]`로 넘어올 수 있는 모든 값의 목록을 반환한다. `getStaticProps` 처럼 빌드타임에 동적 라우트들이 생길 수 있는지 확인하는 작업인 듯.

## API 라우트

`/pages/api`  디렉토리에서 다음 같은 모듈을 만들어서 넥스트 앱에 api 라우트를 만들 수 있다. 

```ts
// req = HTTP incoming message, res = HTTP server response
export default function handler(req, res) {
  // ...
}
```

주의할 점은 이들은 앱의 런타임에 존재하기 때문에 `getStatic~` 함수들에서 요청을 보내면 안된다는 것.

## 배포하기

Vercel 을 쓰면 쉽게 할 수 있도록 만들어둔 듯 하다. 기본적으로는 앱을 bulid 하고 start 하면 사용자 요청에 맞게 HTML을 서빙하는, 필요하다면 SSR 작업도 하는, 그리고 API 라우트도 달려있는 서버를 띄운다. 

하나 재밌는건 export 라는 기능을 통해 만약 가능하다면 (SSR이나 몇몇 기능들이 사용되지 않았다면) fully static 한 빌드파일을 만들어서 CDN에서 서빙할수도 있다는 것.



