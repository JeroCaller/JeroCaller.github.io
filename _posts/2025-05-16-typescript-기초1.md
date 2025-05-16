---
title: "[Typescript] 기초 1"
category: "Typescript"
tag: ["Typescript", "문법", "기초", "Basic"]
---


필자가 직접 작성한 타입스크립트 예제 코드 모음 repo: [https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/typescript](https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/typescript)

# 기본 타입

여기서의 기본 타입은 자바스크립트로 개발 시 일반적으로 사용되는 string, number, array 등의 여러 기본 타입들을 의미한다. 

변수에 타입을 명시하고자 한다면 변수명 오른쪽에 콜론(:)과 함께 타입명을 명시한다. 다음과 같이 말이다.

```tsx
let myString: string = 'hi';
```

코드 1-1.

이와 같이 타입을 명시하는 것을 Type annotation(타입 표기)이라 한다. 위와 같이 타입 표기를 하면 해당 변수에 대입될 값의 타입은 반드시 표기된 타입이어야 한다. 위 예제에서 `myString` 변수에 `string` 타입으로 지정했으므로 그 값도 반드시 `string` 타입이어야 한다. 만약 그 외 다른 타입의 값을 할당할 경우 VSCode에서는 바로 빨간 밑줄과 함께 타입 에러 메시지가 뜬다.

![사진 1-1. 잘못된 타입 값 대입으로 인한 에러 메시지](/images/2025-05-16/typescript-%EA%B8%B0%EC%B4%881/1.png)

사진 1-1. 잘못된 타입 값 대입으로 인한 에러 메시지

아직 컴파일하기도 전에 타입 오류를 바로 잡아내는 것을 확인할 수 있다. 순수 자바스크립트였다면 런타임 시점에서야 잘못된 타입의 값을 대입했다는 것을 뒤늦게 깨달았을 것이다. 

위 에러가 발생했음에도 다음 명령어를 통해 컴파일을 시도하면 콘솔창에서도 에러 메시지가 뜬다.

```tsx
> npx tsc --target es6 primitive.ts
primitive.ts:2:5 - error TS2322: Type 'number' is not assignable to type 'string'.

2 let myString: string = 1;
      ~~~~~~~~

Found 1 error in primitive.ts:2
```

코드 1-2. 컴파일 후 에러 메시지.

다만 위와 같이 컴파일 에러가 나도 자바스크립트 파일 자체는 컴파일되어 나오는 것을 확인하였다.

위 코드 1-1과 같은 형식으로 다음과 같이 여러 타입들을 표기하는 데에 사용할 수 있다. 

```tsx
let myString: string = 'hi';
let intNum: number = 10;
let doubleNum: number = 3.141592;
let isTrue: boolean = true;

// 객체 타입은 interface 또는 type alias(타입 별칭)의 사용을 권장
let user: object = { username: 'kimquel', message: '만나서 반갑습니다!'};

let numArr: number[] = [1, 2, 3];
let numArr2: Array<number> = [4, 5, 6];

let tupleArr: [string, number] = ['jeongdb', 23];

enum Beverage {
  Cola,
  Juice,
  Soda,
  Milk = 6
}

let myDrink: Beverage = Beverage.Cola;
let yourDrink: Beverage = Beverage.Juice;

let myAny: any = 'wow';
let myAny2: any = 123;
let myAny3: any = ['wow', 1312, false];

const addAllAndPrint = (nums: Array<number>): void => {
  let result: number = nums.reduce((prev, current) => prev + current);
  console.log(`총합: ${result}`);
};

addAllAndPrint(numArr);

```

코드 1-3. 

위 코드에서 볼 수 있듯, `string` , `number` , `boolean` 등의 여러 타입들을 사용할 수 있고, `number[]` , `Array<number>` 와 같이 값들의 배열 타입도 명시할 수 있다(두 배열 표기법의 차이는 없다). `object` 와 같이 객체 타입의 값이 올 것임을 명시할 수도 있지만, 사실 이는 별로 좋은 방법은 아니라고 생각한다. `object` 라는 타입 이름에서 이 변수가 어떤 역할을 하는 것인지 전혀 파악할 수 없으며, 객체 내 속성들을 IDE의 코드 자동 완성 기능을 이용하여 미리 확인할 수도 없기 때문이다. 이보다는 나중에 소개할 interface나 type을 이용하여 객체 내 어떤 프로퍼티가 들어갈 수 있는지 자세히 명시하는 것이 더 좋을 것이다. 

