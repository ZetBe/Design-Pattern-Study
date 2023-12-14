# \***\*Mixin 패턴\*\***

자바스크립트는 ES6부터 클래스 문법이 도입되었다.

믹스인은 상속없이 어떤 객체나 클래스에 재사용 가능한 기능을 추가할 수 있는 객체이다.

이 것을 단독으로 사용할 수는 없고 상속 없이 객체나 클래스에 기능을 추가하는 목적으로 사용된다.

```tsx
// 믹스인
let sayHiMixin = {
  sayHi() {
    alert(`Hello ${this.name}`)
  },
  sayBye() {
    alert(`Bye ${this.name}`)
  },
}

// 사용법:
class User {
  constructor(name) {
    this.name = name
  }
}

// 메서드 복사
Object.assign(User.prototype, sayHiMixin)

// 이제 User가 인사를 할 수 있습니다.
new User('Dude').sayHi() // Hello Dude!
```

여기서 `sayHiMixin` 은 `User` 에게 두 가지 메서드를 부여해준다.

만약 `User` 가 다른 클래스로 부터 상속받는다 해도 추가 메서드를 사용할 수 있다.

그리고 믹스인 안에서 믹스인을 상속받을 수 있다.

```tsx
let sayMixin = {
  say(phrase) {
    alert(phrase)
  },
}

let sayHiMixin = {
  __proto__: sayMixin, // (Object.create를 사용해 프로토타입을 설정할 수도 있습니다.)

  sayHi() {
    // 부모 메서드 호출
    super.say(`Hello ${this.name}`) // (*)
  },
  sayBye() {
    super.say(`Bye ${this.name}`) // (*)
  },
}

class User {
  constructor(name) {
    this.name = name
  }
}

// 메서드 복사
Object.assign(User.prototype, sayHiMixin)

// 이제 User가 인사를 할 수 있습니다.
new User('Dude').sayHi() // Hello Dude!
```

실제로 믹스인을 사용하는 경우는 클래스를 확장할 때 사용한다.

```tsx
// 클래스 생성
class Menu {
  choose(value) {
    this.trigger('select', value)
  }
}
// 이벤트 관련 메서드가 구현된 믹스인 추가
Object.assign(Menu.prototype, eventMixin)

let menu = new Menu()

// 메뉴 항목을 선택할 때 호출될 핸들러 추가
menu.on('select', (value) => alert(`선택된 값: ${value}`))

// 이벤트가 트리거 되면 핸들러가 실행되어 얼럿창이 뜸
// 얼럿창 메시지: Value selected: 123
menu.choose('123')
```

만약 실수로 기존 클래스 매서드를 덮어쓰면 충돌이 발생할 수 있기 때문에 믹스인을 만들 때에는 이름을 신중하게 작성해야 한다.

## 리액트(ES6 이전)

리액트에서는 믹스인에 대한 어떤 지원도 없고 실제로 수많은 문제점들을 발견해 추천하지 않는다.

그리고 ES6이상을 사용한다고 해도 어떤 지원도 없으므로 추천하지 않는다.

참조: https://ko.javascript.info/mixins
