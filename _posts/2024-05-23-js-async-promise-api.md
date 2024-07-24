---
title: "[JS][비동기] Promise API"
category: "javascript"
tag: ["web", "js", "javascript", "async", "비동기", "promise", "api"]
---

Promise 클래스에 여러 가지 정적 메서드가 있는데, 그 중 몇 가지를 살펴보겠다. 

# Promise.all

`Promise.all()` 메서드에는 모두 프로미스 객체가 담긴 배열 또는 iterable 객체를 전달한다(여기선 간편하게 배열로 가정하겠다). 그러면 배열 내 모든 프로미스가 처리될 때까지 기다리다가 모두 완료가 되면, 각각의 프로미스 결과값을 담은 배열을 result 프로퍼티의 값으로 삼는 새로운 Promise 객체가 반환된다. 

다음은 어떤 사이트에서 여러 개의 파일들을 동시에 다운로드받는다는 상황을 가정한 예제이다. 

```jsx
let files = [
    ['안내서.txt', 1],
    ['코딩 잘하는 법.pdf', 3],
    ['로드맵.jpg', 2]
];

function download(filePath, delaytime) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(filePath), delaytime * 1000);
    });
}

let requests = files.map(elements => download(elements[0], elements[1]));

Promise.all(requests)
    .then(result => result.forEach(element => {
        console.log("다운로드 완료: " + element);
    }));

```

예제 1-1

```jsx
다운로드 완료: 안내서.txt
다운로드 완료: 코딩 잘하는 법.pdf
다운로드 완료: 로드맵.jpg
```

예제 1-1 실행결과

위 예제에서, `Promise.all()` 다음에 붙는 then 메서드에서는 프로미스 객체의 프로퍼티인 result에는 단일 값이 아닌 여러 값들을 포함하는 배열임을 참고한다. 그래서 위 예제에서 forEach 메서드를 통해 배열 내 모든 요소들을 순회하여 출력하고 있는 것이다. 

이를 이용하면, 동시에 여러 개의 비동기 작업을 수행해야하고, 이 모든 결과값을 받아야 하는 경우에 사용하면 유용할 것이다. 위 예제처럼 여러 개의 파일들을 동시에 다운로드 받아야 하거나 동시에 여러 사이트에 요청을 하는 등의 작업 시 유용할 것이다.

`Promise.all()` 메서드의 인자로 전달된 Promise 객체 배열에서, 단 하나의 Promise라도 거절된다면 `Promise.all()` 메서드가 반환하는 프로미스도 거절된 상태가 된다. 다음은 다운로드 시간이 3초 이상이 걸리면 죽는 병(???)에 걸려 에러를 발생시키는 경우가 있을 때의 예제이다.

```jsx
let files = [
    ['안내서.txt', 1],
    ['코딩 잘하는 법.pdf', 3],
    ['로드맵.jpg', 2]
];

function download(filePath, delaytime) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (delaytime >= 3) {
                reject(new Error("3초 이상은 못 기다린다!"));
            }
            resolve(filePath);
        }, delaytime * 1000);
    });
}

let requests = files.map(elements => download(elements[0], elements[1]));

Promise.all(requests)
    .then(result => result.forEach(element => {
        console.log("다운로드 완료: " + element);
    }))
    .catch(error => {
        console.log("에러가 발생했습니다. 다음은 에러 메시지입니다.");
        console.log(error);
        console.log("에러 메시지 끝.");
    });

```

예제 1-2

```
에러가 발생했습니다. 다음은 에러 메시지입니다.
Error: 3초 이상은 못 기다린다!
    at Timeout._onTimeout (file:///C:/web/html_css_js_study/promise-async-await/promise-api/promise-all-reject.js:11:24)
    at listOnTimeout (node:internal/timers:573:17)
    at process.processTimers (node:internal/timers:514:7)
에러 메시지 끝.
```

예제 1-2 실행결과

`Promise.all()` 메서드에 전달된 프로미스 객체 배열 내에서 하나의 프로미스 객체에서 에러가 발생하여 `all()` 메서드는 거절된 프로미스를 반환하였고, 그 결과 이를 `catch()` 메서드에서 잡게 된 것이다. 

즉, 단 하나의 프로미스라도 거절 상태가 된다면 다른 프로미스들은 정상적으로 처리되더라도 결국엔 `all()` 메서드는 거절된 프로미스 객체를 반환하게 된다는 것이다. 만약 거절된 프로미스가 있더라도 나머지 프로미스들의 결과값을 받아와야 하는 상황이라면 다음에 소개할 `Promise.allSettled()` 메서드를 사용하는 것이 좋을 것이다. 

