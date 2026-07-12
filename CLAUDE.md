# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 언어 및 커뮤니케이션 규칙

- **기본 응답 언어**: 한국어
- **코드 주석**: 한국어로 작성
- **커밋 메시지**: 한국어로 작성
- **문서화**: 한국어로 작성
- **변수명/함수명**: 영어 (코드 표준 준수)

## 프로젝트 개요

일본에 거주하는 고모님을 위한 철거공사팀 경리 회계장부 웹앱입니다. 월별 수입/지출을 기록하고 통계로 분석할 수 있는 정적 웹사이트이며, Firebase Firestore를 데이터 저장소로 사용해 여러 기기에서 동일한 데이터를 볼 수 있습니다.

### 주요 기능
- 연도(2025~2035)/월별 거래 입력, 조회, 수정, 일괄 삭제
- 전월 잔액 자동 이월 및 월말 합계 계산
- 통계 페이지: 월별 수입/지출 차트, 누적 수익 추이, 지출 카테고리 분석
- 다크모드/라이트모드 테마 전환
- CSV(엑셀) 다운로드
- 샘플데이터 로드/전체 삭제 토글 버튼

현재 배포 주소: https://daseulkim128.github.io/accounting-app/ (GitHub Pages)

세션 간 개발 맥락(완료된 기능, 트러블슈팅 기록, 설계 결정 이유)은 `ROADMAP.md`에 기록되어 있으니, 새 세션을 시작할 때는 먼저 이 파일을 읽는다.

## 기술 스택

- **HTML5 / CSS3 (Vanilla)**: 각 HTML 파일 안에 `<style>`로 인라인 작성 (별도 CSS 파일 없음, 빌드 도구 없음)
- **JavaScript (Vanilla)**: 각 HTML 파일 안에 `<script>`로 인라인 작성 (프레임워크·번들러 없음)
- **Firebase Firestore**: 거래 데이터 저장소 (CDN compat SDK, `firebase-app-compat.js` / `firebase-firestore-compat.js`)
- **Chart.js 3.9.1**: 통계 페이지 차트 (CDN)
- **로컬 개발 서버**: `python3 -m http.server`
- **배포**: GitHub Pages (정적 파일 그대로 배포, 빌드 단계 없음)

## 프로젝트 구조

```
/
├── accounting.html          # 메인 회계장부 페이지 (거래 입력/조회/수정/삭제)
├── accounting-stats.html    # 통계 분석 페이지 (차트/월별 테이블)
├── index.html                # GitHub Pages 루트 접속 시 accounting.html로 리다이렉트
├── .gitignore                 # .claude/, .DS_Store 제외
├── ROADMAP.md                # 개발 로드맵 — 새 세션 시작 시 먼저 읽을 것
└── CLAUDE.md                 # 이 파일
```

## 개발 환경 설정

빌드 도구나 패키지 설치가 필요 없다. 로컬 서버만 띄우면 된다.

```bash
# 프로젝트 루트에서 실행
python3 -m http.server 8000

# 브라우저에서 접속
open http://localhost:8000/accounting.html
```

## Firebase 설정

Firestore 프로젝트: `accounting-app-f1bca`

- 컬렉션: `transactions` (필드: `year`, `month`, `date`, `type`, `amount`, `desc`, `createdAt`) — 아직 소유자 구분 필드(`userId` 등)가 없어서 이 URL에 접속하는 모든 사용자가 같은 데이터를 공유한다
- 현재 보안 규칙은 테스트 모드(`allow read, write: if true;`) — Firebase Authentication 연동(ROADMAP.md Phase 7)이 다음 작업으로 예정되어 있음
- Firestore 쿼리에서 `where` 2개 + `orderBy`를 함께 쓰면 복합 색인이 필요하므로, 날짜 정렬은 Firestore가 아니라 응답을 받은 뒤 클라이언트(JS)에서 처리한다 (`accounting.html`의 `loadTransactionsFromFirestore` 참고)

## 개발 가이드

### HTML/CSS 작성
- Tailwind가 아닌 순수 CSS 클래스 사용, 다크모드는 `body.dark-mode` 클래스 토글 방식
- 시맨틱 요소보다 기능 위주 구조 (`.container`, `.header`, `.input-section` 등 커스텀 클래스)
- 모바일 반응형은 `@media (max-width: 768px)` 브레이크포인트 하나로 처리

### JavaScript 기능
- Firestore 비동기 호출은 전부 `async/await`로 처리 (콜백 체이닝 지양)
- 두 페이지 모두 페이지 로드 시 Firebase를 초기화하고, `localStorage`는 테마 설정(`theme` 키)에만 사용 — 회계 데이터 자체는 절대 `localStorage`에 저장하지 않는다
- `accounting.html`의 `currentTransactions`는 화면에 표시 중인 월의 데이터 캐시로, 삭제/다운로드 시 재사용 (중복 Firestore 조회 방지)
- 거래 추가/수정은 별도 폼을 만들지 않고 상단 입력 폼을 재사용한다: `editingTransactionId`가 `null`이면 추가 모드, 값이 있으면 수정 모드로 `addTransaction()`이 `.add()`/`.update()`를 분기 처리한다 (`startEditTransaction()`, `cancelEdit()`, `resetTransactionForm()` 참고)

## 배포

**현재 GitHub Pages로 배포 중**: https://daseulkim128.github.io/accounting-app/ (저장소 `daseulkim128/accounting-app`, public)

### 코드 수정을 배포에 반영하는 방법

```bash
cd /Users/daseulkim/workspaces/claude-code-mastery
git add <바뀐 파일>
git commit -m "설명"
git push
```
push 후 GitHub Pages가 자동으로 다시 빌드된다 (보통 1~2분).

### git 저장소 관련 특이사항 (중요)
- `/Users/daseulkim`(홈 디렉토리) 자체가 커밋 없는 git 저장소로 잡혀 있다. 이건 이 프로젝트의 저장소가 아니므로 **절대 홈 디렉토리 루트에서 `git add`/`commit`을 하지 않는다** — 개인 파일이 통째로 커밋될 위험이 있다.
- 이 프로젝트는 `claude-code-mastery/` 폴더 안에 **독립된 중첩 git 저장소**를 별도로 `git init -b main`으로 만들어 사용한다. `git rev-parse --show-toplevel`을 실행해서 결과가 `claude-code-mastery`인지 반드시 먼저 확인하고 커밋할 것.
- `git push`가 `Invalid username or token. Password authentication is not supported`로 실패하면, `gh auth login`은 되어 있어도 git의 credential helper가 연결이 안 된 상태다 → `gh auth setup-git` 실행하면 해결된다.
- GitHub 저장소 생성·Pages 설정은 `gh` CLI(Homebrew로 설치, `brew install gh`)로 자동화했다: `gh repo create`, `gh api repos/{owner}/{repo}/pages`.

### 다른 배포처
- **Netlify/Vercel**: 저장소 연결 후 자동 배포 (현재는 미사용)

## 참고 사항

- ROADMAP.md에서 개발 단계별 계획, 트러블슈팅 기록, 설계 결정 이유 확인 (전월 이월 방식, 통계에서 이월금 제외 이유 등)
- 브라우저 호환성: 최신 Chrome, Firefox, Safari, Edge 지원
