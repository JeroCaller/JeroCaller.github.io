---
title: "[React] React 기초"
category: "React"
tag: ["React", "Node.js", "npm", "npx"]
---

# React

React는 컴포넌트 기반의 사용자 인터페이스(UI)를 제작하기 위한 자바스크립트 기반 오픈 소스 라이브러리이다. 

실무에서는 프론트엔드 쪽은 이 리액트가 많이 쓰인다고 한다. 이렇게 인기 있는 이유가 무엇일까? 

react의 특징

- 사용자 인터페이스를 구성하는 코드를 Component 단위로 나눠 분리한 후, 이들을 조합하여 만드는 형식이다. 이는 객체 지향 프로그래밍에서 코드의 재사용성와 가독성, 유지보수성을 위해 전체를 이루는 어떠한 기능을 객체라는 작은 부품으로 나눈 후 이들을 결합하는 방식과 유사하다. 즉, 리액트에서 Component 기반의 코드를 작성하도록 지향하는 이유는 복잡한 현대 웹 개발에서 가독성 및 코드 재사용성을 확보하기 위함이다.
- React는 SPA(Single Page Application)을 지향하고 있어 이를 이용하여 SPA 구현 시 사용자 입장에서 페이지 깜빡임 현상이 없어 사용자 편의성을 증대시킬 수 있다.
- React에서는 원래의 DOM과는 별개로 Virtual DOM이라는 개념이 따로 있다. DOM에 변경사항이 생길 경우 DOM에서 직접 변경이 일어나는 것이 아니라, Virtual DOM과 DOM을 비교하여 변경 사항을 체크하는 Dirty Checking이 먼저 선행된 다음, 변경된 DOM 요소만 찾아 이를 업데이트한다. 이로 인해 만약 DOM만 있었다면 부분적 변경 사항임에도 DOM 전체가 리렌더링되어 사용자 입장에서 깜빡임 현상을 관측할 수 있겠으나 이러한 Virtual DOM의 존재로 인해 특히 SNS와 같이 컨텐츠 업데이트가 잦은 동적 웹에서 깜빡임 현상을 덜 관측할 수 있고, 렌더링 성능도 빠르다.
- 프레임워크가 아니라 라이브러리이다. 이러한 이유로 다른 프레임워크에 붙여 사용할 수 있는 유연성을 제공한다.
- 자바스크립트 코드 내부에서 HTML과 거의 똑같이 생긴 JSX라는 문법을 사용하여 UI 코드를 작성할 수 있다. 이로 인해 자바스크립트 내에서도 직관적인 HTML 구성 코드를 작성할 수 있다.
- 컴포넌트를 이용하여 사용자 정의 태그를 정의할 수 있어 좀 더 자유로운 UI 구성을 할 수 있다.

이 외에도 여러 특징들이 있다고 한다. 아무튼 이러한 이유로 인해 기존의 Vanila JS로 UI를 구성하는 방법에 비해 더 편리하게 UI를 구성할 수 있다. 

# React 설치하기

IDE는 Visual Studio Code를 사용하였다. vscode가 이미 설치되어 있다고 가정하고 react 설치 방법을 살펴보겠다. 

우선 Node.js를 설치해야한다. 다음의 사이트에서 LTS 버전으로 다운로드 받자. 참고로 LTS는 Long Term Service의 약자로, Node.js 측에서 오랜 기간동안 지원해주는 버전이기에 상대적으로 안정성이 있는 버전이라 할 수 있겠다. 

