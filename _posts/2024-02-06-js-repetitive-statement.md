---
title: "[JS] 반복문"
category: "javascript"
tag: ["web", "js", "javascript", "loop", "repetitive statement", "for", "while", "do~while", "break", "continue"]
---

# 개요

반복문은 어떤 반복적인 일을 처리할 때 쓰이는 문법으로, 만약 반복문을 쓰지 않는다면 코드가 더 복잡해지고, 중복되는 코드가 늘어날 것이다. 이를 방지하여 가독성도 유지할 수 있는 것이 반복문이다. 

# for 문

자바스크립트에서 사용할 수 있는 반복문 중의 하나로 for문이 있다. 다음은 for문을 이용하여 1부터 100까지를 더한 값을 구하는 예제이다. 

```jsx
var i;
var total = 0;

for(i = 1; i <= 100; i++) {
    total += i;
}
console.log(total);
```

```jsx
5050
```

for문에 대해 자세히 살펴보면 다음과 같다. 

```jsx
for(초기값; 조건식; 증감식) {
    반복하면서 실행할 코드들
}
```

반복문은 언젠가는 벗어나야 한다. 그렇지 않으면 무한루프에 빠지게 되어 반복문을 빠져나가 다음 코드를 실행하지 않는다. 이렇게 특정 조건이 되면 반복문을 빠져나가게 하기 위해 괄호 안에 초기값, 조건식, 증감식을 넣는다. 초기값은 반복문이 반복한 횟수를 세기 위한 변수의 초기값을 설정, 입력하는 곳이다. 위 예제에서처럼 i = 1이라 하면 i = 1부터 반복문의 반복 횟수를 세게 된다. 그 다음, 조건식을 입력한다. 보통 i <= 100 처럼 반복문의 반복 횟수를 제한할 때 쓰인다. 이 조건을 만족해야만 반복문 내 코드를 실행할 수 있다. 그 다음으로 증감식을 사용한다. 특별한 경우가 아니라면 i++와 같이 반복 횟수를 세는 변수를 1 증가시키는 방식이다. 이 증감식이 없다면 언제나 조건식을 만족시키기에 무한루프에 빠질 가능성이 있으니 필수이다. 

for문은 맨 처음엔 초기값(i=1)을 거친 다음, 조건식을 거쳐 조건식이 true인지 검사한다. true라면 중괄호 안의 코드를 실행한다. 다 실행하면 증감식을 거쳐 반복 횟수 변수값을 증감시킨다. 그 후 다시 조건식을 거쳐 조건이 맞는지 검사 후, 맞으면 또 중괄호 안의 코드를 실행하고, 조건에 더 이상 맞지 않으면 바로 for문을 빠져나간다. 

한 편, 초기값, 조건식, 증감식 모두 비울 때에는 무한 루프에 빠지게 된다. 만약 다음을 실행하면 무한 루프에 빠져 프로그램이 영원히 끝나지 않게 된다. 이 경우, 콘솔 창에서 ctrl+C키를 눌러 강제적으로 빠져나가야 한다. 

```jsx
var i;
var total = 0;

for(;;) {
    total += i;
}
console.log(total);  // 무한 루프에 의해 여기까진 도달하지 못한다.
```

한 편, for 문 안에 또 다른 for문을 사용하여 중첩 for문을 사용할 수도 있다. 다음은 웹 페이지에 2~9단의 구구단 표를 출력한 예제이다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        h1 {
            margin: 10px auto;
            padding: 10px;
            width: 90%;
            border: 1px solid black;
            text-align: center;
        }
        #container {
            margin: 10px auto;
            width: 90%;
            border: 1px solid black;
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
        }
        .one-table {
            border: 1px solid black;
            margin: 20px;
            padding: 20px;
        }
        .one-table h2 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>구구단 2~9단 표</h1>
    <div id="container">
        <script>
            var i, j;

            for(i = 2; i < 10; i++) {
                document.write("<div class='one-table'>");
                document.write("<h2>" + i + "단</h2>");
                for(j = 1; j < 10; j++) {
                    document.write(i + " X " + j + ' = ' + i * j + "<br>");
                }
                document.write("</div>");
            }
        </script>
    </div>
</body>
</html>
```

![예제 1-3 실행결과](/images/2024-02-06/js-repetitive-statement/1.png)

예제 1-3 실행결과

# while, do~while문

또 다른 반복문으로 while문과 do~while문이 존재한다. 형태는 다음과 같다.

```jsx
while(조건식) {
    코드
}
```

```jsx
do {
    코드
} while(조건식)
```

while문은 조건식이 맨 위에 존재하여, while문에 진입하면 조건을 먼저 확인한 후, 조건식이 true일 때 해당 반복문을 실행한다. 따라서 맨 처음부터 조건식 결과가 false가 나와서 해당 반복문을 단 한 번도 실행하지 않을 수도 있다. 

반면, do~while문은 조건식이 맨 마지막에 나오므로, 일단은 무조건 한 번은 반복문 내 코드를 실행한 후에 조건식을 나중에 확인하는 구조이다. 

다음은 while문을 이용한 예제이다.

```jsx
// 주어진 수 n의 약수 구하기.
var n = 12;
var rootN = Math.floor(Math.sqrt(n));
var i = 1;
var result = [];

while(rootN >= i) {
    if (n % i == 0) {
        result.splice(result.length, 0, n / i, i);
    }
    i++;
}

result.sort((a, b) => a - b);
console.log(result);
```

```jsx
[ 1, 2, 3, 4, 6, 12 ]
```

# break문

반복문 내에서 특정 조건을 만족하면 반복 횟수를 다 채우지 않아도 중간에 반복문을 무조건 빠져나가게 하는 키워드로 break가 있다. break문은 그 자체만으로 사용할 수도 있고, if문과 결합하여 특정 조건을 만족하면 반복문을 무조건 빠져나오도록 설계할 수도 있다. 

다음은 break 문을 이용하여, 숫자가 담긴 배열을 순회하는 동안 배열 내 요소가 홀수이면 반복문을 중단하고 빠져나오도록 하는 예제이다. 

```jsx
// 홀수 찾기
var numArr = [2, 4, 10, 9, 12];
var i;

for(i = 0; i < numArr.length; i++) {
    if (numArr[i] % 2 == 1) {
        break;
    }
}

console.log("홀수 인덱스는 " + i + "이며, 해당 홀수는 " + numArr[i] + "입니다.");
```

```jsx
홀수 인덱스는 3이며, 해당 홀수는 9입니다.
```

# continue 문

continue 키워드는 반복문 내에서 특정 조건을 만족하면 반복문을 한 번 건너뛰고 다시 블록 내 첫 번째 코드부터 실행하게 할 수 있는 키워드이다. 예를 들어 for문에서 i=3일 때 continue 문을 통해 건너뛰기하면, i=3일 때의 반복문 내 코드는 실행되지 않고, 바로 i=4로 건너뛰게 된다. 

다음은 continue 키워드를 이용하여 숫자가 담긴 배열에서 짝수만 추출하는 예제이다. 

```jsx
// 홀수 제거기
var numArr = [2, 4, 10, 9, 12, 3, 6, 13];
var i;
var evens = [];

for(i = 0; i < numArr.length; i++) {
    if (numArr[i] % 2 == 1) {
        continue;
    }
    evens.push(numArr[i]);
}

console.log(evens);
```

```jsx
[ 2, 4, 10, 12, 6 ]
```


---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석
