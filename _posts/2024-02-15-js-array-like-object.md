---
title: "[JS] 유사 배열 객체 (Array-like Object)"
category: "javascript"
tag: ["web", "js", "javascript", "array", "배열", "object", "객체"]
---

# 유사 배열 객체 (Array-like Object)

```jsx
let arr = ['hi', 'nice', 'to', 'meet', 'you'];

let arrLike = {
    0: 'hi',
    1: 'nice',
    2: 'to',
    3: 'meet',
    4: 'you',
    length: 5
};

console.log(arr[1]);
console.log(arrLike[1]);
console.log(arr.length);
console.log(arrLike.length);

console.log(arr);
console.log(arrLike);
```

![예제 1-1 실행결과](/images/2024-02-15/js-array-like-object/1.png)

예제 1-1 실행결과

위 예제를 보면, 변수 arr와 arrLike는 arr[1], arrLike[1], arr.length, arrLike.length에서 보듯이, arrLike도 배열 객체와 비슷한 행동을 보인다. 그러나 변수 선언 시 할당된 값의 형태를 보면 arr는 대괄호( [] )로 둘러쌓인 배열이고, arrLike는 중괄호 ( {} )로 둘러쌓인 ‘객체’ 선언으로, 둘은 차이가 있음을 알 수 있다. 여기서 arrLike는 배열처럼 보이지만 사실은 일반적인 객체이다. 그럼에도 각 요소들마다 그 키로 0으로 시작되는 인덱스가 부여되어 요소가 하나씩 늘어날수록 그에 대응되는 인덱스도 하나씩 증가되어 부여된다. 또한, 배열처럼 해당 객체에는 length라는 프로퍼티를 가진다. 

이렇게, 배열과 같이 요소마다 인덱스로 접근 가능하고, 전체 요소들의 개수를 저장하는 length 프로퍼티를 가지나, 사실은 배열 객체가 아닌 일반적인 객체를 유사 배열 객체 (Array-like Object)라 한다. 

유사 배열 객체는 배열 객체가 아니기에, 배열 객체에서 쓸 수 있는 메서드들을 사용할 수 없다. 다음은 앞서 살펴본 arr와 arrLike에 각각 배열 객체 메서드인 forEach()를 사용하는 예제이다. 

```jsx
let arr = ['hi', 'nice', 'to', 'meet', 'you'];
let arrLike = {
    0: 'hi',
    1: 'nice',
    2: 'to',
    3: 'meet',
    4: 'you',
    length: 5
};

arr.forEach((n) => console.log(n));
arrLike.forEach((n) => console.log(n));
```

![예제 1-2 실행결과](/images/2024-02-15/js-array-like-object/2.png)

예제 1-2 실행결과

실행 결과, 배열 객체인 arr에 대해서는 forEach() 메서드가 잘 실행되나, 유사 배열 객체인 arrLike에 대해서는 에러가 발생함을 알 수 있다. 

# 유사 배열 객체에 해당하는 객체들

유사 배열 객체에 해당되는 것들에는 다음이 존재한다. 

## 함수의 인자

함수의 인자로 전달된 값들을 저장하는 arguments 객체가 있는데, 해당 객체는 유사 배열 객체이다. 

```jsx
function identifyArgs() {
    console.log(arguments);
    console.log(arguments[0]);
    console.log(arguments.length);
    console.log(arguments.pop());
}
identifyArgs('good', 'morning', 'everyone');
```

![예제 2-1 실행결과](/images/2024-02-15/js-array-like-object/3.png)

예제 2-1 실행결과

위 실행결과를 보면 알겠지만, 인덱싱도 가능하고, length 프로퍼티도 존재하나 배열의 메서드를 사용할 수 없다. 

## NodeList, HTMLCollection

`document.querySelectorAll()` 메서드는 그 반환값으로 NodeList라는 객체를 반환하고, `document.getElementsByClassName()`와 같은 메서드들은 그 반환값으로 HTMLCollection 이라는 객체를 반환하는데, 이 둘 모두 유사 배열 객체이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div class="hi"></div>
    <div class="hi"></div>
    <script src="js-ex.js"></script>
</body>
</html>
```

```jsx
let arr = [1, 2, 3];
let selectors = document.querySelectorAll('div');
let his = document.getElementsByClassName('hi');
console.log(arr);
console.log(selectors);
console.log(his);

