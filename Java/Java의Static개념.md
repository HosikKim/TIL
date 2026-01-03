# Java의 Static

## 개념

클래스 레벨에서 관리되는 멤버로, 모든 인스턴스가 공유
```java
class Counter {
    static int count = 0;  // 모든 객체가 공유
    int instanceCount = 0; // 객체마다 독립적
}
```

## Static 변수 (클래스 변수)

**특징**:
- 클래스 로딩 시 Method Area에 한 번만 생성
- 모든 인스턴스가 같은 값 공유
- 객체 생성 없이 접근 가능
```java
class Student {
    static String school = "한국대학교";  // 모든 학생이 공유
    String name;                         // 학생마다 다름
    
    public Student(String name) {
        this.name = name;
    }
}

// 사용
System.out.println(Student.school);  // 객체 없이 접근

Student s1 = new Student("김철수");
Student s2 = new Student("이영희");

s1.school = "서울대학교";  // static 변수 변경
System.out.println(s2.school);  // "서울대학교" (공유됨)
```

**메모리 상태**:
```
Method Area:
├─ Student.school = "서울대학교" (공유)

Heap:
├─ s1: name = "김철수"
└─ s2: name = "이영희"
```

## Static 메서드 (클래스 메서드)

**특징**:
- 객체 생성 없이 호출 가능
- 인스턴스 변수 사용 불가
- 주로 유틸리티 메서드에 사용
```java
class Calculator {
    // Static 메서드
    public static int add(int a, int b) {
        return a + b;
    }
    
    // 인스턴스 메서드
    public int multiply(int a, int b) {
        return a * b;
    }
}

// 사용
int result1 = Calculator.add(5, 3);  // ✅ 객체 없이 호출

Calculator calc = new Calculator();
int result2 = calc.multiply(5, 3);   // ✅ 객체 생성 후 호출
```

## Static 메서드의 제약
```java
class MyClass {
    static int staticVar = 10;
    int instanceVar = 20;
    
    public static void staticMethod() {
        System.out.println(staticVar);      // ✅ static 변수 사용 가능
        System.out.println(instanceVar);    // ❌ 인스턴스 변수 사용 불가
        
        instanceMethod();                   // ❌ 인스턴스 메서드 호출 불가
        
        // 이유: 어떤 객체의 instanceVar, instanceMethod인지 모름
    }
    
    public void instanceMethod() {
        System.out.println(staticVar);      // ✅ static 변수 사용 가능
        System.out.println(instanceVar);    // ✅ 인스턴스 변수 사용 가능
    }
}
```

**왜 불가능한가?**
- Static 메서드는 객체 없이 실행됨
- 인스턴스 변수/메서드는 객체에 속함
- 어떤 객체의 것인지 특정할 수 없음

## Static vs 인스턴스 비교

| 구분 | Static | 인스턴스 |
|------|--------|----------|
| 메모리 할당 | 클래스 로딩 시 1회 | 객체 생성마다 |
| 메모리 위치 | Method Area | Heap |
| 접근 방법 | 클래스명.멤버 | 객체명.멤버 |
| 공유 여부 | 모든 객체 공유 | 객체마다 독립 |
| 생명 주기 | 프로그램 종료까지 | 객체 소멸까지 |
| this 사용 | 불가 | 가능 |

## 실전 활용

### 1. 유틸리티 클래스
```java
class MathUtils {
    // 모두 static 메서드
    public static int max(int a, int b) {
        return a > b ? a : b;
    }
    
    public static int min(int a, int b) {
        return a < b ? a : b;
    }
    
    public static double average(int... numbers) {
        int sum = 0;
        for (int num : numbers) {
            sum += num;
        }
        return (double) sum / numbers.length;
    }
    
    // 객체 생성 방지
    private MathUtils() {}
}

// 사용 (객체 생성 없이)
int max = MathUtils.max(10, 20);
double avg = MathUtils.average(1, 2, 3, 4, 5);
```

**Java 기본 제공 예시**: `Math.abs()`, `Math.max()`, `Arrays.sort()`

### 2. 상수 정의
```java
class AppConfig {
    public static final String APP_NAME = "MyApp";
    public static final String VERSION = "1.0.0";
    public static final int MAX_USERS = 1000;
    
    // final: 값 변경 불가
    // static final: 클래스 레벨 상수
}

// 사용
System.out.println(AppConfig.APP_NAME);
// AppConfig.APP_NAME = "NewApp";  // ❌ 컴파일 에러
```

