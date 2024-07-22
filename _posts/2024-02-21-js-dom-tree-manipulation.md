---
title: "[JS] DOM tree 조작"
category: "javascript"
tag: ["web", "js", "javascript", "DOM"]
---

# 개요

앞서 [JS - 문서 객체 모델 (Document Object Model)](/javascript/js-document-object-model/) 페이지에서 DOM tree에 대해서 살펴보았다. DOM tree는 HTML 문서 내 모든 요소들의 계층 구조를 트리 자료구조를 이용하여, 요소들은 노드(Node)로, 부모-자식 노드 간의 관계를 나타내기 위해 이 둘을 연결하는 간선(edge)으로 표현한다. (즉, 그래프 이론으로 나타낸다) 

이러한 배경 지식을 토대로, 이번 페이지에서는 DOM tree에서 새 노드를 추가하거나 삭제하는 방법에 대해 살펴보겠다. 

# NodeList

DOM tree의 구조에서, `<ul>` 부모 태그가 여러 개의 `<li>` 태그들을 가질 수 있듯, 특정 부모 노드가 2개 이상의 자식 노드들을 가질 수 있다. 이를 DOM 요소의 childNodes 프로퍼티를 통해 접근할 수 있으며, 해당 프로퍼티는 노드들의 리스트 형태로 반환한다. 또한, `querySelectorAll()` 메서드로 중복되는 이름을 가지는 여러 노드들을 리스트 형태로 모아 반환할 수 있다. 이렇듯, 여러 개의 노드들을 하나의 리스트에 저장한 형태를 노드 리스트라 하며, DOM에서는 NodeList 객체로 표현된다. 

# 새 노드 생성 및 기존 DOM tree에 추가

## 새 노드 생성

DOM tree에는 요소 노드 뿐만 아니라, 텍스트 노드와 속성 노드도 존재한다. 이 각각의 노드들을 생성하기 위해선 다음의 메서드들을 이용한다. 

- 새 요소 노드 생성: `createElement(”새 요소 노드명”);` 
예) `document.createElement(”div”);`
- 새 텍스트 노드 생성 : `createTextNode(”텍스트”);` 
예) `document.createTextNode(”새 텍스트입니다.’);`
- 새 속성 노드 생성 : `createAttribute(”속성명”);`
예) `document.createAttribute(”class”);`
해당 메서드를 통해 생성된 새 속성 노드의 속성값을 지정하려면 `노드명.value = “.myclass”`와 같이 value 프로퍼티에 속성값을 할당하면 된다.

## 생성된 노드들을 서로 연결하기

- `부모노드명.appendchild(자식노드명)` : 부모 요소 노드에 자식 요소 노드를 연결하거나 부모 요소 노드에 텍스트 노드를 연결하고자 할 때 쓰일 수 있다.
- `요소노드명.setAttributeNode(속성노드명)` : 요소 노드에 속성 노드를 연결할 때 쓰이는 메서드.

두 노드를 연결하는 위 메서드들을 이용하면, 새로 생성한 노드끼리의 연결은 물론, 기존 DOM tree 내에 존재하는 노드에도 연결하여 추가할 수도 있다. 

다음은 위에서 보인 여러 메서드들을 활용하여 “자세히보기” 기능을 구현한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0" />
    <style>
        #more-info {
            border: 1px solid black;
            width: 500px;
            margin: 10px auto;
            display: flex;
            align-items: center;
            flex-wrap: wrap;
        }
        #more-info > span, #more-info > p {
            cursor: pointer;
        }
        #info-container {
            border: 1px solid grey;
            margin: 10px;
            padding: 10px;
            display: none;
        }
    </style>
