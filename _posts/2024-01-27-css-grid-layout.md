---
title: "[CSS] Grid layout"
category: "CSS"
tag: ["css", "web", "layout", "grid", "flex"]
---
# Grid layout

그리드 레이아웃은 웹 요소들을 하나의 격자 형태로 배치하는 레이아웃을 말한다. 이 레이아웃을 이용하면 웹 요소들을 좀 더 질서있게 배치할 수 있다. 

그리드 레이아웃의 장점으로 다음이 있다고 한다.

- 사용자에게 시각적으로 안정된 디자인을 제공할 수 있다.
- 내용을 삽입하지 않은 상태에서도 미리 웹 사이트의 레이아웃을 살펴볼 수 있다. 이는 곧 웹 요소들의 배치 구조와 웹 요소에 삽입할 내용을 분리할 수 있다는 것이고, 배치 구조에 문제점이 있다면 내용은 건드리지 않고 배치 구조만 신경쓰면 된다는 뜻이다. 이는 유지보수의 장점으로 이어질 것이다.
- 일반적인 방법에 비해 그리드 레이아웃을 사용하면 원하는 위치에 원하는 요소를 배치하게끔 할 수 있어 개발자에게 좀 더 높은 자유도를 제공한다.

# Grid Layout의 종류

그리드 레이아웃에는 크게 두 가지 종류가 있다.

## Flexible box layout

플렉스 박스 레이아웃(Flex box layout)이라고도 불리는 플렉서블 박스 레이아웃(Flexible box layout)은 수평, 수직 중 한 방향을 주축으로 정한 후, 이를 기준으로 주축을 따라 박스들을 한 줄씩 배치하는 형태의 레이아웃이다. 배치되는 요소들에 대해 각각의 크기를 부여할 수도 있다. 기준축을 하나로 잡기 때문에 “1차원” 레이아웃이라고도 표현한다. 

## CSS Grid layout

CSS 그리드 레이아웃은 수평, 수직 중 한 방향을 무조건 정해 “주축”으로 삼고, 이를 기준으로 박스들을 배치하는 플렉스 박스 레이아웃에 비해, 두 방향 모두를 기준으로 삼아 자유롭게 박스 요소들을 배치할 수 있는 레이아웃이다. 플렉스 박스 레이아웃과 달리 수평, 수직축 모두를 기준축으로 잡기에 “2차원” 레이아웃이라고도 표현된다. 

# Flex box layout

## display

플렉스 박스 레이아웃을 사용하기 위해선 우선 해당 레이아웃을 적용시킬 웹 요소들을 모두 포함시키는 컨테이너(부모 요소)가 있어야 한다. 이는 display 속성에 다음의 속성값들 중 하나를 입력하면 생성할 수 있다. 

- flex : 플렉스 박스 부모 요소 내 모든 요소들을 블록 요소들로 만들어 배치한다.
- inline-flex : 플렉스 박스 부모 요소 내 모든 요소들을 인라인 요소로 만들어 배치한다.

다음은 위 속성을 이용하여 박스 요소들을 플렉스 박스 레이아웃으로 배치한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flexbox1.css">
</head>
<body>
    <div class="container">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="container2">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="container3">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="container4">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    height: 100px;
    display: flex;
    margin: 10px auto;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#container2 {
    display: inline-flex;
}
#container3 {
    justify-content: center;
}
#container4 {
    align-items: center;
}
```

![에제 1-1~2 실행결과](/images/2024-01-27/css-grid-layout/1.png)

에제 1-1~2 실행결과

## flex-direction

앞서, 플렉스 박스 레이아웃에는 수평, 수직 방향 중 하나를 주축으로 설정하고, 이 주축을 기준으로 박스들을 배치한다고 하였다. 생성된 플렉스 박스 부모 요소에서 주축을 설정하는 속성은 flex-direction으로, 해당 속성에 들어갈 수 있는 값으로는 다음이 존재한다. 

- row : 주축을 수평 방향으로 설정한다. 그리고 이에 따라 배치될 박스들은 왼쪽에서 오른쪽 방향으로 배치된다. 기본값으로 설정되어 있는 속성값이다.
- row-reverse : 주축을 수평 방향으로 설정한다. row와 달리, 배치될 박스들을 오른쪽에서 왼쪽으로 배치한다.
- column : 주축을 수직 방향으로 설정한다. 배치될 박스 요소들을 위에서 아래 방향으로 배치한다.
- column-reverese : 주축을 수직 방향으로 설정한다. column과 달리, 배치될 박스 요소들을 아래에서 위 방향으로 배치한다.

다음은 위 속성값들을 하나씩 적용하여 각각의 속성값들의 차이점을 확인하고자 만든 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flex-direction.css">
</head>
<body>
    <div class="container" id="row">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="row-reverse">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="column">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="column-reverse">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    display: flex;
    margin: 10px auto;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#row {
    flex-direction: row;
}
#row-reverse {
    flex-direction: row-reverse;
}
#column {
    flex-direction: column;
}
#column-reverse {
    flex-direction: column-reverse;
}
```

