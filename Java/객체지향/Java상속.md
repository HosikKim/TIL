# Java 상속

## 개념

기존 클래스의 필드와 메서드를 새로운 클래스가 재사용하는 것
```java
class Animal {           // 부모 클래스
    String name;
}

class Dog extends Animal {  // 자식 클래스
    // name 필드를 상속받음
}
```

**is-a 관계**: "Dog는 Animal이다" 관계가 성립할 때 상속 사용

## 기본 문법
```java
// 부모 클래스 (Super Class, Parent Class)
class Animal {
    protected String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    public void makeSound() {
        System.out.println("동물이 소리를 낸다");
    }
}

// 자식 클래스 (Sub Class, Child Class)
class Dog extends Animal {
    private String breed;
    
    public Dog(String name, String breed) {
        super(name);  // 부모 클래스 생성자 호출
        this.breed = breed;
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + "이(가) 멍멍 짖는다");
    }
    
    // Dog만의 고유 메서드
    public void fetch() {
        System.out.println(name + "이(가) 공을 가져온다");
    }
}
```

## super 키워드

### 1. 부모 클래스 생성자 호출
```java
class Dog extends Animal {
    public Dog(String name) {
        super(name);  // 부모 생성자 호출 (첫 줄에 위치)
    }
}
```

### 2. 부모 클래스 메서드 호출
```java
class Dog extends Animal {
    @Override
    public void makeSound() {
        super.makeSound();  // 부모 메서드 먼저 실행
        System.out.println("멍멍!");
    }
}
```

### 3. 부모 클래스 필드 접근
```java
class Dog extends Animal {
    public void printName() {
        System.out.println(super.name);  // 부모 필드 접근
    }
}
```

## 메서드 오버라이딩 (Method Overriding)

부모 클래스의 메서드를 자식 클래스에서 재정의
```java
class Animal {
    public void makeSound() {
        System.out.println("동물 소리");
    }
}

class Dog extends Animal {
    @Override  // 오버라이딩 명시 (권장)
    public void makeSound() {
        System.out.println("멍멍");  // 재정의
    }
}

// 사용
Animal animal = new Dog();
animal.makeSound();  // "멍멍" 출력 (Dog의 메서드 실행)
```

**오버라이딩 규칙**:
- 메서드 이름, 파라미터, 리턴 타입이 같아야 함
- 접근 제어자는 부모보다 넓거나 같아야 함 (public → protected ❌)
- `@Override` 어노테이션 사용 권장

## 실전 예제

### 예제 1: 직원 관리 시스템
```java
class Employee {
    protected String name;
    protected double salary;
    
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
    
    public double calculateSalary() {
        return salary;
    }
}

class Manager extends Employee {
    private double bonus;
    
    public Manager(String name, double salary, double bonus) {
        super(name, salary);
        this.bonus = bonus;
    }
    
    @Override
    public double calculateSalary() {
        return salary + bonus;  // 보너스 포함
    }
}

class Engineer extends Employee {
    private double overtimePay;
    
    public Engineer(String name, double salary, double overtimePay) {
        super(name, salary);
        this.overtimePay = overtimePay;
    }
    
    @Override
    public double calculateSalary() {
        return salary + overtimePay;  // 야근 수당 포함
    }
}

// 사용
Employee[] employees = {
    new Employee("김사원", 3000),
    new Manager("박팀장", 5000, 1000),
    new Engineer("이개발", 4000, 500)
};

for (Employee emp : employees) {
    System.out.println(emp.name + ": " + emp.calculateSalary());
}
```

### 예제 2: 도형 계산
```java
class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    public double getArea() {
        return 0;
    }
}

class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
}

class Rectangle extends Shape {
    private double width;
    private double height;
    
    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double getArea() {
        return width * height;
    }
}
```

## 상속의 특징

### 1. 단일 상속만 가능
```java
class A {}
class B {}

class C extends A, B {}  // ❌ 다중 상속 불가
class C extends A {}     // ✅ 단일 상속만 가능
```

### 2. 모든 클래스는 Object 상속
```java
class MyClass {  
    // 자동으로 extends Object
}

// 따라서 Object의 메서드 사용 가능
MyClass obj = new MyClass();
obj.toString();
obj.equals(null);
obj.hashCode();
```

### 3. protected 접근 제어자
```java
class Parent {
    public String publicField;      // 모든 곳에서 접근 가능
    protected String protectedField; // 같은 패키지 + 자식 클래스
    private String privateField;    // 같은 클래스만
}

class Child extends Parent {
    public void test() {
        System.out.println(publicField);    // ✅
        System.out.println(protectedField); // ✅
        System.out.println(privateField);   // ❌
    }
}
```

## final 키워드로 상속 제한

### 1. 클래스에 final
```java
final class FinalClass {
    // 내용
}

class ChildClass extends FinalClass {}  // ❌ 상속 불가
```

### 2. 메서드에 final
```java
class Parent {
    public final void finalMethod() {
        System.out.println("변경 불가");
    }
}

class Child extends Parent {
    @Override
    public void finalMethod() {}  // ❌ 오버라이딩 불가
}
```

## 장점

**1. 코드 재사용**
```java
// 공통 코드를 부모에 한 번만 작성
class Animal {
    public void eat() { /* 공통 기능 */ }
}

// 여러 자식이 재사용
class Dog extends Animal {}
class Cat extends Animal {}
```

**2. 계층 구조**
```java
Animal
  ├── Dog
  │   ├── Bulldog
  │   └── Poodle
  └── Cat
      ├── Persian
      └── Siamese
```

**3. 다형성 기반**
```java
Animal animal = new Dog();  // 상속 덕분에 가능
```

## 주의사항

### 1. is-a 관계 확인
```java
// ✅ 좋은 예: Car는 Vehicle이다
class Vehicle {}
class Car extends Vehicle {}

// ❌ 나쁜 예: Employee는 Company가 아니다
class Company {}
class Employee extends Company {}  // 잘못된 설계
```

### 2. 상속보다 컴포지션 고려
```java
// 상속 (강한 결합)
class Engine {}
class Car extends Engine {}  // ❌ Car는 Engine이 아니다

// 컴포지션 (약한 결합) - 권장
class Engine {}
class Car {
    private Engine engine;  // ✅ Car는 Engine을 가진다
}
```

### 3. 캡슐화 유지
```java
class Parent {
    private int secretData;  // 자식에게도 숨김
    
    protected int getSecretData() {  // 메서드로 제공
        return secretData;
    }
}
```

## 상속 vs 인터페이스

| 구분 | 상속 | 인터페이스 |
|------|------|-----------|
| 관계 | is-a | can-do |
| 개수 | 단일 상속 | 다중 구현 |
| 구현 | 메서드 구현 포함 | 메서드 시그니처만 |
| 사용 | 공통 기능 재사용 | 규격 정의 |

## 참고 자료

- [Oracle Java 상속 가이드](https://docs.oracle.com/javase/tutorial/java/IandI/subclasses.html)