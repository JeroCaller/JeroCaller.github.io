---
title: "[Java] 상속 (Inheritance)"
category: "Java"
tag: ["java", "자바", "상속", "inheritance", "final", "추상 클래스", "abstract", "인터페이스", "interface", "다형성", "polymorphism"]
---

자바는 객체지향 언어이고, 따라서 파이썬에서와 같이 상속이란 개념도 존재한다. 

상속의 이점

- 클래스 간 전체적인 계층 구조 파악에 용이.
- 기존 클래스에 있는 멤버들을 재사용함으로서 코드 길이의 중복을 피할 수 있다.
- 상속된 클래스에 새로운 맴버 추가에 유리하다.
- 슈퍼 클래스에서 수정할 것이 있으면 슈퍼 클래스에서만 수정하면 되므로 유지보수에 좋다. 만약 상속이란 개념 없이, 중복되는 내용들을 포함하는 클래스들로 구성했다면, 중복되는 내용에 수정이 필요하면 모든 클래스에 대해 일일이 찾아서 수정해야 하는 번거로움이 있다.

단, 자바에서는 private 접근 제한자가 붙은 멤버에 대해서는 하위 클래스로 상속되지 않는다. 

자바에서는 둘 이상의 상위 클래스들을 동시에 상속받는 다중 상속 기능을 지원하지 않는다. 상위 클래스들 중 서로 같은 이름을 가진 변수 및 메서드가 있으면 자식 클래스에서 이들 중 어느 것을 사용해야할지에 대해 판단하기 어렵기 때문이라고 한다. 

자바에서는 상속 시 클래스명 오른쪽에 extends라 적고, 그 뒤에 상속할 슈퍼 클래스명을 입력한다. 예를 들어 A가 슈퍼 클래스, B가 상속받을 서브 클래스라 한다면 다음과 같이 작성한다. 

```java
class B extends A {
    // 코드.
}
```

다음은 상속에 대한 예제이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;

class SiteUser {
    String userName;
    String siteName = "Yeah";
    int myPoint;
    ArrayList shoppingBasket = null;

    SiteUser(String userName, int point) {
        this.userName = userName;
        this.myPoint = point;
    }

    void printProfile() {
        System.out.println("======================");
        System.out.println("사이트명: " + siteName);
        System.out.println("유저 네임 : " + userName);
        System.out.println("현재 보유 포인트 : " + myPoint);
        System.out.println("현재 장바구니 : " + shoppingBasket);
        System.out.println("======================");
    }
}

class FoodMarketUser extends SiteUser {
    String favoriteFood;

    FoodMarketUser(String userName, int point, String favoriteFood) {
        super(userName, point);
        this.favoriteFood = favoriteFood;
        siteName = "푸드 마켓";
    }

    void printFoodMarket() {
        printProfile();
        System.out.println("가장 좋아하는 음식 : " + favoriteFood);
        System.out.println("======================");
    }
}

class BookStoreUser extends SiteUser {
    int myBookNums;

    BookStoreUser(String userName, int point, int bookNums) {
        super(userName, point);
        this.myBookNums = bookNums;
        siteName = "서점";
    }

    void printBookStore() {
        printProfile();
        System.out.println("현재 보유 책 권수 : " + myBookNums);
        System.out.println("======================");
    }
}

public class MySite {
    public static void main(String[] args) {
        FoodMarketUser foodUser = new FoodMarketUser("healthyFood", 2000, "사과");
        foodUser.shoppingBasket = new ArrayList(Arrays.asList("바나나", "사과"));
        foodUser.printFoodMarket();

        BookStoreUser bookUser = new BookStoreUser("bookAndLib", 1000, 12);
        bookUser.shoppingBasket = new ArrayList(Arrays.asList("자바에 대한 모든 것", "코딩의 미래"));
        bookUser.printBookStore();
    }
}

