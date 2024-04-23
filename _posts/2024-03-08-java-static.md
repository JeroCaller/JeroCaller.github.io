---
title: "[Java] Static"
category: "Java"
tag: ["java", "자바", "static"]
---

# Static 변수는 전역 변수

이전에 [자바의 메모리 모델](/java/java-memory-model/)을 살펴보았을 때, static으로 선언된 변수 및 메서드는 프로그램이 실행되기 전에 메서드 영역 내 static 영역에 미리 로드된다고 하였다. 그리고 static 영역은 프로그램 실행 내내 그 어떤 객체에서도 접근 가능하여 static 변수의 경우 전역 변수로서 사용할 수 있다고 하였다. 이를 예제 코드를 통해 확인해보자. 

```java
class Consumer {
    static int bankMoney = 1000;
    int myMoney = 0;
    String name = "";

    Consumer(String name, int myMoney) {
        this.name = name;
        this.myMoney = myMoney;
    }

    void printMoney() {
        System.out.printf("====== %s ======\n", this.name);
        System.out.println("내 머니 : " + myMoney);
        System.out.println("은행이 소유한 총 금액: " + bankMoney);
        System.out.println("============");
    }
}

public class GlobalVar {
    public static void main(String[] args) {
        Consumer person1 = new Consumer("냥이", 300);
        person1.bankMoney = 1500;
        person1.printMoney();

        Consumer person2 = new Consumer("멍이", 500);
        person2.bankMoney = 1200;
        person2.printMoney();
        person1.printMoney();
    }
}

```

```java
====== 냥이 ======
내 머니 : 300
은행이 소유한 총 금액: 1500
============
====== 멍이 ======
내 머니 : 500
은행이 소유한 총 금액: 1200
============
====== 냥이 ======
내 머니 : 300
은행이 소유한 총 금액: 1200
============
```

위 예제에서, Consumer 클래스 내부에 static으로 선언된 변수 bankMoney가 있다. 이후, GlobalVar 클래스의 main 메서드 내부에서 Consumer 객체 두 개를 생성한 후, 각각의 객체에서 bankMoney의 값을 다르게 할당하였다. 그 후 두 객체의 멤버 변수들을 출력하도록 하였다. 출력 결과를 보면, 인스턴스 변수인 myMoney는 두 객체가 서로 다른 값을 가지는 반면, bankMoney 변수값은 둘 다 똑같음을 알 수 있다. 인스턴스 변수는 힙 영역에 할당된 각 객체 내부에 존재한다. 따라서, person1 참조 변수가 가리키는 객체와 person2 참조 변수가 가리키는 객체가 서로 다르기에, person1의 인스턴스 변수 myMoney와 person2의 인스턴스 변수 myMoney는 그 변수명만 같을 뿐 서로 다른 곳에 있는 변수이다. A라는 사람과 B라는 사람 모두 “주민등록증”은 똑같이 가지고 있으나 각자의 주민등록번호는 다른 것과 똑같은 이치이다. 반면, static 변수는 메서드 영역 내부의 static 영역에 있어 어디서든 접근 가능하므로, 위 예제에서 person1을 통해 접근하든 person2를 통해 접근하든 모두 똑같은 하나의 bankMoney 변수에 접근하는 것이다. 이러한 사실을 통해, static 변수는 전역 변수임을 알 수 있다. 

# Preload of Static : 정말로 프로그램 실행 전에 미리 로드되는 걸까?

이전에 [자바의 메모리 모델](/java/java-memory-model/) 을 살펴볼 때 static 영역 내에 저장될 static 변수, 메서드들은 프로그램 실행 전에 미리 로드된다고 설명하였다. 정말로 프로그램 실행 전에 미리 로드되는 것일까? 다음의 예제를 통해 알아보자.

