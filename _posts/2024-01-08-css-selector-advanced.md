---
title: "[CSS] 선택자 심화"
category: "CSS"
tag: ["css", "web", "selector"]
---
# 요소 계층 관계

어떤 요소 A 안에 다른 요소 B가 포함되어 있다면 요소 A는 요소 B의 부모 요소라 부르고, 요소 A에 대해 요소 B는 요소 A의 자식 요소라 불린다. 만약 요소 B 안에 요소 C가 존재한다면 요소 A 입장에서 요소 C를 손자 요소라 부른다. 

부모 요소 A가 있고, A 요소의 자식 요소가 B, C가 있을 때, 자식 요소 B와 C를 서로 형제 관계라고 한다. 이 때, HTML 코드 구조에서 형제 관계의 요소들 중 제일 먼저 정의된 요소를 형 요소, 그 뒤에 나오는 요소를 동생 요소라 부른다. 

```html
<div id='A'>
    <p id='B'>hello</p>
    <p id='C'>world</p>
</div>
```

위 예제에서, 각각 id가 ‘B’, ‘C’를 가지는 두 요소 p는 공통의 부모 요소 id=’A’의 div 요소를 가지므로 두 요소는 형제 관계이며, 그 중 id=’B’의 p 요소가 먼저 나왔기에 형 요소이고, id=’C’인 p 요소는 그 뒤에 나왔기 때문에 동생 요소라 불리게 된다. 

# 연결 선택자 (combination selector)

연결 선택자는 둘 이상의 선택자들을 조합하여 사용해서 ‘조합 선택자(combination selector)’라 불리기도 한다. 연결 선택자에는 여러 종류가 있다. 하위 선택자, 자식 선택자, 인접 형제 선택자, 형제 선택자가 있다. 

## 하위 선택자 (descendant selector)

하위 선택자는 서로 상하관계로 엮인 둘 이상의 요소들에 대해, 지정된 상위 요소 내 지정된 모든 하위 요소들에 대해 스타일 규칙을 지정할 때 사용한다. 상위 요소를 A, A의 하위 요소를 B라 하면, 하위 선택자는 다음과 같이 공백 한 칸으로 구분한다.

```css
A B { /* 스타일 규칙 */ }
```

하위 선택자는 부모 요소 A 안에 있는 모든 하위 요소 B들을 지정한다. 즉, 하위 요소 B가 A에게 있어서 자식 요소이든 손자 요소이든 그 깊이에 관계없이 부모 요소 A 안에 있기만 하면 모두 지정한다. 

다음은 하위 선택자를 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #frame-des p {
            font-style: italic;
            font-weight: bold;
        }
        #frame-child > p {
            font-style: italic;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div id="frame-des">
        <p>
            어느 날, 작은 마을에 한 소년이 살고 있었습니다. 그 소년의 이름은 미카였습니다. 
            마을은 조용하고 평화로웠지만, 미카는 항상 어딘가 모험을 꿈꾸고 있었습니다.
        </p>
        <div>
            <p>
                어느 날, 마을에 한 소녀가 이사를 오게 되었습니다. 
                그 소녀의 이름은 리나였습니다. 리나는 미카와 같은 나이지만, 
                성격은 완전히 다른 반대의 매력을 지니고 있었습니다. 
                미카는 호기심 가득한 소년이었고, 리나는 차분하고 신중한 소녀였습니다.
            </p>
        </div>
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-01-08/css-selector-advanced/1.png)

예제 2-1 실행결과

위 코드를 보면, #frame-des p라는 하위 선택자가 사용되었다. 그리고 해당 스타일 규칙을 적용했을 때, id=’frame-des’인 div 요소 안에 존재하는 모든 요소 p에 대해 해당 스타일 규칙이 적용된다. 자식 요소인 p와 한 단계 더 아래에 존재하는 p 요소 모두에 적용되었다. 

## 자식 선택자 (child selector)

하위 요소의 깊이에 상관없이 모든 하위 요소들을 지정했던 하위 선택자와는 달리, 자식 선택자는 오로지 자식 요소만을 지정한다. 

자식 선택자는 부모 요소와 자식 요소 사이에 ‘>’ 기호를 사용한다. 

```css
A > B {/* 스타일 규칙 */}
```

