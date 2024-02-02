### Client-Side Rendering

클라이언트 사이드 렌더링에서 서버는 뼈대가 되는 최소한의 HTML만 렌더링한다. 로직, 데이터, 템플릿, 라우팅을 통한 페이지 전환은 모두 브라우저에서 실행되는 JavaScript에 의해 처리된다. CSR은 SPA개발에 거의 필수적으로 쓰이며 데스크톱 앱의 환경을 웹에서도 경험할 수 있게 해주었다.

장점

- 페이지 새로고침이나 서버를 통하지 않고 실시간 데이터 변경(시간, 환율 등)이 가능하다. 좋은 사용자 경험을 줄 수 있다.
- 페이지간 라우팅 속도가 빨라져 페이지가 좀 더 빨리 반응하는 것 처럼 보인다.
- 클라이언트와 서버 코드를 조금 더 명확하게 분리하여 관리할 수 있게 한다.

단점

- 의미있는 내용의 렌더링이 늦어져 크롤러가 인덱싱할 수 없을 수 있다. SEO최적화를 위한 추가적인 작업을 해 주어야 한다.
- 페이지의 복잡도가 증가하는 만큼 페이지를 렌더링하기 위해 필요한 JavaScript의 크기 또한 증가한다. 번들의 크기가 클 수록 FCP와 TTI도 함께 증가한다. 사용자는 빈 화면을 보게 된다.
- 데이터의 크기가 증가할 때도 API호출 결과에 따른 시간 지연이 발생할 수 있다.

잘 사용해보려면?

- 번들 크기를 관리하여 초기 페이지 로드 속도를 빠르게 만들자.
- 지연로딩을 사용하여 초기에 받아야 하는 코드량을 줄이자.
- 서비스 워커를 이용하여 앱 내 최소한의 UI를 구성하는 코드들을 캐싱하자.

### Server-Side Rendering

서버 사이드 렌더링은 사용자의 페이지 요청에 대해 전체 HTML을 만들어서 렌더링한다. 렌더링 된 컨텐츠에는 데이터 저장소나 외부 API로부터 받은 데이터가 포함되어 있다. 서버에서 모든 처리를 진행하고 서버와 별도 통신하지 않아도 된다. 이 때 서버는 각 요청들을 모두 새로운 요청으로 간주하여 처음부터 데이터를 생성한다.

장점

- 렌더링 코드를 서버 측에서 실행하여 JavaScript의 크기를 줄이기 때문에 빠른 FCP와 TTI를 갖게 된다. 사용자가 빠르게 화면을 볼 수 있다.
- 쉽게 크롤링되어 SEO최적화 수준이 향상된다.

단점

- 모든 프로세스가 서버에 의해 처리되므로 동시에 아주 많은 요청이 들어오거나 네트워크가 느릴 때 서버의 응답이 지연되는 상황이 발생할 수 있다.
- 모든 코드를 클라이언트에서 사용할 수 없어 서버 데이터가 필요한 경우 전체 페이지를 새로고침 해야 한다. SPA가 어렵다.

잘 사용해보려면?

- Next.js 프레임워크에서 각 요청에 대해 서버에서 pre-render를 수행하는 기능을 제공한다. getServerSideProps()의 context객체에 라우팅 파라미터, 쿼리, 로케일 등 HTTP요청에 대한 데이터가 포함되어 있다. 이를 이용해 포매팅 된 페이지를 React로 렌더링한다.
- React는 유니버셜 코드로 함수를 브라우저 뿐만 아니라 서버와 같은 기타 플랫폼에서도 실행할 수 있다(동형). 따라서 UI 요소들은 Node.js 서버에서도 실행되고 React로 화면 렌더링이 가능하다.

```javascript
// 서버의 js파일
app.get("/", (req, res) => {
  const app = ReactDOMServer.renderToString(<App />);
});
```

ReactDOMServer.renderToString(element) 함수는 React 엘리먼트와 대응되는 HTML문자열을 반환한다.

```javascript
// 클라이언트의 js파일
ReactDOM.hydrate(<App />, document.getElementById("root"));
```

renderToString() 함수는 ReactDOM.hydrate() 함수와 함께 사용된다. 서버에서 렌더링 된 HTML을 그대로 클라이언트에서 쓸 수 있고 클라이언트에서는 이벤트 핸들러들을 등록하면 된다.

### Static Rendering (Static Site Generator)

CSR, SSR의 단점을 개선하기 위해 SSG는 사이트를 빌드할 때 미리 렌더링해 둔 HTML자체를 서빙한다. 사용자가 접속할 수 있는 각 라우팅 경로에 대응하는 HTML파일들이 미리 생성된다. 이 파일들은 캐시가 가능하므로 유연성을 제공한다. 이상적으로는 클라이언트 측 자바스크립트가 최소화 되고 페이지를 다운로드 받자마자 인터랙티브가 가능하게 된다. 빠른 TTFB, FCP, TTI와 좋은 성능을 보인다.

Next.js에서

- 사이트가 next build 명령을 통해 빌드되면 /pages/about.js 파일은 about.html 파일로 프리렌더 되어 /about 경로에 서빙된다.
- 빌드 시점에 데이터를 불러올 필요가 있는 경우 데이터와 HTML로 렌더링 된 템플릿이 병합되어야 한다. 또 페이지는 얼마나 있는지 알기 위해 HTML로 만들어진 페이지에 해당하는 리스트 데이터가 필요하다.

```javascript
// getStaticProps() 함수는 서버에서 페이지를 만들기 전에 호출되고,
export async function getStaticProps() {
  return {
    props: {
      products: await getProductsFromDatabase(),
    },
  };
}
// 불러온 데이터는 pre-render될 페이지 컴포넌트의 props로 제공된다.
export default function Products({ products }) {
  return (
    <>
      <h1>Products</h1>
      <ul>
        {products.map((product) => {
          <li key={product.id}>{product.name}</li>;
        })}
      </ul>
    </>
  );
}
```

- 각각 대응하는 항목의 링크를 클릭하여 라우팅하게 되어야 하는 상세 페이지 등의 경우에는 빌드 시점에 각각의 데이터를 불러오면서 해당 데이터의 path를 매칭시켜 데이터를 보내준다.

```javascript
// /pages/products/[id].js
// getStaticPaths() 함수는 paths 배열을 반환한다.
export async function getStaticPaths() {
  const products = await getProductsFromDatabase();
  const paths = products.map((product) => ({
    params: { id: product.id },
  }));
  return { paths, fallback: false };
}
// 인자로 받은 paths에 해당하는 페이지를 만든다.
export async function getStaticProps({ params }) {
  return {
    props: {
      product: await getProductsFromDatabase(params.id),
    },
  }
}
export default function Products({ products }) {
  ...
}
```

- 하지만 SSG는 컨텐츠가 변경될 때 마다 새로 빌드해야 한다. 컨텐츠 변경 이후 배포하지 않으면 이전 컨텐츠가 계속 보여질 수 있다. 따라서 자주 변경되어야 하는 컨텐츠거나 HTML파일이 아주 많은 경우 적합하지 않을 수 있다.
- 응답 속도를 위해 호스팅 플랫폼을 사용하는 것도 좋은 방법이다. 여러 CDN에서 컨텐츠가 서빙하도록 세팅하면 성능을 이끌어낼 수 있다.
