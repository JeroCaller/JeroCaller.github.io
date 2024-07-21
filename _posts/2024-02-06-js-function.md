---
title: "[JS] 함수 (Function)"
category: "javascript"
tag: ["web", "js", "javascript", "function", "함수"]
---

# 함수

자바스크립트에서는 함수를 선언(정의)할 때 function 키워드를 사용하며, 그 뒤에는 함수명과 괄호, 그리고 그 뒤에는 중괄호로 묶어 실행할 코드들을 묶는다. 

```jsx
function 함수명(매개변수(생략 가능)) {
    실행 코드;
}
```

선언된 함수를 호출하여 실행하고자 할 때에는 함수명() 또는 괄호 안에 필요한 인자를 대입하여 사용하면 된다.

```jsx
함수명();
```

함수 선언부에 코드를 실행한 결과값을 반환하고자 한다면 return 키워드와 그 뒤에 공백 한 칸 띄고 반환하고자 하는 값(또는 변수명)을 기입하면 된다. 

다음은 함수를 활용한 예제이다. 

```jsx
// 홀수 찾기
let numArr = [2, 4, 10, 9, 12];

function findOdd(arr) {
    var i;
    for(i = 0; i < arr.length; i++) {
        if (arr[i] % 2 == 1) {
            break;
        }
    }
    return [i, arr[i]];
}

let [idx, val] = findOdd(numArr);

console.log("홀수 인덱스는 " + idx + "이며, 해당 홀수는 " + val + "입니다.");
```

```jsx
홀수 인덱스는 3이며, 해당 홀수는 9입니다.
```

다른 프로그래밍 언어와 달리, 자바스크립트는 함수의 선언부 위에 함수의 호출부가 먼저 와도 오류가 발생하지 않는다. 이는 웹 브라우저가 자바스크립트 코드를 분석할 때 함수 선언부를 먼저 분석하기 때문이다. 따라서 앞선 예제를 다음 예제로 변경해도 에러 발생 없이 실행된다.

```jsx
// 홀수 찾기
let numArr = [2, 4, 10, 9, 12];

let [idx, val] = findOdd(numArr);

function findOdd(arr) {
    var i;
    for(i = 0; i < arr.length; i++) {
        if (arr[i] % 2 == 1) {
            break;
        }
    }
    return [i, arr[i]];
}

console.log("홀수 인덱스는 " + idx + "이며, 해당 홀수는 " + val + "입니다.");
```

# 매개변수 기본값 지정

ES6 이후로는 매개변수에 기본값을 지정할 수 있게 되었다. 따라서, 함수 호출 시 기본값이 지정된 매개변수 자리에 인수를 대입하지 않아도 기본값으로 지정되어 사용된다. 

```jsx
// 두 수의 최대공약수 (The Greatest common divisor) 구하기.

function getDivisors(n) {
    // 하나의 수를 받으면 그 수의 약수들을 반환하는 함수.
    let result = [];
    let rootN = Math.floor(Math.sqrt(n));
    for(let i = 1; i <= rootN; i++) {
        if (n % i == 0) {
            result.splice(result.length, 0, i, n / i);
        }
    }
    result.sort((a, b) => a - b);
    return result;
}

function gcd(a = 10, b = 15) {
    let aSet = new Set(getDivisors(a));
    let bSet = new Set(getDivisors(b));
    let commonDivisors = new Set([...aSet].filter((value) => bSet.has(value)));
    return Math.max(...commonDivisors);
}

console.log(gcd(12, 18));
console.log(gcd());
```

```jsx
6
5
```

위 예제의 gcd 함수 선언부를 보면, 매개변수 a, b에 각각 10, 15라는 기본값이 설정되었다. 해당 함수의 두 번째 호출부를 보면 아무런 인수도 대입되지 않았다. 이 경우, a = 10, b = 15로 기본값이 설정되어서 계산된다. 

함수 매개변수가 여러 개이고, 어떤 매개변수는 기본값이 설정되어 있지 않고, 또 어떤 매개변수들은 기본값이 설정되어 있는 경우, 해당 매개변수의 순서는 상관없다. 즉, 기본값이 설정된 매개변수가 기본값이 설정되지 않은 매개변수 앞에 올 수도 있다. 이는 파이썬에서는 에러가 나는 일이지만, 자바스크립트에서는 일어나지 않는다. 

