---
title: "[JS][Web Components] Shadow DOM"
category: "javascript"
tag: ["web", "js", "javascript", "shadow dom", "dom"]
---

# DOM아, 너에겐 숨겨진 형제가 있단다 : Shadow DOM

크롬 개발자 도구에서도 볼 수 있고, 자바스크립트를 통해서도 접근 가능한 HTML 요소들은 모두 DOM tree를 구성하는 노드들이나 마찬가지이다. 그런데 이 DOM 말고도 숨겨진 또 다른 DOM이 존재한다. 이름에서부터 “그림자”가 붙은 “Shadow DOM”이다. 참고로 이 shadow DOM과 구별하기 위해, 우리가 일반적으로 알고 있는 DOM을 “light DOM”이라고 한다. (빛의 돔!)

light DOM과 달리 shadow DOM은 그 이름에서부터 암시하듯, 외부에서는 접근할 수 없다. 자바스크립트에서도 일반적인 호출 및 선택자만으로는 접근할 수 없고, 이후에 소개할 특수한 문법을 사용해야 한다. 크롬 개발자 도구에서도 일반적으로는 이 shadow DOM을 볼 수 없다. 

참고로, 크롬 개발자 도구에서 shadow DOM을 볼 수 있는 방법은 다음과 같다. 

1. F12를 눌러 개발자 도구를 연다. 그러면 톱니바퀴 모양 버튼이 보일 것인데, 그것을 클릭한다.
    
    ![사진 1-1.](/images/2024-04-09/js-web-components-shadow-dom/1.png)
    
    사진 1-1.
    
2. 그러면 새로 뜨는 위젯에서, Settings → Preference → Elements 에서 “Show user agent shadow DOM”에 체크 표시를 한다. 
    
    ![사진 1-2](/images/2024-04-09/js-web-components-shadow-dom/2.png)
    
    사진 1-2
    
3. 그러면 다음과 같이 shadow DOM을 확인할 수 있게 된다. 
    
    ![사진 1-3](/images/2024-04-09/js-web-components-shadow-dom/3.png)
    
    사진 1-3
    
    #shadow-root 내부에 있는 모든 요소들이 shadow DOM을 구성하고 있는 것이다. 
    

이렇게, 개발자 도구에서조차 설정을 따로 해야만 shadow DOM을 볼 수 있다. 

DOM tree를 다시 정리해보자면 다음과 같다.

- Light tree : 일반적인 DOM tree의 하위 트리. HTML 요소로 구성된다. 여태까지 봐왔던 모든 DOM tree는 light tree였다.
- Shadow tree : 숨겨진 DOM tree의 하위 트리. HTML에도 보여지지 않는다.

# 용도

그렇다면 이 shadow DOM은 왜 존재하는 걸까? 즉, 어떻게 사용해야 할까? shadow DOM은 외부로부터 전혀 영향을 받지 않는 자신만의 로컬 HTML 요소들과 심지어는 CSS까지도 로컬로 정의하여 해당 웹 컴포넌트에만 적용시킬 수 있다. 이러한 shadow DOM 내부의 HTML, CSS 요소들은 외부에 영향을 끼치지도 않고, 외부로부터 영향을 받지도 않는다. 즉, shadow DOM은 캡슐화를 제공하여 웹 컴포넌트만의 요소 및 스타일을 가지도록 할 수 있는 것이다. 

참고로, 만약 어떤 요소가 light DOM과, shadow DOM을 동시에 보유하고 있다면, 브라우저는 오로지 shadow DOM만 렌더링한다고 한다. 하지만 이 둘을 동시에 사용할 수 있는 방법도 있다고 한다. 

# 자바스크립트에서 shadow DOM 사용해보기

shadow DOM을 이용하면 외부 요소에 영향을 받지 않는 나만의 웹 컴포넌트를 제작할 수 있는데, 자바스크립트에서 이 shadow DOM을 이용하는 것에 대해 살펴보겠다. 

shadow DOM은 앞서 언급했듯, 강력한 캡슐화를 제공하기에, 앞서 [[JS][web components] Custom elements](/js-web-components-custom-elements/) 에서 살펴봤던 커스텀 요소와 같이 사용하여 웹 컴포넌트를 제작하는 데에 쓰일 수도 있다. 

다음은 custom elements와 shadow DOM을 합쳐 사용한 예제이다. 

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
        customElements.define('custom-label', class extends HTMLElement {
            connectedCallback() {
                // shadow DOM의 근간인 shadow root를 연다. 
                // element.attachShadow()의 반환값인 ShadowRoot 객체를 통해서만 
                // shadow DOM에 접근할 수 있다. 
                const shadow = this.attachShadow({mode: 'open'});

                shadow.innerHTML = `
                <style>
                    #label-container {
                        border: 2px solid black;
                        background-color: grey;
                        display: inline-block;
                    }
                </style>
                <div id="label-container">
                    <p>만나서 반가워요, ${this.getAttribute('name')}님!</p>
                </div>`;
            }
        });
    </script>
    <custom-label name="자바스"></custom-label>
