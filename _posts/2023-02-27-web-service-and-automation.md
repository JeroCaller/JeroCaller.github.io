---
layout: single
title:  "[Python][Web] 웹 서비스와 자동화"
categories: ["Python", "Web"]
tag: ["Python", "Web", "web API", "API", "REST", "URL", "URI", "URN"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

여태까지는 HTML 페이지를 이용한 전통적인 웹 클라이언트와 서버 어플리케이션에 대해 살펴보았다. 그렇지만 웹에서 어플리케이션과 데이터를 결합하여 사용하는 포멧에는 HTML말고도 여러가지가 존재한다. 

# webbrowser 모듈

해당 모듈은 url을 입력하면 자동으로 브라우저를 이용하여 해당 웹사이트를 보여주는 모듈이다. 

```python
import webbrowser

url = 'http://www.python.org/'
print(webbrowser.open(url))
```

```python
True
```

위 예제를 실행시키면 파이썬 공식 웹페이지가 브라우저를 통해 자동으로 뜬다. 성공적으로 접속하면 True를 반환하는 것 같다. open() 메서드 외에도 다음의 메서드들이 존재한다.

- open_new(url): 새 창에서 해당 사이트를 연다.
- open_new_tab(url): 브라우저가 탭을 지원할 경우, 해당 사이트를 새 탭에서 열도록 한다.

# 웹 API와 Representational State Transfer(REST)

웹으로부터 데이터를 받을 때 HTML 페이지 대신 웹 Application programming interface (API)를 통해 데이터를 받을 수도 있다. 이 경우 클라이언트가 웹 사이트의 URL로 요청을 하여 해당 서버에 접속한다. 그러면 응답으로 상태와 데이터를 돌려주는 데 이 때 html처럼 사람이 보기에 좋은 형태의 포맷 대신 프로그램이 좀 더 사용하기 쉬운 포맷인 JSON이나 XML 등의 포맷으로 데이터를 받는 것이다. 

REST (Representational State Transfer)는 웹을 위한 소프트웨어 또는 어플리케이션 아키텍쳐의 한 형식으로, 여기서 소프트웨어 구조 또는 소프트웨어 아키텍쳐(software architecture)는 소프트웨어를 구성하는 여러 구성 요소들의 관계를 표현하고 소프트웨어의 설계 및 업그레이드에 관한 일종의 규칙이다. 

REST에 대해 더 설명하기 전에 먼저 URL과 URI에 대해 알아보자. 

- URL (Uniform Resource Locator)은 네트워크 상에서 리소스 (동영상, 이미지, 글 등의 통합 자원. 유튜브 페이지 접속 시 보이는 동영상들도, 구글 이미지 검색 시 나오는 모든 사진들도, 인터넷 기사에 쓰인 글들 모두가 다 리소스이다)를 고유하게 식별해주는 역할과 동시에 그 리소스의 위치까지 표현해주는 규약이다. 보통 URL은 웹 사이트의 주소로 알려져 있는데, [www.google.com](http://www.google.com) 이렇게만 쓰면 이것은 URL이 아닌 URI로, 단순한 식별자가 된다. 반면 이 앞에 특정 네트워크 프로토콜, 즉 https, http, sftp, smp등 도 같이 표기하여 [https://www.google.com](https://www.google.com) 이렇게 쓰면 이것이 URL이 된다. 프로토콜(https)를 주소 앞에 명시함으로써 해당 사이트의 위치를 알 수 있기에 URL이 되는 것이다. URL = 식별자 + 위치
- URI (Uniform Resource Identifier): “통합 자원 식별자”의 뜻을 가진다. 리소스들을 서로 구분하고 식별하기 위해 붙이는 일종의 “이름” 같은 것이다. 통일된 방식으로 리소스들을 식별하기에 Uniform이란 단어가 들어가있다. 결론적으로 URI는 인터넷 상의 리소스 자체를 고유하게 식별해주는 문자열 시퀀스이다. URI = 식별자
- 참고사항) URN (Uniform Resource Name): URL은 특정 웹 사이트, 서버에 있는 웹 문서를 가리키지만, URN은 프로토콜, 서버, 리소스의 위치 등과는 전혀 상관없이 독립적으로 각 자원들에 붙은 식별자. URN이 부여된 리소스들은 특정 서버가 다른 곳으로 옮기는 등 서버의 위치가 변경되더라도 그 리소스들을 찾을 수 있다. 웹 사이트 주소를 예로 들면 네이버에서 어떤 블로그에 들어갔다면 아마 해당 사이트 주소가 https://www.naver.com/blog/ 등으로 되어 있을 것이다. 여기서 /blog 이하 부분이 URN이 된다. 참고로 이 /blog 이하 부분을 경로(path)라고도 하며, 특정 서버의 경로에 대한 상세 정보를 뜻한다. (그렇다면 만약 네이버가 다른 서버로 이전하거나 해도 해당 블로그를 볼 수는 있다는 거네)
- URL는 URI의 부분집합인 관계를 가진다.

이제 다시 REST로 돌아가면, REST에서는 웹 상의 글, 동영상, 데이터베이스 등의 모든 리소스에 고유의 URI들을 부여해준다. 마치 “유저 이름”이나 “주민등록번호”처럼 말이다. 그 다음, HTTP method를 이용하여 리소스에 대해 CRUD 명령을 적용시킨 내용을 클라이언트가 서버에 요청한다. 그러면 그에 대한 응답으로 JSON, XML 등의 데이터 형태로 서버가 응답을 하는 것이다. 

- CRUD와 HTTP METHOD

| CRUD | HTTP Method | 예시 |
| --- | --- | --- |
| CREATE | POST | 블로그에 새 게시글을 생성한다. |
| READ | GET | 블로그에 있던 글을 읽어온다. |
| UPDATE | PUT | 기존에 쓴 글을 수정한다. |
| DELETE | DELETE | 특정 블로그 글을 삭제한다. |

즉, 블로그를 예로 들면 사용자(클라이언트)가 새로운 게시글을 블로그에 올리고자 한다. 그러면 사용자가 CREATE 명령을 POST라는 명령어로 내려서 “새 블로그 글 생성해주세요”라고 서버에 요청하게 된다. 그러면 서버에서 해당 리소스가 새로 생성되며, 이에 대한 정보 (예를 들면 새로운 글 생성 및 해당 글이 서버에 성공적으로 반영되었는지 이를 보여줌)를 응답으로 다시 사용자에게 전송해주는 것이다. 

위 설명을 요약하면 REST는 다음의 요소들을 가진다고 볼 수 있겠다.

- 자원 (Resource): 서버 내의 모든 리소스들에 고유한 URI가 붙으며, 이를 통해 클라이언트가 해당 리소스들에 대해 URI로 접근하여 해당 리소스에 CRUD 명령을 내릴 수 있다.
- 행위 (Verb): 클라이언트가 HTTP METHOD로 리소스를 생성, 삭제, 편집, 읽기 등을 하는 것.
- 표현 (Representation): 클라이언트의 특정 리소스에 대한 CRUD 명령에 대한 서버의 응답. 예) 특정 블로그 글을 수정하였다 ⇒ 수정한 글이 서버에 반영되었는지 서버가 응답으로 편집된 글이란 결과물을 사용자에게 보여준다.

