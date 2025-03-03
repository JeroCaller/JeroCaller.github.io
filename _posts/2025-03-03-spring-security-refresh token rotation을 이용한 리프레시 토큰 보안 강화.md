---
title: "[Spring Security] RTR(Refresh Token Rotation)을 이용한 리프레시 토큰 보안 강화"
category: "Spring"
tag: ["Spring", "Spring boot", "Spring Security", "인증", "보안", "Authentication", "Authorization", "Auth", "JWT", "Token", "Access Token", "Refresh Token", "RTR", "Refresh Token Rotation", "Token Reuse Detection", "Reuse Detection"]
---


이 글은 이전 글인 [[Spring Security] JWT 기반 인증](/spring/spring-security-jwt-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D/) 글을 배경 지식으로 삼아 작성한 글이니 해당 글도 참조하면 좋습니다.

이전 글에서는 JWT 기반 인증에 대해 다뤘다. Access Token만 존재해서는 사용자 입장에서의 편의성과 보안성 이 둘을 모두 만족시키기 어렵기 때문에 Refresh Token이란 개념을 추가하여 이를 보완할 수도 있다는 것을 살펴보았었다. Refresh Token은 사용자가 인증 요청 시 새로 생성되어 DB에 저장되며, Access Token 만료 시 이를 재발급하기 위한 용도로 사용된다. 보통 Access Token은 30분에서 1, 2시간 정도, Refresh Token은 1주일에서 1개월 정도로 만료 시간을 잡는다고 한다. Refresh Token의 존재로 인해 Access Token의 만료 시간을 짧게 잡아도 이를 재발급하면 되기에 사용자 입장에서는 번거롭게 매번 재로그인할 필요가 없고, Access Token의 만료 시간을 짧게 잡아도 되기에 설령 해당 토큰이 탈취되더라도 금세 만료되기에 해커가 사용자의 계정을 악용할 여지가 줄어든다는 보안적인 장점도 챙길 수 있게 된다. 즉, Refresh Token은 편의성과 보안성을 모두 취한 방법이라 볼 수 있다. 

보안 강화를 위해 Refresh Token을 쿠키에 실어 응답으로 클라이언트에 전송할 때에는 자바스크립트로 해당 쿠키 내용을 조작하지 못하도록 HttpOnly로 설정하고, HTTPS라는 좀 더 안전한 프로토콜 환경에서만 주고받도록 설정할 수 있는 등의 여러 추가적인 보안 작업을 설정할 수 있다. 하지만 그럼에도 보안적으로 완벽하지는 않다고 한다. 그래서 Refresh Token이 탈취된다면 Access Token에 비해 만료 기간이 길기에 보안적으로 매우 위험하다고 할 수 있겠다. 이번 글에서는 이를 보완하기 위한 방법인 RTR과 이와 접목한 Token Reuse Detection에 대해 살펴보겠다.

# RTR (Refresh Token Rotation)

RTR(Refresh Token Rotation)은 Refresh Token 탈취에 대비한 보안적 방법으로, Access Token이 만료되어 이를 갱신할 때 Refresh Token도 새로 생성하여 클라이언트에 제공하는 방법이다. 다음은 RTR 방식 사용 시 Access Token, Refresh Token에 대한 클라이언트와 서버 간 통신 과정을 그린 그림이다. 

![그림 1-1. RTR(Refresh Token Rotation) 방식 사용 시 클라이언트-서버 간 통신 과정.](/images/2025-03-03/spring-security-refresh%20token%20rotation%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%A6%AC%ED%94%84%EB%A0%88%EC%8B%9C%20%ED%86%A0%ED%81%B0%20%EB%B3%B4%EC%95%88%20%EA%B0%95%ED%99%94/1.png)

그림 1-1. RTR(Refresh Token Rotation) 방식 사용 시 클라이언트-서버 간 통신 과정.