```

```java
======================
사이트명: 푸드 마켓
유저 네임 : healthyFood
현재 보유 포인트 : 2000
현재 장바구니 : [바나나, 사과]
======================
가장 좋아하는 음식 : 사과
======================
======================
사이트명: 서점
유저 네임 : bookAndLib
현재 보유 포인트 : 1000
현재 장바구니 : [자바에 대한 모든 것, 코딩의 미래]
======================
현재 보유 책 권수 : 12
======================
```

위 예제는 사이트의 회원 정보를 객체화한 것으로, 먼저 사이트들에서 공통적으로 볼 수 있는 유저 네임, 사이트명, 포인트, 장바구니 목록들을 멤버 변수로 추상화하였고, 이를 출력하는 메서드도 정의하였다. 그 뒤로 온라인 푸드 마켓 사이트와 온라인 서점 사이트를 추상화한 클래스들을 정의하였다. 이 때 앞서 만든 SiteUser 클래스를 상속하면서 정의하였다. 이를 통해 하위 클래스는 상위 클래스에서 정의한 멤버 변수들을 또 정의하지 않아도 그대로 사용할 수 있으며, 상위 클래스에는 없는 것들을 추가하여 정의할 수 있게 된다. 

한 편, 하위 클래스에서 상위 클래스의 것을 직접 불러와야할 경우가 생긴다면 super 키워드를 사용하면 된다. 위 예제의 하위 클래스들을 보면 생성자 코드 내부에 super() 키워드를 이용하여 상위 클래스의 생성자를 먼저 호출하여 초기화하도록 한 것을 볼 수 있다. 물론, super 키워드를 반드시 사용하지 않아도 상위 클래스의 멤버 변수를 사용할 수 있다. 위 예제의 각 하위 클래스들의 print 메서드 내부를 보면, SiteUser 클래스의 메서드를 그대로 호출하는 것을 볼 수 있다. 여기에는 super 키워드가 쓰이지 않았음에도, 상위 클래스의 멤버들을 그대로 사용할 수 있음을 알 수 있다. 

# 오버라이딩(overriding)

오버라이딩은 부모 클래스 내 메서드명을 그대로 사용하고, 그 내부의 코드는 추가 및 변형시켜서 사용하도록 부모 클래스의 메서드를 자식 클래스 내에서 재정의하는 것을 의미한다. 동일한 클래스 내부에서 똑같은 메서드명을 가지고 여러 개의 메서드들을 정의하는 오버로딩(overloading)과 달리, 오버라이딩을 할 때에는 부모 클래스의 해당 메서드의 매개변수 개수와 그 자료형, 반환값의 자료형이 모두 동일해야 한다. 즉, 오버로딩은 시그니처가 달라야하지만, 오버라이딩은 시그니처가 같아야 한다. 

앞서 사용한 예제에 오버라이딩을 적용한 예제이다. 각 하위 클래스의 printProfile() 메서드들이 오버라이딩을 적용한 예이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;

class SiteUser {
    String userName;
    String siteName = "Yeah";
    int myPoint;
    ArrayList shoppingBasket = null;

    SiteUser(String userName, int point) {
        this.userName = userName;
        this.myPoint = point;
    }

    void printProfile() {
        System.out.println("======================");
        System.out.println("사이트명: " + siteName);
        System.out.println("유저 네임 : " + userName);
        System.out.println("현재 보유 포인트 : " + myPoint);
        System.out.println("현재 장바구니 : " + shoppingBasket);
        System.out.println("======================");
    }
}

class FoodMarketUser extends SiteUser {
    String favoriteFood;

    FoodMarketUser(String userName, int point, String favoriteFood) {
        super(userName, point);
        this.favoriteFood = favoriteFood;
        siteName = "푸드 마켓";
    }

    void printProfile() {
        super.printProfile();
        System.out.println("가장 좋아하는 음식 : " + favoriteFood);
        System.out.println("======================");
    }
}

class BookStoreUser extends SiteUser {
    int myBookNums;

    BookStoreUser(String userName, int point, int bookNums) {
        super(userName, point);
        this.myBookNums = bookNums;
        siteName = "서점";
    }

    void printProfile() {
        super.printProfile();
        System.out.println("현재 보유 책 권수 : " + myBookNums);
        System.out.println("======================");
    }
}

public class MySite {
    public static void main(String[] args) {
        FoodMarketUser foodUser = new FoodMarketUser("healthyFood", 2000, "사과");
        foodUser.shoppingBasket = new ArrayList(Arrays.asList("바나나", "사과"));
        foodUser.printProfile();

        BookStoreUser bookUser = new BookStoreUser("bookAndLib", 1000, 12);
        bookUser.shoppingBasket = new ArrayList(Arrays.asList("자바에 대한 모든 것", "코딩의 미래"));
        bookUser.printProfile();
    }
}

```

상위 클래스의 메서드를 오버라이딩한 후, 해당 메서드 내부에서 상위 클래스의 메서드를 사용하고자 하는 경우, 이미 오버라이딩한 메서드명과 겹쳐서 어떤 것이 상위 클래스의 메서드이고 어떤 것이 하위 클래스의 메서드인지 구분할 수 없다. 이럴 때 상위 클래스의 메서드명 앞에 super 키워드를 마침표(.)와 함께 붙여 구분하는 것이다. BookStoreUser 하위 클래스를 예로 들면, printProfile() 이라고 메서드 이름을 정의하는 구간은 해당 클래스의 상위 클래스의 동일 메서드로부터 오버라이딩하여 재정의하는 하위 클래스의 메서드이다. 그 내부에서 super.printProfile(); 이라고 작성하였는데, 이 때의 printProfile()은 앞에 super가 붙었으므로, 상위 클래스의 메서드를 지칭하는 것이다. 

# 메서드, 클래스에서의 final

이전의 [변수와 상수](/java/java-변수와-상수/) 페이지에서 상수에 대해 설명할 때 final 키워드도 보았다. 즉, final 키워드를 변수 선언에 쓰인다면 해당 변수는 더 이상 변수가 아니라 상수로 정의된다는 것을 살펴보았다. 

그러나 final 키워드는 꼭 변수에만 사용되지 않는다. 각각 메서드와 클래스를 정의할 때에도 해당 키워드를 사용할 수 있다. 

메서드 정의 시 final 키워드를 사용하면, 해당 메서드가 하위 클래스에서 오버라이딩 될 수 없도록 막는 역할을 해준다. 반면 클래스 정의 시에 사용하면 해당 클래스를 다른 클래스가 상속할 수 없도록 막아주는 역할을 한다. 

다음은 메서드에 final 키워드를 사용했을 때를 살펴보기 위한 예제이다. 

