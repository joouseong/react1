# 주우성 202230236

## 5월 6일(10주차)
### 이벤트와 상호작용
이벤트 핸들러에서 prop 사용하기 - 실습
``` jsx
import ButtonCom from "./ButtonCom";

export default function ToolBar() {
    return(
        <>
            <ButtonCom message="버튼1 클릭">
                버튼1
            </ButtonCom>
            <ButtonCom message="버튼2 클릭">
                버튼2
            </ButtonCom>
        </>
    )
}
```
``` jsx
export default function ButtonCom({message, children}) {
    function handleClick() {
        alert(message)
    }

    return(
        <button onClick={handleClick}>{children}</button>
    )
}
```

이벤트 핸들러를 prop으로 전달하기

* 지금까지는 handleClick 이라는 하나의 이벤트 핸들러만 사용 <br>
    → 버튼의 이름과 출력되는 문자열은 다르지만 하나의 로직에 의해서 출력된 것
* 만일 각각의 버튼이 2가지 이상의 기능을 수행해야 한다면 이벤트 핸들러도 2개 이상이 필요하게 됨
* 구현하는 방법은 여러가지.
    1. 조건문을 사용하여 분기하면 쉽게 구현
    2. 필요한 만큼 Button 컴포넌트를 만들 수 있음 <br>
    → 이런 방법은 어떤 문제?

다음과 같은 방법을 사용하면 관리도 쉽고 재사용도 편리한 컴포넌트를 만들 수 있음
1. 버튼 컴포넌트는 출력만 담당하게
2. 이벤트 핸들러는 별도의 파일에 모듈의 형태로 모아 관리
3. 부모 컴포넌트에서 버튼 컴포넌트를 호출할 때 이벤트 핸들러를 함께 전달

실습 1
``` jsx
export default function ButtonCom({handle, message, children, style}) {
    return(
        <button onClick={ () => handle({message})} className={style}>
            {children}
        </button>
    )
}
```
``` jsx
import ButtonCom from "./ButtonCom";
import handleClick from "./handle";
import style from "./ButtonCom.module.css"

export default function ToolBar() {
    return(
        <>
            <ButtonCom message="버튼1 클릭" handle={handleClick} style={style.myButton}>
                버튼1
            </ButtonCom>
            <ButtonCom message="버튼2 클릭" handle={handleClick} style={style.myButton}>
                버튼2
            </ButtonCom>
        </>
    )
}
```
``` jsx
export default function handleClick({message}) {
    alert(message)
}
```

실습 2
* 버튼을 클릭하면 동영상이 재생되고, 정지하는 실제 동작을 하도록 수정
* React의 state hook 등을 사용하면 간단하게 구현 가능
* 하지만 아직 hook을 학습하지 않았기 때문에 순수 자바스크립트 만으로 구현
``` jsx
export function handlePlay({message}) {
    const videoSource = document.getElementById(message);
    if (videoSource) videoSource.play();
}

export function handleStop({message}) {
    const videoSource = document.getElementById(message);
    if (videoSource) videoSource.pause();
}
```
``` jsx
import ButtonCom from "./ButtonCom";
import {handlePlay, handleStop} from "./handle";
import style from "./ButtonCom.module.css"
import sampleVideo from "../../assets/sample.mp4";

export default function ToolBar() {
    return(
        <>
            <nav>
                <ButtonCom message="videoPlayer" handle={handlePlay} style={style.myButton}>
                    Play
                </ButtonCom>
                <ButtonCom message="videoPlayer" handle={handleStop} style={style.myButton}>
                    Stop
                </ButtonCom>
            </nav>
            <br />
            <section>
                <video id="videoPlayer" src={sampleVideo} controls width="350" />
            </section>
        </>
    )
}
```

Note
* document.getElementById(id)
* HTML 문서에서 고유한 id 속성을 가진 요소를 찾아 JavaScript 객체로 반환하는 메서드
* id 값을 따옴표로 감싸 매개변수로 전달, 요소가 없으면 null을 반환
* 주로 HTML의 내용 변경, 스타일 수정 등 DOM 조작에 사용


