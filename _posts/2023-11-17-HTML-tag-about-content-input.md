---
title: "[HTML] 웹 문서 내 컨텐츠 입력 관련 태그들"
category: "HTML"
tag: ["web", "tags", "HTML"]
---

이 문서에서는 웹 페이지에 텍스트, 이미지, 영상 등 다양한 종류의 정보들을 삽입하기 위해 필요한 태그들에 대해 살펴보겠다. 

# \<blockquote>

닫는 태그 존재 여부: 예

웹 문서 내에서 인용문을 쓰고자 할 때 사용하는 태그. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>오늘의 일기</h1>
    <p>오늘은 춥지만 매우 맑고 창창합니다.</p>
    <p>조금 있다가 낮이 되면 지금보다 더 따뜻해질 것 같습니다.</p>
    <p>며칠 전 제 일기에는 이렇게 언급되어있군요.</p>
    <blockquote>
        오늘은 하루종일 비가 오고 내내 추울 것 같네요.
        바람도 세게 분다고 하니 나갈 때는 장우산을 챙기고 
        옷도 든든하게 입는 게 좋겠네요.
    </blockquote>
    <p>
        갈수록 겨울로 진입하고 있지만 지금만 봐서는 오히려 시간이 지날수록 
        따듯해지는 것 같기도 합니다.
    </p>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/1.png)

예제 1-1 실행결과

해당 태그를 사용하면 태그 안 글들이 인용문으로 처리되어 들여쓰기가 되어져 해당 구문이 인용문임을 강조한다. 

# \<strong>, \<b>

닫는 태그 존재 여부: 둘 다 존재

두 태그 모두 태그 안에 감싸여진 텍스트를 진하게 표시함으로써 해당 텍스트를 강조하는 태그이다. 두 태그의 차이점은, \<b> 태그는 단순히 텍스트를 강조하는 태그이지만, \<strong> 태그 안의 텍스트는 시각장애인을 위한 화면 낭독기가 해당 텍스트를 강조하여 음성으로 읽어주도록 한다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>오늘의 일기</h1>
    <p>오늘은 춥지만 매우 맑고 창창합니다.</p>
    <p>조금 있다가 낮이 되면 지금보다 더 따뜻해질 것 같습니다.</p>
    <p>며칠 전 제 일기에는 이렇게 언급되어있군요.</p>
    <blockquote>
        오늘은 하루종일 <b>비</b>가 오고 내내 추울 것 같네요.
        바람도 세게 분다고 하니 나갈 때는 <strong>장우산</strong>을 챙기고 
        옷도 든든하게 입는 게 좋겠네요.
    </blockquote>
    <p>
        갈수록 겨울로 진입하고 있지만 지금만 봐서는 오히려 시간이 지날수록 
        따듯해지는 것 같기도 합니다.
    </p>
</body>
</html>
```

![예제 2-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/2.png)

예제 2-1 실행결과

# \<em>, \<i>

닫는 태그 존재 여부: 둘 다 존재

두 태그 모두 해당 태그 안에 감싸여진 텍스트에 기울인꼴 스타일을 추가해준다. 

\<em>은 문장 중 더 강조하고픈 부분에 사용하고, \<i>는 단순히 다른 텍스트와 구별하기 위해 사용된다고 한다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>오늘의 일기</h1>
    <p>오늘은 춥지만 매우 <em>맑고</em> 창창합니다.</p>
    <p>조금 있다가 낮이 되면 지금보다 더 따뜻해질 것 같습니다.</p>
    <p>며칠 전 제 <i>일기</i>에는 이렇게 언급되어있군요.</p>
    <blockquote>
        오늘은 하루종일 <b>비</b>가 오고 내내 추울 것 같네요.
        바람도 세게 분다고 하니 나갈 때는 <strong>장우산</strong>을 챙기고 
        옷도 든든하게 입는 게 좋겠네요.
    </blockquote>
    <p>
        갈수록 겨울로 진입하고 있지만 지금만 봐서는 오히려 시간이 지날수록 
        따듯해지는 것 같기도 합니다.
    </p>
</body>
</html>
```

![예제 3-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/3.png)

예제 3-1 실행결과

# \<abbr>

