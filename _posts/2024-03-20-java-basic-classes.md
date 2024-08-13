---
title: "[Java] 기본 클래스"
category: "Java"
tag: ["java", "자바", "클래스", "class"]
---

# Object 클래스 : 모든 클래스들의 왕

자바에는 Object라는 최상위 클래스가 존재한다. 개발자가 직접 정의하는 클래스, 자바에서 기본으로 제공하는 클래스 등 모든 클래스들은 이 클래스로부터 상속받는다. 클래스를 정의할 때에도 Object 클래스를 직접 상속하는 코드를 작성하지 않아도 컴파일 과정에서 자동으로 extends Object 코드를 삽입해준다고 한다. 

또한, Object 클래스에 정의되어 있는 메서드들이 있는데, 그 중에는 자식 클래스에서 오버라이딩하여 재정의하여 사용할 수 있게끔 해놓은 메서드들도 존재한다. 다음은 Object 클래스 내 정의되어 있는 메서드 일부이다. 

| 메서드 | 설명 |
| --- | --- |
| toString() | 객체의 문자열을 반환. |
| equals(Object obj) | 두 객체의 동일 여부를 반환. |
| hashCode() | 객체의 해시 코드를 반환한다. |
| clone() | 객체의 복제본을 반환. |

참고로, 앞서 언급했듯, 개발자가 클래스를 정의해도 해당 클래스는 자동으로 Object 클래스를 상속받는다고 하였다. 그렇기에 개발자가 정의한 클래스 내에서도 Object 클래스 내에 오버라이딩 가능한 메서드를 오버라이딩하여 원하는 대로 사용할 수 있다. 

# toString()

Object 클래스의 toString() 메서드 선언부는 다음과 같이 생겼다.

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

위 메서드 내부 코드에 의하면, 해당 메서드를 호출하면 해당 메서드가 속한 클래스명과 그 객체의 해시코드를 합쳐 문자열로 반환한다. 

클래스를 인스턴스화한 후, 그 객체 자체를 `System.out.println()` 등으로 출력하면 해당 메서드는 그 객체의 toString() 메서드를 호출한 후, 그 반환값을 출력하는 구조이다. 따라서 만약 사용자 정의 클래스를 정의하고, 그 객체를 직접 출력할 때 그 객체 내 어떤 정보가 출력되길 원한다면 toString() 메서드를 오버라이딩하면 되는 것이다. 

다음은 사용자 정의 클래스를 정의한 후, 메인 메서드에서 그 객체를 생성하고, 그 객체의 참조 변수를 직접 출력해보는 예제이다. 해당 예제에는 String형 변수도 출력하고 있다.

```java
class User3 {
    String userId;

    User3(String userId) {
        this.userId = userId;
    }
}

class AboutObject {
    public static void main(String[] args) {
        User3 me = new User3("goodworld");
        String someStr = "hello";

        System.out.println(me);
        System.out.println(me.toString());
        System.out.println(someStr);
        System.out.println(someStr.toString());
    }
}

```

```java
User3@372f7a8d
User3@372f7a8d
hello
hello
```

직접 정의한 User3 클래스로부터 객체를 생성한 후, 해당 참조 변수를 직접 출력해보고, 명시적으로 해당 객체의 toString() 메서드를 호출하여 그 반환값을 출력하고 있다. 그 결과 둘 모두 똑같이 클래스명과 해시코드가 출력됨을 알 수 있다. 참고로, User3 클래스에서는 분명 toString() 메서드를 명시적으로 정의한 적이 없음에도 에러 없이 잘 작동하고 있다. 이는 사용자 정의 클래스도 자동으로 Object 클래스로부터 상속받도록 동작된다는 뜻이다. 

위 예제의 다른 곳을 보면, String 객체도 출력하고 있음에도, 사용자 정의 객체와 달리 클래스명과 해시코드가 아닌 문자열이 출력된 것을 볼 수 있다. 이는 String 클래스 내부에서 toString 메서드를 재정의했다는 것을 암시한다. 

이번에는 User3 객체를 직접 출력하면 그 유저의 아이디가 출력되도록 toString 메서드를 오버라이딩해보겠다. 

