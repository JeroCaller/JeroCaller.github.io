---
title: "[Java] 예외 처리"
category: "Java"
tag: ["java", "자바", "예외", "예외 처리", "에러", "error", "exception"]
---

# 에러의 종류

에러 발생 시점에 따른 에러 분류

- 컴파일 에러(Compile error) : 컴파일 과정에서 생기는 에러. 주로 문법적 오류와 같은 문제에 의해 발생한다. 이러한 문제점들은 대부분 개발자의 실수로 인해 발생한 것이기에 개발자가 이러한 에러를 고침으로써 해결할 수 있다.
- 런타임 에러(Runtime error) : 프로그램 실행 도중에 발생하는 에러.

런타임 에러에는 또한, 개발자가 예측 가능하고 통제할 수 있는 에러인지, 아니면 예측 불가능하고 개발자가 통제할 수 없는 에러인지에 따라 또 다시 나뉜다. 

- 시스템 에러(System error) : 메모리 부족 등의 하드웨어적 문제 또는 운영체제에서의 문제로 인해 발생하는 에러. 이러한 에러는 개발자가 예측하기 어렵고, 소프트웨어적으로 에러를 해결할 수 없다.
- 예외(Exception) : 예측 가능하고, 개발자가 제어 및 처리 가능한 에러. 예외에 대해 개발자는 프로그램이 무사히 종료될 수 있도록 적절히 처리할 수 있으며, 또는 예외를 무시하고 계속 실행하게끔 할 수도 있다.

예외에도 두 가지로 분류된다.

- 실행 예외 : 개발자가 예외 처리를 안해도 컴파일 가능한 비검사형 예외(unchecked exception). 예외를 처리하지 않는다면 이 예외가 자바 가상 머신에 넘어가 처리를 대신 맡으며, 이 경우 에러 메시지를 출력하고 프로그램을 종료시킨다.
- 일반 예외 : 예외 처리를 하지 않으면 컴파일 오류가 발생해서 반드시 처리해야 하는 검사형 예외 (checked exception).

예외를 이렇게 분류하는 이유는, 모든 예외에 대해 무조건 처리하게끔 강제하면 프로그램 성능 저하로 이어질 수 있기 때문에, 실행 예외처럼 예외를 굳이 처리하지 않아도 컴파일이 가능하게끔 한 것이라고 한다[1]. 

자바에서 볼 수 있는 실행 예외로는 다음이 있다고 한다. 

|||
| --- | --- |
| ArithmeticException | 부적절한 산술 연산 수행 시 발생하는 예외. 예) 0으로 나눔.  |
| IllegalArgumentException | 메서드의 매개변수로 부적절한 인자를 전달할 때 발생하는 예외. |
| IndexOutOfBoundException | 배열과 같은 자료구조의 인덱스 범위를 벗어난 인덱스 사용시 발생하는 예외. |
| NoSuchElementException | 사용하고자 하는 요소가 없을 때 발생. |
| NullPointerException | null값을 가진 참조 변수에 접근 시 발생. |
| NumberFormatException | 숫자로 변경 불가능한 문자열을 숫자로 변경하려할 때 발생. |

다음은 일반 예외 일부 목록이다.

|||
| --- | --- |
| ClassNotFoundException | 존재하지 않는 클래스 사용시 발생. |
| NoSuchFieldException | 사용하고자 하는 클래스의 필드가 없을 때 발생. |
| NoSuchMethodException | 사용하고자 하는 클래스의 메서드가 없을 때 발생. |
| IOException | 입출력에 문제 있을 때 발생. |

다음은 실행 예외의 한 예이다.

```java
import java.util.Random;

class ExceptionEx {
    public static void main(String[] args) {
        Random rand = new Random();
        int n1 = 22;
        int n2 = rand.nextInt(2);
        int result = n1 / n2;
        System.out.printf("%d / %d = %d\n", n1, n2, result);

    }
}

```

```java
> 22 / 1 = 22
> Exception in thread "main" java.lang.ArithmeticException: / by zero
        at ExceptionEx.main(ExceptionEx.java:8)
```

