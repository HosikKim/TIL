# Java var (타입 추론)

## 개념
컴파일러가 변수의 초기화 값을 보고 자동으로 타입을 결정하는 타입 추론 키워드

## 목적
* 코드 작성 시 타입 선언을 간소화
* 반복적인 타입 명시를 줄여 가독성 향상
* 제네릭 타입 등 긴 타입 선언을 간결하게 표현

## 기본 문법

### 기존 방식 (명시적 타입)
```java
GameRunner gameRunner = new GameRunner(marioGame);
List<String> names = List.of("김호식", "김철식", "김상식");
Map<String, Integer> scores = new HashMap<>();
```

### var 사용 (타입 추론)
```java
var gameRunner = new GameRunner(marioGame);  // GameRunner 타입으로 추론
var names = List.of("Alice", "Bob", "Charlie");  // List<String> 타입으로 추론
var scores = new HashMap<String, Integer>();  // Map<String, Integer> 타입으로 추론
```

## 타입 추론 원리

컴파일러가 **우변(오른쪽) 할당 값을 보고 타입을 결정**
```java
var number = 10;          // int로 추론
var text = "Hello";       // String으로 추론
var list = new ArrayList<String>();  // ArrayList<String>으로 추론
```

컴파일 시점에 타입이 결정되므로 **런타임 성능에는 영향 없음**

## 사용 가능/불가능 케이스

### ✅ 사용 가능
```java
// 지역 변수 (초기화와 함께)
var name = "John";
var age = 25;
var list = new ArrayList<String>();

// for 문
for (var item : list) {
    System.out.println(item);
}

// try-with-resources
try (var scanner = new Scanner(System.in)) {
    // ...
}
```

### ❌ 사용 불가능
```java
// 1. 초기화 없이 선언
var x;  // 컴파일 에러

// 2. null로 초기화
var data = null;  // 컴파일 에러

// 3. 클래스 필드
class MyClass {
    var field = 10;  // 컴파일 에러
}

// 4. 메서드 파라미터
public void method(var param) {  // 컴파일 에러
    // ...
}

// 5. 메서드 리턴 타입
public var getNumber() {  // 컴파일 에러
    return 10;
}
```

## 사용 가능 범위 요약

| 위치 | 사용 가능 여부 |
|------|---------------|
| 지역 변수 (초기화 필수) | ✅ 가능 |
| for/while 루프 변수 | ✅ 가능 |
| try-with-resources | ✅ 가능 |
| 클래스 필드 | ❌ 불가능 |
| 메서드 파라미터 | ❌ 불가능 |
| 메서드 리턴 타입 | ❌ 불가능 |
| 람다 표현식 파라미터 | ❌ 불가능 (Java 10) / ✅ 가능 (Java 11+) |

## 장점
* **코드 간결성**: 타입 선언이 길어질 때 효과적
* **편리함**: 제네릭 같은 복잡한 타입을 간단히 표현
* **리팩토링 용이**: 타입 변경 시 var 부분은 수정 불필요

```java
// Before
Map<String, List<Map<String, Object>>> data = new HashMap<>();

// After
var data = new HashMap<String, List<Map<String, Object>>>();
```

## 단점 및 주의사항

### 1. 가독성 저하 가능
```java
// 타입이 명확하지 않음
var result = calculate();  // 무슨 타입인지 알 수 없음

// 명시적 타입이 더 나음
CalculationResult result = calculate();
```

### 2. 원하지 않는 타입으로 추론될 수 있음
```java
var list = new ArrayList<>();  // ArrayList<Object>로 추론 (제네릭 미지정)
list.add("String");
list.add(123);  // 혼란 발생

// 올바른 사용
var list = new ArrayList<String>();  // ArrayList<String>으로 추론
```

### 3. 예상과 다른 타입 추론
```java
var number = 10;     // int로 추론 (long이 아님!)
var decimal = 3.14;  // double로 추론 (float이 아님!)

// 명시적으로 타입 지정 필요
var longNumber = 10L;      // long
var floatDecimal = 3.14f;  // float
```

### 4. 인터페이스 타입 손실
```java
var list = new ArrayList<String>();  // ArrayList로 추론
// List 인터페이스가 아닌 ArrayList 구체 타입에 의존

// 명시적 타입이 더 나은 경우
List<String> list = new ArrayList<>();  // 인터페이스 타입 유지
```

## 사용 가이드

### var를 사용하면 좋은 경우
* 타입이 우변에서 **명확하게 드러날 때**
  ```java
  var list = new ArrayList<String>();
  var file = new File("data.txt");
  ```

* 타입 선언이 **매우 길 때**
  ```java
  var map = new HashMap<String, List<Integer>>();
  ```

* **지역 변수**이고 스코프가 좁을 때
  ```java
  for (var entry : map.entrySet()) {
      // ...
  }
  ```

### var를 사용하지 말아야 하는 경우
* 타입이 **불명확할 때**
  ```java
  var data = getData();  // 무슨 타입? ❌
  ```

* **리터럴 값**만 할당할 때
  ```java
  var number = 10;  // int가 명확하지만 var는 불필요
  int number = 10;  // 더 나음
  ```

* **인터페이스 타입**이 중요할 때
  ```java
  List<String> list = new ArrayList<>();  // List 타입 유지 ✅
  var list = new ArrayList<String>();     // ArrayList로 고정 ⚠️
  ```

* **다형성**을 활용할 때
  ```java
  Animal animal = new Dog();  // Animal 타입으로 참조 ✅
  var animal = new Dog();     // Dog 타입으로 고정 ⚠️
  ```



## 버전 정보
* **Java 10** (2018년 3월)부터 도입
* Java 11+에서 람다 파라미터에도 사용 가능

## 핵심 정리
* var는 **지역 변수에만** 사용 가능
* **반드시 초기화**와 함께 선언해야 함
* 컴파일 시점에 타입이 결정되므로 **런타임 성능은 동일**
* **가독성**과 **타입 안정성** 사이의 균형을 고려해서 사용
* 타입이 불명확하거나 인터페이스 타입이 중요할 때는 **명시적 타입 선언** 권장

## 참고 자료
* Java 10 Release Notes