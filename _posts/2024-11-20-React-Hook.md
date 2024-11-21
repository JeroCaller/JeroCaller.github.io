---
title: "[React] Hook"
category: "React"
tag: ["React", "Hook"]
---

# 함수형 컴포넌트 VS 클래스형 컴포넌트. 그리고 Hook의 등장

클래스와 함수의 차이에 대해 생각해보자. 클래스에는 수많은 필드와 메서드들이 존재할 것이다. 이러한 클래스가 인스턴스로 생성되면, 해당 인스턴스 내의 필드와 메서드 참조들은 인스턴스 자체가 소멸되기 전까지는 사라지지 않는다. 한 예로 클래스 내에 특정 private 필드에 대한 setter 메서드를 생성하면 코드 상에서 setter 메서드를 호출하면 그 호출이 끝나도 바뀐 값은 그대로 해당 필드에 유지된다. myNum이라는 필드가 있으며 이를 setter를 통해 2에서 3으로 변경하면 해당 인스턴스 자체가 소멸되지 않는 한, 다음 setter 메서드 호출문을 만나기 전까지는 계속 3이라는 값을 유지할 수 있다. 

즉, 클래스는 그 클래스 내부의 필드가 인스턴스 자체가 소멸하지 않는 한 계속 유지된다. 

반면 함수는 그 내부에 존재하는 지역 변수에 대해, 해당 함수가 한 번 호출되고 나면 그 함수 내부에 존재하는 지역 변수도 메모리 상에서 삭제된다. 

React에서는 state의 변경 등 어떠한 이유로 리렌더링을 trigger하면 함수형 컴포넌트의 경우 매번 함수의 인스턴스가 메모리에 생성되어 실행되고, 실행이 끝나면 해당 함수는 메모리 상에서 소멸된다. 반면 클래스형 컴포넌트의 경우, 리렌더링을 위해선 그저 클래스 내부의 render() 메서드만 다시 호출하면 된다. 그래서 애플리케이션 자체가 종료되지 않는 이상 클래스형 컴포넌트는 계속 메모리 상에 존재하게 된다. 

이러한 클래스와 함수 간의 차이에 의해, 함수에서는 클래스에서는 가능한 여러 가지 일들을 할 수 없다. 예를 들면 함수형 컴포넌트는 리렌더링 후에도 state 값을 유지할 수가 없다. 클래스에서는 `this.state`와 같이 state를 필드로 선언하였기에 render() 메서드가 매번 호출되어도 `this.state`의 값은 그대로 유지될 수 있다. 

이러한 이유로, 예전에는 컴포넌트 제작 시 주로 클래스를 많이 사용하였다고 한다. state, 컴포넌트의 생명 주기(life cycle)에 의한 기능들을 사용하려면 class가 적합했기 때문이다. 

하지만 컴포넌트를 클래스로 작성하다보니 여러 문제점이 생겼다고 한다. 

- 컴포넌트 간에 재사용 가능한 로직을 연결하는 방법이 없었다. 따라서 프로젝트 전체적으로 봤을 때 중복되는 패턴의 코드가 발생한다고 한다.
- 컴포넌트 간 로직 재사용 불가 문제는 render props나 HOC(HIgh Order Component, 고차 컴포넌트) 라는 기술을 이용하여 해결할 수 있다고 한다. 그러나 이 경우 코드가 복잡해져 코드의 추적을 어렵게 만들고, 이는 디버깅의 어려움을 가져온다.

그러던 중 2018년 이후 나온 버전 16.8부터 Hook이라는 새로운 개념이 추가되면서, 함수형 컴포넌트에서는 사용할 수 없었던 state 유지 및 컴포넌트 생명 주기 관련 기능을 사용할 수 있게 되었다. 이로 인해 클래스형 컴포넌트의 장점은 모두 취하면서도 단점까지 가져가지 않는 함수형 컴포넌트를 많이 사용하게 되었다고 하고, 지금도 여전히 컴포넌트는 함수로 많이 작성한다고 한다. 

한마디로 Hook은 state 유지, 컴포넌트 생명 주기 관련 기능 등 클래스형 컴포넌트에서만 사용 가능했던 여러 기능들을 함수형 컴포넌트에서도 사용 가능하게 하는 기능들을 의미한다고 보면 되겠다. 

Hook의 특징은 다음과 같다.

- 함수형 컴포넌트에서만 사용한다. 클래스 컴포넌트에서는 사용하지 않는다.
- react 자체적으로 useState, useEffect와 같은 내장 Hook을 지원한다.
- 개발자가 직접 Hook을 제작하는 것도 가능하다. 일례로, Redux라는 라이브러리에는 `useSelector`, `useDispatch` 와 같은 Hook을 제공하는데, 이 모두 React 내장 Hook은 아니다.
- 함수형 컴포넌트 내에서 사용 시, 항상 함수 내 최상위 레벨에서만 호출해야 한다. return문 이전에 작성해야 하며, 다른 자바스크립트 함수 내부에서 호출하여 사용할 수 없다.
- 컴포넌트 함수 바깥에서 사용할 수 없다.
- React에서의 개념인 컴포넌트를 위한 함수가 아닌 일반적인 순수 자바스크립트 함수 내에서는 사용 불가.

