# Thymeleaf

## 개념

서버 사이드 Java **템플릿 엔진**으로 HTML에 동적 데이터를 삽입

### 특징
- Spring Boot 기본 템플릿 엔진
- 자연스러운 템플릿 (Natural Templates)
- HTML 파일 그대로 사용 가능

## 자연스러운 템플릿

### JSP vs Thymeleaf

**JSP**:
```jsp
<!-- 브라우저에서 직접 열면 깨짐 -->
<h1><%= user.getName() %></h1>
```

**Thymeleaf**:
```html
<!-- 브라우저에서 직접 열어도 정상 표시 -->
<h1 th:text="${user.name}">기본 이름</h1>
```

**장점**: 디자이너와 협업 용이

## 기본 문법

### 네임스페이스 선언
```html
<html xmlns:th="http://www.thymeleaf.org">
```

### 텍스트 출력
```html
<!-- th:text (이스케이프 O) -->
<p th:text="${user.name}">홍길동</p>

<!-- th:utext (이스케이프 X, HTML 허용) -->
<p th:utext="${html}"></p>

<!-- 인라인 -->
<p>Hello, [[${user.name}]]!</p>
```

### 조건문
```html
<!-- if -->
<div th:if="${user.age >= 20}">
  <p>성인입니다</p>
</div>

<!-- unless (if의 반대) -->
<div th:unless="${user.age >= 20}">
  <p>미성년자입니다</p>
</div>

<!-- switch -->
<div th:switch="${user.role}">
  <p th:case="'ADMIN'">관리자</p>
  <p th:case="'USER'">일반 사용자</p>
  <p th:case="*">기타</p>
</div>
```

### 반복문
```html
<ul>
  <li th:each="item : ${items}" th:text="${item}"></li>
</ul>

<!-- 인덱스 포함 -->
<ul>
  <li th:each="item, stat : ${items}">
    [[${stat.index}]]: [[${item}]]
  </li>
</ul>
```

**stat 변수**:
- `index`: 0부터 시작
- `count`: 1부터 시작
- `first`, `last`: 첫/마지막 여부
- `even`, `odd`: 짝수/홀수 여부

## 표현식

| 표현식 | 설명 | 예시 |
|--------|------|------|
| `${...}` | 변수 | `${user.name}` |
| `*{...}` | 선택 변수 | `*{name}` (th:object와 함께) |
| `#{...}` | 메시지 (국제화) | `#{welcome.message}` |
| `@{...}` | URL | `@{/users/{id}(id=${user.id})}` |
| `~{...}` | Fragment | `~{fragments/header}` |

## 속성

### 기본 속성
```html
<!-- href -->
<a th:href="@{/users/{id}(id=${user.id})}">프로필</a>

<!-- src -->
<img th:src="@{/images/logo.png}" />

<!-- class -->
<div th:class="${user.isAdmin} ? 'admin' : 'user'">

<!-- value -->
<input type="text" th:value="${user.name}" />
```

### 폼 바인딩
```html
<form th:action="@{/users}" th:object="${user}" method="post">
  <input type="text" th:field="*{name}" />
  <input type="email" th:field="*{email}" />
  <button type="submit">등록</button>
</form>
```

## Fragment (재사용)

### Fragment 정의
```html
<!-- fragments/header.html -->
<header th:fragment="header">
  <h1>My Website</h1>
  <nav>...</nav>
</header>
```

### Fragment 사용
```html
<!-- 다른 페이지에서 -->
<div th:replace="fragments/header :: header"></div>
<!-- 또는 -->
<div th:insert="fragments/header :: header"></div>
```

**replace vs insert**:
- `replace`: Fragment로 태그 교체
- `insert`: Fragment를 태그 안에 삽입

## Spring Boot 연동

### 의존성
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### Controller
```java
@Controller
public class UserController {
    
    @GetMapping("/users/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        User user = userService.findById(id);
        model.addAttribute("user", user);
        return "user/profile";  // templates/user/profile.html
    }
}
```

### Template
```html
<!-- templates/user/profile.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title th:text="${user.name} + ' Profile'"></title>
</head>
<body>
    <h1 th:text="'Welcome, ' + ${user.name}"></h1>
    
    <div th:if="${user.isAdmin}">
        <p>관리자 권한</p>
    </div>
    
    <h2>게시글</h2>
    <ul>
        <li th:each="post : ${user.posts}" th:text="${post.title}"></li>
    </ul>
</body>
</html>
```

## 국제화 (i18n)

### messages.properties
```properties
# messages_ko.properties
welcome.message=환영합니다, {0}님!
user.age=나이

# messages_en.properties
welcome.message=Welcome, {0}!
user.age=Age
```

### 사용
```html
<h1 th:text="#{welcome.message(${user.name})}"></h1>
<p th:text="#{user.age} + ': ' + ${user.age}"></p>
```

## 레이아웃

### 공통 레이아웃
```html
<!-- layouts/base.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title th:text="${title}">Default Title</title>
</head>
<body>
    <header th:replace="fragments/header :: header"></header>
    
    <main>
        <div th:replace="${content}"></div>
    </main>
    
    <footer th:replace="fragments/footer :: footer"></footer>
</body>
</html>
```

## Thymeleaf vs JSP

| 구분 | Thymeleaf | JSP |
|------|-----------|-----|
| 자연스러운 템플릿 | ✅ | ❌ |
| Spring Boot | 기본 지원 ⭐ | 추가 설정 |
| 브라우저 직접 열기 | 가능 | 불가능 |
| 디자이너 협업 | 쉬움 ⭐ | 어려움 |
| 학습 곡선 | 낮음 | 중간 |

## 장단점

### 장점
- 자연스러운 HTML
- Spring Boot 기본 지원
- 디자이너 협업 용이
- 다양한 표현식

### 단점
- JSP보다 성능 낮음
- 복잡한 로직 구현 어려움

## 참고 자료

- [Thymeleaf 공식 문서](https://www.thymeleaf.org/)
- [Spring Boot Thymeleaf](https://docs.spring.io/spring-boot/docs/current/reference/html/web.html#web.servlet.spring-mvc.template-engines)