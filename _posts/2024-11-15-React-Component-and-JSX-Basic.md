---
title: "[React] Component와 JSX 기초"
category: "React"
tag: ["React", "JSX", "Component"]
---

# JSX

리액트에서는 JSX (Javascript Syntax eXtension)라는 자바스크립트를 확장한 문법을 제공한다. 자바스크립트 내부에서 사용하는 문법이지만 생김새는 HTML 태그와 똑같이 생겼다. 리액트 프로젝트 폴더를 처음 생성할 때의 App.js에도 이를 볼 수 있다. 

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

예제 1-1. App.js

보다시피 HTML과 똑같이 생겼다. 하지만 엄연히 HTML가 아닌 자바스크립트의 확장 문법이다. 내부적으로 Babel이라는 도구를 이용하여 Vanila JS 코드로 변환되어 웹 브라우저가 해석될 수 있도록 변환된다고 한다. 

기존의 Vanila JS를 이용하면 HTML과 JS를 따로 구분하였고, JS에서 HTML를 DOM으로 접근하여 조작했었다. 

```jsx

function someFunc(data) {
  // ex1
	// HTML 요소 하나 지정하기 위한 문법이 너무 길다...
	document.querySelector("#container").addEventListener("click", event => {
			// ...
			// 최종 생성된 HTML 구조가 상상이 안간다. 비직관적인 구조.
			let ulElement = document.createElement('ul');
			ulElement.classList.add("good");
			
			let liElement = document.createElement("li");
			liElement.textContent = data
			ulElement.appendChild(liElement);
	});
	
	// ex2
	// ...
	// 문자열로 작성 시 HTML 문법에 맞게 작성했는지 미리 확인할 수 없다...
	document.querySelector("#display").innerHTML = `
		<ul class="wow">
			<li>apple</li>
			<li>banana...</li>
		</ul>
	`;
}
```

예제 1-2. 가상으로 작성해봤다.

하지만 위 예제에서도 볼 수 있듯, 기존의 Vanila JS로 DOM에 접근하는 방식은 그 코드 자체가 장황할 뿐더러, createElement 방식으로 DOM 객체를 생성하는 방식은 최종적으로 HTML 코드로 어떻게 구성되는지 직관적이지 않으며, 문자열로 작성한 방식은 HTML 문법에 맞게 작성했는지 미리 알 수 있는 방법이 없다. JSX는 이러한 단점을 극복한 문법이라 볼 수 있겠다. 

# Component

React에서는 주로 클래스나 함수 단위로 사용자 인터페이스 UI를 구성하는 코드를 작성하는 식으로 흘러간다. 최근에는 Hook이라는 기술의 등장으로 인해 클래스 단위보다는 함수 단위로 컴포넌트를 작성한다고 한다. 

사실 앞서 본 App.js의 `function App()` 도 하나의 컴포넌트이다. 그 안에 `<div className="App">` 부모 태그를 필두로 하는 JSX로 작성한 HTML 코드가 고스란히 존재하기 때문이다. 

사실 컴포넌트와 이를 사용함으로써 얻는 장점은 객체 지향 방식의 것과 비슷하다고 생각한다. 어떠한 기능을 여러 개의 더 이상 쪼갤 수 없는 최소 단위로 쪼개어 이를 객체를 이용하여 부품을 만들고, 이렇게 만들어진 부품들을 조립하여 하나의 애플리케이션을 만드는 것이 객체 지향의 특징이듯이, React에서의 컴포넌트도 이와 마찬가지로 UI를 구성하는 코드들을 최소 단위로 나눠 각각 컴포넌트화시킨 후, 이 부품들을 조립하여 하나의 웹 페이지를 구성하는 방식인 것이다. 

예를 들어 다음의 코드가 있다고 해보자.

