---
title: "[JS] 호출 스케줄링 : setTimeout, setInterval"
category: "javascript"
tag: ["web", "js", "javascript", "setTimeout", "setInterval"]
---

- 호출 스케줄링(scheduling a call) : 일정 시간이 경과한 후에 특정 함수를 예약 호출하여 실행시키는 것을 의미. 특정 함수를 언제 “호출(call)”할 지 “계획(scheduling)”하는 것이라 생각하면 되겠다.

자바스크립트에서 호출 스케줄링을 지원하는 두 함수가 존재한다. 

- setTimeout : 일정 시간이 경과한 후에 특정 함수를 실행시킨다.
- setInterval : 일정 시간 간격을 두고 함수를 실행시킨다.

# setTimeout

해당 함수는 다음의 형태를 띈다.

```jsx
setTimeout(func, [delay], [args, ...]);
```

매개변수

- func : 실행하고자 하는 함수 선언부. 주의할 것은 함수 실행부가 아닌 선언부를 대입해야 한다.
- delay : func 인자로 대입한 함수를 언제 실행시킬 것인지 그 지연 시간을 명시. 단위는 밀리초(millisecond)로 사용한다. 1000밀리초 = 1초이다. 생략 시 0으로 기본 설정된다.
- args : 함수에 전달할 가변 인수. (IE9 이하에선 지원되지 않는다고 한다)

첫 번째 매개변수에는 함수의 실행부를 넣어선 안된다. 함수의 결과값이 아닌 함수 자체의 참조를 토대로 실행시키기 때문이다. 

```jsx
setTimeout(someFunc(), 1000);   // 잘못된 방법. 함수 호출 코드를 넣어선 안된다.
setTimeout(() => {console.log('hi'), 1000}  // 함수 참조, 즉 선언부를 대입해야 한다.
```

다음은 setTimeout을 사용하는 예제이다. 

```jsx
console.log("Before starting to call setTimeout");
let mySchedule = setTimeout((n) => {
    console.log("Hello, world!");
    let total = 0;
    for(let i = 0; i <= n; i++ ) {
        total += i;
    }
    console.log(`total(${n}) = ${total}`);
}, 1000, 10);
```

```jsx
Before starting to call setTimeout
Hello, world!
total(10) = 55
```

위 예제의 setTimeout 함수의 첫 번째 인자로 화살표 함수를 이용하여 익명의 함수 선언부를 작성하였다. 두 번째 인자로는 딜레이 시간으로 1000밀리초, 즉 1초를 설정하였다. 그래서 setTimeout 함수가 호출되면 그로부터 1초 뒤에 첫 번째 인자로 전달된 함수를 실행한다. 3번째 인자는 첫 번째 인자로 넘긴 함수의 인수로, 여기서는 10을 넘기고 있다. 첫 번째 인자로 전달된 함수가 이 인자를 받아 함수 내부에서 처리할 수 있게 된다. 

한 편, setTimeout 함수는 호출 시 Timeout 객체가 반환된다. 다음은 위 예제에서, setTimeout 함수의 반환값을 받는 mySchedule 변수를 각각 다른 방식으로 출력하는 예제이다. 

```jsx
console.log(mySchedule);
console.log(typeof mySchedule);
console.log(`${mySchedule}`);
```

```jsx
Timeout {
  _idleTimeout: 1000,
  _idlePrev: [TimersList],
  _idleNext: [TimersList],
  _idleStart: 24,
  _onTimeout: [Function (anonymous)],
  _timerArgs: [Array],
  _repeat: null,
  _destroyed: false,
  [Symbol(refed)]: true,
  [Symbol(kHasPrimitive)]: false,
  [Symbol(asyncId)]: 6,
  [Symbol(triggerId)]: 1
}
object
6
```

