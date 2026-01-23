# CORS (Cross-Origin Resource Sharing)

## 개념
다른 출처(Origin)의 리소스에 접근할 수 있도록 브라우저에게 권한을 부여하는 HTTP 헤더 기반 보안 메커니즘

## 목적
* 보안을 유지하면서 필요한 교차 출처 요청 허용
* SOP(Same-Origin Policy)의 제약을 선택적으로 완화
* 프론트엔드와 백엔드가 다른 도메인일 때 통신 가능하게 함

## Origin(출처)이란?

```
https://example.com:3000/api/users
└─┬─┘   └────┬─────┘└┬─┘
  │          │       │
Protocol   Domain   Port
```

**같은 Origin**: 프로토콜, 도메인, 포트가 모두 동일  
**다른 Origin**: 셋 중 하나라도 다르면 다른 출처

## CORS 발생 상황

| 상황 | 출처1 | 출처2 | CORS 필요? |
|------|-------|-------|-----------|
| 프로토콜 다름 | http://localhost:3000 | https://localhost:3000 | ✅ |
| 도메인 다름 | http://example.com | http://api.example.com | ✅ |
| 포트 다름 | http://localhost:3000 | http://localhost:8080 | ✅ |
| 모두 동일 | http://localhost:3000 | http://localhost:3000 | ❌ |

## 동작 방식

### Simple Request
```
[브라우저] ─────────────────> [서버]
         GET /api/users
         Origin: http://localhost:3000

[브라우저] <───────────────── [서버]
         Access-Control-Allow-Origin: *
         (또는 구체적 도메인)
```

### Preflight Request (본 요청 전 사전 확인)
```
[브라우저] ─────────────────> [서버]
         OPTIONS /api/users (Preflight)
         Origin: http://localhost:3000

[브라우저] <───────────────── [서버]
         Access-Control-Allow-Origin: http://localhost:3000
         Access-Control-Allow-Methods: GET, POST, PUT
         
[브라우저] ─────────────────> [서버]
         POST /api/users (실제 요청)
```

## SpringBoot 설정

### 1. @CrossOrigin 어노테이션 (컨트롤러별)

```java
@RestController
@CrossOrigin(origins = "http://localhost:3000")
public class UserController {
    
    @GetMapping("/api/users")
    public List<User> getUsers() {
        return userService.findAll();
    }
}
```

### 2. 전역 설정 (WebMvcConfigurer)

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://localhost:3000")
                .allowedMethods("GET", "POST", "PUT", "DELETE")
                .allowedHeaders("*")
                .allowCredentials(true);
    }
}
```

### 3. Spring Security 사용 시

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) {
    http.cors().and()
        .csrf().disable();
    return http.build();
}
```

## 주요 헤더

| 헤더 | 설명 |
|------|------|
| **Access-Control-Allow-Origin** | 허용할 출처 (`*` 또는 구체적 도메인) |
| **Access-Control-Allow-Methods** | 허용할 HTTP 메서드 |
| **Access-Control-Allow-Headers** | 허용할 요청 헤더 |
| **Access-Control-Allow-Credentials** | 쿠키 포함 여부 (true/false) |
| **Access-Control-Max-Age** | Preflight 결과 캐시 시간 (초) |

## 보안 고려사항

### ❌ 위험한 설정
```java
// 모든 출처 허용 (프로덕션에서 절대 사용 금지)
.allowedOrigins("*")
.allowCredentials(true)  // 같이 사용 불가!
```

### ✅ 안전한 설정
```java
// 구체적인 출처 명시
.allowedOrigins(
    "https://myapp.com",
    "https://admin.myapp.com"
)
.allowedMethods("GET", "POST") // 필요한 메서드만
.allowCredentials(true)
```

## 자주 하는 실수

| 문제 | 원인 | 해결 |
|------|------|------|
| `*` 사용 시 쿠키 안 감 | credentials와 함께 사용 불가 | 구체적 도메인 명시 |
| Preflight 실패 | OPTIONS 메서드 미허용 | allowedMethods에 추가 |
| 로컬은 되는데 배포하면 안 됨 | 출처 설정이 localhost만 | 실제 도메인 추가 |

## 사용 가이드

### 개발 환경
```java
.allowedOrigins("http://localhost:3000", "http://localhost:8080")
.allowedMethods("*")
```

### 프로덕션 환경
```java
.allowedOrigins("https://myapp.com")
.allowedMethods("GET", "POST", "PUT", "DELETE")
.allowedHeaders("Authorization", "Content-Type")
.allowCredentials(true)
.maxAge(3600) // 1시간 캐시
```

## 참고 자료
* [MDN CORS 문서](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
* [Spring CORS 공식 문서](https://spring.io/guides/gs/rest-service-cors/)