![예제 2-1~2 실행결과](/images/2024-01-27/css-grid-layout/2.png)

예제 2-1~2 실행결과

## flex-wrap

정해진 플렉스 박스 부모 요소의 너비를 넘을 만큼의 많은 자식 요소들이 배치될 경우, 그럼에도 한 줄에만 모든 요소들을 배치할 것인지 아니면 다음 줄로 넘어가게 할 것인지를 결정할 수 있는 속성이 flex-wrap이다. 해당 속성의 값으로 다음이 존재한다. 

- nowrap : 플렉스 박스 레이아웃 내에 배치할 자식 요소들이 컨테이너의 너비를 넘더라도 한 줄에 배치한다. 이 경우, 자식 요소의 너비를 정하더라도 컨테이너 너비에 맞게끔 축소될 수 있다. 기본값으로 설정되어 있다.
- wrap : 컨테이너 너비를 넘으면 다음에 배치될 요소들을 다음 줄에 배치한다.
- wrap-reverse : wrap과 동일하나, 배치 순서를 wrap과 정반대로 한다.

다음은 각각의 속성값들을 적용한 결과를 알아보기 위한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flex-wrap.css">
</head>
<body>
    <div class="container" id="no-wrap">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
        <div class="box">Box 5</div>
        <div class="box">Box 6</div>
        <div class="box">Box 7</div>
        <div class="box">Box 8</div>
        <div class="box">Box 9</div>
    </div>
    <div class="container" id="wrap">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
        <div class="box">Box 5</div>
        <div class="box">Box 6</div>
        <div class="box">Box 7</div>
        <div class="box">Box 8</div>
        <div class="box">Box 9</div>
    </div>
    <div class="container" id="wrap-reverse">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
        <div class="box">Box 5</div>
        <div class="box">Box 6</div>
        <div class="box">Box 7</div>
        <div class="box">Box 8</div>
        <div class="box">Box 9</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    display: flex;
    margin: 10px auto;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#no-wrap {
    flex-wrap: nowrap;
}
#wrap {
    flex-wrap: wrap;
}
#wrap-reverse {
    flex-wrap: wrap-reverse;
}
```

![예제 3-1~2 실행결과](/images/2024-01-27/css-grid-layout/3.png)

예제 3-1~2 실행결과

위 실행결과를 보면, 맨 처음에 나오는 플렉스 박스 레이아웃은 flex-wrap: nowrap으로 설정하였다. 그 결과, 내부에 들어갈 박스들의 너비가 이미 정해진 상태임에도 한 줄에 모두를 배치하기 위해 그 너비가 축소되었음을 확인할 수 있다. 

> flex-flow 속성은 flex-direction, flex-wrap 속성을 한꺼번에 지정할 수 있는 속성이다. flex-flow: row wrap; 과 같이 지정하면 된다.
> 

## justify-content

주축 방향을 기준으로 요소들의 정렬 방식을 지정하는 속성이다. 다음은 해당 속성에 지정 가능한 값들이다. 

- flex-start : 주축 시작점을 기준으로 배치
- flex-end : 주축 끝점을 기준으로 배치
- center : 주축 중앙을 기준으로 배치
- space-between : 주축 방향으로 배치된 여러 요소들 중 주축의 시작점에 가장 가까운 첫 번째 요소와 주축의 끝점에 가까운 마지막 요소를 각각 주축 시작점, 끝점에 배치한 후, 그 외 가운데에 있는 요소들은 등간격으로 배치.
- space-around : 모든 요소들을 등간격으로 배치. 하지만 양쪽 끝에 있는 요소들과 주축의 양쪽 끝 사이의 간격은 가운데 요소들의 간격과 다름.
- space-evenly : 모든 요소들을 등간격으로 배치. space-around와는 달리, 주축의 양 끝점과 양 끝에 있는 요소들 간의 간격도 사이에 있는 요소들 간 간격과 동일하게 배치한다.

다음은 위 속성값들을 적용하여 그 차이를 비교한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flex-justify-content.css">
</head>
<body>
    <div class="container" id="flex-start">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="flex-end">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="center">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="space-between">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="space-around">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="space-evenly">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    display: flex;
    margin: 10px auto;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#flex-start {
    justify-content: flex-start;
}
#flex-end {
    justify-content: flex-end;
}
#center {
    justify-content: center;
}
#space-between {
    justify-content: space-between;
}
#space-around {
    justify-content: space-around;
}
#space-evenly {
    justify-content: space-evenly;
}
```