추가적으로, REST에는 다음의 특징들이 존재한다. 다음의 특징들은 특징이라고도 할 수 있지만 REST를 구성할 때 필요한 필수 조건이라고도 볼 수 있을 것이다.

- 인터페이스 일관성: 일관적인 인터페이스로 분리되어 있어야 하며, 일단 이를 만족시키면 플랫폼과 리소스의 종류에 상관없이 모두 같은 형태로 요청이 처리된다.
- 무상태 (Stateless): 클라이언트가 요청 시 해당 상태에 대해 저장되어선 안된다.
- 캐시 처리 가능 (Cacheable): 클라이언트는 응답을 캐싱할 수 있어야 한다. 캐시 (cache)는 서버에 요청 시 그에 대한 정보들을 저장하는 임시 저장 공간이다. 캐시의 존재로 인해 같은 서버에 요청을 보낼 때 이미 정보가 캐시 안에 있으므로 서버에 있는 리소스에 직접 접근하지 않고 캐시에 있는 정보를 제공함으로써 똑같은 요청에 대해 좀 더 효율적인 응답을 할 수 있게 된다.
    - tmi)
        
        1. 캐시는 local cache와 global cache로 나뉘며, local cache는 개인용 컴퓨터처럼 어떤 특정 전자기기 내에서만 저장되어 사용되는 캐시이다. global cache는 일종의 캐시 서버가 있어서 여러 서버에서 해당 캐시 서버에 접근하여 캐시 저장 및 조회용으로 쓰는 것을 말한다. 
        
        2. 캐시라는 저장공간도 그 용량이 한계가 있기 때문에 모든 데이터를 다 담을 수는 없다. 따라서 만약 캐시에 없는 정보를 클라이언트가 요청했다면 서버는 서버에 있는 데이터를 찾아 응답해줘야 한다. 
        
- 계층화 (Layered system): 클라이언트는 대상 서버에 직접 연결되는지 또는 중간 서버를 통해 간접적으로 연결되는 지 알 수 없다. 이를 통해 웹 개발자는 여러 기능들을 수행하는 중간 서버들을 확장시킬 수도 있다.
- 클라이언트-서버 구조: 아키텍쳐를 단순하게 만들고 작은 단위로 분리하여 클라이언트와 서버의 각 부분들이 독립적으로 개선될 수 있도록 해준다. 즉, 서버는 서버의 역할만, 클라이언트는 클라이언트의 역할만 할 수 있도록 서로를 명확하게 구분시켜준다.
- code on demand (필수는 아님): 자바스크립트 등을 통해 서버가 클라이언트가 실행시킬 수 있는 로직을 전송하여 기능을 확장시킬 수 있다.

