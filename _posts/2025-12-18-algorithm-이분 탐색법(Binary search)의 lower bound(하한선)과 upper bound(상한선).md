---
title: "[알고리즘] 이분 탐색(Binary search)의 lower bound(하한선)와 upper bound(상한선)"
category: "Algorithm"
tag: ["Algorithm", "Baekjoon", "백준", "자바", "Java", "이분 탐색법", "이진 탐색법", "Binary Search", "Search", "Lower Bound", "Upper Bound"]
---


이분 탐색(Binary search)은 숫자로 이루어진 정렬된 배열 내에서 특정 값을 찾기 위한 방법 중 하나로, 탐색 범위를 절반씩 줄여나가며 탐색하는 방법이다. 일반적으로 알려지 있는 순차적 탐색법이 최악의 경우 O(N)의 성능을 내는 것에 비해 이분 탐색은 O(log N)이어서 더 빠른 검색 성능을 보여준다. 이분 탐색의 자세한 원리는 이전에 “[이진 배열 탐색](/algorithm/algorithm-binary-array-search/)” 글에서 다룬 적 있으니 참고. 

그런데 이전 글에서 다룬 이분 탐색법에 대해서는 한 가지 암묵적인 전제가 있었으니, 바로 배열 내에 중복되는 숫자가 없다는 가정이었다. 그런데 만약 배열 내에서 찾고자 하는 수가 중복되어 나타날 때에는 어떻게 해야할까? 예를 들어 배열이 `{1, 2, 3, 3, 3, 5, 7}` 과 같이 있을 때 3이 위치한 인덱스를 무엇으로 반환해야할까? 한 가지 분명한 것은 이를 실행해주는 함수를 실행할 때마다 그 출력값이 항상 일정해야할 것이라는 점이다. 3이 여러 개 있다는 이유로 매번 똑같은 입력값에 대해 실행할 때마다 어떨 때는 2, 또 다를 때에는 4를 반환하는 비일관성을 보여선 안될 것이다. 

사실 이분 탐색법에는 이를 해결할 세부적인 방법이 있다. 바로 경계값에 있는 수의 인덱스를 반환하도록 하는 방법이다. 위에서 말한 문제에 대해 이분 탐색법에서는 lower bound와 upper bound 이 두 가지 방식으로 해결한다. lower bound 방식은 찾고자 하는 숫자가 최초로 등장하는 곳의 인덱스를 반환하도록 하는 방식이다. 위에서 든 예시에서는 가장 먼저 발생하는 3의 위치인 2라는 수를 반환하게 된다. 반면 upper bound 방식은 찾고자 하는 숫자를 초과하는 숫자들 중 가장 작은 수의 인덱스를 반환하는 방식이다. 위에서 든 예시에서는 3을 초과하는 숫자 `5, 7`  중에서 가장 작은 5의 인덱스 5를 반환하게 된다. 

lower bound와 upper bound를 구체적으로 어떻게 구현하는지를 살펴보기 앞서, 이들과 비교하기 위해 먼저 기본적으로 알고 있던 일반적인 이분 탐색법을 구현하는 코드를 보겠다. 

```java
/**
 * <p>주어진 오름차순 배열 내에서 특정 값을 찾아 그 인덱스를 반환</p>
 * <p>주어진 배열 내에 찾고자 하는 정확한 값이 없을 경우, -1을 반환함.</p>
 *
 * @param array 검색 대상 배열. 반드시 오름차순 정렬되어있어야 함.
 * @param targetValue 배열 내에서 찾고자 하는 값
 * @return 배열 내 찾고자 하는 값의 인덱스. 찾고자 하는 값을 정확히 찾지 못한다면 -1을 반환.
 */
public static int search(int[] array, int targetValue) {
    int leftIdx = 0;
    int rightIdx = array.length - 1;
    int midIdx = (leftIdx + rightIdx) / 2;

    while (leftIdx <= rightIdx) {
        if (array[midIdx] == targetValue) {
            return midIdx;
        }

        if (array[midIdx] > targetValue) {
            rightIdx = midIdx - 1;
        } else if (array[midIdx] < targetValue) {
            leftIdx = midIdx + 1;
        }

        midIdx = (leftIdx + rightIdx) / 2;
    }

    return -1;
}
```

