---
title: "[React] Routing with React-Router"
category: "React"
tag: ["React", "Route", "Routing", "React Router", "react-router", "react-router-dom"]
---

# 라우팅과 React Router

리액트로 만든 웹 애플리케이션 내에서 사용자가 특정 URL을 요청하면 이에 해당하는 페이지를 사용자에게 보여주기 위해 관련 컴포넌트들을 렌더링해야할 것이다. 이와 같이 사용자가 요청한 URL에 해당하는 페이지를 보여주는 것, 다르게 표현하면 사용자로 하여금 URL에 해당하는 경로로 이동하게끔 해주는 것을 라우팅(Routing)이라 한다. HTML의 a 태그를 이용하여 다른 페이지로 이동하거나 리다이렉팅하는 것도 라우팅이라 할 수 있을 것이다. 

MPA(Multiple Page Application)과 같이 여러 페이지들로 구성된 웹 앱의 경우 HTML의 a 태그 등을 이용하여 하나의 페이지에서 다른 페이지로 이동하는 방식으로 라우팅을 구성할 수 있을 것이다. 그러나 리액트는 기본적으로 SPA(Single Page Application) 방식으로, 하나의 페이지 내에서 라우팅이 이루어져야 하며, 따라서 기존의 HTML의 a 태그를 사용해서는 SPA 방식에 위배될 것이다. 