`any` 는 아무 타입의 값이나 들어올 수 있다. 주로 데이터의 타입이 무엇일지 예상할 수 없을 때 사용할 수 있다. 하지만 이 타입도 구체적인 타입이 아니라 모든 타입을 받을 수 있기에 별로 권장되지는 않는다. 

한 편 타입스크립트에서는 `enum` 타입도 제공한다. Java의 것과 거의 같다고 보면 된다. `enum` 안에 사용되는 상수들은 기본적으로는 순차적으로 0부터 1씩 증가되는 값을 자동으로 부여받는다. 위 코드의 `Beverage` 타입에는 `Cola` , `Juice` , `Soda` 상수들은 각각 0, 1, 2라는 값이 할당된다. `Milk` 의 경우 개발자가 6이라고 명시하였기에 해당 값을 가진다. `enum` 에 대한 개념은 차후에 따로 다루겠다. 

이러한 타입은 함수에도 적용할 수 있다. 매개변수와 함수의 리턴 타입도 지정할 수 있다. 리턴 타입의 경우 아무것도 반환할 것이 없다면 `void` 타입을 표기할 수 있다. 

# 함수

```tsx
const sum = (a: number, b: number): number => {
  return a + b;
};

console.log(sum(1, 2));
console.log(sum(1, 2));
console.log(sum(1, 2, 3));  // 에러
```

코드 2-1.

타입스크립트를 함수에 적용할 때에는 함수 호출 시 모든 인자에 값을 필수로 넣어야 한다. 따라서 특정 인자를 넘길 필요가 없는 상황에서도 `undefined` , `null` 등 garbage 값이라도 넘겨야 한다. 또한 인자의 개수를 넘어서서 인자들을 대입할 수도 없다. 만약 특정 인자는 데이터를 반드시 넣지 않아도 되도록 하고자 할 때에는 다음의 세 가지 방법을 고려해볼 수 있겠다. 

- `?` optional parameter 사용

```tsx
const sum2 = (a: number, b?: number): number => {
  return a + b;
};
console.log(sum2(1));  // NaN 에러 발생하지 않음. 
```

코드 2-2.

위 코드에서 `b` 매개변수에 아무런 인자도 전달되지 않으면 `b` 매개변수에는 `undefined` 가 할당된다. 

- 기본값(Default Parameter) 사용

```tsx
const sum3 = (a: number, b: number = 10): number => {
  return a + b;
}
console.log(sum3(5));   // 15
```

코드 2-3.

위 코드에서는 `b` 매개변수에 아무런 값도 전달하지 않을 경우 기본값인 10이 자동으로 할당된다. 

- `...` rest 문법 사용

```tsx
const addAll = (...nums: Array<number>): number => {
  let total = nums.reduce((prev, current) => prev + current);
  return total;
}

// 아래 모두 에러 없음.
console.log(addAll(1, 2, 3));
console.log(addAll(1));
console.log(addAll(1, 2, 3, 4, 5));
```

코드 2-4. 

# 인터페이스 (Interface)

타입스크립트에서 인터페이스는 여러 프로퍼티들이 타입과 함께 정의되어 있는 객체 타입을 정의하기 위해 사용된다. 

예를 들어 함수의 인자 하나에 여러 프로퍼티들이 있는 객체 리터럴을 타입으로 명시하고자 할 경우, 인터페이스 없이는 다음과 같이 작성할 수 있겠다. 

```tsx
const printUserInfo = (person: { name: string, age: number }): void => {
  console.log(`=== 유저 정보 ===`);
  console.log(`유저네임: ${person.name}`);
  console.log(`나이: ${person.age}`);
  console.log('======================');
};

let user = { name: 'kimquel', age: 25, message: '만나서 반갑습니다.'};
printUserInfo(user);
```

