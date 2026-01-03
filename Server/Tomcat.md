# Tomcat

## 개념

Java 웹 애플리케이션을 실행하는 **오픈소스 WAS (Web Application Server)**

### 특징
- Apache Software Foundation 개발
- Servlet/JSP 실행 환경 제공
- 웹 서버 기능 포함
- 경량, 무료

## 주요 기능

### 1. 서블릿 컨테이너
Java Servlet과 JSP를 실행하는 환경
```
Java 웹 애플리케이션 (.war) → Tomcat → HTTP 응답
```

### 2. 웹 서버 기능
- 정적 파일 제공 (HTML, CSS, JS)
- HTTP 요청/응답 처리
- 단독 실행 가능

### 3. 내장 서버
Spring Boot의 기본 내장 서버
```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
        // 내장 Tomcat 자동 실행
    }
}
```

## Tomcat vs 다른 서버

| 구분 | Tomcat | Apache/Nginx | JBoss/WebLogic |
|------|--------|--------------|----------------|
| 분류 | WAS | 웹 서버 | 엔터프라이즈 WAS |
| Servlet/JSP | ✅ | ❌ | ✅ |
| 정적 처리 | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| 동적 처리 | ✅ | ❌ | ✅ |
| 비용 | 무료 | 무료 | 유료 |
| 규모 | 중소 | 모든 규모 | 대규모 |

## 디렉토리 구조
```
tomcat/
├── bin/           # 실행 파일
├── conf/          # 설정 파일 (server.xml, web.xml)
├── lib/           # 라이브러리
├── logs/          # 로그 파일
├── webapps/       # 애플리케이션 배포 디렉토리
└── work/          # JSP 컴파일 결과
```

## 배포 방식

### WAR 파일 배포
```bash
# WAR 파일을 webapps에 복사
cp myapp.war /tomcat/webapps/

# 접속: http://localhost:8080/myapp
```

### Spring Boot 내장
```java
// 별도 설치 불필요, 내장 Tomcat 자동 실행
SpringApplication.run(Application.class, args);
```

## 실행 및 관리
```bash
# 시작
./bin/startup.sh   # Linux/Mac
startup.bat        # Windows

# 종료
./bin/shutdown.sh
shutdown.bat
```

## Apache + Tomcat 연동
```
클라이언트
  ↓
Apache (정적 파일 처리)
  ↓
Tomcat (동적 처리)
```

**장점**: 역할 분리로 성능 향상

## 장단점

| 장점 | 단점 |
|------|------|
| 무료 오픈소스 | 엔터프라이즈 기능 제한 |
| 경량, 빠른 시작 | 정적 처리 성능 낮음 |
| 설치/설정 간편 | 클러스터링 제한적 |
| Java 표준 준수 | - |

## 사용 시나리오

**Tomcat 적합**:
- 중소규모 웹 애플리케이션
- Spring Boot 개발
- 학습 및 프로토타입

**다른 WAS 고려**:
- 대규모 엔터프라이즈
- 고급 클러스터링 필요

## 참고 자료

- [Apache Tomcat 공식 사이트](https://tomcat.apache.org/)