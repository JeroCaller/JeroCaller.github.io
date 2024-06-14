---
title: "[CSS] 변형 (Transform)"
category: "CSS"
tag: ["css", "web", "transform"]
---
# 변형 (Transform)

웹 요소의 크기, 형태, 위치를 바꾸는 것을 CSS에서 할 수 있다. 이를 이용하면 웹 요소에 애니메이션 효과를 줄 수 있어 사용자 입장에서 좀 더 다채로운 느낌을 받게 할 수 있을 것이다. 

이 변형을 사용하기 위해선 속성으로 transform을 사용하며, 그 값으로 여러 변형 함수 중 하나를 사용하면 된다. 

변형에는 2차원 변형과 3차원 변형이 있다. 2차원 변형은 말 그대로 평면에서만 웹 요소를 변형하는 것이고, 3차원은 여기에 z축을 추가함으로써 원근감도 주도록 변형할 수 있다. 2차원 변형에서 x축은 오른쪽이 양의 방향, y축은 아래쪽이 양의 방향이고, 3차원에서 z축은 화면에서 사용자 쪽으로 뚫고 나오는 방향이 양의 방향이다. 

다음으로는 각각 웹 요소의 이동, 확대-축소, 회전, 왜곡 기능을 할 수 있는 변형 함수들에 대해 살펴보겠다. 

## translate()

해당 함수는 x축, y축, 또는 3차원의 경우 z축까지 웹 요소가 이동할 거리를 지정하면 그만큼 요소가 이동하도록 하는 함수이다. 해당 함수에도 여러 세부 종류가 있는데, 다음과 같다.

- translate(x, y)
- translate3d(x, y, z)
- translateX(x)
- translateY(y)
- translateZ(z)

이를 활용하여, 빨간 상자 위로 마우스를 가져다 대면 빨간 상자가 이동하는 예제 코드를 다음과 같이 작성하였다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="seotda.css">
</head>
<body>
    <div id="container">
        <div class="card three-g">
            <div class="card cover" id="goto-x"></div>
        </div>
        <div class="card eight-g">
            <div class="card cover" id="goto-y"></div>
        </div>
    </div>
</body>
</html>
```

```css
.card {
    border: 2px solid black;
    width: 110px;
    height: 170px;
    float: left;
    margin: 70px;
}
.three-g {
    background-image: url("..\\images\\3gwang.png");
    background-size: cover;
}
.eight-g {
    background-image: url("..\\images\\8gwang.png");
    background-size: cover;
}
.cover {
    background-color: red;
    background-size: cover;
    margin: 0px;
}
#goto-x:hover {
    transform: translateX(50px);
}
#goto-y:hover {
    transform: translateY(70px);
}
```

![예제 1-1 실행결과. 아직 마우스를 두 상자 중 어느 곳에도 대지 않은 상태](/images/2024-01-11/css-transform/1.png)

예제 1-1 실행결과. 아직 마우스를 두 상자 중 어느 곳에도 대지 않은 상태

![예제 1-1 실행결과. 왼쪽 상자에 마우스를 가져다 대었을 때의 모습. 빨간 상자가 X축을 기준으로 오른쪽으로 이동했다.](/images/2024-01-11/css-transform/2.png)

예제 1-1 실행결과. 왼쪽 상자에 마우스를 가져다 대었을 때의 모습. 빨간 상자가 X축을 기준으로 오른쪽으로 이동했다.

![예제 1-1 실행결과. 오른쪽 상자에 마우스를 올려두었을 때의 모습. 빨간 상자가 Y축을 기준으로 아래로 이동하였다.](/images/2024-01-11/css-transform/3.png)

예제 1-1 실행결과. 오른쪽 상자에 마우스를 올려두었을 때의 모습. 빨간 상자가 Y축을 기준으로 아래로 이동하였다.

## scale()

해당 변형 함수는 요소를 확대, 축소할 때 쓰이는 함수이다. 해당 함수의 세부 종류로는 다음이 존재한다. 

- scale(x, y)
- scale3d(x, y, z)
- scaleX(x)
- scaleY(y)
- scaleZ(z)

해당 함수들의 값으로 배율을 사용할 수 있으며, 1.0은 원본 그대로, 그 이상의 값은 확대, 0에서 1 사이의 값은 축소를 한다. 

(확인 결과, px 단위는 적용되지 않고 오로지 배율로만 입력값에 입력할 수 있는 것 같다)

다음은 위 함수들 일부를 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="scale.css">
</head>
<body>
    <div id="container">
        <div id="basic-boxes">
            <div class="box-shape imgbox scale-x"></div>
            <div class="box-shape imgbox scale-y"></div>
            <div class="box-shape imgbox scale-shrink"></div>
        </div>
        <div id="bed">
            <div id="sleep">
                <div class="box-shape imgbox" id="cover"></div>
            </div>
        </div>
    </div>
</body>
</html>
```

