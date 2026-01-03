# SQL 조인 (JOIN)

## 개념

두 개 이상의 테이블을 연결하여 데이터를 조회하는 방법

## 조인 종류

### 1. Equi Join (등가 조인)

**정의**: 두 테이블의 컬럼 값이 **정확히 일치**할 때 사용

**연산자**: `=` (등호)
```sql
-- 명시적 JOIN
SELECT e.name, d.department_name
FROM employee e
INNER JOIN department d ON e.department_id = d.department_id;

-- 암묵적 JOIN
SELECT e.name, d.department_name
FROM employee e, department d
WHERE e.department_id = d.department_id;
```

**특징**:
- 가장 일반적인 조인
- PK-FK 관계에 주로 사용
- 인덱스 활용으로 성능 우수

### 2. Non-Equi Join (비등가 조인)

**정의**: 값이 **정확히 일치하지 않는** 조건으로 조인

**연산자**: `BETWEEN`, `>`, `<`, `>=`, `<=`
```sql
-- 급여 등급 매칭
SELECT e.name, e.salary, s.grade
FROM employee e
JOIN salary_grade s 
ON e.salary BETWEEN s.min_salary AND s.max_salary;

-- 범위 조건
SELECT *
FROM orders o
JOIN promotion p 
ON o.order_date >= p.start_date 
AND o.order_date <= p.end_date;
```

**활용**:
- 급여 등급 분류
- 시간대별 데이터 매칭
- 점수 구간 분류

**주의**: 전체 테이블 스캔 발생 가능 → 성능 주의

### 3. Self Join (자기 조인)

**정의**: **같은 테이블**을 두 번 사용하여 조인
```sql
-- 직원-관리자 관계
SELECT 
    e.name AS 직원,
    m.name AS 관리자
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;

-- 같은 부서 직원 찾기
SELECT 
    e1.name AS 직원1,
    e2.name AS 직원2
FROM employees e1
JOIN employees e2 
ON e1.department_id = e2.department_id
WHERE e1.id < e2.id;  -- 중복 제거
```

**활용**:
- 계층 구조 (조직도, 카테고리)
- 같은 테이블 내 비교
- 연속된 데이터 비교

**주의**: 별칭(alias) 필수 사용

### 4. Outer Join (외부 조인)

**정의**: 조인 조건을 **만족하지 않는 데이터도 포함**

#### LEFT OUTER JOIN
```sql
-- 주문하지 않은 고객도 포함
SELECT c.name, o.order_id
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```
- 왼쪽 테이블의 **모든 행** 포함
- 오른쪽 매칭 없으면 **NULL**

#### RIGHT OUTER JOIN
```sql
-- 고객이 없는 주문도 포함
SELECT c.name, o.order_id
FROM customers c
RIGHT JOIN orders o ON c.customer_id = o.customer_id;
```
- 오른쪽 테이블의 **모든 행** 포함
- 왼쪽 매칭 없으면 **NULL**

#### FULL OUTER JOIN
```sql
-- 양쪽 모든 데이터 포함
SELECT c.name, o.order_id
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id;
```
- 양쪽 테이블의 **모든 행** 포함
- MySQL은 미지원 (UNION으로 구현)

## INNER JOIN vs OUTER JOIN

| 구분 | INNER JOIN | OUTER JOIN |
|------|-----------|------------|
| 결과 | 조건 일치하는 행만 | 조건 불일치도 포함 |
| NULL | 없음 | 있음 |
| 데이터 손실 | 있음 | 없음 |
| 사용 | 관계 확실 | 누락 데이터 확인 |
```sql
-- INNER JOIN (교집합)
SELECT * FROM A INNER JOIN B ON A.id = B.id;

-- LEFT JOIN (A 전체 + B 교집합)
SELECT * FROM A LEFT JOIN B ON A.id = B.id;
```

## 조인 비교표

| 조인 유형 | 조건 | 사용 예시 | NULL | 성능 |
|-----------|------|----------|------|------|
| Equi Join | `=` | PK-FK 관계 | X | 빠름 ⭐⭐⭐ |
| Non-Equi Join | 범위 | 급여 등급 | X | 느림 ⭐ |
| Self Join | 자기 자신 | 조직도 | O | 중간 ⭐⭐ |
| Outer Join | 불일치 포함 | 누락 확인 | O | 중간 ⭐⭐ |

## NULL 처리
```sql
-- NULL을 기본값으로 치환
SELECT 
    c.name,
    COALESCE(o.order_id, 0) AS order_id,
    IFNULL(o.total, 0) AS total
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;

-- NULL 필터링
SELECT *
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;  -- 주문하지 않은 고객만
```

## 조인 선택 가이드

**Equi Join**: 
- 정확한 매칭 필요
- PK-FK 관계

**Non-Equi Join**: 
- 범위/구간 매칭
- 등급 분류

**Self Join**: 
- 계층 구조
- 같은 테이블 내 비교

**LEFT JOIN**: 
- 기준 테이블의 모든 데이터 필요
- 가장 많이 사용

**RIGHT JOIN**: 
- LEFT JOIN으로 대체 가능
- 거의 사용 안 함

**FULL JOIN**: 
- 양쪽 모든 데이터 필요
- 데이터 정합성 확인

## 성능 최적화 팁

1. **인덱스 생성**: 조인 키에 인덱스 필수
2. **작은 테이블 먼저**: FROM 절에 작은 테이블 배치
3. **필요한 컬럼만**: `SELECT *` 지양
4. **EXPLAIN 확인**: 실행 계획 분석
```sql
-- 인덱스 생성
CREATE INDEX idx_dept ON employee(department_id);

-- 실행 계획 확인
EXPLAIN SELECT * FROM employee e
JOIN department d ON e.department_id = d.department_id;
```

## 참고 자료

- [MySQL JOIN 문서](https://dev.mysql.com/doc/refman/8.0/en/join.html)
- [SQL Joins Visualizer](https://sql-joins.leopard.in.ua/)