setTimeout 반환값을 받는 변수를 그냥 출력하면 위와 같이 Timeout 객체 리터럴이 출력되는 것을 확인할 수 있고, 문자열로 출력하면 숫자만 출력된다. 이 반환값은 setTimeout의 식별자, 즉 “타이머 식별자(timer identifier)”이다. 이 타이머 식별자를 변수에 할당하면 추후에 이 변수를 이용하여 특정 setTimeout 함수로 스케줄링된 함수의 실행을 중단시킬 수도 있다. 이에 대한 내용은 바로 다음의 ‘clearTimeout’ 에서 자세히 나온다. 

> setTimeout 함수의 반환값 형태는 실행 환경에 따라 달라진다고 한다. 예를 들어, 위 예제 1-2는 node.js 환경에서 실행한 결과이다. 반면 브라우저 환경에서는 숫자형으로 나온다고 한다.
> 

## clearTimeout : 스케줄링 취소

다음은 1초 간격으로 시간을 세고, 이를 출력하는 예제이다. 

```jsx
let passedSeconds = 0;

let stopwatch = setTimeout(function tick() {
    console.log(passedSeconds);
    passedSeconds += 1;
    stopwatch = setTimeout(tick, 1000);
}, 1000);

```

```jsx
0
1
2
3
4
(생략)
```

위 예제는 스탑워치로서 사용하려고 만든 예제이다. 그런데 한 가지 문제점이 있다. 이를 터미널 창에서 실행시키면 1초 간격으로 시간 데이터가 무한정 출력되고, 원할 때 중지할 수 있는 기능이 없다. 그래서 이 경우, 터미널 창에서 ctrl + c 를 눌러야 빠져나올 수 있다. 하지만 이렇게 빠져나오는 것도 의도한 것이 아니다. 그렇다면 사용자가 원하는 때에 이 스탑워치를 중단시킬 수 있는 방법이 있을까? 

이렇게 setTimeout을 통해 특정 함수가 계속 실행되어 이를 중단하고자 할 때 사용할 수 있는 함수가 `clearTimeout()` 함수이다. 이 함수 인자로 setTimeout 함수의 반환값인 타이머 식별자를 대입하면 이 `clearTimeout()` 함수가 호출될 때 해당 setTimeout의 함수 실행도 중단된다. 

실제로 `clearTimeout()` 함수를 호출하면 기존의 스케줄링이 중단되는지 확인하기 위해 다음의 예제를 준비하였다. 확실한 결과를 보기 위해, 앞선 예제 2-1 코드를 다수 변경하였다. 먼저, 웹 브라우저에서 확인을 하기 위해 html 코드를 다음과 같이 준비하였다. 

```jsx
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style></style>
</head>
<body>
    <div id="stopwatch">
        <div id="time-data"><p></p></div>
        <input type="button" value="중단" id="timer-pause">
    </div>
    <script src="stop-call.js"></script>
</body>
</html>
```

그 다음, 자바스크립트 코드를 다음과 같이 변경하였다. 

```jsx
class StopWatchElement {
    passedSeconds = 0;
    stopwatchId = undefined;

    constructor() {
        this.timeDisplay = document.querySelector('#time-data > p');
        this.pauseButton = document.querySelector('#timer-pause');
    }

    tick() {
        this.timeDisplay.innerHTML = `${this.passedSeconds}`;
        this.passedSeconds += 1;
        // 아래 줄의 setTimeout을 호출할 때마다 그에 대한 타이머 식별자도 
        // 그 값이 바뀌므로, 이 값을 담는 변수에도 계속 갱신해줘야 제 기능을 한다.
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }
    
    executeTimer() {
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
        this.pauseButton.addEventListener('click', () => {
            clearTimeout(this.stopwatchId);
        });
    }
}

let stopwatchEle = new StopWatchElement();
stopwatchEle.executeTimer();
```

위 html 코드를 웹 브라우저 상에서 실행하면 웹 화면이 다음과 같이 보일 것이다. 

![사진 1-1. 예제2-2~3 실행결과](/images/2024-04-05/js-call-scheduling/1.png)

사진 1-1. 예제2-2~3 실행결과