```css
#container {
    margin: auto 10px;
}
.box-shape {
    border: 1px solid black;
    width: 100px;
    height: 100px;
    margin: 50px;
    display: inline-block;
}
.imgbox {
    background-image: url("..\\images\\plants.jpeg");
    background-size: cover;
}
.scale-x:hover {
    transform: scaleX(2);
}
.scale-y:hover {
    transform: scaleY(1.5);
}
.scale-shrink:hover {
    transform: scale(0.6, 0.7);
}

#sleep {
    border: 1px solid black;
    width: 300px;
    height: 400px;
    background-image: url("..\\images\\sleep.png");
    background-size: cover;
    position: relative;
}
#cover {
    border: 1px solid grey;
    position: absolute;
    bottom: 13%;
    left: 16%;
}
#cover:hover {
    transform: scale(2.5, 2.5);
}
#sleep:after {
    content: "이불 좀 덮어줘!";
    position: absolute;
    right: -50%;
}
```

![예제 2-1 실행결과. 아직 어떤 요소에도 마우스를 가져다 대지 않은 상황.](/images/2024-01-11/css-transform/4.png)

예제 2-1 실행결과. 아직 어떤 요소에도 마우스를 가져다 대지 않은 상황.

![예제 2-1 실행결과. 왼쪽 위 상자 요소가 x축을 기준으로 확대됨을 알 수 있다. ](/images/2024-01-11/css-transform/5.png)

예제 2-1 실행결과. 왼쪽 위 상자 요소가 x축을 기준으로 확대됨을 알 수 있다. 

![에제 2-1 실행결과. 가운데 위 상자 요소가 y축 기준으로 확대되었다. ](/images/2024-01-11/css-transform/6.png)

에제 2-1 실행결과. 가운데 위 상자 요소가 y축 기준으로 확대되었다. 

![예제 2-1 실행결과. 오른쪽 위 상자 요소가 x, y축 기준으로 축소되었다. ](/images/2024-01-11/css-transform/7.png)

예제 2-1 실행결과. 오른쪽 위 상자 요소가 x, y축 기준으로 축소되었다. 

![예제 2-1 실행결과. scale()의 확대 기능을 이용하면 이렇게 이불을 덮어줄 수 있다!](/images/2024-01-11/css-transform/8.png)

예제 2-1 실행결과. scale()의 확대 기능을 이용하면 이렇게 이불을 덮어줄 수 있다!

## rotate()

### 2차원

해당 함수는 요소를 회전시키는 함수이다. 단위로는 deg 또는 rad를 사용할 수 있다. 해당 함수의 입력값이 양수일 경우 오른쪽으로, 음수일 경우 왼쪽으로 회전한다. 

rotate() 함수에는 단 하나의 입력값만 들어간다. 

