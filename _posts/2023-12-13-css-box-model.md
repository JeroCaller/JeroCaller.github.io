---
title: "[CSS] 박스 모델"
category: "CSS"
tag: ["css", "web", "box model"]
---
# 박스 모델(box model)

웹 문서에서 HTML의 태그가 적용된 요소들이 보일 때, 해당 요소들은 모두 박스 안에 표시되고, 각 요소들도 이 박스로 구분이 되는데, CSS에서는 이러한 박스들을 박스 모델(box-model)이라고 부른다. 이 박스들은 css의 border 속성으로 따로 테두리를 보이지 않는 한 평소에는 사용자 눈에 보이지 않는다. 다음은 박스 모델을 보이기 위해 각 요소의 박스의 경계선을 명확하게 보이도록 한 코드 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        * {
            border: 2px solid black;
            padding: 5px;
        }
    </style>
</head>
<body>
    <h1>TIL (Today I Learned)</h1>
    <h2>목차</h2>
    <ul type="none">
        <li>CSS의 박스 모델</li>
        <li>박스 모델의 블록 레벨과 인라인 레벨</li>
        <li>박스 모델 관련 속성들</li>
    </ul>
    <div>CSS의 박스 모델</div>
    <p>
        웹 문서에서 HTML의 태그가 적용된 요소들이 보일 때, 
        해당 요소들은 모두 박스 안에 표시되고, 각 요소들도 
        이 박스로 구분이 되는데, CSS에서는 이러한 
        박스들을 <strong>박스 모델(box-model)</strong>이라고 부른다. 이 박스들은 
        css의 border 속성으로 따로 테두리를 보이지 않는 한 평소에는 
        사용자 눈에 보이지 않는다. 
    </p>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-12-13/css-box-model/1.png)

예제 1-1 실행결과

위 예제를 보면 알겠지만, 태그가 적용된 모든 요소가 박스를 지니며, 그 박스 안에 텍스트, 이미지 등의 컨텐츠를 담아 표시하는 것을 알 수 있다. (참고로 \<body> 태그도 박스 모델의 영향을 받기에 위 실행결과의 맨 바깥쪽에도 테두리가 형성된 것을 볼 수 있다)

박스 모델에는 두 가지 형태가 있다. 

- 블록 레벨(block-level) : 요소의 박스의 너비가 양 옆을 다 차지하는 요소이다. 위 예제에서 h1, h2, ul, li, p 태그 등이 이에 해당함을 알 수 있다. 이렇게 한 영역의 너비를 다 차지하는 특성 때문에, 블록 레벨 요소에는 같은 줄에 다른 요소가 침범할 수 없다.
- 인라인 레벨(inline-level): 요소의 박스가 담고 있는 컨텐츠의 크기만큼만의 너비를 가지는 박스 레벨. 위 예제의 strong 태그가 그 예이다. 위 실행결과를 보면 알 수 있듯, 해당 태그가 적용된 요소의 박스는 해당 컨텐츠의 너비만큼만을 차지하는 것을 알 수 있다. 이러한 특성 때문에 블록 레벨과 달리, 인라인 레벨의 박스와 같은 줄에 다른 요소들이 들어올 수 있다.

## 박스 모델의 구성

위 예제를 웹 브라우저에서 실행한 후, 해당 브라우저의 개발자 도구를 통해 “TIL (Today I Learned)”의 박스 구조를 살펴보았다. 

![사진 1. “TIL (Today I Learned)” 박스 구조를 살펴보았다.](/images/2023-12-13/css-box-model/2.png)

사진 1. “TIL (Today I Learned)” 박스 구조를 살펴보았다.

위 사진 1의 오른쪽 아래 부분을 보면, 해당 요소의 박스 모델이 보이는 것을 확인할 수 있다. 해당 사진을 자세히 보면 박스 모델이 어떻게 구성되어있는지 알 수 있다. 박스 모델은 가장 안쪽에서부터 차례대로 컨텐츠(content), 패딩(padding), 테두리(border), 마진(margin)으로 구성되어 있다. 컨텐츠는 텍스트, 이미지 등 사용자에게 보여줄 정보를 의미한다. border는 박스의 경계선을 의미하며, 해당 경계선과 컨텐츠 간의 공간을 padding이라고 한다. 반면, 박스 경계선과 또 다른 박스 경계선 사이의 공간을 margin이라고 한다. 즉, 박스의 테두리와 박스 내 컨텐츠 사이의 여백이 padding, 해당 박스의 테두리와 다른 박스의 테두리 간 여백이 margin이다. 

