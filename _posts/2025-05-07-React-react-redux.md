---
title: "[React] React Redux"
category: "React"
tag: ["React", "Redux", "React Redux", "Redux Toolkit", "RTK", "Boilerplate", "DX", "Developer Experience", "State", "useState", "Reducer", "순수성", "순수 함수", "Immutability", "불변성", "상태", "상태 관리", "리덕스"]
---

필자는 이전의 몇몇 글들을 통해 `useState` 를 이용하여 하나의 컴포넌트 내부에서 사용할 상태를 정의할 수도 있고, props를 이용하여 부모 컴포넌트에서 자식 컴포넌트로 데이터를 넘길 수도 있음을 살펴보았었다. 그런데 만약 여러 컴포넌트들에서 동일한 값을 공유해야 하는 상황이 온다면 이를 어떻게 처리할 수 있을까? 

![사진 1-1. 리액트 컴포넌트 간 상태 공유. 자식 컴포넌트에서 또 다른 자식 컴포넌트로 직접 상태를 전달할 수는 없다.](/images/2025-05-07/React-react-redux/1.png)

사진 1-1. 리액트 컴포넌트 간 상태 공유. 자식 컴포넌트에서 또 다른 자식 컴포넌트로 직접 상태를 전달할 수는 없다.

위 사진 1-1에서 보다시피, 리액트 컴포넌트는 하나의 자식 컴포넌트에서 다른 자식 컴포넌트로 바로 상태를 공유할 수는 없다. 이를 해결하기 위해선 두 자식 컴포넌트가 가진 공통의 부모 컴포넌트를 경유해야 한다. 다음의 예제를 통해 이러한 상황을 구현해보겠다. 참고로 구조는 위 사진 1-1과 동일한 구조로 만들었다. 

