---
title: "[Java] 패키지와 클래스 패스"
category: "Java"
tag: ["java", "자바", "패키지", "클래스 패스", "package", "class path"]
---

자바에서는 보통 프로젝트 패키지를 생성하면 그 아래의 src 폴더 내에 자바 파일들을 생성하고 코딩을 할 것이다. 그런데 프로젝트 규모가 커질수록 모든 자바 파일들을 src 폴더에 일괄적으로 놓으면 관리가 힘들어지고, 겹치는 클래스명이 하나라도 있을 경우 실행이 되지 않는다. 이럴 경우, src 폴더 내에 다른 폴더들을 생성하고, 기능별로 자바 파일들을 구분하여 저장할 것이다. 이렇게 서로 관련 있는 클래스 또는 패키지를 분류해 놓은 것을 패키지(package)라 한다. 

그런데, 이렇게 패키징을 해놓으면 컴파일러는 어느 폴더에 무엇이 있는지 몰라 그냥 실행하면 에러가 발생할 수 있다. 따라서 컴파일러가 패키지 구조를 인식할 수 있게 해야한다. 이번 페이지에서는 패키지 경로를 컴파일러가 인식할 수 있게 하는 방법들에 대해 살펴보고자 한다. 

# Class path

자바 파일을 실행하려면 먼저 해당 파일로부터 클래스 파일을 생성한 후, 자바 가상 머신이 해당 클래스 파일을 찾아 실행시키는 과정을 거친다. 보통은 클래스 파일이 자바 파일과 같은 곳에 있을 것이다. 그런데 만약 클래스 파일과 자바 파일이 다른 폴더에 존재한 상태에서 실행시키면 에러가 발생할 것이다. 자바 가상 머신이 클래스 파일 경로를 인식하지 못했기 때문이다. 

자바 가상 머신이 클래스 파일을 찾는 순서는 다음과 같다. 

1. 같은 폴더 내에서 클래스 파일을 찾고, 있다면 이를 실행시킨다.
2. 경로가 지정되었다면 해당 경로 내 클래스 파일을 찾아 실행시킨다. 이 때 경로 지정은 클래스 패스 또는 패키지를 이용하여 지정할 수 있다. 패키지를 이용하는 방법은 후술.
3. 위 두 방법으로도 찾지 못한 경우, 클래스 패스에 지정된 폴더를 탐색하여 만약 존재한다면 이를 실행시킨다. 

여기서는 자바 파일과 클래스 파일이 서로 다른 경로에 존재할 경우, 자바 가상 머신이 클래스 파일의 위치를 인식할 수 있도록 클래스 경로(class path)를 입력하여 실행시키는 방법에 대해 살펴보겠다. 

다음의 패키지 구조가 있다고 가정해보겠다. 

![사진 1-1.](/images/2024-03-14/2024-03-14-java-package-and-classpath-1.png)

사진 1-1.

그리고 SomeClass.java와 CallSomeClass.java 파일 내 코드는 각각 다음과 같다. 

```java
public class SomeClass {
    public void printHello() {
        System.out.println("Hello, world!");
    }
}

```
SomeClass.java

```java
public class CallSomeClass {
    public static void main(String[] args) {
        SomeClass sc = new SomeClass();
        sc.printHello();
    }
}

```
CallSomeClass.java

명령 프롬프트 창에서 CallSomeClass.java 파일로부터 클래스 파일을 생성하였다. SomeClass 클래스는 CallSomeClass 클래스의 메인 메서드 내부에서 호출되어 사용되어서 해당 자바 파일도 자동으로 클래스 파일이 생성되었다. 그 후, SomeClass.class 파일을 src 폴더 내 sublib 폴더로 이동시켜보았다. 이 상태에서, 현재 작업 경로가 src 폴더로 되어 있는 명령 프롬프트 창에서 `java CallSomeClass` 를 입력하여 해당 파일을 실행시키려고 하면 다음의 메시지가 출력된다. 

![사진 1-2. ](/images/2024-03-14/2024-03-14-java-package-and-classpath-2.png)

사진 1-2. 

`NoClassDefFoundError` 에러가 뜨면서 실행이 되지 않은 것을 볼 수 있다. 즉, 자바 가상 머신이 sublib 내 SomeClass.class 파일을 인식하지 못하여 실행되지 못한 것이다. 

이 경우, 명령어에서 직접 해당 클래스 경로를 입력하여 알려주는 방법이 있다. 명령어 구조는 다음과 같다. 

```java
java -classpath .;다른폴더경로 실행파일
```

