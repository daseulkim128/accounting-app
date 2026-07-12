# 회계장부 앱 ROADMAP

## 프로젝트 개요

일본에 거주하는 고모님이 철거공사팀 경리 업무에 쓸 수 있도록 만든 월별 회계장부 웹앱입니다. Firebase Firestore를 데이터 저장소로 써서 어느 기기에서 접속해도 같은 데이터를 볼 수 있게 하는 것이 핵심 목표입니다.

## 기술 스택

- **HTML5 / CSS3 (Vanilla)**: 빌드 도구 없이 인라인 스타일로 작성
- **JavaScript (Vanilla)**: 프레임워크 없이 인라인 스크립트로 작성
- **Firebase Firestore**: 클라우드 데이터 저장소 (compat SDK, CDN)
- **Chart.js**: 통계 차트 라이브러리 (CDN)
- **배포**: GitHub Pages (빌드 단계 없이 정적 파일 그대로)

## 주요 기능

1. **거래 입력** — 연도/월/일 드롭다운 선택, 수입/지출 구분, 실시간 금액 콤마 포맷팅
2. **거래 조회** — 월별 거래 내역 테이블, 전월 잔액 자동 이월 표시
3. **거래 삭제** — 체크박스 다중 선택 후 일괄 삭제
4. **월말 합계** — 이달 수입/지출/잔액(전월 이월 포함) 카드
5. **통계 페이지** — 월별 수입·지출 막대 차트, 누적 순이익 추이(이월금 제외), 지출 카테고리 도넛 차트, 월별 상세 테이블
6. **연도 전환** — 회계장부/통계 페이지 모두 2025~2035년 선택 가능
7. **CSV 다운로드** — 현재 월 거래 내역을 엑셀에서 열 수 있는 CSV로 다운로드
8. **샘플데이터 버튼** — 비어있으면 2026년 1~7월 예시 데이터 로드, 데이터가 있으면 전체 삭제(토글)
9. **다크모드** — 테마 전환, 로컬 스토리지에 선호도 저장

## 프로젝트 구조

```
/
├── accounting.html          # 메인 회계장부 페이지
├── accounting-stats.html    # 통계 분석 페이지
├── ROADMAP.md                # 이 파일
└── CLAUDE.md                 # Claude Code 작업 가이드
```

## 개발 로드맵

### Phase 1: 기본 회계장부 기능 — ✅ 완료
- [x] 월별 거래 입력 폼 (날짜/유형/금액/설명)
- [x] 거래 내역 테이블 (수입/지출/잔액)
- [x] 월말 합계 카드
- [x] 다크모드 토글
- [x] CSV 다운로드

### Phase 2: 사용성 개선 — ✅ 완료
- [x] 체크박스 다중 선택 삭제 UI
- [x] 날짜순 자동 정렬
- [x] 전월 잔액 이월 표시 (월초 이월금 행)
- [x] 연도별 데이터 구분 (2025~2035년 선택)
- [x] 금액 입력 시 실시간 콤마 포맷팅
- [x] 날짜 입력을 연/월/일 드롭다운으로 변경 + 현재 선택된 연/월 벗어난 입력 방지
- [x] 샘플데이터 로드/전체 삭제 토글 버튼

### Phase 3: 통계 페이지 — ✅ 완료
- [x] 총 수입/지출/순이익/수익률 카드
- [x] 월별 수입·지출 비교 막대 차트
- [x] 누적 순이익 추이 선 차트 (이월금 제외, 순수 월별 실적만 반영)
- [x] 지출 카테고리 분석 도넛 차트 (인건비/연료비/자재비/장비비/보험료/사무비/차량유지비/안전용품)
- [x] 월별 상세 통계 테이블
- [x] 연도 전환 시 실시간 갱신

### Phase 4: Firebase Firestore 연동 — ✅ 완료
- [x] Firebase 프로젝트 생성 및 Firestore 데이터베이스 설정 (`accounting-app-f1bca`, 도쿄 리전)
- [x] compat SDK(CDN) 연동 — `firebase-app-compat.js` / `firebase-firestore-compat.js`
- [x] 거래 추가/조회/삭제를 전부 Firestore 기준으로 통일 (localStorage 의존성 제거)
- [x] 복합 색인 이슈 해결 — Firestore `orderBy` 대신 클라이언트 정렬로 우회
- [x] 통계 페이지도 Firestore에서 직접 데이터 로드
- [x] REST API로 읽기/쓰기/삭제 동작 검증

### Phase 5: 배포 — 🔲 진행 예정
- [x] 프로젝트 폴더 정리 (포트폴리오 관련 파일 제거, 문서 정비)
- [ ] GitHub 저장소 생성 및 push
- [ ] GitHub Pages 활성화
- [ ] 고모님께 배포 URL 공유 및 사용법 안내
- [ ] 다른 기기/브라우저에서 동일 데이터 확인 (Firebase 연동 목적 최종 검증)

