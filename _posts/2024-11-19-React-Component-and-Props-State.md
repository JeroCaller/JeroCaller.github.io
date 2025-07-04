---
title: "[React] Component 및 Props와 State"
category: "React"
tag: ["React", "Component", "Props", "State"]
---

리액트에서는 클래스 또는 함수를 이용하여 UI를 구성하는 최소 단위인 컴포넌트를 만들 수 있으며, 최종적으로 App.js에서 이들을 import하여 HTML 형태의 JSX로 작성할 수 있다. 그런데, 아무래도 자바스크립트로 진행되는 것이다보니 상황에 따라서는 컴포넌트에 어떠한 값을 줘서 화면을 동적으로 꾸며야할 때가 있을 것이다. 부모 컴포넌트에서 자식 컴포넌트로 마치 HTML의 속성처럼 값을 넘기는 props가 있고, 컴포넌트 내부에서 값이 바뀌면 UI도 바뀌게 하도록 하는 state라는 것이 존재한다. 이 페이지에서는 이러한 props와 state에 대해 살펴보겠다.

# Props

다음은 Display라는 간단한 컴포넌트를 만들어 이를 이용하여 UI를 꾸미는 코드이다. 

```jsx
import './App.css';
import Display from './components/Display';

function App() {
  return (
    <div className="App">
      <Display></Display>
    </div>
  );
}

export default App;

```

예제 1-1. App.js

```jsx

const Display = (props) => {

    return (
        <div>
            <h1>hi</h1>
        </div>
    );
}

export default Display;
```

예제 1-2. Display.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/1.png" alt="image">
</div>

이 때, HTML에서 `<div class="app">` 과 같이 태그 내부에 속성을 줄 수 있는데, 리액트에서도 태그에 속성을 줄 수 있다. 

```jsx
import './App.css';
import Display from './components/Display';

function App() {
  return (
    <div className="App">
      <Display message="wow"></Display>
    </div>
  );
}

export default App;

```

예제 1-3. App.js

리액트에서는 `message` 와 같이 속성명을 개발자 마음대로 줄 수 있다. 

이 때, App이라는 부모 컴포넌트의 입장에서 자식 컴포넌트인 Display에서는 해당 속성값을 받아 화면에 출력할 수 있다. 자식 컴포넌트에서 다음과 같이 작성하면 출력할 수 있다. 

```jsx

const Display = (props) => {

    return (
        <div>
            <h1>hi</h1>
            <p>{props.message}</p>
        </div>
    );
}

export default Display;
```

예제 1-4. Display.js

이와 같이 부모 컴포넌트에서 자식 컴포넌트로 넘기는 데이터를 props라 한다. 자식 컴포넌트에서는 함수의 경우 그 매개변수로 props라 주면 되고, `props.message` 와 같이 부모 컴포넌트에서 명시한 속성값을 참조하면 해당 props 값을 자식 컴포넌트 내에서 가져올 수 있다. 

props 값은 자식 컴포넌트에서는 바꿀 수 없다. 다음은 부모로부터 넘어오는 props값을 자식에서 바꾸려고 하는 예제이다.

```jsx

const Display = (props) => {

    props.message = "안녕"; // props 값 변경 시도

    return (
        <div>
            <h1>hi</h1>
            <p>{props.message}</p>
        </div>
    );
}

export default Display;
```

예제 1-5. Display.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/2.png" alt="image">
</div>

위 사진과 같이 props 값을 바꾸려고 하면 에러가 뜨면서 바꿀 수 없고 오로지 읽기 전용으로만 사용할 수 있음을 알 수 있다. 

하지만 정말로 자식 컴포넌트에서 부모 컴포넌트의 값을 변경하는 방법이 아예 없는 건 아니다. 부모 컴포넌트에서 특정 값에 대해 `useState()` 함수를 호출하고 나온 setter 함수 자체를 자식 컴포넌트에 넘기는 방법이 있는데, 이에 대한 자세한 이야기는 이 글 마지막에서 다루겠다. 

