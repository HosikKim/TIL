# Java 타입 캐스팅 & instanceof

## 타입 캐스팅 개념

한 데이터 타입을 다른 타입으로 변환하는 것

## 기본형 타입 캐스팅

### 1. 자동 타입 변환 (Promotion, 묵시적)

작은 타입 → 큰 타입으로 자동 변환
```java
// 변환 순서 (작음 → 큼)
byte → short → int → long → float → double
       char  →

// 예제
byte b = 10;
int i = b;        // 자동 변환
long l = i;       // 자동 변환
double d = l;     // 자동 변환

// 연산 시 자동 변환
int num1 = 10;
double num2 = 5.5;
double result = num1 + num2;  // num1이 double로 자동 변환
```

### 2. 강제 타입 변환 (Casting, 명시적)

큰 타입 → 작은 타입으로 명시적 변환 (데이터 손실 가능)
```java
// 변환 순서 (큼 → 작음)
double → float → long → int → short → byte
                              → char

// 예제
double d = 3.14;
int i = (int) d;        // 3 (소수점 버림)

long l = 1000L;
int i2 = (int) l;       // 1000

int i3 = 300;
byte b = (byte) i3;     // 44 (오버플로우 발생!)
```

**데이터 손실 예시**:
```java
// 소수점 손실
double pi = 3.14159;
int intPi = (int) pi;   // 3 (0.14159 손실)

// 오버플로우
int bigNum = 130;
byte smallNum = (byte) bigNum;  // -126 (범위 초과)
// byte 범위: -128 ~ 127

// 값 범위 확인 후 변환
int value = 100;
if (value >= Byte.MIN_VALUE && value <= Byte.MAX_VALUE) {
    byte b = (byte) value;  // 안전
}
```

### 연산 시 자동 변환
```java
// 정수 연산
int a = 10;
int b = 3;
double result1 = a / b;        // 3.0 (int / int = int)
double result2 = (double) a / b;  // 3.333... (double / int = double)

// 문자열 연산
int num = 10;
String str = "숫자: " + num;  // "숫자: 10" (int가 String으로 변환)

// char 연산
char ch = 'A';
int code = ch;        // 65 (자동 변환)
int result = ch + 1;  // 66 ('B'의 코드값)
```

## 참조형 타입 캐스팅

### 1. 업캐스팅 (Upcasting)

자식 → 부모 타입으로 자동 변환
```java
class Animal {
    public void eat() {
        System.out.println("먹는다");
    }
}

class Dog extends Animal {
    public void bark() {
        System.out.println("짖는다");
    }
}

// 업캐스팅 (자동)
Dog dog = new Dog();
Animal animal = dog;  // 자동 변환

animal.eat();   // ✅ 가능
// animal.bark();  // ❌ 불가능 (Animal에 bark() 없음)
```

### 2. 다운캐스팅 (Downcasting)

부모 → 자식 타입으로 명시적 변환 (위험!)
```java
Animal animal = new Dog();  // 업캐스팅

// 다운캐스팅 (명시적)
Dog dog = (Dog) animal;  // ✅ 가능 (원래 Dog였음)
dog.bark();

// 잘못된 다운캐스팅
Animal cat = new Cat();
Dog dog2 = (Dog) cat;    // ❌ ClassCastException 발생!
```

## instanceof 연산자

객체가 특정 클래스의 인스턴스인지 확인

### 기본 사용법
```java
class Animal {}
class Dog extends Animal {}
class Cat extends Animal {}

Animal animal = new Dog();

// instanceof 사용
if (animal instanceof Dog) {
    System.out.println("Dog입니다");  // 출력
}

if (animal instanceof Cat) {
    System.out.println("Cat입니다");  // 출력 안 됨
}

if (animal instanceof Animal) {
    System.out.println("Animal입니다");  // 출력 (상위 클래스도 true)
}
```

### 안전한 다운캐스팅
```java
public void processAnimal(Animal animal) {
    // instanceof로 타입 확인 후 다운캐스팅
    if (animal instanceof Dog) {
        Dog dog = (Dog) animal;
        dog.bark();
    } else if (animal instanceof Cat) {
        Cat cat = (Cat) animal;
        cat.meow();
    }
}
```

