# String Pool

## 개념

Java 힙 메모리 내 문자열 리터럴을 저장하고 재사용하는 특별한 영역

**목적**: 동일 문자열 중복 저장 방지, 메모리 효율성 향상

## 동작 방식

### 리터럴 방식
- String Pool 사용
- 동일 문자열은 하나의 객체만 생성
- 재사용으로 메모리 절약

### new 연산자
- 일반 힙 영역 사용
- 항상 새 객체 생성
- 메모리 낭비 가능

## 메모리 위치

- Java 7 이전: PermGen 영역
- Java 7 이후: Heap 영역

## == vs equals()

| 연산자 | 비교 대상 | 결과 |
|--------|----------|------|
| == | 참조 (주소) | Pool의 같은 객체면 true |
| equals() | 값 (내용) | 내용이 같으면 true |

**권장**: 문자열 비교는 항상 equals() 사용

## intern() 메서드

**기능**:
- 문자열을 String Pool에 추가
- 이미 존재하면 기존 객체 반환
- 없으면 추가 후 반환

**주의**:
- 과도한 사용 지양
- Pool은 GC 대상 아님
- 메모리 증가 가능

## String vs StringBuffer vs StringBuilder

| 구분 | String | StringBuffer | StringBuilder |
|------|--------|--------------|---------------|
| 가변성 | 불변 | 가변 | 가변 |
| 스레드 안전 | O | O | X |
| 성능 | 느림 | 중간 | 빠름 |
| 사용 시기 | 변경 없음 | 멀티스레드 | 단일스레드 |

### String (불변)
- 수정 시 새 객체 생성
- String Pool 활용
- 상수, 고정 문자열

### StringBuffer (가변, 동기화)
- synchronized로 스레드 안전
- 멀티스레드 환경
- 문자열 빈번한 수정

### StringBuilder (가변, 비동기화)
- 가장 빠른 성능
- 단일 스레드 환경
- 문자열 빈번한 수정

## 성능

**문자열 연결 시**:
1. StringBuilder: 가장 빠름
2. StringBuffer: 중간
3. String: 가장 느림 (매번 새 객체)

**선택 기준**:
- 변경 없음 → String
- 멀티스레드 + 수정 → StringBuffer
- 단일스레드 + 수정 → StringBuilder

## 최적화

**효율적 사용**:
- 리터럴 방식 선호
- new 연산자 지양
- 반복 사용 문자열만 intern()

**주의사항**:
- Pool은 GC 안 됨
- 불필요한 intern() 금지
- 메모리 모니터링

## 정리

**핵심**:
- 메모리 효율성
- 문자열 재사용
- 상황에 맞는 선택

**선택 가이드**:
- 고정 문자열 → String
- 변경 많음 + 단일스레드 → StringBuilder
- 변경 많음 + 멀티스레드 → StringBuffer

## 참고 자료

- [Java String Pool](https://www.baeldung.com/java-string-pool)
- [String vs StringBuffer vs StringBuilder](https://www.baeldung.com/java-string-builder-string-buffer)