selectors.forEach((n) => console.log(n));
his.forEach((n) => console.log(n));
```

![예제 3-1~2 실행결과](/images/2024-02-15/js-array-like-object/4.png)

예제 3-1~2 실행결과

실행결과를 보면, arr 변수를 출력했을 때, `[[Prototype]]`에 `Array(0)`이라 적혀있다. 이는 해당 변수가 배열 객체임을 말해준다. 반면, selectors, his 변수들은 그 타입이 각각 NodeList, HTMLCollection으로 나와 있다. 즉, 유사 배열 객체라는 것이다. HTMLCollection 객체의 경우, 배열 객체의 메서드인 forEach() 메서드를 사용하면 에러가 발생하는 것을 알 수 있다. 

References[4]에 따르면, NodeList 객체는 특이하게 배열 객체에도 존재하는 메서드인 entries(), forEach() 등이 존재한다고 한다. 그래서 위 실행결과에서 NodeList 객체에 대해 forEach()를 사용했음에도 에러가 나지 않았던 것이다. 다만, 배열 객체의 모든 메서드를 가지고 있는 것이 아니라 극히 일부만을 가지고 있다. 

# 유사 배열 객체를 배열 객체로 바꾸기

만약 유사 배열 객체를 배열 객체로 바꿔서 사용하고자 한다면 어떻게 해야 할까? 다음의 방법들이 존재한다. 

## 전개 연산자 (…)

ES6부터 지원되는 전개 연산자 … 문법을 이용하면 된다. 전개 연산자는 배열, 문자열, 객체처럼 반복 가능한(iterable) 객체를 여러 개의 요소들로 확장시킬 수 있는 연산자이다. 다음은 전개 연산자에 대해 살펴본 예제이다.

```jsx
// 객체로 복사
let arr = [1, 2, 3, 4, 5];
let someVar = {...arr};
console.log(someVar);
console.log(typeof someVar);

// 배열로 복사.
let arr2 = [...arr];
arr2[2] = 12;
console.log(arr2);
console.log(arr);

// 함수 인자로 전달
function getTotal(a, b, c) {
    return a + b + c;
}
console.log(getTotal(...[1, 2, 3]));
```

```jsx
{ '0': 1, '1': 2, '2': 3, '3': 4, '4': 5 }
object
[ 1, 2, 12, 4, 5 ]
[ 1, 2, 3, 4, 5 ]
6
```

위 예제에서 볼 수 있듯, 전개 연산자 뒤에 반복 가능한 객체를 사용한 경우, 해당 객체 내 요소들을 하나씩 꺼내는 형태임을 알 수 있다. arr2 변수의 경우 […arr] = [1, 2, 3, 4, 5]와 같다. 이렇게 전개 연산자는 복사의 기능도 있으며, 복제된 배열과 원본 배열이 완전히 분리됨을 알 수 있다(깊은 복사). 한 편, 위 예제의 getTotal() 함수처럼 함수 인자로 대입할 때도 전개 연산자를 사용할 수 있다. getTotal(…[1, 2, 3]) 형태로 함수를 호출하였는데, 이는 곧 배열 [1, 2, 3]에서 안에 있는 모든 요소들을 꺼내는 것으로, 결국 getTotal(1, 2, 3)과 같은 형태가 된다. 

이러한 전개 연산자를 통해 유사 배열 객체를 배열 객체로 변환시킬 수 있다. 다음 예제는 앞서 살펴본 예제 3-2를 약간 수정한 것이다.

```jsx
let arr = [1, 2, 3];
let selectors = document.querySelectorAll('div');
let his = document.getElementsByClassName('hi');
let hisArray = [...his];
console.log(arr);
console.log(selectors);
console.log(his);

selectors.forEach((n) => console.log(n));
hisArray.forEach((n) => console.log(n));
hisArray[1] = 'hello';
// 원본과 복제본이 연결되어 있는지 테스트.
console.log(hisArray);
console.log(his);
```

![예제 4-2 실행결과](/images/2024-02-15/js-array-like-object/5.png)

예제 4-2 실행결과

유사 배열 객체가 할당된 his 변수를 전개 연산자를 이용하여 hisArray 변수에 배열 객체로 복사하였고, 해당 변수에 forEach() 메서드를 사용한 결과, 에러 발생 없이 잘 작동되는 것을 알 수 있다. 또한, 원본 유사 배열 객체와 분리되어 복사되기에, 복사된 배열 객체의 요소를 수정해도 원본 유사 배열 객체에 영향이 가지 않는다는 것도 알 수 있다. 

## Array.from()

유사 배열 객체를 배열 객체로 변환한 복제본을 이용하는 방법은 전개 연산자 말고도 Array.from() 메서드를 이용해도 똑같은 효과를 볼 수 있다. 앞선 예제 4-2에서, `let hisArray = Array.from(his);` 으로 바꿔도 똑같이 동작한다. 

---

References

[1] [Why do you need to know about Array-like Objects?](https://daily.dev/blog/why-do-you-need-to-know-about-array-like-objects)

[2] [velog](https://velog.io/@onezerokang/유사-배열-객체와-배열의-차이)

[3] [Array - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array)

[4] [NodeList - Web API \| MDN](https://developer.mozilla.org/ko/docs/Web/API/NodeList)

[5] 전개 연산자 

[전개 구문 - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)