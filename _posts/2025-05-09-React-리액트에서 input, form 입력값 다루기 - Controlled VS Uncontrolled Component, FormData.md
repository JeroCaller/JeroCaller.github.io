---
title: "[React] 리액트에서 input, form 입력값 다루기 : Controlled VS Uncontrolled Component, FormData"
category: "React"
tag: ["React", "Input", "Form", "입력", "입력값", "사용자 입력", "사용자 입력값", "Uncontrolled Component", "Controlled Component", "state", "상태", "상태 관리", "FormData", "value", "defaultValue"]
---

그동안 리액트를 다루면서도 헷갈렸던 점 중 하나는 `<input>` , `<form>` 태그를 리액트 컴포넌트 내에서 다루는 방법이었다. SPA 구현 시 `event.preventDefault()` 를 사용하여 서버로의 자동 제출로 인한 새로고침 현상을 방지한 후, form 태그 내에 담긴 사용자의 입력값을 AJAX로 요청하는 기본적인 동작부터 유효성 검사 등 조금 더 복잡한 기능까지, 사용자 입력값들을 추출하여 어떠한 작업을 해야할 때가 자주 있다. 그럴 때마다 어떨 때는 직접 DOM으로 접근하여 값을 가져올 때도 있고 또 어떨 때는 React의 state를 이용하는 방법을 쓸 때도 있었다. 매번 이러한 방식들을 혼용하여 사용했기에 어떤 방식을 써야 좋은 건지 헷갈렸다. 그래서 이참에 제대로 글로 정리하고자 한다. 

이 글에서는 크게 사용자 입력 태그인 `<input>` , `<form>` 에 입력되는 사용자 입력값을 추출하는 두 가지 방법에 대해 살펴보겠다. 몰랐는데 여기에도 정식 이름이 붙어 있어 구분되고 있었다. 그 다음으로 해당 태그들에 대한 몇 가지 자잘한 정보들도 이참에 정리하겠다. 

# 사용자 입력 요소를 관리하는 방법 : Uncontrolled VS Controlled Component

리액트 컴포넌트 내에서 사용자 입력 요소를 관리하는 방법으로 크게 두 가지가 있다. Uncontrolled Component와 Controlled Component 방식이다. Uncontrolled, controlled, 즉 여기서 말하는 “제어”라는 것은 `<input>` , `<form>` 등의 사용자 입력 요소들을 리액트 시스템에서 제어하느냐 아니냐를 기준으로 한다. 리액트 시스템에서 제어한다는 것은 주로 state, 즉 사용자 입력값을 리액트의 상태로 관리한다는 것을 의미한다. 

이 두 방식에 대해 각각 살펴보겠다.

## Uncontrolled Component

Uncontrolled Component는 사용자 입력 요소(`<input>`)를 리액트의 상태 관리로 제어하지 않고 직접 DOM으로 접근하는 방법으로 사용자 입력 요소를 다룬다. 

다음은 Uncontrolled Component의 한 예제이다.

```jsx
import { useRef, useState } from "react";

const UnControlledCompEx = () => {

  const messageStyle = {
    error: {
      color: 'red',
    },
    granted: {
      color: 'green',
    },
  };

  const formElement = useRef(null);
  const [ message, setMessage ] = useState('');
  const [ messageCss, setMessageCss ] = useState(null);

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmit = event => {
    event.preventDefault();

    /**
     * @type {String}
     * 
     * form 태그 내 name="username"인 input 요소에 직접 접근.
     */
    const usernameText = formElement.current.username.value.trim();

    // 사용자가 입력한 텍스트에 대한 유효성 검사 진행
    if (!usernameText) {
      setMessage("아이디를 입력하셔야합니다.");
      setMessageCss(messageStyle.error);
      return;
    }

    if (usernameText.length <= 5 || usernameText.length >= 20) {
      setMessage("아이디는 최소 5글자, 최대 20글자 이내여야 합니다.");
      setMessageCss(messageStyle.error);
      return;
    }

    setMessage("사용 가능한 아이디입니다.");
    setMessageCss(messageStyle.granted);
  };

  return (
    <div>
      <form ref={formElement} onSubmit={handleSubmit}>
        <label htmlFor="userId">ID: </label>
        <input type="text" id="userId" name="username" />
        <button type="submit">입력</button>
      </form>
      <p style={messageCss}>Message: {message}</p>
    </div>
  );
};

export default UnControlledCompEx;

```

