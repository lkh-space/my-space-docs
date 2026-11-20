---
id: ARC-008
title: 품질 속성
part: architecture
tier: conditional
condition: 실무
status: approved
version: 1.2.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-20
depends_on: [PRD-006, ARC-004]
related: [ARC-002, ARC-006, ARC-007]
superseded_by: null
traces: [REQ-001, REQ-009, REQ-012, REQ-014, REQ-015, REQ-019, REQ-024, REQ-026, REQ-027, REQ-028, REQ-029, REQ-030, REQ-031, NFR-001, NFR-002, NFR-003, NFR-004, NFR-005, NFR-006, NFR-007, NFR-008]
---

# ARC-008 품질 속성

> v2 설계 승인 (2026-11-20): 사용자 검토 및 승인 완료. ADR-0007~0010은 2026-11-19 승인되었다. 문서 승인으로, v2 구현·시험 완료를 의미하지 않는다. 대체된 ADR 본문은 과거 결정 기록으로 보존한다.

## 이 문서는

- **목적**: [PRD-006 비기능 요구사항](../product/prd-006-nfr.md)에 정의된 품질 요구(`NFR-001`~`NFR-008`)를 아키텍처가 **어떻게** 달성하는지 기술한다. 품질 속성의 우선순위, 자극-응답 기반의 품질 시나리오(`QS-`), 달성 전술, 전술 간 트레이드오프를 다룬다.
- **기준 문서가 아닌 것**: NFR의 정량 목표 수치와 측정 조건은 [PRD-006](../product/prd-006-nfr.md)이 단일 기준 문서다. 이 문서는 목표 수치를 중복 정의하지 않고 `NFR-` ID로만 참조한다.
- **대상 독자**: 프로젝트 소유자, 프론트엔드·백엔드·인프라 개발자, AI 에이전트
- **작성 시점**: 비기능 요구사항([PRD-006](../product/prd-006-nfr.md))과 컨테이너([ARC-004](arc-004-containers-components.md))가 정해진 뒤 작성한다.
- **등급**: 조건부 — 홈랩 Catch-up 복구, 대용량 WASM 이미지 처리, 하이브리드 PDF, 테스트 계정 격리 등 복합적인 품질 전술이 필요한 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [PRD-006 비기능 요구사항](../product/prd-006-nfr.md) | `NFR-001`~`NFR-008` ID, 품질 범주, 목표 수치의 기준 문서 |
| 선행 | [ARC-004 컨테이너와 주요 컴포넌트](arc-004-containers-components.md) | 달성 전술을 적용할 컨테이너와 컴포넌트 이름의 기준 |
| 관련 | [ARC-002 아키텍처 원칙과 제약](arc-002-principles-constraints.md) | 전술 선택을 규율하는 원칙(`AP-01`~`AP-06`)과 제약(`CON-01`~`CON-05`) |
| 관련 | [ARC-006 아키텍처 의사결정 기록(ADR)](arc-006-adr.md) | 핵심 전술을 채택한 이유와 기각한 대안(`ADR-0001`~`ADR-0005`) |
| 관련 | [ARC-007 보안 아키텍처](arc-007-security-architecture.md) | `NFR-006` 보안 시나리오의 신뢰 경계별 통제 위치 |

## 품질 속성 우선순위 [필수]

