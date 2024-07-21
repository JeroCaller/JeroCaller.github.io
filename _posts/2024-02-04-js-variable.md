---
title: "[JS] 변수(Variable)"
category: "javascript"
tag: ["web", "js", "javascript", "variable"]
---

# 변수 (Variable)

프로그래밍 언어에서 변수는 어떤 값, 데이터를 저장하는 무언가이며, 프로그램 실행 도중 그 값이 변할 수 있는 것을 말한다. 변수는 사용자가 식별자로 이름을 지어주면 그 이름을 변수로 사용할 수 있다. 이 변수를 이용하면 변수에 담긴 값이 저장된 메모리 공간의 정확한 위치를 사용자가 몰라도 그 값에 접근하여 사용할 수 있게 된다. 

변수명은 고유한 식별자로, 한 프로그램에 둘 이상 중복되게 이름을 선언할 수는 없다(마치 서로 다른 사람들의 주민등록번호가 일치할 수 없는 것처럼). 

프로그램 실행 도중 값이 변하지 않는 것은 “상수”라 부른다. 

변수명 지정 규칙은 식별자 이름 지정 규칙과 동일하다. 이 규칙은 [JS - 스타일 가이드](/javascript/js-style-guide/) 페이지의 “식별자” 챕터 참고.

자바스크립트에서 변수를 선언할 때는 ‘var’ 키워드를 이용한다. 

```jsx
var yourName;
var uniqueNumber = 10;  // 변수 선언과 동시에 값 할당도 가능.
var birthYear, birthMonth, birthDay; // 여러 개의 변수 한꺼번에 선언 가능.
yourName = '가나다';  // 이전에 선언한 변수에 나중에 할당 가능.
```

자바스크립트에서는 var 키워드 없이도 변수를 선언할 수 있다. 그러나 var 키워드를 같이 사용할 때와 차이점이 있다. 다음의 예제를 보자. 

```jsx
mynum = 5;

function someFunc() {
    mynum = 10;
    console.log(mynum);
}

someFunc();
console.log(mynum);
```

```jsx
10
10
```

mynum이란 변수가 맨 위에도 선언되었고, 함수 someFunc() 함수 내부에도 정의되었다. 그 후, 해당 함수를 먼저 호출한 뒤, 그 후에 mynum이란 변수를 console.log() 함수를 통해 someFunc() 함수 외부에서 출력하고 있다. 그 결과, 함수 내부에서의 console.log() 함수를 통한 해당 변수의 출력값과, 함수 외부에서의 출력값이 같은 걸 알 수 있다. 이는 var 키워드를 사용하지 않고 선언한 변수는 함수 내부이든 외부이든 어디에 선언을 하든 전역 변수로써 사용할 수 있다는 뜻이다. 그래서 위 예제에서의 somFunc() 함수 내부의 mynum 변수는 함수 외부의 mynum 변수와 같다. 그래서 해당 변수의 값이 someFunc() 함수 내부에서 10으로 변경된 것이다. 

이번에는 위 예제에서 someFunc() 함수 내부의 mynum 앞에 var 키워드를 사용해보았다. 

```jsx
mynum = 5;

function someFunc() {
    var mynum = 10;
    console.log(mynum);
}

someFunc();
console.log(mynum);
```

```jsx
10
5
```

아까와는 달리, 전역 공간에 선언된(맨 위에 선언된) mynum 변수의 값이 someFunc() 함수 호출을 거쳤음에도 그 값이 변하지 않았음을 알 수 있다. 즉, var 키워드를 함수 내부에서 사용하면 그 변수는 함수 내부 영역(local)에서만 사용 가능하다. 그래서 위 예제의 경우, 함수 외부에 선언된 mynum 변수와 함수 내부에 선언된 mynum 변수는 사실상 이름만 같을 뿐 서로 다른 변수가 되는 ‘동명이인’이 되는 셈이다. 단, var 키워드로 선언된 변수가 함수 외부 공간인 전역 공간에서 선언되면 그 변수는 전역 공간에서 사용될 수 있다. 

지금은 var 보다는 let과 const 키워드 사용이 권고된다. 

let은 프로그램이 실행될 동안 재할당이 가능한 변수를 선언할 때, const는 재할당이 불가능한 상수를 선언할 때 쓰인다고 한다. 

var와 let은 둘 다 해당 변수를 나중에 재할당하는 것은 가능하다. 그러나 var 키워드로 선언된 변수는 재선언이 가능하나, let 키워드로 선언된 변수는 재선언이 불가능하다. 만약 재선언한다면, 에러를 일으킨다. 

```jsx
var num;
var num; // num 변수 재선언

console.log(num);
num = 5;
console.log(num);
```