코드 1-1. UnControlledCompEx.jsx

![사진 1-1. UncontrolledCompEx 실행결과 1](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/1.png)

사진 1-1. UncontrolledCompEx 실행결과 1

![사진 1-2. UncontrolledCompEx 실행결과 2](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/2.png)

사진 1-2. UncontrolledCompEx 실행결과 2

![사진 1-3. UncontrolledCompEx 실행결과 3](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/3.png)

사진 1-3. UncontrolledCompEx 실행결과 3

위 코드에서 볼 수 있듯, Uncontrolled component의 경우 `useRef` 를 이용하여 `<form>` 또는 `<input>` 요소에 DOM으로 접근하는 방식을 취한다. 이벤트가 발생하면 이벤트 핸들러 내에서 `formElement.current` 와 같은 방식으로 참조하는 HTML 요소로부터 입력값을 가져와 유효성 검사 등의 작업을 수행하는 식이다. 

이러한 Uncontrolled component 방식의 경우 리액트의 상태를 이용하여 사용자 입력값에 접근, 관리하는 방식이 아니기에 사용자 입력값이 변하더라도 컴포넌트의 리렌더링을 트리거하지 않는다. 그렇기에 사용자 입력값과 DOM을 이용하여 가져온 입력값이 항상 일치한다는 보장이 없다. 리액트 컴포넌트 내에서 이 값을 여러 군데에 사용하는 경우에도 값 변경 시 여러 군데에 나뉘어 사용되고 있는 값들이 서로 일치한다는 보장도 없다. 따라서 만약 현재 사용자의 입력값과 리액트 컴포넌트에서 저장된 값과의 일치 및 여러 군데에 흩어져서 사용되고 있는 사용자 입력값들의 일치성이 중요할 때에는 부적절하다. 또한 사용자가 값을 입력할 때마다 실시간으로 그 값을 업데이트해야하는 경우에도 Uncontrolled 방식으로는 부적합할 것이다. 다만 리렌더링을 촉발시키지 않기에 잦은 리렌더링이 불필요한 상황이고, 입력값을 추출하여 간단한 작업을 할 때에는 이 방식을 사용하는 것이 적절할 수도 있다. 

## Controlled component

Controlled component는 사용자 입력값을 리액트의 state를 이용하여 관리하는 방식의 컴포넌트를 의미한다. 다음은 앞서 살펴본 예제를 기능은 똑같은 채로 Controlled component 방식으로 재구현한 코드이다.

```jsx
import { useState } from "react";

const ControlledCompEx = () => {

  const messageStyle = {
    error: {
      color: 'red',
    },
    granted: {
      color: 'green',
    },
  };

  const [ username, setUsername ] = useState('');
  const [ message, setMessage ] = useState('');
  const [ messageCss, setMessageCss ] = useState(null);

  /**
   * 
   * @param {Event} event 
   */
  const handleInputChange = event => {
    console.log(event.target.value);
    setUsername(event.target.value);
  };

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmit = event => {
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

코드 2-1. ControlledCompEx.jsx

실행 결과는 앞선 코드 1-1의 실행결과와 동일하다.

위 코드에서도 볼 수 있듯, Controlled component 방식에서는 사용자 입력 요소를 `useState` 등을 이용하여 리액트의 상태로 관리하는 것을 볼 수 있다. 

리액트의 상태는 그 값이 변경되면 리렌더링을 촉발시키기 때문에 사용자의 입력값이 변하더라도 항상 상태값과 일치한다는 특성을 지닌다. 그래서 사용자 입력값이 여러 군데에 흩어져서 사용되고 있더라도 항상 동일한 값으로 일치시켜 유지할 수 있어 이에 대해서는 개발자가 별도로 고려해야할 점이 없다는 장점이 있다. 이러한 이유로, Controlled component 방식은 사용자 입력값이 실시간으로 변하는 것을 이용해야할 때, 리액트의 상태로 관리하고자 할 때, 사용자 입력값과 상태값, 그리고 여러 군데 흩어져 있는 사용자 입력값의 일치성이 필요할 때에 유용하다. 

한 편 상태값이 변경될 때마다 컴포넌트의 리렌더링을 트리거하기 때문에 잦은 리렌더링으로 인한 성능 이슈를 고려할 수도 있겠다. 다만 웬만한 상황에서는 이로 인한 성능의 악영향이 거의 미미하다고 한다. 그럼에도 만약 잦은 리렌더링으로 인한 성능 이슈가 우려되거나 또는 실제로 이러한 성능 문제의 영향이 클 경우, `useMemo`, `useCallback` 등의 memoization을 적용하여 불필요한 리렌더링을 제거하는 것도 성능 이슈 해결의 한 방법이 된다. 

참고로, 잦은 리렌더링으로 인한 성능 이슈가 눈에 띌 정도로 발생하거나 그러한 일이 발생할 것으로 보이는 복잡하고 대규모인 프로젝트일 경우 이러한 성능 이슈를 줄이고 개발자 입장에서 form 요소를 더 쉽게 다루도록 해주는 [React Hook Form](https://react-hook-form.com/) 라이브러리라는 것도 있다고 하니 참고바란다. 

# FormData

앞선 내용에서는 주로 리액트 컴포넌트에서 `<input>` , `<form>` 등의 사용자 입력 관련 요소들을 어떻게 다룰지에 대한 방법에 대해 다뤄보았다. 여기서부터는 `<input>` , `<form>` 요소 자체와 관련된 여러 정보들을 정리하고자 한다. 

`<form>` 태그로 구성된 입력 폼은 HTML만으로도 서버에 입력 정보들을 제출할 수 있다. 하지만 이러한 사용자 입력값들을 자바스크립트로 쉽게 접근하고 AJAX로 요청하기에도 쉽게 하는 자바스크립트 내장 객체가 있는데, FormData 라는 객체이다. 다음은 이를 이용하여 `<form>` 태그 내 사용자 입력값들을 추출하고 이를 AJAX 요청 정보로도 활용하는 예제이다. 

```jsx
import { useRef, useState } from "react";