다음은 해당 함수를 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="rotate.css">
</head>
<body>
    <div id="container">
        <h1>Quiz</h1>
        <div id="box-container">
            <div class="box" id="arrow-hour"></div>
            <div class="box" id="arrow-minute"></div>
        </div>
        <div id="ui">
            <div id="for-input">
                <input type="time" min="00:00" max="11:59">
                <input type="submit" value="확인">
            </div>
        </div>
    </div>
</body>
</html>
```

```css
#container > h1 {
    text-align: center;
}
#box-container {
    margin: auto 10px;
    display: flex; 
    justify-content: center;
}
.box {
    width: 200px;
    height: 200px;
    margin: 50px;
    background-image: url("..\\images\\arrow.png");
    background-size: cover;
}
#ui {
    text-align: center;
}

#arrow-hour:hover {
    transform: rotate(90deg);
}
#arrow-minute:hover {
    transform: rotate(-100deg);
}
```

![예제 3-1 실행결과. 아직 아무것도 안 했을 때의 모습](/images/2024-01-11/css-transform/9.png)

예제 3-1 실행결과. 아직 아무것도 안 했을 때의 모습

![예제 3-1 실행결과. 왼쪽 화살표 그림에 마우스를 가져다 대었을 때 오른쪽으로 회전한 모습.](/images/2024-01-11/css-transform/10.png)

예제 3-1 실행결과. 왼쪽 화살표 그림에 마우스를 가져다 대었을 때 오른쪽으로 회전한 모습.

![예제 3-1 실행결과. 오른쪽 화살표 그림에 마우스를 가져다 대었을 때 왼쪽으로 회전한 모습.](/images/2024-01-11/css-transform/11.png)

예제 3-1 실행결과. 오른쪽 화살표 그림에 마우스를 가져다 대었을 때 왼쪽으로 회전한 모습.

### 3차원

앞서 살펴본 rotate() 함수는 2차원으로 사용하는 것에 대해 논한 것이었다. 여기서는 3차원에서 요소를 회전시키는 것에 대해 살펴보겠다. 3차원에서는 다음의 세부 함수들이 존재한다. 

- rotate(x, y, angle)
- rotate3d(x, y, z, angle)
- rotateX(angle) : X축 기준 회전.
- rotateY(angle) : Y축 기준 회전.
- rotateZ(angle) : Z축 기준 회전.

또한, 3차원에서는 원근감을 주기 위한 perspective 속성을 같이 사용할 수 있다. 이 속성을 이용하면 사용자의 입장에서 해당 요소가 정말로 3차원적으로 회전함을 느낄 수 있다. (해당 속성을 사용하지 않고 위 함수들만 사용한다면 회전한 모습도 평면적으로 보인다) 다만 해당 속성을 사용할 때에는 주의점이 있다. 

1. 해당 속성값은 항상 0을 제외한 양수여야 하며, 화면 상의 위치에서 얼마나 멀리 떨어뜨릴지를 px 단위로 입력한다. 
2. 회전시키고자 하는 요소에 직접 적용하는 게 아니라, 해당 요소의 부모 요소에 적용시켜야 한다. 

다음은 위 함수들 일부를 이용한 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="rotate-3d.css">
</head>
<body>
    <div id="container">
        <div class="box-container">
            <div class="box"></div>
            <p>원본</p>
        </div>
        <div class="box-container">
            <div class="box rotate-x"></div>
            <p>X축 회전</p>
        </div>
        <div class="box-container">
            <div class="box rotate-y"></div>
            <p>Y축 회전</p>
        </div>
        <div class="box-container">
            <div class="box rotate-z"></div>
            <p>Z축 회전</p>
        </div>
        <div class="box-container pers-on">
            <!-- perspective 속성이 담긴 클래스 pers-on을 적용시켰다.
            이 속성을 적용하여 원근감을 나타내려면 회전하는 요소에 
            직접 넣는 것이 아니라, 해당 요소의 부모 요소에 적용해야 한다. -->
            <div class="box rotate-x"></div>
            <p>X축 회전 + 원근감 추가</p>
        </div>
        <div class="box-container pers-on">
            <div class="box rotate-y"></div>
            <p>Y축 회전 + 원근감 추가</p>
        </div>
        <div class="box-container pers-on">
            <div class="box rotate-z"></div>
            <p>Z축 회전 + 원근감 추가</p>
        </div>
        <div class="box-container">
            <div class="box rotate-vector"></div>
            <p>3D 회전</p>
        </div>
        <div class="box-container pers-on">
            <div class="box rotate-vector"></div>
            <p>3D 회전 + 원근감 추가</p>
        </div>
    </div>
</body>
</html>
```