---
## 4월 29일(9주차)
### 데이터 전달과 렌더링
UI를 트리 구조로 이해하기 - Render 트리
* 브라우저와 모바일 플랫폼처럼 React도 React 앱을 구성하는 컴포넌트 간의 관계를 관리하고, 모델링하기 위해 트리 구조를 사용
* 트리는 React 앱에서 데이터가 흐르는 방식과 렌더링 및 앱 크기를 최적화하는 방법을 이해하는 데 유용한 도구
* 컴포넌트의 중요한 특징은 다름 컴포넌트들을 중첩해서 또다른 컴포넌트를 구성하는 것
* 컴포넌트를 중첩하면 부모 컴포넌트와 자식 컴포넌트의 개념이 생기게 됨
* 이때 각 부모 컴포넌트도 또 다른 컴포넌트의 자식이 될 수 있음. 다만 자식 컴포넌트의 자식이 될 수는 없음
* React 앱을 렌더링할 때, 이 관계를 Render 트리라고 하는 트리로 모델링할 수 있음
<br>
<br>
* 트리는 노드로 구성되어 있으며, 각 노드는 컴포넌트를 나타냄
* React Render 트리에서 루트 노드는 앱의 Root 컴포넌트
* DOM 트리와는 달리 Render 트리는 HTML 태그 업이 React 컴포넌트로만 구성

UI를 트리 구조로 이해하기 - 모듈 의존성 트리
* 모듈 의존성 트리는 React의 또다른 트리 구조 모델링 방법으로 모듈의 종속성을 나타냄
* 컴포넌트와 로직을 별도의 파일로 분리하면 컴포넌트 뿐만 아니라 함수 또는 상수를 export할 수 있는 js 모듈을 만들 수 있음
* 모듈 의존성 트리의 각 노드는 모듈이며, 가지는 해당 모듈의 import 문을 나타냄
<br>
<br>

* 트리의 루트 노드는 루트 모듈, 엔트리 포인트 파일이라고도 함
    * 일반적으로 루트 컴포넌트를 포함하는 모듈
* 동일한 앱의 Render 트리와 유사하지만 몇가지 차이점이 있음
    1. 트리를 구성하는 노드는 컴포넌트가 아닌 모듈을 나타냄
    2. inspirations.js와 같은 컴포넌트가 아닌 모듈도 이 트리에 나타남
    3. Render 트리는 컴포넌트만 캡슈화 하지만 모듈트리는 모듈도 포함
* 의존성 트리는 React 앱을 실행하는 데 필요한 모듈을 결정하는 데 유용하게 이용됨
* React 앱을 프로덕션용으로 빌드할 때, 일반적으로 클라이언트에 제공할 때 필요 모든 JavaScript를 번들로 묶는 빌드 단계가 있음

### JSX에 스타일 적용하기
* JSX에 스타일을 적용하는 방법은 다양함<br>
    : 일반 CSS, 인라인 스타일, CSS-in-JS, CSS 프레임워크, CSS Module
* React에서 권장하는 방법은 CSS Module
* 실무에서는 Tailwind + 일부 CSS Module 혹은 CSS Modules + SCSS의 조합으로 많이 사용

일반 CSS
* 가장 간단하게 사용할 수 있는 방법으로 HTML에서 CSS를 사용하는 방법과 동일
* style.css 파일을 만들어서 필요한 스타일을 정의한 후 사용할 컴포넌트에서 import
* 단, 속성의 이름으로 class가 아닌 className을 사용. 이 것은 모든 방법에서 동일하게 적용됨
* 익숙한 방법이기 때문에 프로젝트에 빠르게 적용할 수 있음
* 컴포넌트 단위로 관리하기 어렵고, 전역 스코프(global)의 클래스 이름과 충돌 가능성이 있기 때문에 주의해야 함

인라인 스타일
* HTML 에서도 인라인 스타일은 유지보수의 어려움 등의 단점이 있어 자주 사용하지 않음
* 조건부 스타일에만 제한적으로 사용됨
* 속성 이름은 kebab-case가 아닌 camelCase를 사용해야 함

CSS-in-JS
* 자바스크립트 코드 내에서 CSS를 직접 작성, 컴포넌트 단위로 스타일을 관리하는 방법
* styled-components, emotion, JSS 등 외부 라이브러리를 사용
* 스타일이 컴포넌트 내에 바인딩 되기 때문에 관리와 유지보수 용이
* props를 기반으로 한 동적(조건부) 스타일링 적용에 매우 편리
* 고유한 클래스명을 자동으로 생성하여 스타일의 충돌 방지
* Provider 컴포넌트를 통해 전역 테마 설정

