---
title: "[Spring][DB] Mybatis"
category: "Spring"
tag: ["Spring", "Java", "DB", "MyBatis", "SQL"]
---

# Mybatis 개요

기존의 JDBC 방식의 DB 연동 작업에는 문제점이 있었다. 

- SQL문을 실행하기 위해 SQL문이 자바 클래스에 내장되어야 한다. 즉, 클래스와 SQL문을 분리할 수 없다. 이로 인해 SQL문을 실행하는 기능에 비해 그 코드가 너무 장황하다.
- 자바 클래스와 SQL문의 결합으로 인해 이들을 분리할 수 없어 코드의 재사용성이 떨어진다.
- 조금씩 달라지는 SQL문으로 인해 전체적인 관점에서 보면 JDBC 자바 클래스 내에서 반복적인 패턴이 나타난다.

Mybatis는 이러한 기존의 JDBC의 문제점을 해결하기 위해 나온 프레임워크로, 개발자가 SQL문을 XML이나 Annotation을 이용하여 자바 클래스로부터 분리하여 작성, 관리할 수 있게 함으로써 기존 JDBC의 문제점을 극복한 프레임워크라 볼 수 있다. 이러한 장점으로 인해 JPA와 함께 Spring 프레임워크에서도 DB 연동 작업을 위해 자주 쓰이는 기술이라고 한다. 

개발자가 작성한 SQL문을 XML 또는 어노테이션으로 따로 분리한 후, 이를 Java 클래스 내 메서드와 자동으로 매핑해주는 역할을 한다. 

# Mybatis 사용을 위한 설정

여기서는 Maven 프로젝트와 스프링 프레임워크 환경의 Java Application 환경 내에서 실습해보겠다. 

우선 DB 연동 작업을 할 것이고, 여기서는 MariaDB를 사용할 것이기에 MariaDB 디펜던시를 pom.xml에 추가한다. 

```xml
<!-- MariaDB -->
<dependency>
  <groupId>org.mariadb.jdbc</groupId>
  <artifactId>mariadb-java-client</artifactId>
  <version>3.1.1</version>
</dependency>
```

Spring 환경에서 Mybatis를 사용하기 위해선 다음의 디펜던시도 pom.xml에 삽입해야 한다. 

```xml
<!-- mybatis -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.5.11</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>3.0.1</version>
</dependency>
```

이 페이지에서는 원활한 실습을 위해 pom.xml에 다음과 같이 여러 디펜던시들을 추가하였다. 

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.example</groupId>
	<artifactId>autowired_example</artifactId>
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
		<dependency>
			<groupId>javax.annotation</groupId>
			<artifactId>javax.annotation-api</artifactId>
			<version>1.3.2</version>
		</dependency>

		<!-- MariaDB -->
		<dependency>
			<groupId>org.mariadb.jdbc</groupId>
			<artifactId>mariadb-java-client</artifactId>
			<version>3.1.1</version>
		</dependency>

		<!-- mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.5.11</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>3.0.1</version>
		</dependency>
		
		<!-- lombok -->
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.34</version>
		</dependency>

	</dependencies>

</project>
```

pom.xml

그 후, 본격적으로 자바에서 DB를 연동하여 사용하기 위해선 다음의 4가지 설정 파일이 필요하다.

- DB 드라이버 및 DB 로그인 정보들이 담긴 .properties 파일
- DB 정보가 담긴 .properties 파일을 토대로 DB와 연동하고, SQL문이 담긴 mapper.xml 파일 정보를 담는 Configuration.xml
- 개발자가 SQL문을 작성하는 xml파일 (xml 파일명은 마음대로 해도 된다)
- xml 파일로 설정된 DB 정보들을 자바 클래스에서 사용하기 위한 클래스.

다음은 그 예이다.

```xml
driver=org.mariadb.jdbc.Driver
url=jdbc:mariadb://127.0.0.1:3306/mvc
username=root
password=1111
```

jdbc.properties

위 파일에는 JDBC driver 및 DB 접속을 위한 url, 유저 네임과 패스워드를 기록한다. 위와 같이 DB 연동 정보를 별도의 파일로 분리하여 보관하면 Github에 공개 레포지토리로 소스 코드를 업로드할 때 민감한 정보가 들어있는 해당 파일은 업로드하지 않고 올릴 수 있기에 보안성을 확보할 수 있다. 

해당 파일명은 아무렇게나 작성해도 된다. db.properties라 해도 된다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<properties resource="jdbc.properties" />
	<environments default="dev">
		<environment id="dev">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="SqlMapper.xml" />
	</mappers>
</configuration>
```

