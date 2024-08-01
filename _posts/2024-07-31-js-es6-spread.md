---
title: "[JS][ES6] 전개 구문 (Spread)"
category: "javascript"
tag: ["web", "js", "javascript", "spread", "rest"]
---

# Spread Operator (전개 연산자)

전개 연산자는 배열, 객체 내 내용들을 펼치는 문법으로, 주로 다른 곳에 복사, 이동 시에 사용된다. 

```jsx
let nums = [1, 2, 3];
let expandedNums = [...nums, 4, 5]; // spread. => [1, 2, 3, 4, 5]

console.log(nums);
console.log(expandedNums);
```

예제 1-1

```jsx
[ 1, 2, 3 ]
[ 1, 2, 3, 4, 5 ]
```

예제 1-1 실행결과

위 예제의 두 번째 줄에서 기존 배열인 nums 앞에 `...` 을 붙여 `expandedNums` 라는 새 배열 내에 마치 배열의 한 요소처럼 대입되었다. 내용을 펼치고자 하는 배열 또는 객체명 앞에 `...` 이라는 전개 연산자를 사용하면 말 그대로 내용을 펼칠 수 있는 것이다. 

```jsx
let nums = [1, 2, 3];
let expandedNums = [...nums, 4, 5];

console.log(nums);
console.log(expandedNums);

nums[0] = 12;
console.log(nums);
console.log(expandedNums);
```

예제 1-2

```jsx
[ 1, 2, 3 ]
[ 1, 2, 3, 4, 5 ]
[ 12, 2, 3 ]
[ 1, 2, 3, 4, 5 ]
```

예제 1-2 실행결과

위 예제는 앞선 예제 1-1에서 조금 더 확장한 것으로, 이번에는 기존 배열 nums 내 일부 요소의 값을 바꿔보았다. nums 배열 내에서 해당 위치에 있는 값은 변했지만, nums로부터 복사받은 expandedNums 배열에는 아무런 변화가 있다. 전개 연산자를 이용하면 이렇게 깊은 복사를 할 수 있다. 즉, 내용에 들어가 있는 데이터는 같지만 참조값이 다른 참조를 복사하여 넘긴 것이기에 원본 배열에 아무런 영향을 끼치지 않는 것이다. 

다음은 전개 연산자를 이용하면 깊은 복사를 할 수 있다는 것을 방증하는 예시이다. 

```jsx
let nums = [1, 2, 3];
let deepCopy = [...nums];

function jsonfy(arr) {
    return JSON.stringify(arr);
}

// 두 배열의 내용은 똑같은가?
console.log(jsonfy(nums) === jsonfy(deepCopy));

// 두 배열의 참조값이 서로 같은가?
console.log(nums === deepCopy);
```

예제 1-3

```jsx
true
false
```

예제 1-3 실행결과

위 예제에서, 처음에는 두 배열을 json 형태로 문자열화하여 두 배열의 내용이 같은지를 비교하고 있다. 그 결과 true가 나왔다. 즉, 두 배열 내 값은 똑같다. 그러나 두 번째 출력에서는 각 배열의 참조값을 담는 참조 변수들끼리 비교하고 있다. 즉, 두 참조 변수들의 참조값이 같은지를 비교하고 있는 것이다. 그 결과 false로 나왔다. 즉, 두 참조 변수는 서로 다른 참조값을 가지고 있다는 뜻이다. 정리하자면, 전개 연산자를 이용하여 복사하면 원본과 복사본의 내용은 같으나 둘의 참조값이 다르다(즉, 메모리 공간 상에서 전혀 다른 공간을 가리키고 있다는 뜻). 이로 인해 한 쪽 배열의 값이 변경되어도 다른 쪽 배열에는 전혀 영향을 끼치지 않는 깊은 복사가 가능해지는 것이다. 

이러한 전개 연산자는 iterable 객체에 한해서 사용 가능하다. 만약 유사 배열 객체도 복사를 하길 원한다면 이 전개 연산자를 사용해도 작동되지 않는다고 한다. 이 경우, 대신 Array.from() 메서드를 사용해야 한다고 한다. 

배열 뿐만 아니라 객체에도 전개 연산자를 사용할 수 있다. 

