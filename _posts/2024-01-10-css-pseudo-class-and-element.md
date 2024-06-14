---
title: "[CSS] 가상 클래스와 가상 요소"
category: "CSS"
tag: ["css", "web", "pseudo class", "pseudo element"]
---
# 가상 클래스(pseudo class)

`<div class=’hi’></div>` 와 같이, html의 요소에 클래스의 이름을 정의하면 css에서 해당 클래스 선택자를 통해 특정 스타일을 적용시킬 수 있었다. 이러한 과정에서는 개발자가 직접 클래스를 정의한다. 이를 “사용자 정의 클래스”라고 한다면 이와 다르게, 사용자가 직접 정의하지 않더라도 어떤 요소가 특정 조건을 만족시키면 이미 정의된 클래스가 해당 요소에 자동 적용되는 경우도 있는데, 가상 클래스가 이러한 역할을 한다.

가상 클래스에는 여러 가지가 있는데, 모든 가상 클래스는 references [3]을 참고. 여기서는 그 중에서 대표적인 :link, :visited, :hover, :active, :focus에 대해 살펴보겠다. 

- :link
링크가 걸린 텍스트, 이미지 등의 웹 요소에 사용자가 아직 이를 클릭하여 해당 링크의 사이트로 이동하지 않았을 때 해당 링크에 특정 스타일을 적용하고자 할 때 쓰이는 가상 클래스 선택자이다.
- :visited
앞서 :link가 사용자가 방문하지 않은 링크에 스타일을 적용하기 위한 가상 클래스 선택자라면, :visited는 사용자가 한 번이라도 방문한 적 있는 링크에 스타일을 적용하기 위해 사용되는 가상 클래스 선택자이다.
- :hover
웹 요소에 사용자가 마우스를 올려놓았을 때 스타일을 적용하기 위해 사용되는 가상 클래스 선택자이다.
- :active
사용자가 특정 웹 요소를 활성화시켰을 때의 스타일을 적용하고자 할 때 쓰이는 가상 클래스 선택자이다. 여기서 활성화란 것은, 마우스의 경우 사용자가 특정 웹 요소를 클릭하는 그 순간을 의미한다.
- :focus
사용자가 특정 웹 요소에 마우스로 한 번 클릭한 상태이거나 탭 키를 통해 특정 텍스트 입력 창에 커서가 깜빡이는 경우, 이를 해당 웹 요소에 포커스가 맞춰졌다고 표현하는데, 이렇게 특정 웹 요소에 포커스가 맞춰줬을 때 스타일을 적용할 때 사용하는 가상 클래스 선택자이다.

위에서 언급한 가상 클래스 선택자들 중, link, visited, hover, active는 같이 사용할 때 반드시 :link → :visited → :hover → :active 순으로 사용해야 한다. 만약 이 순서가 하나라도 틀리면 원하는 대로 스타일이 적용되지 않을 수 있다. 

위 가상 클래스 선택자들의 설명을 보고 알 수 있는 것은 다음과 같다. 

1. 가상 클래스 선택자는 앞에 콜론(:) 기호를 붙인다. 
2. 본래 HTML, CSS는 사용자에게 정보만을 보여줄 뿐, 반대로 사용자가 정보를 입력할 수 없거나 사용자의 액션에 반응을 할 수 없는 정적 언어이다. 그래서 원래 사용자의 입력(가령 마우스 hover, 클릭, 텍스트 입력 등등)에 따라 그에 따른 웹 요소의 스타일을 변경하기 위해선 JS와 같은 동적 언어를 사용해야 했다. 하지만 가상 클래스를 이용하면 굳이 동적 언어를 사용하지 않고도 문제를 해결할 수 있다. → 물론 만약 엄청 복잡한 기능을 원한다면, 가상 클래스들 중에서 원하는 기능을 갖춘 가상 클래스가 없을 수도 있다. 이 경우에는 어쩔 수 없이 동적 언어를 따로 사용해야 할 것이다. 

다음은 위에서 본 가상 클래스 선택자들을 이용해서, 여러 단락들로 이루어진 글과, 각 단락들을 목차로 넣은 하나의 웹 문서에 사용자가 목차 링크를 누르거나 단락 제목에 마우스를 두면 그에 따라 스타일이 바뀌는 예제 코드이다. 

다음에 올 HTML, CSS 전체 소스 코드는 다음의 링크 참조.

