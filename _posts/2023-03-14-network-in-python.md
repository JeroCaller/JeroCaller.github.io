---
layout: single
title:  "[Python] 네트워크(Network)"
categories: ["Python"]
tag: ["Python", "network"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# pattern

네트워크가 가능한 앱을 만들 때 쓰이는 기초적인 패턴들이 있다.

- request-reply (client-server) : 해당 패턴은 동기적이다. 즉, 서버가 응답할 때까지 클라이언트는 계속 기다려야 한다. 웹 브라우저라는 클라이언트가 웹 서버에 HTTP 요청을 하고 그에 대한 응답을 받는 것이 대표적 예이다.
- push (fanout) : 어떤 한 모듈이 직접적으로 호출하는 여러 개의 모듈들을 의미한다. 비유를 들자면 한 명의 전단지(데이터) 뿌리는 사람이 여러 집의 현관문 앞에 전단지를 붙이는 것과 같다.
- pull (fanin) : push와 반대되는 상황으로, 여러 개의 모듈들이 호출하는 한 모듈을 의미한다. 비유를 들자면, 한 집이 피자, 치킨, 짜장면 집 등 여러 음식점으로부터 각자의 전단지를 받는 것과 같다.

![Reference [2] 참조. ](https://blog.kakaocdn.net/dn/b06pJf/btq9MFTyvBn/Of55RlAZhlAb5a8ld4011K/img.png)

Reference [2] 참조. 

위 도식을 예로 들면, A 모듈은 B, C, D 모듈들을 호출하고 있으므로 A모듈의 팬아웃 개수는 B, C, D 모듈로 총 3개이다. 반면, F 모듈은 C, D 모듈에 의해 호출되고 있으므로 F 모듈의 팬인 개수는 C, D 모듈로 총 2개이다. 

- publish-subscribe (pub-sub) : 텔레비전 방송과도 같은 시스템이다. publisher는 데이터를 보낸다 (영상 송출하듯이). 모든 subscriber들은 해당 데이터 복사본을 받을 것이다. 이 때, subscriber가 해당 데이터들 중 특정 자료형의 데이터만을 받기 원한다면(이를 topic이라 한다) publisher는 그 데이터만을 보내준다. fanout과는 달리, 하나 이상의 subscriber들은 publisher가 보내는 데이터들 중 특정 종류의 데이터만을 받도록 할 수 있다. 만약 어떤 topic에 대한 subscriber들이 없다면 해당 데이터는 무시된다. publisher는 데이터를 그저 “송출”할 뿐이지 그 데이터를 받는 (방송을 보는) subscriber들이 누구인지는 알 수 없다.

# TCP/IP

우리의 눈에 보이지 않는 네트워크의 숨겨진 원리를 간단히 알아보자. 

인터넷은 서로 연결하고, 데이터를 교환하고, 연결을 끊는 방법 등에 대한 규칙들에 근거하여 작동한다. 이러한 규칙들을 protocol이라 한다. 그리고 이러한 프로토콜도 layer(층)로 배열되어 있다. 

가장 낮은 층에는 전기 신호와 같은 것들을 다룬다. 이는 더 높은 계층들에 대한 토대가 되어준다. 중간 층에는 IP (Internet Protocal) 계층이 존재한다. 여기서는 네트워크 위치의 주소를 지정하는 방법과 데이터 패킷(packet, 데이터 덩어리, 묶음 정도로 해석하면 될 듯 싶다)이 흐르는 방법에 대해 명시해준다. 

이 위 계층에는 두 개의 프로토콜이 존재하여 네트워크 위치 간에 byte들을 이동시키는 방법에 대해 묘사하고 있다.

- UDP (User Datagram Protocol) : 짧은 기간 동안의 데이터 교환에 쓰인다. datagram은 작은 message를 말한다.
- TCP (Transmission Control Protocol) : UDP에 비해 더 긴 시간 동안의 연결에 대해 적용되는 프로토콜이다. byte의 stream을 전송하며, 중복없이 순서대로 도착했는지를 확인한다.

UDP 메시지는 답장 기능이 없기에 해당 메시지가 제대로 된 목적지에 잘 도착했는지 확인할 수 없다. 반면 TCP는 송신자와 수신자간 연결을 만들어줘서 상호작용이 가능하다.

같은 기기 내에서의 프로세스간에서도 인터넷 프로토콜을 사용할 수 있다. 그래서 자신이 쓰는 로컬 기기에도 IP 주소인 127.0.0.1 또는 localhost라고 하는 loopback interface가 존재하는 것이다. 

웹과 같은 우리가 대부분 사용하는 인터넷 대부분은 IP 프로토콜 위에서 작동하는 TCP 프로토콜에 기반한다. 이 둘을 합쳐 간단히 TCP/IP 라고 부른다. 

# socket

인터넷 사용을 위해 더 높은 네트워크 계층을 사용한다면 낮은 계층의 세부적인 것들을 모두 알 필요는 없다. 그러나 여기서는 네트워크가 어떻게 작동하는지를 간단하게 알아보겠다. 

네트워크의 가장 낮은 계층에서는 socket이란 것을 사용한다. socket을 이용한 코딩은 복잡하나, 보이지 않는 곳에서의 네트워크에 무슨 일이 일어나는지 파악할 수 있다. 예를 들어, 네트워크에 에러가 발생하면 socket에 대한 메시지가 나타난다. 

간단한 client-server 예제를 살펴보도록 하겠다. 클라이언트가 UDP로 서버에 짧은 문자열을 보내면, 서버는 그에 대한 응답으로 짧은 문자열을 반환한다. 이러한 클라이언트와 서버 간 통신을 위해서는 우선 서버는 주소와 port를 가져야만 한다. (마치 편지를 보내기 위해 우체국과 우체국 박스가 필요한 것처럼) 그러면 클라이언트는 이 두 값을 통해 메시지를 주고 받을 수 있게 된다. 

다음 예제는 서버와 클라이언트를 내 컴퓨터 기기에 동시에 구동하도록 구현하였다. 그리고 server쪽 파이썬 파일과 client쪽 파이썬 파일 이렇게 두 개로 나눠 진행한다.

먼저 server 코드는 다음과 같다. udp_server.py 파일로 저장하자.

```python
from datetime import datetime
import socket

server_address = ('localhost', 6789)  # (address, port)
max_size = 4096  # 송수신할 데이터 길이 제한

print('서버 시작 시간: {}'.format(datetime.now()))
print('클라이언트의 호출 대기 중...')

# socket.socket() : socket을 생성하는 메서드
# AF_INET : Internet(IP) socket 생성
# SOCK_DGRAM: UDP를 사용하여 datagram을 주고 받을 것이다.
server = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# bind() : 생성한 socket을 server 주소와 bind (결합)한다.
# 이를 통해 server_address에 적은 특정 서버 주소로 오는 데이터를 수신할 준비를 한다.
server.bind(server_address)

# server로 들어오는 datagram을 기다리고, datagram이 오면
# data와 client에 대한 정보를 얻는다. 
# client 변수에는 client에 대한 address와 port에 대한 정보가 들어온다.
data, client = server.recvfrom(max_size)

print('{} {}: {}'.format(datetime.now(), client, data.decode('utf-8'))) # 클라이언트로부터 온 메시지
server.sendto('절 부르셨나요?'.encode('utf-8'), client)  # server의 답장
server.close()  # 연결을 닫는다.
```

socket을 통해 서버 구현 시 먼저 socket() 메서드를 통해 socket을 먼저 생성하고, bind() 메서드를 통해 서버 주소 및 port와 결합시켜야 한다. 위 코드에 대한 더 자세한 설명은 위 코드 내에 주석으로 설명해놨다. 

다음 예제는 클라이언트 코드이다. udp_client.py로 저장하였다.

```python
import socket
from datetime import datetime

server_address = ('localhost', 6789)
max_size = 4096

print('클라이언트 시작 시간: ', datetime.now())

# 클라이언트 socket 생성
client = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

# 서버는 클라이언트가 먼저 메시지를 보내지 않으면 먼저 메시지를 송신할 수 없다.
# 그러나 클라이언트는 서버에 먼저 메시지를 보낼 수 있다. 
client.sendto(b'hi, server!', server_address)

# 서버로부터 온 데이터와 서버에 대한 주소 및 port 정보를 받아온다. 
data, server = client.recvfrom(max_size)

# 서버로부터 온 응답과 서버의 위치 (address, port) 출력
print('{} {}: {}'.format(datetime.now(), server, data.decode('utf-8')))

# 연결 종료
client.close()
```

cmd 명령 프롬프트 창을 두 개 띄워놓고 위 두 파일을 실행해보았다. 먼저, 서버 파일인 udp_server.py부터 실행한다. 

![20230316_08024025.png](/images/2023-03-14/2023-03-14-network-in-python-1.png)

이제 또 다른 cmd 창에서 클라이언트 파일인 udp_client.py 파일을 실행하면 다음과 같은 결과를 얻을 것이다.

![20230316_08024911.png](/images/2023-03-14/2023-03-14-network-in-python-2.png)

두 cmd 창 중 왼쪽에서 서버 파일을 실행하였고, 오른쪽 창에서 클라이언트 파일을 실행하였다. 서버 파일 실행 후에 클라이언트 파일을 실행하니, 왼쪽 창에서는 클라이언트가 보낸 메시지와 클라이언트의 주소 정보가 떴음을 알 수 있다. 클라이언트가 이렇게 메시지를 보내자, 서버가 그에 대한 응답과 서버 주소를 클라이언트에게 보낸 것을 오른쪽 창에서 볼 수 있다. 

위 cmd 결과와 파일 코드 내용을 보면 알겠지만, 서버와 달리 클라이언트는 따로 주소나 port를 명시하지 않았다. 이는 시스템에서 자동으로 할당해주기 때문이다. 위 실행 결과창을 봤을 때, 클라이언트는 57642라는 port로 자동 할당 받은 것을 알 수 있다. 

UDP는 데이터를 하나의 묶음으로 전송한다. UDP는 데이터 전송을 100퍼센트 보장하진 않는다. 그래서 여러 개의 메시지를 전송하였다면 순서가 뒤죽박죽인 채로 도착하거나, 아니면 아예 메시지 자체가 도착하지 않을 수도 있다. 그래서 UDP는 빠르고 가볍우나, 신뢰성이 떨어진다. (또한 UDP는 비연결성(connectionless)을 가진다)

- 비연결성 (connectionless)
    
    비연결성은 클라이언트가 서버에 요청을 하고 응답을 받으면 바로 TCP/IP 연결을 끊어 연결을 오래 유지하지 않는 것을 의미한다. 해당 속성을 이용하면 서버의 자원의 효율적 관리와 더불어 수많은 클라이언트의 요청에도 대응 가능해진다. 그러나, 한 번 통신을 하면 바로 끊어지는 특성 때문에 연결이 계속 필요할 때마다 일일이 연결을 새로 해야 하므로 그에 따르는 시간이 걸린다. 
    

이번에는 TCP를 이용해보자. 웹처럼 UDP에 비해 더 오랫동안 연결을 할 수 있다. TCP는 여러 개의 데이터들을 보낸 순서대로 전달한다. 만약 문제가 발생하면 다시 데이터를 보낸다. 이번에는 위 예제를 TCP를 이용하여 구현해보도록 하겠다.

```python
import socket
from datetime import datetime

server_address = ('localhost', 6789)
max_size = 1000

print('클라이언트 시작 시간: ', datetime.now())

# SOCK_STREAM : TCP 사용
client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

client.connect(server_address) # connect() : stream으로 연결
client.sendall(b'hi, server!')
data = client.recv(max_size)

print('{} {}: {}'.format(datetime.now(), 'someone replied', data.decode('utf-8')))

client.close()
```

이번에는 클라이언트쪽 파일부터 예로 들었다. 이전의 UDP 예제와 비슷하다. 그러나 위 예제의 주석에서 설명하듯 조금의 차이점들이 존재한다. 

```python
from datetime import datetime
import socket

server_address = ('localhost', 6789)
max_size = 1000

print('서버 시작 시간: ', datetime.now())
print('클라이언트로부터의 호출 대기 중...')

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(server_address)

# 최대 다섯 클라이언트와의 연결을 큐를 통해 대기하게끔 설정함.
server.listen(5)

client, addr = server.accept() # 첫 메시지가 오는대로 받음.
data = client.recv(max_size) # 1000 바이트 길이의 메시지를 받기로 설정

print('{} {}: {}'.format(datetime.now(), client, data))

client.sendall('절 부르셨나요?'.encode('utf-8'))
client.close()
server.close()
```

아까와 같이 cmd 명령 프롬프트 창을 두 개 띄워서 위 두 파일을 실행시켜보자. 먼저 서버 파일부터 실행한다.

![20230316_09061827.png](/images/2023-03-14/2023-03-14-network-in-python-3.png)

그러면 위와 같을 것이다. 왼쪽 창에 서버 파일을 실행시킨 모습이다. 이제 오른쪽 창에서 클라이언트 파일을 실행시켜보면 다음의 결과를 얻는다.

![20230316_09062758.png](/images/2023-03-14/2023-03-14-network-in-python-4.png)

TCP는 UDP와 달리, 다중 socket 호출에서 클라이언트-서버 연결을 유지시키고, 클라이언트의 IP 주소를 기억한다. 

socket을 더 복잡한 용도로 사용한다면 다음의 어려움들을 마주칠 수 있다.

- UDP는 메시지를 전송한다. 그러나 메시지의 길이는 제한되어 있고, 목적지에 제대로 전달된다는 보장이 없다.
- TCP는 byte들의 stream을 전송한다. 즉, 메시지를 전송하는 것이 아니다. 각각의 호출에 대해 시스템이 얼마나 많은 byte들을 송수신할 지 사용자는 알 수 없다.
- TCP를 통해 전체 메시지를 교환하고자 할 경우, 메시지 전송 길이 제한 때문에 전체 메시지를 일부 메시지들로 나눠서 보내야 하는데, 이 때 일부 메시지가 전체 메시지 중 한 부분이라는 것을 알려야 한다. 예를 들면 전체 메시지의 크기라든가, 고정된 메시지 크기(바이트) 등을 명시한다.
- 메시지들이 유니코드 문자열이 아닌 바이트이므로 파이썬의 bytes 자료형을 사용해야 한다.

# Domain Name System (DNS)

127.0.0.1과 같이 IP 주소는 보통 숫자들로 표현된다. 하지만 인간에게 있어서는 숫자보단 이름이 더 기억하기 쉽다.  DNS는 데이터베이스를 통해 IP 주소를 이름으로 바꾸거나 그 역과정으로 바꾸는 것도 가능케 해주는 인터넷 서비스이다. 

몇몇 TCP, UDP 포트 숫자들은 IANA라고 하는 특정 인터넷 서비스로부터 이미 예약되어 있는 경우가 있다. 예를 들어, HTTP의 이름은 http로 표현되고, TCP port로 80이 부여되어 있다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [모듈(Module)의 개념 , 결합도와 응집도 , 패인(Fan in) / 팬아웃(Fan out)](https://naminal.tistory.com/171)

[3] [What is the meaning of fan-out](https://softwareengineering.stackexchange.com/questions/420986/what-is-the-meaning-of-fan-out)

[4] [HTTP, Stateless, Connectionless, HTTP 메시지 개념](https://velog.io/@duarufp06/HTTP-Stateless-Connectionless-HTTP-메시지-개념)

[5] [IANA](https://ko.wikipedia.org/wiki/IANA)