---
title: "[CSS] 배경 관련 속성들"
category: "CSS"
tag: ["css", "web", "background"]
---
# background-color

해당 속성은 배경색을 지정할 때 쓰이는 속성으로, 색상 이름을 넣거나 16진수, 또는 rgb값을 사용할 수 있다. 

다른 속성들과 달리, background-color 속성은 부모 요소에서 특정 색으로 지정해도 자식 요소로 상속되지 않는다고 한다. 부모 요소에 지정된 배경색이 보여도 이것은 부모 요소의 배경색이지 자식 요소에 상속되었기에 보이는 것은 아니라고 한다. 

# background-clip

해당 속성은 특정 스타일 규칙이 적용된 배경을 박스 모델의 어디까지 적용시킬 것인지를 결정하는 속성이다. 해당 속성에는 다음의 3가지 속성값을 사용할 수 있다. 

- border-box : 박스 모델의 경계선까지 모두 배경을 적용한다. 기본값이다.
- padding-box : 박스 모델의 안쪽 여백인 패딩까지만 배경을 적용한다. 즉, 경계선까지는 적용되지 않는다.
- content-box : 박스 모델 내 내용에만 배경을 적용한다.

## 예제

다음은 위에서 본 두 속성인 background-color, background-clip 속성을 적용한 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .allbox {
            border: 10px dotted black;
            width: 200px;
            height: 100px;
            padding: 10px;
            margin: 10px;
            background-color: darkseagreen;
        }
        .box1 {
            background-clip: border-box;
        }
        .box2 {
            background-clip: content-box;
        }
        .box3 {
            background-clip: padding-box;
        }
    </style>
</head>
<body>
    <div class="allbox box1">This is box 1</div>
    <div class="allbox box2">This is box 2</div>
    <div class="allbox box3">This is box 3</div>
</body>
</html>
```

![예제 1-1 실행결과](/images/2024-01-06/css-background-attributes/1.png)

예제 1-1 실행결과

위 예제를 통해 background-clip의 각 속성값들의 배경 적용 범위를 알 수 있다.

# background-image

배경 이미지를 삽입하고자 할 때 사용하는 속성이다. 사용법은 다음과 같다.

```css
background-image: url('이미지 경로');
```

만약 해당 배경 이미지의 크기가 해당 이미지를 적용할 요소보다 더 작으면 가로, 세로 두 방향으로 반복되는 패턴을 가지며 배경을 채운다. 

# background-repeat

해당 속성은 배경 이미지를 반복시킬 방향을 정하는 속성이다. 가로 또는 세로 방향으로만 반복시키게 할 수도 있고, 두 방향 모두 반복시키거나 딱 한 번만 이미지가 나오게끔 설정할 수 있다. 다음은 해당 속성에서 지정할 수 있는 속성값들이다. 

- repeat : 웹 브라우저 화면을 가득 채우도록 수평, 수직 두 방향 모두 반복시킨다. 기본값.
- repeat-x : 화면의 수평 방향으로만 배경 이미지를 반복시킨다.
- repeat-y : 화면 수직 방향으로만 배경 이미지를 반복시킨다.
- no-repeat : 배경 이미지를 딱 한 번만 표시한다.

# background-attachment

해당 속성은 배경 이미지를 적용한 상태에서 해당 웹 문서를 스크롤하여 화면 위아래로 이동할 때 배경 이미지도 그에 따라 움직이도록 할 것인지 아니면 스크롤 여부에 상관없이 항상 고정시킬 것인지를 결정하는 속성이다. 해당 속성에는 다음의 두 가지 속성값 중 하나를 선택할 수 있다. 

- scroll : 사용자가 화면을 스크롤하면 배경 이미지도 같이 움직인다. 기본값.
- fixed : 화면 스크롤 시 배경 이미지는 고정시킨다. 그 외 나머지 요소들은 스크롤에 따라 움직인다.

# background

background란 이름으로 시작되는 모든 속성들을 background라는 단 하나의 속성으로 줄여서 사용할 수 있다. 

# background-size

해당 속성은 배경 이미지의 크기를 조절할 수 있는 속성이다. 해당 속성값으로 px 또는 백분율 등의 단위를 이용하여 크기를 절대적, 또는 상대적으로 지정할 수도 있고, 3개의 키워드 중 하나를 택하여 지정할 수도 있다. 해당 키워드들은 다음과 같다.

- auto : 배경 이미지 원본 크기 그대로 표시한다. 해당 이미지 크기가 요소보다 커서 잘려서 보이더라도 해당 이미지 원본 크기 그대로 표시된다. 기본값.
- contain : 요소 안에 배경 이미지가 들어오도록 이미지를 확대 또는 축소한다.
- cover : 요소의 모든 부분에 배경 이미지가 덮이도록 이미지를 확대 또는 축소한다. contain과 다른 점은, contain은 만약 요소보다 배경 이미지가 더 큰 경우 요소 안에 배경 이미지를 구겨 넣는 개념이라서 요소 일부분이 배경 이미지로 덮이지 못할 수도 있는 반면, cover 속성값은 무조건 해당 요소의 모든 부분이 배경 이미지로 덮이도록 이미지를 확대 또는 축소시킨다는 것이다.

다음은 background-size 속성에 지정할 수 있는 3가지 키워드를 각각 적용해본 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        p {
            color: green;
            background-color: darkkhaki;
        }
        .bg {
            background-image: url('..\\images\\plants.jpeg');
            background-repeat: no-repeat;
        }
        .allbox {
            border: 1px solid black;
            width: 600px;
            height: 200px;
            margin: 10px;
        }
        .box1 {
            background-size: auto;
        }
        .box2 {
            background-size: contain;
        }
        .box3 {
            background-size: cover;
        }
    </style>
</head>
<body>
    <div class="allbox bg box1">
        <p>안녕하세요, 만나서 반가워요!</p>
    </div>
    <div class="allbox bg box2">
        <p>안녕하세요, 만나서 반가워요!</p>
    </div>
    <div class="allbox bg box3">
        <p>안녕하세요, 만나서 반가워요!</p>
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-01-06/css-background-attributes/2.png)

예제 2-1 실행결과

## 예제

다음은 앞서 background-image부터 background-size 속성까지를 적용한 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        body {
            background-image: url('..\\images\\korean-new-year.jpg');
            background-repeat: repeat;
            background-size: 400px; /* 너비 400px, 높이는 자동 계산됨*/
            background-attachment: fixed;
        }
        p {
            color:dimgray;
            background-color: bisque;
        }
        div {
            width: 500px;
        }
    </style>
</head>
<body>
    <div>
        <p>
            The ocean is... <!-- 생략 -->
        </p>
        <p>
            Friendship is... <!-- 생략 -->
        </p>
    </div>
</body>
</html>
```

