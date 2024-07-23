---
title: "[JS] 문서 객체 모델(Document Object Model)"
category: "javascript"
tag: ["web", "js", "javascript", "DOM"]
---

# 문서 객체 모델 (Document Object Model, DOM)

문서 객체 모델은 웹 페이지 상의 모든 요소들을 자바스크립트를 통해 접근, 제어할 수 있는 객체로 다루고, 이러한 HTML 요소와 자바스크립트 간 연결을 가능케 하는 인터페이스를 의미한다. 앞서 BOM에서 보았듯, 그 대상이 브라우저에서 문서로 바뀐 것일 뿐이다. 

DOM에는 크게 세 종류가 있는데, 하나는 모든 문서에서 다룰 수 있는 DOM인 Core DOM, HTML 문서에서 다루는 HTML DOM, 그리고 XML 문서에서 다루는 XML DOM이 있다. (여기서는 이 중 HTML만 다뤘기에 HTML에만 초점을 맞추고 설명을 전개할 것이다)

DOM에서는 웹 문서 속 텍스트, 이미지, 표, 동영상 등의 모든 요소들을 각각의 객체로 본다. 또한 웹 문서 자체도 객체로 보고, 이를 자바스크립트에서는 document란 객체로 접근한다. 

# DOM tree

HTML 내 요소들은 부모 요소와 자식 요소 관계로 이루어질 수도 있다. 즉, 계층 관계가 있다. 그리고 DOM에서는 각 HTML 요소들을 하나의 독립된 객체로 본다. 이러한 HTML 문서가 웹 브라우저에서 렌더링될 때, 요소들의 계층 관계를 트리 자료구조로 구성하고, 각 요소들을 각각의 노드(node)로 치환한다. 이렇게 형성되는 트리를 DOM tree라 한다. 

다음은 HTML 문서를 입력하면 그 문서 내 모든 객체들을 트리 자료구조로 보여주는 사이트를 이용한 모습이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div class="hi">
        <p style="color:blue;">wow<!--이 주석은 어디에?--></p>
    </div>
    <div class="hi"></div>
    <script src="js-ex.js"></script>
