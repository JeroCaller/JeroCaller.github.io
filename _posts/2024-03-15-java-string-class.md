---
title: "[Java] String 클래스"
category: "Java"
tag: ["java", "자바", "string", "문자열"]
---

자바에서는 문자열 데이터를 다룰 때 String 클래스로 다룬다. 따라서 문자열 데이터를 다루고자 한다면 해당 클래스에 대해 알아야한다. 

# 문자열 생성

자바에서는 문자열을 생성하고 이를 변수에 할당하는 방법이 두 가지이다. 하나는 `new String(”문자열”)` 과 같이 명시적으로 String 클래스를 new를 통해 인스턴스화시키는 방법이고, 또 하나는 문자열 리터럴 상수를 그대로 변수에 할당하는 방법이다. 

```java
String myString1 = new String("Hi");
String myString2 = "Hello";
```

그런데, 이 두 가지 방법은 겉으로만 차이가 나는 것이 아니라 메모리 상에서의 처리 과정도 다르다. 

new 키워드를 이용하여 String 클래스를 명시하여 인스턴스화하는 방법은 해당 클래스로부터 객체를 힙에 생성하고, 변수가 이 객체의 참조 값을 할당받는 방식을 가진다. 이 방법은 무조건 String 객체를 생성하기 때문에 아무리 똑같은 문자열 리터럴을 주더라도 항상 String 객체를 생성한다. 

문자열 리터럴을 직접 할당하는 방식은 코드 상에서는 `new String()`을 명시하지 않았지만 실행 시 암시적으로 해당 객체를 생성하고, 이 객체를 변수에 할당하는 방식이다. 다만, 똑같은 문자열 리터럴이 이미 있는 경우, 새로 String 객체를 생성하지 않고 기존에 이미 생성된 String 객체를 참조한다. 

다음은 문자열 생성 방식에 따라 같은 문자열에 대해서도 새로운 String 객체를 생성하는지 아니면 기존 String 객체를 참조하는지를 확인하는 예제이다. 

```java
public class NewString {
    public static void main(String[] args) {
        String sayHello1 = new String("Hello");
        String sayHello2 = new String("Hello");
        String sayHello3 = "Hello";
        String sayHello4 = "Hello";

        System.out.println(sayHello1 + " " + System.identityHashCode(sayHello1));
        System.out.println(sayHello2 + " " + System.identityHashCode(sayHello2));
        System.out.println(sayHello3 + " " + System.identityHashCode(sayHello3));
        System.out.println(sayHello4 + " " + System.identityHashCode(sayHello4));
    }
}

```

```java
Hello 1159190947
Hello 925858445
Hello 798154996
Hello 798154996
```

`System.identityHashCode(object)` 메서드에 객체를 대입하면 그 객체에 대한 고유한 해시코드를 반환한다. 즉, 서로 다른 객체는 그 해시 코드도 다르고, 서로 동일한 객체는 그 해시 코드가 같다. 이러한 방식으로 두 객체가 서로 다른지 같은지를 확인할 수 있다. 

위 예제를 보면 4개의 변수 모두 같은 문자열 “Hello”를 생성, 할당하고 있다. 그럼에도 출력결과를 보면 각 객체들의 해시 코드가 첫 번째, 두 번째는 서로 다르고, 이는 3, 4번째와도 다름을 알 수 있다. 즉, 코드 상으로는 분명 `new String("Hello")` 으로 같지만 메모리 상에서는 새로운 String 객체를 생성하기에 두 개의 String 객체가 생성되어 그 참조값이 각각 sayHello1, sayHello2 변수에 할당된 것이다. 반면, sayHello3, sayHello4 변수의 해시 코드값을 보면 서로 동일한 것을 알 수 있다. 이는 이미 “Hello”에 대한 String 객체를 한 번 생성한 후, sayHello4부터는 해당 객체를 참조하고 있는 것이다. 

# 문자열은 객체다 : 비교 연산의 함정