```jsx
import { useState, useRef } from "react";
import "./App.css";
import Table from "./components/Table";
import InfoDisplayer from "./components/InfoDisplayer";
import Form from "./components/Form";

function App() {
  // 이런저런 코드가 있다고 가정...

  return (
    <div className="App">
      <form onClick={props.eventHandler} ref={props.formRef}>
        <p>
          코드 : <input type="number" required="required" />
        </p>
        <p>
          상품명 : <input type="text" required="required" />
        </p>
        가격 : <input type="number" required="required" />{" "}
        <button className="add">등록</button>{" "}
        <button className="remove">삭제</button>
      </form>
      <table border="1">
        <thead>
          <tr>
            <th>코드</th>
            <th>상품명</th>
            <th>가격</th>
          </tr>
        </thead>
        <tbody>
          {props.allData.map((oneRec, idx) => {
            return (
              <tr key={idx}>
                <td>{oneRec.code}</td>
                <td>{oneRec.name}</td>
                <td>{oneRec.price}</td>
              </tr>
            );
          })}
        </tbody>
      </table>
      <div className="info">
        <span>건수 : {props.allData.length}</span> | &nbsp;
        <span>가격총합 : {total}</span> | &nbsp;
        <span>평균 가격 : {average.toFixed(1)}</span>
      </div>
    </div>
  );
}

export default App;
```

예제 2-1. 

위 예제의 UI 구성 코드를 보면 어딘가 코드가 꽤 길다고 느껴질 것이다. 이럴 경우 세부 단위로 나눠 각각을 컴포넌트화 시킬 수 있다. 위 예제에서는 크게 form, table, `<div className="info">` 이렇게 세개의 단위로 나눌 수 있겠다. 다음은 이들을 각각 컴포넌트화 한 예제이다.

```jsx
const Form = (props) => {

    return (
        <form onClick={props.eventHandler} ref={props.formRef}>
            <p>코드 : <input type="number" required="required" /></p>
            <p>상품명 : <input type="text" required="required" /></p>
            가격 : <input type="number" required="required" />  {" "}
            <button className="add">등록</button> {" "} <button className="remove">삭제</button>
        </form>
    );
    
}

export default Form;
```

예제 2-2. Form.js

```jsx
const Table = (props) => {
  return (
    <table border="1">
      <thead>
        <tr>
          <th>코드</th>
          <th>상품명</th>
          <th>가격</th>
        </tr>
      </thead>
      <tbody>
        {props.allData.map((oneRec, idx) => {
          return (
            <tr key={idx}>
              <td>{oneRec.code}</td>
              <td>{oneRec.name}</td>
              <td>{oneRec.price}</td>
            </tr>
          );
        })}
      </tbody>
    </table>
  );
};

export default Table;
```

예제 2-3. Table.js

```jsx
import { calculator } from "../js/tools";

const InfoDisplayer = (props) => {

    let {total, average} = calculator(props.allData);

    return (
        <div className="info">
            <span>건수 : {props.allData.length}</span> | &nbsp;
            <span>가격총합 : {total}</span> | &nbsp;
            <span>평균 가격 : {average.toFixed(1)}</span>
        </div>
    );
}

export default InfoDisplayer;
```

예제 2-4. InfoDisplayer.js

그리고 App.js에서는 이 각각의 컴포넌트들을 다음과 같이 합칠 수 있다. 

```jsx
import {useState, useRef} from 'react';
import './App.css';
import Table from './components/Table';
import InfoDisplayer from './components/InfoDisplayer';
import Form from './components/Form';
import * as tools from './js/tools';

function App() {

  // 이런저런 코드가 있다고 가정...

  return (
    <div className="App">
      <Form formRef={formElement} eventHandler={addOrRemove}></Form>
      <Table allData={allRecords}></Table>
      <InfoDisplayer allData={allRecords} ></InfoDisplayer>
    </div>
  );
}

export default App;

```

예제 2-5. App.js

각각을 컴포넌트로 만든 후, 이들을 App.js에서 불러오는 방식으로 결합하여 사용해보니 코드가 한결 간결해진 것을 알 수 있다. 또한, 전체적인 구조도 한 눈에 파악할 수 있게 되었다. 객체 지향에서 어떤 최소한의 기능을 객체로 만들어 놓으면 나중에 다른 곳에서도 재사용할 수 있듯, 이렇게 컴포넌트로 만들면 나중에 다른 필요한 곳에서도 이미 만들어놓은 컴포넌트를 그대로 가져다 사용할 수 있게 된다. 이를 활용하면 유용한 컴포넌트들을 미리 작성하여 라이브러리 형식으로 배포할 수 있고, 다른 이들이 이미 만들어진 컴포넌트들을 재사용함으로써 개발 시간도 단축할 수 있다는 장점을 확보할 수 있다. 

