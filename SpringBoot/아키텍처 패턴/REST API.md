# REST API

## 개념

**REST**: 웹 서비스를 위한 아키텍처 스타일

**REST API**: REST 원칙을 따르는 API

**RESTful API**: REST 원칙을 **엄격히** 준수하는 API

## REST 원칙

### 1. 자원 기반 (Resource-Based)
모든 것을 **리소스**로 표현
```
/users          (사용자 목록)
/users/1        (ID가 1인 사용자)
/users/1/posts  (ID가 1인 사용자의 게시글)
```

### 2. HTTP 메서드
| 메서드 | 역할 | 예시 |
|--------|------|------|
| GET | 조회 | `GET /users` |
| POST | 생성 | `POST /users` |
| PUT | 전체 수정 | `PUT /users/1` |
| PATCH | 일부 수정 | `PATCH /users/1` |
| DELETE | 삭제 | `DELETE /users/1` |

### 3. 무상태성 (Stateless)
서버는 클라이언트 상태를 저장하지 않음
```
요청 1: GET /users/1
요청 2: GET /users/2
→ 각 요청은 독립적
```

### 4. 캐시 가능
```http
Cache-Control: max-age=3600
```

### 5. 계층화
```
클라이언트 → 로드 밸런서 → API 서버 → DB
```

### 6. 균일한 인터페이스
일관된 URL 구조

## REST API 설계

### URL 구조
```
✅ 좋은 예시
GET    /users              # 목록 조회
GET    /users/1            # 단일 조회
POST   /users              # 생성
PUT    /users/1            # 수정
DELETE /users/1            # 삭제

❌ 나쁜 예시
GET    /getUsers           # 동사 사용
POST   /createUser         # 동사 사용
GET    /user?id=1          # ID를 쿼리로
DELETE /deleteUser/1       # 동사 사용
```

### 리소스 명명 규칙
```
✅ 복수형 사용
/users
/posts
/comments

✅ 소문자, 하이픈
/user-profiles
/blog-posts

❌ 대문자, 밑줄
/UserProfiles
/blog_posts
```

### 중첩 리소스
```
GET /users/1/posts         # 사용자 1의 게시글 목록
GET /users/1/posts/5       # 사용자 1의 게시글 5
POST /users/1/posts        # 사용자 1의 게시글 생성
```

## HTTP 상태 코드

| 코드 | 의미 | 사용 |
|------|------|------|
| 200 | OK | 성공 |
| 201 | Created | 생성 성공 |
| 204 | No Content | 삭제 성공 (응답 없음) |
| 400 | Bad Request | 잘못된 요청 |
| 401 | Unauthorized | 인증 실패 |
| 403 | Forbidden | 권한 없음 |
| 404 | Not Found | 리소스 없음 |
| 500 | Internal Server Error | 서버 오류 |

## 응답 형식 (JSON)

### 성공
```json
{
  "id": 1,
  "name": "홍길동",
  "email": "hong@example.com"
}
```

### 목록
```json
{
  "data": [
    {"id": 1, "name": "홍길동"},
    {"id": 2, "name": "김철수"}
  ],
  "total": 2,
  "page": 1
}
```

### 에러
```json
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "사용자를 찾을 수 없습니다"
  }
}
```

## REST vs RESTful

| 구분 | REST API | RESTful API |
|------|----------|-------------|
| 원칙 준수 | 부분적 | 엄격 |
| HATEOAS | 선택 | 권장 |
| 사용 | 일반적 | 강조할 때 |

### HATEOAS 예시
```json
{
  "id": 1,
  "name": "홍길동",
  "links": [
    {
      "rel": "self",
      "href": "/users/1"
    },
    {
      "rel": "posts",
      "href": "/users/1/posts"
    }
  ]
}
```

## REST API 특징

### 장점
- 확장성 (클라이언트-서버 분리)
- 유연성 (다양한 클라이언트 지원)
- 독립성 (플랫폼/언어 독립)
- 캐시 활용

### 단점
- 오버페칭 (불필요한 데이터)
- 언더페칭 (추가 요청 필요)
- 표준 부재 (구현 방식 다양)

## REST API vs GraphQL

| 구분 | REST API | GraphQL |
|------|----------|---------|
| 요청 | 여러 엔드포인트 | 단일 엔드포인트 |
| 데이터 | 고정된 구조 | 원하는 필드만 |
| 오버페칭 | 발생 가능 | 없음 |
| 학습 곡선 | 낮음 | 높음 |

## 버전 관리

### URL 버전
```
https://api.example.com/v1/users
https://api.example.com/v2/users
```

### 헤더 버전
```http
GET /users
Accept: application/vnd.example.v1+json
```

## 페이징
```
GET /users?page=1&size=20
GET /users?offset=0&limit=20
```

**응답**:
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "size": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

## 필터링 & 정렬
```
GET /users?status=active&sort=name&order=asc
GET /posts?author=1&category=tech
```

## 참고 자료

- [REST API 설계 가이드](https://restfulapi.net/)
- [HTTP 상태 코드](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)