앞에서 봤듯이 문자열은 String 객체를 통해 다뤄진다. 그러다 보니 이 객체를 참조하는 변수는 말 그대로 참조 변수라서, 값을 가지고 있는 것이 아니라 그 객체의 id값을 참조하는 형식이다. 

이 사실을 모른 상태로 두 문자열이 같은지 비교하기 위해 단순히 문자열 변수들에 대해 == 비교 연산을 취하면 원하는 결과를 얻을 수 없다. 문자열 자체를 비교하는 게 아니라 문자열 객체의 id값을 비교하는 것이기 때문이다. 

```java
public class StringRefCompare {
    public static void main(String[] args) {
        String sayHello1 = new String("Hello");
        String sayHello2 = new String("Hello");
        String sayHello3 = "Hello";
        String sayHello4 = "Hello";

        System.out.println("sayHello1 vs sayHello2");
        if (sayHello1 == sayHello2) {
            System.out.println("match");
        } else {
            System.out.println("dismatch");
        }

        System.out.println("sayHello3 vs sayHello4");
        if (sayHello3 == sayHello4) {
            System.out.println("match");
        } else {
            System.out.println("dismatch");
        }

        System.out.println("sayHello2 vs sayHello3");
        if (sayHello2 == sayHello3) {
            System.out.println("match");
        } else {
            System.out.println("dismatch");
        }
    }
}

```

```java
sayHello1 vs sayHello2
dismatch
sayHello3 vs sayHello4
match
sayHello2 vs sayHello3
dismatch
```

위 예제를 보면, sayHello1, sayHello2 변수는 모두 `new String()` 을 통해 명시적으로 String 객체를 생성하여 문자열을 생성하였다. 이 두 변수에는 서로 다른 객체의 참조값을 가지고 있으므로 비록 문자열이 같다하더라도 == 비교 연산을 취하면 서로 다르다는 결과가 나올 것이다. 

반면, sayHello3, sayHello4는 모두 문자열 리터럴을 직접 대입하는 방식으로 문자열을 할당하였다. 그리고 이 두 변수를 == 연산자를 통해 비교한 결과, 둘이 일치한다고 나왔다. 그러면 이 결과를 보고 우리는 “두 변수에 담긴 두 문자열은 서로 동일해!”라고 할 수 있을까? 이렇게 말하기에는 조금 애매한 면이 있다. 여기서도 문자열 자체를 비교한 것이 아니라 문자열 객체의 참조값을 비교한 것이다. 앞서 언급했듯 문자열 리터럴을 직접 대입하는 방식은 String 객체를 단 한 번만 생성하고, 이후 똑같은 문자열 리터럴에 대한 변수 할당이 이뤄지면 기존 String 객체를 참조하는 방식이라고 했다. 이로 인해 두 변수는 서로 같은 객체를 참조하므로 그 id값도 당연히 같을 수 밖에 없는 것이다. 이조차도 id값끼리 비교한 것이지, 문자열 내용을 비교한 것이라 볼 수 없을 것이다.

String 클래스에서는 문자열을 취급하는 여러 기능들을 메서드로 제공한다. 여기에는 두 문자열을 비교하는 메서드도 있다. 따라서 두 문자열을 비교하고자 한다면 이 메서드를 활용해야 한다. 

# String 클래스의 메서드들

## equals(), compareTo(), compareToIgnoreCase() : 두 문자열 내용 비교

- `boolean String1.equals(String2)` : 두 문자열이 같은지 비교하고 그 결과를 boolean형으로 반환.
- `int String1.compareTo(String 2)` : 두 문자열을 사전 순으로 비교한다. 즉, 각 문자열을 이루는 문자들을 맨 앞에서부터 확인하여 각 문자들의 유니코드 값을 비교한 후, 두 문자열이 완전히 같다면 0을, 만약 어느 문자부터 다르다면 두 유니코드 값의 차를 반환한다. 예를 들어 `“apple”.compareTo(”banana”)` 가 있다면 “apple”의 a와 “banana”의 b가 사전 순으로 차이가 나므로 a의 유니코드 값 - b의 유니코드값 = -1이 나올 것이다. 해당 메서드는 영어 대소문자를 구분한다.
- `int String1.compareToIgnoreCase(String 2)` : 두 문자열을 사전 순으로 비교한다. 단, 영어 대소문자를 구분하지 않는다.

