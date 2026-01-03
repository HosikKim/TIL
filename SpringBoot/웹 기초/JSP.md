# JSP (JavaServer Pages)

## 개념

HTML 내에 Java 코드를 삽입하여 **동적 웹 페이지**를 생성하는 서버 사이드 기술

### 특징
- Servlet 기반
- MVC 패턴에서 **View** 역할
- Java EE 표준 기술

## 동작 방식
```
1. 클라이언트 → JSP 요청 (.jsp)
2. 서버: JSP → Servlet 변환 (최초 1회)
3. 서버: Servlet 컴파일 → .class
4. 서버: Servlet 실행 → HTML 생성
5. 서버 → 클라이언트: HTML 응답
```

**특징**: 최초 실행 시 느림 (변환/컴파일), 이후 빠름

## 기본 문법

### 1. 스크립트 요소
```jsp
<%-- 선언문: 변수, 메서드 선언 --%>
<%!
  int count = 0;
  public int getCount() {
    return count++;
  }
%>

<%-- 스크립틀릿: Java 코드 --%>
<%
  String name = "홍길동";
  int age = 25;
%>

<%-- 표현식: 값 출력 --%>
<p>이름: <%= name %></p>
<p>나이: <%= age %></p>
```

### 2. 지시자
```jsp
<%-- page: JSP 설정 --%>
<%@ page language="java" contentType="text/html; charset=UTF-8" %>

<%-- include: 파일 포함 --%>
<%@ include file="header.jsp" %>

<%-- taglib: 태그 라이브러리 --%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```

### 3. 내장 객체
```jsp
<%
  request.getParameter("name");    // 요청 파라미터
  response.sendRedirect("url");    // 리다이렉트
  session.setAttribute("key", val); // 세션
  out.println("출력");              // 출력
%>
```

| 객체 | 설명 |
|------|------|
| request | HTTP 요청 |
| response | HTTP 응답 |
| session | 세션 |
| application | 애플리케이션 |
| out | 출력 스트림 |

### 4. 표현 언어 (EL)
```jsp
${param.name}           <%-- request.getParameter("name") --%>
${sessionScope.user}    <%-- session.getAttribute("user") --%>
${user.name}            <%-- user.getName() --%>
```

### 5. JSTL
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<%-- 조건문 --%>
<c:if test="${age >= 20}">
  <p>성인입니다</p>
</c:if>

<%-- 반복문 --%>
<c:forEach var="item" items="${list}">
  <p>${item}</p>
</c:forEach>
```

## 전체 예시
```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Example</title>
</head>
<body>
    <%-- 스크립틀릿 --%>
    <%
      String name = request.getParameter("name");
      if (name == null) name = "Guest";
    %>
    
    <%-- 표현식 --%>
    <h1>Welcome, <%= name %>!</h1>
    
    <%-- EL --%>
    <p>Name: ${param.name}</p>
    
    <%-- JSTL --%>
    <c:if test="${not empty param.name}">
      <p>Hello, ${param.name}!</p>
    </c:if>
</body>
</html>
```

## JSP vs Servlet vs Thymeleaf

| 구분 | JSP | Servlet | Thymeleaf |
|------|-----|---------|-----------|
| 역할 | View | Controller | View |
| 문법 | HTML + Java | Java | HTML + 속성 |
| 가독성 | 중간 | 낮음 | 높음 ⭐ |
| 디자이너 협업 | 어려움 | 불가능 | 쉬움 ⭐ |
| Spring Boot | 지원 | 지원 | 기본 ⭐ |

## MVC 패턴에서의 JSP
```
Model (JavaBeans, DAO)
  ↓
Controller (Servlet)
  ↓
View (JSP) ← 여기서 JSP 사용
```

### 예시
```java
// Servlet (Controller)
@WebServlet("/user")
public class UserServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) {
        User user = userService.getUser(1);
        request.setAttribute("user", user);
        request.getRequestDispatcher("/user.jsp").forward(request, response);
    }
}
```
```jsp
<!-- JSP (View) -->
<h1>${user.name}</h1>
<p>Age: ${user.age}</p>
```

## 장단점

### 장점
- Java 생태계 활용
- Servlet보다 간편한 View 작성
- 표준 기술

### 단점
- HTML과 Java 혼재 (가독성 저하)
- 디자이너 협업 어려움
- 최신 프론트엔드 기술과 부조화

## 현재 상황

**사용 감소 중**:
- Spring Boot → Thymeleaf 권장
- React, Vue 등 프론트엔드 분리
- REST API + SPA 구조 선호

**여전히 사용**:
- 레거시 시스템
- 기존 Java 웹 애플리케이션

## 참고 자료

- [JSP 스펙](https://projects.eclipse.org/projects/ee4j.jsp)
- [JSTL 문서](https://eclipse-ee4j.github.io/jakartaee-tutorial/)