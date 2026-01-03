# SQL 함수: 단일행 vs 다중행(그룹) 함수

## 개념

SQL 함수는 처리 단위에 따라 **단일행 함수**와 **다중행 함수**로 구분

## 단일행 함수 (Single-row Function)

**정의**: 각 행마다 **개별적으로 실행**되어 행마다 결과 반환

### 특징
- **입력**: 1개 행 → **출력**: 1개 결과
- 모든 절에서 사용 가능 (SELECT, WHERE, ORDER BY 등)
- 행의 개수 = 결과의 개수

### 주요 함수

| 분류 | 함수 | 설명 | 예시 |
|------|------|------|------|
| 문자 | UPPER() | 대문자 변환 | `UPPER('hello')` → HELLO |
| | LOWER() | 소문자 변환 | `LOWER('HELLO')` → hello |
| | SUBSTR() | 문자열 추출 | `SUBSTR('Hello', 1, 3)` → Hel |
| | LENGTH() | 문자열 길이 | `LENGTH('Hello')` → 5 |
| | CONCAT() | 문자열 결합 | `CONCAT('A', 'B')` → AB |
| 숫자 | ROUND() | 반올림 | `ROUND(123.456, 1)` → 123.5 |
| | CEIL() | 올림 | `CEIL(123.1)` → 124 |
| | FLOOR() | 내림 | `FLOOR(123.9)` → 123 |
| | ABS() | 절댓값 | `ABS(-10)` → 10 |
| 날짜 | NOW() | 현재 시간 | `NOW()` → 2025-01-02 15:30:00 |
| | DATE_FORMAT() | 날짜 포맷 | `DATE_FORMAT(NOW(), '%Y-%m-%d')` |
| | DATEDIFF() | 날짜 차이 | `DATEDIFF('2025-01-10', '2025-01-01')` → 9 |
| NULL | IFNULL() | NULL 대체 | `IFNULL(column, 0)` |
| | COALESCE() | 첫 번째 NOT NULL | `COALESCE(col1, col2, 0)` |
| 조건 | IF() | 조건 분기 | `IF(age >= 20, '성인', '미성년')` |
| | CASE | 다중 조건 | `CASE WHEN ... THEN ... END` |

### 예시
```sql
-- 각 행마다 실행
SELECT 
    name,
    UPPER(name) AS upper_name,
    LENGTH(name) AS name_length,
    ROUND(salary, -3) AS rounded_salary
FROM employees;

-- WHERE 절에서 사용
SELECT * FROM employees
WHERE UPPER(name) LIKE 'KIM%';

-- 중첩 사용 가능
SELECT UPPER(SUBSTR(name, 1, 3)) FROM employees;
```

## 다중행 함수 (Multi-row / Group Function)

**정의**: 여러 행을 그룹으로 묶어 **하나의 결과** 반환

### 특징
- **입력**: N개 행 → **출력**: 1개 결과
- SELECT, HAVING 절에서만 사용
- 주로 GROUP BY와 함께 사용
- NULL 자동 무시 (COUNT(*) 제외)

### 주요 함수

| 함수 | 설명 | NULL 처리 |
|------|------|-----------|
| COUNT() | 행 개수 | 무시 |
| SUM() | 합계 | 무시 |
| AVG() | 평균 | 무시 |
| MAX() | 최댓값 | 무시 |
| MIN() | 최솟값 | 무시 |
| COUNT(*) | 전체 행 개수 | **포함** |

### 예시
```sql
-- 전체 집계
SELECT 
    COUNT(*) AS 총인원,
    COUNT(email) AS 이메일보유자,  -- NULL 제외
    AVG(salary) AS 평균급여,
    MAX(salary) AS 최고급여,
    MIN(salary) AS 최저급여,
    SUM(salary) AS 급여총액
FROM employees;

-- 그룹별 집계
SELECT 
    department_id,
    COUNT(*) AS 인원수,
    AVG(salary) AS 평균급여
FROM employees
GROUP BY department_id
HAVING COUNT(*) >= 5;  -- 그룹 조건

-- 그룹 내 통계
SELECT 
    department_id,
    COUNT(*) AS 인원,
    MAX(salary) - MIN(salary) AS 급여차이
FROM employees
GROUP BY department_id;
```

## 단일행 vs 다중행 함수

| 구분 | 단일행 함수 | 다중행 함수 |
|------|------------|------------|
| 처리 단위 | 행별 개별 처리 | 그룹 단위 처리 |
| 입력 → 출력 | 1 → 1 | N → 1 |
| 사용 위치 | SELECT, WHERE, ORDER BY 등 | SELECT, HAVING |
| 결과 수 | 행 개수만큼 | 그룹 개수만큼 |
| 중첩 | 가능 | 불가 (분석함수 제외) |
| NULL 처리 | 함수별 상이 | 자동 무시 |
| 목적 | 데이터 변환 | 데이터 집계 |

## 함께 사용하기
```sql
-- 단일행 + 다중행 함수 조합
SELECT 
    department_id,
    UPPER(department_name) AS dept_name,  -- 단일행
    COUNT(*) AS emp_count,                -- 다중행
    ROUND(AVG(salary), 0) AS avg_salary   -- 다중행 + 단일행
FROM employees
WHERE YEAR(hire_date) >= 2020             -- 단일행
GROUP BY department_id, department_name
HAVING COUNT(*) > 3                       -- 다중행
ORDER BY AVG(salary) DESC;                -- 다중행
```

## 주의사항

### 1. 혼합 사용 규칙
```sql
-- ❌ 오류: 단일행과 다중행 함수 혼용
SELECT name, COUNT(*) FROM employees;

-- ✅ 올바름: GROUP BY 사용
SELECT name, COUNT(*) 
FROM employees 
GROUP BY name;
```

### 2. WHERE vs HAVING
```sql
-- WHERE: 개별 행 필터링 (그룹 함수 X)
SELECT department_id, AVG(salary)
FROM employees
WHERE salary > 3000  -- ✅ 단일행 조건
GROUP BY department_id;

-- HAVING: 그룹 결과 필터링 (그룹 함수 O)
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 5000;  -- ✅ 그룹 조건
```

### 3. NULL 처리
```sql
-- COUNT(*): NULL 포함
SELECT COUNT(*) FROM employees;  -- 전체 행

-- COUNT(column): NULL 제외
SELECT COUNT(email) FROM employees;  -- email이 NULL인 행 제외

-- 다른 집계 함수: NULL 자동 제외
SELECT AVG(salary) FROM employees;  -- salary가 NULL인 행 제외
```

## 실행 순서
```
FROM → WHERE (단일행) → GROUP BY → HAVING (다중행) → SELECT → ORDER BY
```

## 선택 가이드

**단일행 함수 사용**:
- 각 행의 값을 변환
- 조건 검색 (WHERE)
- 개별 데이터 가공

**다중행 함수 사용**:
- 통계/집계 필요
- 그룹별 요약
- 리포트 생성

## 참고 자료

- [MySQL 함수 레퍼런스](https://dev.mysql.com/doc/refman/8.0/en/functions.html)
- [Oracle SQL 함수](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Functions.html)