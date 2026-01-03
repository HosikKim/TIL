# Web Session

## 개념

서버에 사용자 **상태 정보를 저장**하여 유지하는 기술

## 동작 방식
```
1. 사용자 로그인
   ↓
2. 서버: 세션 생성 + 고유 세션 ID 발급
   ↓
3. 서버 → 클라이언트: 세션 ID 전송 (쿠키)
   Set-Cookie: sessionId=abc123
   ↓
4. 클라이언트 → 서버: 요청 시 세션 ID 포함
   Cookie: sessionId=abc123
   ↓
5. 서버: 세션 ID로 사용자 식별 및 정보 조회
```

## 세션 vs 쿠키

| 구분 | Session | Cookie |
|------|---------|--------|
| 저장 위치 | 서버 | 클라이언트 (브라우저) |
| 보안 | 높음 ⭐ | 낮음 (탈취 위험) |
| 용량 제한 | 없음 (서버 저장) | 4KB |
| 속도 | 느림 (서버 조회) | 빠름 |
| 수명 | 브라우저 종료 시 삭제 | 만료 기간까지 유지 |
| 사용 | 로그인, 민감 정보 | 설정, 추적 |

## 세션 구현

### Node.js (Express)
```javascript
const session = require('express-session');

app.use(session({
  secret: 'my-secret-key',        // 암호화 키
  resave: false,
  saveUninitialized: false,
  cookie: {
    maxAge: 60000 * 60,           // 1시간
    httpOnly: true,               // JS 접근 차단
    secure: true                  // HTTPS만
  }
}));

// 세션 저장
app.post('/login', (req, res) => {
  req.session.userId = user.id;
  req.session.username = user.name;
  res.send('로그인 성공');
});

// 세션 읽기
app.get('/profile', (req, res) => {
  if (req.session.userId) {
    res.send(`환영합니다, ${req.session.username}`);
  } else {
    res.send('로그인 필요');
  }
});

// 세션 삭제 (로그아웃)
app.post('/logout', (req, res) => {
  req.session.destroy();
  res.send('로그아웃 성공');
});
```

### Spring Boot
```java
@GetMapping("/login")
public String login(HttpSession session) {
    session.setAttribute("userId", user.getId());
    session.setAttribute("username", user.getName());
    return "로그인 성공";
}

@GetMapping("/profile")
public String profile(HttpSession session) {
    String username = (String) session.getAttribute("username");
    if (username != null) {
        return "환영합니다, " + username;
    }
    return "로그인 필요";
}

@GetMapping("/logout")
public String logout(HttpSession session) {
    session.invalidate();
    return "로그아웃 성공";
}
```

## 세션 저장소

### 1. 메모리 (기본)
```javascript
// 서버 재시작 시 세션 소멸
app.use(session({ /* ... */ }));
```

### 2. Redis (권장)
```javascript
const RedisStore = require('connect-redis')(session);
const redis = require('redis');
const redisClient = redis.createClient();

app.use(session({
  store: new RedisStore({ client: redisClient }),
  secret: 'my-secret-key'
}));
```

**장점**: 빠름, 확장 가능, 서버 재시작 시 유지

### 3. 데이터베이스
```javascript
const MySQLStore = require('express-mysql-session')(session);

app.use(session({
  store: new MySQLStore(options)
}));
```

## 세션 활용 예시

### 로그인 상태 유지
```javascript
// 로그인
app.post('/login', (req, res) => {
  const user = authenticate(req.body);
  req.session.userId = user.id;
  res.send('로그인 성공');
});

// 인증 미들웨어
function requireLogin(req, res, next) {
  if (!req.session.userId) {
    return res.status(401).send('로그인 필요');
  }
  next();
}

// 보호된 라우트
app.get('/dashboard', requireLogin, (req, res) => {
  res.send('대시보드');
});
```

### 장바구니
```javascript
// 장바구니 추가
app.post('/cart', (req, res) => {
  if (!req.session.cart) {
    req.session.cart = [];
  }
  req.session.cart.push(req.body.item);
  res.send('추가 완료');
});

// 장바구니 조회
app.get('/cart', (req, res) => {
  res.json(req.session.cart || []);
});
```

## 세션 보안

### 1. HttpOnly
```javascript
cookie: {
  httpOnly: true  // JavaScript로 접근 불가
}
```

### 2. Secure (HTTPS)
```javascript
cookie: {
  secure: true  // HTTPS에서만 전송
}
```

### 3. SameSite
```javascript
cookie: {
  sameSite: 'strict'  // CSRF 방지
}
```

### 4. 세션 고정 공격 방지
```javascript
// 로그인 성공 시 세션 ID 재생성
app.post('/login', (req, res) => {
  req.session.regenerate((err) => {
    req.session.userId = user.id;
    res.send('로그인 성공');
  });
});
```

## 세션 vs JWT

| 구분 | Session | JWT |
|------|---------|-----|
| 저장 | 서버 | 클라이언트 |
| 상태 | Stateful | Stateless |
| 확장성 | 어려움 | 쉬움 ⭐ |
| 만료 관리 | 서버 제어 | 토큰 만료 시간 |
| 취소 | 즉시 가능 | 어려움 |
| 사용 | 전통적 웹 | API, 마이크로서비스 |

## 세션 문제점과 해결

### 문제 1: 서버 부하
- 해결: Redis 등 외부 저장소 사용

### 문제 2: 분산 환경
```
서버1 → 세션 A
서버2 → 세션 B (접근 불가)
```
- 해결: Redis 공유 저장소

### 문제 3: 확장성
- 해결: JWT 토큰 방식 사용

## 참고 자료

- [Express Session](https://github.com/expressjs/session)
- [Spring Session](https://spring.io/projects/spring-session)