### null 체크
```java
Animal animal = null;

if (animal instanceof Dog) {
    // 실행 안 됨 (null은 항상 false)
}

// null 체크 불필요
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;  // animal이 null이면 여기 도달 안 함
}
```

## instanceof 사용을 피하는 방법

### 1. 다형성 활용 (권장)
```java
// ❌ instanceof 사용
public void processShape(Shape shape) {
    if (shape instanceof Circle) {
        Circle circle = (Circle) shape;
        System.out.println("원 넓이: " + Math.PI * circle.getRadius() * circle.getRadius());
    } else if (shape instanceof Rectangle) {
        Rectangle rect = (Rectangle) shape;
        System.out.println("사각형 넓이: " + rect.getWidth() * rect.getHeight());
    }
}

// ✅ 다형성 활용
public void processShape(Shape shape) {
    System.out.println("넓이: " + shape.getArea());  // 각 클래스의 메서드 실행
}
```

### 2. 전략 패턴
```java
interface PaymentStrategy {
    void pay(int amount);
}

class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("신용카드 결제: " + amount);
    }
}

class KakaoPayPayment implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("카카오페이 결제: " + amount);
    }
}

// instanceof 불필요
class PaymentProcessor {
    private PaymentStrategy strategy;
    
    public void setStrategy(PaymentStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void process(int amount) {
        strategy.pay(amount);  // 타입 확인 없이 실행
    }
}
```

### 3. Enum 활용
```java
enum ShapeType {
    CIRCLE {
        @Override
        public double calculateArea(double... params) {
            double radius = params[0];
            return Math.PI * radius * radius;
        }
    },
    RECTANGLE {
        @Override
        public double calculateArea(double... params) {
            double width = params[0];
            double height = params[1];
            return width * height;
        }
    };
    
    public abstract double calculateArea(double... params);
}

// 사용
ShapeType type = ShapeType.CIRCLE;
double area = type.calculateArea(5);  // instanceof 불필요
```

## instanceof가 적절한 경우

### 1. equals() 메서드
```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (!(obj instanceof User)) return false;  // 타입 확인 필수
    
    User user = (User) obj;
    return id.equals(user.id);
}
```

### 2. 외부 라이브러리 객체 처리
```java
public void handleException(Exception e) {
    if (e instanceof IOException) {
        // IO 예외 처리
    } else if (e instanceof SQLException) {
        // SQL 예외 처리
    }
}
```

### 3. Java 14+ Pattern Matching
```java
// Java 14+
if (obj instanceof String str) {
    // 자동으로 String str로 캐스팅됨
    System.out.println(str.length());
}

// Java 17+ (switch)
String formatted = switch (obj) {
    case Integer i -> String.format("int %d", i);
    case String s -> String.format("String %s", s);
    default -> obj.toString();
};
```

## 주의사항

**1. ClassCastException 방지**
```java
Object obj = "Hello";
Integer num = (Integer) obj;  // ❌ ClassCastException!

// 안전한 방법
if (obj instanceof Integer) {
    Integer num = (Integer) obj;  // ✅
}
```

**2. null 체크**
```java
Object obj = null;
if (obj instanceof String) {  // false (NullPointerException 안 남)
    String str = (String) obj;
}
```

**3. 과도한 instanceof 사용 지양**
```java
// ❌ 나쁜 예
if (animal instanceof Dog) {
    ((Dog) animal).bark();
} else if (animal instanceof Cat) {
    ((Cat) animal).meow();
} else if (animal instanceof Bird) {
    ((Bird) animal).fly();
}

// ✅ 좋은 예
animal.makeSound();  // 다형성 활용
```

## 정리

**타입 캐스팅 사용 시**:
- 기본형: 데이터 손실 주의
- 참조형: instanceof로 안전하게 확인

**instanceof 사용**:
- 최소한으로 사용
- 다형성으로 대체 가능하면 대체
- 불가피한 경우에만 사용

## 참고 자료

- [Oracle 타입 변환 가이드](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)
- [Pattern Matching (Java 14+)](https://openjdk.org/jeps/305)