Configure.xml

위 파일 내용을 자세히보면 알겠지만, 위 파일은 DB 연동 및 개발자가 작성한 SQL문이 담긴 XML 파일을 모아놓은 일종의 설정 파일이라 보면 되겠다. Mybatis 공식 문서(References [3] 참조)에서는 이러한 파일을 “Configuration XML” 파일이라고 지칭하고 있다. 

다음은 개발자가 SQL문을 작성하는 XML파일이다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dev">
	<sql id="tableName">
		products
	</sql>
	<sql id="whereId">
		WHERE id=#{id}
	</sql>
	
	<select id="selectAll" resultType="pack.model.ProductDto">
		SELECT id, name, price, category FROM <include refid="tableName"/>
	</select>
	
	<insert id="insertOne" parameterType="pack.model.ProductDto">
		INSERT INTO products 
		VALUES (#{id}, #{name}, #{price}, #{category})
	</insert>
	
	<update id="updateOneProductName" parameterType="pack.model.ProductDto">
		UPDATE <include refid="tableName" /> SET name=#{name} <include refid="whereId" />
	</update>
	
	<delete id="deleteOne" parameterType="String">
		DELETE FROM <include refid="tableName" /> <include refid="whereId"/>
	</delete>

</mapper>
```

SqlMapper.xml

위 파일은 SQL문과 이를 사용하여 DB 연동 작업을 할 자바 객체 간의 매핑을 위한 파일이라 볼 수 있다. 공식 문서에서는 이러한 파일을 Mapper XML File이라 칭하고 있다. 위 파일에서는 CRUD 작업을 위한 SQL문이 모두 작성되어 있다. 

위 파일을 좀 더 자세히 보면 다음과 같이 정리할 수 있겠다. 

- `<select>, <insert>, <update>, <delete>` 태그를 이용하여 각각 CRUD 작업을 위한 SQL문을 작성할 수 있다. 해당 태그에 쓰일 수 있는 attribute들이 있는데 위에서 사용된 attribute들만 소개하면 다음과 같다.
    - id : 자바 객체 내에서 참조할 수 있도록 SQL문에 부여하는 고유의 식별자. 예를 들어 `id="selectAll"` 이라고 작성하면 자바 클래스 내에서 데이터 조회를 위한 코드에서 “selectAll”이라고 참조하면 해당 SQL문을 자바 클래스 내에서 사용할 수 있게 된다.
    - resultType : 해당 SQL문을 자바 클래스 내에서 실행했을 때 그 반환값의 타입을 명시한다. 예를 들어 **`public** List<ProductDto> selectAllProducts()` 와 같은 메서드가 사용될 때 반환값의 타입이 ProductDto라는 객체의 리스트로 반환되므로, 이 경우 `resultType="ProductDto"` 라고 명시하는 것이다. 위 XML 파일에서는 해당 DTO 클래스가 특정 패키지 안에 있으므로 해당 패키지 경로까지 명시하였다.
    - parameterType : 해당 SQL문을 자바 클래스 내에서 실행할 때, 메서드 내에서 사용될 매개변수의 타입을 명시한다. 위 예제의 `<insert>` SQL문을 보면 외부의 사용자로부터 데이터를 입력받아 이 데이터를 DB에 삽입하려는 목적을 가지고 있다. 외부의 사용자가 어떤 데이터를 입력할 지 아직 모르므로 이럴 때는 자바 메서드의 매개변수를 통해 전달받아야한다. 이 때의 매개변수 타입을 명시하는 것이다. 위 `<insert>` 태그에서는 **`public** **boolean** insertOneProduct(ProductDto productDto)`  메서드에서 사용될 것이기에 parameterType도 `ProductDto` 라고 명시하였다.
- `<sql id="...">` : Mapper XML 파일 전체에서 반복되는 일부 쿼리문이 있을 때 반복되는 부분을 제거하기 위해 따로 정의할 수 있는 태그이다.
- `<include refid="..."/>` : `<sql id="...">` 태그를 사용하기 위한 태그이다.

이 외에도 많은 태그와 속성들이 있으니 이는 Mybatis 공식 문서를 참조.

XML 파일로 작성한 설정 및 쿼리문 파일들을 자바 클래스에서 사용하기 위해서는 다음과 같은 클래스가 있어야 한다. 

```java
package pack.model;

import java.io.Reader;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

/**
 * 자바 클래스에서 DB 작업을 하기 위해 필요한 sqlSession 객체를 생성하는 
 * SqlSessionFactory 객체를 반환하는 클래스. 
 * DB 연동 설정 정보가 담긴 XML 파일(Configure.xml)을 가져와 객체화함으로써 
 * 개발자 정의 SQL문이 담긴 SqlMapper.xml를 이용하여 DB 작업이 가능하게끔 
 * 설정하는 코드가 담겨 있다. 
 */
public class SqlMapConfig {
	public static SqlSessionFactory sqlSessionFactory; // DB의 SQL명령을 실행시킬 때 필요한 메소드를 갖고 있다.

	static {
		// xml 내 내용들이 객체화한다. 
		String resource = "Configure.xml";
		try (Reader reader = Resources.getResourceAsReader(resource)) {
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
		} catch (Exception e) {
			System.out.println("SqlMapConfig 오류 : " + e);
		}
	}

	public static SqlSessionFactory getSqlSessionFactory() {
		return sqlSessionFactory;
	}
}
```

SqlMapConfig.java

Mybatis에서는 자바 클래스 내에서 SQL문을 사용하기 위해선 SqlSession이라는 객체를 이용해야 한다. Mybatis에서는 “session”이라는 용어를 사용하는데, 실질적으로 자바 클래스 내에서 DB와 연동한 후 SQL문을 실행하고 다시 DB 연동을 해제하는 일련의 과정을 session이라고 하는 것으로 추측된다(웹 개발 시 사용자 요청을 통해 넘어온 HTTP 상태 정보를 저장하는 기술인 session를 지칭하는 것은 아니다. 다만 DB 서버에서의 session을 의미하는 것으로 추측된다). 

이러한 SqlSession 객체를 생성해주는 SqlSessionFactory 객체를 먼저 생성해야한다. 그 후, 앞서 생성한 Configure.xml 파일을 객체화하여 해당 설정 정보들이 주입된 SqlSessionFactory 객체를 생성한다. 이러한 원리 덕분에 개발자가 작성한 SQL문이 담긴 Mapper.xml 정보도 해당 객체에 저장되기에 자바 클래스 내에서 해당 SQL문을 자유롭게 사용할 수 있는 것이다. 

이렇게 XML 설정 정보가 담긴 SqlSessionFactory 객체를 생성한 후 이를 반환하는 메서드인 `public static SqlSessionFactory getSqlSessionFactory()` 을 정의하였다. 어차피 SqlSessionFactory 객체는 단 한 번만 사용하면 되므로 싱글톤으로 정의하였다. 

이제 이 SqlSessionFactory 객체를 사용하여 본격적으로 DB 작업을 하는 방법을 다음의 코드를 통해 엿볼 수 있을 것이다. 

```java
package pack.model;

import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.springframework.stereotype.Repository;

@Repository
public class ProductDao {
	private SqlSessionFactory sqlSessionFactory 
		= SqlMapConfig.getSqlSessionFactory();
	
	public List<ProductDto> selectAllProducts() {
		List<ProductDto> records = new ArrayList<ProductDto>();
		
		try (SqlSession sqlSession = sqlSessionFactory.openSession()) {
			// xml 파일 내 id 속성값 이용.
			records = sqlSession.selectList("selectAll"); 
		} catch (Exception e) {
			System.out.println("[selectAllProducts] Method Error");
			e.printStackTrace();
		}
		
		return records;
	}
	
	public boolean insertOneProduct(ProductDto productDto) {
		boolean isSuccess = false;
		
		// Auto Commit을 원한다면 openSession(true)로 설정. 
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			int resultNum = sqlSession.insert("insertOne", productDto);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit(); // DB 작업 결과를 커밋하여 DB에 반영. 
		} catch (Exception e) {
			System.out.println("[insertOneProduct] Method Error");
			e.printStackTrace();
			
			// DB 작업 실패 시 롤백.
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}
	
	public boolean updateOneById(ProductDto productDto) {
		boolean isSuccess = false;
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			int resultNum = sqlSession.update("updateOneProductName", productDto);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit();
		} catch (Exception e) {
			System.out.println("[updateOneById] Method Error");
			e.printStackTrace();
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}
	
	public boolean deleteOneById(String targetId) {
		boolean isSuccess = false;
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			int resultNum = sqlSession.delete("deleteOne", targetId);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit();
		} catch (Exception e) {
			System.out.println("[deleteOneById] Method Error");
			e.printStackTrace();
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}
}

```

ProductDao.java

먼저 `private SqlSessionFactory sqlSessionFactory = SqlMapConfig.getSqlSessionFactory();` 필드를 통해 SqlSessionFactory 객체를 취득한다. 그 후 각각의 DB 작업을 수행하는 메서드 내에서 SqlSessionFactory 객체로부터 `openSession()` 메서드를 호출하여 SqlSession 객체를 획득한다. 이 SqlSession 객체를 통해 본격적으로 mapper xml 파일 내 SQL문을 실행하는 코드를 작성할 수 있게 된다. 

다음은 Mybatis를 이용하여 CRUD 작업을 진행해보는 간단한 예제 코드들이다. 

```java
package pack.model;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class ProductDto {
	private int id;
	private String name;
	private int price;
	private String category;
}

```

ProductDto.java

```java
package pack.business;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import pack.model.ProductDao;
import pack.model.ProductDto;

@Service
public class BusinessLogic {
	
	@Autowired
	private ProductDao productDao;
	
	public void showAllProducts() {
		List<ProductDto> records = productDao.selectAllProducts();
		
		System.out.println("=== 현재 상품 목록 ===");
		records.forEach(dto -> {
			String oneRecord = String.format(
					"%d \t %10s \t %5d \t %8s", 
					dto.getId(),
					dto.getName(),
					dto.getPrice(),
					dto.getCategory()
			);
			System.out.println(oneRecord);
		});
		System.out.println("총 건수 : " + records.size());
	}
	
	public void insertOne() {
		ProductDto dto = new ProductDto();
		dto.setId(13);
		dto.setName("미니청소기");
		dto.setPrice(30000);
		dto.setCategory("sundries");
		boolean isSuccess = productDao.insertOneProduct(dto);
		
		if(isSuccess) {
			System.out.println("새로운 데이터가 한 건 추가되었습니다.");
			System.out.println(dto.toString());
			showAllProducts();
		} else {
			System.out.println("데이터 한 건 삽입 시도 실패");
		}
		
	}
	
	public void updateOneProductName() {
		ProductDto dto = new ProductDto();
		dto.setId(13);
		dto.setName("노트북 청소기");
		
		boolean isSuccess = productDao.updateOneById(dto);
		
		if(isSuccess) {
			System.out.println("데이터 한 건이 수정되었습니다.");
			System.out.println(dto.toString());
			showAllProducts();
		} else {
			System.out.println("데이터 한 건 수정 시도 실패");
		}
	}
	
	public void deleteOneById() {
		String targetId = "13";
		
		System.out.println("가장 최근에 추가한 데이터 한 건 삭제");
		boolean isSuccess = productDao.deleteOneById(targetId);
		
		if(isSuccess) {
			System.out.println("데이터 한 건이 삭제되었습니다.");
			showAllProducts();
		} else {
			System.out.println("데이터 한 건 삭제 시도 실패");
		}
	}
}

```

BusinessLogic.java

```java
package pack.business;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"pack.model", "pack.business"})
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context
			= new AnnotationConfigApplicationContext(Main.class);
		
		BusinessLogic businessLogic = context.getBean(
				"businessLogic", BusinessLogic.class);
		businessLogic.showAllProducts();
		businessLogic.insertOne();
		businessLogic.updateOneProductName();
		businessLogic.deleteOneById();
	}
}

