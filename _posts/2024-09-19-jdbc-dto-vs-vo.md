---
title: "[JDBC] DTO vs VO"
category: "Servlet & JSP"
tag: ["DTO", "VO"]
---

Servlet & JSP 관련 책을 공부하고, 또 학원에서 강의를 들으면서 무언가 서로 충돌하는 개념이 있었다. 책에서는 DB 로직을 포함하는 DAO(Data Access Object)와 같이 사용하는 디자인 패턴이 VO(Value Object)라고 소개되어 있는데, 강의에서는 DTO(Data Transfer Object)로 소개했기 때문이다. 책에 소개된 VO와 강의 시간에 본 DTO는 겉으로 보면 별 차이가 없어보였다. 그런데 설마 똑같은 디자인 패턴을 가지고 이름이 두 개일리는 없다는 생각에 이참에 이렇게 DTO와 VO의 차이점에 대해 정리하게 되었다. 

# DTO (Data Transfer Object)

DTO는 데이터 교환을 위한 객체로, 주로 JDBC와 같은 DB와의 데이터 교환에 사용된다. 순전히 데이터 전송 및 교환이 목적이라 각 필드마다의 getter, setter 메서드만 가질 뿐, 그 외 다른 로직을 포함하지는 않는다. 

```java
package com.design_pattern;

/**
 * DTO 예제 1.
 * 각 필드마다 getter, setter 메서드를 정의할 수 있다. 
 * setter 메서드가 존재할 경우, 그 필드는 가변성을 띈다. 즉, 
 * 중간에 값이 바뀔 수 있다. 
 */
public class DTO1 {
	private String username = null;
	private String email = null;
	
	public String getUsername() {
		return username;
	}
	
	public void setUsername(String username) {
		this.username = username;
	}
	
	public String getEmail() {
		return email;
	}
	
	public void setEmail(String email) {
		this.email = email;
	}
	
}

```

DTO1.java

```java
<%@page import="com.design_pattern.DTO1"%>
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	DTO1 dto1 = new DTO1();
	
	dto1.setUsername("Kimquel");
	dto1.setEmail("kimquel@coding.com");
	System.out.println(dto1.getUsername() + " : " + dto1.getEmail());
	
	// 값 변경 가능 : 가변성을 띔.
	dto1.setUsername("JeongDB");
	dto1.setEmail("JeongDB@db.com");
	System.out.println(dto1.getUsername() + " : " + dto1.getEmail());
%>
```

dtoTest1.jsp

```java
Kimquel : kimquel@coding.com
JeongDB : JeongDB@db.com
```

dtoTest1.jsp 실행결과 (콘솔창)

참고로, DTO에 setter 메서드를 추가하면 그 객체는 가변성을 띈다. 즉, 데이터 전송 중간에 다른 값으로 언제든지 바뀔 수 있다는 것이다. 위의 DTO1 클래스가 그 예이다. 만약 데이터 전송 중간에 DTO 객체가 담고 있는 필드값에 변조가 일어나길 원치 않는 경우, 생성자를 이용해서 최초 한 번만 값을 초기화한 뒤, setter 메서드를 정의하지 않으면 된다. 다음의 DTO2 클래스가 그 예이다. 

```java
package com.design_pattern;

/**
 * DTO 예제 2.
 * setter 메서드가 없고, 대신 생성자를 통해 초기화를 하게 하면, 
 * 맨 처음 생성자를 통해 딱 한 번만 필드들을 초기화하면 그 이후 데이터 전송 중간에 
 * 값을 변경할 수 없게 만들 수 있다. 즉, 값의 불변성을 지킬 수 있어 변조를 방지할 수 있다. 
 */
public class DTO2 {
	private String userName = null;
	private String email = null;
	
	public DTO2(String userName, String email) {
		this.userName = userName;
		this.email = email;
	}

	public String getUserName() {
		return userName;
	}

	public String getEmail() {
		return email;
	}

	@Override
	public String toString() {
		return this.userName + " : " + this.email;
	}
	
}

```

DTO2.java

```java
<%@page import="com.design_pattern.DTO2"%>
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	DTO2 dto2 = new DTO2("Naython", "python@python.com");
	System.out.println(dto2);
	
	// 실수로 두 값이 뒤바뀌어 입력되어도 에러 없이 삽입됨. 
	DTO2 dto22 = new DTO2("Javas@Javascript.com", "Javas");
	System.out.println(dto22);
%>
```

dtoTest2.jsp

```java
Naython : python@python.com
Javas@Javascript.com : Javas
```

dtoTest2.jsp 실행결과

