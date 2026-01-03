# JSON Object와 JSON Array

## JSON Object (객체)

### 구조
```json
{
  "key1": "value1",
  "key2": "value2"
}
```

**특징**:
- 중괄호 `{}` 사용
- 키-값 쌍 (Key-Value Pair)
- 키는 문자열 (큰따옴표 필수)
- 순서 없음

### 예시
```json
{
  "name": "홍길동",
  "age": 25,
  "city": "서울"
}
```

### JavaScript 접근
```javascript
const user = {
  "name": "홍길동",
  "age": 25
};

console.log(user.name);      // 홍길동
console.log(user["age"]);    // 25
```

## JSON Array (배열)

### 구조
```json
["value1", "value2", "value3"]
```

**특징**:
- 대괄호 `[]` 사용
- 순서 있는 값의 목록
- 인덱스로 접근 (0부터 시작)
- 다양한 타입 포함 가능

### 예시
```json
["apple", "banana", "cherry"]
```
```json
[1, 2, 3, 4, 5]
```

### JavaScript 접근
```javascript
const fruits = ["apple", "banana", "cherry"];

console.log(fruits[0]);  // apple
console.log(fruits[1]);  // banana
console.log(fruits.length);  // 3
```

## Object vs Array 비교

| 구분 | JSON Object | JSON Array |
|------|------------|-----------|
| 기호 | `{}` | `[]` |
| 구조 | 키-값 쌍 | 순서 있는 목록 |
| 접근 | 키 | 인덱스 (0, 1, 2...) |
| 순서 | 없음 | 있음 |
| 용도 | 하나의 데이터 표현 | 여러 개 나열 |

## 중첩 구조

### Object 안에 Array
```json
{
  "name": "홍길동",
  "hobbies": ["독서", "운동", "음악"]
}
```
```javascript
const user = {
  "name": "홍길동",
  "hobbies": ["독서", "운동", "음악"]
};

console.log(user.hobbies[0]);  // 독서
```

### Array 안에 Object
```json
[
  {
    "name": "홍길동",
    "age": 25
  },
  {
    "name": "김철수",
    "age": 30
  }
]
```
```javascript
const users = [
  { "name": "홍길동", "age": 25 },
  { "name": "김철수", "age": 30 }
];

console.log(users[0].name);  // 홍길동
console.log(users[1].age);   // 30
```

### 복잡한 중첩
```json
{
  "company": "ABC Corp",
  "employees": [
    {
      "name": "홍길동",
      "position": "개발자",
      "skills": ["Java", "Python", "JavaScript"]
    },
    {
      "name": "김철수",
      "position": "디자이너",
      "skills": ["Photoshop", "Illustrator"]
    }
  ]
}
```
```javascript
const data = { /* 위 JSON */ };

// 첫 번째 직원의 이름
console.log(data.employees[0].name);  // 홍길동

// 첫 번째 직원의 첫 번째 스킬
console.log(data.employees[0].skills[0]);  // Java
```

## 실전 예시

### REST API 응답 (단일 객체)
```json
{
  "id": 1,
  "title": "게시글 제목",
  "content": "게시글 내용",
  "author": "홍길동",
  "createdAt": "2025-01-03"
}
```

### REST API 응답 (목록)
```json
{
  "total": 2,
  "posts": [
    {
      "id": 1,
      "title": "첫 번째 게시글"
    },
    {
      "id": 2,
      "title": "두 번째 게시글"
    }
  ]
}
```

### JavaScript 처리
```javascript
// API 응답
fetch('/api/posts')
  .then(response => response.json())
  .then(data => {
    console.log(data.total);  // 2
    
    // 배열 순회
    data.posts.forEach(post => {
      console.log(post.title);
    });
  });
```

## Java에서 사용

### JSONObject
```java
import org.json.JSONObject;

JSONObject user = new JSONObject();
user.put("name", "홍길동");
user.put("age", 25);

System.out.println(user.getString("name"));  // 홍길동
System.out.println(user.getInt("age"));      // 25
```

### JSONArray
```java
import org.json.JSONArray;

JSONArray fruits = new JSONArray();
fruits.put("apple");
fruits.put("banana");

System.out.println(fruits.get(0));  // apple
System.out.println(fruits.length()); // 2
```

## 선택 가이드

**Object 사용**:
- 단일 항목 데이터
- 키-값 관계 명확
- 속성이 다양한 경우
```json
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}
```

**Array 사용**:
- 여러 항목 나열
- 순서가 중요
- 같은 구조 반복
```json
["사과", "바나나", "체리"]
```

**조합 사용**:
- 복잡한 데이터 구조
- 여러 항목 + 각 항목의 속성
```json
{
  "users": [
    {"name": "홍길동", "age": 25},
    {"name": "김철수", "age": 30}
  ]
}
```

## 참고 자료

- [JSON 공식 사이트](https://www.json.org/)
- [MDN JSON](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON)