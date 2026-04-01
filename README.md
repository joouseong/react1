# 주우성 202230236
---
## 4월 1일(5주차)
### JSX로 마크업 작성하기
* 태그(Tag)는 HTML과 같은 마크업에서 요소를 표시하기 위한 개별 기호를 의미함. `<div>`,`<li>` 등이 있음
* 엘리먼트(Elememt)는 DOM의 구성 단위로 "여는 태그 + 내용(Content) + 닫는 태그" 전체를 의미하는 개념. DOM 노드라고도 함. 예를 들어 `<p>` 엘리먼트 설명 `</p>`의 형태
* 어트리뷰트(속성)는 태그의 행동을 제어하거나, 엘리먼트에 추가적인 정보(데이터)를 제공하기 위해 여는 태그 안에 사용되는 특수 명령어. 예를 들어 `<img>`태그의 src같은 정적 속성 같은 것
* Property(프로퍼티)는 DOM 트리의 객체 내부에 존재하는 동적인 속성. 프로퍼티는 JavaScript로 제어할 수 있으며, 현재의 동적 상태를 의미
  - 예를 들어 사용자가 input 태그에 글자를 입력하면, 해당 input 요소의 Property(value)는 변경되지만, HTML의 Attribute(value)는 처음 입력된 값 그대로 남아있음
<br>

JSX 안에서 자바스크립트 사용하기
* JavaScript를 JSX에서 사용하는 방법은 4가지
  1. 따옴표로 문자열을 전달하는 방법
  2. 중괄호를 이용해서 JavaScript 변수를 참조하는 방법
  3. 중괄호를 이용해서 JavaScript 함수를 호출하는 방법
  4. 중괄호를 이용해서 JavaScript 객체를 적용하는 방법
``` jsx
export default function UseJsx () {
    const name = "React"
    return (
        <>
            <h1>Hello, {name}</h1>
        </>
    )
}
```

``` jsx
export default function UseJsx () {
    const name = "React"

    function formatDate(date){
        return new Intl.DateTimeFormat(
            "en-US", {weekday: "long"}
        ).format(date);
    }

    return (
        <>
            <h1>Hello, {name}</h1>
            <p>Today is {formatDate(new Date())}</p>
        </>
    )
}
```

### 데이터 전달과 렌더링
개요
* React 컴포넌트는 props를 이용해 서로 통신
* 부모 컴포넌트는 자식 컴포넌트에게 props를 통해 데이터를 전달
* props는 HTML의 속성과 비슷해 보이지만 객체, 배열, 함수를 포함한 모든 JavaScript 값을 전달 할 수 있음

Props의 데이터 전달
* Props는 부모 컴포넌트가 자식 컴포넌트에게 전달되는 데이터 꾸러미라고 할 수 있음
* React에서는 props를 통해 JSX 태그에 정보를 전달
  * 예를 들어 src, alt, width, height으 속성값을 `<img>`태그에 전달할 수 있음
* `<img>`태그에 전달할 수 있는 props는 HTML 표준으로 이미 정의되어 있음
  * HTML 문서를 작성할 때와 동일

컴포넌트에 props 전달하기
* 부모 컴포넌트
  - 자식 컴포넌트를 자신의 구조 안에 포함(Import 및 호출)하고, 데티러르 전달(props)하는 컴포넌트
* 자식 컴포넌트
  - 부모 컴포넌트로부터 전달받은 props를 통해 구체적인 UI를 만들어서 부모 컴포넌트에 다시 반환
  - 독립적으로 재사용될 수 있음

* Props의 특징
  * 일방통행
  * 읽기 전용
  * 다양한 타입

Props의 기본값 지정
* 부모 컴포넌트로부터 전달받은 prop이 없을 때는 기본값을 지정해줄 수 있음
* 기본값을 지정할 때는 변수 뒤에 = 과 함께 기본값을 넣어줌
* 이 기본값은 prop이 없거나(undefined)로 전달될 때 사용됨
* {null} 또는 {0}으로 전달된다면, 기본값은 사용되지 않음

JSX spread 문법으로 props 전달하기
* 모든 props를 한 번에 자식 컴포넌트에 전달하는 방법은 자바스크립트의 spread 문법을 이용
* 자바스크립트에서 spread 문법은 객체를 펼치는 문법
* spread문법의 `<User {...props} />`는 `<User name={props.name} age={props.age} />`과 동일
<br>

[이 방법은 주로 다음과 같은 경우에 사용]
* 전달받은 props 그대로 넘겨줄 때 (Props Forwarding) : 부모 컴포넌트가 받은 props를 중간 단계 컴포넌트가 그대로 자식에게 토스할 때 매우 유용
* 기본 HTML 속성 확장할 때 : 버튼 등을 만들 때, onClick, type, disabled 같은 표준 HTML 속성들을 한꺼번에 전달하고 싶을 때
* 전달하고자 하는 props의 수가 너무 많을 때 : props의 수가 많아서 오히려 가독성이 떨어지거나, 가독성보다 간결성이 필요할 때 사용
<br>
<br>

* 이 밖에도 다양한 방법으로 보낼 수 있음.
* 그러나 무분별하게 spread 문법을 사용하면 X