기존 방식과의 차이점은 Access Token이 만료되어 클라이언트 측에서 해당 토큰 재발급을 위한 요청을 서버에 전송할 때 발생한다. 기존 방식에서는 Access Token 재발급 요청이 들어오면 서버에서는 요청으로 들어온 Refresh Token이 DB 상에 존재하는지와 만료 시간 여부 등을 체크한 뒤, 검증이 되었다면 Refresh Token에는 그 어떠한 변화를 주지않고 그저 Access Token만을 새로 생성하여 응답해줄 뿐이었다. 그러나 RTR 방식에서는 Access Token뿐만 아니라 Refresh Token도 새로 생성하여 이를 DB에 저장 후 클라이언트에 새로 생성된 두 토큰 모두 전송하는 방식이다. 

클라이언트 입장에서는 새로운 Refresh Token을 받는다는 점 이외에는 기존 방식과 별 다른 차이점을 느끼지 못한다. 

이러한 방식을 취하면 사실상 Refresh Token도 Access Token과 같은 만료 시간을 갖는 것이나 마찬가지기에 두 토큰이 탈취당하더라도 신뢰할 수 있는 사용자 측에서 Access Token을 갱신시키면 Refresh Token도 같이 갱신되기에 해커의 입장에서는 두 토큰 모두 짧은 만료 시간을 가진다는 특성에 의해 오랫동안 사용자 계정을 악용할 수 없게 된다. 

> RTR 방식이라 함은 위에서 설명한 것처럼 클라이언트가 Access Token의 재발급을 요청할 때마다 Refresh Token도 새로 생성하는 방법이 일반적인 것으로 보인다. 그러나 다른 방식으로도 Refresh Token 재발급을 수행할 수 있다고 한다. 
1. 시간 기반 : 특정 시간을 지정해놓고 해당 시간이 지나면 자동으로 Refresh Token도 갱신하는 방법. 그러나 이 경우 클라이언트 측이 토큰의 만료 시간을 계속 추적해야 한다는 번거로움이 있다.    
2. 사용 횟수 기반 : 특정 요청 횟수를 정해놓고 해당 요청 횟수가 지나면 자동으로 Refresh Token을 갱신하는 방법. 예를 들어 100회의 요청 이후에는 자동으로 Refresh Token을 재발급하는 방식이다.   
3. 이벤트 기반 : 사용자가 패스워드나 그 외 사용자 정보를 변경한 경우 등의 특정 이벤트 발생 시 재발급하는 방식.
> 

# 토큰 재사용 감지 (Token Reuse Detection)

앞서 언급한 RTR 방식은 Token Reuse Detection 방식과 결합하면 보안을 더 강화시킬 수 있다. 이 방식은 신뢰할 수 있는 사용자가 Access Token 재발급 요청으로 인해 무효화시킨 이전 Refresh Token들을 서버 측에서 기억하고 있다가, 이미 무효화된 이전 Refresh Token으로 요청이 들어온 경우, 이를 신뢰할 수 없는 공격자가 요청한 것으로 간주하여 현재 유효한 Refresh Token도 무효화시키는 방법이다. 이 경우 신뢰할 수 있는 사용자 측에게는 재로그인 요청을 하도록 한다. 엄밀히 하고자 하는 경우, 클라이언트의 Access Token 갱신 요청에 의해 만료된 모든 Refresh Token을 Token Family라고 하는데, 이 Token Family들을 서버에 기록하고 있다가 이 중 하나라도 해당되는 Refresh Token 요청이 들어오면 현재 유효한 가장 최신의 Refresh Token도 무효화시켜 보안을 강화시킬 수 있다. 간단히 하고자 하는 경우, 가장 최근에 무효화시킨 Refresh Token만을 기억하여 해당 토큰만을 기준으로 재사용 감지 방법을 적용시킬 수도 있겠다. 

> Replay Attack : 해커가 원본 메시지와 같은 자격증명을 얻기 위해 이전의 메시지를 가로채어 다시 서버에 전송하는 방법. 여기서는 이미 기존에 사용된 토큰을 요청하는 Replay Attack을 재사용 감지 기법을 통해 막을 수 있는 것이다.
> 

