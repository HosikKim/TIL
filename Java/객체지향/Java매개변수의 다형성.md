# Java 매개변수의 다형성

## 개념

메서드 파라미터를 부모 타입으로 선언하여 모든 자식 클래스 객체를 받을 수 있게 하는 것
```java
public void makeSound(Animal animal) {  // 부모 타입 파라미터
    animal.sound();
}

makeSound(new Dog());  // ✅ Dog는 Animal의 자식
makeSound(new Cat());  // ✅ Cat도 Animal의 자식
```

## 기본 예제
```java
class Animal {
    public void sound() {
        System.out.println("동물 소리");
    }
}

class Dog extends Animal {
    @Override
    public void sound() {
        System.out.println("멍멍");
    }
}

class Cat extends Animal {
    @Override
    public void sound() {
        System.out.println("야옹");
    }
}

// 하나의 메서드로 모든 동물 처리
public static void makeSound(Animal animal) {
    animal.sound();
}

// 사용
makeSound(new Dog());  // 멍멍
makeSound(new Cat());  // 야옹
```

## 실전 활용

### 예제 1: 직원 급여 처리
```java
public class PayrollSystem {
    public void processSalary(Employee employee) {
        double salary = employee.calculateSalary();
        System.out.println(employee.getName() + ": " + salary);
    }
}

// 사용
PayrollSystem payroll = new PayrollSystem();
payroll.processSalary(new Manager("김팀장", 5000, 1000));
payroll.processSalary(new Engineer("박개발", 4000, 200));
payroll.processSalary(new Intern("이인턴", 2000));
```

### 예제 2: 도형 넓이 계산
```java
public class ShapeCalculator {
    public void printArea(Shape shape) {
        System.out.println("넓이: " + shape.getArea());
    }
}

// 사용
ShapeCalculator calc = new ShapeCalculator();
calc.printArea(new Circle(5));        // 원
calc.printArea(new Rectangle(4, 6));   // 사각형
calc.printArea(new Triangle(3, 4));    // 삼각형
```

## 장점

**1. 유연성**: 하나의 메서드로 여러 타입 처리
```java
// 다형성 없이
public void printDogInfo(Dog dog) { }
public void printCatInfo(Cat cat) { }
public void printBirdInfo(Bird bird) { }

// 다형성 활용
public void printAnimalInfo(Animal animal) { }  // 하나로 통합
```

**2. 확장성**: 새로운 클래스 추가 시 기존 코드 수정 불필요
```java
// Bird 클래스 추가해도 makeSound() 메서드는 수정 안 함
class Bird extends Animal {
    @Override
    public void sound() {
        System.out.println("짹짹");
    }
}

makeSound(new Bird());  // ✅ 바로 사용 가능
```

**3. 재사용성**: 동일 로직을 여러 타입에 적용
```java
public void feedAnimal(Animal animal) {
    System.out.println(animal.getName() + "에게 먹이 주기");
    animal.eat();
}

// 모든 동물에게 재사용 가능
```

## 주의사항
```java
public void processAnimal(Animal animal) {
    animal.sound();  // ✅ Animal에 정의된 메서드
    
    // animal.bark();  // ❌ Dog 고유 메서드는 호출 불가
    
    // 하위 클래스 메서드 사용하려면 타입 체크 필요
    if (animal instanceof Dog) {
        Dog dog = (Dog) animal;
        dog.bark();  // ✅
    }
}
```

## 참고 자료

- [Oracle Java 다형성 가이드](https://docs.oracle.com/javase/tutorial/java/IandI/polymorphism.html)