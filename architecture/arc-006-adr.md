---
id: ARC-006
title: 아키텍처 의사결정 기록(ADR)
part: architecture
tier: required
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-05
depends_on: [ARC-001]
related: [ARC-002, ARC-004, ARC-007, ARC-008]
superseded_by: null
traces: [REQ-001, REQ-002, REQ-003, REQ-009, REQ-010, REQ-011, REQ-012, REQ-014, REQ-015, REQ-019, REQ-026, REQ-027, REQ-028, REQ-029, REQ-030]
---

# ARC-006 아키텍처 의사결정 기록(ADR)

## 이 문서는

- **목적**: `my-space` v2 전체 시스템 수준에서 내린 핵심 아키텍처 의사결정(ADR)의 목록과 상태 관리 기준을 정의한다. 각 결정의 상세 맥락, 대안 비교, 결과는 `adr/` 디렉터리의 개별 문서(`ADR-0001`~`ADR-0006`)에 한 파일씩 기록한다. 결정 상태가 바뀌면 이 표와 [ARC-001 시스템 개요](arc-001-system-overview.md#주요-결정-필수)의 표를 함께 갱신한다.
- **대상 독자**: 프로젝트 소유자, 프론트엔드·백엔드·인프라 개발자, AI 에이전트
- **작성 시점**: 시스템 구조, 비기능 품질 특성(`NFR-`), 외부 연동, 보안 경계에 영향을 주는 결정을 내릴 때마다 작성·갱신한다.
- **등급**: 필수 — 모든 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [ARC-001 시스템 개요](arc-001-system-overview.md) | 결정의 대상이 되는 시스템 개요 및 주요 결정 요약 표 |
| 관련 | [ARC-002 아키텍처 원칙과 제약](arc-002-principles-constraints.md) | 각 ADR의 결정 동인이 되는 원칙(`AP-`)과 제약(`CON-`) |
| 관련 | [ARC-004 컨테이너와 주요 컴포넌트](arc-004-containers-components.md) | ADR 결정이 반영된 컨테이너 분리 구조 |
| 관련 | [ARC-007 보안 아키텍처](arc-007-security-architecture.md) | 인증·인가 위임 및 데이터 격리 결정이 반영된 보안 구조 |
| 관련 | [ARC-008 품질 속성](arc-008-quality-attributes.md) | 품질 속성 달성 전술과 트레이드오프 |
| 관련 | [PRD-006 비기능 요구사항](../product/prd-006-nfr.md) | 결정 동인이 되는 `NFR-001`~`NFR-008` 목표 수치 |

## 결정 상태 기준 [필수]

| 결정 상태 | 의미 | 문서 `status` | 다른 문서가 결정을 근거로 쓸 수 있나 |
|---|---|---|---|
| 제안됨 | 작성 완료 후 사용자 검토 중인 결정 | `draft` 또는 `review` | 아니요 (검토 후 승인을 거쳐 확정) |
| 승인됨 | 사용자 검토를 거쳐 현재 유효한 결정 | `approved` | 예 |
| 거부됨 | 검토 끝에 채택하지 않은 결정 (논의 반복 방지용 기록) | `approved` | 아니요 (거부 이유만 참고) |
| 폐기됨 | 대체 결정 없이 더 이상 유효하지 않음 | `deprecated` | 아니요 |
| 대체됨 | 새로운 ADR로 대체됨 (`superseded_by: ADR-NNNN`) | `superseded` | 아니요 (대체한 새 ADR을 참조) |

## ADR 목록 [필수]

현재 작성된 모든 ADR은 사용자 검토 대기 중이므로 결정 상태가 `제안됨`, 메타데이터 `status`는 `review`다. 사용자 승인 시 `승인됨`(`status: approved`)으로 일괄 전환한다.

| ID | 결정 제목 | 결정 상태 | 결정일 | 관련 요구사항·NFR | 영향을 받는 문서 |
|---|---|---|---|---|---|
| [ADR-0001](adr/adr-0001-hybrid-client-server-processing.md) | 민감 도구 연산의 브라우저 로컬 처리(Web Worker·WASM) 및 백엔드(`qpdf`) 하이브리드 분리 | 제안됨 | 2026-11-05 | `REQ-012`, `REQ-014`~`024`, `REQ-029`, `REQ-030`, `NFR-001`~`003`, `NFR-005`, `NFR-006` | [ARC-002](arc-002-principles-constraints.md), [ARC-004](arc-004-containers-components.md), [ARC-005](arc-005-data-flow.md), [ARC-008](arc-008-quality-attributes.md) |
| [ADR-0002](adr/adr-0002-backend-monorepo-api-ai-separation.md) | 백엔드 메인 API(`api`)와 AI 워크스페이스 서버(`ai`) 프로세스 분리 및 저장소 공유 | 제안됨 | 2026-11-05 | `REQ-003`, `REQ-011`, `REQ-027`, `NFR-003` | [ARC-001](arc-001-system-overview.md), [ARC-004](arc-004-containers-components.md), [ARC-008](arc-008-quality-attributes.md) |
| [ADR-0003](adr/adr-0003-homelab-catchup-scheduler.md) | 홈랩 비-상시 구동 환경을 위한 DB 영속화 기반 누락 스케줄 보정(Catch-up) 아키텍처 | 제안됨 | 2026-11-05 | `REQ-009`, `REQ-028`, `NFR-004`, `CON-01` | [ARC-002](arc-002-principles-constraints.md), [ARC-004](arc-004-containers-components.md), [ARC-005](arc-005-data-flow.md), [ARC-008](arc-008-quality-attributes.md) |
| [ADR-0004](adr/adr-0004-forward-auth-and-visitor-isolation.md) | Authelia ForwardAuth 게이트웨이 인증 위임 및 외부 방문자 테스트 계정 인메모리 샌드박스 격리 | 제안됨 | 2026-11-05 | `REQ-026`, `REQ-030`, `NFR-006`, `CON-03`, `CON-05` | [ARC-003](arc-003-system-context.md), [ARC-005](arc-005-data-flow.md), [ARC-007](arc-007-security-architecture.md), [ARC-008](arc-008-quality-attributes.md) |
| [ADR-0005](adr/adr-0005-notification-channel-abstraction.md) | 인앱 알림함 우선 저장 및 외부 알림 채널(Slack Webhook) 어댑터 추상화 | 제안됨 | 2026-11-05 | `REQ-002`, `REQ-010`, `NFR-004`, `AP-04` | [ARC-003](arc-003-system-context.md), [ARC-004](arc-004-containers-components.md), [ARC-005](arc-005-data-flow.md) |
| [ADR-0006](adr/adr-0006-object-storage-migration-to-rustfs.md) | 오브젝트 스토리지를 MinIO에서 RustFS(Apache 2.0)로 전환 | 제안됨 | 2026-11-08 | `REQ-003`, `REQ-028`, `NFR-006` | [ARC-001](arc-001-system-overview.md), [ARC-004](arc-004-containers-components.md), [ARC-005](arc-005-data-flow.md), [ARC-007](arc-007-security-architecture.md) |

## 개별 저장소 내부 ADR과의 경계 [선택]

[my-space-docs/README.md](../README.md#다른-repository와의-관계)의 원칙에 따라, 프로젝트 전체 시스템 경계와 다중 저장소(`my-space-front`, `my-space-backend`, `homelab-infra`)에 걸친 상위 아키텍처 결정은 본 `ARC-006`(`adr/ADR-0001`~`0006`)에서 관리한다. 단일 저장소 내부에 국한된 구현 결정(예: 프론트엔드 Storybook 구성, i18n 라이브러리 설정, 백엔드 pnpm 워크스페이스 버전 스크립트 등)은 각 구현 저장소의 `/docs/adr/`에 두고 참조한다.

## 검토 체크리스트

- [x] 각 ADR이 결정 하나만 다루며 `adr/adr-NNNN-<slug>.md` 파일로 작성되어 있다
- [x] 모든 ADR에 둘 이상의 대안과 장단점, 나빠지는 점(트레이드오프)이 명시되어 있다
- [x] 결정 상태와 메타데이터 `status`가 기준 표와 일치한다
- [x] [ARC-001](arc-001-system-overview.md)의 "주요 결정" 표와 이 문서의 ADR 목록이 일치한다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-05 | 0.1.0 | my-space v2 아키텍처 의사결정 기록 색인(ARC-006) 및 ADR 5종 최초 작성 | Antigravity (사용자 검토 대기) |
| 2026-11-08 | 0.2.0 | ADR-0006(오브젝트 스토리지를 MinIO에서 RustFS로 전환) 추가 | Antigravity (사용자 검토 대기) |
