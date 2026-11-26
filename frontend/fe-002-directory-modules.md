---
id: FE-002
title: 디렉터리와 모듈 구조
part: frontend
tier: optional
status: approved
version: 0.1.2
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-26'
depends_on: [FE-001]
related: [FE-003, FE-005, FE-006, ARC-004, OPS-001]
superseded_by: null
traces: [REQ-001, REQ-031]
---

# FE-002 디렉터리와 모듈 구조

## 이 문서는

- **목적**: `my-space` 프론트엔드 모노레포(`apps/web`)의 디렉터리 구성, 모듈 분리 기준 및 계층 간 의존 규칙을 정의한다. 새로운 기능이나 컴포넌트를 추가할 때 파일의 위치와 import 규칙의 기준이 된다.
- **대상 독자**: 프론트엔드 개발자, 코드 리뷰어, AI 에이전트
- **작성 시점**: 기술 구조([FE-001](fe-001-tech-stack.md)) 작성 직후, 화면 및 컴포넌트 구현 전.
- **등급**: 선택 — 기능이 15개 이상(공간 4종, 도구 15종)인 통합 워크스페이스 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [FE-001 프론트엔드 기술 구조](fe-001-tech-stack.md) | Nx 모노레포 및 React 19/Vite 환경 규약 |
| 관련 | [FE-003 라우팅과 페이지 구성](fe-003-routing-pages.md) | 라우트 단위 페이지 컴포넌트의 위치 |
| 관련 | [FE-005 상태 관리](fe-005-state-management.md) | 전역 상태, 샌드박스 스토어 및 쿼리 훅의 위치 |
| 관련 | [FE-006 공통 컴포넌트와 디자인 시스템 연계](fe-006-components-design-system.md) | 디자인 토큰 및 재사용 공통 컴포넌트의 위치 |
| 관련 | [ARC-004 컨테이너와 주요 컴포넌트](../architecture/arc-004-containers-components.md) | `web` 컨테이너 내부 컴포넌트 책임 |
| 관련 | [OPS-001 용어집](../operations/ops-001-glossary.md) | 공간·도구 표준 용어 및 디렉터리 식별자 |

## 디렉터리 구조 [필수]

`apps/web/src`는 계층형 아키텍처(FSD 기반)를 채택하여 책임과 범위를 분리한다:

```text
apps/web/src/
  app/                    # 애플리케이션 진입점, 전역 프로바이더, 라우터, 글로벌 CSS/토큰
  pages/                  # URL 경로에 1:1 매핑되는 최상위 페이지 컴포넌트 (페이지 전용 로컬 엔진 포함)
  widgets/                # 독립적으로 조합 가능한 복합 UI 블록 (사이드바, 헤더, 대시보드 카드 등)
  features/               # 사용자 인터랙션 단위의 비즈니스 기능 (노트 편집, 피드 모달, 이미지 워커 등 코로케이션)
  entities/               # 도메인 모델 단위의 상태, API 클라이언트 및 세션/샌드박스 스토어 (note, feed, schedule, auth, sandbox)
  shared/                 # 도메인에 종속되지 않는 재사용 UI 컴포넌트, 유틸리티, PWA 정책, 디자인 토큰
```

| 디렉터리 | 책임 | 여기에 두지 않는 것 |
|---|---|---|
| `app/` | `main.tsx`, `App.tsx`, 전역 스타일(`tokens.css`, `reset.css`), 전역 프로바이더/라우터 설정 | 특정 화면 전용 컴포넌트나 비즈니스 로직 |
| `pages/` | `FE-003`의 라우트별 페이지 조립 및 페이지 전용 지연 로딩 로컬 엔진(`pages/pdf-tools/localPdfEngine.ts` 등) | 공통 도메인 API 클라이언트 또는 범용 UI 프리티브 |
| `widgets/` | `Sidebar`, `HeaderBar`, `DashboardGrid`, `GuestNoticeBar`, `PersonalSpaceGuard` 등 복합 화면 블록 | 다른 페이지에서 쓸 수 없는 초세부 비즈니스 로직 |
| `features/` | `markdown`, `image-tools`, `dev-tools`, `calculators` 등 인터랙션 단위와 기능별 Web Worker(`features/image-tools/imageProcessing.worker.ts` 등) | 전역 레이아웃 또는 다른 feature의 내부 상태 참조 |
| `entities/` | `auth`, `sandbox`, `home`, `note`, `feed`, `schedule`, `notification`, `ai` 등 도메인 타입·세션 가드·API 클라이언트 | 화면 전체를 구성하는 레이아웃 구조 |
| `shared/` | `Button`, `Modal`, `Toast`, `SortableFileList`, `markdownSanitizer`, `pwa/offlinePolicy` 등 공통 자산 | 특정 도메인(노트, 일정)에만 종속적인 비즈니스 로직 |

