---
title: "[JS] 예외 처리"
category: "javascript"
tag: ["web", "js", "javascript", "exception", "try", "catch"]
---

# try ~ catch

자바스크립트에서 예외(런타임 에러)를 처리하기 위한 문법은 try ~ catch이다. 형태는 다음과 같다. 

```jsx
try {
    // 코드
} catch(e) {
    // 예외 발생 시 처리할 코드
}
```

try 구문 내에서 에러가 발생하면 그 즉시 catch 구문으로 넘어가 해당 코드가 실행되는 구조이다. 

다음은 try ~ catch 사용 예제이다.

```jsx
try {
    console.log(notDeclared);  // 변수 선언 없이 바로 호출 시도.
} catch(err) {
    console.log("에러 발생!");
    //console.log(err);  // 에러 상세 메시지 출력.
    console.log(err.name);
    console.log(err.message);
}
```

```jsx
에러 발생!
ReferenceError
notDeclared is not defined
```

catch문 뒤의 괄호 내부에는 예외 발생 시 예외 객체를 담는 예외 변수를 선언한다. 해당 변수명은 아무렇게나 지어도 된다. 예외 객체에는 어떤 예외인지에 대한 예외 이름을 담는 프로퍼티인 name과, 좀 더 구체적인 에러 메시지가 담긴 message 프로퍼티가 존재한다. 그리고 예외 변수 자체를 출력하면 name : message 형태의 에러에 대한 자세한 설명이 나온다. 또한, 예외 객체에는 stack이라는 프로퍼티도 존재하여, 에러가 발생하게된 호출 스택의 정보들을 순서대로 보여주는 문자열을 담고 있다. 

# finally

try ~ catch문 뒤에 바로 finally 문을 붙일 수 있다. 

```jsx
try {
    // 코드
} catch(e) {
    // 예외 발생 시 처리할 코드
} finally {
    // 예외 발생 여부 상관없이 무조건 실행할 코드.
}
```

finally 문은 try에서 실행할 코드에서 예외가 발생하든 상관없이 무조건 실행할 코드를 담는 곳이다. finally문은 생략 가능하다. 

finally문은 일단 try문이 실행된 후, 예외가 발생하면 catch문 코드가 실행된 후에 finally가 실행되고, 예외가 발생하지 않으면 바로 finally 구문이 실행되는 방식이다. 즉, 예외가 발생하건 말건 무조건 맨 뒤에 실행되는 구문이다. 그런데 이 finally가 함수 내부의 return문과 만나면 조금 기이한 현상이 발생한다. 다음의 코드를 보자.

```jsx
function someFunc() {
    try {
        return "hi";
    } catch(e) {
        console.log("에러 발생");
    } finally {
        console.log("드디어, 마침내!");
    }
}

console.log(someFunc());
```

```jsx
드디어, 마침내!
hi
```

일반적으로, 함수에서 return문을 만나면 아무리 그 뒤에 코드가 더 있더라도 무조건 반환값을 반환한 후 함수 실행을 그 즉시 중단한다. 그런데 위 예제에서는 finally 구문이 실행된 후에 값이 반환된 후에야 종료된다. 이 경우, try문 안의 return문이 실행되어 값을 반환하기 전에 먼저 finally가 실행된 후에 값이 반환되며 함수가 종료되는 구조이다. 

# setTimeout과 try ~ catch

setTimeout과 같은 함수 실행 시 try ~ catch문과 함께 사용할 때 주의할 점이 있다. 

```jsx
try {
    setTimeout(() => {
        what;
    }, 1000);
} catch (e) {
    console.log("에러 발생!");
}
```

```jsx
C:\web\html_css_js_study\js-try-catch\with-set-time-out.js:3
        what;
        ^

ReferenceError: what is not defined
    at Timeout._onTimeout (C:\web\html_css_js_study\js-try-catch\with-set-time-out.js:3:9)    at listOnTimeout (node:internal/timers:573:17)
    at process.processTimers (node:internal/timers:514:7)

Node.js v20.11.0
```

위 예제에서, 원래대로라면 예외가 발생한 후에 catch문에서 이를 처리했어야 했다. 그런데 실행결과를 보면 try ~ catch문에서 에러를 잡지 못하고 그대로 프로그램이 중단된 것을 볼 수 있다. 이는 자바스크립트 엔진이 코드 실행 시 try ~ catch문을 빠져나간 후에 setTimeout의 인자로 전달된 익명 함수가 실행되는 구조이기 때문이다. 따라서 위와 같은 상황을 방지하기 위해선, setTimeout 함수의 인자로 전달되는 익명 함수 내부에서 try ~ catch문을 사용해야 예외를 잡을 수 있다. 

