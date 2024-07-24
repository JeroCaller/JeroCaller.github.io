---
title: "[JS][Module] Module Import, Export"
category: "javascript"
tag: ["web", "js", "javascript", "module", "import", "export"]
---

# export 키워드 사용하기

외부 모듈에서 사용가능하도록 내보내고자 하는 변수, 함수, 클래스에 대해 export 키워드를 다음과 같이 사용할 수 있다. 

1. 선언부 앞에 직접 export 키워드를 붙인다. 

```jsx
export function sumOneToN(n) {
    let total = 0;
    for(let i = 0; i <= n; i++) {
        total += i;
    }
    return total;
}

export function fibonacci(n) {
    /*
        n : 0 1 2 3 4 5
        f : 0 1 1 2 3 5
    */
    let memo = [0, 1];

    function calc(num) {
        if (num == 0) {
            return 0;
        }
        if (num == 1) {
            return 1;
        }

        if(memo[num] != undefined) {
            return memo[num];
        }

        memo[num] = calc(num-1) + calc(num-2);
        return memo[num];
    }

    return calc(n);
}

```

예제 1-1

1. 선언부와는 분리된 모듈 맨 앞 또는 맨 뒤에 export {식별자명} 코드를 삽입한다. 

```jsx
export {sumOneToN, fibonacci};

function sumOneToN(n) {
    let total = 0;
    for(let i = 0; i <= n; i++) {
        total += i;
    }
    return total;
}

function fibonacci(n) {
    /*
        n : 0 1 2 3 4 5
        f : 0 1 1 2 3 5
    */
    let memo = [0, 1];

    function calc(num) {
        if (num == 0) {
            return 0;
        }
        if (num == 1) {
            return 1;
        }

        if(memo[num] != undefined) {
            return memo[num];
        }

        memo[num] = calc(num-1) + calc(num-2);
        return memo[num];
    }

    return calc(n);
}

// 코드 맨 뒤에 넣어도 된다.
// export {sumOneToN, fibonacci};
```

예제 1-2

위 두 방법 모두 원하는 자원을 export하는 동일한 방법들이다. 

# import로 가져오기

외부 모듈로부터 그 모듈 내에서 가져오고 싶은 게 있다면 다음과 같이 `import {식별자명} from ‘모듈경로’;` 형태로 사용하면 된다. 

```jsx
import {sumOneToN, fibonacci} from './export-ex.js';

console.log(sumOneToN(15));
console.log(fibonacci(15));
```

예제 2-1

만약 import할 모듈 내에 가져올 것들이 많거나 그냥 하나로 묶어 가져오고 싶을 경우, `import * as obj from ‘모듈경로’;` 문법을 사용하면 된다. 그러면 obj 객체 하나에 여러 개의 변수, 함수, 클래스 등을 가져올 수 있다. 그러면 `obj.resource` 처럼 obj 객체의 메서드 또는 프로퍼티로 사용할 수 있다. 

```jsx
import * as tools from './export-ex.js';

console.log(tools.sumOneToN(15));
console.log(tools.fibonacci(15));
```

예제 2-2

# 이름 바꿔 가져오거나 내보내기

import로 외부 모듈을 가져오거나 export로 내부 자원을 내보낼 경우, as 키워드를 사용하면 기존의 식별자명을 다른 이름으로 사용할 수 있다. 

먼저 import할 때의 as 키워드 사용 예이다.

```jsx
import {sumOneToN as sum, fibonacci as fibo} from './export-ex.js';

console.log(sum(20));
console.log(fibo(20));
```

예제 3-1

위와 같이 가져올 모듈 내부의 식별자 이름을 원하는 대로 바꿔 그 별명으로 사용 가능하다. 

또한 export에도 as를 사용할 수 있으며 이 때에는 원하는 이름으로 바꿔 외부로 내보내는 기능을 한다. 

```jsx
export {sumOneToN as sumN, fibonacci as fibo};

function sumOneToN(n) {
    let total = 0;
    for(let i = 0; i <= n; i++) {
        total += i;
    }
    return total;
}

function fibonacci(n) {
    /*
        n : 0 1 2 3 4 5
        f : 0 1 1 2 3 5
    */
    let memo = [0, 1];

    function calc(num) {
        if (num == 0) {
            return 0;
        }
        if (num == 1) {
            return 1;
        }

        if(memo[num] != undefined) {
            return memo[num];
        }

        memo[num] = calc(num-1) + calc(num-2);
        return memo[num];
    }

    return calc(n);
}

```

