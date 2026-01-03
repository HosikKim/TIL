# CGI (Common Gateway Interface)

## 개념

웹 서버와 외부 프로그램 간 데이터를 주고받기 위한 **표준 인터페이스**

### 역할
- 정적 HTML을 넘어 동적 콘텐츠 생성
- 웹 서버가 외부 프로그램 실행 및 결과 반환

## 동작 방식
```
1. 클라이언트 → 웹 서버: CGI 프로그램 요청
   (예: http://example.com/cgi-bin/hello.pl)

2. 웹 서버 → CGI 프로그램: 새 프로세스 생성 및 실행

3. CGI 프로그램: 요청 처리 및 결과 생성
   - 환경 변수로 요청 정보 전달
   - 표준 출력으로 응답 생성

4. 웹 서버 → 클라이언트: 결과 반환
```

## CGI 프로그램 예시

### Perl
```perl
#!/usr/bin/perl
print "Content-type: text/html\n\n";
print "<html>\n<body>\n";
print "<h2>Hello World!</h2>\n";
print "</body>\n</html>\n";
```

### Python
```python
#!/usr/bin/python
print("Content-type: text/html\n")
print("<html><body>")
print("<h2>Hello World!</h2>")
print("</body></html>")
```

### 실행
```bash
# 웹 서버 설정 (Apache)
# /cgi-bin/ 디렉토리에 스크립트 배치
# 실행 권한 부여
chmod +x hello.pl

# 접속
http://example.com/cgi-bin/hello.pl
```

## 환경 변수

CGI 프로그램은 환경 변수로 요청 정보를 받음
```perl
# Perl 예시
print "요청 메서드: $ENV{'REQUEST_METHOD'}\n";
print "쿼리 문자열: $ENV{'QUERY_STRING'}\n";
print "사용자 IP: $ENV{'REMOTE_ADDR'}\n";
```

| 환경 변수 | 설명 |
|----------|------|
| REQUEST_METHOD | GET, POST 등 |
| QUERY_STRING | URL 쿼리 파라미터 |
| CONTENT_TYPE | 요청 본문 타입 |
| REMOTE_ADDR | 클라이언트 IP |

## 장단점

### 장점
- 간단한 구현
- 다양한 언어 지원 (Perl, Python, C 등)
- 언어 독립적

### 단점
- **요청마다 새 프로세스 생성** → 높은 서버 부하
- 느린 응답 속도
- 메모리 비효율
- 확장성 제한

## CGI의 문제점과 대안

### 문제점
```
요청 1 → 프로세스 생성 → 실행 → 종료
요청 2 → 프로세스 생성 → 실행 → 종료  (매번 반복)
요청 3 → 프로세스 생성 → 실행 → 종료
```

### 대안 기술

| 기술 | 특징 |
|------|------|
| FastCGI | 프로세스 재사용으로 성능 향상 |
| mod_perl | Apache에 Perl 내장 |
| mod_php | Apache에 PHP 내장 |
| Servlet | Java 기반, 프로세스 재사용 |
| ASP/ASP.NET | Microsoft 기술 |
| Node.js | 비동기 이벤트 기반 |

## CGI vs 현대 기술

| 구분 | CGI | Servlet/JSP | PHP | Node.js |
|------|-----|------------|-----|---------|
| 프로세스 | 매번 생성 | 재사용 | 재사용 | 단일 프로세스 |
| 성능 | 느림 ⭐ | 빠름 ⭐⭐⭐ | 빠름 ⭐⭐ | 매우 빠름 ⭐⭐⭐ |
| 메모리 | 비효율 | 효율적 | 효율적 | 효율적 |
| 사용 | 거의 없음 | Java 웹 | 널리 사용 | 널리 사용 |

## 역사적 의의

### 1990년대 초반
- 최초의 동적 웹 콘텐츠 생성 기술
- 방명록, 게시판, 카운터 등 구현

### 공헌
- 서버 사이드 프로그래밍 개념 정립
- 동적 웹의 시작
- 현대 웹 기술의 기초

### 현재
- 레거시 시스템에만 존재
- 대부분 현대 기술로 대체
- 학습 목적으로만 사용

## CGI → 현대 웹 기술 발전
```
CGI (1993)
  ↓
FastCGI, mod_perl (성능 개선)
  ↓
Servlet/JSP (Java, 1997)
  ↓
PHP, ASP (간편화, 2000년대)
  ↓
Node.js, Python Django/Flask (현대)
  ↓
Spring Boot, Next.js (현재)
```

## 참고 자료

- [CGI 스펙](https://www.w3.org/CGI/)
- [RFC 3875 - CGI 표준](https://www.rfc-editor.org/rfc/rfc3875)