코드 3-1. 

하지만 인터페이스를 이용하면 다음과 같이 바꿀 수 있다.

```tsx
interface User {
  name: string,
  age: number
}

const printUserInfo2 = (person: User): void => {
  console.log(`=== 유저 정보 ===`);
  console.log(`유저네임: ${person.name}`);
  console.log(`나이: ${person.age}`);
  console.log('======================');
};
printUserInfo2(user);
```

코드 3-2. 

이제 `person` 파라미터에는 `User` 타입의 객체를 전달해야 함을 구체적으로 명시할 수 있게 되었다. 

인터페이스로 생성된 타입이 표기된 변수나 함수의 매개변수에는 인터페이스 내에 정의된 모든 프로퍼티들을 가져야 한다. 다만 특정 프로퍼티는 옵션으로 설정하여 생략해도 되도록 할 수 있다. `?` 를 사용하며, 이를 옵션 속성(Optional Property)이라 한다. 

```tsx
interface User {
  name: string;
  age?: number;
}

let me: User = { name: 'jeongdb' };

const printUser = (user: User): void => {
  console.log(user.name);
  console.log(user.age);
};

printUser(me);
```

코드 3-3.

위 코드에서 `User` 인터페이스의 `age` 속성에 `?` 가 붙어서 옵션 속성이 되었다. 이 타입을 가지는 객체에는 `age` 프로퍼티를 가지지 않아도 허용된다. 

한 편 인터페이스에는 다음과 같이 함수의 타입도 정의할 수 있다. 

```tsx
interface SignUp {
  (username: string, age: number): void;
}

const signUpFunc: SignUp = (name: string, age: number): void => {
  console.log(`가입정보)`);
  console.log(`유저네임: ${name}, 나이: ${age}`);
};

signUpFunc('good', 23);

```

코드 3-4.

인터페이스는 다른 인터페이스를 이용하여 확장될 수 있다. 

```tsx
interface Person {
  name: string;
  age: number;
}

interface Hobby {
  hobbyName: string;
}

interface Developer extends Person, Hobby {
  skills: string[];
}

let me: Developer = { 
  name: 'kimquel', 
  age: 23, 
  hobbyName: '게임', 
  skills: ['React', 'SpringBoot'],
};

console.log(me);
```

코드 3-5. 

참고로 인터페이스는 `class Citizen implements Person` 과 같이 클래스에도 적용할 수 있다. 이 때 클래스는 인터페이스 내에 정의된 프로퍼티 또는 함수 타입들을 따라 특정 값을 할당하거나 메서드를 정의해야 한다. 

# Enum

Enum은 특정 값들이 할당된 상수들의 집합으로, 여러 상수들을 공통의 카테고리로 묶을 떄 사용된다. 

```tsx
// 초기 값을 안 주면 자동으로 0부터 1씩 증가되는 값을 각각 할당받음.
enum Compass {
  North, // 0
  East,  // 1
  West, // 2
  South // 3
}
```

코드 4-1.

위와 같이 Enum이 정의된 경우, 각 상수에 개발자가 아무런 값도 할당하지 않으면 맨 첫 번째 상수부터 차례대로 0부터 1씩 증가되는 값을 자동으로 할당받는다. 

```tsx
enum InitCompass {
  North = 3,
  East,  // 4
  West,  // 5
  South  // 6
}
```

코드 4-2.

위 코드와 같이 맨 첫 번째 상수에 특정 값으로 초기화하는 경우 그 다음 상수부터는 해당 값의 1씩 증가된 값을 자동으로 할당받게 된다. 한 편 상수에 숫자 뿐만 아니라 문자열도 대입하여 사용할 수 있다. 

# Union & Intersection Type

Union type은 이 타입이 표기된 변수나 함수의 인자에 지정된 둘 이상의 타입 중 하나라도 해당되는 경우를 표기하고자 할 때 사용한다. 기호는 `|` 를 사용한다. 

