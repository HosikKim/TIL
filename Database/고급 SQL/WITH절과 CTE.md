# WITH절과 CTE (Common Table Expression)

## 개념

쿼리 내에서 **임시로 이름을 붙인 결과 집합**을 정의하는 구문

### 특징
- 쿼리 실행 동안만 존재
- 서브쿼리를 가독성 있게 표현
- 재사용 가능
- 재귀 쿼리 지원

## 기본 문법
```sql
WITH cte_name AS (
    SELECT 컬럼들
    FROM 테이블
    WHERE 조건
)
SELECT * FROM cte_name;
```

## 기본 사용 예시

### 1. 단순 CTE
```sql
-- 부서별 평균 급여
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
)
SELECT 
    e.name, 
    e.salary, 
    d.avg_salary
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_salary;
```

### 2. 다중 CTE
```sql
-- 여러 CTE 동시 정의
WITH 
sales_summary AS (
    SELECT product_id, SUM(quantity) AS total_qty
    FROM sales
    GROUP BY product_id
),
high_sales AS (
    SELECT product_id
    FROM sales_summary
    WHERE total_qty > 100
)
SELECT p.product_name, s.total_qty
FROM products p
JOIN high_sales h ON p.product_id = h.product_id
JOIN sales_summary s ON p.product_id = s.product_id;
```

### 3. 중첩 CTE
```sql
-- CTE가 다른 CTE 참조
WITH 
dept_total AS (
    SELECT dept_id, SUM(salary) AS total_salary
    FROM employees
    GROUP BY dept_id
),
dept_rank AS (
    SELECT 
        dept_id, 
        total_salary,
        RANK() OVER (ORDER BY total_salary DESC) AS ranking
    FROM dept_total
)
SELECT * FROM dept_rank WHERE ranking <= 3;
```

## 재귀 CTE

### 문법
```sql
WITH RECURSIVE cte_name AS (
    -- 기본 쿼리 (Anchor Member)
    SELECT 초기값
    
    UNION ALL
    
    -- 재귀 쿼리 (Recursive Member)
    SELECT 재귀조건
    FROM cte_name
    WHERE 종료조건
)
SELECT * FROM cte_name;
```

### 예시 1: 계층 구조 (조직도)
```sql
-- 직원 계층 조회
WITH RECURSIVE emp_hierarchy AS (
    -- 최상위 (CEO)
    SELECT 
        employee_id, 
        name, 
        manager_id, 
        1 AS level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- 하위 직원
    SELECT 
        e.employee_id, 
        e.name, 
        e.manager_id, 
        eh.level + 1
    FROM employees e
    JOIN emp_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT 
    CONCAT(REPEAT('  ', level - 1), name) AS hierarchy,
    level
FROM emp_hierarchy
ORDER BY level, name;
```

### 예시 2: 숫자 시퀀스
```sql
-- 1부터 10까지 생성
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;
```

### 예시 3: 날짜 범위
```sql
-- 최근 7일간 날짜 생성
WITH RECURSIVE dates AS (
    SELECT CURDATE() AS date
    UNION ALL
    SELECT DATE_SUB(date, INTERVAL 1 DAY)
    FROM dates
    WHERE date > DATE_SUB(CURDATE(), INTERVAL 6 DAY)
)
SELECT * FROM dates ORDER BY date;
```

## CTE vs 서브쿼리

| 구분 | CTE | 서브쿼리 |
|------|-----|----------|
| 가독성 | 높음 | 낮음 (중첩 시) |
| 재사용 | 가능 | 불가 |
| 재귀 | 가능 | 불가 |
| 위치 | 쿼리 상단 | 쿼리 중간 |
```sql
-- 서브쿼리 (복잡)
SELECT e.name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary) 
    FROM employees 
    WHERE dept_id = e.dept_id
);

-- CTE (명확)
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY dept_id
)
SELECT e.name, e.salary
FROM employees e
JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_salary;
```

## CTE vs View

| 구분 | CTE | View |
|------|-----|------|
| 범위 | 쿼리 내 | 데이터베이스 |
| 지속성 | 일시적 | 영구적 |
| 재사용 | 같은 쿼리만 | 모든 쿼리 |
| 권한 | 불필요 | 필요 |

## 장점

1. **가독성**: 복잡한 쿼리를 논리적 단계로 분리
2. **유지보수**: 수정 및 디버깅 용이
3. **재사용**: 같은 쿼리 내 여러 번 참조
4. **재귀**: 계층 구조 처리 가능

## 주의사항
- 과도한 사용은 쿼리 복잡성 증가 가능
- 성능 최적화 시 실행 계획 확인 필요
- 데이터베이스 시스템에 따라 지원 여부와 구문이 다를 수 있음


## 참고 자료

- [MySQL CTE 문서](https://dev.mysql.com/doc/refman/8.0/en/with.html)
- [PostgreSQL CTE](https://www.postgresql.org/docs/current/queries-with.html)