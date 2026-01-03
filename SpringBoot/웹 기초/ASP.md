# ASP (Active Server Pages)

## 개념

Microsoft의 **서버 사이드 스크립팅 기술**로 동적 웹 페이지를 생성하는 플랫폼

### 발전 과정
```
ASP (Classic ASP, 1996)
  ↓
ASP.NET (2002)
  ↓
ASP.NET MVC (2009)
  ↓
ASP.NET Core (2016, 최신)
```

## ASP 버전별 특징

### 1. Classic ASP (1996~)
- VBScript, JScript 사용
- HTML 내 스크립트 삽입
- Windows 서버 전용
- 현재는 레거시

### 2. ASP.NET (2002~)
- .NET Framework 기반
- C#, VB.NET 사용
- 컴파일 방식
- Windows 전용

### 3. ASP.NET Core (2016~, 권장 ✅)
- 크로스 플랫폼 (Windows, Linux, macOS)
- 오픈소스
- 고성능
- 모듈화된 구조

## Classic ASP vs ASP.NET vs ASP.NET Core

| 구분 | Classic ASP | ASP.NET | ASP.NET Core |
|------|------------|---------|--------------|
| 출시 | 1996 | 2002 | 2016 |
| 언어 | VBScript | C#, VB.NET | C# |
| 플랫폼 | Windows | Windows | 크로스 플랫폼 ⭐ |
| 성능 | 느림 | 중간 | 빠름 ⭐⭐⭐ |
| 오픈소스 | ❌ | ❌ | ✅ |
| 사용 | 레거시 | 유지보수 | 현재 개발 ⭐ |

## Classic ASP 예시
```asp
<%@ Language=VBScript %>
<html>
<body>
<%
  Dim name
  name = Request.QueryString("name")
  Response.Write("Hello, " & name & "!")
%>
</body>
</html>
```

## ASP.NET Core 예시

### Controller
```csharp
using Microsoft.AspNetCore.Mvc;

public class HomeController : Controller
{
    // GET: /Home/Index
    public IActionResult Index()
    {
        return View();
    }
    
    // GET: /Home/About
    public IActionResult About()
    {
        ViewData["Message"] = "About Page";
        return View();
    }
    
    // POST: /Home/Contact
    [HttpPost]
    public IActionResult Contact(ContactModel model)
    {
        if (ModelState.IsValid)
        {
            // 처리 로직
            return RedirectToAction("ThankYou");
        }
        return View(model);
    }
}
```

### Razor View (.cshtml)
```html
@model ContactModel

<h2>Contact Form</h2>

<form asp-action="Contact" method="post">
    <div>
        <label asp-for="Name"></label>
        <input asp-for="Name" />
    </div>
    <div>
        <label asp-for="Email"></label>
        <input asp-for="Email" />
    </div>
    <button type="submit">Submit</button>
</form>
```

## 장점

| 장점 | 설명 |
|------|------|
| Microsoft 통합 | Visual Studio, Azure 등과 원활한 연동 |
| 강력한 도구 | IntelliSense, 디버깅 등 개발 도구 지원 |
| 엔터프라이즈 | 대규모 애플리케이션에 적합 |
| 크로스 플랫폼 | ASP.NET Core는 Linux/macOS 지원 |
| 성능 | ASP.NET Core는 매우 빠름 |
| 타입 안정성 | C# 정적 타입 언어 |

## 단점

| 단점 | 설명 |
|------|------|
| Windows 종속 | Classic ASP, ASP.NET은 Windows 전용 |
| 라이선스 비용 | Windows 서버, Visual Studio 비용 |
| 학습 곡선 | .NET 생태계 학습 필요 |
| 초기 성능 | Classic ASP, 초기 ASP.NET 성능 이슈 |

## ASP.NET Core 프로젝트 구조
```
MyWebApp/
├── Controllers/          # 컨트롤러
├── Views/               # 뷰 (Razor)
├── Models/              # 모델
├── wwwroot/             # 정적 파일 (CSS, JS, 이미지)
├── appsettings.json     # 설정 파일
└── Program.cs           # 진입점
```

## ASP vs JSP vs PHP

| 구분 | ASP.NET Core | JSP | PHP |
|------|-------------|-----|-----|
| 언어 | C# | Java | PHP |
| 플랫폼 | 크로스 플랫폼 | 크로스 플랫폼 | 크로스 플랫폼 |
| 서버 | Kestrel | Tomcat | Apache/Nginx |
| 성능 | 매우 빠름 ⭐⭐⭐ | 빠름 ⭐⭐ | 중간 ⭐⭐ |
| 학습 | 높음 | 중간 | 낮음 |
| 사용 | 엔터프라이즈 | 엔터프라이즈 | 웹 사이트 |

## 사용 시나리오

**ASP.NET Core 적합**:
- 엔터프라이즈 애플리케이션
- Microsoft 기술 스택 사용
- 고성능 웹 API
- 마이크로서비스

**다른 기술 고려**:
- 소규모 프로젝트 → PHP, Node.js
- Java 생태계 → Spring Boot
- 빠른 프로토타입 → Python (Django/Flask)

## 최신 트렌드

### ASP.NET Core 8 (2023)
- 최소 API (Minimal API)
- Blazor (C#로 프론트엔드)
- gRPC 지원
- 컨테이너 최적화

### Minimal API 예시
```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");
app.MapGet("/api/users/{id}", (int id) => 
    new User { Id = id, Name = "Test" }
);

app.Run();
```

## 마이그레이션 가이드
```
Classic ASP → ASP.NET Core
1. VBScript → C# 변환
2. 구조 개선 (MVC 패턴)
3. 데이터베이스 연결 변경
4. 배포 환경 준비
```

## 참고 자료

- [ASP.NET Core 공식 문서](https://docs.microsoft.com/aspnet/core)
- [ASP.NET Core GitHub](https://github.com/dotnet/aspnetcore)