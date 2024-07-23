---
title: "[JS] JS 내에서 JSON 다루기"
category: "javascript"
tag: ["web", "js", "javascript", "JSON"]
---

자바스크립트에서도 JSON을 다룰 수 있다. 자바스크립트에서는 `JSON`이라는 객체를 이용하여 JSON을 다룬다. 

JSON에 대한 내용 및 문법은 [JSON 개요](/json/json-introduction/),  [JSON 문법](/json/json-syntax/) 페이지 참조. 여기서는 JSON 자체보다는 자바스크립트 내에서 JSON을 다루는 방법에 대해 살펴볼 것이다. 

# stringify

자바스크립트의 JSON 객체의 `stringify()` 메서드는 자바스크립트 내에서 사용되는 여러 자료형들의 값을 JSON으로 변경(직렬화)해주는 메서드이다. 해당 메서드 사용법은 다음과 같다.

```jsx
JSON.stringify(value[, replacer, space]);
```

위 코드에서 2, 3번째 인자는 생략 가능하다. 

다음은 자바스크립트 객체 리터럴을 JSON으로 직렬화하는 코드이다. 

```jsx
let myInfo = {
    nickname: "good",
    age: 27,
    isYourself: true,
    langs: ['java', 'python', 'html', 'css', 'js'],
};

let json = JSON.stringify(myInfo);

console.log(typeof json);
console.log(json);
```

```jsx
string
{"nickname":"good","age":27,"isYourself":true,"langs":["java","python","html","css","js"]}
```

`JSON.stringify()` 메서드의 첫 번째 인자로 직렬화하고자 하는 객체를 대입하니 json 변수에 JSON으로 인코딩된 문자열이 대입되었다. 이 문자열을 JSON-encoded(JSON으로 인코딩된) 또는 serialized(직렬화된) 객체라고 불린다. 

한 편, `stringify()` 함수로는 직렬화되지 못하는 자바스크립트 프로퍼티는 다음이 있다. 

- 함수 또는 메서드
- 값이 undefined인 프로퍼티

다음의 예제를 보면 이를 확인할 수 있다. 

```jsx
class User {
    constructor(nickname, age, langs) {
        this.nickname = nickname;
        this.age = age;
        this.langs = langs;
        this.isYourself = true;
    }

    printProfile() {
        console.log("====== 사용자 정보 ======");
        console.log("사용자 ID : " + this.nickname);
        console.log("나이 : " + this.age);
        console.log("프로그래밍 언어 : " + this.langs);
        console.log("본인 인증 : " + this.isYourself);
        console.log("========================");
    }
}

let myInfo = new User("good123", 27, ['java', 'python', 'html', 'css', 'js']);
let json = JSON.stringify(myInfo, null, 2);
console.log(json);
```

```jsx
{
  "nickname": "good123",
  "age": 27,
  "langs": [
    "java",
    "python",
    "html",
    "css",
    "js"
  ],
  "isYourself": true    
}
```

위 예제를 보면, User 클래스 내부에 선언된 멤버 변수들은 모두 JSON으로 인코딩된 것을 볼 수 있으나, 해당 클래스 내부의 메서드 `printProfile()`은 인코딩되지 않고 무시된 것을 볼 수 있다. 

## replacer로 특정 프로퍼티만 직렬화하기

`stringify()` 메서드의 두 번째 매개변수인 replacer에는 JSON으로 변환하고자 하는 프로퍼티명이 담긴 배열 또는 funtion(key, value) 형태의 매핑 함수를 대입할 수 있다. 즉, 이를 통해 모든 프로퍼티들 중 일부만 JSON으로 인코딩할 수 있다. 

다음은 replacer를 이용하여 객체 리터럴 내 일부 프로퍼티들만 JSON으로 직렬화하는 예제이다. 

```jsx
let myInfo = {
    nickname: "good",
    age: 27,
    isYourself: true,
    langs: ['java', 'python', 'html', 'css', 'js'],
};

let json = JSON.stringify(myInfo, ['nickname', 'age']);

console.log(json);
```

```jsx
{"nickname":"good","age":27}
```

위 예제를 보면 stringify 메서드의 두 번째 인자로 JSON으로 인코딩되길 원하는 프로퍼티명의 배열을 입력하였다. 그 결과, nickname, age 프로퍼티들만 JSON으로 인코딩된 것을 확인할 수 있다. 

