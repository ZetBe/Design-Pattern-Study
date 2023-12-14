# React SOLID

## 단일 책임 원칙 (**S**ingle responsibility principle, SRP)

“모든 클래스는 단 하나의 책임만 가져야 한다.”

이 말은 “모든 함수/모듈/컴포넌트는 정확히 한 가지 작업을 수행해야 한다.”라고 해석 가능하다.

여기서 SRP는 가장 따르기 쉽지만 코드 품질을 크게 향상하기 떄문에 가장 영향력 있는 원칙이다.

이 원칙을 리액트에서 활용하는 예시로 jsx를 포함한 함수에서 내부에 있는 훅들을 따로 빼서 일종의 커스텀 훅을 만들어 버리거나

```jsx
const ActiveUsersList = () => {
  const [users, setUsers] = useState([])

  useEffect(() => {
    const loadUsers = async () => {
      const response = await fetch('/some-api')
      const data = await response.json()
      setUsers(data)
    }

    loadUsers()
  }, [])

  const weekAgo = new Date()
  weekAgo.setDate(weekAgo.getDate() - 7)

  return (
    <ul>
      {users
        .filter((user) => !user.isBanned && user.lastActivityAt >= weekAgo)
        .map((user) => (
          <li key={user.id}>
            <img src={user.avatarUrl} />
            <p>{user.fullName}</p>
            <small>{user.role}</small>
          </li>
        ))}
    </ul>
  )
}
```

```jsx
const useUsers = () => {
  const [users, setUsers] = useState([])

  useEffect(() => {
    const loadUsers = async () => {
      const response = await fetch('/some-api')
      const data = await response.json()
      setUsers(data)
    }

    loadUsers()
  }, [])

  return { users }
}

const ActiveUsersList = () => {
  const { users } = useUsers()

  const weekAgo = new Date()
  weekAgo.setDate(weekAgo.getDate() - 7)

  return (
    <ul>
      {users
        .filter((user) => !user.isBanned && user.lastActivityAt >= weekAgo)
        .map((user) => (
          <li key={user.id}>
            <img src={user.avatarUrl} />
            <p>{user.fullName}</p>
            <small>{user.role}</small>
          </li>
        ))}
    </ul>
  )
}
```

jsx내부의 map을 사용하는 배열을 리턴하는 함수를 만들어 jsx를 포함한 함수의 크기를 줄인다.

```jsx
const UserItem = ({ user }) => {
  return (
    <li>
      <img src={user.avatarUrl} />
      <p>{user.fullName}</p>
      <small>{user.role}</small>
    </li>
  )
}

const ActiveUsersList = () => {
  const { users } = useUsers()

  const weekAgo = new Date()
  weekAgo.setDate(weekAgo.getDate() - 7)

  return (
    <ul>
      {users
        .filter((user) => !user.isBanned && user.lastActivityAt >= weekAgo)
        .map((user) => (
          <UserItem key={user.id} user={user} />
        ))}
    </ul>
  )
}
```

```jsx
const getOnlyActive = (users) => {
  const weekAgo = new Date()
  weekAgo.setDate(weekAgo.getDate() - 7)

  return users.filter(
    (user) => !user.isBanned && user.lastActivityAt >= weekAgo
  )
}

const ActiveUsersList = () => {
  const { users } = useUsers()

  return (
    <ul>
      {getOnlyActive(users).map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </ul>
  )
}
```

하지만 추가적인 조작 없이 데이터를 가져오는 것이 이상적이다. 이를 커스텀 훅으로 만들어 캡슐화 할 수 있다.

```jsx
const useActiveUsers = () => {
  const { users } = useUsers()

  const activeUsers = useMemo(() => {
    return getOnlyActive(users)
  }, [users])

  return { activeUsers }
}

const ActiveUsersList = () => {
  const { activeUsers } = useActiveUsers()

  return (
    <ul>
      {activeUsers.map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </ul>
  )
}
```

두 예시의 공통점은 jsx를 포함한 함수를 단순히 jsx를 리턴하는 함수로 단일 책임 원칙을 포함한다는 점이 있다.(컴포넌트가 얻은 렌더링 데이터를 받아 보여준다.)

요약

> 단일 책임 원칙에 따라 큰 모놀리식 코드 덩어리를 효과적으로 가져와 더 모듈화합니다. 모듈화하면 코드를 파악하기 쉬워지고, 의도치 않은 중복 코드를 작성할 가능성이 줄어듭니다. 또한 작은 모듈은 테스트 및 수정하기 더 쉽기 때문에 결과적으로 코드를 보다 쉽게 유지 관리할 수 있어 좋습니다.

