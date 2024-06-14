---
title: "[CSS] 텍스트 스타일"
category: "CSS"
tag: ["web", "css", "style", "text", "text style"]
---
# 글꼴 스타일

## font-family 속성

웹 페이지 내 텍스트의 글꼴 설정은 font-family 속성을 이용한다. 다음은 일부 텍스트 글꼴을 해당 속성을 이용해 바꾸는 예제이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .other_fam {
            font-family: 'Times New Roman', Times, serif;
        }
    </style>
</head>
<body>
    <p>안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="other_fam">안녕하세요. 12345 !@#$% abcde ABCDE</p>
</body>
</html>
```

![예제 1-1 실행결과](/images/2023-12-06/css-text-style/1.png)

예제 1-1 실행결과

웹 페이지의 텍스트 글꼴은 해당 웹 페이지를 보는 사용자 로컬 디바이스에 설치된 글꼴을 이용하는데, 웹 페이지 제작자가 지정한 글꼴이 사용자 로컬 디바이스에는 존재하지 않아 해당 글꼴 속성이 제대로 적용되지 않을 수도 있다. 따라서 이러한 경우에 대비해 일종의 2, 3차 글꼴도 설정한다. 위 예제 1-1에서는 ‘Times New Roman’을 1차 글꼴, 해당 글꼴이 적용되지 않을 경우를 대비한 2차 글꼴이 “Times”, 해당 글꼴도 없다면 “serif” 글꼴을 적용하도록 지정한 모습이다. 

글꼴 간 구별은 쉼표(,)로 구분하며, 글꼴 이름 사이에 띄어쓰기가 있는 경우, 해당 글꼴 이름은 큰, 또는 작은 따옴표로 둘러싼다.

## font-size 속성

해당 속성은 글자의 크기를 조절하는 속성이다. 해당 속성의 값으로는 여러 유형의 속성값을 사용할 수 있다. 

- 키워드 속성값

미리 정해진 키워드로 글자 크기를 지정할 수 있다. 지정된 키워드는 다음이 존재하며, 위로 갈수록 더 작은 크기, 아래로 갈 수록 더 큰 크기 순이다.

* xx-small
* x-small
* small
* medium
* large
* x-large
* xx-large

-----

- 단위 속성값

글자 크기 관련 단위로 다음이 존재한다.

- px: 모니터의 1픽셀(1px)을 기준으로 한 비율값 지정.
- pt: 일반적인 문서에서 사용되는 포인트 단위
- em: 부모 요소에 지정된 글꼴의 영대문자 M의 너비를 1em으로 하는 단위
- rem: 문서 시작 부분에 지정한 크기를 1rem으로 하는 단위

여기서 px, pt는 절대 크기의 단위이며, em, rem은 상대 크기의 단위로, 컴퓨터뿐만이 아니라 모바일 등의 다른 환경의 디바이스 화면도 생각할 때 상대 단위를 사용한다고 한다. 

- 백분위 속성값

부모 요소에 지정된 글자 크기의 특정 비율로 설정하여 글자 크기를 결정하는 방법이다. 이 방법을 사용하고자 한다면 부모 요소의 글자 크기가 상대적 크기가 아닌 절대적 크기로 지정되어 있어야 한다.

다음은 위 예제 1-1에 이어서, 해당 속성을 추가한 모습이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .other_fam {
            font-family: 'Times New Roman', Times, serif;
        }
        .f_size {
            font-size: large;
        }
    </style>
</head>
<body>
    <p>안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="other_fam">안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="f_size">안녕하세요. 12345 !@#$% abcde ABCDE</p>
</body>
</html>
```

![예제 1-2 실행결과](/images/2023-12-06/css-text-style/2.png)

예제 1-2 실행결과

## font-style 속성

이탤릭체로 표시할 때 사용되는 속성으로, 해당 속성의 값으로 normal, italic, oblique 중 하나를 택해 사용한다. italic과 oblique의 차이점은, italic은 이미 기울어진 채로 디자인된 글꼴을 사용하여 이탤릭체를 표현하지만, oblique는 기존 글꼴을 기울여서 표현한다. 웹에서는 주로 italic 속성을 주로 사용한다고 한다. 다음은 예제 1-2에 이어서 특정 텍스트에 해당 속성을 이용하여 이탤릭체를 적용한 모습이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .other_fam {
            font-family: 'Times New Roman', Times, serif;
        }
        .f_size {
            font-size: large;
            font-style: italic;
        }
    </style>
</head>
<body>
    <p>안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="other_fam">안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="f_size">안녕하세요. 12345 !@#$% abcde ABCDE</p>
