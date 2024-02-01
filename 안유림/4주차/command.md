### Command

명령을 처리하는 객체를 통해 메서드와 실행되는 동작의 결합도를 낮출 수 있다.
특정 작업을 실행하는 개체와 메서드를 호출하는 개체를 분리할 수 있다.

클래스에서 메서드를 구현하여 직접 사용하는 도중
특정 메서드의 이름을 변경해야 하는 경우가 생길 수 있다.

```javascript
// before
class OrderManager() {
  constructor() {
    this.orders = []
  }

  placeOrder(order, id) {
    this.orders.push(id)
    return `You have successfully ordered ${order} (${id})`;
  }
}
const manager = new OrderManager()
manager.placeOrder('Pad Thai', '123');
```

```javascript
// after
class OrderManager() {
  constructor() {
    this.orders = []
  }

  execute(command, ...args) {
    return command.execute(this.orders, ...args);
  }
}

class Command {
  constructor(execute) {
    this.execute = execute
  }
}

function PlaceOrderCommand(order, id) {
  return new Command(orders => {
    orders.push(id);
    return `You have successfully ordered ${order} (${id})`;
  })
}

const manager = new OrderManager();
manager.execute(new PlaceOrderCommand('Pad Thai', '123'))
```

OrderManager가 메서드를 직접 갖는 대신 execute 메서드를 통해 분리된 함수를 사용하도록 코드를 리팩토링했다.
