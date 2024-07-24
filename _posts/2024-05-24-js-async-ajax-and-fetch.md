---
title: "[JS][비동기] AJAX and Fetch"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "ajax", "fetch"]
---

# 사전 정보

이 글을 읽기 전에 먼저 읽어두면 좋을 글들을 소개합니다. 

[웹 클라이언트 (Web clients)](/python/web/web-client-and-python/) : “용어 정리”, “웹 원리” 챕터, 그리고 “파이썬 표준 웹 라이브러리” 챕터 내용 중 “HTTP 상태 코드”에 대한 내용만 참고하면 됩니다. 

[웹 서비스와 자동화](/python/web/web-service-and-automation/) : “웹 API와 Representational State Transfer(REST)” 챕터만 보면 됩니다.

위 문서들은 파이썬 언어 예제와 함께 작성되어 있지만, 여기서는 웹에 대한 사전 지식을 요구하는 것이기에, 파이썬을 모르더라도 웹 관련 지식 부분만 참고하면 이해에 지장은 없을 것입니다. 

이 문서에서 설명할 내용은 비동기적 네트워크 통신에 대한 내용이 있으므로, 이 문서에서 나올 예제들은 별다른 언급이 없는 한 브라우저 환경에서 실습해야 제대로 작동합니다. 즉, node.js와 같은 다른 환경에서 실행시키면 제대로 작동되지 않을 수 있으니 참고 바랍니다. 

# AJAX

AJAX(Asynchronous Javascript And XML)는 약자 그대로 “비동기적 자바스크립트와 XML”이란 뜻을 가지고 있는데, 실제로는 서버에 네트워크 요청을 전송하여 서버로부터 정보, 데이터를 비동기적으로 받아오는 기술이라고 보면 된다. 

웹 서버로부터 데이터를 받아와 이를 웹 페이지에 보여주는 웹 사이트가 있을 때, 만약 해당 데이터가 변경되었다면 이 변경된 데이터를 가져와 이를 웹 페이지에 반영해야할 것이다. 이 때, 데이터가 변경되었다는 이유로 페이지 전체를 새로고침하는 것은 매우 비효율적이다. 우선 페이지를 새로고침하면 짧은 시간동안 페이지가 지워졌다가 다시 뜨는 깜빡임 현상이 나타난다. 이러면 웹 페이지의 동적인 화면 변경이 부드럽지 못하다는 인식을 줄 수 있다. 이와는 다르게 화면은 그대로인데 변경된 데이터만 반영해서 보여주는 방식이라면 사용자는 이 방식을 더 선호할 것이다. 즉, 페이지 새로고침(reload) 방식은 상대적으로 사용자의 경험성을 낮추는 단점이 있다. 페이지 새로고침 방식의 또 다른 단점은 서버로부터 전송되는 데이터의 양이 많다는 것이다. 페이지를 새로고침한다는 것은 화면의 모든 요소들을 처음부터 다시 서버로부터 받아온다는 것을 의미한다. 웹 페이지를 구성하는 여러 개의 html, css, js 파일들과 텍스트, 이미지 등의 리소스들까지 모두 다 서버로부터 다운로드받고, 이를 처음부터 재조립(?)하여 웹 페이지를 구성한다는 것이다. 단지 특정 변경된 데이터만을 원한 것이었는데 불필요하게 페이지 전체를 다시 내려받아야 하는 상황이다. 이는 불필요하게 트래픽에 부담을 주기에 비용이 쓸데없이 생긴다는 단점이 발생한다. 또한 이로 인해 사용자 입장에서는 웹 페이지가 느리게 로딩되는 느낌을 준다는 단점도 존재하게 된다. 

그러나 이러한 단점들은 AJAX 기술이면 해결된다. 페이지 전체를 새로고침하여 전체 페이지를 다시 받아오는 방식이 아닌, 그저 특정한 데이터만 가져오고 이를 반영하는 방식이기 때문이다. 깜빡임 현상도 없어져서 사용자 입장에서는 사용감이 더 좋아지고, 페이지 전체를 다시 내려받지 않기에 트래픽에 덜 부담을 주는 일석이조의 효과를 얻는다. 