앞서 위의 ‘하위 선택자’ 챕터의 예제 2-1에서, 최상위에 존재하는 div 요소의 id를 id=’frame-child’으로 바꾸면 다음의 결과를 얻는다. 

![예제 1-1에서 코드를 일부 바꾼 후 실행한 결과.](/images/2024-01-08/css-selector-advanced/2.png)

예제 1-1에서 코드를 일부 바꾼 후 실행한 결과.

id=’frame-child’인 div 부모 요소의 자식 요소 p에만 적용되었고, 손자 요소에는 적용되지 않은 것을 확인할 수 있다. 

## 인접 형제 선택자 (adjacent selector)

형제 요소에서 동생 요소가 여러 개 있을 때, 그 중에서 첫 번째 동생 요소만 선택하는 인접 형제 선택자가 존재한다. 형 요소 A와 동생 요소 B가 있을 때 CSS에서 인접 형제 선택자를 정의하는 방법은 다음과 같다.

```css
A + B {/* 스타일 규칙 */}
```

즉, 형 요소와 첫 번째로 등장하는 동생 요소 사이에 ‘+’ 기호를 삽입하면 된다. 다음은 이 선택자를 이용한 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #title + .paragraph1 {
            font-style: italic;
            font-weight: bold;
	          color: green;
        }
        #title ~ .paragraph2 {
            font-style: italic;
            font-weight: bold;
            color: green;
        }
    </style>
</head>
<body>
    <div id="frame">
        <div id="title">
            <h1>짧은 이야기</h1>
        </div>
        <div class="paragraph1">
            <p>
                어느 날, 작은 마을에 한 소년이 살고 있었습니다. 그 소년의 이름은 미카였습니다. 
                마을은 조용하고 평화로웠지만, 미카는 항상 어딘가 모험을 꿈꾸고 있었습니다.
            </p>
        </div>
        <div class="paragraph1">
            <p>
                어느 날, 마을에 한 소녀가 이사를 오게 되었습니다. 
                그 소녀의 이름은 리나였습니다. 리나는 미카와 같은 나이지만, 
                성격은 완전히 다른 반대의 매력을 지니고 있었습니다. 
                미카는 호기심 가득한 소년이었고, 리나는 차분하고 신중한 소녀였습니다.
            </p>
        </div>
    </div>
</body>
</html>
```

![예제 3-1 실행결과](/images/2024-01-08/css-selector-advanced/3.png)

예제 3-1 실행결과

위 예제 코드에서 \<style> 태그 내에 ‘+’ 기호가 사용된 선택자가 인접 형제 선택자이고, body 요소 내부에서 해당 선택자가 적용된 스타일 규칙들이 적용되었다. 

id=’title’의 div와 id=’paragraph1’의 div 요소는 분명 공통의 부모 요소 id=’frame’의 div를 가지고 있으므로 서로 형제 요소임을 알 수 있다. 그리고 id=’paragraph1’의 div 요소는 동생 요소이며, 2개의 동생 요소가 존재함을 알 수 있다. 인접 형제 선택자는 형 요소 다음으로 가장 첫 번째에 등장하는 동생 요소에만 스타일 규칙을 적용한다. 

## 형제 선택자 (sibling selector)

인접 형제 선택자와 달리, 형제 선택자는 형 요소를 기준으로 모든 동생 요소에 스타일 규칙을 적용시킨다. 형제 선택자는 인접 형제 선택자와 비슷하나 두 요소 사이에 들어가는 기호가 ‘~’이라는 점만 다르다. 

```css
A ~ B { /* 스타일 규칙 */}
```

앞서 인접 형제 선택자에서 다뤘던 예제 3-1의 \<body> 태그 내에서, id=’paragraph1’의 div 요소들의 id를 모두 id=’paragraph2’로 바꾸고 다시 실행하면 다음의 결과를 얻는다. 

![예제 3-1의 내용을 일부 수정한 후 실행한 후의 모습.](/images/2024-01-08/css-selector-advanced/4.png)

예제 3-1의 내용을 일부 수정한 후 실행한 후의 모습.

이번에는 id=’title’ 형 요소 아래의 id=’paragraph2’를 가지는 모든 동생 요소 div에 대해 해당 스타일 규칙이 적용되었음을 확인할 수 있다. 

# 속성 선택자 (Attribute selector)

## [속성] 선택자

HTML 태그 내에서 속성을 사용할 때도 있는데, 특정 속성을 사용하는 태그에만 따로 스타일 규칙을 적용시킬 수 있다. 태그[속성] 형태로 사용되며, 다음은 그 예시이다.

```css
ul[type] {}
```

위 사용 예시를 보면, ul이란 태그들 중 type 속성을 사용하는 태그만 지정하는 선택자를 정의한 것이다. 다음은 이 선택자를 활용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0" />
    <style>
        /* 아이콘 관련 스타일 규칙*/
        .material-symbols-outlined {
            font-variation-settings:
            'FILL' 0,
            'wght' 400,
            'GRAD' 0,
            'opsz' 24
        }
        ul[type] {
            background-color: pink;
        }
    </style>
</head>
<body>
    <h2>todo list</h2>
    <ul type="none">
        <li>
            <span class="material-symbols-outlined">
                check_box_outline_blank
            </span>
            프로그래밍 언어 공부.
        </li>
        <li>
            <span class="material-symbols-outlined">
                check_box_outline_blank
            </span>
            집안일 하기.
        </li>
    </ul>
    <h2>일정</h2>
    <ul>
        <li>
            <span class="material-symbols-outlined">
                event
            </span>
            없음.
        </li>
    </ul>
    <h2>내 취미</h2>
    <ol type="I">
        <li>코딩으로 토이 프로젝트 만들기</li>
        <li>인터넷 방송 또는 유튜브 보기</li>
    </ol>
</body>
</html>
```

