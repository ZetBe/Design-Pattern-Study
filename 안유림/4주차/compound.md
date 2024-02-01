### Compound

하나의 작업을 위해 여러 컴포넌트를 만들어 역할을 분담하게 한다.

```javascript
const FlyOutContext = createContext();

function FlyOut(props) {
  const [open, toggle] = useState(false);

  const providerValue = { open, toogle };

  return (
    <FlyOutcontext.Provider value={providerValue}>
      {props.children}
    </FlyOutcontext.Provider>
  );
}

function Toggle() {
  const { open, toggle } = useContext(FlyOutContext);

  return (
    <div onClick={() => toggle(!open)}>
      <Icon />
    </div>
  );
}

function List({ children }) {
  const { open } = useContext(FlyOutContext);
  return open && <ul>{children}</ul>;
}

function Item({ children }) {
  return <li>{children}</li>;
}

FlyOut.Toggle = Toggle;
FlyOut.List = List;
FlyOut.Item = Item;

// result
export default function FlyOutMenu() {
  return (
    <FlyOut>
      <FlyOut.Toggle />
      <FlyOut.List>
        <FlyOut.Item>Edit</FlyOut.Item>
        <FlyOut.Item>Delete</FlyOut.Item>
      </FlyOut.List>
    </FlyOut>
  );
}
```

- FlyOut 컴포넌트는 상태를 포함하고 자식 컴포넌트들에게 providerValue를 반환하고 있다.
- FlyOut.Toggle 로 FlyOut 컴포넌트에서 Toggle 컴포넌트를 사용할 수 있게 한다.
- FlyOutMenu 자체에는 아무런 상태를 가지고 있지 않다.
- 컴포넌트들을 일일히 import할 필요 없이 사용할 수 있다.