```jsx
import { useState } from 'react';

const [counter, setCounter] = useState(0);  // X. Hook은 반드시 컴포넌트 함수 내부에서만!

const MyComponent = () => {

	const [counter, setCounter] = useState(0); // O. 반드시 컴포넌트를 구성하는 함수의 최상위 레벨에서 Hook 사용.
	
	const myHandler = event => {
		useState(0);  // X. 컴포넌트가 아닌 일반 함수 내에서 호출 불가.
	}
	
	return (
		<div>
			{useState(0)}  {/* X. Hook은 리턴문 내에서 사용 불가 */}
			<button onClick={myHandler}>내 버튼</button>
		</div>
	);
}

export default MyComponent;
```

기존 클래스형 컴포넌트에 비해 Hook 기능을 사용할 수 있게 된 함수형 컴포넌트의 장점은 다음과 같다. 

- 클래스 컴포넌트에서는 컴포넌트 제작을 위해 render() 메서드 정의, React.Component라는 클래스를 반드시 상속해야 하는 등 지켜야 할 규칙도 상대적으로 더 많고 코드도 장황한 반면, 함수형 컴포넌트는 Hook 사용 시 바로 state를 사용하거나 생명 주기 관련 기능을 바로 사용할 수 있어 코드도 짧아진다.
- 컴포넌트 외에 여러 메서드, 필드들과 같이 군더더기들이 많아 컴포넌트를 재사용하기 어려웠던 클래스형 컴포넌트에 비해, 함수형 컴포넌트는 딱 사용자 인터페이스(UI)만을 위한 코드를 작성할 수 있어 최소 단위의 컴포넌트 정의가 가능하고, 이로 인해 재사용 가능한 컴포넌트 생성이 가능해진다.

# React 내장 Hook

React에서 자체적으로 제공하는 Hook에는 여러 가지들이 있다. 이 글에서 소개할 Hook 외에도 많은 Hook들이 있음을 알린다. 

React 내장 Hook을 사용하려면 다음과 같이 import 하면 된다.

```jsx
import { useState, useEffect } from 'react';
```

React 내장 Hook들은 그 이름이 모두 use로 시작한다. 이 hook들은 ‘react’라는 모듈로부터 import하여 사용할 수 있다. 

## useState

컴포넌트 내부에서 state를 사용하기 위한 내장 hook이다. state(상태)를 선언 및 관리할 때 사용한다. 

```jsx
import { useState } from 'react';

const MyComponent = () => {
	const [counter, setCounter] = useState(0); // useState 첫 호출
	
	// 생략 ...
	
	const myEventHandler = event => {
			// ...
			setCounter( (prevState) => prevState + 1); // set 함수를 호출하여 state 값 변경. 
	}
	
	// 생략 ...
}
```

예제 1-1

다음의 특징들을 가진다.

- 해당 hook 함수 호출 시 `useState()` 함수의 인자로 전달된 값으로 초기화된 state와 이 state의 값을 변경할 수 있는 setter 함수가 반환된다.
- setter 함수를 호출하면 state값을 새로운 값으로 변경하도록 React에 요청을 하며, React 라이브러리는 리렌더링, 즉 컴포넌트를 구성하는 함수를 새로 호출하여 새로운 state 값으로 업데이트한다.
- 함수형 컴포넌트 내 일반 변수로 값 변경을 하려고 하면 리렌더링이 트리거되지 않기에 이를 위해선 대신 state를 사용해야 하고, 이를 위해 `useState()` 를 사용한다.

useState에 대한 자세한 설명 및 주의 사항은 [Component 및 Props와 State](/react/React-Component-and-Props-State/) 페이지를 참고하면 되겠다. 

## useEffect

AJAX를 이용하여 외부에서 데이터 가져오기와 같이 외부 시스템과 연결할 때 사용할 수 있는 Hook으로, 브라우저 DOM, 외부 라이브러리와의 연동 등 여러 부수 효과에서도 사용할 수 있다. 

이벤트 발생이 아닌 렌더링이 발생할 때마다 렌더링 이후에 어떤 작업을 수행하게 하기 위해 사용될 수 있다. 

useEffect() 함수 인자에는 크게 다음과 같이 렌더링 이후에 실행될 콜백함수, 해당 콜백함수 내부에 return되는 마무리 작업 코드를 위한 콜백함수, 그리고 의존성 배열 이렇게 3가지로 구성되어 있다고 볼 수 있다. 

