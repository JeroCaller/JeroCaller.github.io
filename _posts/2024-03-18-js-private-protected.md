---
title: "[JS] Private, Protected"
category: "javascript"
tag: ["web", "js", "javascript", "private", "protected"]
---

객체 지향 프로그래밍에서 항상 접해보는 개념 중 하나는 외부에서 객체의 특정 프로퍼티 또는 메서드로의 접근에 대한 것이다. 자동차 등의 어떤 기계의 내부가 바깥에서 다 보이도록, 그리고 심지어 사용자가 직접 만질 수 있을 정도로 오픈되어 있다면 사용자가 부품을 잘못 건드려서 고장이 날 수 있듯, 외부에서 객체 내 모든 프로퍼티와 메서드에 접근이 가능하게 하면 오류, 버그가 발생할 수도 있다. 그래서 때론 특정 프로퍼티, 메서드로의 접근을 제한할 필요가 있다. 

# 인터페이스와 접근 제한자

객체 지향 프로그래밍에서 필드(프로퍼티와 메서드)는 크게 두 종류로 구분할 수 있다고 한다. 

- 내부 인터페이스(internal interface) : 클래스 내부의 다른 메서드에선 접근 가능하나, 클래스 외부에서는 접근할 수 없는 필드. 참고로 이렇게 외부에서 클래스 내부에 함부로 접근하지 못하도록 제한하여 클래스 내부를 보호하는 것을 캡슐화(encapsulation)라고 한다.
- 외부 인터페이스(external interface) : 클래스 외부에서도 접근 가능한 필드.

또한, 자바스크립트에서는 다음의 접근 제한자들이 있다.

- public : 어디서든 접근 가능한 외부 인터페이스 구성에 쓰인다. private, public, protected 등 따로 접근 제한자를 설정하지 않았다면 그 필드는 기본적으로 public으로 설정된다.
- private : 클래스 내부에서만 접근할 수 있도록 내부 인터페이스를 구성해주는 접근 제한자. 필드명 앞에 기호 # 을 붙이면 된다.
- protected : 클래스 내부에서만 접근할 수 있도록 내부 인터페이스를 구성해주는 접근 제한자. private와 다른 점은 (1) private로 설정된 필드는 자식 클래스에서도 접근 불가능하나, protected는 자식 클래스에서는 접근 가능하고, (2) private로 설정된 필드를 외부에서 접근 시 에러가 발생하나, protected는 그러진 않아서 사용자의 주의가 필요하며, (3) protected로 설정하려면 필드명 앞에 언더스코어 (_) 기호를 붙이면 된다.

# Protected로 프로퍼티 보호

Protected와 getter, setter를 함께 활용하면 클래스 내 특정 프로퍼티에 대해 사용자가 아무런 값을 입력하는 것을 방지하거나 읽기 전용 프로퍼티로 만들어 아예 값의 수정을 못하도록 할 수 있다. 

다음은 연료량을 표시하는 자동차를 추상화한 클래스이다. 사용자가 연료량을 숫자로 입력할 때, 음수값은 받아들일 수 없다. 따라서 이를 제한하기 위해 연료량을 나타내는 프로퍼티를 protected로 만든 다음, getter, setter를 활용하여 음수값을 입력하지 못하도록 방지하는 코드를 구현하였다. 

```jsx
class Car {
    _fuel = 0;  // protected 프로퍼티

    set fuel(arg) {
        if (arg < 0) {
            throw new Error("연료량은 음수가 되어선 안됩니다.");
        }
        this._fuel = arg;
    } 

    get fuel() {
        return this._fuel;
    }
}

let car = new Car();
car.fuel = 20;
console.log(car.fuel);
console.log(car._fuel);  // protected 필드는 접근 가능하긴 하다. 
// car.fuel = -1;  // 에러 발생.

```

```jsx
20
20
```

위 예제에서, Car 클래스 내에서 연료량을 의미하는 fuel 프로퍼티에 대해 외부에서 음수값을 입력하는 것을 방지하기 위해 우선 fuel 프로퍼티 앞에 언더스코어(_) 기호를 붙임으로써 해당 프로퍼티를 protected로 만들었다. 그 후, fuel이라는 setter로 접근자 프로퍼티를 생성하여 만약 사용자가 음수를 입력하면 에러를 발생시키도록 하여 음수값을 입력하지 못하도록 설계하였다. 그 결과, 해당 객체에서 fuel 프로퍼티에 음수를 입력하면 에러가 발생한다. 이렇게 protected를 활용하여 클래스 내 특정 정보에 대한 접근을 어느 정도 제한하도록 할 수 있다. 