> 이 글에서 보일 모든 예제 코드의 전체 소스 코드는 하나의 깃허브 repo에 업로드하였다. 따라서 전체 소스 코드는 [이 글](https://github.com/JeroCaller/react-study/tree/master/react-redux-study)을 참고.
> 

먼저 다음과 같이 공통의 부모 컴포넌트를 정의하였다. 

```jsx
import { useState } from "react";
import LeftOne from "../components/no-redux-counter/LeftOne";
import RightOne from "../components/no-redux-counter/RightOne";

const NoReduxCounterPage = () => {

  const [count, setCount] = useState(0);

  const onIncrease = () => {
    setCount(count + 1);
  };

  const onDecrease = () => {
    if (count <= 0) return;
    setCount(count - 1);
  }

  return (
    <div className="no-redux-page no-redux-box">
      <h1>리덕스 없이 자식 컴포넌트 간 상태값 주고 받기</h1>
      <div className="left-right">
        <LeftOne counter={count} />
        <RightOne onIncrease={onIncrease} onDecrease={onDecrease} />
      </div>
    </div>
  );
};

export default NoReduxCounterPage;

```

코드 1-1. NoReduxCounterPage.jsx

여기서는 하나의 컴포넌트에서는 위 코드에서 정의한 컴포넌트 내 `count` 라는 상태값을 표시하고, 다른 컴포넌트에서는 이 상태값을 1씩 증가 또는 감소시키는 버튼이 달린 화면을 렌더링하도록 할 것이다. 위 코드는 이러한 두 컴포넌트들이 공통으로 가지는 부모 컴포넌트이다. 위 코드 1-1에서는 `count` 라는 상태와 더불어 이 상태값을 1씩 증가 또는 감소시킬 수 있는 이벤트 핸들러 `onIncrease` 및 `onDecrease` 함수를 각각 정의한 상태이다. 

이번에는 `LeftOne`이란 컴포넌트를 다음과 같이 정의하였다. 

```jsx
import LeftTwo from "./LeftTwo";

const LeftOne = ( { counter } ) => {

  return (
    <div className="no-redux-box">
      <LeftTwo counter={counter} />
    </div>
  );
};

export default LeftOne;

```

코드 1-2. LeftOne.jsx

그리고 이 컴포넌트의 자식 컴포넌트인 `LeftTwo`를 다음과 같이 정의하였다.

```jsx
const LeftTwo = ( { counter } ) => {

  return (
    <div className="no-redux-box">
      <p>count: {counter}</p>
    </div>
  );
};

export default LeftTwo;

```

코드 1-3. LeftTwo.jsx

`LeftTwo`에서 직접적으로 `count` 라는 상태값을 props 형태로 받아 화면에 표시하도록 하였다. 굳이 `LeftOne` 컴포넌트를 만든 이유는 복잡한 계층적 구조를 띄는 프로젝트와 유사한 구조를 만들기 위함이었다. 실제 프로젝트에서는 이것보다 훨씬 더 복잡하고 계층도 더 많은 구조를 띌 것이다. 

이번에는 `NoReduxCounterPage` 컴포넌트의 오른쪽 자식 컴포넌트인 `RightOne` 을 다음과 같이 정의하였다.

```jsx
import RightTwo from "./RightTwo";

const RightOne = ( { onIncrease, onDecrease } ) => {

  return (
    <div className="no-redux-box">
      <RightTwo onIncrease={onIncrease} onDecrease={onDecrease} />
    </div>
  );
};

export default RightOne;

```

코드 1-4. RightOne.jsx

그리고 이 컴포넌트의 자식 컴포넌트인 `RightTwo` 컴포넌트를 다음과 같이 정의하였다. 

```jsx
const RightTwo = ( { onIncrease, onDecrease } ) => {

  return (
    <div className="no-redux-box">
      <button onClick={onIncrease}>1 증가</button>
      <button onClick={onDecrease} >1 감소</button>
    </div>
  );
};

export default RightTwo;

```

코드 1-5. RightTwo.jsx

`RightTwo` 컴포넌트에서는 앞서 본 `count` 라는 상태값을 직접 1씩 변경할 수 있는 버튼이 달린 UI를 렌더링하도록 정의하고 있다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-05-07/React-react-redux/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

자료 1-1. 

이렇게 두 자식 컴포넌트 간의 상태값을 공유하려면 공통의 부모 컴포넌트에서 상태값을 정의한 후, 이 상태값 그대로 또는 이 상태값을 변경하는 이벤트 핸들러 형태로 자식 컴포넌트에 props 형태로 전달해야 한다. 그런데 이러한 방식은 소규모의 프로젝트에서는 별다른 문제를 느끼지 못할 수 있어도 조금만 그 규모가 커지면 자연스레 다음의 문제들이 따라온다.

- 상태를 직접 사용하지 않는 컴포넌트에도 부모-자식 컴포넌트 간 상태 전달을 위한 코드를 작성해야 한다. 위에서 보인 `LeftOne` , `RightOne` 컴포넌트의 코드를 보더라도 `count` 라는 상태값을 사용하지 않음에도 props 및 이를 자식 컴포넌트에 전달하는 별도의 코드를 작성해야 한다는 점이다. 이는 달리 말하면 특정 상태값과 전혀 연관이 없는 컴포넌트들까지 해당 상태와 어느 정도 의존성을 가지게 된다는 것이다. 이 경우 나중에 리팩토링 등의 이유로 컴포넌트 간 계층 구조에 변화를 줘야 하는 경우 기존 코드까지도 모두 수정해야 할 것이다. 이는 OCP(Open-Closed Principle)에도 위배되는 사항이라 볼 수 있다.
- `NoReduxCounterPage` 컴포넌트에서도 보다시피, `count` 라는 공통의 상태값을 변경하는 함수들을 `onIncrease` , `onDecrease` 와 같이 별도의 함수들로 작성해야 한다는 점이다. 공통의 상태값을 변경하는 기능들을 하나의 함수로 관리하는 것이 유지보수 입장에서도 더 좋을 것이다.

이러한 이유로 인해 하나의 앱에서 여러 컴포넌트들이 공유하는 상태값들이 존재하게 될 경우 앞서 본 방식보다는 차라리 특정 상태들을 따로 저장, 관리하는 별도의 공간을 만든 후, 이를 필요로 하는 컴포넌트들이 이 공간에 직접 접근하게 하는 방식이 더 좋을 것이다.

![사진 1-2. 리덕스를 이용한 상태 관리 방법. 상태를 store라는 공용 공간에 저장한 후, 이를 필요로 하는 컴포넌트에서 직접 이 공간에 접근하여 상태에 접근할 수 있도록 하는 것이 더 효율적일 것이다.](/images/2025-05-07/React-react-redux/2.png)

사진 1-2. 리덕스를 이용한 상태 관리 방법. 상태를 store라는 공용 공간에 저장한 후, 이를 필요로 하는 컴포넌트에서 직접 이 공간에 접근하여 상태에 접근할 수 있도록 하는 것이 더 효율적일 것이다.

이와 같은 방식으로 하면 공통의 상태에 접근하기 위해 중간에 위치한 컴포넌트들을 경유할 필요가 전혀 없을 것이고, 따라서 해당 state와 전혀 상관없는 컴포넌트들은 코드 상으로도 독립적으로 존재할 수 있게 되어 유지보수 및 확장성 확보에도 용이할 것이다. 이를 가능하게 하는 기술이 리덕스(Redux)이다. 

# React Redux

[공식 문서](https://ko.redux.js.org/introduction/getting-started)에 따르면 Redux를 “자바스크립트 앱을 위한 예측 가능한 상태 컨테이너”라고 소개하고 있다. 조금 더 쉽게 말하자면, 애플리케이션의 상태를 “store”라는 하나의 공간에 모아 놓은 전역 수준의 상태 관리 라이브러리라 보면 되겠다. `useState` 를 이용한 상태(state)는 하나의 컴포넌트 내부에서만 사용 가능하다는 점에서 일종의 “지역 상태”라 볼 수 있다면, 리덕스를 이용한 상태는 애플리케이션 어디서든 접근하여 읽고 변경할 수 있기에 “전역 수준의 상태”라고도 표현할 수 있겠다. 이렇게 전역 수준의 상태 관리를 리덕스가 대신 해주기에 개발자는 조금 더 핵심 로직에 집중할 수 있게 된다. 

참고로 Redux라는 기술 자체는 리액트에 특화된 기술이 아니라 자바스크립트로 작성된 앱이라면 사용될 수 있는 기술이다. 그런데 이 Redux를 그대로 리액트 프로젝트에 접목시키기에는 어려움이 있다고 한다. 따라서 실제로는 Redux를 리액트 프로젝트에 사용할 수 있게 해주는 바인딩 라이브러리인 [React Redux](https://react-redux.js.org/)를 사용한다. 다만 이 글에서는 Redux, React Redux라는 두 용어를 편의상 따로 구분하지는 않겠다. 

Redux에서 언급되는 핵심 개념들에 대한 설명을 글로만 풀기보다는 이 Redux를 직접 사용하는 예제와 함께 살펴보는 것이 더 이해하기 쉬울 것 같아 예제와 함께 살펴보고자 한다. 

먼저 명령 프롬프트 창에 다음의 명령어를 입력하여 Redux 라이브러리를 설치한다. 

```jsx
npm i redux react-redux @reduxjs/toolkit
```

코드 2-1. Redux 사용을 위한 의존성 설치 명령어

여기서는 `redux`, `react-redux` , `@reduxjs/toolkit` 이 세 라이브러리를 설치하였다. 다만 공식 문서에 따르면 `redux` 에서 제공하는 API보다는 `@reduxjs/toolkit` , 즉 리덕스 툴킷(줄여서 RTK)을 사용하도록 권고하고 있다. 따라서 이걸 설치하면 `redux` 를 별도로 설치할 필요는 없다고 한다. RTK는 기존의 react-redux를 더 편리하게 사용해주는 툴킷이다. 하지만 여기서는 Redux의 원리 및 개념 파악이 우선이라 생각하기 때문에 먼저 RTK의 사용을 최소화하여 Redux를 사용하는 예제를 보일 것이고, 그 다음에 RTK를 중점적으로 사용하는 예제를 따로 살펴볼 예정이다. 

앞서 본 count 예제를 React Redux로 바꿔보겠다. 이 라이브러리에서는 `<Provider>` 라는 컴포넌트를 제공하는데, 이 React Redux를 사용하고자 하는 앱 주위를 감싸야 그 안에서 Redux를 사용할 수 있게 된다. 이는 마치 이전에 살펴보았던 React Router의 `<BrowserRouter>` 와 똑같은 원리라 보면 되겠다. 이로 인해 앱 전역에서 리덕스를 사용하고자 한다면 main.jsx 파일에서 다음과 같이 설정하는 것이 좋을 것이다. 

```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import App from './App.jsx'
import { BrowserRouter } from 'react-router'
import { Provider } from 'react-redux';
import reduxCounterStore from './redux/redux-counter/reduxCounterStore.js';

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <Provider store={reduxCounterStore}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </StrictMode>,
)

```

코드 2-2. main.jsx

다만 필자는 하나의 리액트 프로젝트에서 여러 상황을 살펴보고자 하기에 특별히 다음의 컴포넌트에 적용하였다. 

```jsx
import { Provider } from "react-redux";
import reduxCounterStore from "../redux/redux-counter/reduxCounterStore";

import LeftOne from "../components/redux-counter/LeftOne";
import RightOne from "../components/redux-counter/RightOne";

/**
 * redux를 활용하여 state를 관리하면 전혀 다른 페이지로 라우팅했다가 다시 
 * 돌아와도 해당 state가 사라지지 않고 저장되어 있다. 
 * 
 * 만약 리덕스를 사용하지 않고 단순히 useState를 이용하면 다른 페이지로 갔다가 
 * 돌아오면 이전 state 정보가 사라져서 0으로 초기화된다. 
 * 
 * @returns 
 */
const ReduxCounterPage = () => {

  return (
    <Provider store={reduxCounterStore}>
      <div className="no-redux-page no-redux-box"> {/* CSS 스타일 재활용 */}
        <h1>리덕스 활용하여 컴포넌트 간 상태값 주고 받기</h1>
        <div className="left-right">
          <LeftOne />
          <RightOne />
        </div>
      </div>
    </Provider>
  );
};

export default ReduxCounterPage;

```

코드 2-3. ReduxCounterPage.jsx

위 코드에서도 볼 수 있듯, `<Provider>` 컴포넌트에는 `store` 라는 prop을 필수로 주입해줘야 한다. 위 코드에서는 `reduxCounterStore` 를 `<Provider>`에 주입하고 있는데, 해당 스토어는 다음과 같이 별도의 js 파일에 정의하였다.

```jsx
import { configureStore } from '@reduxjs/toolkit';
import countReducer from './countReducer';

const reduxCounterStore = configureStore({
  reducer: countReducer,
});

export default reduxCounterStore;
```

코드 2-4. reduxCounterStore.js

앞서 언급했듯, 리덕스에서는 스토어라는 앱 전역 수준의 별도 공간을 생성하여 여기서 상태를 관리한다고 하였다. 위 코드 2-4는 이러한 스토어를 생성하는 코드라 보면 되겠다. RTK에서 제공하는 `configureStore` 함수를 이용하여 스토어를 생성할 수 있고, 이 스토어를 `export` 하면 된다. 

위 코드 2-4에서 볼 수 있듯, `configureStore` 함수의 인자로 들어가는 객체 리터럴 내부에는 `reducer` 라는 프로퍼티가 존재하며, `countReducer` 와 같이 개발자가 직접 reducer 함수를 정의하여 여기에 등록해줘야 한다. 개발자는 컴포넌트 계층에서 직접 스토어에 접근하여 상태값을 변경할 수 없다. 대신 이후에 소개할 action을 미리 정의한 후 이를 이용하여 상태값을 변경해야 한다. 이 때 이 action을 호출하면 내부적으로 리듀서를 이용하여 상태값을 변경하는 구조인 것이다. 

위 코드 2-4에 등록할 `countReducer` 를 별도의 js 파일에 다음과 같이 정의하였다.

```jsx
const initialState = {
  count: 0,
};

const countReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'INCREASE':
      return {...state, count: state.count + 1}
    case 'DECREASE':
      if (state.count === 0) {
        return state;
      }
      return {...state, count: state.count - 1}
    default:
      // 기존의 액션 타입 중 하나라도 해당되지 않을 경우 기존 state를 그대로 반환.
      return state;
  }
};

export default countReducer;

```

코드 2-5. countReducer.js

리듀서(reducer)라는 것은 위 코드에서 볼 수 있듯 state와 action을 인자로 받는 함수로, 기존의 state를 토대로 새로운 state를 반환하는 함수를 의미한다. 여기서의 state는 store에서 관리될 상태를 의미하며, action은 이 state의 값을 어떻게 변경할 것인지 알려주는 객체 리터럴이다. 참고로 action은 `type` 이라는 프로퍼티와, 관례적으로 `payload` 라는 프로퍼티를 포함한다. `type` 프로퍼티는 문자열로 구성되며, 상태값을 어떻게 변경시킬 것인지 그 작업을 설명하는 문자열을 주로 사용한다. 위 예제에서는 숫자 카운트를 위해 각각 숫자 증가와 감소 액션이 존재한다. 따라서 이 경우 각각 `INCREASE` 와 `DECREASE` 라고 액션 타입 이름을 부여할 수 있다. 또한 이 액션에 추가하고자 하는 데이터가 있을 경우 `payload` 라는 프로퍼티 안에 담아 전송할 수 있다. 예를 들어 사용자 정보가 들어있고 이 중 일부를 변경하고자 할 경우, `{type: '...', payload: {username: 'newName', age: '20'}}` 과 같은 형태로 새 데이터를 담아 유저의 정보를 담은 상태값을 새로 변경하도록 할 수도 있는 것이다. 다만 위 카운터 예제에서는 별도의 추가 데이터가 필요하지 않아 `payload` 라는 프로퍼티를 따로 사용하고 있진 않고 있다. 

위 코드 2-5에서처럼 리듀서는 여러 종류의 `action.type` 들을 switch문으로 받아드리며, 각각의 타입에 대한 상태값 변경 로직을 정의하였다. 예로 `INCREASE` 란 액션 타입이 들어오면 `state.count` 의 값을 1 증가하도록 하고, `DECREASE` 란 액션 타입이 들어오면 `state.count` 의 값을 1 감소시키도록 하고 있다. 만약 이렇게 정의한 액션 타입들 중 하나라도 해당되지 않는 액션이 들어온다면 `default` 구문을 통해 기존 state의 값을 그대로 반환시키도록 구성하였다. 

이렇듯 Redux store 내부에 저장, 관리될 상태는 개발자가 직접 접근하여 변경하는 것이 아니라 리듀서라는 함수를 통해 어떻게 변경시킬 것인지 그 로직을 작성하는 형태로 이루어진다. 

한 편, 리듀서는 기존의 state와 action을 토대로 새로운 state를 반환하는 함수이기에 초기 state 값이 설정되어야 하는데, 이를 `initialState` 변수에서 정의하고 있다. 원래 store에는 한 가지의 state가 아닌 여러 종류의 state가 들어갈 수 있다. 예를 들어 유저 정보를 저장하는 state와 할 일 목록들을 저장해 놓은 state가 있을 수 있다. 이렇게 각 기능별로 state를 구분짓기 위해 root state는 `initialState` 에서 보다시피 객체 리터럴로 정의한 후, 프로퍼티로 각 기능별로 사용될 상태 변수를 정의하는 방식을 가진다. 

한 편, 리듀서 함수에서는 기존 state 객체의 프로퍼티를 바꾸는 방식이 아니라 전개 연산자(…)를 이용하여 아예 새로운 state 객체를 반환하는 방식으로 state를 바꾸도록 구성하였다. 굳이 이렇게 하는 이유는 리덕스에서는 상태가 변경되었는지 여부를 이전 state와 현재 state 객체 간의 얕은 비교(shallow comparison)를 통해 판단하기 때문이다. 즉 객체 내 “값”의 비교를 통해 변화를 감지하는 게 아니라 객체의 “참조(Reference)”의 비교를 통해 변경을 감지하는 구조이기 때문이다. 참고로 이와 같이 기존의 state의 값을 변경하지 않는 특성을 “불변성(immutability)”이라 하며, 결국 리덕스에서는 상태 변경을 “불변성”을 지키면서 해야 한다는 뜻이 된다. 

리듀서라는 함수는 Redux에서 정의한 개념이 아니라 [리액트 공식 문서](https://ko.react.dev/learn/extracting-state-logic-into-a-reducer)에서도 다루듯이, 리액트에 존재한다고 볼 수 있는 개념이다. 리듀서는 하나의 공통의 state를 변경하기 위해 분산되어 정의되어있는 여러 개의 이벤트 핸들러들을 하나의 단일 함수로 묶어 관리하기 위한 함수이다. 그런데 이 리듀서 함수는 반드시 “순수”해야 한다는 “순수성”의 특성을 가진다. 여기서의 순수성이란 1. 함수 호출 전 함수 외부에 있는 그 어떠한 변수, 객체를 변경해선 안되며(외부와의 독립성 추구), 2. 수학에서의 `y=ax` 방정식처럼 같은 입력에 대해선 항상 같은 출력을 도출해야한다는 성질을 의미한다. 이에 따르면 다음의 함수는 순수 함수가 될 수 없다.

```jsx
let global = 0;

function notPureFunc() {
    global = global + 1;  // 외부 (전역) 변수의 값을 변경하기에 순수성이 깨짐.
    return `저는 음료수를 ${global}캔 마셨습니다.`;
}

// 위와 똑같은 기능의 순수 함수를 작성하려면 다음과 같이 작성해야 함.
function pureFunc(local) {  // 매개변수로 선언하여 외부 값을 전달받도록 한다. 
    local = local + 1;
    return `저는 음료수를 ${global}캔 마셨습니다.`;
}
```

코드 2-6. 순수 함수가 아닌 예.

상태 변경을 감지하기 위해 얕은 비교를 사용하는 Redux에서는 이를 위해 리듀서라는 개념을 차용한 것으로 보인다. 이러한 “순수성”을 보유한 리듀서를 사용해야 새로운 state 객체를 반환하도록 하여 얕은 비교에 의한 상태 변경이 가능해지기 때문이다. 

> “Reducer”라는 이름은 자바스크립트의 배열 객체에 있는 `reduce()` 라는 메서드에서 따와 지어졌다고 한다. `reduce()` 라는 메서드는 배열에 있는 여러 요소들을 하나의 값으로 누적시키는 연산을 수행한다. 지금까지 누적된 결과를 첫 번째 인자로 하고, 현재 배열 요소를 두 번째 인자로 하는 콜백 함수를 인자로 받는 구조이기도 하다.
> 
> 
> ```jsx
> // 배열 내 모든 숫자의 합을 구하는 예제
> const arr = [1, 2, 3, 4, 5];
> const sum = arr.reduce(
>   (result, number) => result + number
> ); // 1 + 2 + 3 + 4 + 5
> ```
> 
> 코드 2-7. [리액트 공식 문서](https://ko.react.dev/learn/extracting-state-logic-into-a-reducer#why-are-reducers-called-this-way)에서 발췌.   
> 이러한 `reduce()` 메서드의 기능이 리듀서 함수와도 그 맥락이 일치하기에 붙여진 이름이라고 한다. 리듀서 함수도 최초의 상태(`initialState` )로부터 `action` 을 통해 새로운 state를 반환하는 구조이기 때문이다. 
> 

다시 코드로 돌아와서, 이번에는 코드 2-5에서 정의한 리듀서에서 사용할 액션 객체들을 다음과 같이 별도의 파일에 정의하였다.

```jsx
const increase = () => {
  return {
    type: "INCREASE"
  };
};

const decrease = () => {
  return {
    type: "DECREASE",
  };
};

export {increase, decrease};

```

코드 2-8. countAction.js

참고로 이렇게 액션 객체를 생성, 반환하는 함수를 “action creator”, 즉 “액션 생성자”라고 부른다. 이러한 액션 생성자를 정의하면 컴포넌트 단에서 `{type: 'INCREMENT'}` 라고 번거롭게 작성하는 대신 `increase()` 함수만 호출하면 되기에 사용성이 더 편리해진다는 장점이 있다. 

여기까지가 redux 관련 설정이었다. 이제 이를 이용하여 애플리케이션 내 어떤 컴포넌트에서든 `count` 상태값을 읽거나 변경해보도록 해보자. 

앞선 코드 2-3의 `ReduxCounterPage` 의 왼쪽 자식 컴포넌트인 `LeftOne` 는 다음과 같이 작성한다.

```jsx
import LeftTwo from "./LeftTwo";

const LeftOne = () => {

  return (
    <div className="no-redux-box">
      <LeftTwo />
    </div>
  );
};

export default LeftOne;
```

코드 2-9. LeftOne.jsx

이 컴포넌트의 자식 컴포넌트인 `LeftTwo` 컴포넌트를 다음과 같이 정의하였다.

```jsx
import { useSelector } from "react-redux";

const LeftTwo = () => {

  const count = useSelector(state => state.count);

  return (
    <div className="no-redux-box">
      <p>count: {count}</p>
    </div>
  );
};

export default LeftTwo;

```

코드 2-10. LeftTwo.jsx

`LeftTwo` 에서는 앞서 Redux의 store 내 state로 정의한 `count` 를 읽어와 `count` 라는 변수에 담고 있다. 이렇게 Redux의 store 내부에 있는 특정 state를 가져오기 위해 Redux에서 제공하는 `useSelector` 라는 훅을 사용한다. 이 훅의 인자로 `state.count` 의 값을 읽어오기 위한 콜백 함수를 작성한다. 이 콜백함수의 인자는 항상 root state인 `state` 를 인자로 해야 한다. 지금이야 `count` 라는 단 하나의 상태만 사용해서 왜 굳이 이렇게 콜백 함수를 사용해야 하는 것인지 감이 안잡힐 수도 있겠다. 프로젝트 규모가 조금만 커져도 여러 기능들이 생길 것이고 각 기능마다 별도로 사용해야할 상태들도 늘어나기 마련인데, 이렇게 수많이 생성한 상태들 중 특정 상태를 선택하고자 하려면 필수일 수밖에 없다. 예를 들어 유저 정보를 담은 `state.user` 상태를 정의하였고, 판매 물건의 정보를 담은 `state.product` 상태가 있을 때, 어떤 컴포넌트에서 유저 정보만 가져오고 싶다면 `useSelector( state => state.user )` 와 같이 사용하여 두 상태를 서로 구분짓는 것이다. 

액션 생성자와 마찬가지로 `useSelector` 의 인자로 들어갈 콜백 함수 자체를 아예 미리 정의하고 이를 호출하는 방식으로 사용할 수 있다. 

```jsx
const selectCounter = state => state.count;

export {selectCounter};

// 리액트 함수 컴포넌트
const count = useSelector(selectCounter);
```

코드 2-11. 예시

위 `selectCounter` 와 같이 `useSelector` 훅 함수의 인자로 들어갈 콜백 함수를 미리 정의한 함수를 selector라 부른다. 이러한 selector까지 미리 정의하여 사용한다면 개발자는 컴포넌트 단에서 특정 state를 선택하기 위해 번거로운 콜백 함수를 매번 작성할 필요가 없어져 편리해질 것이다. 

한 편 `count` 상태 값을 변경시킬 `RightOne` , `RightTwo` 컴포넌트를 각각 다음과 같이 정의하였다.

```jsx
import RightTwo from "./RightTwo";

const RightOne = () => {

  return (
    <div className="no-redux-box">
      <RightTwo />
    </div>
  );
};

export default RightOne;

```

코드 2-12. RightOne.jsx

```jsx
import { useDispatch } from "react-redux";
import { decrease, increase } from "../../redux/redux-counter/countAction";

const RightTwo = () => {

  const dispatch = useDispatch();

  const handleOnIncrease = () => {
    dispatch(increase());  // dispatch({type: 'INCREMENT'});
  };

  const handleOnDecrease = () => {
    dispatch(decrease());
  };

  return (
    <div className="no-redux-box">
      <button onClick={handleOnIncrease}>1 증가</button>
      <button onClick={handleOnDecrease}>1 감소</button>
    </div>
  );
};

export default RightTwo;

```

코드 2-13. RightTwo.jsx

store 내 특정 state의 값을 컴포넌트 단에서 바꾸고자 할 경우 위 코드 2-13처럼 `useDispatch()` 라는 훅을 사용하면 된다. 앞서 액션 생성자를 별도로 정의하였기 때문에 컴포넌트 단에서는 `dispatch(increase());` 와 같이 원하는 액션 생성자 함수만 호출하면 된다. 

이렇게 보니, Redux를 사용하지 않은 count 예제와 비교했을때 이번에는 중간에 끼인 `LeftOne` , `RightOne` 컴포넌트에는 `count` 와 관련된 코드가 하나도 없음을 알 수 있다. 이 덕분에 Redux를 사용하면 Redux에서 관리하는 상태와 전혀 연관없는 컴포넌트들과의 의존성이 존재하지 않아 추후 리팩토링에 의한 컴포넌트 간 계층에 변화가 생겨도 문제 없이 리팩토링할 수 있다는 장점을 얻을 수 있다. 

## 정리

여기까지 살펴본 Redux의 편리한 점을 정리하면 다음과 같다. 

- 앱을 이루는 컴포넌트들은 부모-자식 관계의 트리 구조의 계층을 가진다. 이 때 중간의 컴포넌트를 경유할 필요없이 store라는 공용 공간에 저장된 state에 접근, 변경할 수 있어 이 state와 관련 없는 컴포넌트들은 이 state와의 독립성을 유지할 수 있고, 따라서 유지보수에도 용이하다. 리팩토링 등의 이유로 컴포넌트 간 계층 구조가 바뀌더라도 상태와 관련해서 신경쓸 코드가 없다는 편리함이 있다.
- Redux는 내부적으로 reducer라는 함수를 사용하기에 똑같은 상태에 대해 여러 가지 형태로 변경하는 여러 개의 이벤트 핸들러들을 단일 함수로 모아 관리하기에 이 점에서도 유지보수의 장점이 있다.

그리고 지금까지 예제를 통해 소개된 Redux 개념들을 정리하면 다음과 같다. 

- store : 애플리케이션 전역 수준에서의 상태 관리를 위한 state 보관 공간.
- action : store에 저장된 특정 state의 값을 변경하기 위한 객체 리터럴. 어떻게 바꿀 것인지에 대한 액션 타입 `type` 과 추가적인 데이터를 위한 `payload` 프로퍼티로 구성되어 있다. 참고로 리덕스에서는 `type` 에 관해 관례적으로 `user/changeUserInfo`와 같이 `domain/eventName` 의 형태로 type 값을 정한다고 한다 (여기서 domain은 어떤 기능명을, eventName은 해당 상태값을 어떻게 변경시킬 것인지에 관한 것이라고 볼 수 있다).
    
    ```jsx
    {
        type: "user/changeUserInfo",
        payload: {
            username: "새로운 닉네임123",
            habit: "게임에서 독서로 변경됨",
        }
    }
    ```
    
    코드 2-14. action 객체 예시.
    
- action creator : action 객체를 생성, 반환하는 함수. 필수는 아니지만 이를 정의해두면 컴포넌트 단에서 `dispatch(increment())` 와 같이 state 값을 변경하는 코드를 더 간결하게 작성할 수 있게 된다.
- reducer : `(state, action)` 을 인자로 받는 순수 함수. 기존의 state값을 새로운 state로 변경하는 역할을 한다. 새 state 반환 시 기존의 state 객체 내 데이터를 바꾸는 방식이 아니라 아예 전혀 새로운 객체를 반환하도록 작성해야 한다. 특정 기능에 대한 여러 변경 방법들을 여기서 정의할 수 있다.
- selector : store에 저장된 여러 종류의 state들 중 특정 state를 선택하기 위한 함수. `useSelector` 훅 함수의 인자로 대입하여 사용할 수 있다. 필수로 정의해야 하는 것은 아니지만 정의하여 사용하면 코드를 더 간결하게 유지할 수 있다.
- `useSelector`: React Redux에서 제공하는 hook으로, store에 저장되어 있는 특정 state를 “선택”하여 가져오기 위한 훅이다. `useSelector(state => state.count)` 와 같은 형태로 콜백 함수를 인자로 대입하여 특정 state를 가져오는 방식이며, selector를 정의하면 `useSelector(selectCounter)` 와 같이 코드를 더 간결하게 작성하여 state를 가져올 수 있다.
- `useDispatch`: React Redux에서 제공하는 hook으로, store에 저장되어 있는 특정 state의 값을 컴포넌트 계층에서 변경하기 위해 사용되는 함수이다. `const dispatch = useDispatch()` 와 같은 형태로 지역 변수에 할당한 후, `dispatch({type: '...', payload: {...}})` 와 같이 호출하여 사용하면 된다. 인자로 action 객체를 대입하며, `payload` 프로퍼티를 사용한다면 보통 `payload` 에 명시된 새로운 객체 내 정보를 토대로 state의 값을 변경 또는 생성해달라는 요청으로 쓰인다. 액션 생성자(action creator)와 함께 사용하면 `dispatch(increase())` 와 같은 형태로 코드를 더 간결하게 사용할 수 있다.

코드 작성 순서로는 다음과 같이 정리할 수 있겠다.

1. React Redux에서 제공하는 `<Provider>` 컴포넌트로 Redux를 사용하고자 하는 최상위 컴포넌트 주위를 감싼다. 보통은 main.jsx에 있는 `<App/>` 컴포넌트를 둘러싸 애플리케이션을 구성하는 모든 컴포넌트에서 Redux를 사용할 수 있도록 하는 편이다. 그 후 `<Provider />`  의 prop인 `store` 에 개발자가 정의한 store 객체를 대입한다. 
2. `<Provider>` 의 prop인 `store`에 넣을 store 객체를 별도의 js 파일에 정의한다. 이 때 `@reduxjs/toolkit` 에서 제공하는 `configureStore` 객체를 이용하여 store 객체를 생성, export 하는 방식으로 작성한다. 그리고 `configureStore` 함수의 객체 인자의 `reducer` 인자에는 개발자가 정의한 리듀서 함수들을 명시하면 된다. 
3. 2에서 말한 `configureStore` 에 들어갈 리듀서 함수들을 별도의 js 파일에 정의한다. 하나의 리듀서 내부에는 같은 state를 대상으로 하는 여러 action type에 대한 코드들을 switch 문과 함께 작성한다. 
4. 필요 시 selector 또는 action creator를 별도의 파일로 작성하여 정의한다. 이는 필수는 아니지만 작성하면 더 편리하게 Redux를 사용할 수 있다. 여기까지가 Redux 설정 과정이라 보면 되겠다.
5. 컴포넌트 계층에서 Redux store 내 특정 state를 사용하고자 한다면 `useSelector` 훅을, state를 변경하고자 한다면 `useDispatch` 훅을 이용한다. selector는 `useSelector` 훅과, action creator는 `useDispatch` 훅과 함께 사용하여 코드를 더 간결하게 작성할 수 있다. 

# Redux Toolkit (RTK)을 사용하여 코드를 더 간결하고 체계적으로 작성하기

지금까지 소개한 방식을 사용하면 Redux를 이용한 애플리케이션 상태 관리가 가능하다. 그러나 action, reducer, selector를 각각 별도의 파일로 작성하여 사용하면 모듈의 수가 불필요하게 늘어날 것이다. 또한 action(또는 action creator), selector를 개발자가 직접 정의하는 것은 불필요한 보일러플레이트 코드를 생성한다는 단점도 있다. Redux의 핵심은 전역 수준에서의 상태 관리이지 action, selector를 직접 정의하는 것이 아니기 때문이다. 

> 보일러플레이트 코드(Boilerplate code)는 최소한의 변경으로 여러 곳에서 재사용되며 반복적으로 비슷한 형태를 띄는, 필수적이면서도 정형화된 코드를 의미한다. 보일러플레이트 코드는 대부분 핵심, 비즈니스 로직과는 연관이 없으나 그렇다고 해서 없으면 실행 자체가 안되는 특징을 지니고 있다. 따라서 보일러플레이트 코드가 프로젝트 전반에 걸쳐 많으면 많을수록 개발자가 비즈니스 로직에만 집중하기 힘들어진다는 단점이 있다. 한 예로 자바의 JDBC를 이용하여 외부 DB와 연결하여 자바 내에서 DB에 접근하고자 할 때에도 보일러플레이트 코드가 발생한다. connection 객체를 생성, try~catch~finally 구문을 이용하여 작업 후 `close()` 메서드로 해체하는 코드, `DriverManager.getConnection(...)` 등의 정형화된 코드로 DB와 연결하는 작업 등이 그 예이다. Redux에서도 마찬가지로 action, action creator 등을 매번 같은 형태로 개발자가 일일히 작성해야 하는데 이도 보일러플레이트 코드라 볼 수 있다. 이러한 보일러플레이트라는 반복적인 코드가 핵심 로직의 집중을 힘들게 하고 개발자 경험(Developer experience, DX)을 떨어뜨린다는 단점 때문에 대부분 이 코드를 추상화하여 개발자가 좀 더 사용하기 쉽도록 만드는 경우가 많다. JDBC의 경우 ORM을 이용하면 JDBC 내에서 발생하는 보일러플레이트 코드를 더 이상 작성하지 않아도 되는 것이 그 예이다. Redux에서도 이러한 보일러플레이트 코드를 추상화하여 개발자가 더 이상 반복적인 코드를 작성하지 않도록 한 것이 Redux Toolkit, RTK이다. 
참고로 옛날 신문사업에서 계속 반복되어 사용되는 문구들을 강철 재질의 인쇄판에 새겨 사용했는데 이 인쇄판을 보일러플레이트라 불렀고, 이러한 특성에서 해당 용어가 소프트웨어 쪽에서도 앞서 설명한 뜻대로 사용되기 시작하였다.
> 

이러한 보일러플레이트 코드를 줄이고 더 간편하게 React Redux를 사용할 수 있도록 해주는 도구가 Redux Toolkit (RTK)이다. 이전에 Redux 설치 시 살펴보았듯 RTK를 사용하려면 먼저 `npm i @reduxjs/toolkit` 명령어로 해당 의존성을 설치해야 한다. 

RTK 사용 시 기존의 React Redux처럼 `<Provider>` , store 객체, 이 store 객체를 생성하기 위한 `configureStore()`를 이용해야 하는 것은 변함이 없다. 다만 RTK만의 특징을 살펴볼 수 있는 구간은 `createSlice()` 라는 메서드를 사용하는 부분이다. 

이에 대해 알아보기 위해 간단한 투두 리스트를 만들어보았다. 여기에는 크게 현재 유저의 정보를 읽고 변경하는 기능, 그리고 여러 할일들을 기록하는 투두 기능이 들어있다. 이 두 가지 기능 모두 React Redux 기능을 이용하여 상태 관리를 한다. 따라서 각각의 Redux 상태 관리를 위한 `todoSlice` , `userSlice` js 파일들을 정의하였다. 

```jsx
import { createSlice } from "@reduxjs/toolkit";

/**
 * @typedef {Object} User
 * @property {String} username
 * @property {String} message
 */

const userSlice = createSlice({
  name: 'user',
  initialState: {
    username: 'anonymous',
    message: 'no message',
  },
  reducers: {
    /**
     * 
     * @param {User} state 
     * @param {Object} action 
     * @param {User} action.payload
     */
    inputUserInfo: (state, action) => {
      state.username = action.payload.username;
      state.message = action.payload.message;
    },
  },
  selectors: {
    selectUser: state => state,
  },
});

export const { inputUserInfo } = userSlice.actions;
export const { selectUser } = userSlice.selectors;
export default userSlice.reducer;

```

코드 3-1. userSlice.js

원래 Redux의 store 내에는 하나의 root state가 있다. 각 기능별로 다양한 형태의 상태들을 저장하기 위해 이 state를 여러 “조각(slice)”으로 분리하여 사용할 수 있는데, 이러한 “조각”을 생성해주는 메서드가 `createSlice` 이다. 인자로는 하나의 객체 리터럴이 들어가며, 이 안에는 크게 다음의 프로퍼티들이 들어간다. 

- `name` : slice의 이름. `configureStore()` 의 인자 내 `reducer` 의 값에 객체 리터럴 형태로 여러 개의 리듀서들을 등록할 수 있는데, 여기서 key값이 되기도 한다.
- `initialState` : 해당 slice의 초기 state 값.
- `reducers` : 여러 리듀서들을 정의하는 곳. 객체 리터럴 안에 리듀서 함수들을 정의하는 형식이다.
- `selectors` : selector 함수들을 정의하는 곳. 필수는 아니다.

위 코드 3-1의 마지막의 export 부분에서 볼 수 있듯,  `createSlice` 메서드로 인해 생성된 slice 객체에는 action, selector, reducer들이 자동으로 생성되어 이들을 각각 별도로 export 할 수도 있다. action, action creator는 자동으로 생성되기에 개발자가 이들을 일일히 정의할 필요가 없으며, selector, reducer, initialState 등을 모두 하나의 객체 내부에 정의할 수 있어 별도의 모듈을 생성할 필요가 없다는 것이 장점이다. 

action의 경우 type 이름은 slice의 `name` 값과 `reducers` 에 정의된 리듀서 함수명이 `name/reducer` 형태로 합쳐져 자동으로 생성된다고 한다. 위 코드 3-1의 경우 `{type: "user/inputUserInfo"}` 형태의 액션이 자동으로 생성된다. 마찬가지로 action creator의 경우에는 `reducers` 에 정의된 리듀서 함수명을 따와 자동으로 생성된다. 위 예제의 경우 컴포넌트 단에서 `dispatch(inputUserInfo({...}))` 와 같은 형태로 사용할 수 있게 된다. 

눈치 좋은 사람은 위 코드를 보며 이상한 점을 벌써 눈치챘을지도 모르곘지만, 리듀서 함수에서 새로운 state 참조를 반환하는 것이 아니라 기존 state 객체를 참조하여 그 안의 특정 값들을 변화시키는 구조를 띠고 있음을 볼 수 있다. 이는 분명 리듀서의 순수성 및 리덕스의 불변성과 어긋나는 것처럼 보인다. 하지만 RTK에서는 내부적으로 “immer”라는 라이브러리를 사용하여 가변적 코드를 불변적 코드로 바꿔준다고 한다. 그래서 개발자 입장에서는 상대적으로 조금 더 작성, 이해하기 쉬운 가변적 코드로 작성해도 결과적으로는 불변성을 잃지 않는다. 

위와 비슷한 형태로 이번에는 투두 리스트의 각 항목에 대한 CRUD를 리듀서로 구현한 슬라이스 모듈을 다음과 같이 정의하였다.

```jsx
import { createSlice } from "@reduxjs/toolkit";

/**
 * @typedef {Object} TodoItem
 * @property {Number} id - 투두 아이템의 고유 번호
 * @property {String} content - 투두에 보일 내용.
 * @property {Boolean} isCompleted - 투두 성취 여부
 */

const todoSlice = createSlice({
  name: 'todo',
  initialState: {
    /**
     * @param {Array<TodoItem>} todoItems
     */
    todoItems: [],
  },
  reducers: {
    /**
     * 투두 리스트에 아이템 하나를 추가
     * 
     * @param {Object} state 
     * @param {Array<TodoItem>} state.todoItems
     * @param {Object} action 
     * @param {TodoItem} action.payload
     */
    addTodo: (state, action) => {
      state.todoItems.push(action.payload);
    },
    /**
     * 투두 리스트의 특정 아이템의 내용 변경
     * 
     * @param {Object} state 
     * @param {Array<TodoItem>} state.todoItems
     * @param {Object} action 
     * @param {TodoItem} action.payload
     */
    updateTodo: (state, action) => {
      const targetIdx = state.todoItems
        .findIndex(item => item.id === action.payload.id);
      state.todoItems[targetIdx] = action.payload;
    },
    /**
     * 특정 투두 아이템 하나를 삭제
     * 
     * @param {Object} state 
     * @param {Array<TodoItem>} state.todoItems
     * @param {Object} action 
     * @param {TodoItem} action.payload
     */
    deleteTodo: (state, action) => {
      const targetIdx = state.todoItems
        .findIndex(item => item.id === action.payload.id);
      state.todoItems.splice(targetIdx, 1);
    },
  },
  selectors: {
    selectTodo: state => state.todoItems,
  },
});

export const { addTodo, updateTodo, deleteTodo } = todoSlice.actions;
export const { selectTodo } = todoSlice.selectors;
export default todoSlice.reducer;

```

코드 3-2. todoSlice.js

이렇게 정의한 slice의 리듀서들을 `configureStore` 에 다음과 같이 등록한다. 

```jsx
import { configureStore } from "@reduxjs/toolkit";
import userReducer from './slices/userSlice';
import todoReducer from './slices/todoSlice';

const todoStore = configureStore({
  reducer: {
    user: userReducer,
    todo: todoReducer,
  },
});

export default todoStore;

```

코드 3-3. todoStore.js

`reducer` 프로퍼티의 값에 들어가는 객체 리터럴 내부의 key 이름은 앞서 만든 각각의 slice들의 `name` 값을 그대로 입력하면 된다. 앞선 `userSlice` 에서는 `name` 이 “user”였기에 위 코드 3-3에서도 `user: userReducer`와 같이 작성한 것이다. 그리고 필자가 헷갈렸던 부분이 있는데, reducer 함수를 등록해야 할 것을 실수로 slice 객체 자체를 등록했었던 적이 있었다. 이 경우 당연히 에러가 발생하므로 slice 객체가 아닌 slice 객체로부터 생성된 reducer 함수만을 등록해야함을 참고하자. 

위와 같이 store를 설정한 경우, selector를 별도로 정의하지 않았다면 `useSelector(state => state.user)` 코드로 유저 정보 객체를 가져오거나 `useSelector(state => state.todo.todoItems)` 와 같이 사용하여 각각의 state를 가져올 수 있다. 지금은 selector를 정의했기에 `useSelector(selectUser)` 와 같이 조금 더 간결하게 사용할 수 있다. 

여기까지 작성한 후에 컴포넌트 단에서 사용하는 방법은 앞서 소개한 기존의 React Redux 때와 똑같다. 다음은 한 가지 예시로 보이기 위해 투두 리스트를 보여주고 항목 하나를 추가하는 기능이 담긴 컴포넌트 코드이다. 

```jsx
import { useDispatch, useSelector } from "react-redux";
import { addTodo, selectTodo } from "../../redux/todo/slices/todoSlice";
import { getMaxIdOfTodoItems } from "../../utils/todoUtils";
import TodoItem from "./todo/TodoItem";

const TodoList = () => {
  
  /**
   * @type {Array<import("../../redux/todo/slices/todoSlice").TodoItem>}
   */
  const todoItems = useSelector(selectTodo);  // #1
  const dispatch = useDispatch();

  const handleNewItem = () => {
    const nextItemId = getMaxIdOfTodoItems(todoItems) + 1;
    
    // #2
    dispatch(addTodo({
      id: nextItemId,
      content: '',
      isCompleted: false
    }));
  };

  let itemsJsx = null;
  
  if ((todoItems === undefined || null) || todoItems.length === 0) {
    itemsJsx = <p>No Items</p>
  } else {
    itemsJsx = todoItems.map(data => <TodoItem key={data.id} todoItemData={data} />);
  }

  return (
    <div className="todolist">
      {/* 투두 아이템 추가 폼 */}
      <button onClick={handleNewItem}>새 항목 추가</button>

      {/*  전체 투두 아이템 목록 보이기 */}
      {itemsJsx}
    </div>
  );
};

export default TodoList;

```

코드 3-4. TodoList.jsx

위 코드의 #1 부분을 통해 전체 투두 리스트의 항목들을 전부 화면에 보이도록 할 수 있고, #2 부분을 통해 새로운 투두 항목을 추가하는 기능을 사용할 수 있는 것이다. 여기서 소개한 투두 리스트의 전체 소스 코드는 [Github Repo](https://github.com/JeroCaller/react-study/tree/master/react-redux-study)를 참조하면 되겠다. 

다음은 앞서 링크로 달아놓은 Github Repo의 소스 코드를 브라우저에 실행하여 투두 리스트 기능을 실행해보는 영상이다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-05-07/React-react-redux/2.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

자료 2-1. 투두 리스트를 브라우저에서 실행하는 모습

# `useState` VS `react-redux` - 상태 저장

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-05-07/React-react-redux/3.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

자료 3-1. `useState` 를 사용하여 지역 상태 관리하는 `no-redux-counter` 와 redux를 사용하여 전역 상태 관리하는 `with-redux-counter` 시연 및 비교 영상

`useState` 를 사용하여 상태를 지역적으로 사용, 관리할 경우 `react-router` 등을 이용하여 다른 페이지로 라우팅하여 갔다가 다시 돌아오는 경우 이전의 상태값이 저장되지 않는다. 반면 Redux를 사용하여 상태를 사용, 관리할 경우 다른 페이지로 이동했다가 다시 돌아와도 이전에 저장한 상태값이 그대로 보여지는 것을 볼 수 있다. `useState` 를 사용한 state는 하나의 컴포넌트 내에서만 사용 가능하고, Redux를 사용한 state는 애플리케이션 전역 수준의 store 공간에 저장되므로 다른 컴포넌트로 라우팅했다가 돌아와도 상태값이 그대로 유지되는 것이다. 

다만 브라우저에서 새로고침을 하면 둘 다 이전의 상태값들이 사라지므로 이를 방지하고자 한다면 브라우저의 localStorage를 이용하든가 아니면 백엔드 서버에 요청하여 DB에 저장하도록 하는 데이터 영속화 작업을 별도로 수행해야할 것이다. 

---

References

[1] Redux 한국어 공식 홈페이지

[Redux - 자바스크립트 앱을 위한 예측 가능한 상태 컨테이너. \| Redux](https://ko.redux.js.org/)

[2] React Redux 공식 홈페이지

[React Redux \| React Redux](https://react-redux.js.org/)

[3] [[React-Redux] 개념부터 기본 사용법 까지](https://velog.io/@s_soo100/Redux-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%A6%AC%EB%8D%95%EC%8A%A4-%EC%8A%A4%ED%84%B0%EB%94%941)

[4] Redux Toolkit 공식 홈페이지

[Redux Toolkit \| Redux Toolkit](https://redux-toolkit.js.org/)

[5] [Redux-toolkit으로 상태관리하기!](https://velog.io/@mael1657/Redux-toolkit%EC%9C%BC%EB%A1%9C-%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0)

[6] 이고잉, “생활코딩! React 리액트 프로그래밍 개정판”, (위키북스, 2024)

[7] react 공식 문서 - useReducer

[useReducer – React](https://ko.react.dev/reference/react/useReducer)

[8] react 공식 문서 - reducer

[state 로직을 reducer로 작성하기 – React](https://ko.react.dev/learn/extracting-state-logic-into-a-reducer)

[9] 순수 함수

[컴포넌트를 순수하게 유지하기 – React](https://ko.react.dev/learn/keeping-components-pure)

[10] [왜 React와 Redux의 State는 불변(immutable) 일까?](https://mmsesang.tistory.com/entry/%EC%99%9C-React%EC%99%80-Redux%EC%9D%98-State%EB%8A%94-%EB%B6%88%EB%B3%80immutable-%EC%9D%BC%EA%B9%8C)

[11] ["Redux가 실제로 작동하는 방식과 의도하는 방식을 구별해야 한다"](https://velog.io/@tlsghwn/Redux%EC%9E%91%EC%84%B1%EC%A4%91)

[12] Boilerplate code 보일러플레이트 코드

[보일러플레이트 코드란?(Boilerplate code)](https://charlezz.medium.com/%EB%B3%B4%EC%9D%BC%EB%9F%AC%ED%94%8C%EB%A0%88%EC%9D%B4%ED%8A%B8-%EC%BD%94%EB%93%9C%EB%9E%80-boilerplate-code-83009a8d3297)