```java
public class FinalMethod {
    public static void main(String[] args) {
        SubClass sub = new SubClass();
        sub.tryToPrintFinal();
        // sub.tryToPrintPrivate(); 에러 발생 부분.
    }
}

class SuperClass {
    final void printFinal() {
        System.out.print("Hi from printFinal");
    }

    private void printPrivate() {
        System.out.println("Hi from printPrivate");
    }
}

class SubClass extends SuperClass {
    // java.lang.IncompatibleClassChangeError
    // 다음의 코드는 에러 발생.
    //void printFinal() {
    //    System.out.println("Hi from printFinal in SubClass");
    //}

    void tryToPrintFinal() {
        super.printFinal();  // final로 정의된 메서드 호출은 가능.
    }

    // 다음의 코드는 에러 발생.
    //void tryToPrintPrivate() {
    //    super.printPrivate();  // 에러 발생 부분.
    //}
}
```

```java
Hi from printFinal
```

위 예제에서 상위 클래스인 SuperClass 내부에 printFinal 메서드를 final로 정의하였다. 이 클래스를 상속받는 하위 클래스 SubClass는 해당 클래스를 오버라이딩할 수 없으며, 만약 시도 시 에러를 발생시킨다. 그러나 오버라이딩만 안되는 것이지, 하위 클래스에 해당 메서드를 호출하여 사용하는 것은 가능하다. 

반면 상위 클래스의 어떤 메서드에 private 접근 제한자를 붙이면, 하위 클래스에서 해당 메서드에 대한 오버라이딩은 물론 호출조차도 안된다. 즉, 상속 자체가 막힌다. private가 접근 자체를 제한하기 때문이다. 

다음은 클래스 정의 시 final 키워드를 붙였을 때의 예제이다.

```java
public class FinalClass {
    public static void main(String[] args) {
        ImYourFather obj = new ImYourFather();
        obj.printNumber();
        obj.printSomething();
    }
}

final class NotYourParent {
    int number = 1000;

    void printSomething() {
        System.out.println("This is message from method \"printSomething\" in class \"NotYourParent0");
    }
}

class ImYourFather extends NotYourParent {
    void printSomething() {
        super.printSomething();
        System.out.println("This is from class ImYourFather");
    }

    void printNumber() {
        System.out.println(super.number);
    }
}
```

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problems: 
        The type ImYourFather cannot subclass the final class NotYourParent
        The method printSomething() is undefined for the type Object
        number cannot be resolved or is not a field

        at ImYourFather.<init>(FinalClass.java:17)
        at FinalClass.main(FinalClass.java:3)
```

위 예제에서, NotYourParent 클래스에 final 키워드가 붙었다. 그래서 다른 클래스에서는 이 클래스를 상속받을 수 없으며, 시도하는 경우 위와 같이 에러가 발생한다. 

# 추상 클래스와 추상 메서드

보통 메서드에는 그 블록 내에 코드가 존재한다. 이러한 메서드들은 구체적인 코드가 들어있기에 구상 메서드(concrete method)라고도 한다. 반면, 코드를 하나도 작성하지 않고 오로지 반환값, 메서드명과 매개변수만을 지정한 메서드도 있다. 이 앞에 abstract 키워드를 붙이면 해당 메서드는 추상 메서드(abstract method)가 된다. 그리고 이러한 추상 메서드를 적어도 하나라도 포함하고 있다면, 이를 포함한다는 의미에서 클래스명 앞에 abstract 키워드를 붙이는데, 이 때의 클래스를 추상 클래스(abstract class)라 한다. 

다음은 체스를 코드로 묘사한 예제로, 먼저 체스말을 추상화하여 이를 추상 클래스로 묘사한 ChessPiece 클래스를 정의한다. 그 후, Pawn, rook이라는 체스말을 추상화한 클래스들을 각각 정의한 후, 이들을 ChessPiece 클래스를 상속받도록 하였다. 

```java
abstract class ChessPiece {
    int x;
    int y;
    String pieceName;

    ChessPiece(int initX, int initY, String pieceName) {
        this.x = initX;
        this.y = initY;
        this.pieceName = pieceName;
    }

    abstract void move(int toX, int toY);
}

class Pawn extends ChessPiece {
    Pawn(int initX, int initY, String pieceName) {
        super(initX, initY, pieceName);
    }

    void move(int toX, int toY) {
        if (Math.abs(this.x - toX) > 1 || Math.abs(this.y - toY) > 1) {
            return;
        }
        this.x += toX;
        this.y += toY;
        System.out.println("폰 한 칸 이동.");
    }
}

class Rook extends ChessPiece {
    Rook(int initX, int initY, String pieceName) {
        super(initX, initY, pieceName);
    }
}

public class Abstract {
    public static void main(String[] args) {
        Pawn pawn1 = new Pawn(2, 1, "Pawn1");
        Rook rook1 = new Rook(1, 1, "Rook1");

        pawn1.move(2, 2);
        rook1.move(1, 5);
    }
}

```

```java
폰 한 칸 이동.
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
        The type Rook must implement the inherited abstract method ChessPiece.move(int, int)

        at Rook.move(Abstract.java:30)
        at Abstract.main(Abstract.java:42)