다음은 “TIL (Today I Learned)” 요소의 박스 모델의 구조를 각각 살펴본 사진들이다. 

![사진 2. 파란색 영역은 박스 모델의 컨텐츠 영역이다.](/images/2023-12-13/css-box-model/3.png)

사진 2. 파란색 영역은 박스 모델의 컨텐츠 영역이다.

![사진 3. 초록색 영역은 박스 테두리와 컨텐츠 사이의 여백인 padding 영역이다.](/images/2023-12-13/css-box-model/4.png)

사진 3. 초록색 영역은 박스 테두리와 컨텐츠 사이의 여백인 padding 영역이다.

![사진 4. 연 노랑색 영역은 박스의 테두리 부분인 border를 가리키고 있다.](/images/2023-12-13/css-box-model/5.png)

사진 4. 연 노랑색 영역은 박스의 테두리 부분인 border를 가리키고 있다.

![사진 5. 주황색 영역은 해당 박스의 테두리와 다른 박스의 테두리 사이의 여백인 margin 영역을 나타내고 있다.](/images/2023-12-13/css-box-model/6.png)

사진 5. 주황색 영역은 해당 박스의 테두리와 다른 박스의 테두리 사이의 여백인 margin 영역을 나타내고 있다.

# 박스 모델 크기 속성

다음은 CSS에서 사용할 수 있는 박스 모델 크기 관련 속성들이다.

## width, height

해당 속성들은 컨텐츠 영역의 너비와 높이를 결정하는 속성들이다. 해당 속성에 사용할 수 있는 속성값들로 다음이 존재한다.

- px, em단위로 설정.
- 박스 모델을 포함하는 부모 요소 기준으로 크기를 설정할 땐 백분율(%)을 사용.
- auto 키워드: 컨텐츠 크기에 따라 자동으로 결정됨. 기본값

다음은 두 박스 모델에 width, height 속성값을 각각 다르게 설정한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .box1 {
            border: 1px solid black;
            width: 200px;
            height: 100px;
            /* 다음 줄은 박스를 웹 브라우저의 가운데로 정렬한다. */
            margin: 10px auto;
            /* 글자도 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .box2 {
            border: 1px solid black;
            width: 400px;
            height: 200px;
            /* 다음 줄은 박스를 웹 브라우저의 가운데로 정렬한다. */
            margin: 10px auto;
            /* 글자도 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
    <div class="box1">
        box1
    </div>
    <div class="box2">
        box2
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2023-12-13/css-box-model/7.png)

예제 2-1 실행결과

다음은 박스 모델 내부의 영역 크기를 확인한 사진이다. 다음 사진을 보면 알 수 있듯, 해당 예제에서 padding 속성값을 설정하지 않았으므로 박스 내부 영역은 오로지 컨텐츠의 영역만 있음을 알 수 있다. 

![사진 6](/images/2023-12-13/css-box-model/8.png)

사진 6

그러나 실전에서는 박스 모델의 크기는 대부분 컨텐츠의 너비, 높이 뿐만 아니라 padding과 border 두께도 포함될 수 있음에 유의한다. 

## box-sizing

해당 속성은 박스 모델의 너비, 높이를 결정할 때 컨텐츠 영역만을 기준으로 설정할 것인지, 아니면 컨텐츠 영역의 크기와 함께 padding, border의 크기값도 포함하여 설정할 것인지 결정할 때 쓰이는 속성이다. 해당 속성의 값으로 다음이 가능하다.

- border-box: 컨텐츠 영역 + padding + border 크기를 모두 합쳐 박스 모델의 크기를 결정한다. 예를 들어 다음의 스타일 규칙들을 설정했을 때…
    
    ```css
    .box1 {
        border: 5px solid black;
        width: 200px;
        height: 100px;
        padding: 20px;
        box-sizing: border-box;
    }
    ```
    
    width를 200px로 했다면 이는 컨텐츠 영역의 너비 + 양옆의 padding 값 (20px * 2 = 40px) + border의 양 옆 두께(5px * 2 = 10px) 이 모두를 합쳐서 너비값이 200px가 되도록 하라는 뜻이다. 위 계산대로라면 컨텐츠 영역의 너비는 200 - 40 - 10 = 150px임을 알 수 있다. 
    
- content-box: 기본값. border-box와 달리 width 또는 height로 박스의 크기를 결정할 때 오로지 컨텐츠 영역만의 크기로 설정. 위 예제를 예로 들면, width: 200px이라는 것은 컨텐츠의 너비만을 200px으로 지정하라는 뜻.

다음은 두 박스의 width, height과 padding, border 두께까지 똑같은 값으로 했을 때 box-sizing의 값에 따라 두 박스의 크기가 어떻게 달라지는지를 확인하는 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .box1 {
            border: 5px solid black;
            width: 200px;
            height: 100px;
            padding: 20px;
            /* 컨텐츠 크기 + padding + border 두께 값 모두 합쳐 
            width=200px, height=100px이 되도록 핢.*/
            box-sizing: border-box;
            /* 다음 줄은 박스를 웹 브라우저의 가운데로 정렬한다. */
            margin: 10px auto;
            /* 글자도 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .box2 {
            border: 5px solid black;
            width: 200px;
            height: 100px;
            padding: 20px;
            /* 컨텐츠 너비만 200px, 높이만 100px가 되도록 함.*/
            box-sizing: content-box;
            /* 다음 줄은 박스를 웹 브라우저의 가운데로 정렬한다. */
            margin: 10px auto;
            /* 글자도 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
        }
    </style>
</head>
<body>
    <div class="box1">
        box1
    </div>
    <div class="box2">
        box2
    </div>
</body>
</html>
```

![예제 3-1 실행결과](/images/2023-12-13/css-box-model/9.png)

예제 3-1 실행결과

# 테두리 스타일 관련 속성

다음은 박스 모델의 테두리 스타일 관련 속성들이다.

## border-style

해당 속성은 테두리의 스타일을 지정하는 속성이다. 다음 코드에서 해당 속성값에는 어떤 것들이 있는지 예제를 통해 보여주고 있다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            /* 글자를 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
            /* --- */
            border-width: 5px;
            padding: 10px;
            margin: 20px;
        }
    </style>
