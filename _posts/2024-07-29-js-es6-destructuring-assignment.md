---
title: "[JS][ES6] 구조 분해 할당 (Destructuring Assignment)"
category: "javascript"
tag: ["web", "js", "javascript", "es6", "구조 분해 할당", "Destructuring assignment"]
---

# 개요

객체나 배열은 여러 개의 데이터들을 속성(property)으로 저장함으로써, 여러 데이터들을 하나로 묶어 저장할 수 있다. 이러한 객체, 배열 내 데이터들을 개별 변수로 분해하는 것을 구조 분해 할당(Destructuring assignment)이라 한다. 

이를 이용하면 함수에 전달할 객체 또는 배열 내 데이터들 중 일부만을 전달하고자 할 때, 객체 또는 배열 내 데이터들을 따로 변수들로 담아야 하는 상황 등이 있을 때 코드를 좀 더 간결하게 작성할 수 있게 된다. 

해당 문법은 ES6에 추가된 문법이라고 한다. 

배열과 객체의 구조 분해 할당에 대한 형태가 조금 다르므로 따로 분리하여 설명하겠다.

# 배열의 구조 분해 할당

다음은 배열의 구조 분해 할당을 보여주는 간단한 예제이다.

```jsx
let arr = ['hi', 'guys'];
let [hello, toWhom] = arr;  // 배열의 구조 분해 할당.

console.log(hello, toWhom);
```

예제 1-1

```jsx
hi guys
```

예제 1-1 실행결과

위 예제를 보면, 배열에 담긴 데이터들을 각각의 개별 변수로 담기 위해, 개별 변수 hello, toWhom들을 배열을 의미하는 대괄호([])로 둘러싸서 let으로 선언하고 있다. 그 후, 오른쪽 항에는 이 개별 변수들에 할당할 배열을 대입한다. 그러면 배열 내 데이터들이 순차적으로 왼쪽부터 있는 개별 변수 hello, toWhom에 할당된다. 이것이 배열의 구조 분해 할당이다. 

```jsx
let arr = ['hi', 'guys'];
let [hello, toWhom] = arr;  // 배열의 구조 분해 할당.

arr[1] = 'boys';

console.log(hello, toWhom);
console.log(arr);
```

예제 1-2 실행결과

```jsx
hi guys
[ 'hi', 'boys' ]
```

예제 1-2 

위 예제에서, 배열 내 일부 데이터를 변경해도, 배열의 데이터로부터 할당 받은 개별 변수들에 할당된 값은 변하지 않는 것을 확인할 수 있다. 배열 내 값들을 복사한 후, 이를 개별 변수에 할당하기 때문이다. 

배열의 크기와 배열로부터 구조 분해 할당받을 개별 변수의 개수가 반드시 동일할 필요는 없다. 

```jsx
// #1
let arr = ['hi', 'guys', 'today'];
let [hello, toWhom] = arr; 

console.log(hello, toWhom);
console.log(arr);

// #2
let arr2 = ['good', 'day'];
let [feel, date, otherVar] = arr2;

console.log(feel, date, otherVar);
console.log(arr2);
```

예제 1-3

```jsx
hi guys
[ 'hi', 'guys', 'today' ]
good day undefined
[ 'good', 'day' ]
```

예제 1-3 실행결과

위 예제의 #1의 경우, 배열의 길이가 개별 변수의 개수보다 더 크다. 그럼에도 배열의 왼쪽 데이터부터 개별 변수에 차례대로 할당된다. 다만 남는 배열 내 데이터(위 예제에선 ‘today’)들은 어딘가에 저장되지 않을 뿐이다.  #2의 경우, 배열의 길이가 개별 변수의 개수보다 더 작음에도 원활하게 구조 분해 할당이 이뤄지고 있다. 이 경우, 남는 개별 변수에는 더 이상 할당받을 배열 내 데이터가 없기에 undefined가 할당된다. 

구조 분해 할당에서, 할당 연산자 우측 자리에는 배열을 비롯하여 모든 반복 가능한 객체(iterable)들도 넣어 구조 분해 할당을 할 수 있다. 다음은 문자열을 구조 분해 할당한 모습이다.

```jsx
let [a, b, c] = 'abc';

console.log(a, b, c);
```

예제 1-4

```jsx
a b c
```

예제 1-4 실행결과

할당 연산자 좌측에는 할당할 수 있는 모든 것들이 올 수 있다. 다음은 배열 내 데이터들을 단순한 변수가 아닌, 객체의 프로퍼티에 할당받는 모습이다.

