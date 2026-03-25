# 주우성 202230236
---
## 3월 25일(4주차)
### Vite에서 SWC가 사라진 이유
프로젝트의 설정 확인
* 프로젝트의 설정확인은 vite.config.js에서 확인가능

Oxc(Oxidation Compiler)와 SWC(Speedy Web Compiler)
* 모두 Rust로 작성되어 매우 빠른 속도를 자랑하는 차세대 자바스크립트/타입스크립트 도구
* SWC: 바벨을 대체하기 위해 만들어진 컴파일러 및 번들러
    - 트렌스파이링(TS->JS)과 번들링에 특화
    - 바벨을 완벽하게 대체 가능. Next.js 등 현대적인 프레임워크에서 기본으로 사용
* Oxc: ESLint, Prettier, TypeScript 트랜스파일러 등을 모두 대체하려는 고성능 도구 모음
    - 특히 파싱 속더에서 SWC보다 3배 이상 빠름
    - 정적 분석 및 린팅에 매우 강점을 가짐
    - ESLint 보다는 100배 이상 빠름

### 나의 첫 번째 컴포넌트 만들기
step 1
``` jsx
import reactLogo from './assets/react.svg'

export default function App() {
  return (
    <>
      <img className="button-icon" src={reactLogo} alt="" />
    </>
  )
}

```

컴포넌트의 기본 구조
``` jsx
function Profile () {
  return (
    <>
      
    </>
  )
}
```

step 2 컴포넌트를 별도의 파일로 분리하기
``` jsx
import Profile from "./Profile"

export default function App() {
  return (
    <>
      <Profile />
    </>
  )
}
```
``` jsx
import reactLogo from './assets/react.svg'

export default function Profile () {
  return (
    <>
      <img className="button-icon" src={reactLogo} alt="" />
    </>
  )
}

```

컴포넌트의 생성과정과 사용방법 정리
* 컴포넌트의 생성
  1. 컴포넌트의 이름과 동일한 이름의 파일 생성
  2. function 구문 앞에 export default 키워드 사용하여 외부에서 사용할 수 있도록 하기
  3. function 구문의 중괄호{} 내부에 컴포넌트의 로직 구현
  4. return 구문의 소괄호() 내부에 컴포넌트에서 반환할 구문 작성
* 컴포넌트의 사용
  1. 사용하고자 하는 컴포넌트를 import
  2. import 할 때 사용한 로컬 변수명을 <변수명 /> 과 같은 구조로 원하는 곳에 사용

### 컴포넌트의 중첩(Nesting)
* React에서의 중첩은 특정 컴포넌트를 다른 컴포넌트안에서 호출하는 것을 의미
* 중첩은 특정 컴포넌트 안에 다른 컴포넌트를 선언하는 것으로 이해하면 X

실습 1
``` jsx
import Gallery from "./components/Gallery"

export default function App() {
  return (
    <>
      <Gallery />
    </>
  )
}
```
``` jsx
import reactLogo from '../assets/react.svg'

export default function Profile () {
  return (
    <>
      <img className="button-icon" src={reactLogo} alt="" />
    </>
  )
}

```
``` jsx
import Profile from "./Profile"

export default function Gallery () {
    return(
        <>
            <Profile />
            <Profile />
            <Profile />
        </>
    )
}
```

실습 2

``` jsx
export default function MyTitle () {
    return (
        <>
            <h1>My Gallery</h1>
        </>
    )
}
```
``` jsx
import Profile from "./Profile"
import MyTitle from "./MyTitle"

export default function Gallery () {
    return(
        <>
            <MyTitle />
            <Profile />
            <Profile />
            <Profile />
        </>
    )
}
```

React가 렌더링 되는 과정
* 여러 컴포넌트들 -> App.jsx라는 루트 컴포넌트 -> main.jsx -> index.html

React에서 컴포넌트와 HTML 태그를 어떻게 렌더링?
* React에서는 소문자로 시작하는 `<section>`은 HTML로
* 대문자로 시작하는 `<MyTitle>`은 컴포넌트로 인식