만약 하나의 웹 사이트가 여러 개의 웹 페이지들로 구성되어 있는 경우, 기존에는 링크를 클릭하여 다른 웹 페이지로 이동하면 크롬과 같은 웹 브라우저 창의 url 창을 보면 url이 변경되는 것을 볼 수 있다. 그러나 ajax 기술을 잘 활용하면, 다른 페이지로의 이동 없이 단 하나의 웹 페이지에서도 다른 웹 페이지의 내용을 볼 수 있도록 꾸밀 수 있다(url도 변경되지 않는다). 역시 페이지 간 전환을 사용자는 느끼지 못하기에 사용자에게 있어 더 매력적인 웹 사이트를 제공할 수 있게 된다. 이렇게 단 하나의 페이지에서 여러 웹 페이지의 내용을 보여줄 수 있도록 한 웹 애플리케이션을 Single Page Application, SPA, 단일 페이지 애플리케이션이라 한다. 

참고로, 기존의 웹 페이지들은 사용자에게 정보를 제공하는 “문서”의 역할을 했다면, 이제는 여기에 마치 앱(app, application)처럼 사용자와의 실시간 상호작용으로 그 결과가 달라지는 형태의 웹 사이트가 많아지고 있는데, 이러한 형태의 웹을 웹앱(web app, web application)이라고 한다. 어떻게 보면 ajax 기술은 이러한 SPA, web app의 기반이 되는 기술이라고 볼 수도 있겠다. 

ajax의 장점은 여기서 그치지 않는다. 이후에 소개할 fetch() 라는 메서드는 최신 자바스크립트에서 제공하는 ajax 기술을 실현시켜주는 메서드인데, 해당 메서드 인자로 타 사이트의 url를 입력하여 타 사이트로부터 데이터를 얻어오는 기능만 하는 것은 아니다. 웹 사이트 제작 시 필요한 프로젝트 폴더 내 파일에도 요청을 보내 해당 파일로부터 정보를 얻어올 수도 있다(ajax 기술은 HTTP 요청을 하는 것이라, 이 상황의 경우 적어도 해당 웹 페이지를 자신의 컴퓨터 내에서 로컬 서버로 돌리는 상황이어야 한다. 즉, 어떻게든 서버가 존재하는 상태에서 이루어져야 하는 상황인 것이다. 파일에 직접 접근하여 파일을 직접 가져오는 방식은 보안의 이유로 안된다고 한다). 이를 이용하면 하나의 웹 문서 내에서 웹 페이지를 구성하는 프론트엔드 부분과 데이터 영역이 서로 섞여 있는 기존 방식에서, 이 둘을 분리함으로써, 만약 데이터가 변경되거나 추가, 삭제되더라도 HTML 파일을 건드리지 않고 해당 데이터가 든 파일만 조작하면 되고, 반대로 화면 구성을 바꿔야 한다면 데이터 영역까지 건드리지 않아도 된다. 이러한 영역 구분의 기능이 생기기에 유지보수에도 좋은 영향을 주는 기술이다. (이렇게 ajax 기술을 이용하여 프론트 영역과 데이터 영역을 구분하는 예시는 후에 보일 예정)

여기까지만 보더라도 ajax 기술이 생각보다 장점이 많아 대단한 기술이라고 여겨지는데, 실제로도 그런 것 같다. 구글이 처음으로 gmail과 구글 지도를 세상에 선보였을 때, 그 어떤 플러그인 없이 작동하는 웹앱 방식이여서 실제로 그 당시에 많은 관심과 화제를 불러일으켰다고 한다. 그 이전에는 예를 들어 지도 사이트의 경우 ActiveX 등의 별도의 플러그인이 있어야만 작동되었다고 한다. 이런 걸 보면 역사적으로도 엄청난 기술이었나보다. 

참고로, ajax에 asynchronous, 즉 비동기란 단어가 붙은 이유는 서버로부터 데이터 요청을 하고 그에 대한 응답이 올 때까지 웹 페이지 전체가 멈추는 게 아니라, 다른 웹 영역들은 여전히 비동기적으로 작동할 수 있기에 붙여진 이름이다. 

# fetch

최신 자바스크립트에서는 이러한 ajax 기술을 이용할 수 있는 것으로 `fetch()` 메서드를 제공한다. 사실 XMLHttpRequest 등 fetch가 아닌 다른 방법들도 있다. 다만 XMLHttpRequest는 예전 기술이고 복잡해서 fetch가 더 많이 쓰이는 것 같다. 아무튼 이 문서에서는 fetch에 집중하여 살펴보도록 하겠다. 