```jsx
let myInfo = {};
let arr = ['자바스', 29];

[myInfo.name, myInfo.age] = arr;
console.log(myInfo);
```

예제 1-5

```jsx
{ name: '자바스', age: 29 }
```

예제 1-5 실행결과

## 쉼표로 배열 요소 버리기

배열의 중간 데이터는 개별 변수에 할당하고 싶지 않을 때, 대신 공백으로 남기면 된다.

```jsx
let foods = ['두부', '취두부', '소시지'];

// 취두부는 싫어서 두 번째 개별 변수 자리를 공백으로 대신하였다. 
let [myFirstFav, , mySecondFav] = foods;  

console.log(myFirstFav, mySecondFav);
```

예제 2-1

```jsx
두부 소시지
```

예제 2-1 실행결과

위 예제에서, 배열의 두 번째 데이터는 개별 변수에 할당하고 싶지 않아 공백을 두었다. 그랬더니 해당 데이터는 그 어떤 개별 변수에도 할당되지 않은 것을 확인할 수 있다. 

## 배열 구조 분해 할당의 활용

배열의 구조 분해 할당을 유용하게 활용할 수 있다. 

먼저, 둘 이상의 변수들의 값을 서로 교환할 수 있다. 다음은 숫자들이 들어있는 배열을 받으면 이를 오름차순 정렬해주는 함수들을 정의한 예제이다.

```jsx
/**
 * 정수가 담긴 배열을 오름차순 정렬한다.
 * @param {number[]} arr 
 */
function arrSort(arr) {
    let nums = [...arr];
    let temp;
    for (let j = 0; j < nums.length; j++) {
        for (let i = 0; i < nums.length - j - 1; i++) {
            if (nums[i] > nums[i+1]) {
                temp = nums[i];
                nums[i] = nums[i+1];
                nums[i+1] = temp;
            }
        }
    }
    return nums;
}

/**
 * 
 * @param {number[]} arr 
 */
function arrSortWithDA(arr) {
    let nums = [...arr];

    for (let j = 0; j < nums.length; j++) {
        for (let i = 0; i < nums.length - j - 1; i++) {
            if (nums[i] > nums[i+1]) {
                // 배열 구조 분해 할당으로 둘 이상의 변수들의 값을
                // 쉽게 교환할 수 있다. 
                [nums[i], nums[i+1]] = [nums[i+1], nums[i]];
            }
        }
    }
    return nums;
}

let nums = [4, 12, 3, 8, 10, 9]; 
let result;

console.log('original: ', nums);
result = arrSort(nums);
console.log('sorted: ', result);

result = arrSort(nums);
console.log('sorted with destructuring assignment: ', result);

nums.sort((a, b) => a - b);
console.log(`answer: `, nums);
```

예제 3-1

```jsx
original:  [ 4, 12, 3, 8, 10, 9 ]
sorted:  [ 3, 4, 8, 9, 10, 12 ]
sorted with destructuring assignment:  [ 3, 4, 8, 9, 10, 12 ]
answer:  [ 3, 4, 8, 9, 10, 12 ]
```

예제 3-1 실행결과

위 예제의 arrSort 함수 내부를 보면, 배열 내 인접한 두 값을 비교하고, 앞선 위치의 값이 바로 뒤의 값보다 크면 두 값의 위치를 바꾸는 방식으로 정렬을 수행하는 구조이다. 이 과정에서 temp라는 임시 변수를 만들어 두 값이 제대로 바뀌도록 돕고 있다. 하지만 이 임시 변수의 존재로 코드가 조금 길어진 느낌이다. 이를 밑의 arrSortWithDA 함수 내부에서는 배열의 구조 분해 할당을 이용하여 해결하고 있다. 임시 변수를 선언하지 않고 바로 두 값을 바꿀 수 있게 된 것이다. 이를 통해 코드의 길이를 조금이나마 줄이고 가독성도 높일 수 있다. 

또한, 배열 객체의 `entries()` 메서드와 결합하여 for 문에서 배열 내 데이터와 그 데이터의 인덱스를 동시에 추출할 수 있다.

```jsx
let arr = ['햄버거', '피자', '짜장면', '김밥'];

for (let [idx, data] of arr.entries()) {
    console.log(idx, data);
}
```

예제 3-2

```jsx
0 햄버거
1 피자
2 짜장면
3 김밥
```

예제 3-2 실행결과

