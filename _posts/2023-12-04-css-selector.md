---
title: "[CSS] 선택자"
category: "CSS"
tag: ["web", "css", "selector"]
---
# 개요

[CSS 기초 개념](/css/css-basic-concepts/) 페이지에서 선택자를 언급했는데, 이번 문서에서는 선택자의 종류에 대해 살펴보겠다.

# 전체 선택자 (Universal selector)

전체 선택자는 스타일을 문서 내 모든 요소에 적용하고자 할 때 사용하는 선택자로, * 기호를 사용한다. 다음은 전체 선택자를 이용하여 내용물과 브라우저 테두리 간 간격을 더 벌리는 에제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        * {
            margin: 20px;
            border: 1px solid black;
        }
    </style>
</head>
<body>
    <h1>Welcome!</h1>
    <p>Hi, Everyone!</p>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-12-04/css-selector/1.png)

예제 1-1 실행결과

위 예제 1-1을 실행한 결과, 전체 선택자를 이용한 스타일 지정을 하지 않았을 때와 비교하여 컨텐츠가 좀 더 오른쪽에 위치하게 된다. 또한 위 실행결과를 보면 알 수 있듯, h1, p 요소들에도 모두 margin, border 속성에 의한 스타일이 적용된 것을 알 수 있다. 

이렇듯, 전체 선택자는 주로 웹 브라우저의 기본 스타일 초기화에 사용된다고 한다. 

# 타입 선택자(Type selector)

태그 선택자(Tag selector)라고도 불리는 타입 선택자는 HTML에서 사용되는 특정 태그를 선택자로 사용하며, 해당 문서 내 모든 해당 태그들에 적용된다. 다음은 타입 선택자에 a를 선택하여 HTML 문서 내 모든 \<a> 태그에 특정 스타일을 지정하는 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        a {
            text-decoration: none;
            color:coral;
        }
    </style>
</head>
<body>
    <h1>검색엔진 목록</h1>
    <ul>
        <li><a href="https://www.naver.com/">Naver</a></li>
        <li><a href="https://www.google.co.kr/?hl=ko">Google</a></li>
        <li><a href="https://www.bing.com/">MS Bing</a></li>
        <li><a href="https://www.daum.net/">Daum</a></li>
    </ul>
</body>
</html>
```

![예제 2-1 실행결과](/images/2023-12-04/css-selector/2.png)

예제 2-1 실행결과

위 예제 2-1에서 보다시피, a 태그 선택자로 지정한 CSS 코드가 \<body> 영역 내 모든 \<a> 태그에 적용된 것을 알 수 있다. 

# 클래스 선택자 (Class selector)

클래스는 서로 같은 태그들임에도 서로 다른 스타일을 적용하고자 할 때 쓰이는 속성이다. 이러한 각각의 클래스에 스타일을 적용하고자 할 때 클래스 선택자를 사용한다. 

클래스 선택자를 사용할 때에는 클래스 이름 앞에 마침표(.)를 반드시 먼저 써야 한다. 다음은 클래스 선택자를 이용하여 같은 \<div> 태그라도 각각 다른 스타일들을 적용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .weather_div {
            margin: 10px;
        }
        .feel {
            margin: 10px;
        }
        .diary_form {
            margin: 10px;
        }
        .diary_text {
            color: white;
            background-color: black;
        }
    </style>
</head>
<body>
    <form style="background-color:blanchedalmond">
        <fieldset>
            <legend>오늘의 일기</legend>
            <div class="weather_div">
                <label for="weather">오늘 날씨</label>
                <select id="weather">
                    <option value="clear" selected>맑음</option>
                    <option value="cloudy">흐림</option>
                    <option value="rain">비</option>
                    <option value="snow">눈</option>
                </select>
            </div>
            <div class="feel">
                <label for="feeling">오늘의 기분</label>
                <input type="text" id="feeling" list="f_list">
                <datalist id="f_list">
                    <option value="good">좋음</option>
                    <option value="happy">행복함</option>
                    <option value="angry">화남</option>
                    <option value="sad">슬픔</option>
                </datalist>
            </div>
            <div class="diary_form">
                <div>
                    <label for="diary">일기 작성</label>
                </div>
                <div>
                    <textarea id="diary" cols="50" rows="20" class="diary_text"></textarea>
                </div>
            </div>
        </fieldset>
    </form>
</body>
</html>
```

![예제 3-1 실행결과](/images/2023-12-04/css-selector/3.png)

예제 3-1 실행결과

한 편, 하나의 태그의 class 속성값에 둘 이상의 클래스명을 입력하여 둘 이상의 스타일 규칙을 적용시킬 수 있다. 이 때 클래스명 끼리는 띄어쓰기 한 칸으로 서로를 구분한다. 

```html
<style>
	.good {
		color: orange;
	}
	.great {
		color: blue;
  }
</style>
<p class="good great"></p>
```

# id 선택자(id selector)

id 선택자는 클래스 선택자와 동일하게 특정 요소에 고유한 스타일을 적용하고자 할 때 사용할 수 있는 선택자이다. 클래스 선택자와 거의 동일하나 다음의 차이점을 가지고 있다.

- CSS에서 id 선택자 이름 앞에 # 기호를 붙여야 한다.
- 클래스 선택자로 지정된 특정 클래스명은 문서 내 여러 요소에 적용시킬 수 있으나, id 선택자로 지정된 특정 id명은 문서 내에서 딱 한 번만 적용시킬 수 있다. 예를 들어 id=’text-style’이라는 id 명이 있을 때, 해당 id명은 문서 내에서 딱 하나의 요소에만 적용시킬 수 있다는 것이다. 이로 인해, id 선택자는 문서의 레이아웃 스타일 지정 또는 자바스크립트에서 요소들을 구별하기 위한 용도로 주로 사용된다고 한다.

# 그룹 선택자(Group selector)

에제 3-1의 \<style>~\</style> 코드를 보면 3개의 클래스에 적용된 스타일 규칙이 모두 똑같은 것을 알 수 있다. 이러한 중복을 제거하기 위해 그룹 선택자를 사용할 수 있다. 선택자 위치에 여러 개의 선택자를 작성하고, 각 선택자들은 쉼표(,)로 구분하면 된다. 예제 3-1의 \<style>~\</style> 코드를 그룹 선택자를 이용해 중복되는 코드를 제거하면 다음과 같다.

```html
<style>
    .weather_div, .feel, .diary_form {
        margin: 10px;
    }
    .diary_text {
        color: white;
        background-color: black;
    }
</style>
```

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석