| 순위 | 품질 범주 | 관련 NFR | 우선하는 이유 |
|---|---|---|---|
| 1 | 보안 | [NFR-006](../product/prd-006-nfr.md#nfr-목록-필수) | 민감 파일·토큰을 외부 사이트에 유출하지 않고 처리하려는 것이 제품 존재 이유이며, 외부 방문자 테스트 계정이 주 사용자의 개인 데이터를 절대 침범해서는 안 되기 때문이다 |
| 2 | 안정성·가용성 | [NFR-004](../product/prd-006-nfr.md#nfr-목록-필수), [NFR-005](../product/prd-006-nfr.md#nfr-목록-필수) | 홈랩 서버가 며칠간 꺼져 있다가 재기동되어도 정기 결제일 갱신과 만료 알림이 단 한 건도 유실·중복되지 않아야 하며, 서버 종료 중에도 모바일/데스크톱 계산·변환 도구가 작동해야 하기 때문이다 |
| 3 | 성능 | [NFR-001](../product/prd-006-nfr.md#nfr-목록-필수), [NFR-002](../product/prd-006-nfr.md#nfr-목록-필수), [NFR-003](../product/prd-006-nfr.md#nfr-목록-필수) | 일회성 도구는 입력 즉시 결과가 나와야 반복 사용성이 유지되고, 고압축 이미지 변환이나 AI 추론 중에도 UI 프리징이나 공간 API 지연이 없어야 하기 때문이다 |
| 4 | 사용성·접근성 | [NFR-007](../product/prd-006-nfr.md#nfr-목록-필수), [NFR-008](../product/prd-006-nfr.md#nfr-목록-필수) | 데스크톱 기록 작업과 모바일 일정·단가 계산을 하나의 반응형 웹앱에서 명도 대비·키보드 접근성 저하 없이 일관되게 수행해야 하기 때문이다 |

## 품질 시나리오 [필수]

[근거: 실무 관행 · arc42 — 품질 요구를 출처·자극·환경·대상·응답·응답 측정 형식의 시나리오로 구체화한다]

| ID | NFR | 출처 | 자극 | 환경 | 대상 | 응답 | 응답 측정 |
|---|---|---|---|---|---|---|---|
| QS-01 | [NFR-006](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 / 외부 방문자 | 사용자가 이미지·EXIF·JWT 도구를 실행하거나, 암호화 PDF를 업로드하거나, 외부 방문자가 테스트 계정으로 공간 API를 호출함 | 평상시 및 오류 유발 시 | `web`, `api`, `ai` | 로컬 도구는 외부 통신 없이 처리하고, `qpdf`는 비밀번호를 `stdin`으로만 전달 후 임시 파일을 즉시 삭제하며, 테스트 계정은 브라우저 메모리에서만 변경을 유지하고 새로고침하면 초기화한다 | 로컬 도구 네트워크 요청 수, CLI 인자 노출 수, 임시 파일 잔존 시간, 계정 간 교차 접근 건수 ([NFR-006 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-02 | [NFR-004](../product/prd-006-nfr.md#nfr-목록-필수) | 홈랩 인프라 | 홈랩 물리 서버가 수일~수주간 전원이 꺼져 있다가 다시 부팅되어 `api` 컨테이너가 기동됨 | 서버 재기동 직후 | `api` (`Catchup & NotificationModule`), `PostgreSQL` | 기동 직후 DB의 `lastRunAt`과 현재 시각을 비교해 누락된 정기 결제 주기 갱신, 만료일 판별, RSS 수집을 멱등하게 소급 실행하고 알림을 생성한다 | 서버 Ready 후 Catch-up 개시 지연 시간, 중복 생성 건수, 커밋 데이터 유실 건수 ([NFR-004 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-03 | [NFR-005](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 | 홈랩 서버가 꺼져 있거나 마트 등 모바일 오프라인 상태에서 단가 비교기·계산기·로컬 개발 도구에 진입함 | 백엔드 서버 종료 또는 네트워크 오프라인 | `web` (`CalcTools`, `DevTools`, Service Worker) | 캐시된 정적 에셋과 클라이언트 로직만으로 페이지를 로드하고 계산·파싱 기능을 정상 수행한다 | 오프라인 상태에서 대상 도구 페이지 로드 및 기능 실행 성공률 ([NFR-005 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-04 | [NFR-001](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 | DBML/NoSQL 스키마, 정규식, cron, 계산기 입력값 또는 일반 PDF·JPEG 파일을 입력함 | 평상시 (데스크톱/모바일 브라우저) | `web` (`DevTools`, `CalcTools`, `DocTools`, `ImageWorker`) | 서버 왕복 없이 브라우저 내 파서·바이너리 리더가 즉시 파싱·시각화·계산 결과를 렌더링한다 | 입력 이벤트 후 결과 렌더링 완료까지의 소요 시간 P95 ([NFR-001 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-05 | [NFR-002](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 | 고해상도 대용량 원본 이미지를 WebP 또는 AVIF 포맷으로 압축·변환 요청함 | 브라우저 변환 실행 중 | `web` (`ImageWorker`) | 이미지 디코딩·리사이즈·WASM 인코딩 연산을 전용 Web Worker 스레드에서 수행하고 메인 스레드는 진행률 UI와 취소 버튼 반응성을 유지한다 | 변환 중 메인 스레드 단일 Long Task 차단 시간 및 전체 변환 완료 시간 P95 ([NFR-002 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-06 | [NFR-003](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 | 공간(노트·피드·관리) 조회·저장을 요청하거나 암호화 PDF 해제를 요청함 (동시에 AI 인덱싱·스트리밍이 실행 중일 수 있음) | 홈랩 로컬 네트워크 평상시 및 AI 작업 병행 시 | `api`, `ai`, `PostgreSQL`, `RustFS` | `api`와 `ai`가 별도 프로세스로 분리되어 있고 노트 인덱싱은 `RabbitMQ`로 비동기 처리되므로 공간 CRUD와 `qpdf` 작업이 지연 없이 응답한다 | 일반 공간 API 및 `qpdf` 처리의 서버 응답 시간 P95 ([NFR-003 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |
| QS-07 | [NFR-007](../product/prd-006-nfr.md#nfr-목록-필수), [NFR-008](../product/prd-006-nfr.md#nfr-목록-필수) | 사용자 / 외부 방문자 | 데스크톱, 태블릿, 모바일 뷰포트 및 키보드 탭 탐색으로 모든 공간과 도구 화면을 조작함 | 전체 뷰포트 및 키보드/스크린리더 사용 시 | `web` (`AppShell`, 전체 화면) | 의미 기반 디자인 토큰으로 텍스트 대비를 확보하고, 모든 조작 요소에 대체 텍스트·포커스 링을 제공하며, 3단 브레이크포인트에 맞춰 레이아웃을 재배치한다 | 명도 대비율, 대체 텍스트 누락 건수, 뷰포트별 가로 오버플로·조작 차단 건수 ([NFR-007, NFR-008 목표값](../product/prd-006-nfr.md#nfr-목록-필수) 참조) |

## 달성 전술 [필수]

| 시나리오 | 전술 | 적용 위치 | 결정 | 검증 방법 |
|---|---|---|---|---|
| QS-01 | 로컬 도구 격리, PDF 임시 파일 수명 관리, 브라우저 체험 상태와 서버 인가 분리 | `web`, `api`, `ai` | ADR-0007, ADR-0009 | 네트워크 미전송·권한 분리·새로고침 초기화·강제 종료 파일 정리 검증 |
| QS-02 | **영속 타임스탬프 기반 멱등 Catch-up**:<br>1) 스케줄 작업별 `lastRunAt`과 항목별 `nextOccurrenceAt`을 `PostgreSQL`에 기록<br>2) `OnApplicationBootstrap` 훅에서 비동기로 누락 구간 계산 후 1회 일괄 보정<br>3) `(scheduleItemId, targetDate, daysBefore)` 유니크 키 및 피드 URL/GUID 해시로 중복 생성 방지 | `api` (`Catchup & NotificationModule`), `PostgreSQL` | [ADR-0003](adr/adr-0003-homelab-catchup-scheduler.md) | 과거 `lastRunAt` 시드 주입 후 서버 부팅 시뮬레이션 통합 테스트 ([PRD-006 NFR-004](../product/prd-006-nfr.md#nfr-목록-필수)) |
| QS-03 | FN-016~023 실행 자산을 최초 캐시 완료 시 확보. 기타 WASM은 해당 도구 진입 시 지연 다운로드·캐시 | `web`, Service Worker | ADR-0009 | 최초 캐시 후 미방문 필수 도구 오프라인 실행; 미캐시 추가 도구 안내 검증 |
| QS-04 | **Zero-Roundtrip 인메모리 파싱 & 바이너리 헤더 부분 읽기**:<br>1) 스키마·정규식·cron·계산기를 순수 TypeScript 함수로 구현<br>2) EXIF 파서는 전체 이미지를 디코딩하지 않고 `DataView`로 JPEG APP1/TIFF 헤더 세그먼트만 즉시 파싱 | `web` (`DevTools`, `CalcTools`, `ImageWorker`) | [ADR-0009](adr/adr-0009-local-tool-execution-contract.md) | Vitest 성능 타이머(`performance.now()`) 테스트 ([PRD-006 NFR-001](../product/prd-006-nfr.md#nfr-목록-필수)) |
| QS-05 | **전용 Web Worker 오프로딩 & `Transferable` 버퍼 이동**:<br>1) 무거운 이미지 리사이즈·WASM 코덱 인코딩을 Web Worker로 분리<br>2) 메인 스레드와 Worker 간 `ArrayBuffer` 복사 없이 소유권 이전(`Transferable`)으로 메모리 오버헤드 최소화 | `web` (`ImageWorker`) | [ADR-0009](adr/adr-0009-local-tool-execution-contract.md) | Chrome Performance Long Task 측정 및 Worker 완료 타이머 테스트 ([PRD-006 NFR-002](../product/prd-006-nfr.md#nfr-목록-필수)) |
| QS-06 | **프로세스 분리(`api`/`ai`) & 큐 기반 비동기 인덱싱 & 복합 인덱스**:<br>1) AI 스트리밍·인덱싱을 `ai` 컨테이너로 격리하고 노트 본문 저장 및 DB·Outbox 커밋 후 성공 응답하고 색인은 재시도 가능한 비동기 처리<br>2) `PostgreSQL`의 `(ownerId, updatedAt)` 등 복합 인덱스로 조회 최적화 | `api`, `ai`, `RabbitMQ`, `PostgreSQL` | [ADR-0002](adr/adr-0002-backend-monorepo-api-ai-separation.md) | 백엔드 HTTP 응답 시간 로깅 및 통합 테스트 ([PRD-006 NFR-003](../product/prd-006-nfr.md#nfr-목록-필수)) |
| QS-07 | **시맨틱 디자인 토큰 & 반응형 앱 셸**:<br>1) 명도 대비가 검증된 의미 토큰(`color.text.primary` 등)만 사용<br>2) 데스크톱(확장/축소 사이드바) → 태블릿(축소/드로어) → 모바일(하단 탭/단일 패널 전환) 3단 반응형 적용 | `web` (`AppShell`, 전체 컴포넌트) | [DSN-005](../design/dsn-005-design-system-tokens.md), [DSN-008](../design/dsn-008-accessibility.md) | 컴포넌트 접근성(`aria-label`, 대비) 테스트 및 3단 뷰포트 검증 ([PRD-006 NFR-007, NFR-008](../product/prd-006-nfr.md#nfr-목록-필수)) |

## 트레이드오프 [필수]

| 전술 | 좋아지는 속성 | 나빠지는 속성 | 받아들인 이유 | 결정 |
|---|---|---|---|---|
| 민감 도구 연산의 브라우저 로컬 처리 (Web Worker·WASM) 및 하이브리드 PDF 분리 | 보안(외부 유출 `0건`), 응답 속도, 오프라인 가용성, 홈랩 서버 자원 절약 | 초기 WASM 에셋 다운로드 크기 증가, 암호화·대용량·지원 불가 PDF를 백엔드로 분기하는 클라이언트 판별 로직 복잡도 증가 | WASM 코덱은 해당 도구 진입 시 지연 로드(Lazy-load) 후 PWA로 캐싱하면 재방문 부담이 없고, 프라이버시 보장이 최우선 가치이기 때문이다 | [ADR-0009](adr/adr-0009-local-tool-execution-contract.md) |
| 백엔드 `api`와 `ai` 프로세스 분리 및 `RabbitMQ` 비동기 인덱싱 | 안정성·성능 격리 (AI 프로세스 장애 전파를 줄임; 공유 DB·호스트 자원 경합은 별도 제한 필요), 노트 저장 응답 속도 향상 | 노트 저장 직후 수 초간 Qdrant 시맨틱 검색에 최신 수정본이 즉시 반영되지 않는 최종 일관성(Eventual Consistency) 지연, 배포 컨테이너 2종 관리 | 개인 워크스페이스에서 색인 지연을 허용하되 장애 시 재처리 상태를 추적하며, 노트 저장 자체의 즉각 응답이 훨씬 중요하다 | [ADR-0002](adr/adr-0002-backend-monorepo-api-ai-separation.md) |
| 기동 시 DB 기반 멱등 Catch-up 스케줄러 | 안정성 (서버 장기 종료 후 재기동 시에도 일정·피드 누락 `0건`), 외부 분산 스케줄러 불필요 | 서버가 꺼져 있는 동안에는 실시간 외부 알림(Slack)이 발송되지 않고 서버가 켜진 직후에 몰려서 발송됨 | 홈랩 물리 서버를 끄는 것은 사용자의 의도된 운영 방식이며, 부팅 즉시 누락 알림 브리핑을 받는 것이 요구사항(`REQ-009`)의 본질이다 | [ADR-0003](adr/adr-0003-homelab-catchup-scheduler.md) |
| 방문자 브라우저 메모리 체험 | 주 사용자 데이터와 변경분 분리 | 새로고침하면 변경 소멸, 체험 어댑터 유지 필요 | PRD-005 FN-025의 의도된 동작 | [ADR-0007](adr/adr-0007-visitor-browser-state.md) |

## 위험과 미해결 문제 [선택]

| 위험 | 영향 받는 시나리오 | 대응 계획 | 관련 문서 |
|---|---|---|---|
| 저사양 모바일 기기에서 초고해상도 이미지 다중 변환 시 브라우저 Worker 메모리 부족 가능성 | QS-05 ([NFR-002](../product/prd-006-nfr.md#nfr-목록-필수)) | 동시 변환 작업 수를 순차(Concurrency 1~2)로 제한하고, 입력 파일 크기·해상도 상한 안내 및 완료 즉시 `ArrayBuffer` 참조 해제 | [PRD-005 FN-013](../product/prd-005-functional-spec.md) |
| 서버 장기 종료(예: 30일) 후 재기동 시 구독 피드 30개를 동시에 수집하면 초기 네트워크·파싱 스파이크 발생 가능 | QS-02 ([NFR-004](../product/prd-006-nfr.md#nfr-목록-필수)) | Catch-up 작업은 서버 부팅을 블로킹하지 않고 백그라운드 비동기로 개시하되, 만료·결제 일정 판별(DB 로컬 연산)을 먼저 끝낸 뒤 외부 RSS 수집은 동시성 제한(예: 3~5개씩 배치)으로 순차 실행한다 | [ADR-0003](adr/adr-0003-homelab-catchup-scheduler.md) |

## 보장 범위와 실패 시나리오

오프라인 필수 범위는 PRD-006 NFR-005의 FN-016~023이다. 기타 도구는 자산 캐시 여부에 따라 달라지고 서버 PDF·AI·공간 저장은 제외한다. WASM 전체 선다운로드를 필수 조건으로 두지 않는다([ADR-0009](adr/adr-0009-local-tool-execution-contract.md)).

프로세스 분리는 장애 전파를 줄이지만 공유 호스트·DB 자원 경합까지 제거하지 않는다. 동시성·리소스 제한 및 AI 병행 부하 시험으로 NFR-003을 확인한다. 색인 지연 1~2초는 보장이 아니며 장애 시 지연과 재처리를 허용한다. QS-02는 개시뿐 아니라 PRD-006의 보정 완료 목표도 측정하고, 알림 키·체크포인트 원자성·발송 재개는 ARC-005를 따른다.

검증에는 본문 저장/DB 커밋/큐 발행 사이 강제 종료, AI 중단 후 복구, 색인 이벤트 순서 역전·중복·삭제, 방문자 새로고침 초기화, PDF 잔여 파일 정리를 포함한다. 목표 수치는 PRD-006을 참조한다.

## 검토 체크리스트

- [x] 목표 수치를 이 문서에 직접 쓰지 않았고, 모든 NFR-ID가 [PRD-006](../product/prd-006-nfr.md)에 존재한다
- [x] 우선순위 상위 NFR마다 품질 시나리오(`QS-01`~`QS-07`)가 하나 이상 있다
- [x] 시나리오마다 응답 측정 방법이 명시되어 있다
- [x] 전술마다 적용 위치와 검증 방법이 있다
- [x] 핵심 전술마다 트레이드오프 표에 나빠지는 속성이 명시되어 있다
- [x] 적용 위치의 구성 요소 이름이 [ARC-004](arc-004-containers-components.md)와 일치한다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-05 | 0.1.0 | my-space v2 품질 속성(ARC-008) 최초 작성 | Antigravity (사용자 검토 대기) |
| 2026-11-12 | 1.0.0 | 아키텍처 검토 완료 및 정식 승인 | @bob (Keunhyeok Lim) |
| 2026-11-16 | 1.1.0 | v2 문서 정합성·실패 복구 경계 수정, 승인 대기 | AI 에이전트 |
| 2026-11-19 | 1.1.1 | ADR 4건 승인·대체 관계 반영; ARC 검토 상태 유지 | @bob (Keunhyeok Lim) |
| 2026-11-20 | 1.2.0 | 사용자 최종 검토 완료 및 ARC 문서 정식 승인 | @bob (Keunhyeok Lim) |
