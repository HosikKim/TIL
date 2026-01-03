# Spring Framework

## 개념

Java 기반의 **엔터프라이즈 애플리케이션 개발**을 위한 오픈소스 프레임워크

### 특징
- 경량 컨테이너
- 의존성 주입 (DI)
- 관점 지향 프로그래밍 (AOP)
- POJO 기반 개발

## Spring vs Spring Boot

| 구분 | Spring Framework | Spring Boot |
|------|-----------------|-------------|
| 설정 | 복잡한 XML 설정 | 자동 설정 |
| 서버 | 외부 서버 필요 | 내장 서버 (Tomcat) |
| 의존성 | 수동 관리 | Starter로 간편 관리 |
| 개발 속도 | 느림 | 빠름 ⭐ |
| 학습 곡선 | 높음 | 낮음 |

### Spring Boot 장점
- 빠른 개발 시작
- 최소한의 설정
- 독립 실행 가능 (jar)
- 운영 편의 기능 (모니터링 등)

## 핵심 개념

### 1. IoC (Inversion of Control)
제어의 역전 - 객체 생성과 관리를 프레임워크가 담당

### 2. DI (Dependency Injection)
의존성 주입 - 객체 간 의존 관계를 외부에서 주입

**장점**:
- 결합도 낮춤
- 테스트 용이
- 코드 재사용성

### 3. AOP (Aspect-Oriented Programming)
관점 지향 프로그래밍 - 공통 관심사 분리

**활용**:
- 로깅
- 트랜잭션 관리
- 보안
- 캐싱

### 4. POJO (Plain Old Java Object)
특정 프레임워크에 종속되지 않은 순수 Java 객체

**장점**:
- 단순함
- 테스트 용이
- 유지보수 편리

## Spring 모듈

| 모듈 | 설명 |
|------|------|
| Spring Core | IoC, DI 기능 제공 |
| Spring MVC | 웹 애플리케이션 개발 |
| Spring Data | 데이터베이스 접근 간소화 |
| Spring Security | 인증/인가 |
| Spring Batch | 배치 처리 |
| Spring Cloud | 마이크로서비스 |

## Spring Boot 주요 기능

### 1. 자동 설정 (Auto Configuration)
조건에 따라 자동으로 Bean 설정

### 2. Starter 의존성
관련 라이브러리를 묶어서 제공
- `spring-boot-starter-web`: 웹 개발
- `spring-boot-starter-data-jpa`: JPA
- `spring-boot-starter-security`: 보안

### 3. 내장 서버
Tomcat, Jetty, Undertow 내장

### 4. Actuator
애플리케이션 모니터링 및 관리

## Spring의 장점

- **생산성**: 빠른 개발
- **유연성**: 다양한 기술 통합
- **확장성**: 모듈화된 구조
- **안정성**: 검증된 프레임워크
- **커뮤니티**: 활발한 생태계

## 사용 분야

- 웹 애플리케이션
- RESTful API
- 마이크로서비스
- 배치 처리
- 엔터프라이즈 시스템

## Spring 생태계
```
Spring Framework (핵심)
  ↓
Spring Boot (간편화)
  ↓
Spring Cloud (마이크로서비스)
  ↓
Spring Data, Security, Batch... (확장)
```

## 학습 순서
```
1. Spring Boot 기초
2. IoC, DI 개념
3. Spring MVC
4. Spring Data JPA
5. Spring Security
6. 고급 주제 (AOP, Cloud 등)
```

## 참고 자료

- [Spring 공식 사이트](https://spring.io/)
- [Spring Boot 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/)