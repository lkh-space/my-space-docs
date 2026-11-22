# AGENTS.md

이 문서는 AI Agent가 `my-space-docs` Repository에서 작업을 수행할 때 반드시 준수해야 하는 **핵심 행동 지침**과 **작업별 참조 문서 길잡이(Routing Map)**를 정의합니다.

---

## 1. 작업별 참조 문서 길잡이 (Routing Map)

AI 에이전트는 사용자의 요청이나 질의를 받았을 때 **전체 문서를 전수 조사하지 말고**, 아래 표를 참조하여 관련 문서를 먼저 확인(`read_file`)한 후 작업을 수행하십시오.

> 상세 전체 문서 카탈로그 및 링크는 [`README.md`](README.md) 및 [`operations/ops-002-document-index.md`](operations/ops-002-document-index.md)를 참조하십시오.

| 작업 목적 / 질문 유형 | 1차 필수 확인 문서 | 연계/상세 참조 문서 | 주요 포함 내용 |
| :--- | :--- | :--- | :--- |
| **프로젝트 비전 & 전체 구조 파악** | [`product/prd-001-product-overview.md`](product/prd-001-product-overview.md)<br>[`architecture/arc-001-system-overview.md`](architecture/arc-001-system-overview.md) | [`operations/ops-001-glossary.md`](operations/ops-001-glossary.md)<br>[`operations/ops-002-document-index.md`](operations/ops-002-document-index.md) | v2 재편 배경, 5대 공간·15개 도구 구성, C4 다이어그램, 도메인 표준 용어 |
| **기능 요구사항 & 스펙 구현**<br>(노트·피드·관리·도구 등) | [`product/prd-002-requirements-scope.md`](product/prd-002-requirements-scope.md)<br>[`product/prd-005-functional-spec.md`](product/prd-005-functional-spec.md) | [`product/prd-003-user-stories.md`](product/prd-003-user-stories.md)<br>[`product/prd-004-user-journeys.md`](product/prd-004-user-journeys.md) | `REQ-001`~`031`, `FN-001`~`025` 입출력/처리 흐름, Acceptance Criteria |
| **UI 화면 개발 & 사이트맵** | [`design/dsn-002-information-architecture.md`](design/dsn-002-information-architecture.md)<br>[`design/dsn-004-wireframes-screen-spec.md`](design/dsn-004-wireframes-screen-spec.md) | [`design/dsn-003-user-flows.md`](design/dsn-003-user-flows.md)<br>[`design/dsn-009-ui-states-errors.md`](design/dsn-009-ui-states-errors.md) | `SCR-001`~`SCR-090` 화면 레이아웃 구역, 요소 배치, 화면 전환 흐름 |
| **디자인 시스템 & 컴포넌트 구현** | [`design/dsn-005-design-system-tokens.md`](design/dsn-005-design-system-tokens.md)<br>[`design/dsn-006-component-guide.md`](design/dsn-006-component-guide.md) | [`design/dsn-007-responsive.md`](design/dsn-007-responsive.md)<br>[`design/dsn-008-accessibility.md`](design/dsn-008-accessibility.md) | `--color-*` CSS 변수 토큰, 버튼·입력·모달 재사용 컴포넌트, 3단 반응형 |
| **백엔드 구조 & 통신 (api vs ai)** | [`architecture/arc-004-containers-components.md`](architecture/arc-004-containers-components.md)<br>[`architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md`](architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md)<br>[`BE-001 백엔드 아키텍처`](backend/be-001-architecture-modules.md) | [`architecture/arc-005-data-flow.md`](architecture/arc-005-data-flow.md)<br>[`BE-005 도메인 모델`](backend/be-005-domain-model-rules.md)<br>[`BE-006 DB 설계·ERD`](backend/be-006-database-erd.md) | 메인 API(`apps/api`)와 AI 비서(`apps/ai`) 분리, 도메인 모델, Prisma ERD, RabbitMQ 인덱싱 |
| **오브젝트 스토리지 (RustFS) 연동** | [`architecture/adr/adr-0010-rustfs-v2-adoption.md`](architecture/adr/adr-0010-rustfs-v2-adoption.md)<br>[`architecture/arc-004-containers-components.md`](architecture/arc-004-containers-components.md) | [`architecture/arc-005-data-flow.md`](architecture/arc-005-data-flow.md)<br>[`homelab-infra 전환 가이드`](file:///Users/limkeunhyeok/workspace/homelab-infra/docs/rustfs-migration-guide.md) | v2 RustFS 버전 고정·사용 S3 기능 검증, v1 전환은 별도 런북 |
| **검색 엔진 (OpenSearch vs Qdrant)** | [`architecture/arc-004-containers-components.md`](architecture/arc-004-containers-components.md)<br>[`product/prd-005-functional-spec.md`](product/prd-005-functional-spec.md) | [`architecture/arc-005-data-flow.md`](architecture/arc-005-data-flow.md)<br>[`product/prd-003-user-stories.md`](product/prd-003-user-stories.md) | OpenSearch 2.11 키워드 전문 검색 vs Qdrant 1.13 벡터 유사도 시맨틱 RAG 검색 |
| **Catch-up 스케줄러 & 알림** | [`architecture/adr/adr-0003-homelab-catchup-scheduler.md`](architecture/adr/adr-0003-homelab-catchup-scheduler.md)<br>[`architecture/adr/adr-0005-notification-channel-abstraction.md`](architecture/adr/adr-0005-notification-channel-abstraction.md) | [`product/prd-004-user-journeys.md`](product/prd-004-user-journeys.md)<br>[`architecture/arc-004-containers-components.md`](architecture/arc-004-containers-components.md) | 비-상시 구동 홈랩 부팅 시 누락 스케줄 소급 실행, 인앱 알림 우선 저장 |
| **보안, SSO & 방문자 샌드박스** | [`architecture/arc-007-security-architecture.md`](architecture/arc-007-security-architecture.md)<br>[`architecture/adr/adr-0007-visitor-browser-state.md`](architecture/adr/adr-0007-visitor-browser-state.md) | [`architecture/arc-005-data-flow.md`](architecture/arc-005-data-flow.md)<br>[`product/prd-006-nfr.md`](product/prd-006-nfr.md) | Authelia ForwardAuth, 주 사용자 영속 DB 격리, 방문자 브라우저 메모리 체험 |
| **로컬 브라우저 처리 (WASM/Worker)** | [`architecture/adr/adr-0009-local-tool-execution-contract.md`](architecture/adr/adr-0009-local-tool-execution-contract.md)<br>[`product/prd-005-functional-spec.md`](product/prd-005-functional-spec.md) | [`architecture/arc-005-data-flow.md`](architecture/arc-005-data-flow.md)<br>[`product/prd-006-nfr.md`](product/prd-006-nfr.md) | 로컬 도구 입력은 브라우저 내부 완결, PDF는 ADR-0009의 작업 단위 조건으로 서버 위임 |
| **비기능 품질 속성 & 성능 검증** | [`product/prd-006-nfr.md`](product/prd-006-nfr.md)<br>[`architecture/arc-008-quality-attributes.md`](architecture/arc-008-quality-attributes.md) | [`architecture/arc-002-principles-constraints.md`](architecture/arc-002-principles-constraints.md) | `NFR-001`~`008` 목표 수치, 품질 시나리오(`QS-01`~`07`), 트레이드오프 |
| **구현 단계 및 릴리스 일정** | [`product/prd-007-release-scope-priority.md`](product/prd-007-release-scope-priority.md) | [`product/prd-002-requirements-scope.md`](product/prd-002-requirements-scope.md) | `v2.1`~`v2.3` 마일스톤, 8단계 세부 구현 순서 및 기능 배분 |

---

## 2. 문서 관리 범위

- 제품 기획 및 요구사항 등 프로젝트 전체 수준의 문서를 관리한다.
- 전체 시스템 아키텍처와 Repository 간 관계를 관리한다.
- 프론트엔드, 백엔드, 인프라의 구현 상세 및 개별 기술적 의사결정(ADR, Spec, Plan 등)은 각 해당 Repository의 `/docs`에서 관리하므로 이 Repository에 작성하지 않는다.

---

## 3. 문서 작성 및 작업 원칙

1. **명시적 승인 우선 (Explicit Approval First)**:
   - 질의나 방향성 논의 시 임의로 문서를 생성·수정하거나 Git 커밋을 실행하지 않는다.
   - 사용자 승인이 떨어진 후 실제 파일 작성 및 편집을 수행한다.
2. **단일 진실 공급원 (Single Source of Truth) 유지**:
   - 확인된 사실과 추정 및 미결정 사항을 명확히 구분한다.
   - 아키텍처나 기능 스펙 변경 시 연관 문서(PRD, DSN, ARC, ADR, 인덱스)를 함께 갱신하여 정합성을 유지한다.
3. **중복 최소화 및 참조 링크 활용**:
   - 이미 존재하는 표준 문서나 다른 Repository 문서를 임의 복제하지 않고, Markdown 링크(`file:///...` 또는 상대경로)로 명확히 참조한다.
4. **번호 체계 및 템플릿 준수**:
   - 기존 파트별 명명 규칙(`PRD-NNN`, `DSN-NNN`, `ARC-NNN`, `ADR-NNN`, `OPS-NNN`)을 순차적으로 유지하며 번호를 임의로 건너뛰지 않는다.

## v2 아키텍처 수정안 참조 (2026-11-16)

ARC-001~008은 2026-11-20 사용자 최종 검토를 거쳐 승인되었다. 방문자 체험은 [ADR-0007](architecture/adr/adr-0007-visitor-browser-state.md), 노트 저장·색인 복구는 [ADR-0008](architecture/adr/adr-0008-note-write-consistency.md), PDF·오프라인 경계는 [ADR-0009](architecture/adr/adr-0009-local-tool-execution-contract.md), RustFS v2 기준은 [ADR-0010](architecture/adr/adr-0010-rustfs-v2-adoption.md)을 우선 확인한다. 이 ADR들은 2026-11-19 승인되었으며 기존 ADR-0001·0004·0006은 각각 ADR-0009·0007·0010으로 대체된 역사 기록이다. ARC-007 보안 보완본도 사용자 검토와 승인을 마쳤다. 현재 상태는 각 문서 frontmatter와 OPS-002를 따른다. ARC와 ADR의 승인은 설계 문서 승인으로 구현 완료를 뜻하지 않는다. v1 코드를 v2 구현 완료 근거로 사용하지 않는다.


### v2 백엔드 검토 기준 (2026-11-22)

사용자 지시에 따라 v2 BE 계약은 현재 이 저장소의 `backend/`에서 검토한다. 기존 구현 저장소의 `/docs`와 코드는 v1 사실 확인용이며 v2 구현 완료 근거가 아니다. API 필드는 [OpenAPI](backend/openapi.yaml), 도메인 규칙은 [BE-005](backend/be-005-domain-model-rules.md), DB·ENT는 [BE-006](backend/be-006-database-erd.md)가 기준이다. BE-001~006·013은 사용자 검토를 거쳐 승인되었다. 별도 BE-007~012 파일의 생략·통합 위치는 [문서 인덱스](operations/ops-002-document-index.md)를 따른다.
