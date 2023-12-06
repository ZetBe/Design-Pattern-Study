# Prototype 패턴

Prototype은 자바스크립트 객체의 기본 속성이므로 Prototype 체인을 활용할 수 있다.

하나의 앱을 만든다 가정하면, 동일한 타입의 여러 객체를 만들어서 사용가능하다.

ES6의 클래스의 여러 인스턴스를 만들어낼 때 유용하게 사용할 수 있다.

```jsx
class Dog {
  constructor(name) {
    this.name = name
  }

  bark() {
    return `Woof!`
  }
}

const dog1 = new Dog('Daisy')
const dog2 = new Dog('Max')
const dog3 = new Dog('Spot')
```

위의 코드를 보면, `name`포로퍼티를 가지고 있는 클래스는 자체적으로 `bark`프로퍼티를 가지고 있다.

ES6 클래스에서 모든 프로퍼티는 클래스 자체에 선언되며 `bark`는 자동으로 prototype에 추가된다.

생성자의 `prototype` 프로퍼티 혹은 생성된 인스턴스의 `Object.getPrototypeOf`프로퍼티를 통해 Prototype 객체를 확인할 수 있다.

```jsx
console.log(Object.getPrototypeOf(dog1))
// constructor: ƒ Dog(name, breed) bark: ƒ bark()

console.log(Dog.prototype)
// constructor: ƒ Dog(name, breed) bark: ƒ bark()
```

만약 객체에 없는 프로퍼티에 접근하려고 한다면 자바스크립트는 이 프로퍼티가 나타날 때 까지 prototype chain을 거슬러 올라간다.

![Prototype1](https://github.com/ZetBe/Design-Pattern-Study/assets/90635746/4cfe96ae-75bb-4711-9e5a-3ab4d34f1dee)

Prototype 패턴은 객체들이 같은 프로퍼티를 가져야 하는 경우 유용하게 쓰일 수 있다.

Prototype에 프로퍼티를 추가하면 모든 인스턴스들이 Prototype 객체를 활용할 수 있다.

모든 인스턴스들이 Prototype에 접근할 수 있기 때문에 인스턴스를 만든 뒤에도 Prototype에 프로퍼티를 추가할 수 있다.

예를 들어, 위의 코드에서 play프로퍼티를 추가할 수 있다.

```jsx
class Dog {
  constructor(name) {
    this.name = name
  }

  bark() {
    return `Woof!`
  }
}

const dog1 = new Dog('Daisy')
const dog2 = new Dog('Max')
const dog3 = new Dog('Spot')

Dog.prototype.play = () => console.log('Playing now!')

dog1.play()
//Playing now!
```

‘Prototype 체인’ 단어 처럼 Prototype은 한 단계 이상도 존재할 수 있다.

Prototype 객체 내에 Prototype 속성을 가질 수 있다는 의미이다.

그렇다면 위의 예제에서 Dog클래스를 상속받아 `SuperDog`라는 강아지를 만든다 할때,

`SuperDog`에 Dog클래스를 상속받아 `fly`메서드를 구현하면 된다.

```jsx
class SuperDog extends Dog {
  constructor(name) {
    super(name)
  }

  fly() {
    return 'Flying!'
  }
}
```

```jsx
class Dog {
  constructor(name) {
    this.name = name
  }

  bark() {
    console.log('Woof!')
  }
}

class SuperDog extends Dog {
  constructor(name) {
    super(name)
  }

  fly() {
    console.log(`Flying!`)
  }
}

const dog1 = new SuperDog('Daisy')
dog1.bark()
dog1.fly()
```

`SuperDog`는 Dog를 상속했기 때문에 인스턴스 `dog1`은 `bark`메서드도 호출할 수 있다.

`SuperDog`의 `Prototype` 객체의 proto는 `Dog.prototype`을 가리키고 있다.

![Prototype2](https://github.com/ZetBe/Design-Pattern-Study/assets/90635746/e63abc85-f6fe-4f74-9440-e7165787d946)

위에서 언급했지만, 현재 객체에 없는 프로퍼티에 접근하려 하는 경우 같은 이름의 프로퍼티를 찾을 때 까지 재귀적으로 객체의 proto를 따라 거슬러 올라가게 된다.

### Object.create

해당 메서드는 Prototype으로 쓰일 객체를 인자로 받아 새로운 객체를 만들어 낸다.

```jsx
const dog = {
  bark() {
    return `Woof!`
  },
}

const pet1 = Object.create(dog)
```

`pet1` 자체적으로는 아무런 프로퍼티가 없지만 `dog` 객체를 Prototype으로 사용하기 때문에 `bark` 메서드를 사용할 수 있다.

이는 단순히 객체가 다른 객체로부터 프로퍼티를 상속받을 수 있게 해준다.

```jsx
const dog = {
  bark() {
    console.log(`Woof!`)
  },
}

const pet1 = Object.create(dog)

pet1.bark() // Woof!
console.log('Direct properties on pet1: ', Object.keys(pet1)) // []
console.log("Properties on pet1's prototype: ", Object.keys(pet1.__proto__))
// ['bark'] 0: 'bark'
```

실행 결과로 생성되는 객체는 Prototype 체인으로 인해 인자로 넘어갔던 객체의 프로퍼티를 활용할 수 있다.

Prototype 패턴은 어떤 객체가 다른 객체의 프로퍼티를 상속받을 수 있도록 해준다.

Prototype 체인을 통해 해당 객체에 프로퍼티가 직접 선언되어 있지 않아도 되므로 메서드 중복을 줄이고 이는 메모리 절약으로 이어진다.