현실은 컴포넌트 사이에 의존성이 더 얽혀있는데, 대부분 `적절하지 못한 추상화`, `다재다능한 전역 컴포넌트 생성`, `데이터의 잘못된 스코프 설정` 등 잘못된 설계로 인해 생길 수 있다.

이는 광범위한 리팩토링으로 해결할 수 있다.

## 개방-폐쇄 원칙 (**O**pen-closed principle, OCP)

OCP는 "소프트웨어 엔티티는 확장을 위해 열려야 하지만 수정을 위해 닫혀 있어야 한다”라고 말한다.

OCP는 원본 소스 코드를 변경하지 않고 확장할 수 있는 방식으로 구성 요소를 구조화하도록 권고한다.

예를 들어,

```jsx
const Header = () => {
  const { pathname } = useRouter()

  return (
    <header>
      <Logo />
      <Actions>
        {pathname === '/dashboard' && (
          <Link to="/events/new">Create event</Link>
        )}
        {pathname === '/' && <Link to="/dashboard">Go to dashboard</Link>}
      </Actions>
    </header>
  )
}

const HomePage = () => (
  <>
    <Header />
    <OtherHomeStuff />
  </>
)

const DashboardPage = () => (
  <>
    <Header />
    <OtherDashboardStuff />
  </>
)
```

위와 같은 `Header` 컴포넌트는 만약 새로운 페이지를 추가한다면 `Header` 컴포넌트로 돌아가서 렌더링할 작업 링크를 알 수 있도록 구현을 조정해야한다.

이런 접근 방식은 `Header` 컴포넌트가 취약해지고 사용되는 컨텍스트와 긴밀하게 결합되어, OCP에 위배된다.

이 문제를 해결하기 위해 컴포넌트 합성을 사용할 수 있다.

```jsx
const Header = ({ children }) => (
  <header>
    <Logo />
    <Actions>{children}</Actions>
  </header>
)

const HomePage = () => (
  <>
    <Header>
      <Link to="/dashboard">Go to dashboard</Link>
    </Header>
    <OtherHomeStuff />
  </>
)

const DashboardPage = () => (
  <>
    <Header>
      <Link to="/events/new">Create event</Link>
    </Header>
    <OtherDashboardStuff />
  </>
)
```

OCP를 통해 컴포넌트 간의 결합을 줄이고 확장성과 재사용성을 높일 수 있다.

## 리스코프 치환 원칙 (**L**iskov substitution principle, LSP)

LSP는 “하위 타입 객체가 상위 타입 객체를 대체할 수 있는 객체 간의 관계 유형”이다.

이 원칙은 클래스의 상속에서 다루는 원칙이기 때문에 리액트에서는 거의 적용할 수 없을 뿐더러 리액트에서 상속을 이용해 작성하면 의도적으로 나쁜 코드를 만들게 된다.

실제로 리액트 공식문서에서 상속을 권장하지 않으므로 넘어간다.

## 인터페이스 분리 원칙 (**I**nterface segregation principle, ISP)

ISP는 “클라이언트는 사용하지 않는 인터페이스에 의존해서는 안된다.”라고 한다.

리액트에서는 “컴포넌트는 사용하지 않는 props에 의존해서는 안된다.”로 해석할 것이다.

ISP를 잘 설명하기 위해 타입스크립트를 사용하여 살펴보면 이렇다.

```tsx
//VideoList.tsx
type Video = {
  title: string
  duration: number
  coverUrl: string
}

type Props = {
  items: Array<Video>
}

const VideoList = ({ items }) => {
  return (
    <ul>
      {items.map((item) => (
        <Thumbnail key={item.title} video={item} />
      ))}
    </ul>
  )
}

//Thumbnail.tsx
type Props = {
  video: Video
}

const Thumbnail = ({ video }: Props) => {
  return <img src={video.coverUrl} />
}
```

여기서 Thumbnail 컴포넌트에 문제가 하나 있는데, 전체 Video 객체가 Props로 전달되어야 하며 실질적으로 프로퍼티 중 하나만 사용한다는 것이다.

왜 이것이 문제인지 살펴보기 위해 추가로 객체들을 추가해 보면 다음과 같다.

```tsx
type LiveStream = {
  name: string
  previewUrl: string
}

type Props = {
  items: Array<Video | LiveStream>
}

const VideoList = ({ items }) => {
  return (
    <ul>
      {items.map((item) => {
        if ('coverUrl' in item) {
          // 여긴 video입니다.
          return <Thumbnail video={item} />
        } else {
          // 여긴 live stream입니다만 우리는 여기서 무엇을 할 수 있을까요?
        }
      })}
    </ul>
  )
}
```

