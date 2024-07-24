---
title: "[JS][Web Components] Slots, Composition"
category: "javascript"
tag: ["web", "js", "javascript", "web components", "slot", "composition"]
---

# 렌더링 문제

```html
<todo-list>
    <span slot="todo-1">공부하기</span>
    <span slot="todo-2">집안일하기</span>
</todo-list>
```

예제 1-1

위 코드와 같이, 부모 요소로 `<todo-list>`라는 커스텀 요소가 있고, 그 아래로 자식 요소들이 있다. `<ul>` 안에 `<li>`가 있듯, 이렇게 부모 요소와 그 아래에 자식 요소가 있는 경우가 많을 것이다. 이 때, 부모 요소에서 자식 요소들의 컨텐츠들을 미리 렌더링하여 이 컨텐츠들을 부모 요소에서 사용하고 싶다고 해보자. 이럴 땐 어떻게 해야할까? 일반적으로, DOM 트리는 부모 요소부터 렌더링되고, 부모 요소가 렌더링될 시점에는 아직 자식 요소가 렌더링되지 않는다. light DOM은 어떻게든 된다고 생각하더라도, 만약 부모 요소가 shadow DOM을 사용하고, 자식 요소의 컨텐츠를 shadow DOM 내부에서 사용하고자 한다면? 

이럴 때 `<slot>` 요소를 사용하면, light DOM에 있는 자식 요소들의 컨텐츠들을 부모 요소인 shadow DOM 내부에 렌더링시켜 이용할 수 있다. 

`<slot>`에는 두 종류가 있다. 이름 있는 슬롯 (named slot)과 기본 슬롯(이름 없는 슬롯, default slot)이 있다. 

# named slot

`<slot>` 태그를 사용할 때에는 두 가지 형태로 사용할 수 있다. 

```html
<slot name="slotname"></slot>
<tag slot="slotname"></slot>
```

다음은 앞서 살펴본 `<todo-list>`에 대한 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        class TodoList extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});
                shadow.innerHTML = `
                    <style>
                        ::slotted(span) {
                            color: beige;
                            background-color: grey;
                        }
                        div {
                            margin: 10px;
                        }
                    </style>
                    <div>1. 
                        <slot name="todo-1"></slot>
                    </div>
                    <div>2. 
                        <slot name="todo-2"></slot>
                    </div>
                `;
            }
        }

        customElements.define("todo-list", TodoList);
    </script>
    <todo-list>
        <span slot="todo-1">공부하기</span>
        <span slot="todo-2">집안일하기</span>
    </todo-list>
</body>
</html>
```

예제 2-1

![예제 2-1 실행결과](/images/2024-04-21/js-web-components-slots-and-composition/1.png)

예제 2-1 실행결과

브라우저가 위 코드를 해석하고 실행시킬 때, light DOM에 있는 `<slot>` 요소를 가져와 shadow DOM에 있는 이름이 서로 맞는 `<slot>`에 삽입된다. 이렇게 두 영역 사이에 있는 `<slot>` 요소들을 하나로 합쳐 해석, 실행시키는 과정을 “합성(composition)”이라 한다. 

위 문서를 실행시키고, 개발자 도구에서 HTML 언어를 보면 다음과 같이 보인다. 

![예제 2-1 실행결과에서, 브라우저의 개발자 도구를 살펴본 모습.](/images/2024-04-21/js-web-components-slots-and-composition/2.png)

예제 2-1 실행결과에서, 브라우저의 개발자 도구를 살펴본 모습.

shadoe DOM 내부의 `<slot>` 요소 내부에 회색 글자로 `<span>`과 같이 작성되어 있는 것을 볼 수 있다. 즉, `<span slot=”todo-1”>`와 같은 light DOM 내 `<slot>` 요소들을 가져와 shadow DOM 내부의 `<slot>` 요소에 삽입된다. 이 결과를 보면 다음과 같을 것이다. 

```html
<todo-list>
    #shadow-root
    <div>1. 
        <slot name="todo-1">
	          <span slot="todo-1">공부하기</span>
        </slot>
    </div>
    <div>2. 
        <slot name="todo-2">
	          <span slot="todo-2">집안일하기</span>
        </slot>
    </div>