CSS 프레임워크
* 일반적으로 프론트엔드 개발에 많이 사용하는 방법
* Tailwind CSS(클래스 단위), Bootstrap(컴포넌트 단위), bulma 등 유명한 CSS 프레임워크들이 있음
* React에서 추천하는 Tailwind CSS는 클래스를 조합하여 스타일을 작성
* 빠른 개발과 디자인의 일관성을 유지할 수 있다는 장점
* 클래스를 조합하는 과정에서 클래스 선언이 길어지기 때문에 문서의 가독성이 떨어진다는 단점

CSS Module
* CSS Module은 클래스명을 `_[클래스이름]_[해쉬값]`의 형태로 자동 변환하여, 고유한 이름의 로컬 스코프(Local Scope)를 제공하는 기술
* 컴포넌트 기반의 프레임워크인 React나 Vue 등에서 채택하고 있는 이 기술은 스타일의 충돌을 완벽하게 방지, 유지보수에도 유리
* 컴포넌트 단위로 스타일링 한다는 것이 가장 큰 특징, 컴포넌트의 재사용에도 유리하게 작용
* 일반 CSS의 문제점 중 하나인 전역으로 선언되기 대문에 다른 컴포넌트와 충돌의 위험이 있는 것을 CSS Module은 빌드 시 고유한 이름으로 변경되기 때문에 충돌의 위험이 없음

CSS Module 사용 방법
* 파일 이름의 규칙: 파일 이름은 [컴포넌트 이름].module.css의 형태
* CSS 작성: <br>
    * css의 내용은 일반 css의 작성법을 따름
    * class 선택자로 스타일 선언
    * Tag 선택자를 사용하는 것은 특별한 경우 아니면 권장하지 않음
    * Tag 선택자는 CSS Module 빌드 시에 고유한 이름을 할당 받지 않고, 전역으로 사용되기 때문
* 클래스에 적용하는 법:
    * import의 변수명(로컬 식별자)은 관행적으로 style을 사용
    * JSX에서는 class 키워드 대신 className 사용
    * class 이름은 객체를 사용할 때처럼 [변수명].[클래스 명]의 형태로 작성
    * class 이름 전체를 중괄호로 감싸줌
* 여러 개의 클래스 적용 방법
```
<nav className={`${styles.toolBar} ${styles.active}`}>...</>
```

실습
``` jsx
import style from "./ButtonCom.module.css"

export default function ButtonCom() {
    return(
        <>
            <h1 className={style.title}>ButtonCom 컴포넌트</h1>
            <nav className={style.navBar}>
                <button className={style.myButton}>버튼1</button>
                <button className={style.myButton}>버튼2</button>
            </nav>
        </>
    )
}
```
``` css
.title {
    font-size: 30px;
    color: blue;
}

.navBar {
    padding: 15px;
    background-color: #ccc;
    border: #000 1px solid;
}

.myButton {
    margin-right: 10px;
    background-color: green;
    border-radius: 25px;
}
```

### 이벤트와 상호작용
개요
* 화면을 구성하는 요소 중 사용자의 입력에 반응해 업데이트 되는 요소가 있음
* 예를 들어 이미지 갤러리에서 특정 이미지를 클릭하면 해당 이미지는 활성 상태가 됨
* 사용자의 클릭과 같은 특정 입력을 이벤트라고 하고, 이벤트가 발생했을 때 반응하는 로직을 이벤트 핸들러라고 함

이벤트에 응답하기
* React에서는 JSX 이벤트 핸들러를 추가할 수 있음
* 이벤트 핸들러는 클릭, 마우스 호버(hover), 폼 입력의 포커스 등 사용자와의 상호작용에 따라 유발되는 사용자 정의 함수
* `<button>`과 같은 내장 컴포넌트는 onClick과 같은 내장 브라우저 이벤트만 지원
* 반면 사용자 정의 컴포넌트의 경우, 이벤트 핸들러 속성에 원하는 애플리케이션별 이름을 지정할 수도 있음

