---
title: "[JS][비동기] Promise"
category: "javascript"
tag: ["web", "js", "javascript", "비동기", "async", "promise"]
---

# Promise

이전 [[JS][비동기] Callback](/javascript/js-async-callback/) 페이지에서 콜백 함수와 이 콜백을 비동기 프로그래밍에서 어떻게 사용되는지에 대해 간략히 살펴보았다. 이 문서에서는 비동기 프로그래밍에서 콜백 대신 사용할 수 있는 프로미스(promise)에 대해 살펴보겠다. 

비동기 상황에서 작업 시간이 걸리는 코드에 대해서 다음으로 분류할 수 있다. 

- 제작 코드(producing code) : 시간이 걸리는 작업 코드를 말함. 타 모듈들을 불러오거나 서버 또는 DB에 보낸 요청에 대한 응답을 받아오는 데 시간이 걸릴 때 해당 작업을 수행하는 코드가 그 예시이다.
- 소비 코드(consuming code) : 제작 코드가 수행하는 작업이 끝나 결과값을 반환받길 기다리다가 받으면 이를 소비(사용)하는 코드. 다른 자바스크립트를 불러오는 작업 수행 후 해당 스크립트 내 함수나 클래스 등의 자원을 사용하는 것이 그 예시가 되겠다.

프로미스는 제작 코드와 소비 코드를 분리시켜 두 영역을 명확하게 보여주고, 이 둘을 연결해주는 역할을 하는 객체라고 할 수 있다. 

# promise 객체 생성 : 제작 코드

promise 객체 생성 문법은 다음의 형태를 띈다.

```jsx
let promise = new Promise((resolve, reject) => {
    // 제작 코드. 
});
```

Promise 클래스를 이용하여 해당 객체를 생성하며, 생성자의 인자로 함수 선언부를 넘긴다. 이 때 인자로 넘겨지는 함수를 executor(실행 함수)라 부른다. 해당 함수는 Promise 객체가 생성될 때 자동으로 실행된다. 

이 executor의 인수로는 첫 번째 인자로 resolve, 두 번째 인자로 reject를 받는다. 이 둘 모두 자동적으로 생성되는 콜백이다. 제작 코드 작성 시에는 이 두 콜백들을 신경 쓸 필요가 없다. 그러나 executor 실행 완료 후 나올 결과값에 대해서는 이 두 콜백 중 적어도 하나를 호출해야 한다. 

- `resolve(value)` : 작업이 성공적으로 완료된 경우 결과를 담은 value 인자를 받고 호출됨.
- `reject(error)` : 에러 발생 시 에러 객체 error를 인자로 받아 호출됨. 사실 error 인자로 아무거나 전달해도 되지만 Error 또는 이를 상속받는 객체 사용이 권고된다.

이 두 콜백이 executor 내에서 모두 사용된 경우, executor 내 작업이 성공하면 `resolve()`가, 에러가 발생하면 `reject()` 함수가 실행되는 방식이다. 

`resolve()`와 `reject()` 모두 인자를 하나만 받거나 아예 생략할 수도 있다. 두 번째 인자부터는 전달하더라도 모두 무시된다고 한다. 

작업이 성공적으로 완료된 프로미스를 fulfilled promise (이행된 약속)이라 부른다. 그리고 해결(resolved)이 되었건 거부(rejected) 되었건 간에 어찌되었든 실행된 프로미스를 “처리된(settled) 프로미스”라 표현한다. 반면, 아직 대기 중인 프로미스는 ‘대기(pending) 상태의 프로미스’라 표현한다. 

다음은 앞서 설명한 promise 객체 생성과 관련된 예제이다.

```jsx
// promise 선언. 제작 코드(producing code)
let promise = new Promise((resolve, reject) => {
    let isSuccess = false; // 원하는 결과값에 따라 true/false 중 하나 지정.
    if (isSuccess) {
        setTimeout(() => resolve("작업이 성공적으로 완료되었습니다."), 1000);
    } else {
        setTimeout(() => reject("작업 도중 에러가 발생했습니다."), 1000);
    }
});
```

예제 1-1

resolve, reject 호출 시 마치 함수의 return문과 같은 효과를 지닌다. 즉, 코드 상에서 resolve 또는 reject가 먼저 호출된 경우, 그 뒤에 존재하는 또 다른 resolve 또는 reject 함수의 호출은 무시된다. 

```jsx
let promise new Promise((resolve, reject) => {
    resolve("수행됨");
    
    // 아래의 resolve, reject 호출 코드는 모두 무시된다. 
    // 마치 함수 선언부 내의 return문 뒤의 코드들은 모두 무시되는 것처럼 말이다.
    reject(new Error("예기치 못한 예외"); 
    resolve("대충 수행됨");
});
```

