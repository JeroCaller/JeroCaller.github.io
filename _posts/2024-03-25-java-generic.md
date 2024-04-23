---
title: "[Java] 제네릭(Generic)"
category: "Java"
tag: ["java", "자바", "제네릭", "generic"]
---

이 페이지에서는 제네릭(Generic)이 무엇인지와 그 문법, 그리고 제네릭을 사용할 때의 장점들에 대해 설명하겠다. 제네릭이 무엇인지 바로 알아보기 전에 우선 제네릭을 왜 사용해야 하는지에 대해 예시를 통해 살펴본 후에 제네릭 개념과 문법을 살펴보고, 이를 통해 제네릭을 사용할 때의 장점에 대해서 살펴보겠다. 

# 제네릭을 사용하지 않았을 때의 단점

어떤 사이트에 가입을 하면 가입 시 지정한 유저 닉네임과 사이트명을 결합한 이메일 주소를 생성해주는 클래스를 만들고자 한다. 유저 닉네임과 사이트명을 멤버 변수로 가지는 각 사이트들을 각각의 클래스로 먼저 구현한다. 사이트의 수가 많아질 수록 이를 추상화한 사이트 클래스들의 수도 많아진다. 그러면 이메일 주소 클래스 내 이메일 주소 생성 메서드의 매개변수 타입이 각 클래스마다 달라지므로 일일이 오버로딩하기엔 중복되는 코드가 많아진다. 그래서 대신 다형성을 이용하여 이 문제를 해결하고자 한다. 다음이 그 예제이다. 

```java
abstract class SiteBluePrint {
    String nickName;
    String siteName;
    String address;

    SiteBluePrint(String nickName) {
        this.nickName = nickName;
    }

    // siteName을 가져오는 getter 메서드 추가
    public String getSiteName() {
        return this.siteName;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}

class FoodSite extends SiteBluePrint {
    FoodSite(String nickName) {
        super(nickName);
        this.siteName = "foodmarket";
    }
}

class BeautySite extends SiteBluePrint {
    BeautySite(String nickName) {
        super(nickName);
        this.siteName = "beautysite";
    }
}

class EmailAddressMaker {
    private Object siteObj;

    void createAddress(Object siteObj) {
        // 성가신 형변환...
        // siteName을 가져오고, 이메일 주소를 생성하는 로직
        if (siteObj instanceof SiteBluePrint) {
            String email = ((SiteBluePrint)siteObj).nickName + "@" + ((SiteBluePrint)siteObj).getSiteName() + ".com";
            ((SiteBluePrint)siteObj).setAddress(email); // 생성한 이메일 주소를 설정
        }
        this.siteObj = siteObj;
    }
    
    Object getSiteObj() {
        return this.siteObj;
    }
}

class NoneGenericEx {
    public static void main(String[] args) {
        FoodSite foodUser = new FoodSite("hanggicheum");
        BeautySite beautyUser = new BeautySite("handsome123");
        EmailAddressMaker foodEmail = new EmailAddressMaker();
        EmailAddressMaker beautyEmail = new EmailAddressMaker();
        EmailAddressMaker wrongEmail = new EmailAddressMaker();

        foodEmail.createAddress(foodUser);
        beautyEmail.createAddress(beautyUser);

        // 잘못된 입력. 그런데 에러가 발생하지 않는다.
        wrongEmail.createAddress("굳");

        // 형변환(type casting) 필요.
        foodUser = (FoodSite)foodEmail.getSiteObj();
        beautyUser = (BeautySite)beautyEmail.getSiteObj();

        System.out.println(foodUser.address);
        System.out.println(beautyUser.address);
        System.out.println(wrongEmail.getSiteObj());  // 잘못된 코드.
    }
}

```

```java
hanggicheum@foodmarket.com
handsome123@beautysite.com
굳
```

위 예제에서 개발자는 두 가지 실수를 했다.

