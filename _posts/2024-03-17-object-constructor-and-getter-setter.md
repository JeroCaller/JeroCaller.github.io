---
title: "[JS] 객체 (2)"
category: "javascript"
tag: ["web", "js", "javascript", "object"]
---

# 생성자 함수 (Constructor function)

어떤 사이트에 가입한 유저를 추상화하여 이를 객체 리터럴로 다음과 같이 정의하였다. 

```jsx
let user = {
    nickname: "bowWow2024",
    pointMoney: 1000,
    printProfile() {
        console.log("===== 사용자 프로필 =====");
        console.log(`닉네임 : ${this.nickname}`);
        console.log(`보유 포인트 : ${this.pointMoney}`);
        console.log("=======================");
    },
};
```

그런데 위와 같이 프로퍼티의 키와 메서드는 같고, 프로퍼티의 값만 다른 유사한 객체를 여러 개 원한다면 어떻게 해야 할까? 

```jsx
let user = {
    nickname: "bowWow2024",
    pointMoney: 1000,
    printProfile() {
        console.log("===== 사용자 프로필 =====");
        console.log(`닉네임 : ${this.nickname}`);
        console.log(`보유 포인트 : ${this.pointMoney}`);
        console.log("=======================");
    },
};

let user2 = {
    nickname: "good",
    pointMoney: 2000,
    printProfile() {
        console.log("===== 사용자 프로필 =====");
        console.log(`닉네임 : ${this.nickname}`);
        console.log(`보유 포인트 : ${this.pointMoney}`);
        console.log("=======================");
    },
};
```

객체 리터럴만 이용한다면 위와 같이 또 다른 객체 리터럴로 비슷하게 정의하는 수밖에 없다. 그런데 이러한 유사한 객체가 100개, 200개 필요하다면? 위와 같은 방식으로는 그 만큼의 객체를 정의해야한다. 그러면 코드가 중복되어 길이가 쓸데없이 늘어난다. 이렇게 말고, 객체를 하나만 정의하여, 유사한 객체들을 여러 개 생성하여 사용하는 방식이 좋을 것이다. 자바스크립트에서는 그 방법 중 하나로 생성자 함수(constructor function)라는 것을 이용할 수 있다. 

생성자 함수는 일반 함수와 거의 동일하다. 그러나 생성자 함수는 그 이름을 지을 때 첫 글자는 대문자로 시작해야 하며, 생성자 함수로 “객체”를 생성하고자 할 때에는 반드시 new 연산자를 사용해야 한다. 다음은 생성자 함수를 정의하고 이로부터 2개의 객체를 생성하는 예제이다.

```jsx
function User(nickName, pointMoney) {
    // this = {}; 빈 객체 임시 생성.

    // 객체 프로퍼티 정의
    this.nickName = nickName;
    this.pointMoney = pointMoney;

    // 메서드 정의
    this.printProfile = function() {
        console.log("===== 사용자 프로필 =====");
        // this는 메서드 호출 시 해당 메서드가 속한 객체를 의미.
        console.log(`닉네임 : ${this.nickName}`);
        console.log(`보유 포인트 : ${this.pointMoney}`);
        console.log("=======================");
    };

    // return this;  this가 암묵적으로 반환됨.
};

// 생성자 함수에 new 키워드를 이용하여 객체 생성 및 변수에 할당.
let user1 = new User("bowWow2024", 1000);
let user2 = new User("Hamburger11", 2000);

user1.printProfile();
user2.printProfile();
```

```jsx
===== 사용자 프로필 =====
닉네임 : bowWow2024
보유 포인트 : 1000
=======================
===== 사용자 프로필 =====
닉네임 : Hamburger11
보유 포인트 : 2000
=======================
```

위 예제와 더 위에서 객체 리터럴로 비슷한 객체를 2번이나 정의하는 방식과 비교해보면, 생성자 함수 방식이 훨씬 더 코드 중복을 줄여 효율적인 방식임을 알 수 있다. 

위 예제의 생성자 함수 내부에 주석으로 적어놓았듯, `new 생성자함수명()` 방식으로 해당 함수를 실행시키면 내부적으로 `this = {}`으로 빈 객체를 생성하고 이를 this에 할당한다. 그 다음 사용자가 명시한 코드를 실행한 후, 맨 마지막에는 this를 암묵적으로 반환한다. 이러한 원리로 생성자 함수를 new 키워드로 사용하면 객체를 생성받을 수 있는 것이다. 

모든 함수는 호출 시 그 앞에 new 키워드를 붙이면 무조건 위와 같은 과정을 거쳐 객체를 생성한다는 것에 유의하라. 

