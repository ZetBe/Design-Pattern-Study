# \***\*Container/Presentational 패턴\*\***

문서에서 `React의 SOC` 를 언급하여 약간 찾아보니 **하나의 코드에 하나의 기능을 한다**라고 설명한다.

그래서 지난주에 다른 분께서 언급했던 기억이 있어 단일책임원칙에 대해 조금 찾아보니 `Robert Martin의 SOLID`원칙이 있었다.

그 SOLID원칙을 React의 함수형 컴포넌트 구조에서 적용하게끔 약간 변형한 내용의 글을 찾아 일단 이 부분을 먼저 설명하겠다.

[React SOLID](https://www.notion.so/React-SOLID-8136dbfb793349af82d28443c18bb417?pvs=21)

\***\*Container/Presentational 패턴인 이유는 각각 컴포넌트를 데이터가 어떻게 사용자에게 보여질 지, 이떤 데이터가 보여질 지에 다루기 때문에 그렇다.\*\***

### hooks

이런 패턴은 hooks로 대체 가능하다.

Container 컴포넌트 없이도 stateless 컴포넌트를 만들 수 있다.

```tsx
export default function useDogImages() {
  const [dogs, setDogs] = useState([])

  useEffect(() => {
    fetch('https://dog.ceo/api/breed/labrador/images/random/6')
      .then((res) => res.json())
      .then(({ message }) => setDogs(message))
  }, [])

  return dogs
}
```

이렇게 사용하면 컴포넌트를 사용할 필요가 없고 대신 Presentational 컴포넌트에서 훅을 직접 호출해서 사용하면 된다.

이를 통해 불필요한 Container 래핑을 줄일 수 있다.

## 장점

- 관심사의 분리를 구현할 수 있다.
- 데이터 변경 없이 화면에 출력하므로 다양한 목적의 재사용이 가능하다.
- 코드베이스에 대한 이해가 깊지 않은 개발자라더라도 쉽게 수정 가능하다.
- 테스트 하기 쉽다.

## 단점

- 훅을 활용하면 굳이 이 패턴을 쓰지 않고 같은 효과를 볼 수 있다.
- 작은 규모의 앱에서는 오버엔지니어링일 수 있다.