1. `EmailAddressMaker.createAddress()` 메서드의 매개변수 siteObj를 최상위 클래스인 Object로 자료형을 정했다. 이 경우 사이트 클래스가 아닌 엉뚱한 객체가 매개변수로 전달되어도 에러가 발생하지 않으며, 버그가 발생해도 어디서 버그가 났는지 파악하기 힘들어진다. 이를 방지하려면 Object 대신 모든 사이트 클래스들의 추상 클래스인 SiteBluePrint를 자료형으로 사용했어야 했다. 
2. `EmailAddressMaker.createAddress()` 메서드의 매개변수는 원래 사이트를 추상화한 SiteBluePrint 추상 클래스를 상속받는 사이트 클래스들의 인스턴스만 받도록 의도되었다. 그런데 위 코드의 “잘못된 입력”이라고 주석을 달아놓은 코드를 보면 엉뚱하게 String 객체를 인자로 넘기고 있다. 그 결과 엉뚱한 결과를 얻었지만 정작 에러가 발생하지 않아 버그의 원인을 찾기가 어려워진다. 그리고 설령 에러가 발생하더라도, 컴파일 단계에서가 아닌 실행 도중 에러가 발생해서 이를 미리 알고 대비하기가 힘들어진다. 

방금 언급한 첫 번째 실수의 해결책도 추상 클래스를 정의했기에 엉뚱한 자료형 데이터를 받는 것을 방지할 수 있는 것이다. 만약 추상 클래스를 사용하지 않는 경우에는 최상위 클래스 Object를 사용해야할 수밖에 없고, 이 경우 버그를 발견하기 힘들어지거나 컴파일 단계에서 에러를 발견하기 어려워진다. 

실수 외에도, 정적 타입 언어인 자바에서 다형성을 사용하다보니 귀찮아지는 점도 있었다. 그건 바로 객체를 메서드에 전달하거나 반환받을 때마다 강제 형변환을 일일이 명시해야 한다는 점이다. 이러한 형변환을 하지 않으면 에러가 발생한다. 이러한 문제점들을 한 번에 해결할 수 있는 방법이 있을까? 

# Generic 개념과 문법

위에서 언급한 문제점들을 한 번에 해결할 수 있는 것이 있다. 바로 Generic(제네릭)이다. 자바에서 Generic은 클래스나 메서드에서 사용할 자료형을 선언부에서 미리 지정하지 않고, 호출부에서 자료형을 지정하는 방법이다. 

자바에서 제네릭을 사용하는 방법은 클래스명의 뒤 또는 메서드 선언부의 반환형 앞에 화살괄호<>를 적은 후, 그 안에 타입 매개변수(Type parameter)를 적는다. 그러면 클래스 내부에서 타입 매개변수를 자료형으로 활용할 수 있게된다. 다음은 위 예제의 EmailAddressMaker 클래스에 제네릭을 적용한 코드이다. 

```java
class EmailAddressMaker2<T extends SiteBluePrint2> {
    private T siteObj;

    void createAddress(T siteObj) {
        // 형변환 안해도 됨. 이미 제네릭에서 타입 매개변수의 타입을 SiteBluePrint2 객체로 한정했기 때문.
        // siteName을 가져오고, 이메일 주소를 생성하는 로직
        String email = siteObj.nickName + "@" + siteObj.getSiteName() + ".com";
        siteObj.setAddress(email); // 생성한 이메일 주소를 설정
        this.siteObj = siteObj;
    }

    T getSiteObj() {
        return this.siteObj;
    }
}
```

제네릭이 적용된 클래스를 인스턴스화하여 사용할 땐 다음의 형태처럼 사용하면 된다. 

```java
EmailAddressMaker2<FoodSite2> foodEmail = new EmailAddressMaker2<FoodSite2>();
EmailAddressMaker2<BeautySite2> beautyEmail = new EmailAddressMaker2<>();
```

변수 자료형 이름 뒤에 화살괄호 <>를 적은 후, 그 안에 원하는 자료형을 대입한다. 이 때, 자료형의 이름과 화살괄호 부분을 묶어 “매개변수화 타입(Parameterized type)”이라 부른다. 그 후, 해당 변수에 객체를 생성해 대입하는 부분에서는 객체명과 괄호 () 사이에 화살괄호 <>를 적는다. 여기에 자료형을 적어넣으면 된다. 그런데 어차피 매개변수화 티입에서 그 자료형을 언급했기에 자바 7 버전 이후부터는 생략이 가능하다. 이렇게 객체를 생성하는 부분에서의 화살괄호 부분을 타입 인수(Type argument)라고 한다. 다시 정리하면 다음과 같다. 

- 타입 매개변수 (Type parameter) : 클래스 또는 메서드 선언부에서 자료형을 인자로 받기 위한 매개변수.
- 매개변수화 타입 (Parameterized type) : 변수 선언부에서, 자료형 이름과 화살괄호 부분을 합친 부분. 위 예제 1-3에서, foodEmail 변수명 앞의 `EmailAddressMaker2<FoodSite2>` 이 이에 해당된다.
- 티입 인수 (Type argument) : 제네릭을 적용한 클래스를 인스턴스화할 때, 객체명과 괄호 사이의 화살괄호 부분. 위 예제 1-3에서, new `EmailAddressMaker2<FoodSite2>();` 의 `<FoodSite2>`가 이에 해당된다.

