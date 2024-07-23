---
title: "[JS] 객체 : 직접 정의하고 사용하기"
category: "javascript"
tag: ["web", "js", "javascript", "object"]
---

# 객체 리터럴

자바스크립트에서 사용자가 객체를 정의하는 방법 중 하나로 객체 리터럴(object literal) 방식이 있다. 중괄호로 정의하며, 그 안에 키-값 쌍의 프로퍼티들을 선언하는 방식이다. 

```jsx
// 객체 리터럴 문법으로 객체 생성.
let siteUser = {
    nickname: "sunandstar11",
    age: 25,
    job: "IT dev",
    "is VIP": true,  // 프로퍼티 키 이름에 띄어쓰기를 쓰고자 한다면 반드시 따옴표로 감싸야함.
    something: "good", 
    // 객체 내 메서드 정의. 
    getAdd: function(a, b) {
        return a + b;
    },
};

// 객체의 프로퍼티 접근.
console.log(siteUser.nickname);
// 프로퍼티 키의 이름에 띄어쓰기가 되어있다면 접근 시 대괄호와 그 안에 따옴표를 이용하여 접근.
// 이러한 접근법을 "대괄호 표기법(square bracket notation)"이라 한다. 
console.log(siteUser["is VIP"]);

siteUser["age"] = 26; // 대괄호 표기법으로 객체 내 프로퍼티에 접근하여 그 값을 변경시킬 수 있다.
console.log(siteUser["age"]); 

// 객체에 새로운 프로퍼티 추가.
siteUser.moneyPoint = 1000;
console.log(siteUser.moneyPoint);

// 객체 내 프로퍼티 삭제.
delete siteUser.something;
console.log(siteUser.something);

// 객체 내 메서드 호출.
console.log(siteUser.getAdd(1, 2));
```

```jsx
sunandstar11
true
26
1000
undefined
3
```

# 상수 객체의 프로퍼티 수정

```jsx
const siteUser = {
    nickname: "sunandstar11",
    age: 25,
    job: "IT dev",
    "is VIP": true,
    something: "good",  
    getAdd: function(a, b) {
        return a + b;
    },
};

siteUser.nickname = "moon";
console.log(siteUser.nickname);

// 에러.
// siteUser = 12;
// console.log(siteUser);
```

```jsx
moon
```

객체를 할당받는 변수를 상수로 선언해도 해당 객체의 프로퍼티의 값을 수정할 수 있다. 상수에 대입된 객체 자체가 다른 값으로 변경되려고 할 때 에러가 발생한다. 

# 계산된 프로퍼티 (Computed property)

```jsx
let someVarName = "good";
let someObj = {
    [someVarName]: "bad",  // 계산된 프로퍼티.
};

console.log(someObj.good);
```

```jsx
bad
```

계산된 프로퍼티는 객체 리터럴로 생성된 객체 내 프로퍼티 키를 대괄호로 둘러싸는 것을 의미한다. 

위 예제에서는 그 전에 정의된 변수 someVarName을 이용하여 계산된 프로퍼티로 사용하였다. 이 때 `[someVarName]`에는 해당 변수에 대입되어 있는 “good”이 사실상 프로퍼티의 키가 된다. 그래서 최종적으로 good: “bad”가 된다. 이렇게 대괄호 내부의 변수명에서 해당 변수에 할당된 값으로 변경되는 과정이 “계산된다”로 보여서 붙여진 이름으로 추측된다. 

# in 연산자 : 객체 내 프로퍼티 존재 여부 확인

```jsx
let siteUser = {
    nickname: "sunandstar11",
    age: 25,
    job: "IT dev",
    "is VIP": true,
    something: "good", 
    getAdd: function(a, b) {
        return a + b;
    },
};

console.log("age" in siteUser);
console.log("a" in siteUser);
let strVar = "job";
console.log(strVar in siteUser);
```

```jsx
true
false
true
```

객체 리터럴 내 프로퍼티 키 존재 여부를 알고자 한다면 위 코드에서 볼 수 있듯 in 키워드를 이용하여 다음의 형태로 사용하면 된다.

```jsx
"key" in obj
```

# 반복문으로 객체 내 프로퍼티 키 순회

for - in 반복문을 이용하여 객체 내 모든 프로퍼티 키들을 순회할 수 있다. 

```jsx
for (let prop in obj) {
    // 코드.
}
```

위 형태에서 let prop는 let을 이용하여 prop 변수를 선언한 것이다. 해당 변수는 for 반복문을 빠져나가면 사용할 일이 없기에 let으로 선언한 것이다. 해당 변수는 반복문을 통해 obj 객체 내 프로퍼티 키를 하나씩 받고, 블록 부분에서 이를 사용하는 방식이다. 

