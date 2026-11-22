---
id: BE-004
title: 인증과 인가
part: backend
tier: conditional
condition: 인증
status: approved
version: 1.1.1
owner: '@bob (Keunhyeok Lim)'
last_reviewed: '2026-11-22'
depends_on:
- ARC-007
- ADR-0007
- ADR-0008
related:
- BE-001
- BE-002
- BE-003
- BE-013
- FE-008
superseded_by: null
traces:
- REQ-026
- REQ-030
- REQ-031
- NFR-006
---

# BE-004 인증과 인가

## 기준

[ARC-007](../architecture/arc-007-security-architecture.md), [ADR-0007](../architecture/adr/adr-0007-visitor-browser-state.md), [ADR-0008](../architecture/adr/adr-0008-note-write-consistency.md)을 구현하는 v2 검토 계약이다. 인증은 Authelia/Traefik에 위임하며 백엔드 자체 비밀번호·JWT 발급 체계를 추가하지 않는다. 비밀번호, 로그인 제한, 세션 만료는 인증 제공자의 설정 책임이다. 백엔드는 매 요청마다 신뢰된 인증 문맥을 검증하며 독립 방문자 세션이나 TTL을 만들지 않는다.

## 신뢰 경계와 신원

1. Traefik은 클라이언트가 보낸 `Remote-*` 및 내부 인증 헤더를 제거·덮어쓴다. Authelia 검증 이후의 사용자 정보만 전달한다.
2. 공개 API 파드는 Ingress를 우회할 수 없도록 네트워크 접근을 제한한다. 프록시 공유 비밀 `X-Forwarded-Auth-Secret`도 검증한다. v2 비로컬 실행은 이 비밀이 없으면 보호 경로를 정상 서비스하지 않는다.
3. 보호된 경로에서 `Remote-User` 누락·잘못된 형식·인증 실패는 401이다. 누락을 `guest`로 치환하지 않는다. 클라이언트의 401 처리는 로그인 UI로 이동하며, API가 로그인 HTML을 성공 JSON처럼 반환하지 않는다.
4. `Remote-User`를 서버 사용자 `username` 및 저장소 `ownerId`의 기준으로 사용한다. `Remote-Groups`, `Remote-Name`, `Remote-Email`은 신뢰된 프록시에서만 읽는다. 요청 JSON의 ownerId/role을 신뢰하지 않는다.
5. 명시적으로 등록된 주 사용자 계정·권한만 OWNER, 테스트 계정만 GUEST로 매핑한다. 알 수 없는 계정/그룹은 403이며 자동 방문자 승격을 하지 않는다. 매핑은 배포 설정으로 검증한다.

현재 v1 `RemoteUserGuard`는 `username/displayName/email/groups` 구조와 명시적 로컬 개발 사용자 주입을 사용한다. 비로컬 공유 비밀 검증은 현재 설정값이 있을 때 수행된다. 위의 비로컬 설정 필수화 및 역할 검증은 **v2 구현 요구사항**이며 완료 사실이 아니다. 로컬 우회는 명시적인 개발 환경에서만 허용하고 배포 설정 검사로 차단한다.

## 권한 매트릭스

| 경로/기능 | OWNER | GUEST | 미인증 |
|---|---|---|---|
| 개인 노트·피드·일정·알림·검색·대화 | 본인 데이터 | 403 | 401 |
| 개인 공간 쓰기·색인·Slack | 허용 | 403 | 401 |
| `/api/v1/samples/{space}` | 샘플 읽기 | 샘플 읽기 | 401 |
| `/api/v1/auth/me` | 자기 신원 | 자기 신원 | 401 |
| 외부 AI/RAG/음성 | 자원 한도 내 허용 | 403, 브라우저 샘플 응답 | 401 |
| PDF 임시 처리·공휴일 조회 | 허용 | 허용 | 401 |
| `/health` | 최소 상태만 | 최소 상태만 | 최소 상태만 |
| `/version` | 배포 메타데이터 | 403 | 401 |
| `/docs` | 개발 환경에서만 | 403 | 401 |
| 내부 AI → api 저장 | 검증된 서비스 + OWNER 문맥만 | 거부 | 거부 |

개인 리소스 조회·변경은 조회 조건에 ownerId를 포함한다. 다른 사용자의 ID는 404로 처리해 존재를 노출하지 않는다. 역할상 금지된 API는 403이다. `/health`는 비밀·의존 서비스 주소를 반환하지 않으며 상세 readiness는 운영망에 한정한다. `/docs`는 운영에서 비활성화한다.

## 방문자 체험

읽기 전용 샘플만 서버에서 제공한다. 작성·수정·삭제·검색 체험은 샘플을 복제한 **브라우저 메모리**에서 수행한다. SPA 내부 이동 동안 유지하고 새로고침·명시적 초기화·탭 종료 시 폐기한다. localStorage·IndexedDB·서버 메모리 세션에 보관하지 않는다.

