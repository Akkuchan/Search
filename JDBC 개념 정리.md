# JDBC(Java DataBase Connectivity)
- 일반 사용자가 사용할 웹 어플리케이션과 DBMS를 연결하는 과정에서 여러 DBMS별로 소스코드와 기능의 차이가 조금씩 존재한다.
- 이로 인해 DBMS를 바꾸거나 어플리케이션 제작시 각 DBMS의 특징에 맞춘 작업을 해야하는 번거로움이 생긴다.
- 이를 방지하는 개념이 바로 JDBC.
- 예로 오라클, mysql, ms sql 등과 어플리케이션을 연결해주는 어댑터, 변환기와 같은 기능을 제공한다.
- 사용자는 각 DBMS별 차이를 생각하지 않아도 JDBC만 사용할 줄 안다면 연결방법을 단일화 할 수 있다는 장점이 있다.
- 즉 우리가 사용할 어플리케이션(언어)과 DBMS를 이어줄 인터페이스인 것이다.

## JDBC 드라이버
- 각 DBMS에 맞춰 만들어진 연결을 위한 클라이언트이다.
- 이때 어플과 DBMS를 실질적으로 이어주는 구동 코드가 JDBC 드라이버이며 JDBS 라이브러리의 자체 기능은 없JDBS

<img src="../images/JDBC 구조.png" width="600" height="300"></img><br/>

## JDBC 기본 코드의 이해
- JDBS를 이용하려면 다름 단계를 거친다.
  1. 드라이버 로드(Driver load)
    - ``` java Class.forName("oracle.jdbc.driver.OracleDriver");```
    - 드라이버를 다운받아 빌드 패스를 실행하고 Class.forName를 통해 드라이버를 메모리상에 로드해 객체화시킨다.
    - 인자값으로는 드라이버 인터페이스를 상속하는 클래스의 이름을 패키지와 함께 명시 ("oracle.jdbc.driver.OracleDriver")
    - 이를 통해 JDBC 드라이버 인스턴스가 생성된다.
      - my sql : com.mysql.jdbc.Driver
      - Oracle : oracle.jdbc.driver.OracleDriver
      - Mssql : com.microsoft.sqlserver.jdbc.SQLServerDriver
  2. DBMS 서버 접속(Connection)
    - ```String url = "jdbs:orcle:thin:@localhost:1521:xepdb1";
      java Connection con = DriverManager.getConnection(url,"kim","1234");```
    - 해당 코드를 통해 연결객체를 생성한다(즉 서버와 연결할 통로를 만든다는 의미)
    - getConnection()를 통해 어플리케이션과 DBMS를 연결해준다.
    - 인자값으로는 getConnection(url, user, password)이며 모두 String 타입이다
      - url은 jdbc:[DBMS]:[데이터베이스식별자]로 구성된다
    - 연결이 완료되면 연결되었다는 상태를 Connection 객체에 담아 반환한다.
  3. Statement/PreparedStatement
    - ```java Statement st = con.createStatement();```
    - Statement는 사용자가 요청하는 SQL문을 DB에 전달하고 처리된 결과를 다시 어플리케이션(클라이언트)에 전달하는 역할을 한다.
    - 이를 위해 connection 객체의 createStatement()를 사용.
  4. SQL문 실행
    - ```java ResultSet re = st.executeQuery(sql);
         rs.next();
         String title = rs.getString("title")```
    - 이전에 생성한 Statement 객체 st를 이용해 DB 서버로 SQL문을 전송하고 처리 결과를 받아온다.
    - ResultSet executeQuery(string sql) : 매개인자 SQL문이 실행되고 난 결과값을 ResultSet 타입으로 반환하다.
    - int executeUpdate(String sql) : SQL문(update, delete, insert등의 결과물이 반환되지 않는 sql문)이 실행된 후 영향을 받은 레코드의 개수를 반환
      - ResultSet은 executeQuery()를 통해 실핸한 SQL문(select문)의 결과값을 담는 객체.
      - ResultSet 객체가 담은 결과값은 ResultSet객체가 보유한 메서드를 통해 추출할 수 있다.
      - 내부의 Cursor를 통해 각 값의 위치를 가리킬 수 있다.
      - boolean 타입의 next()는 결과값에서 다음 레코드가 있는지를 확인 후 boolean타입으로 반환하며 다음값이 있을 때 커서를 다음 레코드로 이동시킨다.
      - Result 객체에는 각 결과값에서 컬럼별로 값을 가져올 수 있는 getter메서드가 존재한다.Ex.rs.getString("컬럼명")
      - 이를 통해 원하는 컬럼별 값을 가져올 수 있다.
  6. 매모리 해제(Close)
    - ```java rs.close();
          st.close();
          con.close(); ```
    - 작업 완료 후 최근 생성 객체부터 차례대로 메모리상에서 값을 제거한다.
