컴포넌트 끼리 상호작용은 점점 흐름을 파악하기 어려워 질 것이다.

그래서 이를 중재해 주는 middleware를 통해 상호작용한다.

이를 대표적으로 사용하는 곳에 채팅 앱인데, 유저는 텍스트를 입력하면 서버에 메시지를 보내고 서버가 각 사용자에게 메시지를 보내는 형태로 진행된다.

![Untitled](https://patterns-dev-kr.github.io/static/a0d685096dcb9311980db6ffc80b95f7/e5da1/mediator-middleware01.jpg)

![Untitled](https://patterns-dev-kr.github.io/static/c5a77b672b11f8fc695b903e7f366ac0/e5da1/mediator-middleware02.jpg)

채팅 애플리케이션 예시

```tsx
class ChatRoom {
  logMessage(user, message) {
    const time = new Date()
    const sender = user.getName()

    console.log(`${time} [${sender}]: ${message}`)
  }
}

class User {
  constructor(name, chatroom) {
    this.name = name
    this.chatroom = chatroom
  }

  getName() {
    return this.name
  }

  send(message) {
    this.chatroom.logMessage(this, message)
  }
}
```

## 사례 분석

Express.js는 특정 라우팅 경로에 대해 콜백을 추가함으로써 요청을 처리할 수 있다.

```tsx
const app = require('express')()

app.use('/', (req, res, next) => {
  req.headers['test-header'] = 1234
  next()
})
```

이 코드에서 `next` 함수는 요청-응답 사이클에 걸려있는 다음 콜백을 호출한다.

![Untitled](https://patterns-dev-kr.github.io/static/d5318d3a6f72ccad25384e5c4e389347/e5da1/mediator-middleware03.jpg)

만약 다음과 같이 작성한다면 이전 콜백의 변경사항을 다음 콜백에서 확인할 수 있다.

```typescript
const app = require('express')()

app.use(
  '/',
  (req, res, next) => {
    req.headers['test-header'] = 1234
    next()
  },
  (req, res, next) => {
    console.log(`Request has test header: ${!!req.headers['test-header']}`)
    next()
  }
)
```