코드 1-1. 이분 탐색법을 자바로 구현.

위 코드는 중복되는 값이 전혀 없는 배열에 대해서는 문제없이 동작하겠지만, 만약 찾고자 하는 값이 배열 내에 중복되어 등장하게 된다면 문제가 발생한다. 그것은 바로 배열 내 위치에 따라 반환되는 결과값이 달라져 일관성이 깨진다는 것이다. 위 메서드에 대해 각각 배열 `{1, 2, 3, 3, 3, 3, 5, 6, 8}` 과 `{1, 2, 3, 3, 3, 3, 5, 6, 8, 9, 10, 11, 12, 13}` , 그리고 검색 대상을 “3”으로 하고 돌려보자. 이 두 배열 모두 3이란 값이 중복되어 나타나며, 이 두 배열 모두 그 중복되는 “3”의 개수가 총 4개로 동일하고, “3”이 끝나기까지는 배열에 놓인 순서도 동일하다. 그러나 위 함수로 구현한 이분 탐색법을 적용하면 전혀 다른 값이 나온다. 전자의 경우, 이분 탐색의 맨 첫 번째 과정에서 배열의 중간값 `midIdx` 에 해당하는 값으로 “3”이 바로 나오므로 결국 “4”라는 인덱스가 반환된다. 반면 후자의 배열의 경우, 탐색 범위가 `{1, 2, 3, 3, 3, 3}` 으로 절반으로 줄어드며, 이 안에서 결국 `midIdx` 가 2인 위치를 탐색하게 되므로 결국 “2”라는 인덱스 값을 반환하게 된다. 즉 전자는 3번째의 “3”을, 후자는 첫 번째의 “3”을 찾게 되는 것이다. 

Lower bound와 upper bound 방식은 이러한 중복 문제를 해결한다. 

Lower bound는 찾고자 하는 값의 시작 위치를 반환하는 알고리즘이다. 위에서 든 예시의 경우, 무조건 맨 처음에 등장하는 “3”의 인덱스 값을 반환하도록 한다. 다음은 이를 구현한 코드이다.

```java
/**
 * <p>Binary search의 Lower bound 방식 탐색 메서드</p>
 * <p>
 *     주어진 배열 내에 찾고자 하는 값이 중복되어 존재할 경우,
 *     찾고자 하는 값들 중 맨 왼쪽에 있는 값의 인덱스를 반환한다.
 * </p>
 * <p>
 *     찾고자 하는 정확한 값이 없다면, 찾고자 하는 값보다 최초로 큰 값의
 *     인덱스를 반환한다.
 * </p>
 * <p>
 *     예) <code>{1, 3, 5, 6}</code>에서 4를 찾고자 하는 경우, 5가 4보다 더 큰 수 중 가장 작은
 *     수이므로 이 수의 인덱스인 "2"를 반환함.
 * </p>
 * <p>
 *     찾고자 하는 값이 주어진 배열의 최대값보다 더 클 경우, 배열의 길이를 반환함.
 * </p>
 *
 * @param array 찾고자 하는 값이 포함된 오름차순 정렬된 정수 배열
 * @param targetValue 배열 내에서 찾고자 하는 값
 * @return 배열 내 찾고자 하는 값의 인덱스. 정확한 값이 없다면 찾고자 하는 값보다 큰 수들 중
 * 가장 작은 수의 인덱스를 반환. 찾고자 하는 값이 배열 내 최대값보다 클 경우 배열 길이를 반환.
*/
public static int getLowerBoundIndexOf(int[] array, int targetValue) {
    int leftIdx = 0;
    int rightIdx = array.length;
    int midIdx = (leftIdx + rightIdx) / 2;

    while (leftIdx < rightIdx) {
        if (array[midIdx] >= targetValue) {
            rightIdx = midIdx;
        } else if (array[midIdx] < targetValue) {
            leftIdx = midIdx + 1;
        }

        midIdx = (leftIdx + rightIdx) / 2;
    }

    return leftIdx;
}
```