## 모듈 경계와 의존 규칙 [필수]

```mermaid
---
title: 프론트엔드 계층별 단방향 의존 규칙
---
flowchart TD
  App["app (최상위 진입점)"] --> Pages["pages (라우트 페이지 · 지연 로딩 로컬 엔진)"]
  Pages --> Widgets["widgets (복합 화면 블록)"]
  Pages --> Features["features (사용자 기능 · 코로케이션 Worker)"]
  Widgets --> Features
  Widgets --> Entities["entities (도메인 모델)"]
  Features --> Entities
  Pages --> Shared["shared (공통 UI·유틸)"]
  Widgets --> Shared
  Features --> Shared
  Entities --> Shared
```

- **목적**: 상위 계층은 하위 계층을 사용할 수 있지만, 하위 계층이 상위 계층을 역참조하는 스파게티 의존성을 원천 차단한다. 별도의 최상위 `workers/` 디렉터리를 분리하지 않고 해당 기능/페이지(`features/`, `pages/`) 내에 Web Worker와 지연 로딩 로컬 엔진을 코로케이션한다.
- **규칙**:
  1. **단방향 의존성**: `app` → `pages` → `widgets` → `features` → `entities` → `shared` 순으로만 의존할 수 있다. 하위 레이어가 상위 레이어를 `import`하는 것은 엄격히 금지된다.
  2. **수평 참조 금지**: `features/note-editor`가 `features/feed-filter-modal`을 직접 참조할 수 없다. 공통 데이터는 `entities`나 `shared`로 내려서 참조한다.
  3. **공개 진입점 (`index.ts`)**: 각 모듈은 루트의 `index.ts`를 통해서만 외부로 심볼을 노출하며, 모듈 내부의 세부 구현 파일(예: `features/note-editor/internal/parser.ts`)을 외부에서 직접 import하지 않는다.

| 모듈 | 의존 가능 계층 | 의존 금지 대상 | 검사 방법 |
|---|---|---|---|
| `pages` | `widgets`, `features`, `entities`, `shared` | `app` | ESLint (`import/order`, 경계 규칙) |
| `widgets` | `features`, `entities`, `shared` | `app`, `pages` | ESLint 규칙 및 코드 리뷰 |
| `features` | `entities`, `shared` (내부 코로케이션 `*.worker.ts` 포함) | `app`, `pages`, `widgets`, 타 `feature` | ESLint 규칙 및 코드 리뷰 |
| `entities` | `shared` | `app`, `pages`, `widgets`, `features` | ESLint 규칙 및 빌드 검증 |
| `shared` | 없음 (완전 독립) | 상위 모든 계층 (`app`, `pages`, `widgets`, `features`, `entities`) | ESLint 규칙 및 빌드 검증 |

## 명명 규칙 [선택]

| 대상 | 규칙 | 예시 |
|---|---|---|
| 디렉터리명 | 소문자 `kebab-case` | `note-editor`, `pdf-tools`, `image-tools` |
| 컴포넌트 파일 | `PascalCase.tsx` | `Button.tsx`, `ModalDialog.tsx`, `GuestNoticeBar.tsx` |
| 유틸리티 및 훅 | `camelCase.ts` | `useAiStream.ts`, `markdownSanitizer.ts`, `offlinePolicy.ts` |
| 테스트 파일 | `*.spec.tsx` 또는 `*.test.ts` | `DashboardPage.spec.tsx`, `offlinePolicy.spec.ts` |
| Worker 파일 | `*.worker.ts` (해당 `features/*` 내 코로케이션) | `features/image-tools/imageProcessing.worker.ts` |

## 검토 체크리스트

- [x] 최상위 디렉터리마다 명확한 책임과 배제 대상이 명시되어 있다
- [x] 의존 다이어그램과 단방향 의존성 규칙 표가 일치한다
- [x] 모듈 간 수평 의존 및 하위 레이어의 상위 역참조 금지 규칙이 명시되어 있다
- [x] 기능 모듈 식별자가 [OPS-001 용어집](../operations/ops-001-glossary.md)의 도메인 용어와 일치한다
- [x] 프레임워크 및 번들러 규약(`Vite`, `Nx`)이 [FE-001](fe-001-tech-stack.md)과 일치한다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-26 | 0.1.2 | 별도 최상위 `workers/`를 제거하고 기능·페이지 내 코로케이션(`imageProcessing.worker.ts`, `localPdfEngine.ts`) 현행 구조로 정렬 | @bob (Keunhyeok Lim) |
| 2026-11-24 | 0.1.1 | `FE-001` 스택 현행화 반영 (`workers/`, `entities/` 설명 정렬) | @bob (Keunhyeok Lim) |
| 2026-11-12 | 0.1.0 | my-space v2 프론트엔드 디렉터리와 모듈 구조(FE-002) 최초 작성 | @bob (Keunhyeok Lim) |
