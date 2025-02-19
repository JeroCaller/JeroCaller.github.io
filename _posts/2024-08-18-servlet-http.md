---
title: "[Servlet] HTTP"
category: "Servlet & JSP"
tag: ["Servlet", "HTTP", "URL"]
---

# 참고할만한 내 글들

[웹 클라이언트 (Web clients)](/python/web/web-client-and-python/) 

[웹 서비스와 자동화](/python/web/web-service-and-automation/) 

# HTTP

HTTP(Hypertext Transfer Protocol)은 웹에서 클라이언트와 서버가 요청(request)과 응답(response)을 교환하기 위한 통신에 사용하는 통신 규약(프로토콜)이다. 

프로토콜(Protocol)은 “통신 규약”을 의미하며, 통신 장비들 사이에서 정보를 주고 받기 위해 합의된 메시지의 형식이자 규칙 체계이다. 

이러한 HTTP를 통해 인터넷 상에서 HTML, 텍스트, 이미지 등의 다양한 자원들을 요청 및 응답할 수 있다. 

웹 앱은 웹 서버에 저장되며, 사용자가 웹 서버에 저장된 웹 앱과 매핑된 url을 요청하면 웹 서버가 그 안에 저장된 웹 앱 페이지를 응답으로 제공하는 형태이다. 

## 특징

HTTP 프로토콜에는 대표적으로 다음의 두 가지 특징이 있다.

- 비연결성(connectionless) : 클라이언트가 서버에 요청을 하면 서버와 잠시 연결되었다가, 서버에서 응답을 전송하면 그 즉시 연결이 끊어지는 특성이다. HTTP 프로토콜에는 비연결성이 있어서 요청 및 응답을 주고 받을 때에만 인터넷 상으로 연결되어 있고, 통신이 끝나면 연결도 끊기는 특징을 가지고 있다.
- 무상태(stateless) : 클라이언트가 서버에 요청을 할 때 그와 함께 어떠한 데이터들을 전달할 때가 있는데, 이러한 데이터를 상태 정보라 한다. 로그인으로 예를 들면 사용자가 입력한 아이디와 비밀번호가 상태 정보가 된다. HTTP로 통신할 때 서버는 클라이언트에서 전송한 상태 정보를 유지하지 않는데, 이러한 특성을 무상태라 한다. 
비유를 들면, 새로 살 스마트폰에 대해 상담받으려고 전화를 하여 상담원과 대화를 하는데, 스마트폰의 특정 기능에 대해 더 자세하게 물었을 때 해당 전문가가 전화를 바꿔 대신 답해주는 것이다. 전화를 바꾼 전문가는 이 상담자가 처음부터 어떤 얘기를 했는지까지는 모르므로 상담자는 맨 처음 전화했던 상담원에게 했던 얘기를 처음부터 다시 해야 하는 것이다. 
반대로 클라이언트가 전송한 상태 정보들을 어떠한 목적을 위해 유지하면서 사용자가 원하는 요청을 처리하는 것을 상태 정보 유지(stateful)라 한다. HTTP는 기본적으로 stateless 특성을 가지므로 만약 상태 정보를 유지해야한다면 다음의 기술들을 고려해야한다.
    - 쿠키(cookie): 클라이언트가 이전에 제공했던 정보들을 또 제공하려고 할 때 그 클라이언트를 고유하게 판별하기 위한 목적으로 서버가 클라이언트에게 전송해주는 상세한 정보들. 예) 단골 손님 리스트를 작성하고 해당 단골 손님들의 각각의 요청들을 기록하여 다음에 해당 손님이 또 올 때 그에 맞는 서비스를 제공하는 것.
    - 세션 (session): 클라이언트가 제공한 정보를 서버가 저장하는 곳.

# HTTP 요청

앞서 HTTP에 대해 설명할 때 잠깐 언급했듯, HTTP라는 프로토콜을 이용하여 클라이언트와 서버가 서로 통신을 주고 받는다. 클라이언트는 서버에게 “요청”을 하고, 서버는 그에 맞는 “응답”을 클라이언트에게 전송한다. 따라서 HTTP 통신을 “요청”과 “응답”으로 나눠서 볼 수도 있을 것 같다. 이 챕터에서는 HTTP 요청에 대해 살펴보자. 

## URL

