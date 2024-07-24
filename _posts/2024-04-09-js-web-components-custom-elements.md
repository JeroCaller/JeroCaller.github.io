---
title: "[JS][Web Components] Custom Elements"
category: "javascript"
tag: ["web", "js", "javascript", "web components", "custom elements"]
---

`<a>, <div>` 등 HTML에는 built-in(기본) 요소들이 내장되어 있다. 그런데 개발자가 직접 HTML 요소를 정의하거나, 기존의 기본 HTML 요소를 확장하여 사용할 수도 있다. 커스텀 요소를 정의할 때에는 클래스로 정의해야 한다. 

개발자가 직접 정의하여 사용할 수 있는 커스텀 요소에는 두 가지 종류가 있다. 

1. 자율적 커스텀 요소 (Autonomous custom elements) : 개발자가 처음부터 끝까지 모두 새롭게 정의할 수 있는 커스텀 요소. HTMLElement 추상 클래스를 상속받아 만들 수 있다. 
2. 커스텀 내장 요소 (Customized built-in elements) : 기존에 이미 내장되어 있는 HTML 요소를 확장하여 정의. 기존의 `<button>` 요소를 확장하고자 한다면 HTMLButtonElement 클래스를 상속받아 커스텀하면 된다. 

# Autonomous custom elements : 처음부터 끝까지 새로운 요소 정의하기

앞서 언급했듯, 아예 새로운 요소를 정의하고자 할 땐, HTMLElement 추상 클래스를 상속받는 클래스를 정의하는 것으로 시작한다. 이 때, 해당 클래스 내부에 선택적으로 특정 메서드들을 정의하면 해당 요소가 문서에 추가 또는 삭제되거나, 해당 요소의 속성이 변경될 때 취할 동작 등을 지정할 수 있다. 

다음은 HTMLElement 추상 클래스를 상속받아 커스텀 요소를 정의하는 클래스 내부 형태이다. 

```jsx
class MyElement extends HTMLElement {
    construtor() {
        super();
        // 해당 요소 생성 시 초기화할 코드.
    }
    
    connectedCallback() {
        // 해당 요소가 문서에 추가될 때 브라우저가 이 메서드를 호출함.
        // 또는 HTML parser(해석기)가 해당 요소를 감지할 때 호출됨.
        // 따라서, 해당 요소가 문서에 추가될 때 실행시킬 코드를 여기에 삽입.
        // 만약 해당 요소가 반복적으로 추가 또는 삭제되면 그 때마다 매번 호출될 수 있다.
    }
    
    disconnectedCallback() {
		    // 해당 요소가 문서에서 삭제될 때 브라우저가 이 메서드를 호출함.
		    // 따라서 해당 요소가 문서에서 삭제될 때 실행할 코드를 여기에 작성.
		    // 해당 요소가 반복적으로 추가, 삭제되면 그 때마다 매번 호출될 수 있음.
    }
    
    static get observedAttributes() {
        // 중간에 속성값이 변경되는 것을 감지하고자 하는 속성명들을 배열로 삽입.
        return ['attr1', 'attr2', ...];
    }
    
    attributeChangedCallback(name, oldValue, newValue) {
        // observedAttributes() 메서드 내에 전달된 속성들의 값이 변경될 때 호출됨.
        // 따라서 특정 속성값이 변경될 때 실행할 코드를 여기에 작성.
        // name : 변경된 속성명.
        // oldValue : 변경되기 전의 속성값.
        // newValue : 변경된 후의 속성값.
    }
    
    adoptedCallback() {
        // 해당 요소가 새 문서로 이동되었을 때 호출됨.
        // document.adoptNode에 일어난다고 함. 
    }
}
```

위 클래스 내부에 적은 메서드들은 모두 선택적으로 정의할 수 있다. 즉, 원치 않는 메서드는 정의하지 않아도 된다. 그리고, 위 클래스 내부에는 다른 프로퍼티, 메서드도 추가할 수 있다. 