부모 컴포넌트에서 자식 컴포넌트의 속성으로 넘기는 값들은 하나로 모여 JSON 객체로 넘겨진다. 다음은 이를 확인하기 위해 props 값을 자식 컴포넌트 매개변수에서 객체 구조 분해 할당 방식으로 받도록 한 예제이다. 

```jsx
import './App.css';
import Display from './components/Display';

function App() {
  return (
    <div className="App">
      <Display
       message="메시지"
       color="blue"
       title="속성값 전달 테스트"
      ></Display>
    </div>
  );
}

export default App;

```

예제 1-6. App.js

```jsx

// 객체 구조 분해 할당을 이용하여 인자를 받는다. 
const Display = ({color, message, title}) => {

    // 스타일도 줄 수 있다!
    let myStyle = {
        backgroundColor: color
    }

    return (
        <div style={myStyle}>
            <h1>{title}</h1>
            <p>{message}</p>
            <p>컬러</p>
        </div>
    );
}

export default Display;
```

예제 1-7. Display.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/3.png" alt="image">
</div>

즉, props는 부모로부터 넘어오는 여러 개의 속성값들이 하나의 JSON 객체의 프로퍼티로 묶여 넘겨지는 것을 알 수 있다. 사실 자식 컴포넌트의 매개변수 이름은 props라고 할 필요는 없다. 다만 관례적으로 props란 이름을 쓸 뿐이다. 

참고로 위 예제와 브라우저 화면 결과를 보면 알 수 있듯, CSS에서 스타일 지정을 위해 사용되는 여러 속성들을 그대로 가져와 객체 리터럴 방식으로 감싼 후 JSX에서 그 값을 style 속성으로 주입할 수 있음을 알 수 있다. 그래서 위 예제에서 배경색이 부모에서 넘긴 값인 파란색으로 색칠된 것을 알 수 있다. 단, 자바스크립트 상에서는 CSS 속성명을 `backgroundColor` 와 같이 camelCase 표기법으로 사용해야 한다. 

한 편, props의 값도 자바스크립트에서의 값이기에 JSX 내에서는 그냥 작성하지 않고 중괄호 ({}) 내부에 사용해야 한다. 즉, 다음과 같이 사용하면 props의 값을 출력할 수 없다. 

```jsx

// 객체 구조 분해 할당을 이용하여 인자를 받는다. 
const Display = (props) => {

    // 스타일도 줄 수 있다!
    let myStyle = {
        backgroundColor: props.color
    }

    return (
        <div style={myStyle}>
            <h1>props.title</h1>
            <p>props.message</p>
            <p>컬러</p>
        </div>
    );
}

export default Display;
```

예제 1-8. Display.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/4.png" alt="image">
</div>

위와 같이 props 내 값이 출력되지 않고 props 속성명 자체가 그대로 출력됨을 알 수 있다. 따라서 화면에 출력 시 꼭 중괄호로 감싸야 한다. 

한 편 부모측에서 여러 값들을 배열이나 객체 등 하나로 감싸 자식에게 전달할 수 있다. 

```jsx
import './App.css';
import Display from './components/Display';

function App() {

  let data = {
    message: "메시지",
    color: "green",
    title: "속성값 전달 테스트"
  };

  return (
    <div className="App">
      <Display
       data={data}
      ></Display>
    </div>
  );
}

export default App;

```

예제 1-9. App.js

```jsx

// 객체 구조 분해 할당을 이용하여 인자를 받는다. 
const Display = (props) => {

    // 스타일도 줄 수 있다!
    let myStyle = {
        backgroundColor: props.data.color
    }

    return (
        <div style={myStyle}>
            <h1>{props.data.title}</h1>
            <p>{props.data.message}</p>
            <p>컬러</p>
        </div>
    );
}

export default Display;
```

예제 1-10. Display.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/5.png" alt="image">
</div>

이러한 props를 이용하면 부모로부터 넘어온 props에 어떤 값이 넘어오느냐에 따라 자식 컴포넌트에서 동적으로 화면을 출력할 수 있다. 위 예제의 경우 title, message 등의 변수의 값만 바꾸면 출력 내용을 바꿀 수 있음을 알 수 있을 것이다. 

