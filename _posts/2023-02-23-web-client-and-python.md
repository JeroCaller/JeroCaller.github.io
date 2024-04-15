---
layout: single
title:  "[Python][Web] 웹 클라이언트"
categories: ["Python", "Web"]
tag: ["Python", "Web"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 용어 정리

- HTTP (Hypertext Transfer Protocol): 요청(request)과 응답(response)을 서로 교환하기 위한 웹 클라이언트와 서버에 관한 설명서. 웹 데이터 교환의 표준 프로토콜이다.
- HTML (Hypertext Markup Language): 클라이언트의 요청에 따른 서버의 응답 결과를 보여주는 포맷 중 하나.
- URL (Uniform Resource Locator): 고유하게 서버를 표현하고 그 서버 안에 있는 항목들을 표현해주는 방법
- Client (클라이언트): 사이트에 접속하는 사용자
- Server (서버): 웹사이트나 웹 API에 대한 데이터를 제공하는 자
- Web API and Service: 보이는 웹페이지가 아닌 다른 방법으로 데이터를 교환하는 방식 (원문: to interchange data in other ways than viewable web pages)
- TCP/IP (Transmission Control Protocol/Internet Protocol): 인터넷의 저수준 네트워크 (원문: the low-level network plumbing of the Internet). 컴퓨터간에 바이트(byte)들을 옮겨주지만 그 바이트들의 의미에 관해서는 신경쓰지 않는다. 고수준(high-level) 프로토콜은 특정 목적을 위한 문법 정의가 되어있어서 바이트 의미 해석을 할 수 있다.
- 프로토콜(Protocol): “통신 규약”을 의미하며, 컴퓨터를 비롯한 통신 장비들 사이에서 정보를 주고 받는 양식 및 규칙 체계.
- 쿠키(cookie): 클라이언트가 이전에 제공했던 정보들을 또 제공하려고 할 때 그 클라이언트를 고유하게 판별하기 위한 목적으로 서버가 클라이언트에게 전송해주는 상세한 정보들.  예) 단골 손님 리스트를 작성하고 해당 단골 손님들의 각각의 요청들을 기록하여 다음에 해당 손님이 또 올 때 그에 맞는 서비스를 제공하는 것.

# 웹 원리

웹은 클라이언트-서버 시스템이다. 클라이언트는 서버에 요청(request)을 한다. 이 때 TCP/IP 연결을 한 후 URL이나 다른 정보들을 HTTP를 통해 전송한다. 그러면 그 결과로 응답(response)을 받는다. 응답 포맷도 HTTP로 정의된다. 여기에는 요청 상태가 포함되며, 요청이 성공할 경우 응답으로 데이터나 포맷을 제공한다. 

웹 클라이언트의 대표적인 예는 웹 브라우저(web browser)이다. 이는 다양한 방식으로 HTTP 요청을 하는데, 가령 사용자가 직접 URL을 주소창에 쳐서 그 사이트로 간다든지, 링크를 눌러서 그 사이트로 가는 방법 등이 있다. 이 요청에 대한 응답으로 해당 웹사이트를 보여주는 것이다. 네이버 URL을 주소창에 검색을 하는 것이 요청이고, 그에 대한 응답으로 네이버 첫 화면이 보이는 것이다. 요청에 의해 얻은 결과물들을 웹사이트로 보이기 위해서 흔히 HTML, JavaScript, CSS, 사진 등이 사용된다. 하지만 요청에 대한 응답은 포괄적으로 꼭 사용자에게 전시하기 위한 목적 뿐만 아니라 여러 형태의 데이터로 얻을 수 있다. 

웹 통신에는 한 가지 특징이 있는데, 클라이언트와 서버 간 통신에 대한 상태에 대해서 상태 유지 (stateful)와 무상태(stateless)라는 것이 존재한다. 

