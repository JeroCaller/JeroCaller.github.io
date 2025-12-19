---
title: "[알고리즘] 이분탐색법의 응용 - 매개변수 탐색법(Parametric search), 그리고 '문제를 푼다'라는 것에 대한 고찰"
category: "Algorithm"
tag: ["Algorithm", "Baekjoon", "백준", "자바", "Java", "이분 탐색법", "이진 탐색법", "Binary Search", "Search", "Lower Bound", "Upper Bound", "매개변수 탐색법", "Parametric Search", "결정 함수", "최적화 문제", "Optimization Problem", "결정 문제", "Decision Problem", "결정적 알고리즘", "Deterministic Algorithm", "비결정적 알고리즘", "Non-deterministic Algorithm", "몬테 카를로 방법", "Monte Carlo Method"]
---

“[백준 2805 - 나무 자르기](https://www.acmicpc.net/problem/2805)”에서는 다음과 같은 문제를 묘사하고 있다. 한 줄로 나란히 서 있는 높이가 제각각인 나무들이 있고, 특정 높이에 톱날을 고정시키고 한 줄로 나열된 나무들의 윗부분을 한꺼번에 자를 수 있는 목재 절단기가 있다. 나무 윗부분을 잘라 얻은 나무들의 총 길이가 적어도 M미터와 같거나 넘기기를 원한다. 이 때, 절단기로 설정할 수 있는 높이의 최대값을 구하는 문제이다. 

조금 더 이해하기 쉽게 해당 문제에 있는 예시를 보자면, 각각 20, 15, 10, 17의 높이를 가진 나무 4그루가 일렬로 서 있다고 할 때, 이 나무들을 잘랐을 때 나오는 나무들의 총 길이가 적어도 7미터와 같거나 넘기고자 한다. 이 때, 이 7미터라는 조건을 만족하는 절단기의 최대 높이를 구하는 것이다. 

절단기의 높이를 10으로 정했다고 해보자. 그러면 나무들에서 각각 10, 5, 0, 7미터의 나무들을 얻게 될 것이다. 이들의 합은 22미터가 되므로 조건인 “7미터 이상”을 만족한다. 그러면 이 문제의 답은 “10”이라 할 수 있을까? 문제에서는 “최대값”을 찾고 있다. 따라서 절단기의 높이가 11미터 이상일 때에도 확인해봐야 한다. 

절단기의 높이를 11, 12, 13, … 미터일 때에 대해서도 위와 똑같은 검증 과정을 거쳐야할 것이다. 그러다가 절단기의 높이를 15로 설정했을 때, 각각 5, 0, 0, 2미터의 나무 잔해들이 나와 총합 7미터로 조건을 딱 맞춘다는 것을 볼 수 있다. 절단기의 높이를 16으로 설정할 경우, 각각 4, 0, 0, 1미터의 나무 잔해들이 나오는데, 합쳐서 5미터로, 조건인 “7미터 이상”을 만족하지 못하게 된다. 그래서 결국 절단기에 설정할 수 있는 높이의 최댓값은 15임을 알 수 있다. 

주어지는 입력값에 따라 절단기의 높이 설정을 0미터로 할 수도 있다. 따라서 주어지는 가능한 모든 경우의 입력값들에 대해 정답을 구할 수 있으려면 0미터부터 적어도 입력값에 나온 나무들 중 가장 높은 나무의 높이까지 1미터 단위로 “M미터 이상”의 조건 만족 여부를 확인하면서 해당 조건을 만족하는 값들 중 최대값을 구해야한다는 것이다. 이는 데이터의 개수가 많아지고, 주어지는 입력값들 중 최대값이 높으면 높을수록 확인해야할 값들이 그만큼 많아진다는 문제가 발생한다. 주어지는 입력값들 중 최대값을 L이라 한다면 이 문제를 풀기 위해선 0미터부터 L미터까지 1미터 단위로 “M미터 이상”이라는 조건을 만족하는지 일일이 확인해봐야 하는 것이다. 

이는 분명 비효율적임을 직감적으로 알 수 있을 것이다. 굳이 따지자면 순차 탐색법으로 일일이 하나씩 확인해야하는 것이다. 그런데 이를 조금 더 효율적으로 해결할 수 있는 알고리즘이 있는데 바로 매개변수 탐색(Parametric search) 알고리즘이다. 

# 매개변수 탐색법(Parametric search)

매개변수 탐색법은 한마디로 말해 “최적화 문제를 결정 문제로 바꿔 푸는 알고리즘”이다. 말이 좀 어려운데, 하나씩 파헤쳐보면 다음과 같다.

- 최적화 문제(Optimization problem) - 주어진 조건 및 제약사항에 한해 최적의 해를 구하는 문제. 보통 어떤 조건을 만족하는 최대값 또는 최소값을 구하는 문제가 여기에 해당한다.
    - 미로에서 탈출구로 향하는 여러 길들 중 가장 짧은 경로를 찾는 문제가 이에 해당.
- 결정 문제(Decision problem) - 어떤 문제에 대해 해가 존재하는지 아닌지, 즉, “YES OR NO”만으로 답할 수 있는 문제를 말한다.
    - 주어진 수 N은 소수(prime number)인가? ⇒ 예 또는 아니오 둘 중 하나로 답이 가능하기에 결정 문제라 할 수 있다.
    - 결정 문제가 아닌 문제들은 해의 존재 여부만으로 답할 수 없는 문제들로, “주어진 수 이하의 자연수들 중 소수를 모두 찾아라”라든가 최적화 문제와 같은 문제들이 그 예라 할 수 있겠다.

즉, 매개변수 탐색법을 풀어서 말하자면, 어떤 조건을 만족하는 최대 또는 최소값을 찾는 최적화 문제를, 이 값이 문제의 해에 맞는지 여부를 묻는 결정 문제로 바꿔 푸는 방식이라는 것이다. 최적화 문제를 바로 풀기 어려울 때 결정 문제로 바꿔 푸는 것이다. 최적화 문제에서는 정답이 될 수 있는 후보값들이 문제에 따라 무수히 많이 존재할텐데, 결정 문제에서는 정답이 “예” 아니면 “아니오” 이 둘 뿐이라 상대적으로 문제를 풀기 쉬울 것이다. 즉, 매개변수 탐색법은 쉽게 말해 풀기 어려운 문제를 상대적으로 풀기 쉬운 문제로 변환하여 푸는 방식인 것이다. 

그렇다면 매개변수 탐색법에서는 어떻게 최적화 문제를 결정 문제로 변환하여 푼다는 것일까? 매개변수 탐색법을 위해서는 우선 결정함수와 이 함수의 입력값인 매개변수를 정의해야 한다. 

- 결정함수는 입력값으로 주어지는 매개변수가 특정 조건을 만족하는지 여부를 true, false로 반환하는 함수이다. 여기서의 조건이라는건 주어진 문제에서 찾아 유추해야한다. 앞서 예를 들었던 “나무 자르기” 문제에서는 “잘려진 나무들의 총합이 M미터와 같거나 이를 넘기는가?”가 조건이 되어 이를 결정함수로 구현할 수 있을 것이다.
- 매개변수는 결정함수의 입력값으로 대입할 때 true가 나오는지 검증하고자 하는 값을 의미한다.

앞서 예로 들었던 “나무 자르기” 문제에 대입해보자면, “잘려진 나무들의 총합이 M미터와 같거나 그 이상인가”를 결정함수 `fn(param)` 으로 설정할 수 있을 것이며, 이 결정함수에 들어갈 입력값 `param` 은 다름아닌 “절단기에 설정할 높이”가 되겠다. 앞서 본 “절단기 높이를 10미터로 설정했을 때 잘려질 나무들의 총합이 7미터 이상인가”라는 문장을 간단히 `fn(10)` 으로 표현할 수 있으며, 앞선 설명에서 “가능하다”라는 결론이 나왔기에 `fn(10) = true` 라고 표현할 수 있게 된다. 

앞서 예로 든 문제를 결정 문제로 환산해보면, `fn(10) = true, fn(15) = true, fn(16) = false` 로 정리할 수 있을 것이다. 여기서 알 수 있는 사실은, 결정함수는 “단조성(Monotonicity)”를 띄어야 한다는 것이다. 원래 수학에서 단조 함수(Monotonic function)라 함은, `y = f(x)` 함수가 주어졌을 때, 입력값 x의 값이 증가함에 따라 y의 값도 항상 증가(단조 증가) 또는 항상 감소(단조 감소)함을 의미한다. 즉, 중간에 다른 방향으로 꺾이지 않고 항상 오른쪽으로 가면 갈수록 계속 위로 그래프가 뻗거나, 아래로 계속 뻗어야 한다는 것이다. `y = x`나 `y = log x` 등이 그 예가 될 것이다. `y = |x|` 처럼 V자 그래프나 낙타 모양의 그래프처럼 중간에 방향을 바꾸는 구간이 하나라도 있어서는 단조성을 만족할 수 없다. 이처럼, 매개변수 탐색법에서의 결정함수도 특정 매개변수까지는 true를 유지하다가, 어느 값 이상부터는 false 값만 존재하는(또는 그 반대의 경우도 마찬가지) 단조성이 필요한 것이다. 앞서 예를 든 문제에서, 0부터 L까지의 결정함수 fn의 값을 배열로 나열했을 때, `{true, true, true, true, ... , true, false, false, false, false}` 처럼 마치 true값의 그룹과 false값의 그룹이 물과 기름처럼 양분화되어야 한다는 것이다. 즉, 말로 풀어서 설명하자면 “절단기의 높이를 H로 정했을 때 결정함수를 만족시키지 못한다면 그보다 더 높은 높이로 설정해도 결정함수를 만족시키지 못할 것이다. 그리고 절단기의 높이를 H로 설정했을 때 결정함수를 만족시킨다면 그보다 더 낮은 높이로 설정해도 결정함수를 만족시킬 것이다”라는 명제가 성립해야 한다는 것이다. 만약 `{true, true, false, true, true, false, false}` 처럼 true와 false 값이 물과 기름처럼 이분화되어 있지 않고 중간에 섞여 있으면 매개변수 탐색법을 사용할 수 없다. 왜냐하면 매개변수 탐색법은 궁극적으로 이분 탐색법(Binary search)을 사용하기 때문이다. 

다시 앞서 살펴본 문제로 돌아가보자. 결정함수를 정의하였으니, 이번에는 여기에 이분 탐색법을 도입해보자. 이를 위해 우선 이분 탐색법으로 살펴볼 값의 범위를 정해야한다. 이 문제에서는 0부터, 입력값으로 주어지는 나무들의 높이 중 가장 높은 나무의 높이 L까지가 범위임을 알 수 있다. 즉, 우리는 여기서 0부터 L까지의 정수 배열 `{0, 1, 2, 3, ..., L-2, L-1, L}`을 얻을 수 있다. 앞서 본 입력값이 `{20, 15, 10, 17}` 인 경우를 보면, 이 입력값들 중 최대값은 20이므로 여기서 우린 0부터 20까지 1씩 증가하는 가상의 배열 `{0, 1, 2, 3, ..., 19, 20}` 을 유도할 수 있게 된다. 

이제 이 가상의 배열에 대해 이분 탐색법을 적용하면 된다. 처음에는 이 배열의 중간값인 10을 택하게 될 것이다. 이 10이란 값을 앞서 정의한 결정함수 `fn()` 에 대입해본다. `fn(10)` 의 값이 true가 나올 것이다. 그러면 여기서 결정함수를 만족하는 10 이상의 수가 더 있을 것을 예상하여 탐색 범위를 11부터 20까지로 좁혀 이분탐색법을 계속 반복 적용하면 되겠다. 

이러한 매개변수 탐색법은 찾고자 하는 최대, 최소값을 대략 O(log N)만에 찾아낼 수 있으므로, 기존에 떠올렸던 O(N)의 순차탐색법에 비해 더 효율적으로 최대, 최소값을 찾을 수 있다는 이점이 있다. 

## 코드 구현

### 백준 문제 기준

앞서 보았던 “나무 자르기” 문제에 대해 매개변수 탐색법을 적용한 코드는 다음과 같다(실제 필자가 백준에 제출한 코드를 그대로 가져왔다).

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedWriter bw =
        new BufferedWriter(new OutputStreamWriter(System.out));
    private static final BufferedReader br =
        new BufferedReader(new InputStreamReader(System.in));

    public static void main(String[] args) throws IOException {
        mySolution();

        bw.flush();
        bw.close();
        br.close();
    }

    public static void mySolution() throws IOException {
        String[] firstLine = br.readLine().split(" ");
        final int N = Integer.parseInt(firstLine[0]);
        final int M = Integer.parseInt(firstLine[1]);
        int[] trees = new int[N];
        int maxTree = 0;
        StringTokenizer stringTokenizer = new StringTokenizer(br.readLine(), " ");

        for (int i = 0; i < N; ++i) {
            trees[i] = Integer.parseInt(stringTokenizer.nextToken());

            if (trees[i] > maxTree) {
                maxTree = trees[i];
            }
        }

        int result = findAnswer(trees, maxTree, M);
        bw.write(String.valueOf(result));
    }

    private static boolean isCandidate(int[] trees, int m, int offer) {
        long total = 0;

        for (int i = 0; i < trees.length; ++i) {
            if (trees[i] < offer) {
                continue;
            }

            total += trees[i] - offer;
        }

        return total >= m;
    }

    private static int findAnswer(int[] trees, int maxTree, int m) {
        int leftIdx = 0;
        int rightIdx = maxTree;
        int midIdx = (leftIdx + rightIdx) / 2;
        int answer = 0;

        while (leftIdx <= rightIdx) {
            if (isCandidate(trees, m, midIdx)) {
                answer = Math.max(answer, midIdx);
                leftIdx = midIdx + 1;
            } else {
                rightIdx = midIdx - 1;
            }

            midIdx = (leftIdx + rightIdx) / 2;
        }

        return answer;
    }
}

