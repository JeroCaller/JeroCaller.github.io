---
title: "[Spring] Ecplise와 Maven으로 Spring 환경 만들기"
category: "Spring"
tag: ["Spring", "Java", "Eclipse", "Maven", "DI", "IOC"]
---

# Eclipse에서 Maven으로 환경 만들기

원래 필자는 웹 프로그래밍 쪽을 공부하고 있으나 여기서는 기초를 위해 우선은 자바 애플리케이션으로 진행하겠다. 

이클립스에서 Maven project로 만들면 외부 라이브러리를 jar 형태로 다운로드 받아 일일이 설치하지 않아도 자동으로 설치해주는 이점이 있다. 아무튼 여기서는 Maven 프로젝트를 이클립스에서 만들어보는 과정을 살펴보겠다. 

맨 처음 워크스페이스를 처음 만들면 다음과 같은 목록이 뜰 텐데 여기서 바로 “Create a Maven project”를 클릭하여 만들어도 된다. 

![화면 캡처 2024-10-17 092548.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/1.png)

아니면, 다음과 같이 [File] → [New] → [Maven Project]를 클릭해도 된다. 

![20241017_09260039.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/2.png)

그러면 다음의 창이 뜬다. 

![화면 캡처 2024-10-17 092629.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/3.png)

위 창에서 [Create a simple project]에 체크를 하고 [Next]를 누른다. 

![화면 캡처 2024-10-17 092659.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/4.png)

그러면 위와 같은 창이 뜨는데, Group id는 아무렇게나 지어도 된다. 단, Artifact id는 프로젝트 폴더명이 되기에 이에 주의한다. 다 지었으면 [Finish]를 누른다. 그러면 다음과 같이 Maven Project 폴더가 새로 생성된다. 

![화면 캡처 2024-10-17 092716.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/5.png)

외부 라이브러리를 가져오는 것은 pom.xml에서 할 수 있다. 해당 파일에 가져오고자 하는 외부 라이브러리 정보를 저장만 해도 자동으로 그에 해당하는 jar 파일이 설치된다. 

원하는 라이브러리의 xml 형태 정보를 얻어오려면 다음의 사이트에서 검색하면 된다.