<video src="/resources/2024-01-06/css-background-attributes/20240106_073204.mp4" controls muted width="70%"></video>

예제 3-1 실행결과

# background-position

해당 속성은 배경 이미지의 수평, 수직 위치를 지정할 수 있는 속성이다. 해당 속성에는 하나 또는 두 개를 지정할 수 있다. 속성값 두 개 지정 시 두 값 사이에 공백을 넣어 구분한다. 두 개의 속성값을 지정한다면 맨 처음 하나는 수평 위치를 그 뒤의 하나는 수직 위치를 지정하는 값이 된다. 

해당 속성에는 px, % 단위로 절대, 상대적 위치를 결정할 수 있고, 또는 키워드로도 지정할 수 있다. 키워드의 경우, 수평 위치의 경우 left, center, right 중 하나를, 수직 위치의 경우 top, center, bottom 중 하나를 선택할 수 있다. 

다음은 해당 속성에 대입할 수 있는 3가지 키워드를 모두 사용해본 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            border: 3px solid black;
            background-image: url('..\\images\\colorful-waves.jpeg');
            background-size: 50px;
            background-repeat: no-repeat;
            height: 100px;
            margin: 10px;
        }
        .box1 {
            background-position: left center;
        }
        .box2 {
            background-position: right top;
        }
        .box3 {
            background-position: center center;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
</body>
</html>
```

![예제 4-1 실행결과](/images/2024-01-06/css-background-attributes/3.png)

예제 4-1 실행결과

# background-origin

해당 속성은 박스 모델에 배경 이미지를 적용할 때 박스 모델의 경계선, 패딩, 내용 중 어디까지 배경 이미지를 적용할 것인지를 결정하는 속성이다. 해당 속성에는 3가지 키워드 중 하나를 선택할 수 있다. 

- content-box : 박스 모델의 내용에만 배경 이미지를 적용시킨다. 기본값.
- padding-box : 박스 모델 내부 여백인 패딩까지만 배경 이미지를 적용시킨다.
- border-box : 박스 모델의 경계선까지 배경 이미지를 적용시킨다.

다음은 위 3가지 키워드를 각각 적용해본 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            border: 10px dotted black;
            background-image: url('..\\images\\colorful-waves.jpeg');
            background-size: 100%;
            background-repeat: no-repeat;
            height: 100px;
            margin: 10px;
            padding: 20px;
        }
        .box1 {
            background-origin: content-box;
        }
        .box2 {
            background-origin: padding-box;
        }
        .box3 {
            background-origin: border-box;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
</body>
</html>
```

![예제 5-1 실행결과](/images/2024-01-06/css-background-attributes/4.png)

예제 5-1 실행결과

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석