stateful이란, 서버와 클라이언트간 상태를 보존하는 것을 의미한다. 즉, 서버와 클라이언트 간에 주고받은 데이터들을 저장하는 것이다. 이를 통해 바로 이전에 주고 받은 데이터들을 기억하여 다음 행동을 취할 수 있다. 보통 이러한 데이터들은 브라우저의 쿠키(cookie) 또는 서버의 session에 저장된다. 예) 한 번 로그인 해놓으면 어떤 항목으로 이동해도 로그인 상태가 그대로 유지된다. 왜냐면 서버가 클라이언트의 아이디 및 패스워드라는 상태를 계속 기억하여 보존, 유지하기 때문. stateful의 한 가지 문제점은 이용하던 서버가 어떠한 이유로 못 쓰게 되어 다른 서버를 이용해야 할 경우, 기존에 있던 상태들은 해당 서버에만 존재했던 것이라서 다른 서버에서 그대로 이용하지 못한다는 점이다. 비유를 들면, 새로 살 스마트폰에 대해 상담받으려고 전화를 하여 상담원과 대화를 하는데, 스마트폰의 메모리 기능에 대해 더 자세하게 물었을 때 해당 전문가가 전화를 바꿔 대신 답해주는 것이다. 전화를 바꾼 전문가는 이 상담자가 처음부터 어떤 얘기를 했는지까지는 모르므로 상담자는 맨 처음 전화했던 상담원에게 했던 얘기를 처음부터 다시 해야 하는 것이다. 또 하나의 문제점은, 데이터를 저장해야 하는데 이 데이터를 저장하는 메모리도 물리적 한계가 있으므로 무한정 새로운 데이터들을 보존하지는 못한다. 콘서트 티켓팅 사이트에 생각보다 많은 유저들이 몰릴 경우 애초에 데이터 저장 용량이 정해져 있어서 누군가 그 웹사이트를 빠져나가야 새로 들어온 유저에 대한 정보를 저장할 수 있다. (마치 맛집에서 웨이팅하는 것 같다) ⇒ 그래서 실제로는 클라이언트 상태 데이터를 캐시 서버에 저장하여 이용한다고 한다. 

반대로 stateless는 서버-클라이언트 간 상태를 보존하지 않는 것으로, 즉 서버-클라이언트 간 주고받은 데이터들을 저장하지 않는다. 여기서 클라이언트가 모든 데이터들을 서버에 전송하여 요청하고, 서버는 그저 그 요청에 대해 응답만 할 뿐이다. 앞서 stateful과는 달리 서버는 상태를 보관, 유지할 의무가 없기에 어떤 서버를 이용하다가 다른 서버를 이용해야하는 경우에도 stateful에서의 문제점이 발생하지 않는다. 그래서 이 경우 서버 확장에 용이하다. 그러나 상태 보관 및 유지의 기능이 없기에 서버에 요청할 때마다 처음부터 모든 정보를 새로 제공해줘야 한다. 그러다보니 상대적으로 더 많은 데이터들을 전달해야만 한다. 

HTTP는 대표적인 stateless의 예이다. 즉, HTTP 연결을 할 때마다 다른 어떠한 요소와도 독립적이다. 이로 인해 웹 동작 자체는 쉬워지지만 다른 것들을 수행하기가 어려워진다. 여기서 다루기 어려워지는 개념들의 예로 다음이 있다. 

- 캐싱 (caching): 변하지 않는 원격 내용들은 웹 클라이언트에 의해 저장되어야 하고, 이를 통해 무언가를 중복으로 다운로드 받는 것을 방지해준다.
- 세션 (session): 클라이언트가 제공한 정보를 서버가 저장하는 곳.
- 인증 (authentication): 사용자가 누구인지 확인하는 과정. 로그인을 위해 아이디와 패스워드를 입력하는 것이 대표적 예

# telnet으로 테스트해보기

그러면 실제 서버에 연결하여 웹을 체험해보자. 명령 프롬프트 창 (cmd)에서 사용할 수 있는 telnet을 이용해 테스트해보겠다. 

바탕화면에서 윈도우키 + R을 눌러 “실행” 창을 활성화 시킨다. 입력창에 cmd라고 치고 확인을 누르면 명령 프롬프트 창이 뜬다. 구글을 실행시키고자 한다면 다음과 같은 명령어를 치면 된다.

```python
telnet www.google.com 80
```

telnet 명령어 구조는 다음과 같다.

```python
telnet [IP 또는 도메인] [포트]
```

