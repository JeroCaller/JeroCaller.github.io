---
title: "[CSS] z-index and stacking context"
category: "CSS"
tag: ["web", "CSS", "z-index", "stacking context"]
---

# z-index

[CSS - 레이아웃 및 관련 속성들](/css/css-layout-and-its-attributes/) 페이지의 “position” 챕터의 예제를 보면, div 요소들로 표현된 상자들이 있고, 한 상자가 다른 상자의 위에 떠 있는 것을 볼 수 있다. 이렇게, 웹 페이지에서는 웹 요소들을 겹치고, 그 요소들 중 어느 것을 위에 놓을 것이고, 어느 것을 아래에 놓을 것인지를 결정할 수 있다. 이를 통해 사용자 입장에서는 마치 종이들을 겹치게 쌓아놓은 것처럼 보이게 할 수 있다. position 속성 말고도, 아예 요소들의 쌓임 순서를 지정할 수 있는 속성인 z-index가 존재한다. 

z-index 속성은 그 값으로 음의 정수, 0, 양의 정수 중 하나를 받을 수 있으며, 숫자가 크면 클수록 상대적으로 작은 숫자를 가지는 요소들보다 더 위에 쌓이게 된다. 해당 속성은 auto 키워드가 기본 속성값으로 정해져 있다. auto 값은 요소의 쌓임 순서를 0으로 지정한다. 

먼저, z-index 속성을 전혀 사용하지 않은 예를 살펴보겠다. 다음 예제는 div 요소를 이용하여 4개의 박스들을 형성하고, 이를 겹치게끔 한 예제로, 이 예제에서는 z-index 속성을 전혀 사용하지 않았다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="z-index-ex1.css">
</head>
<body>
    <div id="container">
        <div class="box" id="box1">
            <p>Box 1</p>
            <p>z-index: </p>
        </div>
        <div class="box" id="box2">
            <p>Box 2</p>
            <p>z-index: </p>
        </div>
        <div class="box" id="box3">
            <p>Box 3</p>
            <p>z-index: </p>
        </div>
        <div class="box" id="box4">
            <p>Box 4</p>
            <p>z-index: </p>
        </div>
    </div>
</body>
</html>
```

```css
#container {
    position: relative;
}
.box {
    position: absolute;
    border: 1px solid black;
    width: 200px;
    height: 200px;
}

#box1 {
    background-color: aquamarine;
}
#box2 {
    background-color: bisque;
    left: 70px;
    top: 80px;
}
#box3 {
    background-color: coral;
    left: 140px;
    top: 150px;
}
#box4 {
    background-color: cornflowerblue;
    left: 210px;
    top: 220px;
}
```

![예제 1-1, 1-2 실행결과](/images/2024-01-27/css-z-index-and-stacking-context/1.png)

예제 1-1, 1-2 실행결과

HTML 코드를 보면 “Box1” 부터 순서대로 “Box4”까지 위에서 아래로 코드를 작성하였으며, 그 결과 웹 페이지 화면에서는 “Box1”이 맨 아래에, “Box4”가 맨 위에 쌓이게 되었다. 이렇게, z-index를 사용하지 않는 경우, HTML 코드 내 정의된 순서에 따르는 것을 알 수 있다. 

이번에는, z-index 속성을 적용하여 “Box 1”이 가장 위에, “Box 4”가 가장 아래에 쌓이도록 수정한 코드이다. 이 때, HTML 코드는 예제 1-1을 그대로 가져와 z-index를 묘사하는 텍스트에 그 속성값만 추가하였다. 따라서 다음 예제에서는 CSS 파일만 보여주겠다. 

```css
#container {
    position: relative;
}
.box {
    position: absolute;
    border: 1px solid black;
    width: 200px;
    height: 200px;
}
.box p {
    text-align: right;
}

