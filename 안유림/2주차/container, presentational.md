### Container / Presentational

비즈니스 로직으로부터 뷰를 분리하여 관심사의 분리(SoC)를 강제한다.

Presentational Components: 데이터의 뷰에 대해서 다루는 컴포넌트. 렌더링.
이 컴포넌트의 기능은 props로 받은 데이터를 화면에 표현하는 것이며 스타일시트를 포함한다.
UI 변경을 위한 상태 외에는 상태를 갖지 않는다.

Container Components: 어떤 데이터를 다루는 컴포넌트(비즈니스 로직). 사진 다운로드.
이 컴포넌트의 기능은 Presentational Component에 데이터를 전달하는 것이다.
화면에 아무것도 렌더링하지 않는다. 스타일시트도 없다.

React에 Hooks가 추가되면서 Container 컴포넌트 없이도 custom hooks를 통해 stateless 컴포넌트를 쉽게 만들 수 있게 되었다.

해당 패턴을 사용하면 자연스럽게 관심사의 분리를 구현하게 된다.
여러 곳에서 다양한 목적으로 재사용할 수 있다.
테스트가 쉽다. 일반적으로 순수함수로 구현되므로 요구하는 데이터만 인자로 넘겨주면 된다.