사실, 사용자는 Car 객체의 _fuel을 직접 입력함으로써 setter를 거치지 않고 직접 프로퍼티로 접근할 수 있다. 다만, 이전에 언급했듯, 프로퍼티명 앞에 언더스코어가 붙어있으면 이를 protected로 간주하여 관례적으로 접근하지 않도록 주의한다고 한다. 따라서 이 관례만 지킨다면 protected를 이용하여 객체 내 정보 접근을 제한함으로써 위와 같은 유용한 기능을 만들 수 있는 이점이 존재한다. 

이번에는 protected와 getter만을 이용하여 객체 내 특정 프로퍼티를 읽기 전용으로 만드는 예제를 살펴보겠다. 즉, 해당 프로퍼티는 외부에서 그 값을 변경할 수 없고, 오로지 읽기만 가능하다. 

```jsx
class Car {
    constructor(mileage) {
        this._mileage = mileage;
    }

    get mileage() {
        return this._mileage;
    }
}

let car = new Car(50);
console.log("주행 거리 :", car.mileage);
car.mileage = 100;
console.log("주행 거리 :", car.mileage);  // setter가 없어 값이 바뀌지 않음.
console.log(car._mileage);
```

```jsx
주행 거리 : 50
주행 거리 : 50
50
```

위 예제에서는 자동차를 추상화한 Car 클래스가 정의되어 있고, 그 안에는 주행거리를 의미하는 _mileage 프로퍼티가 정의되어 있다. 주행거리는 차를 직접 몰고 이동시키지 않는 이상 가만히 있는 자동차의 주행거리를 고의적으로 조작할 수 없다. 이를 반영하여 위 코드에서는 해당 프로퍼티를 읽기 전용으로 만들고자 하였다. 그래서 해당 프로퍼티를 protected로 만들고, getter 메서드를 통해서만 접근하도록 고안하였다. 그 결과, 접근자 프로퍼티를 이용하여 아무리 그 값을 변경하려고 시도해도 변경되지 않음을 알 수 있다. 

# private

private 접근 제한자는 기호 #을 필드명 앞에 붙여 사용하면 적용시킬 수 있다. 이전에 언급했듯, private로 설정된 필드는 클래스 외부에서도, 심지어는 자식 클래스에서도 접근 불가능하며, protected와 달리 외부에서 접근하려고 하면 에러를 발생시킨다. 

다음은 앞선 자동차 예제를 private 접근 제한자로 바꾼 예제이다.

```jsx
class Car {
    #fuel = 0;  // private 프로퍼티

    set fuel(arg) {
        if (arg < 0) {
            throw new Error("연료량은 음수가 되어선 안됩니다.");
        }
        this.#fuel = arg;
    } 

    get fuel() {
        return this.#fuel;
    }
}

let car = new Car();
car.fuel = 20;
console.log(car.fuel);
// console.log(car.#fuel);  // 에러 발생. private 필드는 외부에서 직접 접근 불가.
// car.fuel = -1;  // 에러 발생.

```

```jsx
20
```

자바스크립트에서는 같은 필드명이라도, 앞에 # 기호가 붙은 필드와 그렇지 않은 필드를 구분한다. # 기호가 붙은 필드명은 private로 인식되고, 앞에 아무런 기호도 붙여지지 않은 필드는 public으로 인식된다. 따라서 다음의 코드는 아무런 에러없이 정상적으로 실행된다. 

```jsx
class Car {
    #fuel = 0;
    fuel = 1;

    printProperties() {
        console.log("==== Properties ====");
        console.log("#fuel : " + this.#fuel);
        console.log("fuel : " + this.fuel);
        console.log("====================");
    }
}

let car = new Car();
car.printProperties();
car.fuel = 10;
car.printProperties();
```

```jsx
==== Properties ====
#fuel : 0
fuel : 1
====================
==== Properties ====
#fuel : 0
fuel : 10
====================
```

만약 객체 외부 뿐만 아니라 자식 클래스에서도 특정 필드에 접근하지 못하도록 하고자 한다면 private를, 만약 자식 클래스에서는 접근 가능해야 하는 상황이 있다면 그 때는 protected를 사용하는 것이 적절할 것이다. 

---

References

[1] [private, protected 프로퍼티와 메서드](https://ko.javascript.info/private-protected-properties-methods)