```jsx
setTimeout(() => {
    try {
        what;
    } catch(e) {
        console.log("에러 발생!");
    }
}, 1000);
```

```jsx
에러 발생!
```

# 예외 생성하기

만약 자바스크립트 인터프리터에서는 에러로 인식하지 않지만, 특정 경우가 발생할 경우, 일부러 에러를 일으켜 이를 처리하게끔 하고자 할 때도 있을 것이다. 즉, 예외를 일부러 일으키는 것이다. 자바스크립트에서는 고의적으로 예외를 생성하여 이를 가장 가까운 try ~ catch문에서 처리하게끔 하고자 할 때 throw 키워드를 사용할 수 있다. 또한 자바스크립트에서는 예외도 객체로 다루므로 객체 생성을 위해 new 키워드와 같이 사용해야 한다. 즉, 예외를 생성하는 코드 형태는 다음과 같다. 

```jsx
throw new ErrorObject("에러 메시지 문자열");
```

다른 프로그래밍 언어에서는 대부분 나눗셈 연산 시 분모가 0일 때 에러를 일으킨다. 그러나 자바스크립트에서는 0으로 나누는 연산을 해도 에러가 발생하지 않는다. 이는 다음 예제를 통해 확인할 수 있다. 

```jsx
function getDivide(a, b) {
    return a / b;
}

let a = 1;
let b = 0;

console.log(getDivide(a, b));
```

```jsx
Infinity
```

(사실 수학 시간에 분자가 몇이건 분모가 0이면 무조건 무한대로 취급하긴 했다. 사실 자바스크립트야말로 진정 수학적인 언어가 아닐까? 0분의 몇을 감히 에러 취급하다니…)

만약 분모에 0이 들어오면 이를 에러로 취급하여 에러를 일으킨 후, try ~ catch문에서 이를 처리하고자 한다. 그러면 앞서 언급한 throw 키워드를 이용하면 된다. 위 예제를 다음과 같이 바꿔보았다. 

```jsx
function getDivide(a, b) {
    try {
        if(b === 0) {
            throw new Error("0으로 나누는 연산은 불가능합니다.");
        } 
        return a / b;
    } catch(e) {
        console.log("에러 발생!");
        console.log(e);
        console.log(e.name);
        console.log(e.message);
    }
}

let a = 1;
let b = 0;

console.log(getDivide(a, b));
```

```jsx
에러 발생!
Error: 0으로 나누는 연산은 불가능합니다.
    at getDivide (C:\web\html_css_js_study\js-try-catch\throw-new.js:4:19)
    at Object.<anonymous> (C:\web\html_css_js_study\js-try-catch\throw-new.js:18:13)
    at Module._compile (node:internal/modules/cjs/loader:1376:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1435:10)
    at Module.load (node:internal/modules/cjs/loader:1207:32)
    at Module._load (node:internal/modules/cjs/loader:1023:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:135:12)      
    at node:internal/main/run_main_module:28:49
Error
0으로 나누는 연산은 불가능합니다.
undefined
```

분모 자리에 0이 전달될 경우, 더 이상의 계산을 막기 위해 일부러 예외를 일으킨 후, 이를 catch문에서 처리하도록 고안하였다. 그 결과 분모로 0을 대입했을 때 에러 메시지가 뜬 것을 볼 수 있다. 

throw 키워드를 이용하여 생성된 에러는 원래 런타임 에러로 발생한 예외들처럼 가장 가까운 try ~ catch문을 만나면 거기서 예외가 처리되고, 만약 없다면 더 상위로 올라간다. 함수 내부에 예외를 발생시킨 경우, 메서드 호출부로 올라가 try ~ catch문을 찾는 식이다. 만약 전역 공간에서도 try ~ catch문을 만나지 못하면 에러 메시지를 내며 프로그램이 중단된다. 

## 에러 다시 던지기 (rethrowing)

try 블록 내 코드에서 예외가 발생하면 무조건 바로 다음의 catch문으로 이동되어 그 블록 내부 코드가 실행된다. 보통 try 블록 내에서 발생할 것으로 예상되는 예외를 그 다음 catch에서 처리하는데, 다음과 같은 상황도 발생할 수 있을 것이다. 

1. try 문 내에서 전혀 예상하지 못한 예외가 발생한 경우.
2. try 문 내에서 예상되는 예외가 많을 경우. ⇒ 사실 이 경우, try 문 내에 코드가 길어서 예외가 여러 군데에서 발생할 것 같으면 되도록 코드를 따로 구분하여 처리하는 게 좋다. 그런데 그렇게 못하는 경우도 있을 수도 있다. 
3. catch문에서는 여러 가능한 예외들 중 특정 예외만 처리하고 싶고, 나머지 예외들은 다른 try ~ catch문에서 처리되길 희망하는 경우. 예를 들어 함수 내부에서는 특정 예외들만 처리하고, 나머지 예외들은 함수 호출부에서 처리하고자 하는 경우. 

