---
id: OPS-002
title: 문서 인덱스
part: operations
tier: required
status: approved
version: 1.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-05
depends_on: []
related: [OPS-001, PRD-001, PRD-002, PRD-003, PRD-004, PRD-005, PRD-006, PRD-007, DSN-001, DSN-002, DSN-003, DSN-004, DSN-005, DSN-006, DSN-007, DSN-008, DSN-009, ARC-001, ARC-002, ARC-003, ARC-004, ARC-005, ARC-006, ARC-007, ARC-008, ADR-0001, ADR-0002, ADR-0003, ADR-0004, ADR-0005, ADR-0006]
superseded_by: null
traces: []
---

# OPS-002 문서 인덱스

## 이 문서는

- **목적**: `my-space` v2 문서 저장소(`my-space-docs`)에 작성된 모든 문서의 목록, 등급, 상태, 책임자, 마지막 검토일을 한곳에 모은다. 사람과 AI 에이전트가 문서를 찾는 시작점이다.
- **대상 독자**: 프로젝트 소유자, AI 에이전트
- **작성 시점**: 기획 단계 첫 문서 작성 시 함께 생성하며, 이후 디자인(`DSN`)·아키텍처(`ARC`)·프론트엔드(`FE`)·백엔드(`BE`)·품질(`QA`)·운영(`OPS`) 문서를 추가하거나 승인할 때마다 갱신한다.
- **등급**: 필수 — 모든 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 관련 | [OPS-001 용어집](ops-001-glossary.md) | 모든 문서가 공유하는 도메인·기술 용어의 기준 문서 |
| 관련 | [PRD-001 제품 개요와 비전](../product/prd-001-product-overview.md) | 제품 기획 문서의 시작점 |
| 관련 | [DSN-001 디자인 브리프](../design/dsn-001-design-brief.md) | 제품 디자인 문서의 시작점 |
| 관련 | [ARC-001 시스템 아키텍처 개요](../architecture/arc-001-system-overview.md) | 공통 시스템 아키텍처 문서의 시작점 |

## 문서 목록 [필수]

현재 **1단계: 기획(PRD) 및 공통 기반(OPS)** 문서 9종(`approved`), **2단계: 디자인(DSN)** 문서 9종(`review`), **3단계: 공통 아키텍처(ARC/ADR)** 문서 14종(`review`)이 번호 건너뜀 없이 순차 작성되어 있다. 후속 단계(백엔드 `BE`·프론트엔드 `FE` → 테스트/검증 `QA` → 운영관리 `OPS`) 진행 시 해당 파트 문서를 이 표에 순차 추가한다.