코드 1-2. 이분 탐색법의 lower bound 방식을 구현한 코드

lower bound와 upper bound에서는 이전의 이분 탐색법과는 달리, 찾고자 하는 값이 배열 내 최대값보다 더 큰 경우라면 “배열 내 최대값을 넘긴다”라는 의미에서 배열의 길이를 반환하도록 한다. 따라서 `rightIdx` 의 초기값을 배열의 길이인 `array.length` 로 잡은 것이다. 그리고 앞선 코드 1-1로 보인 일반적인 이분탐색법과 달리, lower bound에서는 찾고자 하는 값을 찾았다고 하더라도 이를 바로 반환하지 않고 범위를 배열의 왼쪽으로 더 좁혀 계속 찾게 한다. 찾고자 하는 값을 찾았더라도 그보다 더 앞선 위치에 중복된 값이 있을 수도 있기 때문이다. 그리고, `leftIdx` 와 `rightIdx` 의 다음 값을 잡는 방법도 조금 다르다. 현재 `midIdx` 의 위치에 있는 값이 정확히 찾고자 하는 값 `targetValue` 일 경우를 대비해 `rightIdx` 의 다음 값을 현재 `midIdx` 값을 포함한 배열 내 왼쪽 부분으로 잡는 반면, 반대로 현재 `midIdx` 위치의 값보다 `targetValue`의 값이 더 클 경우 현재 `midIdx` 는 과감히 배제하고 배열의 다음 오른쪽 부분을 택하는 방식이다. lower bound 방식에서는 찾고자 하는 값이 중복되어 있을 경우 첫 번째로 나타나는 값, 즉 맨 왼쪽에 있는 값의 인덱스를 반환하기에 여기서는 `leftIdx` 를 반환하도록 하였다. 

위 방식을 사용하면 2가지 이점이 있다. 하나는 배열 내 찾고자 하는 값이 중복되어 존재하더라도 항상 중복되는 값들의 첫 번째 값의 인덱스를 반환한다는 것이다. 이는 앞서 제시헀던 일관성의 문제를 해결한다. 또 하나는 비록 배열 내에 찾고자 하는 정확한 값이 없더라도 위 알고리즘에서는 “찾고자 하는 값보다 큰 수들 중 가장 작은 수의 인덱스”를 반환한다는 것이다. 만약 배열 `{1, 2, 3, 3, 3, 5, 6, 8}` 이 주어지고 여기서 “4”를 찾는다면, “4”보다 큰 수인 `5, 6, 8` 중 가장 작은 수인 `5` 의 인덱스를 반환한다는 것이다. 만약 이 위치에 “4”를 넣고, 그 뒤에 있는 수들을 뒤로 한 칸씩 땡기더라도 `{1, 2, 3, 3, 3, 4, 5, 6, 8}` 형태로 오름차순을 그대로 유지할 수 있다. 상황에 따라서는 정확한 값을 찾지 못했다는 이유로 `-1` 만 반환하도록 하기 보다는 “찾고자 하는 수를 이 배열에 넣어도 오름차순을 깨트리지 않는 위치는?”이란 질문에도 답할 수 있게 되어 더 유용할 수도 있는 것이다. 

Upper bound 방식은 찾고자 하는 값보다 처음으로 큰 수의 인덱스를 반환하는 알고리즘이다. 이를 코드로 구현하면 다음과 같다.

