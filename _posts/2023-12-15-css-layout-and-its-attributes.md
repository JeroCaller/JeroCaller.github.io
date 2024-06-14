---
title: "[CSS] 레이아웃 및 관련 속성들"
category: "CSS"
tag: ["css", "web", "layout"]
---
# display

해당 속성은 요소를 block level 또는 inline level 요소로 만들 때 사용하는 속성이다. 해당 속성값에는 요소를 block 요소로 만드는 block, inline으로 만드는 inline, 두 속성을 모두 가지는 inline-block, 그리고 화면 상에 표시하지 않는 none이 있다. 

display: inline-block 을 이용하면 세로로만 배치되던 박스 모델들을 가로로 배치할 수 있다. 이전 페이지인 [CSS - 박스 모델](/css/css-box-model/) 페이지의 “margin 중첩”의 예제 10-1에서 이를 이용하여 선보인 바 있으니 참고.

# float

블록 요소의 경우 같은 줄에 다른 요소가 침범할 수 없다. 그런데 만약 어떤 요소가 블록 요소와 같은 줄에 사용되어야 할 경우 float 속성을 이용할 수 있다. 해당 속성값으로는 요소를 웹 페이지의 왼쪽에 배치하게끔 하는 left, 오른쪽에 배치하는 right, 그리고 기본값으로, 어디에도 배치하지 않는 none이 있다. 

float는 말 그대로 “떠 있다”의 뜻을 가지고 있는데, 그래서 해당 속성으로 요소를 배치할 때 “요소가 떠 있다”, “요소를 떠 있게 한다”라고 표현하기도 한다. 

다음은 해당 속성을 이용하여 텍스트 글과 함께 양쪽에 그림을 띄우는 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        img {
            width: 20%;
            margin: auto 20px;
        }
        #left {
            float:left;
            /* 이미지 좌우 반전 */
            transform: scaleX(-1);
        }
        #right {
            float:right;
        }
    </style>
</head>
<body>
    <img src="..\images\statue_egypt.jpg" id="left">
    <img src="..\images\statue_egypt.jpg" id="right">
    <p>
        Oh, all life of this world, I am Nut, the mother of the sky. 
        I cover the sky, embrace the earth, and create the stars to shed light on your paths.
        I am Ra, the sun god. I raise the sun during the day and the moon at night to gift you time. 
        In this world that I created, you will continue the cycle of life.
        Oh, all life of this world, I am Osiris, the god of death and resurrection. 
        I promise you eternal life. Even after death, your soul will return as a new life.
        You, honor us, the gods, in our names. 
        And this temple will be the place of your prayers and sacrifices. 
        Here, you will receive our blessings.
    </p>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-12-15/css-layout-and-its-attributes.md/1.png)

예제 1-1 실행결과

# clear

먼저 다음의 예제를 보겠다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .bigbox {
            width: 100%;
            height: 100px;
        }
        .smallbox {
            width: 300px;
            height: 100px;
        }
        #box1 {
            background-color: pink;
            float: left;
        }
        #box2 {
            background-color: brown;
            float: right;
        }
        #box3 {
            background-color: green;
        }
    </style>
</head>
<body>
    <div>
        <div class="smallbox" id="box1">box1</div>
        <div class="smallbox" id="box2">box2</div>
        <div class="bigbox" id="box3">box3</div>
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2023-12-15/css-layout-and-its-attributes.md/2.png)

예제 2-1 실행결과

위 예제에서 만약 box3 요소는 다른 박스 box1, box2와 같은 줄에 위치하게끔 하고 싶지않다면 clear 속성을 사용해야 한다. 해당 속성은 이전에 어떤 요소에 float 속성을 사용하였고, 그 뒤에 올 요소에는 float 속성을 적용하지 않고자 할 때 사용하는 속성이다. 해당 속성은 float: left로 지정된 스타일을 해제하기 위한 값인 left, float: right로 지정된 스타일을 해제하기 위한 값인 right, 그리고 두 방향 모두 해제하는 both 값을 속성값으로 넣을 수 있다. 다음은 위 예제의 box3 속성에 clear: both; 속성을 추가하고 다시 실행한 모습이다. 

```html
<!-- 생략 -->
<style>
    /* 생략 */
    #box3 {
        background-color: green;
        clear: both;
    }
		/* 생략 */
</style>
<!-- 생략 -->
```

![예제 2-2 실행결과](/images/2023-12-15/css-layout-and-its-attributes.md/3.png)

예제 2-2 실행결과

