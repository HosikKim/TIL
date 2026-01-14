# Java 추상화 (Abstraction)

## 개념

공통된 특성을 추출하고 불필요한 세부사항을 숨기는 것
- 무엇을 할지 정의 (구현은 하위 클래스에서)
- 공통 기능을 강제하여 일관성 유지
```java
abstract class Animal {
    public abstract void makeSound();  // 선언만 (what)
}

class Dog extends Animal {
    public void makeSound() {
        System.out.println("멍멍");  // 구현 (how)
    }
}
```

## 추상 클래스 (Abstract Class)

### 특징
- `abstract` 키워드로 선언
- 추상 메서드를 0개 이상 포함
- 일반 메서드, 필드, 생성자도 가질 수 있음
- **직접 객체 생성 불가**
- 상속받은 클래스는 모든 추상 메서드 구현 필수

### 기본 문법
```java
abstract class Animal {
    protected String name;
    
    // 생성자
    public Animal(String name) {
        this.name = name;
    }
    
    // 추상 메서드 (구현 없음)
    public abstract void makeSound();
    public abstract void move();
    
    // 일반 메서드 (구현 있음)
    public void sleep() {
        System.out.println(name + "이(가) 잔다");
    }
}

class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
    
    // 추상 메서드 구현 필수
    @Override
    public void makeSound() {
        System.out.println(name + "이(가) 멍멍 짖는다");
    }
    
    @Override
    public void move() {
        System.out.println(name + "이(가) 네 발로 뛴다");
    }
}

// 사용
Animal dog = new Dog("뽀삐");
dog.makeSound();  // 뽀삐이(가) 멍멍 짖는다
dog.move();       // 뽀삐이(가) 네 발로 뛴다
dog.sleep();      // 뽀삐이(가) 잔다

// Animal animal = new Animal("동물");  // ❌ 추상 클래스는 인스턴스화 불가
```


## 추상 클래스 vs 인터페이스

| 구분 | 추상 클래스 | 인터페이스 |
|------|------------|-----------|
| 키워드 | `abstract class` | `interface` |
| 상속/구현 | extends (단일) | implements (다중) |
| 메서드 | 추상 + 일반 메서드 | 추상 메서드 (default, static 제외) |
| 변수 | 모든 종류 가능 | public static final만 |
| 생성자 | 가능 | 불가능 |
| 접근 제어자 | 모두 사용 가능 | public만 (암묵적) |
| 사용 목적 | is-a (공통 특성) | can-do (기능 정의) |
```java
// 추상 클래스
abstract class Vehicle {
    protected String name;
    
    public Vehicle(String name) {  // 생성자 가능
        this.name = name;
    }
    
    public abstract void move();
    
    public void stop() {  // 일반 메서드
        System.out.println("정지");
    }
}

// 인터페이스
interface Flyable {
    // public static final 생략 가능
    int MAX_HEIGHT = 10000;
    
    // public abstract 생략 가능
    void fly();
    
    // Java 8+: default 메서드
    default void land() {
        System.out.println("착륙");
    }
}

// 둘 다 사용
class Airplane extends Vehicle implements Flyable {
    public Airplane(String name) {
        super(name);
    }
    
    @Override
    public void move() {
        System.out.println("비행기 이동");
    }
    
    @Override
    public void fly() {
        System.out.println(name + " 비행 중");
    }
}
```

## 언제 추상 클래스를 사용하는가?

### 추상 클래스 사용
```java
// ✅ 공통 필드와 메서드가 많을 때
abstract class Employee {
    protected String name;
    protected double salary;
    
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
    
    // 공통 구현
    public void printInfo() {
        System.out.println("이름: " + name);
        System.out.println("급여: " + calculateSalary());
    }
    
    // 하위 클래스에서 구현
    public abstract double calculateSalary();
}
```

### 인터페이스 사용
```java
// ✅ 다중 구현이 필요할 때
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

class MultiFunctionPrinter implements Printable, Scannable {
    public void print() { /* 구현 */ }
    public void scan() { /* 구현 */ }
}
```

## 주의사항

### 1. 모든 추상 메서드 구현 필수
```java
abstract class Animal {
    public abstract void eat();
    public abstract void sleep();
}

class Dog extends Animal {
    @Override
    public void eat() {
        System.out.println("먹는다");
    }
    // sleep() 구현 안 하면 컴파일 에러!
}
```

### 2. 추상 클래스도 추상 클래스 상속 가능
```java
abstract class Animal {
    public abstract void move();
}

abstract class Mammal extends Animal {
    public abstract void feedMilk();  // 새로운 추상 메서드 추가
    // move()는 여전히 구현 안 해도 됨
}

class Dog extends Mammal {
    @Override
    public void move() { /* 구현 */ }
    
    @Override
    public void feedMilk() { /* 구현 */ }
}
```

### 3. 인스턴스화 불가 확인
```java
abstract class Animal {
    public abstract void makeSound();
}

// ❌ 불가능
Animal animal = new Animal() {
    // 이건 익명 클래스 (다른 개념)
};

// ✅ 가능
Animal dog = new Dog();  // Dog는 Animal을 상속받은 구체 클래스
```

## 정리

**추상 클래스 사용 이유**:
1. 공통 기능 강제 (일관성)
2. 코드 재사용 (상속)
3. 확장 가능한 설계

**추상화의 핵심**:
- "무엇을" 할지 정의
- "어떻게" 할지는 하위 클래스에 위임

## 참고 자료

- [Oracle Java 추상 클래스 가이드](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)