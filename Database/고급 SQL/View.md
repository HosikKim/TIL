# View (뷰)

## 개념

하나 이상의 테이블로부터 유도된 **가상 테이블**

### 특징
- 물리적 데이터 저장 X
- 쿼리 결과를 저장한 논리적 구조
- 실제 테이블처럼 조회 가능
- 기본 테이블 변경 시 뷰도 자동 반영

## 뷰 생성

### 기본 문법
```sql
CREATE VIEW 뷰이름 AS
SELECT 컬럼들
FROM 테이블
WHERE 조건;
```

### 예시
```sql
-- 단순 뷰
CREATE VIEW high_salary_emp AS
SELECT employee_id, name, salary
FROM employees
WHERE salary > 5000000;

-- 조인 뷰
CREATE VIEW emp_dept AS
SELECT e.name, e.salary, d.department_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- 집계 뷰
CREATE VIEW dept_avg_salary AS
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY dept_id;
```

## 뷰 사용
```sql
-- 뷰 조회 (일반 테이블처럼)
SELECT * FROM high_salary_emp;

-- 뷰에 조건 추가
SELECT * FROM high_salary_emp
WHERE name LIKE '김%';

-- 뷰 수정
CREATE OR REPLACE VIEW high_salary_emp AS
SELECT employee_id, name, salary, dept_id
FROM employees
WHERE salary > 6000000;

-- 뷰 삭제
DROP VIEW high_salary_emp;
```

## 장점

### 1. 보안 강화
```sql
-- 급여 정보 숨기기
CREATE VIEW public_emp AS
SELECT employee_id, name, dept_id
FROM employees;  -- salary 컬럼 제외

-- 특정 부서만 노출
CREATE VIEW sales_emp AS
SELECT * FROM employees
WHERE dept_id = 'SALES';
```

### 2. 쿼리 단순화
```sql
-- 복잡한 조인을 뷰로 단순화
CREATE VIEW order_summary AS
SELECT 
    o.order_id,
    c.customer_name,
    p.product_name,
    o.quantity,
    o.price
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id;

-- 간단하게 사용
SELECT * FROM order_summary
WHERE customer_name = '홍길동';
```

### 3. 논리적 독립성
```sql
-- 테이블 구조 변경 시 뷰로 기존 인터페이스 유지
-- employees 테이블에 컬럼 추가되어도
-- 기존 뷰는 그대로 사용 가능
```

## 단점

### 1. 인덱스 없음
- 뷰 자체에는 인덱스 생성 불가
- 기본 테이블의 인덱스 활용

### 2. 뷰 정의 변경 불가
```sql
-- ❌ ALTER VIEW 없음
-- ✅ CREATE OR REPLACE 사용
CREATE OR REPLACE VIEW emp_view AS
SELECT employee_id, name FROM employees;
```

### 3. DML 제약

**수정 불가능한 뷰**:
```sql
-- 집계 함수 포함
CREATE VIEW avg_view AS
SELECT dept_id, AVG(salary) FROM employees GROUP BY dept_id;

-- 조인 포함
CREATE VIEW join_view AS
SELECT * FROM employees e JOIN departments d ON e.dept_id = d.dept_id;

-- DISTINCT 포함
CREATE VIEW distinct_view AS
SELECT DISTINCT dept_id FROM employees;
```

**수정 가능한 뷰**:
```sql
-- 단순 SELECT만 사용
CREATE VIEW simple_view AS
SELECT employee_id, name, salary FROM employees;

-- 데이터 삽입/수정 가능
INSERT INTO simple_view VALUES (101, '김철수', 5000000);
UPDATE simple_view SET salary = 5500000 WHERE employee_id = 101;
```

## View vs Table

| 구분 | View | Table |
|------|------|-------|
| 저장 | 논리적 (SQL만) | 물리적 (데이터) |
| 데이터 | 동적 (실시간 반영) | 정적 (저장됨) |
| 인덱스 | 불가 | 가능 |
| 수정 | 제한적 | 자유로움 |
| 성능 | 느릴 수 있음 | 빠름 |
| 용량 | 없음 | 차지함 |


## 사용 가이드

**뷰 사용 시**:
- 복잡한 쿼리 반복 사용
- 데이터 보안 필요
- 테이블 구조 숨기기

**테이블 사용 시**:
- 성능이 중요한 경우
- 데이터 수정 빈번
- 인덱스 필수

## 참고 자료

- [MySQL View 문서](https://dev.mysql.com/doc/refman/8.0/en/views.html)
- [PostgreSQL View](https://www.postgresql.org/docs/current/sql-createview.html)