---
title: "[JS][Web Components] Shadow DOM Styling"
category: "javascript"
tag: ["web", "js", "javascript", "web components", "shadow dom", "dom"]
---

Shadow DOM에서는 스타일링을 위해 `<style>`과 `<link rel=”stylesheet” href=”stylesheet.css”>` 태그를 포함할 수 있다. 이 때, `<link>` 태그를 사용하면, 불러올 스타일시트가 HTTP상에서 캐시(cache)된다. 즉, 같은 스타일을 여러 컴포넌트가 사용할 때마다 재 다운로드하지 않아도 된다는 뜻이다. 

보통은 웹 컴포넌트 내부의 shadow dom을 위한 스타일은 해당 shadow tree에만 적용되고, 문서(document) 스타일은 해당 웹 컴포넌트의 외부에만 영향을 끼친다. 하지만 컴포넌트 외부에서 컴포넌트 스타일을 지정할 수 있는 방법들도 존재하며, 그 반대도 있다. 

# 용어 정리

- shadow host : shadow tree를 포함하는 요소(element). 요소이므로, shadow host 자체는 외부(document)에 노출되어 있다고 할 수 있다. 즉, shadow host 자체는 light DOM에 존재한다. 따라서 light DOM에 정의된 스타일의 영향을 받을 수 있다.

# :host 선택자

:host 선택자를 통해 shadow host 요소를 선택할 수 있다. 

```html
<body>
    <template id="tmp">
        <style>
            :host {
                display: inline-block;
                position: absolute;
                left: 45%;
                top: 30%;
                border: 1px solid black;
                padding: 10px;
            }
        </style>
        <slot></slot>
    </template>
    <script>
        class MsgBox extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'}).append(tmp.content.cloneNode(true));
            }
        }
        customElements.define("msg-box", MsgBox);
    </script>
    <msg-box>good</msg-box>
</body>
```

예제 1-1

![예제 1-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/1.png)

예제 1-1 실행결과

위 예제에서, :host 선택자가 포함된 `<style>` 태그는 `<template id=”tmp”>` 태그 내부에 들어있다. 이 `<template>`가 `<msg-box>` 내부의 shadow DOM에 삽입된다. 이 때, shadow DOM 내부에 위치하게 된 `:host` 선택자가 shadow DOM 외부에 있는 `<msg-box>` 요소를 선택하게 된다. 즉, `:host` 선택자는 shadow DOM에서 그 shadow DOM을 포함하는 light DOM 요소를 선택하는 것이다. 

# document에서 shadow host에 직접 스타일 지정하기

shadow tree를 가지는 shadow host(위 예제에선 `<msg-box>`)는 light DOM에 존재하는 요소이다. 따라서 document에서의 CSS에 영향을 받을 수 있다. 따라서 다음의 선택자도 가능하다. 

```html
<style>
	msg-box {// 스타일 규칙}
</style>
```

위 선택자를 통해 해당 요소(`<msg-box>`)에 스타일을 지정시킬 수 있다. 

다음은 shadow host를 직접 선택자로 지정하여 스타일을 적용시키는 예제이다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        msg-box {
            border: 1px solid black;
            background-color: aquamarine;
            padding: 1em;
            position: absolute;
            left: 5em;
            top: 5em;
        }
    </style>
</head>
<body>
    <script>
        class MsgBox extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'}).innerHTML = `<slot></slot>`;
            }
        }
        customElements.define("msg-box", MsgBox);
    </script>

    <msg-box>good</msg-box>
</body>
</html>
```

예제 2-1

![예제 2-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/2.png)

예제 2-1 실행결과

# :host(selector)

`:host(selector)`는 괄호 안의 (selector)에 해당하는 shadow host를 지정하는 선택자이다. 

다음은 속성과 속성값이 각각 `shape=”circle”`인 shadow host에만 따로 스타일을 적용하는 예제이다. 

```html
<body>
    <template id="tmp">
        <style>
            :host([shape='circle']) {
                border-radius: 50%;
            }
            :host {
                display: inline-block;
                border: 1px solid black;
                padding: 1em;
                margin: 1em;
            }
        </style>
        <slot></slot>
    </template>
    <script>
        class MsgBox extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'}).append(tmp.content.cloneNode(true));
            }
        }
        customElements.define("msg-box", MsgBox);
    </script>
    <div id="container">
        <msg-box>good</msg-box>
        <msg-box shape="circle">good</msg-box>
    </div>
