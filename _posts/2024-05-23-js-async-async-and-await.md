---
title: "[JS][비동기] Async and Await"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "await", "async/await"]
---

async와 await는 promise 대신 더 가독성 있게 사용할 수 있는 키워드이다. 

# async

async 키워드는 함수 정의부의 앞에 붙는다. 예를 들어 `function someFunc() {}`이라고 정의했다면, function 키워드 앞에 async 키워드가 붙을 수 있다. 

```jsx
async function someFunc() {
    return 'hi';
}

console.log(someFunc());
someFunc().then(result => console.log(result + " there!"));
```

예제 1-1

```jsx
Promise { 'hi' }
hi there!
```

예제 1-1 실행결과

async가 붙은 함수는 항상 Promise 객체를 반환한다. 설령 해당 함수 내부에서 Promise 객체를 반환하는 코드가 명시되어 있지 않더라도 자동으로 이행된 프로미스 객체가 반환된다. 따라서 위 예제의 마지막 코드처럼 해당 함수 호출 후 바로 그 뒤에 `then()` 메서드를 사용하는 것도 가능하다. 

물론 명시적으로 Promise 객체를 반환하도록 하는 것도 가능하다. 결과는 동일하다. 다음 예제는 앞선 예제 1-1에서 Promise 객체를 명시적으로 작성하여 반환하는 코드로 바꾼 예제이다. 

```jsx
async function someFunc() {
    return new Promise((resolve, reject) => {
        resolve('hi');
    });
}

someFunc().then(result => console.log(result + " there!"));
```

예제 1-2

```jsx
hi there!
```

예제 1-2 실행결과

# await

await 키워드는 async 함수 내부에서만 사용할 수 있는 키워드로, await 키워드 뒤에 작성한 Promise 객체가 처리될 때까지 기다린다. 그러면 await 문이 작성된 줄의 뒷 줄에 존재하는 코드들은 await 문의 프로미스가 완료될 때까지 실행되지 않고 기다리다가, 해당 프로미스가 완료된 후에야 실행된다. 

```jsx
async function someFunc() {
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve('hello world!'), 1000);
    });
    let result = await promise;
    // 이 아래 부분은 then 핸들러 구역과 마찬가지
    console.log("실행 결과: " + result);
    console.log("실행 완료");
    // then 구역 끝
}

someFunc();
console.log("비동기 함수 바깥에서 인사올립니다.");
```

예제 2-1

```jsx
비동기 함수 바깥에서 인사올립니다.
실행 결과: hello world!
실행 완료
```

예제 2-1 실행결과

await 문은 이행된 프로미스의 result 프로퍼티의 값 (성공한 경우 resolve(value)의 value, 거절된 경우 reject(error)의 error가 값이 됨)을 반환한다. 

위 예제 코드에서, async 함수 내부에서 await 문 뒤의 코드들은 마치 promise의 `then()` 메서드의 인자로 전달된 핸들러 함수 내 코드와 똑같이 행동한다. 위 예제를 promise와 `then()` 메서드를 이용하여 변형하면 다음과 같다. 

```jsx
function someFunc() {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve('hello world!'), 1000);
    })
}

someFunc().then(result => {
    console.log("실행 결과: " + result);
    console.log("실행 완료");
});
console.log("비동기 함수 바깥에서 인사올립니다.");
```

예제 2-2

위 예제 코드의 결과는 예제 2-1과 동일하다.

앞서 잠깐 언급했듯, await 키워드는 async 함수 내부에서만 사용할 수 있다. 따라서 일반 함수나 전역 스코프에서는 await 키워드를 사용할 수 없다. 

기존의 promise에서 thenable 객체를 사용할 수 있었듯이, await에서도 똑같이 thenable 객체를 사용할 수 있다. 

```jsx
class MyThenable {
    constructor(name) {
        this.name = name;
    }

    then(resolve, reject) {
        setTimeout(() => resolve(this.name), 1000);
    }
}

async function introduceMyself(yourName) {
    let result = await new MyThenable(yourName);
    console.log(`안녕하신가, ${result}!`);
}

introduceMyself('자바스');
```

예제 2-3

```jsx
안녕하신가, 자바스!
```

예제 2-3 실행결과

