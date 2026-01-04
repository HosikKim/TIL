# Context7 MCP 연결 실패 트러블슈팅

## 문제 상황
- **증상**: Context7 MCP 서버가 "Server disconnected" 오류와 함께 연결 실패
- **발생 시점**: 이전에 잘 작동하던 MCP가 특정 시점부터 갑자기 작동 중단

## 오류 로그 분석

### 1차 오류: npm 폴더 누락 (12월 30일)
```
npm ERR! code ENOENT
npm ERR! syscall lstat
npm ERR! path C:\Users\Kim\AppData\Roaming\npm
npm ERR! errno -4058
npm ERR! enoent ENOENT: no such file or directory, lstat 'C:\Users\Kim\AppData\Roaming\npm'
```

**원인**: npm 글로벌 패키지 폴더가 삭제되거나 손상됨

### 2차 오류: API 키 문제 (12월 31일)
```
Invalid API key. Please check your API key. 
API keys should start with 'ctx7sk' prefix.
```

**원인**: API 키가 잘못 설정됨 (잘못된 형식 → `ctx7sk-my-api-key` 형식으로 수정 필요)

### 3차 오류: Node.js 버전 문제 (1월 3일~)
```
npm WARN EBADENGINE Unsupported engine {
npm WARN EBADENGINE   package: 'commander@14.0.2',
npm WARN EBADENGINE   required: { node: '>=20' },
npm WARN EBADENGINE   current: { node: 'v18.17.1', npm: '9.6.7' }
npm WARN EBADENGINE }

SyntaxError: Unexpected token 'with'
    at ESMLoader.moduleStrategy (node:internal/modules/esm/translators:119:18)

Node.js v18.17.1
```

**핵심 원인**: Node.js v18.17.1은 Context7 MCP가 요구하는 최소 버전(v20)보다 낮음

## 해결 방법

### ✅ 최종 해결책: Node.js 업그레이드

Context7 MCP는 **Node.js 20 이상**을 요구합니다.

#### 방법 1: 공식 웹사이트에서 설치
1. https://nodejs.org/ 접속
2. **LTS 버전** (현재 v20.x 또는 v22.x) 다운로드
3. 설치 프로그램 실행
4. PC 재시작

#### 방법 2: nvm-windows 사용 (권장)
```bash
# PowerShell 관리자 권한으로 실행

# Node.js 20 설치
nvm install 20

# Node.js 20으로 전환
nvm use 20

# 버전 확인
node --version
# v20.x.x 이상이어야 함
```

### 추가 정리 단계

```bash
# npm 캐시 정리
npm cache clean --force

# (선택사항) npx 캐시 정리
npx clear-npx-cache
```

### Claude 설정 확인

Claude Desktop 설정 파일 위치:
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

설정 예시:
```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": [
        "-y",
        "@upstash/context7-mcp",
        "--api-key",
        "ctx7sk-my-api-key"
      ]
    }
  }
}
```

**중요**: API 키는 반드시 `ctx7sk-` 접두사로 시작해야 함

### 재시작

1. Claude 앱 **완전 종료**
2. Claude 재시작
3. MCP 연결 상태 확인

## 검증

다음 명령어로 정상 작동 확인:

```bash
# Node.js 버전 확인
node --version
# ✅ v20.0.0 이상

# npm 버전 확인
npm --version
# ✅ 9.0.0 이상

# Context7 MCP 수동 실행 테스트 (선택사항)
npx -y @upstash/context7-mcp --api-key ctx7sk-my-api-key
```

## 트러블슈팅 체크리스트

- [ ] Node.js 버전이 20 이상인가?
- [ ] API 키가 `ctx7sk-` 접두사로 시작하는가?
- [ ] Claude 설정 파일이 올바른가?
- [ ] Claude 앱을 재시작했는가?
- [ ] npm 캐시를 정리했는가?

## 참고 정보

### MCP 로그 위치
- **Windows**: `%APPDATA%\Claude\logs\`

### 유용한 디버깅 명령어
```bash
# Node.js 버전 확인
node --version

# npm 설치 경로 확인
npm config get prefix

# npx 캐시 위치 확인
npm config get cache
```

## 교훈

1. **의존성 버전 확인**: MCP 서버 설치 시 Node.js 버전 요구사항 확인 필수
2. **로그 분석**: 오류 메시지의 변화 패턴을 추적하면 근본 원인 파악 가능
3. **점진적 해결**: 여러 문제가 겹쳐있을 수 있으므로 하나씩 해결
4. **환경 정리**: 캐시 정리와 재시작은 많은 문제를 해결

## 관련 링크

- [Context7 공식 문서](https://context7.io)
- [MCP 디버깅 가이드](https://modelcontextprotocol.io/docs/tools/debugging)
- [Node.js 다운로드](https://nodejs.org/)
- [nvm-windows](https://github.com/coreybutler/nvm-windows)

---

**작성일**: 2026-01-04  
**해결 소요 시간**: 약 5일 (12/30 ~ 1/4)  
**최종 해결책**: Node.js v18 → v20 업그레이드