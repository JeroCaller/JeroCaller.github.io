---
title: "[JS][Web Components] Shadow DOM and Event"
category: "javascript"
tag: ["web", "js", "javascript", "shadow dom", "dom", "event"]
---

# event retargeting

shadow DOM은 light DOM과 분리되어 웹 컴포넌트만의 독립적인 기능을 구성할 때 쓰인다. 그런데 만약 마우스 클릭과 같은 이벤트가 웹 컴포넌트 속 shadow DOM 내부에서 발생한다면, 이 이벤트를 어떻게 봐야할까? light DOM의 입장에서는 shadow DOM 내부 사정을 전혀 알지 못한다. 이 논리대로라면 shadow DOM 내부에서 발생한 이벤트를 light DOM에서는 전혀 인지하지 못한다는 뜻이다. 

shadow DOM의 캡슐화 특성을 유지하면서도 shadow DOM 내부의 이벤트를 처리할 수 있도록 하기 위해, 브라우저에서는 이벤트를 리타켓팅(retargeting)한다. 즉, 이벤트가 처음 발생한 요소를 shadow DOM 내부의 어떤 요소가 아닌, 그 shadow DOM을 가지는 shadow host 요소(light DOM에 노출된 요소)로 재설정하는 것이다. 

만약 개발자가 `<my-message>`라는 커스텀 요소를 정의하였고, 해당 요소의 shadow tree 내부에 `<button>` 태그를 생성하였으며, 이 버튼에 ‘click’ 이벤트에 대한 핸들러를 설정했다고 해보자. 사용자가 해당 요소를 클릭하면 원래라면 당연히 shadow tree 내부의 `<button>` 요소를 누른 것이기에 `<button>` 요소가 이벤트 target, 즉 이벤트가 처음 발생한 요소로 지정된다. 그러나 light DOM에서는 해당 shadow tree를 가지고 있는 `<my-message>`라는 요소에서 처음 해당 이벤트가 발생했다고 인식한다. 다음은 이를 증명하는 예제이다.

```html
<script>
    customElements.define('my-message', class extends HTMLElement {
        connectedCallback() {
            this.attachShadow({mode: 'open'});
            this.shadowRoot.innerHTML = `<button>
                알림 버튼
            </button>`;

            // shadow DOM 내부에서 click 이벤트에 대한 이벤트 핸들러 추가.
            this.shadowRoot.firstElementChild.addEventListener('click', 
            e => alert(`shadow DOM에서의 target: ${e.target.tagName}`));
        }
    });
</script>

<my-message></my-message>

<script>
    const MsgButton = document.querySelector('my-message');
    // light DOM에서 click 이벤트에 대한 이벤트 핸들러 추가.
    MsgButton.addEventListener('click', 
    e => alert(`document에서의 target: ${e.target.tagName}`));
</script>
```

예제 1-1

![예제 1-1 첫 실행화면](/images/2024-05-11/js-web-components-shadow-dom-and-event/0.png)

예제 1-1 첫 실행화면

![예제 1-1 실행결과1](/images/2024-05-11/js-web-components-shadow-dom-and-event/1.png)

예제 1-1 실행결과1

![예제 1-1 실행결과2](/images/2024-05-11/js-web-components-shadow-dom-and-event/2.png)

예제 1-1 실행결과2

위 예제를 브라우저에서 실행했을 때, 해당 버튼을 누르면 shadow DOM에서의 event target은 `<button>` 요소로 잡히지만, document 입장에서는 `<my-message>`가 첫 이벤트가 발생한 지점으로 인식되는 것을 알 수 있다. 

이러한 사실로 인해, 굳이 light DOM에서 shadow DOM 내부 사정을 몰라도 shadow DOM 내부에서 발생한 이벤트를 다룰 수 있다는 점에서 편리성이 있다. 

# slot 요소에서는 이벤트 retargeting이 되지 않는다

반면, slot된 요소에서 발생한 이벤트는 retareting되지 않는다. slot 요소는 그 자체로는 light DOM에 존재하기 때문이다. 

```html
<script>
    customElements.define('some-thing', class extends HTMLElement {
        connectedCallback() {
            this.attachShadow({mode: 'open'});
            this.shadowRoot.innerHTML = `
            <style>
                .small-box {
                    display: inline-block;
                    border: 1px solid black;
                    padding: 1em;
                    margin: 1em;
                }
            </style>
            <div id="box-container">
                <div class="small-box">
                    <p>From shadow DOM</p>
                </div>
                <div class="small-box">
                    <p>
                        <slot name="label-name"></slot>
                    </p>
                </div>
            </div>`;

            this.shadowRoot.children[1].addEventListener('click', 
            e => alert(`shadow DOM에서의 event target : ${e.target.tagName}`));
        }
    });
</script>

<some-thing>
    <span slot="label-name">자바스</span>
</some-thing>

<script>
    const myCustomElement = document.querySelector('some-thing');

    myCustomElement.addEventListener('click', 
    e => alert(`light DOM에서의 event target: ${e.target.tagName}`));
</script>
```

예제 2-1

![예제 2-1 첫 실행화면](/images/2024-05-11/js-web-components-shadow-dom-and-event/3.png)

예제 2-1 첫 실행화면

![예제 2-1 실행결과1. “From shadow DOM” 상자를 클릭했을 때 나온 결과](/images/2024-05-11/js-web-components-shadow-dom-and-event/4.png)

예제 2-1 실행결과1. “From shadow DOM” 상자를 클릭했을 때 나온 결과

