---
title: "[[CSS] transition"
category: "CSS"
tag: ["css", "web", "transition"]
---
# 트랜지션 (Transition)

Transition이란 영단어는 ‘전이’, ‘한 상태에서 다른 상태로의 변화’를 의미한다. CSS에서 transition이란, 특정 웹 요소의 스타일 속성값을 다른 속성값으로 변화시킬 때 일정 시간을 가지고 애니메이션을 보는 것처럼 천천히, 부드럽게 변화시키도록 하는 효과이다. 일종의 애니메이션 효과라 보는 것이 정확할 것이다. 

단순히 속성값이 달라진다고 해서 트랜지션이라고 보기는 힘들다. 예를 들어, div 요소를 이용하여 조그만한 상자를 만들었고, 이 상자에 마우스 커서를 가져다 대면(hover) 그 상자의 배경색이 변경되도록 하였을 때, transition을 사용하지 않으면 마우스 커서가 그 상자에 닿자마자 바로 배경색이 변경된다. 여기에 transition을 부여한다면 배경색이 변화하는 과정이 시간을 가지고 천천히 부드럽게 변화하도록 할 수 있다. 즉, transition은 일종의 애니메이션 효과이다.

다음은 빨간색이 배경인 두 상자가 있을 때, 하나는 마우스 커서를 올리면 바로 파란색으로 바뀌는 상자가 있고, 또 하나는 마우스 커서를 올리면 서서히 파란색으로 바뀌는 상자를 구현한 예제 코드이다. 이 예제를 통해 transition 효과를 부여했을 때와 부여하지 않았을 때의 차이점을 확인할 수 있다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .box-container {
            display: inline-block;
            margin: 30px;
        }
        .box-container p {
            text-align: center;
        }
        .box {
            border: 1px solid black;
            width: 200px;
            height: 200px;
            margin: 30px;
            background-color: red;
        }
        #apply-tr {
            transition: 2s;
        }
        #apply-tr:hover {
            background-color: blue;
        }
        #non-tr:hover {
            background-color: blue;
        }
    </style>
</head>
<body>
    <div id="container">
        <div class="box-container">
            <div class="box" id="non-tr"></div>
            <p>transition 효과를 부여하지 않은 경우</p>
        </div>
        <div class="box-container">
            <div class="box" id="apply-tr"></div>
            <p>transition 효과를 부여한 경우</p>
        </div>
    </div>
</body>
</html>
```

<video src="\resources\2024-01-16\css-transition\1.mp4" 
    controls="controls"
    muted="muted"
    width="70%"
></video>

예제 1-1 실행결과

## 속성들

앞서 설명한 transition을 적용하려면 우선 transition과 관련된 속성들을 알아야겠다. 

### transition-property

해당 속성은 transition을 적용할 속성을 지정하기 위해 사용되는 속성이다. 해당 속성에 입력할 수 있는 속성값으로 다음이 존재한다. 

- all : 요소 내 모든 속성에 transition 효과를 부여한다. 기본값이며, transition-property 속성 자체를 생략해도 마찬가지로 모든 속성에 부여된다.
- none: 그 어떤 속성에도 transition 효과를 부여하지 않는다.
- 특정 속성 이름을 적용 : transition 효과를 부여할 속성명을 지정한다. 이를 통해 모든 속성들 중 일부 속성들에만 transition 효과를 부여할 수 있다.

위 3개의 속성값들의 차이점을 확인하기 위해 다음의 예제 코드를 준비했다. 다음의 예제에서는 3개의 똑같은 크기의 상자를 준비했으며, 배경색, 경계선, 크기 이 3개의 속성이 변화하도록 고안하였다. 차이점은 각각의 상자에 transition-property가 다르게 설정되었다는 것이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <link rel="stylesheet" href="transition-property.css">
</head>
<body>
    <div id="page">
        <div class="box-container">
            <div id="tr-all"></div>
            <p>all</p>
        </div>
        <div class="box-container">
            <div id="tr-none"></div>
            <p>none</p>
        </div>
        <div class="box-container">
            <div id="tr-target"></div>
            <p>background-color</p>
        </div>
    </div>
</body>
</html>
```