이렇듯, 배열, 객체와 같은 iterable 객체를 for문에서 순회해야할 때 여러 정보들을 한 번에 추출할 때 유용하다. 

## rest

배열 내 일부 데이터들은 하나의 변수에 할당하고자 할 때 rest문법을 사용할 수 있다. 

```jsx
let nums = [1, 2, 3, 4, 5, 6];
let [one, two, ...rest] = nums;

console.log(one, two);
console.log(rest);
```

예제 4-1

```jsx
1 2
[ 3, 4, 5, 6 ]
```

예제 4-1 실행결과

위 예제에서, nums 배열의 세 번째 요소부터는 rest 변수에 하나의 배열로 묶어 할당하였다. rest라는 변수 앞에 `...` 만 붙이면 나머지 데이터들을 하나로 묶을 수 있는 것이다. 

주의할 점은, rest 변수는 반드시 맨 마지막에 위치해야 한다. 다음의 예제는 에러를 일으킨다.

```jsx
let nums = [1, 2, 3, 4, 5, 6];
let [one, two, ...rest, last] = nums;

console.log(one, two);
console.log(rest);
```

예제 5-1

```jsx
SyntaxError: Rest element must be last element
```

예제 5-1 실행결과

이러한 rest 문법은 함수의 매개변수로 사용할 수 있다. 이 경우, 함수에 전달할 인자의 개수가 정해지지 않고 동적으로 변할 때 유연하게 대처할 수 있다. 다음은 여러 개의 숫자들을 받아 이들의 합을 출력하는 함수를 rest 매개변수와 함께 구현한 예제이다.

```jsx
function sum(...rest) {
    let total = 0;
    for(let num of rest) {
        total += num;
    }
    return total;
}

console.log(sum(1, 2));
console.log(sum(1, 2, 3, 4, 5));
console.log(sum(1, 2, 3, 4, 5, 6, 7));

```

예제 5-2

```jsx
3
15
28
```

예제 5-2 실행결과

## 기본값 설정

```jsx
let [one, two] = [];

console.log(one, two);
```

예제 6-1

```jsx
undefined undefined
```

예제 6-1 실행결과

만약 구조 분해 할당할 배열 내 데이터가 전부 없거나 일부 누락된 경우, 위와 같이 개별 변수들을 출력해보면 undefined로 나온다. 이렇게 할당 받지 못한 변수에는 따로 기본값을 지정해줄 수 있다. 

```jsx
let [one = 1, two = '2'] = [3];
console.log(one, two);
```

예제 6-2

```jsx
3 2
```

예제 6-2 실행결과

위와 같이 식의 좌측에 있는 개별 변수에 ‘=’ 할당 연산자와 함께 기본값을 지정해줄 수 있다. 이 경우 식의 우측에 오는 배열 내 데이터가 없어 할당받지 못하면 기본값으로 저장되는 방식이다. 물론 우측에 오는 배열 내 데이터가 있으면 그 값을 할당받는다. 

# 객체의 구조 분해 할당

객체도 배열과 비슷하게 구조 분해 할당할 수 있다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let {job, name, age} = myInfo;

console.log(job, name, age);
```

예제 7-1

```jsx
SW Developer javas 20
```

예제 7-1 실행결과

객체의 경우, 중괄호({})를 이용하여 개별 변수들을 묶어 분해 할당한다. 그리고 객체 내 프로퍼티의 키의 이름과 똑같이 작성한다. 이 때 순서는 상관없다. 

만약 개별 변수명을 객체 내 프로퍼티 키의 이름과 다르게 하고 싶다면 다음과 같이 작성하면 된다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let {job: j, name: n, age: a} = myInfo;

console.log(j, n, a);
```

예제 7-2

위 예제도 예제 7-1의 결과와 동일하다. 위 예제에서는 각각 job, name, age 대신 j, n, a라는 새로운 변수명으로 할당받고 있다. 이 때, 기존의 개별 변수명인 job, name, age는 사용할 수 없고, 새롭게 이름 지은 j, n, a 변수를 사용해야 한다. (콜론 왼쪽의 이름을 사용하려고 하면 정의되지 않은 변수라면서 에러가 뜬다) 

객체에도 기본값을 정의할 수 있으며, 새 변수명을 위한 콜론과 함께 쓰일 수 있다. 아래 예제의 desInc 변수가 그 예이다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let {job: j, name: n, age: a, desiredIncome: desInc = 5000} = myInfo;

