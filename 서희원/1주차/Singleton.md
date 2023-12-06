# Singleton 패턴

Singleton은 `1회에 한하여` 인스턴스화가 가능하며 전역에서 접근 가능한 클래스를 지칭한다.

만들어진 *Singleton 인스턴스*는 앱 전역에서 공유되기 때문에 앱의 전역 상태를 관리하기에 적합하다.

```jsx
let counter = 0

class Counter {
  getInstance() {
    return this
  }

  getCount() {
    return counter
  }

  increment() {
    return ++counter
  }

  decrement() {
    return --counter
  }
}

const counter1 = new Counter()
const counter2 = new Counter()

console.log(counter1.getInstance() === counter2.getInstance()) // false 서로 다르다.
```

위의 코드를 보면

`new` 메서드를 두번 호출하여 `counter1` 과 `counter2` 를 각각 별개의 인스턴스를 가르키도록 했다. 각 인스턴스의 `getInstance` 메서드를 호출해 반환되는 레퍼러스는 같지 않다 (동일한 인스턴스가 아니다)

그렇다면 `한번만 호출할 수 있는` 싱글톤 패턴을 만들려면 어떻게 해야할까?

```jsx
let instance
let counter = 0

class Counter {
  constructor() {
    // 여기선 클래스 초기 설정으로 쓰이지만,
    if (instance) {
      // 보통 상위 클래스로 부터 상속받을 때, super를 안에 같이 사용한다.
      throw new Error('You can only create one instance!')
    }
    instance = this
  }

  getInstance() {
    return this
  }

  getCount() {
    return counter
  }

  increment() {
    return ++counter
  }

  decrement() {
    return --counter
  }
}

const counter1 = new Counter()
const counter2 = new Counter()
// Error: You can only create one instance!
```

사이트에서는 `instance` 변수를 만들어서 이미 할당된 변수면 에러를 발생시키는 코드를 만들었다.

→ 이로써 `한번만 호출 가능한` 싱글톤 패턴을 만들 수 있다.

그리고 추가로 수행해야 하는 부분이 있는데, `Counter` 인스턴스를 `export`하기 전에 인스턴스를 `freeze`해야 한다.

```jsx
const singletonCounter = Object.freeze(new Counter())
export default singletonCounter
```

`Object.freeze`메서드는 객체를 사용하는 쪽에서 직접 객체를 수정할 수 없도록 한다.(추가 및 수정도 불가능하다.)

`index.js`에 각각 빨간 버튼과 파란 버튼을 표시하는 컴포넌트를 `import`하고,

두 컴포넌트에서 `Counter` 클래스를 각각 `import`해서 사용한다면 `Counter` Singleton 인스턴스의 `counter`값은 양쪽 파일에서 모두 공유한다.

### 장점

인스턴스를 하나만 사용하기 때문에 많은 메모리 공간을 절약할 수 있다.

### 단점

안티패턴 혹은 자바스크립트에서는 하지 말아야 할 것으로 언급된다.

다른 Java나 C++은 객체를 직접적으로 만들 수 없기 때문에 객체를 위한 클래스를 작성해야하지만,

자바스크립트는 클래스를 작성하지 않아도 객체 리터럴을 통해 객체를 만들 수 있기 때문에 오버엔지니어링이라 볼 수 있다.

- 객체 리터럴 사용하기
  ```jsx
  let count = 0

  const counter = {
    increment() {
      return ++count
    },
    decrement() {
      return --count
    },
  }

  Object.freeze(counter)
  export { counter }
  ```

### 테스팅

테스트하는 것은 조금 까다롭다.

모든 테스트들은 이전 테스트에서 만들어진 전역 인스턴스를 수정할 수 밖에 없다.

```jsx
import Counter from '../src/counterTest'

test('incrementing 1 time should be 1', () => {
  Counter.increment()
  expect(Counter.getCount()).toBe(1)
})

test('incrementing 3 extra times should be 4', () => {
  Counter.increment()
  Counter.increment()
  Counter.increment()
  expect(Counter.getCount()).toBe(4)
})

test('decrementing 1  times should be 3', () => {
  Counter.decrement()
  expect(Counter.getCount()).toBe(3)
})
```

### 명확하지 않은 의존

```jsx
import Counter from './counter'

export default class SuperCounter {
  constructor() {
    this.count = 0
  }

  increment() {
    Counter.increment()
    return (this.count += 100)
  }

  decrement() {
    Counter.decrement()
    return (this.count -= 100)
  }
}
```

이렇게 작성하면 `SuperCount` 클래스가 싱글톤 객체의 값을 수정하게 될 수 있고 예외로 이어질 수 있다.

### 전역 동작

Singleton 인스턴스는 앱의 전체에서 참조할 수 있어야 하기 때문에, 반드시 같은 동작을 구현하는 데 사용해야 한다.

만약 전역 변수가 올바르지 않다면, 잘못된 값으로 덮어쓰여질 수 있고, 해당 전역 변수를 참조하는 구현들에서 모두 예외를 발생시킬 수 있다.

ES2015에서는 `let`, `const` 키워드를 통해 전역 변수를 생성하는 것을 일반적인 상황으로 보지 않는다.

그리고 `module`시스템을 통해 `export`구문과 `import`구문으로 전역 객체를 수정하지 않고 모듈 내에서 전역으로 쓸 수 있는 변수를 만들게 해준다.

그러나 Singleton 패턴은 일반적으로 전역 상태를 위해 사용한다.

전역 상태는

- 수정가능한 하나의 객체를 직접 접근할 때 예외가 발생하기 쉬우며,
- 데이터를 읽어들이는 부분을 위해 전역 상태를 수정할 경우 실행 순서를 정확하게 설정해야한다.
- 또한 앱의 규모가 커지고 전역 상태를 참조하는 컴포넌트가 많아지고 서로를 참조하는 상황에 이르게 되면 데이터의 흐름을 파악하기 어려워진다.

### React의 상태관리

React에서는 보통 Singleton 객체를 만드는 것 대신 Redux나 React Context를 자주 사용한다.

Singleton 객체는 인스턴스 값을 직접 수정할 수 있지만, 위의 두 도구는 읽기 전용 상태를 제공한다.

Redux에서는 오직 컴포넌트에서 디스패쳐를 통해 넘긴 액션에 대해 실행된 순수함수 리듀서를 통해서만 상태를 업데이트할 수 있다.

개발자의 의도대로만 수정되기 때문에 위의 전역 상태보다는 낫다.
