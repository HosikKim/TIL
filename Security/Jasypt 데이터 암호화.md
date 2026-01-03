# Jasypt 데이터 암호화

## 개념

**Jasypt (Java Simplified Encryption)**: Spring Boot 설정 파일의 민감 정보를 **암호화**하는 라이브러리

### 목적
- DB 비밀번호, API 키 등 보안 정보 보호
- 설정 파일 안전하게 저장
- 소스 코드 노출 방지

## 의존성 추가

### Maven
```xml

    com.github.ulisesbocchio
    jasypt-spring-boot-starter
    3.0.5

```

### Gradle
```gradle
implementation 'com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5'
```

## Jasypt 설정
```java
import org.jasypt.encryption.StringEncryptor;
import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;
import org.jasypt.encryption.pbe.config.SimpleStringPBEConfig;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class JasyptConfig {
    
    private static final String KEY = "mySecretKey";  // 암호화 키
    private static final String ALGORITHM = "PBEWithMD5AndDES";  // 알고리즘
    
    @Bean("jasyptStringEncryptor")
    public StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        
        config.setPassword(KEY);  // 암호화 키
        config.setAlgorithm(ALGORITHM);  // 알고리즘
        config.setKeyObtentionIterations("1000");  // 반복 횟수
        config.setPoolSize("1");  // 인스턴스 풀 크기
        config.setProviderName("SunJCE");
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator");
        config.setStringOutputType("base64");  // 출력 형식
        
        encryptor.setConfig(config);
        return encryptor;
    }
}
```

## 암호화/복호화 사용

### 1. 암호화 테스트 코드
```java
import org.jasypt.encryption.StringEncryptor;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class JasyptTest {
    
    @Autowired
    private StringEncryptor jasyptStringEncryptor;
    
    @Test
    void encryptTest() {
        String plainText = "myPassword123";
        String encrypted = jasyptStringEncryptor.encrypt(plainText);
        System.out.println("암호화: " + encrypted);
        // 출력: 9L1Pn8gfE2k9tCRoqsn0KA==
        
        String decrypted = jasyptStringEncryptor.decrypt(encrypted);
        System.out.println("복호화: " + decrypted);
        // 출력: myPassword123
    }
}
```

### 2. 설정 파일에 적용 (application.yml)
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: ENC(9L1Pn8gfE2k9tCRoqsn0KA==)  # 암호화된 값
    password: ENC(O9wmREUiJMK9npIJuWNWQg==)  # 암호화된 값
    
# API 키 암호화
api:
  secret-key: ENC(xH3mK9pLq2nR7vT5wZ8yB1cD4f==)
```

**형식**: `ENC(암호화된_텍스트)`

### 3. 코드에서 사용
```java
@Configuration
public class DataSourceConfig {
    
    @Value("${spring.datasource.username}")
    private String username;  // 자동으로 복호화됨
    
    @Value("${spring.datasource.password}")
    private String password;  // 자동으로 복호화됨
    
    @Bean
    public DataSource dataSource() {
        // username, password는 평문으로 사용 가능
        return DataSourceBuilder.create()
            .username(username)
            .password(password)
            .build();
    }
}
```

## 암호화 키 관리

### 1. 환경 변수 사용 (권장 ✅)
```java
@Configuration
public class JasyptConfig {
    
    @Bean("jasyptStringEncryptor")
    public StringEncryptor stringEncryptor() {
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        
        // 환경 변수에서 키 읽기
        config.setPassword(System.getenv("JASYPT_ENCRYPTOR_PASSWORD"));
        config.setAlgorithm("PBEWithMD5AndDES");
        // ...
        
        encryptor.setConfig(config);
        return encryptor;
    }
}
```

**실행 시 환경 변수 설정**:
```bash
# Linux/Mac
export JASYPT_ENCRYPTOR_PASSWORD=mySecretKey
java -jar app.jar

# Windows
set JASYPT_ENCRYPTOR_PASSWORD=mySecretKey
java -jar app.jar

