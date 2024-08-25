---
title: "[Java] Call by value Vs Call by Reference"
category: "Java"
tag: ["java", "call by value", "call by reference"]
---

# Call by Value VS Call by Refence

메서드의 매개변수에 값(원시값)을 전달하여 해당 메서드를 호출하는 방식을 Call by Value라 하고, 객체의 메모리 주소값을 전달하여 메서드를 호출하는 방식을 Call by Reference라 한다. 

```java
import java.util.Arrays;

public class Callby {
	
	public static void setVar(int var) {
		var = 10;
	}
	
	public static void setArr(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			arr[i] += 10;
		}
	}
	
	public static void printArray(int[] arr) {
		System.out.println(Arrays.toString(arr));
	}

	public static void main(String[] args) {
		int myNum = 10;
		System.out.println("myNum: " + myNum);
		
		setVar(myNum);  // Call by Value
		System.out.println("myNum after: " + myNum);
		System.out.println();
		
		int[] myNums = {1, 2, 3};
		System.out.print("myNums: ");
		printArray(myNums);
		System.out.println();
		
		setArr(myNums);  // Call by Reference
		System.out.println("myNums after: ");
		printArray(myNums);
		System.out.println();
	}

}

```

예제 1-1

```java
myNum: 10
myNum after: 10

myNums: [1, 2, 3]

myNums after: 
[11, 12, 13]

```

예제 1-1 실행결과

위 예제의 setVar() 메서드를 호출할 때 myNum이라는 변수를 인자로 넘겼는데, 해당 변수에는 int형 원시값이 할당되어 있다. 즉, 값을 직접 메서드에 넘기는 방식으로, Call by Value라 할 수 있다. Call by Value 방식에서는 인자의 값이 매개변수에 복사가 되어 해당 메서드 내부에서 사용된다. 따라서 함수 안에서 사용될 매개변수와 메서드 외부에서 전달된 인자는 그 값이 같더라도 서로 다른 메모리에 각각의 값이 할당되어 있는 상태이다. 따라서 메서드 내부에서 외부 값을 변경할 수는 없다. 위 예제의 setVar 메서드를 보면 매개변수로 전달된 변수의 값을 10이라는 새로운 값으로 대체하려고 하고 있다. 그러나 이러한 시도로는 외부에 있는 myNum의 변수에 할당된 값을 변경할 수 없다. 그래서 메서드 호출 전과 후의 결과가 같은 것이다. 

반면, setArr() 메서드에는 myNums라는 int형 배열 객체를 인자로 전달하고 있다. myNums는 원시값이 저장된 변수가 아닌, 해당 객체의 주소가 담긴 참조 변수이다. 따라서 메서드의 매개변수에도 해당 참조 변수에 담긴 객체의 메모리 주소값이 전달된다. 그러면 메서드 내부의 매개변수에 전달된 객체의 메모리 주소값을 통해 해당 객체에 접근하여 값을 바꿀 수 있게 된다. 실제로 위 예제에서 setArr 메서드를 호출하기 전과 후의 myNums의 배열값을 보면 모두 바뀌어 있는 것을 확인할 수 있다.  이렇게 참조 변수를 메서드의 인자로 전달하여 메서드를 호출하는 방식이 Call by Reference인 것이다. 

# 엄밀히 말하면…

그런데 사실 엄밀히 말하면 자바에는 Call by Reference란 개념이 없다고 한다. 객체의 메모리 주소를 메서드의 인자로 넘기는 방식도 사실 자바에서는 Call by Value로 본다고 한다. 이게 어찌된 일일까? 

사실 어떤 메서드의 호출 방식이 Call by Reference라 할려면, 메서드의 인자로 넘겨진 원본 참조 변수가 담고 있는 메모리 주소값 자체를 메서드 내부에서 바꿀 수 있어야 한다. 그래서 메서드 외부에 존재하는 원본 참조 변수에 할당된 주소값 자체가 달라질 수 있어야 한다. 즉, 전혀 다른 메모리 공간을 가리키게끔 해야 한다. 그런데 자바에서는 이를 수행할 수 없으므로, 자바에서는 Call by Reference라는 방식이 존재하지 않고, 오로지 Call by Value만 존재한다. 

다음의 코드를 살펴보자.