다음은 위 메서드들을 이용한 예제이다.

```java
public class StringCompare {
    public static void printIfMatchStrs(String s1, String s2) {
        if(s1.equals(s2)) {
            System.out.println("Match");
        } else {
            System.out.println("Dismatch");
        }
    }

    public static void printCompareStrs(String s1, String s2, boolean ignoreCase) {
        int compareNum;
        
        if(ignoreCase) {
            compareNum = s1.compareToIgnoreCase(s2);
        } else {
            compareNum = s1.compareTo(s2);
        }

        if(compareNum == 0) {
            System.out.print("Match");
        } else if(compareNum > 0) {
            System.out.print(s1 + " > " + s2);
        } else {
            System.out.print(s1 + " < " + s2);
        }
        System.out.print(" | ");
        System.out.println("compare number: " + compareNum);
    }

    public static void main(String[] args) {
        String strObj1 = new String("wow");
        String strObj2 = new String("Wow");
        String strObj3 = new String("bow");
        String strObj4 = "good";

        printIfMatchStrs(strObj1, strObj2);
        printCompareStrs(strObj1, strObj2, true);
        printCompareStrs(strObj1, strObj2, false);
        printCompareStrs(strObj3, strObj4, false);
    }
}

```

```java
Dismatch
Match | compare number: 0
wow > Wow | compare number: 32
bow < good | compare number: -5
```

## concat() : 두 문자열 합치기

`String string1.concat(string2)` : 두 문자열을 합친 새로운 문자열을 반환.

```java
class ConCat {
    public static void main(String[] args) {
        String str1 = new String("Pineapple");
        String str2 = "Apple";

        String result = str1.concat(str2);  // 문자열 리터럴과 String 합침 가능.
        System.out.println(result);
        System.out.println(str1);  // 원래 문자열에는 변함이 없다.
        System.out.println(str2);  // 원래 문자열에는 변함이 없다.

        System.out.println(result.concat("Pen"));
    }
}

```

```java
PineappleApple
Pineapple        
Apple
PineappleApplePen
```

## indexOf() : 문자열 속 문자 또는 부분 문자열을 찾아 그 시작 인덱스를 반환

`int string.indexof(substring, 찾을 위치)`  : 문자열 속에서 찾을 문자 또는 부분 문자열을 입력하면, 해당 문자 또는 부분 문자열이 존재한다면 해당 문자 또는 부분 문자열의 시작 위치 인덱스를 반환한다. 만약 찾지 못했다면 -1을 반환한다. 만약 두 번째 인자에 인덱스 숫자를 입력했다면, 해당 위치부터 그 뒤에 있는 문자들 사이에서 찾는다. 해당 메서드는 영어 대소문자를 구분한다. 

```java
class IndexOf {
    public static void main(String[] args) {
        String src1 = "HelloWorld";
        int n1 = src1.indexOf("o");
        int n2 = src1.indexOf("o", n1+1);
        int n3 = src1.indexOf("q");

        System.out.println(n1);
        System.out.println(n2);
        System.out.println(n3);

        String src2 = "가는 말이 고와야 오는 말도 곱다.";
        // 한 글자 뿐만 아니라 여러 글자의 단어를 검색할 수도 있다.
        int targetIdx = src2.indexOf("고와야"); 
        System.out.println(targetIdx);
    }
}

```

```java
4
6
-1
6
```

## substring() : 문자열 자르기

`String string.substring(start, end)` : start부터 end-1 인덱스의 문자열만을 반환한다. 두 번째 인자를 생략하면 첫 번째 인자로 대입된 인덱스부터 끝까지를 반환한다. 원래 문자열에 영향을 끼치지 않는다. 