```tsx
let some: string | null = null;

const someFunc = (som: string | null): void => {
  if (som === null) {
    console.log('null');
    return;
  }

  console.log(som);
};

someFunc(some);
```

코드 5-1.

`some` 변수에는 `string | null` 타입이 지정되었다. 이 타입이 Union Type이다. 해당 변수에는 `string` 또는 `null` 타입의 값만 대입할 수 있다. 

Intersection Type은 둘 이상의 여러 타입들을 하나로 합쳐 정의하는 타입이다. 

```tsx
interface Phone {
  phoneNumber: string;
  price: number;
}

interface Laptop {
  ramGB: number;
  price: number;
}

type Device = Phone & Laptop;

let myDevice: Device = {
  phoneNumber: '010-0101-1111',
  price: 1000000,
  ramGB: 16
};

console.log(myDevice);
```

코드 5-2. 

위 코드에서 `Device` 타입은 결국 `Phone` 과 `Laptop` 인터페이스 내 모든 속성들을 하나로 합친 타입이다. 이 두 인터페이스 내에 공통으로 중복되어 존재하는 `price` 속성은 하나로 합쳐진다. 

```tsx
interface Phone {
  phoneNumber: string;
  price: number;
}

interface Laptop {
  ramGB: number;
  price: number;
}

const otherFunc = (device: Phone | Laptop): void => {
  console.log(device.price); // 두 인터페이스 내 공통의 필드 타입만 인식 가능.
  console.log(device.phoneNumber);  // 에러
  console.log(device.ramGB);  // 에러
};

otherFunc({phoneNumber: '000', price: 1000});
```

코드 5-3.

위 코드에서, `otherFunc` 함수의 매개변수인 `device` 에는 `Phone` 타입의 값이 오거나 `Laptop` 타입의 값이 올 수 있다. 그런데 컴파일러의 입장에서는 해당 함수 호출 시 정확히 어떤 타입이 올지 알 수 없기에 결국 두 타입에 공통적으로 존재하는 속성(`price`)들만을 추론하게 된다. 위 예시의 경우 함수 내에서는 오로지 `price` 속성만이 두 인터페이스에 공통적으로 존재하기에 해당 속성만 인식 가능한 것이다. 이 경우 `if` , `switch` 조건문과 `typeof` , `instanceof` 키워드와 함께 사용하여 인자의 타입이 무엇인지를 선별하는 타입 가드(Type Guard)를 사용하여 타입의 범위를 좁혀 주는 것이 좋다. 

# readOnly

타입 표기를 할 때 `readOnly` 키워드를 변수 선언 맨 앞에 두면 해당 변수는 처음에 딱 한 번 초기화한 다음 다른 값으로 변경되지 않게 한다. 이를 통해 해당 변수는 오로지 읽기 전용임을 표기할 수 있는 것이다. 

```tsx
interface User {
  readonly siteName: string;
  name: string;
  age?: number;
}

let me: User = { siteName: '나이버', name: 'kimquel' };
console.log(me);
me.siteName = '굳굳';  // 에러
console.log(me);
```

코드 6-1. 

위 코드에서, `User` 인터페이스 내부의 `siteName` 변수에 `readOnly` 가 맨 왼쪽에 표기되었다. 이 변수는 한 번 초기화한 후 이 값을 변경하려고 하면 에러가 발생한다. 

# 제네릭 (Generics)

타입스크립트에서의 제네릭은 사실상 자바에서의 제네릭 개념과 거의 동일하다고 볼 수 있다. 제네릭은 함수에서 사용할 변수의 타입을 함수 정의할 때 미리 정의하는 것이 아니라 함수를 호출할 때 알려주는 문법이다. 자바에서와 마찬가지로 `<>` 꺽쇠괄호를 사용하여 여기에 원하는 타입 변수(Type Variable)를 전달할 수 있다. 이러한 특성 때문에 제네릭을 사용하면 함수 호출부에서 특정 타입을 동적으로 유연하게 지정할 수 있게 된다. 

