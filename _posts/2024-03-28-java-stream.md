---
title: "[Java] 스트림 (Stream)"
category: "Java"
tag: ["java", "자바", "스트림", "stream"]
---

# 스트림 (Stream)

스트림은 연속적이고 여러 개인 데이터의 흐름을 어떠한 연산을 통해 반복적으로 처리하는 기능을 말한다. 마치 공장의 컨베이어 벨트 위에 놓인 여러 개의 제품들이 일렬로 한 방향으로 움직이며 기계 또는 작업자들이 해당 부품들에 대해 어떠한 작업을 하는 과정과 같다고 생각할 수 있겠다. 그리고 스트림에서 진행되는 연산을 스트림 연산이라 한다. 스트림에 들어갈 데이터 소스는 그 데이터가 여러 개여야 하기에 컬렉션이나 배열 등이 사용된다. 

스트림에 들어오는 데이터 소스는 그 원본 데이터와 구분되기에, 스트림 연산을 진행해도 소스만 바뀔 뿐, 원본 데이터에는 영향을 끼치지 않는다. 또한, 스트림 연산은 중간 연산과 최종 연산으로 나뉜다. 중간 연산은 여러 번 할 수 있지만, 최종 연산은 딱 한 번 할 수 있고, 최종 연산이 끝나면 스트림은 종료된다. 스트림은 재사용이 불가능하다. 

자바에서 스트림은 java.util.stream 패키지 멤버이다. 또한, 상위 BaseStream 인터페이스와 이를 구현하는 Stream, IntStream, LongStream, DoubleStream이 존재한다. 여기서는 주로 Stream과 IntStream에 대해 다뤄보겠다. 

## 스트림 생성

자바에서, 스트림에 넣을 데이터가 기본 자료형의 값들이 담긴 배열인지, 아니면 객체들이 담긴 컬렉션인지에 따라 스트림 생성법이 약간 다르다. 다음은 스트림을 생성하고, 스트림 내 데이터들을 정렬한 뒤에 출력하는 예제이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class User10 {
    String nickName;
    int point;

    User10(String nickName, int point) {
        this.nickName = nickName;
        this.point = point;
    }

    @Override
    public String toString() {
        return this.nickName + " : " + this.point;
    }
}

class PrimitiveAndCollection {
    public static void main(String[] args) {
        int[] numArray = {5, 2, 52, 12, 7}; 
        List<User10> userList = new ArrayList<>(Arrays.asList(
            new User10("bowWow110", 1000),
            new User10("goodAsWell99", 600),
            new User10("dogcat33", 2000)
        ));

        // 기본 자료형의 배열을 소스로 하는 스트림 생성법.
        Arrays.stream(numArray)
              .sorted()
              .forEach(n -> System.out.print(n + ", "));
        System.out.println();

        // 객체 데이터들이 담긴 컬렉션 자료구조를 소스로 하는 스트림 생성법.
        userList.stream()
                .sorted()
                .forEach(n -> System.out.print(n + ", "));
        System.out.println();
    }
}

```

```java
2, 5, 7, 12, 52, 
(goodAsWell99 : 600), (bowWow110 : 1000), (dogcat33 : 2000),
```

기본 자료형 값들이 담긴 배열을 토대로 스트림을 생성하고자 하는 경우, `Arrays.stream()` 메서드에 배열 인자를 대입하면 된다. 반면, 컬렉션 객체를 토대로 스트림을 생성하고자 하는 경우, 컬렉션 객체의 stream() 메서드를 호출하여 사용하면 된다. 

`Arrays.stream()` 메서드에 int형 배열을 대입하면, 해당 메서드는 IntStream 객체를 반환한다. 반면, 컬렉션의 stream() 메서드에서는 컬렉션 내 객체 데이터를 담은 Stream<T> 객체가 반환된다. 이 두 스트림은 둘 중 특정 스트림에서만 사용할 수 있는 메서드가 있고, 둘 모두 존재하는 메서드이지만 인자를 받는 스트림도 있고, 받지 않는 스트림도 있다. 예를 들어, sum() 메서드는 IntStream에만 있고, Stream에는 없다. 한 편 sorted() 메서드는 두 스트림 모두 존재하나 Stream의 sorted() 메서드에는 정렬 기준을 담은 람다식을 대입할 수 있으나, IntStream의 sorted() 메서드에는 인자를 대입하면 안된다. 이처럼 두 스트림에는 미묘한 차이점이 있으니 헷갈리지 않도록 주의해야 한다. 

## 스트림 연산

앞서 언급했듯, 스트림 연산에는 중간 연산과 최종 연산이 존재한다. 

- 중간 연산 : 스트림을 통해 데이터가 흐를 때, 중간 과정에서 행하는 연산. 중간 연산을 할 수 있는 메서드에는 다음이 존재한다.
    - filter() : 여러 요소들 중 특정 조건에 맞는 요소만을 반환하도록 하는 람다식을 대입할 경우 그에 맞게 해당 조건을 만족하는 요소만을 추출한다.
    - map() : 람다식을 인자로 대입할 경우, 람다식의 코드에 맞게 기존 요소들을 변환시킨다.
    - sorted() : 요소 정렬.
- 최종 연산 : 스트림 내 데이터들을 소모하면서 연산을 수행한다. 최종 연산 후에는 해당 스트림에 다른 연산을 적용할 수 없으며, 최종 연산 후에 해당 스트림이 종료된다.
    - forEach() : 요소를 하나씩 꺼낸다.
    - count() : 요소 개수
    - sum() : 요소의 합

다음은 스트림 중간 연산과 최종 연산을 사용해보는 예제이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.IntStream;

class StreamEx {
    public static void printIntArr(int[] arr) {
        for(int element : arr) {
            System.out.print(element + ", ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int[] numArr = {1, 2, 3, 4, 5};

        // 스트림 생성.
        IntStream stream1 = Arrays.stream(numArr);

        // 중간 연산.
        // 소수 판별기
        IntStream stream2 = stream1.filter(n -> {
            if (n == 1) {
                return false;
            }
            for(int i = 2; i < n; i++) {
                if(n % i == 0) {
                    return false;
                }
            }
            return true;
        }); 

        // 최종 연산
        int totalSum = stream2.sum();
        System.out.println(totalSum);

        // 최종 연산2. 필터링하고 나온 최종 요소들을 출력.
        // 최종 연산은 단 한 번만 가능하므로, sum()과 같이 사용하려고 하면 
        // 예외가 발생함.
        // stream2.forEach(System.out::println);

        // 스트림에서 연산이 일어나도 원본 데이터에 영향을 끼치지 않는다. 
        printIntArr(numArr);
    }
}

```