</body>
</html>
```

![예제 1-3 실행결과](/images/2023-12-06/css-text-style/3.png)

예제 1-3 실행결과

## font-weight 속성

해당 속성은 글자의 굵기를 조절하는 속성이다. 키워드를 사용하거나 숫자값을 사용할 수 있다. 

사용할 수 있는 키워드 값으로 다음이 있다.

- normal: 보통 굵기. 기본값으로 설정되어 있다.
- bold: 굵게
- bolder: 원래보다 더 굵게
- lighter: 원래보다 더 가늘게

반면, 사용할 수 있는 숫자 범위는 100~900이며, 100으로 갈수록 가늘은 글자, 900으로 갈수록 굵은 글자로 설정된다. 

다음은 예제 1-3에 이이서, 해당 속성을 이용하여 특정 텍스트의 굵기를 조절한 모습이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .other_fam {
            font-family: 'Times New Roman', Times, serif;
            font-weight: bolder;
        }
        .f_size {
            font-size: large;
            font-style: italic;
        }
    </style>
</head>
<body>
    <p>안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="other_fam">안녕하세요. 12345 !@#$% abcde ABCDE</p>
    <p class="f_size">안녕하세요. 12345 !@#$% abcde ABCDE</p>
</body>
</html>
```

![예제 1-4 실행결과](/images/2023-12-06/css-text-style/4.png)

예제 1-4 실행결과

# 텍스트 관련 스타일

## color

해당 속성은 특정 텍스트의 글자색을 지정할 수 있는 속성이다. 해당 속성값에는 영단어로 정의된 색 이름이나 rgb, rgba, hsl, hsla, 16진수 등의 방법으로 원하는 색상을 지정할 수 있다. 

## text-align

특정 텍스트 문단의 정렬 방법을 지정하는 속성이다. 속성값으로는 다음들 중 하나를 택해 대입하면 된다.

|||
| --- | --- |
| start | 현재 텍스트 줄의 시작 위치에 맞춰 정렬 |
| end | 현재 텍스트 줄의 끝 위치에 맞춰 정렬. |
| left | 왼쪽 정렬 |
| right | 오른쪽 정렬 |
| center | 중앙 정렬 |
| justify | 양쪽 정렬 |
| match-parent | 부모 요소에 지정된 정렬 방식에 따름. |

## line-height

문단의 각 줄 간의 간격을 설정하는 속성이다. 속성값으로 픽셀(px) 단위, 배율 및 퍼센트 방식으로 대입할 수 있다. 줄 간 간격으로 절대 크기로 정하고자 할 때는 px 단위로, 현재 글자 크기 대비 상대적으로 크기를 조절하고자 한다면 배율 또는 퍼센트 방식으로 속성값을 대입하면 된다. 다음은 같은 문단에 대해 각각 다른 방식으로 해당 속성을 이용해 줄 간 간격을 다르게 설정한 모습이다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            border: 1px solid black;
            margin: 10px;
            padding: 10px;
        }
        p {
            font-size: 15px;
						/*
                line-height 속성은 해당 텍스트 글자 크기를 기준으로 
                상대적 크기로 줄 간 간격을 지정할 수도 있다. 
            */
        }
        .pixel {
            line-height: 20px;
        }
        .multiple {
            line-height: 3.0; /* = 15*3.0 = 45px*/
        }
        .percent {
            line-height: 200%; /* = 15*200% = 30px */
        }
    </style>
</head>
<body>
    <div>
        <p class="pixel">
            한가로운 봄날, 작은 공원에서 나무 그늘 아래에 앉아 책을 읽고 있습니다. 
            바람은 부드럽게 불어와 신선한 냄새를 실어줍니다. 
            주위에는 새들의 지저귐과 풀밭에서 피어나는 꽃들의 아름다운 향기가 가득합니다. 
            시간은 천천히 흐르고 마음은 평온해집니다.
        </p>
    </div>
    <div>
        <p class="multiple">
            한가로운 봄날, 작은 공원에서 나무 그늘 아래에 앉아 책을 읽고 있습니다. 
            바람은 부드럽게 불어와 신선한 냄새를 실어줍니다. 
            주위에는 새들의 지저귐과 풀밭에서 피어나는 꽃들의 아름다운 향기가 가득합니다. 
            시간은 천천히 흐르고 마음은 평온해집니다.
        </p>
    </div>
    <div>
        <p class="percent">
            한가로운 봄날, 작은 공원에서 나무 그늘 아래에 앉아 책을 읽고 있습니다. 
            바람은 부드럽게 불어와 신선한 냄새를 실어줍니다. 
            주위에는 새들의 지저귐과 풀밭에서 피어나는 꽃들의 아름다운 향기가 가득합니다. 
            시간은 천천히 흐르고 마음은 평온해집니다.
        </p>
    </div>