#box1 {
    background-color: aquamarine;
    z-index: 3;
}
#box2 {
    background-color: bisque;
    left: 70px;
    top: 80px;
    z-index: 2;
}
#box3 {
    background-color: coral;
    left: 140px;
    top: 150px;
    z-index: 1;
}
#box4 {
    background-color: cornflowerblue;
    left: 210px;
    top: 220px;
    z-index: 0;
}
```

![예제 1-3 실행결과](/images/2024-01-27/css-z-index-and-stacking-context/2.png)

예제 1-3 실행결과

위 예제 1-3을 보면 알겠지만, “Box 1”의 z-index 속성값에는 다른 박스들의 것보다 가장 높은 숫자인 3을 부여하였고, 그 뒤에는 차례대로 2, 1, 0을 부여하였다. 그 결과 쌓임 순서가 아까와는 역전된 것을 확인할 수 있다. 

# 쌓임 맥락 (Stacking context)

이번에는 앞선 예제들과 비슷하지만 조금 다른 예제를 준비하였다. 먼저 코드와 그 실행 결과부터 보자.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="z-index-multiple.css">
</head>
<body>
    <div id="container">
        <div class="sub-edge" id="sub-container1">
            <div class="box" id="box1">
                <p>Box 1</p>
                <p>z-index: 3</p>
            </div>
            <div class="box" id="box2">
                <p>Box 2</p>
                <p>z-index: 2</p>
            </div>
        </div>
        <div class="sub-edge" id="sub-container2">
            <div class="box" id="box3">
                <p>Box 3</p>
                <p>z-index: 1</p>
            </div>
            <div class="box" id="box4">
                <p>Box 4</p>
                <p>z-index: 0</p>
            </div>
        </div>
    </div>
</body>
</html>
```

```css
#container {
    position: relative;
}
.sub-edge {
    width: 500px;
    height: 500px;
}
#sub-container1 {
    border: 2px solid blue;
    position: absolute;
    z-index: 1;
}
#sub-container2 {
    border: 2px solid red;
    position: absolute;
    top: 50px;
    left: 50px;
    z-index: 2;
}
.box {
    position: absolute;
    border: 1px solid black;
    width: 200px;
    height: 200px;
}
.box p {
    text-align: right;
}

#box1 {
    background-color: aquamarine;
    z-index: 3;
}
#box2 {
    background-color: bisque;
    left: 70px;
    top: 80px;
    z-index: 2;
}
#box3 {
    background-color: coral;
    left: 50px;
    top: 110px;
    z-index: 1;
}
#box4 {
    background-color: cornflowerblue;
    left: 110px;
    top: 180px;
    z-index: 0;
}
```

![예제 2-1, 2-2 실행 결과](/images/2024-01-27/css-z-index-and-stacking-context/3.png)

예제 2-1, 2-2 실행 결과

앞서 살펴본 예제 1-3과 비교하여 각 상자에 부여한 z-index는 변하지 않았지만 이상한 현상을 볼 수 있다. “Box 3”의 z-index는 분명 1로, “Box 1”의 3, “Box 2”의 2보다 더 작은 수임에도 “Box 3”이 두 박스보다 더 위에 존재한다. 이건 어떻게 된 일일까? 

이를 이해하려면 쌓임 맥락(stacking context)에 대해 알아야 한다. 쌓임 맥락의 큰 틀에서의 의미는 가상의 z축을 이용하여 HTML 요소들을 3차원으로 확장시키는 개념이다[2]. 하지만 웹 요소로 범위를 한정하여 정의하자면, 쌓임 맥락은 서로 똑같은 부모 요소를 가지면서, 서로 z-index 값을 통해 쌓임 순서를 비교하여 그 순서가 결정되는 요소들의 그룹이라고 볼 수 있다[6]. 한 마디로, 같은 부모 요소들을 가지며 그와 동시에 z-index 값을 통해 서로 쌓임 순서를 비교하는 요소들을 포함하는 일종의 “컨테이너”라 보면 되겠다. 

다시 예제 2-1, 2-2를 살펴보면, 앞선 예제 1-1~3과는 다르게 이번에는 “container”라는 큰 컨테이너 안에 각각 “sub-container1”, “sub-container2”라는 이름의 부분 컨테이너가 정의되었고, 각각의 부분 컨테이너 안에 각각 2개의 div 박스가 들어가 있는 형태이다. 여기서 4개의 박스들은 2개로 나뉘어 서로 다른 쌓임 맥락 안에 들어가 있는 것이다. 이렇게 판단할 수 있는 근거로 다음이 있다. 

1. “Box 1~2”와 “Box 3~4”는 서로 다른 부모 요소를 가진다. “Box 1~2”는 “sub-container1”을 부모 요소로, “Box 3~4”는 “sub-container2”를 부모 요소로 가진다. 
2. “sub-container1”과 “sub-container2”는 각각 스타일 규칙에서 position과 z-index를 사용하였다. 따라서 이 두 컨테이너들은 각각 서로 별개의 쌓임 맥락을 가지게 된다. 