RTR과 재사용 감지 방법을 결합했을 때 보안적으로 더 안전한 이유를 살펴보겠다. 해커가 사용자의 토큰을 탈취한 상태에서, 사용자가 먼저 토큰 갱신 요청한 경우와, 해커가 먼저 토큰 갱신을 요청한 경우로 나눠 살펴보겠다. 

다음은 신뢰할 수 있는 사용자가 먼저 토큰 갱신 요청을 하는 경우이다.

![그림 2-1. RTR 및 Reuse Detection 사용 시 신뢰할 수 있는 사용자가 먼저 토큰 갱신 요청을 하고 그 후에 해커가 토큰 갱신 요청을 하는 경우의 그림.](/images/2025-03-03/spring-security-refresh%20token%20rotation%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%A6%AC%ED%94%84%EB%A0%88%EC%8B%9C%20%ED%86%A0%ED%81%B0%20%EB%B3%B4%EC%95%88%20%EA%B0%95%ED%99%94/2.png)

그림 2-1. RTR 및 Reuse Detection 사용 시 신뢰할 수 있는 사용자가 먼저 토큰 갱신 요청을 하고 그 후에 해커가 토큰 갱신 요청을 하는 경우의 그림.

1. 신뢰할 수 있는 사용자가 먼저 Access Token(A1) 및 Refresh Token(R1)을 가지고 Access Token 재발급 요청을 서버에 전송한다. 그러면 서버에서는 기존 R1을 무효화된 상태로 표시하여 DB에 저장하고, 새로 생성한 Refresh Token(R2)도 DB에 저장한다. 새로운 Access Token(A2) 생성과 함께 두 토큰을 클라이언트에 응답한다. 
2. 해커가 기존에 사용자로부터 탈취한 토큰 R1을 이용하여 새 Access Token 발급 요청을 한다. 이 경우 이미 R1은 무효화된 상태이기에 서버는 DB 조회로부터 만료된 R1으로 요청이 들어옴을 확인한다. 서버는 이를 신뢰할 수 없는 사용자의 요청으로 인식하고 현재 유효한 R2 토큰 마저도 무효화시킨다. 그 후 클라이언트(여기선 해커)에게는 접근 거부 응답을 전송한다. 
3. 신뢰할 수 있는 사용자가 R2를 가지고 새 토큰을 발급 요청한다. 그러나 이 시점에서 이미 R2는 무효화된 상태이므로 역시 접근 거부 응답을 받는다. 이 사용자는 이제 재로그인을 거쳐서 아예 새로운 토큰을 발급받아야 한다. 

이러한 과정을 통해 사용자가 먼저 토큰 갱신 요청을 하는 경우에도 토큰 탈취로 인한 피해를 최소화할 수 있다. 

다음은 해커가 탈취한 토큰을 이용하여 사용자보다 먼저 Access Token 갱신 요청을 하는 경우이다.

![그림 2-2. RTR 및 Reuse Detection 사용 시 해커가 사용자보다 먼저 갈취한 토큰으로 새 토큰 발급 요청을 하는 경우의 그림.](/images/2025-03-03/spring-security-refresh%20token%20rotation%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%A6%AC%ED%94%84%EB%A0%88%EC%8B%9C%20%ED%86%A0%ED%81%B0%20%EB%B3%B4%EC%95%88%20%EA%B0%95%ED%99%94/3.png)

그림 2-2. RTR 및 Reuse Detection 사용 시 해커가 사용자보다 먼저 갈취한 토큰으로 새 토큰 발급 요청을 하는 경우의 그림.