props의 값이 바뀌면 그 때마다 화면에서 리렌더링 된다고 한다. 단 React에서는 Virtual DOM을 사용하므로, 바뀐 값에 대해서만 부분적으로 DOM을 변경한다고 한다. 이로 인해 전체 DOM 트리를 재구성할 필요가 없어 사용자 입장에서 깜빡임 현상을 덜 경험할 수 있다고 한다. 

다음은 props의 값이 바뀔 때마다 그 값이 다시 리렌더링되어 사용자 화면에 보여지는 간단한 예제이다. 버튼을 누르면 숫자가 1씩 증가하는 구조이다. 

```jsx
import './App.css';
import Counter from './components/Counter';
import Display from './components/Display';
import {useState} from 'react';

function App() {

  let [count, setCount] = useState(0);

  let data = {
    message: "wow",
    color: "green",
    title: "오호라"
  };

  const increaseCount = event => {
    setCount((prevData) => count + 1);
  }

  return (
    <div className="App">
      <Display
       data={data}
      ></Display>
      <hr/>
      <Counter count={count}></Counter>
      <button onClick={increaseCount}>숫자 증가시키기</button>
    </div>
  );
}

export default App;

```

예제 1-11. App.js

```jsx

const Counter = (props) => {

    return (
        <div>
            <p>현재 숫자 {props.count}</p>
        </div>
    );
}

export default Counter;
```

예제 1-12. Counter.js

<div style="text-align: center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

# state

Props는 외부로부터 오는 값이라 볼 수 있다. 그렇다면 컴포넌트 내부에서도 변수를 사용하여 값을 사용할 수 있지 않을까? 다음은 일반적인 자바스크립트 방식으로 컴포넌트 내에 어떠한 값이 담긴 변수를 선언한 후, 사용자가 특정 요소를 클릭할 때마다 그 값이 바뀌도록 고안한 예제이다.

```jsx

const OwnCounter = () => {

    // 컴포넌트 내부에서만 사용할 값. 그런데...
    let counter = 0;

    const increaseLogic = event => {
        ++counter;
    }

    return (
        <div>
            <p>현재 카운트: {counter}</p>
            <button onClick={increaseLogic}>값 증가</button>
        </div>
    );
}

export default OwnCounter;
```

예제 2-1. OwnCounter.js

<div style="text-align:center; margin:1em;">
<img src="/images/2024-11-19/React-Component-and-Props-State/6.png" alt="image">
</div>

그런데 버튼을 누르면 값이 증가하지 않는다. 일반적인 값 대신 객체를 선언하여 그 객체의 프로퍼티 값을 변경하도록 하면 될까 싶어 다음과 같이 코드를 변경해보았다.

```jsx

const OwnCounter = () => {

    // 컴포넌트 내부에서만 사용할 값. 그런데...
    let counter = 0;
    let myCounter = {
        counter: 0
    }

    const increaseLogic = event => {
        ++counter;
        myCounter.counter += 1;
    }

    return (
        <div>
            <p>현재 카운트: {myCounter.counter}</p>
            <button onClick={increaseLogic}>값 증가</button>
        </div>
    );
}

export default OwnCounter;
```

예제 2-2. OwnCounter.js

그런데 위 예제도 버튼을 눌러도 값이 변경되지 않는다. 

React에서는 컴포넌트 내부에서 특정 값을 변경시켜 이를 화면에 리렌더링시키도록 하려면 `useState` 라는 함수를 사용해야 한다. 이 함수는 두 개의 값을 반환한다. 첫 번째 값은 `useState()` 함수의 인자로 넘긴 초기값 자체를, 두 번째 값은 이 값을 변경시킬 수 있는 일종의 setter 함수가 반환된다. 즉 다음과 같다. 

```jsx
const [myData, setMyData] = useState(0);  // myData 초기값 0으로 설정 => myData = 0
```

