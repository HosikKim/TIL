# JSON (JavaScript Object Notation)

## 개념

**경량 데이터 교환 형식**으로 사람과 기계 모두 읽기 쉬운 텍스트 기반 포맷

### 특징
- 텍스트 형식
- 언어 독립적
- JavaScript 객체 표기법 기반
- 간결하고 명확한 구조

## 기본 구조

### 1. 객체 (Object)
```json
{
  "name": "홍길동",
  "age": 25,
  "isStudent": false
}
```

### 2. 배열 (Array)
```json
["apple", "banana", "cherry"]
```

### 3. 혼합
```json
{
  "name": "홍길동",
  "age": 25,
  "hobbies": ["독서", "운동", "음악"],
  "address": {
    "city": "서울",
    "zipCode": "12345"
  }
}
```

## 지원 데이터 타입

| 타입 | 예시 | 설명 |
|------|------|------|
| 문자열 | `"hello"` | 큰따옴표 필수 |
| 숫자 | `42`, `3.14` | 정수, 실수 |
| 불리언 | `true`, `false` | 참/거짓 |
| null | `null` | 빈 값 |
| 객체 | `{"key": "value"}` | 키-값 쌍 |
| 배열 | `[1, 2, 3]` | 순서 있는 리스트 |

**주의**: 함수, undefined, Date 객체 등은 직접 표현 불가

## JavaScript에서 사용

### JSON.stringify() - 객체 → JSON 문자열
```javascript
const user = {
  name: "홍길동",
  age: 25,
  hobbies: ["독서", "운동"]
};

const jsonString = JSON.stringify(user);
console.log(jsonString);
// {"name":"홍길동","age":25,"hobbies":["독서","운동"]}

// 들여쓰기
const formatted = JSON.stringify(user, null, 2);
/*
{
  "name": "홍길동",
  "age": 25,
  "hobbies": [
    "독서",
    "운동"
  ]
}
*/
```

### JSON.parse() - JSON 문자열 → 객체
```javascript
const jsonString = '{"name":"홍길동","age":25}';
const user = JSON.parse(jsonString);

console.log(user.name);  // 홍길동
console.log(user.age);   // 25
```

## Java에서 사용

### Jackson 라이브러리
```java
import com.fasterxml.jackson.databind.ObjectMapper;

// 객체 → JSON
User user = new User("홍길동", 25);
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writeValueAsString(user);
// {"name":"홍길동","age":25}

// JSON → 객체
String json = "{\"name\":\"홍길동\",\"age\":25}";
User user = mapper.readValue(json, User.class);
```

### Gson 라이브러리
```java
import com.google.gson.Gson;

// 객체 → JSON
Gson gson = new Gson();
String json = gson.toJson(user);

// JSON → 객체
User user = gson.fromJson(json, User.class);
```

## JSON vs XML

| 구분 | JSON | XML |
|------|------|-----|
| 구조 | 간결 | 복잡 |
| 크기 | 작음 ⭐ | 큼 |
| 가독성 | 높음 ⭐ | 중간 |
| 파싱 속도 | 빠름 ⭐ | 느림 |
| 데이터 타입 | 지원 | 문자열만 |
| 주석 | 불가 | 가능 |

### 비교 예시
```json
// JSON
{
  "name": "홍길동",
  "age": 25
}
```
```xml
<!-- XML -->
<user>
  <name>홍길동</name>
  <age>25</age>
</user>
```

## REST API에서 JSON

### 요청 (POST)
```javascript
fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    name: '홍길동',
    age: 25
  })
});
```

### 응답
```javascript
fetch('/api/users/1')
  .then(response => response.json())
  .then(data => {
    console.log(data.name);  // 서버에서 받은 JSON 파싱
  });
```

## 주의사항

### 1. 큰따옴표 필수
```json
// ❌ 작은따옴표
{'name': 'value'}

// ✅ 큰따옴표
{"name": "value"}
```

### 2. 마지막 쉼표 금지
```json
// ❌ 마지막 쉼표
{
  "name": "홍길동",
  "age": 25,
}

// ✅ 쉼표 없음
{
  "name": "홍길동",
  "age": 25
}
```

### 3. 주석 불가
```json
// ❌ 주석 사용 불가
{
  // 이름
  "name": "홍길동"
}
```

## 활용 분야

| 분야 | 용도 |
|------|------|
| API 통신 | REST API 요청/응답 |
| 설정 파일 | package.json, tsconfig.json |
| 데이터 저장 | NoSQL (MongoDB) |
| 로그 | 구조화된 로그 저장 |
| 웹소켓 | 실시간 데이터 전송 |

## 장단점

### 장점
- 경량 (XML보다 작음)
- 읽기 쉬움
- 빠른 파싱
- 언어 독립적
- 표준화

### 단점
- 주석 불가
- 스키마 정의 어려움
- 복잡한 데이터 구조 표현 제한
- 보안 이슈 (JSON Injection)

## 참고 자료

- [JSON 공식 사이트](https://www.json.org/)
- [MDN JSON](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON)