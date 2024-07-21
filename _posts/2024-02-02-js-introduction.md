---
title: "[JS] 자바스크립트 개요"
category: "javascript"
tag: ["web", "js", "javascript"]
---
# 자바스크립트 활용

웹 페이지 제작 시 자바스크립트로 할 수 있는 일에는 다음이 존재한다. 

- 웹 요소 제어: 사용자의 마우스, 키보드 등의 입력에 따라 그에 따른 다양한 반응을 할 수 있도록 웹 요소들을 제어할 수 있다. 여기에는 일정 시간이 지나면 자동으로 다른 그림으로 넘어가는 사진 슬라이드, 상위 메뉴에 마우스를 올려두면 그 전에는 안 보였던 하위 메뉴를 보여주는 등의 동적인 웹 UI를 만들 수 있다.
- 웹 애플리케이션 제작: 자바스크립트를 이용하면 마치 앱처럼 사용자 간의 정보 교환을 실시간으로 진행할 수 있다. 이를 통해 게임, 데이터 시각화, 실시간 정보 공개 등등의 여러 웹 사이트들을 제작할 수 있다.
- 관련 라이브러리 : React, Angular 등 자바스크립트 라이브러리를 다양하게 사용하여 웹 사이트 개발에 활용할 수 있다.
- 백엔드 개발에도 사용 : Node.js는 자바스크립트를 백엔드 개발에도 사용할 수 있는 프레임워크로, 즉 자바스크립트로도 백엔드 개발이 가능하다.

# 웹 문서에 자바스크립트 적용하기

CSS와 마찬가지로, 자바스크립트도 HTML 문서 내부에 작성하여 사용하는 방법과, 따로 자바스크립트 파일을 만들어 그 곳에 관련 코드들을 작성한 다음, 이 외부 파일을 HTML 파일 내에 불러들여 자바스크립트 코드를 실행시키는 방법이 있다. 이 두 방법 모두 HTML 문서 내에서 `<script>` 태그를 이용한다. 

- 웹 문서 내부에서 자바스크립트를 정의하여 사용할 때에는 자바스크립트 코드는 `<script>` 태그 내부에 작성한다. 그리고 `<script>` 태그 자체는 웹 문서 내 어디에 놓아도 되고, 여러 개의 `<script>` 태그를 사용할 수도 있다. 다만, 대부분의 경우, 자바스크립트 코드 내에서 HTML 문서 내 요소들을 직접 사용, 제어할 때가 많으므로, 이미 웹 요소들이 나온 후의 지점인 `</body>` 앞에 자바스크립트 코드를 위치시킨다고 한다.
- 외부에서 자바스크립트 파일을 따로 정의하여 이를 HTML 문서 내에서 불러오는 방법도 있다. 자바스크립트 파일의 확장자인 .js로 끝나는 파일을 생성한다. 해당 파일 안에 자바스크립트 코드들을 정의한 후, HTML 문서 내에서 `<script src=”myjs.js”></script>` 와 같이 `<script>` 태그 내 src 속성에 해당 외부 자바스크립트 경로를 입력하면 HTML에서 해당 파일을 불러와 자바스크립트 내 정의한 기능들을 그대로 사용할 수 있다.

# 웹브라우저의 웹 문서 코드 해석 과정

웹 브라우저에는 Javascript interpreter가 있어 자바스크립트 코드를 해석하고 이를 처리하여 그 결과를 웹 브라우저 화면 내에 띄울 수 있다. HTML 문서 내에 자바스크립트를 사용하는 경우의 웹 브라우저가 코드를 해석하는 과정은 대략 다음과 같다 .

