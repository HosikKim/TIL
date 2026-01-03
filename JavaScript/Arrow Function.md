# Arrow Function (화살표 함수)

## 개념

ES6에서 도입된 **간결한 함수 표현 방식**

## 문법

### 기본 형태
```javascript
// 일반 함수
function add(a, b) {
  return a + b;
}

// 화살표 함수
const add = (a, b) => a + b;
```

### 다양한 형태
```javascript
// 매개변수 없음
const greet = () => "Hello";

// 매개변수 1개 (괄호 생략 가능)
const double = x => x * 2;
const double2 = (x) => x * 2;  // 괄호 있어도 됨

// 매개변수 2개 이상
const add = (a, b) => a + b;

// 본문이 여러 줄
const calculate = (a, b) => {
  const sum = a + b;
  return sum * 2;
};

// 객체 반환 (괄호 필수)
const makePerson = (name, age) => ({ name, age });
```

## 주요 특징

### 1. 간결한 문법
```javascript
// 일반 함수 (5줄)
function multiply(a, b) {
  return a * b;
}

// 화살표 함수 (1줄)
const multiply = (a, b) => a * b;
```

### 2. 암시적 반환
```javascript
// return 생략 가능 (한 줄)
const square = x => x * x;

// return 필요 (여러 줄)
const square = x => {
  return x * x;
};
```

### 3. this 바인딩
```javascript
// 일반 함수: 자신만의 this
function Person() {
  this.age = 0;
  setInterval(function() {
    this.age++;  // ❌ this는 window
  }, 1000);
}

// 화살표 함수: 외부 this 사용
function Person() {
  this.age = 0;
  setInterval(() => {
    this.age++;  // ✅ this는 Person
  }, 1000);
}
```

### 4. 콜백에서 유용
```javascript
// 배열 메서드
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);

// 이벤트 핸들러
button.addEventListener('click', () => {
  console.log('Clicked!');
});
```

## 일반 함수 vs 화살표 함수

| 구분 | 일반 함수 | 화살표 함수 |
|------|----------|------------|
| this | 동적 바인딩 | 상위 스코프 this |
| arguments | 있음 | 없음 |
| 생성자 | 가능 (new) | 불가능 |
| 메서드 | 적합 | 부적합 |
| 콜백 | 사용 가능 | 적합 ⭐ |

## 사용 불가능한 경우

### 1. 생성자 함수
```javascript
// ❌ 에러 발생
const Person = (name) => {
  this.name = name;
};
new Person('Kim');  // Error!
```

### 2. 메서드 정의
```javascript
const obj = {
  name: 'Kim',
  // ❌ this가 obj가 아님
  sayName: () => {
    console.log(this.name);  // undefined
  }
};

// ✅ 일반 함수 사용
const obj = {
  name: 'Kim',
  sayName() {
    console.log(this.name);  // Kim
  }
};
```

### 3. arguments 객체
```javascript
// ❌ arguments 없음
const sum = () => {
  console.log(arguments);  // Error!
};

// ✅ Rest 파라미터 사용
const sum = (...args) => {
  console.log(args);  // [1, 2, 3]
};
sum(1, 2, 3);
```

## 사용 가이드

**화살표 함수 사용**:
- 콜백 함수
- 배열 메서드 (map, filter 등)
- 간단한 유틸 함수
- this가 상위 스코프를 가리켜야 할 때

**일반 함수 사용**:
- 객체 메서드
- 생성자 함수
- arguments 필요할 때
- this가 동적으로 바뀌어야 할 때

## 참고 자료

- [MDN Arrow Functions](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Arrow_functions)