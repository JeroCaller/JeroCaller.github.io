---
title: "[Java] 컬렉션 프레임워크 (Collection framework)"
category: "Java"
tag: ["java", "자바", "컬렉션", "프레임워크", "collection", "framework"]
---

이 페이지에서는 자료구조에 대한 설명이 나온다. 자료구조에 대한 자세한 내용은 파이썬 언어와 함께 작성된 [자료구조와 알고리즘 블로그 로드맵](/roadmap/data-structure-and-algorithm-roadmap/) 페이지의 “자료구조” 챕터의 자료구조 페이지들을 참고. 

# 자료구조와 컬렉션 프레임워크

자료구조는 수많은 데이터들을 효율적으로 관리하도록 고안되어 데이터들을 담는 일종의 “컨테이너”같은 것이라 보면 되겠다. 자바에서는 이러한 자료구조를 컬렉션 프레임워크로서 기본 제공해준다. 

컬렉션 프레임워크에서는 여러 가지 자료구조들을 구현하기 위한 인터페이스들이 정의되어 있다. 그리고 이 인터페이스들은 상속 관계를 가지며, 다음과 같은 관계에 놓여있다. 

- Iterable<E>
    - Collection<E>
        - List<E>
        - Set<E>
        - Queue<E>
- Map<K, V>

Iterable<E> 인터페이스가 최상위 인터페이스이며, 그 아래에는 Collection<E>, 그 아래에는 각각 List, Set, Queue 자식 인터페이스들이 존재한다. 반면 Map 인터페이스는 독자적으로 구현되어 있다. 

위와 같은 자료구조를 구현하기 위한 여러 가지 인터페이스들을 통해 여러 가지 자료구조들이 클래스로 구현되어 있다. 

- List<E> : 데이터들의 입력된 순서를 유지하는 특성의 자료구조 List의 구현을 위한 인터페이스. 데이터 중복을 허용. 구현 클래스로는 ArrayList, LinkedList가 있다.
- Set<E> : 데이터들의 입력된 순서를 유지하지 않는 특성의 자료구조 Set의 구현을 위한 인터페이스. 데이터 중복이 허락되지 않는다. 구현 클래스로는 HashSet, TreeSet이 있다.
- Map<K, V> : key - value로 이루어진 데이터들을 담는 자료구조 Map을 구현하는 인터페이스. 키는 중복 비허용, 값은 중복 허용되는 자료구조이다. 구현 클래스로는 HashMap, TreeMap이 있다.
- Queue<E> : 입력된 데이터들의 순서를 유지하는 자료구조 Queue를 구현하는 인터페이스. 데이터 중복 허용. 구현 클래스로는 LinkedList가 있다.

이제, 각 인터페이스들을 구현하는 각각의 컬렉션 클래스들에 대해 살펴보겠다. 

# List<E> interface

List<E> 인터페이스를 구현한 컬렉션 클래스로 다음이 존재한다. 

- ArrayList<E> : 배열 기반 자료구조(배열은 아님)로, 즉 배열을 이용하여 객체를 저장하는 방식의 자료구조이다.
- LinkedList<E> : 연결 기반 자료구조.

위 컬렉션 클래스들을 비롯하여 List<E> 인터페이스를 구현하는 컬렉션 클래스들은 다음의 공통점을 가진다. 

- 데이터의 저장 순서 유지
- 데이터 중복 저장 허용

한 편, ArrayList와 LinkedList의 차이점은 다음과 같다. 

