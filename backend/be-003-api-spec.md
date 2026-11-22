---
id: BE-003
title: API 명세 (OpenAPI)
part: backend
tier: conditional
condition: API
status: approved
version: 1.1.1
owner: '@bob (Keunhyeok Lim)'
last_reviewed: '2026-11-22'
depends_on:
- BE-002
- BE-005
- PRD-005
related:
- BE-001
- BE-004
- BE-006
- BE-013
- FE-003
- FE-005
superseded_by: null
traces:
- REQ-002
- REQ-003
- REQ-004
- REQ-005
- REQ-006
- REQ-007
- REQ-008
- REQ-009
- REQ-010
- REQ-011
- REQ-012
- REQ-025
- REQ-026
- REQ-030
- REQ-031
---

# BE-003 API 명세 (OpenAPI)

## 기준과 검토 상태

경로·파라미터·요청·응답의 기준은 [openapi.yaml](openapi.yaml)이다. 아래 표는 추적용 목록이며 필드를 중복 정의하지 않는다. 명세 버전은 OpenAPI 3.1.1, 제품 계약 버전은 `2.0.0-review`다. 기존 `/api/v1` 경로 접두사는 유지한다. 현재 v1 구현에서 이 계약이 제공된다고 주장하지 않는다.

규약은 [BE-002](be-002-api-design-guide.md), 역할·신뢰 헤더는 [BE-004](be-004-authn-authz.md), 도메인 상태는 [BE-005](be-005-domain-model-rules.md), 데이터는 [BE-006](be-006-database-erd.md), 오류는 [BE-013](be-013-error-handling.md)를 따른다. 내부 저장 API는 외부 라우팅 대상이 아니다.

인증은 Ingress의 Authelia 세션 및 신뢰된 ForwardAuth 헤더다. 브라우저가 Remote-User를 보내 인증하는 방식이 아니다. OpenAPI의 쿠키명은 배포 설정과 대조해야 한다. 모든 개인 데이터 오퍼레이션은 서버에서 소유자를 필터링한다. 방문자는 samples·auth/me·PDF·공휴일만 인증 후 사용하며 개인 공간 변경은 브라우저 메모리에서 수행한다.

## 오퍼레이션 목록