```tsx
const normalFunc = (data: any): void => {
  console.log(`data type: ${typeof data}`);
  console.log(`data: ${data}`);
};

const genericFunc = <T>(data: T): void => {
  console.log(`data type: ${typeof data}`);
  console.log(`data: ${data}`);
};

let someStr: string = 'hi';
let myNum: number = 3.14;

normalFunc(someStr);
normalFunc(myNum);
console.log('===============');
genericFunc<string>(someStr);
genericFunc<number>(myNum);

```

코드 7-1.

`normalFunc` 에서는 매개변수 `data` 의 타입을 상황에 따라 자유롭게 정할 수 있도록 `any` 를 사용하였다. 하지만 이 경우 `any` 는 모든 타입들을 다 받아들일 수 있어 타입 검사가 사실상 의미가 없으므로 해당 인자에 어떤 타입의 값이 전달되었는지 알 수가 없다. 대신 `<T>` 와 같이 제네릭을 사용하면 함수 호출부에서 컴파일러가 타입을 알 수 있어 타입 검사가 가능하다는 장점이 있다. 

제네릭이 사용된 함수 호출 시 `genericFunc<string>(someStr);` 와 같이 호출할 수도 있고, 인자로 들어가는 값에는 이미 타입이 정해져 있기에 컴파일러로 하여금 타입 추론이 가능하기에 `<string>` 과 같이 꺾쇠괄호 부분은 생략해도 된다(하지만 그러면 타입스크립트를 사용하는 의미가 희석되는 것 같아서 개인적으로는 웬만하면 사용하고자 한다).

 

```tsx
const logMessageError = <T>(message: T): void => {
  console.log(`텍스트 길이: ${message.length}자`);
  console.log(`텍스트 내용: ${message}`);
};

const logMessageGood = <T>(message: T[]): void => {
  console.log(`텍스트 개수: ${message.length}`);
  console.log(`텍스트 내용들) `);
  message.forEach(text => {
    console.log(text);
  });
};

let messageOne: string = "hello, world! My name is kimquel. Nice to meet you";
let messageTwo: string[] = ['hello, world!', 'My name is kimquel', 'Nice to meet you'];

logMessageError<string>(messageOne);
logMessageGood<string>(messageTwo);
```

코드 7-2.

위 코드와 같이 제네릭을 활용하여 `T[]` 또는 `Array<T>` 와 같이 배열 타입으로도 활용할 수 있다. 

또한 함수에 제네릭을 적용할 수 있다는 사실을 통해 인터페이스 내에도 제네릭을 적용한 함수 타입도 선언 가능하다.

```tsx
 interface FuncInterfaceOne {
  <T>(message: T): void;
}

interface FuncInterfaceTwo<T> {
  (message: T): void;
}

const funcOne: FuncInterfaceOne = <T>(message: T): void => {
  console.log(message);
};

const funcTwo: FuncInterfaceTwo<string> = (message: string): void => {
  console.log(message);
};

let myMessage: string = "hello, world!";
funcOne<string>(myMessage);
funcTwo(myMessage);
```

코드 7-3.

자바에서 제네릭 사용 시 `<T extends MyClass>` 와 같이 타입 매개변수를 어떤 클래스의 자식 클래스로 한정지을 수 있듯, 타입스크립트에서의 제네릭에서도 똑같이 타입 변수를 특정 인터페이스 타입으로 한정 지을 수 있다. 

```tsx
interface TypeThatHaveLength {
  length: number;
}

const logMessage = <T extends TypeThatHaveLength>(message: T): void => {
  console.log(`텍스트 길이: ${message.length}`);
  console.log(`텍스트 내용: ${message}`);
};

let greetings: string = 'hi, everyone!';
logMessage(greetings);
```

코드 7-4. 

# typeof

자바스크립트에서 `typeof` 키워드는 `typeof 'hello'` 와 같이 사용하여 그 데이터의 타입을 확인하고자 할 때 쓰인다. 반면 타입스크립트에서의 `typeof` 키워드는 객체 데이터로부터 객체 타입을 생성해주는 키워드이다. 