이 때 첫 번째 값을 state라 한다. 이 state의 값 변경은 useState 함수 호출의 두 번째 반환값인 setter 함수를 통해 변경하며, 이를 통해 state 값이 변경된다. 이 state 값이 변경될 때마다 화면에서 리렌더링되어 바뀐 값이 화면에 노출된다. 

다음은 앞선 예제를 state를 이용하여 바꾼 예제이다.

```jsx
import { useState } from 'react';

const OwnCounter = () => {

    // 컴포넌트 내부에서만 사용할 값. 그런데...
    /*
    let counter = 0;
    let myCounter = {
        counter: 0
    }

    const increaseLogic = event => {
        ++counter;
        myCounter.counter += 1;
    }*/

    // counter => 이 컴포넌트 내에서 사용할 state
    let [counter, setCounter] = useState(0);

    const increaseLogic = event => {
        setCounter((prevData) => prevData + 1);
        // setCounter(counter + 1);
    }

    return (
        <div>
            <p>현재 카운트: {counter}</p>
            <button onClick={increaseLogic}>값 증가</button>
        </div>
    );
}

export default OwnCounter;
```

예제 2-3. OwnCounter.js

```jsx
import './App.css';
import Counter from './components/Counter';
import Display from './components/Display';
import {useState} from 'react';
import OwnCounter from './components/OwnCounter';

function App() {

  let [count, setCount] = useState(0);

  let data = {
    message: "wow",
    color: "green",
    title: "오호라"
  };

  const increaseCount = event => {
    setCount((prevData) => count + 1);
  }

  return (
    <div className="App">
      <Display
       data={data}
      ></Display>
      <hr/>
      <Counter count={count}></Counter>
      <button onClick={increaseCount}>숫자 증가시키기</button>

      <hr/>
      <OwnCounter></OwnCounter>  {/* 추가 */}
    </div>
  );
}

export default App;

```

예제 2-4. App.js

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/2.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

이처럼 컴포넌트 내에서만 사용되는 값인 state는 `useState` 라는 함수를 사용하여 생성하고,  해당 함수 호출로 반환된 setter 함수를 호출하여 state 값을 변경할 수 있으며, 이렇게 변경된 state 값에 대해, 화면 상에서는 리렌더링이 일어나 변경된 값이 그대로 화면에 반영된다는 것을 알 수 있다. 

한 편 `useState()` 호출로 반환된 setter 함수 사용 시 인자로 다음과 같이 사용할 수 있다.

```jsx
const [data, setData] = useState(0);

사용법 1
setData(data + 1);  // 직접 새로운 값을 대입

사용법 2

setData( (prevData) => prevData + 1 ); // 화살표 함수를 이용.
// 인자에는 기존 state 값이 담겨져 있음. 따라서 
// 이를 이용하여 함수 내부에 새로운 값을 할당하는 로직을 작성하여 새로운 
// 값을 리턴하게 하면 됨.
```

# state 사용 주의 사항

만약 두 개의 state를 사용하고, 두 state가 서로 연관이 있을 때 setter를 이용하여 새 값 할당 시 주의해야할 점이 있다. 

기존의 카운터 state와 더불어, 현재 카운터 값에 10배를 하는 새로운 state인 times를 선언하고, 카운터 값이 바뀔 때마다 바뀐 값에 10배를 한 값을 표시해보도록 하겠다. 

```jsx
import { useState } from 'react';

const OwnCounter = () => {

    // counter => 이 컴포넌트 내에서 사용할 state
    let [counter, setCounter] = useState(0);
    
    // 새로 생성.
    let [times, setTimes] = useState(0);

    const increaseLogic = event => {
        setCounter(counter + 1);
        setTimes(counter * 10);
    }

    return (
        <div>
            <p>현재 카운트: {counter}</p>
            <p>열 배수: {times}</p>
            <button onClick={increaseLogic}>값 증가</button>
        </div>
    );
}

export default OwnCounter;
```

예제 2-5. 