- ArrayList는 배열을 기반으로 하는 자료구조이다. 배열은 그 크기가 고정된 자료구조로, 만약 해당 배열에 새로운 요소들을 추가하고자 한다면 먼저 기존 배열의 크기와 새로운 요소들의 개수만큼의 빈 칸들을 가지는 새로운 배열을 하나 준비해야 한다. 그 후, 기존 배열의 요소들을 새 배열에 복사하고 나서 그 뒤에 새 요소들을 채워넣는 식이다. 이러한 과정들이 모두 배열의 크기가 정적이기 때문에 발생한 일이다. 삭제도 마찬가지로 배열의 크기가 정해져 있기 때문에 기본적으로 불가능하고, 역시 새 배열을 만들고 복사하는 형식을 따라야 한다. 따라서 배열에 요소들을 추가, 삭제할 때 시간도 걸리고 그 과정이 조금 더 복잡해진다. ArrayList는 배열은 아니지만, 배열을 기반으로 하기에, 데이터 추가 및 삭제가 LinkedList에 비해 그 시간이 더 오래 걸리는 단점이 존재한다. 
반면 LinkedList는 하나의 데이터를 담는 노드가 다른 노드들과 선형적으로 연결되어 있는 구조이다. 따라서 연결 리스트의 맨 뒤에 데이터를 추가, 삭제하고자 한다면 추가의 경우 새 노드를 맨 뒤 노드와 연결하면 그만이고, 삭제의 경우에도 맨 뒤 노드만 삭제하면 그만이다. 따라서 ArrayList에 비해선 데이터 추가, 삭제 속도가 더 빠르다. 
(위에서 언급한 데이터 추가 및 삭제는 자료구조의 맨 뒤 요소를 기준으로 설명함)
- 반면, 검색 속도는 ArrayList가 더 빠르다. LinkedList는 노드들을 선형으로 연결한 관계이고, 따라서 특정 노드를 검색하려면 첫 번째 노드부터 시작하여 검색해야 한다. 최악의 경우 모든 데이터들의 수만큼 검색해야 한다. 즉, 최악의 경우 시간복잡도는 O(N)이다. 그러나 배열은 그 인덱스만 알면 O(1)의 시간복잡도로 바로 원하는 데이터를 검색할 수 있다.

반면, ArrayList, LinkedList는 List<E> 인터페이스를 공통으로 받아 구현하기에, 두 클래스들에 정의된 메서드들도 그 이름과 기능이 일치한다. 다음은 두 클래스들을 사용해보는 예제이다. 

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

class ListEx {
    public static void printListElements(List li) {
        for(int i = 0; i < li.size(); i++) {
            if (i == li.size() - 1) {
                // get(i) : i 인덱스의 요소 반환.
                System.out.println(li.get(i));
            } else {
                System.out.print(li.get(i) + ", ");
            }
        }
    }

    public static void testListMethods(List<String> li) {
        if (li instanceof ArrayList) {
            System.out.println("=== ArrayList 테스트 ===");
        } else if (li instanceof LinkedList) {
            System.out.println("=== LinkedList 테스트 ===");
        } else {
            System.out.println("=== List 테스트 ===");
        }

        li.add("good");  // 값을 맨 마지막에 추가.
        li.add("well done");
        li.add(1, "bad");  // 해당 index에 값 삽입.

        printListElements(li);
        li.remove(li.size() - 1);  // 맨 마지막 요소 삭제.
        printListElements(li);

        System.out.println("===================");
        System.out.println();
    }

    public static void main(String[] args) {
        List<String> myArrList = new ArrayList<>();
        List<String> myLinkedList = new LinkedList<>();

        testListMethods(myArrList);
        testListMethods(myLinkedList);
    }
}
```

```java
=== ArrayList 테스트 ===
good, bad, well done
good, bad
===================

=== LinkedList 테스트 ===
good, bad, well done
good, bad
===================
```

위 예제 코드의 testListMethods() 메서드는 그 인자로 List<E> 자료형의 인자만 받고 있다. 따라서 ArrayList와 LinkedList 클래스 타입 모두 인수로 전달 가능하다. 이 둘은 다름에도 List 인터페이스를 공통으로 구현하기에 add(), remove()와 같은 메서드들을 공통적으로 사용할 수 있다. 

## Arrays.asList()로 리스트 초기화

List<E> 인터페이스를 구현하는 컬렉션 클래스에서는 선언과 동시에 초기화가 불가능하다. 즉, 다음의 코드는 에러를 발생시킨다.

```java
List<String> myArrList = new ArrayList<>("good", "bad", "java", "python");
List<String> myLinkedList = new LinkedList<>("good", "bad", "java", "python");
```

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
        Cannot infer type arguments for LinkedList<>

        at AsListEx.main(AsListEx.java:35)
```

하지만 그렇다고 해서 정말로 선언과 동시에 초기화가 불가능한 것은 아니다. java.util.Arrays의 `Arrays.asList()` 메서드를 이용하면 리스트 선언과 동시에 초기화가 가능하다. 

```java
List<String> myList = Arrays.asList("good", "bad", "java", "python");
```

해당 메서드는 List를 반환한다. 위 메서드를 이용하면 ArrayList, LinkedList도 선언과 동시에 초기화가 가능하다. 