```

Main.java

```java
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
=== 현재 상품 목록 ===
1 	  얼큰한 라면 1봉 	  3000 	     food
2 	  도시락 3종 세트 	 12000 	     food
3 	        순대국 	  5000 	     food
4 	        롤케잌 	 10000 	     food
5 	        맨투맨 	 15000 	    cloth
6 	         바지 	 13000 	    cloth
7 	         신발 	 60000 	    cloth
8 	         모자 	  8000 	    cloth
9 	     식사용 접시 	  3000 	 sundries
10 	       공구세트 	 20000 	 sundries
11 	        메모장 	  5000 	 sundries
12 	        책받침 	  6000 	 sundries
총 건수 : 12
새로운 데이터가 한 건 추가되었습니다.
pack.model.ProductDto@3f363cf5
=== 현재 상품 목록 ===
1 	  얼큰한 라면 1봉 	  3000 	     food
2 	  도시락 3종 세트 	 12000 	     food
3 	        순대국 	  5000 	     food
4 	        롤케잌 	 10000 	     food
5 	        맨투맨 	 15000 	    cloth
6 	         바지 	 13000 	    cloth
7 	         신발 	 60000 	    cloth
8 	         모자 	  8000 	    cloth
9 	     식사용 접시 	  3000 	 sundries
10 	       공구세트 	 20000 	 sundries
11 	        메모장 	  5000 	 sundries
12 	        책받침 	  6000 	 sundries
13 	      미니청소기 	 30000 	 sundries
총 건수 : 13
데이터 한 건이 수정되었습니다.
pack.model.ProductDto@3829ac1
=== 현재 상품 목록 ===
1 	  얼큰한 라면 1봉 	  3000 	     food
2 	  도시락 3종 세트 	 12000 	     food
3 	        순대국 	  5000 	     food
4 	        롤케잌 	 10000 	     food
5 	        맨투맨 	 15000 	    cloth
6 	         바지 	 13000 	    cloth
7 	         신발 	 60000 	    cloth
8 	         모자 	  8000 	    cloth
9 	     식사용 접시 	  3000 	 sundries
10 	       공구세트 	 20000 	 sundries
11 	        메모장 	  5000 	 sundries
12 	        책받침 	  6000 	 sundries
13 	    노트북 청소기 	 30000 	 sundries
총 건수 : 13
가장 최근에 추가한 데이터 한 건 삭제
데이터 한 건이 삭제되었습니다.
=== 현재 상품 목록 ===
1 	  얼큰한 라면 1봉 	  3000 	     food
2 	  도시락 3종 세트 	 12000 	     food
3 	        순대국 	  5000 	     food
4 	        롤케잌 	 10000 	     food
5 	        맨투맨 	 15000 	    cloth
6 	         바지 	 13000 	    cloth
7 	         신발 	 60000 	    cloth
8 	         모자 	  8000 	    cloth
9 	     식사용 접시 	  3000 	 sundries
10 	       공구세트 	 20000 	 sundries
11 	        메모장 	  5000 	 sundries
12 	        책받침 	  6000 	 sundries
총 건수 : 12