/**
 * @typedef {Object} UserResponse
 * @property {String} username
 * @property {String} message
 * 
 */

const FormDataEx = () => {

  const formElement = useRef(null);

  /**
   * @type {[UserResponse | null, *]}
   */
  const [ serverData, setServerData ] = useState(null);

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmitForm = event => {
    event.preventDefault();

    const formData = new FormData(formElement.current);
    const formDataJson = JSON.stringify(Object.fromEntries(formData.entries()));

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

코드 3-1. FormDataEx.jsx

![사진 2-1. FormDataEx.jsx 실행결과](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/4.png)

사진 2-1. FormDataEx.jsx 실행결과

![사진 2-2. FormDataEx 실행결과로 출력된 크롬 브라우저 콘솔창 메세지들](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/5.png)

사진 2-2. FormDataEx 실행결과로 출력된 크롬 브라우저 콘솔창 메세지들

위 코드를 보면, 우선 `useRef` 를 이용하여 `<form>` 요소를 DOM으로 직접 접근하였다. `handleSubmitForm` 이라는 이벤트 핸들러 내에서 이러한 form 요소를 토대로 `const formData = new FormData(formElement.current);` 와 같이 FormData 객체를 생성하였다. 이 객체에는 `<form>` 태그 내 모든 `<input>` 의 `name`, `value` 속성값들을 key-value 형태로 저장한 상태이다. 이를 알 수 있는 것이 바로 다음 코드이다.

```jsx
// FormData 객체 내 key, value 모두 출력하기
for (const [key, value] of formData) {
  console.log(`key: ${key}, value: ${value}`);
}
```

코드 3-2. FormData 객체 내 모든 key-value 데이터 출력하기

위 코드 3-1의 실행결과가 위 사진 2-2에서 보여지고 있다. 

참고로, FormData 객체 내에 `<input>` 태그의 값들이 제대로 들어가려면 `<input>` 태그에 `name` 속성이 반드시 존재해야 한다. 그렇지 않는 경우 해당 태그의 값이 FormData 객체에 제대로 반영되지 않는다. 

FormData 객체에는 기존의 `<form>` 태그 내 모든 사용자 입력 관련 요소들의 key-value 데이터를 추출하는 기능만 있는 것은 아니다. FormData 객체에는 다음의 메서드들이 존재한다.

- `append(name, value)` : `name` , `value` 필드 추가. 같은 `name` 이 이미 내부에 존재하는 경우, 기존 데이터는 그대로 놔둔 채 똑같은 `name` 의 새로운 값을 추가한다.
- `delete(name)` : FormData 객체 내 특정 `name` 값을 가지는 key-value 데이터 삭제
- `get(name)` : FormData 객체 내 특정 `name` 값을 가지는 프로퍼티의 value 값을 반환.
- `has(name)` : FormData 객체 내 특정 `name` 존재 여부를 boolean형으로 반환.
- `set(name, value)` : FormData 객체 내에 이미 존재하는 특정 `name` 의 `value` 값을 수정.

이 메서드들을 잘 활용하면 자바스크립트 상에서 추가하고자 하는 데이터도 추가하여 서버에 요청하는 등의 조금 더 복잡한 기능들도 구현할 수 있을 것이다. 

이러한 FormData 객체는 그 자체로 바로 AJAX 요청의 body로 넣어 요청할 수 있다. 

```jsx
fetch('...', {
  method: 'POST',
  body: new FormData(formElement),
});
```

코드 3-3. 예시 코드

그런데 이러한 FormData를 HTTP 요청에 싣고 서버로 전송하면 `Content-Type` 속성 값이 `multipart/form-data` 로 자동으로 지정되어 전송된다고 한다. 이 사실을 간과하고 백엔드에서 이 요청의 body 정보가 JSON 형식인 줄 알고 코드를 작성하면 의도치 않은 에러가 발생한다. 그래서 만약 `application/json` , 즉 JSON 형태로 전송하고자 한다면 위 코드 3-3의 방식 그대로 사용해선 안된다. 대신 앞선 코드 3-1에서처럼 FormData 객체를 JSON 형태의 객체로 변환한 후, 이를 문자열로 직렬화하여 전송하는 중간 과정이 필요하다. 

```jsx
const formData = new FormData(formElement.current);
const formDataJson = JSON.stringify(Object.fromEntries(formData.entries()));

fetch('http://localhost:8080/users', {
  method: 'POST',
  body: formDataJson,
  headers: {
    'Content-Type': 'application/json;charset=utf-8',
  },
})
```

코드 3-4. 코드 3-1의 코드 일부 발췌.

`Object.fromEntries(formData.entries())` 는 FormData 객체를 객체 리터럴(JSON)로 변환 및 반환하는 코드이다. 이렇게 JSON 형태로 변환된 FormData 객체를 `JSON.stringify(...)` 메서드에 대입한 결과물을 HTTP 요청 바디에 실어야 비로소 JSON 형태로 정보를 서버에 전송할 수 있게 된다. 

# input 요소의 value, defaultValue 속성과 onChange 이벤트

리액트에서는 `<input>` 요소와 그 속성 중 하나인 `value` 를 다룰 때에도 여러 방식들을 고려할 수 있다. 

- `value` 속성만 사용하는 경우
- `value` 속성과 `onChange` 이벤트를 함께 사용하는 경우.
- `defaultValue` 속성만 사용하는 경우

다음은 이 세 경우 모두 고려한 예제이다.

```jsx
import { useRef, useState } from "react";

/**
 * `<input>` 태그의 속성 value, defaultValue 및 onChange 확인용
 * 
 * @returns 
 */
const InputValueEx = () => {

  const formWithInputValueWithoutChange = useRef(null);
  const formWithInputDefaultValue = useRef(null);
  const [ displayInputValue, setDisplayInputValue ] = useState('');
  const [ tempInputValue, setTempInputValue ] = useState('');

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmitInputValue = event => {
    event.preventDefault();

    const textValue = formWithInputValueWithoutChange
      .current['inputValueNoChange'].value;
    setDisplayInputValue(textValue);
  };

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmitInputValueWithChange = event => {
    event.preventDefault();
    setDisplayInputValue(tempInputValue);
  };

  /**
   * 
   * @param {Event} event 
   */
  const handleInputTextChange = event => {
    console.log(event.target.value);
    setTempInputValue(event.target.value);
  };

  /**
   * 
   * @param {Event} event 
   */
  const handleSubmitInputDefaultValue = event => {
    event.preventDefault();
    
    const textValue = formWithInputDefaultValue.current.inputDefaultValue.value;
    setDisplayInputValue(textValue);
  };

  return (
    <div>
      <fieldset>
        <legend>input with value attr without onChange</legend>
        <form 
          onSubmit={handleSubmitInputValue} 
          ref={formWithInputValueWithoutChange}
        >
          <input type="text" value={'hi!'} name="inputValueNoChange" />
          <button type="submit">제출</button>
        </form>
      </fieldset>

      <fieldset>
        <legend>input with value attr with onChange</legend>
        <form onSubmit={handleSubmitInputValueWithChange}>
          <input 
            type="text" 
            value="hi2" 
            onChange={handleInputTextChange} 
          />
          <button type="submit">제출</button>
        </form>
      </fieldset>

      <fieldset>
        <legend>input with defaultValue attr</legend>
        <form 
          onSubmit={handleSubmitInputDefaultValue}
          ref={formWithInputDefaultValue}
        >
          <input 
            type="text" 
            name="inputDefaultValue"
            defaultValue={'hello!'} 
          />
          <button type="submit">제출</button>
        </form>
      </fieldset>

      <div className="box" style={{marginTop: '1em', padding: '1em'}}>
        <p>{displayInputValue}</p>
      </div>
    </div>
  );
};

export default InputValueEx;

```

코드 4-1. InputValueEx.jsx

![사진 3-1. 맨 위 입력란을 사용한 결과. `<input>` 요소에 `value` 속성만 사용한 경우이다. 사용자가 텍스트를 입력할 수 없다. ](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/6.png)

사진 3-1. 맨 위 입력란을 사용한 결과. `<input>` 요소에 `value` 속성만 사용한 경우이다. 사용자가 텍스트를 입력할 수 없다. 

![사진 3-2. 사진 3-1과 같이 실행한 경우 발생하는 에러 메시지. `<input>` 태그에 `value` 속성만 사용한 상태에서 이 `value` 속성값을 바꾸려고 시도할 때 뜬다. 위 메시지는 `value` 속성만 사용 시 해당 속성값도 바꿀려면 `onChange` 핸들러와 같이 사용하거나, `defaultValue` 속성으로 바꾸든가 아니면 read-only로만 사용하라는 내용이다. ](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/7.png)

사진 3-2. 사진 3-1과 같이 실행한 경우 발생하는 에러 메시지. `<input>` 태그에 `value` 속성만 사용한 상태에서 이 `value` 속성값을 바꾸려고 시도할 때 뜬다. 위 메시지는 `value` 속성만 사용 시 해당 속성값도 바꿀려면 `onChange` 핸들러와 같이 사용하거나, `defaultValue` 속성으로 바꾸든가 아니면 read-only로만 사용하라는 내용이다. 

![사진 3-3. 중간 입력란을 사용한 결과. `<input>` 요소에 `value` 와 `onChange` 핸들러를 같이 사용하였다. 다만 `value` 속성값에 고정된 값을 작성하는 경우 위와 같이 “안녕하세요”를 뒤에 작성해도 제일 마지막에 입력한 한 글자만 반영된다. 이 문제를 해결하려면 `value` 속성값을 고정된 값이 아닌 state 변수를 사용해야 한다. ](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/8.png)

사진 3-3. 중간 입력란을 사용한 결과. `<input>` 요소에 `value` 와 `onChange` 핸들러를 같이 사용하였다. 다만 `value` 속성값에 고정된 값을 작성하는 경우 위와 같이 “안녕하세요”를 뒤에 작성해도 제일 마지막에 입력한 한 글자만 반영된다. 이 문제를 해결하려면 `value` 속성값을 고정된 값이 아닌 state 변수를 사용해야 한다. 

![사진 3-4. 맨 아래 입력란을 사용한 결과. `<input>` 요소에 `defaultValue` 속성만 사용하였다. 앞선 두 입력란의 결과와 달리, `defaultValue` 속성을 사용하면 고정된 값을 사용해도 언제든 그 값을 변경할 수 있으며, `<input>` 요소에 `onChange` 핸들러를 반드시 할당하지 않아도 잘 작동한다. ](/images/2025-05-09/React-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C%20input%2C%20form%20%EC%9E%85%EB%A0%A5%EA%B0%92%20%EB%8B%A4%EB%A3%A8%EA%B8%B0%20-%20Controlled%20VS%20Uncontrolled%20Component%2C%20FormData/9.png)

사진 3-4. 맨 아래 입력란을 사용한 결과. `<input>` 요소에 `defaultValue` 속성만 사용하였다. 앞선 두 입력란의 결과와 달리, `defaultValue` 속성을 사용하면 고정된 값을 사용해도 언제든 그 값을 변경할 수 있으며, `<input>` 요소에 `onChange` 핸들러를 반드시 할당하지 않아도 잘 작동한다. 

사진 3-1에서는 `<input>` 요소에 `value` 속성만 사용한 입력란을 사용한 결과이다. 이 요소의 `value` 속성값을 변경하려고 하면 사진 3-2와 같이 `onChange` 핸들러와 같이 사용하거나 `defaultValue` 속성으로 바꾸거아 아니면 아예 `value` 속성값을 바꾸지 않고 read-only, 즉 읽기 전용으로만 사용하라는 에러 메시지가 뜬다. 해당 입력란에 아무리 글을 작성해도 입력 자체가 안되는 것도 확인할 수 있다. 따라서 `<input>` 요소에 `value` 속성만 사용할 것이라면 이 요소는 읽기 전용으로만 사용할 수 있음을 알 수 있다. 

사진 3-2에서는 `<input>` 요소에 `value` 속성과 `onChange` 핸들러를 같이 사용하였다. `value` 속성과 `onChange` 핸들러를 같이 사용하여 입력값의 변화를 고려한 것은 좋았으나, 문제는 `value` 속성에 고정된 값을 그대로 대입하는 경우 사용자가 사실상 글을 입력하지 못한다는 문제가 발생한다. 이 문제를 해결하려면 `value` 속성값에 고정된 값을 사용하는 게 아니라 리액트에서 제공하는 state 변수를 대입하도록 해야 한다. 즉 다음과 같은 형태로 사용해야 한다.

```jsx
const [ username, setUsername ] = useState('');
// ...

return (
  // ...
  <input 
    type="text" 
    id="userId" 
    name="username" 
    value={username}
    onChange={handleInputChange}
  />
  // ...
)
```

코드 4-2. 코드 2-1 일부 발췌

사진 3-3에서는 `<input>` 요소에 `defaultValue` 속성만을 사용한 입력란의 사용 결과가 담겨 있다. `defaultValue` 속성은 앞선 두 가지 경우들과 달리, `onChange` 핸들러가 필요하지 않으며, 해당 속성값에 고정된 값을 사용해도 사용자가 새로운 텍스트를 입력할 수 있다는 특징이 있다. 그래서 사실 앞선 두 경우들보다 사용하기 훨씬 더 쉽다고 볼 수 있다. 

즉, 유효한 경우는 `<input>` 태그에 `value` 속성과 `onChange` 핸들러를 같이 사용하는 경우와 `defaultValue` 속성만을 사용하는 두 가지 경우만이다. `value` 속성만을 사용하는 경우는 읽기 전용으로만 사용할 수밖에 없다. 

정리하면 다음과 같다. 

- `<input>` 태그에 `value` 속성만 사용하는 경우: 읽기 속성으로만 사용할 수 있으며, `value` 속성값을 원하는대로 바꿀 수 없다. 에러 메시지도 발생한다.
- `<input>` 태그에 `value` 속성 및 `onChange` 핸들러를 같이 사용하는 경우: 사용자가 자유롭게 다른 입력값으로 바꿔 입력할 수 있다. 다만 이를 위해서는 `value` 속성값을 고정된 값이 아닌 state 값을 사용하여 입력값을 유연하게 변경할 수 있도록 해줘야 한다.
- `<input>` 태그에 `defaultValue` 속성만 사용하는 경우 : 이 역시 사용자가 자유롭게 다른 입력값으로 바꿔 입력할 수 있다. 앞선 경우와 달리 `onChange` , state가 필수가 아니더라도, 심지어는 고정된 값을 넣어도 입력값을 자유롭게 바꿀 수 있다.

---

References

[1] React - Controlled Component VS Uncontrolled Component

[React Uncontrolled & Controlled Component, 프로처럼 사용하기::LEAPHOP TECH BLOG](https://blog.leaphop.co.kr/blogs/33/React_Uncontrolled___Controlled_Component__%ED%94%84%EB%A1%9C%EC%B2%98%EB%9F%BC_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)

[2] React - Controlled Component VS Uncontrolled Component

[[React 디자인 패턴] Controlled vs Uncontrolled Components](https://velog.io/@wlwl99/React-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-Controlled-vs-Uncontrolled-Components)

[3] React - Controlled Component VS Uncontrolled Component

[[React] 제어 컴포넌트(Controlled Component)와 비제어 컴포넌트(Uncontrolled Component)](https://dori-coding.tistory.com/entry/React-%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8Controlled-Component%EC%99%80-%EB%B9%84%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8Uncontrolled-Component)

[4] FormData 객체

[FormData 객체](https://ko.javascript.info/formdata)