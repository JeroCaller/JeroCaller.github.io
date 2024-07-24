---
title: "[JS] 커스텀 이벤트(Custom Event)"
category: "javascript"
tag: ["web", "js", "javascript", "custom event"]
---

‘click’과 같은 내장 이벤트들을 이용하여 핸들러를 할당해줄 수 있을 뿐만 아니라, 개발자가 직접 이벤트를 정의하여 사용할 수 있다. 이러한 커스텀 이벤트를 이용하면 개발자가 제작한 웹 요소의 일부에서 어떤 이벤트가 발생하고 이를 어떻게 처리했는지를 파악하기에 좋다. 

# Event 객체 생성하기

내장 이벤트들도 모두 객체로 표현되는데, 내장 이벤트 클래스의 최상위 클래스는 Event 클래스이다. 이 최상위 클래스를 이용하면 커스텀 이벤트 정의에 사용할 수 있다. 대략적인 문법 형태는 다음과 같다. 

```jsx
let myEvent = new Event(type[, option]);
```

- type : 이벤트 타입을 표현한 문자열. “click”과 같은 내장 이벤트나 “my-event”와 같은 커스텀 이벤트 모두 해당 인자로 전달 가능하다.
- options : 다음의 프로퍼티들을 포함하는 객체를 전달한다.
    - bubbles : true로 지정할 경우 해당 이벤트가 버블링된다. 아무런 값도 지정하지 않으면 false로 기본 설정된다.
    - cancelable : true인 경우, 브라우저의 기본 동작을 실행시키지 않고 취소시키는 event.preventDefault() 기능을 실행할 수 있게 된다. 역시 기본값은 false이다.

다음은 Event 객체를 생성하고 이를 등록한 예제이다. 

```html
<button id="mybutton">그냥 버튼</button>
<script>
    let event = new Event("click");
    const myButton = document.querySelector('#mybutton');
    myButton.dispatchEvent(event);

    document.addEventListener('click', (e) => {
        alert(`${e.target.tagName}가 클릭되었습니다.`);
    });
</script>
```

예제 1-1

![예제 1-1 초기 화면](/images/2024-05-13/js-custom-event/1.png)

예제 1-1 초기 화면

![예제 1-1 실행결과1. 빈 공간을 클릭했을 때.](/images/2024-05-13/js-custom-event/2.png)

예제 1-1 실행결과1. 빈 공간을 클릭했을 때.

![예제 1-1 실행결과2. 버튼을 클릭했을 때.](/images/2024-05-13/js-custom-event/3.png)

예제 1-1 실행결과2. 버튼을 클릭했을 때.

커스텀 이벤트임을 강조하고자 한다면 Event 클래스 대신 CustomEvent 클래스를 사용할 수 있다. 

```jsx
let myCustomEvent = new CustomEvent(type[, options]);
```

Event 클래스와 거의 동일하나 한 가지 차이점이 존재한다. 두 번째 인자에 전달될 객체에 detail이라는 프로퍼티를 추가하여 추가 정보를 덧붙일 수 있다. 

다음 예제는 CustomEvent 객체를 생성하고, detail 프로퍼티를 어떻게 활용하는지 보여주는 예제이다. 

```html
<h3 id="person-info">자바스님에 대한 정보입니다.</p>
<script>
    const title = document.getElementById('person-info');
    let infoEvent = new CustomEvent("showInfo", {
        detail: {name: "자바스", age: "20", job: "Web developer"}
    });
    title.addEventListener("showInfo", e => {
        const infoDiv = document.createElement('div');
        infoDiv.innerHTML = `<ul>
            <li>이름: ${e.detail.name}</li>
            <li>나이: ${e.detail.age}</li>
            <li>직업: ${e.detail.job}</li>
        </ul>`;
        document.body.append(infoDiv);
    });
    title.dispatchEvent(infoEvent);
</script>
```

예제 1-2

![예제 1-2 실행결과](/images/2024-05-13/js-custom-event/4.png)

예제 1-2 실행결과

또한 CustomEvent 클래스는 그 이름 자체에도 명시되어 있듯이, 해당 이벤트가 커스텀 이벤트임을 알려주는 기능도 가지고 있다. 

