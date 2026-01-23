# Github Actions

## 개념
Github 저장소에서 발생하는 이벤트를 트리거로 자동화 작업을 실행하는 CI/CD 플랫폼

## 목적
* 코드 푸시/PR 시 자동으로 테스트 실행
* 빌드, 배포 과정을 자동화
* 코드 품질 유지 및 버그 조기 발견
* 반복 작업 자동화로 개발 생산성 향상

## Workflow 구조

| 요소 | 설명 | 예시 |
|------|------|------|
| **name** | 워크플로우 이름 | Example CI |
| **on** | 트리거 이벤트 | push, pull_request |
| **jobs** | 실행할 작업들 | test, build, deploy |
| **runs-on** | 실행 환경 | ubuntu-latest, windows-latest |
| **steps** | 작업 단계 | checkout, install, test |

## 기본 예시 (Node.js 프로젝트)

```yaml
name: Example CI

# 트리거: main 브랜치에 push/PR 발생 시
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    # 1. 코드 체크아웃
    - uses: actions/checkout@v4
    
    # 2. Node.js 설치
    - name: Use Node.js 22.x
      uses: actions/setup-node@v4
      with:
        node-version: 22.x
        cache: 'npm'
    
    # 3. 의존성 설치
    - run: npm ci
    
    # 4. 테스트 실행
    - run: npm test

     # 배포
 # deploy:
 #    # 테스트 성공해야 실행
 #    needs: test             
 #    runs-on: ubuntu-latest
 #    # 메인 브랜치에 push 할때만 배포
 #    if: github.ref == 'refs/heads/main'  
 #    steps:
 #      - uses: actions/checkout@v4
 #      - name: Build
 #        run: npm run build   # 빌드 파일 생성
 #      - name: Deploy to GitHub Pages
 #        uses: peaceiris/actions-gh-pages@v3
 #        with:
            # 민감한 정보(API 키, 토큰) 안전하게 사용
 #          github_token: ${{ secrets.GITHUB_TOKEN }}
 #          publish_dir: ./dist
```

## 주요 Actions

| Action | 용도 |
|--------|------|
| `actions/checkout` | Git 저장소 코드를 가상 환경으로 가져옴 |
| `actions/setup-node` | Node.js 설치 및 캐시 설정 |
| `actions/setup-java` | Java/Gradle/Maven 설정 |
| `actions/upload-artifact` | 빌드 결과물 업로드 |

## CI/CD 파이프라인

```
코드 Push/PR
    ↓
Trigger 발동
    ↓
가상 환경 생성 (ubuntu-latest)
    ↓
코드 Checkout
    ↓
의존성 설치 (npm ci)
    ↓
테스트 실행 (npm test)
    ↓
결과 피드백 (성공/실패)
```

## 실행 환경

| OS | 키워드 |
|----|--------|
| Ubuntu | ubuntu-latest, ubuntu-22.04 |
| Windows | windows-latest, windows-2022 |
| macOS | macos-latest, macos-13 |

## 장점
* Github과 완전 통합 (별도 서버 불필요)
* 무료 사용량 제공 (Public: 무제한, Private: 월 2000분)
* YAML 기반의 간단한 설정
* 다양한 사전 제작 Actions 활용 가능

## 주의사항
* Private 저장소는 사용량 제한 확인 필요
* Secrets 관리 필수 (API Key, Token 등)
* 무한 루프 방지 (workflow 내에서 push 시 주의)

## 사용 가이드

### 시작 방법
1. `.github/workflows/` 폴더 생성
2. `{이름}.yml` 파일 작성
3. Push → Actions 탭에서 실행 확인

## 참고 자료
* [Github Actions 공식 문서](https://docs.github.com/en/actions)
* [Marketplace](https://github.com/marketplace?type=actions) - 사전 제작 Actions