```

Main.java 콘솔창 출력결과

위와 같이 자바 내에서 DB 작업을 할 수 있음을 알 수 있다. 

## SQL문을 mapper xml 파일 대신 어노테이션으로 작성하기

SQL문을 mapper xml 파일 내에 작성할 수도 있지만, 자바 클래스 내에서 어노테이션을 이용하여 SQL문을 작성할 수도 있다. 이 경우 mapper xml 파일은 필요없게 된다. 

이 경우 Comfigure.xml 에서는 mapper xml 파일을 지정한 `<mapper>` 태그를 삭제한다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<properties resource="jdbc.properties" />
	<environments default="dev">
		<environment id="dev">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
	<!-- 해당 태그는 Annotation 방식 사용 시 필요없음.
	<mappers>
		<mapper resource="SqlMapper.xml" />
	</mappers>
	 -->
</configuration>
```

Configure.xml

그리고 SqlSessionFactory 객체를 생성 및 반환하는 클래스인 SqlMapConfig 클래스 내부에 어노테이션을 적용하는 클래스를 등록해줘야 한다. 여기서는 ProductDaoInterface 라는 인터페이스에서 CRUD 어노테이션을 사용하기에 해당 클래스를 다음과 같이 등록하였다.

```java
package pack.model;

import java.io.Reader;
import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

/**
 * 자바 클래스에서 DB 작업을 하기 위해 필요한 sqlSession 객체를 생성하는 
 * SqlSessionFactory 객체를 반환하는 클래스. 
 * DB 연동 설정 정보가 담긴 XML 파일(Configure.xml)을 가져와 객체화함으로써 
 * 개발자 정의 SQL문이 담긴 SqlMapper.xml를 이용하여 DB 작업이 가능하게끔 
 * 설정하는 코드가 담겨 있다. 
 */
public class SqlMapConfig {
	public static SqlSessionFactory sqlSessionFactory; // DB의 SQL명령을 실행시킬 때 필요한 메소드를 갖고 있다.

	static {
		// xml 내 내용들이 객체화한다. 
		String resource = "Configure.xml";
		try (Reader reader = Resources.getResourceAsReader(resource)) {
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
			
			// 어노테이션이 적용된 클래스를 SqlSessionFactory에 등록한다.
			List<Class> mappers = new ArrayList<Class>();
			mappers.add(SqlMapperInterface.class);  // 어노테이션 적용된 클래스를 등록. 여러 개 등록 가능.
			mappers.forEach(map -> {
				// SqlSessionFactory 객체에 mapper 클래스들을 실제로 등록한다. 
				sqlSessionFactory.getConfiguration().addMapper(map);
			});
			// 어노테이션 클래스 등록 완료
			
		} catch (Exception e) {
			System.out.println("SqlMapConfig 오류 : " + e);
		}
	}

	public static SqlSessionFactory getSqlSessionFactory() {
		return sqlSessionFactory;
	}
}
```