한 편, 위와 같이 Event, CustomEvent 클래스를 통해 생성한 커스텀 이벤트들은 `element.onclick`과 같이 on과 이벤트명을 붙여 이벤트 리스너를 등록할 수 없다. 즉, `on<event>` 문법은 오로지 내장 이벤트만을 위한 문법이다. 그래서 커스텀 이벤트들은 `addEventListener()` 메서드를 이용하여 이벤트를 처리해야 한다. 

> MouseEvent, KeyboardEvent와 같은 UI 이벤트들의 경우, 해당 이벤트 객체를 생성하려면 `new MouseEvent(”click”);` 와 같이 해당 이벤트의 내장 클래스를 이용하여 생성해야 한다. 이렇게 생성하면 각 이벤트에만 존재하는 특정 표준 프로퍼티들을 설정, 사용할 수 있기 때문이다. 이들을 단순히 `new Event();` 로 생성하면 해당 이벤트에만 존재하는 프로퍼티를 설정, 사용할 수 없다. 참고로 각 이벤트에만 존재하는 고유한 프로퍼티 설정은 `new MouseEvent(’click’, {bubbles: true, clientX: 200})`; 과 같이 두 번째 인자로 전달될 객체 내부에 명시하면 된다. 
사실 `event.clientX = 200;` 처럼 이벤트 객체에서 프로퍼티를 직접 사용해서 쓸 수도 있다.
> 

# dispatchEvent로 생성한 이벤트 등록하기

앞선 방법들로 생성한 커스텀 이벤트 객체들은 `element.dispatchEvent(event)` 메서드 인자로 전달하여 반드시 해당 이벤트를 등록해야 한다. 그래야 일반 내장 이벤트처럼 이벤트에 핸들러를 추가하여 해당 이벤트 발생 시 특정 기능을 수행하도록 하는 것이 가능해진다. 위 예제들에서도 이벤트 객체를 생성한 후에 항상 특정 요소에 `dispatchEvent()` 메서드를 호출하여 해당 이벤트 객체들을 등록한 것을 볼 수 있다. 

해당 메서드를 통해 이벤트를 등록함과 동시에 해당 이벤트가 발생한다. 마치 사용자가 웹에서 마우스 클릭 등의 상호작용을 통해 내장 이벤트가 발생한 것처럼 말이다. 

## event.isTrusted

event.isTrusted 프로퍼티는 해당 이벤트가 스크립트 내에서 정의되어 생성된 이벤트인지 아니면 실제로 사용자가 웹과의 상호작용으로 발생한 이벤트인지를 알려주는 프로퍼티이다. 해당 값이 true이면 사용자가 웹과의 상호작용으로 인해 발생한 이벤트라는 의미이고, false면 해당 이벤트가 스크립트에서 생성된 것임을 의미한다. 

```html
<button>클릭</button>
<script>
    const myButton = document.querySelector('button');
    let myEvent = new CustomEvent("nothingEvent", {bubbles: true});

    myButton.addEventListener("click", e => {
        console.log("버튼이 클릭되었습니다.");
        console.log("event.isTrusted: " + e.isTrusted);
        myButton.dispatchEvent(myEvent);
    });
    myButton.addEventListener('nothingEvent', e => {
        console.log('nothingEvent 이벤트가 발생하였습니다.');
        console.log("event.isTrusted: " + e.isTrusted);
    })
</script>
```

예제 2-1

![예제 2-1 실행결과. 콘솔창 결과](/images/2024-05-13/js-custom-event/5.png)

예제 2-1 실행결과. 콘솔창 결과