| operationId | 메서드 | 경로 | 요약 | 역할 | 요구사항 | ENT |
|---|---|---|---|---|---|---|
| getCurrentUser | GET | /api/v1/auth/me | 현재 인증 사용자 | OWNER,GUEST | REQ-030 | — |
| getSpaceSamples | GET | /api/v1/samples/{space} | 읽기 전용 체험 샘플 | OWNER,GUEST | REQ-026 | — |
| getHomeSummary | GET | /api/v1/home/summary | 소유자별 공간 요약 | OWNER | REQ-002 | — |
| getHolidays | GET | /api/v1/holidays | 한국 공휴일 캐시 | OWNER,GUEST | REQ-025 | ENT-020 |
| listFolders | GET | /api/v1/folders | 폴더 트리 구성용 전체 목록 | OWNER | REQ-003 | ENT-001 |
| createFolder | POST | /api/v1/folders | 폴더 생성 | OWNER | REQ-003 | ENT-001 |
| updateFolder | PATCH | /api/v1/folders/{id} | 폴더 이름·부모 변경 | OWNER | REQ-003 | ENT-001 |
| deleteFolder | DELETE | /api/v1/folders/{id} | 자식 폴더·문서를 루트로 이동 후 폴더 삭제 | OWNER | REQ-003 | ENT-001,ENT-002 |
| listTags | GET | /api/v1/tags | 소유자 태그 목록 | OWNER | REQ-003 | ENT-004 |
| listDocuments | GET | /api/v1/documents | 문서 목록 | OWNER | REQ-003 | ENT-002 |
| createDocument | POST | /api/v1/documents | 노트 저장 | OWNER | REQ-003 | ENT-002,ENT-003,ENT-014,ENT-015 |
| getDocument | GET | /api/v1/documents/{id} | 노트 본문 조회 | OWNER | REQ-003 | ENT-002 |
| updateDocument | PUT | /api/v1/documents/{id} | 새 리비전으로 저장 | OWNER | REQ-003 | ENT-002,ENT-003,ENT-014,ENT-015 |
| deleteDocument | DELETE | /api/v1/documents/{id} | 노트 삭제 및 삭제 색인 이벤트 기록 | OWNER | REQ-003 | ENT-002,ENT-014 |
| listDocRevisions | GET | /api/v1/documents/{id}/revisions | 리비전 목록 | OWNER | REQ-003 | ENT-003 |
| compareDocRevisions | GET | /api/v1/documents/{id}/revisions/compare | 두 리비전 비교 원문 | OWNER | REQ-003 | ENT-003 |
| getDocRevision | GET | /api/v1/documents/{id}/revisions/{version} | 과거 리비전 본문 | OWNER | REQ-003 | ENT-003 |
| restoreDocRevision | POST | /api/v1/documents/{id}/revisions/{version}/restore | 선택 리비전으로 새 버전 생성 | OWNER | REQ-003 | ENT-002,ENT-003,ENT-014 |
| uploadNoteAsset | POST | /api/v1/assets | 노트 이미지 업로드 | OWNER | REQ-003 | ENT-002,ENT-021 |
| getNoteAsset | GET | /api/v1/assets/{id} | 본인 노트 첨부 조회 | OWNER | REQ-003 | ENT-021 |
| exportDocument | GET | /api/v1/documents/{id}/export | 노트 파일 내보내기 | OWNER | REQ-003 | ENT-002 |
| searchNotes | GET | /api/v1/notes/search | 소유자 노트 전문 검색 | OWNER | REQ-003 | ENT-002 |
| listFeedSources | GET | /api/v1/feed-sources | RSS 출처 목록 | OWNER | REQ-004 | ENT-006 |
| createFeedSource | POST | /api/v1/feed-sources | RSS 출처 등록 | OWNER | REQ-004 | ENT-006 |
| updateFeedSource | PATCH | /api/v1/feed-sources/{id} | 출처 활성·중지 | OWNER | REQ-004 | ENT-006 |
| deleteFeedSource | DELETE | /api/v1/feed-sources/{id} | 출처와 소속 글 삭제 | OWNER | REQ-004 | ENT-006,ENT-007 |
| syncFeedSource | POST | /api/v1/feed-sources/{id}/sync | 출처 수집 작업 접수 | OWNER | REQ-004 | ENT-006,ENT-016 |
| listFeedItems | GET | /api/v1/feed-items | 피드 글 목록 | OWNER | REQ-004 | ENT-007 |
| getFeedItem | GET | /api/v1/feed-items/{id} | 피드 본문 및 추출 대체 결과 | OWNER | REQ-004 | ENT-007 |
| markFeedItemRead | PATCH | /api/v1/feed-items/{id}/read | 읽음 목표 값 설정 | OWNER | REQ-004 | ENT-007 |
| markAllFeedItemsRead | POST | /api/v1/feed-items/read-all | 현재 필터의 피드 글 모두 읽음 | OWNER | REQ-004 | ENT-007 |
| toggleFeedItemStar | PATCH | /api/v1/feed-items/{id}/star | 북마크 목표 값 설정 | OWNER | REQ-004 | ENT-007 |
| listFilterRules | GET | /api/v1/feed-filter-rules | 키워드 규칙 목록 | OWNER | REQ-005 | ENT-008 |
| createFilterRule | POST | /api/v1/feed-filter-rules | 키워드 규칙 생성 | OWNER | REQ-005 | ENT-008 |
| updateFilterRule | PUT | /api/v1/feed-filter-rules/{id} | 키워드 규칙·가중치·활성 변경 | OWNER | REQ-005 | ENT-008 |
| deleteFilterRule | DELETE | /api/v1/feed-filter-rules/{id} | 키워드 규칙 삭제 및 재평가 | OWNER | REQ-005 | ENT-008 |
| listScheduleItems | GET | /api/v1/schedule-items | 정기 결제·고정 만료·D-day 목록 | OWNER | REQ-006, REQ-007, REQ-008 | ENT-009 |
| createScheduleItem | POST | /api/v1/schedule-items | 일정 생성 | OWNER | REQ-006, REQ-007, REQ-008 | ENT-009 |
| updateScheduleItem | PATCH | /api/v1/schedule-items/{id} | 유형별 일정 필드 수정 | OWNER | REQ-006, REQ-007, REQ-008 | ENT-009 |
| deleteScheduleItem | DELETE | /api/v1/schedule-items/{id} | 일정 및 대기 알림 삭제 | OWNER | REQ-006, REQ-007, REQ-008 | ENT-009 |
| confirmRenewal | POST | /api/v1/schedule-items/{id}/renewal | 고정 만료의 새 만료일 입력 | OWNER | REQ-007 | ENT-009,ENT-018 |
| getManagementSummary | GET | /api/v1/management/summary | 활성 정기 결제 월 환산 합계 | OWNER | REQ-006 | ENT-009 |
| listNotifications | GET | /api/v1/notifications | 인앱 알림 목록 | OWNER | REQ-010 | ENT-010 |
| markNotificationRead | PATCH | /api/v1/notifications/{id}/read | 알림 읽음 값 설정 | OWNER | REQ-010 | ENT-010 |
| markAllNotificationsRead | POST | /api/v1/notifications/read-all | 현재 알림 전체 읽음 | OWNER | REQ-010 | ENT-010 |
| getCatchupAuditLog | GET | /api/v1/catchup/audit | 소유자 범위 Catch-up 감사 기록 | OWNER | REQ-009 | ENT-011 |
| streamTextChat | POST | /api/v1/ai/chat/stream | 텍스트 질문 SSE | OWNER | REQ-011 | ENT-012,ENT-013 |
| streamVoiceAndChat | POST | /api/v1/ai/voice/stream | PTT 음성 질문 SSE | OWNER | REQ-011 | ENT-012,ENT-013 |
| interruptChat | POST | /api/v1/ai/chat/interrupt | 본인 추론 중단 | OWNER | REQ-011 | ENT-013 |
| searchVectorRAG | POST | /api/v1/ai/search | 소유자 문서 벡터 검색 | OWNER | REQ-011 | ENT-019 |
| listChatSessions | GET | /api/v1/ai/sessions | 대화 세션 목록 | OWNER | REQ-011 | ENT-012 |
| getChatMessages | GET | /api/v1/ai/sessions/{id}/messages | 대화 메시지 목록 | OWNER | REQ-011 | ENT-013 |
| saveChatMessageToNote | POST | /api/v1/ai/messages/{id}/save-to-markdown | 완료된 본인 AI 응답을 노트로 저장 | OWNER | REQ-011 | ENT-013,ENT-002,ENT-015 |
| saveMarkdownInternal | POST | /internal/v1/documents | AI 내부 노트 저장 | OWNER | REQ-011 | ENT-002,ENT-014,ENT-015 |
| inspectPdf | POST | /api/v1/tools/pdf/inspect | 서버 PDF 메타데이터 검사 | OWNER,GUEST | REQ-012 | — |
| mergePdf | POST | /api/v1/tools/pdf/merge | PDF 병합 | OWNER,GUEST | REQ-012 | — |
| splitPdf | POST | /api/v1/tools/pdf/split | 범위 추출 PDF | OWNER,GUEST | REQ-012 | — |
| splitPdfAll | POST | /api/v1/tools/pdf/split/all | PDF 낱장 ZIP | OWNER,GUEST | REQ-012 | — |
| unlockPdf | POST | /api/v1/tools/pdf/unlock | PDF 암호 해제 | OWNER,GUEST | REQ-012 | — |
| getHealth | GET | /health | 최소 liveness 상태 | 공개 | REQ-031 | — |
| getVersion | GET | /version | 배포 버전 | OWNER | REQ-031 | — |
| getSwagger | GET | /docs | 개발 환경 OpenAPI UI | OWNER | REQ-031 | — |

