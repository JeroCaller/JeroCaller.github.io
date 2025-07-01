---
title: "[JS][비동기] Microtask"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "microtask"]
---

```jsx
new Promise((resolve, reject) => {
    resolve("hi");
}).then(result => console.log(result));

console.log("프로그램 종료.");
```

예제 1-1

```jsx
프로그램 종료.
hi
```

예제 1-1 실행결과

위 예제에서는 Promise 내부의 실행 함수가 바로 실행되었음에도 아래에 있는 `console.log()` 코드보다 느리게 실행되었다. 이러한 원인이 발생한 이유를 알아보자.

# microtask queue

자바스크립트 엔진에서는 then, catch, finally 핸들러들을 따로 관리하는 PromiseJobs 라는 이름의 microtask queue(마이크로테스크 큐) 또는 내부 큐(internal queue)를 가지고 있다. 이 마이크로테스크 큐는 여러 개의 작업들을 하나의 큐에 넣은 다음, 큐의 출구에 가장 가까이 있는 작업들을 꺼내와 이를 실행시키는 구조이다. 즉, 먼저 들어온 작업(then, catch, finally 핸들러)을 먼저 실행하는 선입선출 구조이다. 이러한 마이크로테스크 큐는 외부에 어떤 작업도 남아있지 않을 때 그제서야 마이크로테스크 큐 내부의 작업들이 실행된다. 위 예제에서는 `then()` 메서드에 할당된 핸들러 함수가 마이크로테스크 큐에 들어간 다음, 그 외부 코드인 `console.log()` 코드가 먼저 실행된다. 그 후, 더 이상 외부에서 실행할 코드가 없으므로 그제서야 마이크로테스크 큐 내부에 있던 작업이 큐에서 꺼내져서 실행된 것이다. 

만약 위 예제 1-1에서, "hi" 출력 코드가 먼저 실행되게끔 하고 싶다면 다음과 같이 수정해야 한다.

```jsx
new Promise((resolve, reject) => {
    resolve("hi");
})
.then(result => console.log(result))
.finally(() => console.log("프로그램 종료"));
```

예제 2-1

```jsx
hi
프로그램 종료
```

예제 2-1 실행결과

# 처리되지 않은 거절

이전에 [[JS}[비동기] Promise and error handling](/javascript/js-async-promise-and-error-handling/) 문서에서, `catch()` 메서드를 통해 거절된 프로미스를 잡지 못한 경우, 브라우저 환경의 경우 window 객체에서 ‘unhandledrejection’ 이벤트가 발생하고, 프로미스 에러는 처리되지 않은 상태로 남는다는 것을 배웠다. 

이 처리되지 않은 프로미스 거부는 microtask queue 끝에서 `catch()`를 통해 처리되지 못한 경우 발생하는 것이다. 따라서 이러한 상황을 방지하기 위해 항상 `catch()` 메서드를 추가해줘야 한다. 

그런데 다음의 상황에서는 ‘unhandledrejection’ 이벤트가 벌어질까?

```html
<style>
    #error-display {
        border: 1px solid red;
        width: 300px;
        height: 300px;
        padding: 1em;
    }
</style>
<div id="error-display"></div>
<script>
    const errorDisplay = document.getElementById('error-display');
        
    window.addEventListener('unhandledrejection', e => {
        errorDisplay.innerHTML = `
        <p>에러가 발생한 프로미스: ${e.promise}</p>
        <p>에러 메시지: ${e.reason}</p>`;
    });

    let promise = new Promise((resolve, reject) => {
        setTimeout(() => reject(new Error("예상치 못한 에러!")));
    });
    setTimeout(() => {
        promise.catch(error => console.log("거절된 프로미스로부터 에러를 잡음."))
    }, 1000);
</script>
```

예제 3-1

![예제 3-1 실행결과](/images/2024-05-23/js-async-microtask/1.png)

예제 3-1 실행결과

분명 promise에 `catch()` 메서드를 부착해줬는데도 위와 같이 ‘unhandledrejection’ 이벤트가 발생한 것을 확인할 수 있다. 왜 이런걸까? 

then, catch, finally 등 프로미스 객체에서 호출할 수 있는 메서드들의 핸들러가 microtask queue에 들어간다면, setTimeout, setInterval, io operation(입출력), UI rendering 등의 작업은 macrotask queue라는 곳에 들어간다. 또한, microtask queue 내 작업이 모두 끝나야 macrotask queue 내부에 있는 작업들이 실행되는 구조이다. 따라서 위 예제의 경우, `catch()` 메서드 관련 코드는 setTimeout 함수 내부에 있어 해당 작업은 macrotask queue에 들어가게 된다. microtask queue에는 프로미스 관련 작업이 모두 끝났으므로 비어있게 되고, 이 상태에서 에러를 처리하지 못했으므로 unhandledrejection 이벤트가 발생하는 것이다. 

---

References

[1] [마이크로태스크](https://ko.javascript.info/microtask-queue)

[2] 참고할만한 자료

[이벤트 루프와 매크로태스크, 마이크로태스크](https://ko.javascript.info/event-loop)