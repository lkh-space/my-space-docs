---
id: BE-002
title: API 설계 가이드
part: backend
tier: conditional
condition: API
status: approved
version: 1.1.2
owner: '@bob (Keunhyeok Lim)'
last_reviewed: '2026-11-22'
depends_on:
- BE-001
- ADR-0008
- ADR-0009
related:
- BE-003
- BE-004
- BE-013
- FE-003
- FE-005
superseded_by: null
traces:
- REQ-003
- REQ-004
- REQ-011
- REQ-012
- REQ-028
---

# BE-002 API 설계 가이드

## 기준과 적용 범위

v2 HTTP 계약의 검토안이다. [BE-003](be-003-api-spec.md)과 [openapi.yaml](openapi.yaml)이 오퍼레이션·필드의 기준이며, v1 컨트롤러의 경로 변경이 이미 완료되었다는 뜻이 아니다. 인증은 [BE-004](be-004-authn-authz.md), 도메인은 [BE-005](be-005-domain-model-rules.md), 오류는 [BE-013](be-013-error-handling.md)를 따른다.

## 경로와 데이터

- 외부 경로는 `/api/v1`을 유지한다. 제품 v2와 API 경로의 v1은 서로 다른 버전이다. 실제 배포 도메인은 INF 설정으로 공급하며 문서 예시 호스트를 운영 주소로 단정하지 않는다.
- 리소스는 복수형 kebab-case, JSON 필드는 camelCase다. 일시는 UTC ISO 8601, 날짜는 `YYYY-MM-DD`이며 날짜 계산은 `Asia/Seoul` 기준이다. 금액은 KRW 원 단위 정수다.
- OpenAPI 문서 형식은 3.1.1을 사용한다. 이번 검토안은 JSON Schema 기반 계약을 작성하는 범위다. 기존 Swagger 생성·클라이언트 생성 도구와의 통합 호환성은 구현 단계에서 검증한다. [OpenAPI 3.1.1 명세](https://spec.openapis.org/oas/v3.1.1.html)
- 현재 v1의 `/api/v1/markdown/*`, `/api/v1/pdf/*`와 v2 계약 경로는 다르다. v1 클라이언트를 함께 운영할 경우 기존 경로를 즉시 제거하지 않고 어댑터 또는 별도 배포 경계를 정해야 한다. 동시 운영·데이터 이전이 확정되지 않은 상태에서 자동 마이그레이션을 전제하지 않는다.

## 메서드와 재요청

| 메서드 | 용도 | 계약 |
|---|---|---|
| GET | 조회 | 데이터 변경 없음, 200 |
| POST | 생성 또는 명시적 액션 | 생성 201, 액션 200, 비동기 접수 202. 중복 보호 대상은 명세에 표시 |
| PUT | 전체 값 지정 | 같은 요청은 같은 의미의 결과. 노트 새 리비전은 멱등 키로 중복 생성 방지 |
| PATCH | 일부 값 지정 | 자동으로 멱등이라 가정하지 않음. 읽음/북마크는 `isRead`/`isStarred` 목표 값을 전송 |
| DELETE | 삭제 | 성공 204. 재요청도 삭제 완료 상태 유지 |

`toggleFeedItemStar` operationId는 기존 추적 식별자로 유지하지만 실제 입력은 `isStarred` 목표 값이다. 서버가 현재 값을 뒤집는 요청으로 해석하지 않는다.

## 노트 저장의 멱등성과 동시 수정

기준은 [ADR-0008](../architecture/adr/adr-0008-note-write-consistency.md)이다. `createDocument`, `updateDocument`, `restoreDocRevision`, `saveChatMessageToNote`, 내부 `saveMarkdownInternal`은 `Idempotency-Key`를 요구한다. 키는 UUID, 범위는 `(ownerId, operationId, key)`다. 서버는 저장 완료 시점부터 24시간 동안 같은 키·같은 입력의 요청에 기존 상태 코드와 결과를 반환한다.

1. 동일 키·동일 입력의 완료 요청은 기존 상태 코드와 결과를 반환한다. 재시도 시 리비전·이벤트를 다시 만들지 않는다.
2. 동일 키·다른 입력은 `409 ERR_IDEMPOTENCY_MISMATCH`, 처리 중인 요청은 `409 ERR_REQUEST_IN_PROGRESS`다. 클라이언트는 처리 중 오류에 같은 키로 재시도한다.
3. 본문 해시와 실행권을 먼저 확보하고, 저장 결과와 Outbox를 노트 커밋에 결합한다. 커밋 전 장애는 복구 가능한 미완료 상태이며 새 키를 강요하지 않는다. 같은 키의 이전 실행권을 회수한 뒤 재시도한다.
4. 수정·복원·삭제는 `expectedVersion`을 요구한다. 현재 버전과 다르면 `409 ERR_NOTE_VERSION_CONFLICT`; 기존 본문을 덮어쓰지 않는다. UI가 최신 문서를 조회해 비교한 뒤 새 사용자 작업으로 다시 요청한다.
5. AI 내부 저장도 외부 요청의 재시도 식별자를 끝까지 유지한다. 보관 기간 이후 재요청은 새 작업이 될 수 있으므로 응답 유실은 기간 내 조회·재시도로 해결한다.

헤더 이름과 보관 기간은 프로젝트 계약이며 HTTP 표준이 모든 API에 이 값을 강제한다는 뜻이 아니다.

## 목록 응답

페이지 목록은 `{items, meta}`다. 오프셋은 `page` 기본 1, `limit` 기본 20·최대 100, meta는 `currentPage`, `limit`, `totalCount`, `totalPages`다. 무한 목록은 불투명 `cursor`, `limit` 기본 30·최대 100, meta는 `nextCursor`(마지막이면 null), `hasMore`다. 동일 정렬 키에서는 ID로 안정적인 순서를 보장한다.

문서·일정·출처·규칙·세션·감사 기록은 오프셋, 피드 글·알림·대화 메시지는 커서다. 폴더 트리·태그·리비전·검색 결과처럼 명시적으로 비페이지 응답인 항목은 `{items}`를 사용하며 명세에 예외를 표시한다. 임의의 bare array로 반환하지 않는다.

## SSE와 중단

`streamTextChat` 및 `streamVoiceAndChat`은 `text/event-stream`이며 캐시하지 않는다. 시작 전에 인증·소유권·입력을 검증한다. HTTP 연결 전에 실패하면 Problem 응답을 반환한다. 연결 후에는 HTTP 상태를 바꾸지 않고 `error` 이벤트로 종료한다.

| 이벤트 | 내용 |
|---|---|
| start | `requestId`, `sessionId`, `messageId` |
| transcript | 음성 전사 `text` |
| token | 생성 중 `token` |
| audio | `chunk`(base64), `mimeType` |
| done | 완료 `messageId`, `totalTokens` |
| interrupt | 요청 중단 `requestId`, `reason` |
| error | `code`, 안전한 `detail`, 대화 요청의 `requestId`, HTTP 요청 추적 문맥의 `traceId` |

`interruptChat`은 현재 사용자 소유 `requestId`만 중단한다. 이미 종료된 요청은 성공적으로 종료된 상태를 반환하며 다른 사용자의 요청은 노출하지 않는다. 연결 종료·사용자 중단은 추론/TTS에 취소를 전달하고 `INTERRUPTED`로 기록한다. 업스트림 장애는 `FAILED`다. 자동 재연결로 같은 추론을 시작하지 않는다. 저장된 메시지를 조회한 뒤 새 요청을 사용자가 시작한다. 중단은 오류 코드가 아닌 정상 상태다.

## 업로드·외부 호출

PDF는 [ADR-0009](../architecture/adr/adr-0009-local-tool-execution-contract.md)의 파일별 50MB·총합 100MB·최대 20개 제한을 서버에서도 검사한다. 파일마다 비밀번호를 인덱스로 연결하고 입력 파일명으로 경로를 만들지 않는다. 범위 분할은 PDF, 낱장 분할은 ZIP이다. 연결 종료도 임시 자원 정리 사유다.

RSS/본문 수집 URL은 HTTP(S)만 허용하고 loopback·사설망·링크 로컬·클라우드 메타데이터 접근을 차단한다. DNS 확인과 리다이렉트마다 다시 검사한다. 외부 호출 시간·응답 크기·동시 실행 한도를 설정하며 초과는 BE-013의 오류 또는 백그라운드 재시도 상태로 변환한다. Readability 실패는 RSS 요약·원본 링크를 제공하고 전체 피드 조회를 실패시키지 않는다.

## 공통 요청 출처·응답 캐시 계약

공개 쿠키 인증 API의 모든 POST/PUT/PATCH/DELETE는 [BE-004](be-004-authn-authz.md)의 동일 출처 검사를 적용한다. JSON·multipart·AI SSE 시작 및 중단 모두 포함하며 `ERR_AUTH_ORIGIN_REJECTED`(403)는 본문 처리·외부 호출 전에 반환한다. 내부 서비스 경로는 별도 인증으로 외부 접근을 막는다. 개인 API·첨부·내보내기·PDF·신원 응답은 no-store이며 Service Worker 캐시에서 제외한다. OpenAPI의 `x-csrf-policy`는 문서 계약으로, 런타임 미들웨어와 계약 테스트 구현을 대체하지 않는다.

## SSE 프레이밍과 종료 판정

SSE의 `event:`는 위 표의 이름, `data:`는 해당 필드의 JSON이다. `[DONE]` 센티널을 사용하지 않는다. 텍스트는 `/api/v1/ai/chat/stream`, 음성은 `/api/v1/ai/voice/stream`이며 `start.requestId`로 `/api/v1/ai/chat/interrupt`를 호출한다. 정상 연결의 종료 이벤트는 done/interrupt/error 중 하나뿐이다. 종료 이벤트 없이 연결이 끊기면 클라이언트는 성공으로 간주하지 않고 메시지 상태를 조회한다. 취소 요청과 정상 완료가 경합하면 서버의 이미 확정된 종료 상태를 유지한다.

## 피드 일괄 처리와 홈 부분 성공

피드 목록과 모두 읽음의 필터는 동일하다. classification 생략은 EXCLUDED 제외이며 태그는 활성 관심 키워드 매칭 배지다. 페이지네이션의 snapshotAt은 collectedAt 상한으로 사용하며 키워드 재평가나 읽음 변경까지 고정한 DB 스냅샷을 의미하지 않는다. 일괄 읽음은 같은 소유자·필터·상한에 맞는 전체 미읽음 글을 원자적으로 변경한다.

홈 sections는 카드 선택 조회다. 인증·요청 오류는 HTTP Problem, 카드별 데이터 오류는 200 응답 내 ERROR/error로 구분한다. 빈 데이터 SUCCESS와 실패 ERROR를 혼동하지 않는다. 자세한 필드는 OpenAPI를 따른다.

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-22 | 1.1.2 | API·화면·검증 범위 계약 불일치 보완 | AI 에이전트 |
| 2026-11-22 | 1.1.1 | Origin 검사와 민감 응답 no-store 공통 계약 추가 | AI 에이전트 |
| 2026-11-12 | 1.0.0 | 최초 검토안 | @bob (Keunhyeok Lim) |
| 2026-11-22 | 1.1.0 | 실제 기술 설정 및 승인된 PRD·ADR·ARC와 정합성 보완. 사용자 검토 대기 | AI 에이전트 |
