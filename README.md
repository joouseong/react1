# 주우성 202230236

## 6월 5일(14주차)
### State와 렌더링

스냅샷처럼 동작하는 State - 실습
``` jsx
import { useState } from "react";

export default function BtnClick() {
    const [number, setNumber] = useState(0);

    function handleIncrease3 () {
        setNumber(number + 1);
        console.log(number)
        setNumber(number + 1);
        console.log(number)
        setNumber(number + 1);
        console.log(number)
    }
    function handleIncrease5 () {
        setNumber(number + 5);
        console.log(number)
        alert(number);
        console.log(number)
    }
    function handleTimer() {
        setNumber(number + 5);
        console.log(number)
        setTimeout(() => { // 많이쓰는 함수
            alert(number);
        }, 1000);
        console.log(number)
    }

    return(
        <>
            <h1>{number}</h1>
            <button onClick={handleIncrease3}>+3</button>&nbsp;
            <button onClick={handleIncrease5}>+5</button>&nbsp;
            <button onClick={handleTimer}>Timer</button>
        </>
    )
}
```
* React에 저장된 state는 alert 창이 실행될 때 변경될 수 있음
* 그러나 사용자가 상호작용한 시점에 이전에 저장되어 있던 state 스냅샷을 사용
* 이것은 이전의 스냅샷이 저장되는 순간 이미 예약된 작업
* 이벤트 핸들러의 코드가 비동기식이라도 렌더링하는 동안 state 변수 값은 절대 변경되지 않음
* 해당 렌더링의 onClick 내에서, setNumber(number + 5)가 호출된 후에도 number의 값은 계속 0
* 이 값은 컴포넌트를 호출해 React가 UI의 "스냅샷을 찍을" 때 고정된 값
* React는 렌더링의 이벤트 핸들러 안에서 state 값을 "고정"으로 유지함

React state 업데이트의 배치처리
* set함수로 state 변수를 저장하면 새로운 렌더링이 큐에 들어감
* 그러나 경우에 따라서는 렌더링을 큐에 넣기 전에, state 변수 값에 몇 가지 작업을 수행하고 싶을 때도 있음
* 이런 경우를 대비해 React가 state 업데이트를 어떻게 배치처리(batches, 일괄처리)하는지를 이해하는 것이 도움이 됨
* 이전 실습에서 확인한 바와 같이 각 렌더링의 state 값은 고정되어있음
* 따라서 setNumber(number + 1)을 계속 호출한다 하더라도 number 값은 항상 0
<br>
<br>
* 그러나 React는 이벤트 핸들러의 모든 코드가 실행될 때까지 state를 업데이트하지 않고 대기
* 따라서 setNumber() 호출이 모두 완료된 이후에만 리렌더링이 일어남
* 여기서 렌더링의 의미를 화면에표시되는 것으로 생각하면 안됨
    * -> "렌더링 과정의 3단계"에서 설명한 바와 같이 렌더링은 계산하는 단계
* React는 이와 같은 프로세스로 동작하기 때문에 불필요하게 많은 트리거의 발생 없이 복수의 state변수를 업데이트할 수 있게 됨
* 이 프로세스를 배칭(batching)이라고 함
<br>
<br>
* 흔한 사례는 아니지만, 다음 렌더링을 하기 전에 동일한 state변수를 여러 번 업데이트 하고 싶은 경우
* 이런 경우 setNumber(number + 1)과 같이 다음 state 값을 전달하는 대신, setNumber(n +> n + 1)과 같이 큐에서 하나 전 state를 기반으로 다음 state를 계산하는 함수를 전달할 수 있음
* 이것은 단순히 state 값을 대체하는 것이 아닌, React에게 "이 state 값을 이렇게 처리해" 라고 지시하는 방법

큐에서 하나 전의 state로, 다음 state를 계산하는 컴포넌트 제작 - 실습
``` jsx
import { useState } from "react";

export default function BtnClick() {
    const [number, setNumber] = useState(0);

    function handleIncrease3 () {
        setNumber(n => n + 1);
        console.log(number)
        setNumber(n => n + 1);
        console.log(number)
        setNumber(n => n + 1);
        console.log(number)
    }
    return(
    <>
        <h1>{number}</h1>
        <button onClick={handleIncrease3}>+3</button>&nbsp;
    </>
    )
}
```
* 여기서 n => n + 1 은 업데이터 함수(updater function)라고 부름
* 이 업데이터 함수를 set함수에 전달할 때 React는 다음과 같이 동작
    1. React는 이벤트 핸들러의 다른 코드가 모두 실행된 후에 이 함수가 처리되도록 큐에 넣음
    2. React는 큐를 순화하며 렌더링을 진행, 최종 업데이트된 state를 제공
    3. 이렇게 저장된 새로운 state는 다음 렌더링에 사용
* console에서는 3개의 0이 출력, 초기값을 출력하기 때문