</head>
<body>
    <div>
        none
    </div>
    <div style="border-style: hidden;">
        hidden
    </div>
    <div style="border-style: solid;">
        solid
    </div>
    <div style="border-style: dotted;">
        dotted
    </div>
    <div style="border-style: dashed;">
        dashed
    </div>
    <div style="border-style: double;">
        double
    </div>
    <div style="border-style: groove;">
        groove
    </div>
    <div style="border-style: inset;">
        inset
    </div>
    <div style="border-style: outset;">
        outset
    </div>
    <div style="border-style: ridge;">
        ridge
    </div>
</body>
</html>
```

![예제 4-1 실행결과](/images/2023-12-13/css-box-model/10.png)

예제 4-1 실행결과

## border-width

해당 속성은 박스 경계선의 두께를 지정하는 속성이다. 해당 속성값으로 px 단위로 직접 값을 지정할 수도 있고, thin, medium, thick 키워드를 사용할 수도 있다. 

해당 속성의 값을 몇 개를 지정하느냐에 따라 박스의 상하좌우에 있는 각각의 경계선에 각각의 크기를 지정할 수도 있고, 전체에 일괄적으로 지정할 수도 있다. 

위 설명에 대한 자세한 내용은 다음의 예제 코드에서 확인할 수 있다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            width: 300px;
            height: 100px;
            margin: 20px;
            border-style: solid;
            border-color: black;
        }
        .box1 { 
            /* 상하좌우의 경계선의 두께를 모두 10px로 한다.*/
            border-width: 10px;
        }
        .box2 { 
            /*
                위 아래의 두꼐는 thin, 좌우의 두께는 thick으로 한다. 
            */
            border-width: thin thick;
        }
        .box3 {
            /*
                경계선 위쪽부터 시계방향으로 진행하여 적용된다.
                위쪽은 thin, 오른쪽은 medium, 아래쪽은 thick이 적용된다. 
                왼쪽은 마주보는 오른쪽의 속성값인 medium을 그대로 가진다.
            */
            border-width: thin medium thick;
        }
        .box4 {
            /*
                경계선 위쪽부터 시계방향으로 진행하여 적용된다. 
                위쪽은 thin, 오른쪽은 medium, 아래쪽은 thick, 왼쪽은 20px
                이 적용되었다.
            */
            border-width: thin medium thick 20px;
        }
    </style>
</head>
<body>
    <div class="box1">box1</div>
    <div class="box2">box2</div>
    <div class="box3">box3</div>
    <div class="box4">box4</div>
</body>
</html>
```