```java
public class StaticPreload {
    static int number;

    // 이름없는 블록 영역에도 static을 붙일 수 있다.
    static {
        number = 12;
    }
    
    public static void main(String[] args) {
        number += 1;
        System.out.println(number);
    }
}

```

```java
13
```

위 예제를 보면, 분명 main 메서드에서는 number라는 변수를 선언하거나 값을 할당하지 않았음에도 바로 그 값에 접근하여 값을 변경하거나 출력할 수 있음을 알 수 있다. 이는 두 가지 사실을 암시한다. 

- static 변수는 프로그램 실행 전에 이미 값의 할당 작업이 행해졌다. 그렇기에 main 메서드에서 해당 변수를 선언하고 해당 변수에 값을 할당하지 않았음에도 에러 없이 정상적으로 동작하는 것이다.
- 같은 static이라도 메서드는 코드 상에서 method()와 같이 직접 호출하는 코드를 만나야 실행되므로, static 변수 및 블록 영역의 실행이 static 메서드보다 더 먼저 메모리에 로드되어 실행될 수 있다.

즉, static 변수의 값 할당 작업은 static 메서드인 main 메서드의 실행보다도 더 먼저 실행되었음을 알 수 있다. 

위 예제를 통해 static 변수, 영역, 메서드는 프로그램 실행 전에 미리 작업이 실행된 상태로 메모리에 적재되고, 특히 그 중 메서드보다 변수, 영역이 더 먼저 로드될 수 있음을 알 수 있다. 

# static의 활용

static을 이용하면 다른 패키지 또는 같은 패키지 내 여러 파일에서 자주 사용되는 기능을 static 메서드로 정의하여 하나의 클래스에 모아둔 유틸리티 클래스(utility class)를 만들 수 있다. 만약 다른 패키지에서 자주 사용하는 기능을 일반 클래스 내부에 정의하여 만든다면, 이 기능을 사용하는 파일마다 해당 기능을 사용하기 위해선 무조건 객체를 생성해야한다. 이는 해당 기능을 사용하는 곳이 많아질수록 힙 영역의 메모리를 더 많이 차지하게 된다는 점이 있다. 하지만 static 메서드를 이용한 유틸리티 클래스로 자주 사용하는 기능들을 모아두면, 메모리 상에서는 static 영역에 단 한 번만 로드되고, 다른 파일들에서 이 기능을 사용할 때 static 영역의 해당 메서드만 가져와서 사용하면 되기에 메모리 사용을 최소화할 수 있다는 장점이 있다. 

다음은 로또 숫자 6개를 무작위로 생성하는 기능이 다른 파일에서 자주 사용될 것이라 가정하여 유틸리티 클래스로 정의한 다음, 같은 패키지의 다른 파일에서 이를 두 번 사용하는 예제이다. 먼저 유틸리티 클래스를 정의한 파일이다. 

```java
import java.util.Random;
import java.util.ArrayList;
import java.util.Comparator;  // 리스트 정렬에 쓰임.

public class LottoGenerator {
    public static ArrayList getSixLotto() {
        ArrayList lottoNumbers = new ArrayList();
        ArrayList AllNumbers = new ArrayList();
        Random rand = new Random();

        // 1부터 45까지의 자연수들을 AllNumbers 변수에 초기화.
        for(int i = 1; i < 46; i++) {
            AllNumbers.add(i);
        }

        // 무작위로 중복되지 않는 숫자 6개를 뽑음.
        for(int i = 0; i < 6; i++) {
            int AllNumIdx = rand.nextInt(0, AllNumbers.size());
            lottoNumbers.add(AllNumbers.remove(AllNumIdx));
        }

        lottoNumbers.sort(Comparator.naturalOrder());
        return lottoNumbers;
    }
}

```

다음 코드는 위 코드를 이용하는 자바 파일 내 코드이다. 

