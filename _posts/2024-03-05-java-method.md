---
title: "[Java] Method"
category: "Java"
tag: ["java", "자바", "메서드", "method"]
---

본래, 프로그래밍 언어에서 특정 기능을 수행하는 코드 block을 하나로 묶어 정의하고 사용하는 “함수”가 존재한다. 그리고 만약 이 함수가 클래스 내부에 정의되어 있으면 해당 함수를 메서드(method)라 부른다. 자바에서도 물론 메서드가 존재하며, 클래스 내부에 정의된 점이라는 것만 빼면 함수와 별반 다를 바가 없다. 다만, 자바는 특이하게도 자바 코드를 실행하기 위해선 클래스를 먼저 정의한 다음, 그 클래스 안에 main 메서드를 정의하고, 그 안에 실행시킬 코드를 작성해야 실행이 되는 구조이다(이는 지난 자바 문서들의 예제를 보면 공통적으로 나타나는 특징임을 알 수 있다). 즉, 큰 틀의 클래스와 그 내부의 main 메서드 외부에 코드를 작성하면 실행이 되지 않을 수도 있다. 그러다보니 사용자가 정의할 모든 함수가 해당 클래스 내부에 정의되는 형태이므로 그 함수들은 모두 “메서드”가 되는 것이다. 따라서 자바에서는 “함수”라는 용어가 없다. 

# 메서드 정의

메서드의 구조는 다음과 같다. 

```java
반환형 메서드명 (매개변수) {
    실행할 코드.
    return 반환값;
}
```

예를 들어, 정수 n을 받을 때 1부터 n까지의 모든 자연수들의 합을 구하고 이를 반환하는 메서드는 다음과 같이 정의할 수 있다. 

```java
int getTotal(int n) {
    int total = 0;

    for (int i = 1; i <= n; i++) {
        total += i;
    }
        
    return total;
}
```

매개변수는 여러 개 설정할 수도 있고, 아예 없을 수도 있다. 단, 메서드 호출 시 매개변수 자리에 대입할 인자의 수가 매개변수의 수와 일치해야 한다. 

메서드명 앞에 반환값의 자료형을 명시한다. 만약 반환값이 없을 경우 void 키워드를 작성하면 된다. 

메서드의 return 키워드 뒤에 작성할 값의 자료형과 메서드명 앞에 적은 자료형이 일치해야 한다. 

메서드명은 첫 글자는 소문자로 시작하는 카멜 표기법을 따른다. 

다음은 위 예제 1-1에서 정의한 메서드를 가지는 전체 코드이다. 

```java
public class MethodDef {
    public static void main(String[] args) {
        int num = 10;
        int total = getTotal(num);

        System.out.println(total);
    }

    public static int getTotal(int n) {
        int total = 0;

        for (int i = 1; i <= n; i++) {
            total += i;
        }
        
        return total;
    }
}

```

```java
55
```

위 예제를 통해 알 수 있는 것은, 여태까지 봐왔던 자바 코드에서의 main 또한 메서드라는 것이다. 

그런데 위 예제에서의 getTotal 메서드는 앞서 본 에제 1-1의 getTotal 메서드와 차이점이 보인다. 바로 앞에 public static이라는 표현이 더 붙어있다는 것이다. 그 중 static은 사실, 해당 메서드를 호출할 메서드에 static이 붙어있으면 호출될 메서드도 static을 붙여야 한다는 규칙 때문이다. 위 예제에서 main 메서드에 static이 붙어있으니, main 메서드 내부에서 호출할 getTotal 메서드 역시 앞에 static 키워드가 작성되어 있어야 한다. 

메서드 내에서 코드를 실행하다가 return 키워드를 만나면 메서드는 곧장 반환값이 있으면 반환하고 메서드 실행을 종료한다. 따라서 이를 적절히 잘 활용하면 반복문 내부에 특정 조건을 만족시키면 해당 반복문을 빠져나가도록 하기 위해 break문을 활용하는 것처럼, 특정 조건을 만족시키면 해당 메서드를 즉시 중단시키게끔 하는 용도로 사용할 수 있다. 

# 변수의 scope

자바에서는 코드 영역을 중괄호({})로 구분할 수 있다. 그리고 각각의 영역에 존재하는 변수들은 사용될 수 있는 scope가 제한되어 있다. 

우선 다음의 예제를 보겠다. 

```java
public class VariableScope {
    // 1
    static String name = "가나다";
    static byte age = 22;

    public static void main(String[] args) {
        String bookName = "자바에서 살아남기";
        int bookSold = 300;
        boolean isGood = true;

        int n = 10;
        int total = 0;
        // int total = 111;

        {
            // 2
            System.out.println(name);
            System.out.println(age);
            System.out.println(bookName);
        }

        for(int i = 1; i <= n; i++) {
            // 3
            total += i;
        }
        System.out.println(total);

        if (isGood) {
            // 4
            // int bookSold = 301;  
            bookSold++;
            System.out.println(bookSold);
            // System.out.println(i);
        }

        {
            // 5
            int localNum = 11;
            System.out.println(localNum);
        }
        // System.out.println(++localNum);
        
    }
}

```

```java
가나다
22
자바에서 살아남기
55
301
11
```

위 예제를 토대로 변수의 scope에 대해 다음을 알 수 있다. 

- 같은 scope 내에서 같은 변수의 재선언은 할 수 없다. 위 예제의 int total = 0; 부분 밑의 주석을 해제하면 total이라는 변수가 두 번이나 중복되어 선언되는 셈인데, 이 경우 에러가 발생한다.
- 더 넓은 영역의 안 쪽에 있는 영역에서는 바로 바깥 영역에 선언된 변수를 재선언할 수 없다. 위 예제에서 main 메서드 내에 선언된 booksold 변수를 해당 메서드의 내부에 있는 if 문 내에서 재선언할 수는 없다. 다만, 해당 변수를 사용하는 것 자체는 문제가 없다.
- 더 넓은 영역 A 내부에 B라는 영역이 존재할 경우, 영역 B에서 영역 A의 변수를 참조할 수는 있지만, 반대로 영역 A에서 영역 B 내부에 선언된 변수를 참조할 수는 없다. 이는 영역 B 내부에 선언된 변수는 해당 영역(B) 내에서만 사용할 수 있기 때문이다. 위 예제를 보면, 맨 마지막에 localNum 변수가 선언된 부분을 보면 중괄호 안에 선언되었다. 따라서 그 아래에 있는 주석을 해제할 경우 에러가 발생한다. 
if 무

참고로 위 예제에서처럼 이름없이 중괄호만 사용하여 영역을 지정하는 것도 가능하다. 

한 편, 위 예제에서, main 메서드 바깥과 VariableScope 클래스 내부에 선언된 변수 name, age는 해당 클래스 영역에서 선언된 변수라 하여 클래스 변수라 하고, 멤버 변수라고도 불린다. 그리고, 클래스의 관점에서 클래스 변수는 필드라고도 한다. 클래스에서는 이러한 필드와 메서드 이 두 가지가 모여 클래스의 “멤버”를 이룬다. 위 예제에서는 main 메서드가 static으로 정의되어 있기에, 해당 메서드 내부에서 클래스 변수를 사용하고자 한다면 해당 클래스 변수들도 static으로 정의되어 있어야 한다. 즉, 클래스 내부에 static으로 정의된 메서드 내부에서 해당 클래스 내부의 다른 메서드나 클래스 변수들을 참조 또는 호출하고자 한다면, 해당 클래스 멤버들 모두 static으로 정의, 선언되어야 한다.

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)