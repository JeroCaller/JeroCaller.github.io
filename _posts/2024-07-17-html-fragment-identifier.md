---
title: "[HTML] Fragment identifier"
category: "HTML"
tag: ["web", "HTML", "fragment identifer"]
---

웹 페이지 내용이 너무 길어 스크롤도 길어지는 경우, 해당 웹 페이지의 중간 내용부터 보게끔 하려면 어떻게 해야할까? 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div>
        <h1 id="intro">1. 서론</h1>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
        </p>
    </div>
    <div>
        <h1 id="main">2. 본론</h1>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
        </p>
    </div>
    <div>
        <h1 id="conc">3. 결론</h1>
        <p>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. 
            Enim aspernatur sint voluptate amet facilis tenetur sunt est commodi accusamus. 
            Porro cumque incidunt quo ullam adipisci veniam magni nobis eos aliquid?
        </p>
    </div>
</body>
</html>
```

예제 1-1

![예제 1-1 실행결과](/images/2024-07-17/html-fragment-identifier/1.png)

예제 1-1 실행결과

위 예제 코드를 브라우저에서 실행시키면 항상 해당 페이지의 맨 윗 부분이 “1. 서론”으로 시작한다. 만약 해당 페이지에서 본론 또는 결론으로 이동하고자 한다면 먼저 위 예제 코드에서처럼 이동하고자 하는 곳에 id=”이름”을 지어준다. 그 후, 브라우저의 url 입력창에 있는 주소 맨 끝에 #id명을 작성하면 된다. 예를 들어, 위 예제 페이지에서 “2.본론” 영역으로 이동하고자 한다면 url 맨 마지막에 다음과 같이 입력한다. 

`http://127.0.0.1:51046/practice_html/fragment-identifier.html#main`

그러면 화면은 다음과 같을 것이다. 

![예제 1-1 실행결과2](/images/2024-07-17/html-fragment-identifier/2.png)

예제 1-1 실행결과2

화면 맨 위 내용이 “1. 서론”이 아닌 “2. 본론”으로 바뀐다. 이렇게 id값이 지정된 특정 요소를 “조각”이란 의미의 fragment라 하고, 이 부분을 가리키는 #id명과 같이 #으로 시작하는 것을 fragment identifier라 부른다. 즉, #id명이 해당 fragment를 가리키는 것이다. 이러한 fragment identifier를 이용하여 해당 영역으로 이동하고자 한다면 위에서와 같이 url 맨 끝에 #으로 시작하는 fragment identifier를 입력하면 된다. 

웹 페이지에는 수많은 텍스트, 영상, 이미지 등의 자원들이 있는데, 이를 resource라고 부르기도 한다. 이러한 리소스들에는 각자 고유한 식별자가 붙어 서로 구분될 수 있는데, 이 때 통일된 방식으로 리소스들을 식별한다. 이러한 형태의 식별자를 URI(Uniform Resource Identifier)라 한다. 

한 편, 이러한 resource들은 인터넷 상에 광범위하게 있을 것이다. 이 세상에 사이트도 무수히 많으니, 그 중 특정 사이트의 특정 리소스에 접근하고자 한다면 URL(Uniform Resources Locator)을 사용해야 한다. [https://www.naver.com](https://www.naver.com) 와 같이 브라우저 맨 위 url 창에 볼 수 있는 주소들이 모두 url이다. url의 약자는 “통합 자원 위치자”로, 역시 통합된 규약을 통해 특정 resource의 식별자와 그 위치를 표기한다. 간단히 말해 url은 식별자에 (네트워크 상의) 위치까지 부여된 개념이라 보면 되겠다. 

사실 우리가 웹 상에서 특정 사이트에 방문했을 때, url 창에서 볼 수 있는 url 주소는 가상 경로이다. 해당 사이트를 구성하는 html 등의 파일들이 서버 어딘가에 위치해 있을 것이고, 이를 불러오려면 해당 파일들의 경로를 명시해야 한다. 하지만 네트워크 상에서 로컬 기기 내에서의 파일 경로인 “로컬 경로”에 직접 접근하는 방식은 금지되어 있다 (인터넷에서 특정 로컬 기기의 특정 파일에 접근하는 셈이 되니 보안적으로 위험해서 금지한 것이 아닐까). 따라서 대신 가상 경로를 쓰는 것이다. 모든 웹 사이트들의 url 주소는 사실 해당 사이트들을 구성하는 html 등의 파일의 로컬 경로를 대체하는 가상 경로이고, 즉, 그 파일을 가리키는 것이다. naver 주소를 치면 나오는 첫 화면도 html 파일로 구성되어 있을 텐데, 이 html 파일에 [https://www.naver.com](https://www.naver.com) 이라는 가상 경로가 붙어있는 것이다. 

이러한 url의 뒤에 fragment identifier를 붙인다는 것은, 해당 url의 파일을 구성하는 특정 리소스를 가리킨다는 것이다. 즉, fragment identifier는 파일 내 특정 리소스의 URI을 참조하는 포인터 같은 개념이라고 볼 수 있겠다. 

정리하자면 fragment idenfier는 하나의 웹 페이지 내에서 특정 리소스를 가리키는 식별자를 의미하며, 이 식별자는 해당 리소스의 URI을 대신하는 “별명”과도 같은 식별자라 볼 수 있겠다.

---

References

[1] [Fragment Identifier \| HTML & CSS Wiki \| Fandom](https://htmlcss.fandom.com/wiki/Fragment_Identifier)

[2]  [초기 페이지 구현 - 생활코딩](https://opentutorials.org/course/3281/20566)

[3] 내 블로그 참고

[[Python][Web] 웹 서비스와 자동화](https://jerocaller.github.io/python/web/web-service-and-automation/)