```java
List<String> otherList = new ArrayList<>(Arrays.asList("good", "bad", "java", "python"));
```

한 가지 주의할 점은, Arrays.asList()의 반환값인 리스트 객체는 요소를 추가, 삭제할 수가 없다. 이를 위해서는 ArrayList 또는 LinkedList로 변환하여야 한다. 

다음은 asList()를 이용한 예제이다.

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.LinkedList;

class AsListEx {
    public static void printListElements(List li) {
        String listType = "";
        if (li instanceof ArrayList) {
            listType = "ArrayList";
        } else if(li instanceof LinkedList) {
            listType = "LinkedList";
        } else {
            listType = "List";
        }

        System.out.printf("=== %s 순회 출력===\n", listType);
        for(int i = 0; i < li.size(); i++) {
            if (i == li.size() - 1) {
                // get(i) : i 인덱스의 요소 반환.
                System.out.println(li.get(i));
            } else {
                System.out.print(li.get(i) + ", ");
            }
        }
        System.out.println("======================");
    }

    public static void main(String[] args) {
        List<String> myList = Arrays.asList("good", "bad", "java", "python");
        // myList.add("great");  // Arrays.asList()로 생성된 리스트는 요소 추가 및 삭제 불가.

        // 에러. 직접 생성자에 요소들을 초기화할 수 없음.
        // List<String> myArrList = new ArrayList<>("good", "bad", "java", "python");
        // List<String> myLinkedList = new LinkedList<>("good", "bad", "java", "python");

        // ArrayList 생성자에 Arrays.asList() 반환값 대입을 통해 
        // 변수 선언과 동시에 초기화 가능.
        List<String> otherList = new ArrayList<>(Arrays.asList("good", "bad", "java", "python"));
        printListElements(otherList);

        myList = new ArrayList<>(myList);  // ArrayList로 변환. 수정 가능.
        myList.add("great");  // 에러 없음.

        printListElements(myList);

        myList = new LinkedList<>(myList);  // ArrayList -> LinkedList 변환.
        myList.remove(myList.size()-1);
        printListElements(myList);
    }
}

```

```java
=== ArrayList 순회 출력===
good, bad, java, python
======================
=== ArrayList 순회 출력===
good, bad, java, python, great
======================
=== LinkedList 순회 출력===
good, bad, java, python
======================
```

# Set<E> interface

Set<E> 인터페이스를 구현하는 컬렉션 클래스들은 다음의 공통점을 가진다. 

- 데이터 입력 순서가 유지되지 않는다. 즉, 순회해보면 입력했을 때의 데이터 순서와 달라진다.
- 중복되는 데이터 입력을 허용하지 않는다. 중복되는 데이터를 입력하면 그 데이터 입력은 무시된다.

해당 인터페이스를 구현하는 컬렉션 클래스로 HashSet과 TreeSet이 있다. 

## HashSet

다음은 HashSet을 이용하는 예제이다.

```java
import java.util.HashSet;
import java.util.Iterator;
import java.util.Set;

class HashSetEx {
    public static void main(String[] args) {
        Set<Integer> mySet = new HashSet<>();
        mySet.add(12);
        mySet.add(3);
        mySet.add(5);
        mySet.add(12);  // 데이터 중복 삽입 시 맨 마지막에 삽입되는 데이터는 무시됨.

        for(Iterator<Integer> itr = mySet.iterator(); itr.hasNext();) {
            System.out.print(itr.next() + "\t");
        }
        System.out.println();

        System.out.println(mySet.size());
    }
}

