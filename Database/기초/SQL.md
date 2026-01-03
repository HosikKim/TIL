# SQL (Structured Query Language)

## 개념

관계형 데이터베이스를 관리하고 조작하기 위한 **표준화된 프로그래밍 언어**

### 역사
- **1970년대**: IBM에서 개발
- **1980년대**: ANSI/ISO 표준화
- **현재**: 모든 주요 RDBMS에서 사용 (MySQL, Oracle, PostgreSQL 등)

### 특징
- **선언적 언어**: "무엇을" 할지 명시 ("어떻게"는 DBMS가 결정)
- **표준화**: 핵심 문법은 동일, DBMS별 확장 기능 존재
- **강력함**: 복잡한 데이터 조작 및 분석 가능

## SQL 분류

### 1. DDL (Data Definition Language) - 데이터 정의어

**역할**: 데이터베이스 구조(스키마) 정의 및 관리

| 명령어 | 기능 | 예시 |
|--------|------|------|
| CREATE | 객체 생성 | `CREATE TABLE users (...)` |
| ALTER | 구조 수정 | `ALTER TABLE users ADD email VARCHAR(100)` |
| DROP | 객체 삭제 | `DROP TABLE users` |
| TRUNCATE | 데이터 전체 삭제 | `TRUNCATE TABLE users` |
```sql
-- 테이블 생성
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    age INT
);

-- 컬럼 추가
ALTER TABLE users ADD email VARCHAR(100);

-- 테이블 삭제
DROP TABLE users;
```

**특징**:
- 자동 커밋 (Auto Commit) - 롤백 불가
- 스키마에 영향

### 2. DML (Data Manipulation Language) - 데이터 조작어

**역할**: 데이터 조회, 삽입, 수정, 삭제

| 명령어 | 기능 | 예시 |
|--------|------|------|
| SELECT | 조회 | `SELECT * FROM users WHERE age > 20` |
| INSERT | 삽입 | `INSERT INTO users VALUES (1, 'Kim', 25)` |
| UPDATE | 수정 | `UPDATE users SET age = 26 WHERE id = 1` |
| DELETE | 삭제 | `DELETE FROM users WHERE id = 1` |
```sql
-- 데이터 삽입
INSERT INTO users (id, name, age) VALUES (1, '홍길동', 25);

-- 데이터 조회
SELECT name, age FROM users WHERE age >= 20;

-- 데이터 수정
UPDATE users SET age = 26 WHERE id = 1;

-- 데이터 삭제
DELETE FROM users WHERE age < 18;
```

**특징**:
- 트랜잭션 제어 가능 (COMMIT, ROLLBACK)
- 실제 데이터에 영향

### 3. DCL (Data Control Language) - 데이터 제어어

**역할**: 권한 및 접근 제어

| 명령어 | 기능 | 예시 |
|--------|------|------|
| GRANT | 권한 부여 | `GRANT SELECT ON users TO user1` |
| REVOKE | 권한 회수 | `REVOKE SELECT ON users FROM user1` |
```sql
-- 권한 부여
GRANT SELECT, INSERT ON users TO 'developer'@'localhost';

-- 권한 회수
REVOKE INSERT ON users FROM 'developer'@'localhost';

-- 모든 권한 부여
GRANT ALL PRIVILEGES ON database.* TO 'admin'@'%';
```

**특징**:
- 보안 및 권한 관리
- 사용자별 접근 제어

### 4. TCL (Transaction Control Language) - 트랜잭션 제어어

**역할**: 트랜잭션 관리

| 명령어 | 기능 |
|--------|------|
| COMMIT | 변경사항 확정 |
| ROLLBACK | 변경사항 취소 |
| SAVEPOINT | 저장점 생성 |
```sql
-- 트랜잭션 시작
START TRANSACTION;

INSERT INTO users VALUES (1, '김철수', 30);
UPDATE users SET age = 31 WHERE id = 1;

-- 확정
COMMIT;

-- 또는 취소
ROLLBACK;
```

## DDL vs DML vs DCL

| 구분 | DDL | DML | DCL |
|------|-----|-----|-----|
| 대상 | 스키마 (구조) | 데이터 | 권한 |
| 영향 | 테이블 구조 | 저장된 데이터 | 사용자 접근 |
| 트랜잭션 | 자동 커밋 | 수동 커밋 | 즉시 적용 |
| 롤백 | 불가 | 가능 | 재부여/회수 |
| 비유 | 집 설계도 | 집 내부 물건 | 출입 열쇠 |

## 핵심 SQL 문법

### SELECT 기본 구조
```sql
SELECT 컬럼명
FROM 테이블명
WHERE 조건
GROUP BY 그룹화컬럼
HAVING 그룹조건
ORDER BY 정렬컬럼 [ASC|DESC]
LIMIT 개수;
```

### 실행 순서
```
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
```

## 자주 사용하는 기능

### 조건 검색
```sql
-- WHERE 절
SELECT * FROM users WHERE age >= 20 AND name LIKE '김%';

-- IN, BETWEEN
SELECT * FROM users WHERE age IN (20, 25, 30);
SELECT * FROM users WHERE age BETWEEN 20 AND 30;
```

### 정렬 및 제한
```sql
-- 정렬
SELECT * FROM users ORDER BY age DESC, name ASC;

-- 상위 N개
SELECT * FROM users ORDER BY age DESC LIMIT 10;
```

### 집계 함수
```sql
SELECT 
    COUNT(*) AS 총인원,
    AVG(age) AS 평균나이,
    MAX(age) AS 최고령,
    MIN(age) AS 최연소
FROM users;
```

## DELETE vs TRUNCATE vs DROP

| 명령어 | 유형 | 데이터 삭제 | 구조 삭제 | 롤백 | 속도 |
|--------|------|------------|-----------|------|------|
| DELETE | DML | O | X | O | 느림 |
| TRUNCATE | DDL | O | X | X | 빠름 |
| DROP | DDL | O | O | X | 빠름 |
```sql
DELETE FROM users WHERE age < 18;  -- 조건부 삭제, 롤백 가능
TRUNCATE TABLE users;              -- 전체 삭제, 구조 유지
DROP TABLE users;                  -- 테이블 자체 삭제
```

## 학습 팁

1. **DDL → DML → DCL 순서**로 학습
2. **실습 필수**: 직접 쿼리 작성 연습
3. **실행 계획 확인**: `EXPLAIN` 사용
4. **공식 문서 참고**: 각 DBMS별 차이 확인

## 참고 자료

- [W3Schools SQL Tutorial](https://www.w3schools.com/sql/)
- [MySQL 공식 문서](https://dev.mysql.com/doc/)
- [SQL 표준 (ISO/IEC 9075)](https://www.iso.org/standard/63555.html)