닫는 태그 존재 여부: 예

줄임말을 표시하고자 할 때 쓰이는 태그로, 해당 태그 속성 title에 텍스트를 입력하면 웹브라우저에서 해당 태그가 적용된 텍스트 위로 마우스를 가져다 대면 말풍선으로 뜨게 된다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>오늘의 일기</h1>
    <abbr title="Today's Diary">TD</abbr>
    <p>오늘은 춥지만 매우 <em>맑고</em> 창창합니다.</p>
    <p>조금 있다가 낮이 되면 지금보다 더 따뜻해질 것 같습니다.</p>
    <p>며칠 전 제 <i>일기</i>에는 이렇게 언급되어있군요.</p>
    <blockquote>
        오늘은 하루종일 <b>비</b>가 오고 내내 추울 것 같네요.
        바람도 세게 분다고 하니 나갈 때는 <strong>장우산</strong>을 챙기고 
        옷도 든든하게 입는 게 좋겠네요.
    </blockquote>
    <p>
        갈수록 겨울로 진입하고 있지만 지금만 봐서는 오히려 시간이 지날수록 
        따듯해지는 것 같기도 합니다.
    </p>
</body>
</html>
```

![예제 4-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/4.png)

예제 4-1 실행결과

# \<cite>

닫는 태그 존재 여부: 예

다른 곳에서 참고한 내용을 적고자 할 때 해당 태그를 사용할 수 있다. 해당 태그를 사용하면 그 태그로 감싸진 텍스트는 기울어진 꼴이 된다. 

```html
<p>
    얕은 사람은 행운을 믿으며, 강한 사람은 원인과 결과를 믿는다.<br>
    (Shallow men believe in luck, Strong men believe in cause and effect)<br>
    <cite>- 랄프 왈도 에머슨 (Ralph Waldo Emerson)</cite>
</p>
```

![예제 5-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/5.png)

예제 5-1 실행결과

# \<code>

닫는 태그 존재 여부: 예

프로그래밍 언어로 작성한 소스 코드를 보여주고자 할 때 사용할 수 있는 태그이다. 

```html
<code>
    def hello_world():<br>
        &nbsp;&nbsp;&nbsp;&nbsp;print('hello_world!')<br>
        &nbsp;&nbsp;&nbsp;&nbsp;return None<br>
</code>
```

![예제 6-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/6.png)

예제 6-1 실행결과

참고로, 웹 상에서 띄어쓰기를 적용하고자 한다면 \&nbsp;를 사용할 수 있다. 해당 키워드는 웹 상에서 공백 한 칸을 표시한다. 위 예제에서는 탭을 구현하기 위해 공백 4칸을 적용한 모습이다. 

# \<small>

닫는 태그 존재 여부: 예

텍스트를 작게 표시해준다. 별로 중요한 말이 아니거나 다른 텍스트에 비해 작게 표시하고자 할 때 사용할 수 있다. 

```html
<p>
    2023년 11월 22일 <small>(춥지만 맑음)</small>
</p>
```

![예제 7-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/7.png)

예제 7-1 실행결과

# \<sup>, \<sub>

닫는 태그 존재 여부: 둘 다 존재

위 또는 아래 첨자 기능의 태그이다. 

```html
<p>E=mc<sup>2</sup></p>
<p>물의 화학식은 H<sub>2</sub>O</p>
```

![예제 8-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/8.png)

예제 8-1 실행결과

# \<s>, \<del>

닫는 태그 존재 여부: 둘 다 존재

둘 다 웹 브라우저 상에서는 텍스트 위에 취소선이 그어진 모습을 띤다. 그러나 의미상으로 다르다. \<s>는 더 이상 정확하거나 관련 없는 정보를 지우고자할 때 사용하고, \<del>은 문서 상에서 삭제하고자 하는 글에 적용하고자 할 때 사용한다. 
이에 대한 자세한 정보는 다음을 참고.

[What is the difference between \<s> and \<del> in HTML, and do they affect website rankings?](https://stackoverflow.com/questions/16743581/what-is-the-difference-between-s-and-del-in-html-and-do-they-affect-website)

```html
<p>HTML은 <s>프로그래밍</s> 마크업 언어입니다.</p>
<p>HTML은 <del>프로그래밍</del> 마크업 언어입니다.</p>
```

![예제 9-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/9.png)

예제 9-1 실행결과

# \<u>, \<ins>

닫는 태그 존재 여부: 둘 다 존재

두 태그 모두 웹 브라우저 상에서는 텍스트에 밑줄이 쳐진 모습으로 나온다. 그러나 앞서 \<s> 태그와 \<del> 태그의 차이점에서 보았듯, \<u> 태그는 단순한 밑줄 기능을, \<ins>는 문서에 새로운 내용을 추가하고자 할 때 해당 내용에 밑줄을 긋는 의미이다. 

```html
<p>
    <u>프론트엔드</u> 분야에는 HTML, CSS <ins>, 그리고 JavaScript</ins>등의 
    언어들이 존재합니다.