[https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css-concepts/pseudo-class-element.html](https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css-concepts/pseudo-class-element.html)

[https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css-concepts/pseudo-class-element.css](https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css-concepts/pseudo-class-element.css)

먼저 HTML 코드는 대략 이런 식이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="study.css">
</head>
<body>
    <div id="page">
        <h1 id="webtitle">AI 단편 스토리 모음집</h1>
        <div class="partition"></div>
        <div id="toc">
            <h3>목차</h3>
            <ul type="none">
                <li><a href="#story1">길을 잃은 모험가</a></li>
                <li><a href="#story2">시간을 건너는 탐험가</a></li>
                <li><a href="#story3">도시의 불면</a></li>
                <li><a href="#story4">밀실의 퀴즈: 제한된 시간</a></li>
            </ul>
        </div>
        <div class="partition"></div>
        <div class="parabox" id="story1">
            <h1>길을 잃은 모험가</h1>
            <p>
                오래된 숲 속에서... <!-- 생략 -->
            </p>
        </div>
        <div class="parabox" id="story2">
            <h1>시간을 건너는 탐험가</h1>
            <p>
                한 명의 용감한... <!-- 생략 -->
            </p>
        </div>
        <div class="parabox" id="story3">
            <h1>도시의 불면</h1>
            <p>
                바쁜 도시... <!-- 생략 -->
            </p>
        </div>
        <div class="parabox" id="story4">
            <h1>밀실의 퀴즈: 제한된 시간</h1>
            <p>
                깊은 밤, 일곱 명의... <!-- 생략 -->
            </p>
        </div>
    </div>
