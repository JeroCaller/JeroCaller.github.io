---
title: "[Typescript] 기초 2"
category: "Typescript"
tag: ["Typescript", "문법", "기초", "Basic"]
---

# 타입 추론 (Type Inference)

만약 개발자가 별도로 타입 표기를 하지 않은 경우, 타입스크립트 컴파일러는 해당 타입을 추론하는 과정을 거친다. 

```tsx
/*
사실 개발자가 따로 타입을 지정하지 않더라도 
타입스크립트가 알아서 타입 추론(Type Inference)을 한다. 
아래 oneNum 변수는 자동으로 number라는 타입으로 추론한다. 
VSCode와 같은 IDE에서 해당 변수 이름 위에 마우스 커서를 올리면 뜨는 
프리뷰 설명을 통해 이를 확인할 수 있다. 
*/

let oneNum = 123;
```

코드 1-1.

위 코드의 `oneNum` 변수에는 아무런 타입 표기도 되어 있지 않다. 이 경우 타입스크립트 컴파일러는 이 변수에 할당된 값의 타입이 `number` 이기에 해당 변수의 타입도 `number` 로 추론한다. 

한 편 다음과 같이 여러 요소들을 가지는 배열과 같은 값의 경우,

```tsx
/*
Best Common Type) 배열과 같이 하나의 변수에 여러 요소들을 포함할 수 있는 
자료구조 형태의 데이터가 들어오고, 개발자가 해당 변수에 별도로 타입을 지정
하지 않았다면, 타입스크립트에서는 전체 요소들 중 가장 공통점이 많은 타입을 
해당 변수의 타입으로 추론한다.
아래 변수의 경우, number 타입이 가장 많으므로 arr 변수는 number 타입으로 
추론된다. 
*/
let arr = [0, 1, null];
```

코드 1-2. 

배열 내 모든 요소들로부터 가장 공통적으로 많은 타입들을 추론하여 해당 변수의 타입을 추론한다. 이를 최적 공통 타입(Best common type)이라 한다. 위 `arr` 변수의 경우 배열 내 요소에 `number` 타입의 요소가 더 많으므로 결국 `arr` 변수는 `number[]` 타입으로 추론된다. 

다음과 같은 경우에는 배열 내 요소들의 타입의 종류가 모두 1개씩 골고루 있으므로 이 경우에는 Union Type으로 추론된다. 

```tsx
/*
아래 변수의 경우 모든 요소의 타입의 종류가 어느 한 쪽이 많지 않고 골고루 있으므로 
(number | string | boolean)[] 형태의 타입으로 추론된다. 
*/
let arr2 = [0, '1', true];
```

코드 1-3.

# 타입 별칭 (Type Aliases)

타입 별칭은 말 그대로 타입의 새로운 또 다른 이름을 만들고자 할 때 사용하는 개념이다. 특정 type, interface를 참조할 수 있는 타입 변수이다. 타입 별칭을 만들기 위해 `type` 키워드를 사용한다. 사용법은 아래 예시처럼 마치 변수 선언하듯이 정의하면 된다. 

```tsx
let str1: string = 'hi';

type Username = string;
let myname: Username = 'tyson';

// =======================
type PersonType = {
  name: string;
  age: number;
}

interface PersonInter {
  name: string;
  age: number;
}

// 타입 이름 위에 마우스 커서를 대어 타입에 대한 프리뷰를 살펴보자.
let iAm: PersonType;
let you: PersonInter;
```

코드 2-1.

위 코드에서처럼 `string` 과 같은 primitive type을 지정할 수도 있고, 객체, interface 등을 대상으로 할 수 있다. 위 코드에서는 `PersonType` 과 `PersonInter` 가 선언되어 있다. 둘은 내용은 똑같으나 각각 타입과 인터페이스로 선언했다는 점이 다르다. VSCode와 같은 IDE에서 해당 타입명 위에 마우스 커서를 올려두면, 인터페이스의 경우 인터페이스 이름만 뜨지만, 타입 별칭은 `{ name: string, age: number }` 와 같이 그 값의 세부 내용까지 모두 나온다. 

타입 별칭은 원리상 새로운 타입을 새로 생성하는 것은 아니라고 한다. 대신 정의된 타입에 새로운 이름을 부여하는 것일 뿐이다. 그래서 인터페이스의 경우에는 `PersonInter` 와 같이 그 이름이 명확히 나오는 반면, 타입 별칭의 경우 예를 들어 객체 리터럴에 타입 별칭을 지었다면 그저 `{ name: string, age: number }` 와 같이 객체 리터럴만 보인다는 차이가 있다. 

인터페이스의 경우에는 `extends` 를 이용하여 인터페이스 간 확장이 가능하다. 타입 별칭의 경우 타입스크립트 버전 2.7 이하에서는 확장이 불가능하고, 그 이상 버전부터는 intersection type을 이용하여 `type FPSUser = Person & { hasGun: true}` 와 같이 확장이 가능하다고 한다. 