```java
class User3 {
    String userId;

    User3(String userId) {
        this.userId = userId;
    }

    // 메서드 오버라이딩.
    // 객체 자체의 원래 출력 결과를 알고 싶으면 다음 메서드를 주석 처리.
    public String toString() {
        return userId;
    }
}

class AboutObject {
    public static void main(String[] args) {
        User3 me = new User3("goodworld");
        String someStr = "hello";

        System.out.println(me);
        System.out.println(me.toString());
        System.out.println(someStr);
        System.out.println(someStr.toString());
    }
}

```

```java
goodworld
goodworld
hello
hello
```

위 예제에서는 사용자 정의 객체 User3 내부에 toString 메서드를 오버라이딩하여 해당 객체의 멤버 변수 userId를 반환하도록 하고 있다. 그 결과, 해당 클래스로부터 생성된 객체를 직접 출력하면 userID 변수에 담긴 문자열 값을 출력한다. 

## equals()

Object 클래스 내 equals() 메서드는 다음과 같이 정의되어 있다.

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

즉, 객체 자신과 매개변수로 전달된 다른 객체와의 id값을 비교하고 있다. 따라서, 사용자 정의 클래스에서 equals() 메서드를 바로 사용하면 두 객체들의 id값을 비교하기에, 객체 내 다른 멤버 변수의 내용이 같은지를 비교하고 싶을 때에는 역시 해당 메서드를 오버라이딩하는 것이 좋다. 

다음은 equals 메서드를 오버라이딩하지 않았을 때의 사용자 정의 객체 출력 결과와, String형 객체의 출력 결과를 보는 예제 코드이다. 

```java
class User4 {
    String userId;

    User4(String userId) {
        this.userId = userId;
    }
}

class EqualsEx {
    public static void main(String[] args) {
        User4 me = new User4("bowWow123");
        User4 you = new User4("bowWow123");
        String meStr = new String("bowWow123");
        String youStr = new String("bowWow123");

        System.out.println(me.equals(you));
        System.out.println(meStr.equals(youStr));
    }
}

```

```java
false
true
```

사용자 정의 클래스 User4에는 equals 메서드를 오버라이딩하지 않았기에 자동으로 Object 클래스의 equals 메서드를 사용하게 된다. 객체들의 id값을 비교하였기에 위와 같이 false 결과가 나온 것이다. 반면, String 클래스에서는 두 문자열을 비교하도록 equals() 메서드를 오버라이딩하였기에 위와 같이 두 문자열이 같으면 true를 반환할 수 있는 것이다. 

User4의 멤버 변수 userId 변수값의 비교를 원한다면 다음과 같이 해당 클래스 내부에 equals() 메서드를 오버라이딩하면 된다. 

```java
class User4 {
    String userId;

    User4(String userId) {
        this.userId = userId;
    }

    // equals 메서드 오버라이딩
    public boolean equals(Object obj) {
        if(this.userId.equals(((User4)obj).userId)) {
            return true;
        } else {
            return false;
        }
    }
}

class EqualsEx {
    public static void main(String[] args) {
        User4 me = new User4("bowWow123");
        User4 you = new User4("bowWow123");
        String meStr = new String("bowWow123");
        String youStr = new String("bowWow123");

        System.out.println(me.equals(you));
        System.out.println(meStr.equals(youStr));
    }
}

```

```java
true
true
```

# java.lang 패키지

java.lang 패키지에는 여러 가지 기본 클래스들을 제공한다. 해당 클래스들을 사용할 때에는 굳이 해당 패키지를 import 하지 않아도 클래스명만 적어서 사용할 수 있다. 다음은 해당 패키지에 포함된 몇몇 클래스들이다. 

| 클래스 | 설명 |
| --- | --- |
| Object | 최상위 클래스. 기본 메서드를 제공해준다. |
| String, StringBuffer, StringBuilder | 문자열 처리 클래스. |
| Number, Integer, Long, Float, Double | 기본 자료형 값을 객체화해준다. 참고로 이러한 클래스를 래퍼 클래스(wrapper class)라 한다. |
| System | 콘솔 입출력 시 사용하는 클래스. |
| Math | 수학 기능 제공. (유틸리티 클래스) |

다음에 소개할 클래스들은 모두 java.lang 패키지에 포함된 클래스들로, 따로 패키지를 import 하지 않아도 바로 사용할 수 있다. 

