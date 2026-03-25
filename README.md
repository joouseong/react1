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

### 나의 첫 번째 프로젝트
step 1
``` react
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
``` react
function Profile () {
  return (
    <>
      
    </>
  )
}
```

step 2