위 예제 2-5를 다시 보면, 이전에 만든 컴포넌트를 마치 HTML의 사용자 정의 태그처럼 `<Form>` 과 같이 사용할 수 있음을 알 수 있다. 

## Component 관련 개념들

Component를 생성하는 방법에는 크게 클래스를 이용한 방식과 함수를 이용한 방식이 있다. 함수를 이용한 방식은 App.js와 같이 JSX로 작성한 HTML 코드를 return 키워드로 바로 반환하는 방식으로 작성한다. 만약 추가적인 작업이 필요하다면 return 문 이전에 코드를 작성할 수 있다. 다음과 같이 이벤트 핸들러 설정, state 값 설정 등의 복잡한 코드를 return 문 이전에 작성할 수도 있다. 

```jsx
import {useState, useRef} from 'react';
import './App.css';
import Table from './components/Table';
import InfoDisplayer from './components/InfoDisplayer';
import Form from './components/Form';
import * as tools from './js/tools';

function App() {

  let [allRecords, setAllRecords] = useState([]);

  let formElement = useRef(null);

  const addList = event => {
    let checkResult = tools.checkIfEmptyInAllInputs(formElement);
    if (checkResult) return;

    const [codeInput, nameInput, priceInput] = formElement.current;
    let inputRecord = {
      code: codeInput.value,
      name: nameInput.value,
      price: priceInput.value
    };
    setAllRecords((oldRecords) => [...oldRecords, inputRecord]);
    tools.clearAllInputs(formElement);
  }

  const removeLastOne = event => {
    setAllRecords((oldRecords) => {
      let records = [...oldRecords];
      records.pop();
      return records;
    });
  }

  /**
   * 
   * @param {Event} event 
   */
  const addOrRemove = event => {
    event.preventDefault();
    switch(event.target.className) {
      case 'add':
        addList(event);
        break;
      case 'remove':
        removeLastOne(event);
        break;
      default: 
        return;
    }
    
  }

  return (
    <div className="App">
      <Form formRef={formElement} eventHandler={addOrRemove}></Form>
      <Table allData={allRecords}></Table>
      <InfoDisplayer allData={allRecords} ></InfoDisplayer>
    </div>
  );
}

export default App;

```

예제 3-1. App.js

클래스의 경우 대게 다음의 형태로 작성할 수 있다. 아래 코드는 기본적으로 제공하는 App.js의 코드를 클래스 컴포넌트로 바꿔본 예제이다. 

```jsx
import logo from './logo.svg';
import './App.css';
import {Component} from 'react';

/*
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
}*/

class App extends Component {
  render() {
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
}

export default App;

```

예제 3-2. App.js

위 예제를 자세히 보면 클래스 방식의 컴포넌트 작성 시 다음의 규칙들이 있음을 알 수 있을 것이다. 

- 클래스의 경우 반드시 Component라는 클래스를 상속받아야 한다. 이는 `import {Component} from 'react'` 문을 통해 얻을 수 있다. 사실 `React.Component` 로 접근할 수도 있다.
- JSX로 작성한 HTML 코드를 반환하는 곳은 render() 메서드이다.
- 그 외 추가적인 기능 작성 시에는 추가적인 메서드를 정의하여 사용할 수 있다.

최근에는 클래스형 컴포넌트보다는 함수형 컴포넌트를 많이 사용한다고 한다. 다만 복잡한 기능 구현 시에는 클래스형 컴포넌트를 사용할 수도 있다고 한다. 

보통 하나의 모듈에 하나의 컴포넌트를 작성하는데, 이 때 해당 컴포넌트를 export 해줘야 한다. 또한, 컴포넌트가 되는 함수명은 무조건 대문자로 시작하는 PascalCase 표기법을 사용한다. 만약 소문자로 시작하게 하면 리액트에서는 컴포넌트로 인식하지 않아 오류가 발생하여 제대로 동작되지 않는다. 