```java
class Person {
	String name;
	
	Person(String name) {
		this.name = name;
	}
}

public class CallByReferenceDoesntExists {
	
	public static void changePersonName(Person p) {
		p.name = "자바스";
	}
	
	public static void changePerson(Person p) {
		p = new Person("정디비");
	}
	
	public static void main(String[] args) {
	  // stage 1
		Person kim = new Person("김큐엘");
		Person recordKim = kim;
		
		// 어떤 사람의 이름을 바꿀 순 있어도...
		changePersonName(kim);
		System.out.println(kim.name);
		System.out.println(kim == recordKim);
		
		System.out.println("==========");
		
		// stage 2
		// 사람 자체를 바꿀 순 없어!
		Person na = new Person("나이썬");
		Person recordNa = na;
		changePerson(na);
		System.out.println(na.name);
		System.out.println(na == recordNa);

	}
	
}

```

예제 2-1

```java
자바스
true
==========
나이썬
true
```

예제 2-1 실행결과

우선 위 예제에서 kim과 changePersonName() 메서드가 포함된 stage 1 영역의 코드만 보자. `Person kim = new Person("김큐엘");` 이라는 코드가 실행될 때 메모리 상에서는 다음의 그림과 같은 상황이 펼쳐진다. 

![그림 2-1.](/images/2024-08-20/java-call-by-value-call-by-reference/1.png)

그림 2-1.

스택 영역에는 main 메서드에 대한 frame 내부에 kim이라는 참조 변수가 생긴다. 그리고 `new Person(”김큐엘”)`  코드 실행으로 Person 인스턴스 하나가 Heap 영역에 할당된다. 편의상 kim 변수가 존재하는 메모리 공간 주소를 100, “김큐엘”이란 이름을 가지는 Person 인스턴스의 메모리 주소가 500이라고 가정하였다. 

이제 `changePersonName(kim);` 이란 코드를 실행하면 다음과 같이 바뀔 것이다. 

![그림 2-2](/images/2024-08-20/java-call-by-value-call-by-reference/2.png)

그림 2-2

changePersonName() 메서드의 매개변수 p는 인자로 전달된 kim이 가리키고 있는 메모리 주소값 “500”을 “복사받아” 넘겨받게 된다. 그리고 해당 매개변수 p도 똑같이 메모리 주소 500에 있는 Person 인스턴스를 참조하게 된다. 여기서 changePersonName 메서드 내부의 `p.name = “자바스”;` 코드를 통해 메모리 주소 500번에 있던 Person 인스턴스의 필드 name의 값을 “자바스”로 변경할 수 있게 되는 것이다. 

그런데, 이 메서드의 호출 방식이 Call by Reference라 불릴려면 해당 메서드의 호출로 인해 kim이란 참조 변수에 할당된 메모리 주소값 자체가 변경되었어야 했다. 그래서 kim이란 참조변수가 전혀 다른 어떠한 메모리 공간을 가리키고 있어야 했다. 바로 다음처럼 말이다. 

![그림 2-3](/images/2024-08-20/java-call-by-value-call-by-reference/3.png)

그림 2-3

그런데 실제로 kim이란 변수에 담긴 메모리 주소값이 바뀌었는가? 아니다. changePersonName() 메서드가 한 것은 그저 kim이란 참조변수가 가리키는 Person 인스턴스의 필드값만 바꾼 것일 뿐이다. 이걸 Call by Reference라 할 수 없다. 즉, Call by Reference라 부를려면 참조변수가 담고 있는 메모리 주소값 자체를 바꿔버려 해당 참조 변수가 전혀 다른 메모리 공간을 가리켜야 하는 것이다. 그런데 changePersonName() 메서드는 그저 원본 참조 변수 kim이 가지고 있는 참조”값(value)”을 복사하여 이를 해당 메서드에 넘긴 것이므로, 엄연히 말하면 Call by Value 방식인 것이다. 그래서 위 예제의 `System.*out*.println(kim == recordKim);` 코드의 출력결과가 true인 것이다. 만약 정말 Call by Reference였다면 이 출력결과는 false로 나왔어야 했다. 

정말일까? 정말로 자바에서는 Call by Reference가 없을까? 이는 위 예제에서 na라는 참조 변수와 changePerson() 코드가 있는 stage 2 영역을 보면 알 수 있다. 해당 메서드는 아예 작정하고 원본 참조 변수 na에 담긴 메모리 주소값을 바꾸려고 시도하고 있다. 우선 `Person na = new Person("나이썬");` 코드에서의 상황은 앞선 그림 2-1과 그 형태가 똑같다. 그저 name이라는 필드값이 바뀌었을 뿐이다. 