```

코드 1-1. 

위 코드에서는 `isCandidate()` 메서드가 결정함수의 역할을 하고 있다. 해당 메서드에서는 주어진 값이 문제의 조건에 부합하는지 확인하고 있다. 조건 만족 여부를 확인하기 위해 검증하고자 하는 값을 주어진 입력값들에 대해 모두 연산하고 있다. 따라서 해당 결정함수의 시간복잡도는 입력값의 개수를 N이라 할 때 O(N)이라 할 수 있다. 

이러한 결정함수를 이분 탐색법이 적용된 `findAnswer()` 메서드에서 사용하고 있다. 탐색 때마다 탐색 범위의 중간값인 `midIdx` 를 `isCandidate()` 라는 결정함수에 대입하여 해당값이 결정함수를 만족하는지 살펴보고 있다. 만약 현재 `midIdx` 값이 결정함수를 만족한다면, 기존에 저장한 `answer` 의 값과 비교하여 더 큰 값을 다시 `answer` 변수에 저장하게끔 한 다음, `leftIdx` 를 `midIdx` 보다 1 더 큰 곳으로 옮긴다. 현재 `midIdx` 값보다 더 큰 결정함수를 만족하는 값이 있을 수도 있음을 확인하기 위해 배열 탐색 범위를 오른쪽으로 옮긴 것이다. 그리고 만약 현재 `midIdx` 값이 결정함수를 만족시키지 못한다면 `rightIdx` 를 `midIdx - 1` 위치에 둠으로써 배열 내 탐색 범위를 왼쪽으로 잡아 결정함수를 만족시킬 값을 계속 찾도록 하고 있음을 볼 수 있다. 

`findAnswer()` 메서드의 경우, 주어진 입력값들 중 가장 높은 나무의 높이를 L이라 할 때, 이분 탐색법을 사용하므로 O(log L)의 성능을 보이고 있다. 

`findAnswer()` 와 `isCandidate()` 를 종합하여 보자면, `findAnswer()` 내에서 `isCandidate()` 를 사용하는 구조이므로, 전체적인 시간복잡도는 O(N * log L)이라 할 수 있다. 

### 매개변수 탐색법 일반화 버전

매개변수 탐색법에서는 문제에 따라 결정함수의 내부 내용이 달라질 뿐, 이분 탐색법을 적용하는 부분은 같기 때문에 필자는 매개변수 탐색법을 특정 문제에 의존하도록 구현하는 것이 아닌, 일반화된 버전으로 구현할 수 있다고 보았다. 따라서 필자는 앞서 언급한 “나무 자르기” 문제 뿐만 아니라 모든 문제에 대해서도 매개변수 탐색법을 구현, 적용할 수 있는 일종의 “틀”을 구현해보았다. 

매개변수 탐색법에서는 문제가 무엇이냐에 따라 결정함수를 구현하는 부분이 달라질 뿐, 이분 탐색법 자체는 구현이 동일할 것이라는 점, 그리고 문제 상황에 따라 결정함수 내에서 사용할 변수들의 수나 형태가 달라질 수도 있다는 점을 고려하여 다음과 같이 추상 클래스를 정의하였다. 

```java
package search;