```jsx
useEffect( () => {
	// 로직 작성
	
	return () => {
		// clean up code. 즉 마무리 코드를 함수 형태로 
		// 작성하여 return하면 된다. 
		// 이는 선택적이다. 
	}
}, []); // 의존성 배열. 배열 내 나열된 특정 값들이 변경될 때마다 콜백 함수를 실행하게끔 함. 생략 시 첫 렌더링 시에만 콜백 함수가 작동됨
```

예제 2-1

첫 번째 인자로는 렌더링 이후 실행할 로직을 담은 콜백 함수를 작성하면 된다. 이 콜백 함수에서는 return문을 사용해도 되고 사용하지 않아도 되는데, 사용할 경우 마무리 작업을 하는 콜백 함수를 return하게 하면 된다. 마치 Java에서 파일 입출력 스트림 또는 JDBC로 DB 연동을 할 때마다 작업을 모두 마치면 항상 close() 메서드를 호출하듯이, 작업 이후 메모리 청소 등 반드시 마무리 지어야 할 코드가 있을 때 사용하면 되겠다. 마무리 작업이 없다면 아무것도 return 하지 않아도 된다. 

두 번째 인자에는 의존성 배열이 들어가는데, state와 같이 리렌더링을 유발하는 값을 넣으면 해당 값이 변경되어 리렌더링이 된 후에 첫 번쨰 인자의 콜백 함수가 실행되게끔 설정할 수 있다. 예를 들어 state인 counter에 대해 `[counter]` 와 같이  의존성 배열에 추가되면 React에서는 해당 값이 변경되어 리렌더링을 트리거할 때마다 리렌더링 후에 항상 첫 번쨰 인자로 전달된 콜백 함수가 실행되게끔 한다. 

만약 의존성 배열에 아무것도 넣지 않는 경우, 초기 렌더링 이후에만 딱 한 번 콜백 함수가 호출된다. 이 점을 활용하면 fetch를 이용하여 외부에서 데이터를 딱 한 번만 가져와서 화면에 출력해야 할 때 유용하게 사용할 수 있을 것이다. 

```jsx
const [fetchData, setFetchData] = useState({});

useEffect(() => {
	fetch('/api/products')
	.then(response => response.json())
	.then(result => {
		setFetchData(result);
	})
	.catch(error => {
		console.log(error);
	});
}, []);

// AJAX로 가져온 데이터를 저장한 fetchData라는 state를 
// 이용하여 데이터를 화면에 출력하는 코드 작성 가능.
```

useEffect는 어떠한 이유로 렌더링이 트리거 되면 렌더링이 다된 후에 실행되기에, state를 의존성 배열에 추가한 채로, useEffect 함수의 첫 번째 인자로 전달되는 콜백 함수 내부에 해당 state 값을 변경하는 setter 함수 호출문을 직접 작성해서는 안된다. 렌더링을 트리거하는 코드가 렌더링 후 실행되는 코드와 맞물려 무한 루프에 빠지기 때문이다. 

```jsx
const [counter, setCounter] = useState(0);

useEffect(() => {
	setCounter(counter + 1); // 무한 루프 생성.
}, [counter]);
```

예제 3-1

앞서 말했듯, useEffect는 주로 외부 시스템과의 연결 작업에 적절하지, event handler로 사용하는 것은 좋지 않다. useEffect는 이벤트 발생으로 실행되는 것이 아니라 리렌더링이 발생한 후에 실행되는 개념이기 때문이다. 

## useRef

useRef는 Vanila JS에서 `document.querySelector()` 등으로 DOM에 접근하고 조작했던 것처럼, React에서 Virtual DOM 요소에 접근할 때 사용되는 Hook이다. `useState` 처럼 호출 시 인자로 변수의 초기값을 지정할 수 있다. DOM을 구성하는 Node도 객체이기에 보통은 `const myElement = seRef(null)` 과 같이 초기화한다. 그 후, JSX 태그 중 접근하고자 하는 태그의 속성으로 `<div ref={myElement}>` 와 같이 작성하면 해당 DOM 노드를 선택할 수 있게 된다. 

`useRef()` 함수 호출로 얻은 변수에는 current라는 단 하나의 속성만을 가지며, `myElement.current.classList` 와 같이 current 속성으로부터 HTML 요소의 여러 속성들에 접근할 수 있다. 

사실 useRef는 꼭 DOM 접근에만 사용되지 않고 일반적인 자료형의 데이터를 담을 때에도 사용된다고 한다. 

state의 경우 `useState()` 함수 호출로 얻는 setter 함수를 이용하면 state 값을 변경시킬 수 있으며, 이는 리렌더링을 유발한다. 그러나 ref를 변경한다고 해서 리렌더링이 되지는 않는다. 

다음은 useRef를 사용하는 예이며, 이를 잘못 활용한 예제이다.

