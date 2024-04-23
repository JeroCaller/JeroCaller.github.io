---
title: "[Java] 객체와 클래스"
category: "Java"
tag: ["java", "자바", "객체", "클래스", "object", "class"]
---

# 객체와 클래스

객체는 현실 세계에서 서로 독립적인 모든 사물, 개념들을 일컫는다. 보통 프로그래밍은 현실에서의 문제를 해결하기 위해 사용되는데, 이러한 현실에서 다루고자 하는 객체를 소프트웨어 내 객체로 표현하기 위해 사용되는 것이 클래스(class)이다. 클래스는 데이터에 해당되는 멤버 변수(필드)와, 데이터를 토대로 어떠한 동작을 해내는 멤버 함수(메서드)로 구성된다. 현실 세계에서 다루고자 하는 데이터 및 행위들은 매우 많고 복잡하므로, 어떤 문제에 대해 어떻게 해결하고자 하는지 그 목적을 정한 후, 그 목적에 맞게 중요한 데이터 및 행위만을 추출하여 이를 프로그램 내 클래스의 맴버 변수와 멤버 함수에 반영하는 것을 “추상화(abstraction)”라고 한다. 예를 들어 같은 “사람”이라는 객체에 대해서도 이를 게임 내에서의 인간 캐릭터로 다루고자 한다면 체력, 필살기, 마나, 몬스터와 마주했을 때 공격 행위 등을 추출하여 추상화할 수도 있고, 어떤 홈페이지에서의 회원 정보로서 다루고자 한다면 유저 닉네임, 나이, 직업, 상품 결제 행위 등을 추상화시킬 수 있는 것이다. 

클래스는 객체를 추상화해놓은 일종의 설계도이다. 이 클래스를 토대로 객체를 생성할 수 있으며, 이렇게 클래스로부터 생성된 객체를 “인스턴스(instance)”라고도 한다. 

다음은 클래스를 정의하고 이를 main 메서드 내부에서 객체로 생성하여 이용하는 예제이다. 

```java
public class ClassAndObject {
    public static void main(String[] args) {
        // 클래스의 인스턴스 생성 및 변수에 할당.
        SiteMember me = new SiteMember();
        
        // 필드 접근
        me.name = "가나다";
        me.userName = "GaNaDa";
        me.age = 22;
        me.job = "IT 개발";
        me.point = 1000;

        // 메서드 호출.
        me.welcome();
        me.printProfile();
    }
}

// 사용자 정의 클래스
class SiteMember {
    // 필드(데이터)
    String name;
    String userName;
    int age;
    String job;
    int point;

    // 메서드(동작)
    void welcome() {
        System.out.printf("어서오세요, %s님.\n", userName);
    }

    void printProfile() {
        System.out.println("===== 회원 정보 =====");
        System.out.println("이름 : " + name);
        System.out.println("유저 닉네임 : " + userName);
        System.out.println("나이 : " + age);
        System.out.println("직업 : " + job);
        System.out.println("현재 포인트 : " + point);
        System.out.println("=====================");
    }
}
```

```java
어서오세요, GaNaDa님.
===== 회원 정보 =====
이름 : 가나다
유저 닉네임 : GaNaDa
나이 : 22
직업 : IT 개발
현재 포인트 : 1000
=====================
```

위 예제에서, 우선 SiteMember라는 클래스를 정의하였다. 해당 클래스 내부에는 name, age 등의 필드와 welcome(), printProfile()의 메서드가 정의되어 있다. main 메서드 내부에서 이 클래스를 생성하기 위해서 다음의 코드가 쓰였다. 

`SiteMember me = new SiteMember();`

변수명 앞에는 클래스 자료형을 작성하였다. 여기서는 SiteMember 객체를 사용할 것이므로 해당 클래스명을 기입하였다. 그 뒤에 변수명을 선언한 뒤, 대입 연산자 뒤로 해당 클래스명과 그 뒤에 괄호를 적은 뒤, 그 앞에는 해당 클래스로부터 새로운 객체를 생성한다는 뜻의 new 키워드가 작성되었다. 이렇게 생성된 SiteMember 객체가 me 변수에 할당되는 것이다. 

