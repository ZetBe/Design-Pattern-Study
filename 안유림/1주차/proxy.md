### Proxy

해당 객체를 직접 다루는 것이 아니고 Proxy 객체와 인터렉션한다.

new Proxy(객체, {핸들러})
객체에 접근할 때 Proxy 핸들러에 정의한 get, set을 통해 결과를 얻을 수 있다.
또는 JavaScript의 Reflect라는 빌트인 객체를 사용하면 대상 객체를 쉽게 조작할 수 있다.

Proxy는 유효성 검사를 구현할 때 유용하다. 유효하지 않을 케이스들에 조건을 추가해준다.
객체를 실수로 수정하는 것을 예방해주어 데이터를 안전하게 관리할 수 있다.
이외에도 포맷팅, 알림, 디버깅 등에 유용하게 사용된다.
하지만 핸들러 객체에서 너무 헤비하게 사용하면 성능에 부정적인 영향을 줄 수 있다.
