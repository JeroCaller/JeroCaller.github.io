---
title: "[Java] 컬렉션 프레임워크 (2)"
category: "Java"
tag: ["java", "자바", "컬렉션", "프레임워크", "collection", "framework"]
use_math: false
---

이전 페이지 [컬렉션 프레임워크 (Collection framework)](/java/java-collection-framework/) 에서는 List<E>, Set<E> 까지 다뤄보았다. 이번에는 이어서 또 다른 컬렉션 인터페이스들에 대해 살펴보겠다. 

# Queue<E> interface

## Queue 사용하기

자바의 컬렉션에서 queue 자료구조를 사용하는 방법 중 하나는 대신 LinkedList<E>를 사용하는 것이다. LinkedList<E>는 List<E>, Queue<E>, Deque<E> 인터페이스들을 모두 구현하는 컬렉션 클래스이기 때문이다. 따라서 참조 변수의 인터페이스 타입을 어떻게 설정하느냐에 따라 리스트, 큐, deque 셋 중의 하나의 자료구조로 사용할 수 있는 것이다. 

다음은 Queue<E> 인터페이스 타입의 참조 변수에 LinkedList 컬렉션 클래스의 인스턴스를 초기화하여 큐로 사용하는 예제이다. 

```java
import java.util.LinkedList;
import java.util.Queue;

class UseQueue {
    public static void printAllInQueue(Queue ll) {
        while(ll.peek() != null) {
            // size() : 현재 큐 내부의 요소들의 개수.
            System.out.println("The number of elements in the queue: " + ll.size());
            // peek() : 다음에 나올 큐 내부의 맨 마지막에 있는 요소 출력. 
            // 실제로 꺼내진 않는다. 
            System.out.println("What's next? " + ll.peek());
            // poll() : 큐 맨 마지막에 있는 요소를 꺼낸 후 반환한다. 
            System.out.println(ll.poll());
        }
    }

    public static void main(String[] args) {
        Queue<Integer> qu = new LinkedList<>();

        // 요소 삽입
        qu.offer(12);
        qu.offer(5);
        qu.offer(200);

        printAllInQueue(qu);
    }
}

```

```java
The number of elements in the queue: 3
What's next? 12
12
The number of elements in the queue: 2
What's next? 5
5
The number of elements in the queue: 1
What's next? 200
200
```

위 코드로부터, Queue로 사용할 때의 메서드들을 정리하면 다음과 같다. 

- size() : 현재 queue 내부에 있는 요소들의 개수를 반환.
- peek() : 큐에서 꺼낼 마지막 위치에 있는 요소를 반환. 이를 통해 다음에 어떤 요소가 나올지 미리 확인할 수 있다. 단, 다음에 나올 요소가 무엇인지 확인하는 용도이지 실제로 큐에서 해당 요소가 빠져나오는 것은 아니다.
- poll() : 큐에서 마지막에 있는 요소를 꺼내고 반환한다. peek()와 달리 실제로 마지막 요소가 큐 내에서 제거된다.
- offer(element) : 큐에 새로운 값을 삽입한다.

## Stack 사용하기

자바에서는 stack을 사용하려면 대신 Deque<E> 인터페이스를 사용한다. 이 인터페이스를 구현하여 deque 자료구조를 구현하는 컬렉션 클래스로 ArrayDeque, LinkedList가 있다. 

deque 자료구조는 큐를 양방향으로 사용할 수 있는 구조여서 사용 방법에 따라 스택으로도, 큐로도 사용 가능하다. 

다음은 deque를 이용하여 각각 큐와 스택으로 사용해보는 예제이다. 

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.LinkedList;

class UseStack {
    public static <T> void insertDeque
    (Deque dq, boolean insertFirst, T[] args) {
        for(T element : args) {
            if (insertFirst) {
                dq.offerFirst(element);
            } else {
                dq.offerLast(element);
            }
        }
    }