</body>
</html>
```

![예제 1-1 실행결과. [6] 참조](/images/2024-02-14/js-document-object-model/1.png)

예제 1-1 실행결과. [6] 참조

다음은 [5]에서 참고한 DOM tree 예시 사진이다.

![사진 1. HTML DOM tree 예시 사진. [5] 참조](https://www.w3schools.com/js/pic_htmltree.gif)

사진 1. HTML DOM tree 예시 사진. [5] 참조

HTML DOM tree를 구성하는 노드에는 다음이 존재한다. 

- 문서 노드 (document node) : HTML 문서 자체를 가리키는 객체이며, DOM 트리에서 루트 노드를 차지한다.
- 요소 노드 (element node) : HTML 태그로 만든 모든 요소들이 요소 노드에 해당한다. 요소 자체가 부모-자식 관계처럼 계층 구조를 형성하기에 트리 구조가 가능한 것이다. 또한, 속성 노드를 가질 수 있는 유일한 노드이다. 요소 노드들 중 최상위 노드에 해당하는 루트 요소 노드는 `<html>` 요소이다.
- 속성 노드 (attribute node) : `<div class=’box’></div>`의 class=’box’, `<p style=”color:blue;”></p>`의 style 속성과 같이, HTML 요소 내 속성들이 DOM tree에서 속성 노드를 구성한다. 특이하게도, 속성 노드는 요소 노드의 자식 노드도, 형제 노드도, 부모 노드도 아니고, 그저 연결되어 있기만 한다. 따라서 속성 노드에 접근하려면 요소 노드에 먼저 접근해야하는 구조이다.
- 텍스트 노드 (text node) : 텍스트도 객체이며, DOM tree의 노드이다. 텍스트 노드는 해당 노드를 자식 노드로 하는 요소 노드를 부모 노드로 가질 수는 있지만, 반대로 텍스트 노드가 자식 노드를 가질 수는 없다. 따라서 텍스트 노드는 leaf 노드가 된다.
- 주석 노드 (comment node) : HTML 문서 내 주석도 DOM tree 상에서는 주석 노드로 변환된다. 해당 주석이 존재하는 영역을 가지는 요소가 부모 노드가 된다. 위 예제에서 “이 주석은 어디에?”라는 주석은 `<p>` 태그로 감싸져 있으므로 p 요소가 부모 요소이다.

# DOM으로 HTML 요소 접근 및 제어

HTML 요소 중 특정 요소를 자바스크립트에서 DOM을 이용하여 가져오기 위한 여러 가지 메서드들이 있다. 그런데 이 메서드들의 공통점은 CSS에서 등장한 개념인 id, class, 태그 선택자들을 그대로 사용하여 특정 요소를 가져올 수 있다는 점이다. 

- 요소명.getElementByID(”id명”) : HTML 내 id 선택자의 이름을 그대로 입력하여 해당 요소(요소 노드)를 가져온다. HTML 내에서 id 선택자의 이름은 하나만 존재해야하므로 해당 메서드의 반환값도 1개이다.
- 요소명.getElementsByClassName(”클래스명”) : HTML 내 class 선택자 이름을 입력하여 해당 요소들을 가져온다. id 선택자와 달리, class 선택자는 문서 내에서 여러 개 존재할 수 있으므로 여러 개의 요소들이 담긴 HTMLCollection 이라는 유사 배열 객체로 반환된다. (유사 배열 객체에 대해서는 [[JS] 유사 배열 객체 (Array-like Object)](/javascript/js-array-like-object/)  문서 참조)
- 요소명.getElementsByTagName(”태그명”) : HTML 내 p, div와 같은 일반 태그명들을 사용하는 요소들을 가져온다. 역시 한 문서 내에서 여러 개의 요소를 가질 수 있으므로 여러 개의 요소들을 HTMLCollection 유사 배열 객체로 반환한다.

getElement… 로 시작되는 메서드들은 해당 요소의 요소 노드만 가져올 수 있다. 만약 텍스트, 속성 노드에 대해서도 접근하고자 한다면 다음의 메서드들을 사용해야 한다. 

- 노드.querySelector(’선택자명’) : 선택자명을 입력하면 그 선택자에 해당하는 노드를 하나 가져온다. id 선택자와 같이 문서 내에 해당 노드가 단 하나일 뿐일 때 사용할 수 있다. 선택자를 해당 메서드에 입력 시 CSS에서의 선택자 입력 방식과 동일하게 입력해야한다. 즉, id 선택자일 경우, #id선택자 와 같은 형태로 입력해야한다.
- 노드.querySelectorAll(’선택자명’) : 클래스 선택자, 태그 선택자와 같이 한 문서 내에 여러 요소들을 가질 수 있는 선택자를 인수로 전달하면 해당 노드들이 NodeList 유사 배열 객체 형태로 반환된다.

참고로, 다음은 HTMLCollection 객체와 NodeList 객체의 차이점을 알아보기 위한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container">
        <h1>큰 제목</h1>
        <h2>작은 제목1</h2>
        <h2>작은 제목2</h2>
        <div class="box"><p>Hi</p></div>
        <div class="box"><p>i'm good</p></div>
        <!-- 그냥 주석 -->
    </div>
    <script>
        let cont = document.getElementById('container');
        console.log(cont);
        console.log(cont.children);
        console.log(cont.childNodes);
        console.log(document.querySelector('#container'));
    </script>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-02-14/js-document-object-model/2.png)

예제 2-1 실행결과

어떤 노드의 children 프로퍼티를 출력하면 HTMLCollection 객체가, childNodes 프로퍼티를 출력하면 NodeList 객체가 출력된다. NodeList 객체의 경우, h2, div.box와 같은 요소 노드 뿐만 아니라 텍스트 노드, 주석 노드까지 포함하는 것을 알 수 있다. 반면, HTMLCollection 객체에는 텍스트 노드, 주석 노드는 없고 요소 노드만 있는 것을 알 수 있다. 

다음은 getElementByClassName() 메서드와 querySelectorAll() 메서드를 이용하여 같은 클래스 선택자에 해당하는 요소들을 가져와 출력하는 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container">
        <h1>큰 제목</h1>
        <h2>작은 제목1</h2>
        <h2>작은 제목2</h2>
        <div class="box"><p>Hi</p></div>
        <div class="box"><p>i'm good</p></div>
        <!-- 그냥 주석 -->
    </div>
    <script>
        let byElement = document.getElementsByClassName('box');
        let byQuery = document.querySelectorAll('.box');
        console.log(byElement);
        console.log(byQuery);
    </script>
</body>
</html>
```

![예제 2-2 실행결과](/images/2024-02-14/js-document-object-model/3.png)

예제 2-2 실행결과

