---
id: FE-006
title: 공통 컴포넌트와 디자인 시스템 연계
part: frontend
tier: optional
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-12
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

외부 UI 프레임워크(Tailwind, shadcn 등) 없이 `apps/web/src/app/tokens.css` 파일에 Figma v2 디자인 토큰을 순수 CSS 커스텀 프로퍼티(`--color-*`, `--radius-*`, `--shadow-*`)로 선언하고 컴포넌트에서 직접 참조한다:

```css
/* apps/web/src/app/tokens.css 요약 */
:root {
  /* Surface Colors */
  --color-surface-canvas: #F7F8FA;
  --color-surface-card: #FFFFFF;
  --color-surface-sunken: #F1F3F5;
  --color-surface-overlay: rgba(15, 23, 42, 0.45);

  /* Text Colors */
  --color-text-primary: #0F172A;
  --color-text-secondary: #475569;
  --color-text-tertiary: #94A3B8;
  --color-text-inverse: #FFFFFF;

  /* Brand & Action */
  --color-action-primary: #2457D6;
  --color-action-primary-hover: #1D46B0;
  --color-action-primary-active: #173891;
  --color-action-on-primary: #FFFFFF;

  /* Borders & Shadows */
  --color-border-subtle: #E2E8F0;
  --color-border-focus: #2457D6;
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 16px;
  --radius-full: 9999px;
  --shadow-1: 0 1px 2px 0 rgba(15, 23, 42, 0.04);
  --shadow-2: 0 4px 6px -1px rgba(15, 23, 42, 0.08), 0 2px 4px -2px rgba(15, 23, 42, 0.04);
  --shadow-3: 0 10px 15px -3px rgba(15, 23, 42, 0.08), 0 4px 6px -4px rgba(15, 23, 42, 0.03);
  --shadow-4: 0 20px 25px -5px rgba(15, 23, 42, 0.12), 0 8px 10px -6px rgba(15, 23, 42, 0.04);

  /* Safe Area Bottom */
  --safe-area-bottom: env(safe-area-inset-bottom, 34px);
}
```

## 주요 공통 컴포넌트 규격 [필수]

### 1. 버튼 (`Button.tsx`)
- **변형(Variants)**: `primary` (브랜드 블루 채움), `secondary` (경계선 테두리), `ghost` (배경 투명), `danger` (위험 삭제)
- **크기(Sizes)**: `sm` (32px), `md` (40px, 기본), `lg` (48px)
- **상태(States)**: 기본, 호버, 액티브, 비활성(`disabled`), 로딩(`isLoading` - 스피너 표시 및 클릭 방지)

```typescript
export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
}
```

### 2. 입력 및 텍스트영역 (`TextInput.tsx`, `Textarea.tsx`)
- **기능**: 상단 라벨, 입력 보조 텍스트(Helper Text), 오류 메시지(Error Message) 및 명확한 블루 포커스 링(`--color-border-focus`) 표시.
- **접근성**: `aria-invalid`, `aria-describedby`를 자동 연결하여 스크린 리더에서 오류 원인 안내.

```typescript
export interface TextInputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string;
  helperText?: string;
  errorMessage?: string;
  isInvalid?: boolean;
}
```

### 3. 모달 다이얼로그 (`ModalDialog.tsx`)
- **용도**: 삭제 확인, 키워드 설정, 필터링 등 사용자 의사결정이 필요한 팝업 레이어.
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

### 4. 피드 4대 특화 모달 (`FeedModals`)
Figma v2 확정 시안에 정의된 피드 리더 4대 모달 다이얼로그:
1. **키워드 필터 모달 (`KeywordFilterModal`)**: 등록된 관심 키워드 칩 토글 및 본문/제목 매칭 선택
2. **키워드 설정 모달 (`KeywordSettingsModal`)**: 새 관심 키워드 추가/삭제 및 하이라이트 색상 지정
3. **제외 규칙 모달 (`ExclusionRulesModal`)**: 광고, 채용공고, 특정 태그 자동 숨김 패턴(Regex/키워드) 관리
4. **채널 삭제 확인 모달 (`ConfirmDeleteModal`)**: 구독 채널 및 캐시된 기사 일괄 삭제 경고 확인

### 5. 파일 드롭존 및 드래그 핸들 (`FileDropzone.tsx`, `SortableFileList.tsx`)
- **용도**: PDF 병합/분할 및 이미지 변환 시 파일 첨부와 순서 변경.
- **인터랙션**:
  - 드래그 앤 드롭 영역 시각적 피드백 (호버 시 테두리 점선 강조)
  - 파일 리스트 항목마다 드래그 핸들(`:::`) 아이콘 제공
  - 키보드 사용자를 위한 항목별 `위로 이동` / `아래로 이동` 버튼 및 스크린 리더 순서 변경 안내 제공

### 6. 하단 고정 결과 패널 (`FixedResultPanel.tsx`)
- **용도**: PDF 병합 완료 후 다운로드 바, 이미지 변환 결과 일괄 다운로드 바.
- **모바일 대응**:
  - 데스크톱: 화면 하단 중앙 플로팅 바
  - 모바일(375px): 하단 탭 내비게이션(56px) 및 Safe Area Bottom(34px) 위에 겹치지 않도록 `bottom: calc(56px + var(--safe-area-bottom) + 16px)` 오프셋 자동 적용

## 검토 체크리스트

- [x] 모든 컴포넌트가 [DESIGN.md](file:///Users/limkeunhyeok/workspace/my-space-front/DESIGN.md)의 디자인 토큰(라이트 테마, `#2457D6`, `#F7F8FA`)을 CSS 변수로 참조한다
- [x] 버튼, 입력, 모달, 토스트, 드롭존 등 핵심 컴포넌트의 Props 인터페이스가 정의되어 있다
- [x] 피드 4대 모달 및 PDF 드래그 핸들 인터랙션이 Figma v2 확정 시안과 일치한다
- [x] 모달 다이얼로그에 포커스 트랩, ESC 닫기, 포커스 복귀 등 WCAG 2.1 AA 접근성 규격이 반영되어 있다
- [x] 하단 고정 결과 패널에 모바일 Safe Area Bottom 오프셋이 적용되어 있다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-12 | 0.1.0 | my-space v2 공통 컴포넌트와 디자인 시스템 연계(FE-006) 최초 작성 | @bob (Keunhyeok Lim) |