위 코드의 `increaseLogic` 이란 이벤트 핸들러 함수 내부에서는 사용자가 버튼을 누르면 먼저 `setCounter` 함수를 호출하여 `counter` 값이 1 증가시키도록 한 다음, 이 증가된 `counter` 값을 토대로 `setTimes` 함수를 호출하여 `times` 라는 state의 값도 새로운 값으로 변경하려고 하고 있다. 다음은 그 결과이다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/3.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

그런데 한 가지 이상한 것은, `times` state 값은 항상 이전 `counter` 값의 10배를 한 값으로만 변경된다는 것이다. “현재 카운트”가 4일 때 원래대로라면 “열 배수” 에서는 4에 10을 곱한 값인 40이 나와야 하는데, 이전 카운터의 값인 3에 대한 10배의 값이 출력되고 있음을 알 수 있다. 왜 이러한 일이 나타나는걸까? 

state라는 개념은 일반적으로 자바스크립트에서 볼 수 있는 변수와는 다른 개념이다. 변수의 경우 다음의 코드와 같이

```jsx
let count = 0;

count += 1; // count: 1
count += 1; // count: 2

console.log(count); // 2
```

예제 2-6.

코드가 위에서 아래로 실행됨에 따라 같은 변수에 대한 값 변경 코드를 만날 때마다 해당 변수의 값을 계속 변경하는 구조이다. 

반면, state는 변수와는 달리, react 자체에서 관리하는 개념으로, 코드 상의 순서와는 관계가 없다. 

```jsx
const [count, setCount] = useState(0);

setCount(count + 1);  // setCount(0 + 1);
setCount(count + 1);  // setCount(0 + 1);

console.log(count);  // 1
```

예제 2-7.

state의 값 변경은 코드 상에서 setter 함수를 만날 때마다 계속 변경되는 구조가 아니라, 다음에 다시 렌더링될 때 딱 한 번 변경되는 구조인 것이다. 일종의 “사진”과도 같다. 즉, 마치 컴포넌트 구성 코드를 사진으로 찍은 것처럼 코드 상의 모든 동일한 state값들은 setter 함수를 여러 번 만나더라도 모두 동일한 값을 가진 상태로 고정되어 있다. 그렇기에 위 예제에서는 코드 상에 존재하는 모든 count 변수에는 그 어떤 코드를 만나더라도 모두 0인 상태인 것이다. 이 state 값은 다음 렌더링이 될 때서야 그 값이 변경되어 변경된 값이 화면에 반영되는 구조인 것이다. 

위 코드의 경우, 처음 count가 0일 때, 컴포넌트 내 모든 count라는 state들은 0이었다가 setter 함수에 의해 다음 렌더링에서는 1이란 값으로 변경될 준비를 하는 것이다. 그 후, 다음에 리렌더링되면 이전 렌더링 때의 setter 함수 호출에 의해 새로이 변경된 state값이 반영되어 화면 상에서는 2가 아닌 1이 보이는 것이다. 

즉, state 값의 변경 순서는 컴포넌트 내 코드 순서와 무관하고, 렌더링 순서와 관계가 있다고 볼 수 있다. 

앞선 위 예제에서도 맨 처음 counter라는 state 값이 0이었을 때…

```jsx
setCounter(counter + 1);  // setCounter(0 + 1); => next: 1
setTimes(counter * 10); // setCounter(0 * 10); => next: 0
```

예제 2-8

위와 같이 진행되므로 당연히 counter의 값과 times의 값이 서로 제대로 매핑되지 않을 수밖에 없는 것이다. 

그렇다면 같은 counter라는 state값을 이용하여 counter라는 state와 times라는 state값이 서로 연동되게 하는 방법은 없을까? 즉, counter가 1일 때 times도 10이 되도록 할 수는 없을까? 이럴 때 setter 함수 인자에 값을 직접 넣는 것이 아니라 새로운 값을 반환하는 콜백함수를 사용하는 것이다. 앞선 예제 2-5를 다음과 같이 변경하였다.

