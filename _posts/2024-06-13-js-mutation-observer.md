---
title: "[JS] Mutation Observer"
category: "javascript"
tag: ["web", "js", "javascript", "mutation observer"]
---

# DOM 변화 감지하기

`MutationObserver(callback)`는 DOM tree에서의 변화를 감지한 후, 변화가 감지되면 생성자로 전달된 콜백함수를 실행시켜주는 클래스이다. 이를 사용하는 방법은 대략 다음과 같다. 

```jsx
// MutationObserver 객체 생성. 
let mutationObserver = new MutationObserver(myCallback);

// MutationObserver 생성자에 전달할 콜백함수 정의.
// 해당 생성자에 전달될 매개변수는 2가지이다. 
// 첫 번째 매개변수: MutationRecord 객체
//    감지된 DOM tree 변경사항을 저장한 객체.
//    감지된 변화가 여러 개이면 여러 개의 변경사항이 저장될 수 있다. 
// 두 번째 매개변수: observer
//    이 콜백을 생성자의 인자로 전달받는 MutationObserver 객체 자기자신.
const myCallback = (mutationRecords, observer) => {
    // 콜백으로 실행시킬 코드.
}

// MutationObserver().observe() 메서드의 두 번쨰 인자로 넣을 옵션.
// 변화를 관찰할 DOM 노드에 연결된 다른 노드들도 그 변화를 감지하길 원하면
// 각 속성값에 true를 기입하면 된다. 
let config = {
    childList: true,
    subtree: true
};

// 대상 노드 targetNode를 observe의 첫 번째 인자로 대입한다. 
// 그러면 해당 노드의 변화를 감지하기 시작할 것이다. 
mutationObserver.observe(targetNode, config);

// 대상 노드의 변화 감지를 중단한다. 
mutationObserver.disconnect();
```

예제 1-1

`MutationObserver()` 클래스는 마치 DOM tree 변화를 감지하는 이벤트 리스너라고 생각해도 될 것이다. 물론 사용법에 있어서 이 둘은 차이가 있지만, 굳이 비유를 하자면 다음의 예제로 비유할 수 있을 것이다. 

```jsx
// DOMChanged는 DOM 트리 변화 이벤트를 의미하는 임의로 지은 이벤트명이다.
document.addEventListener('DOMChanged', myCallback);
```

예제 1-2

사실, 실제로 예전에는 DOM Mutation Event라는 이벤트가 존재했다고 한다. 그러나 성능과 안정성 이슈로 인해 해당 이벤트는 사라졌고, 대신 이를 대체할 MutationObserver 객체가 새로 생겨났다고 한다. 

## observe()

`observe()` 메서드의 두 번째 인자로 전달되는 config 인자에는 DOM에서의 변화를 감지할 대상 노드와 연결된 특정 노드들 중 무엇을 추가로 변화 감지할 것인지를 프로퍼티로 작성한 객체 리터럴을 전달하면 된다. 여기에 사용할 수 있는 프로퍼티에는 다음이 존재한다. 

| property | detail |
| --- | --- |
| childList | 대상 노드의 직속 자식 노드들의 변화를 감지하고자 할 때 |
| subtree | 대상 노드의 전체 하위 노드들의 변화 감지 |
| attributes | 대상 노드의 속성(attribute)들 |
| attributeFilter | 대상 노드의 속성들 중 감지를 원하는 속성들만을 배열로 담는다. ex) [’class’, ‘value’, ‘width’] |
| characterData | 대상 노드의 date를 관측. 여기선 텍스트 노드라 보면 된다.  |
| attributeOldValue | true로 설정 시, 변화가 감지된 속성의 새 값과 그 이전 값 모두를 콜백 함수에 전달한다. false 시 새 값만 전달한다. 이 속성을 사용하려면 attributes 속성을 먼저 명시해야 한다. 콜백 함수의 첫 번째 인자로 전달되는 MutationRecord 객체의 프로퍼티로 전달되는 듯 하다. |
| characterDataOldValue | true 시, data의 새 값과 이전 값 모두를 콜백에 전달한다. false 시 새 값만 전달한다. 역시 characterData 옵션이 먼저 설정되어있어야 한다. |

해당 프로퍼티들의 값은 모두 boolean값이다. 예를 들어 childList: true로 지정하면, 대상 노드의 직속 자식 노드들의 변화까지 감지하는 것으로 설정하겠다는 뜻이다. 

