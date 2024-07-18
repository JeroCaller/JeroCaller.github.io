---
title: "[CSS][반응형 웹] 미디어 쿼리(Media query)"
category: "CSS"
tag: ["css", "web", "media query"]
---

# 미디어 쿼리 (Media query)

미디어 쿼리는 반응형 웹 디자인을 실현하기 위한 수단으로, 웹 페이지에 접근하는 전자기기 화면 크기(또는 브라우저 창의 크기)에 따라 웹 페이지 내 요소들의 레이아웃도 그에 따라 다르게 적용하기 위해 사용되는 수단이다. 미디어 쿼리는 CSS 모듈이라고 하며, 전자기기 화면 크기 또는 브라우저 창의 크기에 따라 CSS 스타일 속성을 다르게 적용하는 원리를 이용하여 반응형 웹 디자인을 실현시켜준다. 

# 미디어 쿼리 문법

미디어 쿼리를 CSS에서 사용할 때의 문법, 기본 구조는 다음과 같다. 

```css
@media screen [and 조건문] {
    선택자 {
        스타일 규칙
    }
}
```

먼저, @media 라는 키워드로 시작하며, 그 다음에는 적용하고자 하는 미디어 유형을 기입한다. 여기서는 screen으로 지정하여 컴퓨터 화면을 대상으로 하였다. 그 뒤로는 조건문을 사용할 수 있으며, 조건문은 and 키워드와 같이 사용하면 여러 개의 조건문도 가능하다(단 하나의 조건문이라도 앞에 and 키워드는 붙여야 한다). 그리고 중괄호 내에는 스타일 규칙을 적용하고자 하는 선택자를, 그 선택자 오른쪽의 중괄호 안에는 원하는 스타일 규칙을 적용하면 된다. 

위 미디어 쿼리 문법을 이용하면, 예를 들어 브라우저 창이 일정 px 이하가 될 경우, 원래 웹 페이지 내에 있던 가로로 배열된 상자들을 세로로 배열하게끔 설정할 수도 있는 것이다. 

참고로, @media 키워드와 미디어 유형 사이에는 only 또는 not 키워드를 사용할 수도 있고, 생략할 수도 있다. only를 쓸 경우, 미디어 쿼리를 지원하지 않는 브라우저라면 해당 미디어 쿼리를 무시하라는 뜻이다. not을 쓸 경우, not screen처럼 해당 미디어를 제외한 나머지 미디어 유형들을 지칭하게 된다. 

만약 위 미디어 쿼리 구문을 HTML 문서 내에서 사용하고자 한다면 `<style></style>` 태그 사이에 작성하면 된다. 

## 미디어 유형

앞서 미디어 쿼리 구문을 살펴볼 때 screen처럼 미디어 유형을 지정할 수 있었다. 지정할 수 있는 미디어 유형에는 다음이 존재한다. 

- all : 모든 미디어에서 스타일 속성을 적용
- print : 프린터기와 같은 인쇄 장치
- screen : 컴퓨터 화면
- speech : 음성 합성장치

> References [1]에 따르면, Media Queries 3 모듈에서는 tv, projection 등 여러 다양한 미디어 유형들을 지원하였으나, Media Queries 4 모듈에서는 제거되었다고 한다. 따라서 최신 미디어 쿼리 모듈에서는 위에서 언급한 미디어 유형들만 사용 가능한 것으로 보인다.
> 

## 조건문

앞서 살펴본 @media 구문의 조건문에는 여러 가지가 들어갈 수 있는데, 뷰포트 크기, 전자기기의 크기, 화면 회전 속성 등이 있다. 이 외에도 조건문으로 사용할 수 있는 속성들이 더 있다고 하니, 더 자세한 사항은 검색 엔진에서 검색해서 조사해보는 것을 추천한다. 

### 뷰포트 크기

우선 뷰포트 크기에 대해 미디어 쿼리 조건문 내에서 사용할 수 있는 속성에는 다음이 존재한다. 

- width : 웹 페이지(뷰포트) 너비
- height : 웹 페이지 높이
- min-width : 웹 페이지의 최소 너비
- min-height : 웹 페이지의 최소 높이
- max-width : 웹 페이지의 최대 너비
- max-height : 웹 페이지의 최대 높이