“중단” 버튼 위에 숫자가 1초 간격으로 하나씩 증가할 것이다. 이 때, “중단” 버튼을 눌러보자. 그러면 숫자가 더 이상 증가하지 않고 멈추는 것을 확인할 수 있다. 

# setInterval

setInterval 메서드는 setTimeout과 같은 문법 형태를 띤다. 

```jsx
let timerId = setInterval(func, [delay], [args...]);
```

다른 모든 것은 동일하나, setInterval 메서드는 첫 번째 인자로 주어진 함수를 두 번째 인자로 주어진 시간마다 주기적으로 실행시켜준다는 점이 다르다. 

setInterval 메서드를 통해 스케줄링되고 있는 함수의 호출을 중단하려면 clearInterval(timerId) 메서드를 사용해야 한다. 

참고로, 다음 예제는 앞서 setTimeout 메서드를 이용하여 스탑워치를 구현했던 예제 2-3을 setInterval 메서드만 사용하는 것으로 바꾼 예제이다. 

```jsx
class StopWatchElement {
    passedSeconds = 0;
    stopwatchId = undefined;

    constructor() {
        this.timeDisplay = document.querySelector('#time-data > p');
        this.pauseButton = document.querySelector('#timer-pause');
    }

    tick() {
        this.timeDisplay.innerHTML = `${this.passedSeconds}`;
        this.passedSeconds += 1;
    }
    
    executeTimer() {
        this.stopwatchId = setInterval(this.tick.bind(this), 1000);
        this.pauseButton.addEventListener('click', () => {
            clearInterval(this.stopwatchId);
        });
    }
}

let stopwatchEle = new StopWatchElement();
stopwatchEle.executeTimer();
```

실행 결과는 앞선 예제와 동일하다.

# 중첩 스케줄링

앞선 스탑워치 예제는 1초마다 지난 시간을 갱신하여 보여주는 특성 상, 누적 시간을 웹 화면상에 출력하여 보여주고, 1을 증가시키는 함수의 기능이 일정 간격을 두고 여러 번 실행되어야 했다. 그리고 이를 위해서 앞서 보인 예제들에서는 다음의 두 방법을 이용하였다. 

1. setTimeout을 재귀적으로 호출하도록 하여 중첩 호출하는 방법.
2. setInterval을 단 한 번만 사용하는 방법.

이처럼, 어떤 기능을 일정 간격을 두고 계속 실행하게 하는 방법이 두 가지가 있다. 위의 스탑워치 예제들을 보면, 코드 상으로는 setTimeout을 중첩하여 사용하는 방법이 setInterval보다 조금 더 복잡하고 가독성도 setInterval에 비해선 상대적으로 떨어지는 듯이 보인다. 하지만, 사실 setInterval보다 setTimeout을 사용하는 것이 더 좋은 점들이 있다. 

일정 시간 간격으로 함수를 여러 번 호출해야 할 경우, 중첩된 setTimeout이 setInterval보다 더 좋은 이유.

1. 일정 시간 간격으로 스케줄링되어 호출되고 있는 함수에 대해, 그 결과에 따라 다음 함수 호출 시 그 기능이 변경되어야 하는 경우. 즉, 현재 함수의 호출 결과가 다음 함수 호출에 영향을 끼치는 경우. 이 경우에 setTimeout 중첩을 사용하면 이를 유연하게 대처할 수 있게 된다. 
2. setTimeout을 중첩적으로 사용하면, 스케줄링되고 있는 함수의 실행시간이 얼마나 걸리든 간에, setTimeout의 두 번째 인자로 전달된 지연 시간을 무조건 보장한다. 반면, setInterval은 함수의 실행 시간도 지연 시간에 포함시기 때문에 대부분의 경우, 개발자가 원하는 지연 시간과 다른 시간 간격으로 스케줄링이 진행될 가능성이 높다. 

