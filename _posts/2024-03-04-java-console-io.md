---
title: "[Java] 콘솔 입출력"
category: "Java"
tag: ["java", "자바", "콘솔"]
---

# 콘솔 출력

콘솔(console)은 컴퓨터에서 데이터를 입출력하는 장치를 아우르는 용어이다. 콘솔창은 CLI 기반의 “명령 프롬프트 창”으로, 그래픽으로 사용자에게 직관적으로 데이터를 보여주는 GUI와 달리, 텍스트로만 데이터를 표시, 입력할 수 있다. 

자바에서 콘솔창을 통해 텍스트를 출력하는 메서드로 다음이 존재한다. 

- System.out.println() : 괄호 안에 입력된 데이터를 콘솔창에 출력한 뒤 자동 개행.
- System.out.print() : 괄호 안에 입력된 데이터를 콘솔창에 출력한다. 출력 후 자동 개행은 이뤄지지 않는다.
- System.out.printf() : 문자열과 데이터를 섞어서 출력할 때 쓰인다.

먼저, 위 세 메서드를 이용한 예제 코드를 보겠다.

```java
public class Prints {
    public static void main(String[] args){
        char alp = 'A';
        String food = "banana";
        int year = 2024;
        double pi = 3.141592;

        System.out.print("nice to meet you.");
        System.out.println("Hello, World!");
        System.out.printf("%c %s %d %f %e %.2f", alp, food, year, pi, pi, pi);
    }
}

```

```java
nice to meet you.Hello, World!
A banana 2024 3.141592 3.141592e+00 3.14
```

위 예제를 보면, print() 메서드를 사용하여 문자열을 출력하면 그 뒤에 자동으로 개행되지 않아 println() 메서드가 출력하는 텍스트와 한 줄로 겹치는 것을 알 수 있다. println() 메서드는 출력 후 자동으로 개행을 하기에 printf() 메서드로 출력된 메시지가 그 다음 줄에 표시되는 것이다. 

printf() 메서드의 경우, 첫 번째 인자는 포맷 문자열을 입력한다. 문자열 안에 %c, %d와 같은 것들이 서식 지정자이다. 이 서식 지정자들은 포맷 문자열 뒤에 오는 쉼표로 구분되는 데이터들을 왼쪽부터 차례대로 가져와 출력시키는 역할을 가진다. 위 예제의 경우, 두 번째 인자부터 맨 앞에 오는 alp 변수의 값이 포맷 문자열 내의 맨 앞에 오는 서식 지정자 %c에 대입되고, 세 번째 인자인 food 변수의 값이 그 다음 서식 지정자인 %s에 대입되는 방식이다. 

서식 지정자에는 다음이 존재한다. 

| 서식 지정자 | 출력 |
| --- | --- |
| %d | 10진수 정수 |
| %o | 8진수 정수 |
| %x | 16진수 정수 |
| %f | 실수 |
| %e | e 표기법으로 실수 출력. 지수 표기법을 e 문자를 통해 표기하는 방식. |
| %g | 출력 대상에 따라 %e 또는 %f 적용 |
| %s | 문자열 |
| %c | 문자 |

참고로 위 예제의 printf 메서드 내 포맷 문자열을 보면 알겠지만, %f의 경우, %.2f와 같이 마침표와 숫자를 추가로 기입하여 소수점 몇 번째 자리까지만 출력할 것인지를 결정할 수 있다. 

# 콘솔 입력 : Scanner 클래스

콘솔창에 데이터를 입력하고자 하는 경우, Scanner 클래스를 이용하면 된다. 먼저, Scanner 클래스를 사용하려면 코드 맨 위 (public class를 벗어난 더 위에)에 해당 클래스를 import 해야 한다. 

```java
import java.util.Scanner;  // Scanner 클래스 임포트

public class App {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 생략
    }
}
```

그 후, main 메서드 내부에서, Scanner 자료형의 변수를 선언한다. 그리고 그 변수에는 new 키워드와 그 뒤에 Scanner() 클래스명을 적고, 괄호 안에 System.in이라 적으면 된다. 그러면 Scanner 인스턴스를 생성하여 scan 변수에 할당하게 되는 것이다. 그 이후로 콘솔창 입력은 이 객체를 이용하면 된다. 