</body>
</html>
```

위 HTML 코드에서 불러오는 CSS 코드의 일부는 다음과 같다.

```css
/* 생략 */
#toc a:link {
    /* 한 번도 방문한 적 없는 링크에 대한 스타일 지정 */
    text-decoration: none;
    color: black;
}
#toc a:visited {
    /* 한 번이라도 방문한 적 있는 링크에 대한 스타일 지정 */
    font-size: large;
    color: cadetblue;
}
#toc a:hover {
    background-color: darkgray;
}
#toc a:active {
    /* 링크 클릭 순간의 스타일 지정 */
    background-color: red;
}
/* 생략 */
.parabox h1:hover {
    background-color:chartreuse;
}
/* 생략 */
```

위 예제 코드를 실행한 결과는 다음과 같다. 

![사진 1. 목차의 한 목록 위로 마우스를 올리면 해당 목록의 배경색이 바뀌는 것을 볼 수 있다. 이는 :hover 가상 클래스 선택자를 이용하여 구현할 수 있었다.  ](/images/2024-01-10/css-pseudo-class-and-element/1.png)

사진 1. 목차의 한 목록 위로 마우스를 올리면 해당 목록의 배경색이 바뀌는 것을 볼 수 있다. 이는 :hover 가상 클래스 선택자를 이용하여 구현할 수 있었다.  

![사진 2. 목차의 한 목록을 클릭하는 순간의 해당 목록의 배경색이 빨간색으로 바뀌는 것을 볼 수 있다. 이는 :active 가상 클래스 선택자를 이용하여 구현한 것이다. ](/images/2024-01-10/css-pseudo-class-and-element/2.png)

사진 2. 목차의 한 목록을 클릭하는 순간의 해당 목록의 배경색이 빨간색으로 바뀌는 것을 볼 수 있다. 이는 :active 가상 클래스 선택자를 이용하여 구현한 것이다. 

![사진 3. 목차의 맨 처음 목록을 클릭한 이후의 모습. 해당 목록에는 \<a> 태그를 이용해 링크가 걸려 있었던 상황으로, 해당 목록에 :visited 가상 클래스 선택자를 이용했기에 방문 이후의 글자색을 바꿀 수 있다. ](/images/2024-01-10/css-pseudo-class-and-element/3.png)

사진 3. 목차의 맨 처음 목록을 클릭한 이후의 모습. 해당 목록에는 \<a> 태그를 이용해 링크가 걸려 있었던 상황으로, 해당 목록에 :visited 가상 클래스 선택자를 이용했기에 방문 이후의 글자색을 바꿀 수 있다. 

![사진 4. 어느 한 단락의 제목 위로 마우스를 올려뒀을 때 해당 요소의 배경색이 바뀐다. 이 역시 :hover 가상 클래스 선택자를 이용한 것이다.](/images/2024-01-10/css-pseudo-class-and-element/4.png)

사진 4. 어느 한 단락의 제목 위로 마우스를 올려뒀을 때 해당 요소의 배경색이 바뀐다. 이 역시 :hover 가상 클래스 선택자를 이용한 것이다.

위 결과를 보면 알 수 있듯, 동적 언어의 사용 없이도 CSS만으로도 동적인 요소를 가상 클래스를 이용하여 부여할 수 있다. 

참고로 위 예제 코드에서, 목차 부분의 목록을 클릭하면 같은 웹 문서에서 해당 목록에 걸린 링크에 해당하는 요소로 이동되는 것을 알 수 있다. 사실 ‘링크’는 현재 문서에서 다른 페이지로 이동할 때 쓰이는 것이고, 이와 달리 같은 문서 내에서 다른 위치로 이동하고자 할 때는 ‘앵커(anchor)’를 쓴다. HTML에서 앵커를 사용하기 위해선 위 예제 코드에서도 볼 수 있듯, 우선 div나 다른 요소에 id를 부여한 후, \<a> 태그의 href 속성값에 \<a href=’#story1’> 처럼 id 선택자 형태로 입력하면 \<a> 태그와 해당 id를 가지는 요소 이 둘이 앵커로 연결된다. 

# 가상 요소 (Pseudo-element)

가상 요소도 가상 클래스와 비슷하다. 다만, 가상 요소는 HTML 내 특정 요소의 상태에 따라 기존 HTML 문서 내에는 없는 스타일 또는 심지어 내용까지도 추가할 수 있는 것이다. 즉, 마치 HTML 내에는 없던 요소를 추가하는 것과 같은 효과를 준다. 

가상 클래스는 앞에 콜론(:) 하나만 붙었던 것과 달리, 가상 요소에는 앞에 콜론(:) 기호가 두개(::)가 들어간다.

가상 요소에도 여러 가지가 있는데, 이는 references [4]를 참고하고, 여기서는 first-line, first-letter, before, after에 대해서만 보겠다. 

- ::first-line
지정된 요소의 첫 번째 줄에 스타일을 적용하고자 할 때 사용할 수 있는 가상 요소.
- ::first-letter
지정된 요소의 첫 번째 줄의 첫 번째 글자에 스타일을 적용하고자 할 때 사용할 수 있는 가상 요소.
- ::before
지정된 요소의 앞에 스타일을 적용하거나 새 텍스트, 이미지를 추가할 때 사용할 수 있는 가상 요소.
- ::after
::before와 똑같으나 지정된 요소의 뒤에 적용할 수 있는 가상 요소.

다음은 앞서 본 예제 1-1, 1-2에서 ::first-letter와 ::after 가상 요소를 이용하여 새로운 텍스트를 추가하거나 특정 글자의 스타일을 변경한 예제 코드이다. 다음 코드는 예제 1-2의 css 파일 내에 이어서 추가한 것이다. 

```css
/* 생략 */
.parabox p::first-letter {
    font-size: larger;
    font-weight: bold;
    color: darkmagenta;
}
.parabox h1::after {
    content: "AI 소설";
    margin-left: 10px;
    font-size: 15px;
    font-weight: lighter;
    color: burlywood;
    background-color: blueviolet;
}
```

![예제 2-1의 적용에 따른 실행결과](/images/2024-01-10/css-pseudo-class-and-element/5.png)

예제 2-1의 적용에 따른 실행결과

위 결과를 보면, “길을 잃은 모험가”라는 제목 뒤에 “AI 소설”이라는 새로운 텍스트가 추가되었는데, 이는 ::after 가상 요소를 사용하여 구현한 것이다. 그 아래 단락 테스트의 첫 번째 줄의 첫 번째 글자의 크기가 조금 커졌고, 색도 달라졌는데 이는 ::first-letter 가상 요소를 이용한 것이다. 이렇듯 가상 요소는 HTML 문서에는 없는 내용도 추가할 수 있는 특징을 가지고 있다. 

---
References

[1] [비전공자를 위한 HTML/CSS](https://www.boostcourse.org/cs120/lecture/92902/)

[2] [비전공자를 위한 HTML/CSS](https://www.boostcourse.org/cs120/lecture/92903?isDesc=false)

[3] 가상 클래스 개념 및 종류

[의사 클래스 - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-classes)

[4] 가상 요소 개념 및 종류

[의사 요소 - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-elements)

[5] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석