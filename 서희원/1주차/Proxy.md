# Proxy 패턴

`Proxy` 란 어떤 이의 대리인을 뜻한다.

즉, 이번 객체는 직접 다루는 것이 아니다.

`Proxy` 객체를 활용하면 특정 객체와의 인터렉션을 조금 더 컨트롤 할 수 있게 된다.

그리고 어떤 객체의 값을 설정하거나 값을 조회할 때 등의 인터렉션을 적접 제어할 수 있다.

자바스크립트 안에는 Proxy 객체가 따로 있으므로 이렇게 작성할 수 있다.

```jsx
const person = {
  name: 'John Doe',
  age: 42,
  nationality: 'American',
}

const personProxy = new Proxy(person, {})
```

여기서 두번째 인자는 핸들러를 의미하는데, 핸들러 객체에서 인터렉션의 종류에 따른 특정 동작들을 정의할 수 있다.

일반적으로 `get` 과 `set` 을

- `get` : 프로퍼티에 접근하려고 할 때 실행된다.
- `set` : 프로퍼티에 값을 수정하려고 할 때 실행된다.

이렇게 만들기로 예상하고 핸들러를 설정하면

`personProxy` 객체와 인터렉션하게 된다.(`person` 객체와 직접 인터렉션하지 않는다.)

```jsx
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`)
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`)
    obj[prop] = value
  },
})
```

여기서 `Handler.get`은 `proxy[foo]`와 `proxy.bar` 로 접근 가능하다.

그리고 `Handler.set`은 `proxy[foo] = bar`와 `proxy.foo = bar` 로 접근 가능하다.

```jsx
personProxy.name
personProxy.age = 43
```

Proxy는 유효성 검사를 구현할 때 유용하다.

사용자는 함부로 `age` 프로퍼티를 문자열로 수정하거나 `name` 프로퍼티를 빈 문자열로 초기화할 수 없다.

그리고 객체에 존재하지 않는 프로퍼티에 접근하려 하면, 알려줄 수 있다.

```jsx
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) {
      console.log(
        `Hmm.. this property doesn't seem to exist on the target object`
      )
    } else {
      console.log(`The value of ${prop} is ${obj[prop]}`)
    }
  },
  set: (obj, prop, value) => {
    if (prop === 'age' && typeof value !== 'number') {
      console.log(`Sorry, you can only pass numeric values for age.`)
    } else if (prop === 'name' && value.length < 2) {
      console.log(`You need to provide a valid name.`)
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`)
      obj[prop] = value
    }
  },
})
```

이렇게 해서 person 객체를 실수로 수정하는 것을 예방하여 데이터를 안전하게 관리할 수 있다.

### Reflect

자바스크립트는 `Reflect` 라는 빌트인 객체를 제공하는데 Proxy와 함께 사용하면 대상 객체를 쉽게 조작할 수 있다.(프록시와 같이 들어가는 메서드와 인자는 같다.)

```jsx
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`)
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`)
    Reflect.set(obj, prop, value)
  },
})
```

Proxy를 너무 헤비하게 사용한다면, 앱의 성능에 부정적인 영향을 줄 수 있으므로 성능문제가 생기지 않을 만한 코드를 사용하는 것이 좋다.