# 타입 단언 (Type Assertion)

타입 단언은 타입스크립트 컴파일러보다 개발자가 어떤 타입인지 확신이 있을 때 사용할 수 있는 타입 지정 방법이다. 개발자가 이렇게 타입 단언을 하면 컴파일러는 해당 타입에 대해서는 별도의 검사를 하거나 데이터의 구조를 신경쓰지 않는다. 주로 `as` 키워드를 이용하여 타입 단언을 사용한다. 

```tsx
// as : 타입 단언 (Type Assertion)
let username = 'kimquel' as string;
```

코드 3-1.

타입 단언을 위한 `as` 키워드는 위 예시처럼 데이터 오른쪽에 작성하며, `as string` 과 같이 `as` 키워드 오른쪽에는 개발자가 단언하고자 하는 타입명을 명시하면 된다. 

이러한 타입 단언을 이용하면 기존의 자바스크립트 프로젝트에서 타입스크립트로 점진적으로 마이그레이션하고자 할 때 유용할 것이다. 타입 단언을 이용하면 기존의 자바스크립트 코드를 거의 변경하지 않는 선에서 타입을 표기할 수 있기 때문이다. 

```tsx
/ Typescript 도입 전
/*
const myInfo = {};
myInfo.name = 'typeson';
myInfo.age = 20;
*/

// Typescript 도입 후 
interface User {
  name: string;
  age: number;
}

/*
const myInfo: User = {};  // 에러. 타입 선언과 동시에 객체 내에 해당 속성들이 정의되어 있어야 함. 
myInfo.name = 'typeson';
myInfo.age = 20;
*/

// 기존 바닐라 자바스크립트의 코드를 거의 건드리지 않고서도 타입을 지정할 수 있다. 
const myInfo = {} as User;
myInfo.name = 'typeson';
myInfo.age = 20;

console.log(myInfo);

/*
이처럼 as 키워드로 대표되는 Type Assertion은 주로 다음의 상황에서 사용하는 것이 좋다.
1. 타입스크립트 컴파일러보다 개발자가 특정 변수에 어떤 타입이 들어가야할지 더 잘 알고 있는 경우.
2. 기존에 자바스크립트로만 작성된 코드를 타입스크립트로 마이그레이션할 경우. 
*/
```

코드 3-2.

# 타입 가드 (Type Guard)

타입 가드는 여러 타입들 중 하나로 타입을 좁히는 것을 의미한다. 주로 Union type을 대상으로 쓰일 수 있다. 

```tsx
/*
타입 가드(Type Guard): 여러 타입들 중 원하는 타입으로 걸러내는 것.
기존의 typeof(원시 타입 대상), instanceof(객체, 클래스 대상) 라는 자바스크립트에 존재하던 키워드를 이용하여 
타입스크립트에서 여러 타입들 중 원하는 타입으로 걸러낸다. 
*/

type SomeData = string | number;

const mockFunc = (data: SomeData): void => {
  if (typeof data === 'string') {
    console.log(`string 타입입니다.`);
    console.log(`텍스트 길이: ${data.length}`);
  } else {
    console.log(`number 타입입니다.`);
    console.log(`소수점 2자리까지의 수: ${data.toFixed(2)}`);
  }
};

mockFunc('hello');
mockFunc(3.141592);

```

코드 4-1.

위 예시에서처럼, 함수의 인자로 Union Type의 값이 들어온 경우 타입 하나로 좁히기 위해 if(또는 switch)문 과 `typeof` (또는 `instanceof` ) 키워드를 이용하여 타입 가드를 하고 있다. 위 예시에서는 `string` 타입일 경우와 `number` 일 경우 각자 다른 메시지가 출력되도록 하였다. 

# 모듈 (Module)

타입에도 `import` , `export` 키워드를 사용하여 다른 모듈에서도 해당 타입을 사용할 수 있게끔 할 수 있다. 이 키워드를 사용하지 않으면 해당 타입이 선언된 모듈 내에서만 사용 가능하다.

```tsx
export interface MySiteUser {
  username: string;
  age: number;
}

const someUser: MySiteUser = {
  username: 'kimquel',
  age: 23
};

console.log(someUser);
```

코드 5-1. setType.ts

```tsx
import { MySiteUser } from './setType.js';

// es6로 컴파일된 자바스크립트가 의도대로 동작하려면
// package.json에 "type": "module"로 지정하여 ESM 방식의 모듈러라는 걸 설정해야 한다.

const me: MySiteUser = {
  username: 'typeson',
  age: 33
};

console.log(me);
```

코드 5-2. useType.ts

# 인덱스 시그니처 (Index Signature)

객체 타입 내에 어떤 속성들이 들어올지는 알 수 없으나 그 속성들의 공통된 타입에 대해선 알 때 이를 `[key: Type]: V]` 의 형태로 타입 표기하는 것을 인덱스 시그니처(Index signature)라 한다. 