```

위 예제에서, 추상 클래스 ChessPiece 내부에는 추상 메서드 move가 정의되어 있다. 추상 메서드를 정의할 때에는 뒤에 중괄호({})를 붙이지 않도록 주의해야 한다. 이 추상 클래스를 상속하는 하위 클래스라면 추상 메서드 move를 반드시 정의해야 한다. 즉, 추상 메서드는 이를 상속받는 자식 클래스로 하여금 해당 메서드를 정의하게끔 강제하는 기능을 가진다. 위 예제에서는 추상 클래스를 상속받는 rook 클래스의 경우 move 메서드를 정의하지 않았기에 위와 같이 에러 메시지가 발생한 것이다. 

추상 클래스는 그 자체로 객체를 형성하여 사용할 수 없다. 위 예제의 main 메서드 마지막에 다음을 추가하여 실행시켜보자. 

```java
public class Abstract {
    public static void main(String[] args) {
        Pawn pawn1 = new Pawn(2, 1, "Pawn1");
        Rook rook1 = new Rook(1, 1, "Rook1");

        pawn1.move(2, 2);
        rook1.move(1, 5);

        ChessPiece cp = new ChessPiece(3, 4, "hi");  // 추가된 코드.
    }
}

```

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
        Cannot instantiate the type ChessPiece

        at Abstract.main(Abstract.java:50)
```

추상 클래스는 인스턴스화할 수 없기에 위와 같은 에러 메시지가 발생한 것이다. 

추상 클래스는 이를 상속받는 클래스가 어떠한 기능을 가져야하는지를 지시하는 일종의 “뼈대” 역할을 하기 위해 사용될 수 있다. 이 메서드는 반드시 가져야하나, 아직 구체적으로 어떤 코드를 작성해야할지 결정되지 않았을 때 추상 클래스를 이용한다. 이러한 추상 클래스를 이용하면, 추후 이를 상속받는 클래스를 정의할 때 필수로 구현해야 하는 메서드들을 정의하지 않으면 에러를 발생시키기에 개발자가 잊지 않고 해당 메서드들을 구현할 수 있도록 해주는 “가이드”역할을 해준다. 

# 인터페이스 (Interface)

인터페이스는 추상 클래스와 비슷한 개념으로, 어떤 클래스 내부에 특정 메서드를 반드시 구현하게끔 하고자 할 때 사용하는 개념이다. 즉, 인터페이스 내부에 추상 메서드를 정의하고 이를 구현받는 클래스에서 해당 메서드를 구현하는 것이다. 

여기까지만 보면 추상 클래스와 동일해보이나, 여러 가지 차이점이 존재한다. 

- 추상 클래스에서는 하위 클래스에 “상속”하는 개념으로 사용되나, 인터페이스에는 상속이란 개념은 없고, 대신 클래스가 어떤 인터페이스를 받아 추상 메서드를 구상 메서드로 구현하도록 하는 것을 “구현”이라고 한다. 즉, A라는 인터페이스가 있고, 이를 클래스 B가 받아드리면 “B는 A를 구현한다”라고 표현할 수 있다.
- 다중 상속은 불가능하나 다중 구현은 가능하다. 즉, 하나의 클래스가 여러 개의 인터페이스를 받을 수 있다.
- 인터페이스가 다른 인터페이스를 상속 받을 수 있으나, 다른 일반 클래스를 상속 받을 수는 없다. 즉, 일반 클래스가 인터페이스를 받을 수는 있으나, 반대로 인터페이스가 일반 클래스를 상속받을 수는 없는 일방적인 관계에 있다.
- 내부 구조가 다르다. 추상 클래스의 내부에는 필드(멤버 변수), 구상 메서드, 추상 메서드 이렇게 포함할 수 있고, 이 중 구상 메서드와 필드는 생략할 수 있다. 반면 인터페이스 내부에는 static 상수, 추상 메서드, 디폴트 메서드로 구성되어 있으며 이 중 디폴트 메서드와 static 상수는 생략할 수 있다(둘 다 추상 메서드도 생략할 수 있다고는 하나, 이러면 애초에 추상 클래스 또는 인터페이스를 사용하는 의미가 없어진다). 즉, 인터페이스 내부에서는 변수가 아닌 static 상수만 선언할 수 있다. 또한, 인터페이스 내부에 작성된 추상 메서드, 디폴트 메서드는 모두 public으로 정의된다. 그러므로 이 인터페이스를 받는 클래스는 해당 추상 메서드를 오버라이딩할 때 반드시 public으로 정의해야 한다.
- 사용 목적도 다르다. 추상 클래스는 이를 상속받는 클래스가 어떠한 개념을 여전히 표방하도록 그 대략적인 구조를 제공하는 역할을 한다. 예를 들어, 마피아 게임을 구현한다고 했을 때, 시민, 마피아, 경찰, 의사 모두 사람이므로 추상 클래스 Person을 정의하고, 시민, 마피아, 경찰, 의사 등의 모든 직업의 사람들이 공통으로 가지고 있는 특징들을 멤버 변수 또는 메서드로 구현하게 할 수 있다. 즉, 이 경우 모든 직업을 가진 사람들이 공통적으로 “사람”임을 알 수 있도록 이를 강제로 구현하게 하는 것이 추상 클래스이다. 
반면 인터페이스는 그저 어떤 기능을 구현하게끔 강제하는 역할에 그친다. 이런 점에서 보면 인터페이스는 추상 클래스보다는 작은 범위를 가진다고도 볼 수 있다. 그럼에도 인터페이스도 유용한 점이 있다. 예를 들어 마피아 게임에서 밤이 되면 자신의 특수 스킬을 사용할 수 있는 직업들이 있는 반면, 시민은 밤에 아무것도 할 수 없다. 만약 밤에 자신의 특수 스킬을 사용하도록 하는 기능을 추상 클래스 내부에 추상 메서드로 정의하면 이를 상속받는 시민 클래스에서도 해당 기능을 사용할 수 있다. 이는 원래 의도와 배치된다. 이럴 때 이 기능을 인터페이스로 구현하여 시민 이외에 특수 스킬을 가진 직업 클래스에만 적용시키면 문제를 해결할 수 있다. 
즉, 추상 클래스를 상속받는 모든 하위 클래스들 중 일부 하위 클래스에만 특정 기능을 추가하고자 할 때 인터페이스를 사용할 수 있다.