위처럼 클래스를 이용하여 요소를 정의하였다면, 이를 등록하는 과정도 필요하다. 다음의 코드를 이용하면 된다. 

```jsx
customElements.define("my-element", MyElement);
```

`customElements.define()`의 첫 번째 인자로는 HTML에서 해당 요소를 사용할 때 쓸 태그명을 문자명으로 입력한다. 위 코드처럼 작성하면 HTML 문서에서 `<my-element></my-element>` 태그로 사용 가능하다. 그리고 두 번째 인자로는 앞서 HTMLElement 추상 클래스를 상속받아 정의된 커스텀 요소 클래스명을 대입하면 된다. 

첫 번째 인자로 전달할 커스텀 HTML 태그명에는 하이픈(-) 기호가 포함되어야 한다. 이는 내장 HTML 태그명과의 충돌을 피하기 위함이다. 

HTML문서가 렌더링될 때, 커스텀 요소 태그를 브라우저가 해석할 때 해당 커스텀 요소의 인스턴스가 생성된다. 

이렇게 정의한 커스텀 요소는 자바스크립트 내에서 `document.createElement(’my-element’)` 와 같이 DOM 트리에 추가할 수도 있다. 

## `connectedCallback()` 만을 이용한 커스텀 요소 정의 및 사용 예

다음은 “스탑워치”를 구현하는 커스텀 요소의 예제이다. 여기서는 앞서 설명한 메서드들 중 `connectedCallback()` 메서드만을 정의하여 사용해보았다. 

먼저 HTML 문서이다. 

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
    <script src="custom-element.js"></script>
    <stop-watch id="my-stopwatch"></stop-watch>
    <input type="button" value="pause" id="pause-stopwatch">
    <script src="pause-stopwatch.js"></script>
</body>
</html>
```

다음은 custom-element.js의 내용이다. `<stop-watch>` HTML 태그를 표현하는 커스텀 요소를 정의하는 코드이다. 

```jsx
class StopWatchElement extends HTMLElement {
    passedSeconds = 0;
    stopwatchId;

    tick() {
        this.innerHTML = this.passedSeconds;
        this.passedSeconds += 1;
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }

    connectedCallback() {
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }
}

customElements.define("stop-watch", StopWatchElement);

```

다음은 pause-stopwatch.js의 내용이다. 웹 문서 내 버튼을 누르면 스탑워치를 중지하는 기능이다.

```jsx
class pauseStopWatch {
    constructor() {
        this.pauseButton = document.querySelector('#pause-stopwatch');
        this.stopwatch = document.querySelector('#my-stopwatch');
        this.pauseStopWatchEvent();
    }

    pauseStopWatchEvent() {
        this.pauseButton.addEventListener("click", () => {
            clearTimeout(this.stopwatch.stopwatchId);
        })
    }
}

