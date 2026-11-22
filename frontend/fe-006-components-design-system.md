---
id: FE-006
title: 공통 컴포넌트와 디자인 시스템 연계
part: frontend
tier: optional
status: review
version: 0.2.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-22
depends_on: [FE-001, DSN-005, DSN-006]
related: [FE-002, DSN-007, DSN-008]
superseded_by: null
traces: [REQ-031, NFR-007]
---

# FE-006 공통 컴포넌트와 디자인 시스템 연계

## 이 문서는

- **목적**: `my-space` 프론트엔드의 Single Source of Truth인 [DESIGN.md](file:///Users/limkeunhyeok/workspace/my-space-front/DESIGN.md) 및 [DSN-005](../design/dsn-005-design-system-tokens.md)의 디자인 토큰을 React 컴포넌트에 바인딩하고, 재사용 공통 컴포넌트(`shared/ui`)의 TypeScript Props 인터페이스 및 시각/동작 규격을 정의한다.
- **대상 독자**: 프론트엔드 개발자, UI/UX 디자이너, AI 에이전트
- **작성 시점**: 프론트엔드 기술 구조([FE-001](fe-001-tech-stack.md)) 및 디자인 시스템 승인 직후.
- **등급**: 선택 — 프로젝트 전역 공통 디자인 토큰과 재사용 컴포넌트를 가진 웹 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [FE-001 프론트엔드 기술 구조](fe-001-tech-stack.md) | React 19 및 Vanilla CSS 토큰 매핑 방식 |
| 선행 | [DSN-005 디자인 시스템과 디자인 토큰](../design/dsn-005-design-system-tokens.md) | CSS 변수 토큰(`--color-*`, 폰트, 간격, 곡률) 정의 |
| 선행 | [DSN-006 컴포넌트 가이드](../design/dsn-006-component-guide.md) | 컴포넌트별 시각 상태와 인터랙션 기준 |
| 관련 | [DESIGN.md](file:///Users/limkeunhyeok/workspace/my-space-front/DESIGN.md) | 프론트엔드 저장소 루트의 최우선 디자인 규격 |
| 관련 | [DSN-007 반응형 디자인](../design/dsn-007-responsive.md) | 모바일 Safe Area Inset 및 뷰포트 적응 규칙 |
| 관련 | [DSN-008 접근성 가이드](../design/dsn-008-accessibility.md) | 명도 대비 4.5:1, 키보드 탭 및 포커스 링 규격 |

## 디자인 토큰 연계 방식 [필수]

외부 UI 프레임워크(Tailwind, shadcn 등) 없이 `apps/web/src/styles.css`(또는 토큰 분리 시 `tokens.css`)에 Figma v2 확정 디자인 토큰을 순수 CSS 커스텀 프로퍼티(`--color-*`, `--radius-*`, `--shadow-*`)로 선언하고 컴포넌트에서 직접 참조한다:

```css
/* apps/web/src/styles.css (DESIGN.md 확정 v2 토큰 요약) */
:root {
  /* Surface & Background */
  --color-surface-canvas: #F7F8FA;
  --color-surface-panel: #FFFFFF;
  --color-surface-subtle: #F1F5F9;
  --color-surface-hover: #F8FAFC;
  --color-surface-active: #EEF2FF;

  /* Text Colors */
  --color-text-primary: #20242C;
  --color-text-secondary: #596273;
  --color-action-on-primary: #FFFFFF;

  /* Brand & Action */
  --color-action-primary: #2457D6;
  --color-action-primary-hover: #1D46B3;

  /* Borders */
  --color-border-subtle: #DDE2EA;
  --color-border-control: #737D8C;
  --color-border-focus: #2457D6;

  /* Semantic Status (Foreground + Surface Tint) */
  --color-status-success: #18794E;
  --color-status-success-bg: #ECFDF3;
  --color-status-warning: #8A5300;
  --color-status-warning-bg: #FEF7E6;
  --color-status-danger: #B42318;
  --color-status-danger-bg: #FEF3F2;

  /* Typography */
  --font-sans: 'Noto Sans KR', -apple-system, BlinkMacSystemFont, system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace;

  /* Geometry (Border Radius) */
  --radius-none: 0px;
  --radius-control: 8px;
  --radius-panel: 12px;
  --radius-full: 9999px;

  /* Elevation Shadows (Level 0~3) */
  --shadow-none: none;
  --shadow-sm: 0 1px 2px rgba(32, 36, 44, 0.05);
  --shadow-md: 0 4px 12px rgba(32, 36, 44, 0.08);
  --shadow-lg: 0 16px 32px rgba(32, 36, 44, 0.14);
  --scrim-backdrop: rgba(0, 0, 0, 0.40);

  /* Shell & Safe Area */
  --sidebar-width: 240px;
  --topbar-height: 68px;
  --safe-area-bottom: env(safe-area-inset-bottom, 34px);
}
```

## 주요 공통 컴포넌트 규격 [필수]

### 1. 버튼 (`Button.tsx`)
- **변형(Variants)**: `primary` (브랜드 블루 채움), `secondary` (경계선 테두리), `ghost` (배경 투명), `danger` (위험 삭제 Solid), `dangerGhost` (경고·연결 해제), `iconOnly` (`40px` 정사각형, `aria-label` 필수)
- **크기(Sizes)**: `sm` (32px, 아이콘 `16~18px`), `md` (40px 기본, 아이콘 `18~20px`) — `DSN-006-03` 확정 2종 규격 (`lg` 48px 공통 규격 없음)
- **상태(States)**: 기본, 호버, 눌림(Pressed), 포커스(`4px` 글로우 / `2px` 아웃라인), 비활성(`disabled`), 로딩(`isLoading` / `aria-busy="true"` - 스피너 표시 및 중복 실행 방지)
- **모바일 터치 영역**: 시각 높이가 `32px` 또는 `40px`여도 모바일 뷰포트에서는 히트 영역을 확장해 최소 `44px × 44px` 터치 타깃을 확보한다.

```typescript
export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger' | 'dangerGhost' | 'iconOnly';
  size?: 'sm' | 'md';
  isLoading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}
```

### 2. 입력 및 텍스트영역 (`TextInput.tsx`, `Textarea.tsx`, `Checkbox.tsx`)
- **기능**: 항상 보이는 상단 라벨, 입력 필드와 수직 `4px` 고정 간격의 하단 도움말/오류 슬롯, 명확한 블루 포커스 링(`--color-border-focus`) 표시.
- **레이아웃 시프트(CLS) 방지**: 오류 상태 전환 시 도움말과 오류 메시지를 동일 슬롯에서 교체해 폼 높이가 밀리지 않도록 유지하며, 오류 시에도 기존 입력값을 보존한다.
- **접근성 및 선택 상태**: `aria-invalid`, `aria-describedby`를 자동 연결하여 스크린 리더에서 오류 원인을 안내한다. `Checkbox`는 `미선택` / `선택됨` / `일부 선택(indeterminate)` / `비활성` 상태를 지원한다.

```typescript
export interface TextInputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  helperText?: string;
  errorMessage?: string;
  isInvalid?: boolean;
}
```

### 3. 모달 다이얼로그 (`ModalDialog.tsx`)
- **용도**: 삭제 확인, 키워드 필터 설정, 일정 등록/수정 등 사용자 의사결정이 필요한 팝업 레이어.
- **규격 및 반응형 전환**: 데스크톱 기본 너비 `440px` (복합 설정 팝업은 `512px` 등 콘텐츠 맞춤), 제목 + 취소/`✕` 닫기 버튼 + 실행 버튼 구성 필수. 모바일(`≤ 767px`, `DSN-007` 기준 통일)에서는 하단 슬라이드 바텀 시트(`max-height: 85vh`, 상단 `16px` 곡률, Safe Area 대응)로 전환한다.
- **접근성 및 인터랙션**:
  - `role="dialog"` 및 `aria-modal="true"`
  - 모달 열림 시 첫 번째 입력 요소로 포커스 자동 이동 및 포커스 트랩(Focus Trap) 유지
  - 키보드 `Escape` 키 또는 배경 오버레이 클릭 시 닫기
  - 모달 닫힘 시 트리거 버튼으로 포커스 복귀

```typescript
export interface ModalDialogProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  description?: string;
  children: React.ReactNode;
  footer?: React.ReactNode;
  size?: 'sm' | 'md' | 'lg' | 'xl';
}
```

### 4. 피드 키워드 필터 설정 팝업 (`FeedKeywordFilterModal`) 및 구독 관리 모달
`DSN-004` 및 `SCR-020` 확정 시안에 따라 피드 모달을 다음 2종으로 구조화한다:
1. **키워드 필터 설정 팝업 (`FeedKeywordFilterModal`, `?modal=keyword-filter`)**:
   - 관심 키워드 가중치(`+1`~`+10`) 추가·수정, 제외 키워드 숨김 규칙 등록, 키워드 삭제 상태를 **단일 팝업 내 상태 전환**으로 처리하며, 상단 `✕`/하단 `취소` 버튼과 `저장하고 닫기` 버튼을 함께 제공한다.
2. **구독 출처 관리 모달 (`FeedSourceModal`, `?modal=feed-sources`)**:
   - 신규 RSS URL 등록, 구독 출처 활성/중지 상태 토글, 출처 및 수집 글 일괄 삭제 확인 인터랙션을 처리한다.

### 5. 파일 드롭존 및 드래그 핸들 (`FileDropzone.tsx`, `SortableFileList.tsx`)
- **용도**: PDF 병합/분할 및 이미지 변환 시 파일 첨부와 순서 변경 (`50MB` 한도 및 로컬/서버 처리 범위 안내).
- **인터랙션 및 접근성**:
  - 드래그 앤 드롭 영역 시각적 피드백 (호버 시 보더 `#2457D6`, 배경 `#EEF2FF` 전환)
  - 파일 행 3가지 시각 상태 구분: 일반 대기(`border: #737D8C`), 활성/드래그 중(`border: 2px solid #2457D6`), 손상·오류(`border: #B42318`, 배경 `#FEF3F2`, `제거` 버튼)
  - 6점 드래그 핸들(`:::`) + **키보드/포인터 대안(`↑` / `↓` 순서 이동 버튼 필수, `Tab`으로 파일 행 진입 후 `Alt + ↑` / `Alt + ↓` 단축키 지원)**
  - 6개 이상 등록 시 목록 영역 `max-height: 320px` 내부 스크롤 적용

### 6. 하단 고정 결과 패널 (`FixedResultPanel.tsx`)
- **용도**: PDF 병합 완료 후 출력 파일명 설정·다운로드 바, 이미지 변환 결과 일괄 다운로드 바.
- **기능**: **출력 파일명 입력 필드(`.pdf` 확장자 자동 부착)**, 총 페이지/압축 결과 요약, 다운로드 및 새 작업 CTA 제공.
- **모바일 대응**:
  - 데스크톱: 화면 하단 중앙 플로팅 바
  - 모바일(375px): 하단 탭 내비게이션(56px) 및 Safe Area Bottom(34px) 위에 겹치지 않도록 `bottom: calc(56px + var(--safe-area-bottom) + 16px)` 오프셋 자동 적용

## 검토 체크리스트

- [x] 모든 컴포넌트가 [DESIGN.md](file:///Users/limkeunhyeok/workspace/my-space-front/DESIGN.md)의 디자인 토큰(라이트 테마, `#F7F8FA`, `#20242C`, `#2457D6`, `68px` 상단바)을 CSS 변수로 참조한다
- [x] 버튼(`sm` 32px / `md` 40px), 입력(CLS 방지 슬롯), 모달(`440px`/`512px`·모바일 768px 미만 바텀 시트), 토스트, 드롭존 등 핵심 컴포넌트 규격이 `DSN-005`·`DSN-006`과 일치한다
- [x] 피드 단일 키워드 필터 설정 팝업·구독 관리 모달 및 PDF 드래그 핸들(`Alt + ↑`/`↓`)·출력 파일명 입력이 Figma v2 확정 시안과 일치한다
- [x] 모달 다이얼로그에 포커스 트랩, ESC 닫기, 포커스 복귀 등 WCAG 2.2 AA 접근성 규격이 반영되어 있다
- [x] 하단 고정 결과 패널에 모바일 Safe Area Bottom 오프셋이 적용되어 있다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-22 | 0.2.0 | 개정된 DESIGN.md 및 DSN-004/005/006(v1.2.1) 확정 토큰·버튼 2종·피드 단일 팝업·접근성 규격 동기화 | @bob (Keunhyeok Lim) |
| 2026-11-12 | 0.1.0 | my-space v2 공통 컴포넌트와 디자인 시스템 연계(FE-006) 최초 작성 | @bob (Keunhyeok Lim) |