# IDE (IntelliJ)
Run → Edit Configurations → Environment Variables
JASYPT_ENCRYPTOR_PASSWORD=mySecretKey
```

### 2. application.yml에서 참조
```yaml
jasypt:
  encryptor:
    password: ${JASYPT_ENCRYPTOR_PASSWORD}  # 환경 변수
    algorithm: PBEWithMD5AndDES
```

## 온라인 암호화 도구

### 사용법
1. https://www.devglan.com/online-tools/jasypt-online-encryption-decryption 접속
2. **Input Text**: 암호화할 평문 입력 (예: `myPassword123`)
3. **Secret Key**: 암호화 키 입력 (예: `mySecretKey`)
4. **Algorithm**: `PBEWithMD5AndDES` 선택
5. **Iteration Count**: `1000` 입력
6. **Encrypt** 클릭
7. 결과: `9L1Pn8gfE2k9tCRoqsn0KA==`
8. 설정 파일에 `ENC(9L1Pn8gfE2k9tCRoqsn0KA==)` 사용

## CLI 도구 사용

### 암호화
```bash
java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI \
  input="myPassword123" \
  password="mySecretKey" \
  algorithm="PBEWithMD5AndDES"
```

### 복호화
```bash
java -cp jasypt-1.9.3.jar org.jasypt.intf.cli.JasyptPBEStringDecryptionCLI \
  input="9L1Pn8gfE2k9tCRoqsn0KA==" \
  password="mySecretKey" \
  algorithm="PBEWithMD5AndDES"
```

## 알고리즘 종류

| 알고리즘 | 보안 수준 | 속도 | 권장 |
|---------|----------|------|------|
| PBEWithMD5AndDES | 낮음 | 빠름 | ❌ (레거시) |
| PBEWithMD5AndTripleDES | 중간 | 중간 | ⚠️ |
| PBEWithSHA1AndDESede | 중간 | 중간 | ⚠️ |
| PBEWithHMACSHA512AndAES_256 | 높음 | 느림 | ✅ (권장) |

### 고급 알고리즘 사용
```java
config.setAlgorithm("PBEWithHMACSHA512AndAES_256");
config.setKeyObtentionIterations("10000");  // 반복 횟수 증가
config.setProviderName("SunJCE");
```

## 프로필별 암호화 키
```yaml
# application-dev.yml (개발)
jasypt:
  encryptor:
    password: devSecretKey

# application-prod.yml (운영)
jasypt:
  encryptor:
    password: ${JASYPT_ENCRYPTOR_PASSWORD}  # 환경 변수
```

## 주의사항

### 1. 키 관리
```java
// ❌ 하드코딩 금지
config.setPassword("mySecretKey");

// ✅ 환경 변수 사용
config.setPassword(System.getenv("JASYPT_ENCRYPTOR_PASSWORD"));
```

### 2. 소스 코드 노출 방지
- `.gitignore`에 키 파일 추가
- 환경 변수 또는 비밀 저장소 사용
- CI/CD 파이프라인에서 주입

### 3. 암호화 키 분실 주의
- 키 분실 시 복호화 불가능
- 키 백업 필수

## 장점

| 장점 | 설명 |
|------|------|
| 보안 강화 | 민감 정보 암호화 |
| 간편함 | 설정만으로 사용 |
| 투명성 | 자동 복호화 |
| 환경 분리 | 개발/운영 환경별 관리 |

## 실무 가이드

**개발 환경**:
- 평문 사용 (디버깅 편의)
- 또는 간단한 암호화

**운영 환경**:
- 강력한 알고리즘 사용
- 환경 변수로 키 관리
- 키 주기적 교체

**암호화 대상**:
- DB 비밀번호 ✅
- API 키/Secret ✅
- OAuth 토큰 ✅
- 서버 주소 (선택)
- 사용자명 (선택)

## 참고 자료

- [Jasypt GitHub](https://github.com/jasypt/jasypt)
- [jasypt-spring-boot](https://github.com/ulisesbocchio/jasypt-spring-boot)
- [온라인 암호화 도구](https://www.devglan.com/online-tools/jasypt-online-encryption-decryption)