이벤트 핸들러 추가하기
* 이벤트 핸들러 추가를 위해서는 먼저 함수를 정의, 이를 적절한 JSX 태그에 prop 형태로 전달

실습
``` jsx
import style from "./ButtonCom.module.css"

function handleClick() {
    alert("버튼 클릭")
}

export default function ButtonCom() {
    return(
        <>
            <h1 className={style.title}>ButtonCom 컴포넌트</h1>
            <nav className={style.navBar}>
                <button onClick={handleClick} className={style.myButton}>버튼1</button>
                <button onClick={handleClick} className={style.myButton}>버튼2</button>
            </nav>
        </>
    )
}
```
* 이벤트 핸들러의 이름은 handle로 시작하고, 이벤트명을 뒤에 붙이는 것이 관례
* Ex : `onClick={handleClick}`, `onMouseEnter={handleMouseEnter}`

이벤트 핸들러 인라인 스타일 정의
* 이벤트 핸들러는 별도의 함수로 정의하는 것이 일반적이지만 JSX 내에 인라인으로 정의할 수 도 있음
`<button onClick={() => {alert("버튼 클릭");}}>`
* 하지만 인라인 스타일의 정의는 함수가 아주 짧을 때만 예외적으로 사용할 것을 권장
* 가독성이 떨어지고, 재사용 및 모듈화에도 불편함

이벤트 핸들러 함수의 전달?
* 이벤트 핸들러 함수는 호출하는 것이 아니라 전달하는 것
* 함수를 전달한다는 것은 다음과 같이 이름만 prop의 형태로 전달
    * `<button onClick={handleClick}>`
* 함수를 호출한다는 것은 함수의 이름에 소괄호를 함께 사용
* 호출은 함수를 직접 사용한다는 것을 의미하기 때문에 잘못된 사용법
    * `<button onClick={handleClick()}>` 은 잘못된 예
* 인라인으로 코드를 작성할 때도 형태는 조금 차이가 있으나 동일한 문제 발생
* 인라인으로 alert() 함수를 직접 호출하면 컴포넌트가 렌더링 될 때마다 실행됨
    > `<button onClick={alert('You clicked me!')}>`
* 만일 이벤트 핸들러를 인라인으로 정의하고 싶다면, 다음과 같이 익명 함수를 사용
    > `<button onClick={ () => alert('You clicked me!') }>`

Note
* 컴포넌트에 데이터를 전달할 때 props를 사용했음
* 이번 실습에서는 `<button>`태그에서 props 을 사용
* 컴포넌트가 아닌 HTML태그에서 props를 사용
* React에서는 button을 컴포넌트처럼 처리하면서 props를 넘기기 때문

---
## 4월 15일(7주차)
### 데이터 전달과 렌더링
배열의 항목들을 필터링하기
* 앞에서 사용한 heroes 데이터를 조금 수정하면 더욱 강력한 구조화가 가능
* JavaScript의 filter() 함수를 사용하여 해당하는 배우의 정보만을 반환 가능
``` jsx
export const heroes = [
    {
        id: 0,
        casting: "스파이더맨",
        name: "피터 파커",
        power: 4
    },
    {
        id: 1,
        casting: "아이언맨",
        name: "토니 스타크",
        power: 5
    },
    {
        id: 2,
        casting: "배트맨",
        name: "브루스 웨인",
        power: 3
    },
    {
        id: 3,
        casting: "슈퍼맨",
        name: "클라크 켄트",
        power: 5
    },
    {
        id: 4,
        casting: "헐크",
        name: "로버트 브루스 배너",
        power: 4
    }
    ];
```
``` jsx
import { heroes } from "./HeroesData";

export default function MovieHeroes() {

    const filterTests = heroes.filter(hero =>
        hero.name === "클라크 켄트"
    );
    const listHeroes = filterTests.map(hero => 
        <li>
            <p>
                {hero.name}의 배역은 {hero.casting} 입니다.
            </p>
        </li>
    );

    return(
        <section>
            <h1>영화 속 영웅들</h1>
            <ul>{listHeroes}</ul>
        </section>
    )
}
```
power가 5인 hero 모두 출력
``` jsx
import { heroes } from "./HeroesData";

export default function MovieHeroes() {

    const filterTests = heroes.filter(hero =>
        // hero.name === "클라크 켄트",
        hero.power === 5
    );
    const listHeroes = filterTests.map(hero => 
        <li>
            <p>
                {hero.name}의 배역은 {hero.casting} 입니다.
            </p>
        </li>
    );

    return(
        <section>
            <h1>영화 속 영웅들</h1>
            <ul>{listHeroes}</ul>
        </section>
    )
}
```
* JavaScript만의 특이점 : `===` 는 `==`보다 더 강력한 Strick Equal Operator(엄격한 비교 연산자)로 피연산자의 값의 타입이 서로 다르면 변환하지 않고 그대로의 값을 비교하는 방식