![예제 5-1 실행결과](/images/2023-12-13/css-box-model/11.png)

예제 5-1 실행결과

## border-color

해당 속성은 박스 경계선의 색을 지정하는 속성이다. 앞서 border-width에서 하나의 값만 지정하여 경계선 상하좌우에 모두 똑같은 두께를 지정하거나, 또는 상하좌우 따로따로 두께를 지정할 수 있었듯이, border-color 속성도 상하좌우에 똑같은 색을 지정하거나 따로 지정할 수 있다. 따로 지정하고자 하는 경우, 방향을 의미하는 top, right, bottom, left 키워드를 border-top-color와 같이 중간에 삽입하면 된다. 

다음은 한 박스에는 테두리 전체에 같은 색을, 다른 박스에는 상하좌우 모두 다른 색을 적용한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            /* 글자를 박스의 정중앙에 배치하고자 한다면 다음의 3줄 코드 삽입.*/
            display: flex;
            justify-content: center;
            align-items: center;
            /* --- */
            margin: 50px;
            width: 500px;
            height: 100px;
            border-style: solid;
            border-width: 5px;
        }
        .box1 {border-color: black;}
        .box2 {
            border-top-color: blue;
            border-right-color: bisque;
            border-bottom-color: blueviolet;
            border-left-color: chocolate;
        }
    </style>
</head>
<body>
    <div class="box1">box1</div>
    <div class="box2">box2</div>
</body>
</html>
```

![예제 6-1 실행결과](/images/2023-12-13/css-box-model/12.png)

예제 6-1 실행결과

## border

지금까지의 border 관련 속성값들을 border 속성에 몰아서 사용할 수 있다. 

```css
p {
	border: 1px solid black;
}
```

속성값들의 순서는 상관없고, 각각의 값들은 공백 한 칸으로 구분한다. 

만약 상하좌우 박스 경계선에 각각 다르게 속성값을 부여하고자 한다면 border-top과 같이 방향 키워드를 추가한 속성을 이용하면 된다.

## border-radius

해당 속성은 박스 네 개의 모서리의 경계선 모양을 설정할 수 있는 속성이다. 해당 속성에 숫자로 속성값을 지정하면 모서리에 해당 속성값을 반지름으로 가지는 가상의 원이 있다고 가정하고 그 원 모양대로 모서리의 모양이 변한다. 

해당 속성값으로 px, em 단위 또는 백분율을 사용할 수 있다. 

다음은 해당 속성을 이용하여 박스의 모서리 부분을 둥글게 만든 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            margin: 30px;
            border: 3px solid black;
        }
        .object1 {
            width: 300px;
            height: 100px;
            border-radius: 30px;
        }
        .object2 {
            width: 100px;
            height: 100px;
            /* 
                박스의 너비와 높이를 똑같이 한 후, 
                border-radius 속성값을 50%으로 설정하면 
                원이 된다.
            */
            border-radius: 50%;
        }
        .object3 {
            width: 300px;
            height: 100px;
            /*
                border-radius 속성값에 슬래시(/)를 기준으로 
                왼쪽에는 가로 반지름의 값을, 오른쪽에는 세로 반지름
                의 값을 대입하면 타원 모양이 되며, 둘 다 
                50%으로 하면 완전한 타원 모양이 된다. 
            */
            border-radius: 50% / 50%;
        }
        .object4 {
            width: 300px;
            height: 100px;
            /*
                슬래시가 없더라도 타원 모양이 된다.
            */
            border-radius: 50% 50%;
        }
        .object5 {
            width: 300px;
            height: 100px;
            /*
                네 모서리 중 일부에만 앞서 설명한 
                원 또는 타원이 될 조건의 속성값을 대입하면 
                해당 모서리만 원 또는 타원 모양이 된다.
            */
            border-top-right-radius: 50%;
            border-top-left-radius: 10px;
            border-bottom-left-radius: 30px;
            border-bottom-right-radius: 50% 50%;
        }
    </style>
</head>
<body>
    <div class="object1"></div>
    <div class="object2"></div>
    <div class="object3"></div>
    <div class="object4"></div>
    <div class="object5"></div>
</body>
</html>
```

