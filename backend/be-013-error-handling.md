---
id: BE-013
title: 오류 처리
part: backend
tier: conditional
condition: API
status: approved
version: 1.1.2
owner: '@bob (Keunhyeok Lim)'
last_reviewed: '2026-11-26'
depends_on:
- BE-002
- BE-005
- ADR-0008
- ADR-0009
related:
- BE-003
- BE-004
- FE-009
superseded_by: null
traces:
- REQ-003
- REQ-004
- REQ-007
- REQ-009
- REQ-011
- REQ-012
- REQ-030
- NFR-006
---

# BE-013 오류 처리

## 범위와 기준

v2 오류 계약의 기준이며 [BE-005](be-005-domain-model-rules.md)의 규칙과 [OpenAPI](openapi.yaml)의 응답에 연결한다. HTTP 실패는 `application/problem+json`을 사용한다. 기준은 RFC 7807을 대체한 [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html)이다. 프로젝트 확장 필드는 `code`, `traceId`, `timestamp`, `invalidParams`다.

```json
{"type":"about:blank","title":"Conflict","status":409,"detail":"최신 문서를 확인해 주세요.","instance":"/api/v1/documents/문서ID","code":"ERR_NOTE_VERSION_CONFLICT","traceId":"요청추적ID","timestamp":"2026-11-22T00:00:00Z"}
```

status는 실제 HTTP 상태와 같고 title은 상태의 안정적인 요약이다. detail은 사용자에게 안전한 설명만 포함한다. invalidParams는 입력 필드명·사유 목록이며 비밀번호나 원본 입력값은 포함하지 않는다. 오류 메시지에 스택·SQL·경로·비밀 값·개인 본문을 노출하지 않는다. API 인증은 ForwardAuth/BE-004를 따르며 자체 JWT 로그인을 추가하지 않는다.

## 오류 코드

| code | HTTP | 원인 | 클라이언트 처리 |
|---|---|---|---|
| `ERR_COMMON_VALIDATION` | 400 | 입력 형식·필수값 검증 실패 | 입력 수정 |
| `ERR_AUTH_UNAUTHENTICATED` | 401 | 신뢰된 인증 문맥 없음 | 로그인 |
| `ERR_AUTH_FORBIDDEN` | 403 | 역할상 금지된 기능 | 권한 안내 |
| `ERR_AUTH_ORIGIN_REJECTED` | 403 | 쿠키 인증 변경 요청의 Origin 누락·불일치·null 또는 cross-site | 출처 오류 안내, 자동 재시도·인증 우회 금지 |
| `ERR_COMMON_NOT_FOUND` | 404 | 대상 없음 또는 다른 소유자 | 목록 새로고침 |
| `ERR_COMMON_CONFLICT` | 409 | 상태·중복·동시 실행 충돌 | 최신 상태 조회 |
| `ERR_IDEMPOTENCY_MISMATCH` | 409 | 같은 멱등 키에 다른 입력 | 새 사용자 작업이면 새 키 |
| `ERR_REQUEST_IN_PROGRESS` | 409 | 동일 요청 실행 중 | 같은 키로 지연 후 재시도 |
| `ERR_NOTE_VERSION_CONFLICT` | 409 | 기대 버전과 현재 버전 불일치 | 최신 본문 비교 |
| `ERR_NOTE_VERSION_IMMUTABLE` | 409 | 기존 리비전 직접 수정 시도 | 복원은 새 버전 생성 |
| `ERR_FOLDER_CIRCULAR_DEPENDENCY` | 400 | 자신·자손 폴더로 이동 | 다른 부모 선택 |
| `ERR_NOTE_DUPLICATE_TITLE` | 409 | 같은 소유자·폴더 제목 중복 | 제목 변경 |
| `ERR_FOLDER_DUPLICATE_NAME` | 409 | 같은 소유자·부모 이름 중복 | 이름 변경 |
| `ERR_NOTE_STORAGE_UPLOAD_FAILED` | 502 | 본문 저장 실패, DB 커밋 전 | 입력 보존 후 같은 키로 재시도 |
| `ERR_FEED_INVALID_URL` | 400 | 유효하지 않거나 금지된 RSS URL | 주소 수정 |
| `ERR_FEED_DUPLICATE_SOURCE` | 409 | 기존 출처 중복 | 기존 출처 확인 |
| `ERR_FEED_FETCH_FAILED` | 502 | 외부 피드 수집 실패 | 기존 결과 유지·작업 재시도 |
| `ERR_FEED_DUPLICATE_RULE` | 409 | 동일 소유자의 정규화 키워드 중복(포함·제외 규칙 공통) | 기존 규칙 수정 |
| `ERR_SCHEDULE_INVALID_TYPE` | 400 | 지원하지 않는 날짜 유형 | 3유형 중 선택 |
| `ERR_SCHEDULE_INVALID_RENEWAL` | 400 | 고정 만료 아님 또는 새 날짜가 기존 날짜 이후가 아님 | 유형·날짜 수정 |
| `ERR_SCHEDULE_VERSION_CONFLICT` | 409 | 일정 버전 충돌 | 최신 일정 조회 |
| `ERR_AI_QUOTA_EXCEEDED` | 429 | 제공자/서비스 자원 한도 | 대기 후 새 요청 |
| `ERR_AI_UPSTREAM_FAILED` | 502 | LLM/STT/TTS 제공자 실패 | 텍스트 보존 |
| `ERR_AI_VECTOR_SEARCH_FAILED` | 502 | 벡터 검색 실패 | 재시도 안내 |
| `ERR_AI_MESSAGE_NOT_COMPLETE` | 409 | 완료되지 않은 응답을 노트로 저장 시도 | 완료 응답 선택 |
| `ERR_PDF_INVALID_FILE` | 400 | 손상되거나 PDF가 아닌 입력 | 파일 교체 |
| `ERR_PDF_INVALID_RANGE` | 400 | 범위 문법·페이지 범위 오류 | 범위 수정 |
| `ERR_PDF_PASSWORD_REQUIRED` | 400 | 암호화 입력의 비밀번호 누락 | 파일별 암호 입력 |
| `ERR_PDF_WRONG_PASSWORD` | 422 | 비밀번호 불일치 | 해당 파일 비밀번호 수정 |
| `ERR_PDF_LIMIT_EXCEEDED` | 413 | 개별/합계/파일 개수 초과 | 입력 축소 |
| `ERR_PDF_UNSUPPORTED` | 422 | 서버 엔진도 처리할 수 없는 입력 | 지원 안내 |
| `ERR_PDF_TIMEOUT` | 504 | 처리 시간 초과 | 작업 축소 |
| `ERR_COMMON_BUSY` | 503 | 동시 작업 한도 또는 일시 사용 불가 | Retry-After 후 재시도 |
| `ERR_HOLIDAY_UNAVAILABLE` | 503 | 사용 가능한 공휴일 캐시와 원천 모두 없음 | 정확한 영업일 계산 보류 |
| `ERR_COMMON_INTERNAL_SERVER` | 500 | 분류되지 않은 내부 실패 | 안전한 메시지·추적 ID |