```jsx
undefined
5
```

var로 변수를 재선언하면 위 예제와 같이 초기화를 하지 않은 경우 에러가 발생하지 않고 ‘undefined’만 출력된다. 위 예제에서 var 키워드를 let 키워드로 대체해보겠다. 

```jsx
let num;
let num;  // num 변수 재선언 -> 에러

console.log(num);
num = 5;
console.log(num);
```

```jsx
Uncaught SyntaxError: Identifier 'num' has already been declared (at js_example.js:2:5)
```

let 키워드를 통해 변수를 재선언하려고 시도하면 에러가 발생함을 알 수 있다. 

만약 코드가 방대해지고 복잡해지면서, 다른 사람들과 협업해야 하는 일이 발생할 때 var 키워드를 사용하면 이미 선언된 변수가 있는지도 모르고 의도치 않은 재선언을 할 수 있고, 이로 인해 원치 않은 버그가 발생할 수 있으며, 에러가 발생하지는 않기에 어디서 버그가 일어났는지 그 원인을 파악하기 힘들 수 있다.

```jsx
var num = 10;

if (num > 5) {
    var num = 20;
}
console.log(num); // 20
```

위 예제에서, if 문 안에 num 변수의 재선언을 의도한 것이라면 문제는 되지 않겠지만, 만약 앞서 num 변수가 미리 선언되어 있다는 것을 몰라서 한 것이라면 의도치 않은 결과를 얻을 수 있다. (위 예제에서 10을 예상했겠지만 20이 나오는 것처럼) 

 따라서 요즘은 var 키워드 대신 let, const 키워드 사용이 권고된다. 

> 위 예제 1-5에서 볼 수 있듯, var 키워드로 선언된 변수는 함수 외부에서 선언될 경우 전역 공간에서 선언된다. 이 때 함수를 제외한 블록(block) 내부도 전역 공간에 포함된다(여기서 블록, 블록 영역은 중괄호({})로 둘러쌓인 영역을 의미한다). if 조건문, 반복문 내부가 그 예이다. 자바스크립트에서는 함수 영역과 블록 영역을 구분하는 것 같다.
> 

let 키워드로 선언된 변수는 블록 영역 내부에서 정의되면 블록 영역 내에서만 사용 가능하다. 

```jsx
if (true) {
    let saying = 'hi';
}
console.log(saying);
```

```jsx
Uncaught ReferenceError: saying is not defined
```

위 예제에서 let 키워드로 선언된 변수 saying은 if 블록 영역 안에 선언되었으므로, 블록 외부에서 해당 변수를 사용할 수 없다. 따라서 위와 같은 에러가 발생한 것이다. 

블록 외부에서 선언된 경우, 전역 변수로 사용 가능하다. 

```jsx
let someVar = 'hi';

if (true) {
    someVar = 'hello';
}

console.log(someVar);
```

```jsx
hello
```

블록 외부에서 선언된 경우, 전역 변수로 선언된 것이기에 블록 영역 내부에서 해당 변수를 참조하여 그 값을 변경할 수 있다. 

전역 변수로 선언하지 않는 이상, var 키워드로 선언된 변수는 함수 scope를 가지고, let, const 키워드로 선언된 변수는 block scope를 가진다. 

# scope

변수를 사용할 수 있는 범위를 scope라 한다. 여기에는 함수 안에서만 사용 가능한 지역 변수(local variable)와 코드 전체 영역에서 사용 가능한 전역 변수(global variable)로 구분 지을 수 있다. 앞선 예제들에서 봤듯, 지역 변수는 전역 변수에서 호출할 수 없다. 지역 변수는 해당 함수 영역 안에서만 존재하기 때문이다. 

# Hoisting

영어로 호이스팅(hoisting)은 “끌어올려지다”라는 뜻을 가지고 있다. 자바스크립트에서 호이스팅이란 것은, 인터프리터가 자바스크립트 코드를 해석할 때, 선언부를 scope 최상단에 끌어올리듯이 먼저 해석하는 현상을 말한다(여기서의 선언은 변수 선언, 함수 선언 등을 말한다). 정말로 해당 코드가 끌어올려지는 것은 아니고, 인터프리터가 코드 내 선언, 할당, 함수 호출 중 선언 코드를 맨 먼저 scope 최상단에 저장하기에 붙여진 이름이다. 이러한 이유로, var 키워드로 선언된 변수와 함수는 선언부 이전에 호출부를 먼저 작성해도 오류없이 정상적으로 작동한다. 

```jsx
console.log("오늘은 ", today, "요일입니다.");
var today = '화';
```