타입 매개변수명을 짓는 관례는 다음과 같다. 다음의 관례를 지키지 않는다고 해서 문제가 되지는 않는다고 한다. 

- 한 문자로 짓는다.
- 대문자로 짓는다.
- 타입 매개변수의 성질에 따라 다음과 같이 지을 수 있다.

| 타입 매개변수명 | 설명 |
| --- | --- |
| E | Element |
| K | Key |
| N | Number |
| T | Type |
| V | Value |

한 편, 타입 매개변수에는 어떠한 자료형도 대입할 수 있는데, 만약 특정 클래스와 그 자식 클래스 자료형만 입력받도록 제한하고 싶다면 extends 키워드를 이용하면 된다. 

```java
class MyClass<T extends Number> {}
```

위 코드에서는 Number 래퍼 클래스와 이를 상속받는 클래스들만 자료형으로 받을 수 있다. 

제네릭은 타입 매개변수를 두 개 이상 선언할 수도 있다. 

```java
class MyClass<T1, T2> {}
```

한 편, 이제껏 클래스에 제네릭을 적용하는 것만 보았는데, 제네릭은 메서드에서도 정의 가능하다. 메서드 선언부에서, 반환형 이름 전에 타입 매개변수를 지정하면 된다. 

```java
class DataDisplayer {
    public static <T extends Number> void printDataWithType(T data) {
        System.out.print(data + " ");
        if (data instanceof Integer) {
            System.out.println("Integer");
        } else if (data instanceof Double) {
            System.out.println("Double");
        } else {
            System.out.println("Number class");
        }
    }
}

class GenericMethodEx {
    public static void main(String[] args) {
        DataDisplayer.<Integer>printDataWithType(12);
        DataDisplayer.printDataWithType(12.4); // 타입 인수 생략 가능
    }
}

```

```java
12 Integer 
12.4 Double
```

제네릭의 타입 인수에는 클래스 타입만을 지정할 수 있고, 기본 자료형을 지정할 수 없다. 만약 기본 자료형을 지정하고자 한다면 대신 래퍼 클래스를 이용하면 된다. 

제네릭을 정리해보면, 제네릭은 어쩌면 메서드와 매우 비슷하다고 볼 수 있다. 타입 매개변수와 타입 인수는 메서드 선언부의 매개변수와 호출부의 인자와 거의 동일하다고 보면 된다. 단지 값을 받는 게 아니라 자료형 자체를 인자로 받는 것이 차이일 뿐이다. 

# 제네릭 적용 예시와 제네릭 장점

다음은 제네릭을 이용하여 이전 예제 1-1을 수정한 코드이다. 

```java
abstract class SiteBluePrint2 {
    String nickName;
    String siteName;
    String address;

    SiteBluePrint2(String nickName) {
        this.nickName = nickName;
    }

    // siteName을 가져오는 getter 메서드 추가
    public String getSiteName() {
        return this.siteName;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}

class FoodSite2 extends SiteBluePrint2 {
    FoodSite2(String nickName) {
        super(nickName);
        this.siteName = "foodmarket";
    }
}

class BeautySite2 extends SiteBluePrint2 {
    BeautySite2(String nickName) {
        super(nickName);
        this.siteName = "beautysite";
    }
}

class EmailAddressMaker2<T extends SiteBluePrint2> {
    private T siteObj;

    void createAddress(T siteObj) {
        // 형변환 안해도 됨. 이미 제네릭에서 타입 매개변수의 타입을 SiteBluePrint2 객체로 한정했기 때문.
        // siteName을 가져오고, 이메일 주소를 생성하는 로직
        String email = siteObj.nickName + "@" + siteObj.getSiteName() + ".com";
        siteObj.setAddress(email); // 생성한 이메일 주소를 설정
        this.siteObj = siteObj;
    }

    T getSiteObj() {
        return this.siteObj;
    }
}

class GenericEx {
    public static void main(String[] args) {
        FoodSite2 foodUser = new FoodSite2("hanggicheum");
        BeautySite2 beautyUser = new BeautySite2("handsome123");
        EmailAddressMaker2<FoodSite2> foodEmail = new EmailAddressMaker2<>();
        EmailAddressMaker2<BeautySite2> beautyEmail = new EmailAddressMaker2<>();
        EmailAddressMaker2<FoodSite2> wrongEmail = new EmailAddressMaker2<>(); // 잘못된 코드.

        foodEmail.createAddress(foodUser);
        beautyEmail.createAddress(beautyUser);

        // 잘못된 입력. 에러가 발생함으로써 개발자의 실수를 바로 잡을 수 있음.
        //wrongEmail.createAddress("굳");

        // 형 변환 안해도 됨.
        foodUser = foodEmail.getSiteObj();
        beautyUser = beautyEmail.getSiteObj();

        System.out.println(foodUser.address);
        System.out.println(beautyUser.address);
        //System.out.println(wrongEmail.getSiteObj()); // 잘못된 코드.
    }
}
```