![사진 1-1. 컴포넌트 이름을 소문자로 시작했을 때 발생할 수 있는 에러. 자세히 보면 “React component names must start with an uppercase letter.”라는 메시지를 볼 수 있다. ](/images/2024-11-15/React-Component-and-JSX-Basic/1.png)

사진 1-1. 컴포넌트 이름을 소문자로 시작했을 때 발생할 수 있는 에러. 자세히 보면 “React component names must start with an uppercase letter.”라는 메시지를 볼 수 있다. 

# JSX 규칙들

JSX 사용 시 여러 규칙들이 있다. 

- `class` 키워드 대신 `className` 키워드를 사용한다. JSX는 HTML처럼 생겼지만 엄연히 자바스크립트 문법이다. 그로 인해 기존에 자바스크립트에 존재하는 `class` 라는 키워드와 HTML에서 사용하는 class 속성 이름이 겹치는 문제가 발생한다. 따라서 JSX에서는 대신 className을 사용하도록 하고 있다. 예) `<div className="App">`
- HTML의 `<label>` 태그에 존재하는 속성명인 `for` 도 자바스크립트의 것과 겹치기에 대신 `<label htmlFor="...">` 와 같이 사용한다.
- 반드시 Root Element를 가져야한다. 다음의 코드는 모든 요소들을 포함하는 최상위 요소가 없다. 따라서 아래 코드는 잘못된 사용 예이다.
    
    ```jsx
    function App() {
    	// 잘못된 예
    	return (
    		<div className="App">
    			...
    		</div>
    		<ul>
    			<li>...</li>
    		</ul>
    	);
    }
    ```
    
    예제 4-1. 잘못된 사용 예
    
    따라서 다음과 같이 어떤 요소든 좋으니 모든 요소들을 포함하는 Root Element를 정의해야 한다.
    
    ```jsx
    function App() {
    	return (
    		<div className="App">
    			...
    			<ul>
    				<li>...</li>
    			</ul>
    		</div>
    	);
    }
    ```
    
    예제 4-2. 올바른 예
    
    심지어는 이름이 없는 태그를 Root Element로 사용해도 된다. 
    
    ```jsx
    import logo from './logo.svg';
    import './App.css';
    import {Component} from 'react';
    
    class App extends Component {
      render() {
        return (
          <>  {/* 이름 없는 태그를 Root Element로 사용 */}
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
          </>
        );
      }
    }
    
    export default App;
    
    ```
    
    예제 4-3. 
    
    이름 없는 태그는 실제 브라우저 상에서는 나타나지 않는다. 
    
    ![사진 2-1. 브라우저 상에서 해석된 HTML 코드를 보면 이름 없는 태그가 그 어디에도 보이지 않는다. ](/images/2024-11-15/React-Component-and-JSX-Basic/2.png)
    
    사진 2-1. 브라우저 상에서 해석된 HTML 코드를 보면 이름 없는 태그가 그 어디에도 보이지 않는다. 
    
- JSX 내부에 자바스크립트 코드를 작성할 일이 있을 경우, 중괄호({}) 작성 후 그 내부에 작성한다. 다음은 이를 활용한 예제이다.
    
    ```jsx
    import './App.css';
    import {useState} from 'react';
    
    function App() {
      const myName = "김큐엘";
    
      let myFavFoods = ["치킨", "소고기무국", "순대국", "삼겹살", "소고기"];
    
      return (
        <div className="App">
          <h3>안녕하세요, 제 이름은 {myName}입니다.</h3>
          <p>제 나이는 {21}살이며, 취미는 {"유튜브 보기"}입니다.</p>
          {/* 이렇게 JSX 내에서 주석을 달 수 있어요 */}
          
          <p>내가 좋아하는 음식</p>
          {/* 중괄호 내부는 HTML이 아닌 자바스크립트 문법이 
          들어올 수 있는 곳이다. 따라서 이를 활용하면 
          동적으로 HTML 요소들을 생성할 수 있다. */}
          <ul>
            {myFavFoods.map((food, idx) => {
              return (
                  <li key={idx}>{food}</li>
              );
            })}
          </ul>
        </div>
      );
    }
    
    export default App;
    
    ```
    
    예제 4-4.
    
    ![예제 4-4 실행결과](/images/2024-11-15/React-Component-and-JSX-Basic/3.png)
    
    예제 4-4 실행결과
    
    물론 JSX 주석도 Root Element 내부에 작성해야 한다. 그 바깥 부분은 엄연히 자바스크립트의 영역(JSX도 자바스크립트 문법이나 기존의 바닐라 자바스크립트와 구분하기 위해 일부러 “JSX”와 “자바스크립트”를 구별하여 부르도록 하겠다)이므로 자바스크립트의 주석을 사용하든가 해야 한다. 
    