개인적으로 생각했을 때, 위와 같이 setter 메서드를 제거하여 생성자를 통해서만 값을 초기화하는 방식은 불변성을 지키는데에는 좋지만, 생성자의 인자로 전달될 값이 너무 많은 경우, 자료형만 같으면 값이 변수에 저장되는 특성 때문에 서로 다른 필드들이 뒤바뀌어 전달되어도 에러가 발생하지 않아 이러한 실수를 조기에 잡지 못할 수 있다. 따라서 다음 예제와 같이, 생성자는 제거하고 setter 메서드를 넣는 대신, setter 메서드 내부에 이미 이전에 한 번 값이 초기화된 적이 있으면 또 다른 새로운 값을 받지 않도록 고안할 수 있겠다고 생각한다. 

```java
package com.design_pattern;

/**
 * DTO의 값 불변성을 지키기 위해 생성자를 사용하면 
 * 초기화할 필드값이 많아질 때 서로 자료형이 겹치는 필드끼리 값을 뒤바꿔서 넣을 수 있다. 
 * 예를 들면 userName과 email 모두 String 자료형이라 DTO3(userName, email)이라고 해야 하는데 
 * DTO3(email, userName)이라고 해도 에러가 발생하지 않아 실수를 할 수 있다. 
 * 그래서 생성자 사용 대신 setter 메서드에서 값을 딱 한 번만 초기화하도록 고안해보았다. 
 * 이를 이용하면 값이 서로 뒤바뀐채 초기화되는 것을 방지함과 동시에 데이터의 불변성도 만족시킬 수 있을 것이다. 
 */
public class DTO3 {
	private String userName = null;
	private String email = null;
	
	public String getUserName() {
		return userName;
	}
	
	public void setUserName(String userName) {
		if (this.userName != null) return;
		this.userName = userName;
	}
	
	public String getEmail() {
		return email;
	}
	
	public void setEmail(String email) {
		if (this.email != null) return;
		this.email = email;
	}

	@Override
	public String toString() {
		return this.userName + " : " + this.email;
	}
	
}

```

DTO3.java

```java
<%@page import="com.design_pattern.DTO3"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	DTO3 dto3 = new DTO3();

	dto3.setUserName("Javas");
	dto3.setEmail("javas@javascript.com");
	System.out.println(dto3);
	
	// 값 변경 시도.
	dto3.setUserName("Naython");
	dto3.setEmail("Naython@python.com");
	System.out.println(dto3);
%>
```

dtoTest3.jsp

```java
Javas : javas@javascript.com
Javas : javas@javascript.com
```

dtoTest3.jsp 실행결과

위와 같은 방식을 사용하면 값의 불변성도 지키면서 동시에 setter 메서드의 사용으로 인해 서로의 값이 뒤바뀌어 초기화되는 실수도 미연에 방지할 수 있다. 

DTO에 대해 정리하면 다음과 같다.

- 데이터 전송을 목적으로 하는 객체.
- 상황에 따라 값에 가변성을 부여하거나 반대로 불변성을 부여할 수도 있다.
- getter, setter외 로직은 불필요하다. 데이터 전송만을 목적으로 하는 객체이기 때문.

# VO (Value Object)

DTO는 여러 데이터들을 전송하는 목적의 객체이지, 데이터 자체에 큰 의미를 두지는 않는다. 반면 VO는 값 그 자체를 의미하는 객체이다. 즉, VO 객체 내 멤버 변수의 값이 곧 그 객체 자체를 의미하는 형태이다. 따라서 두 VO 객체의 값이 같으면 참조값이 다르더라도 두 객체가 동일한 것으로 간주한다. 

VO 클래스를 정의하려면 반드시 `public boolean equals(Object obj)` 메서드와 `public int hashCode()` 메서드를 오버라이딩하여 두 VO 객체의 값(필드)이 같으면 서로 같은 객체로 인식하게끔 해야 한다. 

다음은 hashCode()와 equals()를 오버라이딩하지 않았을 때의 예제이다.

```java

package com.design_pattern;

public class VO1 {
	private String userName = null;
	
	public VO1(String userName) {
		this.userName = userName;
	}
	
	public String getUserName() {
		return this.userName;
	}

}

```

VO1.java

```java
<%@page import="com.design_pattern.VO1"%>
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	VO1 user1 = new VO1("kimquel");
	VO1 user2 = new VO1("kimquel");
	
	System.out.println(user1.equals(user2));
	System.out.println(user1 == user2);
%>
```

voTest1.jsp

```java
false
false
```

voTest1.jsp 실행결과 (콘솔창)