await 문에 Promise 객체가 아닌 값이 오면, 자바스크립트는 해당 값을 result 프로퍼티의 값으로 하는 이행된 프로미스 객체로 전환된다. 

```jsx
async function someFunc() {
    let someVar = await 'hi, there!';
    console.log(someVar);
}

async function otherFunc() {
    let someVar = await new Promise((resolve, reject) => {
        resolve('hi, there!');
    });
    console.log(someVar);
}

someFunc();
otherFunc();
```

예제 2-4

```jsx
hi, there!
hi, there!
```

에제 2-4 실행결과

위 예제의 someFunc와 otherFunc는 사실상 서로 똑같은 코드를 가진 함수들이다. 

# 에러 핸들링

async 함수 내 await 문에서 만약 프로미스가 거절된 경우, 에러가 해당 함수 바깥으로 던져진다. 

```jsx
import { ZeroDivisionError } from "./myerror.js";

async function printDivideResult(a, b) {
    let promise = new Promise((resolve, reject) => {
        if (b === 0) {
            reject(new ZeroDivisionError("0으로 나눌 수 없습니다."));
        }
        resolve(a / b);
    });
    let result = await promise;
    console.log("나눗셈 결과");
    console.log(`${a} / ${b} = ${result}`);
}

printDivideResult(2, 0);
```

예제 3-1

```jsx
reject(new ZeroDivisionError("0으로 나눌 수 없습니다."));
                   ^

ZeroDivisionError: 0으로 나눌 수 없습니다.
    at file:///C:/web/html_css_js_study/promise-async-await/async-await/no-error-handling.js:6:20
    at new Promise (<anonymous>)
    at printDivideResult (file:///C:/web/html_css_js_study/promise-async-await/async-await/no-error-handling.js:4:19)
    at file:///C:/web/html_css_js_study/promise-async-await/async-await/no-error-handling.js:15:1
    at ModuleJob.run (node:internal/modules/esm/module_job:218:25)
    at async ModuleLoader.import (node:internal/modules/esm/loader:329:24)
    at async loadESM (node:internal/process/esm_loader:28:7)
    at async handleMainPromise (node:internal/modules/run_main:113:12)
```

예제 3-1 실행결과

위 예제의 `printDivideResult()` 함수 내부의 실행 함수에서 에러가 발생되었는데, 이를 처리할 코드가 없어 에러가 전역 스코프까지 넘어와 위와 같이 그 에러 메시지가 출력되었다. 

이렇게 async 함수 내에서 발생한 에러를 처리하고자 한다면 다음의 두 가지 방법을 사용하면 된다. 

1. async 함수 내에서 try ~ catch 문을 사용하여 에러를 잡고 처리한다. 
2. async 함수는 Promise 객체를 반환하므로, 해당 함수 호출로 반환된 Promise 객체에 `catch()` 메서드를 부착한다. 

다음은 첫 번째 방법을 사용한 예제이다.

```jsx
import { ZeroDivisionError } from "./myerror.js";

async function printDivideResult(a, b) {
    let promise = new Promise((resolve, reject) => {
        if (b === 0) {
            reject(new ZeroDivisionError("0으로 나눌 수 없습니다."));
        }
        resolve(a / b);
    });
    try {
        let result = await promise;
        console.log("나눗셈 결과");
        console.log(`${a} / ${b} = ${result}`);
    } catch (error) {
        console.log("에러가 발생했습니다.");
    }
}

printDivideResult(2, 0);
```

예제 3-2

```jsx
에러가 발생했습니다.
```

예제 3-2 실행결과

다음은 두 번째 방법을 이용한 예제이다. 

```jsx
import { ZeroDivisionError } from "./myerror.js";

async function printDivideResult(a, b) {
    let promise = new Promise((resolve, reject) => {
        if (b === 0) {
            reject(new ZeroDivisionError("0으로 나눌 수 없습니다."));
        }
        resolve(a / b);
    });
    let result = await promise;
    console.log("나눗셈 결과");
    console.log(`${a} / ${b} = ${result}`);
}

printDivideResult(2, 0).catch(() => console.log("에러가 발생했습니다."));
```

예제 3-3

---

References

[1] [async와 await](https://ko.javascript.info/async-await)

[2] [await - JavaScript \| MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)