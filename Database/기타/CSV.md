# CSV (Comma-Separated Values)

## 개념

텍스트 기반의 **단순한 데이터 저장 및 교환 형식**

### 특징
- 쉼표(,)로 데이터 구분
- 사람이 읽고 수정 가능
- 거의 모든 프로그램에서 지원

## 구조
```csv
이름,나이,이메일
홍길동,25,hong@example.com
김철수,30,kim@example.com
이영희,28,lee@example.com
```

| 요소 | 설명 |
|------|------|
| 레코드(행) | 한 줄 = 하나의 데이터 |
| 필드(열) | 쉼표로 구분된 각 값 |
| 헤더 | 첫 번째 줄 (열 이름) |
| 구분자 | 쉼표(,), 탭(\t), 세미콜론(;) |

## 장점

| 장점 | 설명 |
|------|------|
| 단순성 | 텍스트 에디터로 편집 가능 |
| 호환성 | Excel, DB 등 대부분 지원 |
| 경량 | 파일 크기 작음 |
| 범용성 | 플랫폼/언어 독립적 |
| 속도 | 빠른 처리 |

## 단점

| 단점 | 설명 |
|------|------|
| 제한적 구조 | 계층 데이터 표현 불가 |
| 타입 없음 | 모든 데이터가 문자열 |
| 특수문자 처리 | 쉼표, 줄바꿈 처리 복잡 |
| 표준 부재 | 인코딩, 구분자 등 불명확 |

## 특수문자 처리
```csv
"이름","설명","가격"
"홍길동","안녕하세요, 반갑습니다","10,000"
"김철수","He said ""Hello""","20,000"
```

**규칙**:
- 쉼표 포함 → 큰따옴표로 감싸기
- 따옴표 포함 → 두 번 쓰기 (`""`)
- 줄바꿈 포함 → 큰따옴표로 감싸기

## 프로그래밍 언어별 처리

### Python
```python
import csv

# 읽기
with open('data.csv', 'r', encoding='utf-8') as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row['이름'], row['나이'])

# 쓰기
with open('output.csv', 'w', encoding='utf-8', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(['이름', '나이'])
    writer.writerow(['홍길동', '25'])
```

### Java
```java
// OpenCSV 라이브러리
try (CSVReader reader = new CSVReader(new FileReader("data.csv"))) {
    String[] nextLine;
    while ((nextLine = reader.readNext()) != null) {
        System.out.println(nextLine[0] + " " + nextLine[1]);
    }
}
```

### JavaScript
```javascript
// Papa Parse 라이브러리
Papa.parse(file, {
    header: true,
    complete: function(results) {
        console.log(results.data);
    }
});
```

### SQL (MySQL)
```sql
-- CSV 파일 가져오기
LOAD DATA INFILE '/path/to/file.csv'
INTO TABLE users
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

-- CSV 파일로 내보내기
SELECT * FROM users
INTO OUTFILE '/path/to/output.csv'
FIELDS TERMINATED BY ',' 
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

## CSV vs 다른 형식

| 형식 | CSV | JSON | XML | Excel |
|------|-----|------|-----|-------|
| 구조 | 평면 | 계층 | 계층 | 평면 |
| 크기 | 작음 | 중간 | 큼 | 큼 |
| 가독성 | 높음 | 중간 | 낮음 | 높음 |
| 타입 | 없음 | 있음 | 있음 | 있음 |
| 복잡도 | 낮음 | 중간 | 높음 | 중간 |

### 예시 비교
```csv
// CSV
이름,나이
홍길동,25
```
```json
// JSON
[
  {"이름": "홍길동", "나이": 25}
]
```
```xml


  
    홍길동
    25
  

```

## 데이터베이스 연동

### CSV → DB
```python
import pandas as pd
import sqlite3

# CSV 읽기
df = pd.read_csv('users.csv')

# DB에 저장
conn = sqlite3.connect('database.db')
df.to_sql('users', conn, if_exists='replace', index=False)
```

### DB → CSV
```python
# DB 조회
df = pd.read_sql_query("SELECT * FROM users", conn)

# CSV로 저장
df.to_csv('output.csv', index=False, encoding='utf-8')
```

## 사용 사례

| 분야 | 활용 |
|------|------|
| 데이터 교환 | 시스템 간 데이터 이동 |
| 데이터 분석 | pandas, Excel 분석 |
| 설정 파일 | 간단한 구성 정보 |
| 로그 | 시계열 데이터 기록 |
| 백업 | DB 테이블 백업 |

## 모범 사례

**CSV 생성 시**:
- UTF-8 인코딩 사용
- 헤더 행 포함
- 일관된 구분자
- 특수문자는 큰따옴표로

**CSV 처리 시**:
- 인코딩 명시 (`encoding='utf-8'`)
- 빈 필드 처리 고려
- 대용량은 청크 단위로
- 검증 로직 추가

## 주의사항

### 1. 인코딩 문제
```python
# ❌ 한글 깨짐
df.to_csv('output.csv')

# ✅ UTF-8 BOM (Excel 호환)
df.to_csv('output.csv', encoding='utf-8-sig')
```

### 2. 대용량 처리
```python
# ❌ 메모리 부족
df = pd.read_csv('large.csv')

# ✅ 청크 단위 처리
for chunk in pd.read_csv('large.csv', chunksize=10000):
    process(chunk)
```

### 3. 데이터 타입
```python
# CSV는 모두 문자열
"123" → 문자열
"true" → 문자열

# 타입 변환 필요
df['age'] = df['age'].astype(int)
```


## 참고 자료

- [RFC 4180 - CSV 표준](https://tools.ietf.org/html/rfc4180)
- [Python csv 모듈](https://docs.python.org/3/library/csv.html)
- [Pandas CSV 처리](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html)