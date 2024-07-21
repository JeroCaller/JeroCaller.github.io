---
title: "[JS] 브라우저 객체 모델 (Browser Object Model)"
category: "javascript"
tag: ["web", "js", "javascript", "BOM"]
---

# Browser Object Model(BOM)

브라우저 객체 모델 (Browser object model, BOM)은 자바스크립트에서 웹 브라우저의 정보, 기능 등에 접근하여 이를 제어하기 위해, 웹 브라우저의 기능을 객체로 나타낸 개념이다. 즉, 웹 브라우저 창의 크기, 위치 등의 정보, 웹 브라우저 창에 띄울 알림 창 기능 등을 객체화하여 나타낸 것으로, 개발자는 이러한 객체들을 인스턴스화하여 사용함으로써 웹 브라우저에 대한 정보를 얻어오거나 어떠한 기능들을 실행할 수 있다. 

웹 브라우저 내 웹 문서 영역에서 웹 요소들이 표시될 때 인터프리터는 코드를 해석하며 이를 웹 문서에 표시하는데, 이 때 관련된 객체들을 생성한다. 

브라우저 객체 모델에서, 객체 계층에서 최상위 객체인 window 객체는 웹 브라우저의 창(window)을 표현한 객체이다. 웹 브라우저 창에 대한 기능을 제어할 수 있다. 자바스크립트 코드 상에서는 전역 객체(global object)이다. 이러한 이유로, 자바스크립트 코드 상에서 선언, 사용되는 모든 객체와 함수, 변수들은 window 객체의 프로퍼티 또는 메서드가 된다. 이 중 변수는 window 객체의 property로, 함수는 window 객체의 메서드가 된다. 

이러한 이유로, 사실 여태까지 우리가 알았던 자바스크립트 내 내장 객체나 사용자 정의 함수, 변수들의 식별자 앞에 window. 을 붙여도 문제없이 작동한다. 또한 이 말을 달리 말하자면, window 객체의 프로퍼티, 메서드 사용 시 앞에 ‘window.’은 생략할 수 있다. 

```jsx
var arr = Array(1, 2, 3);
document.write(window.arr + "<br>");
document.write(arr, "<br>");

function getTotalFast(n) {
    // 1부터 n까지의 정수들의 합을 좀 더 빨리 얻을 수 있는 함수. 
    return n * (n + 1) / 2;
}

document.write(window.getTotalFast(100));
```

![예제 1-1 실행결과](/images/2024-02-13/js-browser-object-model/1.png)

예제 1-1 실행결과

> window. 객체명을 사용하면서 이를 console.log()로 출력하려고 시도할 때, 터미널 창에서 node.js를 이용하여 이 코드를 실행하려고 하면 다음의 에러 문구가 뜨면서 실행되지 않음을 확인하였다.
> 
> 
> ```jsx
> ReferenceError: window is not defined
>     at Object.<anonymous> (C:\web\html_css_js_study\js\js-ex.js:2:13)
>     at Module._compile (node:internal/modules/cjs/loader:1376:14)
>     at Module._extensions..js (node:internal/modules/cjs/loader:1435:10)
>     at Module.load (node:internal/modules/cjs/loader:1207:32)
>     at Module._load (node:internal/modules/cjs/loader:1023:12)
>     at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:135:12)      
>     at node:internal/main/run_main_module:28:49
> ```
> 
> 터미널 창에서 node.js를 이용하여 코드 실행 시 window 객체를 인식하지 못하는 것 같다. window 객체명을 사용한 결과를 보려면 웹 브라우저에서 실행시켜야 한다. 
> 위 에러가 나는 이유를 추측해보자면, 원래 해당 자바스크립트는 다른 HTML 문서에서 import 하여 사용되는 코드로, 웹 브라우저 상에서 실행하면 연결된 HTML 문서가 있고, 그 HTML 문서를 웹 브라우저가 해석하고 화면 상에 보여주는 방식이다. 즉, 웹 브라우저라는 개념이 연결되어 있기에 문제 없이 실행된다. 그러나 터미널 창에서 실행할 때, 실행 과정에서 웹 브라우저가 전혀 관여하지 않고, 애초에 웹 브라우저를 거치지 않기 때문에 웹 브라우저 창 객체인 window 객체를 터미널 상에서는 당연히 인식하지 않는 것이라 추측된다. 그래서 위와 같은 에러가 발생했다고 추측한다. 
> 

