---
title: "[JS][비동기] Callback"
category: "javascript"
tag: ["web", "js", "javascript", "비동기", "async", "callback"]
---

# 콜백 (callback)

```jsx
function someFunc(arg1, callback) {
    // 작업 코드
    callback(arg2);
}
```

콜백(callback) 또는 콜백함수란, 함수의 매개변수로 함수명이 들어가 해당 함수 내부에서 호출되어 사용되는 함수를 말한다. 위 예제의 callback이 콜백함수에 해당한다. 

# 콜백과 비동기

둘 이상의 작업들이 서로 독립적으로 실행되는 과정이 비동기(asynchronous)다. 즉, 어떤 작업이 모두 완료되어야 다음 작업이 실행되는 직렬적 방식이 아니라, 타 작업의 완료 여부에 상관없이 작업을 실행시키는 병렬적인 방식이다. 

```jsx
// A, B, c 작업들이 비동기적으로 처리된다고 가정.
funcA();
funcB();
funcC();
```

위 코드와 같이 각각 비동기적으로 작업을 수행하는 함수들을 호출하면, 코드 상 맨 위에 있는 `funcA()` 함수가 호출되더라도 `funcB()` 함수는 `funcA()` 함수의 작업이 모두 완료될 때까지 기다리지 않고, 독립적으로 자신의 작업을 수행한다. 

그런데 비동기 프로그래밍에서도 선행 작업이 모두 완료되어야 후 작업을 할 수 있는 경우가 있을 수 있다. 다른 스크립트들을 모두 로딩해야 해당 스크립트 내부의 자원들을 가져다 쓸 수 있는 경우, 서버나 데이터베이스로부터 데이터를 요청한 후 해당 데이터를 얻어와야 다른 작업을 할 수 있는 경우 등이 그 예일 것이다. 만약 위 코드에서 `funcB()` 함수는 `funcA()` 함수의 작업이 모두 완료되어야 실행되어야 하는 함수라면 에러가 발생하거나 원치 않은 결과를 얻을 것이다. 이러한 문제를 방지하는 한 방법이 바로 콜백을 사용하는 것이다. 

```jsx
function funcA(callback) {
    let data = ...
    // 데이터 등을 가져오는 작업 코드.
    callback(data);
}

funcA(funcB);
funcC();
```

콜백 함수를 사용하여 위와 같이 코드를 변경하면, funcA(또는 B)와 C는 서로 비동기적인 특성을 유지하면서도, funcA 함수의 작업이 모두 완료된 후에 funcB 함수를 실행하게끔 하는 강한 규칙을 적용할 수 있게 된다. 

다음은 서버나 데이터베이스로부터 사용자의 데이터를 얻어오고 이를 출력해보는 예제이다. 먼저 콜백 함수를 전혀 사용하지 않은 예이다.

```jsx
function getData() {
    let userInfo;

    // 유저 정보를 가져오는데 시간이 오래 걸리는 상황을 표현함.
    setTimeout(() => {
        userInfo = {
            name: '자바스',
            age: 20,
            job: '웹 개발자'
        };
    }, 1000);

    return userInfo;
}

function printData(dataObj) {
    try {
        console.log("===== 사용자 정보 =====");
        console.log(`이름 : ${dataObj.name}`);
        console.log(`나이 : ${dataObj.age}`);
        console.log(`직업 : ${dataObj.job}`);
    } catch (e) {
        if (e instanceof TypeError) {
            console.log("에러: 사용자 정보를 읽어올 수 없었습니다.");
        } else {
            throw e;
        }
    }
}

let myInfo = getData();
printData(myInfo);

```

예제 1-1

```jsx
===== 사용자 정보 =====
에러: 사용자 정보를 읽어올 수 없었습니다.
```

예제 1-1 실행결과

위 예제의 `getData()` 함수가 외부에서 데이터를 가져오는 함수이다. 외부에서 데이터를 가져오면 이를 보기 좋게 가공하여 출력한 다음 이를 반환하는 방식이다. 그런데 데이터를 가져오는 시간이 오래 걸리면 그 뒤의 코드가 실행된다. 위 예제에서는 `setTimeout()` 함수를 통해 외부 데이터를 가져오는데 시간이 오래 걸리는 상황을 묘사해보았다. 이 경우, 그 뒤의 코드인 return 문이 실행된다. 그러면 undefined 값이 반환될 것이다. 이 값을 `printData()` 함수에서 존재하지 않는 값의 name, age 등의 프로퍼티에 접근하려고 하니 위와 같이 에러가 발생한다. 이를 콜백함수로 해결하면 다음과 같다. 

