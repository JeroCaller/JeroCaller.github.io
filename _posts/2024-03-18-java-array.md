---
title: "[Java] 배열 (Array)"
category: "Java"
tag: ["java", "자바", "배열", "array"]
---

배열은 둘 이상의 동일한 자료형 데이터들을 하나로 저장할 수 있는 고정된 크기의 선형 자료구조이다. 여러 개의 칸들이 일직선으로 나열되어 연결되어 있는 기차와 같다고 생각하면 된다. 

자바에서는 배열에 값들을 입력, 저장할 때 해당 값들이 모두 동일한 자료형이어야한다. 

# 배열 선언 및 초기화

다음은 배열을 선언하고, 배열 내 각 위치에 새로운 값을 초기화시키는 예제이다. 

```java
class ArrayBasic {
    public static void main(String[] args) {
        // 배열 선언.
        String[] books = new String[3];
        int bookSold[] = new int[3];

        books[0] = "프로그래밍 배우기";
        books[1] = "습관 정립하기";
        books[2] = new String("너가 코딩을 못하는 이유 10가지");

        bookSold[0] = 50;
        bookSold[1] = 21;
        bookSold[2] = 70;

        // 이 방식으론 배열 내 모든 요소들을 출력하여 확인해볼 수 없음.
        System.out.println(books);
        System.out.println(bookSold);

        // 순회하여 배열 내 요소들을 출력.
        for(int i = 0; i < books.length; i++) {
            System.out.printf("%s : %d\n", books[i], bookSold[i]);
        }
    }
}

```

```java
[Ljava.lang.String;@4517d9a3
[I@372f7a8d
프로그래밍 배우기 : 50
습관 정립하기 : 21
너가 코딩을 못하는 이유 10가지 : 70
```

위 예제 코드에서 배열을 선언하는 부분은 다음이다. 

```java
String[] books = new String[3];
int bookSold[] = new int[3];
```

위와 같은 배열 선언법을 일반화하면 다음과 같다. 

```java
자료형[] 변수명 = new 자료형[개수];
자료형 변수명[] = new 자료형[개수];
```

자료형 바로 오른쪽에 대괄호([])를 쓰던 변수명 오른쪽에 대괄호를 쓰던 상관없다. 그리고 new 뒤의 자료형 옆에는 해당 배열의 크기를 설정해준다. 위 예제에서는 books라는 변수를 오로지 String 자료형 값만 저장하는 배열로 선언하였으며 그 크기를 3으로 정한 것이다. booksold 변수의 경우, int형 값만 저장하는 크기 3의 배열을 선언한 것이다. 

자바에서는 배열도 객체로 취급한다. 따라서 new 키워드가 붙은 것이다. 

배열 내 각 위치에 접근할 때에는 books[1]과 같이 인덱싱을 통해 접근하며, 인덱스는 0부터 시작한다. 이 인덱싱을 통해 배열 내 해당 위치의 값에 접근할 수 있고, 값을 변경시킬 수도 있는 것이다. 

참고로, 배열은 숫자형, 불린형 등 기본 자료형으로만 만들 수 있는 게 아니다. 객체 자료형도 가능하며, 여기에는 사용자가 정의한 객체 자료형만 담는 배열도 만들 수 있다. 

```java
class User2 {
    String userID;
    int point;

    User2(String userID, int point) {
        this.userID = userID;
        this.point = point;
    }

    void printProfile() {
        System.out.println("====== 사용자 정보 ======");
        System.out.println("사용자 아이디 : " + userID);
        System.out.println("보유 포인트 : " + point);
    }
}

class ArrayClass {
    public static void main(String[] args) {
        User2[] userArray = new User2[3];

        userArray[0] = new User2("kalguksu123", 1000);
        userArray[1] = new User2("chobocoding111", 2000);
        userArray[2] = new User2("bowwow00", 3000);

        for(int i = 0; i < userArray.length; i++) {
            userArray[i].printProfile();
        }
    }
}

```

```java
====== 사용자 정보 ======
사용자 아이디 : kalguksu123
보유 포인트 : 1000
====== 사용자 정보 ======
사용자 아이디 : chobocoding111
보유 포인트 : 2000
====== 사용자 정보 ======
사용자 아이디 : bowwow00
보유 포인트 : 3000
```

# 배열 초기화

앞선 예제에서는 배열을 선언만 하였지 그 초기화는 그 이후에 이루어졌다. 반면, 배열 선언과 동시에 원하는 값으로 초기화하는 것도 가능하다. 자바에서는 배열을 표현할 때 중괄호 ({})를 이용하며, 중괄호 안에 같은 자료형의 값들을 쉼표(,)를 구분자로 사용하여 나열하는 식으로 배열을 초기화할 수 있다. 

