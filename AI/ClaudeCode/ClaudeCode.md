# Claude Code란?

## Claude Code 개요

Claude Code는 Anthropic에서 개발한 공식 CLI(Command Line Interface) 도구로, 터미널에서 직접 Claude AI와 대화하며 코딩 작업을 수행할 수 있는 에이전틱 코딩 도구입니다.

### 주요 특징

- **터미널 기반**: IDE 없이 터미널에서 직접 사용
- **에이전틱 코딩**: 파일 읽기/쓰기, 명령어 실행 등 실제 작업 수행
- **컨텍스트 인식**: 프로젝트 구조를 이해하고 관련 파일 자동 탐색
- **MCP 지원**: Model Context Protocol을 통한 외부 도구 연동

## 시스템 요구사항 (Windows)

### 필수 요구사항

| 항목 | 요구사항 |
|------|----------|
| 운영체제 | Windows 10/11 |
| Node.js | 18.0.0 이상 |
| RAM | 4GB 이상 권장 |
| 터미널 | PowerShell 또는 Windows Terminal |

### Node.js 설치 확인

```powershell
# Node.js 버전 확인
node --version

# npm 버전 확인
npm --version
```

Node.js가 설치되어 있지 않다면 [공식 사이트](https://nodejs.org/)에서 LTS 버전을 설치합니다.

## 설치 방법

### 공식 설치 스크립트 사용

```powershell
# npm을 통한 전역 설치
npm install -g @anthropic-ai/claude-code
```

### 설치 확인

```powershell
# 버전 확인
claude --version

# 도움말 확인
claude --help
```

### 업데이트

```powershell
# 최신 버전으로 업데이트
npm update -g @anthropic-ai/claude-code
```

## 인증 방법

### Claude Pro 계정 인증

Claude Code를 사용하려면 Claude Pro, Team, 또는 Enterprise 구독이 필요합니다.

```powershell
# Claude Code 최초 실행 시 인증 진행
claude
```

### 인증 과정

1. `claude` 명령어 실행
2. 브라우저가 자동으로 열리며 로그인 페이지로 이동
3. Claude 계정으로 로그인
4. 인증 완료 후 터미널로 돌아옴

### 인증 상태 확인

```powershell
# 현재 인증 상태 확인
claude config
```

## 기본 사용법

### Claude Code 시작

```powershell
# 현재 디렉토리에서 Claude Code 시작
claude

# 특정 디렉토리에서 시작
claude --cwd /path/to/project
```

### 주요 슬래시 명령어

| 명령어 | 설명 |
|--------|------|
| `/init` | 프로젝트 초기화, CLAUDE.md 파일 생성 |
| `/help` | 도움말 표시 |
| `/clear` | 대화 내용 초기화 |
| `/compact` | 대화 내용 요약하여 컨텍스트 절약 |
| `/config` | 설정 확인 및 변경 |
| `/cost` | 현재 세션의 토큰 사용량 확인 |
| `/doctor` | Claude Code 상태 진단 |
| `/quit` | Claude Code 종료 |

### CLAUDE.md 파일

`/init` 명령어로 생성되는 CLAUDE.md는 프로젝트에 대한 컨텍스트를 Claude에게 제공합니다.

```markdown
# CLAUDE.md 예시 내용

- 프로젝트 개요
- 빌드/테스트 명령어
- 코드 구조 설명
- 코딩 컨벤션
```

## 실전 예시

### 예시 1: 코드 분석

```
> 이 프로젝트의 구조를 분석해줘

Claude가 프로젝트 파일들을 탐색하고 구조를 설명합니다.
```

### 예시 2: 파일 생성

```
> src/utils/helper.js 파일을 만들고 날짜 포맷팅 함수를 작성해줘

Claude가 파일을 생성하고 코드를 작성합니다.
(사용자 확인 후 실행)
```

### 예시 3: 버그 수정

```
> login.js에서 발생하는 인증 오류를 찾아서 수정해줘

Claude가 파일을 분석하고 수정 사항을 제안합니다.
```

### 예시 4: Git 작업

```
> 변경사항을 커밋해줘

Claude가 git status 확인 후 적절한 커밋 메시지와 함께 커밋합니다.
```

### 예시 5: 테스트 작성

```
> UserService 클래스에 대한 단위 테스트를 작성해줘

Claude가 테스트 파일을 생성하고 테스트 코드를 작성합니다.
```

## 주의사항

### 권한 관리

- Claude Code는 파일 읽기/쓰기, 명령어 실행 권한을 요청합니다
- 민감한 작업은 사용자 확인 후 실행됩니다
- `.claude/settings.local.json`에서 허용된 명령어 관리 가능

### 보안 고려사항

- API 키, 비밀번호 등 민감한 정보가 포함된 파일 주의
- `.env` 파일 등은 자동으로 제외되지만 확인 필요
- 프로덕션 환경에서의 명령어 실행 시 주의

### 비용 관리

- Claude Pro 구독 시 사용량 제한 존재
- `/cost` 명령어로 토큰 사용량 확인
- 대용량 파일이나 긴 대화는 토큰 소모가 큼

### 네트워크 요구사항

- 인터넷 연결 필수
- 방화벽/프록시 환경에서는 추가 설정 필요할 수 있음

## 참고 자료

- [Claude Code 공식 문서](https://docs.anthropic.com/en/docs/claude-code)
- [Anthropic 공식 사이트](https://www.anthropic.com/)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