문법 형태는 다음과 같다. 

```jsx
let promise = fetch(url, [options]);
```

- url : HTTP 요청을 하고자 하는 URL
- options: HTTP method, header 등을 지정할 때 사용. 생략 시 HTTP method 중 GET 메서드로 진행되어 해당 url로부터 응답을 받는다.

`fetch()` 메서드를 호출하면 브라우저에서 해당 url로 요청을 보내고, 서버가 이에 응답하면 이 응답 정보를 가지는 Promise 객체를 반환한다. 이 때, 해당 Promise 객체의 내부 프로퍼티 result에는 Response라는 내장 클래스의 인스턴스가 담긴다. 개발자는 이 인스턴스를 이용하여 서버로부터 받은 응답에 대해 다양한 액션을 취할 수 있다. 

이 Response를 제대로 사용하려면 사실상 두 단계로 나눠 진행된다. 첫 번째는 Response 인스턴스, 즉 응답에 대한 메타 데이터들을 확인할 수 있는 단계이고, 두 번째 단계는 이 Response 인스턴스로부터 응답 본문을 받아 처리할 수 있는 단계이다. 

첫 번째 단계에서는 응답에 대한 HTTP 상태 코드, 응답이 제대로 왔는지, 응답 헤더 정보 등을 확인할 수 있는 단계이다. 다음은 HTML 문서 내에서 특정 사이트에 요청을 보내고, 그에 대한 응답의 메타 데이터들을 출력해보는 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        fetch('https://jsonplaceholder.typicode.com/posts/1')
            .then(response => {
                console.log(response);  // Response 객체
                console.log(typeof response);
                console.log(response.ok);  //응답에 대한 HTTP 상태 코드가 양호한지에 대한 boolean값
                console.log(response.status); // HTTP 상태 코드
            })
    </script>
</body>
</html>
```

예제 1-1

![예제 1-1 콘솔 출력결과](/images/2024-05-24/js-async-ajax-and-fetch/1.png)

예제 1-1 콘솔 출력결과

위 예제에선 응답 헤더 정보가 생략되었는데, 다음 예제에서 이를 보여주고 있다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <script>
        fetch(`https://jsonplaceholder.typicode.com/posts/1`)
            .then(response => {
                // 응답 헤더 정보 하나만 출력
                console.log(response.headers.get('expires'));
                
                // 응답 헤더 정보 전체를 순회하여 출력
                for([key, value] of response.headers) {
                    console.log(`${key}: ${value}`);
                }
            });
    </script>
</body>
</html>
```

예제 1-2

![예제 1-2 콘솔 출력결과](/images/2024-05-24/js-async-ajax-and-fetch/2.png)

예제 1-2 콘솔 출력결과

응답 본문을 받기 위한 두 번째 단계에서는 이를 위해, Response 객체로부터 다양한 형태의 응답 본문을 받아볼 수 있는 메서드들 중 하나를 호출해야 한다. 

- `response.text()` : 응답받은 컨텐츠를 텍스트로 반환.
- `response.json()` : 응답받은 컨텐츠를 JSON 형태를 파싱한 결과물을 반환.

이 외에도 다양한 형태의 응답 본문을 받아볼 수 있는 메서드들이 존재하며, 이에 대한 자세한 사항은 다음의 사이트를 참고.

[fetch](https://ko.javascript.info/fetch)

앞선 첫 번째 단계에서의 then (또는 await) 내부에서 `response.text()` 등의 메서드를 호출, 반환하는 코드를 통해 그 다음 then (또는 await)에서 이 응답 본문을 처리할 수 있게 된다. 

다음은 특정 사이트로부터 요청을 하여 받은 응답을 json 형태로 파싱하고, 이를 보기 좋게 웹 페이지에 출력하도록 하는 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #display {
            border: 1px solid black;
            padding: 1em;
        }
        #display > ul {
            list-style: none;
            padding: 0px;
        }
        #display > ul > li {
            border: 1px dashed blue;
            padding: 1em;
            margin: 1em;
        }
    </style>
</head>
<body>
    <div id="display">
        <ul></ul>
    </div>
    <script>
        const displayUl = document.querySelector('#display > ul');
        
        function addPostToDisplay(htmlText) {
            let newLi = document.createElement('li');
            newLi.innerHTML = htmlText;
            displayUl.append(newLi);
        }
        
        fetch('https://jsonplaceholder.typicode.com/posts')
            .then(response => response.json())
            .then(result => {
                for(let i = 0; i < 5; i++) {
                    let toHTML = `<p>userId: ${result[i].userId}</p>
                    <p>id: ${result[i].id}</p>
                    <p>title: ${result[i].title}</p>
                    <p>body: ${result[i].body}`;
                    addPostToDisplay(toHTML);
                }
            });
    </script>
</body>
</html>
```