[Note] 화살표 함수의 축약
* setNumber(n => n + 1)의 n => n + 1 은 화살표 함수가 축약된 표현
* 화살표 함수 전체를 표기하면 (n) => {return n + 1;}와 같이 작성
* 여기서 매개변수가 하나이면 소괄호()를 생략할 수 있음
* 그리고 실행문이 하나이고, return만 하는 경우 return 키워드와 중괄호{}를 생략할 수 있음
* 결과적으로 n => n + 1 과 같이 축약해서 사용할 수 있음

[Note] number를 n으로 사용한 이유
* state 변수로 선언된 number대신 set함수에서 n으로 사용하는 것은 둘을 구분하기 위한 것
* number 변수는 렌더링 후 최종적으로 결정된 값을 저장하는 반면, n은 업데이터 함수 내에서 지역적으로 사요되는 것임을 분명히 하기 위함
* 이렇게 구분하여 사용하면 가독성에도 유리
* 물론 n이 아닌 number를 그대로 사용해도 상관없지만 React에서는 구분하여 사용할 것을 권장
* React에서 권장하는 업데이터 함수의 명명규칙은 해당 state 변수의 첫 글자로 지정하는 것

### React 프로젝트 배포하기(feat. GitHub)
GitHub Pages 기본 저장소란?
* GitHub Pages를 운영하려면 먼저 GitHub Pages 저장소를 생성해야 함
* 생성방법은 일반 저장소 생성과 동일, 저장소 이름은 도메인 형태로 해야함

* 또한 최상위 도메인 부분은 .com이 아닌 .io로 해야함
    > `<My GitHub ID>.github.io`
* GitHub에서 직접 저장소를 만들었다면 clone해서 local에서 작업하고 push
* 처음부터 저장소를 local에 만들었다면 그대로 push(추천)

React 프로젝트 배포하기
* GitHub에 joouseong.github.io라는 저장소가 있다면 삭제
* React 프로젝트를 joouseong.github.io라는 이름으로 새로 만들고, git으로 초기화
* commit 후 public으로 push
* npm i ph-pages 로 인스톨
* package.json에서 맨 위에 "homepage": "https://My-GitHub-ID.github.io", 와
* "script"에 "predeploy": "npm run build", "deploy": "gh-pages -d dist" 추가
* GitHub리포지토리 페이지에서 setting->pages->Branch를 gh-pages로 변경
* npm run deploy를 터미널에서 사용하면 배포 완료
* 코드가 수정되고 다시 npm run deploy로 재배포