다음 예제는, 2개의 정수를 입력받은 객체를 함수의 인자로 전달한 후, 해당 객체 내 2개의 정수를 반환하는 메서드를 호출한 후, 이 두 정수를 나눗셈한 결과를 반환하는 프로그램 코드이다. 

```jsx
class ZeroDivisionError extends Error {
    constructor(message) {
        super(message);
        this.name = "ZeroDivisionError";
    }
}

class MyNumbers {
    constructor(n1, n2) {
        this.n1 = n1;
        this.n2 = n2;
    }

    /*
    getTwoNums() {
        return [this.n1, this.n2];
    }*/
}

function getDivide(a, b) {
    if(b === 0) {
        throw new ZeroDivisionError("0으로 나눌 수 없습니다.");
    }
    return a / b;
}

function execute(myObj) {
    try {
        let [a, b] = myObj.getTwoNums();
        return getDivide(a, b);
    } catch(e) {
        console.log("에러 발생! 0으로 나눌 수 없습니다.");
    }
}

let result = execute(new MyNumbers(1, 2));
console.log(result);
```

```jsx
에러 발생! 0으로 나눌 수 없습니다.
undefined
```

위 예제의 execute에서 try ~ catch문을 작성하여 예외를 처리하려고 하고 있다. 그런데 해당 문에서는 0으로 나누는 ZeroDivisionError 예외만을 예상하고 이를 처리하려고 하고 있다. 그런데, execute 함수 인자로 전달받은 객체의 getTwoNums 메서드가 정의되지 않은 채 호출을 하고 있다. 이 경우 ZeroDivisionError가 아닌 전혀 다른 종류의 예외가 발생할 것이다. 그럼에도 예외를 처리하는 catch문에는 여전히 ZeroDivisionError 예외와 동일하게 처리하려고 하고 있다. 이러면 정확히 어떤 예외가 발생한 것인지 파악하기가 어려워진다. 그렇기에 이렇게 예상하지 못한 예외는 해당 catch문에서 처리하기 보다는 다시 예외를 던져 외부에서 처리하기를 원한다. 이 경우, catch문 내에서 일단 예외가 ZeroDivisionError인지를 확인한 후, 아니라면 throw 키워드를 이용하여 외부로 예외를 던져서 처리하는 게 더 좋다. 이렇게 해야 두 예외를 구분하여 처리할 수 있기에 디버깅이 좀 더 수월해질 것이다. 다음은 위 예제를 고친 또 다른 예제이다. 

```jsx
class ZeroDivisionError extends Error {
    constructor(message) {
        super(message);
        this.name = "ZeroDivisionError";
    }
}

class MyNumbers {
    constructor(n1, n2) {
        this.n1 = n1;
        this.n2 = n2;
    }

    
    getTwoNums() {
        return [this.n1, this.n2];
    }
}

function getDivide(a, b) {
    if(b === 0) {
        throw new ZeroDivisionError("0으로 나눌 수 없습니다.");
    }
    return a / b;
}

function execute(myObj) {
    try {
        let [a, b] = myObj.getTwoNums();
        return getDivide(a, b);
    } catch(e) {
        if(e instanceof ZeroDivisionError) {  // instanceof로 예외 종류 파악.
            console.log("에러 발생! 0으로 나눌 수 없습니다.");
        } else {
            throw e;  // rethrowing
        }
    }
}

// ZeroDivisionError 외에 예상치 못한 예외 처리.
try {
    let result = execute(new MyNumbers(1, 0));
    console.log(result);
} catch(e) {
    console.log("에러 발생! 다음은 에러 상세 메시지입니다.");
    console.log(e);
}
```

execute 함수 내 catch문에서 instanceof 연산자를 이용하여 먼저 예외의 종류를 먼저 파악하도록 하였다. 그 결과 ZeroDivisionError라면 내부에서 처리하도록 하였고, 그 외의 예외라면 다시 던져서 함수 호출부에서 예외를 처리하도록 하였다. 그러면 함수 호출부를 try ~ catch문으로 감싸서 여기서는 예상치 못한 예외를 처리하면 되는 것이다. 이렇게 예상했던 예외와 그렇지 않은 예외를 rethrowing을 통해 구분하여 처리할 수 있게 되었다. 

---

References

[1] ['try..catch'와 에러 핸들링](https://ko.javascript.info/try-catch)