## callback

DOM 트리 내 특정 노드의 변화가 감지된 경우, 전달된 콜백 함수가 실행된다. 이 때 해당 콜백 함수의 첫 번째 매개변수로 전달될 MutationRecord 객체에는 감지된 변경사항들이 기록된다. 해당 객체에는 다음의 property들이 존재한다. 

| property | detail |
| --- | --- |
| type | 변화 종류. attributes, characterData, childList 중 하나이다.  |
| target | 변화가 감지된 노드. 노드 자체일 수도, attribute, text 노드일 수도 있다. |
| addedNodes/removedNodes | 추가 또는 삭제된 노드 |
| previousSibling/nextSibling | 추가 또는 삭제된 노드의 이전 또는 다음 형제 노드 |
| attributeName/attributeNamespace | 변경된 속성의 이름 또는 namespace(XML용) |
| oldValue | 텍스트, 속성 노드 변경 시 이전 값. 이 프로퍼티를 사용하려면 observe() 메서드의 두 번째 인자로 들어갈 config 인자에 attributeOldValue, characterDateOldValue 속성이 true로 설정되어 있어야 함. |

콜백함수의 첫 번째 인자로 전달되는 MutationRecord에는 변경 사항이 여러 개일 경우, 이를 배열 형태로 저장하기 위해 객체 리터럴 형태로 되어 있다. 해당 객체는 유사 배열 객체인 것으로 추측된다. 다음은 MutationObserver() 객체 생성자에 전달된 콜백 함수 내부에서 MutationRecord를 출력해본 예제 코드이다.

```jsx
function waitForRenderingAndExecuteFunc(func, targetNode) {
    let mutationOb = new MutationObserver((mutationRecords, observer) => {
        func();
        console.log(mutationRecords);
        console.log(mutationRecords[0].type);
        console.log(mutationRecords[0].target);
        observer.disconnect();
    });
    mutationOb.observe(targetNode, {childList: true});
}
```

예제 2-1

![1.PNG](/images/2024-06-13/js-mutation-observer/1.png)

예제 2-1 실행결과. 브라우저에서 실행하였다.

# 활용

## 렌더링 이슈 해결

MutationObserver를 이용하면 렌더링 이슈를 해결할 수 있다.

다음의 프로젝트 폴더가 있다. 

```
/project-root
    custom-elements.js
    helper.js
    main.html
    sub-element.html
    sup-element.html
```

위에 명시한 각 파일들의 코드들은 다음과 같이 생겼다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script type="module" src="custom-elements.js"></script>
</head>
<body>
    <div id="container"></div>
</body>
</html>
```

main.html

```jsx
import * as helper from './helper.js';

class SubElement extends HTMLElement {
    async connectedCallback() {
        this.innerHTML = await this.setInnerHTML();
    }

    async setInnerHTML() {
        return await fetch('./sub-element.html').then(res => res.text());
    }
}

class SupElement extends HTMLElement {
    async connectedCallback() {
        this.mutationOb;
        this.attachShadow({mode: 'open'}).innerHTML = await this.setInnerHTML();
        /*
        helper.waitForRenderingAndExecuteFunc(
            this.controlDOM.bind(this),
            this.shadowRoot.querySelector('sub-element')
        );*/
        this.controlDOM();
    }

    async setInnerHTML() {
        return await fetch('./sup-element.html').then(res => res.text());
    }

    controlDOM() {
        this.shadowRoot.querySelector('#sub-container > p').innerText = "controlled by super element";
        this.shadowRoot.querySelector('#sup-element > p').innerText += " also controlled by super element"; 
    }
}

function main() {
    customElements.define('sub-element', SubElement);
    customElements.define('sup-element', SupElement);

    document.querySelector('#container').append(document.createElement('sup-element'));
}

main();
```

custom-elements.js

```html
<sub-element></sub-element>
<div id="sup-element">
    <p>this is super element</p>
</div>
```

sup-element.html

```html
<div id="sub-container">
    <h3>hi nice to meet you</h3>
    <p>goooood</p>