### 객체,배열 State
공식 문서 참고(https://ko.react.dev/learn/updating-objects-in-state)

---
## 5월 27일(13주차)
### React가 State를 강조하는 이유
React에서 초보자가 가장 먼저 배워야 하는 것
* JSX / Component / Props 그리고 State
* 실제로 React를 React답게 만드는 것은 대부분 state

기능 | React 없이도 가능?
|---|---|
컴포넌트 분리 | 가능
함수 호출 | 가능
템플릿 문자열 | 가능
HTML 생성 | 가능
state기반 재렌더링 | React 핵심

* React의 핵심 철학 자체가 state 기반 UI이기 때문
* React의 렌더링 모델 대부분이 state로 설명되기 때문
* 다른 Hook들도 결국 state 문제를 해결하기 위한 도구이기 때문
* React 팀이 "DOM 직접 조작" 보다 "상태 기반 사고"를 가장 중요하게 보기 때문
* Effect 같은 핵심 모델이 아니라 예외 처리에 가깝기 때문

* useState를 배우면 동시에: 렌더링 / 재렌더링 / 스냅샷 / 이벤트 핸들러 / JSX 재생성 / React의 함수 호출 방식 / 선언형 UI / Virtual DOM 과 같은 개념들이 연결되기 시작함
* 반대로 useEffect 같은 것은 사실 React의 "핵심 철학"이라기보다 외부 시스템과 동기화하기 위한 예외 처리 장치에 가까움

* 결국 나머지는 "state가 변하면 UI를 어떻게 유지할 것인가?"라는 문제를 해결하는 도구

기능 | 하는 일
|---|---|
useState | State 저장
useReducer | 복잡한 State 관리
Context | State 공유
useMemo | state 기반 계싼 최적화
useEffect | state 변화 후 동작
Suspense | 비동기 상태 처리
Server Components | 상태 경계 최적화

### 이벤트와 상호작용
State Hook의 동작 원리 - State Hook의 기본 동작 정리
* React에서는 useState와 같이 "use"로 시작하는 모든 함수를 Hook이라고 함
* Hook은 React가 오직 렌더링 중일 때만 사용할 수 있는 특별한 함수
* Hook을 사용하면 다양한 React 기능을 "연결(hook into)"할 수 있음
* useState는 React에서는 제공하는 여러가지 Hook 중 하나
* Hook의 사용에는 몇가지 주의할 점이 있음
    1. Hook을 사용하기 위해서는 일반 모듈과 마찬가지로 import 해서 사용
    2. Hook은 컴포넌트의 최상위 수준 또는 사용자 정의 Hook에서만 호출할 수 있음
    3. 조건문, 반복문 또는 기타 중첩 함수 내부에서는 Hook을 호출할 수는 없음
* Hook은 함수의 형식을 취하고 있지만 "컴포넌트가 어떤 기능을 필요로 하는지 React에게 자신의 요구사항을 알려주는 선언문"으로 이해하는 것이 좋음

* useState(0)는 문법적으로는 함수 호출
* 그러나 React의 관점에서 보면 Hook의 사용은 "함수를 실행해서 값을 받아온다"는 의미 보다는 "이 컴포넌트는 state가 필요하다"라고 선언하는 것으로 이해하는 것이 좋음

* useState를 호출하는 것은, React에 이 컴포넌트가 무언가를 기억하기를 원한다고 말하는 것
> const [index, setIndex] = useState(0);
* 이 경우 React가 index를 기억하기를 원한다는 선언
* 변수 이름이 결정되면 setter함수의 이름은 변수이름 앞에 set을 붙여 지정하는 것이 관례적인 규칙
* 원하는 이름을 사용할 수도 있지만 가독성을 현저히 떨어트리게 되기 때문에 이 규칙은 엄격하게 지키는 것이 좋음

* 실제 작동 방식
    1. 컴포넌트가 초기 렌더링
    2. index의 초기값으로 useState를 사용해 0이 전달되었기 때문에 [0, setIndex]를 반환
        - -> React는 0을 최신 state 값으로 기억
    3. 사용자가 버튼을 클릭하면 setIndex(index + 1)을 호출
        - -> 현재 index의 값은 0이기 때문에 setIndex(1)이 됨
        - -> 이것은 React에 index의 값이 1이라는 것을 기억하게 하고, 두번째 렌더링을 함
    4. React는 여전히 useState(0)을 보고 있지만, index가 1로 설정된 것을 기억하고 있기 때문에
        - -> 이번에는 [1, setIndex]를 반환
    5. 버튼을 클릭할 때 마다 이와 같은 동작이 반복

State Hook의 동작 원리 - 여러 개의 state를 사용하기
* 하나의 컴포넌트에서 사용할 수 있는 state 변수의 개수에는 제한이 없으며, 원하는 타입의 state 변수를 가질 수 있음
* 하나의 컴포넌트에 두개 이상의 state 변수를 사용할 때가 있음
* 이런 경우 변수들이 함꼐 변경해야 하는 경우가 자주 발생한다면, 변수는 하나로 합치는 것이 더 좋을 수도 있음
* 예를 들어, 필드가 많은 폼의 경우 필드별로 state 변수를 사용하는 것보다 하나의 객체 state 변수를 사용하는 것이 더 편리

실습
1. 불린(Boolean) 타입의 State를 추가. 변수의 이름은 more
2. 토글할 수 있는 handleClick 이벤트 핸들러 작성
3. 버튼 추가, 클릭 이벤트에 handleMoreClick 핸들러 전달
4. 버튼 라벨은 more의 값에 따라 Hide description / Show description 으로 변경
5. More가 true면 description을 보여주고, false면 보이지 않게 함

``` jsx
import { galleryImages } from "./imgData";
import { useState } from "react";
import style from "./Carousel.module.css"

export default function Carousel() {
    const [index, setIndex] = useState(0);
    // 1. 불린 타입의 State 추가(기본값은 닫혀있도록 false 설정)
    const [more, setMore] = useState(false);

    function handleNext() {
        if (index === galleryImages.length -1) {
            setIndex(0);
        } else {
            setIndex(index + 1);
        }
    }

    function handlePrevious() {
        if (index === 0) {
            setIndex(galleryImages.length -1);
        } else {
            setIndex(index - 1);
        }
    }

    // 2. 토글할 수 있는 이벤트 핸들러 작성
    function handleMoreClick() {
        setMore(!more); // 현재 상태의 반대 값으로 토글
    }

    let slide = galleryImages[index];
    return (
        <section className={style.wrapper}>
            <h2>
                <i>{slide.name} </i>
                by {slide.artist}
            </h2>
            <h3>
                ({index + 1} of {galleryImages.length})
            </h3>
            <img src={slide.url} alt={slide.alt} />
            <p>
                <button onClick={handlePrevious} className={style.button}>Previous</button>
                <button onClick={handleNext} className={style.button}>Next</button>
            </p>

            {/* 3. 버튼 추가 및 handleMoreClick 핸들러 전달 */}
            {/* 4. more 값에 따라 버튼 라벨 변경 (삼항 연산자 사용) */}
            <button onClick={handleMoreClick}>
                {more ? "Hide" : "Show"} description
            </button>

            {/* 5. more가 true일 때만 description을 보여줌 (단락 평가 && 사용) */}
            {more && <p>{slide.description}</p>}
        </section>
    )
}
```

실습 2
* 루트 컴포넌트에 Carousel 컴포넌트를 두번 호출
* 각각의 캐러셀 내부 버튼을 클릭하면 state가 각 컴포넌트에서 독립적으로 동작한다는 것을 알 수 있음
* state는 렌더링된 화면에서 컴포넌트 객체에 지역적
* 동일한 컴포넌트를 몇 번 중첩해서 렌더링해도 각 복사본은 완전히 격리된 state를 가짐
* 어떤 컴포넌트의 state를 변경해도 다른 컴포넌트의 state에는 영향을 미치지 않음
* 이것이 state 변수와 컴포넌트 상단에 선언한 일반 변수(로컬 변수)의 차이
* Carousel 컴포넌트를 두 번 렌더링했기 때문에 각각의 컴포넌트 state는 별도로 저장됨
* Props와는 달리 state는 선언한 컴포넌트 외에는 완전히 비공개

* 부모 컴포넌트도 state를 변경할 수 없음
* state의 이런 특성 때문에 다른 컴포넌트에 영향을 미치지 않고, 어떤 컴포넌트에서나 state를 추가하거나 제거할 수 있음
* 만약 두 개의 캐러셀 state를 동기화하고 싶다면, 자식 컴포넌트에서 state를 제거하고, 가장 가까운 공통 부모 컴포넌트에 state를 추가하면 됨
    - -> 이것을 컴포넌트 간 state 공유라고 함

### State와 렌더링
렌더링 과정의 3단계
* React는 컴포넌트가 화면에 표시되기 전에 렌더링(rendering) 과정을 거치게 됨
* 렌더링이 되는 프로세스를 이해하면 코드가 어떻게 실행되는지 이해하는데 도움이 됨
* React와 렌더링 프로세스는 렌더링 트리거, 컴포넌트 렌더링, DOM에 커밋 등 3단계로 진행
<br>
<br>

1단계 : 렌더링 트리거(Rendering Trigger)
* 컴포넌트 렌더링이 일어나는 이유는 2가지
1. 컴포넌트의 초기 렌더링인 경우
    * 앱을 시작할 때는 초기 렌더링을 촉발시켜야 함. 이 과정을 렌더링 트리거라고 함
    * 대상 DOM 노드와 함께 createRoot를 호출한 다음 해당 컴포넌트로 render 메서드를 호출하면 이 작업이 완료됨
    ``` jsx
    import { StrictMode } from 'react'
    import { createRoot } from 'react-dom/client'
    import App from './App.jsx'

    createRoot(document.getElementById('root')).render(
        <StrictMode>
        <App />
        </StrictMode>,
    )
    ```
    * 위 코드는 main.jsx. Vite로 프로젝트를 생성한 경우 index.jsx의 역할을 main.jsx가 함
    * id가 root인 엘리먼트 즉 DOM 노드를 createRoot()함수로 호출한 다음, render 메소드를 통해 App 컴포넌트를 호출하고 있음
    * 이 작업이 초기 렌더링 작업
    * createRoot(...).render(...) 메소드의 호출을 주석 처리하면 컴포넌트의 화면 출력이 사라짐

2. 컴포넌트의 state가 업데이트된 경우
    * 컴포넌트가 초기 렌더링 된 후에는 set 함수를 통해 state를 업데이트해서, 추가적인 렌더링을 촉발시킬 수 있음
    * 컴포넌트의 state를 업데이트하면 자동으로 렌더링 큐(queue)에 추가되고, 순서대로 렌더링

    [Note] StrictMode 컴포넌트
    * StrictMode 컴포넌트는 개발 모드에서 애플리케이션의 잠재적인 버그와 부작용(Side Effects)을 조기에 발견할수 있도록 돕는 검사 도구
    * 배포(Production) 환경에서는 전혀 영향을 주지 않는 안전장치
    * 이중 렌더링 검사 / Effect 및 Ref 클린업 테스트 / 지원 중단된 API 경고 등

    [Note] 렌더링 큐(Queue)
    * 큐는 선입선출(FIFO: First In First Out) 자료구조
    * 렌더링 큐는 렌더링 요청을 순서대로 보관했다가 가장 먼저 요청한 것부터 차례대로 처리 자료구조를 사용
<br>
<br>

2단계 : 컴포넌트 렌더링
* 1단계인 "렌더링 트리거" 직후 React는 컴포넌트를 호출해서 화면에 표시할 내용을 파악. 즉 "렌더링"은 React가 컴포넌트를 호출하는 것
* 초기 렌더링에서 React는 루트 컴포넌트를 호출
* 초기 렌더링 이후 React는 state 업데이트가 일어나면 렌더링을 촉발시킨 컴포넌트를 호출
* 이 프로세스는 재귀적(Precursive)으로 발생
* State의 업데이트가 발생한 컴포넌트가 다른 컴포넌트를 중첩하고 있다면, 해당 컴포넌트를 렌더링. 중첩되어 반환된 컴포넌트를 호출했는데 그 컴포넌트도 중첩되어 있다면 중첩이 띁날 때까지 렌더링은 계속됨
* React에서 재귀적이라고 하는 것은 우리가 알고 있는 재귀 함수(recursive function)과는 조금 차이가 있음
* 재귀 함수는 어떤 함수가 자기 자신을 다시 호출하는 것
* React에서의 재귀적 렌더링은 특정 컴포넌트가 더 이상 다른 컴포넌트를 중첩(호출)하지 않을 때까지 렌더링을 반복하는 것
<br>
<br>

3단계 : React가 DOM에 변경사항을 커밋
* React는 컴포넌트를 렌더링(호출)한 후에 DOM을 수정
* 초기 렌더링의 경우 appendChild() DOM API를 사용해, 생성한 모든 DOM 노드를 화면에 표시
* 리렌더링의 경우 최신 렌더링의 출력과 일치하도록 DOM을 변경하기 위해 필요한 최소한의 작업을 적용.
<br>
<br>

스냅샷처럼 동작하는 State
* State 변수는 읽고 쓸 수 있는 일반 자바스크립트 변수처럼 보일 수 있음
    - -> 그러나 State는 스냅샷처럼 동작
* state 변수를 set함수로 업데이트해도 이미 가지고 있는 state 변수는 변경되지 않고, 대신 리렌더링이 촉발(트리거)됨
* 컴포넌트의 state에서는 set함수의 호출이 트리거로 작용하여 렌더링이 이루어짐

[Note] 스냅샷(Snapshot)
* 스냅샷은 원래 자연스러운 순간이나 동작을 재빠르게 포착하여 찍은 사진을 의미
* 그러나 IT 분야에서는 "특정 시간대의 데이터 및 파일 시스템 상태"를 그대로 기록해 두는 기술을 의미. 물론 필요한 시점에 기록해둔 스냅샷을 복원하는 것도 가능
<br>
<br>

* "렌더링"이란 React가 컴포넌트(함수)를 호출한다는 뜻
* 호출된 컴포넌트에서 반환하는 JSX는 특정 시점의 UI 스냅샷과 같은 것
* prop, 이벤트 핸들러, 로컬 변수는 모두 렌더링 시점에 state를 사용해서 계산됨
* React가 컴포넌트를 다시 렌더링되는 과정을 정리하면
    1. 상호작용이 발생하면 React가 컴포넌트를 다시 호출, 스냅샷을 계산
    2. 컴포넌트가 새로운 JSX 스냅샷을 반환
    3. React는 컴포넌트가 반환한 스냅샷과 일치하도록 화면을 업데이트. 즉, DOM tree를 업데이트
    4. 새로운 state로 이벤트 핸들러를 생성하고 다음 상호작용을 기다림
<br>
<br>

* state는 컴포넌트의 메모리로 동작하기 때문에, 보통의 함수가 반환된 후 사라지는 일반 변수와는 다름
* state는 컴포넌트 내부에 있는 것이 아닌 React 내부에 존재
* React가 컴포넌트를 호출하면 특정 렌더링에 대한 state의 스냅샷을 제공
* 스냅샷을 제공받은 컴포넌트는 해당 렌더링의 state 값으로 계산된 새로운 props 세트와 이벤트 핸들러가 포함된 UI 스냅샷을 JSX에 반환
* 상호작용이 발생하면 state는 다음과 같은 작업을 수행
    1. React에 state를 업데이트하라고 명령
    2. React가 state 값을 업데이트
    3. React는 업데이트된 state 값의 스냅샷을 컴포넌트에 전달

---
## 5월 20일(12주차)
### 이벤트와 상호작용
로컬 변수에 컴포넌트 상태 저장 - 실습
``` jsx
import slider1 from "./localImg1.jpg";
import slider2 from "./localImg2.jpg";

export const slide = {
    slider1,
    slider2
}
```
``` jsx
import { slide } from "./images"

export const galleryImages = [
    {
        name: "Slide 1",
        artist: "Artist 1",
        description: "Placeholder image for slide 1",
        url: "https://placehold.co/600x400?text=slide1",
        alt: "Slide 1",
    },
    {
        name: "Slide 2",
        artist: "Artist 2",
        description: "Placeholder image for slide 2",
        url: "https://placehold.co/600x400?text=slide2",
        alt: "Slide 2",
    },
    {
        name: "Slide 3",
        artist: "Artist 1",
        description: "Placeholder image for slide 3",
        url: "https://placehold.co/600x400?text=slide3",
        alt: "Slide 3",
    },
    {
        name: "Slide 4",
        artist: "Artist 4",
        description: "Placeholder image for slide 4",
        url: slide.slider1,
        alt: "Slide 4",
    },
    {
        name: "Slide 5",
        artist: "Artist 5",
        description: "Placeholder image for slide 5",
        url: slide.slider2,
        alt: "Slide 5",
    },
]
```
``` jsx
import { galleryImages } from "./imgData";

export default function Carousel() {
    let index = 0;

    function handleClick() {
        index = index + 1;
        console.log(index);
    }

    let slide = galleryImages[index];
    return (
        <>
            <button onClick={handleClick}>Next</button>
            <h2>
                <i>{slide.name} </i>
                by {slide.artist}
            </h2>
            <h3>
                ({index + 1} of {galleryImages.length})
            </h3>
            <img src={slide.url} alt={slide.alt} />
            <p>{slide.description}</p>
        </>
    )
}
```
* VS code의 설정이나 사용하는 익스텐션에 따라서 다를 수 있지만 지금 작성한 Carousel에서는 다음과 같은 오류가 발생할 수 있음
>(index):1 Uncaught (in promise) Error: A listener indicated an asynchronous response by returning true, but the message channel closed before a response was received

* handleClick 이벤트 핸들러는 컴포넌트 스코프의 지역 변수 index를 업데이트 하고 있음
* 하지만 이러한 index가 변해도 화면이 변하지 않는 이유는 두가지가 있음
    1. 지역 변수는 렌더링과 렌더링 사이의 변화가 유지되지 않음. React는 이 컴포넌트를 두 번째로 렌더링할 때 지역 변수에 대한 변경 사항은 고려하지 않고 다시 처음부터 렌더링 함
    2. 지역 변수에 변화가 있어도 React는 새로운 데이터로 컴포넌트를 다시 렌더링해야 한다는 것을 인식하지 못함. 즉 버튼 클릭으로 index가 1로 변겨오디어도, React는 index의 초기값 0을 적용

State Hook에 컴포넌트 상태 저장하기
* React에서는 현재의 상태를 보관할 수 있는 메모리를 제공. (State Hook)
* State Hook을 사용하려면 React에서 useState를 import해야 함
* 그 다음 변수로 선언했던 index를 state로 변경
* index는 state 변수이고, setIndex는 state를 변경하는 setter 함수
* Setter 함수의 이름은 일반적으로 변수 이름 앞에 set을 붙인 함수명을 사용
* useState(0)은 현재 index의 초기값을 0으로 초기화 한 것

실습
* Carousel.jsx 수정
``` jsx
import { galleryImages } from "./imgData";
import { useState } from "react";

export default function Carousel() {
    const [index, setIndex] = useState(0);

    function handleClick() {
        setIndex(index + 1);
        console.log(index);
    }

    let slide = galleryImages[index];
    return (
        <>
            <button onClick={handleClick}>Next</button>
            <h2>
                <i>{slide.name} </i>
                by {slide.artist}
            </h2>
            <h3>
                ({index + 1} of {galleryImages.length})
            </h3>
            <img src={slide.url} alt={slide.alt} />
            <p>{slide.description}</p>
        </>
    )
}
```
* 이미지는 index 1의 이미지가 렌더링 되지만 console을 보면 index 출력 값은 여전히 0인 상태
* 초기 렌더링은 state의 초기값이 0이기 때문에 index 0의 이미지가 출력
* 클릭을 하면 handleClick 함수는 setIndex에는 1을 더한 값이 저장되지만 console.log에서는 현재 index 값이 유지되기 때문

* 클릭을 계속 진행하면 5번째 클릭에서 더 이상 화면이 렌더링 되지 않음
* 기본적으로 React는 렌더링 중에 오류가 발생하면 화면에서 UI를 제거하기 때문에 나타나는 현상
* 이때 console을 보면 다음과 같은 오류메세지 확인 가능
> An error occurred in the <Carousel> component. <br>
Consider adding an error boundary to your tree to customize error handling behavior.
Visit https://react.dev/link/error-boundaries to learn more about error boundaries.

* 이 오류 메세지는 Carousel 컴포넌트에서 오류가 발생했는데 "오류 처리 동작을 사용자 지정하려면 트리에 error boundary를 추가" 하라는 것
* 즉 빈화면이 아니라 error boundary를 이용해 오류메세지를 출력할 수 있도록 수정하라는 것

실습
* 이 오류는 index값이 galleryImages 배열의 길이인 5를 넘어서기 때문에 발생
* 즉 index 값이 4를 넘지 않도록 수정하는 것으로 해결할 수 있음
``` jsx
import { galleryImages } from "./imgData";
import { useState } from "react";

export default function Carousel() {
    const [index, setIndex] = useState(0);

    function handleClick() {
        if (index === galleryImages.length -1) {
            setIndex(0);
        } else {
            setIndex(index + 1);
            console.log(index);
        }
    }

    let slide = galleryImages[index];
    return (
        <>
            <button onClick={handleClick}>Next</button>
            <h2>
                <i>{slide.name} </i>
                by {slide.artist}
            </h2>
            <h3>
                ({index + 1} of {galleryImages.length})
            </h3>
            <img src={slide.url} alt={slide.alt} />
            <p>{slide.description}</p>
        </>
    )
}
```

실습 - Previous 버튼과 이벤트 핸들러를 추가해 캐러셀 완성하기
``` css
.wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.button{
    padding: 10px 20px;
    margin: 5px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}
```
``` jsx
import { galleryImages } from "./imgData";
import { useState } from "react";
import style from "./Carousel.module.css"

export default function Carousel() {
    const [index, setIndex] = useState(0);

    function handleNext() {
        if (index === galleryImages.length -1) {
            setIndex(0);
        } else {
            setIndex(index + 1);
        }
    }

    function handlePrevious() {
        if (index === 0) {
            setIndex(galleryImages.length -1);
        } else {
            setIndex(index - 1);
        }
    }

    let slide = galleryImages[index];
    return (
        <section className={style.wrapper}>
            <h2>
                <i>{slide.name} </i>
                by {slide.artist}
            </h2>
            <h3>
                ({index + 1} of {galleryImages.length})
            </h3>
            <img src={slide.url} alt={slide.alt} />
            <br />
            <button onClick={handlePrevious} className={style.button}>Previous</button>
            <button onClick={handleNext} className={style.button}>Next</button>
            <p>{slide.description}</p>
        </section>
    )
}
```

---
## 5월 13일(11주차)
### 이벤트와 상호작용
Note
* HTML의 `<video>`태그는 보통 `<video>...</video>`의 형태로 사용
* 앞쪽 태그 안의 속성 만으로 충분한 설정이 가능한 경우 싱글 태그 사용 가능
* 그러나 `<source>`, `<track>`, 대체 텍스트 등을 사용해야 하는 경우는 `<video>...</video>`의 형태로 사용할 수도 있음
* React에서 DOM을 제어할 때는 useRef를 사용하여 엘리먼트를 ref 객체로 관리하는 것이 좋음
* React에서는 DOM에 직접 접근하는 것을 권장하지 않음

실습 정리
* 이번 실습에서는 Toolbar 컴포넌트에서 Button컴포넌트를 호출하여, video 엘리먼트의 id와 이벤트 핸들러를 props로 전달
* Props를 전달받은 Button 컴포넌트는 버튼이 클릭될 때 onClick prop으로 이벤트 핸들러를 전달
* 단순한 prop의 전달이 아닌 함수, 즉 이벤트 핸들러를 직접 작성했다는 것이 핵심
* 또 한가지 handle.jsx와 같이 특정 함수만 모아 놓은 파일을 모듈이라고 함
* Handle과 같이 이벤트 핸들러만 모아놓으면 Button 컴포넌트와 함께 어떤 프로젝트에서도 재사용이 쉬워짐
* 이 밖에도 일반적으로 자주 사용하는 로직이 있다면 모듈의 형태로 관리하면 편리하게 사용 가능
* 💡모듈의 이름은 camelCase 사용, PascalCase는 컴포넌트에만 사용

이벤트의 전파 - 실습
``` jsx
export default function Bubble() {
    return (
        <>
            <h1 className={style.title}>Bubble</h1>
            <nav className={style.navBar} onClick={() => alert("네비게이션바 클릭")}>
                <button className={style.button} onClick={() => alert("버튼1 클릭")}>버튼1</button>
                &nbsp;
                <button className={style.button} onClick={() => alert("버튼2 클릭")}>버튼2</button>
            </nav>
        </>
    )
}
```
* 버튼1 클릭: "버튼1 클릭" => "네비게이션바 클릭"
* 버튼2 클릭: "버튼2 클릭" => "네비게이션바 클릭"
* 버튼이 아닌 네비게이션 영역 클릭: "네비게이션바 클릭"만 표시
* 여기서 주목해야할 것: DOM 트리의 하위에 있는 `<button>`이 실행되면, 그 이벤트가 DOM 트리의 상위 즉, 부모에게 전달 됨

이벤트 전파의 중지
* 이벤트 핸들러는 이벤트 오브젝트(object)를 유일한 매개 변수로 사용
* 관례적으로 이벤트 오브젝트를 의미하는 "event"를 "e"로 줄여 호출하는 것이 일반적
* 이 오브젝트는 이벤트의 정보를 읽어 들이는데 사용할 수 있음
* 또한 이벤트 오브젝트가 전파를 멈출 수 있게 해줌
* 이벤트가 부모 컴포넌트에 닿지 못하도록 막으려면, 다음 예제처럼 Bubble 컴포넌트에 Button 컴포넌트를 추가하고, e.stopPropagation()을 호출
``` jsx
function Button({onClick, children}) {
    return(
        <button onClick={e => {
            e.stopPropagation();
            onClick();
        }}>
            {children}
        </button>
    )
}
```
* 이제 버튼을 클릭하면 다음과 같은 절차 진행
    1. React가 `<Button>`에 전달된 onClick 핸들러를 호출
    2. Button 컴포넌트에 정의된 해당 핸들러는 다음을 수행
        - e.stopPropagation()을 호출하여 이벤트가 더 이상 버블링 되지 않도록 방지
        - Bubble 컴포넌트가 prop으로 전달해 준 onClick 함수 호출
    3. Bubble 컴포넌트에서 정의된 onClick 이벤트 핸들러 함수가 버튼의 alert를 표시
    4. 전파가 중단되었기 때문에 부모인 `<div>`의 onClick은 실행되지 않음
* 💡event는 SyntheticEvent 클래스의 instance

이벤트 전파의 중지 - 실습
``` jsx
function Button({onClick, children}) {
    return(
        <button className={style.button} onClick={e => {
            e.stopPropagation();
            onClick();
        }}>
            {children}
        </button>
    )
}

export default function Bubble() {
    return (
        <>
            <h1 className={style.title}>Bubble</h1>
            <nav className={style.navBar} onClick={() => alert("네비게이션바 클릭")}>
                <Button onClick={() => alert("버튼1 클릭")}>버튼1</Button>
                &nbsp;
                <Button onClick={() => alert("버튼2 클릭")}>버튼2</Button>
            </nav>
        </>
    )
}
```

Note
* Button 컴포넌트를 `<Button />`형태가 아닌 `<Button>...</Button>` 형태로 사용
* 다음과 같이 `<Button />` 형태로 사용할 수도 있음
``` jsx
function Button({onClick, value}) {
    return(
        <button className={style.button} onClick={e => {
            e.stopPropagation();
            onClick();
        }}>
            {value}
        </button>
    )
}

export default function Bubble() {
    return (
        <>
            <h1 className={style.title}>Bubble</h1>
            <nav className={style.navBar} onClick={() => alert("네비게이션바 클릭")}>
                <Button onClick={() => alert("버튼1 클릭")} value="버튼 1" />
                &nbsp;
                <Button onClick={() => alert("버튼2 클릭")} value="버튼 2" />
            </nav>
        </>
    )
}
```

브라우저 이벤트 기본 동작 방지하기 - 실습
* 브라우저 이벤트 중에는 자신만의 기본 동작을 갖고 있는 것이 있음
* 예를 들어 `<form>`을 제출하는 이벤트인 onSubmit 이벤트는 `<form>` 내부의 버튼을 클릭할 때 페이지 전체를 리로드하는 것을 기본 동작으로 함
``` jsx
export default function Signup1() {
    return(
        <form onSubmit={() => alert("Submitting!")}>
            <input />
            <button>Send1</button>
        </form>
    )
}
```
* 리로드하는 기본 동작을 방지하는 코드
``` jsx
export default function Signup2() {
    return(
        <form onSubmit={e => {
            e.preventDefault();
            alert("Submitting!");
        }}>
            <input />
            <button>Send2</button>
        </form>
    )
}
```

e.stopPropagation()와 e.preventDefault()
* 전파를 중지하는 데는 둘 다 유용하지만, 전혀 다른 기능임
* e.stopPropagation()은 이벤트 핸들러가 상위 태그에서 실행되지 않도록 멈추는 기능
* e.preventDefault()는 브라우저 기본 동작을 갖고 있는 일부 이벤트가 해당 기본 동작을 실행하지 않도록 방지하는 기능
<br><br>
* 이벤트 핸들러는 사이드 이펙트를 위한 최고의 위치
* 함수를 렌더링 하는 것과 다르게 이벤트 핸들러는 순수할 필요가 없어 무언가를 변경하는데 최적의 위치
* 예를 들어 타이핑에 반응해 입력 값을 수정, 버튼 클릭에 따라 리스트를 변경할 때 적절
* 그러나 일부 정보를 수정하기 위해서는 먼저 그 정보를 저장하기 위한 수단이 필요
* 이를 위해 React에서는 컴포넌트의 정보를 저장하는 역할을 하는 state Hook을 통해 제공

State의 개념과 useState
* State는 컴포넌트의 기억장소
* 컴포넌트는 상호 작용의 결과로 화면의 내용을 변경해야 하는 경우가 많음
* 컴포넌트는 현재 입력 값, 현재 이미지, 장바구니의 상태와 같은 것들을 어딘가에 기억해야 함
* React는 이런 종류의 컴포넌트별 메모리를 state라고 부름

로컬 변수에 컴포넌트 상태 저장 - 실습
* 캐러셀 구현하기
* 로컬 변수를 이용해 index값 저장, 현재 저장된 index 값의 이미지 정보 렌더링
* index값의 결정은 버튼 이용, 클릭 이벤트 핸들러에서 index값을 하나씩 증가

* 로컬 이미지를 호출하려면 각각의 이미지를 import 해야함
* 이미지가 많은 경우 코드가 복잡해짐
* 이미지 디렉토리 안에 index파일을 만들어 그 안에서 처리, 이미지를 사용하는 컴포넌트는 가독성을 높일 수 있음

---
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