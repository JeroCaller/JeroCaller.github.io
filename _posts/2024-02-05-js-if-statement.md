---
title: "[JS] 조건문"
category: "javascript"
tag: ["web", "js", "javascript", "if", "switch", "조건문"]
---

# if, if~else

조건문인 if, if~else문을 이용하면 분기점에서 조건이 맞는지 확인하고, 조건에 맞을 때만 특정 명령을 실행하도록 제어할 수 있다. 

if 문 단독으로 사용할 때는 다음과 같은 형태를 띈다. 

```jsx
let userNumber = parseInt(prompt("숫자값을 입력하세요."));
if (userNumber % 2 == 0) {
    document.write("짝수입니다.");
}
```

if 문 뒤에는 괄호 안에 조건식을 입력한다. 위 예제에서는 userNumber 변수값을 2로 나눈 나머지가 0인지를 확인하는 조건식이 대입되어 있다. 그리고 그 다음에 중괄호({})로 둘러 쌓인 곳에 해당 조건이 true일 때 실행할 코드를 입력하면 된다. 즉 위 예제를 보면 특정 변수를 2로 나눈 나머지가 0일 경우 “짝수입니다.”라는 문자열을 웹 페이지에 출력하도록 하고 있음을 알 수 있다. 

만약, if 조건문이 false일 경우에도 따로 처리할 코드가 있다면 if~else문을 사용하면 된다. 위 예제에서 if~else문으로 확장하면 입력된 수가 홀수여도 홀수임을 알리는 문장을 출력할 수 있게된다. 

```jsx
let userNumber = parseInt(prompt("숫자값을 입력하세요."));
if (userNumber % 2 == 0) {
    document.write("짝수입니다.");
} else {
    document.write("홀수입니다.");
}
```

한 편, if 문 안에 또 다른 if문을 입력하여 중첩 if~else문을 작성할 수도 있다. 

```jsx
let userNumber = parseInt(prompt("숫자값을 입력하세요."));
// parseInt() -> 문자열 자료형의 값을 숫자형으로 바꿔주는 함수.
if (userNumber % 2 == 0) {
    if (userNumber % 4 == 0) {
        document.write("4의 배수입니다.");
    } else {
        document.write("4의 배수가 아닌 짝수입니다.");
    }
} else {
    if (userNumber % 3 == 0) {
        document.write("3의 배수입니다.");
    } else {
        document.write("3의 배수가 아닌 홀수입니다.");
    }
}
```

if문을 통해 거치고자 하는 조건문을 둘 이상일 경우, 위와 같이 중첩 if~else문을 사용할 수 있지만, 가독성 측면에서 좋아보이진 않는다. 파이썬에서는 elif문을 지원하여 여러 개의 조건들을 확인할 수 있지만, 자바스크립트에서는 그런 키워드가 없다. 대신 if~else 문 바로 뒤에 다시 if ~ else 문을 사용하는 식으로 비슷하게나마 구현할 순 있다. 다음 예제에서 확인할 수 있다. 

```jsx
let a = 5;

if (a < 3) {
    console.log('low');
} else if (3 < a < 6) {
    console.log('mid');
} else {
    console.log('high');
}
```

```jsx
mid
```

중첩 if문 보다는 조금 더 가독성이 있다. (else와 if 사이는 공백 한 칸으로 띄어야 한다. elseif 라는 키워드는 존재하지 않기 때문이다) 

## 조건식에 들어갈수 있는 논리 연산자

if 조건식에는 앞서 [JS - [기본 문법] 연산자 (Operator)](/javascript/js-operator/) 페이지의 “논리 연산자” 챕터에서 본 논리 연산자들을 사용할 수 있다. 이 논리 연산자들을 이용하면 2개 이상의 조건을 동시에 확인할 수 있다. 다음은 논리 연산자를 조건식에 활용하여 2개의 조건을 동시에 검사하고 그에 따라 적절한 명령을 처리하게끔 한 예제 코드이다. 

```jsx
let math = 90;
let english = 95;

// 조건문 1
if (math++ % 2 == 0 || english++ % 2 == 0) {
    console.log("적어도 점수 하나는 짝수이다.");
} else {
    console.log("모든 점수가 홀수이다.");
}
console.log(math, english);

// 조건문 2
if (math++ >= 100 && english++ >= 100) {
    console.log("점수가 이상합니다.");
}
console.log(math, english);
```

```jsx
적어도 점수 하나는 짝수이다.
91 95
92 95
```

### 단축 평가(short circuit evaluation)

위 예제 2-1에서와 같이, 조건식이 2개 이상일 경우, 인터프리터는 맨 앞에 있는 조건식부터 확인하여 true인지 false인지를 판별한다. 그렇기에, 만약 AND 연산자로 묶인 두 조건식에서, 맨 앞에 있던 조건식이 false라면 인터프리터는 그 뒤의 조건식을 확인하지 않고 바로 다음으로 건너뛴다. 이렇게 맨 앞의 조건식의 결과에 따라 그 뒤의 조건식은 확인하지 않고 바로 건너뛰는 방식을 “단축 평가”라 한다. 이를 이용하면, AND 연산자로 묶인 두 조건식 중 false가 나올 확률이 높은 조건식을 맨 앞에 배치하는 것이 조건 연산 속도를 조금이나마 빠르게 할 수 있는 방법이다. 이와 마찬가지 원리로, OR 연산자의 경우 두 조건식 중 하나만 true여도 전체가 true가 되기 때문에, 맨 앞 조건식을 true가 나올 확률이 높은 조건식으로 배치하면 좀 더 조건 연산 속도가 빨라질 수 있다. 