# Wrapper Class : 기본 자료형을 객체화하여 포장한 클래스

숫자형, 문자형, 논리형 등 기본 자료형을 객체화한 wrapper class들이 자바 내에 존재한다. 기본 자료형을 객체로 사용하고 싶을 때, 해당 클래스에서 제공하는 여러 기능들을 사용하고자 할 때 유용하다. 

wrapper class들의 이름들은 원래 기본 자료형 이름의 맨 앞 글자를 대문자로 바꾼 것과 동일하다. 예를 들어 boolean은 Boolean 클래스로 사용할 수 있다. 단, int형의 wrapper class명은 Integer이고, char형은 Character이다. 

## java.lang.Number 클래스

### ~value() : 해당 래퍼 클래스 자료형으로 변환하기

Number 클래스는 래퍼 클래스들 중, 숫자형 래퍼 클래스들이 상속하는 추상 클래스이다. 해당 클래스에는 어떤 추상 메서드가 있고, 이 클래스를 상속받는 클래스들에는 각각 shortValue(), intValue(), doubleValue() 등의 메서드가 존재한다. 이 메서드들은 해당 wrapper 객체가 가지고 있는 기본 자료형의 값을 반환한다. 예를 들어, 정수형 wrapper 객체인 Integer()에는 이미 가지고 있는 기본 자료형의 값을 정수형으로 반환하는 intValue() 메서드와 이를 실수형으로 반환하는 doubleValue() 메서드가 있다.    
반대로, 각 wrapper 클래스마다 존재하는 static 메서드인 valueof() 메서드에 인자로 기본 자료형 값을 입력하면 이를 해당 wrapper 객체형으로 변환해주는 역할을 한다. `Integer.valueOf(10)`과 같이 정수 10을 입력하면 이를 포함하는 Integer 객체를 반환하는 식이다. 이를 이용하면 숫자형 레퍼 클래스형 변수를 선언과 동시에 할당도 가능해진다. 


```java
class NumberClass {
    public static void main(String[] args) {
        Integer num = Integer.valueOf(10);
        System.out.println(num.intValue());
        System.out.println(num.doubleValue());
        
        Double douNum = Double.valueOf(3.141592);
        System.out.println(douNum.intValue());
        System.out.println(douNum.doubleValue());
    }
}

```

```java
10
10.0
3
3.141592
```

### parse~() : 문자열을 숫자형 객체로 변환.

각 숫자형 클래스에는 parseShort(), parseInt() 와 같이 숫자가 적힌 문자열을 인자로 전달하면 이를 각 숫자형 객체로 변환하는 메서드가 있다. 

다음은 구분자가 섞인 숫자들이 작성되어 있는 문자열을 숫자들로 분해한 다음, 해당 숫자들을 int형으로 바꿔 모두 더하는 예제이다. 

```java
import java.util.StringTokenizer;

class ParseInto {
    public static void main(String[] args) {
        String source = "000-111-222-333";
        StringTokenizer strToken = new StringTokenizer(source, "-");
        int total = 0;

        while(strToken.hasMoreTokens()) {
            String token = strToken.nextToken();
            total += Integer.parseInt(token);
        }
        System.out.println(total);
    }
}

```

```java
666
```

### equals() : 래퍼 객체 간 기본형 값 비교

래퍼 클래스 내 equals() 메서드는 다음과 같이 오버라이딩되어 있다. (다음은 Integer 클래스 내부의 equals() 메서드 정의부이다)

```java
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}
```

위 코드를 보면 알겠지만, 다른 Integer 객체를 인자로 전달받으면, 객체 자신의 기본형 값 value와 인자로 받은 객체의 기본형 값을 비교하고, 같으면 true를 반환하는 구조임을 알 수 있다. 다음은 이를 확인하는 예제이다. 

```java
class CompareWrapperClasses {
    public static void main(String[] args) {
        Integer n1 = Integer.valueOf(10);
        Integer n2 = Integer.valueOf(11);
        Integer n3 = Integer.valueOf(10);
        Double dn1 = Double.valueOf(10.0);

        System.out.println(n1.equals(n2));
        System.out.println(n1.equals(n3));
        System.out.println(n1.equals(dn1));
    }
}

```