1의 예는 다음과 같다. 만약 특정 시간 간격으로 서버에 요청을 보내 데이터를 얻고, 이를 웹 상에 보여주는 웹 사이트가 있다고 가정해보자. 실시간 날씨 정보 사이트가 될 수도 있고, 실시간 뉴스 정보 사이트가 될 수도 있겠다. 그런데 해당 서버에 이미 요청이 너무 많아서 요청이 실패한다면, 시간당 요청 빈도를 줄이기 위해 요청 시간 간격을 더 늘리는 방법을 떠올릴 수 있을 것이다. 이럴 때 중첩 setTimeout을 이용하면 유동적으로 시간을 늘릴 수 있게 된다. 다음은 이를 구현하는 의사코드이다. 

```jsx
let requestDelay = 10000; // 평상 시 10초 간격으로 서버에 요청.

let timerId = setTimeout(function requestToServer() {
    // 요청 보내는 코드.
    
    if (서버 과부하로 인해 요청이 실패했을 경우) {
        requestDelay *= 3;
    } else {
        // 이전에 요청이 실패했다가 다시 요청이 정상적으로 실행될 경우
        // 를 대비해 요청 시간을 원래대로 복구한다.
        requestDelay = 10000;
    }
    timerId = setTimeout(requestToServer, requestDelay);
}, requestDelay);
```

2의 경우, setInterval 이용 시, 해당 메서드의 첫 번째 인자로 전달한 함수의 실행 시간도 지연 시간에 포함된다. 만약, 개발자가 명시한 지연 시간보다 함수의 실행 시간이 더 많은 경우, 함수의 실행이 종료될 때까지 대기하다가, 실행이 종료되자마자 다음 함수를 호출한다. 그래서 사실상 명시된 지연 시간의 의미가 사라지고, 지연 시간없이 함수를 호출하게 된다. 

반면, 중첩 setTimeout은 함수의 실행 시간에 상관없이, 함수의 실행이 종료되고 나서 지연 시간을 적용시킨다. 따라서 개발자가 명시한 지연 시간이 반드시 보장된다. 그래서 함수의 실행 시간이 지연 시간보다 더 크더라도, 한 함수의 실행 시간이 끝난 후에는 바로 다음 함수가 호출되지 않고 지연 시간 그대로 기다린 후에야 다음 함수를 호출하게 된다. 

## 중첩 setTimeout과 타이머 식별자

스탑워치를 구현하는 앞선 예제 3-1을 다시 살펴보도록 하겠다. 

```jsx
class StopWatchElement {
    passedSeconds = 0;
    stopwatchId = undefined;

    constructor() {
        this.timeDisplay = document.querySelector('#time-data > p');
        this.pauseButton = document.querySelector('#timer-pause');
    }

    tick() {
        this.timeDisplay.innerHTML = `${this.passedSeconds}`;
        this.passedSeconds += 1;
        // 아래 줄의 setTimeout을 호출할 때마다 그에 대한 타이머 식별자도 
        // 그 값이 바뀌므로, 이 값을 담는 변수에도 계속 갱신해줘야 제 기능을 한다.
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
    }
    
    executeTimer() {
        this.stopwatchId = setTimeout(this.tick.bind(this), 1000);
        this.pauseButton.addEventListener('click', () => {
            clearTimeout(this.stopwatchId);
        });
    }
}

let stopwatchEle = new StopWatchElement();
stopwatchEle.executeTimer();
```

위 예제의 `tick()` 메서드의 마지막 줄을 보면, 타이머 식별자를 저장하는 프로퍼티인 stopwatchId에 다음에 호출할 setTimeout가 반환하는 타이머 식별자로 갱신하도록 하고 있다. 만약, 다음 코드처럼 해당 프로퍼티를 갱신하지 않은 채로 실행시키면 어떻게 될까?

```jsx
tick() {
    this.timeDisplay.innerHTML = `${this.passedSeconds}`;
    this.passedSeconds += 1;
    setTimeout(this.tick.bind(this), 1000);
}
```

