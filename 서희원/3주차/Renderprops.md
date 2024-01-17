# Render props 패턴

컴포넌트 재사용을 위한 또 다른 방법으로 render prop 패턴을 사용하는 방법이 있다.

render prop은 JSX 엘리먼트를 리턴한다.

`<Title render={() => <h1>I am a render prop!</h1>} />`

`const Title = props => props.render()`

이렇게 하면 `render` prop만 바꿔가며 여러번 사용가능하다.

하지만 이런 방법 외에도 여러 방법들이 있는데,

render prop 함수를 호출할 때 인자를 전달할 수 있다.

```tsx
function Component(props) {
  const data = { ... }

  return props.render(data)
}

<Component render={data => <ChildComponent data={data} />} />
```

예시에서 여러 컴포넌트의 상태를 부모 컴포넌트로 상태를 올려 보내서 해당 상태 값을 접근하게 하려 한다.

하지만 이 방법은 앱의 규모가 커지면 앱의 성능이 떨어진다는 단점이 있다.

이 방법을 render props 패턴으로 해결 가능한데, `Input` 컴포넌트를 밖으로 꺼내서 사용하면 된다.

```tsx
function Input(props) {
  const [value, setValue] = useState('')

  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="Temp in °C"
      />
      {props.render(value)}
    </>
  )
}

export default function App() {
  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input
        render={(value) => (
          <>
            <Kelvin value={value} />
            <Fahrenheit value={value} />
          </>
        )}
      />
    </div>
  )
}
```

## 자식 컴포넌트를 함수로

일반적인 JSX컴포넌트에 자식 엘리먼트로 React 엘리먼트를 반환하는 함수를 전달할 수 있다.

해당 컴포넌트에서 이 함수는 `children` prop으로 사용 가능하며 이것도 역시 render prop에 해당한다.

```tsx
export default function App() {
  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input>
        {(value) => (
          <>
            <Kelvin value={value} />
            <Fahrenheit value={value} />
          </>
        )}
      </Input>
    </div>
  )
}

function Input(props) {
  const [value, setValue] = useState('')

  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="Temp in °C"
      />
      {props.children(value)}
    </>
  )
}
```

## Hooks

Apollo Client에서 특정 상황에 render props 패턴을 hooks로 대체될 수 있다.

Apollo에서 사용하는 방법 중 하나는 `Mutation`과 `Query`컴포넌트를 사용하는 것이다.

`Mutation` 컴포넌트는 아래와 같이 작성하고 이 함수에서 인자로 데이터를 받을 수 있다.

```tsx
<Mutation mutation={...} variables={...}>
  {addMessage => <div className="input-row">...</div>}
</Mutation>
```

하지만 트리가 깊어지므로 이를 대체하기 위해 훅인 `useMutation` 을 사용한다.

```tsx
const [addMessage] = useMutation(ADD_MESSAGE, {
  variables: { message },
})
```

## 장점

HOC처럼 재사용성과 데이터의 공유 부분에서 이슈를 해결할 수 있다.

부모 컴포넌트로 부터 받은 prop을 명시작으로 받아 처리하기 때문에 HOC처럼 prop이름의 혼동을 줄일 수 있다.

렌더링 컴포넌트와 앱의 로직을 분리할 수 있다.

## 단점

이미 render props로 해결하려 한 문제는 React hooks로 대체되었다.

render prop 내에서는 생명 주기 함수를 사용할 수 없기 때문에 render props 패턴은 받은 데이터를 수정할 필요가 없는 컴포넌트들에 대해 사용 가능하다.