    public static <T> void pollAndPrintAllInDeque
    (Deque<T> dq, boolean pollFirst) {
        while(dq.peek() != null) {
            T data;
            if (pollFirst) {
                data = dq.pollFirst();
            } else {
                data = dq.pollLast();
            }
            System.out.print(data + ", ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        Deque<String> myDeq = new ArrayDeque<>();
        // LinkedList로도 Deque 사용 가능.
        // Deque<String> myDeq = new LinkedList<>();

        String[] strData = {"wow", "good", "hi"};

        // 왼쪽에서 데이터를 넣고 왼쪽에서 꺼내는 스택.
        insertDeque(myDeq, true, strData);
        pollAndPrintAllInDeque(myDeq, true);
        System.out.println(myDeq.size());

        // 오른쪽에서 데이터를 삽입하고 오른쪽에서 꺼내는 스택.
        insertDeque(myDeq, false, strData);
        pollAndPrintAllInDeque(myDeq, false);

        // 왼쪽에서 데이터를 삽입하고 오른쪽에서 꺼내는 큐.
        insertDeque(myDeq, true, strData);
        pollAndPrintAllInDeque(myDeq, false);

        // 오른쪽에서 데이터를 삽입하고 왼쪾에서 꺼내는 큐.
        insertDeque(myDeq, false, strData);
        pollAndPrintAllInDeque(myDeq, true);
    }
}

```

```java
hi, good, wow, 
0
hi, good, wow,
wow, good, hi,
wow, good, hi,
```

Deque에서 사용할 수 있는 메서드들을 정리하면 다음과 같다.

- offerFirst(element) : deque 자료구조가 가로로 존재한다고 상상할 때, 요소를 왼쪽에 삽입.
- offerLast(element) : 요소를 오른쪽에 삽입.
- pollFirst() : 요소를 왼쪽에서 추출.
- pollLast() : 요소를 오른쪽에서 추출.

# Map<K, V> interface

Map 인터페이스에는 key-value 쌍 데이터를 입력, 관리하기 위한 메서드들이 정의되어 있다. 이 때, 해당 인터페이스를 구현하는 클래스의 key는 고유, 즉 중복이 비허용되며, 대신 value는 중복될 수 있다. 

## HashMap<K, V>

HashMap은 해시 알고리즘을 이용하여 구현한 해시맵 자료구조이다. (자료구조에서는 해시맵과 해시테이블이 동일어로 사용되는 것 같은데, 자바에서는 HashMap과 HashTable이 따로 존재한다고 한다)

다음은 HashMap을 이용해보는 예제이다. 

```java
import java.util.HashMap;
import java.util.Set;

enum HowToIter{KEY, VALUE, ALL};

class UseHashMap {
    public static <K, V> void printAllInHashMap
    (HashMap<K, V> hm, HowToIter how) {
        // 해시맵의 키만 담고 있는 Set 객체 반환.
        Set<K> ks = hm.keySet();
        for(K myKey : ks) {
            switch(how) {
                case KEY:
                    System.out.print(myKey + ", ");
                    break;
                case VALUE:
                    System.out.print(hm.get(myKey) + ", ");
                    break;
                case ALL:
                    System.out.print("(" + myKey + " : " + hm.get(myKey) + "), ");
                    break;
            }
        }
        System.out.println();  // 개행.
    }

    public static void main(String[] args) {
        HashMap<Integer, String> userDatabase = new HashMap<>();
        // 고유ID : 닉네임
        userDatabase.put(120, "wow221");
        userDatabase.put(50, "bowWow200");
        userDatabase.put(80, "titan889");

        printAllInHashMap(userDatabase, HowToIter.KEY);
        printAllInHashMap(userDatabase, HowToIter.VALUE);
        printAllInHashMap(userDatabase, HowToIter.ALL);

        System.out.println(userDatabase);
    }
}

```

```java
80, 50, 120, 
titan889, bowWow200, wow221,
(80 : titan889), (50 : bowWow200), (120 : wow221),
{80=titan889, 50=bowWow200, 120=wow221}
```

위 예제에 사용된 HashTable에서 사용 가능한 메서드들을 정리하면 다음과 같다.

- put(key, value) : key-value 쌍의 데이터를 입력한다.
- get(key) : 인자로 전달된 key와 연결된 value를 반환.
- keySet() : 해시맵 내부의 모든 키들만 모은 Set 객체 반환.

HashMap 클래스는 Iterable<T> 인터페이스를 구현하지 않아서, 향상된 for문과 iterator()를 사용할 수 없다. 이는 후술할 TreeMap 클래스도 마찬가지이다. 단, keySet()을 이용하여 Set 객체를 얻으면 이를 통해서는 향상된 for문과 iterator()를 사용할 수 있다. 

또한, 위 코드와 그 출력결과를 보면 알겠지만, HashMap 클래스는 이를 순회하여 출력할 때 데이터를 입력한 순서, 또는 정렬된 채로 출력된다는 보장이 없다. 

## TreeMap<K, V>

TreeMap 클래스는 HashMap 클래스와 동일하지만, 이잔 탐색 트리를 이용하여 key값을 정렬한다. 따라서 TreeMap의 key에 들어갈 객체의 클래스는 Comparable 또는 Comparator 인터페이스가 구현되어 있어야 한다. 

다음은 TreeMap 클래스를 사용해보는 예제이다. 앞선 예제 3-1과 코드 구조가 거의 동일하다.

```java
import java.util.Set;
import java.util.TreeMap;

enum HowToIter{KEY, VALUE, ALL};

class UseTreeMap {
    public static <K, V> void printAllInTreeMap
    (TreeMap<K, V> hm, HowToIter how) {
        // 해시맵의 키만 담고 있는 Set 객체 반환.
        Set<K> ks = hm.keySet();
        for(K myKey : ks) {
            switch(how) {
                case KEY:
                    System.out.print(myKey + ", ");
                    break;
                case VALUE:
                    System.out.print(hm.get(myKey) + ", ");
                    break;
                case ALL:
                    System.out.print("(" + myKey + " : " + hm.get(myKey) + "), ");
                    break;
            }
        }
        System.out.println();  // 개행.
    }

    public static void main(String[] args) {
        TreeMap<Integer, String> userDatabase = new TreeMap<>();
        // 고유ID : 닉네임
        userDatabase.put(120, "wow221");
        userDatabase.put(50, "bowWow200");
        userDatabase.put(80, "titan889");

        printAllInTreeMap(userDatabase, HowToIter.KEY);
        printAllInTreeMap(userDatabase, HowToIter.VALUE);
        printAllInTreeMap(userDatabase, HowToIter.ALL);
    }
}

```

```java
50, 80, 120, 
bowWow200, titan889, wow221,
(50 : bowWow200), (80 : titan889), (120 : wow221),
```

위 예제에서 key값으로 int형 값이 전달되었는데, int형 기본 자료형의 값이 Integer 래퍼 클래스로 auto boxing되어 저장된다. 이 때, Integer 클래스에는 이미 Comparable 인터페이스가 구현되어 있기에 위 출력결과에서 key가 오름차순 정렬된 채로 출력된 것이다. 

# 컬렉션 알고리즘

Collections 클래스에는 여러 가지 알고리즘들을 구현한 메서드들이 있다. 

## 정렬 : sort()

List\<E\>로 구현된 컬렉션 클래스의 객체 내부 요소들을 정렬하고자 할 때 `Collections.sort()` 메서드를 사용하면 된다. 첫 번째 인자로 List로 구현된 컬렉션 객체를 대입하면 기본적으로 오름차순으로 정렬한다. 단, 다른 정렬 기준을 적용하고자 한다면 두 번째 인자로 Comparator 인터페이스를 구현하는 클래스의 객체를 대입하면 된다. 

다음은 Integer형 정수들이 저장되어 있는 배열 리스트를 오름차순 정렬하거나, Comparator 인터페이스를 구현한 클래스를 이용하여 내림차순으로 정렬해보는 예제이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

class IntegerDesc implements Comparator<Integer> {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
}

class CollectionSort {
    public static void main(String[] args) {
        List<Integer> numArr = new ArrayList<>(Arrays.asList(
            3, 12, 1, 99, 42
        ));

        System.out.println("=== 정렬 전 데이터 ===");
        System.out.println(numArr);

        Collections.sort(numArr);
        System.out.println("=== 오름차순 정렬 후 데이터 ===");
        System.out.println(numArr);

        Collections.sort(numArr, new IntegerDesc());
        System.out.println("=== 내림차순 정렬 후 데이터 ===");
        System.out.println(numArr);
    }
}

```

```java
=== 정렬 전 데이터 ===
[3, 12, 1, 99, 42]
=== 오름차순 정렬 후 데이터 ===
[1, 3, 12, 42, 99]
=== 내림차순 정렬 후 데이터 ===
[99, 42, 12, 3, 1]
```

## 이진 탐색을 통한 빠른 검색 : Collections.binarySearch()

`Collections.binarySearch(list, targetElement)` 메서드를 이용하면 이진 탐색을 이용하여 리스트 내 특정 요소를 더 빠르게 찾아 그 요소의 인덱스를 얻을 수 있다. 단, 이진 탐색법은 리스트가 미리 오름차순(또는 내림차순)으로 정렬되어 있어야 제대로 사용할 수 있다. 따라서 `Collections.sort()`를 이용하여 미리 정렬한 후에 사용해야 한다. 

다음은 해당 메서드를 사용하는 예제이다. 

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

class UseBinarySearch {
    public static void main(String[] args) {
        List<Integer> myArrList = new ArrayList<>(Arrays.asList(
            9, 4, 12, 5, 29, 17
        ));

        Collections.sort(myArrList);
        Integer idx1 = Collections.binarySearch(myArrList, 4);
        System.out.println(idx1);

        // 해당 자료구조 내에 없는 값을 검색하려 함.
        Integer idx2 = Collections.binarySearch(myArrList, 100); 
        System.out.println(idx2);
    }
}

```

```java
0
-7
```

만약 주어진 리스트 내에서 찾고자 하는 요소가 없을 경우, 임의의 음수를 반환한다. 

## 복사 : copy()

`Collections.copy(dest, src)` 메서드를 이용하면 원본 리스트를 복사하여 복사할 대상 리스트에 복사할 수 있다. 첫 번째 인자에는 복사한 리스트를 붙여넣을 배열을, 두 번째 인자에는 원본 리스트를 입력하면 된다. 

다음은 해당 메서드를 이용해본 예제이다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

class CollectionsCopy {
    public static void main(String[] args) {
        List<String> originalList = new ArrayList<>(Arrays.asList(
            "자바", "파이썬", "자바스크립트", "자료구조", "알고리즘"
        ));
        List<String> copiedList = new ArrayList<>(originalList);

        // 기존 요소의 값을 변경
        copiedList.set(3, null);
        System.out.println(copiedList);

        // copy(dest, src) : src 리스트를 dest 리스트에 모든 요소 복사.
        // 만약 dest 리스트의 길이가 src보다 작다면 IndexOutOfBoundsException 
        // 예외가 발생하여 복사가 안됨. 
        Collections.copy(copiedList, originalList);

        System.out.println(originalList);
        System.out.println(copiedList);

        copiedList.set(copiedList.size()-1, null);

        // 복사된 배열 리스트의 요소 변경이 원본 배열 리스트에 영향을 미치는지 확인.
        // (즉, 깊은 복사가 되었는가를 확인)
        System.out.println(originalList);
        System.out.println(copiedList);
    }
}

```

```java
[자바, 파이썬, 자바스크립트, null, 알고리즘]
[자바, 파이썬, 자바스크립트, 자료구조, 알고리즘]
[자바, 파이썬, 자바스크립트, 자료구조, 알고리즘]
[자바, 파이썬, 자바스크립트, 자료구조, 알고리즘]
[자바, 파이썬, 자바스크립트, 자료구조, null]
```

한 가지 주의할 점은, 복사 대상 리스트(dest)의 길이가 원본 리스트의 길이보다 더 작으면 예외가 발생하게 된다는 것이다. 따라서 복사 대상 리스트의 길이가 원본 리스트 길이보다 같거나 커야만 복사가 가능하다. (복사 대상 리스트의 길이가 더 길 경우, 원본 리스트의 길이만큼만 복사된다)

---
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)