그런데 replacer 매개변수에 배열 형태로 입력할 때, 직렬화하고자 하는 프로퍼티들이 너무 많으면 그 배열도 길어질 것이다. 이 경우에는 매핑 함수를 고려해볼 수 있다. 다음은 replacer 매개변수에 매핑함수를 대입하여 여러 프로퍼티들 중 단 하나의 프로퍼티만 제외하고 나머지들을 JSON으로 인코딩하는 예제이다.

```jsx
let myInfo = {
    nickname: "good",
    age: 27,
    isYourself: true,
    langs: ['java', 'python', 'html', 'css', 'js'],
};

let json = JSON.stringify(myInfo, function replacer(key, value) {
    return (key == 'isYourself') ? undefined : value;
});

console.log(json);
```

```jsx
{"nickname":"good","age":27,"langs":["java","python","html","css","js"]}
```

위 예제에서는 매핑 함수 내부에서 프로퍼티명이 isYourself인 경우 해당 프로퍼티 값을 undefined로 설정하고, 나머지는 프로퍼티 값 그대로 반환하도록 고안되었다. 앞서 stringify로 프로퍼티들이 JSON 직렬화될 때, 프로퍼티 값이 undefined인 경우에는 직렬화되지 않고 무시된다고 하였다. 바로 이 점을 이용하여 isYourself 프로퍼티만 직렬화되지 않도록 설계한 것이다. 실제로 위 실행결과에서도 isYourself 프로퍼티만 빠진 것을 확인할 수 있다. 매핑 함수를 이용하면 원래 프로퍼티의 값을 다른 값으로 변환시켜 이를 직렬화할 수도 있고, 직렬화 과정에서 어떤 기능을 처리하도록 활용할 수도 있을 것이다. 

## space로 좀 더 읽기 쉽게

`JSON.stringify()` 메서드의 3번째 인자 space는 직렬화된 JSON 문자열을 더 읽기 편하게 하기 위해 각 줄마다 삽입할 공백 문자 수를 설정하는 곳이다. 원래 이 인자를 생략한 후에 JSON 문자열 객체를 출력해보면 프로퍼티들 간에 공백없이 한 줄로 출력된다. 만약 데이터를 전달하는 목적으로만 사용할 것이라면 이 space 인자는 필요없겠지만, 만약 JSON 데이터를 사람이 읽어야 할 일이 생긴다면 이 인자를 설정하여 좀 더 데이터를 읽기 편하게 설정할 수 있다. 

다음은 space 인자를 설정하지 않았을 때와 설정한 후의 JSON 데이터를 출력하도록 하는 예제 코드이다.

```jsx
let myInfo = {
    nickname: "good",
    age: 27,
    isYourself: true,
    langs: ['java', 'python', 'html', 'css', 'js'],
};

let json1 = JSON.stringify(myInfo);
let json2 = JSON.stringify(myInfo, null, 2);
console.log(json1);
console.log(json2);
```

```jsx
{"nickname":"good","age":27,"isYourself":true,"langs":["java","python","html","css","js"]}
{
  "nickname": "good",
  "age": 27,
  "isYourself": true,
  "langs": [
    "java",
    "python",
    "html",
    "css",
    "js"
  ]
}
```

## toJSON

만약 객체 내에 toJSON 메서드가 구현되어 있다면 이 객체를 `stringify()` 메서드를 통해 인코딩할 때, toJSON 메서드가 호출되어 그 메서드의 반환값을 인코딩해준다. 

다음은 사용자 정의 클래스 내부에 toJSON 메서드를 정의하여 여러 멤버 변수들 중 특정 변수만을 반환하게 하여 해당 프로퍼티의 값만 JSON으로 인코딩하도록 하는 예제이다. 

```jsx
class User {
    constructor(nickname, age, langs) {
        this.nickname = nickname;
        this.age = age;
        this.langs = langs;
        this.isYourself = true;
    }
}

class UserJson {
    constructor(nickname, age, langs) {
        this.nickname = nickname;
        this.age = age;
        this.langs = langs;
        this.isYourself = true;
    }

    toJSON() {
        return this.nickname;
    }
}

let me = new User("good123", 27, ["python", "js", "java", "html", "css"]);
let meToJson = new UserJson("good123", 27, ["python", "js", "java", "html", "css"]);

console.log(JSON.stringify(me));
console.log(JSON.stringify(meToJson));
```