</todo-list>
```

예제 2-2

이렇게 이름이 서로 일치하는 `<slot>` 끼리 합쳐진 결과물을 “flattened(평평해진)” DOM이라 한다. 

그러나, 실제로 `<slot>`이 이동되어 삽입되는 것은 아니다. 자바스크립트 입장에서 document.querySelectorAll(’span’) 와 같이 사용하면 그대로 `<span>` 두 요소가 검색된다. 단지 flatterned DOM은 렌더링 및 이벤트 핸들링 관점에서만 바라본 것으로, 실제로는 요소들이 이동되는 것은 아니다(그대로 존재한다). 즉, 해석이 그렇게 된다는 것이지, 실제로 코드가 변경되는 것은 아니다. 

> shadow DOM 내부에서, slot된 (삽입된) 요소들에 스타일 규칙을 적용하고자 한다면 `::slotted(tag)` 선택자를 이용하여 지정할 수 있다.
> 

## `<slot>`이 적용되는 범위

`<slot>` 요소는 shadow DOM의 직접적인 자식 요소일 때에만 렌더링되어 composition이 일어난다. 위 예제의 경우, `<todo-list>`의 바로 한 단계 아래의 자식 요소의 `<slot>`만 합성 대상이 된다. 

예를 들어, 다음의 코드에서 `<slot>` 요소는 shadow DOM과 합성되지 않고 무시된다. 

```html
<todo-list>
		<div>
		    <!-- 아래 두 <slot> 요소들은 합성 과정이 일어나지 않는다.  -->
	      <span slot="todo-1">공부하기</span>
	      <span slot="todo-2">집안일하기</span>
    </div>
</todo-list>
```

예제 3-1

## 만약 `<slot>` 이름이 겹치는 요소들이 있다면?

만약 light DOM에 있는 몇몇 `<slot>` 요소들의 이름이 서로 겹치면, shadow DOM 내부의 해당 이름과 일치하는 `<slot>` 요소 내부에 차례대로 추가된다. 

```html
<body>
    <script>
        class TodoList extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});

                shadow.innerHTML = `
                    <slot name="title"></slot>
                    <ul>
                        <slot name="list"></slot>
                    </ul>
                `;
            }
        }
        customElements.define("todo-list", TodoList);
    </script>
    <todo-list>
        <h1 slot="title">My Todo list</h1>
        <li slot="list">공부하기</li>
        <li slot="list">집안일하기</li>
    </todo-list>
</body>
```

예제 4-1

![예제 4-1 실행결과](/images/2024-04-21/js-web-components-slots-and-composition/3.png)

예제 4-1 실행결과

![예제 4-1 실행 후의 개발자 도구에서 본 HTML 언어](/images/2024-04-21/js-web-components-slots-and-composition/4.png)

예제 4-1 실행 후의 개발자 도구에서 본 HTML 언어

위 사진을 보면 알겠지만, shadow DOM 내부의 `<slot name=”list”>` 요소 하나 안에 두 개의 `<li slot=”list”>` 요소들이 순서대로 삽입된 것을 확인할 수 있다. 

## default content

`<slot></slot>` 태그 내부에 어떤 컨텐츠를 삽입한다면, 그 내용은 default(fallback, 대비책이라고도 한다) 컨텐츠가 된다. 즉, 해당 슬롯과 매칭되는 슬롯이 없을 경우 대신 default 컨텐츠를 보여준다. 

```html
<body>
    <script>
        class TodoList extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});
                shadow.innerHTML = `
                    <style>
                        ::slotted(span) {
                            color: beige;
                            background-color: grey;
                        }
                        div {
                            margin: 10px;
                        }
                    </style>
                    <div>1. 
                        <slot name="todo-1"> hi1 </slot>
                    </div>
                    <div>2. 
                        <slot name="todo-2"> hi2 </slot>
                    </div>
                `;
            }
        }

        customElements.define("todo-list", TodoList);
    </script>
    <todo-list>
        <!--<span slot="todo-1">공부하기</span>
        <span slot="todo-2">집안일하기</span>-->
    </todo-list>
</body>
```

예제 5-1

![예제 5-1 실행결과](/images/2024-04-21/js-web-components-slots-and-composition/5.png)

예제 5-1 실행결과

위 코드에선, `<todo-list>` 태그 내에 주석 외엔 아무것도 입력되지 않았다. 그래서 shadow DOM 내부의 `<slot>` 태그와 매칭되는 요소를 찾지 못한다. 이 경우, `<slot>` 태그 내부에 있는 “hi1” 등이 대신 출력된다. 

# default slot (이름 없는 슬롯)

shadow DOM 내부에서 이름이 지정되지 않은 첫 번째 `<slot>` 요소는 default slot이 된다. 이 요소는 light DOM에서 composition되지 않은 나머지 요소들을 모두 한꺼번에 모아 composition한다. 

```html
<body>
    <script>
        class TodoList extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});
                shadow.innerHTML = `
                    <style>
                        ::slotted(span) {
                            color: beige;
                            background-color: grey;
                        }
                        div {
                            margin: 10px;
                        }
                        .else {
                            border: 1px solid black;
                            display: inline-block;
                            padding: 10px;
                        }
                    </style>
                    <div>1. 
                        <slot name="todo-1"></slot>
                    </div>
                    <div>2. 
                        <slot name="todo-2"></slot>
                    </div>
                    <div class="else">
                        <slot></slot>
                    </div>
                    <div class="else">
                        <slot></slot>
                    </div>
                `;
            }
        }
        customElements.define("todo-list", TodoList);
    </script>
    <todo-list>
        <p>굿굿</p>
        <span slot="todo-1">공부하기</span>
        <span slot="todo-2">집안일하기</span>
        <p>배드배드</p>
    </todo-list>