이제 changePerson() 메서드를 호출할 때의 상황을 메모리 영역에서 살펴보자. 

![그림 2-4](/images/2024-08-20/java-call-by-value-call-by-reference/4.png)

그림 2-4

만약 정말로 해당 메서드의 호출 방식이 Call by Reference였다면 정확히 그림 2-3과 같았어야 했다. 즉, 메서드 내부의 매개변수 p가 원본 참조 변수인 na가 가지고 있는 메모리 주소값을 바꿔버려 전혀 새로운 객체의 메모리 주소를 가리키고 있어야 했다. 그런데 위 그림 2-4와 changePerson 메서드 선언부 코드를 같이 보면 전혀 아님을 알 수 있다. 해당 메서드 내부에서 `new Person(”정디비”);` 라는 코드를 통해 우선 전혀 다른 Person 인스턴스를 생성하였다. 원래 의도는 원본 참조 변수 na를 p가 대신한다고 착각하여 p가 Person(”정디비”) 를 가리키도록 함으로써 원본 참조 변수 na도 해당 객체를 가리키도록 하려고 한 것이었다. 하지만 실상은, 매개변수이자 참조변수인 p에는 원래 na 참조변수가 가리키던 메모리 주소 “500”이란 값에서 “600”이란 값으로 변경된 것일 뿐, 원본 참조 변수 na의 참조값에는 아무런 변화가 없다(참조 변수 na에는 여전히 500이라는 메모리 주소값이 저장되어 있는 상황이다). 그래서 위 그림과 같이 p 변수와 na 변수가 가리키는 객체가 서로 다른 것이다. 이러한 방식은 전혀 Call by Reference가 아니다. 

게다가 changePerson() 메서드의 최악인 점은, “정디비”라는 Person 인스턴스를 메서드 내부에서 생성하였기에, 해당 메서드를 빠져나오면 더 이상 “정디비”라는 Person 인스턴스의 메모리 주소를 참조할 참조 변수가 없기 때문에 garbage가 되어버리고 만다. (메모리 공간 낭비했네…)

즉, Call by Reference이려면 메서드 내부에서 외부 참조 변수의 메모리 주소값을 전달받은 매개변수가 외부 참조 변수에 할당된 메모리 주소값 자체를 바꿔버려야 한다. 하지만 위 코드에서 볼 수 있듯 자바에서는 이를 전혀 실행하고 있지 못하고 있다. 

자바에서는 int, char와 같은 원시”값”을 전달하느냐 아니면 객체의 참조”값”을 전달하느냐의 차이일 뿐, 메서드 호출 방식은 엄연히 말해 모두 Call by Value 방식인 것이다. 

하지만 메서드 외부에서는 이러한 차이를 직접 느낄 수 없어 Call by Reference란 표현도 편의상 쓰이는 것 같다. 즉, 여기서의 Call by Reference는 원시값을 전달하느냐 객체의 주소값을 전달하느냐, 그래서 메서드 내부에서 객체의 필드값을 변경할 수 있느냐 없느냐를 구분짓고 이를 설명하기 위해 사용하는 용어이지, 진정한 의미의 Call by Reference를 의미하는 건 아니라고 볼 수 있겠다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 자바에서의 Call by Value Vs Call by Reference

[☕ 자바는 Call by reference 개념이 없다 ❓](https://inpa.tistory.com/entry/JAVA-☕-자바는-Call-by-reference-개념이-없다-❓)

[3] 자바에서의 Call by Value Vs Call by Reference

[Java 의 Call by Value, Call by Reference](https://bcp0109.tistory.com/360)

[4] c언어의 포인터

[코딩교육 티씨피스쿨](https://www.tcpschool.com/c/c_pointer_intro)

[5] [자바는 항상 Call by Value.](https://www.blog.ecsimsw.com/entry/자바는-Call-by-Value-이다)

[6] [[Java] 자바가 Call by Value 방식인 이유](https://dev-coco.tistory.com/189)

[7] [Is Java "pass-by-reference" or "pass-by-value"?](https://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value)

[8] [[개발자의 교양] C언어에는 call by reference가 없다고???](https://velog.io/@ksk0605/개발자의-교양-C언어에는-call-by-reference가-없다고)

[9] [포인터란? - call by reference와 call by value의 차이](https://butter-shower.tistory.com/36)