![예제 8-1 실행결과](/images/2023-12-13/css-box-model/13.png)

예제 8-1 실행결과

참고로 위 예제 코드의 .object5 내 스타일 규칙을 보면 알 수 있듯, border와 radius 사이에 네 모서리를 지칭하는 방향 키워드를 삽입하면 해당 방향의 모서리에만 스타일이 적용된다. 

# 여백 관련 속성

앞서 박스 모델에서 언급했듯, 박스모델에는 박스 안의 컨텐트와 박스 경계선 사이의 여백인 padding, 그리고 다른 박스와의 간격 여백인 margin 이렇게 두 종류의 여백이 있다고 보았다. 이 둘의 여백과 관련한 속성들이 있다.

## margin

해당 속성은 margin 여백을 주는 속성으로, 해당 속성값이 클수록 다른 박스와의 간격이 더 넓어진다. 해당 속성값으로는 px, em 단위로 숫자값과 함께 넣거나, 백분율(%)을 통해 박스 모델을 포함한 부모 요소 기준 너비값 및 높이값을 상대적으로 지정할 수 있다. 또한, auto값은 display 속성에 지정된 값을 토대로 자동으로 결정해주는 속성값이다. 

해당 속성값은 border-width와 마찬가지로 박스의 상하좌우 각각에 지정할 수도, 일괄적으로 적용할 수도 있으며, 적용 방향은 border-width에서 설명한 것과 동일한 방법으로 적용된다. 

다음은 margin을 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div { 
            border: 3px solid black;
            width: 300px;
            height: 100px;
        }
        #box1 { 
            /* 동서남북 4방향 모두 일괄적으로 적용.*/
            margin: 10px; 
        } 
        #box2 { 
            /* 위아래 10px, 좌우 20px */
            margin: 10px 20px; 
        } 
        #box3 { 
            /* 위 10px, 좌우 20px, 아래 30px*/
            margin: 10px 20px 30px; 
        } 
        #box4 { 
            /* 차례대로 위, 오른쪽, 아래, 왼쪽에 적용.*/
            margin: 10px 20px 30px 40px;
        } 
    </style>
</head>
<body>
    <div id="box1"></div>
    <div id="box2"></div>
    <div id="box3"></div>
    <div id="box4"></div>
</body>
</html>
```

![예제 9-1 실행결과](/images/2023-12-13/css-box-model/14.png)

예제 9-1 실행결과

### margin 활용: 웹 문서 가운데 정렬

margin 속성을 이용하면 웹 문서 내 모든 요소들을 가로 기준으로 가운데 정렬할 수 있다. 이를 위해선 먼저 \<div> 태그 안에 모든 웹 요소들을 넣은 다음, 해당 \<div> 태그의 너비를 width 속성을 통해 정한다. 그 후, 왼쪽, 오른쪽 마진을 margin-left, margin-right 속성을 통해 모두 auto 값으로 지정하면 모든 웹 요소들이 웹 페이지의 가운데로 정렬된다. 다음은 이를 이용하여 웹 페이지 가운데 정렬한 모습이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #webpage {
            border: 3px solid black;
            width: 500px;
	    /*
	    	두 값 중 오른쪽 값이 좌우 방향의 margin값을 
	    	설정하는 속성값이다. 여기에 auto를 삽입하여
	    	해당 박스 모델이 웹 페이지 기준 가운데 정렬이 
	    	되도록 하였다.  
	    */
            margin: 10px auto;
            padding: 10px;
        }
    </style>
</head>
<body>
    <div id="webpage">
        <h1>TIL (Today I Learned)</h1>
        <h2>목차</h2>
        <ul type="none">
            <li>CSS의 박스 모델</li>
            <li>박스 모델의 블록 레벨과 인라인 레벨</li>
            <li>박스 모델 관련 속성들</li>
        </ul>
        <div>CSS의 박스 모델</div>
        <p>
            웹 문서에서 HTML의 태그가 적용된 요소들이 보일 때, 
            해당 요소들은 모두 박스 안에 표시되고, 각 요소들도 
            이 박스로 구분이 되는데, CSS에서는 이러한 
            박스들을 <strong>박스 모델(box-model)</strong>이라고 부른다. 이 박스들은 
            css의 border 속성으로 따로 테두리를 보이지 않는 한 평소에는 
            사용자 눈에 보이지 않는다. 
        </p>
    </div>
</body>
</html>
```