SqlMapConfig.java

그리고 어노테이션이 적용된 코드이다.

```java
package pack.model;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

public interface SqlMapperInterface {
	String TABLE_NAME = " products ";
	String WHERE_ID = " WHERE id=#{id} ";
	
	@Select(value = "SELECT id, name, price, category FROM " 
			+ TABLE_NAME)
	List<ProductDto> selectAllProducts();
	
	@Insert("INSERT INTO products "
			+ "VALUES (#{id}, #{name}, #{price}, #{category})")
	int insertOneProduct(ProductDto productDto);
	
	@Update("UPDATE " + TABLE_NAME + "SET name=#{name} " + 
			WHERE_ID)
	int updateOneById(ProductDto productDto);
	
	@Delete("DELETE FROM " + TABLE_NAME + WHERE_ID)
	int deleteOneById(String targetId);
}

```

SqlMapperInterface.java

```java
package pack.model;

import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.springframework.stereotype.Repository;

@Repository
public class ProductDao {
	private SqlSessionFactory sqlSessionFactory 
		= SqlMapConfig.getSqlSessionFactory();
	
	public List<ProductDto> selectAllProducts() {
		List<ProductDto> records = new ArrayList<ProductDto>();
		
		try (SqlSession sqlSession = sqlSessionFactory.openSession()) {
			// xml 파일 내 id 속성값 이용.
			//records = sqlSession.selectList("selectAll"); 
			SqlMapperInterface inter = sqlSession.getMapper(SqlMapperInterface.class);
			records = inter.selectAllProducts();
		} catch (Exception e) {
			System.out.println("[selectAllProducts] Method Error");
			e.printStackTrace();
		}
		
		return records;
	}
	
	public boolean insertOneProduct(ProductDto productDto) {
		boolean isSuccess = false;
		
		// Auto Commit을 원한다면 openSession(true)로 설정. 
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			SqlMapperInterface inter = sqlSession.getMapper(SqlMapperInterface.class);
			int resultNum = inter.insertOneProduct(productDto);
			//int resultNum = sqlSession.insert("insertOne", productDto);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit(); // DB 작업 결과를 커밋하여 DB에 반영. 
		} catch (Exception e) {
			System.out.println("[insertOneProduct] Method Error");
			e.printStackTrace();
			
			// DB 작업 실패 시 롤백.
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}
	
	public boolean updateOneById(ProductDto productDto) {
		boolean isSuccess = false;
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			SqlMapperInterface inter = sqlSession.getMapper(SqlMapperInterface.class);
			//int resultNum = sqlSession.update("updateOneProductName", productDto);
			int resultNum = inter.updateOneById(productDto);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit();
		} catch (Exception e) {
			System.out.println("[updateOneById] Method Error");
			e.printStackTrace();
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}

	public boolean deleteOneById(String targetId) {
		boolean isSuccess = false;
		SqlSession sqlSession = sqlSessionFactory.openSession();
				
		try {
			SqlMapperInterface inter = sqlSession.getMapper(SqlMapperInterface.class);
			int resultNum = inter.deleteOneById(targetId);
			//int resultNum = sqlSession.delete("deleteOne", targetId);
			if (resultNum > 0) {
				isSuccess = true;
			}
			sqlSession.commit();
		} catch (Exception e) {
			System.out.println("[deleteOneById] Method Error");
			e.printStackTrace();
			sqlSession.rollback();
		} finally {
			if (sqlSession != null) sqlSession.close();
		}
		
		return isSuccess;
	}
}

```