인터페이스를 정의하려면 interface 키워드를 먼저 작성한 후 그 뒤에 인터페이스 이름을 짓는다. 그 후, 중괄호를 작성하고 그 안에 스태틱 상수나 추상 메서드 등을 정의하면 된다. 

```java
interface someInter {
    int interNum = 1000;
    void printInfo();
}
```

형태 자체는 클래스를 정의할 때와 거의 똑같으므로 어려울 것은 없을 것이다. 다만 인터페이스 내부에서 정의되는 모든 변수들은 사실은 public static final로 상수로 지정되고, 추상 메서드의 경우 public abstract로 정의된다. 위 예제에서는 이러한 키워드들을 생략하였지만, 생략하더라도 컴파일러가 자동으로 추가해준다. 위 예제는 원래 다음의 예제와 같은 것이다.

```java
interface someInter {
    public static final int interNum = 1000;
    public abstract void printInfo();
}
```

다음은 인터페이스를 정의하고 이를 클래스에서 구현하도록 하는 예제이다. 

```java
interface someInter {
    int interNum = 1000;
    void printInfo();
}

class Person implements someInter {
    String name;
    String job;
    int uniqueId;

    Person(String name, String job, int id) {
        this.name = name;
        this.job = job;
        this.uniqueId = id;
    }

    public void printInfo() {
        System.out.println("이름 : " + name);
        System.out.println("직업 : " + job);
        System.out.println("id : " + uniqueId);
    }
}

public class InterfaceEx {
    public static void main(String[] args) {
        Person me = new Person("가나다", "시민", 10000);
        me.printInfo();
    }
}

```

```java
이름 : 가나다
직업 : 시민
id : 10000 
```

인터페이스 내에서 정의된 상수는 이 인터페이스를 받는 클래스 내부에서 반드시 정의할 필요는 없다. 

위 예제에서 볼 수 있듯, 클래스가 인터페이스를 받도록 하려면 클래스명 뒤에 implement 키워드를 사용한 다음, 그 뒤에 인터페이스명을 입력하면 된다. 

한 편 인터페이스는 다중 구현도 가능하다. 

```java
class A implements B, C, D {
    // 코드.
}
```

그리고 인터페이스가 인터페이스를 상속받을 때에 한해서 다중 상속도 가능하다. 

```java
interface A extends B, C, D {
    // 코드. 
    // B, C, D 모두 인터페이스
}
```

만약 어떤 클래스에서 상속과 구현을 동시에 받는다면 상속 문법을 먼저 쓴 다음 그 뒤에 인터페이스 구현 문법을 쓴다. 

```java
class SubClass extends SuperClass implements AInter, BInter {
    // 코드.
}
```

## 디폴트 메서드 (Default method)

자바 8 버전 이상부터는 인터페이스 내부에 디폴트 메서드를 정의할 수 있게 되었다. 인터페이스 내부에 디폴트 메서드를 정의하려면 해당 메서드명 앞에 있는 반환형 앞에 default 키워드를 사용하면 된다. 

```java
interface Inter {
    default void someDefault(){};
}
```

디폴트 메서드는 추상 메서드와 달리, 매개변수가 들어가는 괄호 뒤에 중괄호를 붙여야 한다. 

디폴트 메서드로 마찬가지로, 인터페이스 내부에서 정의되면 자동으로 public 으로 정해진다. 즉 위 코드는 다음과 일치한다. 

```java
interface Inter {
    public default void someDefault(){};
}
```

따라서 디폴트 메서드가 있는 인터페이스를 받는 클래스에서는 해당 메서드를 오버라이딩할 때 반드시 앞에 public을 명시해야한다. 

다음은 디폴트 메서드가 정의된 인터페이스와 이를 받아 구현하는 클래스 예제이다. 