</body>
```

예제 6-1

![예제 6-1 실행결과](/images/2024-04-21/js-web-components-slots-and-composition/6.png)

예제 6-1 실행결과

![예제 6-1 실행 후 개발자 도구를 살펴본 결과](/images/2024-04-21/js-web-components-slots-and-composition/7.png)

예제 6-1 실행 후 개발자 도구를 살펴본 결과

위 코드와 사진 결과를 보면, shadow DOM에는 `<div class=”else”>` 태그 내부에 이름이 없는 `<slot></slot>` 태그가 있는 것을 볼 수 있는데, 실행 결과, `<todo-list>` 태그 내부에 `<slot>` 태그 또는 속성이 없는 모든 요소들 (위 코드에서 `<p>` 요소들)이 해당 slot 태그 내부에 모두 차례대로 삽입되어 렌더링 된 것을 확인할 수 있다. 

다만, 두 번째 이름없는 `<slot>` 부터는 무시되어 해당 슬롯에 아무것도 삽입되어 렌더링되지 않는다. 

# slot event

slot이 추가되거나 삭제되는 것에 대한 event가 있다. slotchange 이벤트이다. 다음은 이 slotchange 이벤트를 이용하여, slot이 추가 또는 삭제되어 변화가 일어날 때 slotchange 이벤트를 일으키도록 하여 확인해보는 예제이다. 

```html
<body>
    <todo-list>
        <h1 slot="title">My Todo list</h1>
    </todo-list>
    <script>
        class TodoList extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});

                shadow.innerHTML = `
                    <slot name="title"></slot>
                    <ul>
                        <slot name="list"></slot>
                    </ul>
                `;

                // shadow root 자체에는 이벤트 핸들러를 가질 수 없다. 
                // 따라서, shadow root의 첫 자식 요소를 이용한다. 
                shadow.firstElementChild.addEventListener('slotchange', 
                    e => alert("slot change : " + e.target.name)
                );
                shadow.children[1].addEventListener('slotchange',
                    e => alert("slot change : " + e.target.name)
                );
            }
        }
        customElements.define("todo-list", TodoList);

        const todo = document.querySelector("todo-list");
        
        // (삽입할 위치, 새로 삽입할 노드의 HTML 텍스트)
        todo.insertAdjacentHTML("beforeEnd", '<li slot="list">공부하기</li>');
        todo.querySelector('[slot="title"]').innerHTML = `내 할일 리스트`;
    </script>
</body>
```

예제 7-1

위 예제를 실행해보면, 총 두 번의 slotchange 이벤트가 일어난다. 첫 번째는 shadow.innerHTML 내부에 있는 `<slot name=”title”></slot>` 요소에 이 이름과 맞는 light DOM 내부의 slot 요소를 들여와 삽입할 때이다. 즉, shadow DOM에 light DOM 에서의 slot 요소가 추가된 상황이므로 slotchange 이벤트가 일어난다. 두 번째는, `todo.insertAdjacentHTML("beforeEnd", '<li slot="list">공부하기</li>');` 코드로 인해 새로운 slot 요소가 추가될 때이다. 

헷갈리지 말아야 할 것은, 이미 존재하는 `<slot>` 요소 내의 컨텐츠가 변경될 때에는 slotchange 이벤트가 일어나지 않는다는 것이다. 위 예제에서는 `todo.querySelector('[slot="title"]').innerHTML = `내 할일 리스트`;` 코드로 인해 해당 슬롯의 내용이 변경될 때가 이 경우이다. 

> 참고) 만약 slot 요소 내부의 컨텐츠의 변경사항까지 추적하고자 한다면, MutationObserver라는 것을 이용하면 된다고 한다. 다음은 참고 링크이다.
> 
> [Mutation observer](https://ko.javascript.info/mutation-observer)
> 

# slot api

만약 shadow tree가 `{mode: ‘open’}`을 통해 “열려”있다면, 특정 노드에 할당된 slot 요소를 얻거나 또는 반대로 특정 slot 요소에 할당된 DOM 노드의 정보를 얻을 수도 있다. 다음은 이와 관련된 메서드들이다. 

- `node.assignedSlot` : 해당 노드에 연결된 `<slot>` 요소를 반환한다.
- `slot.assignedNodes({flatten: true or false})` : 해당 슬롯에 연결된 노드들을 반환한다. flatten 옵션은 기본적으로는 false이다, true 인자를 대입하면, flattened DOM 내부의 깊숙한 곳까지 조사하여, 중첩된 컴포넌트들의 경우 중첩된 slot들을, 그 어떤 노드들도 발견하지 못하면 fallback 컨텐트를 반환한다.
- `slot.assignedElements({flatten: true or false})` : 해당 슬롯에 연결된 요소들을 반환한다. flatten 옵션은 위 설명과 동일하나, 오직 요소 노드들만을 반환한다는 차이점이 있다.

---

References

[1] [Shadow DOM slots, composition](https://ko.javascript.info/slots-composition)