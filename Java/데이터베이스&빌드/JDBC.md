# JDBC

## 개념

JDBC(Java Database Connectivity)는 자바에서 데이터베이스에 접근하기 위한 표준 API

- 데이터베이스 종류와 상관없이 동일한 방식으로 사용
- SQL 실행 및 결과 처리 기능 제공

## 주요 구성 요소

| 구성 요소 | 역할 |
|-----------|------|
| DriverManager | 데이터베이스 드라이버 관리 및 연결 생성 |
| Connection | 데이터베이스와의 연결 |
| Statement | SQL 쿼리 실행 |
| PreparedStatement | 파라미터가 있는 SQL 실행 (SQL Injection 방지) |
| ResultSet | 조회 결과 데이터 |

## 사용 순서
```
1. 드라이버 로드
2. 연결(Connection) 생성
3. Statement 생성
4. SQL 실행
5. 결과 처리
6. 자원 해제 (close)
```

## 기본 예제

### SELECT 조회
```java
// 1. 드라이버 로드
Class.forName("oracle.jdbc.OracleDriver");

// 2. 연결 생성
Connection con = DriverManager.getConnection(
    "jdbc:oracle:thin:@localhost:1521:XE", 
    "username", 
    "password"
);

// 3. Statement 생성
Statement stmt = con.createStatement();

// 4. SQL 실행
ResultSet rs = stmt.executeQuery("SELECT * FROM students");

// 5. 결과 처리
while(rs.next()) {
    System.out.println("이름: " + rs.getString("name"));
    System.out.println("나이: " + rs.getInt("age"));
}

// 6. 자원 해제
rs.close();
stmt.close();
con.close();
```

### INSERT 삽입 (PreparedStatement 사용)
```java
String sql = "INSERT INTO students (name, age) VALUES (?, ?)";
PreparedStatement pstmt = con.prepareStatement(sql);

// 파라미터 설정
pstmt.setString(1, "홍길동");
pstmt.setInt(2, 20);

// 실행
int result = pstmt.executeUpdate();
System.out.println(result + "개 행 삽입됨");

pstmt.close();
```

### 자원 자동 해제 (try-with-resources)
```java
String sql = "SELECT * FROM students WHERE age > ?";

try (Connection con = DriverManager.getConnection(url, user, password);
     PreparedStatement pstmt = con.prepareStatement(sql)) {
    
    pstmt.setInt(1, 18);
    
    try (ResultSet rs = pstmt.executeQuery()) {
        while(rs.next()) {
            System.out.println(rs.getString("name"));
        }
    }
} catch (SQLException e) {
    e.printStackTrace();
}
// 자동으로 close() 호출됨
```

## Statement vs PreparedStatement

| 구분 | Statement | PreparedStatement |
|------|-----------|-------------------|
| SQL 작성 | 문자열 직접 연결 | ? 파라미터 사용 |
| SQL Injection | 취약 | 안전 |
| 성능 | 매번 컴파일 | 재사용 가능 |
| 사용 시기 | 정적 쿼리 | 동적 쿼리 (권장) |
```java
// Statement (나쁜 예 - SQL Injection 위험)
String name = "홍길동'; DROP TABLE students; --";
Statement stmt = con.createStatement();
stmt.executeQuery("SELECT * FROM students WHERE name = '" + name + "'");

// PreparedStatement (좋은 예 - 안전)
PreparedStatement pstmt = con.prepareStatement("SELECT * FROM students WHERE name = ?");
pstmt.setString(1, name);
pstmt.executeQuery();
```

## JDBC 드라이버 종류

**Type 4 (Thin Driver)** - 가장 많이 사용
- 순수 자바로 구현
- 네트워크를 통해 직접 데이터베이스 연결
- 예: Oracle Thin Driver, MySQL Connector/J

**Type 1~3** - 레거시, 현재 거의 사용 안 함

## 데이터베이스별 연결 URL
```java
// Oracle
"jdbc:oracle:thin:@localhost:1521:XE"

// MySQL
"jdbc:mysql://localhost:3306/database_name"

// PostgreSQL
"jdbc:postgresql://localhost:5432/database_name"
```

## 장점

- 데이터베이스 독립적 (MySQL → Oracle 전환 시 URL만 변경)
- 표준 API로 학습 용이
- 자바의 예외 처리 활용 가능

## 단점

- 반복적인 코드 (연결, 해제)
- SQL을 문자열로 작성 (오타 위험)
- 객체-관계 불일치 문제

→ 이를 해결하기 위해 **MyBatis, JPA** 같은 프레임워크 사용

## 참고 자료

- [Oracle JDBC 문서](https://docs.oracle.com/javase/tutorial/jdbc/)