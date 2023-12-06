# Provider 패턴

앱 내의 여러 컴포넌트들이 데이터를 사용할 수 있게 해야하는 상황이 있다.

그 경우, props를 통해 전달해도 되지만, 모든 컴포넌트들이 데이터에 접근해야 하므로 매우 번거롭다.

종종 props drilling이라 불리는 안티패턴을 사용하게 되는데,

이 경우 prop에 의존되는 컴포넌트들을 나중에 리팩토링하기란 거의 불가능하지며, 어떤 데이터가 어디서 시작되는지 알기 어렵게 된다.

<img width="575" alt="Provider 패턴" src="https://github.com/ZetBe/Design-Pattern-Study/assets/90635746/b0e59e36-849c-4811-9465-2609ec6e4085">

이런 구조의 컴포넌트 트리가 있고, 맨아래 빨간색 컴포넌트에 데이터를 App컴포넌트로 부터 받아야 한다면,

코드베이스는 아래와 같이 작성된다.

```jsx
function App() {
  const data = { ... }

  return (
    <div>
      <SideBar data={data} />
      <Content data={data} />
    </div>
  )
}

const SideBar = ({ data }) => <List data={data} />
const List = ({ data }) => <ListItem data={data} />
const ListItem = ({ data }) => <span>{data.listItem}</span>

const Content = ({ data }) => (
  <div>
    <Header data={data} />
    <Block data={data} />
  </div>
)
const Header = ({ data }) => <div>{data.title}</div>
const Block = ({ data }) => <Text data={data} />
const Text = ({ data }) => <h1>{data.text}</h1>
```

이런 방식은 꽤 지저분하며, 만약 `data` 라는 프로퍼티의 이름을 변경해야한다면 모든 컴포넌트를 수정해야 한다.

이런 방식 보다는 데이터가 필요한 컴포넌트가 import를 통해 직접 데이터에 접근할 수 있는 방법이 필요하다.

이 경우 Provider 패턴이 매우 유용하다.

이 패턴은 각 레이어에 직접 데이터를 주지 않고도 여러 컴포넌트들에게 데이터를 접근할 수 있게 구현할 수 있다.

구현하는 방법은

1. 모든 컴포넌트를 Provider로 감싼다.

   Provider는 HOC로 `Context` 객체를 제공한다. React가 제공하는 `createContext`메서드를 활용하여 `Context`객체를 만들어 낼 수 있다.

   _(HOC는 고차 컴포넌트로 컴포넌트 로직을 재사용하기 위한 React의 고급 기술, HOC 패턴은 추후 나옴)_

2. Provider 컴포넌트는 `value`라는 `prop`으로 하위 컴포넌트들에 내려줄 데이터를 받는다.

   이 컴포넌트의 모든 자식 컴포넌트들은 해당 `provider`를 통해 `value prop`에 접근할 수 있다.

```jsx
const DataContext = React.createContext()

function App() {
  const data = { ... }

  return (
    <div>
      <DataContext.Provider value={data}>
        <SideBar />
        <Content />
      </DataContext.Provider>
    </div>
  )
}
```

1. 각 컴포넌트는 해당 데이터를 `useContext` 훅을 사용해 데이터에 접근한다.

이를 활용해 라이트 모드와 다크 모드로 전환하게끔 구현하려면

컴포넌트들을 `ThemeProvider` 로 감싸고 테마 컬러값을 `provider`에게 전달한다.

```jsx
export const ThemeContext = React.createContext()

const themes = {
  light: {
    background: '#fff',
    color: '#000',
  },
  dark: {
    background: '#171717',
    color: '#fff',
  },
}

export default function App() {
  const [theme, setTheme] = useState('dark')

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light')
  }

  const providerValue = {
    theme: themes[theme],
    toggleTheme,
  }

  return (
    <div className={`App theme-${theme}`}>
      <ThemeContext.Provider value={providerValue}>
        <Toggle />
        <List />
      </ThemeContext.Provider>
    </div>
  )
}
```

이를 통해 `Toggle`, `List` 컴포넌트가 `ThemeContext` Provider의 자식 컴포넌트로 존재하는 동안 `value` 로 넘겼던 `theme` 과 `toggleTheme` 값에 접근할 수 있다.

각 컴포넌트들은 이렇게 `toggleTheme` 값에 접근한다.