위 예제를 보면, n2 변수에 값을 할당할 때, 0, 1 두 숫자 중에 하나를 무작위로 추출하여 이를 n2 변수에 할당하는 방식이다. 그리고 이 n2를 나눗셈의 분모로 사용하고 있다. 이 경우, n2가 1이 나온다면 정상적으로 프로그램이 실행되지만, 0이 나온다면 에러가 발생한다. 이러한 오류는 프로그램을 실행 시에 발생하며, 컴파일 자체에는 문제가 없기에 실행 예외로 분류된다. 이러한 예외는 개발자가 적절히 예외 처리를 할 수도 있고, 만약 처리를 안한다면 자바 가상 머신으로 이 예외가 넘어가며, 자바 가상 머신은 예외를 마주하면 프로그램을 안정적으로 종료시킨다. 

한 편, 자바에서는 예외도 클래스로 다룬다. 자바에서 다루는 최상위 클래스가 Exception이면 그 아래에 예외 클래스 Throwable 클래스가 자식 클래스로 있으며, 그 아래에 Exception 클래스가 존재한다. 이 Exception 클래스가 모든 예외 클래스들의 최상위 클래스이다. 앞서 살펴본 실행 예외와 일반 예외 목록들의 예외들도 모두 자바에서 다루는 예외 클래스 이름이기도 하다. 

# 예외 처리 : try ~ catch ~ finally

그렇다면 개발자가 예외를 처리하려면 어떻게 해야 할까? 자바에서는 try ~ catch와 finally 문법을 이용하여 예외를 원하는대로 처리할 수 있다. 

```java
// 예시 1. 여러 예외들을 나눠서 처리하고자 할 경우.
try {
    // 코드. 예외가 발생할 것으로 예측되는 코드를 작성한다. 
} catch (예외1 e) {
    // 예외1 발생 시 실행할 코드.
} catch (예외2 e) {
    // 예외2 발생 시 실행할 코드.
} finally {
    // 예외 발생 여부 상관없이 무조건 실행시킬 코드.
}

// 예시 2
try {
    // 실행 코드
} catch (예외 e) {
    // 예외 발생 시 실행할 코드
}

// 예시 3. 예외는 무시하되, 꼭 실행해야할 코드가 있을 경우.
try {
    // 실행 코드
} finally {
    // 예외 발생 여부 상관없이 무조건 실행할 코드.
}
```

위 예시를 보면 알 수 있듯, 자바에서 예외 처리를 위해선 try 구문은 반드시 작성하고, 상황에 따라 여러 개의 catch 문을 넣을 수도, 하나만 넣을 수도 있다. 또한 try ~ catch문만 사용하고 finally를 생략할 수도 있고, 반대로 try ~ finally만 사용하고 catch문은 생략할 수 있다. 

다음 예제는 발생할 수 있는 예외 2개를 각각의 catch문으로 나눠 처리하는 예제이다. 또한 마지막에 finally 구문을 넣어 특정 문구를 예외 발생 여부에 상관없이 무조건 출력하게끔 하고 있다. 

```java
import java.util.InputMismatchException;
import java.util.Scanner;

class TryCatchEx {
    public static void main(String[] args) {
        Scanner userInput = new Scanner(System.in);

        System.out.println("정수 2개를 입력하세요.");
        try {
            int n1 = userInput.nextInt();
            int n2 = userInput.nextInt();
            int result = n1 / n2;
            System.out.printf("%d / %d = %d\n", n1, n2, result);
        } catch(ArithmeticException arEx) {
            String strExMsg = arEx.getMessage();
            System.out.println(strExMsg);
            if (strExMsg.equals("/ by zero")) {
                System.out.println("예외 : 분모에 0이 들어가선 안됩니다.");
            }
        } catch(InputMismatchException inputEx) {
            System.out.println(inputEx.getMessage());
            inputEx.printStackTrace();
        } finally {
            System.out.println("프로그램을 종료합니다.");
        }
    }
}

```

```java
정수 2개를 입력하세요.
1 0
/ by zero
예외 : 분모에 0이 들어가선 안됩니다.
프로그램을 종료합니다.

정수 2개를 입력하세요.
1 ㄷ
null
java.util.InputMismatchException
        at java.base/java.util.Scanner.throwFor(Scanner.java:939)
        at java.base/java.util.Scanner.next(Scanner.java:1594)
        at java.base/java.util.Scanner.nextInt(Scanner.java:2258)
        at java.base/java.util.Scanner.nextInt(Scanner.java:2212)
        at TryCatchEx.main(TryCatchEx.java:11)
프로그램을 종료합니다.
```