### Phase 6: 운영 최적화 (선택) — 🔲 보류
- [ ] Firestore 보안 규칙 강화 (현재는 테스트 모드 `allow read, write: if true;`)
- [ ] 인증 추가 여부 검토 (고모님 혼자 사용하는 동안은 불필요할 수 있음)
- [ ] 데이터 백업 정책 수립

## Firebase 설정 정보

**프로젝트**: `accounting-app-f1bca` (Firestore 리전: 도쿄)

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyCM4RdunrPLGXW3AN2QX8q5oo_ppGlc9aA",
  authDomain: "accounting-app-f1bca.firebaseapp.com",
  projectId: "accounting-app-f1bca",
  storageBucket: "accounting-app-f1bca.firebasestorage.app",
  messagingSenderId: "580950547783",
  appId: "1:580950547783:web:e0214f10ddf507dcfb0062",
  measurementId: "G-6G21YEDHCG"
};
```

**Firestore 데이터 구조** (컬렉션 `transactions`):

| 필드 | 타입 | 설명 |
|------|------|------|
| `year` | number | 거래 연도 |
| `month` | number | 거래 월 |
| `date` | string (`YYYY-MM-DD`) | 거래 날짜 |
| `type` | string | `"income"` 또는 `"expense"` |
| `amount` | number | 거래 금액 |
| `desc` | string | 거래 설명 |
| `createdAt` | timestamp | 문서 생성 시각 |

**보안 규칙** (현재 테스트 모드 — 운영 전환 시 강화 필요):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /transactions/{document=**} {
      allow read, write: if true;
    }
  }
}
```

## 트러블슈팅 기록

Firebase 연동 과정에서 겪었던 문제와 원인 (같은 패턴의 오류가 재발하면 참고):

1. **`Unexpected token 'export'`**: `firebase-app.js`(ES 모듈 전용 빌드)를 `type="module"` 없이 일반 `<script>`로 로드해서 발생. `firebase-app-compat.js` / `firebase-firestore-compat.js`(compat 빌드)는 전역 `firebase` 객체를 노출하므로 일반 `<script>` 태그와 호환된다 — 이걸로 교체해서 해결.
2. **"함수가 정의되지 않음" 에러가 무관한 함수들에서까지 발생**: `loadSampleData()`의 `if/else` 블록에서 닫는 중괄호(`}`) 하나가 빠져 있었음. `<script>` 태그 하나에 구문 오류가 있으면 그 안의 모든 함수가 통째로 정의되지 않는다 — 브라우저가 파싱 단계에서 전체를 포기하기 때문. 중괄호 추가로 해결.
3. **`The query requires an index`**: Firestore는 `where` 조건 2개 이상 + `orderBy`를 함께 쓰는 쿼리에 복합 색인을 요구한다. `orderBy("date")`를 쿼리에서 빼고 응답을 받은 뒤 JS에서 정렬하도록 바꿔서 색인 생성 없이 해결 (한 달치 데이터는 최대 수십 건이라 성능 영향 없음).
4. **`downloadExcel()`이 존재하지 않는 데이터를 참조**: 연도별 구조로 리팩터링하기 전 코드가 남아 `accountingData[currentMonth]`를 읽고 있었음(연도 인덱스 누락). 화면에 표시 중인 캐시(`currentTransactions`)를 쓰도록 수정.

## 주요 설계 결정

| 항목 | 결정 | 이유 |
|------|------|------|
| 데이터 저장소 | Firebase Firestore | 고모님이 여러 기기에서 접속해도 같은 데이터를 보게 하기 위함 |
| 인증 | 당분간 없음 (테스트 모드 규칙) | 고모님 혼자 사용하는 동안은 불필요, 필요시 나중에 추가 |
| 월별 누적 | 전월 잔액 이월 방식 | 실제 회계 장부 방식과 일치 |
| 통계 계산 | 순이익에서 이월금 제외 | 이월금을 포함하면 월별 실적 비교가 왜곡되어 순수 월별 수입/지출만 반영 |
| 날짜 정렬 | Firestore `orderBy` 대신 클라이언트 정렬 | 복합 색인 생성 없이 즉시 동작하게 하기 위함 (위 트러블슈팅 3번 참고) |

## 참고 사항

- Firestore 관련 작업을 할 때는 `where` 다중 조건 + `orderBy` 조합이 복합 색인을 요구한다는 점을 유의한다.
- 배포 완료 후에는 위 "Phase 5" 체크리스트와 실제 배포 URL을 이 문서에 업데이트한다.