</p>
```

![예제 10-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/10.png)

예제 10-1 실행결과

# \<ol>, \<li>

닫는 태그 존재 여부: 예

\<ol>은 순서 있는 목록(ordered list)을 만드는 태그이다. 즉, 순서대로 숫자가 부여된 항목들을 목록 형태로 보여준다. 각 항목에 해당하는 텍스트들은 \<ol> 태그 내에서 \<li> 태그를 이용한다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>졸음을 쫓는 방법</h1>
    <p>
        <ol type="I">
            <li>
                물 마시기: 물을 마시면 몸이 잠시 활성화되어 졸음을 깰 수 있습니다. 
                또한, 데우지 않은 차가운 물을 마시면 더욱 효과적입니다.
            </li>
            <li>
                운동하기: 간단한 스트레칭이나 점프 등의 운동을 하면 혈류가 촉진되어 잠을 깰 수 있습니다.
            </li>
            <li>
                신선한 공기를 마시기: 창문을 열어 신선한 공기를 마시거나, 
                잠시 외출해서 산소를 충분히 섭취하는 것도 졸음을 깨는 데 도움이 됩니다.
            </li>
        </ol>
        <ol type="A" start="4">
            <li>
                균형 잡힌 식사: 단백질과 복합 탄수화물을 함께 섭취하면 에너지가 천천히 방출되어 졸음을 덜게 할 수 있습니다.
            </li>
        </ol>
    </p>
</body>
</html>
```

![예제 11-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/11.png)

예제 11-1 실행결과

\<ol> 태그에는 type과 start 속성을 사용할 수 있다. 

type 속성에 쓰일 수 있는 속성값들.

| type = “1” | 숫자(기본값) |
| --- | --- |
| type = “a” | 영어 소문자 |
| type = “A” | 영어 대문자 |
| type = “i” | 로마 숫자 소문자 |
| type = “I” | 로마 숫자 대문자 |

start 속성에는 start=”3”과 같이 숫자값을 사용할 수 있으며, 해당 숫자부터 시작하여 각 항목에 숫자를 매긴다. 

# \<ul>

닫는 태그 존재 여부: 예

\<ol>과 달리, 순서 없는 목록(unordered list)을 사용할 때 쓰이는 태그이다. 앞선 예제 11-1를 순서 없는 목록으로 바꾸면 다음과 같다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>졸음을 쫓는 방법</h1>
    <p>
        <ul>
            <li>
                물 마시기: 물을 마시면 몸이 잠시 활성화되어 졸음을 깰 수 있습니다. 
                또한, 데우지 않은 차가운 물을 마시면 더욱 효과적입니다.
            </li>
            <li>
                운동하기: 간단한 스트레칭이나 점프 등의 운동을 하면 혈류가 촉진되어 잠을 깰 수 있습니다.
            </li>
            <li>
                신선한 공기를 마시기: 창문을 열어 신선한 공기를 마시거나, 
                잠시 외출해서 산소를 충분히 섭취하는 것도 졸음을 깨는 데 도움이 됩니다.
            </li>
        </ul>
        <ul>
            <li>
                균형 잡힌 식사: 단백질과 복합 탄수화물을 함께 섭취하면 에너지가 천천히 방출되어 졸음을 덜게 할 수 있습니다.
            </li>
        </ul>
    </p>