다음은 Scanner 클래스를 이용하여 사용자로부터 두 개의 숫자를 입력 받고 이를 그대로 출력하는 예제이다. 

```java
import java.util.Scanner;

public class UserInput {
    public static void main(String[] args) {
        Scanner userInput = new Scanner(System.in);  // Scanner 객체 생성.

        // 한 줄씩 나눠서 입력해도 되고, 한 줄에 공백 또는 탭으로 구분하여 작성해도 됨.
        // 입력 예1)
        // 1
        // 2
        // 입력 예2)
        // 1 2
        System.out.println("정수를 총 두 번 입력하고, 각각 엔터키를 입력.");

        int a = userInput.nextInt();  // 숫자 입력을 받음.
        int b = userInput.nextInt();

        System.out.printf("입력한 숫자들: %d %d", a, b);
    }
}

```

```java
정수를 총 두 번 입력하고, 각각 엔터키를 입력.
1 2
입력한 숫자들: 1 2
```

```java
정수를 총 두 번 입력하고, 각각 엔터키를 입력.
1
2
입력한 숫자들: 1 2
```

위 예제에서는 숫자 데이터를 입력받기 위해 Scanner 객체의 nextInt() 메서드를 사용하였다. 해당 메서드의 특징은 한 줄에 거쳐서 여러 개의 데이터를 입력하든, 한 번에 한 개의 데이터씩만 입력하든 모두 입력값을 받는 각각의 변수에 하나씩 대입된다는 것이다. 실행결과1에서는 두 개의 정수를 공백을 기준으로 나눠 한 줄에 한꺼번에 입력하였다. 그럼에도 두 숫자 데이터는 각각 변수 a = 1, b = 2에 할당된 것이다. 실행결과2처럼 한 줄에 하나의 데이터만 입력해도 똑같은 결과를 얻을 수 있다. 

한 줄에 여러 개의 데이터를 입력하는 경우, 공백 또는 탭으로 데이터들을 구분하므로 이에 유의한다. 

위 예제를 실행하면 알 수 있는 또 다른 사실은, 사용자가 데이터를 입력할 때까지 프로그램 실행이 일시중단된다는 것이다. 

nextInt 말고도 next()와 nextLine() 메서드도 있다. 둘 다 입력받은 데이터들을 문자열로 반환한다. 다만, next() 메서드는 사용자가 여러 개의 데이터를 한 줄로 입력해도 공백으로 구분한다면 여러 개의 데이터로 구분하여 변수에 대입할 수 있지만, nextLine() 메서드는 한 줄에 입력된 텍스트 자체를 하나의 데이터로 인식하여 반환한다는 것이 차이점이다. 정확히 말하자면, nextLine() 메서드는 사용자가 엔터키를 누르기 전의 모든 문자열을 하나의 데이터로 인식한다. 즉, nextLine() 메서드를 이용하여 사용자로부터 데이터를 입력받을 때, 아무리 공백 또는 탭으로 데이터를 구분해도, 그 데이터들을 한 줄에 작성하면 그 한 줄 자체가 한 개의 데이터로 인식되는 것이다. 

```java
import java.util.Scanner;

public class UserInput2 {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);

        System.out.println("아무거나 입력한 후 엔터키를 입력.");
        String userInput = scan.nextLine();

        System.out.println("3개의 단어를 공백으로 구분하여 입력.");
        String info1 = scan.next();
        String info2 = scan.next();
        String info3 = scan.next();

        System.out.println("출력 결과");
        System.out.println(userInput);
        System.out.println(info1);
        System.out.println(info2);
        System.out.println(info3);
    }
}

```

```java
아무거나 입력한 후 엔터키를 입력.
hello good wow
3개의 단어를 공백으로 구분하여 입력.
hello good wow
출력 결과
hello good wow
hello
good
wow
```

hello, good, wow라는 세 문자열을 공백으로 구분하여 3개의 데이터를 입력한다는 의도였지만, nextLine() 메서드가 사용될 때는 “hello”, “good”, “wow”처럼 개별적인 3개의 데이터가 아닌 “hello good wow”라는 하나의 데이터로 인식되어 처리되었다.

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)