![예제 4-1~2 실행결과](/images/2024-01-27/css-grid-layout/4.png)

예제 4-1~2 실행결과

## align-items

주축과 수직으로 직교하는 교차축을 기준으로 요소들을 정렬하고자 한다면 align-items 속성을 사용하면 된다. 해당 속성에 입력할 수 있는 값으로는 앞서 살펴본 justify-content 속성의 flex-start, flex-end, center를 그대로 사용할 수 있다. 그 외에 align-items에만 사용할 수 있는 속성값에는 다음이 있다. 

- baseline : 교차축 방향으로 정렬된 요소들을 요소 안 문자의 기준선에 맞춰 정렬한다. 여기서 문자 기준선은 대부분의 문자들의 밑부분을 의미한다. 영어의 경우, 다음의 사진 참조.

![사진 1. baseline의 정의. References [2] 참조.](/images/2024-01-27/css-grid-layout/ref-1.png)

사진 1. baseline의 정의. References [2] 참조.

- stretch : 자식 요소들을 교차축 방향으로 늘려 채운다.

다음은 여러 속성값들을 넣어 차이를 비교한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flex-align-items.css">
</head>
<body>
    <div class="container" id="flex-start">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="flex-end">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="center">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
    <div class="container" id="baseline">
        <div class="box">1</div>
        <div class="box" style="font-size:30px;">2</div>
        <div class="box">3</div>
        <div class="box" style="font-size:25px;">4</div>
    </div>
    <div class="container">
        <div class="box">1</div>
        <div class="box" style="font-size:30px;">2</div>
        <div class="box">3</div>
        <div class="box" style="font-size:25px;">4</div>
    </div>
    <div class="container" id="stretch">
        <div class="box">Box 1</div>
        <div class="box">Box 2</div>
        <div class="box">Box 3</div>
        <div class="box">Box 4</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    height: 100px;
    display: flex;
    margin: 10px auto;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#flex-start {
    align-items: flex-start;
}
#flex-end {
    align-items: flex-end;
}
#center {
    align-items: center;
}
#baseline {
    align-items: baseline;
}
#stretch {
    align-items: stretch;
}
```

![예제 5-1~2 실행결과](/images/2024-01-27/css-grid-layout/5.png)

예제 5-1~2 실행결과

위 실행결과에서, 아래에서 2, 3번째 플렉스 박스들은 각각 baseline으로 지정했을 때와 그렇지 않은 경우를 대조하기 위한 박스들이다. 

## align-self

교차축 방향으로 배치된 요소들 중 특정 요소들에만 특정 정렬 방식을 적용하고자 하는 경우 align-self 속성을 사용하면 된다. 플렉스 박스 컨테이너가 아닌 그 자식 요소에 적용해야 한다. 속성값은 align-items와 동일하다. 다음은 해당 속성을 사용한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="flex-align-self.css">
</head>
<body>
    <div class="container">
        <div class="box">Box 1</div>
        <div class="box" id="flex-start">Box 2</div>
        <div class="box" id="flex-end">Box 3</div>
        <div class="box" id="center">Box 4</div>
        <div class="box" id="stretch">Box 5</div>
    </div>
</body>
</html>
```

```css
.container {
    border: 1px solid black;
    width: 500px;
    height: 100px;
    display: flex;
    margin: 10px auto;
    align-items: center;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#flex-start {
    align-self: flex-start;
}
#flex-end {
    align-self: flex-end;
}
#center {
    align-self: center;
}
#stretch {
    align-self: stretch;
}
```

![예제 6-1~2 실행결과](/images/2024-01-27/css-grid-layout/6.png)

예제 6-1~2 실행결과

## align-content