ProductDao.java

CRUD SQL문 어노테이션 (`@Select` 등)이 적용된 클래스를 Dao에서 불러와 SQL문 작업을 하기 위해 SqlSession.getMapper() 메서드의 인자로 해당 클래스를 입력하면 된다. 

위와 같이 작성하고 Main.java 파일을 실행하면 이전의 XML 방식을 이용하던 것과 동일한 출력결과가 나온다. 

# 지저분한 SQL 정리하기 - SQL Builder Class

문자열로 SQL문을 작성하다보면 SQL문이 길어질수록 가독성을 위해 다음 줄로 넘어가 계속 작성하다보면 역설적으로 가독성이 안좋아진다. 

```java
String sql = "SELECT ... FROM ..." +
"INNER JOIN ..." +
"INNER JOIN ..." +
"ON ..." +
"WHERE ...";
```

또한, SQL문을 문자열로 작성하면 컴파일 단계에서의 SQL 문법 검사를 실행할 수 없다. 그러다보니 SQL문에 잘못된 점이 있으면 런타임에서야 이를 확인할 수 있다는 단점이 있다. Mybatis에서는 이러한 쿼리문 가독성 문제 및 컴파일 단계에서의 문법 체킹 미지원 문제를 해결하기 위해 Mybatis 3 버전부터 SQL Builder Class를 제공한다. 

