---
title: "[JS][비동기] Promise Chaining"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "promise", "promise chaining"]
---

Promise 객체의 then, catch, finally 메서드 모두 그 자체로 Promise 객체를 반환한다. 

```jsx
export function increaseNum(num) {
    let increased = num + 1;
    console.log("증가된 수: " + increased);
    return increased;
}

export function getPromise() {
    return new Promise((resolve) => {
        setTimeout(() => resolve(10), 1000);
    });
}
```

prom-pkg.js

```jsx
import { increaseNum, getPromise } from "./prom-pkg.js";

let promise = getPromise();
let then1 = promise.then((num) => increaseNum(num));
let then2 = then1.then((num) => increaseNum(num));
then2.then(() => {
    console.log("변수 출력 결과들");
    console.log(promise);
    console.log(then1);  // Promise { 11 }
    console.log(then2);  // Promise { 12 }

    console.log(promise === then1);
    console.log(promise === then2);
    console.log(then1 === then2);
});

```

예제 1-1

```jsx
증가된 수: 11
증가된 수: 12
변수 출력 결과들
Promise { 10 }
Promise { 11 }
Promise { 12 }
false
false
false
```

예제 1-1 실행결과

위 예제 코드에서, `then()` 메서드의 반환값을 담은 변수 then1, then2을 출력해보면 그 결과로 `Promise()` 객체가 반환된다는 점을 알 수 있다. 이 점을 활용하면 다음과 같이 프로미스 체이닝 (promise chaining)을 이용할 수 있다. 

```jsx
import {increaseNum, getPromise} from './prom-pkg.js';

// 프로미스 체이닝.
getPromise()
.finally(() => console.log("체이닝 출력 결과"))
.then((num) => increaseNum(num))
.then((num) => increaseNum(num))
.then((num) => increaseNum(num))
.finally(() => console.log("=========="));

// 다음은 프로미스 체이닝이 아니다. 
let promise2 = getPromise();
promise2.then((num) => increaseNum(num));
promise2.then((num) => increaseNum(num));
promise2.then((num) => increaseNum(num));

```

예제 1-2

```jsx
체이닝 출력 결과
증가된 수: 11
증가된 수: 12
증가된 수: 13
==========   
증가된 수: 11
증가된 수: 11
증가된 수: 11
```

예제 1-2 실행결과

위 예제와 같이 `then()` 메서드를 연속으로 호출하는 방식이 프로미스 체이닝이다. 

then 메서드는 프로미스 객체를 반환하지만 다른 then 메서드나 원래의 Promise 객체와는 다른 프로미스 객체를 반환한다. 이는 앞선 예제 1-1을 보면 알 수 있다. promise, then1, then2 변수값들을 비교하는 코드에서 그 결과가 모두 false임을 알 수 있다. 

프로미스 체이닝은 이렇게 계속 달라지는 프로미스 객체를 계속 다음 then 메서드로 전달하는 방식이다. 따라서 위 예제 1-2의 마지막 코드는 프로미스 체이닝이라 할 수 없다. 해당 코드의 경우, 모든 `then()` 메서드가 하나의 동일한 promise2 변수를 참조하기 때문이다. 

프로미스 체이닝의 원리는 다음과 같다. 먼저 처음 프로미스 내부의 제작 코드 실행 시 `resolve(value)` 부분이 호출되면 해당 프로미스 객체의 result 프로퍼티의 값이 value로 할당되며, 이 정보가 다음 then 메서드로 전달된다. 이는 then 메서드 내에 정의된 함수 선언부의 매개변수로 전달된다. 해당 then 메서드 내부의 함수 선언부에서 return 문을 통해 어떤 값을 반환하면 다음 then 메서드에선 이를 result 프로퍼티의 값으로 받아 사용할 수 있다. 이러한 과정이 계속 반복되는 것이다. (참고로 위 예제 1-2에서의 then 메서드 내부에 정의된 화살표 함수는 중괄호가 생략된 한 줄의 코드가 있는데, 여기에는 return 키워드가 생략된 것이지 없는 게 아니다) 

이러한 프로미스 체이닝을 이용하면, 비동기 작업 중 동기적으로(순차적으로) 처리해야할 작업이 둘 이상일 때 이를 이용하면 되겠다. 

# 프로미스 반환

`then(handler)` 메서드 내부에 선언된 핸들러 함수가 프로미스를 반환하는 경우도 있다. 이 경우, 다음 then 메서드는 이전 then 메서드 내 핸들러 내부의 프로미스가 처리될 때까지 대기하다가 완료되면 그 결과를 이어받는다. 

```jsx
function getPromiseWithNum(num) {
    return new Promise((resolve) => {
        setTimeout(() => resolve(num), 1000);
    });
}

getPromiseWithNum(10)
.then((data) => {
    console.log(data);
    return getPromiseWithNum(11);
})
.then((data) => {
    console.log(data);
    return getPromiseWithNum(12);
})
.then((data) => {
    console.log(data);
    return getPromiseWithNum(13);
});

```

예제 2-1

```jsx
10
11
12
```

예제 2-1 실행결과

위 예제 2-1은 then 메서드 내 핸들러 자체가 또 다른 프로미스 객체를 반환한다는 사실을 제외하면 앞서 살펴본 예제 1-1, 1-2와 다를 바가 없다. 다만, 위 예제 2-1에서는 핸들러 내에서 1초 뒤에 결과를 반환하는 프로미스 객체 자체를 반환하는 구조이기에, 결과값이 각각 1초 간격으로 출력된다. 

# Thenable

프로미스 체이닝을 보면, 프로미스 객체의 `then()` 메서드를 호출하고, 이 then 메서드 자체도 또 다른 프로미스를 객체를 반환하는 방식이다. 그래서 하나의 then 메서드에 부착된 핸들러 함수에서 프로미스 객체 대신 `then()` 메서드를 가진 어떤 객체를 반환해도 문제없이 프로미스 체이닝을 이용할 수 있다. 

다음은 `then()` 메서드를 가지는 클래스를 정의하고, 이 클래스의 객체를 then 메서드에 부착된 핸들러 함수에서 반환하도록 하는 예제이다. 

```jsx
import { getPromise } from "./prom-pkg.js";

class MyThenable {
    constructor(num) {
        this.num = num;
    }

    then(resolve, reject) {
        setTimeout(() => resolve(this.num), 1000);
    }
}

getPromise()
.then(result => {
    console.log(result);
    return new MyThenable(result + 1);
})
.then((result) => console.log(result));

```

예제 3-1

```jsx
10
11
```

예제 3-1 실행결과

위 예제에서, 프로미스 객체가 아닌 그저 then 메서드를 가진 사용자 정의 객체를 반환하는 구조임에도 에러없이 잘 작동하는 것을 볼 수 있다. 이렇게 프로미스 객체가 아님에도 `then()` 메서드를 가져 프로미스 객체의 `then()` 메서드와 동일하게 사용할 수 있는 객체를 thenable 객체라 부른다. 

단, 어떤 객체가 thenable이 되려면 위 예제 3-1에서와 같이, then 메서드 정의 시 1) 그 매개변수로 resolve, reject를 받으며, 2) then 메서드 내부에서 resolve, reject 둘 중 하나는 호출하는 코드가 있어야 한다. 이런 점에서는 프로미스 객체의 실행 함수와 동일하다. 

---

References

[1] [프라미스 체이닝](https://ko.javascript.info/promise-chaining)