```java
false
true
false
```

### 그 외 메서드들

```java
class MethodsInWrapper {
    public static void main(String[] args) {
        // 메서드 오버로딩.
        Integer n1 = Integer.valueOf(10);
        Integer n2 = Integer.valueOf("32");

        System.out.println("Max : " + Integer.max(n1, n2));
        System.out.println("Min : " + Integer.min(n1, n2));
        System.out.println("Sum : " + Integer.sum(n1, n2));
        System.out.println("To Binary : " + Integer.toBinaryString(n1));
        System.out.println("To Octal : " + Integer.toOctalString(n1));
        System.out.println("To Hex : " + Integer.toHexString(n1));
    }
}

```

```java
Max : 32
Min : 10
Sum : 42
To Binary : 1010
To Octal : 12
To Hex : a
```

## Boxing and Unboxing

래퍼 클래스는 기본 자료형 값을 마치 “포장”하듯이 객체 내 변수로 저장하여 객체화한 것이다. 이렇게 기본 자료형 값을 래퍼 클래스로 객체화하는 것을 박싱(boxing)이라 하고, 반대로 래퍼 객체를 기본 자료형 값으로 변환하는 것을 언박싱(unboxing)이라 한다. 앞선 예제들로부터 보았듯, 박싱은 래퍼 클래스의 인스턴스 생성을 통해 이루어지고, 언박싱은 래퍼 객체의 메서드 호출을 통해 그 반환값으로 기본 자료형 값이 반환된다는 점을 이용한다. 

래퍼 객체는 한 번 생성되면 그 안의 기본 자료형 값을 변경할 수 없다. 따라서 값을 변경하고자 한다면 변경한 값을 가지는 새로운 래퍼 객체를 생성하는 수밖에 없다. 

```java
class BoxingAndUnBoxing {
    public static void main(String[] args) {
        Integer intObj = Integer.valueOf(5);
        int num = intObj.intValue();

        System.out.println(intObj.intValue() == num);
        System.out.printf("%d : %d\n", intObj.intValue(), num);

        // 새 Integer 객체 생성 후, 이를 intObj 참조 변수가 참조함.
        intObj = Integer.valueOf(intObj.intValue() + 7);
        num += 10;

        System.out.printf("%d : %d\n", intObj.intValue(), num);
    }
}

```

```java
true
5 : 5
12 : 15
```

위 코드를 보면 값의 수정 과정이 꽤 번거로워보인다. 다행히도 자바에서는 auto boxing, auto unboxing 기능을 제공한다. 래퍼 객체 변수에 기본 자료형 값을 직접 대입하면 컴파일러가 자동으로 이를 박싱해준다. 반대로 기본 자료형 변수에 래퍼 객체를 대입하면 자동으로 이를 언박싱해준다. 또한, 값을 수정할 때에도 오토 박싱, 언박싱을 이용할 수 있다. 

```java
class AutoBoxingUnBoxing {
    public static void main(String[] args) {
        Integer intObj = 5;  // auto boxing
        int num = intObj;  // auto unboxing

        System.out.println(intObj == num);
        System.out.printf("%d : %d\n", intObj, num);

        intObj += 7;  // intObj = Integer.valueOf(intObj.intValue() + 7);
        intObj++;  // intObj = Integer.valueOf(intObj.intValue() + 1);
        num += 10;

        System.out.printf("%d : %d\n", intObj, num);
    }
}

```

```java
true
5 : 5  
13 : 15
```

# Math 클래스 : 각종 수학 기능 제공

Math 클래스에는 각종 수학과 관련된 기능을 제공하는 메서드들이 정의되어 있으며, 모두 static 메서드들로만 구성된 유틸리티 클래스여서 따로 인스턴스를 생성하여 사용하지 않아도 된다. 

다음은 해당 클래스에서 제공하는 각종 수학 기능들 중 일부를 이용한 예제이다.

