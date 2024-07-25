---
title: "[HTML] 이미지 맵"
category: "HTML"
tag: ["web", "HTML"]
---

이미지 맵은 전체 이미지 중 특정 영역에만 특정 링크를 다는 것을 말한다. 즉, `<a href=”mysite.com”><img src=”image.jpg”/></a>` 와 같이 전체 이미지에 하나의 링크를 거는 것이 아닌, 하나의 이미지 내 부분 영역들마다 서로 다른 링크들을 달 수 있는 기술이다. 

HTML에서 이미지 맵을 사용하는 기본적인 형태는 다음과 같다.

```html
<img src="..." usemap="#myimage"/>
<map name="myimage">
    <area shape="circle" coords="111, 111, 100" href="mysite.com"/>
</map>
```

이미지 맵을 위해 `<map>` 태그가 같이 사용되며, 해당 요소의 자식 요소로 `<area>` 요소가 사용된다.  이미지에 연결할 링크들에 대한 부분 영역이 2개 이상인 경우, `<area>` 태그도 그 수에 맞게 여러 개 사용될 수 있다. `<area>` 요소를 통해 연결된 이미지의 부분 영역의 위치 및 해당 영역의 모양, 해당 영역과 연결할 하이퍼링크들을 설정할 수 있는 것이다. 이렇게 설정된 `<map>` 태그와 이미지 맵을 적용할 `<img>` 태그를 연결해줘야 이미지 맵이 적용된다. 이를 위해선 `<map>` 태그에는 name 속성을 통해 섹션 이름을 정한다. 그 후, `<img>` 태그의 usemap 속성값을 똑같은 값으로 사용하면 된다. 위 예제를 참고. 

이미지의 부분 영역에 대한 설정을 맡는 `<area>` 태그에 적용할 수 있는 속성들에는 다음이 존재한다. 

- shape : 부분 영역의 모양을 지정한다. 다음의 세 가지 중 하나의 속성값을 택할 수 있다.
    - circle: 원
    - rect:  사각형
    - poly:  다각형 (circle, rect 외의 도형들). 더 정교한 영역을 지정하고자 할 때.
- href: 지정된 영역 클릭 시 이동할 url
- coords: 영역 위치 및 크기 지정.
    - shape=”circle”일 경우, 원 중심점 위치 (x, y)와 반지름 길이를 기입. ex) coords=”x, y, r”
    - 전체 이미지를 x-y 좌표로 보고 px 단위 등으로 지정하면 됨.
    - 특정 좌표 추출을 위해 윈도우의 “그림판”에서 해당 이미지를 불러온 후, 특정 위치에 마우스 커서를 가져다 대면 왼쪽 아래에 해당 좌표가 뜬다.
    - shape=”rect”일 경우, 사각형의 좌상 꼭지점과 우하 꼭지점의 위치를 기입한다. ex) “x1, y1, x2, y2”

다음은 한 장의 이미지 내에서 두 구역에 각각 다른 링크를 매핑한 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>이미지 맵</title>
</head>
<body>
    <img src="../images/cutecat.jpeg" usemap="#cute-cat"/>
    <map name="cute-cat">
        <area shape="circle" coords="512, 317, 50" href="https://www.naver.com"/>
        <area shape="rect" coords="80, 284, 227, 495" href="https://www.google.com"/>
    </map>
</body>
</html>
```

예제 1-1

![1.PNG](/images/2024-07-25/html-image-map/1.png)

예제 1-1 실행결과

위 예제를 브라우저 환경에서 실행시키면, 위와 같은 한 장의 이미지가 뜬다. 여기서 고양이 얼굴을 누르면 네이버로, 초를 누르면 구글 사이트로 이동된다. 위 사진에서 초는 직사각형으로, 고양이 얼굴엔 원형으로 이미지 맵을 적용하였다. 

이러한 이미지 맵을 이용하면, 여러 링크들을 연결시킬 작은 이미지들을 여러 개 준비하여 각각의 이미지에 링크를 매핑하는 방식보다, 하나의 이미지만 준비하고 그 이미지의 각 부분에 여러 개의 링크들을 다는 방식으로 더 효율적으로 작업할 수 있을 것 같다. 여러 이미지들을 사용하면 하나의 화면에 이미지들을 배열하는 게 힘들텐데, 그냥 하나의 이미지로 이미지 맵을 이용하면 그런 고민을 할 필요가 없어지기 때문이다. 

# 이미지 맵을 편리하게!

한 편, 이미지를 업로드하고, 원하는 영역을 지정하기만 하면 해당 html 코드를 자동으로 생성해주는 사이트가 있다. 

[Online Image Map Editor](https://www.maschek.hu/imagemap/)

위 사이트를 들어가면 다음과 같은 화면일 것이다. 

![사진 1-1](/images/2024-07-25/html-image-map/2.png)

사진 1-1

위 사진의 빨간색 테두리 내에서 원하는 이미지를 업로드 할 수 있다. 여기서는 로컬 컴퓨터에 있는 사진을 꺼내 업로드하였다. 이미지를 선택했다면 오른쪽의 “accept” 버튼을 누른다. 

![사진 1-2](/images/2024-07-25/html-image-map/3.png)

사진 1-2

그러면 위 사진과 같이 업로드한 이미지가 뜬다. 해당 이미지 위 원하는 영역에 드래그해보자. 

![사진 1-3](/images/2024-07-25/html-image-map/4.png)

사진 1-3

여기서는 고양이 얼굴 부분과 초 부분을 드래그하였다. 사진 위 표 같은 곳에서 각 영역에 지정할 `<area>` 속성들의 값을 대입할 수 있다. 

위와 같이 영역 지정이 끝났다면 아래로 조금만 스크롤하면 앞서 지정한 영역들에 대한 html 코드가 다음과 같이 나온다. 

![사진 1-4](/images/2024-07-25/html-image-map/5.png)

사진 1-4

위 코드를 그대로 복붙하면 일일이 코드를 치지 않고도 쉽게 이미지 맵을 사용할 수 있다. 

---

References

[1] 에이콘아카데미 강남 강의

[2] [[HTML] 이미지맵 만들기 - 좌표값 쉽게 찾기!](https://velog.io/@seoyaon/HTML-이미지맵-만들기-좌표값-쉽게-찾기-mj7lfivg)

[3] [Online Image Map Editor](https://www.maschek.hu/imagemap/)