위 예제로부터, getElement… 이름으로 시작하며, 인수로 전달된 선택자에 해당하는 요소들이 여러 개 있을 경우, HTMLCollection 객체를 반환한다는 사실과, querySelectorAll() 메서드의 반환값은 NodeList 객체임을 알 수 있다. 즉, 앞선 에제 2-1에서 도출한 사실과 종합해봤을 때, 요소 노드뿐만 아니라 속성, 텍스트 노드까지 접근하고자 한다면 이들을 포함하는 NodeList 객체를 반환하는 querySelectorAll() 메서드를 사용해야 한다는 사실을 재확인할 수 있다. 

# innerText, innerHTML

innerText, innerHTML는 DOM 요소의 프로퍼티로, 웹 요소의 텍스트 내용을 수정할 수 있게 해주는 프로퍼티이다. innerText는 텍스트만 입력 가능하고, innerHTML에는 텍스트와 HTML 태그의 혼용이 가능하다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 1px solid black;
            padding: 10px;
            display: grid;
            grid-template-rows: repeat(2, 100px);
            grid-template-columns: 1fr 3fr;
            gap: 10px 30px;
        }
        #container p {
            border: 1px solid grey;
            display: flex;
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
    <div id="container">
        <button id="text-btn">innerText</button>
        <p id="intext"></p>
        <button id="html-btn">innerHtml</button>
        <p id="inhtml"></p>
    </div>
    <script>
        let textBtn = document.getElementById('text-btn');
        let htmlBtn = document.getElementById('html-btn');
        let textP = document.getElementById('intext');
        let htmlP = document.getElementById('inhtml');
        textBtn.onclick = () => {
            textP.innerText = "안녕하세요, 만나서 반가워요.";
        }
        htmlBtn.onclick = () => {
            htmlP.innerHTML = "<strong>안녕하세요</strong>, 만나서 반가워요.";
        }
    </script>
</body>
</html>
```

![예제 3-1 실행결과](/images/2024-02-14/js-document-object-model/4.png)

예제 3-1 실행결과

# getAttribute(), setAttribute()

웹 요소 객체의 메서드인 getAttribute(), setAttribute()는 각각 특정 요소에 적용된 태그의 속성을 가져오는 메서드와, 속성값을 추가 또는 수정하는 메서드이다. 

```jsx
Element.getAttribute('property');  // 해당 태그의 속성: 속성값을 모두 가져옴.
Element.setAttribute('property', 'value'); // 해당 태그의 속성의 값을 변경 또는 없다면 추가.
```

다음은 위 메서드들을 활용한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 1px solid black;
            margin: 10px auto;
            padding: 10px;
        }
        #container div {
            border: 1px solid grey;
            margin: 20px auto;
            padding: 10px;
            width: 80%;
        }
        #msg {
            height: 50px;
            text-align: center;
            background-color: burlywood;
        }
    </style>
</head>
<body>
    <div id="container">
        <div id="msg"><p style="color: blueviolet;">한 권짜리 프롤로그</p></div>
        <div id="get-attr">
            <button>속성 보기</button>
            <p></p>
        </div>
        <div id="set-attr">
            <button>속성 바꾸기</button>
        </div>
    </div>
    <script>
        let msgDiv = document.querySelector('#msg');
        let getDiv = document.querySelector('#get-attr');
        let setDiv = document.querySelector('#set-attr');
        getDiv.querySelector('button').onclick = () => {
            getDiv.querySelector('p').innerText = msgDiv.querySelector('p').getAttribute('style');
        }
        setDiv.querySelector('button').onclick = () => {
            msgDiv.querySelector('p').setAttribute('style', 'color: brown; background-color:bisque;');
        }

        // 스타일 변경
        for(let ch of getDiv.childNodes.values()) {
            ch.style = "display: inline-block";
        }
    </script>
</body>
</html>
```

![예제 4-1 실행결과](/images/2024-02-14/js-document-object-model/5.png)

예제 4-1 실행결과

위 예제에서, getAttribute() 메서드를 이용하여 `<div id=”msg”>` 태그의 style 속성을 가져와 이를 출력하는 기능을 구현하였고, setAttribute() 메서드를 이용하여 버튼을 누르면 해당 div 요소의 배경색 및 글자색이 변경되도록 설정하였다. 

# CSS 속성 접근

자바스크립트에서 HTML 요소를 가져온 뒤, 해당 요소의 CSS 스타일 속성을 제어하고자 한다면 다음과 같이 사용하면 된다.

```jsx
const element = document.querySelector('#container');

// CSS 스타일 속성 접근 및 변경 방법.
element.style.속성명 = 'css 속성값';  // 방법 1.
element.style = '속성명: 속성값';  // 방법 2.
```