플렉스 박스 컨테이너 내에 배치된 요소들이 2줄 이상으로 배치되어 있을 때, 교차축 기준으로 자식 요소들의 정렬 방식을 align-content를 통해 지정할 수 있다. 해당 속성값으로 flex-start, flex-end, center, space-between, space-around, stretch를 사용할 수 있다. 다음은 각 속성값들을 해당 속성에 적용하여 그 차이를 비교하는 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>align-content</title>
    <link rel="stylesheet" href="flex-align-content.css">
</head>
<body>
    <div id="toplevel-container">
        <div class="super-container">
            <h3>default</h3>
            <div class="flex-container">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>flex-start</h3>
            <div class="flex-container" id="flex-start">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>flex-end</h3>
            <div class="flex-container" id="flex-end">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>center</h3>
            <div class="flex-container" id="center">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>space-between</h3>
            <div class="flex-container" id="space-between">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>space-around</h3>
            <div class="flex-container" id="space-around">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
        <div class="super-container">
            <h3>stretch</h3>
            <div class="flex-container" id="stretch">
                <div class="box">Box 1</div>
                <div class="box">Box 2</div>
                <div class="box">Box 3</div>
                <div class="box">Box 4</div>
                <div class="box">Box 5</div>
                <div class="box">Box 6</div>
                <div class="box">Box 7</div>
                <div class="box">Box 8</div>
                <div class="box">Box 9</div>
            </div>
        </div>
    </div>
</body>
</html>
```

```css
#toplevel-container {
    display: flex;
    flex-wrap: wrap;
    margin: 10px auto;
}
.super-container {
    border: 2px solid grey;
    padding: 0px;
    margin: 10px;
}
.super-container h3 {
    text-align: center;
}
.flex-container {
    border: 1px solid black;
    width: 300px;
    height: 200px;
    display: flex;
    margin: 0px auto;
    flex-wrap: wrap;
}
.box {
    border: 1px solid black;
    background-color: grey;
    margin: 5px;
    width: 70px;
    text-align: center;
}

#flex-start {
    align-content: flex-start;
}
#flex-end {
    align-content: flex-end;
}
#center {
    align-content: center;
}
#space-between {
    align-content: space-between;
}
#space-around {
    align-content: space-around;
}
#stretch {
    align-content: stretch;
}
```

![예제 7-1~2 실행결과](/images/2024-01-27/css-grid-layout/7.png)

예제 7-1~2 실행결과

## 활용: 요소를 가운데 정렬하기

앞서 살펴본 flex box layout과 justify-content, align-items 속성을 이용하면 요소들을 각각 수평, 수직, 또는 정 가운데로 정렬할 수 있다. 다음은 각각 수평, 수직, 수평-수직 기준으로 가운데 정렬을 하는 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #toplevel-container {
            margin: 10px auto;
            display: flex;
            flex-wrap: wrap;
        }
        .container {
            width: 400px;
            height: 400px;
            margin: 10px auto;
            border: 1px solid blue;
            display: flex;
        }
        .box {
            border: 1px solid black;
            width: 100px;
            height: 100px;
        }
        #align1 {
            justify-content: center;
        }
        #align2 {
            align-items: center;
        }
        #align-center {
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
    <div id="toplevel-container">
        <div class="container" id="align1">
            <div class="box">Box</div>
        </div>
        <div class="container" id="align2">
            <div class="box">Box</div>
        </div>
        <div class="container" id="align-center">
            <div class="box">Box</div>
        </div>
    </div>
</body>
</html>
```

![예제 8-1 실행결과](/images/2024-01-27/css-grid-layout/8.png)

예제 8-1 실행결과

# CSS grid layout

CSS grid layout은 flex box layout과 달리, 수평, 수직축을 모두 활용한다. 그래서 마치 요소들을 표(table)처럼 배치할 수 있다. 그래서인지 CSS 그리드 레이아웃에서는 row와 column이라는 표현을 통해 자식 요소들의 위치를 구분, 표현할 수 있다. 또한 해당 레이아웃은 미리 몇 행 몇 열로 자식 요소들을 채울 것인지 결정할 수 있으며, 각 행, 각 열 사이 간격을 조절할 수도 있다. 

## display

CSS grid layout을 설정하기 위해선 우선 컨테이너를 지정해야 한다. 해당 레이아웃을 사용할 컨테이너 역할을 할 요소에 display 속성을 지정하여 grid 또는 inline-grid 속성값을 지정하면 된다. grid는 컨테이너 내에 배치될 자식 요소들을 블록 레벨로 지정하고, inline-grid는 인라인 레벨로 지정한다. 