new pauseStopWatch();
```

![예제1-1~3 실행결과](/images/2024-04-09/js-web-components-custom-elements/1.png)

예제1-1~3 실행결과

위 HTML 문서를 실행하면 위와 같은 구성의 웹 페이지가 나타난다. 기본적으로는 숫자가 1초에 1씩 증가한다. “pause” 버튼을 누르면 해당 숫자의 증가 작업이 중단된다. 

만약 브라우저가 HTML, JS를 해석할 때 `customElement.define()` 코드보다 `<stop-watch>` 즉, 커스텀 HTML 태그를 먼저 렌더링하면, 에러는 나지 않는다. 다만 해당 태그가 어떤 요소인지 브라우저는 아직 알지 못하는 시점이다. 이 시점에서는 해당 태그가 정의되지 않은(undefined) 태그로 인식된다. 참고로, CSS에서는 이러한 정의되지 않은 요소를 `:not(:defined)` css 선택자로 접근하여 스타일 지정을 할 수 있다. 커스텀 HTML 태그를 렌더링한 후에 `customElement.define()` 코드를 만나 렌더링하게 되면, 그제서야 정의되지 않은 요소로 인지된 커스텀 태그가 커스텀 태그로 인지된다. 이 때, 해당 메서드를 통해 커스텀 요소 클래스의 인스턴스가 생성된 후, `connectedCallback()`이 호출되기 때문이다. 참고로, 이 때는 `:defined` 선택자로 스타일 지정할 수 있다. 

다음은 커스텀 요소 관련 정보를 확인할 수 있는 메서드이다. 

- `customElements.get(name)` : name 인자로 전달된 커스텀 태그 이름에 해당하는 커스텀 요소 클래스를 반환.

## 속성 변경 감지하게 하기

앞서 언급했듯, `static get observedAttributes()` 메서드를 이용하면 이후에 특정 속성값의 변경을 감지하게 할 수 있으며, 그 때 실행할 코드를 `attributeChangedCallback()` 메서드 내부에 작성하여 실행시킬 수 있다. 

앞서 구현한 스탑워치는 중단 버튼을 누른 후에 다시 재개할 수 없다. 이번에는 속성 변경 감지 기능을 이용하여 중단 후에도 재개할 수 있게 변경해보았다. 

다음은 일부가 변경된 HTML 문서이다. `<stop-watch>` 태그 내에 `ispaused=”false”` 속성만 추가되었다. 

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
    <script src="custom-element2.js"></script>
    <stop-watch id="my-stopwatch" ispaused="false"></stop-watch>
    <input type="button" value="pause" id="pause-stopwatch">
    <script src="pause-stopwatch2.js"></script>
</body>
</html>
```

다음은 custom-element2.js 코드이다. 

```jsx
class StopWatchElement extends HTMLElement {
    passedSeconds = 0;
    stopwatchId;

    render() {
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }

    tick() {
        this.innerHTML = this.passedSeconds;
        this.passedSeconds += 1;
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }

    connectedCallback() {
        this.render();
    }

    static get observedAttributes() {
        return ["ispaused"];
    }

    attributeChangedCallback(name, oldValue, newValue) {
        if (name === "ispaused") {
            if (newValue === "true") {
                clearTimeout(this.stopwatchId);
            } else if(oldValue === "true" && newValue === "false") {
                this.render();
            }
        }
        
        // 인수들의 값을 확인하기 위한 코드.
        console.log("=================");
        console.log(`name: ${name}`);
        console.log(`oldValue: ${oldValue}`);
        console.log(`newValue: ${newValue}`);
        console.log("=================");
        
        /*
        // 버그 코드. 버튼을 누르면 오히려 2씩 증가하는 괴이한 현상 발생.
        if(this.getAttribute("ispaused") === "true") {
            clearTimeout(this.stopwatchId);
        } else {
            this.render();
        }*/
    }
}

customElements.define("stop-watch", StopWatchElement);

```

다음은 버튼을 누르면 스탑워치를 중단 또는 재개하는 기능이 담긴 pause-stopwatch2.js 코드이다.

```jsx
class pauseStopWatch {
    constructor() {
        this.pauseButton = document.querySelector('#pause-stopwatch');
        this.stopwatch = document.querySelector('#my-stopwatch');
        this.pauseStopWatchEvent();
    }

    pauseStopWatchEvent() {
        this.pauseButton.addEventListener("click", () => {
            if(this.stopwatch.getAttribute("ispaused") === "false") {
                // 시계 정지
                this.pauseButton.setAttribute("value", "restart");
                this.stopwatch.setAttribute("ispaused", "true");
            } else {
                // 시계 재작동
                this.pauseButton.setAttribute("value", "pause");
                this.stopwatch.setAttribute("ispaused", "false");
            }
        })
    }
}

new pauseStopWatch();
```

실행결과는 아까와 똑같으나, “pause” 버튼을 누르면 해당 버튼의 라벨이 “restart”로 변경되며, 여기서 또 버튼을 누르면 중단되었던 스탑워치가 다시 작동된다. 