예제 3-2. export-ex.js

export에서 as 키워드를 이용하여 다른 이름으로 바꾼 경우, 이를 import하는 쪽에서는 해당 이름을 그대로 사용하면 된다.

```jsx
import {sumN, fibo} from './export-ex.js';

console.log(sumN(13));
console.log(fibo(13));
```

예제 3-3

# export default

모듈 구성 시 두 방법이 있다. 

- 여러 개의 개체(변수, 함수 또는 클래스)들이 선언되어 있는 라이브러리 형태의 모듈
- 단 하나의 개체만 선언되어 있는 해당 개체 전용 모듈.

후자의 경우, export 키워드 바로 뒤에 default 키워드를 붙여 `export default` 로 사용하면 해당 개체는 해당 모듈에 단 하나만 있다는 것을 명시해주는 역할을 한다. 

이러한 특성때문에, export default는 파일 하나에 단 하나만 쓸 수 있다. 

이렇게 단 하나의 개체만 선언되어 있어 export default 키워드를 사용하는 모듈의 export를 default export라 한다. 반대로, 하나의 모듈에 여러 개의 개체들이 선언된 경우, 이 모듈을 export하는 것을 named export라 한다. 사실 앞서 살펴본 예제들이 모두 named export였다. 

다음은 export default를 사용하는 예제이다.

```jsx
export default class DataContainer {
    constructor(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }

    printDataInfo() {
        console.log("====== 사용자 정보 ======");
        console.log(`이름: ${this.name}`);
        console.log(`나이: ${this.age}`);
        console.log(`직업: ${this.job}`);
    }
}
```

예제 4-1

default export를 사용하는 모듈에는 어차피 단 하나의 개체만 정의되어 있으므로 그 이름을 생략해도 된다. 다음이 그 예이다. 

```jsx
export default class {
    constructor(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }

    printDataInfo() {
        console.log("====== 사용자 정보 ======");
        console.log(`이름: ${this.name}`);
        console.log(`나이: ${this.age}`);
        console.log(`직업: ${this.job}`);
    }
}
```

예제 4-2

앞선 예제 4-1과 거의 동일하나 이번 예제에는 class 키워드 다음에 아무런 이름이 없는 것을 볼 수 있다. 

이러한 default export를 import하여 사용하는 모듈에서는 import 키워드 다음에 중괄호를 쓰지 않아도 된다. 

```jsx
import DataContainer from './info.js';
import User from './info-2.js';

let me = new DataContainer('자바스', 20, '웹 개발자');
me.printDataInfo();

let you = new User('파이썬', 25, '데이터 분석가');
you.printDataInfo();

```

예제 4-3

```jsx
====== 사용자 정보 ======
이름: 자바스
나이: 20
직업: 웹 개발자
====== 사용자 정보 ======
이름: 파이썬
나이: 25
직업: 데이터 분석가
```

예제 4-3 실행결과

또한, 위 예제 4-3을 보면 알 수 있듯, 이름을 생략한 default export 모듈을 가져올 때에는 가져올 개체의 이름을 마음대로 지정할 수 있다는 장점이 있다. 위 예제에서는 이를 User란 이름으로 가져오고 있다. 반면 named export를 가져올 때에는 as 키워드 없이는 마음대로 이름을 바꿔서 가져올 수는 없다. 

export 뒤에 default가 없다면 개체 이름을 생략해선 안된다. 

named export를 import하는 모듈에서는 앞서 보았듯, 중괄호를 생략할 수 없다. 

# default

```jsx
export default class User {...}
```

위 형태 대신 다음의 형태로도 사용할 수 있다. 

```jsx
class User {...}

export {User as default};
```

하나의 모듈에 default export 하나와 여러 개의 named export가 있을 수도 있다. 

```jsx
export default class UserInfo {
    constructor(address, menu) {
        this.address = address;
        this.menu = menu;
    }
}

export function printDeliverInfo(userObj) {
    try {
        console.log("=== 배달 정보 ===");
        console.log("주소 : " + userObj.address);
        console.log("메뉴 : " + userObj.menu);
    } catch (e) {
        console.log("배달 주문 내역을 받아오지 못했습니다.");
    }
}
```