```java
/**
 * <p>Binary search의 Upper bound 방식의 탐색 메서드</p>
 * <p>
 *     주어진 배열 내에서 찾고자 하는 값보다 큰 수들 중 가장 작은 수의 인덱스를 반환한다.
 * </p>
 * <p>
 *     예) <code>{1, 2, 3, 3, 3, 5, 7, 10}</code> 배열이 주어졌고,
 *     찾고자 하는 값이 3인 경우, 이 값보다 큰 수 <code>{5, 7, 10}</code> 중
 *     가장 작은 수인 5의 인덱스 "5"를 반환한다.
 * </p>
 * <p>
 *     만약 배열 내에 찾고자 하는 값이 없는 경우, 찾고자 하는 값보다 큰 수들 중
 *     가장 작은 수의 인덱스를 반환한다.
 * </p>
 * <p>
 *     예) <code>{1, 3, 5, 6}</code>에서 4를 찾고자 하는 경우, 5가 4보다 더 큰 수 중 가장 작은
 *     수이므로 이 수의 인덱스인 "2"를 반환함.
 * </p>
 * <p>
 *     찾고자 하는 값이 배열 내 최대값보다 큰 경우, 배열 길이를 반환한다.
 * </p>
 *
 * @param array 탐색 대상이 될 오름차순 정렬된 배열
 * @param targetValue 배열 내에서 찾고자 하는 값
 * @return 찾고자 하는 값보다 더 큰 수들 중 가장 작은 수의 인덱스를 반환. 찾고자 하는 값이
 * 배열 내 최대값보다 더 큰 경우 배열의 길이를 반환.
*/
public static int getUpperBoundIndexOf(int[] array, int targetValue) {
    int leftIdx = 0;
    int rightIdx = array.length;
    int midIdx = (leftIdx + rightIdx) / 2;

    while (leftIdx < rightIdx) {
        if (array[midIdx] > targetValue) {
            rightIdx = midIdx;
        } else if (array[midIdx] <= targetValue) {
            leftIdx = midIdx + 1;
        }

        midIdx = (leftIdx + rightIdx) / 2;
    }

    return rightIdx;
}
```

코드 1-3. Upper bound를 구현한 코드

큰 틀에서는 앞선 lower bound 방식과 거의 비슷하나 세부적으로 보면 다른 점이 있다. 

- 조건문 속 등호(`=`)의 위치가 다르다. lower bound 방식에서는 `array[midIdx] >= targetValue` 에 포함되어 있고, 이 조건을 만족하면 `rightIdx = midIdx` 가 되도록 하였다. 반면 upper bound에서는 `array[midIdx] <= targetValue` 에 등호를 포함시켰으며, 이 조건을 만족하면 `leftIdx = midIdx + 1` 이 되도록 하였다. 이렇게 차이나는 것은 upper bound 방식에서는 현재 위치에서 찾고자하는 값을 찾았다 하더라도 그 오른쪽에 같은 값이 중복되어 나타날 수 있기 때문에 이를 찾고자 하여 차이가 나는 것이다.
- 어떻게 보면 “최대 인덱스”를 찾는 문제이기에 `rightIdx` 를 반환하도록 하였다.
    - 필자가 참고했던 다른 자료들에서는 어떤 곳에서는 lower, upper 두 방식 모두 `rightIdx` 를 반환하게 하고, 또 어떤 곳에서는 lower 방식에서는 `leftIdx` 를, upper 방식에서는 `leftIdx - 1` 를 반환하는 방식을 취하고 있었다. 필자의 경우, lower 방식에서는 `leftIdx` , upper 방식에서는 `rightIdx` 를 반환하도록 하였다. 그렇게 해도 결과는 같았다. 결과가 같다면 이왕이면 외우기 쉽게 “lower 방식에서는 최소 인덱스를 반환하기에 `leftIdx` 를, upper 방식에서는 최대 인덱스를 반환하기에 `rightIdx` 를 반환하도록 하는 것”이 좋겠다 판단하였다.

