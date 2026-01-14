# Repository 패턴

## 개념

데이터 접근 로직을 추상화하여 도메인과 데이터 계층을 분리하는 디자인 패턴

**구조**: Service → Repository → Database

## Repository vs DAO

| 구분 | Repository | DAO |
|------|-----------|-----|
| 관점 | 도메인 중심 | 데이터베이스 중심 |
| 추상화 | 높음 | 낮음 |
| 메서드 | save(), findById() | insert(), selectById() |
| 예시 | JPA Repository | JDBC DAO |

**차이**:
- DAO: "테이블에 insert"
- Repository: "객체를 저장"

## Spring Data JPA

### 기본 메서드 (자동 제공)

- save() - 저장/수정
- findById() - 조회
- findAll() - 전체 조회
- delete() - 삭제
- count() - 개수
- existsById() - 존재 확인

### 쿼리 메서드

**메서드 이름으로 자동 쿼리 생성**:

| 메서드 | 기능 |
|--------|------|
| findByName | 이름으로 검색 |
| findByNameAndAge | AND 조건 |
| findByAgeGreaterThan | 크기 비교 |
| findByNameContaining | 포함 검색 |
| findByAgeOrderByNameAsc | 정렬 |
| existsByEmail | 존재 확인 |
| countByAge | 개수 세기 |

### @Query 어노테이션

**복잡한 쿼리 직접 작성**:
- JPQL (객체 기반)
- Native SQL
- 조인, 집계 함수

## 페이징과 정렬

**Pageable 사용**:
- 페이지 번호, 크기
- 정렬 기준
- 전체 페이지 수 제공

## 장점

| 장점 | 설명 |
|------|------|
| 관심사 분리 | Service는 비즈니스, Repository는 데이터 |
| 테스트 용이 | Mock으로 대체 가능 |
| DB 독립성 | MySQL → PostgreSQL 변경 쉬움 |
| 재사용성 | CRUD 자동 제공 |
| 유지보수 | 한 곳에 집중 |

## 주의사항

### N+1 문제
- 연관 관계 조회 시 발생
- fetch join 또는 @EntityGraph로 해결

### 메서드 이름
- 조건 많으면 너무 길어짐
- @Query 사용 권장

### 트랜잭션
- 수정/삭제는 @Transactional 필수
- Service 계층에서 관리

## 정리

**사용 이유**:
- 데이터 접근 로직 추상화
- 계층 분리
- 테스트 용이
- 생산성 향상

**핵심 가치**:
- CRUD 자동화
- 메서드 이름으로 쿼리 생성
- 페이징/정렬 기본 제공

## 참고 자료

- [Spring Data JPA](https://spring.io/projects/spring-data-jpa)
- [Repository 패턴](https://www.baeldung.com/spring-data-repositories)