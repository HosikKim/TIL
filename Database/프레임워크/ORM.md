# ORM (Object-Relational Mapping)

## 개념

객체(Object)와 관계형 DB(Relational)를 **자동으로 매핑**하는 기술
```
Java 객체 ←→ ORM ←→ DB 테이블
User.java      JPA      users
```

### 목적
- 객체 지향 언어와 관계형 DB의 패러다임 불일치 해결
- SQL 없이 객체로 DB 조작

## 동작 방식

### 매핑 예시
```java
// 객체
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue
    private Long id;
    
    @Column(name = "user_name")
    private String name;
    
    private String email;
}
```
```sql
-- 자동 생성되는 테이블
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    user_name VARCHAR(255),
    email VARCHAR(255)
);
```

### CRUD 조작
```java
// INSERT
User user = new User("Kim", "kim@example.com");
entityManager.persist(user);  // SQL 자동 생성

// SELECT
User user = entityManager.find(User.class, 1L);

// UPDATE
user.setName("Lee");
// 트랜잭션 커밋 시 자동 UPDATE

// DELETE
entityManager.remove(user);
```

## 장점

| 장점 | 설명 |
|------|------|
| 생산성 | SQL 작성 불필요 |
| 객체 지향 | 비즈니스 로직에 집중 |
| 유지보수 | 코드 재사용성 높음 |
| DB 독립성 | DBMS 변경 용이 |
```java
// SQL 없이 간결한 코드
List<User> users = userRepository.findByName("Kim");
```

## 단점

| 단점 | 설명 |
|------|------|
| 성능 | 복잡한 쿼리 시 비효율적 |
| 학습 곡선 | 초기 학습 비용 높음 |
| 제어 어려움 | 세밀한 최적화 제한 |
| N+1 문제 | 연관 관계 조회 시 쿼리 폭증 |

## ORM vs SQL Mapper

| 구분 | ORM | SQL Mapper |
|------|-----|-----------|
| 추상화 | 높음 (객체 중심) | 낮음 (SQL 중심) |
| SQL 작성 | 자동 생성 | 직접 작성 |
| 학습 난이도 | 높음 | 낮음 |
| 복잡한 쿼리 | 어려움 | 쉬움 |
| 생산성 | 높음 | 중간 |
| DB 독립성 | 높음 | 낮음 |

### 코드 비교
```java
// ORM (JPA)
List<User> users = userRepository.findByAgeGreaterThan(20);

// SQL Mapper (MyBatis)
@Select("SELECT * FROM users WHERE age > #{age}")
List<User> findByAge(@Param("age") int age);
```

## 주요 ORM 프레임워크

| 언어 | 프레임워크 |
|------|-----------|
| Java | JPA/Hibernate, Spring Data JPA |
| Python | Django ORM, SQLAlchemy |
| JavaScript | Sequelize, TypeORM, Prisma |
| .NET | Entity Framework |
| Ruby | ActiveRecord |

## 핵심 개념

### 1. 엔티티 (Entity)
```java
@Entity  // DB 테이블과 매핑되는 클래스
public class User {
    @Id  // 기본키
    private Long id;
}
```

### 2. 연관 관계
```java
@Entity
public class User {
    @OneToMany(mappedBy = "user")  // 1:N 관계
    private List<Order> orders;
}

@Entity
public class Order {
    @ManyToOne  // N:1 관계
    @JoinColumn(name = "user_id")
    private User user;
}
```

### 3. 지연 로딩 (Lazy Loading)
```java
@OneToMany(fetch = FetchType.LAZY)  // 필요 시에만 조회
private List<Order> orders;
```

## N+1 문제
```java
// ❌ N+1 문제 발생
List<User> users = userRepository.findAll();  // 1번 쿼리
for (User user : users) {
    user.getOrders().size();  // 각 user마다 쿼리 (N번)
}

// ✅ 해결: 페치 조인
@Query("SELECT u FROM User u JOIN FETCH u.orders")
List<User> findAllWithOrders();  // 1번 쿼리로 해결
```

## 선택 가이드

**ORM 적합**:
- 단순 CRUD 중심
- 빠른 개발 필요
- 객체 지향 설계
- DB 변경 가능성

**SQL Mapper 적합**:
- 복잡한 쿼리 많음
- 성능 최적화 중요
- 레거시 DB 연동
- SQL 직접 제어 필요

## 참고 자료

- [JPA 공식 스펙](https://jcp.org/en/jsr/detail?id=338)
- [Hibernate 문서](https://hibernate.org/orm/documentation/)
- [Spring Data JPA](https://spring.io/projects/spring-data-jpa)