`-classpath` 명령 인자와 실행파일명 사이에는 실행하고자 하는 클래스 파일들의 경로명들을 입력하는 곳이다. 위 예제에서 실행하려고 하는 클래스 파일들은 각각 src라는 현재 폴더와, 해당 폴더 아래 sublib 라는 하위 폴더 내에 존재한다. 따라서 이 둘의 경로를 모두 입력해준다. 이 때, 각 폴더 경로 간의 구분은 세미콜론(;)을 이용한다. 이 명령어를 위 예제에 적용해보면 다음과 같다. 

![사진 1-3.](/images/2024-03-14/2024-03-14-java-package-and-classpath-3.png)

사진 1-3.

경로는 상대 경로 또는 절대 경로 어느 것을 해도 상관없다. 명령 프롬프트에 클래스 파일들이 존재하는 위치를 모두 알려주니 자바 가상 머신이 이를 인식하여 실행시킬 수 있었다. 

그런데, 이 방법은 매번 실행할 때마다 클래스 경로를 알려줘야 해서 매우 번거롭다. 이에 대한 대안으로, 패키지 내 각 자바 파일 내에 자신의 패키지명을 입력해놓는 방법이 있다.

# 패키지명을 입력하여 인식하게 하기

다음과 같은 프로젝트 내 패키지 구조가 있다. 

![사진 2-1](/images/2024-03-14/2024-03-14-java-package-and-classpath-4.png)

사진 2-1

각각의 파일 내 코드는 다음과 같다. 

```java
package sublib.mathtools.add;

public class CalcTotal {
    /**
     * 1 부터 n까지의 모든 자연수를 더한 값을 반환.
     * @param n
     * @return int
     */
    public int getTotal(int n) {
        int total = 0;
        for(int i = 1; i <= n; i++) {
            total += i;
        }
        return total;
    }
}

```
sublib.mathtools.add.CalcTotal.java

```java
package sublib.mathtools.product;

public class CalcTotal {
    public int factorial(int n) {
        if (n == 1) return 1;
        return n * factorial(n-1);
    }
}
```
sublib.mathtools.product.CalcTotal.java

```java
public class CalcTotal {
    public int getIntAdd(int a, int b) {
        int result = a + b;
        return result;
    }
}

```
CalcTotal.java

```java
public class MyApp {
    public static void main(String[] args) {
        CalcTotal ct = new CalcTotal();
        sublib.mathtools.add.CalcTotal oneToN = new sublib.mathtools.add.CalcTotal();
        sublib.mathtools.product.CalcTotal fact = new sublib.mathtools.product.CalcTotal();

        System.out.println("두 피연산자 간 덧셈 결과 : " + ct.getIntAdd(3, 2));
        System.out.println("10까지의 모든 자연수 덧셈 결과 : " + oneToN.getTotal(10));
        System.out.println("5! : " + fact.factorial(5));
    }
}

```
MyApp.java

src 폴더를 편의상 루트 폴더 또는 최상위 폴더라고 하겠다. 

위 MyApp.java 실행 시 앞서 소개한 클래스 패스 입력 방식으로 실행하지 않았다. 그럼에도 해당 파일이 문제 없이 실행될 수 있었던 것은, 각 패키지 내 파일 최상단에 자신의 패키지명을 입력해두었기 때문이다. 현재 패키지 내 파일이 든 폴더 경로명을 package 키워드 뒤에 입력한다. 이 때 각 폴더들의 구분은 마침표(.)로 한다. 

```java
package sublib.mathtools.product;
```

위 코드의 경우, sublib > mathtools > product 폴더 내 해당 자바 파일이 존재하는 상태임을 알 수 있다. 

위와 같이 각각의 패키지 내 파일 상단에 패키지명을 입력하면 이 패키지들을 이용하는 main 메서드가 존재하는 최상단 파일에서 문제없이 실행할 수 있다. 

단, main 메서드 내에서 패키지들을 이용할 때, 해당 패키지명을 입력해야한다. 위 MyApp.java 코드를 보면 이를 알 수 있다. 

패키지를 이용하는 파일이나 또는 루트 폴더 바로 아래에 있는 파일에는 자신의 패키지명을 명시하지 않는다. 위 예제의 MyApp, 그리고 src 폴더 바로 아래에 있던 CalcTotal 코드를 보면 상단에 package 키워드로 시작되는 패키지명이 없는 것을 볼 수 있다. 