VO1 클래스는 아직 hashCode와 equals 메서드를 오버라이딩하지 않았기에 두 객체가 아무리 같은 “kimquel”이란 값을 입력받아도 서로 같은 객체로 인식되지 않는다. 그도 그럴 것이, Object 클래스의 equals() 메서드에는 기본적으로 비교하고자 하는 두 객체의 참조값이 같으면 두 객체가 동일한 것으로 인식하게끔 코드가 짜여져 있기 때문이다. 

다음은 두 메서드를 오버라이딩한 후, voTest1.jsp를 그대로 실행한 결과이다.

```java
package com.design_pattern;

import java.util.Objects;

public class VO1 {
	private String userName = null;
	
	public VO1(String userName) {
		this.userName = userName;
	}
	
	public String getUserName() {
		return this.userName;
	}

	@Override
	/**
	 * java.util.Objects.hash() 메서드를 이용하면 굳이 해시 알고리즘을 고안, 구현하지 
	 * 않아도 클래스의 필드값만 전달하면 고유한 해시값을 반환하도록 할 수 있다. 
	 * 클래스 필드가 2개 이상인 경우 모든 필드들을 hash() 메서드의 인자로 넘길 수 있다. 
	 * Objects.hash(this.userName, this.age, this.email); 처럼. 
	 * 그러면 hash() 메서드 내부에서 주어진 필드값들을 토대로 고유한 해시값을 생성하여 반환한다. 
	 */
	public int hashCode() {
		return Objects.hash(this.userName);
	}

	@Override
	/**
	 * 인자로 주어진 obj 객체의 참조값이 이 객체(this)의 참조값과 같은 경우 서로 같은 객체로 판별.
	 * 주어진 obj가 null이거나 애초에 VO1의 인스턴스가 아닐 경우 서로 다른 객체로 판별. 
	 */
	public boolean equals(Object obj) {
		if (!(obj instanceof VO1) || obj == null) return false;
		if (this == obj) return true;
		
		VO1 otherVo = (VO1)obj;
		return this.userName.equals(otherVo.getUserName());
	}
	
}

```

VO1.java (1)

```java
true
false
```

voTest1.jsp 실행 결과 (콘솔창)

VO1 클래스의 userName 필드값이 서로 같으면 서로 같은 객체로 간주하게끔 두 메서드를 오버라이딩한 결과, equals() 메서드를 통해 두 객체를 비교하면 서로 같은 객체로 인식한다는 것을 알 수 있다. 

물론 equals(), hashCode() 메서드를 오버라이딩한다고 해서 두 VO 객체의 참조값도 서로 같아지는 것은 아니다. 단지 두 객체의 어떤 점을 보고 서로 같다고 판단할지 그 기준을 개발자 마음대로 정할 수 있게 된다는 것이다. 

앞서 언급한 VO의 특징과 함께 아직 언급하지 않은 특징들을 같이 정리하면 다음과 같다. 

- VO는 값 그 자체를 의미하는 객체로, 필드값이 서로 같으면 서로 같은 객체로 인식한다.
    - 이를 구현하기 위해선 equals(), hashCode() 이 두 메서드를 오버라이딩해야 한다.
- 객체의 필드값 자체가 객체 자체를 의미하므로, 불변성을 보존하기 위해 setter 메서드는 사용하지 않는다. Read-only (읽기 전용)이기에 getter 메서드만 존재한다.
- DTO와 달리, getter 외 다른 로직 메서드를 포함해도 된다.

# 정리

|  | DTO | VO |
| --- | --- | --- |
| 목적 | 데이터 전송 | 객체가 값 그 자체를 의미. |
| 동등성 | 필드값들이 같아도 같은 객체로 인식하지 않음 | 필드값이 같으면 서로 같은 객체로 인식. |
| 가변성 | 가변성 또는 불변성 둘 중 하나를 가지게 할 수 있다.  | 불변 |
| 그 외 로직 | getter, setter 외에는 다른 로직이 불필요 | 그 외 다른 로직을 포함해도 됨. |

---

References

[1] 에이콘아카데미(강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] DTO vs VO

[[JAVA] DTO와 VO의 차이](https://maenco.tistory.com/entry/Java-DTO와-VO의-차이)

[4] DTO vs VO

[[Spring] DAO와 DTO의 차이 그리고 VO](https://dkswnkk.tistory.com/500)

[5] DTO vs VO

[DTO vs VO vs Entity](https://tecoble.techcourse.co.kr/post/2021-05-16-dto-vs-vo-vs-entity/)

[6] hash 메서드 관련

[Objects.hash() 그리고 Objects.hashCode()](https://velog.io/@gnlals1/Objects.hash-그리고-Objects.hashCode)