## 비동기 작업과 SSE의 경계

- 커밋 후 OpenSearch/Qdrant 실패는 저장 API의 오류가 아니다. `ERR_NOTE_INDEXING_FAILED`를 저장 실패 500으로 사용하지 않는다. Outbox/search projection의 실패·재시도로 기록하고 검색 지연을 표시한다.
- Readability 실패는 `contentStatus=FAILED`와 RSS 요약·원본 링크를 반환한다. 별도 422로 전체 글 조회를 실패시키지 않는다.
- Catch-up 실패는 실행 감사 기록과 체크포인트 미전진으로 표현한다. 감사 목록 조회 자체가 성공하면 200이다. 외부 알림 실패는 Delivery 상태이며 인앱 알림 생성 성공을 취소하지 않는다.
- SSE 시작 전에는 위 HTTP 오류를 반환한다. 시작 후에는 `event: error`의 `code/detail/requestId/traceId`를 보내고 생성 자원을 정리한다. 이미 보낸 HTTP 상태를 500으로 바꾸지 않는다. 중단은 `interrupt` 이벤트와 INTERRUPTED 상태이며 `ERR_AI_INTERRUPTED` 오류로 분류하지 않는다.
- 고정 만료 갱신은 정상 기능이다. 기존 `ERR_SCHEDULE_RENEWAL_MISMATCH`를 폐기하고 잘못된 새 날짜/유형만 `ERR_SCHEDULE_INVALID_RENEWAL`로 검증한다.

## 실제 구현과의 차이

현재 `libs/common/src/filters/all-exceptions.filter.ts`는 Problem 형태를 반환하지만 `ApiException`, `BaseDomainException`, `HttpException`, Prisma 오류를 별도로 해석하고 `details` 및 레거시 `statusCode/message/path`를 포함한다. 현재 코드가 이 문서의 ERR 코드/invalidParams와 이미 일치한다고 주장하지 않는다.

v2 구현 시 오류 변환을 한 곳에 두고 실제 예외를 위 계약으로 매핑한다. 레거시 클라이언트를 함께 운영한다면 호환 필드 제거 전에 영향을 확인한다. SSE는 연결 상태에 맞는 전용 종료 경로를 사용한다. 이미 응답한 요청에 전역 필터가 JSON을 다시 쓰지 않도록 한다.

## 검증

HTTP 상태·Problem.status 일치, 선언된 code, 소유자/방문자 거부, 필드 검증, 중복 키·버전 충돌, PDF 파일별 오류, 저장 후 색인 실패, SSE 중간 오류, 외부 실패 시 기존 데이터 유지, 비밀·본문 비노출을 확인한다. 로그에는 traceId·operationId·안전한 코드와 서버 내부 원인만 제한해 남긴다. 비밀번호·인증 헤더·요청 본문 전체는 로그에 남기지 않는다.

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-26 | 1.1.2 | 피드 규칙 중복 조건을 BE-005 BR-011 및 BE-006의 고유 키와 일치시킴 | AI 에이전트 |
| 2026-11-22 | 1.1.1 | CSRF 요청 출처 거부 오류 코드 추가 | AI 에이전트 |
| 2026-11-12 | 1.0.0 | 최초 검토안 | @bob (Keunhyeok Lim) |
| 2026-11-22 | 1.1.0 | 실제 기술 설정 및 승인된 PRD·ADR·ARC와 정합성 보완. 사용자 검토 대기 | AI 에이전트 |