```jsx
import {useState, useRef} from 'react';

const HookUseRef = () => {

    const nameInput = useRef(null);
    const foodSelect = useRef(null);

    const [userName, setUserName] = useState('');
    const [food, setFood] = useState('');

    /**
     * 
     * @param {Event} event 
     */
    const handleClick = event => {
        event.preventDefault();
        console.log(nameInput.current.value);
        console.log(foodSelect.current.value);
    }

    return (
        <div>
            <form>
                <label htmlFor="name">이름: </label>
                <input type="text" name="name" id="name" ref={nameInput} />
                <p>음식 선택</p>
                <select name="food" ref={foodSelect} value="burger">
                    <option value="burger">햄버거</option>
                    <option value="pizza">피자</option>
                    <option value="pasta">파스타</option>
                </select>
                <button onClick={handleClick}>제출</button>
            </form>
            <hr/>
            <div>
                <h3>사용자 정보 출력란</h3>
                <p>이름 : {(nameInput.current) ? (nameInput.current.value) : ''}</p>
                <p>좋아하는 음식: {(foodSelect.current) ? (foodSelect.current.value) : ''}</p>
            </div>
        </div>
    );
}

export default HookUseRef;
```

예제 4-1

![스크린샷 2024-11-21 095338.png](/images/2024-11-20/React-Hook/1.png)

위 예제에서 입력받은 사용자 정보를 출력하는 곳에 Ref의 값으로 직접 출력하도록 하고 있다. 그 결과 아무리 사용자 정보를 입력하고 출력하더라도 화면에 표시되지 않는다. 이는 state와 달리 ref는 그 값이 변경되더라도 리렌더링을 트리거하지 않는다는 증거이다. 

위 예제를 state와 같이 사용하는 코드로 바꿔보겠다.

```jsx
import {useState, useRef} from 'react';

const HookUseRef = () => {

    const nameInput = useRef(null);
    const foodSelect = useRef(null);

    const [userName, setUserName] = useState('');
    const [food, setFood] = useState('');

    /**
     * 
     * @param {Event} event 
     */
    const handleClick = event => {
        event.preventDefault();
        console.log(nameInput.current.value);
        console.log(foodSelect.current.value);

        setUserName(nameInput.current.value);
        setFood(foodSelect.current.value);
    }

    return (
        <div>
            <form>
                <label htmlFor="name">이름: </label>
                <input type="text" name="name" id="name" ref={nameInput} />
                <p>음식 선택</p>
                <select name="food" ref={foodSelect} defaultValue="pizza">
                    <option value="burger">햄버거</option>
                    <option value="pizza">피자</option>
                    <option value="pasta">파스타</option>
                </select>
                <button onClick={handleClick}>제출</button>
            </form>
            <hr/>
            <div>
                <h3>사용자 정보 출력란</h3>
                <p>이름 : {userName}</p>
                <p>좋아하는 음식: {food}</p>
            </div>
        </div>
    );
}

export default HookUseRef;
```

예제 4-2.

![스크린샷 2024-11-21 095704.png](/images/2024-11-20/React-Hook/2.png)

값 변경 시 리렌더링을 트리거하는 state를 사용하여 이를 출력하니 “제출” 버튼 클릭 시 사용자 입력 정보가 잘 나오는 것을 볼 수 있다. 

React에서 DOM 노드 접근을 할 때 Vanila JS 방식으로 `document.querySelector()` 와 같이 직접 DOM에 접근하는 방식을 취하는 것은 권장되지 않는다. React에서는 Virtual DOM이라는 개념을 이용하여 특정 state 등의 값이 변경될 경우 변경된 사항이 반영된 Virtual DOM과 아직 변경되기 전의 DOM을 서로 비교하여 변경된 부분만 업데이트하는 과정을 거친다. 따라서 여기서 DOM에 직접 접근하는 방식을 취하면 다음의 문제점이 생긴다.

- Virtual DOM을 이용하여 변경 사항에 대한 부분만 DOM에 업데이트 하여 리렌더링하는 방식이지, DOM 트리 전체를 업데이트 하는 방식이 아니다. 이로 인해 사용자 입장에서는 페이지 깜빡임 현상을 경험하지 않을 수 있는데, 여기서 DOM을 직접 접근하는 방식을 취하면 이 장점을 얻을 수 없다.
- React가 Virtual DOM을 이용하여 변경사항을 알아서 업데이트 하는 방식인데, 이를 개발자가 직접 DOM에 접근하여 조작하려고 하면 Virtual DOM과 충돌나 예상치 못한 버그가 발생할 수도 있다.

따라서 되도록이면 useRef를 이용하여 최대한 React가 제공하는 DOM 접근 방식을 사용하는 것이 좋겠다. 

## useCallback

어떤 연산 작업이 중복적으로 호출될 때, 매번 똑같은 연산을 하기보다는, 최초 연산 한 번만 하고, 그 결과를 메모리에 저장한 후, 똑같은 연산 작업 호출 시 실제로 연산을 하지 않고 메모리에 저장된 그 값을 바로 반환하는 것이 더 효율적일 것이다. 이렇게 연산 결과를 메모리에 저장하는 캐싱 기법을 메모이제이션(memoization)이라고 한다. (memorization이 아니다)