```css
/* 실험을 위한 기본 설정*/
#page {
    margin: 50px auto;
    width: auto;
    display: flex;
    justify-content: center;
}

/* 실험을 위한 박스 기본 설정. */
.box-container {
    display: inline-block;
    margin: 30px;
}
.box-container p {
    text-align: center;
}

/* 실험을 위한 스타일 설정 */
#tr-all {
    background-color:cadetblue;
    border: 2px solid blue;
    width: 100px;
    height: 100px;
    transition-property: all;
    transition-duration: 3s;
}
#tr-all:hover {
    background-color: darkorange;
    border: 10px dashed brown;
    width: 300px;
    height: 300px;
}

#tr-none {
    background-color:cadetblue;
    border: 2px solid blue;
    width: 100px;
    height: 100px;
    transition-property: none;
    transition-duration: 3s;
}
#tr-none:hover {
    background-color: darkorange;
    border: 10px dashed brown;
    width: 300px;
    height: 300px;
}

#tr-target {
    background-color:cadetblue;
    border: 2px solid blue;
    width: 100px;
    height: 100px;
    transition-property: background-color;
    transition-duration: 3s;
}
#tr-target:hover {
    background-color: darkorange;
    border: 10px dashed brown;
    width: 300px;
    height: 300px;
}
```

<video src="\resources\2024-01-16\css-transition\2.mp4" 
    controls="controls"
    muted="muted"
    width="70%"
></video>

예제 2-1 실행결과

위 예제 코드와 실행결과를 같이 보면, transition-property의 속성값을 all으로 설정한 경우, 상자의 배경색, 경계선, 크기 이 세 가지 스타일들이 변화할 때 모두 transition 효과가 부여되어 애니메이션처럼 천천히 변화하는 것을 볼 수 있다. 반면 none으로 설정한 경우, 그 어떤 스타일에도 transtion 효과가 부여되지 않아, 마우스를 가져다 대면 곧바로 스타일이 변경되는 것을 알 수 있다. background-color로 설정한 경우, 배경색에만 transition 효과가 부여되어 천천히 바뀌는 반면, 경계선과 크기에는 해당 효과가 부여되지 않아 곧바로 변경되는 것을 알 수 있다. 

## transition-duration

해당 속성은 transition 효과 부여 대상에 대해, transition 지속 시간을 설정하는 속성이다. 즉, 기존 스타일에서 다른 스타일로 변경하는 그 시간을 설정할 수 있다. 단위는 초(s)와 밀리초(ms)를 사용할 수 있다. 앞서 살펴본 위 에제 2-1에서는 3초로 지정하여, 상자의 배경색, 경계선, 크기 모두 3초 동안 변하게 하여 애니메이션 효과를 부여하였다. 

## transition-delay

해당 속성은 transition 효과 시작 시간을 지연시키는 속성이다. duration과 마찬가지로 속성값으로 초(s)와 밀리초(ms)를 단위로 사용할 수 있다. 예를 들어 transition-delay: 3s; 로 지정하면 정확히 3초 후에 transition 효과가 시작된다. 

## transition-timing-function

해당 속성은 transition 효과로 인해 그 스타일이 시간에 따라 변경될 때 그 변경되는 속도의 변화를 지정할 수 있는 속성이다. 예를 들어 처음부터 끝까지 일정한 속도로 변하게 할 수도 있고, 처음에는 느리게 시작되었다가 중간엔 빨라지고, 다시 마지막에는 느리게 끝나도록 할 수도 있다. 

해당 속성값으로 다음이 사용될 수 있다. 

- ease : 시작은 천천히 → 중간은 점점 빠르게 → 마지막은 천천히
- linear : 일정한 속도를 유지하며 애니메이션을 보여준다.
- ease-in : 시작은 천천히
- ease-out : 마지막을 천천히
- ease-in-out : 시작과 마지막 양 끝 부분만 천천히.

## transition

transition 속성만으로도 앞서 설명한 모든 속성들의 값을 한꺼번에 지정할 수 있다. 순서는 상관없으나, duration과 delay의 속성값이 모두 존재하는 경우, 앞에 오는 값이 duration, 뒤에 오는 값이 delay 속성값으로 지정된다. 

## transition 지정하기

앞서 제시한 모든 예시들은 편의상 가상 클래스인 hover와 결합하여 사용하였다. 특정 요소에 마우스를 가져다 대기 전의 해당 요소의 스타일 규칙과, 마우스를 가져다 대었을 때의 스타일 규칙이 CSS 파일 내에 정의되어 있는데, transition 속성은 마우스를 가져다 대기 전의 스타일 규칙 안에 작성하면 된다. 예를 들어 .box 클래스가 있고, .box:hover 클래스가 있다면 transition 속성은 .box:hover가 아닌 .box 클래스에 지정하면 된다. 

정리하자면, transition 속성은 기존 스타일 규칙과 새 스타일 규칙 중 기존 스타일 규칙에 지정하면 된다. 


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [PoiemaWeb](https://poiemaweb.com/css3-transition)