</body>
</html>
```

![에제 12-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/12.png)

에제 12-1 실행결과

참고로, 위 실행결과에서 각 항목 앞에 그려진 원과 같은 그림을 불렛(bullet)이라고 부른다. 

# \<dl>, \<dt>, \<dd>

닫는 태그 존재 여부: 셋 다 존재

\<dl>은 설명 목록(description list)을 생성하는 태그이다. 설명 목록은 이름(name)과 값(value)으로 구성되어 있으며, 마치 사전과 같은 형태로 서로 연결되어 있다. 이름을 지정할 때에는 \<dt> 태그 안에, 해당 이름에 대한 값을 지정할 때에는 \<dd> 태그 안에 텍스트를 넣으면 된다. 이름 태그인 \<dt>가 먼저 나오고 그 아래에 \<dd> 태그가 나오는 형식이다. 하나의 이름 태그 \<dt>에는 여러 개의 \<dd> 태그가 뒤따라올 수도 있다. 

```html
<dl>
    <dt>name</dt>
    <dd>value<dd>
</dl>
```

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>이번 주 할 일</h1>
    <dl>
        <dt>월요일</dt>
        <dd>일주일동안 먹을 반찬 만들기</dd>
        <dd>생필품 구매</dd>
        <dd>파이썬 개인 프로젝트하기</dd>
    </dl>
    <dl>
        <dt>화요일</dt>
        <dd>운동</dd>
        <dd>코딩 공부</dd>
    </dl>
</body>
</html>
```

![예제 13-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/13.png)

예제 13-1 실행결과

직접 실험한 결과, 하나의 \<dl> 태그 안에 여러 개의 \<dt>들을 사용할 수 있다. 하지만 각 항목들의 명확한 분리를 위해서라면 위 예제 13-1처럼 각 \<dl> 태그를 따로 만들어 구분하는 것이 좋을 것이다. 

# 표 관련 태그들

## \<table>, \<caption>, \<tr>, \<td>, \<th>

닫는 태그 존재 여부 : 모두 존재

해당 태그들은 모두 표를 만드는데 사용되는 태그들이다. 

표를 정의하는 태그는 \<table>이며, 표와 관련된 모든 태그, 내용들은 해당 태그 안에서 작성된다. 

표의 행을 정의하는 태그는 \<tr>이며, 여러 개를 사용할 수 있다. 이 경우 여러 개의 행들이 생성된다.  

\<tr> 태그 안에는 \<td> 태그 또는 \<th> 태그가 쓰일 수 있다. \<td>태그는 \<tr>태그로 생성된 행 안에 셀을 형성한다. 여러 개의 \<td> 태그가 쓰이면 그 만큼의 열이 해당 행에 추가되는 셈이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>표 예제</h1>
    <table>
        <caption>표 좌표 (행, 열)</caption>
        <tr>
            <th>1열</th>
            <th>2열</th>
            <th>3열</th>
        </tr>
        <tr>
            <td>(1, 1)</td>
            <td>(1, 2)</td>
            <td>(1, 3)</td>
        </tr>
        <tr>
            <td>(2, 1)</td>
            <td>(2, 2)</td>
            <td>(2, 3)</td>
        </tr>
        <tr>
            <td>(3, 1)</td>
            <td>(3, 2)</td>
            <td>(3, 3)</td>
        </tr>
    </table>