다음은 위 문법을 이용하여 한 객체 내 모든 키를 순회하는 예제이다. 

```jsx
let siteUser = {
    nickname: "sunandstar11",
    age: 25,
    job: "IT dev",
    "is VIP": true,
    something: "good", 
    getAdd: function(a, b) {
        return a + b;
    },
};

for (let prop in siteUser) {
    console.log(`${prop} : ${siteUser[prop]}`);
}
```

```jsx
nickname : sunandstar11
age : 25
job : IT dev
is VIP : true
something : good
getAdd : function(a, b) {
        return a + b;
    }
```

# 객체 리터럴 내 프로퍼티 정렬 방식

앞서 소개한 for 반복문을 이용하여 객체 리터럴 내 프로퍼티들을 모두 순회할 때, 사용자가 프로퍼티들을 정의한 순서대로 순회될까? 

정수 프로퍼티(integer property)의 경우에는 오름차순으로 정렬되고, 그 외의 프로퍼티들은 사용자가 객체에 정의, 추가한 순으로 정렬된다. 

정수 프로퍼티는 정수형에서 문자열형으로, 또는 그 반대로 형변환을 할 때 내용이 변경되지 않는 문자열을 말한다. 문자열 “11”을 정수형으로 바꿔도 11로 그 내용에는 변함이 없다. 그 역순도 마찬가지이다. 그런데 “+11” 문자열을 정수형으로 바뀌면 11로 바뀌며, “+” 문자가 사라진다. 이 경우 해당 문자열은 정수 프로퍼티가 될 수 없다. 

다음은 정수 프로퍼티로만 이루어진 객체와 그 외의 프로퍼티로 이루어진 객체를 각각 순회할 때의 결과를 보기 위한 예제이다.

```jsx
let rankNum = {
    "3": "duck",
    "5": "lion",
    "1": "tiger",
    "12": "snake",
    "2": "cat",
};

let rankAlp = {
    b: "elephant",
    e: "giraffe",
    d: "dog",
    a: "crocodile",
};

function printAndIterObj(obj) {
    for (let prop in obj) {
        console.log(`${prop} : ${obj[prop]}`);
    }
}

printAndIterObj(rankNum);
console.log("==========");
printAndIterObj(rankAlp);
```

```jsx
1 : tiger
2 : cat
3 : duck
5 : lion
12 : snake
==========
b : elephant
e : giraffe
d : dog
a : crocodile
```

위 예제를 보면, 정수 프로퍼티들로 구성된 객체에서는 프로퍼티 순을 일부로 랜덤으로 정렬하여 선언하였다. 그럼에도 출력결과에서는 오름차순으로 정렬되어 출력됨을 알 수 있다. 반면 그 외의 프로퍼티들은 객체에 정의된 순서대로 출력됨을 알 수 있다. 

# 메서드

자바스크립트에서 객체 리터럴 내 메서드를 다음과 같은 방법으로 정의할 수 있다. 

```jsx
let user = {
    nickname: "bowWow2024",
    pointMoney: 1000,
    // 일반적인 메서드 정의 방법. (함수 표현식)
    getMinus: function(a, b) {
        return a - b;
    },
    // 메서드 단축 구문으로 메서드 정의.
    getAdd(a, b) {
        return a + b;
    },
};

// user 객체 내 메서드 정의 및 printProfile 프로퍼티에 할당.
user.printProfile = function() {
    console.log("===== 사용자 프로필 =====");
    // this는 메서드 호출 시 해당 메서드가 속한 객체를 의미.
    console.log(`닉네임 : ${this.nickname}`);
    console.log(`보유 포인트 : ${this.pointMoney}`);
    console.log("=======================");
};

function getSumOneToN(n) {
    let total = 0;
    for(let i = 1; i <= n; i++) {
        total += i;
    }
    return total;
}

// 이미 선언된 함수를 객체의 메서드로 할당 가능.
user.getSumOneToN = getSumOneToN;

user.printProfile();
console.log(user.getAdd(3, 4));
console.log(user.getMinus(3, 4));
console.log(user.getSumOneToN(user.pointMoney));
```

```jsx
===== 사용자 프로필 =====
닉네임 : bowWow2024
보유 포인트 : 1000
=======================
7
-1
500500
```

---

References

[1] [객체](https://ko.javascript.info/object)

[2] [메서드와 this](https://ko.javascript.info/object-methods)