생성자 함수 내부에서, 명시적으로 return을 사용하는 경우, return {}와 같이 객체를 반환하면 this 대신 해당 객체가 반환된다. 이 외의 것을 반환시키면 이를 무시하고 this를 반환한다. 

# getter, setter

객체 프로퍼티에는 데이터 프로퍼티(data property)와 접근자 프로퍼티(accessor property)로 나뉜다. 데이터 프로퍼티는 일반적인 객체 프로퍼티 키:값 쌍을 의미하며, 단순히 데이터를 프로퍼티 키에 저장할 때 쓰이는 프로퍼티이다. 반면, 접근자 프로퍼티는 getter, setter 메서드 형태로, 값을 반환하거나 설정할 때 쓰이는데, 외부에서는 메서드가 아닌 데이터 프로퍼티처럼 접근할 수 있다는 특징을 가지고 있다. 

객체 내에서 getter, setter 메서드를 정의하는 방법은 get, set 키워드를 메서드명 앞에 적은 후, 일반적인 메서드를 정의하는 것과 같이 사용하면 된다. 

```jsx
let user = {
    get userID() {
        return this._nickName;
    },
    set userID(value) {
        if ((value.length < 3 || value.length > 15) || (!/^[a-zA-Z0-9]+$/.test(value))) {
            console.log("아이디는 4글자 이상, 15글자 이하의 영문, 숫자 조합이어야 합니다.");
            return;
        }
        this._nickName = value;
    }
};

user.userID = "aaae1a2";  // setter 접근.
console.log(user.userID);  // getter 접근.
```

```jsx
aaae1a2
```

접근자 프로퍼티는 객체 내부에서는 메서드 형태로 정의되나, 외부에서 접근할 때에는 데이터 프로퍼티처럼 접근하는 특성을 가진다. 그러면 왜 굳이 데이터 프로퍼티로 정의하지 않고, 접근자 프로퍼티로 사용하는지에 대해 의문이 생길 것이다. 사실 이는 위 예제에서 어느 정도 설명해주고 있다. 접근자 프로퍼티를 사용하면, 

1. 프로퍼티 값에 접근하거나 설정할 때 동시에 어떤 특정 기능을 처리하도록 하는 코드를 함께 추가할 수 있다. 위 예제에서는 userID를 설정할 때 문자열이 항상 특정 글자수 범위 이내여야 하고, 영문자 및 숫자로만 구성되어야 하도록 강제하고자 할 때 이를 수행하는 if문을 넣어 검사하는 기능을 추가하였다. 
2. 객체 내부의 프로퍼티가 외부에서 함부로 접근하지 못하도록 할 때에 유용하다. 위 예제에서는 userID 접근자를 통해 값을 할당받는 실제 프로퍼티 키는 `_nickName`이다. 그러나 외부에서는 해당 프로퍼티 키에 직접 접근하지 않고 userID를 통해 간접적으로 접근하도록 하고 있다. userID를 설정할 때 if문 검사를 반드시 통과하도록 설계하고자 한다면 외부에서 `_nickName`이 직접 접근되면 안되기에 위와 같이 설계한 것이다. 물론, `user._nickName`과 같이 외부에서 직접 접근이 가능하긴 하지만, 프로퍼티 이름 앞에 언더스코어(_) 기호가 있으면 관례적으로 외부에서 접근하지 않고 객체 내부에서만 직접 접근하여 사용하도록 한다. 

getter, setter 메서드로 구현된 프로퍼티는 가상으로 생성되는 것으로, 실제 존재하는 프로퍼티는 아니다. 

getter 메서드는 아무런 매개변수를 받지 않고, setter 메서드는 단 한 개의 매개변수만 받는다. 

참고로, `Object.defineProperty(객체, 프로퍼티 키명, 프로퍼티 내용)` 함수를 통해서도 프로퍼티를 설정할 수 있다. 

```jsx
function User() {
    Object.defineProperty(this, "userID", {
        get() {
            return this._nickName;
        },
        set(value) {
            if ((value.length < 3 || value.length > 15) || (!/^[a-zA-Z0-9]+$/.test(value))) {
                console.log("아이디는 4글자 이상, 15글자 이하의 영문, 숫자 조합이어야 합니다.");
                return;
            }
            this._nickName = value;
        }
    });
}

let user1 = new User();
user1.userID = "aaae1a2#";
console.log(user1.userID);
```

위 방식을 이용할 때, 프로퍼티는 접근자와 데이터 프로퍼티 중 하나만 될 수 있다는 사실에 유의해야한다. 프로퍼티 정의 블록 내부에 getter 또는 setter 메서드와 데이터 프로퍼티를 함께 정의하면 에러가 발생하니 주의해야 한다. 

---

References

[1] [new 연산자와 생성자 함수](https://ko.javascript.info/constructor-new)
