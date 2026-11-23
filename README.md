# 📚 my-space-docs

`my-space` v2 프로젝트의 제품 기획, UX/UI 디자인, 시스템 아키텍처 및 공통 기준을 통합 관리하는 전용 Repository입니다.

---

## 🤖 AI 에이전트 및 작업별 참조 문서 길잡이 (Routing Map)

AI에게 기능 구현, 버그 수정, 아키텍처 질문 등 작업을 지시할 때 **전체 문서를 전수 조사할 필요 없이**, 작업 목적에 맞춰 아래의 핵심 참조 문서를 먼저 확인하십시오.

| 작업 목적 / 질문 유형                                     | 1차 필수 확인 문서                                                                                                                                                                                                                                                                                          | 연계/상세 참조 문서                                                                                                                                                                                                                                                                        | 주요 포함 내용                                                                           |
| :-------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------- |
| **프로젝트 비전 & 전체 구조 파악**                        | [`PRD-001 제품 개요와 비전`](product/prd-001-product-overview.md)<br>[`ARC-001 시스템 개요`](architecture/arc-001-system-overview.md)                                                                                                                                                                       | [`OPS-001 용어집`](operations/ops-001-glossary.md)<br>[`OPS-002 문서 인덱스`](operations/ops-002-document-index.md)                                                                                                                                                                        | v2 재편 배경, 5대 공간·15개 도구 구성, C4 다이어그램, 도메인 표준 용어                   |
| **기능 요구사항 & 스펙 구현**<br>(노트·피드·관리·도구 등) | [`PRD-002 요구사항과 범위`](product/prd-002-requirements-scope.md)<br>[`PRD-005 기능 명세`](product/prd-005-functional-spec.md)                                                                                                                                                                             | [`PRD-003 사용자 스토리`](product/prd-003-user-stories.md)<br>[`PRD-004 여정과 업무흐름`](product/prd-004-user-journeys.md)                                                                                                                                                                | `REQ-001`~`031`, `FN-001`~`025` 입출력/처리 흐름, Acceptance Criteria                    |
| **UI 화면 개발 & 사이트맵**                               | [`DSN-002 정보구조·사이트맵`](design/dsn-002-information-architecture.md)<br>[`DSN-004 와이어프레임·화면명세`](design/dsn-004-wireframes-screen-spec.md)                                                                                                                                                    | [`DSN-003 사용자 흐름`](design/dsn-003-user-flows.md)<br>[`DSN-009 UI/오류 상태`](design/dsn-009-ui-states-errors.md)                                                                                                                                                                      | `SCR-001`~`SCR-090` 화면 레이아웃 구역, 요소 배치, 화면 전환 흐름                        |
| **디자인 시스템 & 컴포넌트 구현**                         | [`DSN-005 디자인 시스템·토큰`](design/dsn-005-design-system-tokens.md)<br>[`DSN-006 컴포넌트 가이드`](design/dsn-006-component-guide.md)                                                                                                                                                                    | [`DSN-007 반응형 디자인`](design/dsn-007-responsive.md)<br>[`DSN-008 접근성 가이드`](design/dsn-008-accessibility.md)                                                                                                                                                                      | `--color-*` CSS 변수 토큰, 버튼·입력·모달 재사용 컴포넌트, 3단 반응형                    |
| **백엔드 구조 & 통신 (api vs ai)**                        | [`ARC-004 컨테이너·컴포넌트`](architecture/arc-004-containers-components.md)<br>[`ADR-0002 api/ai 프로세스 분리`](architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md)                                                                                                                          | [`ARC-005 데이터 흐름`](architecture/arc-005-data-flow.md)                                                                                                                                                                                                                                 | 메인 API(`apps/api`)와 AI 비서(`apps/ai`) 분리, RabbitMQ 비동기 인덱싱                   |
| **백엔드 구조 & 통신 (api vs ai)**                        | [`ARC-004 컨테이너·컴포넌트`](architecture/arc-004-containers-components.md)<br>[`ADR-0002 api/ai 프로세스 분리`](architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md)<br>[`BE-001 백엔드 아키텍처`](file:///Users/limkeunhyeok/workspace/my-space-backend/docs/be-001-architecture-modules.md) | [`ARC-005 데이터 흐름`](architecture/arc-005-data-flow.md)<br>[`BE-005 도메인 모델`](file:///Users/limkeunhyeok/workspace/my-space-backend/docs/be-005-domain-model-rules.md)<br>[`BE-006 DB 설계·ERD`](file:///Users/limkeunhyeok/workspace/my-space-backend/docs/be-006-database-erd.md) | 메인 API(`apps/api`)와 AI 비서(`apps/ai`) 분리, 도메인 모델, Prisma ERD, RabbitMQ 인덱싱 |
| **오브젝트 스토리지 (RustFS) 연동**                       | [`ADR-0006 RustFS 전환`](architecture/adr/adr-0006-object-storage-migration-to-rustfs.md)<br>[`ARC-004 컨테이너·컴포넌트`](architecture/arc-004-containers-components.md)                                                                                                                                   | [`ARC-005 데이터 흐름`](architecture/arc-005-data-flow.md)<br>[`homelab-infra 전환 가이드`](file:///Users/limkeunhyeok/workspace/homelab-infra/docs/rustfs-migration-guide.md)                                                                                                             | MinIO CVE 패치 중단 대응, RustFS(Apache 2.0) S3 API 연동 및 `rustfs-pvc`                 |
| **검색 엔진 (OpenSearch vs Qdrant)**                      | [`ARC-004 컨테이너·컴포넌트`](architecture/arc-004-containers-components.md)<br>[`PRD-005 기능 명세`](product/prd-005-functional-spec.md)                                                                                                                                                                   | [`ARC-005 데이터 흐름 (DF-03)`](architecture/arc-005-data-flow.md)<br>[`PRD-003 사용자 스토리`](product/prd-003-user-stories.md)                                                                                                                                                           | OpenSearch 2.11 키워드 전문 검색 vs Qdrant 1.13 벡터 유사도 시맨틱 RAG 검색              |
| **Catch-up 스케줄러 & 알림**                              | [`ADR-0003 Catch-up 스케줄러`](architecture/adr/adr-0003-homelab-catchup-scheduler.md)<br>[`ADR-0005 알림 채널 추상화`](architecture/adr/adr-0005-notification-channel-abstraction.md)                                                                                                                      | [`PRD-004 여정과 업무흐름`](product/prd-004-user-journeys.md)<br>[`ARC-004 컴포넌트`](architecture/arc-004-containers-components.md)                                                                                                                                                       | 비-상시 구동 홈랩 부팅 시 누락 스케줄 소급 실행, 인앱 알림 우선 저장                     |
| **보안, SSO & 방문자 샌드박스**                           | [`ARC-007 보안 아키텍처`](architecture/arc-007-security-architecture.md)<br>[`ADR-0004 ForwardAuth·샌드박스`](architecture/adr/adr-0004-forward-auth-and-visitor-isolation.md)                                                                                                                              | [`ARC-005 데이터 흐름`](architecture/arc-005-data-flow.md)<br>[`PRD-006 비기능 요구사항`](product/prd-006-nfr.md)                                                                                                                                                                          | Authelia ForwardAuth, 주 사용자 영속 DB 격리, 방문자 인메모리 샌드박스                   |
| **로컬 브라우저 처리 (WASM/Worker)**                      | [`ADR-0001 하이브리드 연산`](architecture/adr/adr-0001-hybrid-client-server-processing.md)<br>[`PRD-005 기능 명세 (도구)`](product/prd-005-functional-spec.md)                                                                                                                                              | [`ARC-005 데이터 흐름 (DF-01)`](architecture/arc-005-data-flow.md)<br>[`PRD-006 NFR-001~006`](product/prd-006-nfr.md)                                                                                                                                                                      | 민감 파일/토큰 브라우저 내부 완결, 암호화 PDF만 백엔드 `qpdf` 위임                       |
| **비기능 품질 속성 & 성능 검증**                          | [`PRD-006 비기능 요구사항`](product/prd-006-nfr.md)<br>[`ARC-008 품질 속성`](architecture/arc-008-quality-attributes.md)                                                                                                                                                                                    | [`ARC-002 아키텍처 원칙`](architecture/arc-002-principles-constraints.md)                                                                                                                                                                                                                  | `NFR-001`~`008` 목표 수치, 품질 시나리오(`QS-01`~`07`), 트레이드오프                     |
| **구현 단계 및 릴리스 일정**                              | [`PRD-007 릴리스 범위·우선순위`](product/prd-007-release-scope-priority.md)                                                                                                                                                                                                                                 | [`PRD-002 요구사항과 범위`](product/prd-002-requirements-scope.md)                                                                                                                                                                                                                         | `v2.1`~`v2.3` 마일스톤, 8단계 세부 구현 순서 및 기능 배분                                |

---

## 📁 전체 문서 인덱스 (Catalog)

### 1. 제품 기획 (Product - `product/`)

| 문서                                                                | ID        | 설명                                                                         |
| :------------------------------------------------------------------ | :-------- | :--------------------------------------------------------------------------- |
| [제품 개요와 비전](product/prd-001-product-overview.md)             | `PRD-001` | `my-space` v2 재편 배경, 핵심 가치, 비목표, 홈랩 제약 및 테스트 계정 개요    |
| [요구사항과 범위](product/prd-002-requirements-scope.md)            | `PRD-002` | v2 포함/제외 범위, 요구사항 `REQ-001`~`REQ-031`, 에픽 `E-01`~`E-08`          |
| [사용자 스토리와 수용 기준](product/prd-003-user-stories.md)        | `PRD-003` | 사용자 스토리 `US-001`~`US-024`와 시나리오별 수용 기준(AC)                   |
| [사용자 여정과 업무 흐름](product/prd-004-user-journeys.md)         | `PRD-004` | 핵심 여정 `J-01`~`J-04`와 Catch-up·하이브리드 도구 업무 흐름 및 예외 처리    |
| [기능 명세](product/prd-005-functional-spec.md)                     | `PRD-005` | 기능 `FN-001`~`FN-025`의 상세 입력, 처리 단계, 결과 및 예외 규격             |
| [비기능 요구사항](product/prd-006-nfr.md)                           | `PRD-006` | 파서 속도, WASM 성능, Catch-up 복구율, PWA 오프라인 수치 `NFR-001`~`NFR-008` |
| [릴리스 범위와 우선순위](product/prd-007-release-scope-priority.md) | `PRD-007` | 플랫폼 기반·공간·도구·테스트 계정 8단계 구현 마일스톤(`v2.1`~`v2.3`)         |

### 2. 디자인 명세 (Design - `design/`)

| 문서                                                                  | ID        | 설명                                                                           |
| :-------------------------------------------------------------------- | :-------- | :----------------------------------------------------------------------------- |
| [디자인 브리프](design/dsn-001-design-brief.md)                       | `DSN-001` | Calm Studio Workspace 미학, 정보 밀도, 레이아웃/모션 원칙                      |
| [정보 구조와 사이트맵](design/dsn-002-information-architecture.md)    | `DSN-002` | 공간(5영역)·도구(15종) 2단 내비게이션, 전체 화면(`SCR-001`~`SCR-090`) 사이트맵 |
| [사용자 흐름](design/dsn-003-user-flows.md)                           | `DSN-003` | 대시보드, 노트 스튜디오, 피드 리더, PDF, 테스트 계정 핵심 화면 전환 흐름       |
| [와이어프레임과 화면 명세](design/dsn-004-wireframes-screen-spec.md)  | `DSN-004` | `SCR-001`~`SCR-090` 전체 화면의 구역 분할, 주요 요소 배치, 사용자 상호작용     |
| [디자인 시스템과 디자인 토큰](design/dsn-005-design-system-tokens.md) | `DSN-005` | CSS 변수 토큰(`--color-*`, 폰트, 간격, 모서리 곡률, 그림자) 정의               |
| [컴포넌트 가이드](design/dsn-006-component-guide.md)                  | `DSN-006` | 앱 셸, 버튼, 입력, 뱃지, 토스트, 모달 등 재사용 공통 컴포넌트 규격             |
| [반응형 디자인](design/dsn-007-responsive.md)                         | `DSN-007` | 데스크톱·태블릿·모바일 브레이크포인트 및 모바일 뷰포트 적응 규칙               |
| [접근성 가이드](design/dsn-008-accessibility.md)                      | `DSN-008` | WCAG 2.1 AA 명도 대비, 키보드 탭 내비게이션, 포커스 링, 스크린 리더 랜드마크   |
| [UI 상태와 오류 상태](design/dsn-009-ui-states-errors.md)             | `DSN-009` | 빈 화면, 로딩, 유효성 실패, 오프라인 배너, 테스트 계정 차단 등 상태 피드백     |

### 3. 시스템 아키텍처 (Architecture - `architecture/`)

| 문서                                                                      | ID        | 설명                                                                                              |
| :------------------------------------------------------------------------ | :-------- | :------------------------------------------------------------------------------------------------ |
| [시스템 아키텍처 개요](architecture/arc-001-system-overview.md)           | `ARC-001` | 하이브리드 클라이언트-서버 및 홈랩 k3s 아키텍처, C4 구성 다이어그램                               |
| [아키텍처 원칙과 제약](architecture/arc-002-principles-constraints.md)    | `ARC-002` | 로컬-퍼스트 보안, Catch-up 복원력 등 6대 원칙과 기술·환경 제약                                    |
| [시스템 컨텍스트와 외부 시스템](architecture/arc-003-system-context.md)   | `ARC-003` | 사용자·방문자 및 5대 외부 연동(Authelia, LLM, RSS, Slack, 공공데이터) 경계                        |
| [컨테이너와 컴포넌트 구조](architecture/arc-004-containers-components.md) | `ARC-004` | 8개 컨테이너(`web`, `api`, `ai`, `PostgreSQL`, `RustFS`, `OpenSearch`, `Qdrant`, `RabbitMQ`) 책임 |
| [데이터 흐름](architecture/arc-005-data-flow.md)                          | `ARC-005` | 핵심 시나리오 6종(`DF-01`~`DF-06`) 데이터 이동 경로, 신뢰 경계, 저장소 매핑                       |
| [아키텍처 의사결정 기록 (ADR 색인)](architecture/arc-006-adr.md)          | `ARC-006` | 전체 ADR 목록, 결정 상태 관리 기준 및 단일 저장소 ADR과의 경계                                    |
| [보안 아키텍처](architecture/arc-007-security-architecture.md)            | `ARC-007` | 3대 신뢰 경계, ForwardAuth 인증, 주 사용자 데이터 격리, STRIDE 통제                               |
| [품질 속성](architecture/arc-008-quality-attributes.md)                   | `ARC-008` | `NFR` 달성을 위한 7개 품질 시나리오(`QS-01`~`QS-07`), 달성 전술 및 트레이드오프                   |

### 4. 아키텍처 의사결정 기록 (ADR - `architecture/adr/`)

| 문서                                                                                                          | ID         | 결정 내용 요약                                                                  |
| :------------------------------------------------------------------------------------------------------------ | :--------- | :------------------------------------------------------------------------------ |
| [민감 도구 로컬 처리 및 백엔드 하이브리드 분리](architecture/adr/adr-0001-hybrid-client-server-processing.md) | `ADR-0001` | 민감 파일/토큰은 브라우저(Web Worker·WASM)에서 완결, 암호화 PDF만 `qpdf` 위임   |
| [백엔드 api와 ai 프로세스 분리](architecture/adr/adr-0002-backend-monorepo-api-ai-separation.md)              | `ADR-0002` | NestJS 모노레포 안에서 `api`와 `ai`를 독립 컨테이너로 분리하여 AI 부하 격리     |
| [홈랩 비-상시 구동 Catch-up 스케줄러](architecture/adr/adr-0003-homelab-catchup-scheduler.md)                 | `ADR-0003` | 홈랩 기동 시 `lastRunAt` 기준으로 누락된 결제·만료일·RSS 수집을 멱등 복구       |
| [ForwardAuth 위임 및 방문자 샌드박스 격리](architecture/adr/adr-0004-forward-auth-and-visitor-isolation.md)   | `ADR-0004` | Authelia SSO 헤더 검증 및 테스트 계정(`visitor`) 인메모리 샌드박스 격리         |
| [인앱 알림함 우선 저장 및 알림 채널 추상화](architecture/adr/adr-0005-notification-channel-abstraction.md)    | `ADR-0005` | 인앱 알림함(DB)에 우선 저장 후 Slack Incoming Webhook 어댑터로 비동기 발송      |
| [오브젝트 스토리지 RustFS(Apache 2.0) 전환](architecture/adr/adr-0006-object-storage-migration-to-rustfs.md)  | `ADR-0006` | MinIO CVE 패치 중단 대응을 위해 완전 무료 오픈소스인 RustFS(S3 API 호환)로 전환 |

### 5. 운영 및 공통 기반 (Operations - `operations/`)

| 문서                                                | ID        | 설명                                                                     |
| :-------------------------------------------------- | :-------- | :----------------------------------------------------------------------- |
| [용어집](operations/ops-001-glossary.md)            | `OPS-001` | 공간·도구, 날짜 모델 3유형, Catch-up, ForwardAuth 등 공통 표준 용어 사전 |
| [문서 인덱스](operations/ops-002-document-index.md) | `OPS-002` | `my-space-docs`에 등록된 문서의 상태, 버전, 검토일, 메타데이터 총괄     |

### 6. 인프라 운영 명세 (Infrastructure - `infrastructure/`)

| 문서 | ID | 설명 |
|---|---|---|
| [백업과 복구](infrastructure/inf-009-backup-recovery.md) | `INF-009` | PostgreSQL·RustFS 공동 복원 지점과 격리 복원 기준 |
| [모니터링·로그·추적·알림](infrastructure/inf-010-observability.md) | `INF-010` | 가동 중 수집 신호, NFR 판정, 운영 경보와 수신 경로 |
| [인프라 운영 절차](infrastructure/inf-011-ops-procedures.md) | `INF-011` | 재기동·원본 복구·재색인·롤백·관측 점검 절차 |

배포 구조·환경·접근 제어·CI/CD·시크릿 명세는 [전체 문서 인덱스](operations/ops-002-document-index.md)에서 확인한다.

---

## 🔗 연계 Repository와의 관계

`my-space` 프로젝트는 역할별로 독립된 Repository로 분리되어 있으며, 상위 기획과 세부 구현이 일관되게 연결됩니다.

| Repository                      | 역할                          | 주요 다루는 내용                                                                        |
| :------------------------------ | :---------------------------- | :-------------------------------------------------------------------------------------- |
| **`my-space-docs` (본 저장소)** | 프로젝트 통합 기획 & 아키텍처 | 전체 제품 요구사항(`PRD`), 공통 디자인 시스템(`DSN`), 상위 C4 아키텍처(`ARC`), 전역 ADR |
| **`my-space-front`**            | 프론트엔드 웹 애플리케이션    | React 19, Vite, PWA, Web Worker/WASM 로컬 연산, 디자인 토큰 적용 (`apps/web`)           |
| **`my-space-backend`**          | 백엔드 서비스 모노레포        | NestJS 12 모노레포(`apps/api`, `apps/ai`), Prisma 스키마, RabbitMQ, S3/RustFS 연동      |
| **`homelab-infra`**             | 홈랩 쿠버네티스 인프라        | Windows WSL2 k3s 클러스터 매니페스트, Traefik, Authelia, PostgreSQL, RustFS, Qdrant     |

---

## 📌 문서 관리 및 수정 원칙

1. **단일 진실 공급원 (Single Source of Truth)**: 프로젝트 전역 비즈니스 룰, 화면 와이어프레임, 컴포넌트 규격, 통신 인터페이스는 본 Repository를 기준으로 합니다.
2. **AI 작업 전 라우팅 맵 확인**: AI 에이전트에게 구현 지시를 내리기 전, 상단의 **Routing Map**을 통해 관련 문서를 지정하거나 에이전트가 먼저 읽도록 유도합니다.
3. **일관된 버전 관리**: 아키텍처 결정 사항(예: 스토리지 변경 등)이 발생할 경우 본문의 ADR을 추가하고 관련 `ARC`, `PRD` 문서를 동기화합니다.
