# \***\*Module 패턴\*\***

이 패턴에서는 코드들을 재사용 간으하면서도 작게 나눌 수 있게 해준다.

또한 모듈을 나누는 과정에서 특정 변수들을 파일 내에 private하게 할수 있는데, 모듈 스코프 내에 변수를 선언하고 명시적으로 외부에 export하지 않으면 바깥에서 해당 변수에 접근 할 수 없다.

이를 통해 전역 스코프로 인한 변수들 이름이 충돌하는 문제를 줄일 수 있다.

## ES2015 모듈

ES2015에서는 자바스크립트의 빌트인 모듈 기능이 추가되었다.

모듈은 자바스크립트 코드를 포함한 파일이며 일반적인 스크립트와 동작이 약간 다르다.

예를 들어, `math.js` 라는 파일을 만든다고 가정한다면 해당 함수들을 불러오기 위해

`math.js` 안에서 `export`를 한다.

```tsx
export function add(x, y) {
  return x + y
}

export function multiply(x) {
  return x * 2
}

export function subtract(x, y) {
  return x - y
}

export function square(x) {
  return x * x
}
```

다른 컴포넌트에서 이를 사용하고 싶다면 `import`를 사용하면 된다.

`import { add, multiply, subtract, square } from './math.js'`

## 장점

명시적으로 export한 값들만 외부에 노출된다.

그리고 명시적으로 export하지 않으면 모듈 내에서만 사용할 수 있다.(다른 객체지향 언어에서는 `private` 변수라고 명시한다.)

개발자는 전역 변수를 덮어쓰는 등의 걱정 없이 코드를 작성할 수 있다.

만약 export된 변수와 로컬 변수가 이름이 같다면 as 키워드를 통해 이름을 바꿔서 사용가능하다.

```tsx
import {
  add as addValues,
  multiply as multiplyValues,
  subtract,
  square,
} from './math.js'

function add(...args) {
  return args.reduce((acc, cur) => cur + acc)
}

function multiply(...args) {
  return args.reduce((acc, cur) => cur * acc)
}

/* From math.js module */
addValues(7, 8)
multiplyValues(8, 9)
subtract(10, 3)
square(3)

/* From index.js file */
add(8, 9, 2, 10)
multiply(8, 9, 2, 10)
```

`export` 와 `default export` 의 차이점은

`import` 시에 `export` 를 사용 시 `import { module } from 'module'` 로 작성하고

`default export` 사용 시 `import module from 'module'` 처럼 사용하면 된다.

만약 `default export` 사용시에는 `import` 에서 이름을 자유롭게 변경 가능하다.

그리고 `*`을 사용하여 모든 `export`를 `import`할 수 있다.

그리고 `as`를 통해 객체 형식으로 호출할 수 있다.

```tsx
import * as math from './math.js'

math.default(7, 8)
math.multiply(8, 9)
math.subtract(10, 3)
math.square(3)
```

## Dynamic import

파일의 맨 위에서 모듈들을 import 하면 파일 내 다른 코드들이 실행되기 전에 해당 모듈이 로드된다.

만약 특정 조건에서만 특정 모듈을 로드하고 싶다면 Dynamic import를 통해 필요할 때만 로드할 수 있다.

```tsx
import('module').then((module) => {
  module.default()
  module.namedExport()
})

// Or with async/await
;(async () => {
  const module = await import('module')
  module.default()
  module.namedExport()
})()
```

모듈을 동적으로 로딩하면 페이지 로딩 타임을 줄일 수 있다.

그리고 `import` 함수는 인자로 표현식을 받아서 템플릿 리터럴도 사용가능하다.

그래서 필요에 따라 변수로 필요 모듈을 받아오도록 할 수도 있다.

```tsx
const res = await import(`../assets/dog${num}.png`)
```

사용자의 입력이나 어떤 데이터의 결과에 따라 유연하게 모듈을 로드하여 사용할 수 있다.

모듈 패턴을 사용하면 코드의 일부분을 캡슐화 할 수 있다.

이렇게 하면 전역 변수 할당을 예방하고 여러 의존 모듈을 사용하거나 네임스페이스를 사용할 때 안전하다.

모든 자바스크립트 런타임에서 ES2015의 모듈을 사용하려면 바벨과 같은 트랜스파일러가 필요하다.