## 적용 예외와 검증

- 트리·태그·리비전·검색 결과는 BE-002의 `{items}` 예외, 일반 목록은 Offset/Cursor envelope를 사용한다.
- 태그 생성/연결/해제는 노트 저장의 tags 목록으로 원자적으로 수행한다. 별도 태그 CRUD가 없다고 기능 누락으로 보지 않는다.
- 범위 분할 PDF와 낱장 ZIP을 구분한다. PDF 서버 처리 전 위치 표시·사용자 실행 및 제한은 ADR-0009를 따른다.
- 노트 내보내기·이미지 업로드는 개인 공간 기능이다. 브라우저 로컬 마크다운 변환/이미지 도구에 불필요한 업로드 API를 만들지 않는다.
- 이번 검토에서 YAML 파싱·참조·operationId/목록 일치·입력 스키마를 검사한다. Swagger 렌더링, 코드 생성 및 실제 API 계약 시험은 v2 구현 시 별도 수행한다.
- v1→v2 경로 어댑터·데이터 이전이 필요한지는 동시 운영 여부를 확인한 뒤 구현 계획에 명시한다. 이 명세의 추가만으로 기존 API를 제거하지 않는다.

## 계약 보완 범위

피드 목록은 미읽음·관심 키워드 배지 필터와 snapshotAt을 지원하며 모두 읽음 전용 작업을 제공한다. 홈은 카드별 SUCCESS/ERROR 및 sections 선택 조회를 지원한다. 키워드 가중치의 종류별 제약은 요청·응답 스키마에서 검증한다. SSE 이벤트·종료 규칙은 각 스트림 작업의 x-sse-events와 BE-002를 따른다. 총 62개 operationId이며 v2 구현 완료를 의미하지 않는다.

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-22 | 1.1.1 | API·화면·검증 범위 계약 불일치 보완 | AI 에이전트 |
| 2026-11-12 | 1.0.0 | 최초 검토안 | @bob (Keunhyeok Lim) |
| 2026-11-22 | 1.1.0 | 실제 기술 설정 및 승인된 PRD·ADR·ARC와 정합성 보완. 사용자 검토 대기 | AI 에이전트 |
