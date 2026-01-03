# MVC 패턴 (Model-View-Controller)

## 개념

애플리케이션을 **Model, View, Controller** 세 가지로 분리하는 디자인 패턴

### 목적
- 관심사의 분리
- 유지보수성 향상
- 코드 재사용성

## 구성 요소
```
       사용자
         ↓
    [Controller] ←→ [Model]
         ↓
       [View]
```

### 1. Model (데이터)
**역할**: 데이터와 비즈니스 로직 관리
```java
// User.java (Model)
public class User {
    private Long id;
    private String name;
    private String email;
    
    // getters, setters
}
```

### 2. View (화면)
**역할**: 사용자에게 데이터 표시
```html
<!-- user.html (View) -->
<h1 th:text="${user.name}">이름</h1>
<p th:text="${user.email}">이메일</p>
```

### 3. Controller (제어)
**역할**: 요청 처리 및 Model-View 연결
```java
// UserController.java (Controller)
@Controller
public class UserController {
    
    @GetMapping("/users/{id}")
    public String getUser(@PathVariable Long id, Model model) {
        // Model에서 데이터 조회
        User user = userService.findById(id);
        
        // View에 데이터 전달
        model.addAttribute("user", user);
        
        // View 이름 반환
        return "user";
    }
}
```

## 동작 흐름
```
1. 사용자 → Controller: 요청 (/users/1)

2. Controller → Model: 데이터 요청
   User user = userService.findById(1);

3. Model → Controller: 데이터 반환
   User{id=1, name="홍길동", email="hong@example.com"}

4. Controller → View: 데이터 전달
   model.addAttribute("user", user);

5. View → 사용자: 화면 표시
   <h1>홍길동</h1>
```

## Spring MVC 예시

### 전체 구조
```java
// Model
@Entity
public class Post {
    @Id
    private Long id;
    private String title;
    private String content;
}

// Service (비즈니스 로직)
@Service
public class PostService {
    @Autowired
    private PostRepository repository;
    
    public Post findById(Long id) {
        return repository.findById(id)
            .orElseThrow(() -> new NotFoundException());
    }
}

// Controller
@Controller
@RequestMapping("/posts")
public class PostController {
    
    @Autowired
    private PostService postService;
    
    @GetMapping("/{id}")
    public String getPost(@PathVariable Long id, Model model) {
        Post post = postService.findById(id);
        model.addAttribute("post", post);
        return "post/detail";  // View 이름
    }
    
    @PostMapping
    public String createPost(@ModelAttribute Post post) {
        postService.save(post);
        return "redirect:/posts";
    }
}

// View (post/detail.html)
<h1 th:text="${post.title}">제목</h1>
<p th:text="${post.content}">내용</p>
```

## 역할 분리

| 구성 요소 | 역할 | 예시 |
|----------|------|------|
| Model | 데이터, 비즈니스 로직 | User, Post 엔티티 |
| View | 화면 표시 | HTML, JSP, Thymeleaf |
| Controller | 요청 처리, 흐름 제어 | UserController |

## 장점

- **유지보수 용이**: 각 부분 독립적 수정
- **재사용성**: Model, View 재사용 가능
- **동시 개발**: 팀원이 역할별 개발
- **테스트 용이**: 각 부분 독립 테스트



## MVC의 변형

### MVC Model 1 (JSP)
```
JSP (View + Controller) → JavaBean (Model)
```

### MVC Model 2 (현재 표준)
```
Servlet (Controller) → JSP (View) → JavaBean (Model)
```

### RESTful API (Model-Controller만)
```
Controller (JSON 반환) → Model
(View 없음, 프론트엔드 분리)
```

## 참고 자료

- [Spring MVC 문서](https://docs.spring.io/spring-framework/reference/web/webmvc.html)