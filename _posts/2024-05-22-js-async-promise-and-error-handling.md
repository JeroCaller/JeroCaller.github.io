---
title: "[JS][비동기] Promise and Error Handling"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "promise", "error", "error handling"]
---

거절(reject)된 프로미스는 가장 가까운 `catch()` 메서드로 넘어간다. 프로미스 체이닝에서 `catch()` 메서드도 사용하는 경우, 해당 메서드의 위까지의 에러를 모두 잡아준다. 

```jsx
new Promise((resolve, reject) => {
    resolve('good');  // 에러 발생하지 않음.
}).catch(err => {
    console.log("에러 영역1: 에러 발생");
}).then(result => {
    throw new Error("그냥 에러 내봄");
}).catch(err => {
    console.log("에러 영역2: 에러 발생");
    console.log(err.message);
});
```

예제 1-1

```jsx
에러 영역2: 에러 발생
그냥 에러 내봄
```

예제 1-1 실행결과

위 예제에서, 맨 처음의 Promise 객체 내부의 실행함수에서 에러가 발생하지 않았다. 대신, 첫 번째 `catch()`와 두 번째 `catch()` 사이의 `then()` 메서드 내 핸들러에서 에러가 발생했다. 첫 번째 `catch()` 메서드는 그 위인 Promise 객체에서의 에러만을 잡지, 그 뒤에 있는 then 메서드 내부에서의 에러를 잡진 못한다. 그래서 첫 번째 `catch()` 메서드는 무시된다. 두 번째 `catch()` 메서드는 `then()` 메서드 다음이므로, 해당 then 메서드 내부에서 에러 발생 시 이를 잡아준다. 그래서 결국 위 예제에서는 then에서 발생한 에러가 그 다음 catch 메서드에 잡혀서 출력된 것이다. 

프로미스 체이닝에서, `catch()` 메서드는 그 위의 Promise 객체와 여러 개의 `then()` 메서드 내부에서 일어나는 에러들을 잡아준다. 그래서 여러 개의 then으로 구성된 프로미스 체이닝에서 에러를 한꺼번에 해결하기 위해 `catch()` 메서드를 맨 마지막에 작성하는 경우도 있다. 

# 암시적 (implicit) try ~ catch

프로미스의 실행 함수 내부에서 예상되는 에러에 대해 reject(error) 코드가 없더라도, 해당 함수 내에서 에러가 발생하면 이를 암묵적으로 잡아준다. 즉, 실햄 함수 내에는 암시적 try ~ catch문이 있어 여기서 예외를 잡는 역할을 해준다. 

```jsx
// 명시적 try ~ catch가 없는 경우.
new Promise((resolve, reject) => {
    class EmtpyClass {}
    let empty = new EmtpyClass();
    empty.good();  // 존재하지 않는 메서드 호출 => TypeError
}).catch(err => console.log("에러가 발생했습니다."));
```

예제 2-1

```jsx
에러가 발생했습니다.
```

예제 2-1 출력결과

위 예제에서는 어떤 객체의 존재하지 않는 메서드를 호출하여 에러를 발생시키고 있다. 분명 실행 함수 내부에는 `reject(error)` 호출문도, 일반적인 try ~ catch문도 없다. 그럼에도 실행 함수 내부에서 발생한 에러가 `catch()` 메서드에서 잡힌 것을 확인할 수 있다. 즉, 실행 함수 내에 에러가 발생하면 해당 프로미스는 거절된 프로미스가 되는 것이다. 위 예제는 사실상 다음의 코드와도 같다.

```jsx
// 명시적으로 try ~ catch 구문을 넣은 경우.
new Promise((resolve, reject) => {
    class EmtpyClass {}
    let empty = new EmtpyClass();
    try {
        empty.good();
    } catch(e) {
        reject(e);
    }
}).catch(err => console.log("에러가 발생했습니다."));

```

예제 2-2

이러한 암묵적 try ~ catch는 then 메서드 내 핸들러 함수에도 존재한다. 만약 then 메서드 내 핸들러 함수 내에서 에러가 발생하면 해당 then은 거절된 프로미스를 반환하게 되어, 그 다음 `catch()` 메서드에서 해당 에러를 잡을 수 있게 된다. 