## grid-template-rows, grid-template-columns

한글 문서 내에서 표를 만들려면 우선 몇 줄, 몇 칸으로 표를 만들지 결정해야 했다. 여기서도 마찬가지로, css grid layout을 사용하려면 우선 몇 행 몇 열로 배치할 것인지를 미리 정할 수 있다. grid-template-rows는 몇 행으로 지정할 것인지, 그리고 각 행마다 그 높이는 어떻게 할 것인지를 결정할 수 있는 속성이고, grid-template-columns도 마찬가지로 몇 열로 지정할 것인지, 그리고 각 열마다 그 너비는 어떻게 할 것인지를 결정할 수 있는 속성이다. 

다음은 그리드 컨테이너를 생성하고 그 안에 행과 열의 개수 및 크기를 각각 설정하는 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 2px solid blue;
            display: grid;
            grid-template-columns: 200px 200px 200px;
            grid-template-rows: 200px 200px;
        }
        .box {
            border: 1px solid black;
        }
        .box p {
            margin: 0px;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">
            <p>(생략)</p>
        </div>
        <div class="box">
            <p>(생략)</p>
        </div>
        <div class="box">
            <p>(생략)</p>
        </div>
        <div class="box">
            <p>생략</p>
        </div>
            <p>생략</p>
        </div>
        <div class="box">
            <img src="..\\images\\coffee.png" width="200px">
        </div>
    </div>
</body>
</html>
```

![예제 9-1 실행결과](/images/2024-01-27/css-grid-layout/9.png)

예제 9-1 실행결과

위 예제를 보면 id=”container”인 div 요소를 css grid layout의 컨테이너로 지정하기 위해 display: grid라 지정하였고, 각각의 열의 너비를 200px씩 3개를 지정하였으므로 3열이 생성된다. 그리고 각각의 행의 높이를 200px씩 2개로 지정했으므로 역시 2개의 행이 생성된다. 따라서 결국 2 X 3 형태의 테이블 형태의 css grid layout이 완성되었다. 

### fr unit

CSS grid layout을 반응형 웹 디자인의 요소로 사용할 때 각 행과 열의 크기를 px과 같은 고정 크기로 쓰면 반응형 웹 디자인에 맞게 유동적으로 크기가 변하지 않으므로, 이 경우에는 다른 단위를 사용하는 게 좋은데, 바로 fr(fraction) 단위이다. 

다음은 앞선 예제에서 각 열들의 너비를 fr 단위로 바꿔서 사용한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 2px solid blue;
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            /*grid-template-columns: repeat(3, 1fr);*/
            grid-template-rows: 200px 200px;
        }
        .box {
            border: 1px solid black;
        }
        .box p {
            margin: 0px;
        }
    </style>
</head>
<body>
(생략)
</body>
```

![예제 10-1 실행결과](/images/2024-01-27/css-grid-layout/10.png)

예제 10-1 실행결과

`grid-template-columns: 1fr 1fr 1fr;` 이란 표현은, 열을 3개로 나누고, 그 너비는 1:1:1 비율이 되도록 한다는 뜻이다. 만약 1fr 1fr 2fr 이라면 각각 비율이 1:1:2가 되도록 3개의 열의 너비를 정한다는 뜻이다. 

위 예제의 실행결과와 앞선 예제의 실행결과와 비교하면 fr 단위를 사용했을 때 그리드 컨테이너 내 남는 공간 없이 자식 요소들이 모두 공간을 차지하고 있음을 알 수 있다. 또한, fr 단위를 사용하면 브라우저 창을 확대, 축소할 때에도 그에 맞게 자식 요소들의 사이즈도 알맞게 변한다. 

### 관련 함수들

한 편, grid-template-columns, grid-template-rows에 사용할 수 있는 함수들이 대표적으로 2가지가 있다. repeat()와 minmax() 함수이다. 

- repeat()

앞서 `grid-template-columns: 1fr 1fr 1fr;` 와 같이 지정할 때, 같은 단위의 같은 숫자가 3번 반복된다. 이 경우, 일일이 이렇게 타이핑 칠 필요없이 repeat() 함수를 이용하면 더 편리하다. 

```css
grid-template-columns: repeat(3, 1fr);
```

여기서 해당 함수의 첫 번째 인수는 반복할 횟수를 의미한다. 여기서는 3개의 열에 똑같은 너비를 적용할 것이므로 3을 넣었다. 두 번째 인수는 반복 지정할 너비를 대입하면 된다. 

한 편, repeat() 함수에 사용할 수 있는 auto-fit, auto-fill 키워드가 존재한다. 이 키워드를 해당 함수의 첫 번째 인자로 사용하면 현재 웹 페이지 너비에 따라 열의 개수를 자동 조절하도록 할 수 있다. auto-fit의 경우, 열들을 채우고 나서 남는 공간이 있을 경우, 이를 꽉 채우도록 모든 열들의 너비를 자동으로 확대시키고, auto-fill은 남는 공간이 나와도 채우지 않는다. 다음은 두 키워드를 이용한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container1 {
            border: 2px solid blue;
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, auto));
            grid-template-rows: 100px;
            margin: 30px;
        }
        #container2 {
            border: 2px solid blue;
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, auto));
            grid-template-rows: 100px;
            margin: 30px;
        }
        .box {
            border: 1px solid black;
        }
        .box p {
            margin: 0px;
        }
    </style>