### 3. 싱글톤 패턴
```java
class DatabaseConnection {
    private static DatabaseConnection instance;
    
    // private 생성자 (외부에서 객체 생성 불가)
    private DatabaseConnection() {
        System.out.println("DB 연결 생성");
    }
    
    // static 메서드로 유일한 인스턴스 반환
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }
}

// 사용
DatabaseConnection db1 = DatabaseConnection.getInstance();
DatabaseConnection db2 = DatabaseConnection.getInstance();

System.out.println(db1 == db2);  // true (같은 객체)
```

### 4. 카운터 (공유 변수)
```java
class Visitor {
    static int totalCount = 0;  // 전체 방문자 수
    int visitCount = 0;          // 개인 방문 횟수
    String name;
    
    public Visitor(String name) {
        this.name = name;
        totalCount++;  // 객체 생성마다 증가
    }
    
    public void visit() {
        visitCount++;
        System.out.println(name + " 방문: " + visitCount + "회");
        System.out.println("총 방문자: " + totalCount + "명");
    }
}

// 사용
Visitor v1 = new Visitor("김철수");  // totalCount = 1
Visitor v2 = new Visitor("이영희");  // totalCount = 2

v1.visit();  // 김철수 방문: 1회, 총 방문자: 2명
v2.visit();  // 이영희 방문: 1회, 총 방문자: 2명
v1.visit();  // 김철수 방문: 2회, 총 방문자: 2명
```

## Static 초기화 블록

복잡한 초기화 로직이 필요할 때 사용
```java
class Config {
    static Map<String, String> settings;
    
    // static 초기화 블록 (클래스 로딩 시 1회 실행)
    static {
        System.out.println("Static 초기화 블록 실행");
        settings = new HashMap<>();
        settings.put("url", "localhost");
        settings.put("port", "8080");
        settings.put("timeout", "3000");
    }
    
    // 일반 초기화 블록 (객체 생성마다 실행)
    {
        System.out.println("인스턴스 초기화 블록 실행");
    }
}
```

## 주의사항

### 1. 멀티스레드 환경
```java
class Counter {
    static int count = 0;
    
    // 동기화 필요
    public static synchronized void increment() {
        count++;
    }
}
```

### 2. 메모리 누수 가능성
```java
class Cache {
    static Map<String, Object> cache = new HashMap<>();
    
    // 계속 쌓이면 메모리 누수
    public static void put(String key, Object value) {
        cache.put(key, value);
    }
    
    // 정리 메서드 필요
    public static void clear() {
        cache.clear();
    }
}
```

### 3. 테스트 어려움
```java
class UserService {
    static List<User> users = new ArrayList<>();  // 테스트 간 영향
    
    // 인스턴스 변수로 변경 권장
    // private List<User> users = new ArrayList<>();
}
```

### 4. 객체지향 원칙 위배
```java
// ❌ 나쁜 예: 과도한 static 사용
class MathService {
    public static int add(int a, int b) { return a + b; }
    public static int subtract(int a, int b) { return a - b; }
}

// ✅ 좋은 예: 상태가 필요하면 인스턴스 사용
class Calculator {
    private int memory = 0;
    
    public int add(int a, int b) {
        int result = a + b;
        memory = result;
        return result;
    }
}
```

## 실행 순서
```java
class Order {
    static int staticVar = 1;
    int instanceVar = 10;
    
    static {
        System.out.println("1. static 초기화 블록");
    }
    
    {
        System.out.println("2. 인스턴스 초기화 블록");
    }
    
    public Order() {
        System.out.println("3. 생성자");
    }
}

// 실행
Order o1 = new Order();
// 출력:
// 1. static 초기화 블록 (클래스 로딩 시 1회)
// 2. 인스턴스 초기화 블록
// 3. 생성자

Order o2 = new Order();
// 출력:
// 2. 인스턴스 초기화 블록 (static 블록은 실행 안 됨)
// 3. 생성자
```

## 정리

**Static 사용하는 경우**:
- 유틸리티 메서드 (Math, Arrays 등)
- 상수 정의
- 싱글톤 패턴
- 모든 객체가 공유해야 하는 값

**Static 피해야 하는 경우**:
- 객체의 상태를 관리할 때
- 다형성이 필요할 때
- 테스트가 중요할 때

## 참고 자료

- [Oracle Java Static 가이드](https://docs.oracle.com/javase/tutorial/java/javaOO/classvars.html)