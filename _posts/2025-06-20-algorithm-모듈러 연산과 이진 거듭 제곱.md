---
title: "[알고리즘] 모듈러 연산과 이진 거듭 제곱"
category: "Algorithm"
tag: ["Algorithm", "Baekjoon", "백준", "Binary Exponentiation", "이진 거듭제곱", "Modular", "모듈러", "자바", "Java"]
---

# 관련 문제

[https://www.acmicpc.net/problem/15829](https://www.acmicpc.net/problem/15829)

# 이진 거듭제곱 (Binary Exponentiation)

\\(a^n\\) 와 같은 거듭제곱 연산을 구하기 위해선 보통 \\(a \times a \times a \times ... \times a\\) 와 같이 a를 n번 곱하는 연산을 이용할 것이다. n의 크기가 작은 경우에는 별 문제가 되지 않겠지만 n의 크기가 커질수록 그에 비례하여 연산 시간도 더 오래 걸릴 것이다. 이 방법의 시간복잡도는 \\(O(N)\\) 이다. 

그런데 만약 이것보다 더 빠른 방법으로 \\(O(log N)\\) 의 시간복잡도로 풀 수 있다면 어떨까? 즉 거듭제곱 연산을 더 빠르게 할 수 있는 방법이 있다. “이진 거듭제곱”이라는 방법이다. 이 방법은 거듭제곱에 대한 다음의 성질을 이용한다. 

1. \\(a^{2n} = (a^n)^2\\)
2. \\(a^{2n+1} = a \ \times \ a^{2n}\\)

지수가 짝수일 경우 이를 반으로 나눠 지수를 분리할 수 있고, 홀수일 경우 지수의 1에 해당하는 부분을 따로 분리할 수 있다는 사실을 이용하는 것이다. 

이 두 가지 사실을 기반으로 10진수로 표현된 지수를 2진법으로 변환한다. 예를 들어 \\(3^{11}\\) 이라는 수가 주어져있다면 십진수인 11은 이진수로 1101로 표현할 수 있다. 이 이진수를 다시 십진수로 변환할 때는 다음의 과정을 거친다. 

\\[
1101_{(2)} = 2^3 + 2^2 + 2^0 = 8 + 4 + 1
\\]

즉 \\(3^{11}\\)은 다음과 같이 분해할 수 있다. 

\\[
3^{11} = 3^8 \times 3^4 \times 3^1
\\]

즉, 이와 같이 지수 부분을 이진수로 표현한 다음, 이진수의 각 자리수가 1인 경우에 한해 거듭제곱을 분해하는 방법인 것이다. 위 수식에서는 3개의 항만 계산하면 \\(3^{11}\\)의 값을 구할 수 있는 것이다. 이는 3을 11번 곱하여 결과를 얻는 방식보다 훨씬 효율적인 것을 알 수 있다. 

이러한 이진 거듭제곱은 지수 n을 절반씩 나눠서 이진수로 얻는 과정을 거치므로 이진 거듭제곱 알고리즘의 시간복잡도는 \\(O(log N)\\) 임을 알 수 있다. 

이러한 이진 거듭제곱 알고리즘은 다음과 같이 구현할 수 있다. 자바 언어로 구현하였다. 

```java
// 반복문을 이용한 방식
public static int customPow(int base, int exp) {
    int currentBase = base;
    int currentExp = exp;
    int result = 1;

    while (currentExp > 0) {
        if (currentExp % 2 == 1) {
            result *= currentBase;
        }
        currentExp = currentExp / 2;
        currentBase *= currentBase;
    }
    return result;
}
```

코드 1-1.

위 코드의 과정을 설명해보면 다음과 같다. 

1. currentBase라는 변수를 선언하여 여기에 인자로 들어온 base(밑 부분)를 대입한다. 
2. currentExp라는 변수를 선언하여 여기에 인자로 들어온 exp(지수 부분)의 값을 대입한다. 
3. 연산 결과를 담을 result 변수를 선언하고 1로 초기화한다. 
4. 반복문을 통해 currentExp가 0이 될 때까지 이 값을 2로 나누는 연산을 실행한다. 이 때 2로 나눈 나머지가 1일 경우 currentBase를 result에 곱하여 대입한다. 
5. currentExp에는 2로 나눈 몫을 저장하고, currentBase에는 현재 들어있는 그 값을 제곱한 후 그 값을 다시 currentBase에 대입한다. 
6. 반복문을 다 돌고 나면 result의 값을 반환한다. 

currentBase에는 반복문을 실행할수록 \\(a,\ a^2,\ a^4,\ a^8,\ a^{16},\ ...\\) 와 같은 방식으로 늘어날 것이다. 이는 앞서 언급한 지수를 이진수로 변환하여 계산하는 방식과 일치한다. 그리고 현재 지수를 2로 나눈 나머지가 1일 경우에만 결과에 추가하는 것도 위 코드에 반영되어있음을 볼 수 있다. 

참고로 똑같은 알고리즘을 다음과 같이 분할 정복 방식으로도 구현할 수 있다. 

```java
// 분할 정복 방식
public static int customPowRecursive(int base, int exp) {
    if (exp == 0) return 1;
    if (base == 1) return base;

    int withHalfPow = customPowRecursive(base, exp / 2);

    if (exp % 2 == 0) {
        // if exp is even,
        // base^exp = base^{exp/2} * 2
        return withHalfPow * withHalfPow;
    }
    // if exp is odd,
    // base^exp = base^[{exp/2}+1] = base^{exp/2} * base
    return withHalfPow * withHalfPow * base;
}
```

코드 1-2. 

이러한 이진 거듭제곱 알고리즘을 응용할 수 있는 것 중 하나는 바로 모듈러 거듭제곱법이다. 

# 모듈러 거듭제곱법 (Modular exponentiation)

## 모듈러 연산의 정의와 성질

두 정수를 나눌 때 나머지만 필요할 경우도 있을 것이다. 두 정수 A, B에 대해 A를 B로 나눈 나머지만을 얻고자 할 때 사용하는 것이 모듈러 연산자이다. 모듈러 연산자는 mod 또는 `%` 로 표기한다. A를 B로 나눈 나머지는 다음과 같은 수식 형태로 표현할 수 있다. 

\\[
A \ mod \ B = \ R  \ \ or \ \ A\ \% \ B = R
\\]

예를 들어 10 % 3 = 1이고, 19 % 5 = 4와 같이 나머지를 구할 수 있는 것이다. 

한 편 모듈러 연산의 덧셈, 뺄셈, 곱셈에는 다음과 같은 성질이 있다고 한다. 

\\[
(A \pm B)\ mod \ C = (A\ mod \ C + B \ mod \ C)\ mod C
\\]

\\[
(A \times B)\ mod \ C = (A\ mod\ C \times B\ mod \ C)\ mod C
\\]

위 식을 증명하고자 한다면 우선 위 식이 옳다고 가정한 후, a를 b로 나눈 몫과 나머지를 각각 q, r이라 할 때 a = bq + r 이라는 공식을 대입하여 정리한 양변의 결과가 같음을 증명하는 방식으로 진행하면 된다고 한다. 여기서는 공식과 개념, 그리고 이를 코드로 구현하는 것에 대해서만 집중할 것이므로 증명은 생략하겠다. 

## 모듈러 거듭제곱 알고리즘 구현

앞서 살펴본 모듈러 곱셈의 성질과 이진 거듭제곱법을 결합하면 모듈러 거듭제곱도 구할 수 있다. 즉, 

\\[
a^n \ mod \ m
\\]

의 값도 기존 \\(O(N)\\) 에서 \\(O(log N)\\) 의 시간복잡도로 더 빠르게 그 값을 구할 수 있다. 

코드로 구현할 때에는 앞서 구현했던 이진 거듭제곱법 코드에서 base, result 부분에 mod 연산을 추가하기만 하면 구현할 수 있다. 다음은 이진 거듭제곱법을 응용하여 모듈러 거듭제곱법을 구현한 코드이다. 

```java
// 반복문을 이용한 방식
public static int customMod(int base, int exp, int mod) {
    int currentBase = base % mod;
    int currentExp = exp;
    int result = 1;

    while (currentExp > 0) {
        if (currentExp % 2 == 1) {
            result = (result * currentBase) % mod;
        }
        currentExp = currentExp / 2;
        currentBase = (currentBase * currentBase) % mod;
    }
    return result;
}

// 분할 정복을 이용한 방식
public static int customModRecursive(int base, int exp, int mod) {
    if (exp == 0) return 1;
    if (base == 1) return 1;
    if (mod == 1) return 0;

    int withHalfPow = customModRecursive(base % mod, exp / 2, mod);

    if (exp % 2 == 0) {
        // if exp is even,
        // base^exp = base^{exp/2} * 2
        return (withHalfPow * withHalfPow) % mod;
    }
    // if exp is odd,
    // base^exp = base^[{exp/2}+1] = base^{exp/2} * base
    return (withHalfPow * withHalfPow * base) % mod;
}
```

코드 2-1.

# 글을 마치며…

이진 거듭제곱법은 언뜻 보면 동적 계획법과 비슷해보인다. 큰 문제를 작은 문제로 쪼개어 풀기 위해 재귀 함수를 이용한 분할 정복 방식을 사용하였고, 거듭제곱된 수를 currentBase와 같은 변수에 기록해나가는 측면에서 말이다. 하지만 한 가지 차이점은 동적 계획법은 여러 개로 쪼개진 하위 문제들의 연산이 서로 겹칠 때 중복되는 계산을 피하기 위해 값을 메모리에 메모하고 이를 나중에 재사용하는 방식이다. 그러나 위에서 보인 이진 거듭제곱법은 한 번 연산된 결과를 직접 다시 참조하여 사용하는 방식은 아니기에 동적계획법이라 보긴 어렵다는 생각이다. 실제로 자료 조사를 했을 때에도 이진 거듭제곱법을 소개하는 글에 동적계획법도 언급되는 일은 없었다. 동적 계획법에서는 메모이제이션을 위해 보통 배열을 이용하는 것도 이진 거듭제곱법과의 차이점이기도 하다. 따라서 이진 거듭제곱법은 차라리 분할정복법에 더 가깝다고 볼 수 있을 것 같다. 

솔직히 지수가 매우 큰 경우의 거듭제곱을 구하는 문제를 백준에서 마주치지 못했으면 이러한 알고리즘도 있다는 것을 알지 못했을 것 같다. 코딩 테스트 연습 겸 문제해결 능력을 기르기 위해 요즘 자주 백준에서 문제를 풀고 있는데, 덕분에 새로운 알고리즘들도 알아가는 것 같아서 유익하다. 문제를 많이 푸는 것도 중요하겠지만은, 그것보다는 문제를 상대적으로 적게 풀더라도 새로운 알고리즘을 발견하게 되면 이를 정리하는 것에 더 집중해야겠다는 생각이 든다. 문제를 풀다보면 같은 알고리즘을 요구하는 경우도 있기 때문이기도 하다. 앞으로도 코딩 테스트 문제를 풀다가 새로운 알고리즘을 마주치게 되면 틈틈이 이렇게 글로 정리할 생각이다. 

---

References

[1] 모듈로 연산 

[모듈러 연산( modular, 나머지 )](https://louanna97.tistory.com/93)

[2] 모듈로 연산

[Khan Academy](https://ko.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/what-is-modular-arithmetic)

[3] 이진 거듭제곱 (Binay Exponentiation)

[이진 거듭제곱(Binary Exponentiation)](https://velog.io/@shjk0531/%EC%9D%B4%EC%A7%84-%EA%B1%B0%EB%93%AD%EC%A0%9C%EA%B3%B1Binary-Exponentiation)

[4] `Math.pow()` & 이진 거듭제곱

[Math.pow() 함수에 대한 정리](https://velog.io/@littlegiant/Math.pow-%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)

[5] 모듈로 연산

[Khan Academy](https://ko.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/fast-modular-exponentiation)