---
title: "[JS] 연산자(Operator)"
category: "javascript"
tag: ["web", "js", "javascript", "operator"]
---

# 산술 연산자

자바스크립트에서 사용하는 산술 연산자에는 다음 예제를 통해 확인할 수 있다. 

```jsx
let a = 10;
let b = 5;

console.log(a + b);  // 덧셈
console.log(a - b);  // 뺄셈
console.log(a * b);  // 곱셈
console.log(a ** b);  // a의 b 제곰. 10^5
console.log(a / b);  // a를 b로 나눈 값 자체
console.log(a % b);  // a를 b로 나눈 값의 나머지만을 반환.
```

```jsx
15
5
50
100000
2
0
```

또한, 자바스크립트에서는 증감(++, —) 연산자가 존재한다. 이 두 연산자는 각각 변수의 숫자값을 1만큼 증가 또는 감소시킨다. 이 연산자들은 변수명의 앞 또는 뒤에 쓸 수 있는데, 앞에 쓰느냐 뒤에 쓰느냐에 따라 그 결과가 달라진다. 

```jsx
let x = 10;
let y;
y = x++ + 10;
console.log(x);
console.log(y);
```

```jsx
11
20
```

위 예제의 3번째 줄의 x 변수 뒤에 증가 연산자(++)가 쓰였다. 이 경우, 해당 연산자가 포함된 연산식을 먼저 계산한 후에 x 변수의 값을 1 증가시킨다. 이로 인해, y 값은 x의 값이 1 증가하기 전의 값인 x=10을 대상으로 연산이 되어 y = 10 + 10 = 20이 되는 것이다. 이렇게 3번째 줄의 연산식이 진행되고 나서야 x의 값이 1증가한다. 

만약 같은 상황에서 증가 연산자를 x 변수 앞에 작성하여 ++x와 같이 한다면 어떻게 될까?

```jsx
let x = 10;
let y;
y = ++x + 10;
console.log(x);
console.log(y);
```

```jsx
11
21
```

이 경우, 연산식이 실행되기 전에 먼저 x의 값이 1 증가한다. 그 후에 연산식이 실행되는 방식이다. 그래서 3번째 줄의 식은 사실상 y = 11 + 10 = 21이 되는 것이다. 

# 할당 연산자(Assignment operator)

`let a = 10;` 과 같이 할당 연산자의 우측에서 값 또는 연산식의 결과가 있으면 이를 해당 연산자 왼쪽에 있는 변수에 할당하는 연산자가 할당 연산자이다. 이는 기호로 등호 (=)를 사용한다. 

등호 뿐만 아니라, 산술 연산자와 합쳐서 사용할 수도 있다. 이 경우, 연산자 우측에 있는 값과 좌측에 있는 변수의 값을 산술 연산자를 통해 연산한 결과값을 다시 좌측의 변수에 재할당하라는 뜻이다. 다음의 예제 참고.

```jsx
let x = 10;
let y = 5;

y += x;  // y = y + x;
console.log(x, y);

y -= x;  // y = y - x;
console.log(x, y);

y *= x;  // y = y * x;
console.log(x, y);

y /= x;  // y = y / x;
console.log(x, y);

y %= x;  // y = y % x;
console.log(x, y);
```

```jsx
10 15
10 5
10 50
10 5
10 5
```

# 연결 연산자

연결 연산자는 둘 이상의 문자열들을 하나로 합칠 때 사용하는 연산자이며, 기호는 ‘+’ 기호를 사용한다. 이는 파이썬에서 두 숫자를 더할 때나 두 문자열을 연결할 때 모두 ‘+’ 기호를 사용하는 것과 일치한다.

```jsx
let someStr = 'hi';

console.log(someStr + ' nice to meet you. ' + 'good');
console.log('hi ' + 'nice to meet you. ' + 'good');
```

```jsx
hi nice to meet you. good
hi nice to meet you. good
```

# 비교 연산자 (comparision operator)

비교 연산자는 두 개의 피연산자 값을 비교하여 특정 조건을 만족하면 true, 아니면 false를 반환하도록 하는 연산자이다. 비교 연산자는 다음의 예제를 통해 확인할 수 있다. 

```jsx
let x = 10;
let y = 5;

// 다음의 물음에 참이면 true, 거짓이면 false를 반환.
console.log(x == y);  // 두 피연산자의 값은 같은가?
console.log(x === y);  // 두 피연산자의 값도, 자료형도 동일한가? 둘 다 같아야 true
console.log(x != y);  // 두 피연산자의 값이 다른가? 
console.log(x !== y);  // 두 피연산자의 값이 다르거나 자료형이 다른가? 둘 중 하나라도 다르면 true
console.log(x < y);  // 왼쪽 피연산자 값보다 오른쪽 피연산자 값이 더 큰가?
console.log(x <= y);  // 왼쪽 피연산자의 값보다 오른쪽 피연산자의 값이 크거나 같은가? 
console.log(x > y);  // 왼쪽 피연산자의 값이 오른쪽 피연산자의 값보다 큰가?
console.log(x >= y);  // 왼쪽 피연산자의 값이 오른쪽 피연산자의 값보다 크거나 같은가?
```

```jsx
false
false
true
true
false
false
true
true
```

==, != 기호를 사용하면, 두 피연산자의 자료형이 서로 같아지도록 자동 형 변환이 일어난다. 그러나 ===, !== 기호는 자동 형 변환이 일어나지 않고 그대로 두고 연산한다. 이러한 이유로, 다음의 결과가 나오는 것이다. 

```jsx
let x = 10;
let y = '10';

console.log(x == y);
console.log(x === y);
```

```jsx
true
false
```

x == y 코드에서, 10 == ‘10’을 ‘10’ == ‘10’으로 자동 형 변환하여 연산한다. 그렇기에 두 값이 서로 같다고 나오는 것이다. 반면, x === y 코드에서는 10 === ‘10’으로 형 변환 없이 그대로 연산하기에 두 자료형이 다르므로 두 값도 다르므로 false라는 결과값이 나오는 것이다. 

한 편, 비교 연산자는 숫자에 대해서만 연산이 가능한 게 아니라, 문자열에 대해서도 연산이 가능하다. 이 경우, 문자들의 ASCII 코드표의 값을 토대로 비교를 한다. 만약 비교하는 두 피연산자의 값이 하나의 문자가 아닌 여러 개의 문자들의 연속이라면, 맨 앞 첫 글자의 ASCII 값을 비교한 후, 만약 둘이 같다면 그 다음 글자들의 ASCII 값을 비교하는 방식으로 연산한다. (한글의 경우 unicode 표를 이용하는 것 같다)

```jsx
console.log('a' > 'b');
console.log('a' > 'A');
console.log('hi' < 'ha');

console.log('가' < '나');
```

```jsx
false
true
false
true
```

# 논리 연산자

불리언 연산자(Boolean operator)라고도 불리는 논리 연산자는 피연산자로 true, false만 받는 연산자로, 연산 결과도 true, false 중 하나이다. 자바스크립트에서는 다음의 논리 연산자들이 있다. 

```jsx
console.log(true || false);  // OR 연산자. 두 피연산자들 중 하나라도 true면 true 반환
console.log(true && false);  // AND 연산자. 두 피연산자 모두 true여야 true 반환.
console.log(!false);  // NOT 연산자. 하나의 피연산자만 사용하며, 해당 피연산자의 반대값을 반환.
```

```jsx
true
false
true
```



---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석