![예제 4-1 실행결과](/images/2024-01-08/css-selector-advanced/5.png)

예제 4-1 실행결과

위 예제 코드를 보면, \<style> 태그 내 ul[type] 선택자를 사용하여 ul 태그 중 type 속성을 사용한 태그에만 배경색을 분홍색으로 바꾸게끔 하였다. 그 결과, \<body> 태그 내 총 3개의 목록 요소들 중 ul 태그이면서 type 속성을 가진 요소에만 해당 스타일 규칙이 적용된 것을 알 수 있다. 

## [속성=속성값] 선택자

한 편, 속성 뿐만 아니라 특정 속성값도 가지는 태그에만 스타일 규칙을 적용하도록 할 수도 있다. 다음은 그 예이다.

```css
ol[type='I'] {}
```

위 사용 예시를 보면, ol 태그의 type 속성을 가지는 요소들 중, type=’I’의 속성값을 가지는 요소들만 지정한다는 의미이다. 다음은 이를 활용한 예제 코드이다. 기본적인 틀은 앞선 예제 4-1과 동일하므로 여기선 예제 4-1에서 추가한 코드와 일부 코드만 보여주고 있다. 

```html
<!-- 생략 -->
<style>
	  /* 생략 */
    ol[type='I'] {
        background-color: cornflowerblue;
    }
</style>
<!-- 생략 -->
<ol type="I">
    <li>코딩으로 토이 프로젝트 만들기</li>
    <li>인터넷 방송 또는 유튜브 보기</li>
</ol>
<h2>그냥 목록</h2>
<ol type="A">
    <li>이제 더 이상</li>
    <li>쓸 말이 없다.</li>
</ol>
<!-- 생략 -->
```

![예제 5-1 실행결과](/images/2024-01-08/css-selector-advanced/6.png)

예제 5-1 실행결과

위 예제를 보면, ol 태그들 중 type 속성의 속성값이 정확히 ‘I’인 요소에만 배경색 지정 스타일 규칙이 적용된 것을 알 수 있다. 

## [속성~=값] 선택자

해당 선택자는 하나의 특정 속성에 하나 이상의 여러 속성값들이 있을 때, 그 중 지정한 속성값이 포함된 요소를 선택한다. 

```css
[class ~= bgcolor] {}
```

예를 들어 위와 같이 선택자를 정의하면… 

```html
<div class='box bgcolor'></div>
<div class='bgcolor'></div>
```

위 두 요소 모두를 가리키게 된다. 

주의할 점은, 속성 선택자 내의 속성값과 한 글자라도 다른 요소에 대해서는 해당 선택자의 선택을 받지 못한다. 예를 들어 ‘bgcolors’, ‘bgcolor-blue’와 같은 속성값들은 위 선택자의 지정을 받지 못한다. 