이와 더불어, 예제 2-2의 CSS 코드를 자세히 살펴보면, #sub-container1 자체는 z-index를 1로, #sub-container2는 z-index를 2로 가지므로, 애초에 sub-container2의 z-index가 더 크므로, 웹 페이지 상에서는 당연히 sub-container2와 그 자식 요소들이 sub-container1과 그 자식 요소들보다 더 위에 쌓이게 될 수 밖에 없다. 이러한 이유로 예제 2-1~2의 실행결과에서 보는 현상이 나타나는 것이다. 

이를 통해 알 수 있는 사실로, 첫째는 쌓임 맥락이 항상 웹 페이지 전역의 공간을 다 포함하는 개념은 아니며(물론 전역 공간을 차지하는 쌓임 맥락도 있긴 하다. 이에 대한 내용은 아래 참조), 둘째는 국소적인(local) 영역을 가지는 쌓임 맥락을 두 개 이상 생성할 수 있다는 것이다. 

이와 더불어, 쌓임 맥락에는 다음의 특징들도 있다. 

- 쌓임 맥락 내부에 다른 쌓임 맥락을 포함하여 부모-자식 관계를 가지는 계층적인 쌓임 맥락 구조를 이루게 할 수도 있다.
- 부모-자식 관계가 아니고 서로 형제 관계에 놓인 둘 이상의 쌓임 맥락들은 서로 독립적인 존재이다. 앞서 살펴본 예제에서처럼, A라는 쌓임 맥락 안에 놓여 있는 요소들의 z-index는 B라는 형제 쌓임 맥락에 놓여 있는 요소들의 z-index에 영향을 끼칠 수 없다. 이로 인해 A의 z-index 값이 B보다 더 높으면 아무리 B 내부의 특정 요소들이 A 내부의 요소들의 z-index값보다 높아도 항상 A와 그 자식 요소들이 B보다 더 위에 쌓이게 된다.

또한, 모든 요소들이 쌓임 맥락을 항상 생성하는 것은 아니다. 어떤 요소가 쌓임 맥락을 생성하려면 여러 조건들 중 하나를 만족해야 하는데, 그 중 일부 조건들을 소개하면 다음과 같다. 

- 루트 요소. `<html>` 요소를 가리키는 것이나, 사실 `<html>` 태그 안에는 `<head>`, `<body>`로 나뉠 수 있고, 실질적으로 코드를 작성하는 곳은 `<body>` 태그이므로, 사실 상 `<body>` 태그가 루트 쌓임 맥락을 생성한다고 볼 수 있다. 또한, `<body>` 태그가 모든 하위 요소들의 최상위 부모 요소이므로, 화면에서 본다면 `<body>` 요소의 쌓임 맥락은 웹 페이지 전체 공간을 차지한다고도 볼 수 있겠다. 루트 쌓임 맥락은 아래에서 언급할 다른 조건들과 비교하여, 특별히 무언가를 충족해야할 조건이 없으므로, 루트 쌓임 맥락은 자동으로 생성되는 것으로 보인다.
- position의 속성값이 absolute, relative 중 하나를 가지면서, 그와 동시에 z-index의 속성값이 “auto”가 아닌 요소. 앞서 살펴본 예제의 CSS 파일들을 보면 특정 선택자의 스타일 규칙 내에 항상 position 속성과 그 속성값이 absolute, relative 중 하나를 가지며, 그와 동시에 z-index 속성과 그 값을 지정하고 있는 것을 볼 수 있다.
- z-index가 없어도, position의 속성값이 fixed, sticky 중 하나일 경우.
- 요소의 불투명도를 조정하는 속성인 opacity가 1보다 작은 요소일 경우.

이 외에도 쌓임 맥락을 생성하는 조건들이 더 많이 존재하므로 이에 대한 자세한 사항은 Reference [2]의 "쌓임 맥락 - CSS: Cascading Style Sheets \| MDN" 사이트를 참조.

위에서 언급한 쌓임 맥락 형성 조건에서 볼 수 있듯, 꼭 z-index 속성을 써야만 쌓임 맥락이 형성되는 것은 아니란 것도 알 수 있다. 

---

References

[1] z-index

[z-index - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/z-index)

[2] z-index → 쌓임 맥락

[쌓임 맥락 - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context)

[3] z-index

[[CSS] z-index와 쌓임 맥락 (stacking context)](https://velog.io/@moonheekim0118/CSSz-index와-쌓임-맥락-stacking-context)

[4] 쌓임 맥락

[쌓임 맥락 예제2 - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context_example_2)

[5] z-index와 쌓임 맥락

[[번역] z-index와 쌓임 맥락에 대한 오해](https://itchallenger.tistory.com/880)

[6] [The CSS z-index Property: What You Need to Know](https://blog.hubspot.com/website/z-index)