Mybatis에서 제공하는 `org.apache.ibatis.jdbc.SQL` 클래스를 이용하면 된다. 다음의 코드는 해당 클래스를 이용하여 SQL문을 반환하는 클래스이다.

{% raw %}

```java
package pack.model;

import org.apache.ibatis.jdbc.SQL;

public class QueryBuilder {
	private final String TABLE_NAME = " products ";
	private final String WHERE_ID = " id=#{id} ";
	
	public String getQuerySelectAllProducts() {
		// 메서드 체이닝 방식
		return new SQL()
			.SELECT("id, name, price, category")
			.FROM(TABLE_NAME)
			.toString();
	}
	
	public String getQueryInsertOneProduct() {
		// 내부 클래스 방식.
		return new SQL() {{
			INSERT_INTO(TABLE_NAME);
			VALUES("id", "#{id}");
			VALUES("name", "#{name}");
			VALUES("price", "#{price}");
			VALUES("category", "#{category}");
		}}.toString();
	}
	
	public String getQueryUpdateOneById() {
		return new SQL()
			.UPDATE(TABLE_NAME)
			.SET("name = \#{name}")
			.WHERE(WHERE_ID)
			.toString();
	}
	
	public String getQueryDeleteOneById() {
		return new SQL()
			.DELETE_FROM(TABLE_NAME)
			.WHERE(WHERE_ID)
			.toString();
	}
	
}

```