```java
interface PersonFunc {
    void printProfile();
    default void useSkill() {};
}

abstract class PersonInGame {
    String name;
    String job;
    
    PersonInGame(String name, String job) {
        this.name = name;
        this.job = job;
    }
}

class Citizen extends PersonInGame implements PersonFunc {
    final String skill = "없음";
    
    Citizen(String name, String job) {
        super(name, job);
    }

    public void printProfile() {
        System.out.println("====== 사용자 정보 ======");
        System.out.println("이름 : " + name);
        System.out.println("직업 : " + job);
        System.out.println("보유 스킬 : " + skill);
        System.out.println("========================");
    }
}

class Mafia extends PersonInGame implements PersonFunc {
    final String skill = "죽일 시민 지정";

    Mafia(String name, String job) {
        super(name, job);
    }

    public void printProfile() {
        System.out.println("====== 사용자 정보 ======");
        System.out.println("이름 : " + name);
        System.out.println("직업 : " + job);
        System.out.println("보유 스킬 : " + skill);
        System.out.println("========================");
    }

    public void useSkill() {
        System.out.printf("%s 가 %s 스킬을 사용하였습니다.", name, skill);
    }
}

public class defaultMethod {
    public static void main(String[] args) {
        Citizen citi1 = new Citizen("시민1", "시민");
        Mafia mafia1 = new Mafia("마피아1", "마피아");
        
        citi1.printProfile();
        mafia1.printProfile();

        mafia1.useSkill();
    }
}

```

```java
====== 사용자 정보 ======
이름 : 시민1
직업 : 시민
보유 스킬 : 없음
========================
====== 사용자 정보 ======
이름 : 마피아1
직업 : 마피아
보유 스킬 : 죽일 시민 지정
========================
마피아1 가 죽일 시민 지정 스킬을 사용하였습니다.
```

위 예제 코드를 유심히 보면 알 수 있는 점은, 인터페이스 내에 정의된 디폴트 메서드는 이 인터페이스를 상속받는 클래스에서 반드시 구현해야 하는 것은 아니라는 것이다. 이는 추상 메서드와의 큰 차이점이다. 

이렇게, 인터페이스 내 정의된 디폴트 메서드의 특성을 이용하면 추후 인터페이스 업데이트 시에 유용하게 사용할 수 있다. 이미 기존에 인터페이스가 정의되어 있고, 해당 인터페이스를 구현하는 수많은 클래스가 있다고 가정해보자. 그 클래스 들 중 일부 클래스에 특정 기능을 추가해야한다는 것을 명시하기 위해 인터페이스에 그 기능을 만약 추상 메서드로 정의한다면, 해당 기능을 추가하지 않아도 되는 다른 모든 클래스들까지 해당 메서드를 정의해야만 에러가 발생하지 않는다. 이는 프로젝트 규모가 커질 수록 사실상 불가능한 일이라 볼 수 있다. 이럴 때 추상 메서드 대신 디폴트 메서드를 사용하면, 이 인터페이스를 구현하는 다른 모든 클래스들이 해당 메서드를 구현해야하는 의무를 지지 않으며, 그와 동시에 해당 기능이 필요한 일부 클래스에만 이 디폴트 메서드를 오버라이딩하여 사용하면 된다. 

# 다형성 (polymorphism)

이전에 오버로딩과 오버라이딩에 대해 살펴보았을 때, 둘 다 공통점은 같은 메서드를 다양한 형태로 다룰 수 있었다는 점이었다. 또한, 상속받은 클래스로 생성한 객체는 부모 클래스의 객체로 다룰 수도 있고, 자식 클래스의 객체로 다룰 수 있다. 즉, 객체 지향 프로그래밍에서는 메서드 또는 객체를 다양한 형태로 다룰 수 있는데, 이러한 성질을 다형성(polymorphism)이라 한다. 메서드의 다형성은 이미 오버로딩, 오버라이딩에서 살펴보았으므로, 여기서는 객체의 다형성에 대해 살펴보겠다. 

변수 선언 시 미리 그 자료형을 선언해야 하는 정적 타이핑 방식의 자바는 이러한 다형성을 이용하여 부모 클래스 자료형으로 선언한 참조 변수에 자식 클래스로 생성한 객체를 할당할 수 있다. 예를 들어 상위 클래스 Super와 하위 클래스 Sub가 정의되어 있을 때, 상위 클래스 자료형 참조 변수에 하위 클래스 객체를 할당할 수 있다. 

```java
class Super {}
class Sub extends Super {}
public class MainClass {
    public static void main(String[] args) {
        Super someVar = new Sub();
    }
}
```

상위 클래스를 구성하는 멤버들보다는 하위 클래스를 구성하는 멤버들이 보통 더 많을 것이다. 따라서 위 예제와 같이 하위 클래스로 생성된 객체를 상위 클래스 자료형 참조 변수에 대입하더라도 문제없이 사용할 수 있다. 상위 클래스에 정의된 멤버들이 하위 클래스에도 그대로 있을 것이기 때문이다. 다만, 하위 클래스에만 정의되어 있는 멤버들은 상위 클래스 참조 변수로 호출하려고 하면 에러가 발생한다. 해당 멤버들은 상위 클래스에는 없기 때문이다. 이 점만 조심하면 상위 클래스 자료형 참조 변수로도 얼마든지 하위 클래스 객체를 사용할 수 있는 것이다. 