```jsx
new Promise((resolve, reject) => {
    resolve('hi');
}).then((result) => {
    thereIsNoSuchThisFunc();  // 존재하지 않는 함수 호출 => ReferenceError
}).catch(err => console.log("에러가 발생했습니다."));

```

예제 2-3

```jsx
에러가 발생했습니다.
```

예제 2-3 실행결과

단, 암시적 try ~ catch에 대해 주의할 점이 있다. 만약 실행 함수 내부에 `setTimeout()` 메서드가 있고, 이 메서드의 인자로 전달된 함수 내부에서 에러가 발생한 경우, 이를 그 뒤의 `catch()` 메서드가 잡아주지 못한다. 

```jsx
new Promise((resolve, reject) => {
    setTimeout(() => {
        throw new Error("그냥 에러 발생시켜봄");
    }, 1000);
}).catch(err => console.log("catch() 메서드에서 에러를 감지하였습니다."));

```

예제 2-4

```
file:///C:/web/html_css_js_study/promise-async-await/promise-error-handling/implicit-catch-2.js:3
        throw new Error("그냥 에러 발생시켜봄");
        ^

Error: 그냥 에러 발생시켜봄
    at Timeout._onTimeout (file:///C:/web/html_css_js_study/promise-async-await/promise-error-handling/implicit-catch-2.js:3:15)
    at listOnTimeout (node:internal/timers:573:17)
    at process.processTimers (node:internal/timers:514:7)
```

예제 2-4 출력결과

`setTimout()` 메서드의 인자로 전달된 함수는 실행함수가 실행된 후 1초 뒤에 실행된다. 즉, 실행 함수가 살행되는 동안에 발생한 에러가 아니라 그 이후에 발생한 에러이기 때문에 그 뒤의 `catch()` 메서드에서 해당 에러를 잡을 수 없는 것이다. 

위와 같은 상황이 발생할 때, `setTimeout()` 메서드에 연결된 함수 내부에서 발생한 에러를 처리하고자 한다면 다음과 같이 명시적으로 try ~ catch 문을 쓸 수 밖에 없다. 

```jsx
new Promise((resolve, reject) => {
    setTimeout(() => {
        try{
            throw new Error("여기서 예기치 못한 에러가 발생했다고 가정.");
        } catch (e) {
            console.log("try ~ catch 문에서 에러를 감지함.");
        }
    }, 1000);
}).catch(err => console.log("catch() 메서드에서 에러를 감지하였습니다."));
```

예제 2-5

```jsx
try ~ catch 문에서 에러를 감지함.
```

예제 2-5 실행결과

위 예제에서 발생한 에러는 `catch()` 메서드에 닿지 않는다. 따라서 실전에서는 뒤의 `catch()` 메서드를 붙일 필요가 없게 된다. 

# 에러 다시 던지기

일반적인 try ~ catch 문에서, 특정 catch문에서 잡은 에러가 예측하지 못한 에러이거나 해당 catch 문에서는 처리할 수 없는 에러가 발생하면, 이 에러를 다시 던져 가까운 catch 문에서 이 에러를 처리하도록 할 수 있었다. 프로미스에서도 마찬가지로 특정 `catch()` 메서드로 넘어온 에러가 해당 영역에서는 해결할 수 없어 다른 곳으로 에러를 다시 던질 수 있다. 

다음은 이를 보여주는 예제이다. 먼저, 임의대로 에러 클래스를 다음과 같이 정의해보았다.

```jsx
export class ZeroDivisionError extends Error {
    constructor(message) {
        super(message);
        this.name = "ZeroDivisionError";
    }
}

export class NotNumberError extends Error {
    constructor(message) {
        super(message);
        this.name = "NotNumberError";
    }
}

```

예제 3-1. myerror.js

그리고, 두 수를 입력 받아 이 둘의 나눗셈 값을 계산하는 프로미스를 작성하고, 다음과 같이 작성하였다.