</body>
</html>
```

![예제 14-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/14.png)

예제 14-1 실행결과

(위 태그들로는 표의 구분선이 생기진 않는 걸로 확인되었다)

\<th> 태그는 \<td>와 비슷하나 표의 제목 행에 사용되어 해당 행에 있는 각 셀의 글자들을 강조하여 해당 행이 제목 행임을 표시하는 용도로 사용된다. 

\<caption> 태그는 해당 표의 위 중앙에 표의 제목을 붙이는 용도의 태그이다. 

## \<td>, \<th> 태그에 사용될 수 있는 속성들

여러 연속된 행이나 열들의 셀들을 하나로 합칠 수 있는 속성으로 rowspan과 colspan이 있다. rowspan은 여러 개의 연속된 행들끼리, colspan은 여러 개의 연속된 열들끼리 합칠 수 있다. rowspan=”2”와 같이 쓰면 해당 속성이 쓰인 셀로부터 해당 셀의 행을 포함하여 다음 한 행까지의 셀을 합친다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>표 예제</h1>
    <table>
        <caption>향후 구매 목록</caption>
        <thead>
            <tr>
                <th>종류</th>
                <th>제품명</th>
                <th>가격</th>
                <th>수량</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td rowspan="2">오프라인</td>
                <td>라이스페이퍼</td>
                <td>3000</td>
                <td>1</td>
            </tr>
            <tr>
                <td>음료</td>
                <td>1000</td>
                <td>2</td>
            </tr>
            <tr>
                <td rowspan="2">온라인</td>
                <td>닭가슴살</td>
                <td>1580</td>
                <td>15</td>
            </tr>
            <tr>
                <td>바디워시</td>
                <td>15000</td>
                <td>1</td>
            </tr>
        </tbody>
    </table>
</body>
</html>
```

![예제 14-2 실행결과](/images/2023-11-17/HTML-tag-about-content-input/15.png)

예제 14-2 실행결과

위 예제 14-2의 코드를 살펴보면, “오프라인” 셀의 \<td> 태그의 rowspan=”2”를 대입하였다. 해당 셀이 해당 행의 첫 번째 열이므로, 다음 행의 첫 번째 열은 생략한다. 그래야 두 행의 첫 번째 열의 두 셀이 하나로 합쳐질 수 있기 때문이다. 이런 식으로 행을 합쳐 사용하면 된다. 

다음은 colspan 속성을 이용하여 위 예제 14-2에서 만든 표의 행과 열을 바꾼 예제이다.

```html
(생략)
<table>
        <caption>향후 구매 목록2</caption>
        <thead>
            <tr>
                <th></th>
                <th colspan="2">오프라인</th>
                <th colspan="2">온라인</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>제품명</td>
                <td>라이스페이퍼</td>
                <td>음료</td>
                <td>닭가슴살</td>
                <td>바디워시</td>
            </tr>
            <tr>
                <td>가격</td>
                <td>3000</td>
                <td>1000</td>
                <td>1580</td>
                <td>15000</td>
            </tr>
            <tr>
                <td>수량</td>
                <td>1</td>
                <td>2</td>
                <td>15</td>
                <td>1</td>
            </tr>
        </tbody>
    </table>
(생략)
```

![예제 14-3 실행결과](/images/2023-11-17/HTML-tag-about-content-input/16.png)

예제 14-3 실행결과

## \<thead>, \<tbody>, \<tfoot>

닫는 태그 존재 여부: 모두 존재

해당 태그들은 표의 구조를 정의하는 태그들이다. 차례대로 \<thead>는 표의 제목 부분을, \<tbody>는 표의 본문을, \<tfoot>은 표의 요약 부분을 나타내며, 각각 표의 위, 중간, 아래 부분에 위치한다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>표 예제</h1>
    <table>
        <caption>표 좌표 (행, 열)</caption>
        <thead>
            <tr>
                <th>제품명</th>
                <th>가격</th>
                <th>수량</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>책</td>
                <td>15000</td>
                <td>1</td>
            </tr>
            <tr>
                <td>닭가슴살</td>
                <td>1580</td>
                <td>10</td>
            </tr>
            <tr>
                <td>옷</td>
                <td>30000</td>
                <td>2</td>
            </tr>
        </tbody>
        <tfoot>
            <td>총합</td>
            <td>60,800</td>
        </tfoot>
    </table>