```java
int[] numArr = {12, 5, 11, 7};
double[] realArr = {1.3, 4.2, 3.141592, 12.0};
String[] strArr = {"great", "wow", "cool", "jar"};
```

```java
자료형[] 변수명 = {값1, 값2, ...};
자료형 변수명[] = {값1, 값2, ...};
```

배열을 선언과 동시에 초기화할 경우, `new 자료형[]` 부분은 생략할 수 있다. 이미 배열을 초기화함으로써 배열의 크기를 알려준 것이나 다름없기 때문이다. 

만약 배열을 선언만 하는 경우, 기본 자료형의 배열의 경우 모든 값들이 0 (실수의 경우 0.0, boolean형의 경우 0은 false 취급이므로 false)으로 초기화 된다. 단, char형 배열의 경우 null을 의미하는 유니코드 ‘\u0000’으로 초기화된다. 그리고 객체 배열의 경우 모두 null로 초기화된다. 

```java
class SomeClass {};

class ArrayAutoInit {
    public static void main(String[] args) {
        int len = 3;
        int[] numArr = new int[len];
        boolean[] boolArr = new boolean[len];
        double[] doubleArr = new double[len];
        char[] charArr = new char[len];
        String[] strArr = new String[len];
        SomeClass[] someArr = new SomeClass[len];

        for(int i = 0; i < len; i++) {
            System.out.println(numArr[i]);
            System.out.println(boolArr[i]);
            System.out.println(doubleArr[i]);
            System.out.println(charArr[i]);
            System.out.println(strArr[i]);
            System.out.println(someArr[i]);
            System.out.println("===========");
        }
        // 유니코드 \u0000은 null을 의미.
        System.out.println(charArr[0] == '\u0000');
    }
}

```

```java
0
false
0.0

null
null
===========
0
false
0.0

null
null
===========
0
false
0.0

null
null
===========
true

```

# 배열형 매개변수 및 반환

배열은 메서드의 매개변수 또는 반환형에도 사용할 수 있다. 

```java
class ParaReturn {
    public static String combineStrings(String[] strSource) {
        StringBuilder sb = new StringBuilder();

        // 향상된 for문 (Enhanced for)
        // 배열 순회 시 사용할 수 있는 문법이다.
        for(String token : strSource) {
            sb.append(token);
        }
        return sb.toString();
    }
    public static void main(String[] args) {
        // 배열 생성 및 초기화.
        String[] myStrs = {"000", "-", "111", "-", "222"};
        String result = combineStrings(myStrs);

        System.out.println(result);

        // 배열은 그 크기가 고정되어서 크기 이상의 값을 추가할 수 없다.
        // 다음의 코드는 에러가 발생함.
        // myStrs[5] = "333";
        // System.out.println(myStrs.length);
    }
}

```

```java
000-111-222
```

위 예제에서 combineStrings 메서드의 매개변수로 String 배열형으로 선언되어, 해당 배열을 인자로 받는 것을 확인할 수 있다. 

# Enhanced for (for~each)

방금 전 예제 3-1의 combineStrings 메서드 내부를 보면, 인자로 받은 배열을 순회할 때의 for문이 일반적인 for문과는 다른 것을 알 수 있다. 일반적인 for문으로 배열 전체를 순회할 때는 다음과 같았다.

```java
for(int i = 0; i < arr.length; i++) {
    // 코드
}
```

그러나 예제 3-1에서 쓰인 for문은 다음의 형태이다.

```java
for(자료형 변수명 : 배열명) {
    // 코드
}
```

배열 순회 시에 사용되는 이러한 for 문법을 “향상된 for문 (enhanced for)”이라 한다. for 문안에 있는 배열에서 값을 처음 위치부터 하나씩 접근하여 이를 임시 변수에 저장하고, 이 임시 변수를 토대로 반복문 코드를 구현할 수 있는 것이다. 

# main 메서드

main 메서드의 매개변수를 보면 항상 String형의 배열이 선언된 것을 알 수 있다. 

```java
class MainParameter {
    public static void main(String[] args) {
        if (args.length == 0) {
            System.out.println("main 메서드로 넘어온 인자 없음.");
        } else {
            for(String element : args) {
                System.out.println(element);
            }
        }
    }
}

```

main 메서드는 사용자가 직접 호출하지 않고 대신 자바 가상 머신(JVM)이 해당 파일 실행 시 자동으로 호출하는 메서드다. 

main 메서드의 매개변수를 이용하는 방법으로는 명령 프롬프트를 통해 인자를 전달하는 방법이 있다. 명령 프롬프트 창에서 javac 명령어로 먼저 클래스 파일을 형성한 후, 다음의 명령어를 입력해보자.

