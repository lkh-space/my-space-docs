---
id: INF-007
title: 시크릿 관리
part: infrastructure
tier: conditional
condition: 배포
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-23'
depends_on: [INF-003]
related: []
superseded_by: null
---

# INF-007 시크릿 관리

현재 v1 세 Deployment는 `envFrom.secretRef`로 각각 `frontend-secret`, `api-secret`, `ai-secret`을 참조한다. 이 중 **`frontend-secret`은 컨테이너 시작 시 `env-config.js`로 브라우저에 평문 노출되는 공개 설정**이므로, 비밀 자격 증명을 절대 넣지 않는다. Kubernetes Secret 참조는 주입 경로이며 실제 값의 보관·회전 상태는 운영 환경에서 확인한다.

| 비밀 종류 | v2 소비 범위 및 컷오버 변경 사항 |
|---|---|
| ForwardAuth 공유 비밀 | 게이트웨이(`forward-auth-secret` 미들웨어)가 검증된 요청에 주입하고 `api`·`ai`(`AUTH_FORWARDED_SECRET`)가 검사한다. 브라우저에서 지정한 값은 제거한다. |
| ai → api 서비스 인증 | 내부 저장 호출(`POST /internal/v1/documents`, `X-Internal-Service-Secret`)의 두 당사자(`api-secret`, `ai-secret`)에만 **신규 추가**한다. 게이트웨이 비밀과 구분하고 사용자 문맥도 검증한다. |
| DB·객체 저장소·큐·검색 자격 증명 | v2 컷오버 시 서비스별 최소 권한으로 일괄 교체한다. `api-secret`은 DB·RustFS 쓰기·OpenSearch·RabbitMQ 발행을 맡고, `ai-secret`에서는 불필요한 **`OPENSEARCH_*` 키를 제거**하며 DB(노트 읽기·AI 상태 전용)와 RustFS(원문 읽기 전용) 계정을 분리한다. |
| 외부 API·알림 키 | `GEMINI_API_KEY`는 `ai-secret`에만, `SLACK_WEBHOOK_URL`·`HOLIDAY_API_KEY`는 `api-secret`에만 **신규 추가**한다. 방문자 샘플과 프론트 번들(`frontend-secret`)에서 완전히 제외한다. |
| 레지스트리 자격 증명 | CI의 이미지 발행(`DOCKERHUB_TOKEN`)과 클러스터 이미지 읽기(`dockerhub-secret`) 권한을 분리한다. |

배포 전 Git 추적 파일·렌더링된 미들웨어·CI 로그·프론트 정적 자산(`env-config.js` 포함)에 실제 비밀 값이 노출되지 않는지 확인한다. 예시 설정(`.env.*.example`)에는 키 이름만 둔다. Secret 조회 권한과 클러스터 저장 시 보호 설정을 운영자가 확인한다. 회전은 소비자와 발급자를 식별하고 새 값 배포·Pod 재기동·정상 인증 확인 후 이전 값을 폐기한다. 이전 자격 증명 거부와 로그 마스킹을 검증한다.

근거: [API Deployment](../../homelab-infra/k8s/apps/api/deployment.yaml), [AI Deployment](../../homelab-infra/k8s/apps/ai/deployment.yaml), [OpenAPI 보안 스킴](../backend/openapi.yaml), [계정 보안 가이드](../../homelab-infra/docs/account-security-guide.md), [회전 절차](../../homelab-infra/docs/password-rotation-guide.md), [v2 인프라 전환 가이드](../../homelab-infra/docs/my-space-v2-infra-guide.md).