</head>
<body>
    <div id="container1">
        <div class="box">1</div>
        <div class="box">2</div>
        <div class="box">3</div>
        <div class="box">4</div>
        <div class="box">5</div>
        <div class="box">6</div>
    </div>
    <div id="container2">
        <div class="box">1</div>
        <div class="box">2</div>
        <div class="box">3</div>
        <div class="box">4</div>
        <div class="box">5</div>
        <div class="box">6</div>
    </div>
</body>
</html>
```

![예제 11-1 실행결과](/images/2024-01-27/css-grid-layout/11.png)

예제 11-1 실행결과

- minmax()

앞선 예제 9-1의 실행결과를 자세히 보면, 윗 칸의 텍스트가 아래의 선을 넘어가는 현상이 발생한다. 이를 방지하기 위해 고정된 사이즈가 아닌 최소, 최대 사이즈를 정해 브라우저 창의 크기가 변하더라도 텍스트가 칸 밖을 넘지 않게 할 수 있다. 다음은 에제 9-1에서 스타일 규칙 일부를 수정한 모습이다. 

```html
<style>
    #container {
        border: 2px solid blue;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-template-rows: minmax(150px, auto);
    }
</style>
```

![예제 11-2 실행결과](/images/2024-01-27/css-grid-layout/12.png)

예제 11-2 실행결과

각 행의 높이의 최소값을 150px로 지정했고, 최대값은 auto로 지정함으로써 150px 이상에서는 자동으로 알맞게 결정하도록 하였다. 

## column-gap, row-gap, gap

박스 사이 간격을 주기 위해 margin을 사용했던 것처럼, css grid layout 내 자식 요소들 사이에도 간격을 줄 수 있는데, column-gap, row-gap, gap 속성을 이용하면 된다. column-gap은 열과 열 사이의 간격을, row-gap은 행과 행 사이의 간격을 줄 수 있다. 이 둘을 한꺼번에 지정하려면 gap 속성을 이용하면 되며, 차례대로 row-gap, column-gap의 값을 지정하면 된다. 다음은 앞선 에제 11-2에서 gap 속성을 추가한 예제이다. 

```html
(생략)
<style>
    #container {
        border: 2px solid blue;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-template-rows: minmax(150px, auto);
        gap: 20px 50px;  /* 행간 간격, 열간 간격*/
    }
(생략)
</style>
(생략)
```

![예제 12-1 실행결과](/images/2024-01-27/css-grid-layout/13.png)

예제 12-1 실행결과

## 칸 확장하기

HTML의 <table>, <th>, <td> 등의 태그를 이용하여 웹 페이지에 표를 나타낼 수 있음을 이전에 살펴본 적 있다. 이 때, 연속된 행 또는 열들의 칸끼리 하나의 칸으로 합칠 수 있음을 본 적이 있다. css grid layout도 마찬가지로 연속된 행 또는 열의 칸들끼리 뭉쳐 하나의 칸으로 합칠 수 있다. 이를 실행하는 방법에는 두 가지가 있다. 

### 그리드 라인 이용하기

설명을 위해 먼저 다음의 예제를 준비하였다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 1px solid grey;
            display: grid;
            grid-template-columns: repeat(4, 100px);
            grid-template-rows: repeat(4, 100px);
            width: 400px;
            margin: 0px auto;
        }
        .box {
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <h3 style="text-align: center;">Tetris</h3>
    <div id="container">
        <div class="box">1</div>
        <div class="box">2</div>
        <div class="box">3</div>
        <div class="box">4</div>
        <div class="box">5</div>
        <div class="box">6</div>
        <div class="box">7</div>
        <div class="box">8</div>
        <div class="box">9</div>
        <div class="box">10</div>
        <div class="box">11</div>
        <div class="box">12</div>
        <div class="box">13</div>
        <div class="box">14</div>
        <div class="box">15</div>
        <div class="box">16</div>
    </div>
</body>
</html>
```