예제 1-2

# then, catch, finally 소비 메서드를 이용한 소비 코드

시간이 걸리는 비동기적 작업을 `new Promise()` 객체를 통해 그 내부에 제작 코드로 작성하였다면, 이 제작 코드가 실행된 후 그 결과를 사용하는 소비 코드도 있어야 할 것이다. 이 promise에 대한 소비 코드의 역할을 하는 메서드로는 then, catch, finally가 있다. 

## then(success, error)

```jsx
promise.then(
    result => {// 결과(result)값을 다룰 함수 선언},
    error => {// 에러(error)를 다룰 함수 선언}
};
```

`.then` 메서드는 그 첫 번째 인자로, 프로미스가 성공적으로 실행되었을 때 제작 코드 내에서 호출될 `resolve(value)`의 value를 결과값으로 받아 이를 처리할 함수 선언부를 전달하면 된다. 두 번째 인자로는 프로미스가 거부되었을 때 `reject(error)` 의 error를 받아 이를 처리할 함수 선언부를 전달하면 된다. 프로미스가 성공하면 첫 번째 인자로 전달된 함수가, 실패하면 두 번째 인자로 전달된 함수가 실행되는 구조이다. 

```jsx
// promise 선언. 제작 코드(producing code)
let promise = new Promise((resolve, reject) => {
    let isSuccess = false;  // 원하는 결과값에 따라 true/false 중 하나 지정.
    if (isSuccess) {
        setTimeout(() => resolve("작업이 성공적으로 완료되었습니다."), 1000);
    } else {
        setTimeout(() => reject("작업 도중 에러가 발생했습니다."), 1000);
    }
});

// promise 사용. 소비 코드(consuming code)
// then(promise 실행 결과가 성공했을 때 호출할 콜백, 실패(에러 발생)했을 때 호출할 콜백)
promise.then(
    result => {
        console.log("====== Phase 1 ======");
        console.log(result);
    },
    error => {
        console.log("====== Phase 1 ======");
        console.log(error);
    }
);
```

예제 2-1

```jsx
====== Phase 1 ======
작업 도중 에러가 발생했습니다.
```

예제 2-1 실행결과1. `isSuccess = false` 로 했을 때의 출력결과

```jsx
====== Phase 1 ======
작업이 성공적으로 완료되었습니다.
```

예제 2-1 실행결과2. `isSuccess = true` 로 했을 때의 출력결과

만약, 프로미스가 성공한 경우만 다루고자 한다면 `then()` 메서드에 두 번째 인수를 생략하면 된다. 반대로 프로미스가 실패한 경우만 다루고자 한다면 `then(null, function)`과 같이 첫 번째 인자로 null를 전달하거나, 바로 다음에 나올 catch 메서드를 사용하면 된다. 

하나의 프로미스에 여러 개의 `then()` 메서드를 달아도 된다. 이 경우, 하나의 프로미스의 결과로부터 이를 다양한 방식으로 처리하고자 할 때 유용할 것이다. 

```jsx
promise.then(// 프로미스 결과를 처리할 핸들러1);
promise.then(// 프로미스 결과를 처리할 핸들러2);
```

## catch(error)

`catch()` 메서드는 프로미스가 거절될 경우(실행 함수에서 에러가 발생한 경우)만을 다룰 때 사용하는 메서드이다. then과 마찬가지로 그 인자로 에러 발생 시 처리할 함수 선언부를 전달하면 된다. 다음은 그 사용 예시이다.

```jsx
// catch(errorHandlingFunc) == then(null, errorHandlingFunc)
promise.catch(error => {
    console.log("====== Phase 2 ======");
    console.log(error);
});
```

예제 3-1

```jsx
====== Phase 2 ======
작업 도중 에러가 발생했습니다.
```

예제 3-1 실행결과. 프로미스가 거절된 상태일 때 위와 같은 출력결과가 생김.

## finally()

`finally()` 메서드는 프로미스가 이행되었건 거부되었건 상관없이 그 인자로 전달된 함수를 무조건 실행하는 메서드이다. try-catch-finally의 finally와 똑같은 성질을 가진다고 볼 수 있다. 

```jsx
// finally(function) : 프로미스 실행 결과가 성공이든 실패건 무조건 실행됨.
promise
    .finally(() => console.log("====== Phase 3 ======"))
    .finally(() => console.log("프로미스가 실행되어 결과를 반환했습니다."))
    .then(
        result => console.log(result),
        error => console.log(error)
    )
    .finally(() => console.log("프로미스 사용이 완료되었습니다."));
```