```jsx
{"nickname":"good123","age":27,"langs":["python","js","java","html","css"],"isYourself":true}
"good123"
```

# parse

`JSON.parse()` 메서드를 이용하여 JSON으로 인코딩된 문자열을 다시 자바스크립트 내에서 사용되는 값으로 디코딩할 수 있다. `JSON.stringify()` 의 반대 과정을 수행하는 메서드라 보면 되겠다. 해당 메서드의 문법은 다음과 같다. 

```jsx
JSON.parse(str, [reviver]);
```

다음은 JSON 문자열을 JSON.parse 메서드를 이용하여 디코딩하는 예제이다. 

```jsx
let myInfoJson = `{
    "nickname": "good",
    "age": 27,
    "isYourself": true,
    "langs": ["java", "python", "html", "css", "js"]
}`;

let toJS = JSON.parse(myInfoJson);

console.log(typeof toJS);
console.log(toJS);
```

```jsx
object
{
  nickname: 'good',
  age: 27,
  isYourself: true,
  langs: [ 'java', 'python', 'html', 'css', 'js' ]
}
```

## reviver로 디코딩 통제하기

다음의 JSON 문자열이 있다. 

```jsx
let myInfoJson = `{
    "nickname": "good",
    "age": 27,
    "isYourself": true,
    "langs": ["java", "python", "html", "css", "js"]
}`;
```

위 JSON 문자열을 디코딩하여 이를 myInfo라는 변수에 할당한 후, 자바스크립트에 내에서 사용할 때, myInfo.langs 에 접근할 때에는 langs가 어떤 객체이고, 프로그래밍 언어들을 보기 좋은 형태로 출력해주는 `printLangs()` 메서드에 접근하여 이를 출력하고자 한다. 즉, 다음의 출력 결과가 나오길 바란다. 

```jsx
let myInfo = JSON.parse(myInfoJson);
myInfo.langs.printLangs();
```

```jsx
=== 사용 프로그래밍 언어 ===
[ 'java', 'python', 'html', 'css', 'js' ]
===========================
```

위와 같이 접근하면 아래와 같은 출력결과가 나오길 원한다. 그런데 위 코드는 사실 에러가 발생한다. 애초에 langs에는 `[ 'java', 'python', 'html', 'css', 'js' ]` 의 배열 형태로 존재하며, 당연히 해당 값에는 printLangs() 메서드는 존재하지 않기 때문이다. 

그러나 `JSON.parse()` 메서드의 두 번째 매개변수인 reviver를 이용하면 JSON 문자열을 디코딩할 때 특정 프로퍼티의 값을 원하는대로 변형시킬 수 있다. 다음은 위 요구사항을 반영한 예제이다. 

```jsx
let myInfoJson = `{
    "nickname": "good",
    "age": 27,
    "isYourself": true,
    "langs": ["java", "python", "html", "css", "js"]
}`;

let myInfo = JSON.parse(myInfoJson, function(key, value) {
    if (key == 'langs') {
        class LangObj {
            constructor(val) {
                this.langs = val;
            }

            printLangs() {
                console.log("=== 사용 프로그래밍 언어 ===");
                console.log(this.langs);
                console.log("===========================")
            }
        }
        return new LangObj(value);
    }
    return value;
});

myInfo.langs.printLangs();

//let myInfo2 = JSON.parse(myInfoJson);
//console.log(typeof myInfo2.langs);
//console.log(myInfo2.langs);
//myInfo2.langs.printLangs();
```

```jsx
=== 사용 프로그래밍 언어 ===
[ 'java', 'python', 'html', 'css', 'js' ]
===========================
```

위 예제의 `JSON.parse()` 메서드의 두 번째 인자에는 `function(key, value) `형태의 함수를 대입한다. 해당 함수에는 만약 키가 langs일 경우, 해당 프로퍼티의 값을 LangObj이라는 사용자 정의 객체로 변환하도록 하고 있다. 그 결과 원하던 요구사항을 충족할 수 있었다. 

---

References

[1] [JSON과 메서드](https://ko.javascript.info/json)