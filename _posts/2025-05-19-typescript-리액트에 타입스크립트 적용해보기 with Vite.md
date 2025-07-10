---
title: "[Typescript] 리액트에 타입스크립트 적용해보기 with Vite"
category: "Typescript"
tag: ["Typescript", "React", "Vite", ".ts", ".d.ts"]
---


이번 글에서는 타입스크립트를 리액트에 적용해보는 과정에 대해 서술할 것이다. 이 글에서는 Vite를 이용하여 Typescript가 적용된 React 프로젝트를 생성하고 여기서 살펴볼 것이다. 

# React에 Typescript 적용하기 개요

## `@types` - 타입만 별도로 선언된 라이브러리

외부 라이브러리의 경우, 처음부터 타입스크립트를 적용한 라이브러리도 있겠지만, 자바스크립트로만 작성된 라이브러리가 많다. 아무래도 타입스크립트를 사용하지 않는 개발자들을 위해서 그런 것일 수 있다. 그래서 대부분의 라이브러리들은 타입만을 정의해놓은 타입스크립트 파일을 별도로 제공한다고 한다. 외부 라이브러리에서 제공하는 타입 선언까지 사용하려면 `@types` 이란 이름으로 시작하는 라이브러리를 별도로 추가하여 설치하면 된다. 

리액트도 마찬가지로 state, props 등 리액트에서 정의한 여러 개념들에도 타입을 별도로 제공한다. 이를 위해 현재 프로젝트 내에서 `@types/react` , `@types/react-dom` 라이브러리를 설치한다. 

```tsx
npm install @types/react @types/react-dom
```