하나의 페이지 내에서 사용자가 현재 도메인 내에서 특정 URL을 요청하면 그 URL에 해당하는 컴포넌트를 렌더링하여 보여줘야 할텐데, 이를 어떻게 해야할까? SPA의 특징을 유지하면서 라우팅하는 기술을 직접 구현하는 것은 만만치 않을 것이다. 다행히도 리액트 진영에는 이러한 기술이 있는데, 바로 [React Router](https://reactrouter.com/home)이다. 

React Router의 공식 문서에 따르면 특이하게도 해당 기술은 아예 프레임워크로 사용할 수도 있고, 간편하게 사용하기 위해 라이브러리처럼 사용할 수도 있다. 정확히는 다음과 같은 “mode”들이 있다. 위에서 아래로 갈수록 지원하는 기능들이 더 많아진다고 한다. 

- Declarative: 가장 기본적인 라우팅 기술만 지원한다. 여기에는 URL과 매핑되어 있는 컴포넌트 렌더링, 웹 앱 내에서의 navigating 등이 포함된다.
- Data: data loading, pending states 등 데이터와 관련된 기능들이 추가된다.
- Framework: 앞선 두 모드들을 모두 포함하며, 그 외에도 SPA, SSR, SSG에서의 라우팅 등의 추가 기능들을 제공한다고 한다.

이 글에서는 리액트로 만든 웹 앱 내에서 어떻게 라우팅을 구현할 수 있는지에 초점을 맞출 것이기에 최소한의 기능만 담겨있는 Declarative 모드만을 사용해볼 것이다. 

# React Router (Declarative mode) 설치 및 사용 환경 조성

여기서부터 보일 코드들의 전체 소스 코드는 [Github repo](https://github.com/JeroCaller/react-study/tree/master/with-react-router)를 참조.

리액트 프로젝트 폴더를 이미 생성한 상태라 가정하겠다. 필자는 Vite를 이용하여 생성했다. 명령프롬프트 창에서 해당 폴더의 루트에 위치한 상태에서 다음의 명령어를 입력하여 React Router 의존성을 설치한다.

```bash
npm i react-router
```

코드 1-1. react-router 설치 명령어

`react-router-dom` 으로 설치, 표현하는 자료들도 있지만 공식 문서에서는 현재 “react-router”라고만 표현하기에 필자도 이 글에서는 “react-router”라고만 표현할 것이다. 

설치를 성공적으로 마쳤다면 package.json의 “dependencies”에도 “react-router” 의존성이 들어왔을 것이다. 

이 react-router에는 BrowserRouter라는 컴포넌트가 있는데, 이는 라우팅을 사용하고자 하는 영역 또는 컴포넌트를 감싸는 역할을 한다. 이 BrowserRouter 컴포넌트가 감싸진 영역 또는 자식 컴포넌트 내에서만 라우팅을 사용할 수 있다. 보통은 어디에 라우팅 기능을 사용할지 모르기에 main.jsx에 있는 최상위 컴포넌트라 할 수 있는 App 컴포넌트를 감싸는 편이다. 

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router';
import './index.css';
import App from './App.jsx';

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>,
)

```

코드 1-2. main.jsx

위와 같이 `<App />` 컴포넌트를 감싸면 어느 컴포넌트에서든지 react-router에서 제공하는 라우팅 기능을 사용할 수 있게 된다. 

# 라우팅 구현하기

react-router에는 라우팅을 위해 다음의 컴포넌트들을 제공한다.

- Routes : Route라는 자식 컴포넌트를 감싸는 부모 컴포넌트
- Route : 라우팅하고자 하는 URI 경로(path) 및 해당 경로로 라우팅될 때 렌더링하여 사용자에게 보여줄 컴포넌트를 정한다.
- Link, NavLink : Route 컴포넌트로 명시한 특정 경로로 이동할 수 있게 해주는 링크를 화면에 렌더링하여 보여준다. 사용자는 화면에 생성된 이 링크를 누르면 Route에 명시된 경로로 라우팅되며, 그 경로에 해당하는 페이지를 볼 수 있게 된다. HTML의 a 태그와 같다고 보면 된다. 실제로 브라우저에서 확인해보면 a 태그로 렌더링된다.

다음은 방금 소개한 react-router 제공 컴포넌트들을 이용하여 라우팅을 구현한 예이다.

```jsx
import './App.css';
import Welcome from './pages/Welcome';
import { Routes, Route } from 'react-router';
import NotFound from './pages/NotFound';
import Tabs from './components/Tabs';
import MyInfo from './pages/MyInfo';
import Products from './pages/Products';
import NewsMain from './pages/NewsMain';
import LocalNewsPage from './pages/news/LocalNewsPage';
import DevNewsPage from './pages/news/DevNewsPage';

const App = () => {

  return (
    <div>
      <Tabs />
      <div className="route">
        <Routes>
          <Route path="/" element={<Welcome />} />  // #1
          <Route path="/my-info" element={<MyInfo />} />
          <Route path="/products" element={<Products />} />
          
          // #2
          <Route path="/news/*" element={<NewsMain />}>
            <Route path="local-region/:id" element={<LocalNewsPage />} />
            <Route path="dev/:id" element={<DevNewsPage />} />
          </Route>
          
          {/* 현재 도메인 내에서 존재하지 않는 
            URI 입력 시 처리할 페이지로 라우팅 
          */}
          <Route path="/*" element={<NotFound />} />  // #3
        </Routes>
      </div>
    </div>
  );
};

export default App;

```

코드 1-3. App.jsx

```jsx
import { Link, NavLink } from "react-router";

const Tabs = () => {

  return (
    <ul className="tab">
      <li><Link to="/">메인으로</Link></li>
      <li><Link to="/my-info">유저 정보</Link></li>
      <li><NavLink to="/products">상품 목록</NavLink></li>
      <li><NavLink to="/news">뉴스</NavLink></li>
    </ul>
  );
};

export default Tabs;

```

코드 1-4. Tabs.jsx

코드 1-3을 보면, 라우팅 기능을 위해 먼저 `<Routes>` 컴포넌트를 사용한 뒤, 자식 컴포넌트로서 `<Route>` 컴포넌트를 사용한 것을 볼 수 있다. Route 컴포넌트에는 두 개의 props가 사용되었는데, path와 element이다. 이 둘을 통해 어느 경로에 어떤 컴포넌트를 렌더링하여 사용자에게 보여줄지를 매핑하는 것이다. 예를 들어 #1의 경우 사용자가 “/”의 경로로 이동하고자 하는 경우 `<Welcome>` 컴포넌트를 렌더링하여 화면에 보이겠다는 것이다. 

한 편, Route 컴포넌트도 또 다른 자식 Route 컴포넌트를 가질 수 있다. 위 코드 1-3의 #2가 그 예이다. 이 경우, “/news”, “/news/local-region”, “/news/dev” URI가 생성되는 것이다. 이렇게 부모-자식 Route의 구조를 “Nested Routes”, 즉 중첩 Route라 한다. 중첩 Route의 경우, 부모 Route에 path를 지정하지 않으면 자식 Route에 지정된 Path만 생성된다. 반면 부모 Route에 element를 지정하지 않으면 자식 Route들에 공통의 상위 경로만 생성되고, 부모 Route에 해당하는 컴포넌트가 화면에 렌더링되지 않는다. 위 #2에서는 path, element 모두 지정했기에 부모 Route에 해당하는 경로와 화면 요소 모두 생성된다. 

중첩된 라우팅에서 자식 라우팅의 렌더링된 화면은 부모 라우팅에 매핑된 컴포넌트 내부에 `<Outlet>` 컴포넌트를 사용하여 보여줄 수 있다.

```jsx
import { Link, Outlet } from "react-router";
import { mockNews } from "../mock/mockNews";

const NewsMain = () => {

  return (
    <div>
      <h1>오늘의 뉴스</h1>
      <div className="news-main">
        <h2>지역</h2>
        <ul>
          {mockNews.local.map(data => <li key={data.id}>
            <Link to={`/news/local-region/${data.id}`}>{data.title}</Link> // #2
          </li>)}
        </ul>
        <h2>개발</h2>
        <ul>
          {mockNews.dev.map(data => <li key={data.id}>
            <Link to={`/news/dev/${data.id}`}>{data.title}</Link>  // #3
          </li>)}
        </ul>

        <hr/>

        {/* 자식 route는 부모 route 내의 <Outlet />을 통해 렌더링된다.*/}
        <Outlet />   // #1
      </div>
    </div>
  );
};

export default NewsMain;

```

코드 1-5. NewsMain.jsx

코드 1-3의 #2를 다시 보면 부모 Route에는 NewsMain 컴포넌트가 매핑되어 있고, 자식 Route에는 각각 LocalNewsPage, DevNewsPage 컴포넌트가 매핑되어 있다. 만약 사용자가 “/news/local-region” 경로로 이동한다면 해당 경로는 자식 Route에 해당하기에 그에 매핑된 화면을 보여주기 위해선 LocalNewsPage를 렌더링해야 할 것이다. 그러면서도 LocalNewsPage 컴포넌트는 자식 Route가 부모 Route 내부에 있기에 해당 컴포넌트도 부모 컴포넌트인 NewsMain 내부 어딘가에 위치해야할 것이다. 그 위치를 react-router에서 제공하는 `<Outlet>` 컴포넌트가 대신하는 것이다. Outlet 컴포넌트 자리에 LocalNewsPage 컴포넌트가 대신 차지하면서 렌더링된 화면이 NewsMain 내부에 보일 것이다(”/news/dev” 경로로 이동하면 DevNewsPage 컴포넌트가 Outlet 컴포넌트 자리에 대신 들어갈 것이다. 이렇게 어떤 자식 Route가 선택되느냐에 따라 동적으로 자식 컴포넌트가 선택되는 원리라 보면 되겠다). 아래에 보일 자료 1-1의 영상에 “뉴스” 탭을 클릭한 화면에서 특정 뉴스 제목 링크를 클릭하면 그 아래에 뉴스 내용이 나오는데, 이 사실을 이용하여 구현한 것이다. 

한 편, 개발자가 만든 웹 앱 도메인 내에 존재하지 않는 경로로 사용자가 들어올 수도 있는데, 이 때 Not found 메시지를 화면에 보여주면 사용자가 잘못된 경로로 들어왔음을 알릴 수 있을 것이다. 이를 코드 1-3의 #3에서 구현하였다. “/*”라고 하면 개발자가 정의하지 않은 경로로 들어오면 무조건 `<NotFound>` 컴포넌트를 화면에 보여주도록 하는 방식이다. 

```jsx
const NotFound = () => {

  return (
    <div>
      <h1>요청하신 페이지가 존재하지 않습니다.</h1>
    </div>
  );
};

export default NotFound;
```

코드 1-6. NotFound.jsx

![사진 1-1. 존재하지 않는 경로로 들어온 경우 “요청하신 페이지가 존재하지 않습니다.”라는 메시지를 화면에 띄우도록 하여 사용자가 잘못된 경로로 들어왔다고 알린다.](/images/2025-04-29/React-Routing-with-React-Router/1.png)

사진 1-1. 존재하지 않는 경로로 들어온 경우 “요청하신 페이지가 존재하지 않습니다.”라는 메시지를 화면에 띄우도록 하여 사용자가 잘못된 경로로 들어왔다고 알린다.

코드 1-3과 같이 Route만 가지고는 사용자가 다른 경로로 이동할 링크가 화면에 보여지지 않는다. 사진 1-1에 보여지는 탭 컴포넌트와 같이, 사용자가 다른 경로로 이동할 수 있게 해줄 링크를 화면에 보이도록 하려면 Link 또는 NavLink 컴포넌트를 사용해야 한다. 위 코드 1-4의 Tabs 컴포넌트 내부에 이를 사용하고 있다. `to`라는 props를 이용하여 앞서 만든 Route 컴포넌트의 prop인 `path`에 명시된 그 경로를 그대로 작성하면 된다. 

다음은 라우팅 기능이 완성된 웹 앱 시연 모습이다.

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-04-29/React-Routing-with-React-Router/1.mp4"
  controls="controles"
  muted="muted"
  width="70%"
></video>
</div>

자료 1-1. 라우팅 기능이 추가된 웹 앱 모습

위 영상을 보면 알 수 있듯, 탭으로 구현한 링크를 클릭하면 브라우저 URL 창에 변화가 생기면서 그에 해당하는 페이지가 화면에 보여지는 것을 알 수 있다. 라우팅 기능이 성공적으로 구현된 것이다. 그와 동시에 링크 클릭 시 페이지 깜빡임이나 페이지 로딩 시간이 걸린다는 것을 전혀 느끼지 못할 것이다. 이렇게 react-router를 이용하면 SPA의 특성을 지키면서 한 페이지 내에서 다른 화면을 보여줄 수 있도록 라우팅할 수 있는 것이다. 

## Dynamic segment : 경로의 동적 생성

코드 1-3을 다시 보면 다음과 같은 코드가 있다.

```jsx
<Route path="local-region/:id" element={<LocalNewsPage />} />
```

코드 2-1. 

`path` prop에 보이는 “:id”와 같이 사용되는 “:” 부분은 dynamic segment라 하여 동적으로 경로를 정할 때 사용되는 문법이다. id 부분에 1이라는 값이 들어오면 “/local-region/1”이란 경로가 생성되고, 3이 들어오면 “/local-region/3”이란 경로가 생성되는 방식이다. 코드 1-5, NewsMain.jsx에서 각각 #2, #3과 같이 동적으로 경로를 지정하는 것을 볼 수 있다. 링크 목록이 몇 개 생성될지 모를 때 이와 같이 활용할 수 있는 것이다. 

Dynamic segment는 파싱(parse)되어 Router Parameter로 불린다. 이러한 parameter는 이 경로와 매핑된 컴포넌트 내부에서 값으로 파싱하여 접근, 사용할 수 있는데, 이를 위해 사용되는 것이 react-router에서 제공하는 useParams라는 훅이다. 

```jsx
import { useParams } from "react-router";
import { mockNews } from "../../mock/mockNews";
import CommonNewsPage from "./CommonNewsPage";
import NewsNotFound from "./NewsNotFound";

const LocalNewsPage = () => {

  const params = useParams();
  const newsData = mockNews.local.find(data => Number(params.id) === data.id);

  if (newsData === undefined || null) {
    <NewsNotFound />
    return;
  };

  return (
    <div>
      <CommonNewsPage data={newsData} />
    </div>
  );
};

export default LocalNewsPage;

```

코드 2-2. LocalNewsPage.jsx (DevNewsPage 컴포넌트 내부도 이와 거의 똑같다)

위 코드에서 useParams 훅을 사용하여 얻은 Route 관련 모든 파라미터 정보가 담긴 객체를 params 변수에 할당하였다. 그러면 앞서 dynamic segment로 “:id”라 하였기에 `params.id` 와 같이 해당 파라미터의 값에 접근할 수 있는 것이다. 위 코드에서는 1부터 시작하는 번호가 부여된 여러 뉴스 데이터들 중 경로의 파라미터로 들어온 값과 일치하는 뉴스 데이터를 찾아 이를 화면에 렌더링하도록 구현하였다. 

## NavLink

NavLink는 Link와 같은 기능을 한다. 한 가지 다른 사실은 NavLink로 구현된 링크를 사용자가 클릭한 경우, NavLink에 해당하는 element에 자동으로 `.active` 라는 CSS 클래스가 생성된다. 반대로 다른 링크를 클릭했다면 해당 CSS 클래스가 사라지는 동적인 구조를 띈다. 이러한 사실을 이용하면 현재 클릭한 링크의 스타일을 변경하여 현재 사용자가 어느 링크를 선택했는지 알 수 있도록 할 수 있다. 

```css
/* NavLink가 선택된 상태일 때의 스타일 정의 */
.active {
  background-color: #888;
}
```

코드 3-1. App.css

위 자료 1-1의 영상을 보면 탭 중 “상품 목록” 또는 “뉴스” 링크를 클릭하면 해당 링크의 배경색만 바뀌는 데 위 코드 3-1과 함께 NavLink를 사용했기에 가능한 것이다. 

## useNavigate로 코드로 라우팅하기

앞서 본 모든 코드는 모두 사용자가 링크를 클릭하여 라우팅하는 방식이었다. 그런데 웹 앱을 구현하다보면 사용자가 링크를 클릭할 수 없는 상황에서 자동으로 다른 곳으로 라우팅되도록 해야하는 경우가 발생한다. 로그인 화면에서 로그인에 성공한 후 바로 이전 페이지로 이동하는 것이 그 예가 되겠다. 이렇게 사용자가 링크를 클릭하여 라우팅하는 방식이 아닌, 코드를 통해 특정 조건에서 자동으로 라우팅되도록 하려면 react-router에서 제공하는 useNavigate 훅을 사용하면 된다. 

다음은 사용자가 특정 버튼을 누른 후 뜨는 메시지 창에서 “확인” 버튼을 클릭했을 때에만 다른 경로로 라우팅되도록 한 예제이다.

```jsx
import { useNavigate } from "react-router";

const Products = () => {

  const mockData = [
    {
      id: 1,
      name: "포도주스",
      price: "5000"
    },
    {
      id: 2,
      name: "제로콜라",
      price: "2000"
    },
  ];

  const navigator = useNavigate();  // #1

  const handleClickButton = () => {
    if (confirm("정말로 메인으로 이동하시겠습니까?")) {
      navigator("/");  // #2
    }
  };

  return (
    <div>
      <h1>상품 목록</h1>
      <button onClick={handleClickButton}>메인으로</button>
      <table border="1">
        <thead>
          <tr>
            <th>상품번호</th>
            <th>상품명</th>
            <th>가격</th>
          </tr>
        </thead>
        <tbody>
          {mockData.map((data) => <tr key={data.id}>
            <td>{data.id}</td>
            <td>{data.name}</td>
            <td>{data.price}</td>
          </tr>)}
        </tbody>
      </table>
    </div>
  );
};

export default Products;

```

코드 4-1. Products.jsx

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-04-29/React-Routing-with-React-Router/2.mp4"
  controls="controles"
  muted="muted"
  width="70%"
></video>
</div>

자료 2-1. useNavigate를 이용한 라우팅 시연

위 코드 4-1의 #1과 같이 useNavigate() 훅을 사용하여 그 참조값을 할당받은 navigator 변수를 #2와 같이 라우팅하고자 하는 특정 경로를 문자열로 입력하여 특정 조건에서 호출하도록 하면 사용자가 Link, NavLink로 구성된 링크를 클릭하지 않아도 자동으로 라우팅되도록 할 수 있다. 

# 글을 마치며…

이 글에서는 리액트로 구현한 SPA 내에서 라우팅을 구현하는 방법에 대해 살펴보았다. 이 글에서 다 소개하지 못한 세부 기능들도 있는데, 이는 [React Router](https://reactrouter.com/home) 공식 사이트의 문서를 참고하면 된다. 

---

References

[1] 이고잉, “생활코딩! React 리액트 프로그래밍 개정판”, (위키북스, 2024)

[2] React Router official

[React Router Home](https://reactrouter.com/home)

[3] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife/QbpR/73)