QueryBuilder.java

SQL Builder 클래스를 사용하는 경우, Mapper 어노테이션도 바뀐다. 

```java
package pack.model;

import java.util.List;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.DeleteProvider;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.InsertProvider;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.SelectProvider;
import org.apache.ibatis.annotations.Update;
import org.apache.ibatis.annotations.UpdateProvider;

public interface SqlMapperInterface {
	String TABLE_NAME = " products ";
	String WHERE_ID = " WHERE id=#{id} ";
	
	//@Select(value = "SELECT id, name, price, category FROM " + TABLE_NAME)
	@SelectProvider(type = QueryBuilder.class, method = "getQuerySelectAllProducts")
	List<ProductDto> selectAllProducts();
	
	//@Insert("INSERT INTO products " + "VALUES (#{id}, #{name}, #{price}, #{category})")
	@InsertProvider(type = QueryBuilder.class, method = "getQueryInsertOneProduct")
	int insertOneProduct(ProductDto productDto);
	
	//@Update("UPDATE " + TABLE_NAME + "SET name=#{name} " + WHERE_ID)
	@UpdateProvider(type = QueryBuilder.class, method = "getQueryUpdateOneById")
	int updateOneById(ProductDto productDto);
	
	//@Delete("DELETE FROM " + TABLE_NAME + WHERE_ID)
	@DeleteProvider(type = QueryBuilder.class, method = "getQueryDeleteOneById")
	int deleteOneById(String targetId);
}

```
{% endraw %}   
SqlMapperInterface.java

위와 같이 `@SelectProvider` 처럼 provider로 끝나는 어노테이션을 대신 사용하면 된다. 위 상태에서 Main.java를 실행하면 이전처럼 잘 실행된다. 

# 정리

Mybatis에서 사용되는 설정 파일들의 관계를 나타내면 다음의 그림처럼 나타낼 수 있다. 

![Mybatis.drawio.png](/images/2024-10-20/Spring-DB-MyBatis/Mybatis.drawio.png)

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] Mybatis 공식 사이트

[mybatis – MyBatis 3 \| Introduction](https://mybatis.org/mybatis-3/index.html)

[4] Mybatis VS JPA

[JPA vs Mybatis, 현직 개발자는 이럴 때 사용합니다. I 이랜서 블로그](https://www.elancer.co.kr/blog/detail/231)

[5] Mybatis에서 SQL문을 조금 더 깔끔하게 작성하는 방법.

[mybatis – MyBatis 3 \| The SQL Builder Class](https://mybatis.org/mybatis-3/statement-builders.html)

[6] SQL 구문 빌더 사용 시 쿼리문 적용 방법

[myBatis - 6. 구문 빌더(SelectBuilder, SqlBuilder)](https://memory-develo.tistory.com/127)

[7] SQL 구문 빌더 사용 시 쿼리문 적용 방법

[[MyBatis]MyBatis Java API 및 SQL Builder 사용법](https://m.blog.naver.com/hj_kim97/223393071895)