![예제 13-1 실행결과. 실행결과에 설명을 위한 표시를 추가하였다. ](/images/2024-01-27/css-grid-layout/14.png)

예제 13-1 실행결과. 실행결과에 설명을 위한 표시를 추가하였다. 

그리드 컨테이너 안에 16개의 정사각형 자식 요소들을 4 X 4로 배치하였다. 

그리드 레이아웃은 표와 같아서, 각 칸을 형성하기 위해선 위와 같이 그리드 라인이 그어지기 마련이다(실무에서는 아마 그리드 라인을 위 예시처럼 대놓고 보여주는 일이 없을 수도 있다. 그럼에도 그리드 라인은 존재한다). 행의 경우, 맨 위에서부터 차례대로 그리드 라인에 1번 부터 번호가 매겨지고(파란색 표시), 열의 경우 왼쪽에서부터 오른쪽으로 1번 부터 차례대로 번호가 매겨진다(빨간색 표시). 이 그리드 라인 번호를 이용하여 칸을 확장할 수 있다. 이를 수행하는 css 속성에는 다음이 존재한다. 

- grid-column-start : 칸을 합칠 열의 그리드 라인 시작 번호 지정.
- grid-column-end : 칸을 합칠 열의 그리드 라인 끝 번호 지정.
- grid-column : grid-column-start의 값 / grid-column-end의 값 형태로 한꺼번에 지정. 예) grid-column: 2/3;
- grid-row-start : 칸을 합칠 행의 그리드 라인 시작 번호 지정.
- grid-row-end : 칸을 합칠 행의 그리드 라인 끝 번호 지정.
- grid-row : grid-row-start의 값 / grid-row-end의 값 형태로 한꺼번에 지정. 예) grid-row: 1/2;

예를 들어 grid-row: 1/3; 이라 하면, 행에서 맨 위에 있는 1번 그리드 라인부터 3번 그리드 라인 사이에 있는 칸들을 하나로 합치라는 뜻이다. 이 경우, 1번부터 3번 그리드 라인 사이에는 2개의 칸이 있으므로 이 두 칸을 하나로 합친다. 

다음은 위 속성들을 이용하여 테트리스를 완성하기 위해 그리드 컨테이너 내 일부 칸들을 확장시킨 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 1px solid grey;
            display: grid;
            grid-template-columns: repeat(4, 100px);
            grid-template-rows: repeat(4, 100px);
            width: 400px;
            margin: 0px auto;
        }
        .box {
            border: 1px solid black;
        }
        .bluebox {
            background-color: blue;
        }
        .blue-grid-box {
            grid-column: 1/4;
        }
        .redbox {
            background-color: red;
        }
        .red-grid-box {
            grid-row: 2/5;
        }
        .yellowbox {
            background-color: yellow;
        }
        .yellow-grid-box1 {
            grid-row: 2/4;
        }
        .yellow-grid-box2 {
            grid-row: 3/5;
        }
        .sky-grid-box {
            background-color: skyblue;
            grid-column: 4/5;
            grid-row: 1/5;
        }
    </style>
</head>
<body>
    <h3 style="text-align: center;">Tetris</h3>
    <div id="container">
        <div class="box blue-grid-box bluebox">1</div>
        <div class="box sky-grid-box">2</div>
        <div class="box red-grid-box redbox">3</div>
        <div class="box yellow-grid-box1 yellowbox">4</div>
        <div class="box bluebox">5</div>
        <div class="box yellow-grid-box2 yellowbox">6</div>
        <div class="box redbox">7</div>
    </div>