리액트에서 리렌더링할 때마다 컴포넌트를 구성하는 함수(클래스의 경우 render() 메서드)를 매번 호출 하는데, 그 내부에 존재하는 여러 콜백 함수들도 매번 메모리에 생성하여 적재한다고 한다. 이러한 과정이 포함되기에 화면에 리렌더링이 완료되는 시간도 늘어져서 사용자 입장에서는 페이지 깜빡임 또는 최악의 경우(?) 몇 초(??) 동안 원하는 기능이 화면에 노출되지 않을 수도 있다. 따라서 콜백 함수에 대해서도 메모이제이션 기법을 적용하여 최초 생성 시에만 콜백 함수의 참조를 메모리에 기록하여 똑같은 콜백 함수 생성 명령을 또 받으면 이를 재생성하는 대신 기존에 메모리에 저장된 콜백 함수의 참조만 반환하게 하면 렌더링 속도도 올라갈 것이다. 이럴 때 사용하는 React 내장 Hook이 `useCallback()` 함수이다. 

`useCallback()` 함수 호출 방법은 `useEffect()` 와 많이 닮아있다. 

```jsx
const myEventHandler = useCallback((event) => {
		// 콜백 함수 로직 작성...
}, []); // 의존성 배열.
```

예제 5-1. 

첫 번째 인자로는 메모이제이션을 적용할 콜백 함수를 전달하면 된다. 두 번째 인자로는 의존성 배열이 들어가며, 의존성 배열에는 state와 같이 값 변화 시 리렌더링을 촉발하는 특정 변수를 넣어, 해당 변수의 값이 변할 때마다 실행되도록 설정할 수 있다. 의존성 배열을 비워놓으면 첫 렌더링 직후에서만 콜백 함수가 실행된다. 

부모 컴포넌트에서의 state 값이 자식 컴포넌트의 prop으로 넘어가도록 설정되어 있을 때 부모 컴포넌트에서 리렌더링이 자주 발생하면 이는 state 값이 계속 변한다는 뜻으로, 이로 인해 props로 받는 자식 컴포넌트도 리렌더링의 대상이 된다. 만약 부모 컴포넌트만 리렌더링이 필요하다면 자식 컴포넌트의 리렌더링은 매우 불필요한 작업일 것이다. 이럴 때에도 state의 setter 함수 호출문을 useCallback 함수의 첫 번째 인자로 전달되는 콜백 함수 내에 작성하도록 하면 자식 컴포넌트의 불필요한 리렌더링 작업을 방지할 수 있을 것이다. 

다음은 부모 컴포넌트와 자식 컴포넌트에 관한 예제이다.

```jsx
import {useCallback, useState} from 'react';
import Child from './Child';

const Parent = () => {

    const [counter, setCounter] = useState(0);

    const clickHandler = event => {
        setCounter((prevState) => prevState + 1);
        console.log("부모 컴포넌트의 리렌더링");
    }

    return (
        <div>
            <h2>부모 컴포넌트</h2>
            <input type="text" readOnly value={counter} />
            <button onClick={clickHandler}>카운트하기</button>
            <hr/>

            <Child onClick={clickHandler}></Child>
        </div>
    );
};

export default Parent;
```

예제 5-2. Parent.js

```jsx
import React from "react";

const Child = (props) => {

    console.log("자식입니다.");
    
    return (
        <div>
            <h3>자식 컴포넌트</h3>
            <button onClick={props.onClick}>자식 버튼</button>
        </div>
    );
};

export default Child;
```

예제 5-3. Child.js

![화면 캡처 2024-11-21 173546.png](/images/2024-11-20/React-Hook/3.png)

![화면 캡처 2024-11-21 173601.png](/images/2024-11-20/React-Hook/4.png)

부모 컴포넌트에서 버튼을 계속 클릭하면 그만큼 자식 컴포넌트도 리렌더링 되는 것을 콘솔창 출력결과를 통해 알 수 있다. 또한, 자식 컴포넌트 내 “자식 버튼”을 클릭해도 역시 부모-자식 모두 리렌더링되는 것을 볼 수 있다. 

이번에는 불필요한 자식 컴포넌트의 리렌더링 방지를 위해 다음과 같이 코드를 수정하였다.

```jsx
import {useCallback, useState} from 'react';
import Child from './Child';

const Parent = () => {

    const [counter, setCounter] = useState(0);

    /*
    const clickHandler = event => {
        setCounter((prevState) => prevState + 1);
        console.log("부모 컴포넌트의 리렌더링");
    }*/
    
    const clickHandler = useCallback(event => {
        setCounter((prevState) => prevState + 1);
        //setCounter(counter + 1);
        console.log("부모 컴포넌트의 리렌더링");
    }, []);

    return (
        <div>
            <h2>부모 컴포넌트</h2>
            <input type="text" readOnly value={counter} />
            <button onClick={clickHandler}>카운트하기</button>
            <hr/>

            <Child onClick={clickHandler}></Child>
        </div>
    );
};

export default Parent;
```

