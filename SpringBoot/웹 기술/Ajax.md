# CORS (Cross-Origin Resource Sharing)

## 개념

**다른 출처**(도메인, 프로토콜, 포트)의 리소스 접근을 허용하는 보안 메커니즘

## 출처 (Origin)

### 같은 출처 ✅
```
https://example.com:443/page1
https://example.com:443/page2
```

### 다른 출처 ❌
```
https://example.com  →  http://example.com       (프로토콜)
https://example.com  →  https://api.example.com  (도메인)
https://example.com  →  https://example.com:8080 (포트)
```

## 동일 출처 정책 (SOP)

브라우저 기본 정책: **다른 출처 접근 차단**
```javascript
// https://example.com 에서 실행
fetch('https://api.other.com/data')  // ❌ CORS 오류
```

## CORS 동작 방식

### 단순 요청
```
1. 브라우저 → 서버: Origin 헤더 포함
2. 서버 → 브라우저: Access-Control-Allow-Origin 응답
3. 브라우저: 허용 여부 확인
```

### 프리플라이트 (복잡한 요청)
```
1. 브라우저 → 서버: OPTIONS 요청 (사전 확인)
2. 서버 → 브라우저: 허용 여부 응답
3. 브라우저 → 서버: 실제 요청
```

## 주요 헤더

| 헤더 | 설명 |
|------|------|
| Access-Control-Allow-Origin | 허용 출처 |
| Access-Control-Allow-Methods | 허용 메서드 |
| Access-Control-Allow-Headers | 허용 헤더 |
| Access-Control-Allow-Credentials | 쿠키 포함 허용 |

## 서버 구현

### Node.js (Express)
```javascript
const cors = require('cors');

// 특정 출처만 허용 (권장)
app.use(cors({
  origin: 'https://app.example.com',
  methods: ['GET', 'POST'],
  credentials: true
}));

// 모든 출처 허용 (개발용)
app.use(cors());
```

### Spring Boot
```java
@Configuration
public class WebConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                    .allowedOrigins("https://app.example.com")
                    .allowedMethods("GET", "POST");
            }
        };
    }
}
```

## CORS 오류 해결

### 오류 메시지
```
Access to fetch at 'https://api.example.com' from origin 
'https://app.example.com' has been blocked by CORS policy
```

### 해결 방법

**1. 서버에 CORS 설정 (올바른 방법 ✅)**
```javascript
app.use(cors({
  origin: 'https://app.example.com'
}));
```

**2. 프록시 사용 (개발 환경)**
```json
// package.json
"proxy": "http://localhost:5000"
```

## 보안 주의사항

### ❌ 위험
```javascript
// 모든 출처 허용 + 쿠키
app.use(cors({
  origin: '*',
  credentials: true
}));
```

### ✅ 안전
```javascript
// 허용 출처 명시
app.use(cors({
  origin: ['https://app.example.com'],
  credentials: true
}));
```

## 실무 팁

**환경별 설정**
```javascript
const corsOptions = {
  origin: process.env.NODE_ENV === 'production'
    ? 'https://app.example.com'
    : '*'
};
app.use(cors(corsOptions));
```

**쿠키 포함 요청**
```javascript
fetch('https://api.example.com/users', {
  credentials: 'include'  // 쿠키 포함
});
```

## CORS가 적용되지 않는 경우

- 서버-서버 통신
- Postman, curl 등 도구
- 네이티브 앱

## 참고 자료

- [MDN CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)