```jsx
// Toggle.js
import React, { useContext } from 'react'
import { ThemeContext } from './App'

export default function Toggle() {
  const theme = useContext(ThemeContext)

  return (
    <label className="switch">
      <input type="checkbox" onClick={theme.toggleTheme} />
      <span className="slider round" />
    </label>
  )
}

//List.js
import React, { useContext } from 'react'
import { ThemeContext } from './App'

export default function TextBox() {
  const theme = useContext(ThemeContext)

  return <li style={theme.theme}>...</li>
}
```

그리고 최종적으로 App.js에서는 이렇게 작성한다.

```jsx
import React, { useState } from 'react'
import './styles.css'

import List from './List'
import Toggle from './Toggle'

export const themes = {
  light: {
    background: '#fff',
    color: '#000',
  },
  dark: {
    background: '#171717',
    color: '#fff',
  },
}

export const ThemeContext = React.createContext()

export default function App() {
  const [theme, setTheme] = useState('dark')

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light')
  }

  return (
    <div className={`App theme-${theme}`}>
      <ThemeContext.Provider value={{ theme: themes[theme], toggleTheme }}>
        <>
          <Toggle />
          <List />
        </>
      </ThemeContext.Provider>
    </div>
  )
}
```

### Hooks

각 컴포넌트에서 `useContext`를 직접 import하는 대신 필요로 하는 컨텍스트를 직접 반환하는 훅을 구현할 수 있다.(커스텀 훅)

```jsx
function useThemeContext() {
  const theme = useContext(ThemeContext)
  return theme
}
```

훅이 유용하게 사용되는지 검증하기 위해 컨텍스트가 falsy value일 때 예외를 발생시키도록 구현한다.

```jsx
function useThemeContext() {
  const theme = useContext(ThemeContext)
  if (!theme) {
    throw new Error('useThemeContext must be used within ThemeProvider')
  }
  return theme
}
```

컴포넌트들을 `ThemeContext.Provider`로 직접 래핑하게 하는 것 대신, HOC를 만들어 간단하게 사용 할 수 있다.

이렇게 하면 컨텍스트 로직과 렌더링 로직을 분리하여 재 사용성을 증가시킨다.

```jsx
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('dark')

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light')
  }

  const providerValue = {
    theme: themes[theme],
    toggleTheme,
  }

  return (
    <ThemeContext.Provider value={providerValue}>
      {children}
    </ThemeContext.Provider>
  )
}

export default function App() {
  return (
    <div className={`App theme-${theme}`}>
      <ThemeProvider>
        <Toggle />
        <List />
      </ThemeProvider>
    </div>
  )
}
```

하위 컴포넌트들은 `useThemeContext`훅을 사용하면 된다.

### 사례 분석

styled-components에서는 자식 컴포넌트들이 값을 쉽게 사용할 수 있도록 자체적으로 Provider를 제공한다.

자체적인 Provider는 `ThemeProvider` 로, 각 styled component는 해당 Provider의 값에 접근할 수 있다.

위의 예시를 styled-components로 바꿔서 구현하면 이렇게 작성할 수 있다.

```jsx
import { ThemeProvider } from 'styled-components'

export default function App() {
  const [theme, setTheme] = useState('dark')

  function toggleTheme() {
    setTheme(theme === 'light' ? 'dark' : 'light')
  }

  return (
    <div className={`App theme-${theme}`}>
      <ThemeProvider theme={themes[theme]}>
        <>
          <Toggle toggleTheme={toggleTheme} />
          <List />
        </>
      </ThemeProvider>
    </div>
  )
}
```

```jsx
import styled from 'styled-components'

export default function ListItem() {
  return (
    <Li>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
      tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
      veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
      commodo consequat.
    </Li>
  )
}

const Li = styled.li`
  ${({ theme }) => `
     background-color: ${theme.backgroundColor};
     color: ${theme.color};
  `}
`
```

### 장점

리팩토링 과정에서 개발자가 실수할 확률을 줄여준다.

Provider 패턴을 이용해 데이터가 필요없는 컴포넌트에게 props를 받을 필요가 없어진다.

### 단점

매번 과하게 사용할 경우, 특정 상황에서 성능 이슈가 발생한다.(컨텍스트를 참조하는 모든 컴포넌트는 컨텍스트 변경시 모두 리렌더링 된다.)

소규모 앱에서는 큰 문제가 되진 않지만, 앱의 규모가 커지면 여러 앱끼리 자주 업데이트 된 값을 넘길 경우 성능에 악영향을 끼칠 수 있다.

그래서 여러 Provider로 쪼갤 필요가 있다.