- “'telnet'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는
배치 파일이 아닙니다.” 라는 메세지가 뜨는 경우.
    
    [제어판]→[프로그램]→[프로그램 및 기능]→[Windows 기능 켜기/끄기] 순서대로 들어간다. “Windows 기능”이라는 창이 뜨면서 아래 여러 체크박스 리스트들이 뜰 것이다. 거기서 “텔넷 클라이언트” 체크박스를 체크해주고 “확인”버튼을 누른다. 그 후 텔넷을 다시 실행시켜본다.
    

성공 시 “telnet [IP 또는 도메인 이름]”이란 제목의 새로운 cmd창이 열린다. 

![연결 성공.PNG](/images/2023-02-23/2023-02-23-web-client-and-python-1.png)

그런데 위와 같이 빈 창이 뜬다. 이 상태에서는 어떤 글을 치더라도 화면에 아무것도 뜨지 않는다. 이 상태에서 Ctrl + ] 키를 동시에 누르면 다음과 같이 텔넷 클라이언트가 뜬다.

![텔넷 클라이언트로 이동함.PNG](/images/2023-02-23/2023-02-23-web-client-and-python-2.png)

사실 여기까지 온 것 자체가 해당 서버에 연결되었다는 것이다. 그러나 여기서는 한 가지 더 무언가를 해보겠다. 위 상태에서 엔터 키를 누르면 다시 빈 창이 뜬다. 하지만 이번에는 글자를 입력하면 글자가 보일 것이다. 여기에 “HEAD / HTTP/1.1”이라고 써보겠다. 그 후에 엔터키를 두 번 누른다. 그러면 다음의 결과를 얻는다. (여기서 주의할 점은 글자로 하나라도 잘 못치면 다시 지우고 쳐도 응답을 받는데 실패할 수 있다. 왜 실패하는지는 모르겠다)

![요청 성공.PNG](/images/2023-02-23/2023-02-23-web-client-and-python-3.png)

끌려면 이 상태에서 다시 Ctrl + ] 키를 눌러 다시 텔넷 클라이언트 창으로 나온 후에 q 또는 quit 입력 후 엔터를 치면 텔넷을 종료한다. 

HEAD 명령어는 해당 페이지에서 제공하는 기본적인 헤더 정보들을 얻고자 할 때 쓰인다. 이 외에도 GET, POST 명령어 등이 있다. 

HEAD / HTTP/1.1 이라는 명령어를 요청함으로써 위 사진처럼 서버로부터 특정 데이터로 응답을 받은 것이다. 

# 파이썬 표준 웹 라이브러리

파이썬 3에서는 웹 클라이언트와 서버에 관한 표준 패키지 2개를 기본으로 제공한다. 

- http: 클라이언트-서버 HTTP에 관한 것들을 다룬다.
    - client: 클라이언트 관련 코드를 짤 때 사용
    - server: 서버 관련 코드를 짤 때 사용됨
    - cookies, cookiejar: 쿠키를 다룸.
- urllib : http와 비슷한 기능을 함
    - request : 클라이언트의 요청을 다룸
    - response: 서버의 응답을 다룸
    - parse: URL를 여러 부분들로 나눠줌

