# Java 커스텀 예외 처리

## 개념

> "표준 예외로는 부족할 때, 우리만의 예외를 만든다"

애플리케이션 고유의 예외 상황을 표현하기 위해 직접 만드는 예외 클래스

```java
// 표준 예외로는 의미 불명확
throw new Exception("잔액 부족");

// 커스텀 예외로 의미 명확
throw new InsufficientFundsException(amount);
```

## 커스텀 예외 생성 방법

### 1. Checked Exception (Exception 상속)
```java
public class InsufficientFundsException extends Exception {
    private double amount;  // 추가 정보
    
    // 기본 생성자
    public InsufficientFundsException() {
        super("잔액이 부족합니다.");
    }
    
    // 메시지 생성자
    public InsufficientFundsException(String message) {
        super(message);
    }
    
    // 추가 정보 포함 생성자
    public InsufficientFundsException(double amount) {
        super("잔액 부족: " + amount + "원이 부족합니다.");
        this.amount = amount;
    }
    
    // Getter
    public double getAmount() {
        return amount;
    }
}
```

### 2. Unchecked Exception (RuntimeException 상속)
```java
public class InvalidAgeException extends RuntimeException {
    private int age;
    
    public InvalidAgeException(int age) {
        super("유효하지 않은 나이: " + age);
        this.age = age;
    }
    
    public int getAge() {
        return age;
    }
}
```

## 실전 예제


### 예제 : 회원 가입 시스템
```java
// 커스텀 예외들
class DuplicateUserException extends Exception {
    private String userId;
    
    public DuplicateUserException(String userId) {
        super("이미 존재하는 사용자입니다: " + userId);
        this.userId = userId;
    }
    
    public String getUserId() {
        return userId;
    }
}

class InvalidPasswordException extends RuntimeException {
    public InvalidPasswordException(String reason) {
        super("유효하지 않은 비밀번호: " + reason);
    }
}

class InvalidEmailException extends RuntimeException {
    public InvalidEmailException(String email) {
        super("유효하지 않은 이메일 형식: " + email);
    }
}

// 사용
class UserService {
    private List<String> userIds = new ArrayList<>();
    
    public void register(String userId, String password, String email) 
            throws DuplicateUserException {
        
        // 비밀번호 검증 (Unchecked - 프로그래밍 오류)
        validatePassword(password);
        
        // 이메일 검증 (Unchecked)
        validateEmail(email);
        
        // 중복 검사 (Checked - 비즈니스 오류)
        if (userIds.contains(userId)) {
            throw new DuplicateUserException(userId);
        }
        
        userIds.add(userId);
        System.out.println("회원가입 완료: " + userId);
    }
    
    private void validatePassword(String password) {
        if (password.length() < 8) {
            throw new InvalidPasswordException("8자 이상이어야 합니다");
        }
        if (!password.matches(".*[A-Z].*")) {
            throw new InvalidPasswordException("대문자를 포함해야 합니다");
        }
    }
    
    private void validateEmail(String email) {
        if (!email.contains("@")) {
            throw new InvalidEmailException(email);
        }
    }
}

// 메인
public class UserExample {
    public static void main(String[] args) {
        UserService service = new UserService();
        
        try {
            service.register("user1", "Password123", "user1@example.com");
            service.register("user1", "Password456", "user2@example.com");  // 중복
        } catch (DuplicateUserException e) {
            System.out.println(e.getMessage());
        }
        
        try {
            service.register("user2", "pass", "user2@example.com");  // 비밀번호 짧음
        } catch (InvalidPasswordException e) {
            System.out.println(e.getMessage());
        }
    }
}
```


## Checked vs Unchecked 선택 기준

| 상황 | 타입 | 이유 |
|------|------|------|
| 비즈니스 로직상 예상 가능한 오류 | Checked | 호출자가 처리 강제 |
| 프로그래밍 오류 | Unchecked | 수정해야 할 버그 |
| 복구 가능한 오류 | Checked | try-catch로 대응 |
| 복구 불가능한 오류 | Unchecked | 프로그램 종료 |
```java
// Checked - 비즈니스 로직 오류
class OrderNotFoundException extends Exception {
    // 주문을 찾지 못함 → 다른 주문 조회 등 복구 가능
}

// Unchecked - 프로그래밍 오류
class InvalidOrderStatusException extends RuntimeException {
    // 잘못된 상태값 → 코드 수정 필요
}
```

## 예외 계층 구조
```java
// 최상위 예외
abstract class PaymentException extends Exception {
    protected String orderId;
    
    public PaymentException(String orderId, String message) {
        super(message);
        this.orderId = orderId;
    }
    
    public String getOrderId() {
        return orderId;
    }
}

// 하위 예외들
class PaymentFailedException extends PaymentException {
    public PaymentFailedException(String orderId, String reason) {
        super(orderId, "결제 실패: " + reason);
    }
}

class PaymentCanceledException extends PaymentException {
    public PaymentCanceledException(String orderId) {
        super(orderId, "결제 취소됨");
    }
}

// 사용 - 상위 예외로 한 번에 처리 가능
try {
    processPayment(orderId);
} catch (PaymentException e) {  // 모든 결제 예외 처리
    System.out.println("주문번호: " + e.getOrderId());
    System.out.println("에러: " + e.getMessage());
}
```

## 모범 사례

### 1. 의미 있는 이름
```java
// ❌ 나쁜 예
class MyException extends Exception {}

// ✅ 좋은 예
class UserNotFoundException extends Exception {}
class InvalidEmailFormatException extends RuntimeException {}
```

### 2. 충분한 정보 제공
```java
// ❌ 나쁜 예
class OrderException extends Exception {
    public OrderException() {
        super("주문 오류");
    }
}

// ✅ 좋은 예
class OrderException extends Exception {
    private String orderId;
    private String errorCode;
    private LocalDateTime timestamp;
    
    public OrderException(String orderId, String errorCode, String message) {
        super(String.format("[%s] 주문 %s: %s", 
            errorCode, orderId, message));
        this.orderId = orderId;
        this.errorCode = errorCode;
        this.timestamp = LocalDateTime.now();
    }
    
    // Getters...
}
```

### 3. 기존 예외 활용 고려
```java
// 기존 예외로 충분한 경우
throw new IllegalArgumentException("나이는 0 이상이어야 합니다");
throw new IllegalStateException("이미 처리된 주문입니다");

// 커스텀 예외가 필요한 경우
throw new InsufficientFundsException(shortage);  // 추가 정보 필요
```

## 정리

**커스텀 예외를 만드는 경우**:
- 기존 예외로 의미 전달 부족
- 추가 정보 필요
- 예외 계층 구조 필요
- 비즈니스 로직 명확히 표현

**만들지 않는 경우**:
- 기존 예외로 충분
- 단순한 검증 오류
- 범용적인 오류

## 참고 자료

- [Oracle 예외 처리 가이드](https://docs.oracle.com/javase/tutorial/essential/exceptions/)
- [Effective Java - 예외](https://www.baeldung.com/java-exceptions)