예제 1-3

![예제 1-3 실행결과](/images/2024-05-24/js-async-ajax-and-fetch/3.png)

예제 1-3 실행결과

## option

앞서 fetch()의 두 번째 인자로 HTTP method와 요청 header를 정할 수 있다고 하였다. 주로 다음의 형태를 가지고 지정할 수 있다. 

```jsx
async function requestFunc(content) {
    let reponse = await fetch('url', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json;charset=utf-8',
            '헤더 프로퍼티': '헤더 값'
        },
        body: JSON.stringfy(content);
    });
}
```

method 프로퍼티에는 GET, POST 등의 HTTP method를 지정하여 특정 사이트로부터 컨텐츠를 얻어올 것인지 아니면 업로드할 것인지 등을 정할 수 있다. headers 프로퍼티에는 여러 가지 헤더 정보들을 넣어 요청할 수 있다. 다만, 보안상의 이유로 특정 헤더 정보들을 사용할 수는 없다고 한다. 금지된 헤더 목록에 대해서는 다음의 사이트들을 참고.

[fetch](https://ko.javascript.info/fetch#ref-287)

[Fetch Standard](https://fetch.spec.whatwg.org/#forbidden-header-name)

또한, 만약 POST 메서드를 이용하여 특정 사이트에 내용을 전송하는 방식이라면, body 프로퍼티를 추가 사용해야 한다. 해당 프로퍼티에는 문자열, FormData 객체, Blob 등의 요소들 중 하나를 선택하여 지정한다. 위 예제에서는 json 문자열 방식으로 설정하였다. 

참고로, 요청 본문이 문자열일 경우, Content-Type 헤더가 ‘text/plain;charset=UTF-8’로 기본 설정되는데, 이를 json으로 전송하고자 한다면 이를 ‘application/json’으로 설정해야 한다고 한다. 

# fetch()를 이용하여 프론트 영역과 데이터 영역 분리하기

이번에는 `fetch()` 메서드를 이용하여 프론트 영역과 데이터 영역을 분리하는 것을 예제를 통해 살펴보겠다. 기존에는 화면을 구성하는 프론트 영역을 담당하는 코드와 데이터 코드가 하나의 HTML 파일 내에 섞여 위치하였기에, 화면 구성만 바꾸려다가 자칫 데이터 영역까지 건드리고, 그 반대의 상황도 발생할 수 있었다. 이를 분리함으로써 유지보수를 원활히 할 수 있다. 

이를 살펴보기 위해 다음의 예제를 준비하였다. 다음은 단어들이 목록으로 배열되어 있고, 이 중 원하는 단어를 클릭하면 바로 아래에 해당 단어를 설명하는 글이 보여지는 이른바 간단한 백과사전 웹사이트의 코드이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        nav > ul > li {
            border: 1px solid black;
            width: 8em;
            margin-bottom: 1em;
        }
        article {
            border: 2px solid black;
            padding: 2em;
        }
    </style>
</head>
<body>
    <div id="container">
        <header>
            <h1>AI 백과사전</h1>
        </header>
        <nav>
            <ul>
                <li><a href="#고양이">고양이</a></li>
                <li><a href="#개">개</a></li>
                <li><a href="#우주">우주</a></li>
                <li><a href="#인공지능">인공지능</a></li>
                <li><a href="#자바스크립트">자바스크립트</a></li>
            </ul>
        </nav>
        <article>
            <p id="고양이">
                고양이 (Felis catus)

                고양이는 가장 인기 있는 애완동물 중 하나로, 사랑스러운 외모와 호기심 많은 성격으로 유명합니다. 고양이의 눈은 어둡고 반짝이며, 날렵한 몸집과 부드러운 털이 특징입니다. 
                
                고양이는 사회성 동물이며, 주인에 대한 애정을 표현하고 특별한 유대감을 형성합니다. 또한 독립적인 성향을 지니고 있어, 혼자 시간을 보내는 것을 즐기기도 합니다.
                
                이들은 사냥을 좋아하며, 밤에 활동적이고 경계심이 많은 성격을 가지고 있습니다. 고양이는 민첩하고 재빠르며, 뛰어난 시력과 청각을 보유하고 있어 사냥을 위한 뛰어난 능력을 갖추고 있습니다.
                
                고양이는 다양한 털 색깔과 무늬를 가지고 있으며, 페르시안, 스코티쉬 폴드, 시암, 러시안 블루 등 다양한 종류가 있습니다.
                
                애완동물로서 고양이는 주인에게 행복과 안정을 주는 반려동물로 사랑받고 있습니다. 
            </p>
            <p id="개">
                개 (Canis lupus familiaris)

                개는 오랜 역사를 가진 인간의 가장 친한 친구이자 애완동물로 유명한 동물입니다. 그들은 매우 사교적이며, 충실하고 온순한 성격으로 알려져 있습니다. 수많은 품종과 크기, 색상, 털질 등의 다양한 특징을 가지고 있으며, 이는 수천 년 동안 인간의 선택과 번식에 의해 형성되었습니다.

                개는 그들의 주인에 대한 충성심과 애정으로 유명하며, 가정에서는 친절하고 사랑스러운 반려동물로 자리 잡고 있습니다. 뿐만 아니라, 개는 경비, 구조, 안내, 사냥 등 다양한 분야에서 인간을 도와주는 일손으로 활약하고 있습니다. 또한 최근에는 재활치료나 정서적 지원을 위한 반려견으로서도 활용되고 있습니다.

                개는 사랑스럽고 충실한 동반자로서 우리 삶에 많은 기쁨을 주는 존재이며, 인간과의 조화로운 동반 생활을 이루어가고 있습니다.
            </p>
            <p id="우주">
                우주 (Universe)

                우주는 모든 존재와 물리적 대상들이 포함된 거대한 공간이자 시간의 집합체입니다. 별, 행성, 은하, 암흑 물질, 어두운 에너지 등으로 이루어져 있으며, 그 규모와 다양성은 우리의 상상력을 초월합니다. 인류는 오랜 기간 동안 천체들과 우주의 기원, 진화, 운동, 구조, 운명에 대한 깊은 이해를 추구해 왔습니다.

                우주의 탄생과 진화, 천체들 간의 상호작용, 우주의 미스터리 등에 대한 탐구는 인류의 영원한 과제 중 하나입니다. 천문학적 연구와 탐사를 통해 인류는 우주의 기원과 구조, 우주의 미스터리에 접근하고 있습니다. 또한 우주 탐사는 기술적인 혁신과 과학의 발전을 촉진시키며, 우리의 존재와 우주의 관계에 대한 깊은 통찰력을 제공합니다.

                우주에 대한 연구는 우리가 살아가는 세계에 대한 놀라운 통찰력을 제공하며, 우리의 역사와 미래에 대한 큰 질문들을 안겨줍니다. 또한 우주의 미스터리와 복잡성은 우리의 상상력을 자극하여 인류의 지적 호기심을 자극하고, 우리의 위치와 의미에 대한 깊은 사유를 이끌어냅니다.
            </p>
            <p id="인공지능">
                인공지능 (Artificial Intelligence, AI)

                인공지능은 컴퓨터 시스템이 인간의 학습, 추론, 결정, 언어 이해, 문제 해결 등의 지능적인 작업을 수행하는 능력을 가리킵니다. 이러한 시스템은 데이터를 분석하고 패턴을 식별하여 학습하며, 이를 기반으로 예측하고 결정을 내릴 수 있습니다.

                인공지능은 머신 러닝, 딥 러닝, 자연어 처리, 컴퓨터 비전 등 다양한 분야의 기술과 이론을 활용하여 구현됩니다. 머신 러닝은 데이터를 기반으로 패턴을 학습하고 예측하는 알고리즘을 개발하는 분야이며, 딥 러닝은 심층 신경망을 사용하여 복잡한 문제를 해결하는 기술입니다. 자연어 처리는 인간의 언어를 이해하고 해석하는 기술을 다루며, 컴퓨터 비전은 이미지와 비디오를 분석하고 해석하는 기술을 다룹니다.

                인공지능 기술은 의료 진단, 자율주행 자동차, 언어 번역, 음성 인식, 금융 예측, 제조업 등 다양한 분야에서 활발하게 적용되고 있습니다. 또한, 인공지능은 빅데이터와 클라우드 컴퓨팅과 같은 기술과 결합하여 더욱 강력한 성능을 발휘하고 있습니다.

                인공지능 기술은 빠르게 발전하고 있으며, 이에 따라 윤리적, 사회적 문제에 대한 논의도 활발히 이루어지고 있습니다. 인간과의 상호작용, 개인 정보 보호, 안전 문제 등에 대한 관심이 증폭되고 있으며, 이러한 문제들을 해결하기 위한 연구와 노력도 계속되고 있습니다.
            </p>
            <p id="자바스크립트">
                자바스크립트 (JavaScript)

                자바스크립트는 웹 페이지를 동적으로 만들고 제어하기 위해 사용되는 프로그래밍 언어입니다. 브라우저에서 실행되며, HTML과 CSS와 함께 웹 개발의 핵심 요소로 자리 잡고 있습니다. 자바스크립트는 클라이언트 측 웹 개발을 위해 사용되며, 최근에는 서버 측 프로그래밍에서도 널리 사용되고 있습니다.

                자바스크립트는 다양한 브라우저에서 지원되며, 강력한 기능과 유연성을 제공합니다. 이를 통해 사용자와 상호작용하고, 웹 페이지를 동적으로 변경하며, 데이터를 처리하고, 비동기적으로 서버와 통신하는 등 다양한 작업을 수행할 수 있습니다.

                또한, 자바스크립트는 Node.js와 같은 런타임 환경을 통해 서버 측 애플리케이션 개발에도 사용됩니다. 이를 통해 자바스크립트는 웹 프론트엔드와 백엔드 개발 모두에서 중요한 위치를 차지하고 있습니다.

                자바스크립트는 동적으로 변화하는 웹 환경에서 필수적인 언어로 자리 잡고 있으며, 더 나아가서는 모바일 애플리케이션, 데스크톱 애플리케이션, 게임 등 다양한 플랫폼에서도 사용되고 있습니다. 이러한 이유로 자바스크립트는 웹 개발 및 소프트웨어 개발 분야에서 널리 사용되고 있으며, 지속적으로 발전하고 있습니다.
            </p>
        </article>
    </div>
</body>
</html>
```

예제 2-1

![예제 2-1 실행결과](/images/2024-05-24/js-async-ajax-and-fetch/4.png)

예제 2-1 실행결과

그런데 브라우저 상의 해당 웹 페이지 화면을 보면 한 가지 문제가 있다. 원래는 여러 항목들 중 하나를 클릭하면 그 단어에 대한 설명만 나오길 원했다. 그런데 위 결과를 보면 모든 단어들에 대한 설명들이 그대로 모두 나와있음을 알 수 있다. 또한 코드 상에서도 문제점이 있다. 각 항목에 대한 설명글이 길어 HTML 코드 자체의 길이 또한 매우 길어졌으며, `<article>` 내의 `<p>` 태그, `<nav>` → `<ul>` 내의 `<li>` 태그가 내용만 다를 뿐 그 패턴은 똑같이 반복된다는 점이다. 이 모든 문제점들은 `fetch()` 메서드 하나로 해결할 수 있다. 

먼저 위 예제를 `fetch()` 메서드를 이용하여 재구성하려면 위 예제에서 데이터에 해당하는 정보들을 따로 분리하여 텍스트 파일에 저장하는 것이다. 다음은 재구성한 프로젝트 루트 폴더 내 구조이다.

```
root/
    navlist
    index.html
    fetch.js
    articles/
        개
        고양이
        우주
        인공지능
        자바스크립트
    
```

root 폴더 내에 화면 구성을 위한 index.html와 더불어, `fetch()` 메서드를 이용하여 프론트 영역과 데이터 영역을 이어줄 fetch.js 파일을 준비하였다. 그리고 articles 폴더 내에 각 단어와 그 단어에 대한 설명글을 담을 텍스트 파일들을 준비하였다. 여기에 각 파일에 단어에 대한 설명들을 넣어 저장하면 된다. 뒤에 .txt 확장자를 붙이지 않아도 텍스트로 인식하기에 확장자를 붙이진 않았다. 그리고 이 모든 단어들을 항목으로 나타내주는 navlist 파일도 생성하였다. 해당 파일 내에는 다음과 같이 모든 항목들을 쉼표로 구분하여 나타냈다. 

```
고양이,개,우주,인공지능,자바스크립트
```

navlist

앞서 살펴본 HTML 코드에서 데이터 영역에 해당하는 각 단어들과 설명글들을 각 텍스트 파일로 분리하였기 때문에, 새로 구성될 index.html 내 코드는 다음과 같다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script type="module" src="fetch.js"></script>
    <style>
        nav > ul > li {
            border: 1px solid black;
            width: 8em;
            margin-bottom: 1em;
        }
        article {
            border: 2px solid black;
            padding: 2em;
        }
    </style>
</head>
<body>
    <div id="container">
        <header>
            <h1>AI 백과사전</h1>
        </header>
        <nav>
            <ul></ul>
        </nav>
        <article></article>
    </div>
</body>
</html>
```

index.html

`<nav>`의 `<ul>` 태그 내부와 `<article>` 태그 내부가 모두 비어있다는 것을 볼 수 있다. 이 빈 자리는 대신 fetch.js가 채워줄 것이다. 

다음은 fetch.js 파일 내 코드이다.

```jsx
async function getNavList() {
    let result= await fetch('navlist').then(response => response.text());
    return result.split(',');
}

function constructNav(ulElement, navListStr, articleElement) {
    for(let oneList of navListStr) {
        let li = document.createElement('li');
        li.innerHTML = `<a href="#${oneList}">${oneList}</a>`;
        li.addEventListener('click', async () => {
            await addArticleToDom(articleElement, oneList);
        });
        ulElement.append(li);
    }
    return ulElement;
}

async function getOneArticle(title) {
    let articleText = await fetch(`articles/${title}`);
    if(!articleText.ok) {
        throw new Error(`${articleText.status} HTTP request failed.`);
    }
    return articleText.text();
}

async function addArticleToDom(articleElement, title) {
    let articleText;
    try {
        articleText = await getOneArticle(title);
    } catch (e) {
        articleElement.innerHTML = `해당 글을 불러오는 데에 실패했습니다. ㅠㅠ`;
        console.log(e);
        return;
    }
    articleElement.innerHTML = `<p>${articleText}</p>`;
    return articleElement;
}

async function main() {
    const navUl = document.querySelector('nav > ul');
    const articleElement = document.querySelector('article');

    let navList = await getNavList();
    constructNav(navUl, navList, articleElement);
    articleElement.innerHTML = `<h3>AI 백과사전에 오신 것을 환영합니다!</h3>`;
}

main();
```

fetch.js

해당 파일에는 navlist 파일로부터 단어들을 불러와 이를 화면의 `<nav>` → `<ul>` 내부에 삽입하고, 해당 목록에서 특정 단어를 클릭하면 그 단어에 해당하는 설명글을 article 폴더 내부에서 불러와 이를 `<article>` 태그 내부에 삽입하는 기능들이 들어가 있다. 

이제 index.html을 브라우저에서 실행시켜보면 다음의 결과를 얻는다.

![실행결과1. 첫 화면](/images/2024-05-24/js-async-ajax-and-fetch/5.png)

실행결과1. 첫 화면

![실행결과2. 목록에서 “개”를 클릭했을 때의 화면. “개”에 대한 설명글만 나오는 것을 볼 수 있다.](/images/2024-05-24/js-async-ajax-and-fetch/6.png)

실행결과2. 목록에서 “개”를 클릭했을 때의 화면. “개”에 대한 설명글만 나오는 것을 볼 수 있다.

![실행결과3. 목록에서 “인공지능”을 클릭했을 때의 화면. 역시 “인공지능”에 대한 설명글만 나오는 것을 볼 수 있다. ](/images/2024-05-24/js-async-ajax-and-fetch/7.png)

실행결과3. 목록에서 “인공지능”을 클릭했을 때의 화면. 역시 “인공지능”에 대한 설명글만 나오는 것을 볼 수 있다. 

![실행결과4. 기존의 웹 문서에 “스마트폰”이란 단어를 navlist에 추가하고, 대신 일부러 articles 폴더 내부에는 그에 해당하는 글을 추가하지 않았을 때의 결과. 결과적으로 없는 파일을 fetch() 에서 요청한 것이므로 404 not found 에러가 뜬다. 여기서는 해당 에러가 뜨면 웹 화면의 <article> 태그에 특정 문구를 출력하도록 고안하였다.](/images/2024-05-24/js-async-ajax-and-fetch/8.png)

실행결과4. 기존의 웹 문서에 “스마트폰”이란 단어를 navlist에 추가하고, 대신 일부러 articles 폴더 내부에는 그에 해당하는 글을 추가하지 않았을 때의 결과. 결과적으로 없는 파일을 `fetch()` 에서 요청한 것이므로 404 not found 에러가 뜬다. 여기서는 해당 에러가 뜨면 웹 화면의 `<article>` 태그에 특정 문구를 출력하도록 고안하였다.

이로서 애초에 원했던 목적을 달성하였다. 즉, 여러 단어들 중 하나만 클릭하면 그에 대한 단어만 나오도록 하는 기능을 구현할 수 있었고, 그와 동시에 프론트 영역과 데이터 영역을 구분하여 가독성과 유지보수성을 높일 수 있었다. 만약 html, js을 하나도 모르는 사람이 컨텐츠를 해당 웹 사이트에 업데이트하려고 한다 해도 그저 articles 폴더 내에 새 파일을 추가하고, 해당 단어를 navlist에 추가하면 끝이다. 굳이 HTML 파일이나 JS 파일을 따로 건드릴 필요가 없게 된다. 설명글에 대한 수정, 삭제 등도 이와 같은 원리로 진행되기 때문에 한층 수월해진다. 

또한, 위 코드를 브라우저 상에서 실행시켜보면, 브라우저의 url창의 주소를 보면 항목을 누를 때마다 맨 끝의 주소만 달라질 뿐, index.html 라는 주소 자체는 변경되지 않는 것을 볼 수 있다. 만약 위 예제들을 자바스크립트의 fetch()를 사용하지 않고 그저 여러 개의 HTML 파일들로만 구성했다면 어떻게 되었을까? 즉, 다음과 같이 루트 폴더들을 구성하였다면,

```jsx
root/
    index.html
    dog.html
    cat.html
    space.html
    ai.html
    javascript.html
```

그리고 index.html에 있는 각 항목들에 `<a>` 태그를 이용하여 다른 html 파일들을 각각 연결하는 방식이었다면, 웹 페이지 상에서 항목들을 누를 때마다 url창의 주소 자체가 변경되었을 것이다. 예를 들어 index.html 에서 “우주” 단어를 클릭하면 url 창에는 index.html 에서 space.html 으로 바뀌었을 것이다. 즉, 한 페이지에서 다른 페이지로 이동한 것이다. 그러나 `fetch()`를 이용한 방식의 웹 페이지는 어떤 항목을 누르더라도 index.html에 고정되어 있다. 그러면서도 각 항목에 대한 설명글이 각각 다르게 화면에 표시되었다. 즉, 이를 통해 우린 단일 페이지 앱, SPA를 구현했다고 볼 수 있다. 즉 사용자가 어딜 클릭하느냐에 따라 다른 내용들을 하나의 페이지 내에서 표현할 수 있는 구조의 웹 페이지를 구현하게 된 셈이다. 이는 앞서 ajax 챕터에서 설명했던 장점인 깜빡임 현상이 사라져 사용자 입장에서 시각적으로 더 편하게 즐길 수 있다는 점을 확보하게 된 셈이다. `fetch()` 하나로 여러 개의 장점들을 한꺼번에 잡게 된 것이다. 

---

References

[1] [fetch](https://ko.javascript.info/fetch)

[2] AJAX 강의 - 생활코딩

[Ajax - 생활코딩](https://opentutorials.org/course/3281)

[3] AJAX 설명

[](https://namu.wiki/w/AJAX)

[4] [AJAX 데이터 요청하는 방법들 (fetch)](https://velog.io/@kjwboa/AJAX-데이터-요청하는-방법들-fetch)

[5] SPA (Single Page Application)

[https://namu.wiki/w/SPA#Single Page Application](https://namu.wiki/w/SPA#Single%20Page%20Application)

[6] SPA 관련 참고 글

[SPA란? 웹 개발 트렌드 SPA의  특징부터 구현 방법까지 모두 알려드립니다! I 이랜서 블로그](https://www.elancer.co.kr/blog/view?seq=214)

[7] Ajax 관련 참고 글

[Ajax 란, 활력 있는 웹페이지를 만들기 위한 필수 기능 I 이랜서](https://www.elancer.co.kr/blog/view?seq=182)