```java
class SubString {
    public static void main(String[] args) {
        String src = "Cat-Cow-Bowl";
        int idx1 = src.indexOf("Cow");
        int idx2 = src.indexOf("Bowl");

        String subStr1 = src.substring(idx1, idx2 - 1);
        String subStr2 = src.substring(idx2);

        System.out.println(subStr1);
        System.out.println(subStr2);

        // 원본 문자열은 보존되는가?
        System.out.println(src);
    }
}

```

```java
Cow
Bowl
Cat-Cow-Bowl
```

## length() : 문자열의 길이 얻기

`int string.length()` : 문자열의 길이를 반환한다. 

```java
class Length {
    public static void main(String[] args) {
        String str = "good";
        System.out.println(str.length());
    }
}

```

```java
4
```

## valueOf() : 다른 자료형을 문자열로 변형.

`valueOf()` 괄호 안에 문자열 자료형으로 바꿀 다른 기본 자료형 값을 대입하면 해당 값을 문자열로 바꾼 결과물을 반환한다. 

```java
class ValueOf {
    public static void main(String[] args) {
        String s1 = String.valueOf(true);
        String s2 = String.valueOf(2);
        String s3 = String.valueOf(3.14);
        String s4 = String.valueOf('W');

        System.out.println(s1);
        System.out.println(s2);
        System.out.println(s3);
        System.out.println(s4);
    }
}

```

```java
true
2
3.14
W
```

## 그 외 메서드들

```java
class OtherStringMethods {
    public static void main(String[] args) {
        String con = "shape";
        System.out.println(con.contains("a"));

        // stringSrc.startsWith(string) : 문자열이 string으로 시작하는지를 조사 후 boolean으로 반환.
        System.out.println("good".startsWith("g"));
        System.out.println("good for you".startsWith("good"));

        // stringSrc.endsWith(string) : 문자열이 string으로 끝나는지를 조사 후 boolean으로 결과값 반환.
        System.out.println("good for you".endsWith("you"));

        // boolean string.isEmpty() : 문자열의 길이가 0인지 확인. (string.length == 0)
        System.out.println("".isEmpty());

        // toUpperCase() : 문자열의 모든 영문자들을 대문자로 변경.
        // toLowerCase() : 문자열의 모든 영문자들을 소문자로 변경.
        System.out.println("how ARE you".toUpperCase());
        System.out.println("HOW are YOU".toLowerCase());

        String strWithSpace = "  굳 아이디어  ";
        // 문자열 양쪽 공백 제거한 결과물을 반환.
        String strWithoutSpace = strWithSpace.trim();
        System.out.println(strWithoutSpace);
        System.out.println(strWithSpace.length());
        System.out.println(strWithoutSpace.length());
    }
}

```

```java
true
true
true
true
true
HOW ARE YOU
how are you
굳 아이디어
10
6
```

# 문자열 관련 연산

문자열에 대해 + 기호를 사용하면 `concat()` 메서드처럼 두 문자열을 하나로 합친다. 이는 += 이라는 복합 대입 연산도 마찬가지이다. 

```java
class StringOperators {
    public static void main(String[] args) {
        String str1 = "hi" + "good";
        String str2 = "hi".concat("good");
        String str3 = "hi";
        str3 += "good";

        System.out.println(str1);
        System.out.println(str2);
        System.out.println(str3);

        // String thisYear = "이번년도 : ".concat(String.valueOf(2024));
        String thisYear = "이번년도 : " + 2024;
        System.out.println(thisYear);

        String suceedStr = "aa".concat("bb").concat("cc");
        System.out.println(suceedStr);
    }
}

```

```java
higood
higood
higood
이번년도 : 2024
aabbcc
```