import exception.sub.RequiredVarsNotInitializedException;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.experimental.SuperBuilder;
import sort.SortChecker;

import java.util.Arrays;

@Getter
@NoArgsConstructor
@AllArgsConstructor
@SuperBuilder
abstract public class AbstractParametricSearch {
    protected int[] array;
    protected int bottomValue;
    protected Integer topValue;

    protected abstract boolean isPossible(int x);

    public int getMaximumValue() throws RequiredVarsNotInitializedException {
        initialize();

        int answer = bottomValue;
        int leftIdx = bottomValue;
        int rightIdx = topValue;
        int midIdx = (leftIdx + rightIdx) / 2;

        while (leftIdx <= rightIdx) {
            if (isPossible(midIdx)) {
                answer = Math.max(answer, midIdx);
                leftIdx = midIdx + 1;
            } else {
                rightIdx = midIdx - 1;
            }

            midIdx = (leftIdx + rightIdx) / 2;
        }

        return answer;
    }

    public int getMininmumValue() throws RequiredVarsNotInitializedException {
        initialize();

        int answer = bottomValue;
        int leftIdx = bottomValue;
        int rightIdx = topValue;
        int midIdx = (leftIdx + rightIdx) / 2;

        while (leftIdx <= rightIdx) {
            if (isPossible(midIdx)) {
                answer = Math.min(answer, midIdx);
                leftIdx = midIdx + 1;
            } else {
                rightIdx = midIdx - 1;
            }

            midIdx = (leftIdx + rightIdx) / 2;
        }

        return answer;
    }