화살표 함수에 대하여
* 화살표 함수는 묵시적으로 => 바로 뒤에 있는 식을 반환하기 때문에 return문이 필요 없음
* 그러나 => 뒤에 {} 중괄호가 오는 경우에는 return을 명시적으로 작성해야 함
* => {}를 표현하는 화살표 함수를 "block body"를 가지고 있다고 함
* 이 함수를 사용하면 한 줄 이상의 코드를 작성할 수 있지만, return 문을 반드시 작성해야 함
* 그렇지 않으면 아무것도 반환되지 않음
* 일반적으로 원데이터는 복수형(heroes)를 사용하고, 임시저장소는 원데이터의 단수형(hero)를 사용

Key prop을 사용하는 이유
* > Each child in a list should have a unique "key" prop.
* 이 경고는 목록(배열)의 각 자식 요소는 고유한 'key' prop을 가져야 하는데 그렇게 설정되지 않아서 발생하는 경고
* 배열의 각 항복은 다른 목록들과 명확히 구분되는 고유한 문자열 혹은 숫자를 key로 지정해야 함
* 이것을 key prop라고 함
* Key prop은 배열 중 어떤 자식 요소인지 확인할 수 있도록 함
* 배열의 자식 요소가 정렬 등으로 인해 이동, 삽입, 삭제되어도 각 자식 요소를 구별하는데 중요하게 사용됨
* Key prop은 즉석에서 생성하는 것이 아니고, 배열안에 포함되어 있어야 함

프래그먼트와 key prop
* 각 항목이 하나가 아닌 여러 개의 DOM 노드를 렌더링 해야하는 경우, 즉 반환해야 하는 태그가 여러개 있을 경우
* 프래그먼트 `<>...</>`구문을 사용하거나, `<div>`태그 등으로 묶어서 하나로 노드로 만들어 반환해야 함
* 그러나 프래그먼트 구문으로는 key를 전달할 수 없음
* 이런 경우 `<div>` 등의 태그로 그룹화하거나, React에서 제공하는 `<Fragment>`컴포넌트를 사용해야함
``` jsx
import Fragment from React

const listHeroes = filterTests.map (hero =>
    <Fragment key={hero.id}>
        <h1>{hero.name}</h1>
        <p>{hero.casting}</p>
    </Fragment>
)
```

컴포넌트를 순수하게 유지하기
* 순수 함수란
    - 같은 입력 값을 넣으면 항상 같은 결과를 반환하는 함수
    - 외부의 상태를 변경하지 않는, 즉 사이드 이펙트(side effect)가 없는 함수를 의미
* 컴포넌트를 만들 때도 순수 함수로 만들면 사이드 이펙트가 없는 순수한 컴포넌트 완성
* 코드베이서의 규모가 점점 커지더라도 예상밖의 동작이나 버그를 피할 수 있음

순수함수로 구현되는 컴포넌트
* 특징
    1. 자신의 일에만 집중하고, 함수가 호출되기 전에 존재했던 어떤 객체나 변수도 변경하지 않는다
    2. 같은 입력이 주어졌다면 순수 함수는 항상 같은 값을 반환
* React에서 컴포넌트는 함수로 정의하기 때문에 순수함수로 작성된 컴포넌트는 순수 컴포넌트라고 할 수 있음

``` jsx
export default function OrderUp({order}) {
    return(
        <section>
            <p>치즈버거 {order}개/콜라 {order}개 + (이벤트)프렌치 프라이 {2 * order}개</p>
        </section>
    )
}
```
``` jsx
import OrderUp from "./OrderUp";

export default function Kiosk() {
    return(
        <section>
            <h2>치즈버거 세트 메뉴를 주문하세요.</h2>
            <p>일반세트 : </p>
            <OrderUp order={1} />
            <p>패밀리 세트 : </p>
            <OrderUp order={2} />
            <p>이용해 주셔서 감사합니다.</p>
        </section>
    )
}
```

