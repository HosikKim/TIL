# MyBatis

## 개념

SQL과 객체를 매핑해주는 **SQL Mapper 프레임워크**

### 특징
- SQL을 XML 또는 어노테이션으로 분리
- 복잡한 JDBC 코드 제거
- 동적 SQL 지원
- 캐싱 기능

## 기본 구조

### 1. 설정 파일 (mybatis-config.xml)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 2. Mapper XML
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.UserMapper">
    
    <!-- 조회 -->
    <select id="findById" parameterType="long" resultType="User">
        SELECT id, name, email FROM users WHERE id = #{id}
    </select>
    
    <!-- 목록 -->
    <select id="findAll" resultType="User">
        SELECT id, name, email FROM users
    </select>
    
    <!-- 삽입 -->
    <insert id="insertUser" parameterType="User">
        INSERT INTO users (name, email) VALUES (#{name}, #{email})
    </insert>
    
    <!-- 수정 -->
    <update id="updateUser" parameterType="User">
        UPDATE users SET name = #{name}, email = #{email} WHERE id = #{id}
    </update>
    
    <!-- 삭제 -->
    <delete id="deleteUser" parameterType="long">
        DELETE FROM users WHERE id = #{id}
    </delete>
    
</mapper>
```

### 3. Mapper 인터페이스
```java
package com.example.mapper;

public interface UserMapper {
    User findById(Long id);
    List<User> findAll();
    void insertUser(User user);
    void updateUser(User user);
    void deleteUser(Long id);
}
```

### 4. 사용
```java
// SqlSession 생성
SqlSessionFactory factory = new SqlSessionFactoryBuilder()
    .build(Resources.getResourceAsStream("mybatis-config.xml"));
SqlSession session = factory.openSession();

try {
    // Mapper 획득
    UserMapper mapper = session.getMapper(UserMapper.class);
    
    // 조회
    User user = mapper.findById(1L);
    
    // 삽입
    User newUser = new User("Kim", "kim@example.com");
    mapper.insertUser(newUser);
    session.commit();
    
} finally {
    session.close();
}
```

## 어노테이션 방식
```java
@Mapper
public interface UserMapper {
    
    @Select("SELECT * FROM users WHERE id = #{id}")
    User findById(Long id);
    
    @Insert("INSERT INTO users (name, email) VALUES (#{name}, #{email})")
    @Options(useGeneratedKeys = true, keyProperty = "id")
    void insertUser(User user);
    
    @Update("UPDATE users SET name = #{name} WHERE id = #{id}")
    void updateUser(User user);
    
    @Delete("DELETE FROM users WHERE id = #{id}")
    void deleteUser(Long id);
}
```

## 동적 SQL

### if
```xml
<select id="findUsers" resultType="User">
    SELECT * FROM users
    WHERE 1=1
    <if test="name != null">
        AND name LIKE #{name}
    </if>
    <if test="email != null">
        AND email = #{email}
    </if>
</select>
```

### choose/when/otherwise
```xml
<select id="findUsers" resultType="User">
    SELECT * FROM users
    <where>
        <choose>
            <when test="name != null">
                AND name = #{name}
            </when>
            <when test="email != null">
                AND email = #{email}
            </when>
            <otherwise>
                AND status = 'ACTIVE'
            </otherwise>
        </choose>
    </where>
</select>
```

### foreach
```xml
<select id="findByIds" resultType="User">
    SELECT * FROM users
    WHERE id IN
    <foreach item="id" collection="list" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>
```

### trim/where/set
```xml
<!-- where -->
<select id="findUsers" resultType="User">
    SELECT * FROM users
    <where>
        <if test="name != null">name = #{name}</if>
        <if test="email != null">AND email = #{email}</if>
    </where>
</select>

<!-- set -->
<update id="updateUser">
    UPDATE users
    <set>
        <if test="name != null">name = #{name},</if>
        <if test="email != null">email = #{email},</if>
    </set>
    WHERE id = #{id}
</update>
```

## ResultMap

### 기본 매핑
```xml
<resultMap id="userResultMap" type="User">
    <id property="id" column="user_id"/>
    <result property="name" column="user_name"/>
    <result property="email" column="user_email"/>
</resultMap>

<select id="findById" resultMap="userResultMap">
    SELECT user_id, user_name, user_email FROM users WHERE user_id = #{id}
</select>
```

### 1:N 관계
```xml
<resultMap id="userWithOrdersMap" type="User">
    <id property="id" column="user_id"/>
    <result property="name" column="user_name"/>
    <collection property="orders" ofType="Order">
        <id property="id" column="order_id"/>
        <result property="amount" column="amount"/>
    </collection>
</resultMap>

<select id="findUserWithOrders" resultMap="userWithOrdersMap">
    SELECT u.id as user_id, u.name as user_name,
           o.id as order_id, o.amount
    FROM users u
    LEFT JOIN orders o ON u.id = o.user_id
    WHERE u.id = #{id}
</select>
```

### N:1 관계
```xml
<resultMap id="orderWithUserMap" type="Order">
    <id property="id" column="order_id"/>
    <result property="amount" column="amount"/>
    <association property="user" javaType="User">
        <id property="id" column="user_id"/>
        <result property="name" column="user_name"/>
    </association>
</resultMap>
```

## 장점

| 장점 | 설명 |
|------|------|
| SQL 제어 | 완전한 SQL 제어 가능 |
| 학습 용이 | SQL만 알면 쉽게 사용 |
| 성능 | 복잡한 쿼리 최적화 용이 |
| 분리 | SQL과 코드 분리 |
| 유연성 | 동적 SQL 작성 편리 |

## 단점

| 단점 | 설명 |
|------|------|
| SQL 의존 | 모든 CRUD에 SQL 작성 필요 |
| DB 종속 | DBMS 변경 시 수정 필요 |
| 반복 작업 | 매핑 설정 반복 |
| 객체 불일치 | 여전히 패러다임 불일치 존재 |

## MyBatis vs Hibernate/JPA

| 구분 | MyBatis | Hibernate/JPA |
|------|---------|---------------|
| 분류 | SQL Mapper | ORM |
| SQL | 직접 작성 | 자동 생성 |
| 제어 | 완전 제어 | 제한적 |
| 학습 | 쉬움 | 어려움 |
| 복잡한 쿼리 | 쉬움 | 어려움 |
| DB 독립성 | 낮음 | 높음 |
| 생산성 | 중간 | 높음 |

## Spring 연동

### 의존성 (Maven)
```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```

### application.yml
```yaml
mybatis:
  mapper-locations: classpath:mappers/**/*.xml
  type-aliases-package: com.example.domain
  configuration:
    map-underscore-to-camel-case: true
```

### Mapper 사용
```java
@Service
public class UserService {
    
    @Autowired
    private UserMapper userMapper;
    
    public User getUser(Long id) {
        return userMapper.findById(id);
    }
}
```

## 사용 가이드

**MyBatis 선택 시**:
- 복잡한 SQL 많음
- SQL 직접 제어 필요
- 레거시 DB 연동
- 성능 최적화 중요

**JPA 선택 시**:
- 단순 CRUD 중심
- DB 독립성 필요
- 객체 중심 설계

## 참고 자료

- [MyBatis 공식 문서](https://mybatis.org/mybatis-3/)
- [MyBatis Spring Boot](https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)