</body>
```

예제 3-1

![예제 3-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/3.png)

예제 3-1 실행결과

위 예제를 보면, `<style>` 태그 내부에 `:host([shape=”circle”])`이란 선택자가 존재하는데, 이 선택자는 결국 `<msg-box shape=”circle”>`에만 해당 스타일을 적용하게 된다. 즉, shape=”circle” 속성이 아닌 다른 `<msg-box>`에는 해당 스타일이 적용되지 않는다. 

# :host-context(selector)

`:host-context(selector)` 는 괄호 안에 넘겨진 선택자와 일치하는 shadow host 또는 그의 부모 요소가 있는지를 찾고 이를 지정하는 선택자이다. 

다음은 커스텀 요소인 `<msg-box>`의 부모 요소 `<div id=”container”>`를 지정하여 스타일을 지정한 예제이다. 

```html
<body>
    <template id="tmp">
        <style>
            :host {
                display: inline-block;
                border: 1px solid black;
                padding: 1em;
                margin: 1em;
                background-color: aquamarine;
            }
            
            :host-context(#container) {
                border: 5px dashed red;
                width: 90%;
                height: 100%;
            }
        </style>
        <slot></slot>
    </template>
    <script>
        class MsgBox extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'}).append(tmp.content.cloneNode(true));
            }
        }
        customElements.define("msg-box", MsgBox);
    </script>
    <div id="container">
        <msg-box>good</msg-box>
    </div>