다음은 인덱스 시그니처를 각각의 방식으로 활용하는 예시들이다. 

```tsx
// interface에서 정의한 프로퍼티 외 다른 프로퍼티까지 추가하고자 하는 경우 사용 가능.
interface BusinessCard {
  name: string;
  phone: string;
  job: string;
  hasThisCard: boolean;
  [propNames: string]: any;  // 예) obj.name = obj["name"]  // 객체 속성 key는 이렇게 string으로도 나타낼 수 있다. 그렇기에 key 타입을 string으로 지정한 것이다. 
}

const kimquel: BusinessCard = {
  name: 'kimquel',
  phone: '000-1111-2222',
  job: 'Backend Developer',
  hasThisCard: true,
  companyName: 'kimDB'  // 추가
};
console.log(kimquel);
```

코드 6-1.

```tsx
// 배열 요소 타입 지정 가능.
interface stringArray {
  // 배열 인덱스는 숫자이므로 number로 지정하였고, 배열 요소(key에 대한 value)는 string 타입임.
  [index: number]: string;
}

const myArr: stringArray = ['5', '3', '12'];
myArr[2] = '4';
console.log(myArr);
console.log(myArr[1]);
```

코드 6-2.

```tsx
interface MyObj {
  [propName: string]: string;
  age: number;  // 에러. 위의 index signature의 value 타입과 맞춰야 함. age: string 이어야 함.
  // 이러한 에러를 피하고자 한다면 [propName: string]: any와 같이 모든 타입이 오게끔 할 수도 있다. 
}
```

코드 6-3. 

인덱스 시그니처의 key의 타입은 `number`, `string` , `symbol` , template string 패턴 및 이들의 Union Type만 가능하다고 한다. 

이처럼 객체 내 추가적인 속성들을 더할 수 있도록 허용하고자 할 때 유용하게 사용할 수 있지만, 지정된 타입만 만족하면 어떤 속성이든 계속 들어올 수 있기에 타입의 엄격함을 원한다면 인터페이스 확장 등 다른 방법을 이용하는 것이 더 좋을 것 같다. 

# 유틸리티 타입 (Utility Type)

유틸리티 타입은 이미 정의한 타입을 조금 다르게 변환하고자 할 때 사용할 수 있는 개념이다. 유틸리티 타입에는 정말 여러 가지 많이 있어 여기서는 `Partial` , `Pick` , `Omit` 에 대해서만 살펴보겠다. 

## Partial

특정 타입의 모든 부분 집합 타입을 정의한다. 

```tsx
interface Book {
  name: string;
  price: number;
}

type PartialBook = Partial<Book>;

// 다음의 세 가지 경우의 수 모두 가능.
const oneBook: PartialBook = {};
const twoBook: PartialBook = { name: '타입스크립트 기본만 빠르게 훑기!' };
const threeBook: PartialBook = { name: '굳', price: 10000 };

console.log(oneBook);
console.log(twoBook);
console.log(threeBook);

```

코드 7-1.

## Pick

특정 객체 타입에서 속성 일부만 선택하여 그 속성으로만 이루어진 타입을 재정의하고자 할 때 사용된다. 

```tsx
interface Info {
  name: string;
  phone: string;
  address: string;
  age: number;
}

const onlyMyname: Pick<Info, 'name'> = {name: 'kimquel'};

// Union 연산자를 이용하여 둘 이상의 프로퍼티들을 골라올 수 있음.
const person : Pick<Info, 'name' | 'age' > = { name: 'typeson', age: 23 };
const chickenRestaurant : Pick<Info, 'phone' | 'address' > = {
  phone: '000-1111-2222',
  address: 'xx시 xx구 xx대로'
};

console.log(onlyMyname);
console.log(person);
console.log(chickenRestaurant);

```

코드 8-1

## Omit

특정 객체 타입에서 몇몇 속성만 제거하여 재정의하고자 할 때 사용된다.

```tsx
interface SiteUserInfo {
  name: string;
  phone: string;
  address: string;
  age: number;
}

// 둘 이상의 속성 제거를 원한다면 union 연산자를 이용한다. 
const basicUser: Omit<SiteUserInfo, 'phone' | 'address'> = {
  name: 'typeson',
  age: 34
};

const restaurant: Omit<SiteUserInfo, 'age'> = {
  name: '일식집',
  address: 'xx시 xx동 xx대로',
  phone: '000-1111-2222' 
};

console.log(basicUser);
console.log(restaurant);
```

코드 9-1.

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

[5] Type inference

[Documentation - Type Inference](https://www.typescriptlang.org/docs/handbook/type-inference.html)

[6] 인덱스 시그니처

[Documentation - Object Types](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)

[7] 인덱스 시그니처

[[TypeScript] 인덱스 시그니처](https://velog.io/@ahsy92/TypeScript-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%8B%9C%EA%B7%B8%EB%8B%88%EC%B2%98)