다음은 위 속성들을 이용한 예이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .box { 
            border: 1px solid black;
            width: 200px;
            height: 200px;
            margin: 10px;
            display: inline-block;
        }
        .box p {
            text-align: center;
        }
        @media screen and (max-width:800px) {
            /* 뷰포트의 최대 너비가 800px일 경우.*/
            /* 
                브라우저 창의 너비를 800px 이하로 줄일 경우, 
                상자들의 배치가 가로에서 세로로 바뀐다. 
            */
            .box {
                display: block;
            }
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box">
            <p>Box 1</p>
        </div>
        <div class="box">
            <p>Box 2</p>
        </div>
    </div>
</body>
</html>
```

<video src="/resources/2024-01-26/css-responsive-web-media-query/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>

예제 2-1 실행결과

단, 뷰포트의 “높이”를 미디어 쿼리의 조건문으로 사용할 때 주의해야할 점이 있다. 

- screen이 아닌 나머지 미디어에 대해서는 단지 브라우저에 보이는 웹 페이지 영역의 높이만을 상정하는 것이 아니라, 스크롤 위부터 아래까지의 전체 문서의 높이로 지정해야한다고 한다.
- print에서는 한 페이지의 높이를 기준으로 한다고 한다.

### 디바이스 장치의 크기

한 편, 뷰포트가 아니라, 디바이스 장치 화면 내 브라우저 창의 크기를 사용하여 미디어 쿼리의 조건문으로 사용할 수 있다. 

> 브라우저를 켜보면 알겠지만, url 입력 부분, 북마크 등의 요소는 웹 페이지를 보여주는 영역이 아니므로 뷰포트의 영역이 아니다. 보통 브라우저 안에 뷰포트가 포함되어 있는 구조이다.
> 

다음은 디바이스 장치 내 브라우저 창의 크기를 조건문으로 이용할 때 사용할 수 있는 속성들이다. 

- device-width, device-height : 디바이스의 너비, 높이 지정
- min-device-width, min-device-height : 디바이스의 최소 너비, 높이 지정
- max-device-width, max-device-height : 디바이스의 최대 너비, 높이 지정

> 모든 디바이스의 화면 크기를 정확히 알 수는 없는 노릇이니, 검색 엔진에서 스마트폰, 태블릿 등 여러 디바이스들의 화면 크기를 알려주는 사이트를 검색해서 찾아보는 것이 좋을 듯 싶다.
> 

# 중단점 (Break point)

중단점은 화면 크기에 따라 반응형 웹 페이지 내 스타일 속성들을 미디어 쿼리를 이용하여 각각 다르게 적용하고자 할 때의 화면 크기 기준을 의미한다. 예를 들어, 스마트폰 화면에서는 A라는 스타일 속성을 지정하고자 할 때, 해당 스마트폰 화면 크기가 중단점이 되고, 마찬가지로 스마트폰보다 큰 태블릿 화면에서는 B라는 다른 스타일 속성을 지정하고자 할 때 이 때의 태블릿 화면 사이즈가 중단점이 된다. 

보통은 중단점을 모바일, 태블릿, 데스크톱 이렇게 세 개로 구분짓는다고 한다. 기준은 다음과 같다고 한다.

- 스마트폰에서는 기본적으로는 따로 미디어 쿼리를 지정하지 않아도 된다고 한다.
- 태블릿은 가로 * 세로 크기가 1024px * 768px 이상일 경우를 태블릿으로 간주한다.
- 1024px 이상이면 데스크톱으로 간주한다.

 물론 원한다면 앞서 살펴본 디바이스 화면 크기 속성 또는 뷰포트 크기 속성을 이용하여 미디어 쿼리 조건문으로 활용함으로써 여러 개의 중단점을 만들 수도 있겠다. 

# 미디어 쿼리를 HTML 문서에 적용하는 방법

## 미디어 쿼리 구문을 외부 CSS 파일에 정의하고, 이를 HTML 문서에서 불러오는방법

미디어 쿼리 구문을 외부 CSS 파일 내에서 정의를 했다면, 이를 사용하기 위해 HTML 문서에서 이를 불러들여야 하는데, 여기에는 2가지 방법이 있다. 하나는 HTML의   `<link>` 태그를 사용하는 방법과 @import 키워드를 사용하는 방법이다.

`<link>` 태그를 이용하여 외부 CSS 파일을 import 해오는 방법은 [CSS 기초 개념](/css/css-basic-concepts/) 페이지에도 언급되어 있고, @import에 대한 이야기는 [HTML&CSS] Link VS @import (현재 준비 중) 문서에서도 언급되어 있으니 참조. 여기서는 외부 CSS 파일 내 미디어 쿼리 구문을 import 해오는 방법에 대해 언급할 것이다. 

- <link> 태그를 이용하기.

해당 태그를 이용하여 미디어 쿼리를 import 해올 수 있다. 

```html
<link rel="stylesheet" media="screen" href="css\style.css">
```

즉, <link> 의 media 속성값으로 불러들여올 외부 CSS 파일 내에 정의되어 있는 미디어 유형 (또는 조건문도 뒤에 붙일 수 있다)을 기입하면 된다. 

- @import 키워드 사용하기

<link> 태그가 HTML 문법이라면, @import 키워드는 CSS 문법으로, 역시 외부 CSS 파일을 불러올 때 사용하는 키워드이다. 만약 HTML 문서 내에서 이 키워드를 이용하여 외부 CSS 파일을 불러오고자 한다면, 해당 키워드는 CSS 문법이므로 <style> 태그 내에 작성해야 한다. 

```html
<style>
    @import url('css\style.css') screen;
</style>
```

다음은 이러한 import 방식을 각각 예제 2-1에 적용해본 코드들이다. 예제 2-1의 코드를 html, css 파일로 나눠서 작성하였다. 실행 결과는 모두 예제 2-1과 동일하였다. 

```css
.box { 
    border: 1px solid black;
    width: 200px;
    height: 200px;
    margin: 10px;
    display: inline-block;
}
.box p {
    text-align: center;
}
@media screen and (max-width:800px) {
    /* 뷰포트의 최대 너비가 800px일 경우.*/
    /* 
        브라우저 창의 너비를 800px 이하로 줄일 경우, 
        상자들의 배치가 가로에서 세로로 바뀐다. 
    */
    .box {
        display: block;
    }
}
```

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        @import url("media.css") screen;
    </style>
</head>
<body>
    <div id="container">
        <div class="box">
            <p>Box 1</p>
        </div>
        <div class="box">
            <p>Box 2</p>
        </div>
    </div>
</body>
</html>
```

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="media.css" media="screen">
</head>
<body>
    <div id="container">
        <div class="box">
            <p>Box 1</p>
        </div>
        <div class="box">
            <p>Box 2</p>
        </div>
    </div>
</body>
</html>
```

## HTML 문서 내에 직접 사용하는 방법

반면, HTML 문서 내에 직접 미디어 쿼리 구문을 정의하여 적용하는 방법도 있다. 여기에도 두 가지 방법이 있는데, 하나는 `<style>` 태그 내에 다른 CSS 스타일 규칙처럼 미디어 쿼리 구문도 그대로 쓰는 방법이고, 또 하나는 `<style>` 태그 자체의 속성 media로 정의하는 방법이다. `<style>` 태그 내에 사용하는 전자의 경우는 일반적인 CSS 스타일 규칙을 `<style>` 태그 내에 작성하는 것과 동일하므로 여기서는 후자의 경우를 살펴보겠다. 

```html
<style media="screen and (max-width:800px)">
	.box {
        display: block;
    }
<style>
```

즉, `<style>` 태그 내 media 속성의 값으로 미디어 유형과 그 조건문을 입력한 후, 해당 태그 내부에서 적용하고자 하는 스타일 규칙을 그대로 적용하면 된다. 

이렇게 미디어 쿼리를 적용하는 방법에는 다양한 방법이 있지만, 사실 미디어 쿼리 문법 자체는 그 구조에서 큰 차이가 없으므로 그리 어렵지 않게 사용할 수 있을 것이다. 


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [미디어 쿼리 사용하기 - CSS: Cascading Style Sheets \| MDN](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_media_queries/Using_media_queries)