위 예제에서, 버튼에 두 가지 이벤트 리스너를 추가하였다. 하나는 내장 이벤트인 ‘click’에 대한 이벤트로, 해당 이벤트 리스너 내부의 끝에 개발자가 정의한 커스텀 이벤트인 ‘nothingEvent’ 객체를 `dispatchEvent()` 메서드를 통해 등록하고 있다. 또 하나의 이벤트는 nothingEvent라는 커스텀 이벤트이다. 사용자가 버튼을 클릭하면 먼저 “click” 이벤트에 대한 처리가 진행되고, 이후 “nothingEvent”에 대한 이벤트가 처리되는 방식이다. 위 결과를 보면 ‘click’ 이벤트에 대한 isTrusted 프로퍼티의 값이 true이고, ‘nothingEvent’ 이벤트에 대한 isTrusted 프로퍼티의 값은 false임을 알 수 있다. ‘click’은 내장 이벤트이며, 사용자가 버튼을 “클릭”함으로써 생성된 이벤트임에 반해, ‘nothingEvent’ 이벤트는 개발자가 스크립트 내에서 정의, 생성하고 여기에 이벤트 리스너를 달아놓은 경우이다. 이 구분을 isTruested 프로퍼티를 통해 할 수 있는 것이다. 

# 커스텀 이벤트의 기본 동작 취소하기

내장 이벤트가 처리될 때에는 브라우저에서 기본적으로 동작하도록 하는 ‘기본 동작’들이 실행된다. 제출 버튼 클릭 시 제출 기능이 자동적으로 실행되는 것, 링크 클릭 시 해당 링크에 연결된 웹 사이트로 이동하기 등이 그 예이다. 

만약 이벤트 기본 동작을 취소하고자 한다면 `event.preventDefault()` 메서드를 호출하면 된다. 이러한 이벤트 기본 동작 취소 작업은 내장 이벤트뿐만 아니라 커스텀 이벤트에도 똑같이 적용 가능하다. 후자의 경우, 이벤트 기본 동작이 취소되면 `element.dispatchEvent(event)` 메서드 호출 시 해당 메서드에서 false가 반환된다. 

사실 커스텀 이벤트에는 ‘기본 동작’이라는 것이 없지만, 앞서 설명한 `element.dispatchEvent(event)`의 반환값을 이용하면 커스텀 이벤트 발생 시 실행할 기본 동작을 설정해줄 수 있다. 

다음은 웹 화면에 코드 스니펫을 띄우고, 버튼을 누르면 해당 코드를 숨기거나 다시 보이게끔 할 수 있는 예제이다. 

```html
<style>
    .code-box {
        display: inline-block;
        padding: 1em;
    }
    .code-box > code {
        font-size: larger;
        font-weight: bold;
    }
    .code-box > code > pre {
        border: 1px solid blue;
        padding: 1em;
    }
</style>
<div class="code-box">
    <code>
        <pre>
function someGreatFunc() {
    console.log("아주 좋은 코드");
    return true;
}
        </pre>
    </code>
    <input type="button" value="코드 숨기기">
</div>
<script>
    const codeBox = document.querySelector('.code-box');
    const codeSnippet = codeBox.querySelector('code');
    const codeButton = codeBox.querySelector('input[type="button"]');

    codeButton.addEventListener('click', e => {
        let showHideEvent = new CustomEvent("showhide", {cancelable: true});
        // === #1 ===
        if (!codeSnippet.dispatchEvent(showHideEvent)) {
            console.log("기본 동작이 취소되어 동작하지 않습니다.");
            return;
        }
        // === #1 end ===

        // ===== #3 =====
        const hideLabel = "코드 숨기기";
        const showLabel = "코드 보기";
        let buttonValueAttr = codeButton.getAttribute('value');
        if (buttonValueAttr === hideLabel) {
            codeSnippet.style.display = "none";
            codeButton.setAttribute('value', showLabel);
        } else if (buttonValueAttr === showLabel) {
            codeSnippet.style.display = "inline";
            codeButton.setAttribute('value', hideLabel);
        }
        // ===== #3 end =====
    });
    codeSnippet.addEventListener("showhide", e => {
        // ===== #2 ======
        const question = `이벤트 기본 동작을 취소하겠습니까?`;
        if(confirm(question)) {
            e.preventDefault();
        }
    });
</script>
```

예제 3-1

![예제 3-1 실행 초기 화면](/images/2024-05-13/js-custom-event/6.png)

예제 3-1 실행 초기 화면