![예제 9-2 실행결과](/images/2023-12-13/css-box-model/15.png)

예제 9-2 실행결과

### margin 중첩

margin에는 margin overlap(마진 중첩) 또는 margin collapse(마진 상쇄)라 불리는 개념이 있다. 이는 여러 개의 박스 모델이 1열로 모여 있을 때, 박스 모델들에 적용된 margin값이 모두 적용되지 않고, 그 중 가장 큰 margin값을 가지는 박스 모델의 margin값만 적용된다는 개념이다. 

이에 대해 더 자세히 알아보기 위해 다음의 예제 코드를 준비했다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .bigedge {
            padding: 20px;
            margin: 30px;
            border: 3px solid black;
        }
        .box {
            width: 200px;
            height: 100px;
        }
        .box2 {
            display: inline-block;
        }
        #colbox1 {
            border: 2px solid brown;
            margin: 30px auto;
        }
        #colbox2 {
            border: 2px solid blueviolet;
            margin: 20px auto;
        }
        #colbox3 {
            border: 2px solid goldenrod;
            margin: 10px auto;
        }
        #rowbox1 {
            border: 2px solid cadetblue;
            margin: auto 30px;
        }
        #rowbox2 {
            border: 2px solid darkkhaki;
            margin: auto 20px;
        }
        #rowbox3 {
            border: 2px solid firebrick;
            margin: auto 10px;
        }
    </style>
</head>
<body>
    <div class="bigedge">
        <div class="box" id="colbox1"></div>
        <div class="box" id="colbox2"></div>
        <div class="box" id="colbox3"></div>
    </div>
    <div class="bigedge">
        <div class="box box2" id="rowbox1"></div>
        <div class="box box2" id="rowbox2"></div>
        <div class="box box2" id="rowbox3"></div>
    </div>
</body>
</html>
```

![예제 10-1 실행결과](/images/2023-12-13/css-box-model/16.png)

예제 10-1 실행결과

위 실행결과 화면에서, 웹 브라우저의 “개발자 도구”를 이용하여 가장 위의 박스와 중간 박스를 눌러 해당 박스 모델들이 차지하는 margin 영역을 살펴보았다. 

![사진 7](/images/2023-12-13/css-box-model/17.png)

사진 7

![사진 8](/images/2023-12-13/css-box-model/18.png)

사진 8

앞서 예제 코드를 보면, id=”colbox1”에 해당하는 가장 위의 박스에는 margin: 30px 값을 주었고, 바로 아래 박스에는 margin: 20px 값을 주었다. 생각을 해보면 이 두 마진값을 합친 50px의 값이 두 박스 사이의 거리가 되었어야 했다. 그런데 실제 실행결과를 보면 둘 중 값이 가장 큰 가장 위 박스의 margin값 30px만 적용된 것을 알 수 있다. 이것이 마진 중첩의 결과이다. 이는 박스들을 하나의 열로 배치할 때 중간의 마진값이 커지는 것을 방지하기 위함이라고 한다. 

반면, 박스들을 한 행으로 정렬할 경우, 마진 중첩은 일어나지 않고, 두 박스의 마진값이 더해진 값만큼 두 박스 간의 거리가 결정된다. 아래 사진들을 보면, 아까와 달리 어느 한 박스의 margin값이 두 박스 사이의 영역을 다 차지하지 않고, 딱 정해진 margin값만큼만 차지하는 것을 알 수 있다. 

![사진 9](/images/2023-12-13/css-box-model/19.png)

사진 9

![사진 10](/images/2023-12-13/css-box-model/20.png)

사진 10

## padding

컨텐츠와 박스 경계선 사이의 여백을 설정할 때 쓰이는 속성인 padding 역시 margin과 동일하게 상하좌우 모두에 같은 값을 일괄적으로 적용할 수도 있고, 방향에 따라 서로 다른 값으로 설정할 수 있다. 값을 2개 이상 지정할 경우 적용되는 방향은 margin과 동일하다.

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석