```java
> java MainParameter
main 메서드로 넘어온 인자 없음.
```

java 명령어 뒤에 자바 파일명을 입력한 후, 뒤에 아무것도 입력하지 않으면 main 메서드의 args 매개변수로 그 어떤 인자도 대입되지 않는다. 이번엔 다음과 같이 입력해보자.

```java
> java MainParameter 1 2 3 one two three
1
2
3
one
two
three
```

공백으로 나누면서 문자열들을 입력하니 해당 문자열들이 그대로 출력된 것을 확인할 수 있다. 이렇듯 자바 파일명 뒤에 공백을 기준으로 여러 개의 문자열들을 입력하면 그대로 args에 배열 형태로 대입되어 이를 해당 메서드 내부에서 처리할 수 있게 되는 것이다.

# 다차원 배열

반복문 안에 반복문을 사용할 수 있고, if문 안에 if문을 쓸 수 있듯, 배열도 배열 안에 배열을 쓸 수 있다. 이렇게 배열을 중첩하여 사용한 형태의 배열을 다차원 배열이라 한다. 이전까지 살펴본 배열은 모두 일차원 배열이었던 것이다. 

예를 들어, int형 2차원 배열을 선언하고자 한다면 다음과 같이 선언할 수 있다. 

```java
int[][] movieSeats = new int[10][10];
// 2차원 배열을 꼭 정사각형마냥 행과 열의 개수를 똑같이 맞출 필요는 없다.
```

3차원이면 대괄호를 3번 사용하고, n차원이면 대괄호를 n번 사용하는 방식이다. 

다차원 배열도 선언과 동시에 초기화가 가능하다.

```java
int[][] seatsNonSquare = {
            {1},
            {2, 3},
            {4, 5, 6},
        };
```

다음은 int형 2차원 배열을 선언하고 이를 초기화한 후, 순회하며 모든 값들을 출력하는 예제이다.

```java
class TwoDArray {
    /**
     * 주어진 2차원 배열을 1부터 연속적인 자연수로 초기화한다.
     * @param twoArr  : 2차원 int형 배열
     * @return int[][] : 초기화가 완료된 2차원 int형 배열
     */
    public static int[][] initializeTwoDArrayWithNumbers(int[][] twoArr) {
        int num = 1;
        for(int i = 0; i < twoArr.length; i++) {
            for(int j = 0; j < twoArr[i].length; j++) {
                twoArr[i][j] = num++;
            }
        }
        return twoArr;
    }

    public static void printTwoDArray(int[][] twoArr) {
        for(int[] oneArr : twoArr) {
            for(int num : oneArr) {
                System.out.print(num + "\t");
            }
            System.out.println();  // 개행.
        }
    }

    public static void main(String[] args) {
        int[][] movieSeats = new int[10][10];
        int[][] seatsNonSquare = {
            {1},
            {2, 3},
            {4, 5, 6},
        };
        
        movieSeats = initializeTwoDArrayWithNumbers(movieSeats);
        printTwoDArray(movieSeats);

        printTwoDArray(seatsNonSquare);
    }
}

```

```java
1       2       3       4       5       6       7       8       9       10
11      12      13      14      15      16      17      18      19      20
21      22      23      24      25      26      27      28      29      30
31      32      33      34      35      36      37      38      39      40
41      42      43      44      45      46      47      48      49      50
51      52      53      54      55      56      57      58      59      60
61      62      63      64      65      66      67      68      69      70
71      72      73      74      75      76      77      78      79      80
81      82      83      84      85      86      87      88      89      90
91      92      93      94      95      96      97      98      99      100
1
2       3
4       5       6
```

# 배열의 메서드들

앞서 말했듯, 배열도 객체이다. 또한 배열에 대해 여러 가지 기능을 제공해주는 기본 유틸리티 메서드들도 존재한다. 이를 사용하려면 먼저 다음의 코드를 입력하여 Arrays 클래스를 import하여야 한다.

```java
import java.util.Arrays;
```

## fill() : 배열을 모두 같은 값으로 초기화하기

`public static void fill(int[] array, int value)` : value 매개변수의 값으로 배열 내 모든 값들을 초기화. 배열 전체 초기화 메서드.

`public static void fill(long[] a, int fromIndex, int toIndex, long val)` : fromIndex부터 (toIndex - 1) 범위의 인덱스에 해당하는 배열 내 값들을 val 값으로 초기화. 배열 부분 초기화 메서드.

## 배열 복사

`public static int[] copyOf(int[] original, int newLength)`  : 첫 번째 매개변수로 대입된 배열을 두 번째 매개변수로 지정된 숫자값만큼의 길이만큼만 복사한 배열을 반환.