</body>
</html>
```

![예제 1-1 실행결과](/images/2024-04-09/js-web-components-shadow-dom/4.png)

예제 1-1 실행결과

먼저, 요소 내에서 shadow DOM을 사용하고자 한다면 먼저 해당 요소 내에서 `element.attachShadow()` 메서드를 호출한다. 해당 메서드에는 `{mode: ‘open’}` 형태의 인자를 넘길 수 있다. 

mode는 캡슐화 수준을 정하는 옵션이다. 다음의 값들 중 하나를 가질 수 있다. 

- “open”: `element.ShadowRoot` 형태로 shadow root에 접근할 수 있다.
- “closed”: 그 어떤 방식으로도 shadow DOM에 접근할 수 없다. element.ShadowRoot는 항상 null값을 가진다.

shadow DOM은 `attachShadow()` 메서드가 반환하는 참조값(ShadowRoot)을 통해서만 접근할 수 있다. `<input type=”range”>`와 같이 내장 shadow DOM은 닫혀있어, 접근할 방법이 없다. 

`attachShadow()` 메서드로 shadow root를 열었다면, `element.shadowRoot`와 같이 직접 shadow root에 접근할 수도 있다. 위 예제에서는 `this.shadowRoot`로 사용할 수 있겠다. 

`attachShadow()` 메서드에서 반환된 shadow root는 마치 요소처럼 사용할 수 있어, innerHTML이나 DOM 메서드를 사용할 수 있다. 위 예제에서는 innerHTML에 새로운 요소들과 스타일을 적용한 것을 볼 수 있다. 이처럼, shadow root를 가진 element를 “shadow tree host”라고 한다. 해당 요소는 host 프로퍼티로 접근할 수 있다. 

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
        customElements.define("my-element", class extends HTMLElement {
            connectedCallback() {
                const shadow = this.attachShadow({mode: 'open'});
                alert(shadow.host === this);
            }
        });
    </script>
    <my-element></my-element>
</body>
</html>
```

![예제 1-2 실행결과](/images/2024-04-09/js-web-components-shadow-dom/5.png)

예제 1-2 실행결과

shadowRoot 객체가 담긴 shadow의 shadow.host가 커스텀 요소와 동일함을 알 수 있다. 

한 편, shadow root를 사용할 때에는 다음의 제약사항이 있다.

- 하나의 요소에 단 하나의 shadow root만 생성할 수 있다.
- shadow root를 사용할 요소는 커스텀 요소이던가 아니면 다음의 내장 요소들 중 하나여야 한다.  img와 같은 다른 요소들은 shadow tree를 생성할 수 없다.
    - article, aside, blockquote, body, div, footer, h1 ~ h6, header, main, nav, p, section, span

# Encapsulation of Shadow DOM

shadow DOM의 캡슐화로 인한 특성들에는 다음이 존재한다. 

- Shadow DOM 요소들은 light DOM의 querySelector 등으로 선택할 수 없다.
- Shadow DOM 요소들이 가진 id는 light DOM 요소들의 것과 중복되어도 충돌나지 않는다. 다만, Shadow DOM 요소들만으로 한정한다면, id는 고유해야 한다.
- Shadow DOM은 외부와 구별되는 그들만의 스타일 시트(stylesheets)를 가질 수 있다.

이 모두 캡슐화로 인해 외부와 구별되기에 생길 수 있는 특성들이다. 다음은 Shadow DOM의 캡슐화를 확인하기 위한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #label-container {
            border: 5px dashed blue;
            background-color: red;
        }
    </style>
</head>
<body>
    <script>
        customElements.define('custom-label', class extends HTMLElement {
            connectedCallback() {
                // shadow DOM의 근간인 shadow root를 연다. 
                // element.attachShadow()의 반환값인 ShadowRoot 객체를 통해서만 
                // shadow DOM에 접근할 수 있다. 
                const shadow = this.attachShadow({mode: 'open'});

                shadow.innerHTML = `
                <style>
                    #label-container {
                        border: 2px solid black;
                        background-color: grey;
                        display: inline-block;
                    }
                </style>
                <div id="label-container">
                    <p>만나서 반가워요, ${this.getAttribute('name')}님!</p>
                </div>`;
            }
        });
    </script>
    <style>
        #label-container {
            border: 5px dashed blue;
            background-color: red;
        }
    </style>
    <custom-label name="자바스"></custom-label>
    <div id="label-container">
        <p>Nice to meet you!</p>
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-04-09/js-web-components-shadow-dom/6.png)

예제 2-1 실행결과

위 예제 코드를 보면, 앞 뒤로 `<style>` 태그가 정의되어 있음에도, `<custom-label>` 요소에는 이 스타일이 적용되지 않고, 내부의 shadow DOM에 정의된 스타일만 적용된 것을 볼 수 있다. 또한, shadow DOM에 정의된 해당 스타일도 외부 light DOM 요소에는 영향을 끼치지 않는 것도 확인할 수 있다. 

또한, light DOM에서 커스텀 요소에 적용된 id와 동일한 값을 사용했음에도 에러나 버그가 발생하지 않은 것도 확인할 수 있다. 이로서, shadow DOM의 캡슐화를 확인할 수 있다. 

---

References

[1] [Shadow DOM](https://ko.javascript.info/shadow-dom)