한 편, 웹 브라우저 객체 계층에는 앞서 언급했듯 최상위에는 window 객체가 존재하고, 그 아래로는 document, navigator, history, location, screen 등의 하위 객체들이 존재한다. 이 중 document 객체는 웹 문서 영역 내에서 웹 요소들을 표시할 때 사용되는 HTML 태그와 관련된 객체로, 그 아래에 image, form 등의 하위 객체들을 가진다. 

# window 객체

window 객체에 대한 설명은 위에서 하였다. 여기서는 간단하게 window 객체의 프로퍼티, 메서드를 활용한 간단한 예제를 살펴보겠다. 참고로 window 객체에 내장된 여러가지 property, method들은 References [3]에서 자세히 살펴볼 수 있다. 

다음은 버튼을 누르면 현재 웹 페이지의 크기를 알려주는 웹 페이지를 구현한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <!-- 현재 웹 페이지의 크기를 확인하는 웹 페이지.-->
    <div id="window-info">
        <p></p>
    </div>
    <input type="button" value="현재 웹 페이지 크기 확인" id="control-window">
    <script>
        let windowInfoP = document.querySelector('#window-info p');
        let getInfoButton = document.querySelector('#control-window');
        
        function updateWindowSize() {
            windowInfoP.textContent = `현재 웹 페이지의 너비, 높이는 각각 ${window.innerWidth}px, ${window.innerHeight}px 입니다.`;
        }

        getInfoButton.addEventListener("click", updateWindowSize);
        updateWindowSize();
    </script>
</body>
</html>
```

![예제 2-1 실행결과](/images/2024-02-13/js-browser-object-model/2.png)

예제 2-1 실행결과

## window 객체 활용 : 팝업창 만들어보기

window 객체의 open(), close() 메서드를 이용하면 웹 브라우저 상에서 팝업창을 띄울 수도 있고, 해당 팝업창을 닫을 수도 있다. 

### open()

새 창을 여는 메서드로, 구조는 다음과 같다. 

```jsx
window.open(path, name, option);
```

- path : 새 창으로 띄울 웹 문서 또는 사이트 url.
- name : 새 창의 이름을 설정한다. 만약 이 메서드를 팝업창으로 활용할 경우, 팝업창의 이름을 설정하지 않는다면, 팝업창을 띄우는 웹 페이지에서 계속 새로고침하면 중복되는 팝업창이 여러 개나 뜬다. 반대로 이름을 설정하면 해당 웹 페이지를 아무리 새로고침해도 팝업창은 2개 이상 생성되지 않고 하나만 띄워진다.
- option : 팝업창의 크기, 위치를 지정한다. 위치는 웹 브라우저 창을 기준으로 하며, left, right, top, bottom 등의 속성을 이용한다. 예) right=100px. 위치 지정을 하지 않으면 창의 왼쪽 위에 생성된다. 크기의 경우, width, height 속성을 이용한다. 이 옵션을 지정하지 않으면 팝업창이 아니라 새 창에서 해당 웹 문서가 뜬다.

웹 브라우저에서는 팝업창이 뜨는 것을 차단하는 기능이 있는데, 이로 인해 팝업창이 뜨지 않으면 open() 메서드는 null을 반환한다. 이를 이용하면 팝업창 차단으로 인해 팝업창을 볼 수 없으니 필요한 경우 차단 해제하라는 알림창을 띄울 수도 있다. 

### close()

새 창을 여는 open() 메서드와 정반대로 창을 닫는 메서드이다. 팝업 창 내 “닫기” 버튼을 눌러 해당 창을 닫는 기능을 추가하고자 한다면 팝업창으로 띄워진 문서 HTML 코드에 다음의 코드를 삽입하면 된다. 

```html
<button onclick="javascript:window.close();">닫기</button>
```

### 팝업창 예제

다음은 위 메서드들을 활용하여 팝업창을 띄우는 예제이다. 먼저 팝업창 내에 표시할 웹 문서 코드는 다음과 같다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #container {
            background-color:antiquewhite;
            color:darkblue;
            padding: 10px;
            margin: 0px;
            position: relative;
        }
        h1 {
            text-align: center;
        }
        #container > ul > li {
            margin: 30px;
        }
        .sub-ul li {
            margin: 10px;
        }
        button {
            position: absolute;
            right: 10px;
            bottom: 10px;
        }
    </style>
</head>
<body>
    <div id="container">
        <h1>공지사항</h1>
        <ul>
            <li>
                전자 도서관 구독 서비스 요금이 다음과 같이 인하되었습니다. 
                <ul class="sub-ul">
                    <li>Basic : <s>6,000원</s> -> 4,000원</li>
                    <li>Premium : <s>13,000원</s> -> 10,000원</li>
                </ul>
            </li>
            <li>
                점검 안내. 전자 도서관 점검이 있을 예정이니 참고 바랍니다. 
                <ul class="sub-ul">
                    <li>일시 : 20XX년 X월 XX일 오전 03:00 ~ 09:00</li>
                    <li>점검 내용 : 오류 해결, UI 개선</li>
                </ul>
                이 시간 동안에는 저희 사이트를 이용할 수 없으니 양해바랍니다.
            </li>
        </ul>
        <button onclick="javascript:window.close();">닫기</button>
    </div>
</body>
</html>
```