</head>
<body>
    <div id="more-info">
        <span class="material-symbols-outlined">
            chevron_right
        </span>
        <p>자세히 보기</p>
    </div>
    <script>
        const moreInfo = document.querySelector('#more-info');
        const icon = moreInfo.querySelector('span');
        const infoText = moreInfo.querySelector('#more-info > p');

        const closedIcon = 'chevron_right';
        const openedIcon = 'expand_more';

        let alreadyCreated = false;

        function changeIcon() {
            if (icon.innerText === closedIcon) {
                icon.innerText = openedIcon;
                infoText.textContent = '닫기';
            } else {
                icon.innerText = closedIcon;
                infoText.textContent = '자세히 보기';
            }
        }
        icon.addEventListener('click', changeIcon);
        infoText.addEventListener('click', changeIcon);

        function addDetailInfo() {
            const infoContainer = document.createElement('div');
            const infoId = document.createAttribute('id');
            infoId.value = 'info-container';  // # 기호를 앞에 붙이면 안된다.
            infoContainer.setAttributeNode(infoId);

            const newP = document.createElement('p');
            const newText = document.createTextNode("사실 여긴 자세히 볼게 없습니다. 대신 귀여운 고양이를 보여드리겠습니다.");
            newP.appendChild(newText);

            const newImage = document.createElement('img');
            const imageSrc = document.createAttribute('src');
            const imageWidth = document.createAttribute('width');
            const imageHeight = document.createAttribute('height');
            imageSrc.value = '..\\images\\cutecat.jpeg';
            imageWidth.value = '400px';
            imageHeight.value = '400px';
            newImage.setAttributeNode(imageSrc);
            newImage.setAttributeNode(imageWidth);
            newImage.setAttributeNode(imageHeight);

            infoContainer.appendChild(newP);
            infoContainer.appendChild(newImage);

            moreInfo.appendChild(infoContainer);
        }

        function showOrHideDetail() {
            if (icon.innerText === openedIcon) {
                if (alreadyCreated === false) {
                    addDetailInfo();
                    alreadyCreated = true;
                }
                document.getElementById('info-container').style.display = 'block';
            } else {
                document.getElementById('info-container').style.display = 'none';
            }
        }

        icon.addEventListener('click', showOrHideDetail);
        infoText.addEventListener('click', showOrHideDetail);
    </script>
</body>
</html>
```

![예제 1-1 실행결과1. 초기 화면](/images/2024-02-21/js-dom-tree-manipulation/1.png)

예제 1-1 실행결과1. 초기 화면

![예제 1-1 실행결과2. “자세히 보기” 버튼을 클릭한 결과.](/images/2024-02-21/js-dom-tree-manipulation/2.png)

예제 1-1 실행결과2. “자세히 보기” 버튼을 클릭한 결과.

위 예제의 자바스크립트 코드에서 `addDetailInfo()` 콜백 함수 내부에서 새 노드들을 생성하고 이 노드들을 연결하는 코드를 볼 수 있다. 

`appendChild()` 메서드는 부모 노드 내 자식 노드들 중 맨 마지막 위치에 새 자식 노드를 삽입한다. 만약 중간에 삽입하고자 한다면 해당 메서드 대신 `insertBefore()` 메서드를 사용하면 된다. 

```jsx
부모요소.insertBefore(삽입할 새 노드, 대상 자식 노드)
// 부모 요소 내 대상 자식 노드보다 앞에 새 노드를 삽입하는 메서드.