위 예제 코드의 thisYear 변수 부분을 보면, + 기호를 썼으나 한 쪽은 문자열, 다른 한 쪽은 숫자형이 사용되었다. 이 경우, 문자열이 아닌 기본 자료형의 값이 문자열로 자동 형변환되어 합쳐진다. 

문자열에 대한 + 연산을 실행하면 컴파일러는 이를 `concat()` 메서드로 바꿔서 실행시킨다고 한다. 

# StringBuilder 클래스로 여러 개의 문자열을 하나로 합치기

`“문자열”.concat(”a”).concat(”b”).concat(”c”);` 와 같이 여러 개의 문자열들을 하나로 결합 할 때 concat() 메서드를 사용하면 반환값인 String 객체가 그만큼 여러 개가 나오는 셈이 되므로 메모리를 효율적으로 사용하는 방식이라 보기 어렵다. String 객체는 한 번 초기화된 문자열이 변경될 수 없는 immutable 자료형이라서 두 문자열을 합치려면 아예 새로운 빈 String 객체를 생성한 후, 두 문자열을 합친 결과물을 해당 객체에 할당하여 이를 반환하는 방식이어야 한다. 

이러한 문자열 결합 시 메모리 비효율성을 극복하기 위해, 대신 StringBuilder 클래스를 사용한다. 해당 클래스는 mutable 변수를 가져 객체를 매번 생성할 일이 없다고 한다. 그래서 메모리 효율성을 확보할 수 있고, 문자열 결합 속도도 그 만큼 빨라진다고 한다. 

다음은 StringBuilder 클래스를 이용하는 예제이다.

```java
class AboutStringBuilder {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("자바");
        
        sb.append("파이썬");
        System.out.println(sb.toString());

        sb.append("JavaScript");
        System.out.println(sb.toString());

        // delele(start, end) : start 인덱스부터 end - 1까지를 삭제
        sb.delete(2, 5);
        System.out.println(sb.toString());

        // replace(start, end, newStr) : start 인덱스부터 end - 1까지를 newStr 문자열로 대체.
        sb.replace(1, 4, "CSS");
        System.out.println(sb.toString());

        sb.reverse();
        System.out.println(sb.toString());
    }
}

```

```java
자바파이썬
자바파이썬JavaScript
자바JavaScript
자CSSvaScript
tpircSavSSC자
```

또한, 만약 스레드 안정성을 고려해야하는 상황이 있다면 대신 StringBuffer 클래스를 사용할 수 있다. 스레드 안정성이 있다는 걸 빼면 StringBuilder 클래스와 동일한 기능을 수행한다고 한다. 

# 문자열 분할

문자열 결합과 달리, 문자열 내에서 반복되는 구분자를 기준으로 여러 개의 부분 문자열로 분할시킬 수 있는 클래스도 존재한다. StringTokenizer 클래스가 이러한 역할을 한다. 참고로 분할된 문자열을 토큰(token)이라고 한다. 

해당 클래스는 문자열 결합 시에 사용하는 StringBuilder와 달리, 따로 import를 해줘야 한다. 

```java
import java.util.StringTokenizer;
```

다음은 해당 클래스를 활용한 예제이다.

```java
import java.util.StringTokenizer;

class Token {
    public static void printTokens(StringTokenizer stTo) {
        // hasMoreTokens() : 토큰이 아직 하나라도 남아있다면 true 반환.
        while (stTo.hasMoreTokens()) {
            // nextToken() : 토큰을 순서대로 하나씩 반환한다. 
            System.out.println(stTo.nextToken());
        }
    }

    public static void main(String[] args) {
        // 첫 번째 인자 : 분할할 문자열.
        // 두 번째 인자 : 구분자. (생략 시 공백, 탭 또는 개행(\n)으로 기본 설정)
        StringTokenizer st = new StringTokenizer("1 2 3");
        StringTokenizer st2 = new StringTokenizer("1-2-3", "-");

        printTokens(st);
        System.out.println("=======");
        printTokens(st2);
    }
}

```

```java
1
2      
3      
=======
1      
2      
3
```

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)