</body>
```

예제 4-1

![예제 4-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/4.png)

예제 4-1 실행결과

위 예제에서, 커스텀 요소인 `<msg-box>`의 부모 요소로 `<div id=”container”>` 요소가 있다. `<style>` 태그 내부에서는 `:host-context(#container)` 선택자를 이용하여 사실상 `<div id=”container”>` 요소를 지정하여 스타일을 적용하였다. 

# slot 요소에 스타일 적용하기

슬롯(slot)된 요소는 light DOM으로부터 온다. 그래서 웹 컴포넌트 내 스타일이 아닌 document에서 지정한 스타일을 적용받는다. 다음 예제를 통해 이 사실을 확인할 수 있다. 

```html
<body>
    <style>
        span {
            padding: 1em;
            color:crimson;
        }
    </style>
    <script>
        customElements.define('person-info', class extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'});
                this.shadowRoot.innerHTML = `
                    <style>
                        span {
                            background-color: blue;
                        }
                    </style>
                    <p>이름: <slot name='person-name'></slot></p>
                `;
            }
        });
    </script>
    <person-info>
        <span slot="person-name">자바스</span>
    </person-info>
</body>
```

예제 5-1

![예제 5-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/5.png)

예제 5-1 실행결과

위 예제에서, `<body>` 태그 내부에서의 `<style>`과, 커스텀 요소 내부의 shadow root 내부에서의 `<style>`이 있다. 그런데 결과를 보면 `<body>` 태그 내부의 `<style>` 태그의 스타일만 적용되었고, shadow root 내부에 정의한 스타일 규칙은 전혀 적용되지 않은 것을 볼 수 있다. 

만약 컴포넌트 내부에서 슬롯된 요소의 스타일을 꾸미고 싶다면 두 가지 방법을 고려해볼 수 있다. 

1. CSS 상속을 이용한다. `<slot>` 요소의 자식 요소로 `<span>` 과 같은 자식 요소를 삽입하고, 그 안에 텍스트 등의 컨텐츠를 삽입한 후, 부모 요소인 `<slot>`의 스타일을 지정하면, CSS 상속에 의해 자식 요소들도 해당 스타일을 상속받는다. 
2. `::slotted(selector)` 선택자를 이용한다. 

다음은 첫 번째 방법인 “CSS 상속”을 이용한 예제이다. 

```html
<body>
    <style>
        span {
            padding: 1em;
            color:crimson;
        }
    </style>
    <script>
        customElements.define('person-info', class extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'});
                this.shadowRoot.innerHTML = `
                    <style>
                        slot[name="person-name"] {
                            background-color: blue;
                            font-style: italic;
                        }
                    </style>
                    <p>이름: <slot name='person-name'></slot></p>
                `;
            }
        });
    </script>
    <person-info>
        <div slot="person-name"><span>자바스</span></div>
    </person-info>
</body>
```

예제 5-2

![예제 5-2 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/6.png)

예제 5-2 실행결과

위 예제에서는 `<div slot="person-name">` 가 부모 요소가 되고, 자식 요소로 `<span>` 요소를 사용하였다. 그리고 shadow root 내부에서는 부모 요소에 스타일을 지정하고 있다. 이러면 CSS 상속에 의해 같은 스타일 규칙이 자식 요소에도 똑같이 적용된다. 그래서 위 실행결과를 보면 `font-style: italic;` 스타일이 적용되어 글꼴이 기울어진 것을 확인할 수 있다. 

그러나 이 방법은 배경색 등을 지정하는 데에는 사용할 수가 없다는 단점이 존재한다. 위 예제에서도 `background-color: blue;` 스타일 규칙이 적용되지 않은 것을 확인할 수 있다. 즉, 몇몇 스타일 속성들은 CSS 상속이 되지 않는다는 단점이 있다. 

이러한 단점을 해결할 수 있는 것이 `::slotted(selector)` 선택자이다. 의사 클래스(pseudo-class)이며, 이 선택자는 다음의 조건을 모두 만족하는 요소를 가리킨다. 

- light DOM에서 온 슬롯된 요소. 이 때, slot의 이름은 상관없이 `<slot>`이기만 하면 모두 적용되며, 단, 해당 요소의 자식 요소들은 가리키지 않는다. 즉 slot된 요소만을 가리킨다. 
이 선택자로는 `::slotted(div span)`, `::slotted(div) span`과 같이 슬롯된 요소의 자식 요소를 가리킬 수 없다.
- `::slotted(selector)`의 괄호 안 선택자와 일치하는 요소만을 가리킨다.

다음은 앞선 예제를 `::slotted(selector)` 선택자로 바꾼 예제이다. 

```html
<body>
    <style>
        span {
            padding: 1em;
            color:crimson;
        }
    </style>
    <script>
        customElements.define('person-info', class extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'});
                // ::slotted(div span), ::slotted(div) span 모두 적용안됨.
                this.shadowRoot.innerHTML = `
                    <style>
                        ::slotted(div) {
                            background-color: blue;
                            font-style: italic;
                        }
                    </style>
                    <p>이름: <slot name='person-name'></slot></p>
                `;
            }
        });
    </script>
    <person-info>
        <div slot="person-name"><span>자바스</span></div>
    </person-info>
</body>
```

예제 5-3

![예제 5-3 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/7.png)

예제 5-3 실행결과

이번에는 CSS 상속을 이용했던 이전 예제와는 달리, 파란 배경색이 적용된 것을 볼 수 있다. 

앞서 언급했듯, 슬롯된 요소만 선택자가 가리킬 뿐, 슬롯된 요소의 자식 요소들까지 가리키지 않기 때문에 위 예제에서 파란 배경색은 `<span>자바스</span>` 가 아닌 `<div slot="person-name">` 라는 슬롯 요소에 직접 적용되었다. 

# 커스텀 CSS 속성으로 shadow DOM 스타일 적용하기

지금까지는 shadow DOM에서 light DOM에 있는 요소를 지정하여 스타일을 적용하는 여러 가지 방법들에 대해 살펴보았다. 이번에는 반대로 light DOM에서 컴포넌트 내부의 shadow DOM을 지정하여 스타일을 적용시키는 방법에 대해 살펴보겠다. 

## CSS custom property

CSS에는 custom property(커스텀 속성) (또는 CSS variable(CSS 변수), cascading variables라고도 한다)이 존재하는데, 이는 개발자가 직접 CSS 속성에 사용될 속성값을 변수를 정의하여 담을 때 사용된다. 

이 커스텀 속성은 맨 앞에 dash `--` 기호 두 개를 붙여 이름을 정의하여 사용한다. 

```css
body {
    --my-background-color: skyblue;
    --my-text-color: brown;
}
```

이 커스텀 속성을 사용할 때에는 var() 함수와 함께 사용한다. 다음은 커스텀 속성을 정의하고 이를 사용하는 예제이다. 

```css
<body>
    <style>
        body {
            --my-background-color: skyblue;
            --my-text-color: brown;
        }
        .box {
            background-color: var(--my-background-color, blue);
            color: var(--my-text-color, red);
        }
    </style>
    <div class="box"><span>hi</span></div>
</body>
```

예제 6-1

![예제 6-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/8.png)

예제 6-1 실행결과

위 예제에서의 `<style>` 내부를 보면, body 스타일 규칙에 dash 기호 두 개가 맨 앞에 붙은 커스텀 속성 두 개가 정의되어있다. 맨 첫 번째는 배경색을 담는 커스텀 속성으로, 그 값은 skyblue로 지정되어있다. 두 번째 커스텀 속성은 글자색을 담는 속성으로, 그 값으로 brown이 지정되어 있다. 이러한 커스텀 속성들은 .box 스타일 규칙 내부에서 var() 변수와 함께 쓰였다. 

var() 함수의 첫 번째 인자는 앞서 정의한 커스텀 속성을 넣는다. 만약 해당 커스텀 속성을 찾지 못한다면 default로 적용시킬 속성값을 두 번째 인자에 넣으면 된다. 위 예제에서, 만약 두 커스텀 속성을 브라우저가 찾지 못하면, 두 번째 인자로 대입된 blue와 red가 각각 적용되어 파란색 배경에 빨간색 글자로 스타일이 지정될 것이다. 참고로 두 번째 인자는 선택적 인자로, 아무것도 대입하지 않아도 된다. 

이렇게 커스텀 속성을 이용하면 프로젝트 규모가 커질 때 같은 속성값을 사용하는 여러 속성들에 대해 나중에 일괄적으로 속성값을 바꿔야 할 때가 와도 커스텀 속성의 값만 바꾸면 되기에 매우 편리한 점이 있다. 이러한 점에서, 커스텀 속성은 프로그래밍 언어의 “변수”와 같은 역할을 한다. 

## CSS 커스텀 속성을 이용하여 light DOM에서 shadow DOM 스타일 적용하기

CSS 커스텀 속성은 light DOM과 shadow DOM 모든 곳에서 사용할 수 있는 개념이다. 그래서 이를 이용하여 light DOM에서 컴포넌트 내 shadow DOM의 스타일을 적용시킬 수 있다. 

다음은 커스텀 속성을 이용하여 컴포넌트 내 shadow DOM 영역에 적용될 스타일을 결정하는 예제이다. 

```html
<body>
    <style>
        /* light DOM에서의 스타일 */
        my-profile {
            --profile-background-color: skyblue;
            --profile-text-color: brown;
        }
    </style>
    <template id="mytemp">
        <style>
            /* shadow DOM에 적용될 스타일 */
            #person-info > li {
                background-color: var(--profile-background-color);
                color: var(--profile-text-color);
                width: 10em;
                padding: 0.5em;
                margin: 0.5em;
            }
        </style>
        <ul id="person-info">
            <li>이름: <slot name="person-name"></slot></p>
            <li>생년월일: <slot name="birthday"></slot></p>
        </ul>
    </template>
    <script>
        customElements.define('my-profile', class extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'});
                this.shadowRoot.append(document.getElementById('mytemp').content.cloneNode(true));
            }
        });
    </script>
    <my-profile>
        <span slot="person-name">자바스</span>
        <span slot="birthday">1990/1/1</span>
    </my-profile>
</body>
```

예제 7-1

![예제 7-1 실행결과](/images/2024-04-27/js-web-components-shadow-dom-styling/9.png)

예제 7-1 실행결과

위 예제를 살펴보면, `<body>` 태그가 시작되는 부분에 `<style>` 태그 내부에서 커스텀 속성이 정의되어 있는 것을 볼 수 있다. 이 `<style>` 태그는 light DOM (document) 영역에 정의되어 있다. 그리고, 컴포넌트의 shadow DOM에 삽입될 `<template>` 태그 내부에는 shadow DOM 스타일링에 적용될 `<style>` 태그가 있는데, 이 태그 내부에서 앞서 정의한 커스텀 속성값을 이용하여 스타일을 지정하고 있다. light DOM 영역에 있던 커스텀 속성값들이 각각 스카이 블루, 갈색으로 설정되어 있어서 위 실행결과처럼 배경색과 글자색이 해당 색들로 결정된 것을 볼 수 있다. 이러한 원리를 이용하면, 향후 컴포넌트 내 스타일을 일괄적으로 바꿀 일이 생겨도 light DOM에서 커스텀 속성에 담긴 값만 바꾸면 되기에 매우 편리할 것이다. 이렇게 CSS 커스텀 속성을 이용하여 light DOM에서 컴포넌트 내 shadow DOM의 스타일에 영향을 끼치는 법에 대해 살펴보았다. 

---

References

[1] [Shadow DOM styling](https://ko.javascript.info/shadow-dom-style#ref-1015)

[2] 커스텀 속성, CSS 변수

[Using CSS custom properties (variables) - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)

[3] 커스텀 속성, CSS 변수

[Custom properties (--\*): CSS variables - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)