이러한 REST를 이용하여 만든 웹 사이트, 웹 어플리케이션 등을 REST interface 또는 RESTful interface라고 부른다. 

# 크롤링과 스크랩 (Crawl and Scrape)

때때로, 어떤 웹사이트로부터 특정 정보를 추출하기를 원하는데, API로 제공이 안되고 오로지 HTML 페이지로만 제공된다고 해보자. 그러면 해당 웹사이트의 글 속에서 특정 정보를 찾아 일일이 마우스로 직접 다른 곳에 복붙하는 과정을 거칠 수 있다. 한 두번이면 모를까, 자주 이러한 반복적인 일이 필요하다면 마우스로 일일이 특정 글을 드래그하여 복사, 붙여넣기하는 행위는 매우 손이 피로할 것이다. 이러한 과정들을 자동화해주는 것, 즉, HTML 포맷의 웹페이지 내에서 내용을 자동으로 추출하는 것을 크롤러(crawler) 또는 스파이더(spider)라고 불린다 (근데 실제로는 크롤러, 크롤링이라는 말이 더 자주 쓰이는 것 같다). 또한, 스크래퍼 (scraper)는 일단 웹서버로부터 내용들을 추출한 뒤에 이를 parse (분석한다라는 뜻이 있다) 해주는 것을 말한다. 

크롤러와 스크래퍼가 강력히 결합되어 해당 기능을 사용하고자 한다면 scrapy라는 프레임워크를 사용할 수 있겠다. 터미널 창에서 pip install scrapy라 치고 다운받으면 된다. 

그러나 여기서는 예제로 BeautifulSoup4라는 모듈을 이용해 볼 것이다. 

## BeautifulSoup 모듈로 HTML 스크랩하기

만약 웹사이트로부터 HTML 데이터를 가지고 있다면 거기서부터 특정 정보만을 추출하길 원할 때도 있을 것이다. 이 때 BeautifulSoup를 쓰면 좋다. 사실 HTML은 텍스트 주위로 무수히 많은 태그들과 복잡한 구조를 가지고 있어 일일이 HTML 파일로부터 특정 텍스트나 데이터만을 추출하는 게 생각보다 어렵다. 

일단 해당 모듈이 처음이라면 터미널 창에서 pip install beautifulsoup4 라고 치고 다운을 받자. 

해당 모듈을 이용하여, 특정 웹페이지의 URL를 입력하면 그 웹페이지의 모든 서브 페이지들의 URL를 출력해주는 예제를 만들어보겠다. 다음이 그 예제이다.

```python
def get_links(url):
    import requests
    from bs4 import BeautifulSoup as soup  #1
    result = requests.get(url)
    page = result.text
    doc = soup(page)
    links = [element.get('href') for element in doc.find_all('a')]  #2
    return links

if __name__ == '__main__':
    import sys
    for url in sys.argv[1:]:
        print('사이트 주소:', url)
        for num, link in enumerate(get_links(url), start=1):
            print(num, link)
        print()
```

위 예제를 links.py 파이썬 파일로 저장하였다. 그 뒤, 터미널 창에서 다음의 코드를 입력하여 실행시킨다.

```python
python links.py http://boingboing.net
```

여기서는 boingboing이라는 특정 웹사이트의 URL를 입력해보았다. 결과는 다음과 같다.

```python
사이트 주소: http://boingboing.net
1 https://boingboing.net
2 https://boingboing.net/search
3 https://store.boingboing.net
4 https://boingboing.net/search
5 https://store.boingboing.net
6 https://boingboing.net/blog
7 https://bbs.boingboing.net
8 https://bbs.boingboing.net/faq
9 https://store.boingboing.net
10 https://bit.ly/boingboingdealssupport
(생략)
```

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [REST(Representational State Transfer)란?](https://tibetsandfox.tistory.com/19)

[3] 위키피디아, REST

[REST](https://ko.wikipedia.org/wiki/REST)

[4] 이랜서, “URI와 URL, 어떤 차이점이 있나요?”

[URI와 URL, 어떤 차이점이 있나요? \| 이랜서 블로그](https://www.elancer.co.kr/blog/view?seq=74)

[5] Charlezz, “URI랑 URL 차이점이 뭔데?”

[URI랑 URL 차이점이 뭔데? \| 찰스의 안드로이드](https://www.charlezz.com/?p=44767)

[6] [캐시란 무엇이며 어떻게 삭제하나요? — Zyngagames.com도움말 센터](https://zyngasupport.helpshift.com/hc/ko/29-zyngagames-com/faq/1148-what-is-cache-and-how-do-i-clear-my-cache/?l=ko)

[7] [캐시란 무엇인가](https://velog.io/@tyjk8997/캐시와-궁금한점)

[8] [[Server] Cache(캐시)란?](https://mangkyu.tistory.com/69)