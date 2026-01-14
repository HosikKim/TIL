# Java 메모리 구조

## 개념

JVM이 프로그램을 실행하기 위해 OS로부터 할당받은 메모리 공간

## 메모리 영역 구조
```
┌─────────────────┐
│   Method Area   │ ← 클래스 정보, static 변수
├─────────────────┤
│   Heap 영역     │ ← 객체, 배열 (new로 생성)
├─────────────────┤
│   Stack 영역    │ ← 지역 변수, 메서드 호출
└─────────────────┘
```

## 주요 영역

### 1. Method Area (메서드 영역)
**저장 내용**:
- 클래스 정보 (필드, 메서드, 생성자)
- static 변수
- 상수 풀 (Constant Pool)

**특징**:
- 프로그램 시작 시 생성, 종료 시 소멸
- 모든 스레드가 공유

### 2. Stack 영역
**저장 내용**:
- 지역 변수 (primitive 타입 값, 참조 타입 주소)
- 메서드 매개변수
- 메서드 호출 정보

**특징**:
- 메서드 호출 시 생성, 종료 시 자동 소멸
- LIFO (후입선출) 구조
- 스레드마다 독립적으로 생성
- 컴파일 타임에 크기 결정
```java
public void method() {
    int a = 10;      // Stack에 저장
    String str;      // Stack에 참조 변수만 저장
}
```

### 3. Heap 영역
**저장 내용**:
- new로 생성한 객체
- 배열
- 인스턴스 변수

**특징**:
- 프로그램 실행 중 동적으로 할당
- 가비지 컬렉터(GC)가 자동으로 메모리 관리
- 모든 스레드가 공유
- 런타임에 크기 결정
```java
String str = new String("Hello");  // Heap에 객체 생성
int[] arr = new int[5];            // Heap에 배열 생성
```

## 메모리 할당 예시

### 기본형 변수
```java
int a = 100;
int b = 200;
int result = a + b;
```

**메모리 상태**:
```
Stack:
┌──────────┐
│ a = 100  │
│ b = 200  │
│ result = 300 │
└──────────┘
```

### String 리터럴
```java
String str = "Hello";
String str2 = "Hello";
```

**메모리 상태**:
```
Stack:                Heap (String Pool):
┌──────────┐         ┌──────────────┐
│ str  ───────────→  │  "Hello"     │
│ str2 ───────────→  │              │
└──────────┘         └──────────────┘
                     (같은 객체 참조)
```

### new로 생성한 String
```java
String str1 = "Hello";           // String Pool
String str2 = new String("Hello"); // Heap
```

**메모리 상태**:
```
Stack:                Heap:
┌──────────┐         ┌──────────────┐
│ str1 ─────────┐    │ String Pool: │
│ str2 ────────┐│    │  "Hello"     │
└──────────┘  ││    ├──────────────┤
              ││    │ new String:  │
              │└────→  "Hello"     │
              └─────→              │
                     └──────────────┘
```

### 배열
```java
int[] a = new int[3];
a[0] = 10;
a[1] = 20;
a[2] = 30;
```

**메모리 상태**:
```
Stack:                Heap:
┌──────────┐         ┌──────────────┐
│ a ──────────────→  │ [0] = 10     │
└──────────┘         │ [1] = 20     │
                     │ [2] = 30     │
                     └──────────────┘
```

### 참조형 배열
```java
String[] s = new String[3];
s[0] = "A";
s[1] = "B";
s[2] = "A";
```

**메모리 상태**:
```
Stack:                Heap:
┌──────────┐         배열 객체:      String Pool:
│ s ──────────────→  ┌─────┐        ┌─────┐
└──────────┘         │ [0] ────────→ "A"  │
                     │ [1] ────────→ "B"  │
                     │ [2] ────────→ "A"  │
                     └─────┘        └─────┘
                                   (s[0]과 s[2]는 같은 "A" 참조)
```

### 2차원 배열
```java
int[][] a = new int[2][3];
```

**메모리 상태**:
```
Stack:                Heap:
┌──────────┐         ┌──────────────┐
│ a ──────────────→  │ [0] ─┐       │
└──────────┘         │ [1] ─┼──┐    │
                     └──────┼──┼────┘
                            │  │
                            ↓  ↓
                     ┌──────────────┐
                     │ [0][0-2]     │
                     │ [1][0-2]     │
                     └──────────────┘
```

## Stack vs Heap 비교

| 구분 | Stack | Heap |
|------|-------|------|
| 저장 데이터 | 지역 변수, 메서드 정보 | 객체, 배열 |
| 생명 주기 | 메서드 종료 시 자동 소멸 | GC가 제거 |
| 크기 결정 | 컴파일 타임 | 런타임 |
| 속도 | 빠름 | 상대적으로 느림 |
| 공유 | 스레드별 독립 | 모든 스레드 공유 |

## 실전 예제
```java
public class MemoryExample {
    static int staticVar = 100;  // Method Area (static)
    
    public static void main(String[] args) {
        int a = 10;              // Stack
        String str = "Hello";    // Stack (참조), Heap (String Pool)
        
        Person person = new Person("홍길동");  // Stack (참조), Heap (객체)
        
        method1(a);
    }
    
    public static void method1(int param) {  // Stack (새로운 프레임)
        int local = 20;          // Stack
    }  // method1 종료 → Stack 프레임 소멸
}
```

**메모리 할당**:
```
Method Area:
├─ MemoryExample 클래스 정보
├─ staticVar = 100
└─ main(), method1() 메서드 정보

Stack (main 스레드):
┌─────────────────┐
│ method1 frame:  │
│  param = 10     │
│  local = 20     │
├─────────────────┤
│ main frame:     │
│  a = 10         │
│  str ───────────┼───┐
│  person ────────┼─┐ │
└─────────────────┘ │ │
                    │ │
Heap:               │ │
┌──────────────────┐│ │
│ String Pool:     ││ │
│  "Hello" ◄───────┘│ │
│                   │ │
│ Person 객체: ◄────┘ │
│  name ─────────────┘
│  "홍길동"
└───────────────────┘
```

## 가비지 컬렉션 (GC)
```java
public void createObjects() {
    Person p1 = new Person("A");
    Person p2 = new Person("B");
    
    p1 = null;  // 더 이상 참조 안 함 → GC 대상
}  // p2도 메서드 종료 시 참조 사라짐 → GC 대상
```

**GC 동작**:
- Heap에 더 이상 참조되지 않는 객체 자동 제거
- Stack에서 참조가 사라지면 GC 대상이 됨
- 개발자가 직접 메모리 해제 불필요

## 참고 자료

- [Oracle JVM 메모리 구조](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)