다음은 해당 선택자를 활용한 예이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .box {
            border: 3px solid black;
            width: 500px;
            height: 100px;
            margin: 10px;
        }
        .bgcolor-blue {
            border: 3px solid blue;
        }
        div[class~=bgcolor] {
            background-color: cadetblue;
            width: 500px;
            height: 100px;
            margin: 10px;
        }
    </style>
</head>
<body>
    <div class="box">good</div>
    <div class="bgcolor">wow</div>
    <div class="box bgcolor">hi</div>
    <div class="bgcolor-blue">jeez</div>
    <p class="bgcolor">Emtpy paragraph</p>
</body>
</html>
```

![예제 6-1 실행결과](/images/2024-01-08/css-selector-advanced/7.png)

예제 6-1 실행결과

위 예제에서 `div[class~=bgcolor]` 선택자는 `<div class="bgcolor">wow</div>`와 
`<div class="box bgcolor">hi</div>` 요소를 선택한 것을 알 수 있다. 만약 태그 종류 상관없이 class 속성에 bgcolor라는 속성값을 가진 모든 요소에 적용하고자 한다면 `div[class~=bgcolor]`에서 `[class~=bgcolor]`로 바꾸면 된다. 이렇게 바꾸면 다음과 같은 결과를 얻는다. 

![예제 6-1의 일부 코드를 변경한 후 재실행한 후의 모습](/images/2024-01-08/css-selector-advanced/8.png)

예제 6-1의 일부 코드를 변경한 후 재실행한 후의 모습

그러면 class=’bgcolor’를 가지는 p 요소에도 같은 스타일이 적용된 것을 알 수 있다. 

## [속성 |= 값] 선택자

해당 속성 선택자의 \| 기호는 키보드에서 엔터키 위에 있는 백슬래시(\\) 키와 shift 키를 동시에 누르면 된다. 

해당 선택자는 속성값이 단 한 개만 있는 요소에만 적용되며, 속성값 내에 하이픈(-) 기호가 붙어 있어도 이 요소를 지정한다. 예를 들어, 

```css
div[class|=bgcolor] {}
```

이러한 선택자가 있다면, 해당 선택자는 다음의 요소들을 지정한다. 

```html
<div class="bgcolor">wow</div>
<div class="bgcolor-blue">jeez</div>
```

즉, bgcolor-로 시작하는 속성값을 가지는 요소도 선택한다. 다음은 이를 활용한 예제로, 앞서 예제 6-1 코드에 다음의 코드를 추가하였다. 

```html
<style>
    /* 생략 */
    div[class|=bgcolor] {
        background-color:darkorchid;
        width: 500px;
        height: 100px;
        margin: 10px;
    }
</style>
<!-- 생략 -->
```

![예제 6-2 실행결과](/images/2024-01-08/css-selector-advanced/9.png)

예제 6-2 실행결과

## 속성값 위치에 따른 속성 선택자들

속성값 내에 일부 속성값이 포함되어 있으면 이를 지정하는 속성 선택자들이 있다. 이 때, 찾고자 하는 일부 속성값의 위치에 따라 사용되는 기호가 각각 다르다. 다음은 이에 대한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        li {
            margin: 20px;
        }
        /* a 태그 중 href 속성의 값이 www로 시작하는 요소에 적용.*/
        a[href^=www] {
            border: 5px solid black;
        }
        /* a 태그 중 href 속성의 값이 org로 끝나는 요소에 적용.*/
        a[href$=org] {
            border: 5px dashed violet;
        }
        /* 
            a 태그 중 href 속성의 값에 news가 포함되어 있는 요소에 적용.
            news가 속성값 내에 어느 위치에 있든 상관없이 존재하기만 하면 
            해당 선택자의 선택을 받는다. 
         */
        a[href*=news] {
            border: 5px solid green;
        }
    </style>
</head>
<body>
    <ul>
        <li><a href="www.google.com">Google</a></li>
        <li><a href="developer.mozilla.org">Mozilla</a></li>
        <li><a href="www.daum.net">Daum</a></li>
        <li><a href="https://news.einfomax.co.kr">einfomax</a></li>
        <li><a href="https://news.google.com">Google news</a></li>
    </ul>
</body>
</html>
```

![예제 7-1 실행결과](/images/2024-01-08/css-selector-advanced/10.png)

예제 7-1 실행결과

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석