```jsx
let engDict = {
    '사과': 'apple',
    '바나나': 'banana'
};

let myEngDict = {
    '구조 분해 할당': 'destructuring assignment',
    '사람': 'person',
    ...engDict  // 전개 연산자를 이용하여 내용 복사해 넣음.
};

console.log(myEngDict);
console.log("=================");

myEngDict['사과'] = 'delicious';
// 깊은 복사가 되었는지 확인.
console.log(myEngDict);
console.log(engDict);
```

예제 1-4

```jsx
{
  '구조 분해 할당': 'destructuring assignment',
  '사람': 'person',
  '사과': 'apple',
  '바나나': 'banana'
}
=================
{
  '구조 분해 할당': 'destructuring assignment',
  '사람': 'person',
  '사과': 'delicious',
  '바나나': 'banana'
}
{ '사과': 'apple', '바나나': 'banana' }
```

예제 1-4 실행결과

## 전개 연산자의 활용

앞서 보았듯, 전개 연산자를 이용하면 같은 내용을 다른 곳에 깊은 복사하는 데에 활용될 수 있다. 

또 다른 활용법은 함수를 호출할 때 사용할 수 있다. 

```jsx
function sum(...args) { // ...args => rest 매개변수
    return args.reduce((acc, cur) => acc + cur, 0);
}

console.log(sum(1, 2, 3));

let nums = [12, 5, 2];

console.log(sum(nums[0], nums[1], nums[2])); // 번거로움...
console.log(sum(...nums));  // spread
```

예제 2-1

```jsx
6
19
19
```

예제 2-1 실행결과

위 예제와 같이 함수에 여러 인자들을 대입해야 하는데, 인자로 넘기고자 하는 데이터가 배열로 묶여 있을 경우, 전개 연산자가 없다면 `//번거로움` 주석이 있는 줄처럼 일일이 배열 요소 하나하나에 접근하여 대입해야하는 번거로움이 있다. 그러나 전개 연산자를 적용하면 배열 내 모든 요소들을 개별로 펼치기에 간편하게 인자로 대입할 수 있다. 

# rest 변수 VS Spread

[[JS][ES6] 구조 분해 할당 (destructuring assignment)](/javascript/js-es6-destructuring-assignment/) 페이지에서 보인 rest 변수와 spread 연산자 모두 `...` 연산자를 사용하기에 겉으로 보기에 헷갈릴 수 있다. 이 둘의 차이점은 다음과 같다. 

- rest 변수는 구조 분해 할당에서 나머지 데이터들을 하나로 묶어 할당받는 변수의 역할을 한다. 할당식의 왼쪽에 있는 개별 변수들 중 가장 마지막에 존재한다. 반면 전개 구문은 단순히 배열 또는 객체를 펼치는 용도로, 다른 배열 또는 객체 내부에서 사용되어 복사하는 역할을 한다.

```jsx
let arr = ['a', 'b', 'c', 'd', 'e'];
let [a, b, ...rest] = arr;  // rest

console.log(rest);

// ============
let otherArr = [...arr];  // spread
console.log(otherArr);
```

예제 3-1

```jsx
[ 'c', 'd', 'e' ]
[ 'a', 'b', 'c', 'd', 'e' ]
```

예제 3-1 실행결과

위 예제를 보더라도, rest 변수와 spread 변수의 위치부터가 다르다. rest 변수가 할당식의 왼쪽에 있는 반면, spread 변수는 할당식의 오른편에 있음을 알 수 있다. 즉, rest 변수는 데이터를 할당받는 변수, spread 변수는 다른 배열이나 객체에 자신의 내용을 복사하여 붙여넣는 용도라 볼 수 있다. 

- 함수에서 사용할 때에도 둘의 사용 위치가 다르다. rest 변수는 함수의 매개변수에 사용되어 인자로 받을 데이터들을 하나로 묶는 역할을 한다. 반면, spread 변수는 함수의 인자로 전달되어 하나로 묶인 여러 데이터들을 펼치는 역할을 한다. 즉, 이 둘은 함수에서도 정반대의 역할을 한다. 이에 대한 예제는 앞선 예제 2-1을 보면 된다.

---

References

[1] [나머지 매개변수와 전개 구문](https://ko.javascript.info/rest-parameters-spread)

[2] [07. spread 와 rest 문법 · GitBook](https://learnjs.vlpt.us/useful/07-spread-and-rest.html)

[3] [[TokTokHan.dev]ES6 문법정리](https://tech.toktokhan.dev/2021/08/22/es6/)