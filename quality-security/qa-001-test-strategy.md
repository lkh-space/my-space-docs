---
id: QA-001
title: 테스트 전략
part: quality-security
tier: conditional
condition: 실무
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-26'
depends_on: [PRD-005, PRD-006, ARC-008]
related: [FE-003, BE-003, QA-005, QA-008]
superseded_by: null
traces: [NFR-003, NFR-004, NFR-005, NFR-006, NFR-007]
---

# QA-001 테스트 전략

## 테스트 범위와 환경

웹 화면에서 동일 출처 Ingress를 거쳐 API·AI에 도달하고, 노트 저장이 색인·검색으로 이어지는 흐름을 검증한다. 테스트 계정과 합성 문서·파일을 사용한다. v2는 빈 DB에서 시작한다. v1 운영 데이터 이관은 적용 대상에서 제외한다. [기능 명세](../product/prd-005-functional-spec.md) · [품질 목표와 측정 조건](../product/prd-006-nfr.md)

## 위험과 테스트 케이스

| ID | 우선순위·영역 | 입력과 기대 결과 | 실행 근거 |
|---|---|---|---|
| TC-V2-01 | 최우선 · 세션 격리 | OWNER 조회 후 GUEST로 전환한다. 이전 본문과 지연 응답은 사라지고 체험 편집은 새로고침하면 초기화된다. 개인 API는 GUEST에게 403이다. | [프런트 QA](../../my-space-front/docs/qa/frontend-qa-specification.md), [HTTP 계약](../../my-space-backend/test/api/v2-http.e2e-spec.ts), 실제 ForwardAuth |
| TC-V2-02 | 최우선 · 노트 무결성 | 동일 키 재시도에서 버전이 늘지 않는다. 충돌한 편집은 409다. 삭제 후 재색인해도 전문·벡터 검색에 다시 나타나지 않는다. | [노트 생명주기](../../my-space-backend/test/api/v2-note-lifecycle.e2e-spec.ts), [재색인](../../my-space-backend/test/ai/reindex.e2e-spec.ts) |
| TC-V2-03 | 높음 · AI | 텍스트·음성 시작, 정상 완료, 중단, 공급자 실패를 나눠 본다. 시작 후 오류에는 requestId와 traceId가 있고 EOF만 받은 화면은 메시지를 재조회한다. | [AI E2E](../../my-space-backend/test/ai/chat.e2e-spec.ts), 스테이징 브라우저·실제 공급자 |
| TC-V2-04 | 높음 · 피드·일정 | 두 페이지 사이 새 글 수집 후 이전 snapshotAt으로 모두 읽음 처리한다. 새 글은 미읽음이다. 고정 만료일을 갱신하고 재기동해도 같은 알림이 중복되지 않는다. | [피드·Catch-up 테스트](../../my-space-backend/test/api/feed-catchup.e2e-spec.ts) |
| TC-V2-05 | 높음 · PDF·로컬 도구 | 지원되는 비암호화 합계 20 MiB 이하 병합은 로컬에서 끝난다. 서버 경계의 입력은 사용자 실행 후 전송한다. 취소·실패 후 임시 객체 정리를 확인한다. | [도구 계약](../architecture/adr/adr-0009-local-tool-execution-contract.md), 브라우저 Network·서버 임시 디렉터리 |
| TC-V2-06 | 높음 · 복구·관측 | 격리 환경에서 DB와 객체를 같은 복원점으로 복구한다. 본문·첨부·검색을 확인하고 테스트 경보의 실제 수신 시각을 기록한다. | [복구 절차](../infrastructure/inf-009-backup-recovery.md), [관측 기준](../infrastructure/inf-010-observability.md) |
| TC-V2-07 | 화면 회귀 · 접근성·성능 | 주요 화면을 키보드로 탐색하고 오류·로딩·빈 상태에서 포커스와 레이블을 확인한다. PRD-006의 대상별 지연·오프라인 기준에 측정값을 대조한다. | [접근성 기준](../design/dsn-008-accessibility.md), [NFR 측정표](../product/prd-006-nfr.md) |

## 테스트 계층과 실행 시점

변경 중에는 날짜·분류·상태 전이를 단위 테스트로 반복한다. PR에서는 클라이언트 요청·응답과 PostgreSQL 트랜잭션을 통합 테스트로 확인한다. 고정 비율 대신 다수의 단위 테스트와 경계별 통합 테스트를 유지하고, 실제 인증·공급자·복구 경로는 동일 릴리스 후보로 스테이징에서 확인한다. 대역으로 얻은 결과에는 대체한 의존성을 명시한다.

백엔드에서는 `ruby scripts/check-openapi.rb`, `pnpm run build`, `pnpm test`, `pnpm run test:e2e`를 사용한다. 프런트에서는 `pnpm typecheck`, `pnpm lint`, `pnpm test`, `pnpm build`와 브라우저 시나리오를 사용한다. CI에서 실행하는 일부 E2E와 전체 E2E를 기록에서 구분한다. [백엔드 CI](../../my-space-backend/.github/workflows/ci.yml)

## 완료 기준과 결함 관리

실행자가 후보 SHA·환경·테스트 ID·기대값·실측값·증거 위치를 기록한다. 실패하면 영향 범위와 재검증 대상을 붙인다. 기능 담당자가 수정 후보에서 해당 케이스를 재실행하고 프로젝트 소유자가 배포 여부를 결정한다. 미실행 경계는 QA-008 결과에 남긴다. [릴리스 판정표](qa-008-release-verification-regression.md)

## 변경 이력

| 날짜 | 버전 | 변경 내용 |
|---|---|---|
| 2026-11-26 | 0.1.0 | v2 검토 초안 작성 |