```java
10
1, 2, 3, 4, 5,
```

## Pipeline

Stream 인터페이스에서의 메서드들은 대부분 Stream 타입의 값을 다시 반환한다. 이를 이용하면 Stream 메서드들을 연속적으로 호출하여 사용할 수 있다. 이를 파이프라인이라 한다. 다음은 스트림으로 파이프라인을 구성한 예제이다.

```java
import java.util.Arrays;

class PipeLine {
    public static void main(String[] args) {
        int[] numArr = {1, 2, 3, 4, 5};

        // 주어진 데이터들 중 소수만 감별하여 이 소수들의 합을 구함.
        int totalSum = Arrays.stream(numArr)
                    .filter(n -> {
                        if (n == 1) {
                            return false;
                        }
                        for(int i = 2; i < n; i++) {
                            if(n % i == 0) {
                                return false;
                            }
                        }
                        return true;
                    })
                    .sum();
        System.out.println(totalSum);
    }
}

```

```java
10
```

# 스트림 연산 메서드들

다음에 기술할 메서드들을 사용할 수 있는 스트림에는 Stream과 IntStream만을 기준으로 한다. 나머지 LongStream, DoubleStream 스트림에는 특정 메서드를 사용할 수 있는지, 또는 사용법이 어떻게 되는지는 확인하지 않음.

## sorted()

스트림 내 데이터들을 정렬하는 메서드이다. 

- 연산 종류 : 중간 연산
- 사용 가능한 스트림: Stream, IntStream 둘 다. 단, IntStream에서의 sorted()에는 아무런 인자를 대입할 수 없어 특정 정렬 기준을 사용할 수 없다.

다음은 해당 메서드의 사용 예제이다. 

```java
import java.util.Arrays;
import java.util.List;

class SortedEx {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(12, 3, 5, 2);

        // Collection 객체의 stream() 메서드에는 인자를 넣지 않는다.
        list.stream()
            .sorted((n1, n2) -> n2 - n1)  // 내림차순 정렬.
            .forEach(n -> System.out.print(n + ", "));
        System.out.println();

        int[] myNums = {12, 3, 5, 2};
        Arrays.stream(myNums)  // Arrays.stream()에는 데이터 배열을 인자로 대입해야함.
              .sorted()  // IntStream.sorted() 인자에는 따로 정렬 메서드를 넣을 수 없다.
              .forEach(n -> System.out.print(n + ", "));
        System.out.println();
    }
}

```

```java
12, 5, 3, 2, 
2, 3, 5, 12,
```

## map()

스트림 내 데이터들을 조건에 따라 변환시키는 연산을 하는 메서드.

- 연산 종류 : 중간 연산
- 사용 가능한 스트림 : IntStream, Stream 둘 모두.

```java
import java.util.Arrays;
import java.util.List;

class MapEx {
    public static void main(String[] args) {
        int[] myNums = {1, 5, 3, 12, 4};
        List<String> myStrs = Arrays.asList("java", "javascript", "python");

        myStrs.stream()
              .map(s -> s + "-" + s.length())
              .sorted((s1, s2) -> s2.length() - s1.length())
              .forEach(s -> System.out.print(s + ", "));
        System.out.println();

        Arrays.stream(myNums)
              .map(n -> n + 1)
              .sorted()
              .forEach(n -> System.out.print(n + ", "));
        System.out.println();
    }
}

```

```java
javascript-10, python-6, java-4, 
2, 4, 5, 6, 13,
```

