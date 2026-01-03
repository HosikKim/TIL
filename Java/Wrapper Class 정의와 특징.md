# Wrapper Class

## 개념

기본 데이터 타입(primitive type)을 객체로 감싸는 클래스

**목적**: 기본 타입을 객체로 다뤄야 하는 상황에서 사용

## Wrapper Class 종류

| 기본 타입 | Wrapper Class |
|----------|--------------|
| byte | Byte |
| short | Short |
| int | Integer |
| long | Long |
| float | Float |
| double | Double |
| char | Character |
| boolean | Boolean |

**위치**: 모두 java.lang 패키지 (자동 import)

## 왜 필요한가?

### 1. 컬렉션 사용
- ArrayList<int> ❌ 불가능
- ArrayList<Integer> ✅ 가능
- 컬렉션은 Object 기반, 기본 타입은 불가

### 2. null 값 표현
- 기본 타입: null 불가 (int는 0, boolean은 false)
- Wrapper: null 가능 (값이 없음 표현)

### 3. 유틸리티 메서드
- 문자열 변환
- 진법 변환
- 최대/최소값 상수

## Boxing과 Unboxing

### Auto-boxing (Java 5+)
```
기본 타입 → Wrapper 자동 변환
int num = 10;
Integer obj = num;  // 자동 변환
```

### Auto-unboxing
```
Wrapper → 기본 타입 자동 변환
Integer obj = 10;
int num = obj;  // 자동 변환
```

## 주요 기능

### 문자열 변환
- Integer.parseInt("123") → 123
- Double.parseDouble("3.14") → 3.14
- Integer.toString(123) → "123"

### 상수
- Integer.MAX_VALUE / MIN_VALUE
- Double.NaN, POSITIVE_INFINITY

### 진법 변환
- Integer.toBinaryString(10) → "1010"
- Integer.parseInt("ff", 16) → 255

## 주의사항

### 1. == 비교 금지
- == 는 참조 비교
- equals() 사용 필수
- 캐싱 범위(-128~127)만 == 가능

### 2. null 체크
- Auto-unboxing 시 null이면 NullPointerException
- null 체크 필수

### 3. 캐싱
- Integer: -128 ~ 127
- 이 범위는 같은 객체 재사용
- 범위 밖은 새 객체 생성

## 정리

**사용 시기**:
- 컬렉션 → Wrapper
- null 필요 → Wrapper
- 성능 중요 → 기본 타입
- 일반 연산 → 기본 타입

## 참고 자료

- [Oracle Wrapper Classes](https://docs.oracle.com/javase/tutorial/java/data/numberclasses.html)