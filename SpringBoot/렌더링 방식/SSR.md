# SSR (Server-Side Rendering)

## 개념

서버에서 HTML을 **완성하여** 클라이언트에 전송하는 렌더링 방식

## 동작 방식
```
1. 클라이언트 → 서버: 페이지 요청

2. 서버:
   - 데이터 조회 (DB, API)
   - HTML 생성 (템플릿 + 데이터)

3. 서버 → 클라이언트: 완성된 HTML 전송

4. 브라우저:
   - HTML 파싱 및 렌더링
   - 사용자가 콘텐츠 확인 가능 ✅

5. 브라우저:
   - JavaScript 다운로드 및 실행
   - 하이드레이션 (정적 → 동적)

6. 완전한 인터랙티브 페이지 ✅
```

## SSR 과정 상세

### 1. 서버에서 렌더링
```
요청 → 서버 (데이터 조회) → HTML 생성 → 전송
```

### 2. 하이드레이션 (Hydration)
```
정적 HTML + JavaScript = 동적 페이지
```

**하이드레이션**: 서버에서 받은 정적 HTML에 JavaScript로 이벤트 핸들러 등을 연결하는 과정

## SSR 예시

### Node.js + Express
```javascript
const express = require('express');
const app = express();

app.get('/user/:id', (req, res) => {
  // 1. 데이터 조회
  const user = getUserFromDB(req.params.id);
  
  // 2. HTML 생성
  const html = `
    <html>
      <head><title>${user.name}</title></head>
      <body>
        <h1>${user.name}</h1>
        <p>Age: ${user.age}</p>
      </body>
    </html>
  `;
  
  // 3. HTML 전송
  res.send(html);
});

app.listen(3000);
```

### JSP (Java)
```jsp
<%
  User user = userService.getUser(request.getParameter("id"));
%>
<html>
  <head><title><%= user.getName() %></title></head>
  <body>
    <h1><%= user.getName() %></h1>
    <p>Age: <%= user.getAge() %></p>
  </body>
</html>
```

### React SSR (Next.js)
```javascript
// pages/user/[id].js
export async function getServerSideProps({ params }) {
  // 서버에서 실행
  const user = await fetchUser(params.id);
  
  return {
    props: { user }
  };
}

export default function UserPage({ user }) {
  return (
    <div>
      <h1>{user.name}</h1>
      <p>Age: {user.age}</p>
    </div>
  );
}
```

## 장단점

### 장점

| 장점 | 설명 |
|------|------|
| 빠른 초기 로딩 | 완성된 HTML 즉시 표시 |
| SEO 최적화 | 검색 엔진이 콘텐츠 크롤링 가능 ⭐ |
| 저사양 기기 | 클라이언트 부담 적음 |
| 소셜 공유 | 메타 태그 정보 표시 가능 |

### 단점

| 단점 | 설명 |
|------|------|
| 서버 부하 | 요청마다 HTML 생성 |
| 느린 TTFB | Time To First Byte 증가 |
| 페이지 이동 | 전체 새로고침 필요 (전통적 SSR) |
| 개발 복잡도 | 서버/클라이언트 코드 관리 |

## SSR vs CSR

| 구분 | SSR | CSR |
|------|-----|-----|
| 렌더링 위치 | 서버 | 클라이언트 (브라우저) |
| 초기 로딩 | 빠름 ⭐ | 느림 (빈 HTML) |
| 이후 이동 | 느림 (전통적) | 빠름 ⭐ |
| SEO | 좋음 ⭐ | 나쁨 (크롤링 어려움) |
| 서버 부하 | 높음 | 낮음 ⭐ |
| 적합 | 콘텐츠 중심 | 인터랙티브 앱 |

### 비교 예시

**SSR**:
```
요청 → 서버 (HTML 생성) → HTML 전송 → 즉시 표시 ✅
```

**CSR**:
```
요청 → 빈 HTML 전송 → JS 다운로드 → 데이터 요청 → 렌더링 ✅
```

## SSR 적합한 경우

**SSR 사용**:
- 뉴스, 블로그 (SEO 중요)
- 전자상거래 (제품 상세)
- 마케팅 랜딩 페이지
- 포트폴리오, 회사 소개

**CSR 사용**:
- 관리자 대시보드
- 채팅 애플리케이션
- 소셜 미디어 피드
- 실시간 협업 도구

## 현대적 SSR

### 1. Next.js (React)
```javascript
// 모든 페이지 SSR
export async function getServerSideProps() {
  const data = await fetchData();
  return { props: { data } };
}
```

### 2. Nuxt.js (Vue)
```javascript
export default {
  async asyncData() {
    const data = await fetchData();
    return { data };
  }
}
```

### 3. SvelteKit (Svelte)
```javascript
export async function load({ params }) {
  const data = await fetchData(params.id);
  return { data };
}
```

## 하이브리드 렌더링

### SSG (Static Site Generation)
```
빌드 시 HTML 생성 → CDN 캐싱 → 빠른 서빙
```

**장점**: SSR의 SEO + CSR의 속도

### ISR (Incremental Static Regeneration)
```
정적 생성 + 주기적 재생성
```

**Next.js 예시**:
```javascript
export async function getStaticProps() {
  const data = await fetchData();
  
  return {
    props: { data },
    revalidate: 60  // 60초마다 재생성
  };
}
```

## 전통적 SSR vs 현대적 SSR

| 구분 | 전통적 (JSP, PHP) | 현대적 (Next.js) |
|------|------------------|-----------------|
| 페이지 이동 | 전체 새로고침 | SPA 방식 (빠름) |
| 하이드레이션 | 없음 | 있음 |
| 라우팅 | 서버 | 클라이언트 + 서버 |
| 상태 관리 | 어려움 | React 상태 유지 |

## 참고 자료

- [Next.js SSR 문서](https://nextjs.org/docs/basic-features/pages#server-side-rendering)
- [React Server Components](https://react.dev/blog/2023/03/22/react-labs-what-we-have-been-working-on-march-2023#react-server-components)