</body>
</html>
```

![예제 13-2 실행결과](/images/2024-01-27/css-grid-layout/15.png)

예제 13-2 실행결과

위 예제와 실행결과에서, 확장되지 않은 작은 정사각형의 칸의 경우 사면에 검은색 경계선을 두르도록 설정하였다. 위 예제에서는 확장되지 않은 칸은 5, 7번 칸으로, 나머지 칸들은 앞서 소개한 속성을 이용하여 하나의 칸으로 합쳐졌음을 알 수 있다. 

### grid-template-areas, grid-area 속성을 이용하여 칸 확장하기

앞서 그리드 라인을 이용하여 칸을 확장하는 것보다 조금 더 직관적으로 수행할 수 있는 방법이 있다. 바로 grid-template-areas와 grid-area 속성을 이용하는 것이다. 우선 예제부터 보겠다. 다음 예제의 실행결과는 앞선 예제 13-2와 동일하다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            border: 1px solid grey;
            display: grid;
            grid-template-columns: repeat(4, 100px);
            grid-template-rows: repeat(4, 100px);
            grid-template-areas: 
                "bluebox bluebox bluebox skybox" 
                "redbox yellowbox1 . skybox" 
                "redbox yellowbox1 yellowbox2 skybox" 
                "redbox . yellowbox2 skybox";
            width: 400px;
            margin: 0px auto;
        }
        .box {
            border: 1px solid black;
        }
        .bluebox {
            background-color: blue;
            grid-area: bluebox;
        }
        .blue-small-box {
            background-color: blue;
        }
        .redbox {
            background-color: red;
            grid-area: redbox;
        }
        .red-small-box {
            background-color: red;
        }
        .yellowbox1 {
            background-color: yellow;
            grid-area: yellowbox1;
        }
        .yellowbox2 {
            background-color: yellow;
            grid-area: yellowbox2;
        }
        .skybox {
            background-color: skyblue;
            grid-area: skybox;
        }
    </style>
</head>
<body>
    <h3 style="text-align: center;">Tetris</h3>
    <div id="container">
        <div class="box bluebox">1</div>
        <div class="box skybox">2</div>
        <div class="box redbox">3</div>
        <div class="box yellowbox1">4</div>
        <div class="box blue-small-box">5</div>
        <div class="box yellowbox2">6</div>
        <div class="box red-small-box">7</div>
    </div>
</body>
</html>
```

파란색 상자를 중점적으로 보면, 맨 처음에 `<div class="box bluebox">1</div>` 코드가 맨 먼저 나옴으로써, 해당 파란색 박스가 1행 1열에 위치하게 된다. 그리고 bluebox 클래스 내에는 grid-area 속성을 통해 해당 영역에 템플릿 이름을 부여한다. 여기서는 bluebox라는 이름을 지어줬다. 그 이후, 그리드 컨테이너인 #container 선택자의 스타일 규칙에 다음과 같은 속성이 지정되었다. 

```css
#container {
    (생략)
    grid-template-areas: 
        "bluebox bluebox bluebox skybox" 
        "redbox yellowbox1 . skybox" 
        "redbox yellowbox1 yellowbox2 skybox" 
        "redbox . yellowbox2 skybox";
    (생략)
}
```

그리드 컨테이너 각 행을 큰 따옴표 한 줄로 표현하며, 각 칸에 grid-area로 지정한 템플릿 이름을 원하는 곳에 대입하면 된다. 위 예제의 경우, bluebox라는 템플릿 이름을 1행의 1, 2, 3열에 지정하였다. 이로써 1행의 1, 2, 3열에는 똑같은 파란색 박스가 연속으로 존재하게 되므로 이 세 칸은 하나의 칸으로 합쳐진다. 이러한 원리로 연속된 칸들을 하나로 합칠 수 있다. 

참고로, 특정 영역은 비우고자 한다면 위 예제에서처럼 마침표(.)를 넣으면 된다. 

정리하자면 다음과 같다. 

1. 확장하고자 하는 그리드 컨테이너 내 자식 요소의 스타일 규칙에 grid-area 속성을 이용하여 템플릿 이름을 지어준다. 
2. 그리드 컨테이너 스타일 규칙에 grid-template-areas 속성을 이용하여 grid-area로 지정된 자식 요소의 템플릿 이름을 원하는 곳에 기입한다. 

이 방법은 그리드 라인을 이용하는 것에 비해, 마치 실제 그리드 칸들을 미리 보는 것과 같은 효과가 있어 좀 더 직관적이다. 


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [What's the difference between flex-start and baseline?](https://stackoverflow.com/questions/34606879/whats-the-difference-between-flex-start-and-baseline)

[3] fr 단위

[[CSS] 5. fr 단위 대하여](https://blog.sonim1.com/198)

[4]  [이번에야말로 CSS Grid를 익혀보자](https://studiomeal.com/archives/533)

[5] [이번에야말로 CSS Flex를 익혀보자](https://studiomeal.com/archives/197)