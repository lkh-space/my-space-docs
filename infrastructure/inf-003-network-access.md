---
id: INF-003
title: 네트워크와 접근 제어
part: infrastructure
tier: conditional
condition: 배포
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-23'
depends_on: [ARC-007]
related: []
superseded_by: null
---

# INF-003 네트워크와 접근 제어

현재 v1 Web Ingress(`my-space.homelab.local`)는 Traefik websecure·TLS와 ForwardAuth 미들웨어를 사용해 `/api/v1/ai`는 `ai:3000`, `/api`는 `api:3000`, `/`는 `frontend:80`으로 라우팅한다. 별도 v1 API Ingress(`api.homelab.local`)에는 `api:3000`으로 향하는 `/` 포괄 경로가 남아 있다.

| 흐름 | v2 허용 조건 |
|---|---|
| 브라우저 → Ingress → 앱 | `my-space.homelab.local` 단일 호스트로 브라우저용 경로만 공개한다. 위조된 신원 헤더(`Remote-*`)와 내부 헤더(`X-Forwarded-Auth-Secret`, `X-Internal-Service-Secret`)를 제거·덮어쓴다. 쿠키 인증 변경 요청의 Origin은 Web origin과 정확히 일치해야 한다. |
| ai → api 내부 저장 | 클러스터 내부 서비스 DNS(`my-space-api.apps.svc.cluster.local:3000`)로 `POST /internal/v1/documents`를 호출하며, 내부 서비스 인증(`X-Internal-Service-Secret`), 검증된 OWNER 문맥, 요청 식별자를 모두 검사한다. |
| 워크로드 → 저장소 | api에는 DB·RustFS·OpenSearch·RabbitMQ, ai에는 필요한 DB·원문 읽기·RabbitMQ·Qdrant 접근만 허용한다. 공개 웹앱 경로에서의 브라우저 저장소 직접 접근을 차단한다. (홈랩 LAN 내부 운영자 관리용 `infra-ingress`·`NodePort` 노출 범위는 운영자 망 경계로 별도 관리한다.) |

v2 컷오버 시 별도 API Ingress([`k8s/apps/api/ingress.yaml`](../../homelab-infra/k8s/apps/api/ingress.yaml))와 [`kustomization.yaml`](../../homelab-infra/k8s/apps/api/kustomization.yaml)의 해당 참조를 **삭제**하여 동일 출처 정책을 적용하고 `/internal/v1/documents` 외부 노출 경로를 원천 차단한다. 저장소의 k8s 매니페스트에서 `NetworkPolicy` 선언을 찾지 못했으므로, 배포 전 클러스터 CNI의 정책 집행 여부(`--disable-network-policy` 미설정 등)를 확인하고 `apps` 네임스페이스 우회 경로 차단 수준을 결정·적용한다. 배포 후 미인증 요청, GUEST의 개인 데이터 요청, 외부의 내부 저장 호출이 각각 거부되고 허용된 동일 출처 요청만 성공하는지 검증한다.

근거: [Web Ingress](../../homelab-infra/k8s/apps/frontend/ingress.yaml), [API Ingress](../../homelab-infra/k8s/apps/api/ingress.yaml), [보안 아키텍처](../architecture/arc-007-security-architecture.md), [API 명세](../backend/be-003-api-spec.md), [v2 인프라 전환 가이드](../../homelab-infra/docs/my-space-v2-infra-guide.md).