[Maven Central](https://central.sonatype.com/)

스프링을 사용하려면 “spring-context”와 “spring-core” dependency가 필요하다. 위 사이트에서 해당 키워드로 검색하면 다음과 같이 찾을 수 있다. 

![스크린샷 2024-10-17 175440.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/6.png)

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.1.10</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>6.1.10</version>
</dependency>
```

앞서 Maven 프로젝트를 생성할 때 같이 생성된 pom.xml의 첫 모습은 다음과 같을 것이다. 

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>project</groupId>
  <artifactId>FirstSpring</artifactId>
  <version>0.0.1-SNAPSHOT</version>
</project>
```

여기에 앞서 얻은 스프링 프레임워크에 대한 의존성을 주입하려면 다음과 같이 작성한다. 

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>project</groupId>
  <artifactId>FirstSpring</artifactId>
  <version>0.0.1-SNAPSHOT</version>

  <dependencies>
    <dependency>
	  <groupId>org.springframework</groupId>
	  <artifactId>spring-context</artifactId>
	  <version>6.1.10</version>
	</dependency>
	<dependency>
	  <groupId>org.springframework</groupId>
	  <artifactId>spring-core</artifactId>
	  <version>6.1.10</version>
	</dependency>
</dependencies>

</project>
```

pom.xml

즉, `<dependencies>` 태그를 생성한 후, 해당 태그의 자식 태그로 앞서 얻은 `<dependency>` 태그들을 넣으면 된다. 그 후 pom.xml을 저장하면 원래는 없었던 “Maven Dependencies”라는 폴더가 자동으로 생성된다. 

![화면 캡처 2024-10-17 175929.png](/images/2024-10-16/Eclipse-Maven-Spring-environment/7.png)

pom.xml에 외부 라이브러리에 대한 디펜던시를 걸어 저장만 해도 자동으로 관련 jar 파일이 등록되는 것을 알 수 있다. 기존에는 개발자가 일일히 외부 라이브러리 또는 프레임워크의 jar 파일을 다운로드받아 프로젝트 폴더 내 WEB-INF/lib 폴더 내에 저장하고 따로 build path 설정까지 걸어야 하는 불편함이 있었다. 그러나 이제는 pom.xml만 있으면 자동으로 이를 처리해준다. 

자바 클래스는 src/main/java 내에 작성하면 된다. 

# DI, IOC 직접 확인해보기

이제 마련된 환경 내에서 DI, IOC를 직접 확인해보자. 사실 스프링 프레임워크를 이용하여 개발자가 작성한 클래스를 스프링 컨테이너가 생성 및 관리할 수 있는 bean으로 등록해주는 방법은 크게 두 가지 방법이 있다. 하나는 xml 파일에 등록하는 방법으로, 이는 옛날식 방법이라고 한다. 또 하나는 Annotation을 이용하는 방법으로, 최근에는 이를 사용한다고 한다. 여기서는 두 가지 방법 모두 살펴보겠다. 

## xml 방식 이용해보기

xml 방식에 대한 기본적인 내용은 다음의 공식 사이트를 참고.

[Container Overview :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/beans/basics.html)

다음의 기본 구조를 사용하면 된다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">
	<!-- 이 안에 bean 등록 -->

</beans>
```

`<beans>` 태그 안에 `<bean>` 태그를 사용하여 개발자가 작성한 POJO를 등록하면 된다. 해당 파일은 보통 Maven Project 폴더 내에 src/main/resources 폴더 내에 위치한다. xml 파일 이름은 아무렇게나 줘도 된다. 

`<bean>` 태그 사용 형태는 대략 다음과 같다. jsp 표준 액션 태그로 java bean을 사용했을 때와 거의 유사하다. 

```xml
<!-- 
1. class 속성에는 등록할 자바 클래스명을, id에는 해당 클래스로부터 생성한 객체를 참조하는 
참조 변수명을 작성한다.  

다음의 코드는 스프링 컨테이너가 객체 생성 시 다음의 코드와도 같다.
MyClass myobj = new MyClass();

그런데 한 가지 주의할 점은, 기본적으로는 싱글톤 방식으로 객체를 생성한다고 한다. 
-->
<bean id="myObj" class="pack.business.MyClass"/>
<bean id="..." class="..." />
<bean id="..." class="..." scope="prototype"/> <!-- 별도의 객체들을 여러 번 생성하고자 할 때 -->
<bean id="..." class="..." scope="singleton"/> <!-- 기본값. 생략 시 이 값으로 설정됨. -->

<!--
2. 의존성 주입
-->
<bean id="myObj" class="pack.business.MyClass">
	<construct-arg>
		<value>223</value> <!-- 생성자 의존성 주입 -->
		<!-- construct-arg의 value 속성값으로 값을 정해도 된다. -->
	</construct-arg>
</bean>

<bean id="someDao" class="pack.db.SomeDao" />
<bean id="myObj2" class="pack.business.MyClass">
	<construct-arg>
		<!-- ref 태그와 bean 속성을 통해 다른 bean 객체를 주입할 수 있다. -->
		<ref bean="someDao" /> <!-- 생성자 의존성 주입 -->
	</construct-arg>
</bean>

<!-- setter 의존성 주입 -->
<bean id="yourObj1" class="pack.business.YourClass" />
<bean id="myObj3" class="pack.business.MyClass">
	<!-- name : setter 메서드의 set을 제외한 나머지 이름. -->
	<property name="yourObj">
		<ref bean="yourObj1" /> <!-- 다른 bean 객체 참조하여 setter 메서드에 주입 -->
		<!-- 역시 <value> 태그를 써도 되며, property 태그의 ref, value 속성을 사용해도 됨. -->
	</property>
</bean>
```

그 후, 해당 xml을 통해 스프링이 객체 생성 및 관리를 하게끔 하려면 자바에서 다음의 코드를 사용해야 한다.

```java
package pack.business;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	public static void main(String[] args) {
	  // xml 환경 설정 파일명을 인자로 전달하여 
	  // 스프링 컨테이너가 이를 토대로 객체 생성 및 관리를 하게끔 
	  // bean 정보를 넘긴다. 
		ApplicationContext context = 
			new ClassPathXmlApplicationContext("BeanContainer.xml");
		
		// xml 환경 설정을 load한 상태에서, 
		// 해당 파일에 설정된 bean들 중 특정 bean 객체를 얻는다. 
		BusinessLogic businessLogic = context.getBean(
				"businessLogic", BusinessLogic.class
		);
		businessLogic.printData();
	}

}

```

실습을 통해 간단하게 살펴보겠다. 다음은 간단한 데이터들을 준비하여 이를 dao 클래스에서 반환하도록 하고, 비즈니스 로직 클래스에서 이 데이터를 받아 출력하는 간단한 예제이다. 

```java
package pack.model;

import java.util.Arrays;
import java.util.List;

public class SomeDao {
	
	public List<String> selectAllData() {
		List<String> dataList = Arrays.asList("강아지", "고양이", "거북이", "기린");
		return dataList;
	}
	
}

```

SomeDao.java

위 코드에서는 그저 간단한 데이터들을 준비해보았다. 

다음은 위 dao 클래스로부터 데이터들을 받아 출력하는 역할의 비즈니스 로직 클래스 코드이다. 

```java
package pack.business;

import java.util.List;

import pack.model.SomeDao;

public class BusinessLogic {
	private SomeDao someDao;
	
	// 생성자 의존성 주입.
	public BusinessLogic(SomeDao someDao) {
		this.someDao = someDao;
	}
	
	void printData() {
		List<String> allData = someDao.selectAllData();
		
		System.out.println("=== 동물 농장 ===");
		allData.forEach(animal -> {
			System.out.println(animal);
		});
	}
}

```

BusinessLogic.java

위 클래스에서는 외부로부터 dao 객체를 주입받아야 하는 상황이다. 위 클래스에서는 이를 생성자를 통해 주입받로고 설정되어 있다. 이러한 의존성 주입은 xml 파일에 명시하면 스프링 컨테이너가 알아서 진행해줄 것이다. 

다음은 main 메서드가 포함된 클래스의 코드이다. 

```java
package pack.business;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	public static void main(String[] args) {
		ApplicationContext context = 
			new ClassPathXmlApplicationContext("BeanContainer.xml");
		
		// bean 이름은 xml 파일 내 `<bean id="..."/>` 에 있는 id값을 그대로 사용한다. 
		BusinessLogic businessLogic = context.getBean(
				"businessLogic", BusinessLogic.class
		);
		businessLogic.printData();
	}

}

```

Main.java

이제 자바에서는 모든 준비가 완료되었다. 이제 src/main/resources 폴더 내에 BeanContainer.xml 파일을 생성하고 다음과 같이 코드를 작성하였다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">
	<!-- 이 안에 bean 등록 -->
	<bean id="someDao" class="pack.model.SomeDao" />
	<bean id="businessLogic" class="pack.business.BusinessLogic">
		<constructor-arg>
			<ref bean="someDao" />
		</constructor-arg>
	</bean>

</beans>
```

BeanContainer.xml

위 xml 파일에서는 BusinessLogic 인스턴스의 생성자를 통해 SomeDao 인스턴스가 주입되도록 설정하였다. 이제 Main.java를 콘솔창에서 실행해보면 다음의 결과를 얻는다.

```xml
=== 동물 농장 ===
강아지
고양이
거북이
기린

```

Main.java 실행결과

이렇게 xml 설정 파일을 이용하여 스프링 컨테이너가 알아서 개발자가 만든 객체를 생성하고 의존성을 주입해주도록 하는 방법에 대해 살펴보았다. 

이번에는 어노테이션 방법을 살펴보자.

## Annotation 방식 살펴보기

Annotation 방식에도 일부는 XML파일을 이용하는 방법과 완전히 Annotation 만 이용하는 방법으로 나뉜다. 일단은 일부 기능은 XML 파일을 이용하는 방법에 대해 살펴보겠다. 

XML 파일을 이용하는 것은 각각의 클래스들을 bean으로 등록한 후, 스프링 컨테이너가 어디에 bean이 존재하는지를 스캔할 수 있도록 그 위치를 알려주는 역할을 한다고 보면 되겠다. 

Annotation 방식에서의 XML 파일 기본 구조는 앞서 본 XML 파일 구조와 조금 다르다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd">

</beans>
```

위 상태에서 Annotation을 이용하여 각각의 클래스들을 bean으로 등록한 후 이들을 스프링 컨테이너가 스캔할 수 있도록 하려면 다음의 태그를 사용해야 한다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd">
	
	<!-- bean으로 등록된 클래스가 존재하는 패키지 경로 알려주기 -->
	<context:component-scan base-package="pack.business" />
	
</beans>
```

만약 bean으로 등록된 클래스들이 여러 패키지에 있을 경우 다음과 같이 쉼표로 구분하여 등록할 수 있다.

```xml
<context:component-scan base-package="pack.business, pack.model" />
```

이제 앞서 살펴본 간단한 예제에서 Annotation을 이용해보겠다. 

```java
package pack.model;

import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Repository;

@Repository
public class SomeDao {
	
	public List<String> selectAllData() {
		List<String> dataList = Arrays.asList("강아지", "고양이", "거북이", "기린");
		return dataList;
	}
	
}

```

SomeDao.java

개발자가 작성한 클래스를 스프링 컨테이너에게 bean으로 등록하려면 다음의 annotation을 사용할 수 있다. 

- `@Component` - 기본적인 Annotation. 개발자가 작성한 클래스를 bean으로 등록한다.
    - 만약 외부 라이브러리에서 제공하는 클래스를 bean으로 등록하고자 한다면 대신 `@bean` 어노테이션을 사용한다. 이 때, 해당 객체를 반환하는 메서드 생성 후, 해당 메서드에 해당 어노테이션을 적용하면 된다.
    - 이 어노테이션이 적용된 클래스에 대해, `@Component` 라고만 작성하면 해당 클래스의 이름을 카멜 케이스로 변경한 이름이 bean id로 적용되며, context에서 해당 이름을 사용하면 된다. bean id명을 변경하고자 한다면 `@Component(value = "...")` 와 같이 value 파라미터를 이용하면 된다.
- `@Service` - `@Component` 와 그 기능은 같으나, 해당 클래스가 “비즈니스 로직”을 구현하는 클래스임을 명시하고자 할 때 쓰인다.
- `@Repository` - `@Component` 와 그 기능은 같으나, 해당 클래스가 “DB 연동 로직”을 구현하는 클래스임을 명시하고자 할 때 쓰인다.

즉, 위의 두 Annotation들은 `@Component` 와 그 기능은 같으나 가독성을 위해 역할적으로 나뉜 것이라 보면 된다. 위 SomeDao 클래스는 엄연히 DB 작업 (실제로 DB와 연결한 건 아니지만)을 하는 클래스이기에 이를 명시하기 위해 `@Component` 대신 `@Repository` 를 사용하였다. 사실 `@Component` 를 사용해도 기능적으로는 문제 없다. 

```java
package pack.business;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import pack.model.SomeDao;

@Service
public class BusinessLogic {
	private SomeDao someDao;
	
	// 생성자 의존성 주입.
	@Autowired
	public BusinessLogic(SomeDao someDao) {
		this.someDao = someDao;
	}
	
	void printData() {
		List<String> allData = someDao.selectAllData();
		
		System.out.println("=== 동물 농장 ===");
		allData.forEach(animal -> {
			System.out.println(animal);
		});
	}
}

```

BusinessLogic.java

생성자에 SomeDao 객체라는 의존성 주입을 위해 해당 생성자 메서드에 `@Autowired` 어노테이션을 적용하였다. 

`@Autowired` 어노테이션

- Type에 따라 Bean을 자동 주입해주는 어노테이션이다. 위 예제에서 BusinessLogic이라는 클래스는 외부에 존재하는 SomeDao 타입의 객체를 주입받아야 했기에 해당 어노테이션을 사용하였다. 해당 어노테이션은 생성자뿐만 아니라 setter 메서드 또는 심지어는 필드 자체에도 적용할 수 있다. 다음은 그 예이다.

```java
// Pseudo code

@Compoenet
public class Wow {
		
	@Autowired // 필드 의존성 주입
    priavate OtherObj otherObj;
    
    @Autowired  // 생성자 의존성 주입
    public Wow(OtherObj otherObj) {
        this.otherObj = otherObj;
    }
    
    @Autoewired // setter 메서드 의존성 주입
    public void setOtherObj(OtherObj otherObj) {
        this.otherObj = otherObj;
    }
}
```

autowired annotation 사용 예

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context
		https://www.springframework.org/schema/context/spring-context.xsd">
	
	<context:component-scan base-package="pack.business, pack.model" />
	
</beans>
```

registerAnno.xml

두 개의 패키지에서 어노테이션을 사용하여 클래스를 bean으로 등록하였기에 이들을 스캔할 수 있도록 하기 위해 두 패키지 경로를 모두 입력해주었다. 

```java
package pack.business;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {

	public static void main(String[] args) {
		ApplicationContext context = 
			new ClassPathXmlApplicationContext("registerAnno.xml");
		
		/*
			@Service
			public class BusinessLogic  -> businessLogic
		*/
		BusinessLogic businessLogic = context.getBean(
				"businessLogic", BusinessLogic.class
		);
		businessLogic.printData();
	}

}

```

Main.java

xml 파일도 “registerAnno.xml” 파일을 읽어오도록 하였다. 위 클래스에서 실행하면 콘솔창에서 다음의 결과를 얻는다.

```java
=== 동물 농장 ===
강아지
고양이
거북이
기린

```

Main.java 콘솔창 출력 결과

한 편, bean을 스캔하기 위한 환경 설정 조차도 xml이 아닌 어노테이션으로 처리하는 방법이 있다. 

```java
package pack.business;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ClassPathXmlApplicationContext;

@Configuration
// package가 하나일 경우 basePackages = "mypack" 과 같이 단일한 문자열 값으로 줘도 됨.
@ComponentScan(basePackages = {"pack.model", "pack.business"})
public class Main {

	public static void main(String[] args) {
		//ApplicationContext context = 
		//	new ClassPathXmlApplicationContext("registerAnno.xml");
		
		// bean을 얻기 위한 컨텍스트 타입이 다름.
		
		// 이 클래스에서 애플리케이션이 실행되므로 
		// 생성자에 이 클래스의 class를 전달하도록 함. 
		AnnotationConfigApplicationContext context 
			= new AnnotationConfigApplicationContext(Main.class);
		
		BusinessLogic businessLogic = context.getBean(
				"businessLogic", BusinessLogic.class
		);
		businessLogic.printData();
		
		// AnnotationConfigApplicationContext에서는 사용 후 
		// close() 메서드를 호출하여 메모리 상의 자원을 반환하도록 하는 것이 좋다. 
		context.close();
	}

}

```

Main.java

```java
=== 동물 농장 ===
강아지
고양이
거북이
기린

```

Main.java 실행결과

여기에서는 `@Configuration` 과 `@ComponentScan` 어노테이션이 같이 쓰이며, 컨텍스트 객체도 `AnnotationConfigApplicationContext` 라는 객체를 이용한다. 

참고로, xml과 어노테이션 방식을 같이 사용할 경우 xml이 더 우선순위가 높기에, xml에 작성된 설정이 우선적으로 적용된다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] Eclipse Maven 설정

[이클립스 메이븐 프로젝트 초기세팅](https://shurimp.tistory.com/43)

[4] [[Java] Eclipse에서 Maven Project로 구현하기](https://velog.io/@mina_dev/Java-Eclipse에서-Maven-Project로-구현하기)

[5] IoC Container, xml-based configuration

[Container Overview :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/beans/basics.html)

[6] [[Spring] 스프링 XML 설정 파일 작성 방법 정리](https://atoz-develop.tistory.com/entry/Spring-스프링-XML-설정-파일-작성-방법-정리)

[7] xml 에서 component-scan의 base-package 에 여러 개의 패키지 등록하는 방법. 

[스프링 component-scan 개념 및 동작 과정](https://velog.io/@hyun-jii/스프링-component-scan-개념-및-동작-과정)