예제 5-4. Parent.js

```jsx
import React from "react";

const Child = (props) => {

    console.log("자식입니다.");
    
    return (
        <div>
            <h3>자식 컴포넌트</h3>
            <button onClick={props.onClick}>자식 버튼</button>
        </div>
    );
};

export default React.memo(Child);
//export default Child;
```

예제 5-5. Child.js

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-20/React-Hook/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

여기서 중요한 것은, 부모 컴포넌트에서 자식 컴포넌트로 전달할 이벤트 핸들러 함수에 `useCallback()` 함수를 사용함과 동시에 자식 컴포넌트에서 export할 때의 컴포넌트 함수명을 `React.memo()` 인자에 대입하도록 해야한다는 것이다. 부모 컴포넌트 내 이벤트 핸들러 함수에 `useCallback`을 적용하지 않거나 자식 컴포넌트에서 `React.memo()` 를 적용하지 않거나 둘 중 하나라도 하지 않으면 위와 같이 자식 컴포넌트의 리렌더링 현상을 방지할 수 없음에 주의해야한다. 

## useMemo

`useCallback` 과 비슷하게 메모이제이션 기법을 사용하는 Hook으로, 특정 함수의 연산 결과로 나오는 반환값을 메모(memo)하여 리렌더링 후에도 중복되는 연산을 생략하고자 할 때 사용할 수 있다. 사용 방법은 대략 다음과 같다.

```jsx
const myVar = useMemo(() => { // 계산에 필요한 소요 시간이 많은 함수 로직...}, [의존성 배열]);
```

`useEffect` 함수의 인자와 똑같은 구조를 가지고 있다. 첫 번째 인자에는 그 반환값을 메모이제이션할 콜백 함수를 전달하고, 두 번째 인자의 의존성 배열 내에는 첫 번째 인자로 전달된 콜백 함수에서 참조하는 컴포넌트 내 props, state와 같이 리렌더링을 촉발시키는 값들을 넣는다. 그러면 렌더링 이후와 이전의 의존성 배열에 포함된 값들이 변경되었는지를 확인하고, 변경되지 않았다면 첫 번째 인자의 콜백 함수를 실행시키지 않고 메모리 상에 캐싱된 연산 결과만을 반환함으로써 똑같은 요청에 대해 불필요한 연산을 방지할 수 있다. 만약 이전 렌더링과 비교하여 의존성 배열에 포함된 변수의 값이 변경되었다면 첫 번째 인자로 전달된 콜백 함수를 실행시킨 후, 그 결과를 메모리 상에 저장하여 다음에 똑같은 결과를 내는 연산 요청이 들어올 때 해당 값을 반환할 수 있도록 준비를 한다. 

다음은 사용자에게 “피보나치 수열의 n번째 값 계산기” 서비스를 제공해주는 간단한 웹 페이지가 있다고 가정하고 만든 예제이다. 원래 n 번째 피보나치 수열의 값이 무엇인지 계산할 때 단순하게 재귀 함수 방식으로 구현하면 n의 값이 조금만 커져도 연산에 소요되는 시간이 커진다는 특징을 가지고 있다. (이에 대한 자세한 설명은 [동적 계획법 (Dynamic programming)](/algorithm/algorithm-dynamic-programming/) 페이지를 참고) 다음은 useMemo를 사용하지 않았을 때의 예제이다. 