1. 웹 브라우저는 HTML 코드를 위에서부터 순차적으로 읽어내려간다. 이로 인해 맨 처음 `<!DOCTYPE html>` 태그를 마주칠 텐데, 이를 통해 해당 문서가 웹 문서임을 인식하여 그 다음 코드들을 HTML 문법으로 인식하게 된다. 
2. HTML 문서 내 여러 태그들의 순서 및 포함 관계, 태그 간 관계를 검사하여 태그 분석을 실시한다. 
3. 그 후, CSS 파일 내 코드들을 분석한다. 
4. 만약 HTML 문서를 읽어내려가다가 `<script>` 태그를 만나면 즉시 HTML 해석을 중단하고, 연결된 자바스크립트를 Javascript interpreter에 넘겨 해석하기 시직한다. 
5. 앞서 분석한 HTML, CSS 코드에 따라 웹 페이지를 화면에 띄운다. 
6. 사용자의 상호작용 중에 자바스크립트 내 정의된 코드의 기능을 필요로 하는 경우, 앞서 분석한 자바스크립트 파일을 실행시켜 그 처리 결과를 웹 페이지에 표시한다. 

# 자바스크립트 기본 사실들

## expression and statement

자바스크립트에서는 표현식(expression)과 문(statement)이 있다. 표현식의 경우, 특정 값을 낼 수 있다면 모두 표현식이 된다. 연산식, 값, 함수 호출도 식이 된다. 식은 보통 변수에 저장하여 사용할 수 있다. 
표현식 예)

```jsx
'hellow world!';
23
radius * 3.14;
```

반면 문은 명령으로, 끝에 항상 세미콜론(;)을 붙여 다른 코드와 구분한다. 조건문, 반복문이 여기에 해당한다. 문은 값, 표현식 모두를 포함한다. 

## 주석