```java
import java.util.ArrayList;

public class LottoExample {
    public static void main(String[] args) {
        LottoGenerator lotge = new LottoGenerator();
        ArrayList lastWeek = lotge.getSixLotto();
        System.out.println(lastWeek);

        ArrayList thisWeek = LottoGenerator.getSixLotto();
        System.out.println(thisWeek);
    }
}

```

```java
[17, 18, 19, 33, 40, 42]
[14, 18, 19, 22, 24, 40]
```

위 예제 3-2의 main 메서드 내부를 보면, LottoGenerator 클래스의 getSixLotto메서드를 사용하고, 그 반환값을 저장하기 위해 각각 lastWeek, thisWeek 변수가 사용되었다. lastWeek 변수의 경우, 먼저 LottoGenerator 클래스를 인스턴스화 한 다음에 해당 객체의 getSixLotto() 메서드를 호출하여 사용하는 방식을 취하고 있다. 반면 thisWeek 변수에는 클래스명.메서드명과 같이 인스턴스화하지 않고 바로 클래스에 접근하여 해당 메서드를 사용하고 있다. 사실, 해당 메서드는 static으로 정의되어 있어 프로그램 실행 이전에 static 메모리 영역에 미리 로딩된다. 그렇기에 굳이 해당 클래스를 객체로 생성하여 사용할 필요가 없다. 그렇기에 thisWeek와 같이 객체 생성 없이 클래스명으로 직접 접근하여 사용하면 된다. 

위와 같이 유틸리티 클래스는 굳이 객체화하여 사용할 필요가 없으므로, 혹시나 사용자가 실수로 인스턴스화하는 것을 방지하기 위해 유틸리티 클래스 내부에서는 private 생성자를 정의하기도 한다. 앞선 예제 3-1의 LottoGenerator 클래스 내부에 `private LottoGenerator() {}` 코드를 삽입하면 에제 3-2 실행 시 `LottoGenerator lotge = new LottoGenerator();` 부분에서 에러가 발생한다. LottoGenerator 클래스 외부에서 해당 클래스 생성자에 접근하는 꼴이 되었기 때문이다. 

이제 자바에서 코드를 읽을 때, 클래스명.메서드명() 과 같이 해당 클래스를 객체화하지 않고 바로 메서드에 접근하여 사용하는 방식을 보면 해당 메서드는 static으로 정의되었음을 추측할 수 있다. 그리고 해당 클래스에 이러한 방식으로 사용하는 메서드들이 많이 있다면 해당 클래스는 유틸리티 클래스일 가능성이 높다. 

# static 변수의 주의점

앞서 static 변수는 전역 변수처럼 사용할 수 있음을 확인하였다. 그런데 사실, 다른 프로그래밍 언어에서도 많은 사람들이 되도록 전역 변수는 사용하지 않는 게 좋다고 조언한다. 여기서도 마찬가지이다. 우선, 전역 변수가 그 어떤 곳에서도 접근하여 사용할 수 있다는 점에서, 의도치 않은 곳에서 전역 변수의 값이 변형되어 버그가 발생할 수 있다. 이러한 이유로, 함수를 작성할 때에도 되도록 외부 변수와 독립적으로 실행되게끔 작성하도록 권유한다. 이는 객체지향 프로그래밍에서도 비슷한데, 객체 내부의 데이터를 외부에서 접근하여 수정하거나 반대로 외부 변수를 객체 내부에서 조작하지 않도록 독립성을 유지해야 의도치 않은 버그, 에러를 막을 수 있다. 그런데 객체 내부의 멤버 변수를 static으로 지정한다면? 객체 내부의 데이터를 외부에서 마음대로 조작할 수 있게 되는 셈이다. 이는 앞서 [객체와 클래스](/java/java-object-and-class/) 에서 살펴본 객체 지향 프로그래밍의 특성 중 하나인 정보 은닉을 지키지 못한다. 

---

References

[1] [정적 유틸리티 클래스 (Static Utility Class)](https://be-study-record.tistory.com/24)

[2] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)