```

```java
3       5       12
3
```

### hashCode()

중복되는 데이터 입력을 방지하기 위해, 기존의 요소들 중 중복되는 요소가 있는지 먼저 검색해보는 절차가 필요하다. 여기서 해시(hash)를 이용한다. 해시는 key를 받으면 이를 토대로 특정한 해시 알고리즘을 적용하여 특정한 값이 나오도록 하는 함수라고 보면 되겠다. 예를 들어, hash(key)라는 함수가 있다고 가정할 때, 해당 함수의 해싱 알고리즘이 `key % 10`이라고 가정하고 `key=14`를 대입하면 그 값으로 `14 % 10 = 4`가 나온다. 즉, `hash(14) = 4`가 되는 것이다. 이 때, 해시 함수에서 나온 값을 해시 코드라 부른다. 이러한 원리로, 키를 해싱하여 나온 값을 인덱스로 삼아 메모리에 저장하는 “ [해시 테이블 (hash table)](/data%20structure/data-structure-hash-table/) “ 과 동일한 방식을 취한다. 해시를 이용하면 HashSet에 삽입되는 데이터를 키로 삼아 나온 해시 코드를 인덱스로 하여 특정 메모리 공간에 저장하고, 후에 이 데이터를 입력하여 해당 데이터의 존재 여부를 검색할 수 있게 된다. 이를 통해 새로 삽입할 요소가 기존 요소들 중 하나와 중복되는지를 검색할 수 있게 되는 것이다. 

예) `Set(14, 2, 3, 5)`가 있을 때, 14란 요소를 추가하려고 한다. 그러면 먼저 hash(14)를 계산한다. `key % 10` 이란 알고리즘이 적용되었다고 가정할 때, `hash(14) = 4`라는 해시 코드가 나온다. 이 해시 코드를 인덱스로 삼아 `Set[4]` 인덱스에 위치한 메모리 공간에 접근한다. 그런데 이미 이전에 14란 값을 입력하였으므로 해당 공간에 이미 그 값이 존재한다. 이를 통해 중복 여부를 검사할 수 있다. 

자바에서는 이러한 해시 함수를 Object 클래스의 hashCode() 메서드를 통해 제공하고 있다. 이를 오버라이딩하여 자신만의 해시 알고리즘을 적용하여 해시 코드를 반환하도록 할 수 있다. 이를 통해 각 객체만의 고유한 해시 코드 값을 부여하여 서로 다른 객체들을 구분하는 기능을 구현할 수 있을 것이다. hashCode()는 그 자체로는 인자를 전달받지 않기에, 해당 객체 내부의 멤버 변수를 해시의 키로 사용하는 것이 좋다. 만약 자신이 직접 해시 알고리즘을 짜기 싫고, 이미 정의된 해시 알고리즘을 사용하고자 한다면 j`ava.util.Objects.hash(가변 인수)` 메서드를 hashCode() 내부에서 사용하면 된다. 해당 메서드는 입력된 인자들을 토대로 해시 코드를 반환해준다. 

HashSet에서는 데이터의 중복을 방지하기 위해 검색 시 hashCode()로 나온 해시 코드를 인덱스로 하는 메모리 공간에 접근한다. 이 때, 해당 메모리 공간에는 해시 충돌로 인해 여러 개의 서로 다른 값들이 존재할 수 있다. 그래서 equals() 메서드를 호출하여 그 중에서 새로 추가할 값과 동일한 값이 있는지를 확인한다. 

이로 인해, 만약 개발자가 직접 클래스를 정의하고, 이로부터 생성된 객체들을 HashSet에 저장할 때 객체의 중복 검색을 위해서는 해당 클래스에서 hashCode() 메서드와 equals() 메서드를 오버라이딩을 해야 한다. 

다음은 유저의 닉네임과 고유 번호를 담은 클래스 User9를 정의하고, 이 인스턴스들을 HashSet 자료구조에 저장하는 예제이다. User9 내부에는 HashSet 내부의 데이터 중복 방지용 검색을 위해 hashCode()와 equals() 메서드를 오버라이딩하고 있다. 

```java
import java.util.HashSet;

class User9 {
    private String nickName;
    private int uniqueCode;

    User9(String nickName, int uniqueCode) {
        this.nickName = nickName;
        this.uniqueCode = uniqueCode;
    }

    @Override
    public String toString() {
        return "(" + this.nickName + " : " + this.uniqueCode + ")";
    }

    /*
    // 이미 정의된 해시 알고리즘 이용.
    // #1
    @Override
    public int hashCode() {
        return java.util.Objects.hash(this.nickName, this.uniqueCode);
    }*/

    
    // 사용자 정의 해시 코드
    // #2
    @Override
    public int hashCode() {
        return this.uniqueCode % 10;
    }

    @Override
    public boolean equals(Object obj) {
        if(this.uniqueCode == ((User9)obj).uniqueCode) {
            return true;
        } else {
            return false;
        }
    }
}

class HashSetWithMyObj {
    public static <E> void printAllElementsInHashSet(HashSet<E> s) {
        System.out.print("(");
        for(E element : s) {
            System.out.print(element + ", ");
        }
        System.out.printf(") : %d\n", s.size());
    }

