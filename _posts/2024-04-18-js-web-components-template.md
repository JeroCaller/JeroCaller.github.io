---
title: "[JS][Web Components] Template"
category: "javascript"
tag: ["web", "js", "javascript", "web components", "template"]
---

HTML에는 내장 요소인 `<template>`를 제공한다. 해당 태그는 HTML 언어를 담는 태그이며, 브라우저에서는 `<template>` 태그를 무시하고, 대신 문법만 올바르게 작성했는지 체크만 한다. 해당 태그는 자바스크립트를 통해 사용 가능하다. 

`<template>`는 브라우저가 무시하기에, 그냥 정의만 하는 것으로는 그 안의 코드가 실행되지 않는다. `<style>`로 작성된 스타일도 적용되지 않고, `<script>`로 작성된 코드도 실행되지 않는다. 

```html
<template>
    <style>
        p {
            background-color: aquamarine;
            color:bisque;
        }
    </style>
    <script>
        alert("hi");
    </script>
    <div>
        <p>good</p>
    </div>
</template>
```

위 예제를 실행하면 화면에 아무것도 뜨지 않는다. 

`<template>` 태그 내 내용들은 DOM에 추가되어야 실행된다. 

# template 사용법

보통 `<template>`에 id값을 부여한다. 그래야 자바스크립트 내에서 해당 템플릿을 사용할 수 있기 때문이다. 자바스크립트 내에서는 바로 해당 템플릿의 id값으로 접근 가능하다. `<template id=”mytemp”>`라 하였다면 자바스크립트 내에서 mytemp으로 바로 접근 가능하다. 

다음은 커스텀 요소 내 shadow DOM의 innerHTML에 삽입할 언어를 대신 `<template>`에 두고, 이를 내부로 가져와 사용하는 예제이다. 

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
    <template id="mytemp">
        <style>
            #label-container {
                border: 2px solid black;
                background-color: grey;
                display: inline-block;
                padding: 10px;
            }
        </style>
        <div id="label-container">
            <p>만나서 반가워요!</p>
        </div>
    </template>
    <script>
        customElements.define('custom-label', class extends HTMLElement {
            connectedCallback() {
                this.attachShadow({mode: 'open'});
                this.shadowRoot.append(mytemp.content.cloneNode(true));
            }
        });
    </script>
    <custom-label name="자바스"></custom-label>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-04-18/js-web-components-template/1.png)

예제 2-1 실행결과

요소에 `<template>` 내의 요소들을 추가하고자 한다면, `element.append()` 메서드 내부에 `temptag.content.cloneNode(true)`를 인자로 넘기면 된다. 위 예제에서는 `this.shadowRoot.append(mytemp.content.cloneNode(true));` 와 같이 사용하였다. 그러면 `<template>` 내부에 있던 `<style>, <div>` 태그들이 자식 요소로 추가된다. 다음은 위 실행결과를 크롬 개발자 도구로 살펴본 결과이다. 

![예제 2-1 실행결과2](/images/2024-04-18/js-web-components-template/2.png)

예제 2-1 실행결과2

`<template>` 내부에 있던 요소들이 커스텀 요소 내부의 shadow DOM으로 추가됨을 알 수 있다. 

참고로, `<template>` id 이름에 하이픈(-)이 들어가면 자바스크립트에서 이를 인식하지 못한다. 예를 들어 `<template id=”my-temp”>`이라고 하면 자바스크립트에서 인식을 하지 못하고 에러를 발생시킨다. 그러므로 하이픈은 빼고 (mytemp와 같이) id값을 짓는 것이 좋겠다. 

`<template>`를 이용하면, 커스텀 요소 내부에 새 요소 및 CSS를 추가할 때 요소 내부의 this.innerHTML에 직접 HTML 언어를 작성하지 않고도 외부의 template만 삽입하면 된다. 이를 이용하면 똑같은 커스텀 요소에도 매번 다른 스타일 속성 및 새 요소들을 다르게 추가할 수도 있을 것이다. 

---

References

[1] [Template element](https://ko.javascript.info/template-element)