```jsx
import { useState } from 'react';

const OwnCounter = () => {

    // counter => 이 컴포넌트 내에서 사용할 state
    let [counter, setCounter] = useState(0);
    
    let [times, setTimes] = useState(0);

    const increaseLogic = event => {
        /*
        setCounter(counter + 1);
        setTimes(counter * 10);
        */
        
        setCounter((prevData) => {
            const newData = prevData + 1;
            setTimes(newData * 10);
            return newData;
        });
        
    }

    return (
        <div>
            <p>현재 카운트: {counter}</p>
            <p>열 배수: {times}</p>
            <button onClick={increaseLogic}>값 증가</button>
        </div>
    );
}

export default OwnCounter;
```

예제 2-9

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/4.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

이번에는 두 state 값이 제대로 매핑된 것을 알 수 있다! 

즉, 어떻게 보면 state의 setter 함수 호출은 비동기적으로 수행된다고도 볼 수 있겠다. 그래서 이전 예제에서는 setCounter() 함수 호출과 setTimes() 함수 호출이 비동기적으로 진행되었기에 서로 제대로 매핑된 결과를 볼 수 없었던 것이다. 그런데 위 예제에서는 콜백 함수를 setter 함수에 전달함으로써, counter라는 state가 먼저 그 값이 변경된 다음, 그 후에 times라는 값이 변경되도록 하는 순서를 보장해주는 동기적 방식으로 흘러감을 알 수 있다. 

- state에 객체형 데이터가 담겨져 있고, 이 state를 set 함수를 이용하여 객체 내 특정 값 변경 시, 이전 객체 참조를 통해 특정 객체 프로퍼티를 변경하는 방식이 아니라, 아예 새로운 객체 참조를 대입해야 state가 업데이트 된다.

```jsx
import { useState } from 'react';

const List = () => {

    const [favFoods, setfavFoods ] = useState([
        "햄버거", "소고기무국", "삼겹살"
    ]);

    const changeFoodHandler = () => {
        //setfavFoods((prevData) => prevData[0] = "피자");  // 에러
        
        // 작동 안됨
        setfavFoods((prevData) => {
            prevData[0] = "피자";
            return prevData;
        });

    }    

    return (
        <div>
            <h1>내가 좋아하는 음식들</h1>
            <ul>
                {favFoods.map((food, idx) => (
                    <li key={idx}>{food}</li>
                ))}
            </ul>
            <button onClick={changeFoodHandler}>상태 변경하기</button>
        </div>
    );
}

export default List;
```

예제 2-10.

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/5.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

위 예제에서는 setfavFoods 함수 호출 시 이전 데이터인 prevData 참조 변수를 그대로 이용하여 특정 배열 값을 변경하려고 시도하고 있다. 그러나 실제로는 배열값이 변경되지 않음을 알 수 있다. 

다음 코드로 변경하면 잘 실행된다.

```jsx
import { useState } from 'react';

const List = () => {

    const [favFoods, setfavFoods ] = useState([
        "햄버거", "소고기무국", "삼겹살"
    ]);

    const changeFoodHandler = () => {
        //setfavFoods((prevData) => prevData[0] = "피자");  // 에러
        
        
        // 작동 안됨
        /*
        setfavFoods((prevData) => {
            prevData[0] = "피자";
            return prevData;
        });*/

        // 작동 잘 됨
        setfavFoods((prevData) => {
            let newData = [...prevData];  // 깊은 복사
            newData[0] = "피자";
            return newData;
        });
    }    

    return (
        <div>
            <h1>내가 좋아하는 음식들</h1>
            <ul>
                {favFoods.map((food, idx) => (
                    <li key={idx}>{food}</li>
                ))}
            </ul>
            <button onClick={changeFoodHandler}>상태 변경하기</button>
        </div>
    );
}

export default List;
```

예제 2-11.

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-19/React-Component-and-Props-State/6.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

위 예제에서는 기존 state의 객체 참조 변수인 prevData를 전개 연산자(…)를 이용하여 전혀 새로운 변수 newData에 “깊은 복사”를 하고 있다. 그 후, newData에서 특정 배열값을 변경하고 있다. 즉, prevData라는 변수가 가리키고 있는 배열 객체와 newData가 가리키는 배열 객체는 서로 다른 객체이다. 이렇게 전혀 다른 객체를 set 함수 인자의 콜백 함수 내에서 반환하도록 설계해야만 state의 객체 내 특정 프로퍼티의 값이 변경될 수 있다. 