```java
class UseMath {
    public static void main(String[] args) {
        System.out.println("5 ^ 3 = " + Math.pow(5, 3));
        System.out.println("root(2) = " + Math.sqrt(2));
        System.out.println("log(e) e = " + Math.log(Math.E));  // base e
        System.out.println("PI : " + Math.PI);

        System.out.println("max(10, 20) : " + Math.max(10, 20));
        System.out.println("min(10, 20) : " + Math.min(10, 20));

        System.out.println("Math random : " + Math.random());

        // 소수부를 무조건 올림
        System.out.println("PI : " + Math.ceil(Math.PI));
        
        // 소수부를 무조건 버림.
        System.out.println(Math.floor(3.796));

        // 소수점 첫째 자리에서 반올림한 정수 반환.
        System.out.println(Math.round(3.4));
        System.out.println(Math.round(3.5));

        // 인자의 double형 값과 가장 가까운 정수를 double형으로 반환.
        System.out.println("PI : " + Math.rint(Math.PI));
    }
}

```

```java
5 ^ 3 = 125.0
root(2) = 1.4142135623730951    
log(e) e = 1.0
PI : 3.141592653589793
max(10, 20) : 20
min(10, 20) : 10
Math random : 0.2532949968488807
PI : 4.0
3.0
3
4
PI : 3.0
```

# java.util

다음에 소개할 클래스들은 모두 java.util 패키지에서 제공하는 클래스들로, java.util.클래스명을 따로 import 해줘야 사용할 수 있다. 

## Random

Random 클래스는 무작위 난수를 얻을 때 사용하는 클래스이다. 해당 클래스에는 각 기본 자료형에 대응되는 next로 시작되는 메서드들이 존재한다. 해당 메서드들은 각 기본 자료형에 해당하는 값을 무작위로 뽑아 반환하는 기능을 가진다. 

| 메서드 | 설명 |
| --- | --- |
| nextBoolean() |  |
| nextInt() |  |
| nextLong() |  |
| nextFloat() | [0.0, 1.0) 범위의 float형 난수 반환 |
| nextDouble() | [0.0, 1.0) 범위의 double형 난수 반환 |

위 메서드들 중 nextBoolean()을 제외한 나머지 메서드들은 아무런 인자를 대입하지 않으면 실수형은 0.0부터 1.0 미만의 범위 내에서 난수를 반환하고, 정수형은 해당 자료형이 표현할 수 있는 최대 음수, 양수 범위 내에서 난수를 반환한다. 

한 편, Random 클래스를 인스턴스화하여 사용할 경우, 생성자에 아무런 인자도 대입하지 않으면 매번 다른 순서대로 난수값을 얻는다. 반면, seed 값을 입력하면 매번 같은 순서의 난수값을 얻는다. 

다음은 Random 클래스를 이용한 예제이다. 

```java
import java.util.Random;

class UseRandom {
    public static void printRandRepeatly(Random rand, int endNum, int repeatNum) {
        for(int i = 0; i < repeatNum; i++) {
            System.out.print(rand.nextInt(endNum) + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        Random completelyRand = new Random();
        Random seedRand = new Random(102134);
        int rangeEnd = 10;
        int repeatNum = 10;

        System.out.println(completelyRand.nextInt());

        printRandRepeatly(completelyRand, rangeEnd, repeatNum);
        printRandRepeatly(seedRand, rangeEnd, repeatNum);
    }
}

```

```java
-27705771
5 8 7 9 3 5 9 5 6 9 
8 7 8 7 1 1 7 0 0 1
```

위 예제에서, completelyRand 참조 변수에는 Random 클래스 생성자에 아무런 값도 입력하지 않았다. 반면 seedRand 참조 변수에는 Random 클래스 생성자에 임의의 시드값을 입력하였다. 그 후 두 참조 변수에 대해 10번 연속 난수값을 출력하도록 하였을 때, completelyRand 변수를 이용하는 경우, 해당 자바 파일을 매번 실행하면 그 값도 매번 다른 반면, 시드값을 입력한 seedRand 변수의 경우 해당 파일을 매번 실행해도 매번 같은 난수 수열이 출력되는 것을 확인할 수 있다. 위 예제에서는 seedRand  변수를 이용하여 출력한 난수 수열이 “8 7 8 7 1 1 7 0 0 1” 인데, 해당 코드를 매번 실행해도 똑같이 “8 7 8 7 1 1 7 0 0 1” 으로 나온다. 

## Arrays

