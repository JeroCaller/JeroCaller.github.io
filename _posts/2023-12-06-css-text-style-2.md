---
title: "[CSS] 텍스트 스타일 2"
category: "CSS"
tag: ["web", "css", "style", "text", "text style"]
---
# 목록 스타일

\<ul>, \<ol> 태그를 이용하여 구현하는 목록에 대해서도 관련 스타일을 지정할 수 있는 몇몇 스타일 속성들이 있다. 

## list-style-type

순서 없는 목록에서, 각 목록 요소 앞에 오는 문양을 불릿(bullet)이라 한다. 이러한 불릿의 모양과 더불어 순서 있는 목록에서 각 목록 요소마다 부여되는 번호에도 스타일을 지정할 수 있다. 해당 속성으로 지정할 수 있는 불릿 스타일은 다음의 예제 코드에서 확인할 수 있다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <ul style="list-style-type: disc;">
        <li>Full circle</li>
    </ul>
    <ul style="list-style-type: circle;">
        <li>Emtpy circle</li>
    </ul>
    <ul style="list-style-type: square;">
        <li>Full square</li>
    </ul>
    <ol style="list-style-type: decimal;">
        <li>Normal number</li>
    </ol>
    <ol style="list-style-type: decimal-leading-zero;">
        <li>Decimal-leading-zero</li>
    </ol>
    <ol style="list-style-type: lower-roman;">
        <li>Lower roman</li>
    </ol>
    <ol style="list-style-type: upper-roman;">
        <li>Upper roman</li>
    </ol>
    <ol style="list-style-type: lower-alpha;">
        <li>Lower alphabet</li>
    </ol>
    <ol style="list-style-type: upper-alpha;">
        <li>Upper alphabet</li>
    </ol>
    <ol style="list-style-type: none;">
        <li>No style</li>
    </ol>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-12-06/css-text-style-2/1.png)

예제 1-1 실행결과

## list-style-image

불릿의 유형은 한정되어 있어서 만약 마음에 안드는 경우, 원하는 이미지로 대신할 수 있다. 이 때, 이미지의 크기가 불릿을 대체할 정도로 작은 것이 좋다. 해당 속성의 값으로 이미지 파일 경로를 입력하거나, 이미지를 원치 않을 경우 none을 입력한다. 

## list-style-position

해당 속성은 목록 전체를 들여 쓸 수 있는 속성이다. 해당 속성의 가능한 값으로 inside와 outside가 있으며, 들여쓰기 위한 속성값은 inside이다. 반면 기본값은 outside로 지정되어 있으며, 해당 속성값은 목록을 들여쓰지 않을 때 쓰는 속성값이다. 다음은 해당 속성을 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <div>
        <ul style="list-style-position: outside;">
            <li>하나</li>
            <li>둘</li>
            <li>셋</li>
        </ul>
    </div>
    <div>
        <ul style="list-style-position: inside;">
            <li>하나</li>
            <li>둘</li>
            <li>셋</li>
        </ul>
    </div>
</body>
</html>
```

![예제 1-2 실행결과](/images/2023-12-06/css-text-style-2/2.png)

예제 1-2 실행결과

## list-style

지금까지 소개한 목록 스타일과 이외의 목록 스타일들을 한 줄의 속성값으로 작성하고자 한다면 list-style 속성을 이용한다. 해당 속성값으로 각각의 속성들을 공백으로 구분하여 대입하면 된다. (순서는 상관 없을 것이다)

# 표 스타일

\<table> 태그를 통해 생성한 표에도 여러가지 css 속성을 적용시켜 표 스타일을 변경시킬 수 있다. 

## caption-side

해당 속성은 캡션의 위치를 설정하는 속성으로, 표의 위쪽에 캡션을 두려면 top을, 아래쪽에 두려면 bottom을 해당 속성값으로 대입하면 된다. 기본값은 top이다. 다음은 이를 적용한 표 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .col1 {
            background-color: bisque;
        }
        .right-col {
            background-color: blueviolet;
            width: 100px;
        }
        table {
            caption-side: bottom;
        }
    </style>
</head>
<body>
    <h1>표 예제</h1>
    <table>
        <caption>향후 구매 목록</caption>
        <colgroup>
            <col class="col1">
            <col>
            <col class="right-col">
            <col class="right-col">
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
</body>
</html>
```