방문자의 개인 공간 API 접근, 외부 AI·RAG, 노트 저장, Slack 및 색인 발행은 서버에서 거부한다. 단순 UI 비활성화에 의존하지 않는다. AI는 샘플 응답을 사용하며 실제 외부 호출 범위는 추가 승인 전 도입하지 않는다. PDF는 ADR-0009의 임시 처리 범위에서 허용하고 방문자의 영속 쓰기로 취급하지 않는다.

## 내부 저장과 검증

내부 저장은 외부 공개 경로와 분리하고 서비스 인증 비밀과 전달 사용자 문맥을 함께 검증한다. 브라우저가 내부 헤더를 지정해도 Ingress가 전달하지 않는다. AI 서비스 권한만으로 임의 사용자를 대리할 수 없으며, 원 요청에 검증된 OWNER 문맥과 요청 식별자가 필요하다. 노트 생성 결과는 동일 사용자에게만 반환한다.

검증 항목: 헤더 누락/위조, 공유 비밀 누락, 알 수 없는 역할, 다른 소유자 ID, 방문자 직접 HTTP 쓰기, 내부 경로 외부 노출, 샘플에 개인 데이터 혼입, 로컬 우회 배포 방지. 인증·소유권 검증은 SSE 연결과 파일 처리 시작 전에 수행한다. 오류는 [BE-013](be-013-error-handling.md), 경로별 역할은 [OpenAPI](openapi.yaml)의 `x-roles`를 따른다.

## 브라우저 요청 출처와 CSRF 방어

v2 Web과 공개 `/api/v1` API는 동일 origin(스킴·호스트·포트)으로 제공한다. 이 절은 앱 API 계약이며 Authelia 자체 로그인·로그아웃 경로의 방어는 인증 제공자 설정에서 별도로 검증한다.

- 쿠키 인증의 POST/PUT/PATCH/DELETE 요청은 인증·인가에 더해 `Origin`을 배포 설정의 정확한 Web origin과 비교한다. 누락, `null`, 다중 값, 다른 origin은 `403 / ERR_AUTH_ORIGIN_REJECTED`로 거부한다. Referer나 요청의 Host/X-Forwarded-Host로 허용 origin을 추측하지 않는다. 개발 환경도 명시된 로컬 origin만 허용한다.
- `Sec-Fetch-Site: cross-site`는 거부한다. 이 헤더가 없더라도 Origin 검사는 생략하지 않으며 `same-site`도 동일 origin으로 간주하지 않는다. PDF multipart, 첨부 업로드, AI 스트림 시작·중단에도 적용한다. 업로드 파싱·임시 파일 생성·AI 호출 등 비용이나 부작용이 발생하기 전에 검사한다.
- 브라우저의 Origin은 브라우저가 설정한다. 비브라우저 테스트 도구가 이를 흉내 낼 수 있으므로 이 검사는 사용자·서비스 인증을 대체하지 않는다. Origin 누락을 허용하는 우회 플래그를 공개 API에 두지 않는다. 내부 서비스 경로만 별도 서비스 인증 계약을 적용하며 외부 Ingress에서 차단한다.
- credential을 허용하는 교차 출처 CORS는 열지 않는다. 와일드카드·Origin 반사·서브도메인 포괄 허용은 금지한다. OPTIONS는 상태를 바꾸지 않으며 GET/HEAD도 조회만 수행한다. 별도 origin의 Web 배포가 필요해지면 이 계약과 CSRF 방어를 먼저 개정한다.
- Authelia 세션 쿠키는 HTTPS에서 `Secure`, `HttpOnly`, 최소 `SameSite=Lax`를 적용하고 Domain/Path는 SSO에 필요한 최소 범위로 제한한다. SameSite만으로 CSRF 방어를 충족했다고 보지 않는다. 세션 쿠키를 앱 JavaScript로 복사·보관하지 않는다.
- 개인 데이터·신원·AI·첨부·내보내기·PDF 응답과 관련 오류 응답은 `Cache-Control: no-store`를 적용한다. 프록시/CDN/Service Worker는 인증 API를 캐시하지 않는다. 의도한 TanStack Query 메모리 캐시는 FE-005의 세션 격리 규칙을 따른다. 공개 정적 앱 자산만 오프라인 캐시 대상으로 삼는다.

검증: 정상 origin 성공, 다른 origin·같은 사이트의 다른 서브도메인·누락·null 거부, multipart 및 SSE 시작 전 거부, CORS 우회 실패, 인증 없는 동일 origin 거부, 내부 경로 외부 접근 거부를 확인한다. 세션 만료·역할 변경 시 클라이언트 격리는 [FE-005](../frontend/fe-005-state-management.md)를 따른다.

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-22 | 1.1.1 | 동일 출처 CSRF 방어·쿠키·응답 캐시 계약 및 검증 기준 추가 | AI 에이전트 |
| 2026-11-12 | 1.0.0 | 최초 검토안 | @bob (Keunhyeok Lim) |
| 2026-11-22 | 1.1.0 | 실제 기술 설정 및 승인된 PRD·ADR·ARC와 정합성 보완. 사용자 검토 대기 | AI 에이전트 |