</body>
</html>
```

![예제 2-1 실행결과](/images/2023-12-06/css-text-style/5.png)

예제 2-1 실행결과

## text-decoration

해당 속성은 텍스트에 줄을 긋는 것에 관련된 속성이다. 해당 속성에 사용할 수 있는 속성값들은 다음 예제 코드에서 볼 수 있다. 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        .no {
            text-decoration: none;
        }
        .under {
            text-decoration: underline;
        }
        .over {
            text-decoration: overline;
        }
        .line-th {
            text-decoration: line-through;
        }
    </style>
</head>
<body>
    <p class="no">Alright! let's roll!</p>
    <p class="under">Alright! let's roll!</p>
    <p class="over">Alright! let's roll!</p>
    <p class="line-th">Alright! let's roll!</p>
</body>
</html>
```

![예제 2-2 실행결과](/images/2023-12-06/css-text-style/6.png)

예제 2-2 실행결과

해당 속성은 \<a> 태그가 적용되어 하이퍼링크가 적용된 텍스트에 자동으로 적용되는 밑줄을 삭제할 때에도 유용하게 사용될 수 있다. 

## text-shadow

해당 속성은 텍스트에 그림자를 부여하는 속성이다. 속성값의 형태는 다음과 같다. 

```html
text-shadow: none | <가로 거리> | <세로 거리> | <번짐도> | <색상>
```

그림자를 주지 않고자 할 때 none을 사용할 수 있다. 나머지 속성값들은 다음을 참조.

위 예제 2-3에서 | 기호는 “또는” 이라는 뜻이다. 

|||
|---|---|
| <가로 거리> | 텍스트와 그림자 간 가로 거리 지정. 필수로 지정해야 함. 양숫값은 텍스트의 오른쪽, 음수값은 왼쪽에 그림자를 위치시킴. |
| <세로 거리> | 텍스트와 그림자 간 세로 거리 지정.필수로 지정해야한다. 양수값은 텍스트의 아래, 음수값은 텍스트의 위에 그림자를 위치시킴. |
| <번짐도> | 그림자의 번지는 정도를 지정. 양수값 사용시 그림자가 바깥 방향으로 퍼지고, 음수값 사용 시 그림자가 안쪽 방향으로 수축된다. 기본값은 0이다. |
| <색상> | 그림자의 색상을 지정. 여러 색상을 지정할 수도 있으며, 여러 색상 지정 시 공백을 기준으로 나누어 기입한다. 기본값은 현재 글자색이다. |

다음은 해당 속성을 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        h1 {
            text-align: center;
        }
        .right-bottom {
            text-shadow: 5px 5px blue;
        }
        .left-up {
            text-shadow: -10px -10px brown;
        }
        .spread {
            text-shadow: 5px 5px 10px blue;
        }
    </style>
</head>
<body>
    <h1 class="right-bottom">Welcome!</h1>
    <h1 class="left-up">WOW!</h1>
    <h1 class="spread">MOM!</h1>
</body>
</html>
```

![예제 2-3 실행결과](/images/2023-12-06/css-text-style/7.png)

예제 2-3 실행결과

## text-transform

text-transform 속성은 영문 텍스트의 대소문자 변환 관련 속성이다. 해당 속성에는 다음의 값들을 사용할 수 있다.

|||
| --- | --- |
| capitalize | 영어 텍스트의 맨 앞 단어를 대문자로 바꾼다. |
| uppercase | 영어 텍스트의 모든 영문들을 대문자로 바꾼다. |
| lowercase | 영어 텍스트의 모든 영문들을 소문자로 바꾼다. |

다음은 위 속성값들을 확인하는 코드 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <h1 style="text-transform: capitalize;">book</h1>
    <h1 style="text-transform: uppercase;">note</h1>
    <h1 style="text-transform: lowercase;">paper</h1>
</body>
</html>
```

![예제 2-5 실행결과](/images/2023-12-06/css-text-style/8.png)

예제 2-5 실행결과

## letter-spacing, word-spacing

letter-spacing 속성은 글자와 글자 간 간격을, word-spacing 속성은 단어와 단어간 간격을 조정하는 속성이다. 다음은 해당 속성들을 이용한 예제 코드이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <p>Good!</p>
    <p style="letter-spacing:3px">Good!</p>
    <p>
        한가로운 봄날, 작은 공원에서 나무 그늘 아래에 앉아 책을 읽고 있습니다.
    </p>
    <p style="word-spacing: 5px;">
        한가로운 봄날, 작은 공원에서 나무 그늘 아래에 앉아 책을 읽고 있습니다.
    </p>
</body>
</html>
```

![예제 2-6 실행결과](/images/2023-12-06/css-text-style/9.png)

예제 2-6 실행결과

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석