다음은, 사용자가 두 정수 입력 후 두 정수를 나눈 결과를 출력하는 예제 코드인데, 사용자가 데이터 입력 시 두 번째 정수에 0을 넣어 의도치 않게 0으로 나누는 산술 연산 예외와, 숫자가 아닌 문자를 기입하는 예외(InputMismatchException) 이 두 예외를 하나의 catch 문에 합쳐서 처리하는 예제이다. 

```java
import java.util.InputMismatchException;
import java.util.Scanner;

class TryCatchEx2 {
    public static void main(String[] args) {
        Scanner userInput = new Scanner(System.in);

        System.out.println("정수 2개를 입력하세요.");
        try {
            int n1 = userInput.nextInt();
            int n2 = userInput.nextInt();
            int result = n1 / n2;
            System.out.printf("%d / %d = %d\n", n1, n2, result);
        } catch (ArithmeticException | InputMismatchException ex) {
            System.out.println("==========");
            ex.printStackTrace();
            System.out.println("==========");
        }
    }
}

```

```java
정수 2개를 입력하세요.
1 0
==========
java.lang.ArithmeticException: / by zero        
        at TryCatchEx2.main(TryCatchEx2.java:12)
==========
```

위 예제들과 같이, 발생할 것으로 예상되는 예외들에 대해, 해당 예외 클래스들을 catch 문에서 사용하여 처리하고 있음을 알 수 있다. 반면, 모든 예외 클래스의 최상위 클래스인 Exception 클래스를 직접 사용함으로써, 모든 예외들을 다루게끔 할 수도 있다. 

```java
import java.util.InputMismatchException;
import java.util.Scanner;

class WithException {
    public static void main(String[] args) {
        Scanner userInput = new Scanner(System.in);

        System.out.println("정수 2개를 입력하세요.");
        try {
            int n1 = userInput.nextInt();
            int n2 = userInput.nextInt();
            int result = n1 / n2;
            System.out.printf("%d / %d = %d\n", n1, n2, result);
        } catch(Exception e) {
            System.out.println("========= 예외 발생 =========");
            System.out.println("자세한 사항은 다음 에러 메시지를 참고.\n");
            e.printStackTrace();
            System.out.println("============================");
        }
    }
}

```

## 예외 변수

앞선 예제들을 보면, catch 문 뒤에 나오는 괄호 안에 예외 클래스명을 적은 뒤, 그 뒤에 변수명을 적은 것을 공통적으로 볼 수 있다. 

```java
catch(Exception e) 
```

예외 클래스 뒤에는 예외 객체를 담는 참조 변수이다. 따라서 해당 변수에 대해 instanceof 연산자를 적용할 수 있다. 

```java
class ExceptionVar {
    public static int getDivide(int a, int b) {
        return a / b;
    }
    
    public static void main(String[] args) {
        try {
            int result = getDivide(1, 0);
        } catch(ArithmeticException arEx) {
            System.out.println(arEx instanceof ArithmeticException);
            System.out.println(arEx instanceof Exception);
        }
    }
}

```

```java
true
true
```

앞선 예제들에서도 보았지만, 예외 변수에 담긴 예외 객체로 다음의 메서드들을 사용할 수 있다.

| 메서드 | 설명 |
| --- | --- |
| e.getMessage() | 해당 에러에 대한 상세 메시지를 String형으로 반환. |
| e.printStackTrace() | 에러가 발생한 곳들에 대한 정보를 단계적으로 출력. |

# 예외 처리 던지기

어떤 메서드를 다른 메서드 내부에서 호출하고, 그 메서드를 또 다른 메서드에서 호출하는 형태의 코드들도 볼 수 있다. 이 때, 어떤 메서드에서 예외가 발생하면 그 메서드에서 바로 처리하지 않고, 그 메서드를 호출하는 곳에 예외를 넘겨버릴 수도 있다. 이를 보통 예외를 “던진다(throw)”라고 표현한다. 이 경우, 예외가 발생한 메서드를 호출하는 메서드에서 예외를 처리하게끔 하거나, 계속 예외를 미뤄 main 메서드, JVM까지 미룰 수도 있다. 

예외 처리를 미루는 방법은 간단하다. 예외가 발생할 것으로 예상되는 메서드에서 try ~ catch문을 통한 예외 처리문을 따로 작성하지 않으면 된다. 그러면 예외 발생 시 해당 메서드를 호출한 곳으로 예외가 자동으로 넘어간다. 다음은 예외를 미루고, 이를 그 어디에도 try ~ catch문을 통해 예외 처리하지 않는 예제 코드이다. 