1. 해커가 기존에 사용자로부터 탈취한 Refresh Token(R1)으로 새 Access Token 재발급을 요청한다. 서버에서는 R1을 무효화한 상태로 DB에 기록한 후, 새로운 Access Token(A2) 및 Refresh Token(R2)를 발급하고, R2도 DB에 저장한다. 그 후 이 두 토큰을 해커에게 전송한다. 
2. 신뢰할 수 있는 사용자가 R1을 가지고 새 Access Token을 요청한다. 이 시점에서 R1은 이미 무효화된 상태이므로 서버는 DB 조회를 통해 이를 감지하여 현재 유효한 R2 토큰까지도 무효화시킨다. 사용자에게는 접근 거부 및 재로그인이 필요하다는 응답을 보낸다.
3. 해커가 R2와 함께 새 Access Token 재발급 요청을 한다. 이 시점에서 이미 R2는 무효화되었으므로 서버는 이를 비정상적인 요청으로 인식하여 역시 접근 거부를 한다. 

Reuse Detection 없이 RTR 방식만 사용하는 경우, 해커가 사용자보다 먼저 Refresh Token으로 갱신 요청을 하면 이후 신뢰할 수 있는 사용자가 갱신 요청을 하면 이미 무효화된 토큰으로 요청하는 셈이므로 요청이 거부된다는 점에서는 동일하다. 그런데 해커의 입장에서는 로그아웃을 하지 않는 이상 무한정 Access Token과 Refresh Token을 가지고 사용자의 계정을 악용할 수 있게 된다는 문제점이 남는다. 그러나 Reuse Detection을 이용하면 이러한 시나리오까지 방지할 수 있음을 알 수 있다. Reuse Detection을 사용하면 설령 해커가 신뢰할 수 있는 사용자보다 먼저 토큰 재발급 요청을 하더라도 이후 신뢰할 수 있는 사용자가 똑같이 토큰 재발급 요청을 하면 사용자와 해커 모두의 토큰이 무효화되기에 결과적으로 사용자의 보안을 지킬 수 있는 것이다. 

본래 토큰 기반 인증 방식에서는 클라이언트가 전송한 토큰만으로는 서버측에서는 이 토큰을 전송한 자가 신뢰할 수 있는 사용자인지 아니면 악의적인 목적을 가진 공격자인지 구분할 방도가 없었다. RTR과 Reuse Detection은 이러한 취약점을 보강한 방식으로, 하나의 Token Family는 하나의 사용자만 사용 가능하다고 가정하고, 기존에 만료된 Token Family 중 하나로 요청하는 경우 이를 또 다른 알 수 없는 사용자가 요청한 것으로 간주함으로써 사용자의 보안을 강화하는 방식인 것이다. 

# RTR 및 Reuse Detection의 예상되는 단점과 이에 대한 보완책

RTR과 Reuse Detection을 사용하면 Refresh Token이 탈취당하더라도 짧은 시간 내에 이를 무효화시킴으로써 악용될 기회와 시간을 최소화시킨다는 보안적 장점을 얻을 수 있음을 확인하였다. 그러나 이 방식들에 대한 예상되는 단점으로 다음이 있다. 

- Access Token을 재발급할 때마다 Refresh Token도 재발급하고 이를 DB에 저장하므로 서버 또는 DB에 대한 접근이 상대적으로 많아져 서버 또는 DB에 대한 부담이 더해질 것으로 예상된다. 이는 사용자의 수가 많아질수록 그 문제점이 부각될 것으로 보인다.
- 기존 Refresh Token 사용 방식에 비해 RTR 및 Reuse Detection을 구현하기 위한 코드도 추가되는 것이므로 코드의 복잡성이 늘어난다.