</body>
</html>
```

![예제 14-4 실행결과](/images/2023-11-17/HTML-tag-about-content-input/17.png)

예제 14-4 실행결과

표의 구조를 정의하는 해당 태그들을 사용하면 웹 상에서는 표현이 되지 않는 것처럼 보이지만, 시각 장애인이 화면 낭독기를 통해 표의 구조와 내용을 쉽게 이해하게 할 수 있다. 또한, CSS를 사용하여 표의 제목, 본문, 요약 부분에 각자 다른 스타일을 지정하여 꾸밀 수도 있다. 

## \<colgroup>, \<col>

닫는 태그 존재 여부: \<colgroup> - 예, \<col> - 아니오.

해당 태그들은 표의 특정 열들에 각각의 스타일을 지정하고자할 때 사용하는 태그이다. 하나의 열에만 지정하고자 한다면 \<col> 태그를, 둘 이상의 \<col> 태그들을 하나로 묶고자 할 때 \<colgroup> 태그를 사용한다. 즉, \<colgroup> 태그 안에 \<col> 태그를 사용한다. 

\<colgroup> 안에 \<col> 태그 사용 시 표의 열 개수만큼의 \<col> 태그를 사용해야 한다. 만약 특정 열에 부여할 스타일이 없다면 그저 \<col> 태그만 작성한다. 특정 열에 스타일을 부여하고자 한다면 \<col style=””>처럼 \<col> 태그 안에 style 속성에 CSS를 사용할 수 있다. 

해당 태그들은 \<table> 태그 안의 \<caption> 바로 아래, 표가 시작하는 부분의 위 부분에 작성한다. 

다음은 해당 태그들의 사용 예이다.

```html
(생략)
<table>
        <caption>향후 구매 목록</caption>
        <colgroup>
            <col style="background-color: bisque;">
            <col>
            <col style="width:100px;background-color: blueviolet;">
            <col style="width:100px;background-color: blueviolet;">
        </colgroup>
        <thead>
            <tr>
                <th>종류</th>
                <th>제품명</th>
                <th>가격</th>
                <th>수량</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td rowspan="2">오프라인</td>
                <td>라이스페이퍼</td>
                <td>3000</td>
                <td>1</td>
            </tr>
            <tr>
                <td>음료</td>
                <td>1000</td>
                <td>2</td>
            </tr>
            <tr>
                <td rowspan="2">온라인</td>
                <td>닭가슴살</td>
                <td>1580</td>
                <td>15</td>
            </tr>
            <tr>
                <td>바디워시</td>
                <td>15000</td>
                <td>1</td>
            </tr>
        </tbody>
    </table>
(생략)
```

![예제 14-5 실행결과](/images/2023-11-17/HTML-tag-about-content-input/18.png)

예제 14-5 실행결과

# \<img>

닫는 태그 존재 여부: 아니오

이미지를 삽입하는 태그이다. 다음은 해당 태그에 쓰이는 여러 가지 속성들이다. 

- src

이미지 파일 경로를 대입한다. 웹 상에서 이미지를 보여주기 위해 필수로 설정해야한다. 이미지 파일 경로를 상대경로로 대입 시 해당 HTML 문서를 기준으로 이미지 파일 경로를 입력해야한다. 예를 들어, spaceman.png 이름의 이미지 파일과 web.html 문서 파일이 같은 디렉토리 안에 있다면 src=”spaceman.png”와 같이 이미지 파일명만 입력하면 된다. 그러나 예를 들어 해당 html 파일이 들어있는 디렉토리의 하위 디렉토리 images 안에 해당 이미지 파일이 있는 경우, src=”images/spaceman.png”와 같이 작성해줘야 한다. 

물론, 이미지 파일 경로를 절대경로로 대입해도 작동된다. 

다음은 상대경로로 이미지 파일을 html 문서 안에 삽입하는 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>이미지 삽입하기</h1>
    <img src="images/spaceman.png">
</body>
</html>
```

![예제 15-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/19.png)

예제 15-1 실행결과

참고로, 내 로컬 컴퓨터 내 이미지 파일뿐만 아니라 인터넷 상에서의 이미지 주소를 복사하여 사용할 수도 있다. 

- alt

이미지가 어떠한 이유로 웹 상에서 뜨지 않을 때, 해당 이미지에 대한 설명을 텍스트로 대신 해줄 수 있는 속성이 alt이다. 

다음은 이미지가 안 뜰 경우 alt 속성값에 설정된 메시지가 웹 상에서 뜨는지를 확인하기 위해 src 속성값으로 설정할 이미지 파일 경로를 일부러 틀리게 한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>이미지 삽입하기</h1>
    <img src="aceman.png" alt="앉아서 커피 한잔 하는 우주인">
