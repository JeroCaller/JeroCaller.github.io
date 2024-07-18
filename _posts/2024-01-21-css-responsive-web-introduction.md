---
title: "[CSS] 반응형 웹 개요"
category: "CSS"
tag: ["css", "web"]
---

# 반응형 웹 디자인 (Responsive web design)

반응형 웹 디자인은 각 전자기기의 화면 크기에 맞게끔 자동으로 웹 요소들의 배치 및 크기 등을 조절해주는 디자인이다. 이를 이용하면 PC, 스마트폰, 태블릿 등 화면의 크기가 각자 다른 전자기기들을 위해 따로 웹 사이트를 제작할 필요없이 반응형 웹을 이용하기만 하면 되기에 편리해진다. 또한, 같은 전자기기라도, 브라우저의 크기를 사용자가 마음대로 조절할 경우, 그 크기에 맞게 웹 요소들의 배치와 크기를 자동으로 조절하게끔 할 수 있다. 

## 뷰포트 (Viewport)

뷰포트는 전자기기의 화면 속에서 웹 요소들이 표시되는 전체 영역을 의미한다. 쉽게 말하면, 브라우저에서 웹 사이트를 보여주는 전체 영역이라 보면 되겠다. PC, 스마트폰, 태블릿 등의 각종 전자기기들은 저마다의 화면 크기가 다 다르므로 각자의 뷰포트도 다를 것이다. (같은 스마트폰이라도 크기가 각자 다 다르기도 하다) 그래서 각 전자기기마다의 화면 크기에 맞게끔 웹 사이트 영역 크기를 조절해야 한다. 문제는, 모바일 브라우저에서는 뷰포트의 기본 크기가 설정되어 있어 이를 고려하지 않는 경우, 아무리 웹 사이트의 크기를 모바일 기기 화면에 맞게끔 설정해놓아도 모바일 브라우저에서 지정한 기본 뷰포트 크기로 설정되어, 예상과 다르게 웹 요소들의 배치가 이상하게 된다든가 내용이 더 작게 표시될 수도 있다. 따라서 이 경우, 뷰포트의 개념을 숙지하고 문제에 대응해야겠다. 

우리가 HTML, CSS에서 뷰포트에 대해 할 수 있는 것은 대표적으로 뷰포트의 속성을 지정하는 것과, 뷰포트 내에서 보여질 웹 요소들의 크기에 대한 단위 설정이 있다. 

### 뷰포트 속성 지정

뷰포트에 대한 속성 지정은 HTML 문서 내의 <head>태그 내부에서 <meta> 태그의 속성들을 이용하여 지정할 수 있다. 다음은 그 예시이다.

```html
<head>
	<!-- 생략 -->
	<meta name="viewport" content="width=device-width, user-scalable=yes">
</head>
<body>
	<!-- 생략 -->
</body>
```

<meta> 태그 내에서 name=’viewport’로 지정한 뒤, content 속성 내에서 뷰포트의 속성들을 지정할 수 있다. 여러 개의 속성들을 한꺼번에 지정할 수 있으며, 이 때 각 속성들은 쉼표(,)로 구분한다. content 속성값으로 지정할 수 있는 뷰포트 속성과 그 값에는 다음이 있다.

- width: 뷰포트의 너비를 설정하는 속성으로, device-width 라는 키워드를 사용하여 웹 사이트를 보여줄 전자기기 화면의 너비로 지정하거나, 또는 숫자로도 지정할 수 있다. 기본값은 브라우저의 기본값을 따른다. → 앞서 말한 뷰포트의 개념을 고려하지 않고 모바일용으로 웹 사이트의 크기를 지정해도 원하는 크기로 지정되지 않는다고 언급한 것이 여기서 비롯되는 것 같다.
- height: 뷰포트의 높이를 설정하는 속성으로, device-height라는 키워드로 전자기기의 높이에 맞게끔 설정할 수 있으며, 또는 숫자값으로 따로 지정할 수도 있다. 이 속성도 기본값은 브라우저의 기본값을 따른다.
- user-scalable: 사용자가 웹 사이트를 확대, 축소하게끔 할 수 있는지를 결정할 수 있는 속성으로, yes, no중 하나를 선택할 수 있다. 이 때, yes는 1, device-width와 device-height은 10으로 간주한다고 한다. 기본값은 yes이다.
- initial-scale : 초기 확대-축소값으로, 1부터 10까지 중의 숫자 중에서 택할 수 있다. 기본값은 1이다.

### 뷰포트 단위

한 편, 웹 요소 크기의 단위를 뷰포트의 크기를 기준으로 삼을 수도 있다. 다음은 뷰포트 단위들이다. 

- vw (Viewpoint Width) : 1vw = 뷰포트 너비의 1%. 예) 뷰포트 너비가 500px이라면 1vw = 1% * 500px = 5px
- vh (Viewpoint Height) : 1vh = 뷰포트 높이의 1%
- vmin (Viewpoint Minimum) : 1vmin = 1% * min(width, height) → 뷰포트 너비와 높이 중 작은 값의 1%. 예) 뷰포트 너비가 500px, 높이가 300px일 경우 1vmin = 1% * 300px = 3px
- vmax (Viewpoint Maximum) : 1vmax = 1% * max(width, height) → 뷰포트 너비, 높이 중 큰 값의 1%

