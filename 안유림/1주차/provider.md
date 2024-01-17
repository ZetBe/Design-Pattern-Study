### Provider

앱 내의 여러 컴포넌트들이 데이터를 사용할 수 있게 해야 하는 상황에서 prop drilling에 의존하지 않고 데이터를 공유한다.

먼저 모든 컴포넌트를 Provider로 감싼다. Provider는 HOC로 Context 객체를 제공한다.
Provider 컴포넌트는 value라는 prop으로 하위 컴포넌트들에 내려줄 데이터를 받는다.
각 컴포넌트는 useContext 훅을 활용하여 data에 접근한다. 이 훅은 data와 연관된 DataContext를 받아 data를 읽고 쓸 수 있는 컨텍스트 객체를 제공한다.

컨텍스트를 참조하는 모든 컴포넌트는 컨텍스트 변경시마다 모두 리렌더링되기 때문에 성능 이슈가 있다.
앱의 규모가 커진다면 불필요한 렌더링을 막기 위해서 여러 Provider로 쪼개 사용할 필요가 있다.
