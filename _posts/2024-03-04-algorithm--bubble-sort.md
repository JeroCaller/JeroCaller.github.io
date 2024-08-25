---
title: "[알고리즘][Java] 버블 정렬 (Bubble Sort)"
category: "Algorithm"
tag: ["algorithm", "java", "sort", "정렬", "bubble sort", "버블 정렬"]
---

```java
{2, 5, 4, 1, 3}
```

위와 같이 무작위의 순서대로 나열된 자연수들로 이루어진 배열이 있다고 해보자. 이를 오름차순 정렬하고자 할 때, 버블 정렬은 다음의 과정을 따른다. 

1. 인덱스 0부터 시작하여 첫 인덱스와 인접한 오른쪽 인덱스의 요소 값끼리 비교한 후, 왼쪽에 있는 요소의 값이 더 클 경우 이 둘의 위치를 바꾼다. 그렇지 않은 경우 그대로 둔다. 
2. 1번 과정을 마지막 요소에 다다를 때까지 반복한다. 한 번 배열 내 요소들을 모두 순회하면 배열 내에서 가장 큰 값의 요소가 배열의 맨 오른쪽 끝에 위치하게 될 것이다. 
3. 위 과정들을 반복하면 배열의 오른쪽 부분에 가장 큰 숫자 순으로 이동되어 더 이상 자리 바꿈이 없어질 것이다. 이를 단 한 번도 자리 바꿈이 일어나지 않을 때까지 반복한다. 

위 과정을 앞선 배열을 예로 들어 시각화해보면 다음과 같다. 현재 선택된 요소들을 오른쪽에 O로 표현하고, 이미 정렬되어 더 이상 자리 바꿈이 필요하지 않은 요소 오른쪽에는 X로 표현하겠다. 

```java
{2, 5, 4, 1, 3}  초기 배열
{2O, 5O, 4, 1, 3} i=0 요소와 i=1 요소 간 비교. 이미 오름차순이라서 교환되지 않음.
{2, 5O, 4O, 1, 3} i=1 요소와 i=2 요소 간 비교. 교환 작업 일어남.
{2, 4, 5O, 1O, 3}
{2, 4, 1, 5O, 3O}
{2, 4, 1, 3, 5X}  한 바퀴 끝. 배열 내 가장 큰 수인 5는 더 이상 자리 교환 필요 없음.
{2O, 4O, 1, 3, 5X}
{2, 4O, 1O, 3, 5X}
{2, 1, 4O, 3O, 5X}
{2, 1, 3, 4X, 5X}  두 바퀴 끝. 4는 5와 비교할 필요가 없다. 5는 이미 가장 큰 수로 정렬되었기 때문.
{2O, 1O, 3, 4X, 5X}
{1, 2O, 3O, 4X, 5X}
{1, 2, 3X, 4X, 5X} 세 바퀴 끝.
{1O, 2O, 3X, 4X, 5X}
{1X, 2X, 3X, 4X, 5X} 네 바퀴 끝.
```

위와 같은 버블 정렬 알고리즘을 다음과 같이 자바로 구현하였다.

```java
class BubbleSort {
    public static void executeBubbleSort(int[] arr) {
        for(int i = 0; i < arr.length; i++) {
            for(int j = 0; j < arr.length - 1 - i; j++) {
                if(arr[j] > arr[j+1]) {
                    int temp = arr[j+1];
                    arr[j+1] = arr[j];
                    arr[j] = temp;
                }
            }
        }
    }
}

```

위 예제를 다른 파일에서 실행해보았다. 

```java
import java.util.Random;

class SortMain {
    public static void printIntArray(int[] arr, boolean addNewLine) {
        System.out.print("{");
        for(int num : arr) {
            System.out.print(num + ", ");
        }
        System.out.print("}");
        if(addNewLine) {
            System.out.println();
        }
    }

    public static int[] getRandIntArray(int len, int rangeStart, int rangeEnd) {
        Random rand = new Random();
        int[] arr = new int[len];

        for(int i = 0; i < len; i++) {
            arr[i] = rand.nextInt(rangeStart, rangeEnd);
        }
        return arr;
    }

    public static void main(String[] args) {
        int[] arr = getRandIntArray(10, 0, 11);

        System.out.println("정렬 전");
        printIntArray(arr, true);

        BubbleSort.executeBubbleSort(arr);
        System.out.println("정렬 후");
        printIntArray(arr, true);
        System.out.println("정렬 확인 : " + SortTester.isSortedAsce(arr));
    }
}

```

```java
정렬 전
{1, 0, 0, 3, 8, 7, 10, 8, 1, 3, }
정렬 후
{0, 0, 1, 1, 3, 3, 7, 8, 8, 10, }
정렬 확인 : true
```

참고로, 오름차순 정렬되었는지 확인하는 SortTester 클래스는 다음과 같이 구현하였다.

```java
class SortTester {
    /**
     * 주어진 정수 배열이 오름차순으로 정렬되었는지 확인하는 메서드.
     * @param arr  정수 배열.
     * @return  true : 오름차순으로 정렬됨을 확인. false : 정렬 안됨.
     */
    public static boolean isSortedAsce(int[] arr) {
        for(int i = 0; i < arr.length - 1; i++) {
            if(arr[i] > arr[i+1]) {
                return false;
            }
        }
        return true;
    }
}

```

버블 정렬에 필요한 비교 연산은 최악의 경우, 맨 처음에는 N-1번 필요하고, 그 다음에는 N-2, N-3, …, 1번이 된다. 즉, (N-1) + (N-2) + (N-3) + … + 1 = \\(\frac{N(N-1)}{2}\\) 번의 비교 연산이 필요하다. 즉, 최악의 경우 시간복잡도는 \\(O(N^2)\\)이다. 

---

References

[1] 이재환, “이재환의 자바 프로그래밍 입문”