# 자식 컴포넌트에서 부모 컴포넌트의 state 값 변경하기

props를 살펴볼 때, 자식 컴포넌트에서는 props의 값을 변경할 수 없음을 살펴보았다. 그러나 자식 컴포넌트에서 부모 컴포넌트의 state값을 변경하는 방법이 있다. 바로 부모 컴포넌트에 존재하는 state의 setter 함수 참조 자체를 자식 컴포넌트의 prop으로 넘긴 후, 자식 컴포넌트에서 setter 함수를 호출하는 것이다. 

```jsx
import { useState } from 'react';
import Child from './Child';

const Parent = () => {

    const [colorSet, setColorSet] = useState({
        backgroundColor: 'black',
        color: 'white'
    });

    return (
        <div style={colorSet}>
            <h1>부모입니다.</h1>
            <Child styleSetter={setColorSet} />
        </div>
    );
}

export default Parent;
```

예제 3-1. Parent.js

```jsx
import { useRef } from "react";

const Child = (props) => {
  // JSX로 작성한 HTML 태그를 DOM으로
  // 접근 가능한 Hook.
  // document.querySelector() 와 같은 효과.
  // useRef() 함수의 반환값은 항상 current 프로퍼티를 가지고 있어
  // 그 안에 current.id 와 같이 요소의 여러 속성들을 가지고 있음.
  const spanElement = useRef(null);

  const changeStyleHandler = (event) => {
    const toggleVars = {
      on: {
        name: "bw",
        style: {
          backgroundColor: "black",
          color: "white",
        },
      },
      off: {
        name: "br",
        style: {
          backgroundColor: "blue",
          color: "red",
        },
      },
    };

    if (spanElement.current.textContent === toggleVars.on.name) {
      props.styleSetter(toggleVars.off.style); // 부모의 state 변경 시도
      spanElement.current.textContent = toggleVars.off.name;
    } else if (spanElement.current.textContent === toggleVars.off.name) {
      props.styleSetter(toggleVars.on.style); // 부모의 state 변경 시도
      spanElement.current.textContent = toggleVars.on.name;
    }
  };

  return (
    <div>
      <h3>자식입니다.</h3>
      <span ref={spanElement}>bw</span>
      <button onClick={changeStyleHandler}>스타일 변경하기</button>
    </div>
  );
};

export default Child;
```

예제 3-2. Child.js

![1.PNG](/images/2024-11-19/React-Component-and-Props-State/7.png)

![2.PNG](/images/2024-11-19/React-Component-and-Props-State/8.png)

부모 컴포넌트가 자식 컴포넌트에게 prop으로 부모 state인 `colorSet` 의 set 함수의 참조를 자식에게 넘기고 있다. 이 set 함수를 받은 자식 컴포넌트는 해당 함수에 새로운 값을 대입하여 부모의 state 값을 바꿀 수 있는 것이다. 따라서 위 예제에서처럼 자식 요소가 부모의 배경색 스타일을 바꿀 수 있는 것이다. 

---

References

[1] 에이콘아카데미 (강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 이고잉, “생활코딩! React 리액트 프로그래밍 개정판”, (위키북스, 2024)

[4] useState 잘못된 사용법

[react useState에 대한 공통적인 실수](https://sangcho.tistory.com/entry/Commons-Mistakes-with-React-useState)

[5] useState 잘못된 사용법

[[React] 잘못된 useState 사용](https://velog.io/@jhy979/React-%EC%9E%98%EB%AA%BB%EB%90%9C-useState-%EC%82%AC%EC%9A%A9)

[6] react 공식 문서 : useState

[useState – React](https://ko.react.dev/reference/react/useState#ive-updated-the-state-but-logging-gives-me-the-old-value)

[7] state 작동원리

[스냅샷으로서의 State – React](https://ko.react.dev/learn/state-as-a-snapshot)