웹 서버에는 사용자들이 웹 사이트에 접근할 수 있도록 웹 페이지를 구성하는 HTML, CSS, JS, 이미지 등의 여러 자원(resource)들을 보관하고 있다. 사용자가 이 웹페이지에 접근하기 위해선 해당 자원들의 위치를 알아야 한다. 예를 들어 어떤 사이트의 메인 페이지에 접근하기 위해선 그 메인 페이지를 구성하는 HTML 파일의 위치를 알아야할 것이다. URL(Uniform Resource Locator)은 이렇게 네트워크 상에서 자원(resource) (동영상, 이미지, 글 등의 통합 자원. 유튜브 페이지 접속 시 보이는 동영상들도, 구글 이미지 검색 시 나오는 모든 사진들도, 인터넷 기사에 쓰인 글들 모두가 다 리소스이다)을 고유하게 식별해주는 역할과 동시에 그 리소스의 위치까지 표현해주는 규약이다. 

보통 URL은 웹 사이트의 주소로 알려져 있는데, [www.google.com](http://www.google.com/) 이렇게만 쓰면 이것은 URL이 아닌 URI로, 단순한 식별자가 된다. 반면 이 앞에 특정 네트워크 프로토콜, 즉 https, http, sftp, smp등 도 같이 표기하여 https://www.google.com 이렇게 쓰면 이것이 URL이 된다. 프로토콜(https)을 주소 앞에 명시함으로써 해당 사이트의 위치를 알 수 있기에 URL이 되는 것이다. URL = 식별자 + 위치

참고로 URI (Uniform Resource Identifier)은 “통합 자원 식별자”의 뜻을 가진다. 리소스들을 서로 구분하고 식별하기 위해 붙이는 일종의 “이름” 같은 것이다. 통일된 방식으로 리소스들을 식별하기에 Uniform이란 단어가 들어가있다. 결론적으로 URI는 인터넷 상의 리소스 자체를 고유하게 식별해주는 문자열 시퀀스이다. URI = 식별자. URI가 더 넓은 개념이므로 URL을 포함하는 관계를 가진다. 

URL의 형태는 다음과 같다.

```
http://localhost:8080/MyFirstWebApp/folder/login.html

(1)protocol:(2)//(3)domain:(4)port/(5)webapp/(6)directory/(7)file.html
```

| 번호 | 이름 | 예 | 설명 |
| --- | --- | --- | --- |
| 1 | 프로토콜(protocol) | http | 서버에 요청 시 사용하는 프로토콜 |
| 2 | 프로토콜 구분자  | // | 프로토콜과 도메인 이름을 구분하는 구분자. |
| 3 | 호스트(도메인) | localhost | 웹 서버가 설치된 컴퓨터. localhost는 현재 사용하고 있는 로컬 컴퓨터를 의미 |
| 4 | 포트 | 8080 | 컴퓨터 내에서는 여러 개의 프로그램들을 작동시킬 수 있다. 이러한 프로그램들을 구별해주는 고유한 주소를 포트라 한다. 서버(호스트) 컴퓨터에서 클라이언트와 통신하기 위한 세부 주소라 할 수 있다. |
| 5 | 웹 앱 | MyFirstWebApp | 웹 앱 이름. 여기서는 프로젝트명 |
| 6 | 디렉토리 | folder | 웹 앱 내 디렉토리. 없다면 생략 가능. |
| 7 | 파일 | login.html | 웹 앱 내부에 있는 특정 파일명 |

포트 번호 이후(5 이후)가 URI에 해당한다. 

이렇게 클라이언트가 서버에 HTTP 요청을 할 때 URL을 입력함으로써 요청을 한다. 앞서 `http://localhost:8080/MyFirstWebApp/login.html` 주소를 브라우저의 url 입력창에 입력하고 엔터키를 입력한 것 자체가 “login.html 페이지 보여주세요”라고 서버에 HTTP 요청을 한 것이다. 

브라우저의 url 창에 직접 특정 사이트의 url을 입력하고 엔터키를 치는 행위는 기본적으로 GET 방식으로 요청을 하는 것이다. 

## HTTP 요청 프로토콜 구조

HTTP 요청 프로토콜에는 다음의 요소들로 구성되어 있다.

- start-line
- message-header
- message-body

```
start-line
message-header
CRLF
message-body
```

CRLF(Carriage Return Line Feed)는 헤더와 바디를 구분하는 용도의 한 줄의 공백을 의미한다. 

- start-line

맨 첫 줄에 나오며, 요청방식-요청 URI - 프로토콜명 및 버전이라는 세 가지 정보를 보여준다. 형태는 다음과 같다.

```
GET MyFirstWebApp/login.html HTTP/1.1

요청 방식(method) 요청 URI 프로토콜명/버전
```

요청 방식은 HTTP Method라고도 하며, 클라이언트가 서버에 리소스에 대해 어떤 요청을 할 것인지에 대한 것이다. CRUD(Create, Read, Update, Delete)로 구성되어 있으며, 정확한 HTTP Method 이름은 각각 POST, GET, PUT, DELETE이다. 

| CRUD | HTTP Method | 설명 |
| --- | --- | --- |
| CREATE | POST | 등록(생성). 새 리소스를 생성하는 요청 |
| READ | GET | 조회. 특정 리소스를 읽어오는 요청 |
| UPDATE | PUT | 수정. 특정 리소스의 내용을 수정하는 요청. |
| DELETE | DELETE | 삭제. 특정 리소스를 삭제하는 요청 |

이 외에도 여러 요청 방식이 있다고 한다. 

요청 URI은 앞서 URL에서 설명했듯, 웹 앱 내의 특정 리소스를 요청하기 위한 해당 리소스의 위치를 말한다. URL에서 포트 번호 이후의 주소가 URI에 해당한다. 

다음은 앞서 만든 login.html의 HTTP 요청에 대한 start-line이다. 크롬 기준 키보드에서 F12키를 누르면 개발자 도구가 뜨는데, 거기서 [Network] 탭을 누른다. 아래에 아무것도 뜨지 않으면 새로 고침을 한 번 하면 서버로부터 응답받아 전송받은 여러 리소스들이 보일 것이다. 그 중 아무거나 하나를 클릭하면 요쳥 및 응답 헤더들을 볼 수 있다. 

```
GET /MyFirstWebApp/login.html HTTP/1.1
```

위 start-line은 해당 URI에 대해 HTTP/1.1 버전의 프로토콜을 이용하여 GET Method, 즉, 해당 리소스를 조회하겠다는 요청을 의미한다. 

- message-header

브라우저와 관련된 정보들이 키(key):값(value) 쌍의 형태로 정리된 영역이다. 대략 다음과 같은 형태를 띤다. 

```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Cache-Control: max-age=0
Connection: keep-alive
Host: localhost:8080
If-Modified-Since: Sun, 18 Aug 2024 07:58:49 GMT
If-None-Match: W/"700-1723967929097"
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36
sec-ch-ua: "Not)A;Brand";v="99", "Google Chrome";v="127", "Chromium";v="127"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
```

다음은 대표적인 헤더 정보들이다.

| key | value |
| --- | --- |
| Host | 요청 전송 대상이 되는 서버의 호스트 이름 및 포트 번호 |
| User-agent | 브라우저의 이름 및 버전 정보 |
| Accept-Encoding | 브라우저가 처리할 수 있는 압축 방식 |
| Accept-language | 브라우저가 처리할 수 있는 언어 목록 |
   
- message-body

message-body에는 사용자가 입력한 정보들이 설정된다. 이 때, HTTP 요청 Method를 어떤 것을 사용했느냐에 따라 message-body에 설정되는 정보가 달라진다. POST인 경우 사용자가 입력한 정보들이 message-body에 설정되나, GET인 경우 해당 영역에 아무것도 설정되지 않는다. 

# HTTP 응답

클라이언트로부터 전송받은 HTTP 요청 프로토콜로부터 서버는 정보를 추출하여 요청을 처리하고, HTTP 응답 프로토콜을 생성하여 처리 결과를 다시 클라이언트에 전송한다. 여기에서도 HTTP 응답 프로토콜에 구조가 있는데, HTTP 요청 프로토콜과 거의 흡사하다. 

```
status-line
message-header
CRLF
message-body
```

- status-line

status-line에는 HTTP 버전, 상태 코드(status code), 상태 메세지(status text)로 구성된 문자열이 설정된다. 

```
HTTP/1.1 304 Not Modified
```

상태 코드는 요청에 대한 처리 결과를 나타내는 정보이다. 대체로 다음으로 분류된다. 

- 1xx (정보) : 서버가 요청을 받긴 했지만 클라이언트에 대한 추가 정보를 가지고 있다는 뜻.
- 2xx (성공): 연결에 성공했다는 뜻. 200이 아닌 다른 200번 대 코드는 그 외에 다른 행동을 수행한다는 뜻이다.
- 3xx (재지정, redirection): 해당 리소스가 이동되어서 클라이언트에게 새 URL를 반환함
- 4xx (클라이언트 에러): 클라이언트 쪽에서 문제 발생. 404 (not found)가 유명한 예. 418 (I’m a teapot)은 만우절 농담이라고 한다.
- 5xx (서버 에러): 서버 쪽에서 문제 발생. 502 (bad gateway)는 웹 서버와 백앤드 어플리케이션 서버 간에 연결이 끊겼을 때 발생한다.

그리고 상태 메시지는 이러한 상태 코드를 영어로 간단하게 설명한 텍스트라 보면 되겠다. 

대표적인 상태 코드 및 상태 메시지에는 다음이 존재한다.

| 상태 코드 | 상태 메시지 | 뜻 |
| --- | --- | --- |
| 200 | OK | 정상적 처리 |
| 403 | Forbidden | 클라이언트가 요청한 파일에 접근 불가 |
| 404 | Not Found | 클라이언트가 요청한 파일이 서버에 존재하지 않음. |
| 405 | Method Not Allowed | 서버에서 미지원하는 HTTP method를 클라이언트가 요청함 |
| 500 | Internal Server Error | 요청한 기능을 서버에서 처리하는 과정에서 예외(Exception)가 발생함. |
   
- message-header

주로 서버가 클라이언트에 응답으로 전송하는 문서 정보가 설정된다. 

- message-body

클라이언트가 요청한 실질적인 문서가 포함된다. 이미지 파일을 요청했다면 이미지의 바이너리 정보가, HTML 파일 요청 시 HTML 파일 내용이 담긴다. 브라우저는 전송받은 응답의 message-body로부터 문서를 추출하여 화면에 출력하는 것이다. 

# GET과 POST

HTML의 form 태그에서 GET과 POST가 사용될 때 작동 방식이 각자 다르다. 이를 확인해보자. 

1. login.html 파일 내 form 태그의 action을 “processLogin”으로, method를 “get”으로 바꾼다. 그 후, 아이디 입력란과 비밀번호 입력란에 해당하는 input 태그에 각각 name=”id”, name=”password”를 입력한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>JeroCaller의 홈페이지 로그인</title>
    <link rel="stylesheet" href="./style.css" />
  </head>
  <body>
    <div id="container">
      <h1>Welcome!</h1>
      <img src="./images/jerocaller.jpg" width="50%" />
      <form action="processLogin" method="get"> <!-- 변경된 코드 -->
        <fieldset>
          <legend>로그인</legend>
          <label for="id">아이디 : </label>
          <input type="text" id="id" name="id" /> <!-- 변경된 코드 -->
          <label for="password">비밀번호 : </label>
          <input type="password" id="password" name="password" /> <!-- 변경된 코드 -->
          <input type="submit" value="로그인" />
        </fieldset>
      </form>
    </div>
  </body>
</html>
```

1. 해당 웹 페이지에서 새로 고침을 한 뒤, 아이디와 비밀번호 입력란에 각각 아무거나 적은 뒤, “로그인” 버튼을 눌러보자. 그러면 다음과 같은 결과를 얻을 것이다. 
    
    ![1.PNG](/images/2024-08-18/servlet-http/1.png)
    

form 태그의 action 속성에 지정한 processLogin에 해당하는 리소스가 서버에 없기에 위와 같이 에러 화면이 뜬 것이다. 그런데 url 입력창을 잘 보면 앞서 입력한 아이디와 패스워드가 고스란히 보인다. 이렇듯, GET 방식은 서버에 전달할 URI 뒤에 물음표(?)로 시작하는 키-값 형태의 사용자가 입력한 정보들을 덧붙여서 서버에 전달한다. 이렇게 사용자가 입력한 정보들을 URI 뒤에 특정 형태로 덧붙여지는 문자열을 쿼리 문자열(query string)이라 한다. 쿼리 문자열은 물음표로 시작되며, 그 뒤에 키-값 쌍의 데이터들을 & 기호를 구분자로 하여 나열하는 방식이다. 

GET 방식에서는 사용자가 입력한 정보가 URL에 고스란히 나타나기에 로그인과 같이 민감한 정보를 입력해야 하는 곳에서는 사용해선 안되는 방식이다. 

GET 방식에서는 사용자가 입력한 정보가 url 및 start-line에 명시되며, message-body에는 아무런 정보도 기록되지 않는다. 

> input 태그에 name 속성이 지정되면 위와 같이 url에 해당 정보가 고스란히 보인다. 그러나 name 속성이 없으면 물음표만 덧붙여질 뿐 그 뒤에 아무런 정보도 덧붙여지지 않는다. 그런데 이건 반대로 말하면 name 속성이 없다면 서버에 제대로 해당 정보들을 전달하지 못한다는 뜻이 아닐까 싶다. 물론 이건 내 추측일 뿐이다.
> 

이번에는 form 태그의 method 속성값을 “post”로 바꿔보고 다시 실행해보자. 그러면 다음의 결과를 얻는다. 

![2.PNG](/images/2024-08-18/servlet-http/2.png)

post 방식의 요청은 사용자가 입력한 정보들을 url에 덧붙이지 않는다. 대신 message-body에 해당 정보들을 기록한다. 따라서 적어도 사용자가 쉽게 볼 수 있는 url창에 사용자의 민감한 정보가 노출되지 않게 하면서 HTTP 요청을 전송하려면 POST 방식으로 하는 것이 좋다는 것을 알 수 있다. 

---

References

[1] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[2] [[간단정리] HTTP Request/Response 구조](https://hahahoho5915.tistory.com/62)