```jsx
// 두 수의 최대공약수 (The Greatest common divisor) 구하기.

function getDivisors(n) {
    // 하나의 수를 받으면 그 수의 약수들을 반환하는 함수.
    let result = [];
    let rootN = Math.floor(Math.sqrt(n));
    for(let i = 1; i <= rootN; i++) {
        if (n % i == 0) {
            result.splice(result.length, 0, i, n / i);
        }
    }
    result.sort((a, b) => a - b);
    return result;
}

function gcd(a = 10, b) {
    let aSet = new Set(getDivisors(a));
    let bSet = new Set(getDivisors(b));
    let commonDivisors = new Set([...aSet].filter((value) => bSet.has(value)));
    return Math.max(...commonDivisors);
}

console.log(gcd(12, 18));
console.log(gcd());
```

```jsx
6
-Infinity
```

다만 위 예제에서처럼 예기지 않은 버그가 일어날 수도 있다. 이를 방지하고자 한다면 기본값이 없는 매개변수 다음에 기본값이 설정된 매개변수 순으로 나열하는 것이 좋겠다. 

# 익명 함수

파이썬에서는 이름이 없는 함수를 lambda를 이용하여 정의할 수 있었다. 자바스크립트에서도 익명 함수가 존재한다. 사용법은 function 키워드 다음에 함수명을 쓰지 않고 바로 괄호를 쓰는 것이다. 함수 자체는 식(expression)이기에, 이렇게 생성한 익명 함수를 바로 변수, 또는 함수 인자로 할당하여 사용한다. 변수에 할당한 경우, 변수 자체를 호출하여 사용한다. 

```jsx
var average = function(...arr) {
    let total = 0;
    for(let i = 0; i < arr.length; i++) {
        total += arr[i];
    }
    return total / arr.length;
}

console.log(average(1, 2, 3));
console.log(average(5, 5, 5));
console.log(average(90, 95));
console.log(average(10));
```

```jsx
2
5
92.5
10
```

# 화살표 함수

ES6 부터는 익명 함수를 화살표 함수로 더 간단하게 선언할 수 있다. 화살표 함수의 구조는 다음과 같다.

```jsx
(매개변수) => { 코드 }
```

매개변수가 없을 경우엔 빈 괄호로 둔다. 매개변수가 한 개일 경우에는 괄호를 생략할 수 있으며, 매개변수가 2개 이상인 경우에는 괄호를 생략할 수 없고, 매개변수들의 구분은 쉼표(,)로 한다. 

또한, 중괄호 내 코드가 한 줄 뿐이라면 중괄호를 생략할 수 있으며, 이 경우, return문도 생략할 수 있다. 

```jsx
let var1 = () => 'Useless function';
let var2 = n => {
    let total = 0;
    for(let i = 1; i <= n; i++) {
        total += i;
    }
    return total;
}
let var3 = (x, y) => x + y;

console.log(var1());
console.log(var2(10));
console.log(var3(2, 3));
```

```jsx
Useless function
55
5
```

# 즉시 실행 함수

만약 단 한 번만 실행되고, 그 즉시 사용하고자 한다면 즉시 실행 함수를 사용할 수 있다. 즉시 실행 함수는 다음의 구조를 띈다. 

```jsx
(function(매개변수) {  // 매개변수는 생략 가능. 생략 시 빈 괄호
    코드
}(매개변수에 대응되는 인자)); // 매개변수가 없으면 생략 가능. 생략 시 빈 괄호.
```

일반적인 함수는 선언부부터 해석한 후 메모리 상에 저장해두었다가 호출되면 해당 함수를 실행하는 방식이었다. 그러나 즉시 실행 함수는 해석과 동시에 실행된다. 

즉시 실행 함수는 식이므로 맨 뒤에 세미콜론(;)을 붙인다. 

```jsx
(function() {
    console.log("이런 함수는 단 한 번만 사용될 거라 이렇게 정의했어요.");
}());
```

```jsx
이런 함수는 단 한 번만 사용될 거라 이렇게 정의했어요.
```

```jsx
(function(x, y) {
    console.log("이런 함수는 단 한 번만 사용될 거라 이렇게 정의했어요.");
    console.log(x + y);
}(3, 4));
```

```jsx
이런 함수는 단 한 번만 사용될 거라 이렇게 정의했어요.
7
```

---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석