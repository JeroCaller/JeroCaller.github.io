---
title: "[CSS][보충] Position을 이용한 배치"
category: "CSS"
tag: ["web", "css", "position"]
---

css의 position에 대한 전반적인 내용은 [CSS - 레이아웃 및 관련 속성들](/css/css-layout-and-its-attributes/) 페이지에서 볼 수 있다. 

# relative vs absolute

position의 속성값으로 가질 수 있는 relative와 absolute의 차이점은 무엇일까? 

relative는 요소 자신을 기준으로 한다. position 속성값을 relative로 정한 후, left, top 등의 속성을 통해 위치를 지정하면 그 곳으로 이동되는데, 이 이동되는 기준은 원래 자기 자신 요소가 있던 곳을 기준으로 한다. 예를 들어 원래 요소가 웹 화면의 위에서 100px, 왼쪽에서 200px 떨어진 상태이고, 해당 요소에 다음과 같이 스타일을 지정하면..

```css
.box {
    position: relative;
    top: 50px;
    left: 50px;
}
```

해당 요소의 위치는 웹 화면 기준으로 위쪽에서 100 + 50 = 150px, 왼쪽에서 200 + 50 = 250px이 된다. 즉, 원래 해당 요소가 있던 위치를 기준으로 이동하는 것이다. 

그런데 relative로 설정되어 원래와는 다른 곳으로 이동된 요소의 원래 위치에는 화면 상에는 아무것도 보이지 않지만, 사실 해당 요소가 구조상으로는 원래 위치에 그대로 있게 된다. 그래서 div 박스 3개로 수직 정렬된 상태에서, 두 번째 div 상자를 position: relative를 통해 다른 위치로 이동시키면, 3번째 박스가 2번째 박스가 있던 위치로 들어가지 않고 그대로 있게 된다. 그 이유는 1, 3번째 div 박스 사이에는 구조 상으로는 2번째 div 박스가 그대로 있기 때문이다. 어떻게 보면 화면 상에서 이동된 요소는 일종의 “할루시네이션”이라고도 볼 수 있겠다. 

absolute는 position: relative 속성이 설정된 부모 요소를 기준으로 한다. 이 때 주의할 점은 아무리 HTML 코드 구조 상 부모 요소가 존재하더라도 그 부모 요소에 position: relative와 같은 기준이 없다면 해당 요소를 무시하고, 해당 속성을 가지는 부모를 계속 올라가 찾아 그 부모 요소를 기준으로 삼는다. 만약 position: relative 속성이 적용된 부모 요소가 없다면, 최상위 요소인 body를 기준으로 한다. 

relative로 이동한 요소는 화면 상으로는 이동되었더라도 구조 상으로는 그 자리에 그대로 있는 반면, absolute로 이동한 요소는 구조 상으로도 이동되어 앞선 예의 경우, 3번째 div 박스가 2번째 div 박스가 있던 자리로 올라간다. 

위 사실을 확인하기 위해 다음의 예제를 보자. 다음은 창고에 박스가 높이 쌓여있는 상황을 가정하였다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .storage {
            width: 500px;
            height: 400px;
            background-color: firebrick;
        }
        .box {
            width: 200px;
            height: 30%;
            background-color: coral;
            border: 2px solid black;
            
            display: flex;
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
    <h1>창고</h1>
    <div class="storage">
        <div class="box">A4용지</div>
        <div class="box">종이컵</div>
        <div class="box">휴지</div>
    </div>
</body>
</html>
```

예제 1-1

![예제 1-1 실행결과](/images/2024-07-19/css-position-layout/1.png)

예제 1-1 실행결과

여기서 중간에 있는 박스를 꺼내고자 한다. 그래서 다음의 스타일 규칙을 추가하였다. 

```css
.storage .box:nth-child(2) {
  position: relative;
  left: 250px;
  top: 130px;
}
```

예제 1-2

![예제 1-2 실행결과](/images/2024-07-19/css-position-layout/2.png)

예제 1-2 실행결과

위 코드를 추가하여 웹 화면을 보면 두 번째 상자가 오른쪽으로 꺼내진 것을 볼 수 있다.  그런데 3번째 div 요소인 “휴지”를 담은 상자가 원래 “종이컵” 상자가 있던 자리로 이동하지 않는 것을 볼 수 있다. 

이번에는 해당 요소의 position 값을 absolute로 바꿔보겠다. 이를 위해, 부모 요소인 `<div class=”storage”>` 의 스타일에 `position: relative;` 를 추가한다. 

```css
.storage {
  width: 500px;
  height: 400px;
  background-color: firebrick;
  position: relative;
}
.box {
  width: 200px;
  height: 30%;
  background-color: coral;
  border: 2px solid black;

  display: flex;
  justify-content: center;
  align-items: center;
}
.storage .box:nth-child(2) {
  position: absolute;
  left: 250px;
  top: 130px;
}
```

예제 1-3

![예제 1-3 실행결과](/images/2024-07-19/css-position-layout/3.png)

예제 1-3 실행결과

위와 같이 이번에는 “종이컵” 상자가 다른 곳으로 이동하자 “휴지” 상자가 원래 “종이컵” 상자가 있던 곳으로 이동된 것을 볼 수 있다. 

# fixed

`position: fixed;` 로 설정하면, 해당 요소는 viewport를 기준으로 삼는다. 그러면 웹 페이지에서 스크롤하더라도 해당 요소는 지정된 위치에 그대로 존재하게 된다. 

이를 활용하면, 웹 페이지 내용이 너무 많아 스크롤을 길게 해야 할 경우, 맨 아래까지 스크롤한 뒤 맨 위로 다시 올라가야 할 때 그만큼 스크롤하는 것이 귀찮으므로, 이 때 “맨 위로 이동” 버튼을 position: fixed; 로 설정하면 웹 페이지의 어떤 곳에 위치하더라도 해당 버튼을 누를 수 있어 언제든지 맨 위로 올라갈 수 있게 된다. 

다음은 `position: fixed` 속성을 이용하여 “맨 위로”와 “맨 아래로” 이동시키는 버튼을 구현한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>맨 위로 버튼 구현</title>
    <style>
        .container {
            width: 60em;
            padding: 1em;
            text-indent: 1em;
            margin: 1em auto;
            border: 1px solid black;
        }

        .move-buttons {
            position: fixed;
            bottom: 1em;
            right: 1em;

            display: flex;
            flex-direction: column;
        }
        .move-buttons input[type="button"] {
            width: 5em;
            height: 2em;
            margin-bottom: 1em;
            background-color:darkgoldenrod;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 id="most-top">서론</h1>
        <p>
            <!-- 생략... -->
        </p>
        <h1>본론</h1>
        <p>
            <!-- 생략... -->
        </p>
        <h1 id="most-bottom">결론</h1>
        <p>
            <!-- 생략... -->
        </p>
    </div>
    <div class="move-buttons">
        <a href="#most-top"><input type="button" value="맨 위로"/></a>
        <a href="#most-bottom"><input type="button" value="맨 아래로"/></a>
    </div>
</body>
</html>
```

예제 2-1

<video src="/resources/2024-07-19/css-position-layout/1.mp4"
    controls="controls"
    muted="muted"
    width="70%"
></video>

---

References

[1] 에이콘아카데미 강남 강의