반면, 이 반대는 성립하지 않는다. 즉, 하위 클래스 자료형 참조 변수에 상위 클래스의 객체를 할당할 수 없다. 하위 클래스 형 참조 변수를 선언할 때 이미 해당 변수는 하위 클래스의 설계도를 기반으로 메모리에 할당될 것이다. 이 때, 하위 클래스에만 존재하는 멤버들도 포함되어 있다. 이 상태에서 상위 클래스의 객체를 생성하여 해당 참조 변수에 할당하면, 이 참조 변수에서 하위 클래스에만 존재하는 멤버들에 접근하면 에러가 발생하므로, 애초에 컴파일러는 이러한 대입 자체를 막는 것이다. 

다음은 위에서 언급한 점들을 토대로 한 예제 코드이다. 

```java
class Device {
    String kind;

    Device(String kind) {
        this.kind = kind;
    }

    void showScreen() {
        System.out.println("화면이 켜졌습니다.");
    };
}

class Computer extends Device {
    int deviceId;

    Computer(String kind, int deviceId) {
        super(kind);
        this.deviceId = deviceId;
    }

    void showScreen() {
        System.out.println("컴퓨터 화면이 켜졌습니다. " + deviceId);
    }

    void printDeviceKind() {
        System.out.println(kind);
    }
}

public class Polymorphism {
    public static void main(String[] args) {
        Device dev = new Computer("컴퓨터", 1002);
        dev.showScreen();
        // dev.printDeviceKind();  // 에러. 상위 클래스에는 해당 메서드가 없기 때문.

        // 에러. 더 큰 규모의 상위 클래스 객체를 더 작은 규모의 하위 클래스 참조 변수에 대입 불가.
        // Computer dev2 = new Device("컴퓨터");  
    }
}

```

```java
컴퓨터 화면이 켜졌습니다. 1002
```

위 예제에서는 Device가 상위 클래스, Computer가 하위 클래스다. 하위 클래스에는 상위 클래스에는 없던 필드 deviceId와 메서드 printDeviceKind가 추가되어 있다. 메인 메서드 내부를 보면, 상위 클래스인 Device를 자료형으로 하는 참조 변수 dev를 선언한 뒤, 여기에 하위 클래스 Computer로 생성한 객체를 할당하고 있다. 하위 클래스에는 상위 클래스와 공통적으로 가지고 있는 showScreen 메서드가 있으므로 상위 클래스 자료형 참조 변수에서 해당 메서드에 접근할 수 있다. 반면, printDeviceKind 메서드는 하위 클래스에만 있는 메서드로, 해당 변수에서는 접근하면 에러가 발생한다. 어찌되었건, 상위 클래스 자료형 참조 변수에 하위 클래스 객체를 할당하는 것 자체는 가능함을 확인할 수 있다. 반면, 아래 주석 처리된 dev2 변수를 주석 해제하고 실행하면 에러가 발생한다. 애초에 할당 자체가 안되는 것이다. 

하위 클래스 자료형 참조 변수에 상위 클래스 객체를 대입하지 못하는 이유를 다음의 비유를 통해서도 이해할 수 있다. 같은 회사에서 나온 일반 에어컨과 고급 에어컨을 가지고 있다고 가정해보자. 일반 에어컨의 리모컨에는 “전원”, “온도조절” 버튼만 있고, 고급 에어컨에는 여기에 더해 “풍향조절” 기능도 포함되어 있다고 가정해보자. 일반 에이컨의 리모컨이 두 에어컨에 대해 호환하여 사용 가능하다고 할 때, 일반 에이컨을 키는 데에 사용할 수 있고, 고급 에어컨을 키는 데에 사용할 수 있다. 다만 일반 에어컨 리모콘에는 “풍향조절” 버튼이 포함되어 있지 않기에, 고급 에어컨에 해당 기능만 사용할 수 없을 뿐이다. 그런데 반대로 고급 에어컨 리모컨을 일반 에어컨에 사용한다고 해보자. 고급 에어컨 리모컨에는 “풍향조절” 버튼이 포함되어 있다. 이 버튼을 누르면 일반 에어컨에는 해당 기능이 없기에 자칫 어떠한 에러가 발생할 수도 있다. 그렇기에 고급 에어컨 리모컨을 일반 에어컨에 사용할 수 없는 것이다. 

다형성을 이용하면, 여러 클래스 자료형들을 하나로 묶어 사용할 수 있다. 이러면 중복되는 코드를 줄일 수 있으며, 매개변수, 반환값이 허용하는 자료형이 한정되어 있을 경우에도 유용하다. 다음의 예제를 보자.

```java
class Korean {
    String bookKind = "국어";
}

class Math {
    String bookKind = "수학";
}

class Study {
    void printWhatYouStudy(Korean k) {
        System.out.println(k.bookKind + " 과목을 공부하고 있습니다.");
    }

    void printWhatYouStudy(Math m) {
        System.out.println(m.bookKind + " 과목을 공부하고 있습니다.");
    }
}

public class PolyEx1 {
    public static void main(String[] args) {
        Korean koreanBook = new Korean();
        Math mathBook = new Math();
        Study study = new Study();

        study.printWhatYouStudy(koreanBook);
        study.printWhatYouStudy(mathBook);
    }
}

```

```java
국어 과목을 공부하고 있습니다.
수학 과목을 공부하고 있습니다.
```