`public static int[] copyOfRange(int[] original, int from, int to)` : 첫 번째 매개변수로 지정된 배열에 대해, from부터 (to - 1) 까지의 인덱스에 해당하는 요소들을 복사한 새 배열을 반환.

```java
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length)
```

arraycopy : 해당 메서드는 Arrays가 아닌 System (System.out.println 할 때 그 System) 클래스에 포함된 메서드이다. 원본 배열 src의 인덱스 srcPos 부터 length 만큼의 길이를 가지는 요소들을 대상 배열 dest의 destPos 위치부터 복사해준다. 

다음은 fill 메서드와 배열 복사 메서드들을 함께 사용한 예제이다.

```java
import java.util.Arrays;

class ArraysMethods {
    public static void printIntArray(int[] arr) {
        for(int element : arr) {
            System.out.print(element + "  ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        // 초기화 되지 않은 int형 배열은 모두 0으로 자동 초기화됨.
        int[] numbers1 = new int[10];
        int[] numbers2 = new int[10];
        int[] numbers3 = new int[10];
        int[] numbers4 = new int[10];

        printIntArray(numbers1);
        System.out.println("====================");

        // 배열 내 모든 요소들을 두 번째 인자로 초기화.
        Arrays.fill(numbers1, 1);
        Arrays.fill(numbers2, 2);
        Arrays.fill(numbers3, 3);

        printIntArray(numbers1);
        printIntArray(numbers2);
        printIntArray(numbers3);
        System.out.println("====================");

        numbers2 = Arrays.copyOf(numbers1, numbers1.length - 3);
        numbers3 = Arrays.copyOfRange(numbers1, 4, numbers1.length - 3);

        printIntArray(numbers1);
        printIntArray(numbers2);
        printIntArray(numbers3);
        // 복사 대상 배열의 원래 길이보다 더 짧게 복사한 경우, 길이도 변한다.
        System.out.println(numbers2.length);
        System.out.println(numbers3.length);
        System.out.println("====================");

        printIntArray(numbers4);
        // copyOf, copyOfRange와 달리, 복사 대상 배열의 원래 길이보다 더 짧게 복사해도 
        // 배열의 길이 자체는 변하지 않는다. 
        System.arraycopy(numbers1, 0, numbers4, 0, numbers1.length-2);
        printIntArray(numbers4);
    }
}

```

```java
0  0  0  0  0  0  0  0  0  0  
====================
1  1  1  1  1  1  1  1  1  1  
2  2  2  2  2  2  2  2  2  2  
3  3  3  3  3  3  3  3  3  3  
====================
1  1  1  1  1  1  1  1  1  1
1  1  1  1  1  1  1
1  1  1
7
3
====================
0  0  0  0  0  0  0  0  0  0
1  1  1  1  1  1  1  1  0  0
```

## equals() : 배열 내용 비교

`Arrays.equals(arr1, arr2)` : 두 배열이 완전히 같은지 비교하고, 같으면 true, 아니면 false를 반환.

```java
import java.util.Arrays;

class ArraysMethods2 {
    public static void main(String[] args) {
        boolean[] boolArr = {true, false, true};
        boolean[] boolArr2 = {true, false, true};
        boolean[] boolArr3 = {false, true, false};
        boolean[] boolArr4 = {true};
        // 배열 길이가 달라도 false를 반환.
        boolean[] boolArr5 = {true, false, true, true};

        System.out.println(Arrays.equals(boolArr, boolArr2));
        System.out.println(Arrays.equals(boolArr, boolArr3));
        System.out.println(Arrays.equals(boolArr, boolArr4));
        System.out.println(Arrays.equals(boolArr, boolArr5));
    }
}

```

```java
true
false
false
false
```

## sort() : 배열 요소 정렬

`Arrays.sort(arr)` : 배열 요소를 오름차순으로 정렬.

```java
import java.util.Arrays;

class ArraySort {
    public static void main(String[] args) {
        int[] numArr = {12, 5, 11, 7};
        double[] realArr = {1.3, 4.2, 3.141592, 12.0};
        String[] strArr = {"great", "wow", "cool", "jar"};

        String delimiter = "   ";

        Arrays.sort(numArr);
        Arrays.sort(realArr);
        Arrays.sort(strArr);
        
        for(int n : numArr) {
            System.out.print(n + delimiter);
        }
        System.out.println();

        for(double d : realArr) {
            System.out.print(d + delimiter);
        }
        System.out.println();

        for(String s : strArr) {
            System.out.print(s + delimiter);
        }
        System.out.println();
    }
}

```

```java
5   7   11   12   
1.3   3.141592   4.2   12.0
cool   great   jar   wow
```

-----
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)