> display: inline-block과 float 속성은 둘 모두 요소들의 박스들을 가로로 배치하는 기능이 있다. 다만, display 속성으로 배치한 박스들에는 기본 margin, padding 여백이 존재하지만 float 속성으로 배치한 박스들에는 기본 margin, padding 여백이 존재하지않아 원할 경우 따로 지정해줘야 한다는 차이점이 있다. 또한, display: inline-block을 특정 요소에 적용하면 그 다음에 오는 요소에는 해당 속성이 적용되지 않지만, float 속성은 해당 속성을 직접 명시하지 않은 요소에도 영향을 주기에 이를 원치 않을 경우 clear 속성을 이용해야 한다는 차이점이 있다.
> 

# position

해당 속성은 웹 요소의 위치를 지정할 때 위치를 지정하는 기준을 설정하는 속성이다. 해당 속성에 대입할 수 있는 속성값으로는 다음이 존재한다.

- static : 위에서 아래 순으로 요소를 배치한다. 기본값.
- relative : 원래 해당 요소가 있어야 할 위치 기준으로 새로운 위치를 지정할 수 있게 해주는 속성값. 그 외에는 웹 문서의 위에서 아래 순으로 요소를 배치시킨다.
- absolute : position: relative;라고 지정된 부모 요소를 기준으로 새 위치를 지정할 때 사용.
- fixed: 브라우저 창을 기준으로 위치를 지정.

그리고, 웹 요소의 위치를 직접 지정하는 속성으로는 left, right, top, bottom이 있으며, 예를 들어 left 속성의 경우, 기준 위치로부터 해당 요소를 왼쪽에서 얼마나 떨어뜨려 위치시킬 것인지를 지정할 수 있다. 

다음은 absolute, relative 속성값의 의미와, left, right, top, bottom 속성이 어떻게 적용되는 것인지를 확인하기 위한 예제 코드이다. (혼동을 최소화하기 위해, 관련이 없는 코드는 생략시켰다. 전체 코드를 원한다면 다음의 주소를 참조.)

[https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css_properties/position3.html](https://github.com/JeroCaller/HTML-CSS-JS-study/blob/main/css_properties/position3.html)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
		<!-- 생략 -->
    <style>
        #edge {
            width: 90%;
            height: 500px;
            border: 1px solid black;
            padding: 10px;
            position: relative;
        }
        /* 생략 */
        .pos1 {
            position: absolute;
            left: 100px;
            top: 200px;
        }
    </style>
</head>
<body>
    <div id="edge">
        <div class="msgbox">
            <span class="material-symbols-outlined">Warning</span>
            <p>ItsGonnaRaiseError: 에러가 발생할 것 같습니다!!</p>
        </div>
        <!--원래 박스의 위치-->
        <div class="msgbox" style="border: 2px solid red">
            <span class="material-symbols-outlined">Warning</span>
            <p>ActuallyNoErrorOccured: 에러가 안 발생했습니다!</p>
        </div>
        <!--position:relative를 적용한 박스의 위치-->
        <div class="msgbox pos1">
            <span class="material-symbols-outlined">Warning</span>
            <p>ActuallyNoErrorOccured: 에러가 안 발생했습니다!</p>
        </div>
    </div>
</body>
</html>
```

![예제 3-1 실행결과](/images/2023-12-15/css-layout-and-its-attributes.md/4.png)

예제 3-1 실행결과

위 예제에서, 우선 작은 메시지 박스들을 담을 큰 박스를 \<div id=’edge’> 태그를 통해 적용시켰다. 그리고 그 안에 여러 작은 메시지 박스들을 담았다. 

세 번째에 위치한 파란색 박스에는 position: absolute라 지정되었고, left: 100px, top: 200px; 의 스타일 규칙들이 적용되었다. 그랬더니 두 번째에 위치한 빨간색 박스와 겹쳐져서 보인다. 이는 position: relative로 지정된 부모 요소인 \<div id=’edge’> 요소의 박스를 기준으로 위로부터 200px만큼, 왼쪽으로 100px만큼 떨어져서 위치시키라는 명령에 의한 결과이다. 만약 해당 메시지 박스에 해당 규칙을 적용시키지 않는다면 위에 있던 박스들과 나란하게 빨간색 박스 바로 아래에 겹쳐지지 않게 위치해 있었을 것이다. (이는 세 번째 메시지 박스에 해당하는 \<div> 태그의 class 속성값에서 “pos1”을 지워보면 확인할 수 있다)

이처럼, position: absolute로 지정하여 위치를 배치시키려면 부모 요소가 position: relative 스타일 규칙을 가지고 있어야만 한다.

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석