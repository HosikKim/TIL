# 유용한 MCP 서버들

실제로 사용해보거나 유용해 보이는 MCP 서버들을 모아둔 문서입니다.

---
**목차**
- [Context7](#context7)
- [GitHub MCP Server](#github-mcp-server)
- [Notion MCP Server](#notion-mcp-server)
- [Figma MCP Server](#figma-mcp-server)

## Context7

**기능**: 최신 프레임워크/라이브러리 문서 검색 및 참조

**주요 기능**:
- 최신 프레임워크 문서를 실시간으로 검색
- Next.js, React, Vue, Tailwind 등 다양한 라이브러리 지원
- 코드 예제와 함께 정확한 API 참조 제공

**유용한 점**:
- AI 모델의 학습 데이터가 오래되었을 때 최신 문법/API 확인 가능
- 공식 문서를 직접 찾아볼 필요 없이 바로 참조 가능

**링크**:
- [Context7 공식 사이트](https://context7.com/)
- [GitHub](https://github.com/upstash/context7)


## GitHub MCP Server

**기능**: GitHub 리포지토리 관리 및 작업 자동화

**주요 기능**:
- 리포지토리 생성, 삭제, 조회
- Issue 및 Pull Request 관리
- 파일 읽기/쓰기 및 커밋 작업
- Branch 생성 및 관리
- Repository 검색 및 포크

**유용한 점**:
- Claude와 대화하면서 바로 GitHub 작업 수행 가능
- 반복적인 GitHub 작업 자동화
- 코드 리뷰나 이슈 관리를 대화형으로 처리

**링크**:
- [GitHub - modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers/tree/main/src/github)
- [MCP 공식 문서](https://modelcontextprotocol.io/)


## Notion MCP Server

**기능**: Notion 워크스페이스 관리 및 콘텐츠 작업 자동화

**주요 기능**:
- 페이지 생성, 읽기, 업데이트, 삭제
- 데이터베이스 조회 및 항목 관리
- 블록 콘텐츠 추가 및 수정
- 페이지 검색 및 필터링
- 페이지 속성 관리

**유용한 점**:
- Claude와 대화하면서 Notion 문서 자동 생성
- 회의록, 보고서 등 반복적인 문서 작성 자동화
- 데이터베이스 항목 일괄 관리 및 업데이트
- 문서 구조화 및 정리 작업 간소화

**링크**:
- [GitHub - makenotion/notion-mcp-server](https://github.com/makenotion/notion-mcp-server)
- [Notion API 문서](https://developers.notion.com/)


## Figma MCP Server

**기능**: Figma 디자인을 코드로 변환하고 디자인 컨텍스트 추출

**주요 기능**:
- 선택한 프레임에서 코드 자동 생성
- 디자인 시스템 변수, 컴포넌트, 레이아웃 데이터 추출
- FigJam 다이어그램 콘텐츠 가져오기
- Make 파일에서 코드 리소스 수집
- Code Connect를 통한 디자인 시스템 컴포넌트 일관성 유지

**유용한 점**:
- 디자인을 직접 코드로 변환하여 개발 시간 단축
- AI 에이전트에게 정확한 디자인 컨텍스트 제공
- 디자인 시스템과 코드베이스 간 일관성 유지
- 프로토타입에서 프로덕션으로의 빠른 전환
- FigJam 아이디어를 바로 개발에 통합

**설정 방법**:
- **데스크톱 서버**: Figma 데스크톱 앱에서 Dev Mode → MCP 서버 활성화
- **원격 서버**: `https://mcp.figma.com/mcp` 엔드포인트 직접 연결

**지원 편집기**: VS Code, Cursor, Windsurf, Claude Code, Android Studio 등

**링크**:
- [Figma MCP 공식 가이드](https://help.figma.com/hc/ko/articles/32132100833559)
- [Figma MCP 개발자 문서](https://developers.figma.com/docs/figma-mcp-server)
- [MCP 카탈로그](https://www.figma.com/mcp-catalog)