main 메서드와 달리, 사용자 정의 클래스 내의 메서드에는 static 키워드가 붙어있지 않다. new 키워드를 통해 객체를 생성하는 경우 해당 객체 내 메서드들에는 static을 붙이지 않는다. 

생성된 객체의 필드 및 메서드에 접근하기 위해서 마침표(.)를 사용하여 객체.필드 또는 객체.메서드 와 같이 객체와 객체 내 필드, 메서드를 구분하여 접근한다. 

# 오버로딩 (Overloading)

동일한 클래스 내에서 동일한 메서드명을 사용하여 매개변수의 자료형 및 개수, 반환값 타입, 내부 코드를 조금씩 바꿔 정의하는 것을 오버로딩이라 한다. 

```java
class TotalObj {

    /**
     * 1부터 n까지의 자연수를 모두 더한 값을 반환.
     * @param n 자연수
     * @return 1부터 n까지의 모든 자연수의 합.
     */
    int getTotalOfSeries(int n) {
        int total = 0;
        for(int i = 1; i <= n; i++) {
            total += i;
        }
        return total;
    }

    /**
     * 정수 start부터 end까지의 모든 정수를 더한 값을 반환.
     * @param start 시작 부분의 정수
     * @param end 끝 부분의 정수
     * @return start부터 end까지의 모든 정수들의 합
     */
    int getTotalOfSeries(int start, int end) {
        int total = 0;
        for(int i = start; i <= end; i++) {
            total += i;
        }
        return total;
    }

    /**
     * 0부터 정수 또는 실수 n까지 interval 간격으로 존재하는 모든 실수
     * 또는 정수들을 모두 더한 값을 반환.
     * @param n  합의 끝 부분이 되는 실수.
     * @param interval  간격
     * @return  0부터 n까지의 interval 간격으로 더한 결과값.
     */
    double getTotalOfSeries(double n, double interval) {
        double total = 0.0;
        for(double i = 0.0; i <= n; i = i + interval) {
            total += i;
        }
        return total;
    }
}

public class Overloading {
    public static void main(String[] args) {
        TotalObj calc = new TotalObj();
        int s1 = calc.getTotalOfSeries(100);
        int s2 = calc.getTotalOfSeries(-5, 100);
        double s3 = calc.getTotalOfSeries(10.5, 3.3);

        System.out.println(s1);
        System.out.println(s2);
        System.out.println(s3);
    }
}

```

```java
5050
5035
19.799999999999997
```

위 예제에서, TotalObj 클래스 내부에서 getTotalOfSeries 이라는 똑같은 메서드명을 반환값의 타입 및 매개변수를 달리하여 정의한 것을 볼 수 있다. 이것이 오버로딩이다. 

메서드명과 매개변수를 묶어 시그니처(signature)라 하는데, 자바에서는 메서드명이 아닌 시그니처를 기준으로 구분하기에 오버로딩이 가능한 것이다. 이를 거꾸로 생각해보면, 시그니처가 똑같은 메서드를 새로 정의하려고 하면 에러가 난다는 뜻이기도 하다. 

System.out.println() 도 오버로딩이 적용된 메서드이다. 

# 생성자 (Constructor)

생성자는 인스턴스 생성 시에만 호출되는 메서드로, 주로 해당 객체의 필드값을 초기화할 때 사용한다. 파이썬의 클래스 내부의 \_\_init\_\_() 메서드와 동일하다.

변수에 클래스의 인스턴스를 생성하여 할당할 때, new 키워드 다음에 나오는 클래스명() 이 바로 생성자이다. 

```java
HomePageMember me = new HomePageMember();
```

클래스를 정의할 때에도 해당 클래스명과 동일한 생성자 메서드가 생성된다. 다만, 사용자가 따로 생성자를 재정의하지 않으면 자동으로 매개변수 및 반환값이 없는 생성자 메서드가 생성되는데, 이를 디폴트 생성자라 한다. 디폴트 생성자는 기본적으로 생략해도 인스턴스화 시에 자동으로 생성된다. 

