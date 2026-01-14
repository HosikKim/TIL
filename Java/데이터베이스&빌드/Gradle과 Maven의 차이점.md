# Maven과 Gradle

## 개념
소스코드를 실행 가능한 형태로 변환하고 의존성을 관리하는 빌드 관리 도구

## 빌드(Build)란?
소스코드 파일을 **컴퓨터에서 실행할 수 있는 독립적인 형태로 변환하는 과정 및 결과**

구체적으로:
* `.java`, `.xml`, `.properties` 같은 파일 및 자원을
* **JVM**이나 **톰캣 같은 WAS**가 인식할 수 있도록
* **패키징**하는 과정 및 결과물

## 목적
* 소스코드를 실행 가능한 애플리케이션으로 자동 변환
* 프로젝트의 의존성(라이브러리) 자동 관리
* 빌드 과정을 표준화하고 자동화
* 배포 가능한 패키지(JAR, WAR) 생성

## Maven

### 특징
* **Java 전용** 프로젝트 관리 도구
* **Lifecycle 관리 목적** 빌드 도구
* XML 기반 설정 파일 (`pom.xml`)
* 일관된 빌드 생명주기 제공

### 주요 강점
* **표준화된 프로젝트 관리**에 뛰어남
* 명확하고 일관된 프로젝트 구조
* 방대한 플러그인 생태계
* 오래된 레거시 프로젝트와의 호환성

### 설정 예시
```xml
<!-- pom.xml -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.2.0</version>
    </dependency>
</dependencies>
```

## Gradle

### 특징
* **프로젝트 구성 관리 및 범용 빌드 툴**
* **Groovy 스크립트** 언어 기반 설정 파일 (`build.gradle`)
* 스프링부트와 안드로이드에서 표준으로 사용
* 다양한 언어 지원: Java, C/C++, Python 등

### 주요 강점
* **Maven에 비해 10~100배 가량 속도가 빠름**
* 유연한 구성과 뛰어난 성능
* 복잡한 빌드 요구사항을 효과적으로 처리
* 증분 빌드(Incremental Build) 지원

### 설정 예시
```groovy
// build.gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web:3.2.0'
}
```

## Maven vs Gradle 비교

| 항목 | Maven | Gradle |
|------|-------|--------|
| **설정 파일** | pom.xml (XML) | build.gradle (Groovy/Kotlin) |
| **지원 언어** | Java 전용 | Java, C/C++, Python 등 다양 |
| **빌드 속도** | 기본 | 10~100배 빠름 |
| **유연성** | 제한적 (표준화 중심) | 높음 (스크립트 기반) |
| **학습 곡선** | 쉬움 (단순함) | 중간 (Groovy 학습 필요) |
| **주요 사용처** | 레거시 프로젝트, 기업 | 스프링부트, 안드로이드 |
| **캐싱** | 제한적 | 강력한 증분 빌드 |
| **가독성** | 낮음 (XML 장황) | 높음 (간결한 DSL) |

## 빌드 생명주기 예시

### Maven Lifecycle
```
compile → test → package → install → deploy
```

### Gradle Tasks
```
compileJava → processResources → classes → jar → build
```

## 의존성 관리 비교

### Maven
```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>library</artifactId>
    <version>1.0.0</version>
    <scope>compile</scope>
</dependency>
```

### Gradle
```groovy
implementation 'com.example:library:1.0.0'
// 훨씬 간결함
```

## 사용 가이드

### Maven을 사용하면 좋은 경우
* **간단한 프로젝트**나 명확한 구조가 필요할 때
* **표준화가 요구되는 프로젝트** (기업, 대규모 조직)
* 레거시 시스템과의 호환성이 중요할 때
* 팀원들이 Maven에 익숙할 때
* XML 기반 설정을 선호할 때

### Gradle을 사용하면 좋은 경우
* **스프링부트** 또는 **안드로이드** 프로젝트
* **빌드 속도가 중요한 대규모 프로젝트**
* 복잡한 빌드 로직이 필요할 때
* 멀티 모듈 프로젝트 (Gradle이 더 효율적)
* 최신 기술 스택을 사용하는 신규 프로젝트
* CI/CD 파이프라인에서 빌드 시간 단축이 필요할 때

## 주요 명령어 비교

| 작업 | Maven | Gradle |
|------|-------|--------|
| 빌드 | `mvn clean install` | `gradle build` |
| 테스트 | `mvn test` | `gradle test` |
| 패키징 | `mvn package` | `gradle jar` |
| 의존성 확인 | `mvn dependency:tree` | `gradle dependencies` |
| 클린 빌드 | `mvn clean` | `gradle clean` |

## 장점 요약

### Maven
* ✅ 일관된 빌드 생명주기
* ✅ 표준화된 프로젝트 구조
* ✅ 방대한 플러그인 생태계
* ✅ 낮은 학습 곡선

### Gradle
* ✅ 빠른 빌드 속도 (10~100배)
* ✅ 유연한 설정 (Groovy DSL)
* ✅ 증분 빌드 지원
* ✅ 다양한 언어 지원
* ✅ 간결한 설정 파일

## 단점 및 주의사항

### Maven
* ⚠️ 느린 빌드 속도
* ⚠️ XML 설정이 장황하고 복잡
* ⚠️ 유연성 부족
* ⚠️ 복잡한 빌드 로직 구현 어려움

### Gradle
* ⚠️ Groovy/Kotlin DSL 학습 필요
* ⚠️ 디버깅이 Maven보다 어려울 수 있음
* ⚠️ 설정이 자유로워 팀 간 일관성 유지 필요

## 최근 트렌드
* 스프링부트 공식 문서는 Gradle을 기본으로 제시
* 새로운 프로젝트 대부분이 Gradle 채택
* 안드로이드는 Gradle이 공식 빌드 도구
* 하지만 많은 기업과 레거시 프로젝트는 여전히 Maven 사용

## 참고 자료
* Maven 공식 문서: https://maven.apache.org/
* Gradle 공식 문서: https://gradle.org/