</body>
</html>
```

![예제 15-2 실행결과](/images/2023-11-17/HTML-tag-about-content-input/20.png)

예제 15-2 실행결과

alt 속성은 보통 이미지가 웹 상에서 뜨지 않았을 때 텍스트로 부연 설명할 때 쓰이나, 만약 이미지가 웹 상의 특정 버튼, 메뉴, 로고 등에 적용되어 텍스트 대신 사용하는 경우, 이미지 자체를 설명하기 보다는 해당 이미지에 연결된 기능에 대해 설명하는 것이 좋다. 예를 들어, 메뉴에 이미지를 적용한 경우, alt=”사이트맵, 카테고리” 등 원래 메뉴 안에 있는 목록들을 설명하는 것이 좋을 것이다. 

- width, height

각각 이미지의 너비, 높이를 지정하는 속성이다. 두 속성 중 하나만 지정해도 나머지 하나의 속성값은 자동으로 계산되어 지정된다. 예를 들어 width 값만 지정한 경우, height 값도 자동으로 지정된다. 

해당 속성을 사용하면 이미지 원본 사이즈에는 영향을 주지 않고, 오로지 웹 상에서 보이는 이미지의 크기만을 조정한다. 

두 속성에는 숫자값을 지정하는데, 두 가지 방법이 있다. 하나는 숫자 뒤에 ‘%’ 기호를 붙이는 것으로, 예를 들어 width=”50%”라 지정하면 현재 웹 브라우저 창의 크기의 50% 크기의 너비로 지정된다. 그래서 웹 브라우저 창의 크기를 자유자재로 조절하면 그에 따라 이미지의 크기도 width=’”50%”에 맞추기 위해 자동으로 변경된다. 참고로 원본 이미지의 50%가 아닌, 웹브라우저 창의 50% 크기로 조절되는 것이니 헷갈리지 않도록 한다. 또 하나는 px(픽셀) 단위로, 픽셀 단위로 이미지의 크기를 지정한다. 이 경우, 웹 브라우저 창의 크기에 상관없이 고정된 크기를 차지한다. width=”50”이라 지정해도 되고, width=”50px”로 해도 잘 작동된다. 

다음은 위 내용을 반영한 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>img 태그의 alt 속성 사용 시</h1>
    <img src="ceman.png", alt="앉아있는 우주인">
    <h1>사진 크기 조절</h1>
    <h2>원래 크기의 사진</h2>
    <img src="images/spaceman.png">
    <h2>현재 웹 브라우저 창 크기의 50%으로 줄인 사진</h2>
    <img src="images/spaceman.png" width="50%">
    <h2>100px 크기로 고정 시킨 사진</h2>
    <img src="images/spaceman.png" width="100px">
</body>
</html>
```

![예제 16-1 실행결과의 일부 모습](/images/2023-11-17/HTML-tag-about-content-input/21.png)

예제 16-1 실행결과의 일부 모습

# 멀티미디어 관련 태그들

## \<object>

닫는 태그 존재 여부: 예

오디오, 비디오, 각종 파일들을 웹 문서에 삽입하여 보여줄 수 있는 태그이다. 

```html
<object data="파일경로" width="너비값" height="높이값"></object>
```

다음은 pdf파일을 웹 문서에 삽입하는 코드 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>여러 가지 멀티미디어 삽입</h1>
    <p>
        <h2>object 태그를 이용하여 pdf 파일 삽입</h2>
        <object data="files/2023_신년운세.pdf" width="100%" height="800"></object>
    </p>
</body>
</html>
```

![예제 17-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/22.png)

예제 17-1 실행결과

## \<embed>

닫는 태그 존재 여부: 아니오

\<object> 태그와 마찬가지로 여러 멀티 미디어 파일을 삽입할 수 있는 태그이다. HTML 초기 버전부터 지원한 태그라고 한다. 그래서 대부분의 웹 브라우저에서도 지원이 된다고 한다. 그래서 \<audio>, \<object>, \<video> 태그 등이 지원되지 않는 웹 브라우저에 대해서는 \<embed> 태그를 대신 사용하면 된다. 

```html
<embed src="파일 경로", width="너비값", height="높이값">
```

다음은 해당 태그를 이용한 코드 예제이다. 

```html
<p>
    <embed src="files/drive-breakbeat-173062.mp3">
    <br>
    <small>pixabay 무료 음악</small>