- HTML의 특정 요소에 이벤트를 걸기 위해 해당 태그 속성으로 `onClick`, `onChange` 와 같이 작성하여 이벤트 핸들러를 걸면 된다. 주의할 점은 원래 HTML에서는 `onclick` 과 같이 대문자가 전혀 없는데, JSX에서는 `on` 바로 다음에 영어 대문자로 작성해야 한다. 즉, camelCase로 작성해야 한다. 한 편 이벤트 핸들러 함수는 JSX 바깥에서 작성해도 되고, JSX 내부에서 중괄호({}) 내부에 vanila JS 문법으로 이벤트 핸들러 함수를 작성해도 된다. 
참고로, HTML에서는 HTML 코드와 자바스크립트 코드를 분리하기 위해 HTML 태그 속성에 onclick과 같은 문법을 작성하지 않는 것을 권고하였는데, 지금의 경우는 자바스크립트 기반의 JSX에서 작업하고 있으므로 `onClick` 과 같은 문법을 사용해도 된다. 브라우저 상에서 렌더링 될 때는 `onClick` 과 같은 표시가 HTML 태그에 전혀 표시되지 않는다. 
다음은 `onClick` 과 같은 이벤트명과 그에 적절한 이벤트 핸들러를 매핑한 예제이다.

```jsx
import { useState } from "react";

const MyComp = () => {

    const [number, setNumber] = useState(0);
    
    /**
     * 
     * @param {Event} event 
     */
    const increase = event => {
        setNumber(number + 1);
    }

    return (
        <> 
            <button onClick={increase}>숫자 증가</button>
            <p>
                <span>현재 숫자: </span>
                <span>{number}</span>
            </p>
        </>
    );
}

export default MyComp;
```

예제 4-5. MyComp.js

<div style="text-align: center;">
    <img src="/images/2024-11-15/React-Component-and-JSX-Basic/4.png" alt="image" />
    <img src="/images/2024-11-15/React-Component-and-JSX-Basic/5.png" alt="image" />
</div>

- 자바스크립트의 for, map과 같은 기능을 이용하여 `<li>` , `<td>` 와 같이 반복적인 요소들을 return하여 출력하는 형태일 경우, 각각의 JSX 태그 요소의 `key` 속성에 고유한 값을 부여해줘야 한다. 다음의 예제 코드를 보겠다.

```jsx
import './App.css';
import {useState} from 'react';
import MyComp from './components/MyComp';

function App() {
  const myName = "김큐엘";

  let myFavFoods = ["치킨", "소고기무국", "순대국", "삼겹살", "소고기"];

  return (
    <div className="App">
      <h3>안녕하세요, 제 이름은 {myName}입니다.</h3>
      <p>제 나이는 {21}살이며, 취미는 {"유튜브 보기"}입니다.</p>
      {/* 이렇게 JSX 내에서 주석을 달 수 있어요 */}
      
      <p>내가 좋아하는 음식</p>
      {/* 중괄호 내부는 HTML이 아닌 자바스크립트 문법이 
      들어올 수 있는 곳이다. 따라서 이를 활용하면 
      동적으로 HTML 요소들을 생성할 수 있다. */}
      <ul>
        {myFavFoods.map((food, idx) => {
          return (
              <li>{food}</li>
          );
        })}
      </ul>
      <MyComp></MyComp>
    </div>
  );
}

export default App;

```

예제 4-6. App.js

위 예제에서의 `<ul>` 태그 내부에서 `myFavFoods` 배열 내 모든 요소들을 `<li>` 로 출력하기 위해 자바스크립트의 배열에서 제공하는 `map()` 함수를 사용하고 있다. 이를 화면에서 보면 다음과 같다. 