예제 4-1

```jsx
====== Phase 3 ======
프로미스가 실행되어 결과를 반환했습니다.
작업 도중 에러가 발생했습니다.
프로미스 사용이 완료되었습니다.
```

예제 4-1 실행결과. `isSuccess = false` 로 지정했을 때의 출력 결과.

위 예제를 보면 알 수 있듯, `finally()` 메서드는 그 뒤에 있는 then, catch, `finally()` 메서드로 프로미스의 결과를 전달한다. 그렇기에 `finally()`들 사이에 낀 then() 메서드도 정상적으로 실행되는 것이다. finally는 어떻게 보면 then, catch와는 달리 프로미스의 결과를 처리하는 종착점이 아닌 중간 연결 통로와도 같다고 볼 수 있다. 

then, catch, finally 메서드 모두 프로미스가 처리될 때까지 기다리다가 처리되면 해당 메서드들이 즉시 실행되는 구조이다. 

# 프로미스 예제

앞서 살펴본 프로미스 객체 생성과 사용 방법을 이용하여 이전의 [[JS][비동기] Callback](/javascript/js-async-callback/) 문서의 “중첩 콜백” 부분에서 사용된 예제를 콜백 대신 프로미스로 대체하여 재구성해보겠다. 단, 여기서는 중첩 콜백에 해당하는 부분을 구현하진 않았다. 다음은 해당 예제 코드들이다. 

```jsx
import {database} from './user-info.js';

function getData(id) {
    return new Promise((resolve, reject) => {
        let userInfo;

        try {
            setTimeout(() => {
                userInfo = database[id];
                resolve(userInfo);
            }, 1000);
        } catch (e) {
            reject(e);
        }
    });
}

getData(1).then(
    result => {
        console.log("===== 사용자 정보 =====");
        console.log(`이름 : ${result.name}`);
        console.log(`나이 : ${result.age}`);
        console.log(`직업 : ${result.job}`);
    },
    error => {
        console.log("에러 : 에러가 발생했습니다. 다음은 에러 메시지입니다.");
        console.log(error);
    }
);

```

예제 5-1

```jsx
===== 사용자 정보 =====
이름 : 파이썬
나이 : 25    
직업 : 데이터 분석가
```

예제 5-1 실행결과

위 예제에서 import하는 user-info.js 내부 코드는 [[JS][비동기] Callback](/javascript/js-async-callback/) 페이지의 “중첩 콜백” 챕터에 나오는 user-info.js와 동일하다. 

# 프로미스 VS 콜백

방금 살펴본 예제 5-1을 콜백을 사용하여 재구성하면 아마 다음과 같을 것이다.

```jsx
import {database} from './user-info.js';

function getData(id, callback) {
    let userInfo;
    
    setTimeout(() => {
        userInfo = database[id];
        callback(userInfo);
    }, 1000);
    
    return userInfo;
}

getData(1, dataObj => {
     console.log("===== 사용자 정보 =====");
     console.log(`이름 : ${dataObj.name}`);
     console.log(`나이 : ${dataObj.age}`);
     console.log(`직업 : ${dataObj.job}`);
});
```

예제 6-1

위 예제를 보면, 제작 코드에 해당하는 getData 함수 선언부 내 코드 내부에 소비 코드에 해당하는 `callback()` 함수를 호출하고 있다. 즉, 제작 코드 내에 소비 코드가 있는 셈이다. 물론, `getData()` 함수를 호출할 때 그 인자로 어떤 일을 어떻게 처리할 것인지에 대한 콜백 함수 선언부가 들어가는 것이지 getData 함수 선언부의 매개변수 또는 함수 내부에 그 콜백 함수 선언부를 지정하는 건 아니지만, 그럼에도 getData 함수 선언부 내에 콜백 함수 호출부가 들어가 있다는 점에서 제작 코드와 소비 코드가 완전히 분리되어 있다는 느낌이 들지 않는다. 반면, 프로미스를 이용한 에제 5-1을 다시 보면, 제작 코드는 오로지 `new Promise()` 객체 내 실행 함수 내부에만 존재하며, 소비 코드는 그 외부에서 `then, catch, finally()`메서드를 사용하는 방식이기에 제작 코드와 소비 코드가 완전히 분리되어 있다고 볼 수 있다. 