첫 번째 문제에 대한 보완책으로, 인증 서버를 따로 두는 방식을 통해 다른 기능을 하는 서버로까지의 부하를 방지하도록 하는 것을 떠올릴 수 있겠다. 또한, 기존 DB는 하드디스크 또는 SSD와 같은 보조기억 장치에 저장된 데이터를 불러와 주기억장치인 RAM에 load해야하는 과정이 있어 속도적인 측면에서 불리하나, Redis와 같은 인메모리(In-memory) 방식의 DBMS를 사용하면 주기억장치에 저장된 데이터를 바로 불러올 수 있어 속도의 이점이 있다고 한다. 특히 사용자가 많은 대규모 사이트일 경우 기존 DBMS 대신 인메모리 방식의 DBMS를 사용하는 것을 고려해볼 수도 있겠다. (단, 인메모리 방식의 DBMS는 말 그대로 주기억장치, 즉 휘발성 메모리에 데이터를 저장하여 사용하는 방식이기에 혹시라도 전원이 꺼지면 모든 데이터가 날아간다는 단점이 있다. 하지만 그렇다 하더라도 Refresh Token의 정보만 날아가는 것이기에 사용자 입장에서는 재로그인만 하면 된다.)

하지만 이 보완책을 쓰더라도 서버의 메모리이건 DB이건 서버 측의 자원을 소모한다는 점에서는 차이가 없을 것이라 본다. 이는 토큰의 본래 장점인 stateless의 성격을 다소 잃어버리는 약점이라고 할 수 있겠다. 

결국 사용자의 편의성, 보안성, 서버 측의 부담 등 여러 가지 조건들을 고려하여 가장 우선적으로 지켜야할 것을 먼저 고려한 후 그에 맞는 인증 방식을 세우는 것이 좋을 것이다. 만약 인증, 인가가 별로 필요없고, 필요하더라도 사용자의 민감 정보를 저장하지 않는 사이트라면 RTR 및 Reuse Detection을 무조건 사용해야하는가에 대한 필요성을 재고해볼 수 있겠다. 엄격한 보안이 필요하지 않은 상황에서 이 방식들을 사용하면 없어도 될 서버의 부담이 가해져 불필요한 비용이 발생하는 것이나 다름없기 때문이다. 또는 만약 보안성과 서버의 부담 완화 이 두 가지 토끼를 모두 잡고자 한다면 사용자의 편의성을 포기하더라도 Access Token만 사용하는 방식을 고려해볼 수도 있겠다. 실제 몇몇 공공기관 또는 금융 측 사이트에서 로그인을 하면 사이트 맨 위에 로그인 유효 시간이 대게 15~30분으로 뜨면서 이 시간 이내에 아무런 활동이 없는 경우(마우스 클릭 또는 키보드 입력 없을 시)에 무조건 재인증하도록 유도하는 경우도 있고, 또는 이러한 조건도 없이 무조건 해당 시간이 지나면 재인증하도록 요구하는 사이트들도 있다. 이 사이트들의 인증 방식이 토큰 기반 인증인지 아니면 세션 기반 인증으로 하고 세션 유효 시간을 30분으로 설정한 것인지까지는 잘 모르겠지만 어쨌건 상황에 따라서는 이러한 방식을 택하는 것도 고려해볼 수 있겠다. 

즉, 결론적으로 말하자면 현재 제작하고자 하는 사이트에서 사용자의 편의성이 중요한지, 아니면 보안성 또는 서버 측의 부담 완화가 더 중요한지 등 여러 요소들을 따진 후에 그에 맞는 적절한 인증 방식을 사용하는 것이 좋겠다. 

---

References

[1] Refresh Token, RTR(Refresh Token Rotation), Redis

[[인증/인가] RefreshToken은 왜 Redis를 사용해 관리할까? (with. RTR 방식)](https://pgmjun.tistory.com/125)

[2] 참고 자료) RTR

[인증과 인가를 안전하게 처리하기 (Refresh Token Rotation)](https://velog.io/@chchaeun/%EC%9D%B8%EC%A6%9D%EA%B3%BC)

[3] RTR

[The Developer’s Guide to Refresh Token Rotation](https://www.descope.com/blog/post/refresh-token-rotation)

[4]  JWT Refresh Token Rotation And Reuse Detection

[jwt authentication](https://aryanshaw.hashnode.dev/jwt-token-rotation-and-reuse-detection)