인터프리터가 두 조건식에서 맨 앞 조건식만 true 또는 false임을 판별해도 바로 다음으로 넘어간다는 근거는 위 예제 2-1에서 확인할 수 있다. 조건문 1을 보면 두 조건식이 OR 연산자로 묶여 있고, 각 조건식마다 변수 뒤에 증가 연산자(++)를 덧붙였다. 인터프리터가 이 조건식을 확인할 때, 이미 첫 번째 조건식이 true인 상황이다. 이 경우, 인터프리터는 그 뒤의 조건식을 확인하지 않고 다음으로 넘어간다. 그 증거로, 해당 조건식을 거친 후의 두 변수값을 확인해보면, math 변수는 1만큼 증가되어있으나, english 변수값은 그대로이다. 조건식 2도 마찬가지로, 두 조건식이 AND로 묶여있을 때, 이미 맨 앞 조건식이 false이므로, 그 뒤의 조건식은 확인하지 않아 역시 math 변수값만 1 증가하였음을 알 수 있다. 

# 조건 연산자

if, if~else 문 외에도 조건에 따라 명령을 실행할 수 있는 방법이 있다. 만약 조건식의 결과가 각각 true, false일 때 실행할 명령이 하나라면 다음과 같은 조건 연산자를 사용해도 된다. 

```jsx
let userNumber = parseInt(prompt("숫자값을 입력하세요."));
(userNumber % 2 == 0) ? document.write("짝수입니다.") : document.write("홀수입니다.");
// (조건식) ? true일 경우 실행할 코드 : false일 경우 실행할 코드
```

# switch 문

한 편, 조건식의 결과로 나올 수 있는 값들이 여러 개이고, 이 여러 경우에 따라 각각의 명령을 처리하고자 한다면, switch 문이 적절할 것이다. 

```jsx
let menu = parseInt(prompt("메뉴 선택. 1. 게임 시작 2. 설정 3. 게임 종료"));

switch (menu) {
    case 1:
        document.write("게임 시작");
        break;
    case 2:
        document.write("설정");
        break;
    case 3:
        document.write("게임 종료");
        break;
    default:
        document.write("범위 밖을 벗어난 숫자.");
}
```

![예제 4-1 실행결과1](/images/2024-02-05/js-if-statement/1.png)

예제 4-1 실행결과1

![예제 4-1 실행결과2](/images/2024-02-05/js-if-statement/2.png)

예제 4-1 실행결과2

switch 키워드 다음에 괄호 안에 조건식을 기입한다. 그 후 중괄호 안에는 조건식의 결과로 나올 수 있는 여러 값들에 대한 경우의 수를 case 문으로 처리한다. case 키워드 다음에 예측되는 값을 작성한 후, 그 아래에 해당 값일 경우에 처리할 코드를 삽입한다. 그 후에는 break; 문을 마지막에 추가하여 그 아래에 있는 다른 case 문들도 실행되지 않고 바로 switch문을 빠져나오도록 한다. 만약 조건식의 결과가 그 어떤 case의 값과도 일치하지 않는다면 default 키워드 내에 있는 명령 코드를 실행하게 된다. 

![예제 4-1 실행결과3](/images/2024-02-05/js-if-statement/3.png)

예제 4-1 실행결과3

위 실행결과3은 예제 4-1에서 case 2에서 break문을 빠뜨렸을 경우를 실행한 결과이다. 조건식 결과가 2일 경우 case 2의 내용에 break 문이 빠졌으므로 그 다음인 case 3의 코드도 실행되는 것이다. 따라서 이를 원치 않는다면 반드시 각 case마다 break문을 넣어야 한다. 

반대로, break문을 빼고 case문을 연속으로 작성함으로써 여러 경우의 수가 나오더라도 이를 공통의 코드로 처리할 수도 있다. 

```jsx
let user = prompt("당신의 국적을 작성해주세요.");

switch (user) {
    case '한국':
    case '대한민국':
    case '남한':
    case 'Korea':
    case 'South Korea':
        document.write("만나서 반갑습니다!");
        break;
    case 'USA':
    case 'US':
    case '미국':
    case '미합중국':
    case 'America':
        document.write("Nice to meet you!");
        break;
    default:
        document.write("추후 더 많은 국적 등록 예정.");
}
```

위 예제에서, ‘한국’, ‘대한민국’, ‘남한’, ‘Korea’, ‘South Korea’ 모두 같은 한 국가를 지칭하는 것이기에 case문들을 연속으로 작성하여 공통의 명령을 처리하도록 설계했음을 볼 수 있다. 

switch문의 case문을 쓸 때 주의할 점은, case 문 뒤의 값은 switch문의 조건식의 결과와 일치해야 한다. 그래서 비교 연산자는 사용할 수 없다. (사용할 경우 무조건 default 구문이 실행됨을 확인하였다) 즉, switch 문은 조건식의 결과와의 일치 여부만을 판단한다. 3 < x < 5와 같이 범위 내에 있는지 여부를 조건식으로 쓰고자 한다면 if문을 사용해야 한다. 

default문은 필수로 작성해야 하는 키워드는 아니다. 해당 키워드가 없어도 작동은 된다. 



---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [if...else - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/if...else)

[3] [switch문](https://ko.javascript.info/switch)

[4] [[JavaScript] 자바스크립트 switch문 조건문 이해하기](https://bigtop.tistory.com/29)