[주의사항]
1. 가독성 저하: 자식 컴포넌트가 어떤 props를 받는지 코드만 보고 파악하기 힘들어짐. 이것을 'Magic Props' 문제라고 함
2. 불필요한 데이터 전달: 객체에 담긴 데이터 중 자식에게 필요 없는 정보까지 모두 넘어가게 됨. 이는 성능 저하나 예기치 못한 버그를 유발할 수 있음
3. 우선순위 문제: 아래처럼 순서에 따라 값이 덮어씌워질 수 있음. 만일 userData에도 age가 있고, 값이 20인 경우 뒤에 쓴 age={30}이 최종적으로 적용
><Profile {...userData} age={30}>

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

### Default Export와 Named Export의 차이
* 두 가지 선언 방식의 차이는 default 키워드의 사용 여부
* 두 가지 방법 모두 한 파일에서 사용할 수도 있음
* 주의할 점은 하나의 파일에는 하나의 default export만 존재할 수 있고, named export는 여러개 존재 가능
* default는 별칭 사용이 가능하지만 named는 불가능

Default import의 사용
* import 키워드 다음에 다른 이름으로 변수명을 선언할 수 있음
* 이 이름을 로컬 식별자 혹은 변수명이라고 함
* 예를 들어 import Banana from './Button'라고 선언하면, 이 파일안에서는 Banana라는 이름으로 사용할 수 있음
* 변수명은 대문자로 시작해야 함

named import를 사용
* export하는 곳과 import하는 곳의 컴포넌트의 이름이 같아야 함
* 모듈에 여러 개의 named 컴포넌트가 있을 경우 전부 혹은 일부만 import해서 사용할 수 있음

### Named Export의 다양한 사용법

Named Export한 경우 4가지 방법으로 import 가능
1. 1개의 컴포넌트만 import하는 경우, 중괄호{} 안에 컴포넌트 이름을 넣음
2. 2개 이사의 컴포넌트를 import하는 경우, 중괄호 안에 콤마로 구분
3. Default Export 컴포넌트처럼 변수 명을 바꾸고 싶을 때는 as 키워드 사용
4. 모든 컴포넌트를 가져올 때는 스타 기호( * )와 Namespace import 방식을 사용

실습
``` jsx
export function NamedComponent1 () {
    return(
        <>
            <h1>네임드 1</h1>
        </>
    )
}

export function NamedComponent2 () {
    return(
        <>
            <h1>네임드 2</h1>
        </>
    )
}

export function NamedComponent3 () {
    return(
        <>
            <h1>네임드 3</h1>
        </>
    )
}
```
``` jsx
// import { NamedComponent1 } from "./NamedComponent" //방법 1
// import { NamedComponent1, NamedComponent3 } from "./NamedComponent" //방법 2
// import { NamedComponent1 as Foo, NamedComponent3 as Bar } from "./NamedComponent" //방법 3
import * as Foo from "./NamedComponent" //방법 4

export default function NamedComponentTest() {
    return(
        <>
            <h1>Named Component Test</h1>
            <Foo.NamedComponent1 />
            <Foo.NamedComponent2 />
            <Foo.NamedComponent3 />
        </>
    )
}
```

Named Export를 권장하는 이유
* 일관성: 팀원 모두가 같은 컴포넌트를 같은 이름으로 부르게 됨
* 리팩토링 용이성: 컴포넌트 이름을 바꿀 때, 에디터가 연결된 모든 파일의 이름을 한꺼번에 안전한게 바꿔줌
* 트리 쉐이킹(Tree Shaking): 사용하지 않는 코드를 제거하는 과정에서 Named Export가 더 유리한 경우가 많음

어떤 방식을 사용하든 컴포넌트와 파일의 이름을 의미있게 명명하는 것이 매우 중요

### JSX로 마크업 작성하기
JSX란 무엇인가?
* JSX는 JavaScript를 확장한 문법으로, Java Script 파일을 HTML과 비슷한 형태의 마크업을 작성할 수 있도록 해줌
* 컴포넌트를 작성하는 다른 방법도 있지만, JSX의 간결함 때문에 대부분의 React 개발자가 선호
<br>
<br>
* HTML, CSS, JavaScript 코드는 일반적으로 분리되 파일로 관리
* 그러나 Web이 더욱 인터랙티브 해지며, 로직이 내용을 결정하는 경우가 많아짐
* 따라서 효율적인 렌더링을 위해 로직을 담당하는 JavaScript가 HTML을 담당하는 것이 좋음
* React의 컴포넌트가 좋은 예
<br>
<br>
* 버튼(input 태그)의 렌더린 로직과 마크업이 함께 있으면, 매번 변화가 생길 때마다 서로 동기화 상태를 유지할 수 있음
* 반대로 버튼의 마크업과 사이드바의 마크업처럼 관련이 없는 항목들은 서로 분리되어 있어 각각 개별적으로 변경할 수 있어 안전한 관리 가능
* 즉 React 컴포넌트는 JavaScript 함수로 작성되며, 이 로직과 함께 JSX라는 확장된문법으로 마크업 작성
*JSX는 HTML과 비슷해 보이지만 조금 더 엄격하게 적용, 동적으로 정보를 표시 가능

JSX의 3가지 규칙
1. 하나의 루트 엘리먼트로 반환해야 함
2. 모든 태그는 닫아줘야 함
3. 속성(attribute)는 카멜 케이스(camelCase)로 작성
* 파스칼 케이스(PascalCase): 첫 단어부터 대문자로 시작, React 컴포넌트의 이름에 사용
* 카멜 케이스(camelCase): 첫 단어는 소문자로 시작, 두 번째 단어부터 대문자 시작. JSX의 속성 이름에 사용