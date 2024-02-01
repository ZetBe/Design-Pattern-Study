### Render props

JSX 엘리먼트를 props를 통해 컴포넌트에게 전달한다.
단순히 JSX 엘리먼트를 렌더링 하는 것 외에도 많은 동작을 할 수 있다. 함수를 호출하거나, 함수를 호출할 때 인자를 전달할 수도 있다.

```javascript
function App(props) {
  const [value, setValue] = useState("");
  return (
    <div className="App">
      <Input value={value} handleChange={setValue} />
      <Kelvin value={value} />
      <Fahrenheit value={value} />
    </div>
  );
}
```

규모가 큰 앱에서 컴포넌트가 여러 자식 컴포넌트를 가지고 있는 경우, 상태를 모두 props로 내려주었을 때 상태의 변경은 모든 자식 컴포넌트의 리렌더링을 유발할 수 있고, 이런 상황이 쌓이면 전체적인 성능을 떨어트릴 수 있다.

```javascript
<Input>
  {(vaule) => (
    <>
      <Kelvin value={value} />
      <Fahrenheit value={value} />
    </>
  )}
</Input>
```

일반적인 JSX 컴포넌트에 자식 엘리멘트로 React 엘리먼트를 반환하는 함수를 전달할 수 있다.
Input 컴포넌트에 명시적으로 render prop을 넘기는 대신 자식 컴포넌트를 함수로 넘겼다.

```javascript
function Input(props) {
  const [value, setValue] = useState("");
  return (
    <>
      <input
        type="text"
        value={value}
        onChange={(e) => setValue(e.target.value)}
        placeholder="blabla"
      />
      {props.children(value)}
    </>
  );
}
```

이렇게 하여 render prop의 이름을 어떻게 지을까 고민하지 않고 Kelvin과 Fahrenheit 둘 다 사용자의 입력 값에 접근할 수 있다.
이름이 문제는 아니고 . . .

이렇게 하여 Input 컴포넌트에서 useState를 관리하고 App의 다른 요소들에는 성능 이슈를 주지 않거나 줄일 수 있다.