```jsx
import {useMemo, useRef, useState, useEffect} from 'react';

const Fibonacci = () => {

    // 피보나치 계산 끝났는지 여부 확인용.
    const [isFinished, setIsFinished] = useState(false);

    // 첫 렌더링에는 아직 사용자가 값을 입력하지 않은 상태이므로
    // "계산 중..."이라는 문구가 뜨지 않도록 한다.
    useEffect(() => {
        setIsFinished(true);
    }, []);

    // 함수 실행 시간 기록용
    const [seconds, setSeconds] = useState(0);

    // 모든 계산 내역 저장용
    /*
        [
            {id: 1, n: 10, time: 30}, ...
        ]
    */
    const [allResults, setAllResults] = useState([]);
    const [nextId, setNextId] = useState(1);

    const [result, setResult] = useState(0);
    const [inputNum, setInputNum] = useState(0);
    const inputElement = useRef(null);

    const fibonacci = (n) => {
        if (n === 0) return 0;
        if (n === 1) return 1;
        return fibonacci(n-1) + fibonacci(n-2);
    }
    
    const stopwatch = (func) => {
        return (...args) => {
            const start = Date.now();

            let result = func(...args);

            const end = Date.now();
            let elapsedMillies = end - start;
            setIsFinished(true);
            setSeconds((prevState) => elapsedMillies);
            console.log(`실행 시간: ${elapsedMillies}`);

            setAllResults((prevState) => [...prevState, {id: nextId, n: args[0], time: elapsedMillies}]);
            setNextId((prevState) => prevState + 1);
            
            return result;
        }
    }

    const calculate = (event) => {
        let userNum = Number(inputElement.current.value);
        //console.log(`userNum: ${userNum}`);

        setInputNum(userNum);

        const decoratedFunc = stopwatch(fibonacci);
        let res = decoratedFunc(userNum);

        //console.log(`result: ${res}`);
        setResult((prevState) => res);
    }

    /**
     * 
     * @param {Event} event 
     */
    const enterKeyDownHandler = event => {
        if (event.key === 'Enter') {
            calculate();
        }
    }

    return (
        <div>
            <h3>피보나치 수 계산기 (n=0부터 시작)</h3>
            <input type="number" 
                id="num" ref={inputElement} 
                onKeyDown={enterKeyDownHandler}
            />
            <button onClick={calculate}>계산</button>

            <div>
                <p>계산 결과</p>
                {
                    (isFinished) ?
                    <div>
                        <p>F({inputNum}) = {result}</p>
                        <p>계산 소요 시간(밀리초): {seconds}</p>
                    </div> :
                    <p>계산 중...</p>
                }
            </div>

            <hr/>
            <div>
                <p>계산 내역</p>
                <table border="1">
                    <thead>
                        <tr>
                            <th>id</th>
                            <th>n</th>
                            <th>elapsedMillies</th>
                        </tr>
                    </thead>
                    <tbody>
                        {
                            allResults.map((record) => (
                                <tr key={record.id}>
                                    <td>{record.id}</td>
                                    <td>{record.n}</td>
                                    <td>{record.time}</td>
                                </tr>
                            ))
                        }
                    </tbody>
                </table>
            </div>
        </div>
    );
}

export default Fibonacci;
```

예제 6-1.

위 예제에서, fibonacci 함수가 입력값 n의 피보나치 수열 값을 구하는 함수이다. 그리고 계산하는데에 걸리는 시간을 측정하기 위해 stopwatch 함수를 정의하였다. 이 함수는 fibonacci 함수 내부를 전혀 건드리지 않으면서도 해당 함수가 실행하는 데에 걸린 시간을 밀리초 단위로 측정하여 seconds라는 state에 저장하는 기능을 추가해주는 decorator이다. 이 decorator 덕분에 fibonacci 함수 실행과 실행 시간 측정 두 기능을 동시에 수행할 수 있다. 

그리고 매번 fibonacci 함수가 실행되면 그에 대한 결과값과 실행 시간 등의 정보를 배열에 담아 기록하는 allRecords라는 state에 업데이트하고, 이 내역을 화면에 출력하도록 하고 있다. 이를 거꾸로 말하면, 만약 사용자가 똑같은 값을 준채로 매번 버튼을 누를 때마다 내역이 계속 추가된다면, 이는 메모이제이션을 전혀 사용하지 않은 채 매번 똑같은 입력값에 대해 중복된 연산을 하고 있다는 뜻이 된다. 

위 예제를 수행한 결과는 다음의 영상에서 확인할 수 있다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-20/React-Hook/2.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

위 영상을 보면 40이라는 똑같은 입력값에 대해 계산 버튼을 누를 때마다 계속 그 내역이 추가되고 있다. 즉, 똑같은 입력값에 대해 계속 똑같은 연산을 반복한다는 것이다. 

다음은 위 예제에서 useMemo를 적용한 예제 코드이다.

