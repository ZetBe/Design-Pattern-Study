# Hooks 패턴

## 클래스 컴포넌트

Hooks가 추가되기 전에 React에서 상태와 생명 주기 함수를 사용하려면 클래스 컴포넌트를 꼭 사용해야 했다. 일반적인 React의 클래스 컴포넌트는 아래 예제 코드와 같이 생겼다.

```tsx
class MyComponent extends React.Component {
  /* Adding state and binding custom methods */
  constructor() {
    super()
    this.state = { ... }

    this.customMethodOne = this.customMethodOne.bind(this)
    this.customMethodTwo = this.customMethodTwo.bind(this)
  }

  /* Lifecycle Methods */
  componentDidMount() { ...}
  componentWillUnmount() { ... }

  /* Custom methods */
  customMethodOne() { ... }
  customMethodTwo() { ... }

  render() { return { ... }}
}
```

클래스 컴포넌트에는 생성자에서 상태를 선언하고 생명 주기 메서드들은 컴포넌트의 생명 주기를 기준으로 사이드 이펙트를 발생시켰다.

추가로 클래스 컴포넌트의 단점들은 무엇이 있을까?

## ES2015의 클래스를 알아야 한다.

만약 버튼을 구현한다면 클래스 컴포넌트는 다음과 같다.

```tsx
export default class Button extends React.Component {
  constructor() {
    super()
    this.state = { enabled: false }
  }

  render() {
    const { enabled } = this.state
    const btnText = enabled ? 'enabled' : 'disabled'

    return (
      <div
        className={`btn enabled-${enabled}`}
        onClick={() => this.setState({ enabled: !enabled })}
      >
        {btnText}
      </div>
    )
  }
}
```

이렇게 만들다 보면 실무에서는 이를 리팩토링하기에는 매우 까다롭다.

그리고 bind, this, 생성자와 같은 것들을 고려하는 것은 어렵기 때문에 마찬가지로 동작을 유지한 채로 리팩토링하는 것은 매우 까다롭다.

## 재설계

중첩된 컴포넌트간에 코드를 공유하기 위해 컴포넌트 래핑을 많이 하면 Wrapper Hell이라는 안티패턴이 나타날 수 있다.

## 복잡도

생명주기 메서드들은 꽤 많은 코드의 중복을 만들어낸다.

```tsx
import React from 'react'
import './styles.css'

import { Count } from './Count'
import { Width } from './Width'

export default class Counter extends React.Component {
  constructor() {
    super()
    this.state = {
      count: 0,
      width: 0,
    }
  }

  componentDidMount() {
    this.handleResize()
    window.addEventListener('resize', this.handleResize)
  }

  componentWillUnmount() {
    window.removeEventListener('resize', this.handleResize)
  }

  increment = () => {
    this.setState(({ count }) => ({ count: count + 1 }))
  }

  decrement = () => {
    this.setState(({ count }) => ({ count: count - 1 }))
  }

  handleResize = () => {
    this.setState({ width: window.innerWidth })
  }

  render() {
    return (
      <div className="App">
        <Count
          count={this.state.count}
          increment={this.increment}
          decrement={this.decrement}
        />
        <div id="divider" />
        <Width width={this.state.width} />
      </div>
    )
  }
}
```

이런 식으로 작성되어 있다면 컴포넌트와 관련된 로직을 추려내기도 어려워진다.

복잡하게 얽힌 로직 뿐만 아니라 일부 로직은 생명주기 함수 내에서 중복되어 있다.

## Hooks

클래스 컴포넌트가 항상 좋은 방법은 아니다. 그래서 React Hooks가 탄생한 것이다.

다음은 React Hooks로 인해 가능하게 된 기능들이다.

- 함수형 컴포넌트에 상태를 추가한다
- `componentDidMount` 혹은 `componentWillUnmount`와 같은 생명주기 메서드 없이도 컴포넌트의 생명 주기를 관리할 수 있다.
- 앱 내에 상태를 가진 로직을 여러 컴포넌트에서 재사용할 수 있게 한다.

## State, Effect, Custom hooks

이 부분은 useState, useEffect, custom hook과 관련한 이야기라 생략하겠다.

## Hook의 장단점

장점은

코드가 간결해지며, 생명 주기와 얽히지 않아 코드들을 관심사와 기능에 따라 분류할 수 있다.

자바스크립트의 클래스는 관리하기 힘들고 핫 리로딩과 함께 쓰기 어려우며 minifiy도 잘 되지 않는다.

React의 Hook은 이런 문제들을 해결하고 함수형 프로그래밍을 쉽게 만들어주었다.

단점은 다음과 같다.

- 규칙에 따라 작성해야 한다. 정적 분석기 플러그인을 사용하지 않으면 어떤 훅이 규칙을 어기고 있는지 알기 어렵다
- 올바르게 사용하기 위해 익숙하게 쓸 줄 알아야 한다
- 잘못 쓸 수 있다 (예를 들어 useCallback, useMemo)