위 코드로 실행해보면, “중단” 버튼을 눌러도 스탑워치가 전혀 중단되지 않고 계속 실행되는 것을 볼 수 있다. 위 코드의 tick() 메서드는 executeTImer 메서드 내에서 처음으로 호출하고, tick() 메서드 내부에서는 `tick()` 메서드 자기 자신을 재귀적으로 호출하는 구조이다. 이 때, setTimeout은 호출될 때마다 항상 다른 타이머 식별자를 반환한다. 따라서 이후에 `clearTimeout()`으로 해당 기능을 중단시키려면 계속 지연 시간이 지난 후에 실행되는 setTimeout의 타이머 식별자를 이용해야 한다. 그렇기에 중첩 setTimeout 에서는 일정 간격으로 실행할 함수 내부에 setTimeout을 작성할 때, 반드시 해당 타이머 식별자를 받는 외부 변수가 존재해야 한다. 이를 통해 해당 함수가 실행될 때마다 새롭게 생성되는 타이머 식별자를 외부에서 갱신하여 이를 활용할 수 있게 된다. 

# 호출 스케줄링과 메모리

자바스크립트 실행 시, 더 이상 외부로부터 참조되지 않아 사용되지 않는 메모리는 자동 메모리 관리 시스템인 가비지 컬렉션에 의해 자동으로 해당 데이터를 메모리 상에서 제거해준다. 그런데, setInterval, setTimeout의 첫 번째 인자로 넘긴 함수는 비록 외부에서 이를 참조하는 것이 없다고 하더라도, 가비지 컬렉션의 대상이 되지 않는다. 이는 호출 스케줄링을 관리하는 스케줄러가 해당 함수의 참조를 저장하고 있기 때문이다. 

setInterval은 clearInterval이 호출되기 전까지는 함수의 참조가 메모리 공간 상에 계속 남아있다고 한다. 

그런데 이러한 특성은 메모리 공간에 악영향을 끼칠 수도 있다. setInterval 또는 setTimeout 메서드의 첫 번째 인자로 넘긴 함수를 someFunc이라고 해보겠다. 그런데 이 함수는 외부 변수 a를 참조한다고 할 때, 해당 함수가 실행할 때에는 someFunc 함수의 참조와 함께 외부 변수 a에 담긴 데이터까지도 메모리 공간에 존재하게 된다. 즉, 함수 내부에서 참조하는 외부 변수의 데이터까지도 메모리 공간을 차지하게 된다. 따라서 이러한 메모리 남용을 방지하려면 스케줄링이 더 이상 필요없어진 함수에 대해서는 clearTimeout, clearInterval 등을 통해 취소하는 것이 좋다. 

# 일반 코드와 호출 스케줄링

```jsx
setTimeout(() => {console.log('hi')});

function getTotal(n) {
    let total = 0;
    for(let i = 0; i <= n; i++) {
        total += i;
    }
    return total;
}

let userN = 1000000000;
//userN = 1;
console.log(getTotal(userN));
```

위 예제에서는 지연 시간이 0인 `setTimeout()` 코드가 먼저 등장하고, 그 후에는 0부터 n까지의 모든 자연수를 더한 값을 반환하는 함수의 선언부와 이를 실행하는 코드로 이루어져 있다. 참고로 위 코드에서 `getTotal()` 함수 실행 시간은 약 1초이다. 

언뜻 보면, setTimeout의 지연 시간이 0으로, getTotal 함수의 실행 시간보다 더 빠르기도 하고, 코드 상으로도 맨 위에 나와있으므로, ‘hi’가 먼저 출력된 다음에 `getTotal()` 함수의 결과값이 출력될 것처럼 보인다. 하지만 실행결과를 보면 전혀 다르다. 

```jsx
500000000067109000
hi
```

이는, setTimeout의 외부 코드가 먼저 실행된 후에 setTimeout에 전달된 함수가 실행되는 순서이기 때문이다.  

---

References

[1] [setTimeout과 setInterval을 이용한 호출 스케줄링](https://ko.javascript.info/settimeout-setinterval)