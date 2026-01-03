# Callback 패턴

## 개념

다른 함수에 **인자로 전달**되어 나중에 실행되는 함수

### 특징
- 비동기 작업 완료 후 실행
- 함수를 일급 객체로 활용
- 실행 순서 제어

## 기본 사용

### 동기 콜백
```javascript
// 배열 메서드
const numbers = [1, 2, 3];
numbers.forEach(function(num) {
  console.log(num);
});

// 화살표 함수로
numbers.forEach(num => console.log(num));
```

### 비동기 콜백
```javascript
function getData(callback) {
  setTimeout(() => {
    const data = "서버 데이터";
    callback(data);
  }, 1000);
}

getData((result) => {
  console.log(result);  // 1초 후 실행
});
```

## 콜백 지옥 (Callback Hell)

### 문제 상황
```javascript
getData(function(a) {
  getMoreData(a, function(b) {
    getMoreData(b, function(c) {
      getMoreData(c, function(d) {
        getMoreData(d, function(e) {
          console.log(e);  // 😱 깊은 중첩
        });
      });
    });
  });
});
```

### 해결 방법
```javascript
// 함수 분리
function step1(data) {
  return getMoreData(data);
}
function step2(data) {
  return getMoreData(data);
}

// Promise 체이닝
getData()
  .then(step1)
  .then(step2)
  .then(result => console.log(result));

// async/await
async function process() {
  const a = await getData();
  const b = await getMoreData(a);
  const c = await getMoreData(b);
  console.log(c);
}
```

## 에러 처리

### 에러 우선 콜백 (Node.js 스타일)
```javascript
function readFile(path, callback) {
  fs.readFile(path, (error, data) => {
    if (error) {
      callback(error, null);  // 에러 발생
    } else {
      callback(null, data);   // 성공
    }
  });
}

// 사용
readFile('file.txt', (err, data) => {
  if (err) {
    console.error('에러:', err);
    return;
  }
  console.log('데이터:', data);
});
```

## 콜백 vs Promise vs Async/Await

| 방식 | 가독성 | 에러 처리 | 사용 |
|------|--------|----------|------|
| Callback | ⭐ | 복잡 | 레거시 |
| Promise | ⭐⭐ | .catch() | 중간 |
| Async/Await | ⭐⭐⭐ | try/catch | 최신 ⭐ |

### 비교 예시
```javascript
// Callback
getData(function(result) {
  console.log(result);
});

// Promise
getData()
  .then(result => console.log(result));

// Async/Await
const result = await getData();
console.log(result);
```

## 장단점

### 장점
- 비동기 작업 처리 가능
- 코드 재사용성
- 유연한 함수 구성

### 단점
- 콜백 지옥 발생 가능
- 에러 처리 복잡
- 가독성 저하

## 실전 예시

### setTimeout
```javascript
setTimeout(() => {
  console.log('1초 후 실행');
}, 1000);
```

### 이벤트 리스너
```javascript
button.addEventListener('click', () => {
  console.log('클릭됨');
});
```

### 배열 메서드
```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
```

## 주의사항

1. **깊은 중첩 지양**: 함수 분리 또는 Promise 사용
2. **에러 처리 필수**: 에러 우선 콜백 패턴 적용
3. **this 바인딩**: 화살표 함수 사용 권장

## 현대적 대안

### Promise
```javascript
function getData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("데이터");
    }, 1000);
  });
}

getData()
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

### Async/Await
```javascript
async function fetchData() {
  try {
    const data = await getData();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

## 참고 자료

- [MDN Callback Function](https://developer.mozilla.org/ko/docs/Glossary/Callback_function)
- [JavaScript.info - Callbacks](https://ko.javascript.info/callbacks)