urllib를 이용하여 예제를 살펴보겠다. 다음에 볼 예제는 “[cat-facts](https://alexwohlbruck.github.io/cat-facts/)”라는 무료 api 사이트로부터 고양이에 관한 사실들을 받아보도록 하는 코드이다. 먼저 해당 URL에 요청을 해보자.

```python
import urllib.request as ur

url = 'https://cat-fact.herokuapp.com/facts'
conn = ur.urlopen(url)
print(conn)
```

```python
<http.client.HTTPResponse object at 0x000001763E67CD00>
```

이번에는 서버로부터 받은 데이터들이 무엇인지를 살펴보자. 이를 위해 HTTPResponse 객체의 메서드인 read()를 이용한다. 

```python
import urllib.request as ur

url = 'https://cat-fact.herokuapp.com/facts'
conn = ur.urlopen(url)
print(conn)
response_data = conn.read()
print(response_data)
```

```python
<http.client.HTTPResponse object at 0x000002709AB7CF10>
b'[{"status":{"verified":true,"feedback":"","sentCount":1},"_id":"5887e1d85c873e0011036889","user":"5a9ac18c7478810ea6c06381","text":"Cats make about 100 different sounds. Dogs make only about 10.","__v":0,"source":"user","updatedAt":"2020-09-03T16:39:39.578Z","type":"cat","createdAt":"2018-01-15T21:20:00.003Z","deleted":false,"used":true},{"status":{"verified":true,"sentCount":1},"_id":"588e746706ac2b00110e59ff","user":"588e6e8806ac2b00110e59c3","text":"Domestic cats spend about 70 percent of the day sleeping and 15 percent of the day grooming.","__v":0,"source":"user","updatedAt":"2020-08-26T20:20:02.359Z","type":"cat","createdAt":"2018-01-14T21:20:02.750Z","deleted":false,"used":true},{"status":{"verified":true,"sentCount":1},"_id":"58923f2fc3878c0011784c79","user":"5887e9f65c873e001103688d","text":"I don\'t know anything about cats.","__v":0,"source":"user","updatedAt":"2020-08-23T20:20:01.611Z","type":"cat","createdAt":"2018-02-25T21:20:03.060Z","deleted":false,"used":false},{"status":{"verified":true,"sentCount":1},"_id":"5894af975cdc7400113ef7f9","user":"5a9ac18c7478810ea6c06381","text":"The technical term for a cat\xe2\x80\x99s hairball is a bezoar.","__v":0,"source":"user","updatedAt":"2020-11-25T21:20:03.895Z","type":"cat","createdAt":"2018-02-27T21:20:02.854Z","deleted":false,"used":true},{"status":{"verified":true,"sentCount":1},"_id":"58e007cc0aac31001185ecf5","user":"58e007480aac31001185ecef","text":"Cats are the most popular pet in the United States: There are 88 million pet cats and 74 million dogs.","__v":0,"source":"user","updatedAt":"2020-08-23T20:20:01.611Z","type":"cat","createdAt":"2018-03-01T21:20:02.713Z","deleted":false,"used":false}]'
```

위 과정에서 해당 사이트에 TCP/IP 연결을 하였고, HTTP 요청을 하고, 그로부터 HTTP 응답을 받은 것이다. 

HTTP로부터 응답을 받을 때 응답을 잘 받았는지 그 상태를 확인하는 것도 중요하다. 위 코드에 다음의 코드를 추가하여 이를 출력해보자.

```python
print(conn.status)
```

```python
200
```

200이란 것은 요청을 제대로 해서 응답을 제대로 받았다는 것이다. 이 외에도 HTTP 상태 코드에는 여러 가지가 있는데, 다음과 같다. 

- 1xx (정보) : 서버가 요청을 받긴 했지만 클라이언트에 대한 추가 정보를 가지고 있다는 뜻.
- 2xx (성공): 연결에 성공했다는 뜻. 200이 아닌 다른 200번 대 코드는 그 외에 다른 행동을 수행한다는 뜻이다.
- 3xx (재지정, redirection): 해당 리소스가 이동되어서 클라이언트에게 새 URL를 반환함
- 4xx (클라이언트 에러): 클라이언트 쪽에서 문제 발생. 404 (not found)가 유명한 예. 418 (I’m a teapot)은 만우절 농담이라고 한다.
- 5xx (서버 에러): 서버 쪽에서 문제 발생. 502 (bad gateway)는 웹 서버와 백앤드 어플리케이션 서버 간에 연결이 끊겼을 때 발생한다.

앞서 언급했듯, 웹 서버는 요청에 대한 응답으로 데이터를 그 어떤 포맷으로도 전달할 수 있다. 예로 html이라든가 아니면 그저 text일 수도 있다. HTTP 응답으로 받을 데이터 포맷은 HTTP 응답으로 받는 헤더(header)에 기록되어 있으며 “Content-Type”이란 이름으로 저장되어 있다. 파이썬에서는 다음의 코드를 입력하면 된다.

```python
print(conn.getheader('Content-Type'))
```

```python
application/json; charset=utf-8
```

위 예제에서 사용한 페이지로부터 응답으로 받는 데이터 포맷이 utf-8 인코딩의 json 데이터임을 알 수 있다. 

모든 HTTP 헤더들을 파악하려면 getheaders() 메서드를 이용하면 된다. (앞에서 배운 getheader 뒤에 s가 하나 더 붙었음을 주의!)

```python
for key, value in conn.getheaders():
    print(key, ":", value)
```

```python
Server : Cowboy
Connection : close
X-Powered-By : Express
Access-Control-Allow-Origin : *
Content-Type : application/json; charset=utf-8
Content-Length : 1675
Etag : W/"68b-25VLvc9X+ykqz7RP7OPGT3PIXAw"
Set-Cookie : connect.sid=s%3Af8XxNPSn2jEvRUtPjC8NmEcvtXpExOH6.tGIUT%2FMuFi0%2FVniS6HPlnKHMWdZNQiIvNBIeo7frk4U; Path=/; HttpOnly
Date : Thu, 23 Feb 2023 02:19:04 GMT
Via : 1.1 vegur
```

# Requests

requests 라이브러리는 앞서 살펴본 urllib와 같은 웹 클라이언트 라이브러리이다. 차이점은 requests는 기본으로 내장된 표준 라이브러리가 아닌 써드 파티(third party) 라이브러리이다. 따라서 처음 사용한다면 해당 라이브러리를 다운로드 받아야 한다. 터미널 창에 다음을 입력하면 자동으로 해당 모듈을 다운 받는다.

```python
pip install requests
```

해당 모듈을 이용해 앞선 예제를 다시 구현해보겠다.

```python
import requests
import json
from pprint import pprint

url = 'https://cat-fact.herokuapp.com/facts'
response = requests.get(url)
print(response)

decoded_data = json.loads(response.text)
pprint(decoded_data)
```

```python
<Response [200]>
[{'__v': 0,
  '_id': '5887e1d85c873e0011036889',
  'createdAt': '2018-01-15T21:20:00.003Z',
  'deleted': False,
  'source': 'user',
  'status': {'feedback': '', 'sentCount': 1, 'verified': True},
  'text': 'Cats make about 100 different sounds. Dogs make only about 10.',
  'type': 'cat',
  'updatedAt': '2020-09-03T16:39:39.578Z',
  'used': True,
  'user': '5a9ac18c7478810ea6c06381'},
 {'__v': 0,
  '_id': '588e746706ac2b00110e59ff',
  'createdAt': '2018-01-14T21:20:02.750Z',
  'deleted': False,
  'source': 'user',
  'status': {'sentCount': 1, 'verified': True},
  'text': 'Domestic cats spend about 70 percent of the day sleeping and 15 '
          'percent of the day grooming.',
  'type': 'cat',
  'updatedAt': '2020-08-26T20:20:02.359Z',
  'used': True,
  'user': '588e6e8806ac2b00110e59c3'},
(생략)
```

response 변수에 requests.get()을 할당하였고, 이 변수 자체를 출력하면 요청 결과가 반환됨을 알 수 있다. <Response [200]>이 출력된 것을 봐서 요청이 성공적으로 이뤄졌음을 알 수 있다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 위키백과, “통신 프로토콜”

[통신 프로토콜](https://ko.wikipedia.org/wiki/통신_프로토콜)

[3] inpa Dev, “stateful/stateless 차이 정리”

[[WEB] 🌐 Stateful / Stateless 차이 💯 정리](https://inpa.tistory.com/entry/WEB-📚-Stateful-Stateless-정리#stateless_통신_예시)

[4] ss-won, “[CS/네트워크] 사용자 인증과 허가에 대한 이야기 - 1. 인증과 허가란 ?”

[[CS/네트워크] 사용자 인증과 허가에 대한 이야기 - 1. 인증과 허가란 ?](https://velog.io/@ss-won/CS네트워크-사용자-인증과-허가에-대한-이야기-1.-인증과-허가란)

[5] 확장형 뇌 저장소, “[네트워크] 텔넷(telent) 명령어 - 포트(Port) 확인”

[[네트워크] 텔넷(telnet) 명령어 - 포트(Port) 확인](https://extbrain.tistory.com/103)

[6] [[윈도우] 텔넷 사용방법/탈출방법](https://sosopro.tistory.com/119)

[7] [telnet 명령어로 HTTP 테스트하기 \| 퍼니오 호스팅](http://www.fun25.co.kr/blog/telnet-http-test)