| ID | 문서 | 한 줄 설명 | 등급 | 상태 | owner | last_reviewed |
|---|---|---|---|---|---|---|
| PRD-001 | [제품 개요와 비전](../product/prd-001-product-overview.md) | `my-space` v2의 공간·도구 재편 배경, 핵심 가치, 비목표, 홈랩 제약 및 테스트 계정 개요 | 필수 | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-002 | [요구사항과 범위](../product/prd-002-requirements-scope.md) | v2 포함·제외 범위와 요구사항 `REQ-001`~`REQ-031`, 에픽 `E-01`~`E-08` 정의 | 필수 | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-003 | [사용자 스토리와 수용 기준](../product/prd-003-user-stories.md) | 사용자 스토리 `US-001`~`US-024`과 체크리스트·시나리오형 수용 기준 | 필수 | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-004 | [사용자 여정과 업무 흐름](../product/prd-004-user-journeys.md) | 핵심 여정 `J-01`~`J-04`와 홈랩 Catch-up·하이브리드 도구 업무 흐름 및 예외 처리 | 조건부(UI) | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-005 | [기능 명세](../product/prd-005-functional-spec.md) | 기능 `FN-001`~`FN-025`의 입력, 처리 순서, 결과, 예외 정의 | 조건부(실무) | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-006 | [비기능 요구사항](../product/prd-006-nfr.md) | 파서·WASM 성능, 홈랩 Catch-up 복구, PWA 오프라인, 보안·접근성 수치 `NFR-001`~`NFR-008` | 조건부(배포) | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| PRD-007 | [릴리스 범위와 우선순위](../product/prd-007-release-scope-priority.md) | 플랫폼 기반·공간·도구·테스트 계정 구현 순서에 따른 `v2.1`~`v2.3`(세부 1~8단계) 마일스톤 배분 | 선택 | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-001 | [디자인 브리프](../design/dsn-001-design-brief.md) | Calm Studio Workspace 미학, 정보 밀도, 레이아웃·모션 원칙 정의 | 선택 | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-002 | [정보 구조와 사이트맵](../design/dsn-002-information-architecture.md) | 공간(5영역)·도구(15종) 2단 내비게이션과 전체 화면(`SCR-001`~`SCR-090`) 사이트맵 | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-003 | [사용자 흐름](../design/dsn-003-user-flows.md) | 대시보드·노트·피드·PDF·테스트 계정 핵심 화면 전환 흐름 `UF-01`~`UF-05` | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-004 | [와이어프레임과 화면 명세](../design/dsn-004-wireframes-screen-spec.md) | `SCR-001`~`SCR-090` 전체 화면의 레이아웃 구역, 주요 요소, 상호작용 명세 | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-005 | [디자인 시스템과 디자인 토큰](../design/dsn-005-design-system-tokens.md) | 색상(`--color-*`), 타이포그래피, 간격, 반경, 그림자 CSS 변수 토큰 정의 | 선택 | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-006 | [컴포넌트 가이드](../design/dsn-006-component-guide.md) | 공통 셸, 버튼, 입력, 뱃지, 토스트, 모달 등 재사용 UI 컴포넌트 규격 | 선택 | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-007 | [반응형 디자인](../design/dsn-007-responsive.md) | Desktop/Tablet/Mobile 브레이크포인트 및 모바일 전용 읽기·입력 레이아웃 적응 규칙 | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-008 | [접근성 가이드](../design/dsn-008-accessibility.md) | WCAG 2.1 AA 명도 대비, 키보드 포커스 링, 스크린 리더 랜드마크 및 모션 축소 규칙 | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| DSN-009 | [UI 상태와 오류 상태](../design/dsn-009-ui-states-errors.md) | 빈 화면, 로딩, 유효성 오류, 오프라인 배너, 테스트 계정 차단 등 상태 피드백 | 조건부(UI) | review | @bob (Keunhyeok Lim) | 2026-11-01 |
| ARC-001 | [시스템 아키텍처 개요](../architecture/arc-001-system-overview.md) | 하이브리드 클라이언트-서버·홈랩 k3s 아키텍처 스타일과 구성요소 한눈에 보기 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-002 | [아키텍처 원칙과 제약](../architecture/arc-002-principles-constraints.md) | 로컬-퍼스트 보안, Catch-up 복원력, 의존성 절제 등 6대 원칙과 기술·조직 제약 | 조건부(실무) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-003 | [시스템 컨텍스트](../architecture/arc-003-system-context.md) | C4 Level 1 컨텍스트와 Authelia·LLM·RSS·Slack·공공데이터포털 외부 인터페이스 | 조건부(연동) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-004 | [컨테이너와 컴포넌트 구조](../architecture/arc-004-containers-components.md) | C4 Level 2/3 컨테이너(`web`, `api`, `ai`, DB/스토리지) 및 내부 모듈 책임과 의존 규칙 | 조건부(실무) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-005 | [데이터 흐름](../architecture/arc-005-data-flow.md) | 핵심 시나리오 5종의 데이터 이동 경로, 저장소 매핑, 민감 데이터 로컬 격리·마스킹 규칙 | 조건부(개인정보) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-006 | [아키텍처 의사결정 기록(ADR)](../architecture/arc-006-adr.md) | 시스템 아키텍처 핵심 의사결정(`ADR-0001`~`ADR-0005`) 색인 및 운영 규칙 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-007 | [보안 아키텍처](../architecture/arc-007-security-architecture.md) | 신뢰 경계 4영역, Authelia ForwardAuth·방문자 격리, STRIDE 위협 모델과 통제 수단 | 조건부(배포) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ARC-008 | [품질 속성](../architecture/arc-008-quality-attributes.md) | `PRD-006` 비기능 요구사항을 달성하는 품질 속성 시나리오 `QA-01`~`QA-08`과 트레이드오프 | 조건부(실무) | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0001 | [민감 도구 연산의 브라우저 로컬 처리 및 백엔드 하이브리드 분리](../architecture/adr/adr-0001-hybrid-client-server-processing.md) | 민감 파일·원문 도구는 브라우저 Web Worker·WASM에서 처리하고 암호화 PDF만 `qpdf`로 분리 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0002 | [백엔드 메인 API와 AI 워크스페이스 서버 프로세스 분리](../architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md) | NestJS 모노레포 내 `apps/api`와 `apps/ai` 독립 배포 및 저장소·스키마 공유 결정 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0003 | [홈랩 비-상시 구동 환경을 위한 DB 영속화 기반 누락 스케줄 보정(Catch-up)](../architecture/adr/adr-0003-homelab-catchup-scheduler.md) | `SystemJobState` 기반 기동 즉시 Catch-up 및 일 단위 멱등 키 중복 방지 아키텍처 결정 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0004 | [Authelia ForwardAuth 게이트웨이 인증 위임 및 외부 방문자 샌드박스 격리](../architecture/adr/adr-0004-forward-auth-and-visitor-isolation.md) | Traefik ForwardAuth 헤더 신뢰와 방문자 모드 브라우저 인메모리 샌드박스·읽기 전용 가드 결정 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0005 | [인앱 알림함 우선 저장 및 외부 알림 채널 어댑터 추상화](../architecture/adr/adr-0005-notification-channel-abstraction.md) | 인앱 알림함 영구 기록 우선 보장 및 `NotificationChannelPort` 기반 Slack Webhook 어댑터 분리 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-05 |
| ADR-0006 | [오브젝트 스토리지를 MinIO에서 RustFS(Apache 2.0)로 전환](../architecture/adr/adr-0006-object-storage-migration-to-rustfs.md) | MinIO CVE 보안 패치 중단 대응 및 완전 오픈소스 RustFS(S3 호환) 전환 결정 | 필수 | review | @bob (Keunhyeok Lim) | 2026-11-08 |
| OPS-001 | [용어집](ops-001-glossary.md) | 공간·도구, 공통 날짜 모델 3유형, Catch-up, 테스트 계정 격리 등 표준 용어와 코드 식별자 | 필수 | approved | @bob (Keunhyeok Lim) | 2026-11-01 |
| OPS-002 | [문서 인덱스](ops-002-document-index.md) | `my-space-docs` 전체 문서 목록과 작성 현황 | 필수 | approved | @bob (Keunhyeok Lim) | 2026-11-08 |