```java
hanggicheum@foodmarket.com
handsome123@beautysite.com
```

위 예제에서, `//wrongEmail.createAddress("굳");` 부분을 주석 해제한 후에 명령 프롬프트를 이용하여 컴파일을 시도해보겠다. 

```java
> javac GenericEx.java -encoding utf-8
GenericEx.java:62: error: incompatible types: String cannot be converted to FoodSite2
        wrongEmail.createAddress("굳");
                                 ^
Note: Some messages have been simplified; recompile with -Xdiags:verbose to get full output
1 error
```

위 예제 1-4을 vscode 창에서 F5 키를 눌러 실행시켜보면 다음의 예외 메시지를 얻는다. 

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
        The method createAddress(FoodSite2) in the type EmailAddressMaker2<FoodSite2> is not applicable for the arguments (String)

        at GenericEx.main(GenericEx.java:62)
```

어느 방법으로 실행을 하든, 제네릭 클래스의 메서드에 잘못된 자료형 데이터를 대입하면 컴파일 단계에서 이를 에러로 잡아준다. 이렇게 되면 개발자 입장에서는 프로그램을 실행하기 전에 미리 에러를 파악할 수 있어 문제를 조속히 해결할 수 있다는 이점을 얻게 된다. 

위 예제 코드를 보면 알겠지만, 제네릭을 사용하는 또 다른 이점은 다형성을 사용할 때 강제 형변환이 필요없게 된다는 것이다. 

이 외의 제네릭의 장점들은 다음에 정리하였다.

## 제네릭의 장점

제네릭을 사용할 때의 장점들을 정리하면 다음과 같다. 

- 강한 자료형 체크를 통한 타입 안정성 확보 : 제네릭 사용 시 컴파일 단계에서 자료형을 제대로 사용하였는지 확인할 수 있다. 프로그램 실행 도중에 잘못된 자료형 사용으로 인해 발생하는 버그, 에러는 그 원인을 파악하기가 어렵다. 그리고 만약 그 프로그램이 대다수의 고객들이 사용하는 것이라면 무수히 많은 컴플레인이 들어올 것이다. 이러한 문제점들을 컴파일 단계에서 미리 확인하고 해결할 수 있다.
- 명시적 형변환 제거: 위 예제에서 살펴보았듯이, 다형성을 이용할 때 형변환을 명시해야 하는 번거로움이 있었는데, 제네릭을 사용하면 이러한 번거로움이 사라진다. (참고: 형변환을 type casting이라고도 한다)
- 코드 중복 방지 : 다형성을 이용할 때 제네릭을 사용하지 않는다면 단지 자료형이 다르다는 이유로 같은 구조의 메서드들을 오버로딩을 통해 일일이 구현해야 한다. 이 경우 코드가 중복되어 코드가 불필요하게 길어지고, 그에 따라 가독성도 떨어질 것이다. 하지만 제네릭을 이용하면 자료형을 인수로 전달하기만 하면 어떤 자료형이 되었건 입력되는 데이터들을 모두 한 메서드에서 한 번에 처리할 수 있는 이점이 존재한다.
- 코드 유연성 및 명확성 확보 : 여러 종류의 자료형 데이터들을 처리할 수 있는 단일한 코드를 구현할 수 있어 유연성을 확보할 수 있다. 또한, 여러 개의 값들을 받는 스택, 큐 등의 자료구조에서 제네릭을 적용하면 해당 자료구조 내부에 들어있는 값들의 자료형이 무엇인지도 확인할 수 있는 장점도 존재한다.

---
References

[1] [Why Use Generics? (The Java™ Tutorials >        
            Learning the Java Language > Generics (Updated))](https://docs.oracle.com/javase/tutorial/java/generics/why.html)

[2] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)