![예제 2-1 실행결과2. “From shadow DOM” 상자를 클릭했을 때 나온 결과](/images/2024-05-11/js-web-components-shadow-dom-and-event/5.png)

예제 2-1 실행결과2. “From shadow DOM” 상자를 클릭했을 때 나온 결과

![예제 2-1 실행결과3. 오른쪽의 “자바스” 상자를 클릭했을 때 나온 결과](/images/2024-05-11/js-web-components-shadow-dom-and-event/6.png)

예제 2-1 실행결과3. 오른쪽의 “자바스” 상자를 클릭했을 때 나온 결과

![예제 2-1 실행결과4. 오른쪽의 “자바스” 상자를 클릭했을 때 나온 결과](/images/2024-05-11/js-web-components-shadow-dom-and-event/7.png)

예제 2-1 실행결과4. 오른쪽의 “자바스” 상자를 클릭했을 때 나온 결과

위 예제와 실행결과를 보면, `<slot>` 요소가 전혀 없는 왼쪽 상자를 클릭하면 shadow DOM에서의 event target과 light DOM에서의 event target이 다르다. 이는 retargeting이 되었다는 뜻이다. 반면, `<slot>` 요소가 있는 오른쪽 상자를 클릭하면, shadow DOM에서의 event target과 light DOM에서의 그것이 span 요소로 동일한 것을 볼 수 있다. 이를 통해 event retargeting이 되지 않았음을 알 수 있다. 

# 버블링의 관점에서 보기

앞서 살펴본 이벤트 리타겟팅 현상을 버블링으로 살펴보기 위해 flatten DOM을 사용해보자. 앞서 살펴본 예제 2-1을 flatten DOM으로 보면 다음과 같다. 

```html
<some-thing>
    #shadow-root
    <div id="box-container">
	      <div class="small-box">
	          <p>From shadow DOM</p>
	      </div>
	      <div class="small-box">
	          <p>
	              <slot name="label-name">
	                  <span slot="label-name">자바스</span>
	              </slot>
	          </p>
	      </div>
    </div>
</some-thing>
```

만약 `<slot>` 요소가 있고, 그 안에서 이벤트가 발생하면 `<slot>` 내부에 발생한 이벤트가 `<slot>` 요소로 버블링되고, 그 상위 요소로도 버블링이 진행된다. 

slot 요소 없이, shadow DOM에서 발생한 이벤트가 리타겟팅 되는 경우, shadow DOM에서 발생한 이벤트가 light DOM으로까지 버블링되는 과정이 전제된다. 이 과정 중에서 앞서 언급한 리타겟팅이 일어나는 것이다. 

만약 shadow DOM에서 일어난 어떤 이벤트가 light DOM까지 버블링되길 원한다면, 이벤트 객체의 bubbles와 composed 속성이 true값으로 설정되어야 한다. click, mousedown과 같은 대부분의 이벤트 객체에는 composed 속성이 true로 설정되어 있다고 한다. 

이벤트 객체의 `event.composedPath()` 메서드에서는 이벤트가 버블링하면서 지나치는 모든 요소들을 배열로 반환한다. 다음은 앞선 예제 2-1에 `event.composedPath()` 메서드를 삽입하여 이벤트가 버블링되면서 만나는 요소들을 순서대로 반환한 배열을 출력하여 살펴보는 예제이다. 

```html
<script>
    customElements.define('some-thing', class extends HTMLElement {
        connectedCallback() {
            this.attachShadow({mode: 'open'});
            this.shadowRoot.innerHTML = `
            <style>
                .small-box {
                    display: inline-block;
                    border: 1px solid black;
                    padding: 1em;
                    margin: 1em;
                }
            </style>
            <div id="box-container">
                <div class="small-box">
                    <p>From shadow DOM</p>
                </div>
                <div class="small-box">
                    <p>
                        <slot name="label-name"></slot>
                    </p>
                </div>
            </div>`;

            this.shadowRoot.children[1].addEventListener('click', e => {
                alert(`shadow DOM에서의 event target : ${e.target.tagName}`);
                console.log(e.composedPath());  // 이벤트가 버블링되면서 지나치는 모든 요소들을 배열로 반환.
            });
        }
    });
</script>
<!-- 이하 예제 2-1과 동일 -->
```

예제 3-1

![예제 3-1 실행결과. 요소를 클릭했을 때 발생한 이벤트가 버블링하면서 지나친 수많은 요소들을 순서대로 출력한 것을 볼 수 있다. 맨 위 결과는 “From shadow DOM” 상자를 클릭했을 때, 아래 결과는 “자바스” 상자를 클릭했을 때 얻은 결과이다.](/images/2024-05-11/js-web-components-shadow-dom-and-event/8.png)

예제 3-1 실행결과. 요소를 클릭했을 때 발생한 이벤트가 버블링하면서 지나친 수많은 요소들을 순서대로 출력한 것을 볼 수 있다. 맨 위 결과는 “From shadow DOM” 상자를 클릭했을 때, 아래 결과는 “자바스” 상자를 클릭했을 때 얻은 결과이다.

배열 맨 왼쪽부터 처음에 이벤트가 발생한 요소가 나열되어 있고, 오른쪽으로 갈수록 상위 요소로 버블링하면서 만나는 상위 요소들이 나열된 것을 볼 수 있다. 이렇게 해당 메서드를 이용하여 버블링되면서 만나는 모든 요소들을 확인해볼 수 있음을 알게되었다. 

---

References

[1] [Shadow DOM and events](https://ko.javascript.info/shadow-dom-events)