    public static void main(String[] args) {
        HashSet<User9> mySet = new HashSet<>();
        mySet.add(new User9("good123", 1212));
        mySet.add(new User9("bowWow9912", 1212));
        mySet.add(new User9("good123", 1000));

        printAllElementsInHashSet(mySet);
    }
}

```

```java
((good123 : 1000), (good123 : 1212), ) : 2
```

위 코드에서, #2 영역의 hashCode() 메서드에서는 멤버 변수 중 하나인 uniqueCode의 값만을 이용하여 해시 코드를 생성하게끔 설계되었다. 그리고 이 uniqueCode 값을 비교하는 equals() 메서드를 재정의하였다. 그 결과, `User9("bowWow9912", 1212)` 은 `User9("good123", 1212)` 와 비교하여 nickName 값은 서로 다르지만, uniqueCode 값이 중복되기에 후에 입력된 `User9("bowWow9912", 1212)` 은 입력되지 않은 것을 확인할 수 있다. 그래서 3개의 객체 데이터를 입력했지만 결과적으로는 2개의 객체 데이터들만 HashSet에 입력된 상태이다. 

반면, #2 영역을 주석 처리한 후, #1 영역을 주석 해제하여 실행하면 다음의 결과를 얻는다. 

```java
((good123 : 1212), (good123 : 1000), (bowWow9912 : 1212), ) : 3
```

#1 영역의 hashCode() 메서드에서는 멤버 변수 nickName과 uniqueCode 이 둘을 모두 활용하고 있다. 따라서, 이 두 변수값이 모두 동일한 객체만 중복 처리된다. 위 예제에서 입력된 3개의 객체들은 모두 nickName과 uniqueCode 이 둘 모두 겹치는 객체는 존재하지 않는다. 따라서 3개의 객체 데이터 모두 HashSet에 입력될 수 있었다. 

## TreeSet

TreeSet은 HashSet과 동일하나, 출력 시 그 출력값들을 먼저 정렬한 채로 출력해주도록 하는 클래스이다. 

> 주의 : 데이터 입력 순으로 출력하는 것이 아니라, 데이터들을 정렬한 순서대로 출력하는 것이다. 이를 헷갈리지 않도록 하자.
> 

다음은 TreeSet을 이용하여 데이터를 입력하고, 이를 순회하여 출력하도록 하는 예제이다. 

```java
import java.util.TreeSet;

class TreeSetEx {
    public static <E> void printAllInTreeSet(TreeSet<E> ts) {
        System.out.print("(");
        for(E element: ts) {
            System.out.print(element + ", ");
        }
        System.out.print(")  : " + ts.size() + "\n");
    }
    public static void main(String[] args) {
        TreeSet<Integer> treeSet = new TreeSet<>();

        treeSet.add(12);
        treeSet.add(23);
        treeSet.add(5);
        treeSet.add(3);
        treeSet.add(13);
        treeSet.add(3);  // 중복되어서 무시됨.

        printAllInTreeSet(treeSet);
    }
}

```

```java
(3, 5, 12, 13, 23, )  : 5
```

중복되는 데이터는 입력되지 않음과 동시에 출력 시 데이터들의 오름차순으로 출력되었다. 

TreeSet에서는 데이터의 정렬을 위해 이진 탐색 트리(Binary Search Tree, BST)를 사용한다. 

만약 TreeSet 내부에 사용자 정의 클래스의 인스턴스들이 데이터로 삽입되는 경우, 해당 클래스에는 반드시 객체들을 어떤 기준으로 정렬할 것인지를 명시해야한다. 이를 위해서, Comparable 인터페이스에 제네릭을 적용한 Comparable<T> 인터페이스의 compareTo(T obj) 메서드를 오버라이딩해야 한다. 또는, 만약 해당 클래스에 이미 Comparable<T> 인터페이스의 compareTo() 메서드가 정의되어 있음에도 이를 무시하고 새로운 정렬 기준을 적용하고자 한다면, 정렬 기준이 될 클래스를 정의한 후, 해당 클래스에서 Comparator<T> 인터페이스의 compare() 메서드를 오버라이딩하고, TreeSet() 생성자에 해당 클래스의 인스턴스를 대입하면 된다. 

Comparable 인터페이스는 java.lang 패키지, Comparator는 java.util 패키지에 포함되어 있다. 

compareTo(), compare()에서 반환값을 어떻게 설정하느냐에 따라 그 정렬 기준도 오름차순일지 내림차순일지 결정된다. 

| 정렬 순서 | 방식 |
| --- | --- |
| 오름차순 | - 객체 A의 compareTo() 인자로 전달된 객체 B의 특정 멤버 변수 값이 A의 변수 값보다 작을 때 양수 반환. 
||⇒ A.var > B.var → return 1 <br> - A.var < B.Var → return -1 <br> - A.var = B.var = return 0 |
| 내림차순 | - A.var > B.var → return -1 <br> - A.var < B.var → return 1 <br> - A.var = B.var → return 0 |

다음은 사용자 정의 클래스에 Comparable<T>의 compareTo()를 오버라이딩한 후, 이 객체들을 담은 TreeSet을 출력하는 예제이다. 

```java
import java.util.Set;
import java.util.TreeSet;