[Node.js — Run JavaScript Everywhere](https://nodejs.org/en)

리액트에서 사용되는 JSX와 같은 새로운 문법을 웹브라우저 엔진이 해석할 수 있게끔 중간에 번역해주는 Babel이라는 기술과, 리액트의 컴포넌트 기반 프로그래밍에 의해 여러 개의 파일들로 나눠 작성할 것인데, 이들을 하나로 결합하여 bundle을 생성해주는 Webpack이라는 도구가 필요한데, 이들이 Node.js 기반으로 제작되었기 때문에 React 사용을 위해 Node.js를 먼저 설치하는 것이다. 

Node.js 설치도 매우 간단하기에 여기서는 생략하겠다. 

설치 후 cmd 창에서 다음의 명령어를 입력하여 Node.js가 잘 설치되었는지 확인해보자.

```html
npm -v

npx -v
```

필자의 경우 해당 명령어 실행 시 다음과 같이 출력되었다.

```html
C:\> npm -v
10.2.4

C:\> npx -v
10.2.4

```

위와 같이 버전 번호가 나오면 Node.js가 잘 설치되었다는 뜻이다. 

참고로 npm은 Node Package Manager의 준말로, Node.js 에서의 패키지 관리자이다. Node.js 설치 시 같이 자동으로 설치된다. npm을 사용하면 Javascript 패키지 설치 및 관리를 할 수 있어 의존성 패키지 설치에 사용된다. 

npx는 npm 5.2.0 버전 이상에서 추가된 패키지 관리자로, npm 패키지를 실행할 수 있으며, 주로 제한된 영역에서 필요한 패키지만 설치하고자 할 때 사용되는 도구라고 한다. npm을 이용하여 의존성 패키지 설치 시 너무 무거운 용량의 파일들이 설치되는 단점이 있으나 npx를 사용하면 불필요한 파일들을 설치하지 않아 비교적 가벼운 장점이 있다고 한다. 

사실 위 과정을 통해 모든 설치 과정이 끝났다. 

## React 첫 프로젝트 폴더 생성해보기

IDE는 Visual Studio Code를 사용하였다. 터미널은 Window Powershell을 기준으로 하였다. 

먼저 워크스페이스 폴더를 정한다. 해당 폴더 내에 실질적인 프로젝트 폴더를 생성하여 React 작업을 할 것이다. 예를 들어 워크스페이스 경로를 `C:\Work\ReactStudyWorkSpace` 로 정했다고 해보자. 

사실 여기서 터미널 창을 이용하여 리액트 프로젝트 폴더를 생성하는 방법이 여러가지가 있는데, 권장되는 방식은 node.js 설치로 자동 설치된 npx를 이용하는 것이다. 터미널 창에서 다음의 명령어를 입력하면 원하는 프로젝트 폴더를 생성할 수 있다.

```powershell
npx create-react-app 폴더명
```

예를 들어 필자는 `npx create-react-app test-react` 라는 명령어를 입력하였다. 그러면 다음과 같이 설치 관련 메시지들이 뜨면서 결국에는 성공적으로 프로젝트 폴더가 생성되는 것을 볼 수 있다. 

![사진 1-1. npx create-react-app 명령어를 입력하여 프로젝트 폴더를 생성하는 과정에서 설치 관련 메시지가 뜨고 있다. ](/images/2024-11-14/React-basic/1.png)

사진 1-1. npx create-react-app 명령어를 입력하여 프로젝트 폴더를 생성하는 과정에서 설치 관련 메시지가 뜨고 있다. 

![사진 1-2. 프로젝트 생성 완료 후의 모습. 생성 완료의 메시지와 더불어 test-react라는 새 프로젝트 폴더가 생성되었음을 알 수 있다.](/images/2024-11-14/React-basic/2.png)

사진 1-2. 프로젝트 생성 완료 후의 모습. 생성 완료의 메시지와 더불어 test-react라는 새 프로젝트 폴더가 생성되었음을 알 수 있다.

이로서 프로젝트 폴더 생성을 완료하였다. 매우 간단한 것을 알 수 있다. 

참고로, npm 대신 npx를 사용하면 의존성 라이브러리 설치 시 불필요하거나 중복되는 라이브러리는 제외하고 설치하기에 더 가볍다고 한다. 그래서 npx 명령어를 사용하여 프로젝트 폴더를 생성하는 게 권장된다고 한다. 

# React 살펴보기

처음 리액트 프로젝트 폴더 생성 시 내부를 보면 다음과 같이 구성되어 있다. 

![화면 캡처 2024-11-14 162916.png](/images/2024-11-14/React-basic/3.png)

크게 node_modules, public, src 폴더로 구성되어 있는데, 실제 개발할 때에는 주로 src 폴더 내에서 진행된다고 한다. 

src에서 주목할만한 파일에는 다음과 같다. 

- App.js
- index.js

App.js에서 실질적인 UI 구성 코드가 담겨져 있으며, 이 곳에서 실질적인 UI 관련 코딩을 진행하면 된다. 그러면 index.js에서 App.js 내에 정의된 App이란 컴포넌트를 가져와 실행하는 구조이다. 이렇게 실행된 결과가 public 폴더 내 index.html 페이지를 통해 화면을 구성하는 구조인 것이다. 

![그림 1. react 프로젝트 폴더의 기본 구조.](/images/2024-11-14/React-basic/react_basic_structure.drawio.png)

그림 1. react 프로젝트 폴더의 기본 구조.

터미널 창에서 `npm start` 라 치면 개발용 로컬 서버가 실행된다. 웹 브라우저에서 이를 살펴보면 다음과 같을 것이다. 

![화면 캡처 2024-11-14 163301.png](/images/2024-11-14/React-basic/4.png)

![스크린샷 2024-11-14 163306.png](/images/2024-11-14/React-basic/5.png)

실제로 개발 시에는 index.js와 index.html은 건드리는 일이 잘 없다고 한다. 

실제 개발 시에는 주로 App.js에서 작업하며, 만약 코드의 길이가 길어지거나 컴포넌트의 양이 많아질 경우 src 폴더 내 하위 폴더를 만들어 그 안에 새로운 js 파일을 만들어 작성하는 식으로 진행된다. css의 경우에도 App.css 파일을 이용하거나 별도의 css 하위 폴더 및 css 파일을 생성하여 사용하면 되겠다. 

즉, public은 개발 시 거의 건드리지 않으며, src 폴더 내에서도 주로 App.js, App.css 또는 별도의 하위 폴더를 만들어 사용한다고 한다. 

![그림 2. 여러 컴포넌트들 및 CSS 파일들을 생성해야할 때의 구조 예상도. 프로젝트 규모가 더 복잡해지면 하위 폴더 내에서도 또 다른 하위 폴더들이 카테고리 별로 생성되어 관리할 수도 있겠다.](/images/2024-11-14/React-basic/react_folder_structure.drawio.png)

그림 2. 여러 컴포넌트들 및 CSS 파일들을 생성해야할 때의 구조 예상도. 프로젝트 규모가 더 복잡해지면 하위 폴더 내에서도 또 다른 하위 폴더들이 카테고리 별로 생성되어 관리할 수도 있겠다.

참고로 처음 프로젝트 생성 시 App.js 내 코드는 다음과 같이 되어 있다.

```jsx
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;

```

App.js

# react 프로젝트 폴더 용량 최대한 줄이기

React 프로젝트 폴더를 만들기만 하고 새로운 파일을 추가하거나 코드를 전혀 추가하지 않았는데도 해당 폴더의 기본적인 용량이 200MB에 육박한다. 폴더 구조도 그리 복잡해보이지 않고 파일들도 그리 많아보이지 않음에도 그에 비해 용량이 크다고 느껴졌다. 그런데 알고 보니 node_modules 폴더가 큰 용량을 잡아 먹고 있기 때문이었음을 알게 되었다. 그런데 이를 지우고 리액트를 로컬 서버에서 실행하려고 하면 실행되지 않는다. 해당 폴더가 리액트를 구동하는데 필요한 의존성들이 포함되어 있기 때문이다. 

```jsx
PS C:\Work\ReactStudyWorkSpace\ignore_node_modules_test> npm start

> first-react@0.1.0 start
> react-scripts start

'react-scripts'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는
배치 파일이 아닙니다.
```

위와 같이 node_modules 폴더가 없는 상태에서 로컬 서버를 실행하려고 하면 에러가 뜨면서 실행되지 않는다. 

실무에서도 리액트 프로젝트 폴더 내 node_modules 폴더가 용량을 많이 잡아먹기 때문에 github에 올릴 때에도 이 폴더만 제외하여 올린다고 한다. 그러면 이를 다시 다운받아 실행하려면 어떻게 해야할까? 

일단 터미널 창에서 프로젝트 폴더 위치로 이동한다. 그 후 다음의 명령어를 통해 package-lock.json을 삭제한다. (

```jsx
리눅스, MacOS 기준 명령어라 한다) rm -rf package-lock.json
Window PowerShell) Remove-Item package-lock.json

```

그 후 다음의 명령어로 “node_modules” 파일을 제거한다. 

```jsx
Window PowerShell) RD node_modules  // 삭제
RD node_modules -Recurse // 내부까지 모두 삭제
```

해당 폴더는 용량이 많기에 삭제하는데에 시간이 걸린다. 위와 같이 하면 다음과 같이 해당 폴더와 파일이 삭제되었음을 알 수 있다. 

![화면 캡처 2024-11-14 182453.png](/images/2024-11-14/React-basic/6.png)

이 상태에서는 node_modules가 없기에 `npm start` 를 해도 서버가 실행되지 않는다. 

사실 의존성은 package.json 파일 내에 기록되어 있다. 그렇기에 node_modules 폴더가 삭제되더라도 package.json 파일만 있다면 언제 어디서든 다시 node_modules라는 의존성을 추가할 수 있다. 

다시 node_modules 폴더를 생성하려면 해당 프로젝트 폴더 위치에서 터미널 창에 `npm install` 이라고만 치면 된다. 그러면 다음과 같이 다시 node_modules와 package-lock.json 파일이 생성된다.

![화면 캡처 2024-11-14 182837.png](/images/2024-11-14/React-basic/7.png)

이제 다시 `npm start` 명령어를 입력하여 로컬 서버를 시작해보자. 확인 결과 다음과 같이 잘 되는 것을 확인하였다.

![스크린샷 2024-11-14 182942.png](/images/2024-11-14/React-basic/8.png)

다시 정리하면 다음과 같다.

- 프로젝트 폴더 용량 줄이기

```jsx
window PowerShell 기준으로 다음의 명령어를 입력

Remove-Item package-lock.json  // package-lock.json 파일 삭제
RD node_modules -Recurse  // node_modules 폴더 삭제. -Recurse 옵션 포함 시 내부 모든 파일들도 싹 다 삭제
```

위 명령어들을 통해 삭제하면 된다. (사실 그냥 폴더에서 직접 삭제해도 된다)

- 삭제된 의존성 폴더 다시 추가하기

```jsx
Window PowerShell 기준

npm install
```

위 명령어 입력 후 조금만 기다리면 삭제되었던 node_modules 및 package-lock.json 파일들을 다시 설치할 수 있다. 이는 package.json 파일에 이미 리액트 프로젝트를 실행하는데에 필요한 의존성 정보들이 명시되어 있기 때문에 해당 파일만 보존되면 언제든지 node_modules 폴더를 삭제 및 재설치할 수 있다고 한다. 

이러한 방법을 이용하면 github에 프로젝트 폴더를 올리거나 다른 이와 공유하고자 할 때 좀 더 가벼운 용량으로 전달할 수 있으니 잘 활용해보자. 

---

References

[1] 에이콘아카데미 (강남) 강의

[2] 이고잉, “생활코딩! React 리액트 프로그래밍 개정판”, (위키북스, 2024)

[3] React 한국어 공식 사이트

[React](https://ko.react.dev/)

[4] [https://namu.wiki/w/React(%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC)](https://namu.wiki/w/React(%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC))

[5] node_modules 삭제 후 재설치 방법

[NPM :: node_modules 깔끔히 삭제 후 재설치](https://bornatnoon.tistory.com/entry/NPM-nodemodules-%EA%B9%94%EB%81%94%ED%9E%88-%EC%82%AD%EC%A0%9C-%ED%9B%84-%EC%9E%AC%EC%84%A4%EC%B9%98)

[6] window powershell 기초 명령어

[PowerShell : 기초 명령어 (복사, 삭제, 이동)](https://sacstory.tistory.com/entry/PowerShell-%EA%B8%B0%EC%B4%88-%EB%AA%85%EB%A0%B9%EC%96%B4-%EB%B3%B5%EC%82%AC-%EC%82%AD%EC%A0%9C-%EC%9D%B4%EB%8F%99#google_vignette)

[7] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife/QbpR/63)