```java
class DelayException {
    public static void methodCaller(int n) {
        System.out.println(getDivideQuotient(n, 0));
    }

    public static int getDivideQuotient(int a, int b) {
        return a / b;
    }

    public static void main(String[] args) {
        methodCaller(10);
    }
}

```

```java
Exception in thread "main" java.lang.ArithmeticException: / by zero
        at DelayException.getDivideQuotient(DelayException.java:7)
        at DelayException.methodCaller(DelayException.java:3)
        at DelayException.main(DelayException.java:11)
```

위 코드에서 예외가 직접 발생한 곳은 getDivideQuotient 메서드 내부이다. 그러나 해당 메서드에서 바로 예외를 처리하지 않아 이 예외가 해당 메서드를 호출한 methodCaller 메서드로 넘어간다. 그런데 해당 메서드에서도 예외를 처리하지 않아 예외가 main 메서드로, main 메서드에서도 예외를 처리하지 않아 JVM으로 넘어가 결국엔 예외 메시지를 출력하고 종료된 모습이다. 

이렇게 던져진 예외를 try ~ catch문으로 처리할 때 던져진 예외를 뜻하는 Throwable 예외 클래스를 사용하면 된다. 

```java
import java.util.Scanner;

class UseThrowable {
    public static void inputAndPrint() {
        Scanner scan = new Scanner(System.in);

        System.out.println("두 정수 입력.");
        int n1 = scan.nextInt();
        int n2 = scan.nextInt();
        int result = getDivide(n1, n2);
        System.out.println(result);
    }

    public static int getDivide(int a, int b) {
        return a / b;
    }

    public static void main(String[] args) {
        try {
            inputAndPrint();
        } catch(Throwable e) {  // Throwable로 던져진 예외 처리.
            System.out.println("====== 에러 발생 ======");
            System.out.println(e.getMessage());
            e.printStackTrace();
            System.out.println("======================");
        }
    }
}

```

```java
두 정수 입력.
1 a
====== 에러 발생 ======
null
java.util.InputMismatchException
        at java.base/java.util.Scanner.throwFor(Scanner.java:939)
        at java.base/java.util.Scanner.next(Scanner.java:1594)
        at java.base/java.util.Scanner.nextInt(Scanner.java:2258)
        at java.base/java.util.Scanner.nextInt(Scanner.java:2212)
        at UseThrowable.inputAndPrint(UseThrowable.java:9)
        at UseThrowable.main(UseThrowable.java:20)
======================
```

만약 예외가 발생할 수 있는 메서드를 여러 곳에서 사용할 경우, 사용처마다 예외 처리를 각기 다르게 하고 싶을 때 예외 처리를 뒤로 미루는 방식을 이용한다. 이 것이 예외 처리를 뒤로 미루는 이유이다. 

# 메서드 선언부에 발생 가능한 예외 선언

```java
class DeclareExceptionInMethod {
    public static void printIntArray(int[] numArr, int len) 
        throws IndexOutOfBoundsException 
    {
        for(int i = 0; i < len; i++) {
            System.out.println(numArr[i]);
        }
    }
    public static void main(String[] args) {
        int[] numArr = {1, 2, 3, 4, 5};

        try {
            printIntArray(numArr, 10);
        } catch(IndexOutOfBoundsException e) {
            System.out.println(e.getMessage());
            printIntArray(numArr, numArr.length);
        }
    }
}

```

```java
1
2
3
4
5
Index 5 out of bounds for length 5
1
2
3
4
5
```

위 예제의 printIntArray 선언부를 보면, 시그니처 바로 다음에 throws 키워드가 나오고, 그 뒤에 예외 클래스명이 나온 것을 볼 수 있다. 이렇게 명시하면 해당 메서드를 사용하는 사람이 해당 메서드에서 어떤 예외가 발생할 수 있는지 미리 알 수 있다는 이점이 있다. 예외 선언 시 위 예제처럼 하나만 선언할 수도 있고, 쉼표를 통해 여러 예외들을 열거하며 선언할 수도 있다. 

예외 선언에는 실행 예외와 일반 예외 모두 사용할 수 있지만, 보통은 반드시 예외 처리를 해야만 하는 일반 예외만 선언한다고 한다. 아무래도 일반 예외가 실행 예외에 비해서는 반드시 처리해야 하는 강제성이 있어서 그런 것 같다.

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)