위 함수에 대해 배열 `{1, 2, 3, 3, 3, 3, 5, 6, 8}` 에 대해 “3”을 찾도록 하면, 중복되는 “3”들 중 가장 뒤에 있는 “3”의 다음 요소인 “5”의 인덱스 6을 반환하게 된다. 

Lower bound 방식이 중복되는 값의 첫 번째 값의 인덱스를, upper bound 방식에서는 중복되는 값 바로 다음 값의 인덱스를 반환한다는 사실을 이용하면, 배열 내 찾고자 하는 값이 총 몇 개 있는지도 알 수 있게 된다. 

```java
/**
 * <p>배열 내에서 찾고자 하는 값의 개수를 반환한다</p>
 * 
 * @param array 탐색 대상이 될 오름차순 정렬된 배열
 * @param targetValue 찾고자 하는 값
 * @return 배열 내 찾고자 하는 값의 개수
 */
public static int getLengthOfEqualValues(int[] array, int targetValue) {
    return getUpperBoundIndexOf(array, targetValue) - getLowerBoundIndexOf(array, targetValue);
}
```

코드 1-4. lower bound 및 upper bound 방식을 결합하여 배열 내 찾고자 하는 값의 개수를 반환하는 함수. 

순전히 `upper bound의 결과` - `lower bound의 결과` 를 반환하기만 해도 배열 내 찾고자 하는 값의 개수를 알아낼 수 있다. 앞서 든 예시인 배열 `{1, 2, 3, 3, 3, 3, 5, 6, 8}` 에서도 찾고자 하는 값 “3”에 대해, upper bound의 결과인 6에서 lower bound의 결과인 2를 빼면 4가 나오며, 이는 해당 배열 내 “3”의 개수가 4개인 점과 동일하다는 것을 알 수 있다. 

한 편, lower bound와 upper bound 방식 모두 이분 탐색법을 기반으로 하고 있기에 둘 다 모두 시간 복잡도 O(log N)를 가진다. 다만 실제로는 값을 일단 찾으면 반환하는 기존 방식에 비해 “중복되는 값이 더 있는지 여부”를 확인하기 위해 조금 더 탐색을 하므로 기본적인 이분 탐색법에 비해서는 약간의 연산 작업이 더 들 것이다. 그럼에도 배열 내 중복되는 값들에 대한 문제를 해결할 수 있고, 여전히 O(log N)의 성능을 가져 조금의 연산만 반복해주면 되기에 성능적으로 큰 문제가 되지 않을 것이다. 

<p class="notice--info">
💡

이 글에서 보인 코드들의 전체 소스 코드는 <a href="https://github.com/JeroCaller/StudyAlgorithmWithJava/blob/master/src/main/java/search/BinarySearch.java">https://github.com/JeroCaller/StudyAlgorithmWithJava/blob/master/src/main/java/search/BinarySearch.java</a>와 이에 대한 테스트 코드인 <a href="https://github.com/JeroCaller/StudyAlgorithmWithJava/blob/master/src/test/java/search/BinarySearchTest.java">https://github.com/JeroCaller/StudyAlgorithmWithJava/blob/master/src/test/java/search/BinarySearchTest.java</a>을 참고.

</p>

---

References 

[1] 관련 백준 문제

[백준 7795 - "먹을 것인가 먹힐 것인가"](https://www.acmicpc.net/problem/7795)

[2] 내 블로그 - 이분 탐색

[[알고리즘] 이진 배열 탐색 (Binary array search)](/algorithm/algorithm-binary-array-search/)

[3] [Lower bound & Upper bound 개념 및 구현](https://yoongrammer.tistory.com/105)

[4] [[Algorithm/Java] 이진탐색 Binary Search / UpperBound / LowerBound](https://innovation123.tistory.com/65#6.%20Upper%20Bound%20%EB%B0%A9%EC%8B%9D%EA%B3%BC%20Lower%20Bound%20%EB%B0%A9%EC%8B%9D-1-7)