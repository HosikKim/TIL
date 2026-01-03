# Java 다형성 (Polymorphism)

## 개념

하나의 객체가 여러 타입을 가질 수 있는 성질
- 상위 클래스 타입으로 하위 클래스 객체를 참조
- 같은 메서드 호출이지만 객체에 따라 다르게 동작

## 다형성의 기본 원리

### 클래스 계층 구조 예시
```java
public class Employee {
    private int id;
    private String name;
    private double salary;
    
    public double getAnnSalary() {
        return salary * 12;
    }
}

public class Manager extends Employee {
    private double bonus;
    
    public double getBonus() {
        return bonus;
    }
    
    @Override
    public double getAnnSalary() {
        return super.getAnnSalary() + bonus;
    }
}
```

### 다형성 활용
```java
// 방법 1: 다형성 활용 (업캐스팅)
Employee employee = new Manager(111, "김철수", 1000, 100);

// 방법 2: 일반 객체 생성
Manager manager = new Manager(101, "이영희", 5000, 500);
```

## 업캐스팅 (Upcasting)

하위 클래스 객체를 상위 클래스 타입으로 참조
```java
Employee employee = new Manager(111, "김철수", 1000, 100);
//   ↑ 상위 타입      ↑ 실제 객체는 Manager

// 자동으로 캐스팅됨 (명시적 형변환 불필요)
```

**특징**:
- 자동 형변환 (명시적 캐스팅 불필요)
- Employee에 정의된 멤버만 접근 가능
- 실제 실행되는 메서드는 Manager의 메서드 (오버라이딩된 경우)

## 접근 가능한 멤버
```java
Employee employee = new Manager(111, "김철수", 1000, 100);

// ✅ 가능: Employee에 정의된 메서드
System.out.println(employee.getAnnSalary());  // Manager의 getAnnSalary() 실행

// ❌ 불가능: Manager 고유 메서드
System.out.println(employee.getBonus());  // 컴파일 에러!
```

**왜 불가능한가?**
- 컴파일러는 선언된 타입(Employee)만 보기 때문
- Employee에는 getBonus() 메서드가 없음

## 다운캐스팅 (Downcasting)

하위 클래스 고유 메서드를 사용하려면 명시적 형변환 필요
```java
Employee employee = new Manager(111, "김철수", 1000, 100);

// 방법 1: 직접 캐스팅 (위험)
Manager manager = (Manager) employee;
System.out.println(manager.getBonus());  // ✅

// 방법 2: instanceof로 타입 체크 (안전)
if (employee instanceof Manager) {
    Manager manager = (Manager) employee;
    System.out.println(manager.getBonus());  // ✅
}

// 잘못된 캐스팅 예시
Employee employee2 = new Employee(222, "박민수", 2000);
Manager manager2 = (Manager) employee2;  // ❌ ClassCastException 발생!
```

## 메서드 오버라이딩과 다형성
```java
public class Main {
    public static void main(String[] args) {
        Employee emp1 = new Employee(1, "직원", 3000);
        Employee emp2 = new Manager(2, "매니저", 5000, 1000);
        
        // 같은 메서드 호출이지만 다른 결과
        System.out.println(emp1.getAnnSalary());  // 36000 (3000 * 12)
        System.out.println(emp2.getAnnSalary());  // 61000 (5000 * 12 + 1000)
        //                  ↑ Manager의 오버라이딩된 메서드 실행
    }
}
```

**핵심**: 
- 선언 타입은 Employee지만
- 실제 실행되는 메서드는 각 객체의 타입에 따라 결정됨

## 다형성의 실전 활용

### 1. 배열/컬렉션에서 활용
```java
// 다양한 타입의 직원을 하나의 배열로 관리
Employee[] employees = new Employee[3];
employees[0] = new Employee(1, "김직원", 3000);
employees[1] = new Manager(2, "이매니저", 5000, 1000);
employees[2] = new Engineer(3, "박엔지니어", 4000, "Java");

// 모든 직원의 연봉 계산 (각자의 방식으로)
for (Employee emp : employees) {
    System.out.println(emp.getAnnSalary());
}
```

### 2. 메서드 파라미터에서 활용
```java
public void printSalary(Employee employee) {
    System.out.println("연봉: " + employee.getAnnSalary());
}

// Employee, Manager, Engineer 모두 전달 가능
printSalary(new Employee(1, "직원", 3000));
printSalary(new Manager(2, "매니저", 5000, 1000));
printSalary(new Engineer(3, "엔지니어", 4000, "Java"));
```

### 3. 반환 타입에서 활용
```java
public Employee createEmployee(String type) {
    if (type.equals("manager")) {
        return new Manager(1, "매니저", 5000, 1000);
    } else if (type.equals("engineer")) {
        return new Engineer(2, "엔지니어", 4000, "Java");
    }
    return new Employee(3, "직원", 3000);
}

// 사용
Employee emp = createEmployee("manager");  // 실제로는 Manager 객체
```

## 업캐스팅 vs 일반 객체 생성 비교

| 특징 | Employee emp = new Manager() | Manager mgr = new Manager() |
|------|------------------------------|------------------------------|
| 접근 가능 멤버 | Employee 멤버만 | Manager 모든 멤버 |
| 다형성 활용 | O | X |
| 유연성 | 높음 (다양한 하위 클래스 할당) | 낮음 (Manager만 할당) |
| 다운캐스팅 필요 | 하위 클래스 메서드 사용 시 | 불필요 |
| 사용 상황 | 여러 타입을 동일하게 처리 | 특정 타입만 사용 |

## 다형성의 장단점

### 장점
- 코드 유연성: 하나의 타입으로 여러 객체 처리
- 확장성: 새로운 하위 클래스 추가 쉬움
- 유지보수: 변경 사항이 적음

### 단점
- 하위 클래스 고유 메서드 직접 호출 불가
- 다운캐스팅 필요 시 타입 체크 필수
- 런타임 오류 가능성 (잘못된 캐스팅)

## 안전한 다운캐스팅 패턴
```java
public void processEmployee(Employee employee) {
    // 공통 메서드 실행
    System.out.println("연봉: " + employee.getAnnSalary());
    
    // Manager인 경우에만 보너스 출력
    if (employee instanceof Manager) {
        Manager manager = (Manager) employee;
        System.out.println("보너스: " + manager.getBonus());
    }
    
    // Engineer인 경우에만 기술 스택 출력
    if (employee instanceof Engineer) {
        Engineer engineer = (Engineer) employee;
        System.out.println("기술: " + engineer.getTechStack());
    }
}
```

## Java 14+ Pattern Matching (참고)
```java
// Java 14+ (Preview Feature)
if (employee instanceof Manager manager) {
    // 자동으로 캐스팅된 manager 변수 사용 가능
    System.out.println(manager.getBonus());
}
```

## 정리

**다형성 사용하는 경우**:
- 여러 타입을 하나로 묶어 처리
- 메서드 파라미터로 다양한 타입 받기
- 확장 가능한 설계

**일반 객체 생성하는 경우**:
- 특정 타입의 고유 기능만 사용
- 타입이 명확하고 변경 가능성 없음

## 참고 자료

- [Oracle Java 다형성 가이드](https://docs.oracle.com/javase/tutorial/java/IandI/polymorphism.html)