```jsx
function getData(callback) {  // 콜백 함수 인자 추가
    let userInfo;

    setTimeout(() => {
        userInfo = {
            name: '자바스',
            age: 20,
            job: '웹 개발자'
        };
        callback(userInfo);  // 콜백함수 호출
    }, 1000);

    return userInfo;
}

function printData(dataObj) {
    try {
        console.log("===== 사용자 정보 =====");
        console.log(`이름 : ${dataObj.name}`);
        console.log(`나이 : ${dataObj.age}`);
        console.log(`직업 : ${dataObj.job}`);
    } catch (e) {
        if (e instanceof TypeError) {
            console.log("에러: 사용자 정보를 읽어올 수 없었습니다.");
        } else {
            throw e;
        }
    }
}

getData(printData);

```

예제 1-2

```jsx
===== 사용자 정보 =====
이름 : 자바스
나이 : 20
직업 : 웹 개발자
```

예제 1-2 실행결과

콜백함수를 사용하니, 아무리 외부 데이터를 가져오는 시간이 오래 걸리더라도, 이를 가져온 후에 출력하는 과정이 보장되어 에러가 발생하지 않는 것을 볼 수 있다. 

콜백함수를 사용하는 예로는 `setTimeout()`, `addEventListener()` 등이 있다. 

# 중첩 콜백

상황에 따라 콜백 속의 콜백, 즉 중첩 콜백이 발생할 수도 있다. 다음의 예제를 보겠다. 

```jsx
class User {
    constructor(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }
}

export const database = [
    new User('자바스', 20, '웹 개발자'), 
    new User('파이썬', 25, '데이터 분석가'),
    new User('나자바', 28, '백엔드 개발자'),
    new User('김백수', 30, '무직')
];

export function printData(dataObj) {
    try {
        console.log("===== 사용자 정보 =====");
        console.log(`이름 : ${dataObj.name}`);
        console.log(`나이 : ${dataObj.age}`);
        console.log(`직업 : ${dataObj.job}`);
    } catch (e) {
        if (e instanceof TypeError) {
            console.log("에러: 사용자 정보를 읽어올 수 없었습니다.");
        } else {
            console.log("알 수 없는 예외 발생. 다음은 해당 에러 메시지입니다.");
            console.log(e);
        }
    }
}
```

예제 2-1. user-info.js

```jsx
import { database, printData } from "./user-info.js";

function getData(id, callback) {
    let userInfo;

    setTimeout(() => {
        userInfo = database[id];
        callback(userInfo);
    }, 1000);

    return userInfo;
}

// 중첩 콜백
getData(0, data => {
    getData(1, data => {
        getData(2, data => {
            getData(3, printData);
            printData(data);
        });
        printData(data);
    });
    printData(data);
});

```

예제 2-2

```jsx
===== 사용자 정보 =====
이름 : 자바스
나이 : 20
직업 : 웹 개발자
===== 사용자 정보 =====
이름 : 파이썬
나이 : 25
직업 : 데이터 분석가
===== 사용자 정보 =====
이름 : 나자바
나이 : 28
직업 : 백엔드 개발자
===== 사용자 정보 =====
이름 : 김백수
나이 : 30
직업 : 무직
```

예제 2-2 실행결과

중첩 콜백 코드와 실행결과를 비교해보면, 바깥 함수부터 실행된 후에 안쪽 함수가 실행되는 순서임을 알 수 있다. 

위와 같은 중첩 콜백 코드가 깊어질 수록 오른쪽 방향의 화살표 모양이 되어버려 코드의 가독성이 떨어질 것이다. 이러한 현상을 멸망의 피라미드(pyramid of doom) 또는 콜백 지옥(callback hell)이라고 부른다. 

이러한 콜백 지옥의 문제점을 해결할 수 있는 대안으로 프로미스(promise)가 있다고 한다. 이에 대해선 다음 문서에서 다뤄보도록 하겠다. 

---

References

[1] [콜백](https://ko.javascript.info/callbacks)

[2] [자바스크립의 콜백 함수 – 자바스크립트에서 콜백 함수가 무엇이고 어떻게 사용하는지 알아봅시다](https://www.freecodecamp.org/korean/news/https-www-freecodecamp-org-news-javascript-callback-functions-what-are-callbacks-in-js-and-how-to-use-them/)

[3] [📚 콜백 함수(Callback) 개념 & 응용 - 완벽 정리](https://inpa.tistory.com/entry/JS-📚-자바스크립트-콜백-함수)