    protected void throwExceptionIfArrayNotInitialized() throws
        RequiredVarsNotInitializedException {
        if (this.array == null) {
            throw new RequiredVarsNotInitializedException("""
                탐색 대상 배열이 초기화되지 않았습니다. 탐색 대상 배열 변수가
                null이어선 안됩니다.
            """);
        }
    }

    protected void sortArray() {
        throwExceptionIfArrayNotInitialized();
        Arrays.sort(array);
    }

    protected void initialize() {
        throwExceptionIfArrayNotInitialized();

        if (!SortChecker.isSortedAsc(array)) {
            sortArray();
        }

        if (topValue == null) {
            topValue = array[array.length - 1];
        }
    }
}

```

코드 2-1. `AbstractParametricSearch.java`  참고로 `RequireVarsNotInitializedException` 은 필자가 임의로 만든 커스텀 예외 클래스이며, `SortChecker.isSortedAsc()` 메서드도 필자가 만든 메서드로, 주어진 배열이 오름차순인지를 확인하는 기능의 메서드이다. 

위 추상화 클래스에서는 이분 탐색법을 이용하여 최대 또는 최소값을 구하는 부분만 구현하였고, 결정 함수 자체는 위 클래스를 상속하는 자식 클래스에서 구현하도록 설계하였다. 

앞서 본 백준 문제 “나무 자르기” 문제를 위 코드를 이용하여 구현해본다면 다음의 코드처럼 작성할 수 있겠다.

```java
package search.mock;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.experimental.SuperBuilder;
import search.AbstractParametricSearch;

/**
 * <p>
 *     백준 문제 <a href="https://www.acmicpc.net/problem/2805">"나무 자르기"</a> 를
 *     풀기 위한 매개변수 탐색 클래스
 * </p>
 */
@Getter
@AllArgsConstructor
@SuperBuilder
public class CutTreeProblemParametricSearch extends AbstractParametricSearch {
    protected int treeHeight;

    @Override
    protected boolean isPossible(int x) {
        long total = 0;

        for (int num : this.array) {
            if (num < x) {
                continue;
            }

            total += num - x;
        }

        return total >= treeHeight;
    }
}

