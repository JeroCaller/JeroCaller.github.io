---
title: "[CSS] Animation"
category: "CSS"
tag: ["css", "web", "animation"]
---
# Animation 예제

CSS3에는 transition과 비슷한 animation 속성이 있다. 애니메이션 진행 중간에 바꿀 스타일 속성을 지정하여 애니메이션 효과를 내는 방식이다. 

먼저 예제 코드와 그 결과부터 보겠다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="animation1.css">
</head>
<body>
    <div id="container">
        <div id="image-frame"></div>
    </div>
</body>
</html>
```

```css
#image-frame {
    border: 1px solid black;
    width: 500px;
    height: 500px;
    margin: 100px auto;
    position: relative;
    animation-name: img-change;
    animation-duration: 20s;
    animation-iteration-count: infinite;
    animation-direction: alternate;
}
@keyframes img-change {
    from {
        background-image: url("..\\images\\colorful-waves.jpeg");
        background-size: cover;
    }
    17% {
        background-image: url("..\\images\\flowers.jpeg");
        background-size: cover;
    }
    34% {
        background-image: url("..\\images\\korean-new-year.jpg");
        background-size: cover;
    }
    51% {
        background-image: url("..\\images\\new_year_in_korea.jpeg");
        background-size: cover;
    }
    68% {
        background-image: url("..\\images\\plants.jpeg");
        background-size: cover;
    }
    85% {
        background-image: url("..\\images\\spaceman.png");
        background-size: cover;
    }
    to {
        background-image: url("..\\images\\desert-world.jpeg");
        background-size: cover;
    }
}
```

<video 
    src="/resources/2024-01-16/css-animation/1.mp4" controls="controls"
    muted="muted"
    width="50%"
></video>

위 예제의 CSS 파일을 잘 보면, from부터 to까지 그 사이에 17% 등 애니메이션 진행 지점마다 다른 스타일 속성을 지정하면 from부터 to까지 애니메이션이 진행됨에 따라 배경 이미지가 부드럽게 달라지는 것을 확인할 수 있다. 이를 이용하여 이미지 슬라이드를 구현한 것이다. 

이제 이러한 animation 속성에 대해 살펴보겠다. 

# @keyframes

앞서 예제 1-1의 CSS 파일 부분을 보면, 애니메이션 중간에 스타일 속성이 바뀌는 곳을 지정하여 서로 다른 스타일 속성을 적용한 것을 볼 수 있다. 이렇게 애니메이션 진행 과정에서 중간에 스타일 속성이 바뀌는 지점을 키프레임(keyframe)이라 한다. CSS에서는 이를 @keyframes 속성을 이용하여 구현할 수 있다. 위 예제에서 해당 속성 부분만 살펴보면 다음과 같다. 

```css
@keyframes img-change {
    from {
        background-image: url("..\\images\\colorful-waves.jpeg");
        background-size: cover;
    }
    17% {
        background-image: url("..\\images\\flowers.jpeg");
        background-size: cover;
    }
    34% {
        background-image: url("..\\images\\korean-new-year.jpg");
        background-size: cover;
    }
    51% {
        background-image: url("..\\images\\new_year_in_korea.jpeg");
        background-size: cover;
    }
    68% {
        background-image: url("..\\images\\plants.jpeg");
        background-size: cover;
    }
    85% {
        background-image: url("..\\images\\spaceman.png");
        background-size: cover;
    }
    to {
        background-image: url("..\\images\\desert-world.jpeg");
        background-size: cover;
    }
}
```

먼저 @keyframes 키워드 다음에는 해당 에니메이션 이름을 원하는대로 지정해준다. 여기서는 배경 이미지가 계속해서 변하는 애니메이션을 구현할 것이므로 ‘img-change’라고 지어줬다. 그리고 중괄호 안에는 from { … } 와 같이 선택자와 중괄호의 패턴이 반복적으로 나타나는 것을 확인할 수 있다. 여기서 애니메이션 중간 지점을 선택하여 그 지점에 대한 스타일 속성을 지정할 수 있는 곳이다. 여기서 from은 애니메이션의 맨 처음 부분(0%)이고, to 선택자는 애니메이션의 맨 끝 부분(100%)이다. 그리고 중간 부분을 지정하려면 선택자로 68%와 같이 백분율로 지정하면 된다. 그리고 해당 선택자의 내부에는 원하는 스타일 속성을 각각 지정하면 된다. 위 예제에서는 배경 이미지만 바꿀 것이므로 이미지 주소만 다르게 지정한 모습이다. 

# animation-name

앞서 keyframes로 애니메이션 중간 지점들의 스타일 속성들을 지정하였다면 이를 특정 속성에 적용해야 할 것이다. 이는 animation-name 속성으로 지정할 수 있으며, 해당 속성의 값으로 keyframes 속성에 지정한 애니메이션 이름을 그대로 쓰면 된다. 앞서 예제 1-1을 보면 다음의 코드가 있었다. 

```css
#image-frame {
    border: 1px solid black;
    width: 500px;
    height: 500px;
    margin: 100px auto;
    position: relative;
    animation-name: img-change;
    animation-duration: 20s;
    animation-iteration-count: infinite;
    animation-direction: alternate;
}
```

해당 키프레임을 #image-frame이란 선택자에서 사용하고자 하므로 해당 스타일 규칙 내에 `animation-name: img-change;` 와 같이 작성한 것을 볼 수 있다. 참고로 해당 속성에는 none이란 키워드도 사용 가능하다. 

# animation-duration

해당 속성은 애니메이션이 진행될 시간을 지정하며, 초(s) 또는 밀리초(ms) 단위로 지정할 수 있다. 위 예제 1-1에서는 해당 애니메이션을 20초 동안 진행하도록 하였다. 만약 이 속성값이 없으면 아예 애니메이션이 진행되지 않고, 위 예제의 경우에는 흰 바탕의 박스만 보일 것이다. 

# animation-direction

해당 속성 자체에 그 의미가 내포되어 있듯, 해당 속성은 애니메이션의 진행 방향을 설정하는 속성이다. 해당 속성에 대입할 수 있는 값으로는 다음이 존재한다. 

- normal : keyframe 속성 내 정의된 from에서 to 방향으로 진행되며, 기본값이다. 예제 1-1을 예로 들면, 이미지를 A, B, C, D 순으로 배치했을 때, A → B → C → D 순으로 이미지가 바뀐다.
- reverse : normal의 진행방향과 정반대로, to에서 from 방향으로 진행된다. D → C → B → A 순으로 이미지가 바뀌어 보일 것이다.
- alternate : 처음에는 normal이었다가 to에 다다르면 다시 역방향인 reverse로 진행되어 다시 from까지 간다. A → B → C → D → C → B → A
- alternate-reverse : alternate와 방향이 정반대이다. 처음에는 reverse처럼 진행하다가 애니메이션의 끝 부분에 도달하면 다시 normal 모드로 변하여 다시 역방향으로 진행된다. D → C → B → A → B → C → D

alternate, alternate-reverse 효과를 제대로 확인하려면 애니메이션 반복 횟수를 적어도 2번 이상으로 해놓아야 해당 효과를 확인할 수 있다. 반복 횟수 설정에 관련한 속성은 다음에 언급할 animation-iteration-count 속성에서 지정할 수 있다. 

# animation-iteration-count

해당 속성은 애니메이션의 반복 횟수를 지정하는 속성이며, 속성값으로 반복할 횟수를 숫자로 기입해도 되고, 만약 무한으로 반복하게 하고 싶으면 infinite 키워드를 사용하면 된다. 

# animation-delay, animation-timing-function

animation-delay는 애니메이션 시작까지 지연시킬 시간을 지정하며, 지정 방법은 animation-duration 속성과 동일하다. 

animation-timing-function은 transition과 마찬가지로 애니메이션 진행 도중의 속도 변화를 지정하는 속성으로, transition-timing-function과 똑같은 속성값을 이용할 수 있다. 

# animation

transition과 마찬가지로, animation도 animation 속성 하나로 앞서 언급한 모든 속성값들을 한꺼번에 지정할 수 있다. 순서는 상관없으며, 각 속성값들을 구분하기 위해 그 사이에 공백 한칸을 넣는다. 그리고 keyframes 속성으로 지정한 애니메이션이 2개 이상이고, 이 둘 이상의 애니메이션을 한꺼번에 지정하고자 할 경우, 쉼표(,)로 구분하여 기입하면 된다.


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석