```java
class HomePageMember {
    // 필드(데이터)
    String name;
    String userName;
    int age;
    String job;
    int point;

    // default construtor
    // 디폴트 생성자는 반환형도, 매개변수도 없다. 
    HomePageMember() {

    }
```

그런데 클래스의 인스턴스화와 동시에 해당 인스턴스의 필드들을 초기화하고 싶다. 다음과 같이 말이다. 

```java
HomePageMember me2 = new HomePageMember("가나다", "GaNaDa", 22, "IT 개발", 2000);
```

이를 위해선, 클래스 내 디폴트 생성자를 명시적으로 정의한 다음, 오버로딩한 또 다른 생성자 시그니처를 정의하면 된다. 다음은 인스턴스 생성 시 사용자가 해당 인스턴스의 필드에 맞는 정보들을 입력하면 이를 초기화하도록 생성자를 오버로딩한 예제이다. 

```java
public class ConstructorEx {
    public static void main(String[] args) {
        HomePageMember me = new HomePageMember();
        me.printProfile();

        HomePageMember me2 = new HomePageMember("가나다", "GaNaDa", 22, "IT 개발", 2000);
        me2.printProfile();
    }
}

class HomePageMember {
    // 필드(데이터)
    String name;
    String userName;
    int age;
    String job;
    int point;

    // 디폴트 생성자 명시적 정의.
    HomePageMember() {
        name = "홍길동";
        userName = "EastWest";
        age = 20;
        job = "유통업";
        point = 1000;
    }
    HomePageMember(String n, String un, int ag, String j, int p) {
        name = n;
        userName = un;
        age = ag;
        job = j;
        point = p;
    }

    // 메서드(동작)
    void welcome() {
        System.out.printf("어서오세요, %s님.\n", userName);
    }

    void printProfile() {
        System.out.println("===== 회원 정보 =====");
        System.out.println("이름 : " + name);
        System.out.println("유저 닉네임 : " + userName);
        System.out.println("나이 : " + age);
        System.out.println("직업 : " + job);
        System.out.println("현재 포인트 : " + point);
        System.out.println("=====================");
    }
}
```

```java
===== 회원 정보 =====
이름 : 홍길동
유저 닉네임 : EastWest
나이 : 20
직업 : 유통업
현재 포인트 : 1000
=====================
===== 회원 정보 =====
이름 : 가나다
유저 닉네임 : GaNaDa
나이 : 22
직업 : IT 개발
현재 포인트 : 2000
=====================
```

위 예제에서는 인스턴스 생성 시 인스턴스 내 필드들도 같이 초기화하도록 하기 위해 디폴트 생성자를 오버로딩하여, 각 필드에 들어갈 매개변수들을 차례대로 지정하고, 이를 필드에 할당하도록 설게하였다. 설령 사용자가 객체 생성 시 생성자에 아무런 인자를 대입하지 않아도, 자동으로 기본값들이 필드에 할당되록 디폴트 생성자 내부에 코드를 설계하였다. 

만약 디폴트 생성자를 명시적으로 정의하지 않으면 생성자를 오버로딩한 시그니처도 사용할 수 없다. 위 예제에서 디폴트 생성자 부분만 지우면 에러가 발생한다. 따라서 생성자를 오버로딩하고자 한다면 중괄호 코드 블럭을 비우더라도 디폴트 생성자를 명시적으로 정의해야 한다. 

# 접근 제한자

자바에는 어떤 클래스의 멤버에 대한 접근을 제한할 것인지, 제한한다면 어떻게 할 것인지에 대한 접근 제한자가 존재한다. 

| 접근 제한자 | 설명 |
| --- | --- |
| public | 외부 클래스의 어디서든지 접근 가능. |
| protected | 같은 패키지 내부 및 상속 관계의 클래스에서만 접근 가능. |
| default | 같은 패키지 내부에서만 접근 가능. 클래스 정의 시 class 키워드 앞에 아무것도 작성하지 않으면 됨. |
| private | 같은 클래스 내부에서만 접근 가능 |

앞선 예제 3-3에서, HomePageMember 클래스는 정의 시 class 키워드 앞에 아무것도 작성하지 않았으므로 해당 클래스는 default로 정의되었다. 따라서 같은 모듈 내부에 있는 main 메서드에서 해당 클래스에 접근할 수 있던 것이다. 