버튼을 클릭할 때 `<stop-watch>`의 ispaused 속성값을 변경시키는 이벤트 리스너를 생성하였다. 그러면 StopWatchElement 커스텀 요소 클래스의 `observedAttributes()` 메서드에 의해 해당 속성값 변화가 감지되며, 그 후 바로 `attributeChangedCallback()` 메서드가 실행된다. 해당 메서드 내부에는 변경된 속성값에 따라 스탑워치를 중단시키거나 재개시키는 코드를 삽입함으로써, 스탑워치 중단 및 재개 기능을 구현하였다. 

# 커스텀 내장 요소

이번에는 기존의 내장되어 있는 요소를 확장시키는 커스텀 내장 요소에 대해 살펴보겠다. 

다음은 기존의 `<button>` 태그를 확장하여 재정의한 예제 코드이다. 

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
        class NoticeButton extends HTMLButtonElement {
            constructor() {
                super();
                let msg = `사이트에 오신 것을 환영합니다.`;
                this.addEventListener('click', () => alert(msg));
            }
        }

        customElements.define('notice-button', NoticeButton, {extends: 'button'});
    </script>
    <button is="notice-button">안내문 보기</button>
</body>
</html>
```

![예제 3-1 실행결과](/images/2024-04-09/js-web-components-custom-elements/2.png)

예제 3-1 실행결과

내장 HTML 태그에는 그에 해당하는 클래스가 존재한다. 위 예제를 예로 들면, `<button>` 태그에 해당하는 클래스는 HTMLButtonElement이다. 이를 상속받는 자식 클래스에서 원하는 코드를 작성함으로써 기존 버튼 요소를 확장시킬 수 있다. 

커스텀 내장 요소를 등록할 때 사용하는 `customElements.define()` 메서드는 이전의 자율적 커스텀 요소 때와는 다른 점이 하나 있다. 세 번째 인자에는 확장시키고자 하는 기존 태그명을 문자열로 `{extends: ‘tagName’}` 형태로 입력해야 한다. 

이 후, HTML 에서 사용할 때에는 기존 내장 태그 (`<button>`)를 작성한 후, 해당 태그의 is 속성에 커스텀 내장 요소 태그명을 입력하면 사용할 수 있다. 

커스텀 내장 요소를 사용하면 좋은 장점에는 다음이 있다. 

1. 기존의 내장 요소가 가지고 있는 원래 기능들을 그대로 사용할 수 있다. 따라서 해당 기능들을 개발자가 일일이 정의하지 않아도 된다. 
2. 시맨틱 태그의 경우, 이를 확장하여 사용하면 검색 엔진이 해석하게 할 수 있다. 

# 렌더링 순서

HTML 해석기가 문서를 해석할 때, 부모 요소가 먼저 해석되어 해당 요소가 생성되고, 생성된 요소가 DOM 트리에 추가된다. 이 이후에 자식 요소가 똑같은 과정을 거친다. 

다음은 새로운 커스텀 요소를 하나 생성하고 이를 HTML에 정의한 코드이다. 

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
    <script src="rendering-order.js"></script>
    <user-profile>자바스</user-profile>
</body>
</html>
```

```jsx
class UserProfile extends HTMLElement {
    connectedCallback() {
        let msg = `사용자 정보
        사용자 이름: ${this.innerHTML}`;
        alert(msg);
    }
}

customElements.define("user-profile", UserProfile);
```

![예제 4-1~2 실행결과1](/images/2024-04-09/js-web-components-custom-elements/3.png)

예제 4-1~2 실행결과1

![예제 4-1~2 실행결과2](/images/2024-04-09/js-web-components-custom-elements/4.png)

예제 4-1~2 실행결과2

위 코드를 브라우저 상에서 실행하면, 알림창이 뜨지만, `<user-profile>` 태그 안에 작성한 “자바스” 텍스트가 알림창에 뜨지 않는다. 해당 알림창을 닫은 후에야 웹 문서에 해당 텍스트가 뜬다. 