class User10 implements Comparable<User10>{
    private String nickName;
    private int uniqueCode;

    User10(String nickName, int uniqueCode) {
        this.nickName = nickName;
        this.uniqueCode = uniqueCode;
    }

    @Override
    public String toString() {
        return "(" + this.nickName + " : " + this.uniqueCode + ")";
    }

    @Override
    public int compareTo(User10 o) {
        return this.uniqueCode - o.uniqueCode;  // 고유번호 오름차순 정렬
        // return o.uniqueCode - this.uniqueCode;  // 고유번호 내림차순 정렬.
        // return this.nickName.compareTo(o.nickName);  // 닉네임 오름차순 정렬. 
        // return o.nickName.compareTo(this.nickName);  // 닉네임 내림차순 정렬.
    }
}

class TreeSetWithCompareTo {
    public static <E> void printAllInSet(Set<E> s) {
        System.out.print("(");
        for(E element : s) {
            System.out.print(element + ", ");
        }
        System.out.print(") : " + s.size() + "\n");
    }
    public static void main(String[] args) {
        Set<User10> treeSet = new TreeSet<>();
        treeSet.add(new User10("good123", 1212));
        treeSet.add(new User10("bowWow9912", 1302));
        treeSet.add(new User10("titan999", 1000));

        printAllInSet(treeSet);
    }
}

```

```java
((titan999 : 1000), (good123 : 1212), (bowWow9912 : 1302), ) : 3 // 고유번호 오름차순 정렬 
((bowWow9912 : 1302), (good123 : 1212), (titan999 : 1000), ) : 3 // 고유번호 내림차순 정렬.
((bowWow9912 : 1302), (good123 : 1212), (titan999 : 1000), ) : 3 // 닉네임 오름차순 정렬.
((titan999 : 1000), (good123 : 1212), (bowWow9912 : 1302), ) : 3 // 닉네임 내림차순 정렬.
```

다음은 User 객체에 이미 Comparable<T> 인터페이스의 compareTo() 메서드가 재정의되어 있음에도, 새로운 정렬 기준을 원해서 Comparator<T> 인터페이스의 compare() 메서드를 구현하는 새 클래스 StringLenComparator를 정의하고, 이 클래스의 인스턴스를 TreeSet 생성자에 대입함으로써 새로운 정렬 기준을 적용한 예제이다. 

```java
import java.util.Comparator;
import java.util.Set;
import java.util.TreeSet;

class User11 implements Comparable<User11>{
    String nickName;
    int uniqueCode;

    User11(String nickName, int uniqueCode) {
        this.nickName = nickName;
        this.uniqueCode = uniqueCode;
    }

    @Override
    public String toString() {
        return "(" + this.nickName + " : " + this.uniqueCode + ")";
    }

    @Override
    public int compareTo(User11 o) {
        return this.uniqueCode - o.uniqueCode;  // 고유번호 오름차순 정렬
        // return o.uniqueCode - this.uniqueCode;  // 고유번호 내림차순 정렬.
        // return this.nickName.compareTo(o.nickName);  // 닉네임 오름차순 정렬. 
        // return o.nickName.compareTo(this.nickName);  // 닉네임 내림차순 정렬.
    }
}

class StringLenComparator implements Comparator<User11> {
    @Override
    public int compare(User11 o1, User11 o2) {
        // 문자열의 길이가 같은 데이터는 중복 처리되어 추가되지 않음.
        return o1.nickName.length() - o2.nickName.length();
    }
}