의도하지 않은 결과 사이드 이펙트
* 잘못된 코드
``` jsx
/* eslint-disable */
let guest = 0;

function Cup() {
    guest = guest + 1;
    return <h2>Tea cup for guest #{guest}</h2>
}

export default function TeaSet() {
    return(
        <>
            <Cup />
            <Cup />
            <Cup />
        </>
    )
}
```

* 올바른 코드
``` jsx
function Cup({guest}) {
    return <h2>Tea cup for guest #{guest}</h2>
}

export default function TeaSet() {
    return(
        <>
            <Cup guest={1}/>
            <Cup guest={2}/>
            <Cup guest={3}/>
        </>
    )
}
```

지역 변경(local mutation)
* 잘못된 예제의 문제점은 컴포넌트가 외부에 있는 기존 변수를 렌더링 중에 변경했다는 것
* 이런 사이드 이펙트를 "변경(Mutation)"라고 부르기도 함
* 순수 함수는 함수 스코프 외부의 변수나 호출 전에 생성된 객체를 변경하지 않음
* 그러나, 렌더링하는 동안에 생성된 변수와 객체를 변경하는 거슨 문제가 되지 않음

* 자바스크립트의 push() 메서드는 배열의 맨 끝에 하나 이상의 요소를 추가하고, 배열의 새로운 길이(length)를 반환하는 함수
```jsx
function Cup({guest}) {
    return <h2>Tea cup for guest #{guest}</h2>
}

export default function TeaGathering() {
    const cups = [];
    for (let i=1; i<=12; i++) {
        cups.push(<Cup key={i} guest={i} />);
    }
    return cups;
    // or return(<>{cups}</>)
}
```

UI를 트리구조로 이해하기 - Render 트리
* React를 비롯한 많은 UI 라이브러리는 UI를 트리의 형태로 모델링함
* 애플리케이션을 트리로 생각하면 컴포넌트 간의 관계를 이해하는 데 도움이 됨
* 또한 향후 성능이나 상태 관리와 같은 개념을 파악하는데도 도움이 됨
* 트리는 요소 사이의 관계 모델이며, UI는 이 트리 구조를 사용하여 표현됨
    - 브라우저는 HTML(DOM)과 CSS(CSSOM)을 모델링하기 위해 트리 구조를 사용
    - 모바일 플랫폼에서도 뷰의 계층 구조를 나타내는 데 트리를 사용

---
## 4월 8일(6주차)
### 데이터 전달과 렌더링
조건부 렌더링
* 컴포넌트는 조건에 따라 다른 항목을 표시해야 하는 경우가 많음
* React는 if문, 삼항 연산자와 같은 자바스크립트 문법을 사용하여 조건부로 JSX를 렌더링 할 수 있음

조건부로 JSX 반환하기[실습] <br>

isPacked가 true면 ✅ 표시하기
``` jsx
import Items from "./Items"

export default function PackingList() {
    return(
        <section>
            <h1>여행 짐 리스트</h1>
            <ul>
                <Items 
                isPacked={false}
                name="여분 옷"
                />
                <Items 
                isPacked={false}
                name="노트북"
                />
                <Items 
                isPacked={true}
                name="컵라면"
                />
            </ul>
        </section>
    )
}
```
``` jsx
export default function Items({name, isPacked}) {
    if(isPacked) {
        return <li>{name}✅</li>
    }
    return <li>{name}</li>
}
```
* 조건문에 중복되는 코드 `return <li>{name}</li>`가 존재<br>
* 유지 보수를 더 어렵게 만들 수 있음


삼항 연산자를 사용하여 중복 코드 제거하기
``` jsx
// 삼항 연산자를 사용하여 중복 코드 제거하기(방법 1)
export default function Items({name, isPacked}) {
    return <li>{name} {isPacked ? "✅" : ""}</li>
}
```
``` jsx
// 삼항 연산자를 사용하여 중복 코드 제거하기(방법 2)
export default function Items({name, isPacked}) {
    return(
      <li>
          {isPacked ? name + '✅' : name}
      </li>
    )
}
```