```jsx
 import {memo, useMemo, useRef, useState} from 'react';

const MemoFibonacci = () => {

    // 피보나치 계산 끝났는지 여부 확인용.
    const [isFinished, setIsFinished] = useState(true);

    // 함수 실행 시간 기록용
    const [seconds, setSeconds] = useState(0);

    // 모든 계산 내역 저장용
    /*
        [
            {id: 1, n: 10, time: 30}, ...
        ]
    */
    const [allResults, setAllResults] = useState([]);
    const [nextId, setNextId] = useState(1);

    //const [result, setResult] = useState(0);
    const [inputNum, setInputNum] = useState(0);
    const inputElement = useRef(null);

    const fibonacci = (n) => {
        if (n === 0) return 0;
        if (n === 1) return 1;
        return fibonacci(n-1) + fibonacci(n-2);
    }
    
    const stopwatch = (func) => {
        return (...args) => {
            const start = Date.now();
            setIsFinished(false);

            let result = func(...args);

            const end = Date.now();
            let elapsedMillies = end - start;
            setIsFinished(true);
            setSeconds((prevState) => elapsedMillies);
            console.log(`실행 시간: ${elapsedMillies}`);

            setAllResults((prevState) => [...prevState, {id: nextId, n: args[0], time: elapsedMillies}]);
            setNextId((prevState) => prevState + 1);
            
            return result;
        }
    }

    const calculate = (event) => {
        let userNum = Number(inputElement.current.value);
        setInputNum(userNum);
    }

    // useMemo 사용
    const memoizationResult = useMemo(() => {
        const decoratedFunc = stopwatch(fibonacci);
        console.log("메모 콜백 함수 호출됨 " + nextId);
        return decoratedFunc(inputNum);
        
    }, [inputNum]);

    /**
     * 
     * @param {Event} event 
     */
    const enterKeyDownHandler = event => {
        if (event.key === 'Enter') {
            calculate();
        }
    }

    return (
        <div>
            <h3>피보나치 수 계산기 (by useMemo) (n=0부터 시작)</h3>
            <input type="number" 
                id="num" ref={inputElement} 
                onKeyDown={enterKeyDownHandler}
            />
            <button onClick={calculate}>계산</button>

            <div>
                <p>계산 결과</p>
                {
                    (isFinished) ?
                    <div>
                        <p>F({inputNum}) = {memoizationResult}</p>
                        <p>계산 소요 시간(밀리초): {seconds}</p>
                    </div> :
                    <p>계산 중...</p>
                }
            </div>

            <hr/>
            <div>
                <p>계산 내역</p>
                <table border="1">
                    <thead>
                        <tr>
                            <th>id</th>
                            <th>n</th>
                            <th>elapsedMillies</th>
                        </tr>
                    </thead>
                    <tbody>
                        {
                            allResults.map((record) => (
                                <tr key={record.id}>
                                    <td>{record.id}</td>
                                    <td>{record.n}</td>
                                    <td>{record.time}</td>
                                </tr>
                            ))
                        }
                    </tbody>
                </table>
            </div>
        </div>
    );
}

export default MemoFibonacci;
```

예제 6-2.

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-20/React-Hook/3.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

똑같은 입력값에 대해 계산 버튼을 눌러도 최초로 단 한 번만 계산 내역에 추가되고, 그 이후로는 추가되지 않고 있음을 볼 수 있다. 즉, 똑같은 입력값에 대해 단 한 번만 fibonacci 함수를 실행하여 그 결과값을 반환하고, 그 이후로는 똑같은 입력값이므로 fibonacci 함수를 실행시키지 않고, 메모리 상에 기록한 결과값을 화면에 출력하는 형태임을 알 수 있다. 이로서 useMemo가 실제로 함수의 연산 결과를 잘 캐싱한다는 것을 확인할 수 있다. 

useMemo는 바로 이전 렌더링 때의 값과 바로 이후의 렌더링 때의 값이 동일할 때에만 메모된 값을 반환하는 구조이다. 만약 중간에 전혀 다른 입력값을 준다면 다시 fibonacci 함수가 실행된다. 예를 들어 처음에는 입력값을 40을 주고 계산을 한 후, 그 다음에 39라는 값을 주고 나서 다시 40을 주면 모두 fibonacci 함수가 실행된다. 다음 영상은 이를 증명하는 자료이다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-11-20/React-Hook/4.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

즉, useMemo를 사용하여 캐싱 효과를 제대로 누릴려면 바로 이전 렌더링 때의 state 값과 바로 이후 렌더링의 state값이 동일한 상황이 자주 재현되는 경우에 사용하면 좋겠다. 

> 이 외에도 더 많은 React 내장 Hook이 있으며, Redux에서의 useSelector, useDispatch와 같은 외부 라이브러리에서의 Hook들도 많이 존재한다. 이들에 대해선 필자가 직접 접하게 될 때마다 다른 글에서 정리해보도록 하겠다.
> 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] [Hook의 개요 – React](https://ko.legacy.reactjs.org/docs/hooks-intro.html)

[4] [내장된 React Hook – React](https://ko.react.dev/reference/react/hooks)

[5] [Hook의 규칙 – React](https://ko.legacy.reactjs.org/docs/hooks-rules.html#gatsby-focus-wrapper)

[6] useEffect 내용

[Effect로 동기화하기 – React](https://ko.react.dev/learn/synchronizing-with-effects)

[7] useRef 내용

[Ref로 DOM 조작하기 – React](https://ko.react.dev/learn/manipulating-the-dom-with-refs)

[8] useRef 내용

[Ref로 값 참조하기 – React](https://ko.react.dev/learn/referencing-values-with-refs)

[9] 메모이제이션

[메모이제이션](https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EC%9D%B4%EC%A0%9C%EC%9D%B4%EC%85%98)

[10] 메모이제이션 관련 내용 (내 블로그)

[[알고리즘] 동적 계획법 (Dynamic Programming)](https://jerocaller.github.io/algorithm/algorithm-dynamic-programming/)

[11] [useMemo를 알아봅시다. (Feat. 예제)](https://highero.tistory.com/entry/useMemo%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B4%85%EC%8B%9C%EB%8B%A4-Feat-%EC%98%88%EC%A0%9C)