코드 1-1. [리액트 공식 문서](https://ko.react.dev/learn/typescript)에서 발췌.

덕분에 우리는 리액트에 존재하는 각종 객체들에 일일히 직접 타입 선언을 하며 사용하지 않아도 편리하게 리액트 타입을 사용할 수 있다. 

다만 Vite를 이용하여 타입스크립트 및 리액트 프로젝트를 생성하는 경우 위 라이브러리들이 자동으로 설치되기에 개발자가 이에 대해 앞선 명령어를 입력할 필요는 없다. 

어떤 외부 라이브러리에서는 이러한 타입 라이브러리를 별도로 제공하지 않는 경우도 있을 수 있다고 한다. 그래서 이 경우엔 어쩔 수 없이 개발자가 직접 그에 맞게 타입 선언을 할 수밖에 없을 것이다. 

## 타입스크립트 + 리액트 컴포넌트 관련 사실

타입스크립트는 기본적으로 JSX를 지원한다고 한다. 타입스크립트가 적용된 리액트 컴포넌트 파일을 만들고자 한다면 파일 확장자명을 `.tsx` 로 해야 한다. 

# Vite로 React + Typescript 프로젝트 생성하기

먼저 다음과 같은 명령어를 입력한다.

```tsx
npm create vite@latest
```

코드 2-1.

그 후 다음 사진에서처럼 프로젝트명을 입력한다. 

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/1.png" alt="image">
</div>

사진 1-1. 프로젝트 이름까지 정하는 모습. 여기서는 프로젝트 생성 과정만 보이기에 임시로 프로젝트를 만드려고 하고 있다. 후에 보일 소스 코드는 `react-typescript` 라는 프로젝트에서 작성하였다. 

그 후는 다음의 사진 순서대로 따르면 된다.

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/2.png" alt="image">
</div>

사진 1-2. React 선택. 

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/3.png" alt="image">
</div>

사진 1-3. Typescript 선택

그러면 프로젝트가 생성된다. 프로젝트 폴더 내 구조는 다음과 같다.

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/4.png" alt="image">
</div>

사진 1-4. Vite로 생성한 React + Typescript 프로젝트 폴더 구조

순수 리액트 프로젝트와는 조금 다른 것을 알 수 있다. App 파일이 `.tsx` 확장자로 다르며, 타입스크립트 설정 파일인 tsconfig.json, tsconfig.app.json, tsconfig.node.json 및 vite-env.d.ts가 존재하는 것을 볼 수 있다. 

Vite를 이용하여 타입스크립트 + 리액트 프로젝트 생성 시 좋은 점은 타입스크립트 사용을 위한 설정 파일들 및 여러 설정들을 일일히 개발자가 직접 해줄 필요 없이 자동으로 설정되고, 심지어는 `npx tsc` 명령어를 개발자가 직접 사용하지 않아도 컴파일 되어 웹 화면에 반영된다는 점이다. 

## 주의사항

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/5.png" alt="image">
</div>

사진 2-1. 빨간 밑줄로 에러 표시가 뜨는 상황

만약 위 사진처럼 분명 타입을 제대로 선언, 표기했음에도 곳곳에 빨간 밑줄이 뜨며 에러가 발생한다면 다음의 조치를 고려해봐야한다. 다음과 같이 tsconfig.app.json과 tsconfig.node.json 파일 각각 내부에 있는 `moduleResolution` 속성값이 기본적으로 `bundler` 로 설정되어 있는 상태에서 `node` 로 바꿔 저장해본다. 

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/6.png" alt="image">
</div>

사진 2-2. tsconfig.app.json 파일 내 기본 설정들. 

<div style="text-align:center; margin:1em;">
<img src="/images/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/7.png" alt="image">
</div>

사진 2-3. `"moduleResolution"` 속성값을 `"node"` 로 바꾼 결과. 

그러면 빨간 밑줄의 에러 표시가 사라질 것이다.

# 타입스크립트 기반 리액트 프로그래밍

여기서부터는 타입스크립트를 기반으로 하여 리액트로 간단한 예시 웹 앱을 작성하는 과정을 살펴보고자 한다. 이를 통해 리액트 내 몇몇 개념들에 어떻게 타입을 지정하는지 간단하게 살펴볼 것이다. 이 글에서는 리액트에서의 타입스크립트에 대한 모든 개념을 다루지 않으므로, 리액트에서 제공하는 타입들과 그 사용법을 알고자 한다면 다음의 사이트를 참고해볼 수 있다. 

[React TypeScript Cheatsheets \| React TypeScript Cheatsheets](https://react-typescript-cheatsheet.netlify.app/)

원래는 공부용 예시 코드로 작성한 순수 리액트 프로젝트가 있었는데, 이를 그대로 복사해와 타입스크립트를 적용하였다. 이전의 순수 리액트 프로젝트 소스 코드는 다음을 참고. 

[순수 리액트로 작성한 간단한 예시 프로젝트 Github repo](https://github.com/JeroCaller/react-study/tree/master/input-and-form)

반면 아래에 보일 타입스크립트를 적용한 리액트 코드의 전체 소스 코드는 다음을 참고.

[타입스크립트가 적용된 리액트 예시 프로젝트 Github repo](https://github.com/JeroCaller/react-study/tree/master/react-typescript)

다음은 타입스크립트가 적용된 리액트 예시 웹 앱 시연 영상이다(타입스크립트 적용한다고 해서 웹 화면 상에서 시각적으로 바뀌는 것은 없다). 

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-05-19/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%20%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0%20with%20Vite/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

영상 1-1. 

프로그래밍을 하다보니 프로젝트 전역에 걸쳐서 공통적으로 사용되는 타입들이 일부 있었다. 그래서 필자는 src 폴더에 `types.ts` 라는 파일을 생성하여 다음과 같이 여러 타입들을 정의하였다. 

```tsx
import { JSX } from "react";

export interface UriInfo {
  /** 화면에 보일 요소명 */
  title: string;
  
  /** 라우팅 URI. 예) "/users" */
  uriPath: string;

  /** uri와 매핑되어 렌더링될 컴포넌트 */
  element: JSX.Element | null;
}

export interface UriInfoArrayProps {
  uriInfo: UriInfo[];
}

/** JSON의 직렬화된 문자열임을 강조하기 위함. */
export type JsonString = string;

```

코드 3-1. types.ts 

`<MyComponent />` 와 같은 컴포넌트 JSX는 `JSX.Element` 타입이다. 

> `.d.ts` VS `.ts`   
`.d.ts` 파일은 타입 정의를 위한 파일이다. `.ts` 파일에는 타입 선언 뿐만 아니라 실제 실행 가능한 자바스크립트 코드와 함께 존재하는 것이 대부분인 것과 조금 대조적이다. `.d.ts` 파일의 용도는 기존에 자바스크립트 코드가 있는데 이를 타입스크립트 프로젝트에서 사용해야 할 때 활용되는 파일이다. 타입스크립트 시스템 내에서 자바스크립트 코드만 있어 관련 타입이 정의되어 있지 않은 상태이면 타입스크립트 컴파일러는 해당 자바스크립트 파일 내 여러 변수, 함수들의 타입을 추론할 수 없게 된다. 이러한 컴파일러에게 타입을 미리 알려주어 타입 추론이 가능하게끔 타입 선언을 해두는 파일이 `.d.ts` 파일이다. 예를 들어 `myModule.js` 라는 파일이 기존에 있다면 `myModule.d.ts` 파일을 별도로 생성하여 `myModule.js` 모듈 내에 존재하는 모든 변수, 함수, 함수 인자들에 대한 타입들을 여기에 정의해둘 수 있다. 이러한 이유로 `.d.ts` 파일은 주로 순수 자바스크립트만 사용하고자 하는 이용자와 타입스크립트와 병행하여 사용하고자 하는 이용자 모두를 만족시킬 라이브러리를 제작할 때 사용된다고 한다. 아니면 기존에는 순수 자바스크립트(또는 순수 리액트)로 작성된 프로젝트를 타입스크립트로 전환하고자 할 때에도 활용될 수 있다.      
그러나 라이브러리 제작용이 아니며, 처음부터 타입스크립트를 적용하여 독립적인 웹 앱을 만드는 경우, 프로젝트 전반에 사용할 타입들을 모아 선언하기 위해  `.d.ts` 파일을 사용하는 것은 별로 좋은 방법이 아니라고 한다. 앞서 언급했듯 `.d.ts` 파일은 기존의 자바스크립트 파일을 타입스크립트 컴파일러가 해석할 수 있도록 별도로 제공하는 파일이며, 따라서 이미 자바스크립트 파일이 별도로 존재한다는 가정으로 개발된 것이 `.d.ts` 파일이라고 한다. 따라서 기존 자바스크립트 파일이 없이 `.d.ts` 파일만 있는 경우 런타임에서 에러가 발생할 수도 있다고 한다. 따라서 이 경우는 그저 `.ts` 파일로 만드는 것이 좋다고 한다. 필자가 위 코드 3-1에서 굳이 `.ts` 확장자 파일에 공통 타입들을 선언한 것도 이 이유에서이다. `.d.ts` 파일은 다른 사람에게 제품을 판매할 때 같이 제공하는 일종의 “사용설명서” 같은 느낌이라 보면 되겠다. 사실 이와 같이 `.d.ts` 파일의 무분별한 사용에 대한 경고를 알려주는 내용은 국내 자료에서는 거의 없었고 해외 영어권 자료에서만 찾아볼 수 있었기에 이렇게 글을 남긴다. 이와 관련된 자료 출처들은 다음을 참고하면 되겠다. 모두 영어권 자료이니 참고바란다.   
[https://www.youtube.com/watch?v=zu-EgnbmcLY](https://www.youtube.com/watch?v=zu-EgnbmcLY)   
[https://github.com/microsoft/TypeScript/issues/52593#issuecomment-1419505081](https://github.com/microsoft/TypeScript/issues/52593#issuecomment-1419505081)
> 

다음은 App.tsx이다.

```tsx
import './App.css';
import RouterSet from './components/RouterSet';
import SideBar from './components/Sidebar';
import ControlledCompEx from './pages/ControlledCompEx';
import FormDataEx from './pages/FormDataEx';
import InputValueEx from './pages/InputValueEx';
import MainPage from './pages/MainPage';
import UnControlledCompEx from './pages/UnControlledCompEx';

/*
타입을 가져오는 경우 `type` 키워드도 같이 사용하여 
타입을 가져오는 것임을 명시한다. 
이렇게 하지 않으면 브라우저 콘솔창에서 다음의 에러를 만나게 된다.
`Uncaught SyntaxError: The requested module '/src/types.ts' does not provide an export named 'UriInfo'`

현재 types.ts에는 타입만 정의되어 있는 상태이고, 실제 컴파일될 때에는 
타입 정의는 사라지고 자바스크립트 코드만 남기 때문에 
컴파일러는 타입만 정의되어 있는 types.ts에 아무것도 없는 상태라 인식한다. 
이 상태에서 무언가를 import 하는 시도가 진행되니 위와 같은 에러 메시지가 뜨는 것이다. 
따라서 import 하는 것이 타입임을 `import type` 과 같은 방식으로 명시해야 
해당 에러를 방지할 수 있다. 
*/
import type { UriInfo } from './types';

/**
 * `input-and-form` 리액트 프로젝트에 타입스크립트 적용 버전 프로젝트. 
 * 리액트에 타입스크립트를 어떻게 적용하는지 연습하기 위한 용도.
 * 
 * @returns 
 */
const App = () => {

  const uris: UriInfo[] = [
    {
      title: "메인",
      uriPath: "/",
      element: <MainPage />
    },
    {
      title: "UnControlled Component Ex",
      uriPath: "ex/uncontrolled",
      element: <UnControlledCompEx />
    },
    {
      title: "Controlled Component Ex",
      uriPath: "ex/controlled",
      element: <ControlledCompEx />
    },
    {
      title: "FormData Ex",
      uriPath: "ex/form-data",
      element: <FormDataEx />
    },
    {
      title: "input-value ex",
      uriPath: "ex/input-value",
      element: <InputValueEx />
    },
  ];

  return (
    <div>
      <p>타입스크립트를 적용한 간단한 리액트 웹 애플리케이션입니다.</p>
      <div className='app'>
        <SideBar uriInfo={uris} />
        <RouterSet uriInfo={uris} />
      </div>
    </div>
  );
}

export default App;

```

코드 3-2. App.tsx

위 코드에서 주석으로 이미 설명했듯, `types.ts` 파일에는 오로지 타입 선언만 있고 실행 가능한 자바스크립트 코드가 전혀 없어서 컴파일 후에 해당 파일에는 아무것도 남지 않게 된다. 이 상태에서 `App.tsx` 파일에서는 `types.ts` 파일에 정의된 `UriInfo` 를 import를 시도하면 `UriInfo` 가 `types.ts` 파일에 존재하지 않는다는 에러가 뜬다. 따라서 이를 방지하기 위해 `import type` 과 같이 `type` 키워드를 추가해줘야 한다. 

아무튼 `types.ts` 파일에 정의한 `UriInfo` 타입을 import하여 `uris` 변수에 적용한 것을 볼 수 있다. 다음은 위 코드에 보이는 `SideBar.tsx` 와 `RouterSet.tsx` 코드이다. 

```tsx
import { NavLink } from 'react-router';
import type { UriInfoArrayProps } from '../types';

/**
 * 사이드바 컴포넌트. 각 페이지들을 항목으로 하며, 사용자가 특정 항목 클릭 시 
 * 그에 해당하는 페이지로 라우팅됨.
 * 
 * React.FC 타입은 현재는 추천되지 않는 사용방법이라고 함. 
 * 대신 prop에 직접 타입을 표기해주는 게 좋다고 함. 이는 RouterSet.tsx를 참고.
 * 
 * @returns 
 */
const SideBar: React.FC<UriInfoArrayProps> = ( { uriInfo } ) => {

  if (uriInfo === null || undefined) {
    return <div>none</div>
  }

  return (
    <div className='sidebar'>
      <div className='sidebar-title'>
        <p>사이드바</p>
      </div>
      <ul>
        { uriInfo.map((data, idx) => <li key={idx}>
            <NavLink to={data.uriPath}>
              {data.title}
            </NavLink>
        </li>) }
      </ul>
    </div>
  );
};

export default SideBar;

```

코드 3-3. SideBar.tsx

```tsx
import { Routes, Route } from 'react-router';
import NotFound from '../pages/NotFound';
import type { UriInfoArrayProps } from '../types';

const RouterSet = ( { uriInfo }: UriInfoArrayProps ) => {

  return (
    <div className='router-set'>
      <Routes> 
        { uriInfo.map((data, idx) => <Route 
          key={idx} 
          path={data.uriPath} 
          element={data.element} 
        />) }

        <Route path="/*" element={<NotFound />} />
      </Routes>
    </div>
  );
};

export default RouterSet;

```

코드 3-4. RouterSet.tsx

위 두 모듈에서 공통적으로 볼 수 있는 부분은 바로 리액트의 props 매개변수에 대한 타입 표기법이다. `SideBar.tsx` 파일에서는 `React.FC<UriInfoArrayProps>` 와 같은 형태로 `{ uriInfo }` prop에 대한 타입을 표기하였다. 여기서 `FC` 는 Functional Component의 약자로, 즉, 이 함수의 반환값의 타입이 “함수형 컴포넌트”라는 것을 의미한다. 그러나 현재는 이러한 방식은 별로 추천되지 않는다고 한다. 대신 코드 3-4의 `RouterSet.tsx` 에서 보이는 방식을 권장한다고 한다. 

다음은 리액트에서의 타입스크립트 적용에 대해 많은 것을 살펴볼 수 있는 컴포넌트 모듈이다. 

```tsx
import { useState } from "react";

interface MessageStyle {
  [propName: string]: React.CSSProperties;
}

const ControlledCompEx = () => {

  /*
  const messageStyle: {
    error: React.CSSProperties,
    granted: React.CSSProperties
  } = {
    error: {
      color: 'red',
    },
    granted: {
      color: 'green',
    },
  };
  */
  
  const messageStyle: MessageStyle = {
    error: {
      color: 'red',
    },
    granted: {
      color: 'green',
    },
  };

  const [ username, setUsername ] = useState<string>('');
  const [ message, setMessage ] = useState<string>('');
  const [ messageCss, setMessageCss ] = useState<React.CSSProperties | undefined>(undefined);

  const handleInputChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    console.log(event.target.value);
    setUsername(event.target.value);
  };

  const handleSubmit = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();

    if (!username) {
      setMessage("아이디를 입력하셔야합니다.");
      setMessageCss(messageStyle.error);
      return;
    }

    if (username.length <= 5 || username.length >= 20) {
      setMessage("아이디는 최소 5글자, 최대 20글자 이내여야 합니다.");
      setMessageCss(messageStyle.error);
      return;
    }

    setMessage("사용 가능한 아이디입니다.");
    setMessageCss(messageStyle.granted);
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label htmlFor="userId">ID: </label>
        <input 
          type="text" 
          id="userId" 
          name="username" 
          value={username}
          onChange={handleInputChange}
        />
        <button type="submit">입력</button>
      </form>
      <p style={messageCss}>Message: {message}</p>
    </div>
  );
};

export default ControlledCompEx;

```

코드 3-5. ControlledCompEx.tsx

리액트에서는 굳이 CSS 파일을 사용하지 않더라도 자바스크립트 객체를 통해 스타일링을 적용할 수 있다는 것을 기억할 것이다. 이러한 스타일 적용을 위한 객체 타입은 `React.CSSProperties` 이다. 따라서 이를 적용한 `MessageStyle` 인터페이스를 정의하고, 이를 스타일을 위한 변수인 `MessageStyle` 에 표기하였다. 

`useState` 훅에서 사용할 state의 타입은 `useState<string>` 과 같이 제네릭을 이용하여 표기한다. 

한 편 이벤트 핸들러에서 사용되는 이벤트 객체는 리액트에서 따로 정의한 이벤트 타입들이 있어 이를 사용한다. `<input type="text">` 요소에서 사용되는 change 이벤트를 표기하기 위해 `React.ChangeEvent<HTMLInputElement>` 가 사용되었다. 이 때 구체적으로 어떤 요소에서 발생하는 이벤트인지 표기하기 위해 제네릭을 이용하여 `HTMLInputElement` 임을 명시하였다. 사실 여기까지 명시하지 않으면 `event.target` 에 `value`  속성이 있다는 것을 타입스크립트 컴파일러가 알지 못해 빨간 밑줄로 에러 표시가 난다. 

`<form>` 요소에서의 submit 이벤트 타입을 표기하기 위해 `React.FormEvent<HTMLFormElement>` 가 사용되었다. 

```tsx
import { useRef, useState } from "react";
import type { JsonString } from "../types";

interface UserResponse {
  username: string;
  message: string;
}

const FormDataEx = () => {

  // ! => non null assertion operator. 절대 null이 아니라는 것을 표기할 때 쓰임.
  // 따라서 formElement에 반드시 null이 아닌 무언가를 할당해야 함.
  const formElement = useRef<HTMLFormElement>(null!);
  const [ serverData, setServerData ] = useState<UserResponse | null>(null);

  const handleSubmitForm = (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();

    const formData: FormData = new FormData(formElement.current);
    const formDataJson: JsonString = JSON.stringify(Object.fromEntries(formData.entries()));

    // FormData 객체 내 key, value 모두 출력하기
    for (const [key, value] of formData) {
      console.log(`key: ${key}, value: ${value}`);
    }

    fetch('http://localhost:8080/users', {
      method: 'POST',
      body: formDataJson,
      headers: {
        'Content-Type': 'application/json;charset=utf-8',
      },
    })
    .then(response => {
      if (response.ok) {
        return response.json();
      }
    })
    .then(data => {
      setServerData(data);
    })
    .catch(error => {
      console.error("에러 발생");
      console.error(error);
    });
  };

  return (
    <div className="form-data-ex">
      <form onSubmit={handleSubmitForm} ref={formElement}>
        <ul>
          <li>
            <label htmlFor="username">username: </label>
            <input 
              type="text" 
              id="username" 
              name="username" 
            />
          </li>
          <li>
            <label htmlFor="message">message: </label>
            <input 
              type="text" 
              id="message" 
              name="message" 
            />
          </li>
        </ul>
        <button type="submit">제출</button>
      </form>
      <div className="box">
        <p>HTTP Response result</p>
        <div className="box message">
          { (serverData !== null) ? <>
            <p>username: {serverData.username}</p>
            <p>message: {serverData.message}</p>
          </> :  ''}
        </div>
      </div>
    </div>
  );
};

export default FormDataEx;

```

코드 3-6. FormDataEx.tsx

위 코드에서 주로 살펴볼만한 것은 DOM 요소에 접근하기 위한 용도의 `useRef` hook에 대한 타입 표기이다. 위 코드에서는 `<form>` 요소에 접근하므로 `useRef<HTMLFormElement>(null!)` 와 같이 제네릭으로 해당 요소가 `<form>` 요소임을 알려주고 있다. 

방금 `useRef` 호출 코드에서 인자로 `(null!)` 을 대입하면서 맨 뒤에 느낌표를 붙였는데, 만약 이를 안 붙이는 경우, `refElement.current` 와 같이 접근할 때 if 문 등을 이용하여 반드시 `current` 속성이 존재하는지 검사를 미리 하고 접근해야 한다. 만약 절대 null일 수 없고 반드시 요소가 ref에 할당되는 것이 확실하다면 non null assertion operator인 느낌표(!) 연산자를 뒤에 붙일 수 있다. 하지만 이 경우 타입 안전성을 살리는 방향이 아니기에 반드시 ref에 요소를 할당해야 하는 것을 까먹지 말아야 한다. 엄격한 타입 안정성을 위한다면 해당 연산자를 사용하는 대신 앞서 언급했듯 if 문 등을 이용하여 미리 null이 아닌지 체크하는 것이 더 좋을 것이다. 

# 글을 마치며…

사실 여기까지만 해도 리액트에서 타입스크립트를 사용하는 기본적인 것들은 거의 다루고 있어 타입스크립트의 기초만 안다면 많은 참고가 될거라 본다. 그럼에도 추가적인 정보가 필요하다면 직접 검색하거나 아니면 다음의 자료들을 참고해보면 되겠다. 

[리액트 공식 문서 - Typescript 사용하기](https://ko.react.dev/learn/typescript)

[React Typescript CheatSheets](https://react-typescript-cheatsheet.netlify.app/)

[React Redux에서 Typescript 사용 Quick Start](https://react-redux.js.org/tutorials/typescript-quick-start)

[React Redux에서 Typescript 사용](https://react-redux.js.org/using-react-redux/usage-with-typescript)

---

References

[1] 리액트 공식 문서 - 타입스크립트 사용하기

[TypeScript 사용하기 – React](https://ko.react.dev/learn/typescript)

[2] React Typescript Cheatsheets

[React TypeScript Cheatsheets \| React TypeScript Cheatsheets](https://react-typescript-cheatsheet.netlify.app/)

[3] [코딩에 진심인 사람을 위해 준비한 리액트 타입스크립트 \| 실제 회사에서 쓰는 레벨 ver](https://www.youtube.com/watch?v=V9XLst8UEtk)

[4] [8장. 리액트 프로젝트에서 타입스크립트 사용하기 · GitBook](https://react.vlpt.us/using-typescript/)

[5] [타입스크립트 핸드북](https://joshua1988.github.io/ts/)

[6] [TypeScript 한글 문서](https://typescript-kr.github.io/)

[7] .d.ts

[[TypeScript] .d.ts파일이 뭘까?](https://ssocoit.tistory.com/253)

[8] .d.ts

[타입 정의(d.ts) 파일이 뭔가요? \| 코드잇](https://www.codeit.kr/tutorials/90/%ED%83%80%EC%9E%85%20%EC%A0%95%EC%9D%98(d.ts)%20%ED%8C%8C%EC%9D%BC%EC%9D%B4%20%EB%AD%94%EA%B0%80%EC%9A%94%3F)

[9] .d.ts

[typescript에서 d.ts가 뭐야?](https://blog.naver.com/designerkhs/223656108804)

[10] 타입 정의를 `.d.ts` 파일에 함부로 넣지 말아야 하는 이유.

[Don't put your types in .d.ts files](https://www.youtube.com/watch?v=zu-EgnbmcLY)

[11] 타입 정의를 `.d.ts` 파일에 함부로 넣지 말아야 하는 이유.

[Module documentation tracking issue · Issue #52593 · microsoft/TypeScript](https://github.com/microsoft/TypeScript/issues/52593#issuecomment-1419505081)