```jsx
// 콜백 방식
// 작업 함수 선언부
function someFunc(callback) {
    // 작업 코드
    callback();
}

// 작업 함수 호출부
someFunc(() => {
    // 콜백 함수 선언부.
});

// 프로미스 방식

// 작업 함수 선언부
function otherFunc() {
    return new Promise((resolve, reject) => {
        // 작업 코드.
        resolve(value);
        reject(error);  
    });
}

// 작업 함수 호출부
otherFunc().then((result) => {// 프로미스 성공 시 처리할 코드});
```

예제 6-2

위 예제를 보면, 콜백 방식에서는 함수 호출 시 그 매개변수에 콜백 함수를 전달해야 한다. 그래서 해당 함수와 콜백 함수가 서로 결합되어 있다는 느낌을 준다. 반면, 프로미스 방식에서는 함수 호출한 뒤, 그 뒤에 then을 붙여 그 안에 핸들러 함수를 넣는 방식으로, 호출할 함수와 핸들러 함수를 분리할 수 있다. 프로미스 방식은 일단 함수를 호출한 다음, 그 결과값을 어떻게 처리할 지는 그 후에 생각하자, 라는 방식이라서, 작업의 흐름을 더 쉽게 읽을 수 있다. 

또한, 콜백은 한 번에 하나만 함수에 인자로 전달할 수 있는 반면, 프로미스는 then을 여러 번 호출할 수 있기에, 프로미스는 콜백 지옥을 방지하여 가독성을 확보할 수 있다. 다음은 이전 문서인 [[JS][비동기] Callback](/javascript/js-async-callback/) 의 “중첩 콜백” 챕터에 있던 콜백 지옥 부분을 프로미스로 변경한 예제이다.

```jsx
import {database, printData} from './user-info.js';

function getData(id) {
    return new Promise((resolve, reject) => {
        let userInfo;

        try {
            setTimeout(() => {
                userInfo = database[id];
                resolve(userInfo);
            }, 1000);
        } catch (e) {
            reject(e);
        }
    });
}

getData(0)
    .then(result => {
        printData(result);
        return getData(1);
    })
    .then(result => {
        printData(result);
        return getData(2);
    })
    .then(result => {
        printData(result);
        return getData(3);
    })
    .then(result => printData(result))
    .catch(error => {
        console.log("에러 발생");
        console.log(error);
    });

```

예제 6-3

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

예제 6-3 실행결과

위 예제와 같이 then 등의 메서드를 하나의 프로미스에 여러 개 부착하는 것을 프로미스 체이닝(promise chaining)이라고 한다. then, catch, finally 메서드 자체에서도 독립된 프로미스를 반환하기에 이를 이용하여 연속적으로 비동기 작업을 처리한 것이다. 콜백 지옥은 깊이가 깊어질 수록 화살표 모양을 띠는 반면, 프로미스 체이닝은 그렇지 않기에 이 점에서도 가독성을 확보할 수 있다. 

콜백은 개발자가 제작 코드 내에 try ~ catch문을 따로 쓰지 않는 이상 기본적으로는 에러 핸들링 기능이 없는 반면, 프로미스는 `catch()` 메서드를 통해 기본적으로 에러를 처리할 수 있다. 

# Promise 객체 프로퍼티

`new Promise()`를 통해 생성된 객체는 state와 result 프로퍼티를 가진다. 각각의 프로퍼티들의 값은 프로미스 실행 전, 그리고 실행 후 성공적으로 완료되었는지 에러가 발생했는지에 따라 달라진다. 다음은 이를 구체적으로 정리한 표이다.

| property / case | 프로미스 실행 전 | resolve(value) 호출 시 | reject(error) |
| --- | --- | --- | --- |
| state | “pending” | “fulfilled” | “rejected” |
| result | undefined | value | error |

이 두 프로퍼티들은 직접 접근할 수는 없고, then, catch, finally 메서드 내부로 전달되는 핸들러 함수의 인수로서 접근 가능하다. 

---

References

[1] [프라미스](https://ko.javascript.info/promise-basics)

[2] [🌐 자바스크립트의 핵심 '비동기' 완벽 이해 ❗](https://inpa.tistory.com/entry/🌐-js-async#비동기와_콜백_함수)

[3] callback vs promise (1)

[Callback vs Promise vs Async/Await in JavaScri](https://medium.com/@lelianto.eko/callback-vs-promise-vs-async-await-in-javascri-f29a63d57f72)

[4] callback vs promise (2)

[Promise vs Callback in JavaScript - GeeksforGeeks](https://www.geeksforgeeks.org/promise-vs-callback-in-javascript/)

[5] callback vs promise (3)

[Understanding Callbacks vs. Promises in JavaScript](https://www.linkedin.com/pulse/understanding-callbacks-vs-promises-javascript-zeeshan-tanveer)