class TreeSetWithCompare {
    public static <E> void printAllInSet(Set<E> s) {
        System.out.print("(");
        for(E element : s) {
            System.out.print(element + ", ");
        }
        System.out.print(") : " + s.size() + "\n");
    }

    public static void main(String[] args) {
        Set<User11> treeSet = new TreeSet<>(new StringLenComparator());
        treeSet.add(new User11("good123", 1212));
        treeSet.add(new User11("bowWow9912", 1302));
        treeSet.add(new User11("titan999", 1000));

        // "good123"과 길이가 동일함. 따라서 이 객체는 TreeSet에 추가되지 않음.
        treeSet.add(new User11("great12", 2000));
        
        printAllInSet(treeSet);
    }    
}

```

```java
((good123 : 1212), (titan999 : 1000), (bowWow9912 : 1302), ) : 3
```

Comparable 인터페이스만 적용한 이전 예제 5-2의 출력 결과와 비교해보아도 그 결과가 다름을 알 수 있다. 또한, main 메서드에서 nickName 멤버 변수값의 문자열 길이가 같은 객체를 삽입하려고 해도 이미 기존에 같은 문자열 길이를 가지는 객체가 존재하기에 이를 중복 처리하여 무시한 것을 통해, User 객체 내부에 정의된 Comparable 인터페이스의 compareTo 메서드가 아닌, Comparator 인터페이스를 구현하는 StringLenComparator 클래스가 우선 적용된 것을 확인할 수 있다.

# 컬렉션 클래스에 대한 Iterator 사용

앞서 컬렉션 프레임워크 내 인터페이스들의 상속 관계에서 보았듯, ArrayList, LinkedList 등의 컬렉션 클래스들은 궁극적으로 Iterable 인터페이스를 구현하기에, 순회 시 Enhanced for문과 Iterator 반복자를 사용할 수 있다. 

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;

class IteratorEx {
    public static <T> void printListElements(List<T> li) {
        System.out.println("=== Enhanced for ===");
        for(T element : li) {
            System.out.print(element + ", ");
        }
        System.out.println();
        System.out.println("=====================");
    }

    public static void printListElementsWithIter(Iterator itr) {
        System.out.println("=== Iterator ===");
        while(itr.hasNext()) {
            System.out.print(itr.next() + ", ");
        }
        System.out.println();
        System.out.println("=================");
    }

    public static void testListMethods(List li) {
        if (li instanceof ArrayList) {
            System.out.println("=== ArrayList 테스트 ===");
        } else if (li instanceof LinkedList) {
            System.out.println("=== LinkedList 테스트 ===");
        } else {
            System.out.println("=== List 테스트 ===");
        }

        li.add("good");  // 값을 맨 마지막에 추가.
        li.add("well done");
        li.add(1, "bad");  // 해당 index에 값 삽입.

        printListElements(li);

        Iterator<String> itr = li.iterator();
        printListElementsWithIter(itr);

        System.out.println("=======================");
        System.out.println();
    }

    public static void main(String[] args) {
        List<String> myArrList = new ArrayList<>();
        List<String> myLinkedList = new LinkedList<>();

        testListMethods(myArrList);
        testListMethods(myLinkedList);
    }
}

```

```java
=== ArrayList 테스트 ===
=== Enhanced for ===
good, bad, well done,
=====================
=== Iterator ===
good, bad, well done,
=================
=======================

=== LinkedList 테스트 ===
=== Enhanced for ===
good, bad, well done,
=====================
=== Iterator ===
good, bad, well done,
=================
=======================
```

위 코드에서 나온 내용들을 정리하면 다음과 같다. 

- Iterable 인터페이스를 구현한 컬랙션 클래스에서는 iterator() 메서드를 통해 반복자 iterator를 얻을 수 있다. 이는 해당 자료구조 내부의 모든 요소들을 순차적으로 순회할 수 있게 해주는 객체라 보면 된다.
- iterator() 메서드로 반환받은 Iterator 객체에는 다음의 메서드들 존재한다.
    - Iterator.hasNext() : 순회할 요소가 아직 남았는지 확인하고 이를 boolean 형으로 반환.
    - Iterator.next() : 순회할 다음 요소를 반환.

분량이 길어질 것 같아서 이후의 내용들은 다른 페이지에서 계속 작성하겠다.

---
References

[1] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)