참고로, 화면을 돌릴 때 브라우저 화면도 같이 회전하도록 되어 있는 경우, 뷰포트의 너비가 높이가 되고, 높이가 너비가 되므로 이 경우 1vw, 1vh의 값이 달라질 수 있다. 단, 너비와 높이 중 최대, 최소값은 변하지 않기에 vmin, vmax에는 변화가 없다. 

다음은 웹 요소에 뷰포트 단위인 vw를 적용했을 때와 px을 적용했을 때 브라우저 창의 크기를 변화시킴으로써 웹 요소들의 크기에 어떤 변화가 있는지를 확인하는 예제 코드들이다. 먼저 vw 단위를 적용한 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>When uses unit of viewpoint</title>
    <style>
        .box {
            border: 1px solid black;
            width: 30vw;
            height: 30vw;
            margin: 5vw auto;
        }
        .box p {
            font-size: 2vw;
            text-align: center;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">
            <p>브라우저 창의 크기를 조절하면서 보세요.</p>
        </div>
    </div>
</body>
</html>
```

<video src="/resources/2024-01-21/css-responsive-web-introduction/video1.mp4"
    controls="controles"
    muted="muted"
    width="80%"
></video>

에제 2-1 실행결과

위 예제 2-1을 보면 알 수 있듯, 텍스트 크기와 박스 너비, 높이를 결정할 때 vw 단위를 사용하였다. 그 후 브라우저 상에서 실행하여 브라우저 창을 변화시켜보면 그에 따라 글자 크기와 박스의 크기도 자동적으로 변하는 것을 알 수 있다. 

반면, px 단위로만 사용한다면? 다음은 그 예제와 예제 코드의 실행결과이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>None unit of viewpoint</title>
    <style>
        .box {
            border: 1px solid black;
            width: 300px;
            height: 300px;
            margin: 5vw auto;
        }
        .box p {
            font-size: 20px;
            text-align: center;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">
            <p>브라우저 창의 크기를 조절하면서 보세요.</p>
        </div>
    </div>
</body>
</html>
```

<video src="/resources/2024-01-21/css-responsive-web-introduction/video2.mp4"
    controls="controles"
    muted="muted"
    width="80%"
></video>

예제 2-2 실행결과.

px 단위를 사용하는 경우, 브라우저 창의 크기 변화에도 글자 크기와 박스의 크기는 변하지 않는 것을 알 수 있다. 

# 크롬 브라우저의 device toolbar 활용

디바이스 화면의 크기마다 웹 페이지가 어떻게 보일지를 미리 확인해볼 수 있는 도구가 있다. 크롬 브라우저의 개발자 도구의 device toolbar를 이용하면 된다. 

먼저 크롬 브라우저에서 보고자 하는 웹 페이지를 띄운 후, 키보드에서 F12 버튼을 누른다. 그러면 아래 사진처럼 오른쪽 화면에 개발자 도구가 나타난다. 

![사진 1.](/images/2024-01-21/css-responsive-web-introduction/1.png)

사진 1.

개발자 도구의 위의 “Elements” 왼쪽에 PC 모니터와 스마트폰이 같이 있는 아이콘을 클릭한다. (위 사진의 하늘색 사각형으로 표시한 곳) 그러면 왼쪽 화면이 다음과 같이 변한다. 

![사진 2](/images/2024-01-21/css-responsive-web-introduction/2.png)

사진 2

웹 페이지 화면의 맨 위를 보면 무언가 툴바 같은게 뜬 것을 확인할 수 있다. 툴바 아래에는 눈금이 있는데, 여기서는 원하는 빈 상자 영역을 클릭하면 그 폭에 맞게 웹 페이지의 폭이 변한다. 

![사진 3.](/images/2024-01-21/css-responsive-web-introduction/3.png)

사진 3.

위 사진은 Mobile L” 사이즈로 변경한 것으로, 모바일 기기 중 큰 사이즈를 가정하고 웹 페이지의 너비도 그에 맞춘 것이다. 이를 이용하면 원하는 디바이스 너비로 축소, 확대 시켜서 웹 페이지가 어떻게 보일지를 미리 확인할 수 있는 것이다. 

또한, 맨 위의 “Dimension: Responsive” 옆의 ▼ 삼각형을 클릭하면 다음과 같은 리스트가 뜬다. 

![사진 4.](/images/2024-01-21/css-responsive-web-introduction/4.png)

사진 4.

여기서 실제 스마트폰 기종을 선택하여 해당 기종의 실제 화면으로 변경하여 웹 페이지가 어떻게 보이는지를 확인할 수 있다. 이러한 기능을 이용하면 굳이 실제 스마트폰, 전자기기를 가져와서 확인해보지 않아도 이 도구를 이용하여 미리 웹 페이지 내 요소들 간의 배치나 요소들의 크기를 파악할 수 있다. 

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] 뤼튼 - GPT-4

[3] [viewport란?](https://velog.io/@ken1204/viewport란)