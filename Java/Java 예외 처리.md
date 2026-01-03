# Java 예외 처리

## 개념

프로그램 실행 중 발생하는 예외 상황을 처리하여 프로그램이 비정상 종료되지 않도록 하는 메커니즘

## 예외 클래스 계층 구조
```
        Throwable
           ↓
    ┌──────┴──────┐
  Error         Exception
                   ↓
            ┌──────┴──────────┐
     RuntimeException    IOException
     (Unchecked)        (Checked)
```

## 예외의 분류

### 1. Checked Exception
컴파일 시점에 반드시 처리해야 하는 예외

**특징**:
- Exception 클래스 상속 (RuntimeException 제외)
- 컴파일러가 처리 여부 확인
- 처리하지 않으면 컴파일 에러 발생

**주요 예외**:

| 예외 | 발생 상황 |
|------|----------|
| IOException | 파일 입출력 실패 |
| SQLException | 데이터베이스 작업 실패 |
| ClassNotFoundException | 클래스 로드 실패 |
| FileNotFoundException | 파일을 찾을 수 없음 |

**예제**:
```java
// 컴파일 에러 발생 - 예외 처리 필수!
public void readFile(String path) {
    FileReader file = new FileReader(path);  // ❌ 컴파일 에러
}

// 방법 1: try-catch로 처리
public void readFile(String path) {
    try {
        FileReader file = new FileReader(path);  // ✅
        // 파일 읽기 작업
    } catch (FileNotFoundException e) {
        System.out.println("파일을 찾을 수 없습니다: " + e.getMessage());
    }
}

// 방법 2: throws로 호출자에게 전파
public void readFile(String path) throws FileNotFoundException {
    FileReader file = new FileReader(path);  // ✅
}
```

### 2. Unchecked Exception (RuntimeException)
컴파일 시점에 처리를 강제하지 않는 예외

**특징**:
- RuntimeException 클래스 상속
- 컴파일러가 처리 여부 확인 안 함
- 주로 프로그래밍 오류로 인한 예외

**주요 예외**:

| 예외 | 발생 상황 | 예방 방법 |
|------|----------|----------|
| NullPointerException | null 객체 참조 | null 체크 |
| ArrayIndexOutOfBoundsException | 배열 인덱스 초과 | 범위 검증 |
| IllegalArgumentException | 부적절한 인자 전달 | 인자 검증 |
| ArithmeticException | 0으로 나누기 등 | 나누기 전 검증 |
| ClassCastException | 잘못된 타입 캐스팅 | instanceof 확인 |
| NumberFormatException | 문자열→숫자 변환 실패 | 형식 검증 |

**예제**:
```java
// NullPointerException
String str = null;
System.out.println(str.length());  // ❌ NPE 발생

// 예방
if (str != null) {  // ✅ null 체크
    System.out.println(str.length());
}

// ArrayIndexOutOfBoundsException
int[] arr = {1, 2, 3};
System.out.println(arr[5]);  // ❌ 인덱스 초과

// 예방
if (index >= 0 && index < arr.length) {  // ✅ 범위 체크
    System.out.println(arr[index]);
}

// NumberFormatException
String num = "abc";
int value = Integer.parseInt(num);  // ❌ 변환 불가

// 예방
try {
    int value = Integer.parseInt(num);  // ✅ try-catch
} catch (NumberFormatException e) {
    System.out.println("숫자 형식이 아닙니다.");
}
```

## 예외 처리 방법

### 1. try-catch-finally
```java
try {
    // 예외 발생 가능 코드
    int result = 10 / 0;
    
} catch (ArithmeticException e) {
    // 특정 예외 처리
    System.out.println("0으로 나눌 수 없습니다.");
    
} catch (Exception e) {
    // 모든 예외 처리 (위에서 잡히지 않은 예외)
    System.out.println("예외 발생: " + e.getMessage());
    
} finally {
    // 항상 실행 (생략 가능)
    System.out.println("finally 블록 실행");
}
```

### 2. try-with-resources (Java 7+)
AutoCloseable 자원 자동 해제
```java
// 기존 방식 - 수동으로 close() 호출
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (br != null) {
        try {
            br.close();  // 번거로움
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// try-with-resources - 자동으로 close() 호출
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}
// 자동으로 br.close() 호출됨
```

### 3. throws 키워드
예외를 호출자에게 전파
```java
public void method1() throws IOException {
    method2();  // IOException 발생 가능
}

public void method2() throws IOException {
    throw new IOException("파일 읽기 실패");
}

// 사용
try {
    method1();
} catch (IOException e) {
    System.out.println(e.getMessage());
}
```

### 4. throw 키워드
직접 예외 발생
```java
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("나이는 0 이상이어야 합니다.");
    }
    this.age = age;
}
```

## 다중 catch 블록
```java
// Java 6 이전
try {
    // 코드
} catch (IOException e) {
    System.out.println("IO 예외");
} catch (SQLException e) {
    System.out.println("SQL 예외");
}

// Java 7+ - Multi-catch
try {
    // 코드
} catch (IOException | SQLException e) {
    System.out.println("예외 발생: " + e.getMessage());
}
```

## 예외 처리 모범 사례

### 1. 구체적인 예외 사용
```java
// 나쁜 예
catch (Exception e) {  // 너무 광범위
}

// 좋은 예
catch (FileNotFoundException e) {  // 구체적
    // 파일이 없을 때 대체 파일 사용
}
```

### 2. 예외 정보 로깅
```java
catch (SQLException e) {
    logger.error("DB 연결 실패: {}", e.getMessage(), e);
    // 스택 트레이스도 함께 기록
}
```

### 3. 리소스 자동 해제
```java
// try-with-resources 사용
try (Connection conn = DriverManager.getConnection(url);
     Statement stmt = conn.createStatement()) {
    // DB 작업
}
```

### 4. 예외 래핑 (Exception Wrapping)
저수준 예외를 고수준 예외로 변환
```java
public User getUser(Long id) {
    try {
        // JDBC 코드
        return user;
    } catch (SQLException e) {
        // 저수준 예외를 비즈니스 예외로 변환
        throw new UserNotFoundException("사용자를 찾을 수 없습니다: " + id, e);
    }
}
```

### 5. 빈 catch 블록 지양
```java
// 나쁜 예
catch (Exception e) {
    // 아무것도 안 함 - 문제 발생 시 추적 불가
}

// 좋은 예
catch (Exception e) {
    logger.error("예외 발생", e);
    // 또는 적절한 처리
}
```

## 트랜잭션과 예외
```java
@Transactional
public void updateUser(User user) {
    // Checked Exception - 롤백 안 됨 (기본)
    // Unchecked Exception - 자동 롤백
}

// Checked Exception도 롤백하려면
@Transactional(rollbackFor = Exception.class)
public void updateUser(User user) throws Exception {
    // 모든 예외에서 롤백
}
```

## Checked vs Unchecked 선택 기준

| 상황 | 권장 |
|------|------|
| 복구 가능한 상황 | Checked |
| 프로그래밍 오류 | Unchecked |
| 외부 시스템 연동 | Checked |
| 잘못된 인자/상태 | Unchecked |


## 참고 자료

- [Oracle Java Exception 가이드](https://docs.oracle.com/javase/tutorial/essential/exceptions/)
- [Effective Java - 예외 처리](https://www.baeldung.com/java-exceptions)