</p>
```

![예제 17-2 실행결과](/images/2023-11-17/HTML-tag-about-content-input/23.png)

예제 17-2 실행결과

## \<audio>, \<video>

닫는 태그 존재 여부: 둘 다 존재

\<audio>는 오디오 파일을, \<video>는 비디오 파일을 웹 문서에 삽입하는 태그이다. 둘 다 파일 경로 지정을 src 속성값을 이용하여 지정하며, \<video> 태그의 경우, 웹 상에서의 비디오 크기를 width, height 속성으로 지정할 수 있다고 한다. 

다음은 오디오와 비디오를 해당 태그들을 이용하여 웹 문서에 삽입하는 코드 예제이다.

```html
<h2>audio, video 태그를 이용한 오디오, 비디오 삽입</h2>
<p>
    <audio src="files/drive-breakbeat-173062.mp3" controls></audio>
    <video src="files/testvideo.mp4" width="80%" controls></video>
</p>
```

![예제 17-3 실행결과](/images/2023-11-17/HTML-tag-about-content-input/24.png)

예제 17-3 실행결과

다음은 두 태그에서 공통적으로 사용하거나 둘 중 하나에만 사용되는 속성들의 표이다.

| 속성 | 설명 | 사용할 수 있는 태그(\<audio>, \<video>) |
| --- | --- | --- |
| controls | 플레이어 화면에 사용자가 조정할 수 있는 컨트롤 바를 삽입한다. 속성값없이 해당 속성만을 작성하면 된다. (실험 결과, 해당 속성을 작성하지 않으면 아예 웹 상에서 보이지 않거나 사용자가 플레이 시킬 수 없으므로 사실상 필수로 넣어야 하는 속성이다) | 둘 다 |
| autoplay | 자동 실행. 속성값없이 해당 속성만을 작성하면 된다. (실험 결과, 크롬에서는 해당 속성을 지정해도 자동으로 플레이가 되지 않는 것을 확인함) | 둘 다 |
| loop | 반복 재생. 속성값 없이 해당 속성만을 작성하면 된다. | 둘 다 |
| muted | 음소거. 속성값없이 해당 속성만을 작성하면 된다. | 둘 다 |
| width, height | 비디오 화면 크기 조정. 둘 중 하나만 작성하면 나머지 크기는 자동으로 계산되어 보여짐 | \<video> |
| poster=”이미지 파일 경로” | 비디오 재생 전 화면에 표시될 이미지 지정. 썸네일이라 보면 되겠다. | \<video> |

autoplay를 이용한 자동 재생은 대부분의 웹브라우저에서 금지하고 있다고 한다. 

# \<a>

닫는 태그 존재 여부: 예

텍스트 또는 이미지에 외부 사이트의 링크를 달아주는 기능의 태그이다. \<a> 태그를 통해 링크가 달린 텍스트 또는 이미지를 클릭하면 해당 링크의 사이트로 이동된다. 

```html
<a href="링크 주소">텍스트 또는 이미지</a>
```

이미지의 경우, \<a> 태그 안에 \<img> 태그를 삽입하면 해당 이미지에 링크가 달린다. 다음은 텍스트와 이미지에 링크를 다는 코드 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>하이퍼링크 삽입</h1>
    <h2>텍스트에 링크 삽입</h2>
    <p>
        <a href="https://developer.mozilla.org/ko/docs/Web/HTML">HTML docs</a>
    </p>
    <h2>이미지에 링크 삽입</h2>
    <p>
        <a href="https://developer.mozilla.org/ko/docs/Web/HTML">
            <img src="images/spaceman.png" width="70%">
        </a>
    </p>
</body>
</html>
```

![예제 18-1 실행결과](/images/2023-11-17/HTML-tag-about-content-input/25.png)

예제 18-1 실행결과

위 코드를 실행시켰을 때 링크가 달린 텍스트 또는 이미지를 클릭하면 해당 창에서 해당 사이트로 이동되는데, 만약 새로운 창에서 해당 사이트가 열렸으면 좋겠을 때에는 \<a> 태그 안에 target=”_black” 속성을 사용하면 된다.

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석