```tsx
const instaAppInfo = {
  name: 'instagram',
  since: 2010,
  appType: 'SNS'
};

/*
type FamousAppInfo = {
  name: string;
  since: number;
  ppType: string;
}
*/
type FamousAppInfo = typeof instaAppInfo;

const wrtnAppInfo: FamousAppInfo = {
  name: 'wrtn',
  since: 2021,
  appType: 'AI'
}

console.log(instaAppInfo);
console.log(wrtnAppInfo);

console.log(typeof 'hi');  // 순수 자바스크립트 문법에서의 typeof
```

코드 8-1.

인스타 앱에 대한 정보를 기록하기 위해 `instaAppInfo` 이라는 객체를 만들었다. 그런데 생각해보니 이 객체의 속성들을 타입으로 따로 빼내어 모든 앱에 대해서도 같은 방식으로 기록할 수 있을거란 생각이 든다. 이럴 떄 `typeof` 키워드를 사용할 수 있다. 해당 키워드를 객체에 적용하면 해당 객체 내 속성들과 그 속성의 타입을 따로 추출하여 하나의 또 다른 타입으로 생성해준다. 위 코드의 `FamousAppInfo` 가 그 예이다. 이를 이용하여 똑같은 타입으로 새로운 데이터를 기록할 수 있게 된다. 

# keyof

keyof 연산자는 객체 내 속성들의 key 이름을 문자열 형태로 가져와 그들을 Union Type으로 생성해주는 연산자이다. 

```tsx
interface ProjectInfo {
  name: string;
  duringInMonth: number;
  skills: string[];
}

type ProjectInfoUnion = keyof ProjectInfo;  // "name" | "duringInMonth" | "skills"

let nameVar: ProjectInfoUnion = "name";
let duringVar: ProjectInfoUnion = "duringInMonth";
let skillsVar: ProjectInfoUnion = "skills";

// ==== keyof + typeof 응용

const plainObj = {
  id: 1,
  auth: 'OAuth2.0',
  db: 'MariaDB'
};

type ProjectSkill = keyof typeof plainObj;  // "id" | "auth" | "db"
let idVar: ProjectSkill = "id";
let authVar: ProjectSkill = "auth";
let db: ProjectSkill = "db";

```

코드 9-1. 

`ProjectInfo` 인터페이스 내에 존재하는 속성 이름인 `name` , `duringInMonth` , `skills` 를 추출하여 `"name" | "duringInMonth" | "skills"` 으로 만들어준다. 

이 `keyof` 키워드와 제네릭을 같이 혼합하면 다음의 경우도 가능하다.

```tsx
const getValueOfProperty = <O, K extends keyof O>(obj: O, key: K): O[K] => {
  return obj[key];
}

let myObj = {
  a: 1, 
  b: 2,
  c: 3
};

console.log(getValueOfProperty(myObj, 'a'));
console.log(getValueOfProperty(myObj, 'x'));  // Error
```

코드 9-2.

즉 위 코드의 `getValueOfProperty` 함수는 인자로 객체와 그 객체 내에 존재하는 모든 속성 중 하나를 타입으로 하는 데이터를 받는다. 그래서 객체 내에 없는 속성값을 두 번째 인자로 넣을 수 없게끔 할 수 있다. 

---

References

[1]  타입스크립트 핸드북

[타입스크립트 핸드북](https://joshua1988.github.io/ts/)

[2] 타입스크립트 공식 핸드북

[TypeScript 한글 문서](https://typescript-kr.github.io/pages/basic-types.html)

[3] Typescript 공식 사이트

[JavaScript With Syntax For Types.](https://www.typescriptlang.org/)

[4] 타입스크립트 튜토리얼

[TypeScript Tutorial](https://www.typescripttutorial.net/)

[5] keyof 연산자

[📘 객체를 타입으로 변환 - keyof / typeof 사용법](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-keyof-typeof-%EC%82%AC%EC%9A%A9%EB%B2%95#keyof_%EC%97%B0%EC%82%B0%EC%9E%90)