```css
/* 틀 설정 */
#container {
    margin: 30px;
}

/* 박스 기본 설정 */
.box-container {
    display: inline-block;
    margin: 20px;
}
.box-container p {
    text-align: center;
}
.box {
    border: 1px solid black;
    width: 150px;
    height: 150px;
    background-image: url("..\\images\\flowers.jpeg");
    background-size: cover;
    margin: auto;
}

.pers-on {
    /* 사용자가 보는 화면에서 200px만큼 멀어지도록 표현. */
    perspective: 200px;
}

.rotate-x:hover {
    transform: rotateX(60deg);
}
.rotate-y:hover {
    transform: rotateY(60deg);
}
.rotate-z:hover {
    transform: rotateZ(60deg);
}
.rotate-vector:hover {
    transform: rotate3d(5, 6, 7, 30deg);
}
```

<style>
    #img-box {
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 1em 1em;
        width: 100%;
    }
</style>
<div id="img-box">
    <img src="/images/2024-01-11/css-transform/12.png">
    <img src="/images/2024-01-11/css-transform/13.png">
    <img src="/images/2024-01-11/css-transform/14.png">
    <img src="/images/2024-01-11/css-transform/15.png">
    <img src="/images/2024-01-11/css-transform/16.png">
    <img src="/images/2024-01-11/css-transform/17.png">
    <img src="/images/2024-01-11/css-transform/18.png">
    <img src="/images/2024-01-11/css-transform/19.png">
</div>

## skew()

해당 함수는 지정된 요소를 입력한 각도만큼 비틀어버린다. 세부 함수로는 다음이 존재한다. 

- skew(x-angle, y-angle) : 두 번째 입력값을 입력하지 않으면 0도로 자동 설정됨.
- skewX(x-angle)
- skewY(y-angle)

다음은 이를 활용한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="skew.css">
</head>
<body>
    <div id="container">
        <div class="box-container">
            <div class="box"></div>
            <p>원본</p>
        </div>
        <div class="box-container">
            <div class="box" id="skew-x"></div>
            <p>X축 기준 비틀기</p>
        </div>
        <div class="box-container">
            <div class="box" id="skew-y"></div>
            <p>Y축 기준 비틀기</p>
        </div>
        <div class="box-container">
            <div class="box" id="skew-xy"></div>
            <p>X, Y축 기준 비틀기</p>
        </div>
    </div>
</body>
</html>
```

```css
#container {
    margin: 30px;
}

/* 박스 기본 설정 */
.box-container {
    display: inline-block;
    margin: 40px;
}
.box-container p {
    text-align: center;
}
.box {
    border: 1px solid black;
    width: 150px;
    height: 150px;
    background-image: url("..\\images\\flowers.jpeg");
    background-size: cover;
    margin: auto;
}

#skew-x:hover {
    transform: skewX(20deg);
}
#skew-y:hover {
    transform: skewY(20deg);
}
#skew-xy:hover {
    transform: skew(-20deg, 20deg);
}
```

![20240116_08250117.png](/images/2024-01-11/css-transform/20.png)

![20240116_08250649.png](/images/2024-01-11/css-transform/21.png)

![20240116_08251171.png](/images/2024-01-11/css-transform/22.png)

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석