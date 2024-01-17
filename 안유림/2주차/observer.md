### Observer

특정 객체를 구독할 수 있다. 구독하는 주체를 observer라 하고, 구독 가능한 객체를 observable이라 한다.
이벤트가 발생할 때 마다 observable은 모든 observer에게 이벤트를 전파한다.

observers: 이벤트가 발생할 때 마다 전파할 observer들의 배열
subscribe(): observer를 배열에 추가
unsubscribe(): observer를 배열에서 제거
notify(): 등록된 observer들에게 이벤트를 전파

비동기 호출 혹은 이벤트 기반 데이터를 처리할 때 유용하다. 어떤 컴포넌트가 특정 데이터의 다운로드 완료 알림을 받기 원하거나, 사용자가 메시지 보드에 새로운 메시지를 게시했을 때 모든 멤버가 알림을 받는 등의 상황이다.

RxJS는 Observer 패턴을 구현한 유명 오픈소스 라이브러리이다.
이벤트의 순서를 이상적으로 관리할 수 있다.

Observer 객체는 Observable 객체와 강결합되어있지 않고 언제든 분리될 수 있다.
Observable 객체는 이벤트 모니터링의 역할을 갖고, Observer는 받은 데이터를 처리한다.
하지만 복잡해지면 알림을 전파하는 데 성능 이슈가 발생할 수 있다.