위 예시에서 보듯, 방법 1 또는 방법 2 중에 하나를 선택하여 사용하면 된다. 배경색 스타일 속성인 background-color를 예로 들면 다음과 같다. 

```jsx
const divBox = document.querySelector('.box');

divBox.style.backgroundColor = 'blue';
divBox.style = "background-color: blue";
```

style 키워드 다음에 속성명을 직접 작성하는 경우(방법 1), 속성명이 두 단어 이상이 합쳐진 경우, 두 번째 단어부터는 해당 단어의 첫 글자는 대문자로 적는다. 즉, background-color의 경우, backgroundColor와 같이 적는다. 다만, 앞서 보인 방법 2처럼 style 다음에 등호를 입력하고, 그 뒤에 문자열로 입력하는 경우에는 css 문법을 그대로 사용하면 된다. 

# DOM tree에서 부모-자식 노드에 접근하기

DOM tree는 부모-자식 관계를 이루는 모든 HTML 요소들의 그 관계를 트리 자료구조로 나타낸 것임을 앞서 살펴보았다. 그렇다면, 특정 부모 노드로부터 직접 그 자식 노드에 접근할 수 있지 않을까? 반대로, 어떤 자식 노드로부터 직접 그 부모 노드에 접근할 수 있지 않을까? 자바스크립트에서는 이러한 기능들도 제공한다. 

## 자식 노드에 접근

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <ul>
        <li>hi</li>
        <li style="color:red;">nice</li>
        <li>to</li>
        <li>meet</li>
        <li>you</li>
    </ul>
    <script>
        const qs = document.querySelector('ul');
        const ullist = document.getElementsByTagName('li');
        console.log(qs.children);
        console.log(qs.childNodes);
        console.log(qs.childElementCount);
    </script>
</body>
</html>
```

![예제 5-1 실행결과](/images/2024-02-14/js-document-object-model/6.png)

예제 5-1 실행결과

어떤 부모 노드로부터 그 자식 노드에 접근하고자 한다면 children과 childNodes 프로퍼티를 이용하면 된다. children은 HTMLCollection 객체라 자식 요소 노드만을 반환하며, childNodes는 NodeLIst 객체라 요소 노드와 더불어 텍스트 노드에도 접근할 수 있다. 

한 편, childElementCount 프로퍼티를 통해, 자식 노드들 중 요소 노드들의 수만 확인할 수 있다. 

## 부모 노드에 접근하기

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <ul>
        <li>hi</li>
        <li style="color:red;">nice</li>
        <li>to</li>
        <li>meet</li>
        <li>you</li>
    </ul>
    <script>
        const qs = document.querySelector('ul');
        const ullist = document.getElementsByTagName('li');
        console.log(ullist[0].parentNode);
        console.log(typeof ullist[0].parentNode);
        console.log(ullist[0].parentElement);
        console.log(typeof ullist[0].parentElement);
    </script>
</body>
</html>
```

![예제 5-2 실행결과](/images/2024-02-14/js-document-object-model/7.png)

예제 5-2 실행결과

parentNode, parentElement 모두 부모 노드에 접근할 수 있는 프로퍼티이다. 


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [코딩교육 티씨피스쿨](https://www.tcpschool.com/javascript/js_dom_concept)

[3] [코딩교육 티씨피스쿨](https://www.tcpschool.com/javascript/js_dom_node)

[4] DOM 트리를 트리 자료구조의 관점으로 바라본 아티클

[DOM 트리를 트리 자료구조로 바라보기 \| 요즘IT](https://yozm.wishket.com/magazine/detail/1803/)

[5] [JavaScript HTML DOM](https://www.w3schools.com/js/js_htmldom.asp)

[6] HTML 문서를 입력하면 해당 DOM 트리를 시각화하여 보여주는 사이트.

[Live DOM Viewer](https://software.hixie.ch/utilities/js/live-dom-viewer/)

[7] [[Javascript]DOM(1) node와 node 취득](https://wayin.tistory.com/entry/javascript-dom-access-node)

[8] [문서 객체 모델 DOM 과 자바스크립트 JavaScriptㅣ생성 방식 및 접근 방법 - 코드스테이츠 공식 블로그](https://www.codestates.com/blog/content/dom-javascript)

[9] HTMLCollection VS NodeList

[[JavaScript]HTMLCollection과 NodeList 차이점](https://developer-talk.tistory.com/848)