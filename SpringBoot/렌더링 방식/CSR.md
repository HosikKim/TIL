# CSR (Client-Side Rendering)

## 개념

클라이언트(브라우저)에서 JavaScript로 페이지를 **동적으로 렌더링**하는 방식

## 동작 방식
```
1. 클라이언트 → 서버: 페이지 요청

2. 서버 → 클라이언트: 최소한의 HTML + JS
   <div id="root"></div>  <!-- 비어있음 -->
   <script src="app.js"></script>

3. 브라우저:
   - JavaScript 다운로드 및 실행
   - API로 데이터 요청
   - DOM 생성 및 렌더링 ✅
```

## 예시

### React
```javascript
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch('/api/users')
      .then(res => res.json())
      .then(data => {
        setUsers(data);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <div>Loading...</div>;
  
  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  );
}
```

## 장단점

### 장점
| 장점 | 설명 |
|------|------|
| 빠른 인터랙션 | 페이지 이동 시 새로고침 없음 ⭐ |
| 서버 부하 감소 | 렌더링을 클라이언트에서 처리 |
| 부드러운 UX | SPA 구현 가능 |

### 단점
| 단점 | 설명 |
|------|------|
| 느린 초기 로딩 | JS 다운로드 + 실행 필요 |
| SEO 불리 | 빈 HTML로 크롤링 어려움 ⭐ |
| JavaScript 필수 | JS 비활성화 시 작동 안 됨 |

## SSR vs CSR

| 구분 | SSR | CSR |
|------|-----|-----|
| 초기 HTML | 완성됨 | 비어있음 |
| 초기 로딩 | 빠름 ⭐ | 느림 |
| 페이지 이동 | 느림 (전통적) | 빠름 ⭐ |
| SEO | 좋음 ⭐ | 나쁨 |
| 서버 부하 | 높음 | 낮음 ⭐ |

## SPA (Single Page Application)

CSR의 대표적 형태
```
전통적: 페이지1 → 새로고침 → 페이지2 → 새로고침

SPA:     페이지1 → (JS) → 페이지2 → (JS) → 페이지3
         (새로고침 없이 화면 전환)
```

### React Router 예시
```javascript
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users" element={<Users />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## 적합한 경우

**CSR 사용**:
- 관리자 대시보드
- 채팅 애플리케이션
- 소셜 미디어 피드
- 실시간 협업 도구

**SSR 사용**:
- 뉴스, 블로그 (SEO 중요)
- 전자상거래 상품 페이지
- 마케팅 랜딩 페이지

## 성능 최적화

### Code Splitting
```javascript
// 필요할 때만 로드
const Users = React.lazy(() => import('./Users'));

<Suspense fallback={<div>Loading...</div>}>
  <Users />
</Suspense>
```

### 번들 크기 줄이기
```javascript
// ❌ 전체 라이브러리
import _ from 'lodash';

// ✅ 필요한 것만
import debounce from 'lodash/debounce';
```

## SEO 개선 방법

1. **SSR 하이브리드** (Next.js): 중요 페이지만 SSR
2. **프리렌더링**: 빌드 시 HTML 생성
3. **Dynamic Rendering**: 크롤러 감지 → SSR

## 주요 프레임워크

| 프레임워크 | 특징 |
|-----------|------|
| React | 가장 인기, 생태계 풍부 |
| Vue | 간단, 학습 곡선 낮음 |
| Angular | 엔터프라이즈급 |
| Svelte | 경량, 빠른 성능 |

## 참고 자료

- [React 공식 문서](https://react.dev/)
- [Vue 공식 문서](https://vuejs.org/)