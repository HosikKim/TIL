# Gradle과 Maven 차이점

## 개념

Java 프로젝트의 빌드, 의존성 관리, 배포를 자동화하는 빌드 도구

## 주요 차이점

| 구분 | Maven | Gradle |
|------|-------|--------|
| 설정 파일 | XML (pom.xml) | Groovy/Kotlin DSL (build.gradle) |
| 빌드 속도 | 느림 | 빠름 (최대 100배) |
| 유연성 | 제한적 | 높음 |
| 학습 곡선 | 낮음 | 높음 |
| 멀티 프로젝트 | 상속 구조 | 설정 주입 |

## 설정 파일 비교

### Maven (pom.xml)
```xml
org.springframework.boot
spring-boot-starter-web
3.2.0
```

### Gradle (build.gradle)
```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
}
```

## 장단점

### Maven
**장점**: 간단한 설정, 오래된 역사로 자료 풍부, 표준화된 구조
**단점**: 느린 빌드 속도, 유연성 부족, XML 장황함

### Gradle
**장점**: 빠른 빌드, 높은 유연성, 간결한 문법, Android 공식 빌드 도구
**단점**: 학습 곡선, 상대적으로 적은 레퍼런스

## 선택 기준

- **Maven**: 간단한 프로젝트, 팀이 익숙함, 표준화 선호
- **Gradle**: 대규모 프로젝트, 빌드 성능 중요, Android 개발