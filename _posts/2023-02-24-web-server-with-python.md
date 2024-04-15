---
layout: single
title:  "[Python][Web] 웹 서버"
categories: ["Python", "Web"]
tag: ["Python", "Web"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

# 가장 간단한 파이썬 웹 서버

터미널 창에 다음의 코드를 입력하면 가장 간단한 웹 서버를 열 수 있다.

```python
python -m http.server
```

이 코드는 가장 간단한 파이썬 HTTP 서버를 실행시킨다. 실행에 문제가 없다면 아마 다음과 같은 메세지를 받을 것이다.

```python
Serving HTTP on :: port 8000 (http://[::]:8000/) ...
```

원래 :: 자리에 TCP 주소가 들어가야한다. 내 추측으로는 IP version 6 (IPv6)의 주소로 쓰인 것 같다. IPv4이라면 0.0.0.0처럼 뜰 텐데 이는 임의의 TCP 주소라는 뜻이다. 즉, 서버가 어떤 주소를 가지고 있든 웹 클라이언트가 접속할 수 있다는 것이다. (추측: 0.0.0.0은 ::와 같나?)

어쨌건, 서버를 무사히 실행하였다. 이제 크롬과 같은 웹 브라우저 URL 입력창에 http://localhost:8000을 입력해보자. 그러면 현재 자신의 컴퓨터의 디렉토리 리스트들을 볼 수 있을 것이다. 그리고 터미널 창에서는 서버가 다음과 같은 로그들을 띄울 것이다.

```python
::1 - - [24/Feb/2023 11:05:19] "GET / HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:05:19] "GET /favicon.ico HTTP/1.1" 404 -
::1 - - [24/Feb/2023 11:05:25] "GET /some_sayings.txt HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:05:29] "GET /test1.py HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:05:31] "GET /test_folder/ HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:05:35] "GET /lotto_generator.py HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:05:38] "GET /receipt.csv HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:07:58] "GET /json_fake_test.json HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:08:03] "GET /our_phone_web/ HTTP/1.1" 200 -
::1 - - [24/Feb/2023 11:08:04] "GET /our_phone_web/introduction/ HTTP/1.1" 200 -        
::1 - - [24/Feb/2023 11:08:06] "GET /our_phone_web/introduction/history_of_our_company.py HTTP/1.1" 200 -
```

위에 나온 파일 이름들은 모두 현재 내 폴더에 있는 파일들이다. 

localhost와 127.0.0.1은 서로 같은 의미의 용어로, 뜻은 내 로컬 컴퓨터를 의미한다. (즉 내가 쓰고 있는 현재 컴퓨터를 의미) 따라서 해당 주소는 인터넷 연결 여부에 무관하게 작동된다. (위의 ::1은 127.0.0.1의 IPv6 주소 버전이다.)

위 결과는 다음과 같이 해석한다.

- ::1 ⇒ 클라이언트의 IP 주소
- 첫 번째 대시 (-)는 remote 유저 이름이다 (발견될 경우에 표시됨)
- 두 번째 대시 (-)는 로그인된 유저 이름이다. (필요할 경우에 표시됨)
- GET / HTTP/1.1은 웹 서버에 보내진 커맨드이다.
    - GET은 HTTP 메서드
    - /은 요청된 리소스
    - HTTP/1.1은 HTTP의 버전을 의미

이제 브라우저에서 아무 파일이나 눌러보자. 어떤 파일을 누르면 해당 요청이 서버에 보내져서 서버는 그에 대한 응답으로 파일 내용을 보여준다. 

서버 실행을 멈출려면 터미널 창에서 Ctrl + C를 누르면 된다. 

위에서 실습해본 파이썬 웹 서버는 무언가를 빠르게 테스트하고자 할 때 적합하다. 그러나 이 서버를 실제 상용 웹사이트로 쓰는 건 비추다. Apache나 Nginx같은 웹 서버가 정적 파일 실행에 더 빠르다. 게다가, 이러한 간단한 서버는 동적 컨텐츠를 다룰 수 없다. 

# 프레임워크 (Framework)

- Web Server Gateway Interface (WSGI): 파이썬 웹 애플리케이션과 웹 서버 사이에서 쓰이는 보편적인 API.

웹 서버에서는 HTTP나 WSGI 같은 것들을 자세히 다루지만, 사용자가 실제로 웹 사이트를 구동시키기 위해 파이썬 코드를 작성할 때 필요한 것은 웹 프레임워크이다. 

파이썬으로 웹사이트를 구동시키기 위한 코드 작성 시에 쓸 수 있는 파이썬 웹 프레임워크는 다양하게 존재한다. 이 웹 프레임 워크는 클라이언트의 요청과 서버의 응답을 최소한으로 다룬다. 다음의 요소들 중 일부 또는 전체를 제공할 수 있다.

- 루트 (Routes): URL을 해석하고 그에 해당하는 서버 파일 또는 파이썬 서버 코드를 찾아줌
- template: 서버 쪽 데이터와 HTML 페이지를 하나로 병합시켜줌
- 인증 및 승인 (Authentication and authorization) : 유저 이름 및 패스워드, 허가 등을 다룸
- session: 사용자가 웹사이트에 방문하는 동안 일시적인 데이터를 유지.

다음 장에서는 bottle과 flask라는 대표적인 프레임워크에 대해서 간단하게 알아보겠다.

## Bottle

Bottle은 하나의 파이썬 파일로 이루어져 있어서 실행해보기 쉽다. 외부 라이브러리라서 처음 사용한다면 pip install bottle 코드를 이용해 해당 라이브러리를 깔도록 하자. 

이번에는 bottle1.py라는 파이썬 파일을 만들고 그 안에 다음의 코드를 작성하겠다.

```python
from bottle import route, run

@route('/')
def home():
    return "초라하지만 어쨌든 내 첫 웹페이지!"

run(host='localhost', port=9999)
```

route 데코레이터는 그 다음에 오는 함수와 URL를 연관시키는 기능을 한다. 위 예제에서는 home() 이라는 함수가 / (홈페이지)를 다루게 된다. 

run() 함수는 bottle에 내장된 파이썬 테스트 웹 서버를 실행시켜주는 역할을 한다. 참고로 run() 함수 안에 debug=True를 넣으면 HTTP 에러 발생 시 디버그 페이지를 형성시킬 수 있고, reloader=True를 넣으면 파이썬 코드에 변화를 준 경우 해당 페이지를 재실행하도록 해준다.

위 파일을 저장 후, 터미널 창에서 다음의 코드를 입력하여 해당 파일을 실행시키자.

```python
python bottle1.py
```

그럼 터미널 창에서 다음의 메세지가 뜰 것이며…

```python
Bottle v0.12.24 server starting up (using WSGIRefServer())...
Listening on http://localhost:9999/
Hit Ctrl-C to quit.
```

이제 브라우저 URL 입력창에 http://localhost:9999/을 입력하면 다음을 볼 수 있을 것이다. 

![첫 웹페이지.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-1.png)

이제 해당 웹페이지를 끄고 터미널 창에서 Ctrl + C를 눌러 서버를 종료시키자.

방금은 웹페이지에 보여줄 문장을 파이썬 파일 내에 썼지만 이번에는 이를 별도로 분리시켜 html 문서에 작성하고, 파이썬 파일에서 이를 불러들여 실행시키도록 해보자. 

우선 my_saying_homepage.html이라는 파일을 만들고 그 안에 다음의 텍스트를 쓰겠다.

```html
내 <b>새로운</b> 그리고 <i>조금은 향상된</i> 홈페이지이다!
```

그리고 이번에는 bottle1.py 내용을 다음과 같이 변경하고 저장하도록 하겠다.

```python
from bottle import route, run, static_file

@route('/')
def main():
    return static_file('my_saying_homepage.html', root='.')

run(host='localhost', port=9999)
```

static_file() 함수에서는 먼저 웹페이지에 보일 html문서 파일명을 입력하고, 해당 파일이 어디에 있는지 root= 파라미터를 통해 명시해준다. 여기서는 ‘.’이라고 했는데 . (점)은 현재 디렉토리를 의미한다. 즉, 현재 디렉토리에서 해당 html 파일을 이용하겠다는 뜻이다. 

아까처럼 터미널 창에 python bottle1.py라 치고 해당 웹페이지로 다시 들어가보자.

![조금 향상된 웹페이지.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-2.png)

이번에는 해당 홈페이지의 하위 페이지를 만들어보자. 현재 작동하고 있는 서버를 중단시키고 bottle2.py라는 이름의 새로운 파이썬 파일을 만들고 그 안에 다음의 코드를 작성한 후 저장하자.

```python
from bottle import route, run, static_file

@route('/')
def home():
    return static_file('my_saying_homepage.html', root='.')

@route('/subpage/<thing>')  #1
def subpage(thing):  #2
    return "안녕하세요. 여긴 저의 {} 입니다. 어떻게 이 곳을 잘 찾아오셨군요. ".format(thing)  #3

run(host='localhost', port=9999)
```

#1에서 해당 홈페이지 뒤에 subpage/\<thing>을 덧붙여서 하위 페이지를 형성하고자 하였다. 그리고 그 바로 아래에 있는 subpage 함수에서 subpage/\<thing> 페이지에서 보여줄 문장을 정의하였다. 여기서 \<thing>에 쓰인 꺽쇠괄호는 “thing”이라는 매개변수에 어떤 문자열이 들어가든 그 문자열이 쓰인 URL를 생성하겠다는 것이다. 예를 들어 subpage 함수의 매개변수 thing으로 문자열 “hello”가 입력되었다면 http://localhost:9999/subpage/hello라는 페이지가 형성되는 것이다. 

터미널 창에 python bottle2.py를 입력하여 서버를 실행시켜보자. 그 후 웹 브라우저 URL창에 “http://localhost:9999/subpage/나의 비밀 방”이라고 쳐보자. 그러면 다음의 결과를 얻을 것이다.

![하위 페이지.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-3.png)

이번에는 해당 서버를 그대로 유지시킨 채 다른 것을 해보겠다. 바로 이전 장에서 배운 requests 모듈을 이용하여 특정 페이지로 클라이언트의 요청이 잘 전송되는지, 그리고 그에 대한 응답이 제대로 이뤄지는 지 테스트를 해보자. 

bottle_test.py라는 파이썬 파일 생성 후, 그 안에 다음의 코드를 입력한 뒤 저장하자.

```python
import requests

resp = requests.get('http://localhost:9999/subpage/나의 비밀 방')
if resp.status_code == 200 and \
    resp.text == '안녕하세요. 여긴 저의 나의 비밀 방 입니다. 어떻게 이 곳을 잘 찾아오셨군요. ':
    print('접속 성공.')
else:
    print('접속 실패')
```

그 후 터미널 창(이미 이전 서버가 실행 중이므로 아마 새로운 터미널 창에서 해야 할 것이다)에서 python bottle_test.py라 쳐서 해당 파일을 실행시켜보자. 성공적으로 해당 웹페이지에 접속했다면 아마 다음의 메세지를 받을 것이다.

```python
접속 성공.
```

## Flask

이번에는 flask를 살펴보자. 해당 라이브러리를 처음 쓴다면 pip install flask를 통해 다운받자. flask는 bottle에 비해 좀 더 전문적인 웹 개발에 유용한 더 많은 확장 기능들을 제공한다고 한다. 예를 들면 페이스북 인증, 데이터베이스 통합 등이 가능하다고 한다. 

앞서 bottle에서 썼던 예제를 여기서도 똑같이 사용해보겠다. flask로 바꾼 예제는 다음과 같다.

```python
from flask import Flask

app = Flask(__name__, static_folder='.', static_url_path='')  #1

@app.route('/')  #2
def home():
    return app.send_static_file('my_saying_homepage.html')  #3

@app.route('/subpage/<thing>')  #4
def subpage(thing):
    return "안녕하세요. 여긴 저의 {} 입니다. 어떻게 이 곳을 잘 찾아오셨군요. ".format(thing)

app.run(port=9999, debug=True)  #5
```

Flask는 정적 파일 (static file)이 있는 홈 디렉토리 이름이 “static”으로 디폴트되어 있고, 그에 대응되는 URL도 ‘/static’으로 기본 설정되어 있다. 따라서 위 예제의 #1에서는 폴더를 현재 디렉토리 ‘.’으로 설정하였고, URL /이 “my_saying_homepage.html” 파일과 연동될 수 있도록 빈칸 ‘’으로 지정해두었따. 

또한 run() 메서드에는 debug=True 매개변수를 삽입하여 자동으로 reload되도록 하였다. 이는 bottle에서는 debug와 reload를 따로 분리하여 사용했던 것과 차별되는 점이기도 하다. 

이제 해당 파일을 터미널 창에 실행시켜보자.

```python
python flask1.py
```

그러면 해당 터미널 창에서는 다음과 같이 뜰 것이며,

```python
*** Serving Flask app 'flask1'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:9999
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 637-720-720**
```

웹 브라우저 URL창에 “http://localhost:9999/subpage/나의 비밀 방”이라고 쳐보면 아까 bottle로 실습했었을 때와 같은 결과를 얻을 것이다.

run() 메서드에서 debug=True를 해두면 좋은 점이 있다. 만약 서버 코드에 에러 발생 시 특수 포맷된 페이지를 반환하여 무엇이 잘못되었는지 상세정보를 보여준다. 이를 직접 보기 위해 flask1.py의 #3 부분에서 static을 staic으로 일부러 오타를 쳐보았다. 만약 run() 메서드에 debug=True가 없는 채로 실행시키면 웹 브라우저에서 다음을 볼 것이다.

![플라스크 디버그 미설정시.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-4.png)

이번에는 run() 메서드에 debug=True를 넣은 상태에서 재실행해보겠다. 그러면 다음을 얻는다. 

![플라스크 디버그 설정시1.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-5.png)

![플라스크 디버스 설정시2.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-6.png)

(중간에 파일 경로까지 상세히 보여주면서 정확히 어디서 잘못되었는지도 보여준다. 여기서는 내 개인정보가 보여서 일부러 그 부분만 생략하였다)

이렇게 서버에서 에러 시 위 사진처럼 그에 대한 상세한 정보를 제공해준다. 주의해야 할 점은 실제 상용 웹서버에서 해당 매개변수 설정 시 나쁜 해커들에게 해당 정보까지 보여주는 셈이 될 수 있으니 주의해야 한다. 

## bottle과 다른 flask만의 특징

Flask는 확장적인 templating system인 jinja2를 가지고 있다. 이 둘을 결합하여 사용한 예제를 보자.

먼저 templates라는 디렉토리를 만들고, 그 안에 flask2.html이라는 파일을 저장한다. 해당 파일 내 텍스트는 다음과 같다.

```html
<html>
    <head>
        <title>Flask2 예제</title>
    </head>
    <body>
        안녕하세요. {{ thing }}입니다.
    </body>
</html>
```

그리고 이전 예제에서 썼던 my_saying_homepage.html도 해당 디렉토리에 넣는다. 그 다음 templates보다 바로 한 단계 상위 디렉토리 안에 flask2.py 파일을 만들고 다음과 같이 작성 후 저장한다.

```python
from flask import Flask, render_template  # render_template 추가

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('my_saying_homepage.html')

@app.route('/subpage/<thing>')
def subpage(thing):
    return render_template('flask2.html', thing=thing)  #1

app.run(port=9999, debug=True)
```

그리고 해당 파일을 터미널 창에서 실행 시켜 서버 실행 후, 브라우저 URL창에 http://localhost:9999/subpage/나의 비밀 방

이라고 치고 들어가보자. 그러면 브라우저에 “안녕하세요. 나의 비밀 방입니다”라고 나올 것이다. 이렇듯 서버 응답으로 얻은 결과를 브라우저에 보여 줄 데이터 (여기선 html)들을 templates 폴더 안에 따로 넣어서 따로 보관하여 사용할 수 있음을 알았다. 이를 이용하면 서버 실행 파일인 flask2.py와 html 파일을 따로 분리하여 관리를 더 수월하게 할 수 있을 것이다. 

templates 폴더를 따로 사용할 경우 이 폴더 안의 html 파일을 읽어들여 사용할 경우 render_template() 메서드를 사용하면 된다. 

flask2.py 파일의 #1 코드에서 thing=thing은 subpage 함수의 인자로 들어온 thing 변수 내 문자열 값을 html 파일 의 {{ thing }}에 넘긴다는 것이다. 그래서 브라우저 실행 시 thing으로 넘긴 “나의 비밀 방” 단어가 같이 결과에 보이는 것이었다. 이렇게 html로 변수를 넘기는 것은 1개 뿐만 아니라 여러 변수도 넘길 수 있다. 

이번에는 2개 인자를 html에 넘겨 출력하는 방식으로 바꿔보자. 기존의 flask2.html 내용을 다음처럼 바꾸어 flask3.html으로 저장해보자.

```html
<html>
    <head>
        <title>Flask3 예제</title>
    </head>
    <body>
        안녕하세요. {{ thing }}입니다.
        여기에는 {{ other_thing }}이 있습니다.
    </body>
</html>
```

그리고 flask3a.py 파일에 다음 코드를 작성 후 저장하자.

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('my_saying_homepage.html')

@app.route('/subpage/<thing>/<other_thing>')  #1
def subpage(thing, other_thing):
    return render_template('flask3.html', thing=thing, other_thing=other_thing)  #2

app.run(port=9999, debug=True)
```

그리고 터미널 창에서 해당 파일 실행 후 http://localhost:9999/subpage/나의 비밀 방/보석

을 치고 들어가보자. 그러면 다음의 결과를 얻을 것이다.

![flask3a.PNG](/images/2023-02-24/2023-02-24-web-server-with-python-7.png)

결과는 같지만 subpage 함수 내부를 조금 달리 해서 실행시키는 방법도 있다. 다음은 thing, other_thing같은 인자들을 GET의 매개변수로 받는 형식을 띠는 flask3b.py 파일이다.

```python
from flask import Flask, render_template, request  #1 request 추가

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('my_saying_homepage.html')

@app.route('/subpage/')
def subpage():  #1 매개변수가 사라졌다.
    thing = request.args.get('thing')  #2
    other_thing = request.args.get('other_thing')  #3
    return render_template('flask3.html', thing=thing, other_thing=other_thing)

app.run(port=9999, debug=True)
```

이번에는 URL에 다음과 같이 치자

http://localhost:9999/subpage?thing=나의 비밀 방&other_thing=보석

그러면 아까와 같은 결과를 얻는다. 

이렇게 URL에 GET 커맨드가 사용될 때에는 인자들이 &key1=value1&key2=value2 와 같은 방식으로 넘겨진다. 

만약 넘길 인자가 너무 많다면 thing=thing, other_thing=other_thing처럼 일일이 쓰면 코드가 너무 길어질 것이다. 이를 줄이기 위해 키워드 인자 ** 를 사용할 수 있다. 다음 예제는 flask3c.py 파일로, 해당 기능을 이용하여 인자들을 render_template에 넘기는 것을 보여주고 있다. 

```python
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('my_saying_homepage.html')

@app.route('/subpage/')
def subpage():
    kwargs = {}
    kwargs['thing'] = request.args.get('thing')
    kwargs['other_thing'] = request.args.get('other_thing')
    return render_template('flask3.html', **kwargs)

app.run(port=9999, debug=True)
```

# 기타 설명

## 그 외 웹 서버들

### 파이썬이 아닌 웹 서버들

웹사이트를 실제 상용 목적으로 만들 때에는 더 빠른 웹 서버를 원하게 될 것이다. 이 때 쓰일 수 있는 웹 서버에는 apache와 nginx 등이 있다. 

### 파이썬 웹 서버들

apache, nginx처럼 작동하나 다중 프로세스및 쓰레드(thread)를 사용하여 요청들을 동시에 처리하는 독립적인 파이썬 기반 WSGI 서버들에는 다음이 있다.

- uwsgi
- cherrypy
- pylons

하나의 프로세스만을 사용하지만 어떤 개개의 요청을 막는 것을 피하는 이벤트 기반(event-based) 서버에는 다음이 있다.

- tornado
- gevent
- gunicorn

## 그 외 프레임워크들

종종 웹사이트와 데이터베이스가 결합되어 쓰이는 경우가 있다. bottle, flask는 이러한 데이터베이스에 대한 직접적인 지원을 하지 않는다. (따로 애드온 달면 가능하다)

데이터베이스 벡엔드 웹사이트를 빠르게 만들고자 하고, 데이터베이스 디자인이 그리 자주 바뀌지 않는 경우, 더 큰 파이썬 웹 프레임워크들을 사용하는 것을 고려해볼 수 있다. 이를 위해 주로 쓰이는 파이썬 웹 프레임워크들에는 다음이 있다고 한다.

- django: 큰 사이트 제작에 쓰이는 가장 유명한 프레임워크. ORM (Object-Relational Mapper) 코드가 포함되어 있어 전형적인 데이터베이스의 CRUD(Create, Replace, Update, Delete) 함수들을 쓸 수 있는 웹 페이지를 제작할 수 있다.
- web2py: django와 거의 비슷하다고 한다.
- pyramid: 역시 django와 어느 정도 비슷하다고 한다.
- turbogears: ORM, 많은 데이터베이스와 여러 template 언어를 지원한다.
- wheezy.web : 실행에 최적화가 된 최신 프레임워크로, 다른 프레임워크보다 더 빠르다고 한다.

사실, 관계형 데이터베이스 (Relational database)와 연동된 웹사이트 개발 시 꼭 위에 언급한 프레임워크들을 사용하지 않고도 bottle, flask와 관계형 데이터베이스 모듈을 결합하여 사용할 수도 있다. 또한 ORM 코드 대신 직접 SQL를 사용할 수도 있다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [한국인터넷정보센터(KRNIC)](https://xn--3e0bx5euxnjje69i70af08bea817g.xn--3e0b707e/jsp/resources/ipv6Info.jsp)