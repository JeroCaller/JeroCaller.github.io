---
title: "[HTML] Inline, Block element"
category: "HTML"
tag: ["web", "HTML", "inline", "block", "element"]
---

HTML의 요소는 크게 두 종류로 나눌 수 있다. 하나는 inline 요소, 또 하나는 block 요소이다. 이 둘의 차이점은 다음과 같다. 

- block 요소는 그 내용의 크기에 상관없이 화면의 한 행을 차지하는 박스를 형성시킨다. 이러한 이유로, 다른 요소들이 block 요소들과 수평 위치에 있을 수 없고, 그 아래에 수직적으로 배치될 수밖에 없다. 물론 css를 통해 block 요소 옆에 수평적으로 다른 요소를 배치할 수는 있다. 다만 개발자가 그렇게 코드로 명시하지 않는 한 기본적으로는 한 행을 차지한다. 
반면 inline 요소는 컨텐츠의 크기만큼만 그 영역을 차지한다. 그렇기에 해당 요소 옆에 다른 요소가 들어올 수 있어 수평적 배치가 가능해진다.
- 사실 inline 요소는 block과 달리 박스 자체가 형성되지 않는다. 그렇기에 박스의 크기를 결정하는 width, height와 padding, margin 등의 여백을 지정할 수 없다. 다만, 좌우 여백은 지정 가능하다. 여백의 경우, 컨텐츠를 둘러싸는 테두리가 박스 역할을 하는데, inline 요소에는 이 박스 자체가 생성되지 않으므로 내외부 공간 구분이란 의미 자체가 없어지기 때문에 여백을 지정할 수 없는 것이다.
- block 요소 내부에는 inline 요소를 포함할 수 있으나, 정반대는 불가능하다. 이 역시 inline 요소에는 block 요소와 달리 박스를 형성하지 않기 때문이다. 
inline 요소 내부에 block 요소를 포함하는 코드 작성 시 웹 화면에서는 정상적으로 보이나, 추후 복잡한 작업 시 문제가 발생할 수 있기에 주의해야한다.
- inline Element ex) img, a, span, Pseudo element selectors, …
- block Element ex) div, p, h, ul, …

다음은 앞서 설명한 것들 중 일부를 보여주는 예제이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>inline and block</title>
    <style>
        .block {
            background-color:beige;
        }
        span {
            background-color: chocolate;
        }
        a {
            background-color: cornflowerblue;
        }

        .block-2 {
            border: 1px solid orange;
            width: 200px;
            height: 100px;
        }
        .span-size {
            width: 200px;
            height: 100px;
        }
        
        .mar-pad {
            border: 1px solid blue;
            margin: 100px;
            padding: 100px;
        }
    </style>
</head>
<body>
    <!-- 줄바꿈 여부 확인 -->
    <div class="container">
        <div class="block">이것은 div</div>
        <span>이것은 span</span>
    </div>

    <hr/>

    <div class="container">
        <span>이것은 span</span>
        <a>이것은 a</a>
    </div>

    <!--
        다음은 inline 요소 내부에 
        block 요소를 포함하는 코드 
    -->
    <a href="">
        <p>안녕하십니까. p 태그입니다.</p>
    </a>

    <!-- 크기 지정하기 -->
    <div class="container">
        <div class="block-2">이것은 div</div>
        <span class="span-size">이것은 span</span>
    </div>

    <!-- 여백 지정하기 -->
    <hr/>
    <div class="mar-pad">이것은 div</div>
    <hr/>
    <span class="mar-pad">이것은 a</span>
</body>
</html>
```

예제 1-1

![예제 1-1 실행결과](/images/2024-07-18/html-inline%2C%20block%20element/1.png)

예제 1-1 실행결과

# 느낀 점

박스 모델이란 것이 inline 요소든 block 요소든 모든 요소들을 박스로 본다고 알고 있었는데, block 요소만 박스로 보는 것 같다. 화면 상에서 볼 땐 inline 요소도 배경색 또는 경계선을 설정하면 박스처럼 보이지만, 그건 컨텐츠 내용 부분에만 적용되는 거라 inline은 박스 모델로 볼 수는 없다는 것 같다. 

---

References

[1] 에이콘아카데미 강남 강의