```jsx
import { ZeroDivisionError, NotNumberError } from "./myerror.js";

function getDivide(a, b) {
    return new Promise((resolve, reject) => {
        if (b === 0) {
            reject(new ZeroDivisionError("0으로 나누는 연산은 불가합니다."));
        } else if (typeof a != 'number' || typeof b != 'number') {
            reject(new NotNumberError("숫자가 아닌 값이 입력되었습니다."));
        } else {
            resolve(a / b);
        }
    });
}

getDivide('a', 1)
.catch(error => {
    if (error instanceof ZeroDivisionError) {
        console.log("0으로 나누는 연산 시도 감지.");
        console.log("두 번째 인자는 0이 아닌 다른 수를 입력해주세요.");
    } else {
        console.log("ZeroDivisionError가 아닌 다른 예외 발생");
        throw error;
    }
}).then(result => console.log("나눗셈 결과: " + result))
.catch(error => {
    console.log("처리되지 않은 에러 발생!");
    console.log("다음은 해당 에러에 대한 메세지입니다.");
    console.log(error.name + ": " + error.message);
    console.log("=============================");
});

```

에제 3-2

```
ZeroDivisionError가 아닌 다른 예외 발생
처리되지 않은 에러 발생!
다음은 해당 에러에 대한 메세지입니다.
NotNumberError: 숫자가 아닌 값이 입력되었습니다.
=============================
```

예제 3-2 출력 결과

위 예제의 프로미스 내 실행 함수에서, 입력된 두 수 중 두 번째 수가 0일 경우, 어떤 수를 0으로 나눌 수 없으므로 ZeroDivisionError 예외를 발생시키도록 하고 있다. 그리고 두 수 중 하나라도 숫자가 아닌 문자 등의 자료형일 경우 NotNumberError 예외를 일으키도록 되어 있다. 

해당 함수에 대한 프로미스 체이닝은 위 예제에서 볼 수 있듯, 첫 번째 `catch()` 메서드 내에서 ZeroDivisionError 예외를 처리하도록 되어 있다. 그런데 만약 다른 예외가 발생한 것이라면 이를 `throw error;` 문을 통해 그 뒤에 있는 가장 가까운 `catch()` 메서드가 이를 잡아 처리하도록 에러를 다시 던지도록 되어 있다. ZeroDivisionError가 아닌 다른 예외가 발생한 경우, 해당 `catch()` 문에서 해당 에러가 다시 던져진 후, 그 다음의 `then()` 메서드는 무시되고, 맨 마지막의 `catch()` 메서드에 도달하여 해당 메서드에서 다시 던져진 에러를 받아 처리하게 된다. 이처럼 프로미스 체이닝에서도 에러 다시 던지기가 가능하다. 

# 처리되지 않은 거절된 프로미스

만약 프로미스 내에서 발생한 에러를 `catch()` 메서드가 없어 처리하지 못하는 경우, 에러 메시지를 출력 시키며 프로그램이 중단된다. 

브라우저 환경에서는 이렇게 `catch()` 메서드가 없어 처리되지 못한 에러에 대해 ‘unhandledrejection’ 이벤트를 발생시키고, 이 이벤트에 핸들러를 부착해 해당 핸들러에서 처리할 수 있다. 

```jsx
window.addEventListner('unhandledrejection', e => {
    // unhandledrejection 이벤트에는 다음의 두 프로퍼티가 존재한다. 
    console.log(e.promise);  // 에러가 발생한 프로미스
    console.log(e.reason);  // 처리되지 않은 에러 객체.
});
```

단, 처리되지 않은 에러는 전역 에러이므로, 브라우저 환경에서는 document가 아닌 window 객체에서 이를 처리해야한다. 

다음은 거절된 프로미스의 처리되지 않은 에러를 감지하고 이를 웹 문서 상으로 출력하는 예제이다.

```html
<script>
    window.addEventListener('unhandledrejection', e => {
        const newDiv = document.createElement('div');
        newDiv.innerHTML = `<p>${e.promise}</p>
        <p>${e.reason}</p>`;
        document.body.append(newDiv);
    });
    new Promise(() => {
        throw new Error("갑작스러운 에러");
    });  // 에러를 처리할 catch()문이 없음.
</script>
```

예제 4-1

![예제 4-1 실행결과](/images/2024-05-22/js-async-promise-and-error-handling/1.png)

예제 4-1 실행결과

---

References

[1] [프라미스와 에러 핸들링](https://ko.javascript.info/promise-error-handling)