웹 브라우저가 HTML 문서를 해석할 때 먼저 `<user-profile>` 태그를 만나 해석한다. 이 때 UserProfile 클래스의 인스턴스가 생성되며, 이 요소가 DOM 트리에 추가되므로 뒤이어 `connectedCallback()` 메서드가 호출된다. 이 시점에선 아직 `<user-profile>`의 자식 텍스트 요소인 “자바스” 요소가 해석되지도 않은 상태이다. 그러므로 `connectedCallback()` 메서드 내부의 this.innerHTML에는 “자바스”가 저장되지 않는다. 

이 문제를 해결하려면 두 가지 방법이 있다.

- 커스텀 요소 클래스 내부에 사용할 값을 자식 요소가 아닌 커스텀 태그 속성의 값으로 사용한다. `<user-profile mytext=”자바스”>`처럼. 이 경우에는 `connectedCallback()`에서 “자바스” 값을 인식할 수 있게된다.
- `setTimeout()` 호출 스케줄러를 이용한다.

다음은 앞선 예제를 `setTimeout()` 함수로 해결한 코드이다. 자바스크립트 코드만 변경하였다. 

```jsx
class UserProfile extends HTMLElement {
    connectedCallback() {
        setTimeout(() => {
            let msg = `사용자 정보
            사용자 이름: ${this.innerHTML}`;
            alert(msg);
        });
    }
}

customElements.define("user-profile", UserProfile);
```

![예제 4-3 실행결과](/images/2024-04-09/js-web-components-custom-elements/5.png)

예제 4-3 실행결과

아까와 달리 이번에는 알림창에 자식 텍스트 요소가 잘 출력됨을 볼 수 있다. `<user-profile>` 태그의 자식 요소까지 인식된 다음에 `setTimeout()` 메서드가 실행되었기 때문이다. 

만약 setTimeout을 사용하는 커스텀 요소 태그를 중첩하여 사용하면 어떻게 될까? 

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
        class UserProfile extends HTMLElement {
            connectedCallback() {
                setTimeout(() => alert(`${this.id} setTimeout으로 연결됨`));
                alert(`${this.id} 연결됨`);
            }
        }
        customElements.define("user-profile", UserProfile);
    </script>
    <user-profile id="parent">
        <user-profile id="child"></user-profile>
    </user-profile>
</body>
</html>
```

위 예제를 실행하면 알림창이 총 4번 뜬다. 결과는 다음과 같이 차례대로 뜬다.

- parent 연결됨
- parent setTimeout으로 연결됨.
- child 연결됨
- child setTimeout으로 연결됨.

위 예제에서도 부모 요소부터 렌더링되는 것을 확인할 수 있다. 

## connectedCallback vs constructor

만약 `<stop-watch id=”my-watch”>`와 같이 커스텀 태그에 속성(attribute)도 같이 쓰여진 경우, 브라우저는 `<stop-watch>` 부분부터 해석하게 된다. 이 때, 해당 커스텀 요소 클래스 StopWatchElement의 인스턴스가 생성된다. 즉, 이 때 constructor 생성자가 호출된다. 그러나 이 시점에서는 아직 브라우저가 그 뒤에 있는 `id=”my-watch”` 와 같은 속성들을 해석하지 않은 상태이다. 따라서 만약 생성자 안에 `getAttribute()` 와 같은 메서드를 호출하면 null을 반환받는다. 

즉, 커스텀 요소가 먼저 해석되어 커스텀 요소 객체가 먼저 생성되며, 그 이후에 속성이 렌더링된다. constructor가 먼저 호출되며, 그 이후에 해당 요소가 DOM 트리에 추가되므로 그제서야 `connectedCallback()` 메서드가 호출된다. 

---

References

[1] [Custom elements](https://ko.javascript.info/custom-elements)