console.log(j, n, a, desInc);
```

예제 7-3

```jsx
SW Developer javas 20 5000
```

예제 7-3 실행결과

객체의 경우, 원하는 프로퍼티의 값만 뽑을 수 있다. 위 예제의 경우 `let {job} = myInfo` 라고 지정하면 job의 값만 가져올 수 있다. 

객체에서도 마찬가지로 rest 변수를 사용할 수 있다. 이 때 rest 변수에는 나머지 프로퍼티들이 객체 형태로 할당된다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let {job, ...rest} = myInfo;

console.log(job, rest);
```

예제 7-4

```jsx
SW Developer { name: 'javas', age: 20 }
```

예제 7-4 실행결과

## 주의사항 : let 없이 사용할 때

let 등의 선언자 없이도 분해 할당할 수 있는데, 객체의 경우 이에 주의해야 한다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let job, name, age;
{job, name, age} = myInfo; // SyntaxError: Unexpected token '='

console.log(job, name, age);

```

예제 8-1

자바스크립트에서 중괄호는 여러 코드들을 묶은 코드 블록으로도 인식할 수 있기 때문이다. 그래서 이 경우 전체 할당문을 괄호()로 감싸야 한다. 

```jsx
let myInfo = {
    name: 'javas',
    age: 20,
    job: 'SW Developer'
};

let job, name, age;
({job, name, age} = myInfo);  // 괄호로 감싸야 에러가 발생하지 않는다.

console.log(job, name, age);

```

예제 8-2

# 구조 분해 할당 활용

구조 분해 할당을 이용하여 함수의 매개변수에 적용할 수 있다. 

```jsx
let userInfo = {
    nickName: 'javas',
    age: 25,
    email: 'javas@aaa.com',
    job: 'Web Dev'
};

function printUserInfo(nickName, age = 20, email, job) {
    console.log("=== 사용자 정보 ===");
    console.log("닉네임: ", nickName);
    console.log("나이: ", age);
    console.log("이메일: ", email);
    console.log("직업: ", job);
    console.log("==================");
}

printUserInfo(userInfo.nickName, undefined, userInfo.email, userInfo.job);
```

예제 9-1

```jsx
=== 사용자 정보 ===
닉네임:  javas
나이:  20
이메일:  javas@aaa.com
직업:  Web Dev
==================
```

예제 9-1 실행결과

위 예제에서 `printUserInfo` 함수에는 문제점이 있다. 객체 내 정보들을 인자로 넘길 때 `userInfo.nickName` 과 같이 일일이 프로퍼티에 접근하여 이를 전달해줘야 한다. 만약 매개변수의 수가 더 많았다면 꽤 번거로웠을 것이다. 

또한, 매개변수 중간에 기본값이 설정된 매개변수가 있을 때, 해당 기본값을 사용하고자 한다면 위와 같이 `undefined` 라고 명시적으로 넘겨야 한다. 그냥 공백으로 두면 에러가 발생하기 때문이다. 

이러한 문제점들을 객체의 구조 분해 할당으로 해결할 수 있다. 

```jsx
let userInfo = {
    nickName: 'javas',
    age: 25,
    email: 'javas@aaa.com',
    job: 'Web Dev'
};

function printUserInfo({nickName, age = 20, email, job, say = "할 말 없음"}) {
    console.log("=== 사용자 정보 ===");
    console.log("닉네임: ", nickName);
    console.log("나이: ", age);
    console.log("이메일: ", email);
    console.log("직업: ", job);
    console.log("남긴 말: ", say);
    console.log("==================");
}

printUserInfo(userInfo);
```

예제 9-2

```jsx
=== 사용자 정보 ===
닉네임:  javas
나이:  25
이메일:  javas@aaa.com
직업:  Web Dev
남긴 말:  할 말 없음
==================
```

예제 9-2 실행결과

구조 분해 할당으로 인해 함수의 인자로 객체 하나만 전달하면 되어 훨씬 더 간편해졌다. 

---

References

[1] [구조 분해 할당](https://ko.javascript.info/destructuring-assignment)

[2] [[JavaScript] ES6 - 구조 분해 할당 (feat. Spread/Rest 문법)](https://velog.io/@klloo/JavaScript-ES6-구조-분해-할당)

[3] [구조 분해 할당 - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

[4] [나머지 매개변수와 전개 구문](https://ko.javascript.info/rest-parameters-spread)

[5] [07. spread 와 rest 문법 · GitBook](https://learnjs.vlpt.us/useful/07-spread-and-rest.html)