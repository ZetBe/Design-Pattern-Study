# \***\*Observer 패턴\*\***

해당 패턴에서는 특정 객체를 구독할 수 있는데, 구독하는 주체를 Oberver라 하고, 구독 가능한 객체를 Observable이라 한다.

Observable은 모든 Observers에게 이벤트가 발생할 때 마다 알려준다.

Observable에는 주요 특징을 가지고 있다.

- `observers` : 이벤트가 발생할때마다 전파할 Observer들의 배열
- `subscribe()` : Observer를 Observer 배열에 추가한다
- `unsubscribe()` : Observer 배열에서 Observer를 제거한다
- `notify()` : 등록된 모든 Observer들에게 이벤트를 전파한다

아래에는 Observable을 구현한 예제다.

```tsx
class Observable {
  constructor() {
    this.observers = []
  }

  subscribe(func) {
    this.observers.push(func)
  }

  unsubscribe(func) {
    this.observers = this.observers.filter((observer) => observer !== func)
  }

  notify(data) {
    this.observers.forEach((observer) => observer(data))
  }
}
```

이런 객체를 활용해서 `Button` 컴포넌트와 `Switch` 컴포넌트를 가진 기본적인 앱을 만들 수 있다.

```tsx
export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
    </div>
  )
}
```

이런 앱에서 구현하고자 하는 것은

앱 내에서 일어나는 사용자 인터렉션을 추적하고 사용자가 버튼을 누르던 스위치를 토글하던 타임 스탬프를 로깅하려고 한다.

또한 이벤트 발생시 마다 토스트 알림을 화면에 노출하려고 한다.

그래서 Observable의 `notify` 를 통해 함수들에게 데이터를 포함한 이벤트를 전파하면 된다.

```tsx
import { ToastContainer, toast } from 'react-toastify'

function logger(data) {
  console.log(`${Date.now()} ${data}`)
}

function toastify(data) {
  toast(data)
}

export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
      <ToastContainer />
    </div>
  )
}
```

이렇게만 작성하면 Observable과 연결되있지 않기 때문에 `subscribe` 메서드를 사용해야 한다.

```tsx
import { ToastContainer, toast } from 'react-toastify'

function logger(data) {
  console.log(`${Date.now()} ${data}`)
}

function toastify(data) {
  toast(data)
}

observable.subscribe(logger)
observable.subscribe(toastify)

export default function App() {
  return (
    <div className="App">
      <Button>Click me!</Button>
      <FormControlLabel control={<Switch />} />
      <ToastContainer />
    </div>
  )
}
```

이렇게 사용하면 만약 버튼이나 토글을 누를 시 `logger` 와 `toastify` 에 함수가 실행된다.

이런 패턴은 비동기 호출 혹은 이벤트 기반 데이터를 처리할 때 매우 유용하다.

## 사례 분석

RxJS는 Oberver 패턴을 구현한 유명 오픈소스 라이브러리이다.

(ReactiveX 는 Observer 패턴, 이터레이터 패턴, 함수형 프로그래밍을 조합하여 이벤트의 순서를 이상적으로 관리할 수 있다.)

아래 공식문서에 소개된 코드를 통해 Observable과 Observer를 만들어 낼 수 있다.

```tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { fromEvent, merge } from 'rxjs'
import { sample, mapTo } from 'rxjs/operators'

import './styles.css'

merge(
  fromEvent(document, 'mousedown').pipe(mapTo(false)),
  fromEvent(document, 'mousemove').pipe(mapTo(true))
)
  .pipe(sample(fromEvent(document, 'mouseup')))
  .subscribe((isDragging) => {
    console.log('Were you dragging?', isDragging)
  })

ReactDOM.render(
  <div className="App">Click or drag anywhere and check the console!</div>,
  document.getElementById('root')
)
```

## 장점

이 패턴도 관심사의 분리와 단일 책임 원칙을 강제하기 위한 좋은 방법이다.

Observer와 Observable 객체와 강결합되어 있지 않아 언제든지 분리 가능하며 Observable 객체는 이벤트 모니터링의 역할을 갖고, Observer는 받은 데이터를 처리하는 역할을 갖게 된다.

## 단점

Observer가 복잡해지면 모든 Observer들에게 알림을 전파하는데 성능 이슈가 발생할 수 있다.
