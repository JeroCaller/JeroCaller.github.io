---
title: "[JDBC] DB 구축과 JDBC"
category: "Servlet & JSP"
tag: ["Servlet", "DB", "JDBC", "DBMS", "RDBMS", "H2", "DAO", "VO"]
---

# DB와 DBMS

모든 프로그래밍 언어에서는 데이터베이스와 연동할 수 있는 기능을 제공한다. 자바에서는 java.sql 패키지를 통해 이 기능을 제공한다. 이를 JDBC API라고도 한다. 

우선 DBMS 프로그램을 설치해야겠다. H2라는 DBMS를 사용해보자.

## H2 설치과정

구글에 “h2 database”라고 치고 나오는 검색결과 중 다음의 사이트를 방문한다.

![스크린샷 2024-08-28 164632.png](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/1.png)

[H2 Database Engine](https://www.h2database.com/html/main.html)

아래 사진과 같이 해당 홈페이지가 뜰 텐데, 바로 보이는 “All Platforms”를 클릭하여 zip 파일을 다운로드 받는다. 

![h2 데이터베이스 사이트 및 다운로드](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/2.png)

h2 데이터베이스 사이트 및 다운로드

그 후 다운로드 받은 zip 파일을 원하는 위치에 압축 해제하면 설치 끝이다. 

## H2 db 실행해보기

압축해제한 h2폴더 내 bin 폴더 내부로 가보자. 그러면 다음과 같은 화면을 볼 것이다.

![h2 db 폴더 내 bin 폴더 내 [h2w.ba](http://h2w.ba)t 파일 ](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/3.png)

h2 db 폴더 내 bin 폴더 내 h2w.bat 파일 

여기서 h2w.bat 파일을 더블클릭하여 실행해보자. 그러면 브라우저에서 다음의 창이 뜰 것이다. 

![h2w.bat 파일 더블 클릭 시 뜨는 화면. 브라우저에서 h2 서버와 연결되어 그 첫 화면이 보이고 있다. ](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/4.png)

h2w.bat 파일 더블 클릭 시 뜨는 화면. 브라우저에서 h2 서버와 연결되어 그 첫 화면이 보이고 있다. 

위 화면에서 그대로 [연결] 버튼을 누른다. 만약 그렇지 않고 바로 다음에 소개할 JDBC URL을 바로 입력하고 [연결]할 경우 연결이 안될 수도 있다. 그러니 일단 JDBC URL을 건드리지 않은 상태에서 [연결] 버튼을 누르고 화면 왼쪽 상단의 “N”자 모양의 아이콘을 눌러 빠져나온다. 

여기서, “JDBC URL” 창에 “jdbc:h2:tcp://localhost/~/test” 라고 입력 뒤 “연결” 버튼을 눌러보자.

![ jdbc url을 위와 같이 수정한 후 [연결] 버튼을 누름.](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/5.png)

 jdbc url을 위와 같이 수정한 후 [연결] 버튼을 누름.

![[연결] 버튼 클릭 후 나오는 화면. db와 연결된 상태이다. 연결을 끊고자 한다면 왼쪽 상단에 “N” 자 모양의 아이콘을 클릭하면 된다. ](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/6.png)

[연결] 버튼 클릭 후 나오는 화면. db와 연결된 상태이다. 연결을 끊고자 한다면 왼쪽 상단에 “N” 자 모양의 아이콘을 클릭하면 된다. 

첫 화면에서, 아래 사진과 같이, 파란색 박스 내부에서 SQL 쿼리문을 작성할 수 있고, 이를 위의 “실행”버튼을 누르면 그 결과가 아래 주황색 박스 내부에 나오게 된다. 

![파란색 영역은 sql 쿼리문 작성 구역이고, 작성을 완료했으면 위의 [실행] 버튼을 누르면 아래 노란색 영역에 그 결과가 나온다. ](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/7.png)

파란색 영역은 sql 쿼리문 작성 구역이고, 작성을 완료했으면 위의 [실행] 버튼을 누르면 아래 노란색 영역에 그 결과가 나온다. 

h2에 대해 간략하게 살펴보았다.

# JDBC

- JDBC(Java Database Connectivity) : 자바 프로그램과 RDBMS 간의 연동 기능을 제공하는 표준 API로, JDBC API라고 하면 java.sql 패키지를 의미한다.
- DBMS에는 MariaDB, 오라클 등 여러 회사들의 제품들이 존재한다. 그러나 JDBC API를 이용하면 이러한 DBMS 제품에 종속되지 않으면서 프로그램과 연동할 수 있다. 즉, 어떤 DBMS 제품을 사용하건 상관없이 자바 프로그램 내에서 DB를 다룰 수 있게 된다. DBMS에 대한 호환성이 좋다고 볼 수 있겠다. 이로 인해 프로그램 운영 단계에서는 다른 DBMS 제품으로 쉽게 전환할 수 있다고 한다.

그러면 어떻게 JDBC가 DBMS 제품 종류에 상관없이 비종속적으로 프로그램과 연동할 수 있는 걸까?

- JDBC 드라이버 : JDBC API에 존재하는 인터페이스를 구현한 클래스를 말한다. JDBC 드라이버 자체는 특정 회사에서 제공하는 jar 압축파일로, 이로 인해 특정 DBMS에 종속적인 코드들로 이루어져 있다. (JDBC 드라이버는 각 회사마다 홈페이지에서 다운로드 받을 수 있다고 한다)

JDBC API에서 제공하는 인터페이스를 반드시 구현해야 하는 JDBC 드라이버의 특성으로 인해, 아무리 JDBC 드라이버 자체가 특정 회사마다 종속적이라 하더라도 큰 틀에서는 표준을 지켜야 하기에, 자바에서 어떤 DBMS에 접속하더라도 표준화된 방식으로 DB에 접속하여 데이터에 접근, 수정할 수 있게 되고, 이로 인해 비종속적 특성을 띌 수 있는 것이다. 

또한, 자바 프로그램에서 DB와 연동할 때 개발자가 직접 DBMS에 접근하여 데이터 검색, 조작하는 방식이 아니라, JDBC API가 그 중간 매개체 역할을 하기 때문에 개발자는 JDBC API에서 제공하는 규칙대로 SQL문을 작성하면 JDBC가 이를 DB에 전송하여 데이터 검색 및 조작을 할 수 있게 되는 것이다. 이 점에서도 DBMS와의 비종속적 관계를 형성할 수 있게 된다. 

특정 DBMS 회사 제품에 종속적인 JDBC 드라이버는 그저 다른 회사의 JDBC 드라이버로 교체하기만 하면 바로 해당 회사의 DBMS 제품으로 전환할 수 있기에 전체적으로 봤을 때 자바 프로그램은 DBMS에 비종속적으로 연동할 수 있게 되는 것이다. 

JDBC를 통해 DB와 연동하여 데이터 변경 및 조회를 하기 위해 먼저 다음의 테이블을 h2 프로그램에서 생성하였다.

![스크린샷 2024-08-30 175307.png](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/8.png)

참고로 h2에서는 `desc 테이블명` 이 아닌 `show columns from 테이블명` 쿼리문을 대신 사용한다. 

## JDBC 프로그래밍 절차 : DB 연동 및 데이터 검색, 조작 방법

그렇다면 자바 프로그램에서 DB와 연동하여 데이터 검색 및 조작하는 구체적 과정을 살펴보자. 

1. JDBC 드라이버 등록을 위해, java.sql.DriverManager 클래스의 `registerDriver()` 메서드를 통해 드라이버 객체를 등록, 메모리에 로딩한다. 
    1. h2의 경우, h2가 설치된 폴더 내 bin 폴더 내부에 `h2-2.3.232.jar` 파일이 있는데, 이를 복사한 후, WEB-INF 내 lib 폴더 내에 위치시킨다. 해당 파일이 h2라는 DBMS의 JDBC 드라이버이다. 
    2. `registerDriver()` 메서드는 java.sql.SQLException을 throws하기에, 해당 메서드 호출 시 반드시 try ~ catch문을 이용하여 예외 처리하거나 이 메서드를 호출하는 외부 메서드에도 throws를 이용하든가 해야한다. 
2. 이제 자바 프로그램과 DB를 연결(connection)해야 한다. 이를 위해 java.sql.Connection 인터페이스와 `DriverManager.getConnection(jdbcUrl, userName, password)` 메서드를 이용한다. 
3. sql 문을 작성한 후, 이 sql문을 자바 프로그램에서 DBMS로 전송하기 위해선 PreparedStatement라는 객체가 필요하다.  해당 객체를 이용해 sql문에 대입할 실제 값들을 입력한 후, `executeUpdate()` (DML의 경우) 또는 `executeQuery()` (DQL의 경우) 메서드를 호출하여 db에 전송한다. 
4. sql 작업이 모두 완료되었다면 Connection 객체의 `close()`를 호출하여 db와의 연결을 끊는다. 

자세한 코드는 다음과 같다. 

```java
package com.cola.db.user;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class DMLTest {

	public static void main(String[] args) {
		Connection conn = null;
		PreparedStatement pState = null;
		
		try {
			
			// 특정 DBMS의 드라이버를 등록한다.
			DriverManager.registerDriver(new org.h2.Driver());
			
			// db 연결을 위해 특정 dbms의 jdbc URL과 등록된 사용자 이름 및 비밀번호를 입력하여
			// db 연결.
			String jdbcUrl = "jdbc:h2:tcp://localhost/~/test";
			String userName = "sa";
			String password = "";
			conn = DriverManager.getConnection(jdbcUrl, userName, password);
			
			// sql문을 String으로 작성한다. 
			// 이 때, 실제 값을 넣을 자리에는 대신 물음표(?) 기호를 넣는다. 
			String sql = "insert into site_users values (?, ?, ?, ?, ?)";
			
			/*
			 * Connection 객체 참조 변수인 conn의 prepareStatement 메서드에 
			 * 앞서 작성한 sql 문자열을 인자로 전달하여 호출하면 
			 * PreparedStatement 객체가 반환된다. 이를 pState라는 참조 변수로 받았다. 
			 */
			pState = conn.prepareStatement(sql);
			
			/*
			 * PrepareStatement 객체를 통해 sql문의 물음표 자리를 실제 값으로 채운다. 
			 * set이란 이름으로 시작하는 메서드를 호출하여 대입하면 되며, 
			 * 첫 번째 인자는 대입할 물음표 자리를 의미한다. (1부터 시작) 
			 * 두 번째 인자에 실제 삽입할 값을 입력.
			 * 단, 값의 자료형에 따라 호출할 메서드명이 다르다. 
			 * DB에서의 integer 형 데이터는 setInt로, 
			 * DB에서의 varchar 형 데이터는 setString으로, 
			 * DB에서의 Date 형 데이터는 setDate 메서드를 이용하는 방식이다. 
			 * 
			 */
			pState.setInt(1, 1);
			pState.setString(2, "kimQuel123");
			pState.setString(3, "sqkim123");
			pState.setString(4, "ADMIN");
			
			// sql의 date 타입 데이터 삽입법
			// 참고) https://sinau.tistory.com/5 
			java.util.Date utilDate = new java.util.Date();
			java.sql.Date sqlDate = new java.sql.Date(utilDate.getTime());
			pState.setDate(5, sqlDate);
			
			/*
			 * PreparedStatement 객체의 executeUpdate() 메서드 호출을 통해, 
			 * 앞서 set~ 메서드를 통해 실제 값을 대입하여 완성된 sql문을 연결된 DBMS에 전송한다. 
			 * 참고로 executeUpdate()는 DML sql문으로 인해 성공적으로 변경된 레코드들의 
			 * 수를 반환한다. 
			 */
			int count = pState.executeUpdate();
			System.out.println(count + "건 데이터 처리 성공!");
			
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			/*
			 * 사용이 끝난 Connection 및 PreparedStatement 객체는 
			 * close() 메서드를 통해 호출해줘야 한다. 
			 */
			try {
				pState.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
			
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		
	}

}

```

DMLTest.java

위 파일을 실행할 때에는 다음의 창에서 [Java Application]을 택하고 실행하자. 

![스크린샷 2024-08-30 172126.png](/images/2024-08-28/jdbc-db%20%EA%B5%AC%EC%B6%95%EA%B3%BC%20jdbc/9.png)

그러면 콘솔창에 다음의 결과가 뜰 것이다.

```java
1건 데이터 처리 성공!
```

> 다음의 메시지가 뜰 때도 있다.
> 
> 
> ```java
> org.h2.jdbc.JdbcSQLNonTransientConnectionException: Connection is broken: "java.net.ConnectException: Connection refused: getsockopt: localhost" [90067-232]
> 	at org.h2.message.DbException.getJdbcSQLException(DbException.java:690)
> 	at org.h2.message.DbException.getJdbcSQLException(DbException.java:489)
> 	at org.h2.message.DbException.get(DbException.java:212)
> 	at org.h2.engine.SessionRemote.connectServer(SessionRemote.java:443)
> 	at org.h2.engine.SessionRemote.connectEmbeddedOrServer(SessionRemote.java:331)
> 	at org.h2.jdbc.JdbcConnection.<init>(JdbcConnection.java:124)
> 	at org.h2.Driver.connect(Driver.java:59)
> 	at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:683)
> 	at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:230)
> 	at com.cola.db.user.DMLTest.main(DMLTest.java:24)
> Caused by: java.net.ConnectException: Connection refused: getsockopt
> 	at java.base/sun.nio.ch.Net.pollConnect(Native Method)
> 	at java.base/sun.nio.ch.Net.pollConnectNow(Net.java:682)
> 	at java.base/sun.nio.ch.NioSocketImpl.timedFinishConnect(NioSocketImpl.java:542)
> 	at java.base/sun.nio.ch.NioSocketImpl.connect(NioSocketImpl.java:592)
> 	at java.base/java.net.SocksSocketImpl.connect(SocksSocketImpl.java:327)
> 	at java.base/java.net.Socket.connect(Socket.java:751)
> 	at org.h2.util.NetUtils.createSocket(NetUtils.java:135)
> 	at org.h2.util.NetUtils.createSocket(NetUtils.java:99)
> 	at org.h2.engine.SessionRemote.initTransfer(SessionRemote.java:131)
> 	at org.h2.engine.SessionRemote.connectServer(SessionRemote.java:439)
> 	... 6 more
> Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.sql.PreparedStatement.close()" because "pState" is null
> 	at com.cola.db.user.DMLTest.main(DMLTest.java:77)
> 
> ```
> 
> h2 프로그램 자체를 끈 상태에서 실행했을 때 나타나는 메시지인 것 같다. 브라우저에서 h2 프로그램이 켜져 있는 상태에서 진행해야한다.
> 

참고로 위 코드는 insert, delete, update의 DML 언어에서 사용 가능하다. 

DQL인 select문도 형태는 비슷하다. 한 가지 차이점은 테이블로부터 결과를 얻기 위해 ResultSet이라는 클래스를 사용해야 한다는 것이다. 다음은 새 자바 파일에서 만든 select문의 sql문을 DBMS에 전송하여 결과를 얻어와 출력하는 코드이다. 

```java
package com.cola.db.user;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class DQLTest {
	public static void main(String[] args) {
		Connection conn = null;
		PreparedStatement pState = null;
		ResultSet rs = null;
		
		try {
			DriverManager.registerDriver(new org.h2.Driver());
			
			String jdbcUrl = "jdbc:h2:tcp://localhost/~/test";
			String userName = "sa";
			String password = "";
			conn = DriverManager.getConnection(jdbcUrl, userName, password);
			
			String sql = "select * from site_users";
			pState = conn.prepareStatement(sql);
			rs = pState.executeQuery();
			
			System.out.println(" ====== site_users ====== ");
			while(rs.next()) {
				System.out.print(rs.getInt("unique_id") + " \t ");
				System.out.print(rs.getString("user_name") + " \t ");
				System.out.print(rs.getString("password") + " \t ");
				System.out.print(rs.getString("role") + " \t ");
				System.out.println(rs.getDate("sign_date"));
			}
			
		} catch (SQLException e) {
			e.printStackTrace();
		} finally {
			try {
				pState.close();
			} catch (SQLException e) {
				e.printStackTrace();
			} 
			
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
			
			try {
				rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}

```

DQLTest.java

```java
 ====== site_users ====== 
1 	 kimQuel123 	 sqkim123 	 ADMIN 	 2024-08-30
2 	 kimQuel123 	 sqkim123 	 ADMIN 	 2024-08-30

```

DQLTest.java 실행결과

(테스트 하느라 데이터를 한 건 더 넣었다)

DML 언어의 쿼리문 실행 시에는 PreparedStatement의 `executeUpdate()` 메서드를 사용했고, 그 결과로 반영된 행의 개수가 반환된 반면, DQL 언어 쿼리문 실행시에는 `executeQuery()` 메서드를 실행하여 sql문을 DB에 전송하고, 그 결과로 얻어온 데이터를 ResultSet 객체로 반환받는다. 

반환받은 ResultSet 객체는 `next()`를 호출할 때마다 맨 첫 결과 데이터부터 차례대로 읽어온다. `next()`를 최초로 한 번 호출하면 첫 번째 데이터가, 두 번째로 호출하면 두 번째 데이터를 읽어와 반환하는 구조이다. 이러한 이유로, 결과 데이터를 모두 출력하기 위해 위 코드에서는 while문을 사용하였다. 

ResultSet 객체는 SELECT를 통해 얻어온 데이터들이 담겨져 있는데, DB의 테이블 형태로 담겨져 있다고 생각하면 된다. 그리고 `next()` 메서드를 호출하면 하나의 행을 가리키는 커서라는 것이 존재하여 이 커서가 한 줄 밑으로 내려가, 그 커서가 가리키는 하나의 행 데이터를 읽어와 반환하는 구조를 띈다. ResultSet 객체에는 데이터가 없는 영역인 “Before First”와 “After Last”가 각각 첫 번째 행 이전과 마지막 행 다음에 존재한다. 그리고 맨 처음에 커서는 “Before First” 영역에 있어 아무런 데이터도 가리키지 않는 형태이다. 

```java
맨 처음 ResultSet 객체 내 상황)
cursor ->    Before First
             ------------
             행 데이터 1
             행 데이터 2
             ...
             ------------
             After Last
            
 처음 next() 메서드 호출 시)
             Before First
             ------------
 cursor  ->  행 데이터 1  (행 데이터 1이 반환됨)
             행 데이터 2
             ...
             ------------
             After Last
```

그러다 `next()` 메서드를 호출할 때마다 이 커서가 한 행씩 아래로 내려가 커서가 가리키는 행 하나를 반환하는 구조이다. while문을 이용하여 이 `next()` 메서드를 호출하다가 커서가 After Last 영역을 가리키면 더 이상 읽어올 데이터가 없기에 `next()` 메서드는 false를 반환하여 반복문을 빠져나간다. 

ResultSet 객체도 모두 사용하였으면 `close()` 메서드를 호출하여 닫아줘야 한다. 

## 코드 다듬기

그런데 위 코드를 보면 어딘가 꺼림직하다. JDBC 프로그래밍의 핵심은 연결된 DB에 SQL문을 전송하여 DB 내 데이터를 변경하거나 조회해오는 것이다. 그런데 위 코드를 보면 알겠지만, 쿼리문 하나를 실행하기 위한 코드가 너무 장황하고 어쩐지 복잡해보인다. 그래서 지금부터는 하나씩 코드를 최대한 간결하게 다듬어보겠다. 

### 시작과 끝은 하나로 : 드라이버 등록 절차, DB 연동과 연동 해제 기능을 하나의 클래스로

앞서 살펴본 두 자바 소스 코드를 보면 어딘가 패턴이 겹친다. 우선 드라이버 등록 과정과 DB 연동, 그리고 연동 해제의 기능을 하나의 클래스에 별도로 분리하여 클래스로 만들어보겠다. 다음이 그 코드이다. 

```java
package com.cola.db.lib;

import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.SQLException;

public class JDBCTool {
	private String jdbcUrl = "jdbc:h2:tcp://localhost/~/test";
	private String userName = "sa";
	private String password = "";
	private Driver driver = new org.h2.Driver();
	
	public void setJdbcUrl(String jdbcUrl) {
		this.jdbcUrl = jdbcUrl;
	}
	
	public void setUserName(String userName) {
		this.userName = userName;
	}
	
	public void setPassword(String password) {
		this.password = password;
	}
	
	public void setDriver(Driver driver) {
		this.driver = driver;
	}
	
	public JDBCTool() {}
	public JDBCTool(String jdbcUrl, String userName, String password, Driver driver) {
		this.jdbcUrl = jdbcUrl;
		this.userName = userName;
		this.password = password;
		this.driver = driver;
	}
	
	public void handleSQLException(SQLException exp) {
		System.out.println("=== SQL 관련 예외 발생. 자세한 사항은 다음을 참조 ===");
		exp.printStackTrace();
	}
	
	public Connection getConnection() {
		Connection conn = null;
		
		try {
			DriverManager.registerDriver(driver);
			
			conn = DriverManager.getConnection(jdbcUrl, userName, password);
		} catch (SQLException e) {
			handleSQLException(e);
		}
		return conn;
	}
	
	/**
	 * AutoCloseable 인터페이스를 상속받는 객체들의 close() 메서드를 호춯한다.
	 * @param closes - Connection, ResultSet, PreparedStatement 객체
	 */
	public void close(AutoCloseable ...closes) {
		for (AutoCloseable autoc : closes) {
			if (autoc == null) {
				continue;
			}
			
			try {
				autoc.close();
			} catch (Exception e) {
				if (e instanceof SQLException) {
					handleSQLException((SQLException)e);
				} else {
					System.out.println("=== 예기치 못한 예외 발생 ===");
					e.printStackTrace();
				}
			}
		}
	}
	
}

```

JDBCTool.java

위 코드에서의 핵심은 `getConnection()`과 `close()` 메서드이다. jdbc 드라이버 등록과 db 연동 기능은 `getConnection()` 메서드로 정의하여 연동 완료된 Connection 객체를 반환하도록 하고 있다. 또한, db 작업이 모두 끝난 후에 Connection, PreparedStatement, ResultSet 객체들의 `close()` 메서드를 모두 호출하여 닫아주도록 `close()` 메서드를 구현하였다. 이 세 객체들의 공통점은 `close()` 추상 메서드를 가지는 AutoCloseable 부모 인터페이스를 구현하고 있다는 점이다. 이 사실을 이용하여 `close()` 내부 코드를 더 간결하게 만들었다. 

사실, db의 jdbc url이나 사용자명, 패스워드, 드라이버를 하나로 고정하여 사용한다면 해당 정보들을 모두 `getConnection()` 메서드 내부에 작성한 뒤,  `getConnection()`, `close()` 메서드를 모두 static으로 선언하여 JDBCTool 클래스를 유틸리티 클래스로 변경하여 사용해도 된다. 이 경우 각 정보들에 대한 getter, setter 메서드는 필요 없으므로 지운다. 여기서는 그냥 위 코드를 그대로 사용하겠다. 

이제 이 코드를 적용하여 각각 데이터 삽입과 조회 코드를 작성해보았다. 

```java
package com.cola.db.user;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

import com.cola.db.lib.JDBCTool;

public class DMLWithTool {

	public static void main(String[] args) {
		JDBCTool jTool = new JDBCTool();
		Connection conn = null;
		PreparedStatement state = null;
		
		try {
			conn = jTool.getConnection();
			
			String sql = "insert into site_user values(?, ?, ?, ?, ?)";
			state = conn.prepareStatement(sql);
			
			state.setInt(1, 2);
			state.setString(2, "jeongdb123");
			state.setString(3, "jeongdb1111");
			state.setString(4, "ADMIN");
			
			java.util.Date today = new java.util.Date();
			java.sql.Date sqlDate = new java.sql.Date(today.getTime());
			state.setDate(5, sqlDate);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
		} catch (SQLException e) {
			jTool.handleSQLException(e);
		} finally {
			jTool.close(conn, state);
		}
	}

}

```

DMLWithTool.java

```java
1건 처리되었습니다.
```

DMLWithTool.java 실행결과

```java
package com.cola.db.user;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import com.cola.db.lib.JDBCTool;

public class DQLWithTool {

	public static void main(String[] args) {
		JDBCTool jTool = new JDBCTool();
		Connection conn = null;
		PreparedStatement state = null;
		ResultSet rs = null;
		
		try {
			conn = jTool.getConnection();
			
			String sql = "select * from site_user";
			state = conn.prepareStatement(sql);
			
			rs = state.executeQuery();
			
			System.out.println("=== site_user table ===");
			while(rs.next()) {
				System.out.printf("%d \t %s \t %s \t %s \t ", 
					rs.getInt("unique_id"),
					rs.getString("user_name"),
					rs.getString("password"),
					rs.getString("role")
				);
				System.out.println(rs.getDate("sign_date"));
			}
		} catch (SQLException e) {
			jTool.handleSQLException(e);
		} finally {
			jTool.close(conn, state, rs);
		}
		
	}

}

```

DQLWithTool.java 

```java
=== site_user table ===
1 	 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 	 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01

```

DQLWithTool.java  실행결과

위 두 자바 파일 코드를 보면, JDBCTool 클래스를 사용하지 않았던 이전 코드와 달리 적어도 db 연동과 연동 해제 코드 부분은 간략해졌다는 것을 알 수 있다. 

그럼에도 아직 DB 작업 코드가 장황하다. 이 코드를 더 줄이기 위해 디자인 패턴 중 하나인 DAO(Data Access Object)를 사용하겠다.

### DAO (Data Access Object)

우리는 하나의 웹 앱을 제작하는 과정에 놓여 있다. 그 과정에서 DB와 연동하여 데이터를 다루는 작업이 필요한 것이지, 사실 웹 앱 제작 과정에서는 비즈니스 로직을 짜는 것이 더 중요할 것이다. 그래서 DB 작업 코드를 더 간결하게 하는 것이 중요하다. 그래야 가독성이 좋아지고, 더 중요한 작업 코드에 집중할 수 있게 된다. 

이를 위해, DB에 접근하여 데이터 작업을 수행하는 코드를 하나의 클래스로 만들어 모듈화하는 것이 좋다. 이 클래스가 DAO(Data Access Object, “다오”라 읽는다고 한다)이다. 

앞서 살펴봤던 DB 작업 코드들을 DAO 클래스로 다음과 같이 만들어 보았다. 이 과정에서 앞서 만들었던 DB 연동 및 연동 해제 역할을 하는 JDBCTool 클래스도 같이 사용되었다. 

```java
package com.cola.db.lib;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class DBDao {
	private Connection conn = null;
	private PreparedStatement state = null;
	private ResultSet rs = null;
	private JDBCTool jtool = new JDBCTool();
	
	private final String ALL_DQL = "select * from site_user";
	private final String INSERT_SQL = "insert into site_user values(?, ?, ?, ?, ?)";
	private final String UPDATE_SQL = "update site_user set user_name = ?, password = ? where unique_id = ?";
	private final String DELETE_SQL = "delete site_user where unique_id = ?";
	
	public DBDao() {
		conn = jtool.getConnection();
	}
	
	public void getAllRecords() {
		try {
			state = conn.prepareStatement(ALL_DQL);
			rs = state.executeQuery();
			
			System.out.println("=== site_user table ===");
			System.out.println("unique_id \t user_name \t password \t role \t sign_date");
			System.out.println("-------------------------------------------------------------------");
			while(rs.next()) {
				System.out.printf("%d \t\t %s \t %s \t %s \t ", 
					rs.getInt("unique_id"),
					rs.getString("user_name"),
					rs.getString("password"),
					rs.getString("role")
				);
				System.out.println(rs.getDate("sign_date"));
			}
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	public void insertOneRecord(int uniqueId, String userName, String password, String role) {
		try {
			state = conn.prepareStatement(INSERT_SQL);
			
			state.setInt(1, uniqueId);
			state.setString(2, userName);
			state.setString(3, password);
			state.setString(4, role);
			
			// 회원 가입 날짜는 이 코드를 실행하는 현재 날짜로 함.
			java.util.Date today = new java.util.Date();
			java.sql.Date sqlDate = new java.sql.Date(today.getTime());
			state.setDate(5, sqlDate);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	/**
	 * 한 건의 유저 이름 및 패스워드만 다른 값으로 수정한다. 
	 * @param uniqueId - 수정할 대상 데이터의 unique_id를 지정.
	 * @param userName
	 * @param password
	 */
	public void updateOneRecord(int uniqueId, String userName, String password) {
		try {
			state = conn.prepareStatement(UPDATE_SQL);
			
			state.setInt(3, uniqueId);
			state.setString(1, userName);
			state.setString(2, password);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	/**
	 * 특정 unique_id를 가지는 데이터 한 건을 삭제한다. 
	 * @param uniqueId
	 */
	public void deleteOneRecord(int uniqueId) {
		try {
			state = conn.prepareStatement(DELETE_SQL);
			
			state.setInt(1, uniqueId);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	public void close() {
		jtool.close(conn, state, rs);
	}
	
}

```

DBDao.java 

위 클래스 내부에는 select문으로 조회한 모든 데이터들을 출력하는 `getAllReconds()`와, DML 언어인 insert, update, delete 문을 사용하는 `insertOneRecord()`, `updateOneRecord()`, `deleteOneRecord()` 메서드를 각각 정의하였다. 또한, DB 연동 작업을 생성자 메서드 내부에 실행하도록 하였고, 원하는 때에 DB 연동을 해제할 수 있도록 `close()` 메서드를 따로 정의하였으며, 이 메서드는 내부적으로 JDBCTool의 `close()` 메서드를 사용하고 있다. 

이제 DAO 패턴을 이용하여 데이터 조회 및 삽입, 수정, 삭제 작업을 해보자. 

```java
package com.cola.db.user;

import com.cola.db.lib.DBDao;

public class InsertWithDao {

	public static void main(String[] args) {
		DBDao dao = new DBDao();
		
		System.out.println("새 데이터 삽입 전");
		System.out.println();  // 개행
		dao.getAllRecords();
		
		System.out.println("새 데이터 삽입 후");
		System.out.println();
		dao.insertOneRecord(3, "javas456", "javas2222", "USER");
		dao.getAllRecords();
		
		dao.close();
	}

}

```

InsertWithDao.java 

```java
새 데이터 삽입 전

=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
새 데이터 삽입 후

1건 처리되었습니다.
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 javas456 	 javas2222 	 USER 	 2024-09-01

```

InsertWithDao.java  실행결과

```java
package com.cola.db.user;

import com.cola.db.lib.DBDao;

public class UpdateWithDao {

	public static void main(String[] args) {
		DBDao dao = new DBDao();
		
		System.out.println("수정 전");
		System.out.println(); // 개행
		dao.getAllRecords();
		System.out.println();
		
		dao.updateOneRecord(3, "naithon789", "naithon1111");
		System.out.println("수정 후");
		System.out.println();
		dao.getAllRecords();
		System.out.println();
		
		dao.close();
	}

}

```

UpdateWithDao.java

```java
수정 전

=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 javas456 	 javas2222 	 USER 	 2024-09-01

1건 처리되었습니다.
수정 후

=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 naithon789 	 naithon1111 	 USER 	 2024-09-01

```

UpdateWithDao.java 실행결과

```java
package com.cola.db.user;

import com.cola.db.lib.DBDao;

public class DeleteWithDao {

	public static void main(String[] args) {
		DBDao dao = new DBDao();
		
		System.out.println("삭제 전\n");
		dao.getAllRecords();
		System.out.println();
		
		System.out.println("삭제 후\n");
		dao.deleteOneRecord(3);
		dao.getAllRecords();
		
		dao.close();
	}

}

```

DeleteWithDao.java

```java
삭제 전

=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 naithon789 	 naithon1111 	 USER 	 2024-09-01

삭제 후

1건 처리되었습니다.
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01

```

DeleteWithDao.java 실행결과

위 코드들을 보면 알겠지만, 데이터 작업 코드가 매우 간결해진 것을 확인할 수 있다. 또한 하나의 파일 내에 DML 작업과 DQL 작업이 동시에 이뤄지고 있다. DAO를 사용하지 않았더라면 하나의 SQL문을 사용해도 코드가 너무 길었을 텐데, DAO를 사용하니 둘 이상의 쿼리문을 사용해도 코드 상으로 간결하고, 코드의 재사용성도 확보할 수 있음을 알 수 있다. 

### DAO의 문제점과 VO 패턴 적용

그런데, 앞서 작성한 DBDao 클래스의 insertOneRecord() 메서드를 자세히 보면 한 가지 문제점이 있다. 여러 매개변수들 중 String 자료형을 가지는 매개변수들이 3개나 되어 만약 userName을 입력할 공간에 role을 입력한다든가 해도 에러가 발생하지 않고 그대로 데이터가 삽입되고 말 것이다. 그래서 이 경우 데이터의 무결성을 지킬 수 없게 된다. 다음의 코드를 통해 사용자 이름과 권한명이 뒤바뀌어 버린 대참사(?)가 날 수 있음을 볼 수 있다. 

```java
package com.cola.db.user;

import com.cola.db.lib.DBDao;

public class WrongInsertWithDao {

	public static void main(String[] args) {
		DBDao dao = new DBDao();
		
		System.out.println("새 데이터 삽입 전");
		System.out.println();  // 개행
		dao.getAllRecords();
		
		System.out.println("새 데이터 삽입 후");
		System.out.println();
		
		// 사용자 이름과 권한명이 실수로 뒤바뀐채 삽입하려고 하고 있다. 그런데 에러 없이 수행되어버리고 만다...
		dao.insertOneRecord(3, "USER", "javas2222", " javas456");
		dao.getAllRecords();
		
		dao.close();
	}

}

```

WrongInsertWithDao.java

```java
새 데이터 삽입 전

=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
새 데이터 삽입 후

1건 처리되었습니다.
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 USER 	 javas2222 	  javas456 	 2024-09-01

```

WrongInsertWithDao.java 실행결과

이렇듯, 메서드의 매개변수의 개수가 너무 많으면 개발자가 실수로 데이터가 서로 바뀐 채 인자로 전달할 수 있는 위험성이 있다. 이러한 매개변수의 개수를 줄이면서 동시에 데이터의 무결성도 지킬 수 있는 방법으로 VO(Value Object) 디자인 패턴을 사용해보자. 

### VO(Value Object)

다음의 코드에서도 알 수 있듯, VO 클래스는 값들만 존재하는 클래스로, DB에 삽입할 필드들을 맴버 변수로 선언하고, 이를 getter, setter 메서드로 값 조회 및 초기화할 수 있도록 고안되어 있다. 

아래의 DBVo 클래스가 VO 패턴에 해당한다. 

```java
package com.cola.db.lib;

import java.sql.Date;

public class DBVo {
	private int uniqueId = 0;
	private String userName = null;
	private String password = null;
	private String role = null;
	private Date signDate = null;
	
	public int getUniqueId() {
		return uniqueId;
	}
	
	public void setUniqueId(int uniqueId) {
		this.uniqueId = uniqueId;
	}
	
	public String getUserName() {
		return userName;
	}
	
	public void setUserName(String userName) {
		this.userName = userName;
	}
	
	public String getPassword() {
		return password;
	}
	
	public void setPassword(String password) {
		this.password = password;
	}
	
	public String getRole() {
		return role;
	}
	
	public void setRole(UserRole role) {
		switch (role) {
			case USER:
				this.role = "USER";
				break;
			case ADMIN:
				this.role = "ADMIN";
				break;
		}
	}
	public void setRole(String role) {
		this.role = role;
	}
	
	public Date getSignDate() {
		return signDate;
	}

	public void setSignDate(Date signDate) {
		this.signDate = signDate;
	}
	
}

```

DBVo.java

```java
package com.cola.db.lib;

public enum UserRole {
	USER, ADMIN
}

```

UserRole.java

그리고 이 VO 클래스를 사용하도록 DBDaoVo 클래스를 작성하였다. 

```java
package com.cola.db.lib;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class DBDaoVo {
	private Connection conn = null;
	private PreparedStatement state = null;
	private ResultSet rs = null;
	private JDBCTool jtool = new JDBCTool();
	
	private final String ALL_DQL = "select * from site_user";
	private final String INSERT_SQL = "insert into site_user values(?, ?, ?, ?, ?)";
	private final String UPDATE_SQL = "update site_user set user_name = ?, password = ? where unique_id = ?";
	private final String DELETE_SQL = "delete site_user where unique_id = ?";
	
	public DBDaoVo() {
		conn = jtool.getConnection();
	}
	
	public void printAllRecords() {
		try {
			state = conn.prepareStatement(ALL_DQL);
			rs = state.executeQuery();
			
			System.out.println("=== site_user table ===");
			System.out.println("unique_id \t user_name \t password \t role \t sign_date");
			System.out.println("-------------------------------------------------------------------");
			while(rs.next()) {
				System.out.printf("%d \t\t %s \t %s \t %s \t ", 
					rs.getInt("unique_id"),
					rs.getString("user_name"),
					rs.getString("password"),
					rs.getString("role")
				);
				System.out.println(rs.getDate("sign_date"));
			}
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	public List<DBVo> getAllRecords() {
		List<DBVo> allData = new ArrayList<DBVo>();
		
		try {
			state = conn.prepareStatement(ALL_DQL);
			rs = state.executeQuery();
			
			while(rs.next()) {
				DBVo vo = new DBVo();
				vo.setUniqueId(rs.getInt("unique_id"));
				vo.setUserName(rs.getString("user_name"));
				vo.setPassword(rs.getString("password"));
				vo.setRole(rs.getString("role"));
				vo.setSignDate(rs.getDate("sign_date"));
				
				allData.add(vo);
			}
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
		
		return allData;
	}
	
	public void insertOneRecord(DBVo vo) {
		try {
			state = conn.prepareStatement(INSERT_SQL);
			
			state.setInt(1, vo.getUniqueId());
			state.setString(2, vo.getUserName());
			state.setString(3, vo.getPassword());
			state.setString(4, vo.getRole());
			
			// 회원 가입 날짜는 이 코드를 실행하는 현재 날짜로 함.
			java.util.Date today = new java.util.Date();
			java.sql.Date sqlDate = new java.sql.Date(today.getTime());
			state.setDate(5, sqlDate);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	/**
	 * 한 건의 유저 이름 및 패스워드만 다른 값으로 수정한다. 
	 * @param uniqueId - 수정할 대상 데이터의 unique_id를 지정.
	 * @param userName
	 * @param password
	 */
	public void updateOneRecord(DBVo vo) {
		try {
			state = conn.prepareStatement(UPDATE_SQL);
			
			state.setInt(3, vo.getUniqueId());
			state.setString(1, vo.getUserName());
			state.setString(2, vo.getPassword());
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	/**
	 * 특정 unique_id를 가지는 데이터 한 건을 삭제한다. 
	 * @param uniqueId
	 */
	public void deleteOneRecord(int uniqueId) {
		try {
			state = conn.prepareStatement(DELETE_SQL);
			
			state.setInt(1, uniqueId);
			
			int rowCount = state.executeUpdate();
			System.out.println(rowCount + "건 처리되었습니다.");
			
		} catch (SQLException e) {
			jtool.handleSQLException(e);
		}
	}
	
	public void close() {
		jtool.close(conn, state, rs);
	}
	
}

```

DBDaoVo.java

위 코드의 `InsertOneRecord()`, `updateOneRecord()` 메서드의 매개변수가 DBVo 단 하나의 객체만 받도록 되어있는 것을 볼 수 있다. 그리고 메서드 내부에서 DBVo 객체의 getter 메서드를 호출하여 실제 값을 SQL문에 대입하는 방식임을 확인할 수 있다. 

다음은 DBDaoVo와 DBVo를 이용하여 DML 작업을 하는 코드이다. 

```java
package com.cola.db.user;

import com.cola.db.lib.DBDaoVo;
import com.cola.db.lib.DBVo;
import com.cola.db.lib.UserRole;

public class DMLWithVo {

	public static void main(String[] args) {
		DBDaoVo daoVo = new DBDaoVo();
		
		// 잘못 입력된 데이터 삭제
		daoVo.deleteOneRecord(3);
		
		System.out.println("기존 테이블 데이터");
		daoVo.printAllRecords();
		System.out.println();
		
		System.out.println("데이터 한 건 삽입 후\n");
		DBVo vo = new DBVo();
		vo.setUniqueId(3);
		vo.setUserName("javas456");
		vo.setPassword("javas2222");
		vo.setRole(UserRole.USER);
		daoVo.insertOneRecord(vo);
		daoVo.printAllRecords();
		System.out.println();
		
		System.out.println("데이터 한 건 수정 후\n");
		DBVo voUpdate = new DBVo();
		voUpdate.setUniqueId(3);
		voUpdate.setUserName("naithon789");
		voUpdate.setPassword("naithon3333");
		daoVo.updateOneRecord(voUpdate);
		daoVo.printAllRecords();
		
		daoVo.close();
	}

}

```

DMLWithVo.java

```java
1건 처리되었습니다.
기존 테이블 데이터
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01

데이터 한 건 삽입 후

1건 처리되었습니다.
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 javas456 	 javas2222 	 USER 	 2024-09-01

데이터 한 건 수정 후

1건 처리되었습니다.
=== site_user table ===
unique_id 	 user_name 	 password 	 role 	 sign_date
-------------------------------------------------------------------
1 		 kimquel123 	 kimquel1111 	 USER 	 2024-09-01
2 		 jeongdb123 	 jeongdb1111 	 ADMIN 	 2024-09-01
3 		 naithon789 	 naithon3333 	 USER 	 2024-09-01

```

DMLWithVo.java 실행결과

이렇게 VO 클래스를 사용하면 개발자가 필드가 서로 바뀐 채 데이터를 삽입하는 등의 실수를 미연에 방지할 수 있게 된다. 

또한, VO 클래스를 사용하면 데이터를 조회할 때에도 원하는 데이터만 가져올 수 있다. DBDaoVo 클래스의 `public List<DBVo> getAllRecords()` 가 그 예이다. 다음은 이 메서드를 이용하여 원하는 결과만 출력하는 예제이다. 

```java
package com.cola.db.user;

import java.util.ArrayList;
import java.util.List;

import com.cola.db.lib.DBDaoVo;
import com.cola.db.lib.DBVo;

public class CustomDQL {

	public static void main(String[] args) {
		// TODO 전체 레코드로부터 출력하고자 하는 데이터만 골라 출력할 수 있다.
		
		DBDaoVo daoVo = new DBDaoVo();
		
		List<DBVo> allRecords = daoVo.getAllRecords();
		
		System.out.println("site_user 테이블 내 전체 건수: " + allRecords.size());
		System.out.println(); // 개행.
		
		// role이 USER인 사람들의 데이터만 조회.
		List<DBVo> users = new ArrayList<DBVo>();
		for (DBVo vo : allRecords) {
			if (vo.getRole().equals("USER")) {
				users.add(vo);
			}
		}
		
		System.out.println("USER 권한을 가지는 사용자들의 수: " + users.size());
		System.out.println();
		
		System.out.println("=== 사용자 목록 ===");
		System.out.println("unique_id \t user_name \t sign_date");
		System.out.println("-------------------------------------------");
		users.forEach((vo) -> {
			System.out.println(
					vo.getUniqueId() + " \t\t " + 
					vo.getUserName() + " \t " + 
					vo.getSignDate()
			);
		});
		
	}

}

```

CustomDQL.java

```java
site_user 테이블 내 전체 건수: 3

USER 권한을 가지는 사용자들의 수: 2

=== 사용자 목록 ===
unique_id 	 user_name 	 sign_date
-------------------------------------------
1 		 kimquel123 	 2024-09-01
3 		 naithon789 	 2024-09-01

```

CustomDQL.java 실행결과

이렇게 DAO와 VO 디자인 패턴을 사용하면 JDBC를 더 수월하게 다룰 수 있게 되며, DB 작업에 대한 코드 재사용성 및 가독성 확보, 긴 코드를 압축할 수 있음으로 인해 다른 로직에 더 집중할 수 있게 된다는 이점을 확보할 수 있게 된다. 따라서 JDBC를 다룰 때에는 가급적 DAO와 VO 디자인 패턴을 적극적으로 사용하도록 해보자. 

# 추가 정보

DB와 연동하여 데이터를 주고받을 때 사용하는 디자인 패턴이 엄연히 말하면 VO가 아닌 DTO라고 한다. VO, DTO 디자인 패턴에 대한 개념과 둘의 차이점에 대한 자세한 사항은 [[JDBC] DTO vs VO](/servlet%20&%20jsp/jdbc-dto-vs-vo/) 페이지 참고. 

---

References

[1] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[2] DAO pattern

[디자인 패턴 정리 - DAO Pattern](https://ws-pace.tistory.com/181)

[3] DB에서의 Date 자료형을 JDBC에서 다루는 법.

[[Java] java.util.Date와 java.sql.Date의 차이점 및 변환](https://sinau.tistory.com/5)