## sum()

데이터가 모두 숫자일 경우, 모든 요소들의 합을 반환.

- 연산 종류: 최종 연산
- 사용 가능한 스트림: Stream 에서는 불가능

## count()

데이터의 총 개수를 반환.

- 연산 종류 : 최종 연산
- 사용 가능한 스트림 : IntStream, Stream 모두 가능

## average()

데이터들이 모두 숫자일 경우, 모든 데이터들의 평균을 반환.

- 연산 종류 : 최종 연산
- 사용 가능한 스트림 : Stream에서는 불가능.

## min(), max()

모든 데이터들 중 각각 최소값과 최대값을 반환.

- 연산 종류 : 최종 연산
- 사용 가능한 스트림 : IntStream, Stream 모두 가능. 단, Stream에서는 메서드 인자로 비교 기준이 될 Comparator 인터페이스를 구현하는 클래스 또는 람다식을 인자로 대입해야 한다.

다음은 sum(), count(), average(), min(), max() 모두 사용해본 예제이다.

```java
import java.util.Arrays;
import java.util.List;
import java.util.OptionalDouble;
import java.util.OptionalInt;

class StreamOperators {
    public static void main(String[] args) {
        int[] myNums = {5, 1, 2, 7, 6};
        List<Integer> myNumList = Arrays.asList(5, 1, 2, 7, 6);

        // === int형 배열에 대한 스트림
        int totalSum = Arrays.stream(myNums).sum();
        System.out.println("합 : " + totalSum);

        long countNums = Arrays.stream(myNums).count();
        System.out.println("개수 : " + countNums);

        OptionalDouble aver = Arrays.stream(myNums).average();
        System.out.println("평균 : " + aver);

        OptionalInt myMin = Arrays.stream(myNums).min();
        System.out.println("최소 : " + myMin);

        OptionalInt myMax = Arrays.stream(myNums).max();
        System.out.println("최대 : " + myMax);
        // ===
        System.out.println("===============");

        // === 컬렉션 스트림
        long count2 = myNumList.stream().count();
        System.out.println("개수 : " + count2);

        System.out.println("최소 : " + myNumList.stream().min((n1, n2) -> n1 - n2));
        System.out.println("최대 : " + myNumList.stream().max((n1, n2) -> n1 - n2));
        // ===
    }
}

```

```java
합 : 21
개수 : 5
평균 : OptionalDouble[4.2]
최소 : OptionalInt[1]
최대 : OptionalInt[7]
===============
개수 : 5
최소 : Optional[1]
최대 : Optional[7]
```

## reduce()

스트림을 구성하는 데이터들에 대해, 처음부터 순차적으로 두 요소들에 대해 어떤 연산을 실행하면서 누적되는 결과를 추출하고, 이 결과값과 그 다음 요소에 대해서도 같은 연산을 실행하는 작업을 반복한다. 그 후 결과적으로 어떤 누적값 하나를 반환하는 메서드이다. 

- 연산 종류 : 최종 연산
- 사용 가능한 스트림 : IntStream, Stream 모두

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

class ReduceEx {
    public static void main(String[] args) {
        int[] myNums = {4, 1, 2, 6, 3};
        List<String> myStrs = Arrays.asList(
            "java", "javascript", "python"
        );

        // 모든 수의 총합을 구하기
        int total = Arrays.stream(myNums)
                           .reduce(0, (n1, n2) -> n1 + n2);
        System.out.println("총합 : " + total);

        // 모든 문자열 데이터들 중 가장 길이가 긴 문자열을 반환.
        String lengthy = myStrs.stream()
                               .reduce(myStrs.get(0), (s1, s2) -> 
                               (s1.length() > s2.length()) ? s1 : s2);
        System.out.println("가장 길이가 긴 문자열 : " + lengthy);
    }
}

```

```java
총합 : 16
가장 길이가 긴 문자열 : javascript
```

reduce() 메서드에 인자를 두 개 대입할 경우, 첫 번째 인자는 연산을 시작할 초기값으로, 최종 결과의 기본 결과값이 된다. identity라고도 불린다. 위 예제의 lengthy 변수의 값으로 할당하는 코드를 보면, `myStrs.get(0)`에 해당하는 “java” 문자열이 만약 스트림 내 모든 요소들 중에서 가장 길이가 긴 값이였다면 “java”가 최종적으로 반환되는 식이다. 한 편 두 번째 인자로는 두 매개변수를 받아 어떤 연산을 하고, 그 연산 결과를 반환하는 메서드를 대입하면 된다. 해당 인자를 accumulator라고도 부른다. 위 예제에서는 람다식을 사용하였다. 

---
References

[1] [자바 스트림 설명부터 사용하는 이유 파헤쳐보기 #JAVA #스트림](https://zangzangs.tistory.com/171)

[2] [[Java] Stream의 reduce()와 BinaryOperator](https://velog.io/@jwkim/java-stream-reduce-binaryoperator)

[3] [Guide to Stream.reduce() \| Baeldung](https://www.baeldung.com/java-stream-reduce)

[4] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)