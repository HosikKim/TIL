# URL 패턴

## 개념

URL에서 **동적으로 데이터를 전달**하는 방식

## URL 구조
```
https://example.com/api/users/123?page=1&size=10#section

프로토콜://도메인/경로/경로변수?쿼리파라미터#해시
```

## 1. 쿼리 파라미터 (Query Parameters)

### 형태
```
/search?key1=value1&key2=value2
```

### 특징
- `?` 뒤에 키-값 쌍
- `&`로 구분
- 필터링, 검색, 페이징에 사용

### 예시
```javascript
// 검색
/search?keyword=javascript&category=books

// 페이징
/products?page=2&size=20

// 정렬
/users?sort=name&order=asc

// 여러 값
/filter?color=red&color=blue
```

### JavaScript 처리
```javascript
// URL 생성
const params = new URLSearchParams({
  keyword: 'javascript',
  page: 1
});
const url = `/search?${params}`;
// /search?keyword=javascript&page=1

// URL 파싱
const url = new URL('https://example.com/search?keyword=js&page=1');
console.log(url.searchParams.get('keyword'));  // 'js'
console.log(url.searchParams.get('page'));     // '1'
```

## 2. 경로 변수 (Path Variables)

### 형태
```
/api/users/{id}
/posts/{postId}/comments/{commentId}
```

### 특징
- URL 경로의 일부가 변수
- RESTful API에서 리소스 식별
- 필수 값

### 예시
```javascript
// 사용자 조회
/api/users/123

// 게시글 상세
/posts/456

// 중첩 리소스
/posts/456/comments/789
```

### JavaScript 사용
```javascript
// Fetch API
const userId = 123;
fetch(`/api/users/${userId}`)
  .then(response => response.json())
  .then(data => console.log(data));

// Axios
axios.get(`/api/users/${userId}`)
  .then(response => console.log(response.data));
```

### Spring Boot 처리
```java
@GetMapping("/api/users/{id}")
public User getUser(@PathVariable Long id) {
  return userService.findById(id);
}

// 여러 경로 변수
@GetMapping("/posts/{postId}/comments/{commentId}")
public Comment getComment(
  @PathVariable Long postId,
  @PathVariable Long commentId
) {
  return commentService.findComment(postId, commentId);
}
```

## 경로 변수 vs 쿼리 파라미터

| 구분 | 경로 변수 | 쿼리 파라미터 |
|------|----------|--------------|
| 위치 | 경로 내 | `?` 뒤 |
| 용도 | 리소스 식별 | 필터링, 옵션 |
| 필수성 | 필수 | 선택적 |
| 예시 | `/users/123` | `/users?age=25` |
| RESTful | ✅ 권장 | 부가 정보 |

### 선택 기준
```javascript
// ✅ 경로 변수: 리소스 식별
GET /users/123           // 특정 사용자
GET /posts/456           // 특정 게시글

// ✅ 쿼리 파라미터: 필터링/옵션
GET /users?age=25        // 나이로 필터링
GET /posts?page=2        // 페이징
GET /search?q=keyword    // 검색
```

## 조합 사용
```javascript
// 경로 변수 + 쿼리 파라미터
GET /users/123/posts?status=published&limit=10

// 특정 사용자(123)의 게시글 중
// 상태가 published인 것을 10개만 조회
```

### 예시
```javascript
// JavaScript
const userId = 123;
const params = new URLSearchParams({
  status: 'published',
  limit: 10
});
fetch(`/users/${userId}/posts?${params}`);
```
```java
// Spring Boot
@GetMapping("/users/{userId}/posts")
public List getUserPosts(
  @PathVariable Long userId,
  @RequestParam String status,
  @RequestParam int limit
) {
  return postService.findPosts(userId, status, limit);
}
```

## 템플릿 리터럴 (JavaScript)

### 기본 사용
```javascript
const userId = 123;
const url = `/api/users/${userId}`;
// /api/users/123

// 여러 변수
const postId = 456;
const commentId = 789;
const url = `/posts/${postId}/comments/${commentId}`;
// /posts/456/comments/789
```

### 복잡한 URL 생성
```javascript
function buildUrl(userId, filters) {
  const params = new URLSearchParams(filters);
  return `/users/${userId}/posts?${params}`;
}

const url = buildUrl(123, { status: 'active', page: 2 });
// /users/123/posts?status=active&page=2
```

## RESTful API URL 설계

### 좋은 예시
```
GET    /users          # 사용자 목록
GET    /users/123      # 특정 사용자
POST   /users          # 사용자 생성
PUT    /users/123      # 사용자 수정
DELETE /users/123      # 사용자 삭제

GET    /users/123/posts              # 특정 사용자의 게시글
GET    /posts/456/comments           # 특정 게시글의 댓글
```

### 나쁜 예시
```
❌ /getUser?id=123        → ✅ /users/123
❌ /createUser            → ✅ POST /users
❌ /updateUser?id=123     → ✅ PUT /users/123
❌ /deleteUser?id=123     → ✅ DELETE /users/123
```

## URL 인코딩
```javascript
// 특수문자 인코딩
const keyword = '자바스크립트 & Node.js';
const encoded = encodeURIComponent(keyword);
// %EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%20%26%20Node.js

const url = `/search?q=${encoded}`;

// 디코딩
const decoded = decodeURIComponent(encoded);
// 자바스크립트 & Node.js
```

## 참고 자료

- [URL 표준 (WHATWG)](https://url.spec.whatwg.org/)
- [RESTful API 설계 가이드](https://restfulapi.net/)