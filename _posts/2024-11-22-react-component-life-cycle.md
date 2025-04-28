---
title: "[React] Component의 Life Cycle"
category: "React"
tag: ["React", "Component", "Hook", "Life cycle"]
---

React로 만든 웹 애플리케이션이 가동되는 동안 수많은 컴포넌트들이 React에 의해 생성(mount), 렌더링되어 사용자 화면에 보이기도 하고, state와 같은 값이 변경되어 업데이트(update)되기도 하며, 최종적으로 제거(unmount)되기도 한다. 즉, React에서의 컴포넌트의 생명 주기는 대략 다음의 과정을 거친다.

생성(mount) → 업데이트(update) → 제거 (unmount)

클래스형 컴포넌트에서는 이러한 생명 주기(Life Cycle) 관련 메서드들을 제공하고, 함수형 컴포넌트에서는 대신 Hook을 통해 생명 주기와 관련된 기능을 연동하여 사용할 수 있다. 

# 클래스형 컴포넌트에서의 생명주기 관련 메서드

클래스형 컴포넌트에서는 컴포넌트의 생명 주기와 관련된 여러 가지 메서드들이 있다. (모두 부모 클래스인 React.Component에서 제공하는 메서드라 보면 되겠다) 이러한 클래스형 컴포넌트에서 제공하는 생명 주기 관련 메서드들을 먼저 살펴봐야 함수형 컴포넌트에 사용되는 Hook에서의 생명 주기에 대해 살펴볼 수 있기에 여기선 먼저 클래스형 컴포넌트의 생명 주기에 대해 살펴보겠다. 

## 마운트 (mount)

컴포넌트가 생성되는 단계를 마운트(mount)라 한다. 마운트 단계에서 사용 가능한 메서드들은 다음과 같다. 다음의 순서대로 호출된다고 한다. 

1. constructor - 생성자 메서드로, 컴포넌트 생성 시 가장 먼저 실행되는 메서드이다. 여기서 props를 받을 수 있고, state를 생성할 수 있다. 
2. `static getDerivedStateFromProps(props, state)` - props 데이터를 state에 주입하고자 할 때 사용된다. (자주 사용되지는 않는 것 같다)
3. render - 컴포넌트 렌더링에 사용되는 메서드이다. 
4. componentDidMount - 컴포넌트가 첫 렌더링을 마칠 때 호출되는 메서드이다. 이 메서드가 호출될 때에는 이미 화면에 컴포넌트가 표시된 상태이다. AJAX를 통한 외부로부터의 데이터 가져오기, 네트워크 요청, DOM을 사용해야 하는 외부 라이브러리 연동 등 외부 시스템과의 연동에 주로 사용된다. Hook에서는 `useEffect` 함수가 의존성 배열에 아무런 의존성이 존재하지 않은 채로 호출될 때 내부적으로 componentDidMount가 호출된다. 
    
    ```jsx
    useEffect(() => {
    		// ...
    }, []);
    ```
    
    코드 1-1.
    

## 업데이트(Update)

컴포넌트가 업데이트되는 시점에서는 관련된 생명 주기 관련 메서드들이 다음의 순서대로 호출된다고 한다.

- `static getDerivedStateFromProps(props, state)` - 최초 마운트 시점과 더불어 업데이트(컴포넌트의 리렌더링) 시점에서도 호출된다. props 또는 state가 바뀌었을 때에도 props 데이터들 중 특정 데이터를 state에 주입하고자 할 때 사용된다.
- `shouldComponentUpdate(nextProps, nextState)` : 현재의 state 또는 props의 변화를 감지하여 리렌더링을 트리거할 지 여부를 판단할 수 있는 메서드이다. boolean형으로 return되어 리렌더링 결정 여부를 판단할 수 있다. 최초 렌더링(마운트) 시에는 호출되지 않는 메서드이다. 
`React.memo()`와 같이 불필요한 리렌더링을 방지하여 성능 최적화를 위한 메서드라고 한다. 
`shouldComponentUpdate()` 메서드를 개발자가 직접 오버라이딩하기보다는 PureComponent 메서드를 대신 사용하여 코드를 작성하는 것이 좋다고 한다. 해당 메서드는 이전의 props 및 state와 현재의 것과 얕은 비교를 수행하여 꼭 필요한 갱신 작업을 건너뛸 확률을 낮춘다고 한다.
    - 얕은 비교(Shallow Compare) : 객체 참조형 값들에 대해, 그 값 또는 프로퍼티가 아닌 메모리 상의 참조 위치만을 비교하는 방식. 두 객체 참조 변수가 존재할 때 그 값이 서로 같더라도 서로 별개의 객체라면 두 참조가 다르다고 판단한다.
    - 깊은 비교(Deep compare) : 참조가 아닌 값으로 비교한다. 두 객체의 값이 같으면 같은 것으로 판단한다.
    - 리액트에서는 컴포넌트의 리렌더링을 할지 여부를 이전과 현재의 state, props 간 얕은 비교를 통해 결정한다고 한다.
- `render()` : 역시 업데이트 될 때 리렌더링 시에도 해당 메서드가 호출된다.
- `getSnapshotBeforeUpdate()`
- `componentDidUpdate()` : 컴포넌트의 업데이트 이후 발생한다. 이는 의존성 배열에 명시된 값들이 변할 때만 useEffect가 실행되는 것과 같다.
    
    ```jsx
    useEffect(() => {
      // ...
    }, [count, myState]);
    ```
    
    코드 2-1.
    

## 언마운트(unmount)

컴포넌트가 화면에서 사라져 소멸될 때 호출되는 메서드는 다음의 하나 뿐이라고한다. 

- `componentWillUnmount()` : 컴포넌트가 화면에서 소멸되기 직전에 호출된다. 주로 DOM에 등록했던 이벤트 해제, setTimeout의 clearTImeout 호출 등 작업을 수행. useEffect 내 콜백 함수의 리턴문과 동일한 기능을 한다.
    
    ```jsx
    useEffect(() => {
        // ...
        return () => { // clean up...};
    }, []);
    ```
    
    코드 3-1. 
    

# 함수형 컴포넌트의 생명주기

클래스형 컴포넌트에서는 앞서 본 것처럼 메서드를 이용하여 생명 주기를 관리하는 반면 함수형 컴포넌트는 hook을 사용하며, 그래서 생명 주기 관리 방법이 다르다. hook에는 useState, useEffect 등이 있으며, 리액트 기본 내장된 hook들의 자세한 사항은 [이 글](/react/React-Hook/)을 참조하면 되겠다.

함수형 컴포넌트에서는 주로 useEffect hook을 이용하여 컴포넌트의 생명 주기를 관리한다. useEffect에는 앞서 소개한 클래스형 컴포넌트의 생명주기 관련 메서드들 중 componentDidMount(마운트), componentDidUpdate(업데이트), componentWillUnmount(언마운트)의 세 기능들을 동시에 제공한다. 

```jsx
import {useState, useEffect} from 'react';

const MyFunctionalComponent = (props) => {

  const [count, setCount] = useState(0);

	// 의존성 배열에 아무것도 없는 경우 컴포넌트가 처음 마운트될 때에만 한 번 실행됨(마운트).
  useEffect(() => {...}, []);
	
  useEffect(() => {
    // 작업 수행 코드
    return (() => { console.log("컴포넌트 언마운트됨"); } // 컴포넌트 언마운트 시 실행(언마운트).
  }, [count, props.myValue]);  // 의존성 배열에 들어있는 의존성 중 하나라도 그 값이 변경되면 콜백함수가 실행됨(업데이트)

};

export default MyFunctionalComponent;
```

코드 4-1. 함수형 컴포넌트의 생명주기를 관리하는 useEffect hook.

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife/QbpR/72)

[3] 라이프 사이클

[[React.js] 리액트 라이프사이클(life cycle) 순서, 역할, Hook](https://velog.io/@minbr0ther/React.js-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4life-cycle-%EC%88%9C%EC%84%9C-%EC%97%AD%ED%95%A0)

[4] [State and Lifecycle – React](https://ko.legacy.reactjs.org/docs/state-and-lifecycle.html)

[5] [반응형 effects의 생명주기 – React](https://ko.react.dev/learn/lifecycle-of-reactive-effects#the-lifecycle-of-an-effect)

[6] 컴포넌트 생명주기 관련 메서드들 설명.

[React.Component – React](https://ko.legacy.reactjs.org/docs/react-component.html)

[7] 얕은 비교 VS 깊은 비교

[얕은 비교(Shallow Compare) 와 깊은 비교(Deep Compare)](https://velog.io/@neighborkim/%EC%96%95%EC%9D%80-%EB%B9%84%EA%B5%90Shallow-Compare-%EC%99%80-%EA%B9%8A%EC%9D%80-%EB%B9%84%EA%B5%90Deep-Compare)