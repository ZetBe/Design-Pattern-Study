# HOC 패턴

같은 로직을 여러 컴포넌트에서 재사용하는 방법 중 하나로 고차 컴포넌트 패턴을 활용하는 방법이 있다.

이 패턴을 통해 앱 전반적으로 재사용 가능한 로직을 여러 컴포넌트들이 쓸 수 있게 해준다.

아래와 같은 코드를 통해 `withLoader`가 HOC역할을 함을 알 수 있다.

```tsx
import React from 'react'
import withLoader from './withLoader'

function DogImages(props) {
  return props.data.message.map((dog, index) => (
    <img src={dog} alt="Dog" key={index} />
  ))
}

export default withLoader(
  DogImages,
  'https://dog.ceo/api/breed/labrador/images/random/6'
)
```

```tsx
import React, { useEffect, useState } from 'react'

export default function withLoader(Element, url) {
  return (props) => {
    const [data, setData] = useState(null)

    useEffect(() => {
      async function getData() {
        const res = await fetch(url)
        const data = await res.json()
        setData(data)
      }

      getData()
    }, [])

    if (!data) {
      return <div>Loading...</div>
    }

    return <Element {...props} data={data} />
  }
}
```

## Composing

여러 HOC를 조합할 수 있다.

만약 위의 예제에서 마우스에 사진을 올려서 hovering이라는 메시지를 띄우고 싶다면

아래 처럼 한번 더 씌워서 `withLoader`의 리턴 값을 받아 `withHOver`를 만들면 된다.

```tsx
import React from 'react'
import withLoader from './withLoader'
import withHover from './withHover'

function DogImages(props) {
  return (
    <div {...props}>
      {props.hovering && <div id="hover">Hovering!</div>}
      <div id="list">
        {props.data.message.map((dog, index) => (
          <img src={dog} alt="Dog" key={index} />
        ))}
      </div>
    </div>
  )
}

export default withHover(
  withLoader(DogImages, 'https://dog.ceo/api/breed/labrador/images/random/6')
)
```

## Hooks

hook을 통해 특정 상황에서 HOC를 대체할 수 있다.

예를 들어, hover는 `useHover`훅을 사용해 구현할 수 있다.

이를 통해 컴포넌트의 트리가 깊어지는 것을 막을 수 있다.

HOC를 활용하면 동일한 로직을 한 군데 구현하여 여러 컴포넌트에 제공할 수 있다.

훅은 컴포넌트의 내부에서 특정한 동작을 추가할 수 있게 해 주지만. HOC에 비해 버그를 발생시킬 확률을 증가시킨다.

## 사례 분석

Apollo Client에서는 `graphql()`이 있다.

`graphql()` HOC를 사용하면 컴포넌트에서 사용 가능한 데이터를 만들어낼 수 있다.

하지만 단점이 있는데,

컴포넌트가 여러 리졸버(=필드 요청이 들어온 경우 그 값을 제공하는 역할)에 접근해야하는 경우

`graphql()` 을 여러번 중첩해 사용해야 한다.

그리고 HOC를 사용해야하는 순서가 중요하다면 리팩토링 과정에서 버그를 만들어내기 쉽다.

React에서 훅이 추가된 이후 Apollo도 자체 훅을 지원하기 시작했다.

## 장점

한 곳에 구현한 로직들을 여러 컴포넌트에서 재사용할 수 있다.

동일 구현을 여러군데에 직접 구현하며 버그를 만들어 낼 확률을 줄일 수 있다.

로직을 한 곳에서 관리하여 관심사의 분리도 적용할 수 있게 되었다.

## 단점

props의 이름이 겹칠 수 있다.

이 경우 prop 병합을 통해 해결한다.

```tsx
function withStyles(Component) {
  return props => {
    const style = {
      padding: '0.2rem',
      margin: '1rem',
      ...props.style
    }

    return <Component style={style} {...props} />
  }
}

const Button = () = <button style={{ color: 'red' }}>Click me!</button>
const StyledButton = withStyles(Button)
```

HOC를 여러번 조합하는 경우 모든 prop이 안에서 병합되므로 HOC와 prop사이에 어떤 관련이 있는지 파악하기 어렵다.

앱의 디버깅이나 규모를 키울 때 방해될 수 있다.