//예시
const searchUl = document.querySelector('#search > ul');
const liElement = document.createElement('li');
// 새 자식 노드를 맨 앞에 추가.
searchUl.insertBefore(liElement, searchUl.childNodes[0]);
```

# DOM tree 내 기존 노드 삭제하기

기존 DOM tree에 존재하는 노드를 삭제하려면, 무조건 부모 노드의 입장에서 자식 노드를 삭제하는 방식으로만 삭제가 가능하다. 즉, 현재 노드에서 자체적으로 해당 노드를 삭제할 수는 없다는 것이다. 따라서, 만약 현재 노드를 삭제하고자 한다면 먼저 현재 노드의 부모 노드에 접근한 다음, 해당 부모 노드에서 삭제하고자 하는 자식 노드를 삭제하는 방식으로 처리해야 한다. 참고로, 부모 노드에서 특정 자식 노드를 삭제하고자 한다면 `removechild(노드명)` 메서드를 사용한다.

```jsx
// targetNode : 기존 DOM tree 내에서 삭제하고자 하는 노드
targetNode.parentNode.removeChild(targetNode);
```

다음은 검색창에 키워드를 입력했을 때 나오는 검색 기록을 보여주고, 그 중 삭제하고자 하는 검색 기록의 X 버튼을 누르면 삭제하게끔 해주는 예제이다. 삭제 기능은 앞서 언급한 `removechild()` 메서드를 사용하였다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200" />
    <style>
        #search {
            border: 1px solid black;
            width: 60%;
            margin: 70px auto;
            padding: 10px;
        }
        #search > h1 {
            text-align: center;
        }
        #search > form {
            display: flex;
            justify-content: center;
        }
        #search > form > button {
            background-color: whitesmoke;
        }
        #search > ul {
            list-style: none;
            padding: 0px;
            width: 80%;
            margin: 0px auto;
        }
        #search > ul li {
            border: 1px solid grey;
            margin-bottom: 5px;
            padding: 5px 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        #search > ul li > span {
            cursor: pointer;
        }
        #search > h2 {
            width: 80%;
            margin: 10px auto;
        }
    </style>
</head>
<body>
    <div id="container">
        <div id="search">
            <h1>가짜 검색 엔진</h1>
            <form action="">
                <input type="search" size="40" id="search-input">
                <button class="material-symbols-outlined" id="search-button">search</button>
            </form>
            <h2>검색 기록</h2>
            <ul></ul>
        </div>
    </div>
    <script>
        const search = document.querySelector('#search-input');
        const button = document.querySelector('#search-button');
        const searchUl = document.querySelector('#search > ul');
        const closeIcons = document.querySelectorAll('#search > ul span');

        const createSearchHistory = function(text) {
            /*
                <li><p>text</p><span class='...'>icon-name</span></li>
            */
            const liElement = document.createElement('li');
            const textInLi = document.createTextNode(text);
            const textP = document.createElement('p');
            const spanElement = document.createElement('span');
            const spanAttr = document.createAttribute('class');
            const spanIconText = document.createTextNode('close');
            spanAttr.value = 'material-symbols-outlined';
            spanElement.setAttributeNode(spanAttr);
            spanElement.appendChild(spanIconText);
            textP.appendChild(textInLi);
            liElement.appendChild(textP);
            liElement.appendChild(spanElement);

            // span element를 클릭하면 해당 li 요소를 삭제하는 기능.
            spanElement.addEventListener('click', (event) => {
                let li = event.currentTarget.parentNode;
                li.parentNode.removeChild(li);
            });

            //searchUl.appendChild(liElement);
            // 최근에 입력된 검색 기록이 맨 위에 놓게 하고자 한다면 다음 코드를 실행.
            // 부모요소.insertBefore(삽입할 새 노드, 대상 자식 노드)
            // 부모 요소 내 대상 자식 노드보다 앞에 새 노드를 삽입하는 메서드.
            searchUl.insertBefore(liElement, searchUl.childNodes[0]);
        }

        button.addEventListener('click', (event) => {
            // 버튼의 기본 기능인 submit 기능이 기존 콜백 함수를 무시하고 
            // 실행된다고 하므로, 이를 방지하기 위한 코드. 
            // 현재 이벤트의 기본 동작을 중단하는 메서드.
            // HTML 태그 내 onclick과 같은 속성 사용 시 다음과 같이 처리.
            // <div onclick="callbackFunc(); return false;">
            event.preventDefault();

            // string.trimStart() : 문자열 맨 왼쪽의 공백이 있다면 없앤다. 
            if (search.value.trimStart() !== '') {
                createSearchHistory(search.value);
                search.value = '';
            }
        });
    </script>
</body>
</html>
```

위 예제에서 `removeChild()`를 이용하여 특정 노드를 삭제하는 코드는 다음과 같다. 

```jsx
// span element를 클릭하면 해당 li 요소를 삭제하는 기능.
spanElement.addEventListener('click', (event) => {
    let li = event.currentTarget.parentNode;
    li.parentNode.removeChild(li);
});
```

위 코드를 실행하면 다음과 같다. 

![예제 2-1 실행결과1. 검색 창에 각각 1, 2라고 입력하고 ‘돋보기’ 버튼을 누른 결과. 검색 기록의 각 항목의 오른쪽에 있는 X 버튼을 누르면 해당 항목만 삭제할 수 있다. ](/images/2024-02-21/js-dom-tree-manipulation/3.png)

예제 2-1 실행결과1. 검색 창에 각각 1, 2라고 입력하고 ‘돋보기’ 버튼을 누른 결과. 검색 기록의 각 항목의 오른쪽에 있는 X 버튼을 누르면 해당 항목만 삭제할 수 있다. 

![예제 2-1 실행결과2. 앞선 실행결과1에서 검색 기록 맨 위에 있던 ‘2’라는 검색 항목의 X 버튼을 누른 결과. 해당 항목만 삭제된 것을 알 수 있다. ](/images/2024-02-21/js-dom-tree-manipulation/4.png)

예제 2-1 실행결과2. 앞선 실행결과1에서 검색 기록 맨 위에 있던 ‘2’라는 검색 항목의 X 버튼을 누른 결과. 해당 항목만 삭제된 것을 알 수 있다. 



---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