다음은 위 문서를 팝업창으로 띄울 웹 페이지 HTML 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <script>
        let popupOpen = window.open("mock-web-notice.html", "", "width=500, height=470, left=300, top=100");
        console.log(popupOpen);
        console.log(typeof popupOpen);
        if (popupOpen === null) {
            alert("필독 사항 팝업창을 보시려면 팝업창 차단을 해제하고 새로고침 하세요.");
        }
    </script>
</body>
</html>
```

“mock-web.html”을 실행하면 다음의 결과를 얻는다. 

![예제 3-1~2 실행결과](/images/2024-02-13/js-browser-object-model/3.png)

예제 3-1~2 실행결과

> 실제 사이트에서는 open() 메서드를 통해 웹 브라우저를 직접 이용하여 팝업창을 띄우는 대신, 아예 웹 페이지 내에서 사이트 위에 팝업창을 띄우는 레이어 방식을 선택한다고 한다. 즉, 웹 브라우저 창을 활용하는 것이 아닌, 아예 문서 내부에서 팝업창을 띄우는 형태이다. 이는 `<div>` 태그와 DOM을 이용하여 구현할 수 있다고 한다.
> 

# navigator 객체

navigator 객체는 웹 브라우저의 정보를 담고 있는 객체로, 웹 브라우저 정보만을 보여주기에, 해당 정보를 사용자가 수정할 수는 없다. 

이 객체를 이용하면, 웹 페이지에 접근하는 사용자의 웹 브라우저 정보를 읽어들여 다른 웹 브라우저라도 똑같이 작동할 수 있도록 대비할 수 있다. 웹 브라우저마다 HTML, CSS를 해석하는 렌더링 엔진(rendering engine, layout engine이라고도 함)과 자바스크립트 코드를 해석하는 자바스크립트 엔진이 다르기에, 사용자가 어떤 웹 브라우저를 사용하냐에 따라 웹 페이지가 다르게 보일 수도 있다고 한다. 그래서 navigator 객체를 이용해 사용자의 웹 브라우저 종류를 파악하고, 해당 웹 브라우저에서도 작동할 수 있도록 하여 호환성을 유지할 수 있다. → 이렇게 사용자의 웹 브라우저 종류를 파악하여 어떤 웹 브라우저에서든 웹 페이지를 열람할 수 있도록 호환성 조치를 하는 것을 브라우저 스니핑(browser sniffing)이라고 한다. 

크롬 기준, 웹 브라우저에서 개발자 도구를 킨 다음, 자바스크립트 코드를 입력할 수 있는 console 창에서 “navigator” 입력 뒤 엔터키를 누르면 현재 웹 브라우저에 대한 여러 가지 정보들이 navigator 객체의 property로 보여진다. 

![사진 1. 크롬 웹 브라우저의 개발자 도구에서 navigator 객체의 프로퍼티들을 보여주고 있다. ](/images/2024-02-13/js-browser-object-model/4.png)

사진 1. 크롬 웹 브라우저의 개발자 도구에서 navigator 객체의 프로퍼티들을 보여주고 있다. 

여기에는 쿠키 정보를 허락하였는지 여부의 cookieEnabled, 브라우저 UI 언어 정보인 language, 사용자가 사용하고 있는 웹 브라우저의 정보를 담고 있는 userAgent 등이 있다. userAgent에는 현재 사용자의 웹 브라우저 종류, 사용자 디바이스의 OS(컴퓨터의 경우 윈도우 또는 macOS, 모바일의 경우 iphone 또는 android 등), 정보도 뜬다. 

# history 객체

웹 브라우저에서 방문한 사이트들의 url 정보들을 히스토리라 하는데, history 객체는 이러한 히스토리 정보에 대한 객체이다. 보안 이슈로 인해 history 객체에서 사용할 수 있는 프로퍼티, 메서드가 한정되어 있다고 한다. 

property

- length : 현재 브라우저 창의 history 목록 내 항목 수. 즉, 여태까지 방문한 사이트들의 수.

methods

- back() : 이전 페이지를 불러온다.
- forward() : 다음 페이지를 불러온다.
- go() : 현재 페이지 기준으로 history 목록 내 상대적 위치에 존재하는 페이지를 불러온다. go(1)은 forward()와 같은 효과를 내고, go(-1)은 back()과 같은 효과를 낸다.

다음은 위 정보들을 이용하여 현재 방문 사이트 개수 및 이전, 다음 페이지로 갈 수 있는 기능을 탑재한 웹 페이지 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            border: 1px solid black;
            margin-top: 10px;
            padding: 10px;
        }
    </style>
</head>
<body>
    <div id="container">
        <div id="info">
            <p></p>
        </div>
        <div id="back">
            <p>이전 페이지로 가고자 한다면 다음의 버튼을 클릭하세요.</p>
            <button id="back-btn">이전 페이지로 이동</button>
        </div>
        <div id="forward">
            <p>다음 페이지로 가고자 한다면 다음의 버튼을 클릭하세요.</p>
            <button id="forward-btn">다음 페이지로 이동</button>
        </div>
        <div id="go-back">
            <p>history.go(-1)</p>
            <p>이전 페이지로 가고자 한다면 다음의 버튼을 클릭하세요.</p>
            <button id="go-back-btn">이전 페이지로 이동</button>
        </div>
        <div id="go-forward">
            <p>history.go(1)</p>
            <p>다음 페이지로 가고자 한다면 다음의 버튼을 클릭하세요.</p>
            <button id="go-forward-btn">다음 페이지로 이동</button>
        </div>
    </div>
    <script>
        let infoP = document.querySelector('#info p');
        infoP.textContent = `현재 방문 사이트 수는 ${history.length}개입니다.`;

        let backBtn = document.querySelector("#back-btn");
        backBtn.onclick = () => history.back();

        let forwardBtn = document.querySelector("#forward-btn");
        forwardBtn.onclick = () => history.forward();

        let goBackBtn = document.querySelector("#go-back-btn");
        goBackBtn.onclick = () => history.go(-1);

        let goForwardBtn = document.querySelector('#go-forward-btn');
        goForwardBtn.onclick = () => history.go(1);
    </script>
</body>
</html>
```

