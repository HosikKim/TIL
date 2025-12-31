# Claude Desktop에 MCP 서버 추가하기


## 실습 환경

- OS: [Windows]
- Node.js 버전: [v18.17.1] <- 20버전 이상 추천

## 사전 준비

### 1. Claude Desktop 설치
- [Claude Desktop 다운로드](https://claude.ai/download)
- 설치 후 로그인

### 2. Node.js 설치 확인
```bash
node --version
npm --version
```

## MCP 서버 추가 과정

### 1. 설정 파일 위치 찾기
- 왼쪽 상단위 [파일 -> 설정 -> 개발자 -> 구성편집] 으로 설정 파일 확인

**macOS**
```bash
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Windows**
```bash
%APPDATA%\Claude\claude_desktop_config.json
```

**Linux**
```bash
~/.config/Claude/claude_desktop_config.json
```

### 2. 설정 파일 편집

`claude_desktop_config.json` 파일을 열어서 다음과 같이 수정:

```json
{
  "mcpServers": {
    "서버이름": {
      "command": "node",
      "args": [
        "/path/to/your/mcp-server/build/index.js"
      ]
    }
  }
}
```


### 3. Claude Desktop 재시작

설정 파일을 저장한 후 Claude Desktop을 완전히 종료하고 다시 실행.

## 연결 확인

왼쪽 상단위 [파일 -> 설정 -> 개발자 -> 구성편집] 다시 들어가서 로컬 MCP 서버 상태 확인


## 배운 점

- MCP 서버는 설정 파일에 등록해야 Claude가 인식할 수 있다
- 설정 변경 후 반드시 재시작 필요
- 여러 개의 MCP 서버를 동시에 사용 가능