## 작성하지 않는 문서 [필수]

본 저장소는 프로젝트에 실제로 필요한 문서만 선별하여 각 파트 내에서 `001`번부터 빈 번호 없이 순차적으로 번호를 부여한다.

| 구분 | 내용 | 다시 판단할 시점 |
|---|---|---|
| 상용 비즈니스 KPI 및 다중 조직 이해관계자 분리 문서 | 본 프로젝트는 개인 홈랩 기반 통합 워크스페이스이므로 배경·핵심 가치·사용자 유형(주 사용자 및 외부 방문자)은 별도 문서로 쪼개지 않고 [PRD-001 제품 개요와 비전](../product/prd-001-product-overview.md)과 [PRD-003 사용자 스토리와 수용 기준](../product/prd-003-user-stories.md)에 통합 기술했다 | 다중 조직·상용 과금 체계가 도입될 때 |

## 저장소 밖 문서 [선택]

| 자료 | 위치 | 용도 | 관련 문서 ID |
|---|---|---|---|
| v1 프론트엔드 디자인 시스템 (`DESIGN.md`) | `my-space-front/DESIGN.md` | 색상 토큰, 타이포그래피, 반응형 브레이크포인트 기준 | PRD-006, DSN-005 |
| v1 프론트엔드 SDD 명세 및 ADR | `my-space-front/docs/` | v1에서 구현 완료된 마크다운 노트, AI, PDF, DBML 프론트엔드 사양 | PRD-002, PRD-005, ARC-004 |
| v1 백엔드 SDD 명세 및 ADR | `my-space-backend/docs/` | v1에서 구현 완료된 백엔드 API 및 `qpdf`·AI 파이프라인 사양 | PRD-002, PRD-005, ARC-004, ARC-006 |
| 홈랩 쿠버네티스 인프라 매니페스트 | `homelab-infra/` | k3s 클러스터, Traefik, Authelia, PostgreSQL, RustFS, Qdrant, RabbitMQ 구성 | ARC-001, ARC-003, ARC-004, ARC-007 |
| v2 초기 기능 기획 메모 | `solution-docs-guide-main/temp-space.md` | 사용자 대화 기반 초기 아이디어 및 결정 스냅샷 | PRD-001, PRD-002, PRD-007 |

## 검토 체크리스트

- [x] 현재 저장소에 있는 모든 문서 파일이 문서 목록 표에 있고, 표의 링크가 모두 열린다
- [x] 표의 상태·owner·last_reviewed가 각 문서의 메타데이터와 일치한다
- [x] 번호 건너뜀 없이 순차 번호(`PRD-001`~`PRD-007`, `DSN-001`~`DSN-009`, `ARC-001`~`ARC-008`, `ADR-0001`~`ADR-0006`, `OPS-001`~`OPS-002`)가 부여되어 있다
- [x] owner가 빈 칸 없이 명시되어 있다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-08 | 1.2.0 | 오브젝트 스토리지 RustFS 전환에 따른 ADR-0006 추가 등록 | Antigravity (사용자 검토 대기) |
| 2026-11-05 | 1.1.0 | 디자인(`DSN-001`~`DSN-009`) 및 공통 아키텍처(`ARC-001`~`ARC-008`, `ADR-0001`~`ADR-0005`) 문서 목록 등록 | Antigravity (사용자 검토 대기) |
| 2026-11-01 | 1.0.0 | 사용자 검토 완료에 따라 approved(승인) 상태로 확정 | Antigravity (사용자 승인 완료) |
| 2026-10-31 | 0.1.0 | my-space v2 문서 인덱스 최초 작성 (PRD 7종 + OPS 2종 등록) | Antigravity (사용자 검토 대기) |
| 2026-10-31 | 0.1.1 | 기획 문서 번호를 건너뜀 없이 `PRD-001`~`PRD-007`로 순차 재부여 | Antigravity (사용자 검토 대기) |
| 2026-11-01 | 0.1.3 | 소모품 교체 및 습관·대여 제거(REQ 31건, US 24건, FN 25건)에 따른 번호 체계 재정렬 |
| 2026-11-01 | 0.1.2 | 요구사항(`REQ-001`~`REQ-031`)·스토리(`US-001`~`US-024`)·기능(`FN-001`~`FN-025`) 번호 재정렬 반영 | Antigravity (사용자 검토 대기) |