한 편, private를 적용한 클래스에서는, 해당 클래스 외부에서 해당 클래스 내의 멤버, 즉 필드와 메서드에 접근할 수 없다. 위 예제 코드에서 HomePageMember 클래스를 private로 정의하였다면 main 메서드에서도 해당 클래스를 인스턴스화하고 이를 사용할 수 없었을 것이다. 

private로 정의된 클래스에서는 외부 클래스에서 해당 클래스 내부의 변수에 접근할 수 없지만, 변수값을 할당하는 setter 메서드와 변수값을 반환하는 getter 메서드를 이용하면 간접적으로 외부에서 해당 변수에 접근이 가능하다. 

```java
public class InfoHiding {
    public static void main(String[] args) {
        PageMemeber me = new PageMemeber("가나다", "GaNaDa", 22, "IT 개발", 3000);
        me.printProfile();

        // 다음의 두 줄은 에러 발생.
        //me.point += 4000;
        //System.out.println(me.point);

        me.setPoint(5000);
        System.out.println(me.getPoint());
    }
}

class PageMemeber {
    String name;
    String userName;
    int age;
    String job;
    private int point;

    PageMemeber() {
        name = "홍길동";
        userName = "EastWest";
        age = 20;
        job = "유통업";
        point = 1000;
    }
    PageMemeber(String n, String un, int ag, String j, int p) {
        name = n;
        userName = un;
        age = ag;
        job = j;
        point = p;
    }

    void welcome() {
        System.out.printf("어서오세요, %s님.\n", userName);
    }

    void printProfile() {
        System.out.println("===== 회원 정보 =====");
        System.out.println("이름 : " + name);
        System.out.println("유저 닉네임 : " + userName);
        System.out.println("나이 : " + age);
        System.out.println("직업 : " + job);
        System.out.println("현재 포인트 : " + point);
        System.out.println("=====================");
    }

    // point 변수에 대한 getter 메서드.
    public int getPoint() {
        return point;
    }

    // point 변수에 대한 setter 메서드.
    public void setPoint(int point) {
        if (point < 0) {
            System.out.println("포인터가 음수가 될 수 없습니다.");
            return;
        }
        this.point = point;
    }
}
```

```java
===== 회원 정보 =====
이름 : 가나다
유저 닉네임 : GaNaDa
나이 : 22
직업 : IT 개발
현재 포인트 : 3000
=====================
5000
```

위 예제에서는 main 메서드 내부와, PageMember 클래스 내부의 point 변수, getPoint, setPoint 메서드에 주목하면 된다. 먼저 PageMember 클래스 내부의 변수 point를 private로 지정하였다. 그 결과, main 메서드 내에서 me.point와 같이 해당 변수에 직접적으로 접근할 수 없다. 따라서 이 경우 getter, setter 메서드인 getPoint(), setPoint() 메서드를 이용해야한다. 

이렇게 getter, setter 메서드를 정의하여 private 변수에 접근하도록 고안하는 이유는 첫째로 해당 변수에 값 할당 시 예기치 않은 값이 할당되어 이후 버그 발생을 사전에 방지하고자 함이고, 둘째로 해당 변수를 새 값으로 할당하거나 반환할 때 일련의 가공을 할 코드를 같이 작성하여 해당 기능을 실행한 후에 해당 변수에 값을 재할당하거나 반환하도록 할 수 있기 때문이다. 위 예제에서는 setter 메서드에 매개변수로 입력된 값이 원치 않은 값은 아닌지 검사하는 코드를 작성하였는데, 이 뿐만 아니라, 새 값이 할당되면 해당 변수와 연관된 다른 멤버 변수들의 정보도 자동으로 업데이트 하는 기능 등을 추가할 수도 있는 것이다. 

이렇게 private로 특정 변수와 메서드에 대한 외부로부터의 접근을 제한하고, 필요에 따라 getter, setter 메서드를 정의하여 간접적으로 해당 개체에 접근하도록 고안하는 것을 정보 은닉화(infomation hiding)이라고 한다.

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)