![예제 3-1 실행결과1. 버튼을 누르면 위와 같은 메세지 창이 뜬다. ](/images/2024-05-13/js-custom-event/7.png)

예제 3-1 실행결과1. 버튼을 누르면 위와 같은 메세지 창이 뜬다. 

![예제 3-1 실행결과2. 만약 “확인” 버튼을 누르면 개발자 도구의 콘솔창에 위와 같은 메시지가 뜨면서 코드 숨기기 또는 보이기 기능이 실행되지 않는다. ](/images/2024-05-13/js-custom-event/8.png)

예제 3-1 실행결과2. 만약 “확인” 버튼을 누르면 개발자 도구의 콘솔창에 위와 같은 메시지가 뜨면서 코드 숨기기 또는 보이기 기능이 실행되지 않는다. 

위 예제를 브라우저에서 실행시킨 후, 버튼을 누르면 알림창이 먼저 뜬 후, 거기서 “확인”을 누르면 코드 숨기기 및 보이기 기능이 동작하지 않고, “취소” 버튼을 누르면 해당 기능이 동작한다. 이 과정을 미루어 보아 위 예제에서는 다음과 같은 순서로 진행되었음을 알 수 있다. 

1. ‘click’ 내장 이벤트에 대한 핸들러 코드가 실행된다. 이 때 #1에서 `dispatchEvent()` 메서드가 호출됨을 알 수 있는데, 해당 메서드를 통해 ‘showhide’라는 커스텀 이벤트가 발생한다. 
2. 해당 커스텀 이벤트가 발생하면 이를 처리하는 코드(#2)가 동작한다. 여기서 커스텀 이벤트 기본 동작을 취소할 것인지 아니면 그대로 진행시킬 것인지 묻는 알림창이 뜬다. 만약 여기서 “확인” 버튼을 누르면 `event.preventDefault()` 메서드 호출로 인해 `codeSnippet.dispatchEvent(showHideEvent)` 메서드의 반환값이 false가 된다. 그러면 #1 코드의 로직에 의해 해당 핸들러의 동작이 중간에 취소되고 종료된다. 반면 “취소” 버튼을 누르면 해당 반환값이 true가 되어 핸들러 중단 로직을 무시하고 그 뒤에 있는 코드(#3)들을 마저 진행시킨다. 

이를 통해 또 알 수 있는 점은, 위 예제에서의 커스텀 이벤트 ‘showhide’에 대한 기본 동작이 #3 코드라는 점이다. #1 코드에서 if문에 `dispatchEvent()` 메서드가 호출되었고, 해당 커스텀 이벤트의 핸들러에 `event.preventDefault()` 메서드가 호출될 수 있는 로직이 있기에, `event.preventDefault()`가 실행되면 #1 코드의 if문의 조건문이 true가 되어 해당 핸들러 함수를 중단하고 나가게끔 설계되었기 때문이다. 그렇기에 그 아래에 있는 #3 코드가 ‘showhide’ 커스텀 이벤트에 대한 기본 동작이 되는 것이다. 

# 중첩 이벤트

이벤트는 동기적으로 처리된다. 즉, A라는 이벤트가 발생하여 이를 처리하고 있는 도중에 B라는 이벤트가 새로 발생하면, 브라우저는 A 이벤트에 연결된 핸들러를 계속 실행시켜 A 이벤트를 모두 처리한 다음에서야 B 이벤트를 처리하는 구조이다. 이러한 과정은 queue 자료구조를 통해 처리될 수 있다. 

그런데, 이벤트 내부에 이벤트가 있는 경우에는 이야기가 조금 달라진다. A라는 이벤트를 처리할 핸들러가 이를 처리하는 도중 핸들러 내부에서 B라는 또 다른 이벤트가 발생하면 그 즉시 A 이벤트에 대한 처리 과정을 중단하고 B 이벤트를 먼저 처리한다. 해당 이벤트가 모두 처리된 다음에야 A 이벤트 처리 과정을 재개한다. (이 경우에도, A, B 모두 서로에 상관없이 비동기적으로 처리되는 것이 아니라 A → B → A 순서대로 앞의 작업이 끝날 때까지 뒤의 작업이 대기하는 형태라서 이 상황도 동기적이라고 볼 수 있겠다)

다음 예제를 보면 이를 확인할 수 있다. 

```html
<button>클릭하여 이벤트 발생시키기</button>
<script>
    const myButton = document.querySelector('button');
        
    myButton.addEventListener('click', () => {
        console.log("클릭 이벤트 처리 시작");

        let myEvent = new CustomEvent("button-clicked", {bubbles: true});
        myButton.dispatchEvent(myEvent);

        console.log("클릭 이벤트 처리 끝");
    });
    document.addEventListener('button-clicked', () => console.log('중첩 이벤트 발생 및 처리'));
</script>
```

예제 4-1

```
클릭 이벤트 처리 시작
중첩 이벤트 발생 및 처리
클릭 이벤트 처리 끝
```

예제 4-1 실행 후 버튼 클릭 후 개발자 도구의 콘솔창 출력결과

위 실행결과를 보면 알 수 있듯, ‘click’ 이벤트를 처리하는 핸들러 함수가 실행될 때, 중간에 `myButton.dispatchEvent(myEvent);` 코드를 만나 `button-clicked` 라는 커스텀 이벤트를 발생시키게 된다. 그러면 ‘click’ 이벤트를 처리하는 핸들러 함수의 실행이 잠시 중단되고, `button-clicked` 커스텀 이벤트를 처리하는 다른 핸들러 함수가 실행된다. 그 후 실행이 완료되면 다시 ‘click’ 이벤트 핸들러 함수로 돌아와 남은 작업을 마저 끝내는 구조이다. 

이러한 상황은 dispatchEvent를 통해 중첩 이벤트가 발생할 때뿐만 아니라 이벤트 핸들러 내부에서 또 다른 이벤트가 발생하는 모든 상황에서 발생한다. 이러한 상황은 사실 앞서 살펴본 “코드 숨기거나 보이기” 예제인 예제 3-1에서 미리 경험하였다. 해당 예제에서 처음에는 ‘click’ 이벤트에 대한 핸들러 함수가 실행되다가 중간에 `dispatchEvent()` 메서드 호출 코드를 만나 커스텀 이벤트가 발생하고, 이로 인해 해당 커스텀 이벤트의 핸들러 함수가 먼저 실행되고 해당 작업이 모두 종료된 뒤에야 ‘click’ 이벤트 핸들러 함수가 다시 실행되는 구조이다. 

만약 중첩 이벤트에 대해 외부 이벤트 핸들러가 모두 실행된 뒤에 중첩 이벤트 핸들러가 실행되게끔 하고 싶다면 다음의 선택지 중 하나를 선택하면 되겠다. 

- 중첩 이벤트 발생 코드를 핸들러 함수 중간이 아닌 맨 뒤에 배치한다.
- 중첩 이벤트 발생 코드를 `setTimeout()` 함수로 감싼다. 그러면 외부 이벤트 핸들러가 먼저 실행된 이후에 중첩 이벤트 핸들러가 실행된다.

다음은 앞선 예제 4-1에서의 중첩 이벤트를 `setTimeout()` 함수로 감싸서 ‘click’ 이벤트 핸들러가 모두 실행된 다음에 ‘button-clicked’ 커스텀 이벤트 핸들러가 실행되도록 설계한 예제이다. 

```html
<button>클릭하여 이벤트 발생시키기</button>
<script>
    const myButton = document.querySelector('button');
        
    myButton.addEventListener('click', () => {
        console.log("클릭 이벤트 처리 시작");

        let myEvent = new CustomEvent("button-clicked", {bubbles: true});
        setTimeout(() => myButton.dispatchEvent(myEvent));

        console.log("클릭 이벤트 처리 끝");
    });
    document.addEventListener('button-clicked', () => console.log('중첩 이벤트 발생 및 처리'));
</script>
```

예제 4-2

```
클릭 이벤트 처리 시작
클릭 이벤트 처리 끝
중첩 이벤트 발생 및 처리
```

예제 4-2 실행결과

---

References

[1] [커스텀 이벤트 디스패치](https://ko.javascript.info/dispatch-events)