`<del>` 태그 추가하기
``` jsx
export default function Items({name, isPacked}) {
    return(
      <li>
          {isPacked ? (<del>{name + '✅'}</del>) : (name)}
      </li>
    )
}
```

논리 연산자 AND(&&) 사용하기
* if문이나 삼항 연산자를 사용하는 방법 외에 일반적으로 사용하는 또 다른 방법은 JavaScript 논리연산자 사용하는 것
* React 컴포넌트에서는 조건이 참일 때 일부 JSX를 렌더링하고, 거짓이면 아무것도 렌더링하지 않는 경우가 많음
```jsx
// && 연산자 사용
export default function Items({name, isPacked}) {
    return(
        <li>
        {name} {isPacked && '✅'}
        </li>
    )
}
```
* JavaScript &&표현식은 왼쪽이 true면 오른쪽의 값을 반환, 그러나 조건이 false면 전체 표현식이 false가 됨
* React는 false를 null 또는 undefined처럼 JSX 트리의 "구멍"으로 간주하고 그자리에 아무것도 렌더링하지 않음
* && 왼쪽에 숫자를 두면 안됨
* JavaScript는 조건을 테스트하기 위해 표현식 왼쪽을 자동으로 부울(bool)로 변환. 그러나 왼쪽이 숫자 0이면 전체 식이(0)을 얻게 되고, React는 0을 렌더링

변수에 조건부로 JSX를 할당하기
* 지금까지의 방법이 방해가 되거나 불편하다면, if문과 변수를 함께 사용하면 좋음
``` jsx
// 변수에 조건부로 JSX를 할당하기
export default function Items({name, isPacked}) {
    let itemContent = name;
    if(isPacked) {
        itemContent = <del>{name + "✅"}</del>
    }
    return(
        <li>
            {itemContent}
        </li>
    )
}
```
* let으로 정의된 변수는 재할당할 수 있으므로 표시할 기본 내용인 name을 먼저 대입
* 다음으로 if문을 사용하여 isPacked가 true인 경우 JSX 표현식을 itemContent에 다시 할당
* return문의 JSX 트리에 중괄호를 사용, 위의 if문에서 계산된 변수 itemContent를 JSX 내부에 중첩하여 포함

리스트 렌더링
* 컴포넌트에서 여러 개의 데이터를 같은 형식으로 출력해야 하는 경우가 있음
* 이럴 때 JavaScript의 배열 관련 함수를 사용해, 배열을 컴포넌트의 기능에 맞게 렌더링할 수 있음

배열을 데이터로 렌더링하기
``` jsx
<ul>
  <li>스파이더맨: 피터 파커</li>
  <li>아이언맨: 토니 스타크</li>
  <li>배트맨: 브루스 웨인</li>
  <li>슈퍼맨: 클라크 켄트</li>
  <li>헐크: 브루스 배너</li>
</ul>
```
* 이러한 형식의 리스트 항목의 특이한 점은 콘텐츠, 즉 데이터
* 댓글 목록이나 이밎 갤러리 등의 인터페이스를 구축할 때 자주 사용되는 형태
  * 이런 경우 컴포넌트의 목록에 있는 데이터를 각각의 객체로 표시해야 함
* 즉 해당 데이터를 JavaScript 배열에 저장해야, map()과 filter() 같은 함수를 사용해서 리스트를 렌더링 할 수 있음

``` jsx
const heroes = [
        '스파이더맨: 피터 파커',
        '아이언맨: 토니 스타크',
        '배트맨: 브루스 웨인',
        '슈퍼맨: 클라크 켄트',
        '헐크: 로버트 브루스 배너'
    ];

export default function MovieHeroes() {
    const listHeroes = heroes.map(hero => <li>{hero}</li>);
    return(
        <section>
            <h1>영화 속 영웅들</h1>
            <ul>
                {listHeroes}
            </ul>
        </section>
    )
}
```
* 브라우저에서 출력을 확인해보면 정상적으로 출력되는 것을 확인할 수 있음
* 하지만 콘솔에서 확인하면 다음과 같은 경고메시지를 확인할 수 있음
* > Each child in a list should have a unique "key" prop.

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