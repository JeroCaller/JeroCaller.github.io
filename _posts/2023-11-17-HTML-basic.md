---
title: "[HTML] HTML 기초"
category: "HTML"
tag: ["web", "HTML", "basic"]
---
# 개념

HTML은 HyperText Markup Language의 준말로, 여기서 HyperText는 문서들을 서로 연결해주는 링크를, Markup은 ‘표시’의 의미를 가지고 있다. 즉, HTML은 웹 문서를 제작하는 데에 쓰이는 언어로, 웹 문서에 표시할 정보들을 표시해주고, 이러한 웹 문서들을 링크를 통해 묶어주는 역할을 한다. 

HTML에는 웹 문서의 구조나 웹 요소들을 구별하기 위해 꺾인 괄호(<>)를 일종의 이름표처럼 사용하는데, 이를 태그(tag)라 한다. \<p\>, \<h1\> 등이 모두 태그이다. 

# HTML의 기본 구조

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>HTML로 간단한 웹 문서 만들기</title>
</head>
<body>
    <h1>프론트엔드</h1>
    <p>HTML</p>
    <p>CSS</p>
    <p>JavaScript</p>
</body>
</html>
```

HTML의 구조는 기본적으로 위 예제 1-1에서 볼 수 있듯 여러 영역으로 나눠서 볼 수 있다. 

- \<!DOCTYPE html\> : 웹 문서의 형식을 지정해주는 곳으로, 여기서는 해당 웹 문서가 html임을 지정하고 있다. 이렇게 지정해주면 웹 브라우저가 해당 웹 문서를 해석할 때 해당 웹 문서가 HTML 문서임을 알 수 있다.
- \<html\> ~ \</html\> : 웹 문서 전체를 지정하는 영역. \<html\> 부분이 웹 문서의 시작, \</html\>이 웹 문서의 끝이라 보면 되겠다. \</html\> 뒤에는 어떤 내용도 있으면 안된다. 
\<html\> 태그 안의 속성으로 lang 속성을 사용할 수 있다. 해당 속성은 해당 웹 문서가 사용할 언어를 지정할 때 쓴다. 위 예제 1-1에서는 해당 웹 문서가 한국어를 사용할 것이기에 lang=”ko”라고 적었다.
- \<head\> ~ \</head\> : 웹 문서에 대한 정보들을 모아둔 곳. 이러한 웹 문서의 정보들은 웹 브라우저가 해당 문서를 해석하거나 검색엔진이 해당 문서를 참고하기 위해 필요한 정보들을 제공해주는 역할을 한다. 이 영역에 있는 코드들은 웹 브라우저 화면에는 대부분 안 보인다.
- \<body\> ~ \</body\> : 실제로 웹 브라우저 화면에 보이는 영역이다.

\<head\> 태그 안의 영역에는 주로 다음의 태그들이 사용된다. 

- \<title\> ~ \</title\> : 웹 문서 제목을 나타낸다. 웹브라우저의 탭 제목, 검색 엔진에서 검색 시 맨 처음 사용자에게 보이게 할 제목으로 보이게 된다.
- \<meta\> : 웹 문서 관련 정보들을 지정한다.
    - \<meta charset=’UTF-8’> : 화면에 표시할 문자의 인코딩 방식을 지정한다. 한글의 경우 웹 브라우저 화면에서 깨져서 보일 수도 있기에 \<meta> 태그의 charset 속성을 ‘utf-8’로 지정해주는 것이 좋다.
    - 웹 문서의 키워드, 상세 설명, 제작자 정보를 다음과 같이 작성할 수 있다. 해당 정보들은 검색 엔진이 해당 문서를 검색할 때 참조할 수 있는 정보가 된다.
        
        ```html
        <meta name="keywords" content="html 기초">
        <meta name="description" content="html의 구조를 알아보기 위한 페이지.">
        <meta name="author" content="JeroCaller">
        ```
        

# 시맨틱 태그(Semantic tag)

HTML에서의 태그는 시맨틱 태그라고도 불린다. 여기서 시맨틱(Semantic)은 “의미론적인”이라는 뜻을 가지고 있다. 글의 단락을 나타내는 \<p> (p→paragraph), 하이퍼링크를 달 때 쓰이는 \<a> (a→anchor) 태그 등 각각의 의미가 태그 이름 안에 담겨져 있기에 붙은 이름이다. 

웹 문서는 시맨틱 태그 없이도 만들 수는 있으나, 시맨틱 태그를 사용하여 문서를 만들면 문서의 구조와 문서 내 요소들을 명확히 구분하여 쉽게 파악하게 할 수 있다. 

## 웹 문서의 구조를 만드는 시맨틱 태그들

- \<header> : 헤더 영역. 사이트에서의 헤더는 주로 웹 페이지의 맨 위쪽 또는 왼쪽을 차지한다. 사이트 메뉴, 검색 창을 주로 헤더 영역 안에 넣는다.
- \<nav> : 네비게이션 영역. 같은 사이트 내 다른 문서 또는 다른 사이트로 향하는 링크를 만드는 영역이다. 해당 태그는 다른 태그 영역에 포함시킬 수도 있고, 아예 독립적인 영역으로 만들 수도 있다.
- \<main> : 웹 문서 내에서 핵심적인 컨텐츠들을 담는 영역. 해당 영역에는 해당 웹 문서에만 존재하는 컨텐츠들을 담는 역할을 한다. 따라서 해당 영역에 모든 웹 문서에 공통적으로 존재하는 메뉴, 사이드 바 등의 요소들을 담지 않는다. 해당 태그는 하나의 웹 문서에서 단 한 번만 사용할 수 있다.
- \<article> : 독립적인 웹 콘텐츠 영역. 블로그 글이나 기사 글처럼 하나의 큰 독립적인 컨텐츠를 담을 때 쓰인다. 하나의 문서 안에 여러 번 사용할 수 있으며, 해당 태그 안에 \<section> 태그를 사용할 수도 있다.
- \<section> : 여러 컨텐츠들을 하나의 영역 안에 묶어 담고자 할 때 쓰는 태그. 만약 단순히 스타일을 지정하는 용도를 위해서 컨텐츠들을 묶고자 한다면 \<div> 태그를 대신 사용하면 된다.
- \<aside> : 웹 페이지의 한 쪽에 사이드바를 만든다.
- \<footer> : 웹 문서의 맨 아래쪽의 푸터 영역을 생성하는 태그. 주로 웹 사이트 보유자(개인 또는 기업)의 연락처, 사이트 정보, 사이트맵 등의 메뉴들을 넣는다.
- \<div> : 구별되는 문서 구조 생성 시 사용되는 태그. 영역을 구분하거나 특정 영역의 스타일을 지정하고자 할 때 쓰인다.

# 태그(tag)와 요소(element)

HTML에서 태그와 요소는 서로 똑같아 보이지만 엄연히 차이가 있다. 태그는 괄호(<>)로 쌓여진 \<h1>, \<p>과 같은 것을 태그라 한다. 반면, 요소는 태그 자체를 포함하여 태그가 적용된 컨텐츠까지를 포함하는 말이다.

```html
<p>Hello world!</p>
```

위 예제 2-1에서 \<p>, \</p>는 태그이며, `<p>Hello world!</p>` 전체가 요소라 불린다.

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석