![화면 캡처 2024-11-19 122659.png](/images/2024-11-15/React-Component-and-JSX-Basic/6.png)

화면에 잘 출력되기는 하나 브라우저 콘솔창에서 경고창이 뜬다. 이는 반복을 통해 출력되는 각 `<li>`  요소에 “고유키”가 있어야 한다는 뜻이다. 다음과 같이 `key={idx}` 속성을 각 `<li>` 마다 부여하도록 추가해보았다.

```jsx
import './App.css';
import {useState} from 'react';
import MyComp from './components/MyComp';

function App() {
  const myName = "김큐엘";

  let myFavFoods = ["치킨", "소고기무국", "순대국", "삼겹살", "소고기"];

  return (
    <div className="App">
      <h3>안녕하세요, 제 이름은 {myName}입니다.</h3>
      <p>제 나이는 {21}살이며, 취미는 {"유튜브 보기"}입니다.</p>
      {/* 이렇게 JSX 내에서 주석을 달 수 있어요 */}
      
      <p>내가 좋아하는 음식</p>
      {/* 중괄호 내부는 HTML이 아닌 자바스크립트 문법이 
      들어올 수 있는 곳이다. 따라서 이를 활용하면 
      동적으로 HTML 요소들을 생성할 수 있다. */}
      <ul>
        {myFavFoods.map((food, idx) => {
          return (
              // key 속성 부여
              <li key={idx}>{food}</li>
          );
        })}
      </ul>
      <MyComp></MyComp>
    </div>
  );
}

export default App;

```

예제 4-7. App.js

![스크린샷 2024-11-19 122913.png](/images/2024-11-15/React-Component-and-JSX-Basic/7.png)

더 이상 브라우저 콘솔창에 경고창이 뜨지 않는 것을 볼 수 있다. 위 예제처럼 꼭 배열의 인덱스로 고유키를 줄 필요는 없고, 각 요소들을 구별할 수 있는 고유한 무언가이면 된다. 

- JSX 내부에서 중괄호 사용 시 중괄호 내에서는 if문을 사용할 수 없다. 중괄호는 표현식, 즉 어떠한 값이 나오게끔 하는 표현식만 들어갈 수 있기 때문에 반환값이 없는 if문은 사용할 수 없다. 단, 삼항 연산자를 대신 사용하면 된다. 다음은 조회된 데이터를 `<table>` 형태로 출력하되, 조회된 데이터가 없는 지를 삼항 연산자로 판단하고, 없으면 “조회된 결과가 없습니다” 식으로 출력하도록 한 예제이다.

```jsx

const TableDisplay = (props) => {

    return (
        <table border="1">
            <thead>
                <tr>
                    <th>사번</th>
                    <th>이름</th>
                    <th>부서명</th>
                    <th>직급</th>
                    <th>연봉</th>
                </tr>
            </thead>
            <tbody>
		            {/* 삼항 연산자 용*/}
                { (props.data.length !== 0) ? 
                    props.data.map((jikwon) => {
                        return (
                            <tr key={jikwon.jikwonno}>
                                <td>{jikwon.jikwonno}</td>
                                <td>{jikwon.jikwonname}</td>
                                <td>{jikwon.buserDto.busername}</td>
                                <td>{jikwon.jikwonjik}</td>
                                <td>{jikwon.jikwonpay}</td>
                            </tr>
                        );
                    }) : 
                    <tr>
                        <td colSpan={5}>조회된 데이터 없음</td>
                    </tr>
                }
            </tbody>
        </table>
    );
}

export default TableDisplay;
```

예제 4-8

<div style="text-align:center;">
    <img src="/images/2024-11-15/React-Component-and-JSX-Basic/8.png" alt="image" />
    <img src="/images/2024-11-15/React-Component-and-JSX-Basic/9.png" alt="image" />
</div>

---

References

[1] 에이콘아카데미 (강남) 강의

[2] [[React] 2. JSX란? (정의, 장점, 문법)](https://goddaehee.tistory.com/296)

[3] [JSX로 마크업 작성하기 – React](https://ko.react.dev/learn/writing-markup-with-jsx)

[4] [State and Lifecycle – React](https://ko.legacy.reactjs.org/docs/state-and-lifecycle.html)