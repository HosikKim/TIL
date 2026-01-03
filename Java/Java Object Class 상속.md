# Java Object 클래스 상속

## 개념

모든 자바 클래스는 자동으로 `java.lang.Object` 클래스를 상속받음
```java
// 이렇게 작성해도
public class MyClass {
}

// 실제로는 이렇게 동작
public class MyClass extends Object {
}
```

## Object 클래스의 위치

- 자바 클래스 계층 구조의 최상위
- `java.lang` 패키지 (자동 import)
- 모든 클래스의 공통 조상
```
        Object
          ↓
    ┌─────┴─────┐
  String      ArrayList
              ↓
          MyClass
```

## 주요 메서드

### 1. toString()
객체를 문자열로 표현
```java
public class User {
    private String name;
    private int age;
    
    // Object의 toString() 오버라이딩
    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}

User user = new User("홍길동", 25);
System.out.println(user);  // User{name='홍길동', age=25}
// toString() 생략 시: User@15db9742 (클래스명@해시코드)
```

### 2. equals()
객체 동등성 비교
```java
public class User {
    private String id;
    
    // Object의 equals() 오버라이딩
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;  // 같은 참조
        if (obj == null || getClass() != obj.getClass()) return false;
        
        User user = (User) obj;
        return id.equals(user.id);
    }
}

User user1 = new User("hong");
User user2 = new User("hong");

user1 == user2;        // false (다른 객체)
user1.equals(user2);   // true (id가 같음)
```

### 3. hashCode()
객체의 해시코드 반환 (HashMap, HashSet에서 사용)
```java
@Override
public int hashCode() {
    return Objects.hash(id);  // Java 7+
}

// equals()를 오버라이딩하면 hashCode()도 함께 오버라이딩 필수!
```

### 4. getClass()
런타임에 객체의 클래스 정보 반환
```java
User user = new User();
Class clazz = user.getClass();

System.out.println(clazz.getName());        // User
System.out.println(clazz.getSimpleName());  // User
```

### 5. clone()
객체 복사 (얕은 복사)
```java
public class User implements Cloneable {
    private String name;
    
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}

User user1 = new User("홍길동");
User user2 = (User) user1.clone();  // 복사본 생성
```

### 6. finalize() (Deprecated)
가비지 컬렉션 전 호출 - **사용 권장하지 않음**
```java
// 사용하지 말 것!
@Override
protected void finalize() throws Throwable {
    // 리소스 정리
}
```

## 다형성 활용
```java
// 모든 객체를 Object 타입으로 받을 수 있음
public void printAny(Object obj) {
    System.out.println(obj.toString());
}

printAny("문자열");
printAny(123);
printAny(new User());
printAny(new ArrayList<>());
```

## 리플렉션 활용
- `getClass()` 메서드를 통한 런타임 타입 정보 접근
- 동적 프로그래밍 기능 강화
```java
User user = new User();

// 클래스 정보
Class clazz = user.getClass();

// 메서드 정보
Method[] methods = clazz.getDeclaredMethods();
for (Method method : methods) {
    System.out.println(method.getName());
}

// 필드 정보
Field[] fields = clazz.getDeclaredFields();
for (Field field : fields) {
    System.out.println(field.getName());
}
```

## 실전 예제

### DTO에서 Object 메서드 활용
```java
public class UserDto {
    private Long id;
    private String name;
    private String email;
    
    // 생성자, getter, setter
    
    @Override
    public String toString() {
        return "UserDto{id=" + id + ", name='" + name + "'}";
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        UserDto userDto = (UserDto) o;
        return Objects.equals(id, userDto.id);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}

// 사용
UserDto user1 = new UserDto(1L, "홍길동", "hong@example.com");
UserDto user2 = new UserDto(1L, "홍길동", "hong@example.com");

System.out.println(user1);              // toString() 자동 호출
System.out.println(user1.equals(user2)); // true (id가 같음)

// HashMap에서 키로 사용 가능
Map map = new HashMap<>();
map.put(user1, "데이터");
map.get(user2);  // "데이터" (equals()와 hashCode() 덕분)
```

## 참고 자료

- [Object 클래스 공식 문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)