위 예제의 Study 클래스를 보자. 만약 수업 과목이 영어, 사회, 과학 등 더 늘어난다면 그 만큼의 메서드들을 오버라이딩하여 작성해야할 것이고, 이러면 Study 클래스 내 코드가 더 길어진다. 게다가 오버라이딩이라지만 형태가 비슷해서 어딘가 코드가 중복된다는 느낌을 받을 것이다. 이러한 문제를 다형성이 해결해줄 수 있다. 모든 과목들을 아우르는 상위 클래스를 정의한 후, 각 과목 클래스들이 해당 클래스를 상속받도록 설계하면 된다. 

```java
class TextBook {
    String bookKind;
}

class Korean extends TextBook {
    Korean() {bookKind = "국어";}
}

class Math extends TextBook {
    Math() {bookKind = "수학";}
}

class English extends TextBook {
    English() {bookKind = "영어";}
}

class Study {
    void printWhatYouStudy(TextBook tb) {
        System.out.println(tb.bookKind + " 과목을 공부하고 있습니다.");
    }
}

public class PolyEx1 {
    public static void main(String[] args) {
        Korean koreanBook = new Korean();
        Math mathBook = new Math();
        English engBook = new English();
        Study study = new Study();

        study.printWhatYouStudy(koreanBook);
        study.printWhatYouStudy(mathBook);
        study.printWhatYouStudy(engBook);
    }
}

```

```java
국어 과목을 공부하고 있습니다.
수학 과목을 공부하고 있습니다.
영어 과목을 공부하고 있습니다.
```

아무리 과목이 늘어나도, 다형성을 이용하기에 Study 클래스 내부의 코드를 중복없이 간결하게 사용할 수 있다. 

메서드의 반환값의 자료형은 딱 하나만 지정할 수 있다. 그런데 경우에 따라 여러 객체 자료형 중 하나를 반환하게끔 하고자 할 때에도 다형성을 사용할 수 있다. 

```java
abstract class Cloth {
    String kind;

    abstract void printKind();
}

class Cap extends Cloth {
    Cap() {kind = "모자";}

    void printKind() {
        System.out.println(kind);
    }
}

class TShirt extends Cloth {
    TShirt() {kind = "티셔츠";}
    
    void printKind() {
        System.out.println(kind);
    }
}

class Jean extends Cloth {
    Jean() {kind = "바지";}

    void printKind() {
        System.out.println(kind);
    }
}

class YourCloth {
    Cloth getYourCloth(String name) {
        Cloth clothObj = null;
        switch(name) {
            case "모자":
                clothObj = new Cap();
                break;
            case "바지":
                clothObj = new Jean();
                break;
            case "티셔츠":
                clothObj = new TShirt();
                break;
        }
        return clothObj;
    }
}

public class PolyEx2 {
    public static void main(String[] args) {
        YourCloth uc = new YourCloth();

        Cloth myCloth = uc.getYourCloth("모자");
        myCloth.printKind();

        Cloth myCloth2 = uc.getYourCloth("티셔츠");
        myCloth2.printKind();
    }
}

```

```java
모자
티셔츠
```

YourCloth 클래스의 getYourCloth 메서드의 반환값의 자료형을 모든 옷 클래스의 상위 클래스인 Cloth로 지정함으로써, Cap, Jean, TShirt 자료형 객체 모두 해당 메서드에서 반환이 가능해진다. 

# instanceof 연산자

inst instanceof class 형식의 instanceof 연산자를 사용하면 주어진 객체가 주어진 클래스 자료형의 객체인지를 검사하고 이를 boolean 자료형으로 반환하는 연산자이다. 해당 연산자 뒤에는 클래스 뿐만 아니라 인터페이스명도 대입 가능하며, 이 경우 해당 객체가 해당 인터페이스를 구현했는지를 검사한다. 

다음은 해당 연산자를 사용한 예제이다.

```java
class Device2 {}

class Computer2 extends Device2 {
    String kind = "컴퓨터";

    void printKind() {
        System.out.println(kind);
    }
}

class Phone2 extends Device2 {
    String kind = "폰";

    void printKind() {
        System.out.println(kind);
    }
}

class DeviceDetector {
    Device2 execMethod(Device2 dev) {
        if (dev instanceof Computer2) {
            Computer2 co = (Computer2)dev;
            co.printKind();
            return co;
        } else {
            Phone2 ph = (Phone2)dev;
            ph.printKind();
            return ph;
        }
    }
}

public class InstOfEx {
    public static void main(String[] args) {
        Device2 dev = new Device2();
        Computer2 com = new Computer2();
        Phone2 ph = new Phone2();
        DeviceDetector dd = new DeviceDetector();

        System.out.println(com instanceof Computer2);
        System.out.println(ph instanceof Phone2);
        System.out.println(com instanceof Device2);
        System.out.println(ph instanceof Device2);
        System.out.println(dev instanceof Computer2);
        dd.execMethod(com);
    }
}

```

```java
true
true
true
true
false
컴퓨터
```

위 예제의 DeviceDetector 클래스 내부를 보면 알 수 있듯, instanceof 연산자는 다형성 사용 시 활용될 수 있음을 알 수 있다. 

---

References

[1] [💠 자바의 다형성(Polymorphism) 완벽 이해하기](https://inpa.tistory.com/entry/OOP-JAVA의-다형성Polymorphism-완벽-이해)

[2] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)