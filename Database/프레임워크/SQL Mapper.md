# SQL Mapper

## 개념

SQL을 **직접 작성**하고 실행 결과를 객체에 매핑하는 프레임워크
```
SQL 작성 → 실행 → 결과를 객체로 매핑
```

### 특징
- 개발자가 SQL 완전 제어
- XML 또는 어노테이션으로 매핑 정의
- 객체는 단순 데이터 전달 역할

## 동작 방식

### MyBatis 예시
```xml
<!-- UserMapper.xml -->
<mapper namespace="com.example.UserMapper">
    <select id="findById" resultType="User">
        SELECT id, name, email
        FROM users
        WHERE id = #{id}
    </select>
    
    <insert id="insertUser" parameterType="User">
        INSERT INTO users (name, email)
        VALUES (#{name}, #{email})
    </insert>
</mapper>
```
```java
// Java 코드
public interface UserMapper {
    User findById(Long id);
    void insertUser(User user);
}

// 사용
User user = userMapper.findById(1L);
```

### 어노테이션 방식
```java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM users WHERE id = #{id}")
    User findById(Long id);
    
    @Insert("INSERT INTO users (name, email) VALUES (#{name}, #{email})")
    void insertUser(User user);
    
    @Update("UPDATE users SET name = #{name} WHERE id = #{id}")
    void updateUser(User user);
}
```

## 장점

| 장점 | 설명 |
|------|------|
| SQL 제어 | 복잡한 쿼리 최적화 가능 |
| 학습 용이 | SQL만 알면 사용 가능 |
| 성능 튜닝 | 세밀한 성능 조정 |
| 유연성 | 동적 쿼리 작성 편리 |
```xml
<!-- 동적 쿼리 예시 -->
<select id="searchUsers" resultType="User">
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

## 단점

| 단점 | 설명 |
|------|------|
| SQL 반복 | CRUD마다 SQL 작성 필요 |
| DB 종속성 | DBMS 변경 시 수정 필요 |
| 생산성 | ORM보다 개발 시간 소요 |
| 객체 불일치 | 패러다임 불일치 여전히 존재 |

## SQL Mapper vs ORM

| 구분 | SQL Mapper | ORM |
|------|-----------|-----|
| SQL | 직접 작성 | 자동 생성 |
| 제어 | 완전 제어 | 제한적 |
| 학습 | 쉬움 | 어려움 |
| 생산성 | 중간 | 높음 |
| 복잡한 쿼리 | 쉬움 | 어려움 |
| DB 독립성 | 낮음 | 높음 |

### 코드 비교
```java
// SQL Mapper (MyBatis)
@Select("SELECT u.*, d.name as dept_name " +
        "FROM users u JOIN departments d ON u.dept_id = d.id " +
        "WHERE u.age > #{age} AND d.location = #{location}")
List<User> findUsers(@Param("age") int age, @Param("location") String location);

// ORM (JPA)
List<User> users = userRepository.findByAgeGreaterThanAndDepartment_Location(age, location);
// 또는
@Query("SELECT u FROM User u JOIN u.department d WHERE u.age > :age AND d.location = :location")
List<User> findUsers(@Param("age") int age, @Param("location") String location);
```

## 주요 SQL Mapper

### MyBatis
```xml
<mapper namespace="UserMapper">
    <!-- 결과 매핑 -->
    <resultMap id="userMap" type="User">
        <id property="id" column="user_id"/>
        <result property="name" column="user_name"/>
        <association property="department" javaType="Department">
            <id property="id" column="dept_id"/>
            <result property="name" column="dept_name"/>
        </association>
    </resultMap>
    
    <select id="findUserWithDept" resultMap="userMap">
        SELECT u.id as user_id, u.name as user_name,
               d.id as dept_id, d.name as dept_name
        FROM users u JOIN departments d ON u.dept_id = d.id
    </select>
</mapper>
```

### JdbcTemplate (Spring)
```java
public class UserDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    public User findById(Long id) {
        return jdbcTemplate.queryForObject(
            "SELECT * FROM users WHERE id = ?",
            new Object[]{id},
            (rs, rowNum) -> new User(
                rs.getLong("id"),
                rs.getString("name"),
                rs.getString("email")
            )
        );
    }
}
```

## 동적 쿼리
```xml
<!-- MyBatis 동적 SQL -->
<select id="searchUsers" resultType="User">
    SELECT * FROM users
    <where>
        <if test="name != null">
            AND name LIKE CONCAT('%', #{name}, '%')
        </if>
        <if test="ageMin != null">
            AND age >= #{ageMin}
        </if>
        <if test="ageMax != null">
            AND age <= #{ageMax}
        </if>
        <if test="deptIds != null">
            AND dept_id IN
            <foreach item="id" collection="deptIds" open="(" separator="," close=")">
                #{id}
            </foreach>
        </if>
    </where>
    <choose>
        <when test="orderBy == 'name'">
            ORDER BY name
        </when>
        <otherwise>
            ORDER BY created_at DESC
        </otherwise>
    </choose>
</select>
```

## 사용 가이드

**SQL Mapper 선택 시**:
- 복잡한 조인/서브쿼리 많음
- 성능 최적화 중요
- 레거시 SQL 재사용
- SQL 직접 제어 필요
- 데이터 중심 설계

**ORM 선택 시**:
- 단순 CRUD 중심
- 빠른 개발 필요
- 객체 중심 설계
- DB 변경 가능성

## MyBatis 핵심 기능

### 1. 파라미터 매핑
```xml
<!-- 단일 파라미터 -->
<select id="findById" parameterType="long" resultType="User">
    SELECT * FROM users WHERE id = #{id}
</select>

<!-- 객체 파라미터 -->
<insert id="insertUser" parameterType="User">
    INSERT INTO users (name, email) VALUES (#{name}, #{email})
</insert>

<!-- Map 파라미터 -->
<select id="search" parameterType="map" resultType="User">
    SELECT * FROM users WHERE name = #{name} AND age = #{age}
</select>
```

### 2. 결과 매핑
```xml
<!-- 자동 매핑 -->
<select id="findAll" resultType="User">
    SELECT id, name, email FROM users
</select>

<!-- 수동 매핑 -->
<resultMap id="userResultMap" type="User">
    <id property="id" column="user_id"/>
    <result property="name" column="user_name"/>
    <result property="email" column="user_email"/>
</resultMap>
```

## 참고 자료

- [MyBatis 공식 문서](https://mybatis.org/mybatis-3/)
- [Spring JdbcTemplate](https://docs.spring.io/spring-framework/reference/data-access/jdbc/core.html)