예제 5-1. deliver-tools.js

이 경우, 해당 모듈을 import 하는 모듈에서는 default export와 named export를 구분하여 가져오려면 다음의 형태로 가져오면 된다. 

```jsx
import {default as UserInfo, printDeliverInfo} from './deliver-tools.js';

printDeliverInfo(new UserInfo(
    "XX도 OO시 OO로 OOO동 OOO호", ['뿌링클순살', '프라이']
));

```

예제 5-2

```jsx
=== 배달 정보 ===
주소 : XX도 OO시 OO로 OOO동 OOO호
메뉴 : 뿌링클순살,프라이
```

예제 5-2 실행결과

만약 `import * as obj from ‘…’;`  형태로 가져올 경우, default export를 사용하려면 `obj.default` 프로퍼티로 호출하면 된다. 

```jsx
import * as deliver from './deliver-tools.js';

let User = deliver.default;

deliver.printDeliverInfo(new User(
    "XX도 OO시 OO로 OOO동 OOO호", ['뿌링클순살', '프라이']
));

```

예제 5-3

# re-export : 모듈 다시 내보내기

`export {…} from ‘…’;` 문법을 이용하면 외부 모듈로부터 가져온 개체를 바로 다시 내보낼 수가 있다. 다음은 그 예이다. 

먼저, 패키지 구성은 다음과 같다.

```
use-index.js
    /mylibs
	      index.js
	      math-tools.js
	      print-tool.js
```

각각의 코드는 다음과 같다.

```jsx
export {sumOneToN, fibonacci, mydata};

function sumOneToN(n) {
    let total = 0;
    for(let i = 0; i <= n; i++) {
        total += i;
    }
    return total;
}

function fibonacci(n) {
    /*
        n : 0 1 2 3 4 5
        f : 0 1 1 2 3 5
    */
    let memo = [0, 1];

    function calc(num) {
        if (num == 0) {
            return 0;
        }
        if (num == 1) {
            return 1;
        }

        if(memo[num] != undefined) {
            return memo[num];
        }

        memo[num] = calc(num-1) + calc(num-2);
        return memo[num];
    }

    return calc(n);
}

let mydata = {
    'name': '자바스',
    'age': 20,
    'job': '웹 개발자'
};
```

mylibs/math-tools.js

```jsx
export default function print(arg) {
    console.log(arg);
}
```

mylibs/print-tool.js

```jsx
export {sumOneToN, fibonacci, mydata} from './math-tools.js';
export {default as print} from './print-tool.js';

```

mylibs/index.js

```jsx
import * as tools from './mylibs/index.js';

tools.print(tools.sumOneToN(10));
tools.print(tools.fibonacci(5));
tools.print(tools.mydata);

```

use-index.js

```jsx
55
5
{ name: '자바스', age: 20, job: '웹 개발자' } 
```

use-index.js 출력 결과

만약 위와 같이 어떤 자바스크립트 패키지를 만들었다고 할 때, 이를 사용하는 개발자가 해당 패키지 내부 아무 곳이나 접근하여 내부 구조나 내용이 망가지는 것을 방지하고자 한다면 index.js 파일을 만들고 이 안에서 내보낼 모듈들을 다시 내보내도록 하여 외부에 공개할 자료들만 index.js에 공개하는 형식으로 사용할 수 있다. 

default export를 다시 내보내기할 때 주의할 점은 다음과 같다. 

- `export identifier from ‘모듈경로’;` 와 같이 사용하면 에러가 발생하므로 반드시 `export {default as identifier} from ‘모듈경로’;` 형태로 사용한다.
- `export * from ‘모듈경로’;` 형태로 모든 개체들을 하나로 묶어 다시 내보내기하면 default export는 무시되고 named export만 다시 내보내기가 진행된다. 이 경우 둘 다 내보내려면 각각 따로 re-export해줘야 한다. 다음과 같이 말이다.

```jsx
export * from '모듈경로';
export {default as identifier} from '모듈경로';
```

이렇게 두 구문을 사용해야 한다. 

---

References

[1] [모듈 내보내고 가져오기](https://ko.javascript.info/import-export)