여기서 LiveStream과 Video가 서로 호환되지 않기 때문에 else문에서(후자에서) Thumbnail 컴포넌트에 전달할 수 없다.

전달 할 수 없는 이유로 2가지가 있다면 다음과 같다.

1. 타입 자체가 다르기 때문에 타입스크립트에서 즉시 문제를 제기한다.
2. 두 객체에서 서로 다른 프로퍼티에 썸네일 URL을 가지고 있다.

이는 재사용성이 낮아지기 때문에 일부 수정해야 한다.

```tsx
//Thumbnail.tsx
type Props = {
  coverUrl: string
}

const Thumbnail = ({ coverUrl }: Props) => {
  return <img src={coverUrl} />
}

//VideoList.tsx
type Props = {
  items: Array<Video | LiveStream>
}

const VideoList = ({ items }) => {
  return (
    <ul>
      {items.map((item) => {
        if ('coverUrl' in item) {
          // 여긴 video입니다.
          return <Thumbnail coverUrl={item.coverUrl} />
        } else {
          // 여긴 live stream입니다.
          return <Thumbnail coverUrl={item.previewUrl} />
        }
      })}
    </ul>
  )
}
```

인터페이스를 분리하여 컴포넌트들 간 의존성을 최소화해 컴포넌트의 결합도를 낮추고 재사용성을 높일 수 있도록 한다.

## 의존관계 역전 원칙 (**D**ependency inversion principle, DIP)

DIP는 “구체화가 아닌 추상화에 의존해야 한다”라고 말한다.

즉, 한 컴포넌트가 다른 컴포넌트에 직접적으로 의존해서는 안되며, 둘 다 공통된 추상화에 의존해야 한다.

추상화에 대한 예시로 `LoginForm` 컴포넌트를 살펴보자면

```tsx
import api from '~/common/api'

const LoginForm = () => {
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')

  const handleSubmit = async (evt) => {
    evt.preventDefault()
    await api.login(email, password)
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      <button type="submit">Log in</button>
    </form>
  )
}
```

`LoginForm` 컴포넌트는 api 모듈을 직접 참조하므로 둘 사이는 긴밀히 결합되어 있다.

한 컴포넌트의 변경이 다른 컴포넌트에 영향을 끼치므로 이런 의존성은 좋지 않다.

DIP는 이런 결합을 깨는 것을 권하므로 이를 어떻게 할 수 있을지 살펴본다.

먼저 api 모듈에 대한 직접 참조를 제거하고 props를 통해 필요한 기능을 주입할 수 있도록 한다.

```tsx
type Props = {
  onSubmit: (email: string, password: string) => Promise<void>
}

const LoginForm = ({ onSubmit }: Props) => {
  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')

  const handleSubmit = async (evt) => {
    evt.preventDefault()
    await onSubmit(email, password)
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      <button type="submit">Log in</button>
    </form>
  )
}
```

여기서 LoginForm컴포넌트에서는 더 이상 api모듈에 의존하지 않는다.

api에 크리덴셜을 제출하는 로직은 onSubmit을 통해 추상화되었다.

그리고 상위 컴포넌트를 하나 만들에서 api 모듈에 위임하는 연결된 버전을 생성한다.

```tsx
import api from '~/common/api'

const ConnectedLoginForm = () => {
  const handleSubmit = async (email, password) => {
    await api.login(email, password)
  }

  return <LoginForm onSubmit={handleSubmit} />
}
```

위 컴포넌트는 api와 LoginForm 사이에 접착제 역할을 하며 독립적으로 만들었다.

그렇기 때문에 테스팅도 의존성에 대해 고려하지 않고 독립적이고 반복적으로 실행 가능하다.

결론적으로 DIP는 애플리케이션의 서로 다른 컴포넌트 간의 결합을 최소화하는 것을 목표로 한다.

최소화는 개별 컴포넌트에 대한 책임 범위를 최소화하는 것 부터 컴포넌트 간의 인식 및 의존성을 최소화하는 것 까지 모든 SOLID원칙 전반에 걸쳐서 반복되는 주제다.

## 결론

SOLID 원칙을 유연하게 해석하고 리액트에서 유지관리하기 쉽고 강력하게 만드는 방법을 살펴봤다.

하지만 맹신하면 악영향을 주고 오버 엔지니어링으로 이어질 수 있다.

그리고 컴포넌트를 지나치게 분리하거나 분해해도 복잡도 개선에 도움이 되지 않는 경우도 살펴봐야한다.