```jsx
오늘은  undefined 요일입니다.
```

위 예제의 경우, today 변수 선언에도 호이스팅 현상이 일어났다. 이 때, 해당 변수의 선언을 호이스팅하는 것이지, 할당된 값까지 호이스팅하는 것은 아니다. 따라서 var 키워드로 선언된 변수는 호이스팅될 때 마치 사용자가 아무런 값도 할당하지 않은 것처럼 해석된다. var로 선언된 변수에 사용자가 초기화하지 않으면 자동으로 undefined라는 값으로 초기화된다. 따라서 위 예제의 경우 에러가 일어나지 않는 대신, undefined라는 값이 출력되는 것이다. 

let, const 키워드로 선언된 변수들도 그 선언부는 호이스팅된다. 다만, var 키워드로 선언된 변수는 초기화되지 않을 경우 자동으로 ‘undefined’란 값으로 초기화되는 대신, let, const 키워드로 선언된 변수들은 사용자가 따로 초기화를 하지 않으면 자동 초기화가 되지 않는다. 이로 인해 변수 선언부보다 호출부가 먼저 오게 되면 에러가 발생하게 되는 것이다. 위 예제 2-1에서 var를 let으로 바꾸고 다시 실행하면 에러가 발생한다. 이는 사실상 초기화되지 않은 변수를 호출한 것이나 마찬가지이기 때문이다. 

앞서 언급했듯, 변수 선언 뿐만 아니라 함수 선언도 호이스팅된다. 그래서 다음의 코드도 에러없이 정상적으로 동작한다. 

```jsx
hi(3);

function hi(num) {
    var i;
    for(i = 0; i < num; i++) {
        console.log('hi');
    }
}
```

```jsx
hi
hi
hi
```

# 변수 사용 가이드

- 전역 변수 선언 및 사용 횟수는 최소화한다. 
어느 함수에서도 사용 가능하므로, 코드가 복잡해짐에 따라 자신도 모르게 원치 않은 곳에서 전역 변수를 사용하여 그 값이 원치 않게 바뀔 수도 있다.
- var 키워드로 선언할 변수는 전역 변수의 경우 코드 최상단에, 함수 내부에서 선언할 경우 함수 블록 내 최상위 위치에 선언한다. 
호이스팅에 의해 만약 var 변수 선언부보다 호출부가 더 위에 있게 되면 ‘undefined’란 값으로 출력되기에 원치 않은 버그가 생길 수 있다.
- 반복문 내에서 반복 횟수를 세기 위한 변수는 반복문 바깥에서 그대로 사용할 것이 아니라면 반복문 내에서 let 키워드로 선언하도록 한다.

다음의 예제를 보자.

```jsx
function getTotal(n) {
    let total = 0;
    for(var i = 1; i <= n; i++) {
        total += i;
    }
    console.log(i);
    return total;
}

console.log(getTotal(100));
```

```jsx
101
5050
```

var 변수는 앞서 말했듯, 함수 scope에 존재하는 것이지 블록 scope에 제한되지 않는다. 따라서 위 예제의 경우 반복문을 빠져나가더라도 var 변수인 i 변수는 여전히 함수 내부에 있기에 접근 가능한 것이다. 

반복문 내에서 반복 횟수를 세는 용도의 변수는 되도록 let 키워드를 사용하여 외부에서 접근하지 못하도록 한다.

```jsx
function getTotal(n) {
    let total = 0;
    for(let i = 1; i <= n; i++) {
        total += i;
    }
    console.log(i);
    return total;
}

console.log(getTotal(100));
```

```jsx
ReferenceError: i is not defined
```

- var보단 let, const를 사용하자. 
이에 대한 이유는 앞서 이미 언급했다.



---
References

[1] 고경희 - Do it! 한 권으로 끝내는 웹 기본 교과서 HTML+CSS+자바스크립트 웹 표준의 정석

[2] [javascript에서의 var 이용 유무에 따른 변수 scope](https://wjheo.tistory.com/entry/javascript에서의-var-이용-유무에-따른-변수-scope)

[3] [Var, Let, Const의 차이점은?](https://www.freecodecamp.org/korean/news/var-let-constyi-caijeomeun/)

[4] [[모던 자바스크립트] var를 사용하지 않아야 하는 이유](https://bbubbush.tistory.com/47)

[5] 참고 자료 - 호이스팅

[[JavaScript] 호이스팅(Hoisting)이란? - 하나몬](https://hanamon.kr/javascript-호이스팅이란-hoisting/)

[6] [JS Execution Context (실행 컨텍스트) 란? \| 감구마 개발블로그](https://blog.gamguma.dev/post/2022/04/js_execution_context)