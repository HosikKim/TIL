# ON CASCADE

## 개념

외래키(FK) 제약조건에서 **부모 테이블 변경 시 자식 테이블을 자동으로 처리**하는 옵션

### 목적
- 참조 무결성 자동 유지
- 고아 레코드(Orphan Record) 방지
- 데이터 일관성 보장

## CASCADE 종류

### 1. ON DELETE CASCADE

**정의**: 부모 행 삭제 시 자식 행도 **자동 삭제**
```sql
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50)
);

CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(50),
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
        ON DELETE CASCADE
);

-- 부서 삭제 시 소속 직원도 자동 삭제
DELETE FROM departments WHERE dept_id = 10;
-- employees 테이블에서 dept_id=10인 행들도 자동 삭제됨
```

### 2. ON UPDATE CASCADE

**정의**: 부모 PK 수정 시 자식 FK도 **자동 수정**
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON UPDATE CASCADE
);

-- 고객 ID 변경 시 주문 테이블도 자동 갱신
UPDATE customers SET customer_id = 999 WHERE customer_id = 100;
-- orders 테이블의 customer_id=100도 999로 자동 변경
```

## 다른 옵션들

| 옵션 | DELETE 시 동작 | UPDATE 시 동작 |
|------|---------------|----------------|
| CASCADE | 자식 행 삭제 | 자식 FK 수정 |
| SET NULL | 자식 FK를 NULL로 | 자식 FK를 NULL로 |
| SET DEFAULT | 자식 FK를 기본값으로 | 자식 FK를 기본값으로 |
| NO ACTION | 에러 발생 (기본값) | 에러 발생 (기본값) |
| RESTRICT | 에러 발생 | 에러 발생 |

### 예시
```sql
-- SET NULL
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
        ON DELETE SET NULL  -- 부서 삭제 시 dept_id를 NULL로
);

-- NO ACTION (기본값)
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    dept_id INT,
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
        ON DELETE NO ACTION  -- 직원이 있으면 부서 삭제 불가
);
```


## 장점

1. **자동화**: 수동 삭제/수정 불필요
2. **일관성**: 참조 무결성 자동 보장
3. **편의성**: 코드 단순화

## 단점 및 주의사항

### 1. 의도치 않은 대량 삭제
```sql
-- ⚠️ 위험: 부서 하나 삭제로 수백 명 직원 삭제 가능
DELETE FROM departments WHERE dept_id = 10;
```

### 2. 성능 영향
```sql
-- 많은 자식 행 있을 경우 느려짐
DELETE FROM customers WHERE customer_id = 1;
-- orders, order_items, payments 등 연쇄 삭제
```

### 3. 복구 어려움
- 트랜잭션 없이 삭제 시 복구 불가
- 백업 필수

### 4. 트리거와 충돌
```sql
-- CASCADE 설정 테이블에 INSTEAD OF 트리거 생성 불가
-- 둘 중 하나만 선택해야 함
```

## 사용 가이드

**CASCADE 사용 권장**:
- 명확한 종속 관계 (주문-주문상세)
- 부모 삭제 시 자식도 무의미
- 1:N 관계에서 N이 독립적 가치 없음

**CASCADE 지양**:
- 중요한 데이터
- 다대다 관계
- 자식 데이터가 독립적 가치 있음
- 복잡한 비즈니스 로직 필요

**대안**:
```sql
-- 애플리케이션에서 처리
BEGIN;
DELETE FROM order_items WHERE order_id = 1;
DELETE FROM orders WHERE order_id = 1;
COMMIT;

-- 또는 소프트 삭제
UPDATE orders SET deleted_at = NOW() WHERE order_id = 1;
```

## 제약조건 확인
```sql
-- MySQL: 외래키 정보 조회
SELECT 
    CONSTRAINT_NAME,
    TABLE_NAME,
    REFERENCED_TABLE_NAME,
    DELETE_RULE,
    UPDATE_RULE
FROM information_schema.REFERENTIAL_CONSTRAINTS
WHERE TABLE_SCHEMA = 'database_name';
```

## 참고 자료

- [MySQL Foreign Key](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)
- [PostgreSQL Foreign Key](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-FK)