```

코드 2-2. `CutTreeProblemParametricSearch.java`  여기서 `treeHeight` 는 해당 백준 문제에서의 `M` 이다. 

그 후 자식 클래스 `CutTreeProblemParametricSearch` 의 객체를 빌더 패턴을 이용하여 각각 탐색 대상 배열과 탐색 범위의 하한선 및 상한선을 지정하여 생성하고, `getMaximumValue()` 를 호출하면 주어진 문제에 대해 특정 조건을 만족하는 최대값을 구할 수 있게 된다. 

<div class="notice--info">
💡

위 코드 2-1~2-2로 구현된 코드들의 전체 소스 코드는 <a href="https://github.com/JeroCaller/StudyAlgorithmWithJava/tree/master/src" target="_blank">https://github.com/JeroCaller/StudyAlgorithmWithJava/tree/master/src</a>를 참고. 해당 repo에서 각각 다음의 모듈 및 패키지를 확인하시면 됩니다. 

<ul>
  <li><code>src/main/java</code>
    <ul>
      <li><code>/search/AbstractParametricSearch.java</code></li>
      <li><code>/sort/SortChecker.java</code></li>
      <li><code>/exception</code></li>
    </ul>
  </li>
  <li><code>src/test/java</code>
    <ul>
      <li><code>/search/mock/CutTreeProblemParametricSearch.java</code></li>
      <li><code>/search/parametric/CutTreeProblemTest.java</code></li>
    </ul>
  </li>
</ul>
</div>

## 시간 복잡도

앞서 본 구현 코드에서도 볼 수 있듯, 매개변수 탐색법의 시간복잡도는 주로 결정함수의 시간복잡도에 크게 의존한다는 것을 알 수 있다. 이분탐색법을 적용하여 최대 또는 최소값을 구하는 과정 자체는 O(log N)이나, 이분탐색법 내부에서 반복문을 돌며 결정함수를 계속 실행하기에, 결정함수의 시간복잡도를 O(D)라고 할 때 매개변수 탐색법의 전체 시간복잡도는 O(D * log N)이라 할 수 있다. 

만약 매개변수 탐색법이 아닌, 앞서 언급헀던 순차 탐색법으로 해결하려고 했다면 O(D * N)만큼 걸렸을 것이다. 

## 기존 이분 탐색법과의 비교

필자는 이미 예전에 “[이분탐색법](/algorithm/algorithm-binary-array-search/)”의 기본 개념을 소개하는 글을 작성한 적이 있으며, 여기서 파생된 “[lower, upper bound](/algorithm/algorithm-%EC%9D%B4%EB%B6%84-%ED%83%90%EC%83%89%EB%B2%95(Binary-search)%EC%9D%98-lower-bound(%ED%95%98%ED%95%9C%EC%84%A0)%EA%B3%BC-upper-bound(%EC%83%81%ED%95%9C%EC%84%A0)/)” 관련 글을 바로 최근에 작성하기도 하였다. 특히 lower, upper bound 알고리즘과 이 글에서 소개한 매개변수 탐색법 간에는 비슷한 점이 있다. lower, upper bound 알고리즘과 매개변수 탐색법 모두 “주어진 문제에 대한 최대 또는 최소값”을 구한다는 목적에 대해서는 공통점이 있다. 반면 이 두 알고리즘의 차이점도 뚜렷하다. lower, upper bound 알고리즘은 “배열 내에 찾고자 하는 값이 중복으로 여러 개 존재할 때 각각 최소, 최대의 위치값(인덱스)”를 반환하는 것에 비해, 매개변수 탐색법은 “주어진 어떤 조건(결정함수)을 만족하는 여러 값들 중 최대 또는 최소값”을 구하는 것이라는 점이 다르다. 즉, 매개변수 탐색법에서는 그 “조건”이라는 것이 문제에 따라 달라진다는 것이다. 또한 lower, upper bound 알고리즘과는 달리, 매개변수 탐색법에서는 “결정함수”라는 일종의 보조 도구가 필요하다는 점도 차이점이라 볼 수 있겠다. 이로 인해 엄밀히 말하자면 두 알고리즘의 시간복잡도도 다르다. lower, upper bound 알고리즘은 거의 이분 탐색법의 기본형과 비슷하기에 O(log N)인 반면, 매개변수 탐색법에서는 이분 탐색법으로 구현한 최대, 최소값을 구하는 부분이 내부적으로 결정함수에 의존하기에 결정함수의 시간복잡도에 크게 의존한다는 것이 차이점이다. 그래서 매개변수 탐색법에서는 결정함수의 시간복잡도를 O(D)라 할 때, 결국 O(D * log N)이 되기에 엄밀히 말하면 일반적인 이분탐색법 및 lower, upper bound의 것과 차이가 있음을 알 수 있다. 

매개변수 탐색법은 이분 탐색법을 응용하여 좀 더 현실에 있을 법한 문제를 효율적으로 해결할 수 있는 알고리즘이다. 그래서 현실 세계에서는 lower, upper bound를 포함한 순수한 이분 탐색법보다 더 자주 사용될 것 같다는 느낌이 든다. 또한 매개변수 탐색법은 이분탐색법을 활용하여 어려운 문제인 “최적화 문제”를 상대적으로 쉬운 문제인 “결정 문제”로 치환하여 문제를 단순화한다는 점이 필자 개인적으로는 굉장히 흥미로운 아이디어라 생각한다. 

## 매개변수 탐색법 정리

앞서 소개한 매개변수 탐색법에 대해 요약하여 정리하자면 다음과 같다. 

- 매개변수 탐색법은 주어진 상황, 조건 속에서 최대 또는 최소값을 찾는 다소 어려울 수 있는 “최적화 문제”를, “주어진 값이 문제의 상황, 조건을 만족하는지 여부”를 묻는 “결정 문제”라는 상대적으로 쉬운 문제로 치환하여 푸는 알고리즘이다.
- 매개변수 탐색법에서는 결정함수와 이분탐색법을 활용하여 O(D * log N)이라는 효율적인 방식으로 최대 또는 최소값을 찾는다.
    - 결정함수는 “매개변수”라는 입력값이 특정 조건을 만족하는지 검사하는 함수로, true 또는 false를 반환하는 함수이다.
    - 결정함수에 입력하여 특정 조건에 부합하는지를 확인하는데에 사용될 입력값을 “매개변수”라 부르며, 이 매개변수는 이분탐색법에서 사용되는 `midIdx` 의 값이 된다.
    - 결정함수는 “단조성”을 띄어야 한다. 즉, 주어진 여러 값들 중 특정 범위 내의 입력값들에 대한 출력값은 항상 일정해야 한다. `{true, true, false, true, false, true}` 가 아닌 `{true, true, true, false, false, false}` 처럼 마치 “물과 기름이 양분화”된 것과 같은 결과를 내야한다.
- 매개변수 탐색법을 이용하여 어떤 문제를 풀기 위해서는, 문제로부터 이분탐색법에 활용할 “배열”과 그 배열의 “범위”를 잘 추출할 수 있어야 한다.

# 번외: 관련 개념들 정리

매개변수 탐색법에 대해 조사하던 중 “최적화 문제”와 “결정 문제”라는 용어를 마주하게 되었다. 그리고 이와 관련된 몇몇 개념들에 대해서도 알게 되었고, 비록 이 글에서는 “매개변수 탐색법”을 메인으로 다루기에 이와 직접적인 연관이 없겠지만, 그럼에도 알면 좋을 내용들인 것 같아서 추가적으로 여기에 정리하고자 한다. 

## 결정 문제(Decision problem) VS 결정적 알고리즘(Deterministic algorithm)

결정 문제는 앞서 언급했듯, 주어진 문제에 대해 “예” 또는 “아니오” 만으로 답할 수 있는 문제를 말한다. 조금 더 어렵게 말한다면 주어진 문제에 대한 해의 존재 여부를 묻는 문제라 할 수 있겠다. 

이와 이름이 비슷한 다른 개념이 있는데, 그것은 “결정적 알고리즘(Deterministic algorithm)”이라는 것이다. 결정적 알고리즘은 동일한 입력값에 대해 항상 동일한 출력값을 내는 알고리즘을 의미한다. 보통 수학에서 볼 수 있는 `y = f(x)` 와 같은 함수들이 결정적 알고리즘의 특성을 가지고 있다고 볼 수 있겠다. 결정적 알고리즘은 항상 같은 입력값에 대해 일관적인 결과를 내므로 예측 가능하며, “확률”이란 것이 존재하지 않는 비확률적 알고리즘이다. 

결정적 알고리즘의 예시로는 우리가 코딩을 할 때 보통 마주할 수 있는 대부분의 함수들이 이에 해당할 것이다. 함수 내부에 난수 알고리즘이나 어떠한 확률적인 알고리즘을 도입하지 않은 이상, 같은 입력값에 대해선 항상 같은 출력값을 내기 때문이다. 덧셈, 뺄셈과 같은 사칙연산 관련 함수, 주어진 수들 중 최대 또는 최소값을 출력하는 함수 등이 이 예가 되겠다. 

앞서 다룬 매개변수 탐색법이나 이분 탐색법도 결정적 알고리즘의 한 예라 볼 수 있겠다. 주어진 문제에 대해 최대, 최소값을 도출하는데, 동일한 입력값들에 대해선 동일한 최대, 최소값을 항상 도출하기 때문이다. 또는 BFS처럼 “미로에서 도착점까지 최소한의 거리를 가지는 경로를 찾는 문제”도 항상 같은 입력값에 대해서는 같은 최소 경로를 도출하기에 결정적 알고리즘의 한 예라 볼 수 있을 것이다. 

사실 필자가 매개변수 탐색법에 대해 설명하는 자료들 대부분에서 “결정 문제”라는 용어를 언급하기에 무슨 개념인지 몰라서 조사했었는데, “결정”이라는 같은 이름을 쓴다는 이유로 종종 “결정적 알고리즘”이란 이야기도 듣게 되어서 두 개념을 혼동하지 않기 위해 이렇게 정리하게 되었다. 두 개념은 이름만 같을 뿐 사실상 서로 전혀 다른 개념을 다루고 있다. 결정 문제는 “문제의 유형”중 하나를, 결정적 알고리즘은 “문제를 해결하는 방식” 중 하나를 언급하는 것이기에 엄연히 서로 다른 주제를 다루고 있는 개념들인 것이다. 

뭐, 그렇다고 하더라도 어떤 문제가 결정 문제이면서 동시에 그 문제의 해결책을 결정적 알고리즘으로 풀 수 있는 경우도 있기에 두 개념이 아예 서로 상관없는 개념이라 하기에는 어려운 점도 있을 것이다. 앞서 보인 매개변수 탐색법이 그 예시이다. 앞서 살펴본 “나무 자르기” 문제는 매개변수 탐색법을 이용할 때 “결정 문제”로 환원하여 풀었으며, 그에 대한 해결책으로 “결정 함수”와 “이분 탐색법”을 사용하였는데, 이 둘 모두 동일한 입력값에 대해선 항상 동일한 출력값을 보장하기에 “결정적 알고리즘”의 한 예라 볼 수 있을 것이다. 즉, 우린 이미 앞선 글에서 “결정 문제”와 “결정적 알고리즘”을 모두 겪은 것이나 마찬가지이다. 

한 편, “결정적 알고리즘”의 정반대 개념도 있을 것이다. 비결정적 알고리즘(Non-deterministic algorithm)은 결정적 알고리즘과 달리, 동일한 입력값에 대해 동일한 출력값을 내는 보장이 없는 알고리즘을 말한다. 흔히 난수 알고리즘을 활용한 기능들이 그 예시가 되겠다. 예를 들어 “가상 로또 번호 6자리 출력기”라는 함수를 `random` 등의 난수 API를 이용하여 구현하였다면, 이 함수를 동일한 환경에서 매번 실행해도 매번 서로 다른 결과값들이 도출될 것이므로 비결정적 알고리즘의 한 예라 볼 수 있겠다. 

사실 일반적으로는 비결정적 알고리즘의 대표 예시로 유전 알고리즘(Genetic algorithm)과 몬테 카를로 방법(Monte Carlo Method) 등이 잘 알려져 있다. 

## 최적화 문제와 결정 문제, 결정적 알고리즘 간의 관계

앞서 매개변수 탐색법에서 “최적화 문제”가 무엇인지 그 개념을 설명하면서 그와 동시에 매개변수 탐색법은 “최적화 문제를 결정 문제”로 바꾸는 기법이라고 언급하였다. 즉, 최적화 문제를 결정 문제로 바꾸는 것이 가능함을 “매개변수 탐색법”이라는 예시를 통해 알 수 있었다. 

한 편, 최적화 문제의 해(solution)에는 크게 지역(또는 국소) 최적해(Local optimum)와 전역 최적해(Global optimum)로 구분할 수 있다. 지역 최적해는 주어진 범위 내 특정 범위 내에서는 최적의 해일 수 있겠지만, 전체 범위 내에서는 최적의 해가 아닐 수 있는 해를 말한다. 반면 전역 최적해는 주어진 범위 전체에서의 최적해를 의미한다. 그리디 알고리즘이 이에 대한 적절한 예시로 보인다. 그리디 알고리즘은 항상 인접한 해들 중 최소 또는 최대값을 찾아 문제 전체의 최적해를 찾는 방법론이다. 항상 “지역 최적해”를 찾으려는 방식이기에 그리디 알고리즘을 이용하여 어떤 문제를 풀 때에는 반드시 “지역 최적해가 전역 최적해임을 보장할 수 있는 문제”에만 적용해야 정확한 값을 얻을 수 있을 것이다. 

이렇게 지금까지 최적화 문제, 결정 문제, 결정적 알고리즘이라는 개념들에 대해 설명하였는데, 이번에는 이들 간의 관계에 대해 살펴보겠다. 앞선 매개변수 탐색법에서 알게된 “최적화 문제를 결정 문제로 환원할 수 있다”라는 사실 외에도 다음과 같은 사실들이 알려져 있다. 

- 결정 문제는 항상 결정적 알고리즘으로만 풀 수 있는 것은 아니며, 비결정적 알고리즘으로도 해결할 수 있다.
- 전역 최적화 문제, 즉 전역 최적해를 찾아야하는 문제에서는 비결정적 알고리즘이 효과적이다.

사실 “비결정적 알고리즘”이라고 하면 언뜻 보면 동일한 입력값에 대해 동일한 출력값을 보장하지 않기 때문에 “이걸로 문제를 풀 수 있다고?”란 생각이 들 수도 있겠다. 하지만 입력값-출력값에 대한 비일관성으로도 문제를 해결할 수 있다. 

예를 들어 한 등산가가 어떤 산맥의 입구에 왔다고 해보자. 이 산맥에는 여러 개의 봉우리가 있으며, 이 등산가는 이 산맥 중 어떤 봉우리가 가장 높은가를 알고 싶다고 해보자. 가장 높은 봉우리를 찾기 위한 알고리즘으로 “계속 오르막길로만 가며, 오르막길이 더 이상 앞에 존재하지 않으면 그 곳이 봉우리이며, 가장 높은 위치이다.”라고 결정했다고 해보자. 

![사진 1-1. 등산가가 산맥의 여러 봉우리 중 가장 높은 고도를 가진 위치를 찾고자 한다. Gemini AI로 생성한 이미지](/images/2025-12-19/algorithm-%EC%9D%B4%EB%B6%84%ED%83%90%EC%83%89%EB%B2%95%EC%9D%98%20%EC%9D%91%EC%9A%A9%20-%20%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%20%ED%83%90%EC%83%89%EB%B2%95(Parametric%20search)%20%EA%B7%B8%EB%A6%AC%EA%B3%A0%20%EB%AC%B8%EC%A0%9C%EB%A5%BC%20%ED%91%BC%EB%8B%A4%EB%9D%BC%EB%8A%94%20%EA%B2%83%EC%97%90%20%EB%8C%80%ED%95%9C%20%EA%B3%A0%EC%B0%B0/1.png)

사진 1-1. 등산가가 산맥의 여러 봉우리 중 가장 높은 고도를 가진 위치를 찾고자 한다. Gemini AI로 생성한 이미지

이 알고리즘은 한 가지 문제가 있다. 바로 “지역 최적해”를 찾는 데에만 좋은 알고리즘이며, 전역 최적해는 찾지 못할 수 있다는 것이다. 위 사진에서처럼 등산가가 산맥의 봉우리 A 지점부터 탐색한다고 해보자. 그러면 A 봉우리의 꼭대기에서 등산가는 더 이상 오르막길이 없으므로 해당 위치가 산맥에서 가장 높은 위치라고 착각할 수 있다. 하지만 우린 위 사진을 통해 알 수 있듯, 봉우리 C가 가장 높은 고도를 가지고 있음을 알고 있다. 

그렇다면 이 문제를 어떻게 해결할 수 있을까? 여러 방법이 있겠지만, 실제로 현실에서 이행하기엔 매우 비현실적이지만 이론적으로는 가능한 방법 하나를 소개하겠다. 

우선 비행기에 전문 스카이다이버들을 태운다. 비행기는 산맥의 A 지점부터 D 지점까지 비행하며, 그 사이에 스카이다이버들이 다이빙한다. 다이빙하는 지점은 매번 랜덤이다. 그들은 낙하산을 펼치며 바로 아래에 위치한 산맥의 어느 지점에 도착할 것이다. 이들에게는 각자 현재 위치의 고도를 측정할 수 있는 기계가 있다고 가정해보겠다. 그리고 산맥에 떨어진 스카이다이버들이 전화로 보고하는 것이다. “산맥 입구로부터 X 지점만큼 떨어진 위치의 고도는 Y 미터입니다”. 

산맥에 떨어진 스카이다이버의 수가 적다면 어떨 때에는 아무도 봉우리 C에 도착한 사람이 없어 결국 산맥에서 가장 높은 위치는 어디인지를 알 수 없게 되어 지역 최적해가 전역 최적해인 것마냥 답하는 오류를 범할 수 있을 것이다. 하지만 스카이다이버의 수를 조금씩 늘려가면서 이 비행기 낙하를 반복한다면? 누군가는 봉우리 C의 꼭대기에 도착할 것이고, 이를 통해 우린 전역 최적해를 알게 될 것이다. 

참고로 지금까지 말한 “낙하산 방식”이 바로 비결정적 알고리즘의 대표 예시 중 하나인 “몬테 카를로 방법(Monte Carlo Method)”이다. 몬테 카를로 방법은 무작위로 추출된 여러 난수들을 이용하여 어떤 문제의 값을 알아내는 방법론으로, 쉽게 말해 “매번 확률적으로 다른 시도를 충분히 많이 반복 시행하여 정답을 근사적으로 알아가는 방법론”이라 보면 되겠다. 이러한 방식은 언뜻 보면 “로또를 백만, 천만장 무한히 사서 로또 1등에 당첨되자!”와 같이 무리수적인 방식으로 보이겠지만, 이러한 몬테카를로 방법은 실제로 과학, 공학, 가치 예측 등의 금융 분야 등 여러 분야에서 다양하게 사용하는 방법론이다. 

몬테카를로 방법을 이용하여 문제를 푸는 대표적인 예시가 바로 “원주율 구하기”이다. 

<style>
  .single-image {
    text-align: center;
    margin: 1em;
  }
  .single-image > p {
    margin-top: 0.5em;
  }
  .single-image > .details {
    border: 2px solid grey;
    padding: 1em;
    margin-top: 0.5em;
  }
</style>
<div class="single-image">
  <img width="50%" src="/images/2025-12-19/algorithm-%EC%9D%B4%EB%B6%84%ED%83%90%EC%83%89%EB%B2%95%EC%9D%98%20%EC%9D%91%EC%9A%A9%20-%20%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%20%ED%83%90%EC%83%89%EB%B2%95(Parametric%20search)%20%EA%B7%B8%EB%A6%AC%EA%B3%A0%20%EB%AC%B8%EC%A0%9C%EB%A5%BC%20%ED%91%BC%EB%8B%A4%EB%9D%BC%EB%8A%94%20%EA%B2%83%EC%97%90%20%EB%8C%80%ED%95%9C%20%EA%B3%A0%EC%B0%B0/Pi_30K.gif" alt="image">
  <div class="details">
    <p>
        참고 사진 1-1. 몬테카를로 방법을 이용하여 원주율을 구하는 시뮬레이션. 주어진 영역 내에 랜덤한 위치에 점을 찍어나간다. 그리고 원 안에 찍힌 점들의 개수를 전체 영역에서 찍힌 점들의 개수로 나누면 원주율과 근접한 값을 얻게 된다. 이러한 시뮬레이션은 찍는 점의 개수를 많이 늘릴수록 정답에 더 가까워진다.
    </p>
    <ul>
      <li><small>
        사진 출처: <a href="https://ko.wikipedia.org/wiki/%EB%AA%AC%ED%85%8C%EC%B9%B4%EB%A5%BC%EB%A1%9C_%EB%B0%A9%EB%B2%95" target="_blank">https://ko.wikipedia.org/wiki/몬테카를로_방법</a> 사이트에서 발췌.
      </small></li>
      <li><small>
        저작권 표시) 
        저자: <a href="//commons.wikimedia.org/wiki/User:Nicoguaro" target="_blank" title="User:Nicoguaro">nicoguaro</a> - <span class="int-own-work" lang="ko">자작</span>, <a href="https://creativecommons.org/licenses/by/3.0" target="_blank" title="Creative Commons Attribution 3.0">CC BY 3.0</a>, <a href="https://commons.wikimedia.org/w/index.php?curid=14609430" target="_blank">링크</a>
      </small></li>
    </ul>
  </div>
</div>

벽에다가 정사각형의 큰 종이를 붙여두고, 그 정사각형 안에 내접하는 원을 그린다. 그 후 멀리서 다트를 충분히 많이 던진다. 그러면 벽에 걸린 종이 위에는 랜덤한 위치에 다트에 의한 점이 찍힐 것이다. 이렇게 충분히 많이 시도를 한 후, 정사각형 내부에 찍힌 전체 점들의 개수와, 원 안에 찍힌 점들의 개수를 센다. 그 후 `원 안에 찍힌 점들의 개수 / 정사각형 내부에 찍힌 전체 점들의 개수` 를 구하면 원주율에 가까운 근사치를 얻게 된다. 이는 던지는 다트의 수를 더 많이 늘릴수록 원주율의 정확한 값에 더 가까워진다. 

이 문제는 “원주율의 값은 3.1415….인가?”라는 “결정 문제”를 몬테 카를로 방법이라는 “비결정적 알고리즘”으로 푸는 대표적인 예시라 할 수 있겠다. 

이러한 몬테 카를로 방법을 사용할 때에는 충분히 많은 확률적 시도와, 무작위로 추출된 난수값들의 분포가 골고루 퍼져야 더 정확한 값을 얻을 수 있을 것이다. 

어떻게 보면 우리의 직관과 반대되는 내용일 수 있다. 우리는 자신도 모르게 “문제를 풀 때에는 항상 정해진 방식이 있으며, 그 방식대로 따르면 항상 모든 문제를 풀 수 있어”라고 생각한다. 그래서 앞선 등산가의 “오르막길” 알고리즘처럼 행동하기도 한다. 하지만 역설적으로 “정해진 방식 대신 오로지 확률적으로 무한번 시행한다”라는 어떻게 보면 주먹구구식 방식으로도 문제를 해결할 수 있는 것이다. 

마지막으로, 지금 다룬 내용을 AI가 제공한 요약을 인용함으로써 이 글을 마무리 짓겠다.

> "흔히 '결정 문제'는 '결정적 알고리즘'의 전유물이라 생각하기 쉽다. 하지만 원주율을 구하는 몬테카를로 방법이 보여주듯, 우리는 **'확률'이라는 비결정적인 도구를 사용해서도 'Yes/No'라는 명확한 결정 문제의 답을 내릴 수 있다.** 결국 결정 문제는 우리가 마주한 '**질문의 형태**'일 뿐이며, 그것을 정해진 레일 위에서 풀지(결정적), 아니면 확률의 바다를 항해하며 풀지(비결정적)는 전적으로 설계자의 선택에 달려 있는 것이다."
> 

---

References

[1] 관련 백준 문제 1

["백준 2805 - 나무 자르기"](https://www.acmicpc.net/problem/2805)

[2] 관련 백준 문제 2

["백준 1654 - 랜선 자르기"](https://www.acmicpc.net/problem/1654)

[3] [[C++ Algorithm] 매개 변수 탐색(Parametric Search)](https://m42-orion.tistory.com/70)

[4] [[알고리즘] 매개변수 탐색(Parametric Search)과 이분 탐색(Binary Search)](https://rkdrkd-history.tistory.com/134)

[5] [결정 문제(Decision Problem)와 결정적 알고리즘(Deterministic Algorithm) 그리고 최적화 문제](https://hemahero.tistory.com/200)

[6] [[알고리즘] parametric search와 lower bound, upper bound](https://pledge24.tistory.com/376)

[7] KB 금융지주 경영연구소, “KB 지식 비타민 : 몬테카를로(Monte-Carlo) 방법 소개와 산업 및 금융 적용사례”, 2017-04-19, 17-31호

[8] 몬테 카를로 방법

[몬테카를로 방법](https://ko.wikipedia.org/wiki/%EB%AA%AC%ED%85%8C%EC%B9%B4%EB%A5%BC%EB%A1%9C_%EB%B0%A9%EB%B2%95)