해당 클래스는 이미 [배열 (Array)](/java/java-array/) 페이지에서 다뤄보았다. 해당 페이지에서는 배열 내 요소들이 모두 기본 자료형 값일 때만 보았는데, 여기서는 객체들이 배열 요소로 있을 때에 대해 살펴보겠다. 

### 객체 배열 비교

`Arrays.equals(arr1, arr2)` 메서드를 이용하면, 두 배열의 길이도 같고 두 배열의 요소들의 값들도 모두 같은지를 확인할 수 있었다. 그러나 기본 자료형이 아닌 객체가 배열을 이루고 있을 경우, 해당 객체에 equals() 메서드를 따로 오버라이딩하여 재정의하지 않으면 제대로 된 배열 간 비교를 할 수 없다. 따라서 이 경우, 객체 내에 equals() 메서드를 오버라이딩하여 배열 내 객체 간 비교가 가능하도록 정의해줘야 한다. 

다음은 사용자 정의 객체들을 요소로 하는 두 배열 간 비교를 위해, 해당 객체 내에서 equals 메서드를 오버라이딩하는 예제이다. 

```java
import java.util.Arrays;

class MyInteger {
    int num;

    MyInteger(int num) {
        this.num = num;
    }

    @Override
    public String toString() {
        return Integer.valueOf(this.num).toString();
    }

    @Override
    public boolean equals(Object obj) {
        if(this.num == ((MyInteger)obj).num) {
            return true;
        } else {
            return false;
        }
    }
}

class ArraysEqual {
    public static void printMyIntegerArray(MyInteger[] myArr) {
        for(MyInteger num : myArr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        MyInteger[] arr1 = {new MyInteger(24), new MyInteger(7)};
        MyInteger[] arr2 = {new MyInteger(24), new MyInteger(7)};

        printMyIntegerArray(arr1);
        printMyIntegerArray(arr2);
        System.out.println(Arrays.equals(arr1, arr2));
    }
}

```

```java
24 7 
24 7
true
```

### 객체 배열 정렬

객체 배열을 정렬하고자 할 때, 우선 객체 내의 어떤 데이터를 기준으로 정렬할 지 미리 정한 뒤, 이를 Comparable 인터페이스의 compareTo 추상 메서드를 구현하면 정렬 방법을 정의할 수 있다. 

다음은 사이트의 유저를 추상화한 사용자 정의 클래스 User5에 대해, 유저의 ID와 포인트를 각각 담은 멤버 변수들 중, 포인트를 기준으로 User5 객체들이 담긴 배열이 오름차순 정렬되도록 내부에서 compareTo 메서드를 구현한 예제이다. 객체 배열 정렬 기준 설정 방법은 다음 예제의 compareTo 메서드 내부의 주석을 참고. 참고로 Comparable 인터페이스는 java.lang에서 제공하는 인터페이스이다. 

```java
import java.util.Arrays;

class User5 implements Comparable {
    String userId;
    int point;

    User5(String userId, int point) {
        this.userId = userId;
        this.point = point;
    }

    @Override
    public int compareTo(Object o) {
        User5 otherUser = (User5)o;

        // 현 객체의 변수값 > 비교 대상 객체의 변수값 에 대해 양수 반환 시
        // 이 객체의 배열을 정렬하면 오름차순으로 정렬됨.
        // 반대로, 같은 상황에서 음수 반환 시 
        // 내림차순으로 정렬됨. 

        // 오름차순 정렬을 위한 코드. 
        // 내림차순으로 바꾸고자 한다면 첫 if문의 반환값을 음수로, 
        // 두 번째 if문의 반환값을 양수로 바꾸면 된다. 
        if(this.point > otherUser.point) {
            return 1;
        } else if(this.point < otherUser.point) {
            return -1;
        } else {
            return 0;
        }
    }

    @Override
    public String toString() {
        return userId + " : " + point;
    }
}

class ArraysSort {
    public static void main(String[] args) {
        User5[] arr = {
            new User5("bowWow123", 2000),
            new User5("goodWorld111", 1000),
            new User5("checkGPT", 1500),
        };

        Arrays.sort(arr);

        for(User5 u : arr) {
            System.out.println(u);
        }
    }
}

```

```java
goodWorld111 : 1000
checkGPT : 1500
bowWow123 : 2000
```

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)