</div>
```

sub-element.html

```jsx
export function waitForRenderingAndExecuteFunc(func, targetNode) {
    let mutationOb = new MutationObserver((mutationRecords, observer) => {
        func();
        console.log(mutationRecords);
        console.log(mutationRecords[0].type);
        console.log(mutationRecords[0].target);
        observer.disconnect();
    });
    mutationOb.observe(targetNode, {childList: true});
}
```

helper.js

웹 화면에 웹 요소들을 표시할 때, 웹 컴포넌트로 만들어 서로 간섭하지 않도록 독립적인 컴포넌트로 만들었다. 이를 위해 커스텀 요소로 정의하는 방법을 채택하였다. 또한, 각각의 커스텀 요소들을 구성할 실제 html 코드들을 커스텀 요소 클래스와 분리하여 따로 html 파일로 보관하고, 이를 커스텀 요소 내부에서 fetch()를 이용하여 가져오는 방식을 취하고 있다. 이는 커스텀 요소를 구성할 html 코드가 너무 길어질 경우, 자바스크립트 코드와 분리하여 관리하기 위해 택한 방식이다. 

하나의 커스텀 요소에는 상위 커스텀 요소(SupElement)와 하위 커스텀 요소(SubElement)로 다시 분리되어 있다. 상위 커스텀 요소가 하위 커스텀 요소를 품고 있는 구조이다. 이 역시 커스텀 요소를 구성하는 요소의 코드가 너무 길어질 경우, 이를 또 다시 커스텀 요소로 재구성하여 좀 더 코드를 파악하기 쉽게 하기 위함이다. 

위 코드들이 브라우저에서 실행되면, SubElement는 SupElement의 shadow DOM 내부에 렌더링될 것이다. 그리고 SupElement에서는 `controlDOM()` 메서드를 통해 SubElement 내 DOM 노드를 조작하려고 하고 있다. 

main.html을 실행해보면 다음의 결과를 얻는다.

![main.html 실행결과](/images/2024-06-13/js-mutation-observer/2.png)

main.html 실행결과

상위 요소에서 하위 요소의 DOM 노드를 조작하려고 했는데, 이는 실패하고 대신 에러 메시지만 얻었다. 이러한 에러가 발생한 원인으로, 상위 요소 내 shadow DOM 내부에 하위 요소가 렌더링이 다 되기 전에 하위 요소 조작 코드를 호출해서 발생한 문제인 것으로 보인다. (fetch로 코드를 가져오는 것과, 그 코드를 토대로 실제 DOM tree에 추가하는 작업이 동시에 일어나지 않으며, DOM tree에 추가되는 렌더링 과정에서 시간이 어느 정도 소요되기 때문에 이러한 현상이 발생한 것으로 추측된다) 즉, 아직 하위 요소가 렌더링되어 DOM 트리에 추가되지 않은 상태에서 하위 요소를 조작하려고 했기 때문에 발생한 문제이다. 

이러한 문제는 앞서 살펴본 MutationObserver를 이용하면 해결 가능하다. 앞서 살펴본 custom-element.js → SupElement 클래스 → connectedCallback 내부에 주석 처리된 코드를 주석 해제한 후, 마지막의 `this.controlDOM()` 코드를 주석 처리하고 다시 실행해보자. 

![수정된 main.html 실행결과](/images/2024-06-13/js-mutation-observer/3.png)

수정된 main.html 실행결과

이번에는 원하는 목적대로 되었음을 확인할 수 있다. 즉, 상위 요소를 정의하는 커스텀 클래스 내부에서 하위 요소의 DOM 노드를 조작하는 작업이 가능해졌다. 이는 `MutationObserver()` 객체의 콜백 함수에서 하위 요소가 DOM 트리에 추가되는지를 관찰하도록 하고, 추가되었으면 해당 하위 요소의 노드를 조작하는 코드를 콜백 함수 내에 넣어 실행했기에 가능해진 것이다. 이렇게 렌더링 순서 이슈를 `MutationObserver()`를 이용하여 해결할 수 있음을 알게 되었다. 

---

References

[1] [Mutation observer](https://ko.javascript.info/mutation-observer)

[2] [MutationObserver - Web API \| MDN](https://developer.mozilla.org/ko/docs/Web/API/MutationObserver)

[3] [DOM MutationObserver – reacting to DOM changes without killing browser performance. – Mozilla Hacks - the Web developer blog](https://hacks.mozilla.org/2012/05/dom-mutationobserver-reacting-to-dom-changes-without-killing-browser-performance/)

[4] [DOM 변화 감지하기 - MutationObserver & ResizeObserver](https://velog.io/@ggong/MutationObserver)

[5] [MutationRecord - Web API \| MDN](https://developer.mozilla.org/ko/docs/Web/API/MutationRecord)