패키지 내 파일들이 최상단 폴더 내 main 메서드가 있는 파일 내에서 실행되려면 패키지 내 파일 내 클래스, 메서드들은 모두 public으로 정의해야 한다. 그래야 다른 폴더에 있음에도 main 메서드가 포함된 자바 파일이 정상적으로 해당 패키지들을 불러와 실행할 수 있다. (물론 그 중에, main 메서드가 있는 파일에서의 접근을 방지하고 싶은 일부 클래스 및 메서드들은 굳이 public으로 지정할 필요는 없다)

이렇게 패키지명을 명시하는 방식의 장점은 실행 시 일일이 모든 클래스 파일들이 존재하는 경로들을 입력할 필요가 없어 상대적으로 실행이 간단할 뿐만 아니라, 서로 다른 패키지 내에 클래스명이 서로 중복되더라도 어차피 패키지명으로 구분하기 때문에 클래스명이 중복되어도 문제없이 사용할 수 있다는 것이다. 위 예제에서, CalcTotal이라는 클래스명이 여러 패키지에서 중복되는데도 불구하고, 실행해보면 문제없이 작동하는 것을 통해 확인할 수 있다. 

# Import

만약 프로젝트 내에 중복되는 클래스명이 없다면 앞서 패키지 파일마다 일일이 패키지명을 package 키워드와 함께 명시하는 방법보다 조금 더 간편한 방법이 있다. 바로 패키지들을 불러와서 이를 호출, 실행시키는 파일의 최상단에 import 키워드와 함께 해당 패키지명.클래스명을 코드로 작성하는 방법이다. 

다음의 프로젝트 폴더가 있다. 

![사진 3-1](/images/2024-03-14/2024-03-14-java-package-and-classpath-5.png)

사진 3-1

각각의 파일 내 코드는 다음과 같다.

```java
public class SubTool {
    public void printHello() {
        System.out.println("Hello, world!");
    }
}

```
sub.SubTool.java

```java
import sub.SubTool;

public class MainWithImport {
    public static void main(String[] args) {
        SubTool st = new SubTool();
        st.printHello();
    }
}

```
MainWithImport.java

다른 패키지를 불러와 사용하는 자바 코드 최상단에 import 키워드와 함께 그 뒤에 사용하고자 하는 패키지의 패키지명과 클래스명을 마침표(.)로 구분하여 작성하면 된다. 그리고 해당 클래스를 사용할 때에는 패키지명은 빼고 그저 클래스명만 사용하면 된다. 

위 방법은 앞서 패키지명 명시 방법과 달리, 패키지들을 불러와 사용할 코드 내부에서만 패키지명들을 import 시키고, 클래스명만 명시하여 사용하면 되기에 상대적으로 더 간편한 방법이다. 

하지만 위 코드를 보면 알겠지만, 만약 단 하나라도 패키지 파일 간에 중복되는 클래스명이 존재한다면 import 방식으로 사용하기 어려움을 알 수 있다. 패키지들을 불러와 사용하는 코드 내부에서는 패키지명은 사용하지 않고 클래스명만 사용하기 때문이다. 

이렇게 상황에 따라 패키지명 방식, import 방식 중 더 적절한 방법을 골라 사용하면 된다. 

# 패키지명과 클래스명

앞선 예제들을 보며 이미 눈치챘겠지만, 클래스명을 지을 때 맨 첫글자는 반드시 대문자로 해야 해서, 패키지명을 지을 때도 똑같이 첫 글자를 대문자로 하면 클래스와 패키지 간 구분이 가질 않는다. 따라서 패키지명을 지을 때는 첫 글자는 소문자로 시작하는 것이 좋다. 

# 자바 기본 제공 패키지 및 클래스

위 내용들을 숙지하였다면, 이미 자바에서 기본으로 제공하는 패키지나 클래스를 사용해왔다는 것을 알 수 있다. 

먼저 import 키워드를 사용하여 불러와서 사용할 수 있는 자바 기본 클래스들은 다음과 같이 존재한다. 

- java.util.Scanner
- java.util.Random
- java.lang.System

java.lang, java.util 모두 첫 글자가 소문자이므로, 패키지명, 즉 폴더임을 알 수 있다. 반면 맨 뒤에 오는 Scanner, Random 등은 모두 첫 글자가 대문자이므로 클래스명임을 알 수 있다. 

이 중, java.lang.System은 사실 System.out.println() 와 같은 메서드를 사용할 때 쓰이는 클래스이다. 자바 기본 제공 클래스 중에는 이렇게 import 키워드를 사용하지 않더라도 바로 클래스를 사용할 수 있도록 하는 패키지들도 일부 존재한다. 

자바에서 제공하는 기본 클래스 소스 코드는 jdk 설치 폴더 내 lib > src.zip 파일을 해제하면 볼 수 있다.

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)