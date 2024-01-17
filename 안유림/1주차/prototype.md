### Prototype

동일 타입의 여러 객체들이 프로퍼티를 공유할 때 사용된다. JavaScript 객체의 기본 속성이므로 Prototype 체인을 사용할 수 있다.

ES6클래스를 사용하면 모든 프로퍼티는 클래스 자체에 선언되며 자동으로 prototype에 추가된다. Object.getPrototypeOf를 사용하면 확인할 수 있다. 객체에 없는 프로퍼티에 접근하려 하는 경우 JavaScript는 이 프로퍼티가 나타날때까지 prototype chain을 거슬러 올라간다.

모든 인스턴스들이 prototype에 접근 가능하기 때문에, 인스턴스를 만든 뒤에도 프로퍼티를 추가할 수 있다.

SuperDog 클래스가 Dog 클래스를 상속받았을 경우 SuperDog의 prototype 객체는 Dog.prototype을 가리키고 있다.

prototype 체인을 통해 해당 객체에 프로퍼티가 직접 선언되어 있지 않아도 되므로 메서드 중복을 줄일 수 있고 이는 메모리 절약으로 이어진다.