자바스크립트에서의 주석은 슬래시 두 개 (//)를 이용하면 된다. 

```jsx
// 이 메시지는 웹 브라우저에서 출력되지 않아요.
```

## 대화상자 사용하기

웹 브라우저에서 정보 창이나 사용자에게 어떠한 값을 입력하도록 요구하는 창을 대화상자(dialogue box)라 한다. 자바스크립트로 띄울 수 있는 대화상자에는 다음이 존재한다. 

- alert()

alert() 함수 안에 원하는 메시지를 따옴표(’’ 또는 “” 둘 중 아무거나 넣으면 된다) 안에 넣으면 웹 브라우저 상에서 알림 창을 띄울 수 있다. 

예제에 앞서, 다음의 빈 HTML 문서를 준비하고, “js_example.js”란 자바스크립트 파일을 외부에서 불러들여오고 있다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="js_example.js"></script>
</head>
<body>
</body>
</html>
```

그리고 해당 자바스크립트 파일 안에는 다음의 코드를 정의하였다. 

```jsx
alert("hello, world!");
```

그리고 이를 실행하면 다음의 결과를 얻는다. 

![예제 1-1~2 실행결과](/images/2024-02-02/js-introduction/1.png)

예제 1-1~2 실행결과

- confirm()

confirm() 함수는 alert() 함수와 비슷하지만, 사용자에게 “확인”과 “취소” 버튼을 제공하여 둘 중 하나를 택하도록 할 수 있는 확인 창을 불러오는 함수이다. 이를 이용하여, 사용자가 “확인” 또는 “취소” 버튼 중 어느 버튼을 누르냐에 따라 서로 다른 작업을 처리하게 할 수도 있다. alert()와 똑같이 괄호 안에 따옴표와 함께 출력할 메시지를 입력하면 된다. 다음은 이를 활용한 예제이다. 

```jsx
confirm("hello, world!");
```

![예제 1-3 실행결과](/images/2024-02-02/js-introduction/2.png)

예제 1-3 실행결과

- prompt()

해당 함수는 한 줄 크기의 텍스트 입력창까지 있는 대화상자를 호출하는 함수로, 사용자로 하여금 텍스트를 입력하게 하고, 그 내용을 코드로 받아와서 여러 가지 작업을 할 수도 있다. 해당 함수는 다음의 둘 중 하나를 선택하여 사용할 수 있다. 

```jsx
prompt('문자열'); // 텍스트 필드 내 아무것도 표시되지 않는다. 
prompt('문자열', 기본값); // 텍스트 필드 내 지정된 기본값이 보여진다.
```

다음은 이를 이용한 예제이다. 

```jsx
prompt("당신의 오늘 하루는 어땠나요?", "좋았어요");
```

![예제 1-4 실행결과](/images/2024-02-02/js-introduction/3.png)

예제 1-4 실행결과

## 화면에 출력하기

자바스크립트 코드를 통해 어떠한 결과값을 확인하기 위해 화면에 출력하고자 한다면 document.write() 함수를 사용할 수 있다. 해당 함수의 사용법은 다음과 같다. 

```jsx
document.write("<h1>제목</h1>");
document.write("평범한 문장");
```

![예제 2-1 실행결과](/images/2024-02-02/js-introduction/4.png)

예제 2-1 실행결과

위 예제와 같이 문자열을 따옴표와 함께 입력할 수 있다. 이 때, HTML 태그도 안에 삽입할 수 있다. 그러면 위 실행결과와 같이 해당 태그가 그대로 적용된 결과값이 출력될 것이다. 

한 편, 해당 함수 인자로 변수를 기입해도 된다. 이 때, 문자열과 함께 사용할 때에는 ‘+’를 기호를 이용하여 문자열과 변수를 하나의 문자열로 연결시켜준다. 이 때의 ‘+’ 기호를 연결 연산자라 한다. 다음은 사용자로부터 원의 반지름을 입력받으면 그에 대한 원의 둘레와 넓이를 구해주는 코드이다. 

```jsx
radius = prompt("원의 반지름 입력");

circum = 2 * 3.14 * radius;
area = 3.14 * radius ** 2;
document.write("<h2>입력한 값: " + radius + "</h3>");
document.write("<h3>원의 둘레: " + circum + "</h3>");
document.write("<h3>원의 넓이: " + area + "</h3>");
```

![예제 2-2 실행결과](/images/2024-02-02/js-introduction/5.png)

예제 2-2 실행결과

## console.log()

console.log() 괄호 안에 값을 넣으면 그 값을 콘솔 창에 출력해준다. document.write()와 마찬가지로 문자열을 넣을 수도 있고, 변수를 넣을 수도 있다. 그러나 문자열의 경우, 그 안에 HTML 태그를 사용해도 별 의미는 없다. 결과값이 웹 페이지 화면에 보이는 것이 아니라 콘솔에 출력되기 때문이다. (콘솔에는 HTML 태그를 적용할 순 없다) 

다음은 앞서 구현한 원의 둘레, 넓이를 구하는 계산기에 각각의 값들을 콘솔 창에 출력해주는 예제이다. 

```jsx
radius = prompt("원의 반지름 입력");

circum = 2 * 3.14 * radius;
area = 3.14 * radius ** 2;
document.write("<h2>입력한 값: " + radius + "</h3>");
document.write("<h3>원의 둘레: " + circum + "</h3>");
document.write("<h3>원의 넓이: " + area + "</h3>");

console.log(radius, circum, area);
console.log("<h1>" + radius + "</h1>");
```

![예제 3-1 실행결과](/images/2024-02-02/js-introduction/6.png)

예제 3-1 실행결과

참고로, 웹 브라우저에서의 콘솔 창은 크롬 기준으로 F12를 누르면 웹 브라우저 오른쪽에 나오는 “개발자 도구”에서 “console” 버튼을 누르면 나온다. 이 콘솔 창은 후에 자바스크립트 코드에서 오류가 발생할 때에도 그 오류 메시지가 이 창에 뜨므로 이 콘솔 창을 적극 활용하면 좋을 것이다. 

한 편, 위 실행결과를 보면 알 수 있듯, HTML 태그를 적용한 값을 console.log()를 통해 출력해도 태그가 적용되지 않고 그대로 출력되는 것을 알 수 있다.



---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석