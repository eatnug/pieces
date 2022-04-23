MVC, MVP, MVVM 모두 UI가 있는 어플리케이션을 구성하는 방법이라고 이해하자. 계속 머리속에서 spring mvc, 장고의 mtv?가 떠올라서 길을 잃는다. 그런데 킹짱 해리가 한시간 보고 이해안되면 넘어가라고 해서 대충 감만 이해하고 넘어가야겠다.

## MVC

소프트에웨어를 모델, 뷰, 컨트롤러 세가지 role로 나눈다. 

모델은 데이터, 비즈니스로직을 다루는 역할을 하고, 뷰는 UI를 렌더하는 역할을 한다. 컨트롤러는 요청을 받고, 적당한 모델을 불러다가 적당한 로직을 수행하고 적당한 뷰를 골라서 화면을 렌더하게 하는 컨트롤 타워 역할을 한다.

여기서의 문제는 컨트롤러가 뷰에 강하게 결합될 수 있고, 컨트롤러가 너무 거대해질 수 있다.

## MVP

모델은 기존과 동일하고, 뷰와 프레젠터는 조금 바뀌었다.

기존의 요청을 받던 컨트롤러는 사라지고, 뷰가 요청을 받는다. 
프레젠터가 뷰와 모델의 중개자 역할을 한다. 뷰와 모델의 중개자라는 role을 억지로 맞춰둬서 view - presenter 가 1:1이 되는듯.

여전히 프레젠터가 많아지고 거대해진다.

## MVVM

요청은 view를 통해 들어온다. 요청은 viewmodel에 전달되고 모델을 수정하거나 이런저런 비즈니스 로직을 수정하고 viewmodel도 수정합니다. view 가 viewmodel을 구독하는 형태이기 때문에 view 의 viewmodel에 대한 의존성, viewmodel의 model에 대한 의존성 정도만 발생한다. 어떻게 보면 리액트가 state의 변화를 구독하고 있다가 화면에 그려주는 형태는 이것과 비슷한 듯...?


[ref](https://brunch.co.kr/@oemilk/113)
[ref](https://academy.realm.io/posts/eric-maxwell-mvc-mvp-and-mvvm-on-android/)