`Promise.all()` 메서드에 전달될 iterable 객체 내부에 Promise 객체가 아닌 값이 포함되어도 문제 없이 실행된다. 이 경우, 해당 값이 그대로 `all()` 메서드가 반환하는 배열 내의 요소로 포함된다. 

```jsx
Promise.all([
    new Promise((resolve, reject) => {
        setTimeout(() => resolve('hi'), 1000);
    }),
    'nice to',
    'meet',
    'you'
]).then(result => console.log(result));

```

예제 1-3

```jsx
[ 'hi', 'nice to', 'meet', 'you' ]
```

예제 1-3 실행결과

# Promise.allSettled

단 하나의 프로미스라도 거절되면 거절된 프로미스를 반환하는 `Promise.all()` 메서드와 달리, `Promise.allSettled()` 메서드는 각각의 프로미스 결과에 따라 각각 다른 요소들을 담는 배열을 반환한다. 

배열 내 각각의 프로미스 객체들에 대해, 만약 이행된 프로미스라면 `{status: “fulfilled”, value: result}` 객체 리터럴을, 거절된 프로미스라면 `{status: “rejected”, reason: error}` 객체 리터럴을 반환될 배열의 요소로 삼는다. 

```jsx
let files = [
    ['안내서.txt', 1],
    ['코딩 잘하는 법.pdf', 3],
    ['로드맵.jpg', 2]
];

function download(filePath, delaytime) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (delaytime >= 3) {
                reject(new Error("3초 이상은 못 기다린다!"));
            }
            resolve(filePath);
        }, delaytime * 1000);
    });
}

let requests = files.map(elements => download(elements[0], elements[1]));

Promise.allSettled(requests).then(results => console.log(results)); // 테스트용
/* // 활용 코드
Promise.allSettled(requests)
    .then(results => {
        results.forEach(result => {
            if (result.status == 'fulfilled') {
                console.log("다운로드 성공: " + result.value);
            } else if(result.status == 'rejected') {
                console.log("다운로드 실패: " + result.reason);
            }
        });
    });*/
```

예제 2-1

```
[
  { status: 'fulfilled', value: '안내서.txt' },
  {
    status: 'rejected',
    reason: Error: 3초 이상은 못 기다린다!
        at Timeout._onTimeout (file:///C:/web/html_css_js_study/promise-async-await/promise-api/promise-allsettled.js:11:24)
        at listOnTimeout (node:internal/timers:573:17)
        at process.processTimers (node:internal/timers:514:7)
  },
  { status: 'fulfilled', value: '로드맵.jpg' }
]
```

예제 2-1 실행결과1

위 예제에서 “테스트용”이란 주석이 붙은 줄에는 주석 처리하고, 아래의 “활용 코드” 부분의 코드들을 주석 해제하고 실행하면 다음의 결과를 얻는다. 

```
다운로드 성공: 안내서.txt
다운로드 실패: Error: 3초 이상은 못 기다린다!
다운로드 성공: 로드맵.jpg
```

예제 2-1 실행결과2

> `Promise.allSettled` 메서드는 비교적 최근에 추가된 메서드여서 구형 브라우저에서는 실행되지 않을 수 있다. 이러한 경우에는 해당 메서드를 개발자가 직접 구현하는 수밖에 없다. 구현 예시는 다음 사이트의 `Promise.allSettled` → 폴리필 챕터를 참고.
> 
> 
> [프라미스 API](https://ko.javascript.info/promise-api)
> 

# Promise.race

앞서 소개한 다른 메서드들과 마찬가지로, `race()` 메서드도 프로미스 객체들이 담긴 iterable 객체를 인자로 전달받는다. 단, `race()` 메서드는 여러 프로미스 객체들 중 가장 빨리 처리된 프로미스의 result 값만을 반환한다. 만약 가장 빨리 처리된 프로미스가 거절된 프로미스라면 그 error 값을 반환한다. 

```jsx
let files = [
    ['안내서.txt', 1],
    ['코딩 잘하는 법.pdf', 3],
    ['로드맵.jpg', 2]
];

function download(filePath, delaytime) {
    return new Promise((resolve, reject) => {
        setTimeout(() => resolve(filePath), delaytime * 1000);
    });
}

let requests = files.map(elements => download(elements[0], elements[1]));

Promise.race(requests)
    .then(result => console.log(result));
```

예제 3-1

```
안내서.txt
```

예제 3-1 실행결과

---

References

[1] [프라미스 API](https://ko.javascript.info/promise-api)