![에제 2-1 실행결과](/images/2023-12-06/css-text-style-2/3.png)

에제 2-1 실행결과

## border

표에도 테두리를 그릴 수 있는 속성인 border 속성이 있다. 해당 속성은 표 바깥 테두리를 그릴려면 \<table> 태그에, 셀 테두리를 지정하려면 \<td> 또는 \<th> 태그에 해당 속성을 지정하면 된다. (\<tr> 태그에는 적용되지 않는다고 한다)

다음은 예제 2-1의 \<style> 요소에서 표 바깥 테두리와 셀 테두리를 지정한 코드 일부이다.

```css
/* 예제 2-1의 <style> 태그 안에 다음을 추가 작성. */
table {
    caption-side: bottom;
    border: 2px solid black;
}
td, th {
    border: 1px solid black;
    padding: 5px;
}
```

![예제 2-2 실행결과](/images/2023-12-06/css-text-style-2/4.png)

예제 2-2 실행결과

## border-spacing

해당 속성은 셀 간 여백을 지정할 때 사용하는 속성이다. \<table> 태그에 적용하여 사용하며, 수평 여백과 수직 여백을 똑같이 지정하고자 한다면 하나의 값만을, 두 여백을 다르게 지정하고자 한다면 공백으로 구분하여 두 값을 대입하면 된다. 참고로 해당 속성은 적용하고자 하는 표의 속성 border-collapse 값이 separate일 때만 적용 가능하다. (border-collapse 속성의 기본값은 separate로 지정되어 있다)

다음은 예제 2-2의 표에서 셀 간 여백을 해당 속성을 이용하여 지정한 일부 코드와 그 결과이다.

```html
<style>
  /* 예제 2-1의 <style> 태그 내부 */
	/* 생략 */
    table {
        caption-side: bottom;
        border: 2px solid black;
        border-spacing: 5px; /* 추가된 속성 */
    }
  /* 생략 */
</style>
```

![예제 2-3 실행결과](/images/2023-12-06/css-text-style-2/5.png)

예제 2-3 실행결과

## border-collapse

\<table>, \<td>, \<th> 태그에 border 속성을 적용하여 테두리를 표시했을 때, 각 셀 간 여백이 생기면서 각 셀마다 테두리가 형성된다. 이 때 한 셀과 다른 셀 사이의 테두리 선이 두 줄로 보인다(예제 2-2, 2-3의 실행결과 참조). border-collapse 속성은 이 두 줄로 보이는 셀 테두리를 하나로 합치는 데에 쓰일 수 있는 속성이다. 해당 속성은 두 개의 가능한 값을 가질 수 있는 데, 두 줄로 보이는 셀 테두리를 하나로 합치고자 할 때에는 collapse를, 셀 테두리를 두 줄로 보이게 할 때에는 separate를 속성값으로 대입하면 된다. separate가 기본값으로 설정되어 있으며, 해당 속성은 \<table> 태그에 적용하면 된다. 다음은 예제 2-3에서 border-spacing 속성은 지우고 대신 `border-collapse: collapse` 속성을 추가하여 셀 간 테두리를 한 줄로 합친 예제 코드와 그 실행결과이다.

```html
<style>
  /* 예제 2-1의 <style> 태그 내부 */
	/* 생략 */
    table {
        caption-side: bottom;
        border: 2px solid black;
        border-collapse: collapse; /* 바뀐 속성 */
    }
  /* 생략 */
</style>
```

![예제 2-4 실행결과](/images/2023-12-06/css-text-style-2/6.png)

예제 2-4 실행결과

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석