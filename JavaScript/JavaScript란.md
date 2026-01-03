# JavaScript

## 개념

웹 페이지에 **동적 기능**을 추가하는 프로그래밍 언어

### 특징
- 브라우저에서 실행
- 인터프리터 언어 (컴파일 불필요)
- 동적 타입 언어
- 객체 기반 언어

## 역사

| 연도 | 주요 사건 |
|------|----------|
| 1995 | Brendan Eich가 Netscape에서 10일 만에 개발 |
| 1997 | ECMAScript 표준화 (ES1) |
| 2009 | Node.js 등장 (서버 사이드) |
| 2015 | ES6 (ES2015) - 현대 JavaScript 시작 |
| 현재 | 매년 새 버전 출시 (ES2016~) |

## 실행 환경

### 1. 클라이언트 사이드 (브라우저)
- 웹 페이지 동작 제어
- DOM 조작
- 이벤트 처리
- AJAX 통신

### 2. 서버 사이드 (Node.js)
- 백엔드 개발
- API 서버 구축
- 파일 시스템 접근
- 데이터베이스 연동

## JavaScript 역할

### 웹 개발에서의 역할
```
HTML (구조) + CSS (디자인) + JavaScript (동작)
```

| 기술 | 역할 | 비유 |
|------|------|------|
| HTML | 구조, 내용 | 건물의 뼈대 |
| CSS | 스타일, 디자인 | 인테리어 |
| JavaScript | 동작, 기능 | 전기, 엘리베이터 |

## 주요 특징

### 1. 동적 타입
변수 타입을 선언 시 지정하지 않음

### 2. 프로토타입 기반
클래스 없이 객체 생성 가능 (ES6부터 class 문법 추가)

### 3. 일급 함수
함수를 변수처럼 사용 가능

### 4. 비동기 처리
콜백, Promise, async/await으로 비동기 작업 처리

### 5. 이벤트 기반
사용자 행동(클릭, 입력 등)에 반응

## JavaScript vs Java

| 구분 | JavaScript | Java |
|------|-----------|------|
| 타입 | 동적 타입 | 정적 타입 |
| 컴파일 | 인터프리터 | 컴파일 필요 |
| 실행 환경 | 브라우저, Node.js | JVM |
| 용도 | 웹 개발 | 엔터프라이즈, 안드로이드 |
| 문법 | 유연함 | 엄격함 |

**공통점**: 이름만 비슷함 (완전히 다른 언어)

## 활용 분야

### 프론트엔드
- 웹 페이지 동적 제어
- SPA (React, Vue, Angular)
- 모바일 앱 (React Native)

### 백엔드
- Node.js 서버
- Express, NestJS

### 데스크톱
- Electron (VS Code, Slack)

### 게임
- Canvas, WebGL
- Phaser, Three.js

## ECMAScript 버전

| 버전 | 연도 | 주요 기능 |
|------|------|----------|
| ES5 | 2009 | strict mode, JSON 지원 |
| ES6 (ES2015) | 2015 | let/const, Arrow Function, Class, Promise |
| ES2016 | 2016 | ** (제곱 연산자) |
| ES2017 | 2017 | async/await |
| ES2020 | 2020 | Optional Chaining (?.) |
| ES2021 | 2021 | Logical Assignment |

## 장단점

### 장점
- 배우기 쉬움
- 브라우저에서 바로 실행
- 풍부한 라이브러리/프레임워크
- 프론트엔드/백엔드 모두 가능
- 활발한 커뮤니티

### 단점
- 브라우저별 호환성 이슈
- 동적 타입으로 인한 런타임 에러
- 싱글 스레드 (CPU 집약 작업 부적합)

## 학습 순서
```
1. 기본 문법 (변수, 연산자, 조건문, 반복문)
2. 함수, 객체
3. DOM 조작
4. 이벤트 처리
5. 비동기 처리 (Promise, async/await)
6. ES6+ 문법
7. 프레임워크 (React, Vue 등)
```

## 참고 자료

- [MDN JavaScript 가이드](https://developer.mozilla.org/ko/docs/Web/JavaScript)
- [ECMAScript 명세](https://tc39.es/ecma262/)