![예제 4-1 실행결과](/images/2024-02-13/js-browser-object-model/5.png)

예제 4-1 실행결과

# location 객체

location 객체는 웹 브라우저의 url 입력칸의 url 정보에 대한 객체로, url 정보를 얻어오거나 관련된 기능을 제어할 수 있다. 프로퍼티와 메서드가 많기에 이 모든 것을 보고자 한다면 다음 사이트를 참고.

[Location - Web API \| MDN](https://developer.mozilla.org/ko/docs/Web/API/Location)

대표적으로, location 객체의 프로퍼티로 현제 페이지의 전체 url을 담고 있는 href 등이 있고, 메서드로는 assign(), reload() 등이 있다. 메서드에 대해 좀 더 자세히 살펴보면,

- assign() : 현재 페이지에서 새 웹 페이지 주소를 괄호 안에 문자열 형태로 기입하면 새 웹 페이지 주소로 할당되어 해당 웹 페이지로 이동한다. (현재 문서에서 새 웹 페이지 화면이 뜬다)
- replace() : assign() 메서드와 똑같은 기능을 하지만, 새 페이지를 로딩해오기 전에 현재 웹 페이지의 방문 기록을, 즉 브라우저의 히스토리에서 지운 후에 새 페이지로 이동한다.
- reload() : 새로 고칭과 같은 기능.

다음은 location 객체를 이용하여 현재 웹 페이지의 주소를 웹 문서에 보여주고, 버튼을 누르면 각 검색 엔진 사이트를 현재 창에서 보여주는 예제 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="container">
        <header>
            <h1>검색 엔진 목록</h1>
        </header>
        <main>
            <p>버튼 클릭 시 현재 페이지에서 해당 페이지로 이동됩니다.</p>
            <ul>
                <li>
                    <p>네이버 (Naver)</p>
                    <button id="to-naver">네이버로 이동하기</button>
                </li>
                <li>
                    <p>구글 (Google)</p>
                    <button id="to-google">구글로 이동하기</button>
                </li>
                <li>
                    <p>다음 (Daum)</p>
                    <button id="to-daum">다음으로 이동하기</button>
                </li>
            </ul>
        </main>
        <footer>
            <p></p>
        </footer>
    </div>
    <script>
        let currentUrl = document.querySelector('footer p');
        currentUrl.textContent = `현재 웹 페이지 주소: ${location.href}`;

        const urlTable = {
            'to-naver': "https://www.naver.com/",
            "to-google": "https://www.google.co.kr/",
            "to-daum": "https://www.daum.net/",
        }

        let btns = document.querySelectorAll('button');
        for(const btn of btns.values()) {
            btn.onclick = () => location.assign(urlTable[btn.id]);
        }
    </script>
</body>
</html>
```

![예제 5-1 실행결과](/images/2024-02-13/js-browser-object-model/6.png)

예제 5-1 실행결과

# screen 객체

웹 페이지에 접속한 사용자의 기기의 디스플레이 정보를 가지는 객체이다. 자세한 프로퍼티와 메서드는 다음 사이트 참고.

[코딩교육 티씨피스쿨](https://www.tcpschool.com/javascript/js_bom_screen)

(아직까진 이 객체의 중요성이 체감이 안되어서 이렇게 생략한다)





---
References

[1] [코딩교육 티씨피스쿨](https://www.tcpschool.com/javascript/js_bom_window)

[2] [브라우저 환경과 다양한 명세서](https://ko.javascript.info/browser-environment)

[3] [Window - Web API \| MDN](https://developer.mozilla.org/ko/docs/Web/API/Window)

[4] 전역 객체

[전역 객체](https://ko.javascript.info/global-object)

[5] navigator의 userAgent 관련 정보

[[JS] User Agent 브라우저 정보 얻기 (크롬인지 아닌지 체크하기)](https://zinee-world.tistory.com/512)

[6] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석
