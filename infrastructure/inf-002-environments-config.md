---
id: INF-002
title: 환경 분리와 구성 관리
part: infrastructure
tier: conditional
condition: 배포
status: approved
version: 1.0.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-23'
depends_on: [BE-004]
related: []
superseded_by: null
---

# INF-002 환경 분리와 구성 관리

현재 Argo CD 선언이 지정하는 배포 대상은 홈랩 클러스터의 `apps`다. 현행 v1 프론트 배포는 `frontend-secret`으로 `VITE_API_BASE_URL=https://api.homelab.local` 등을 주입하며, 컨테이너 시작 시 `env-config.js`로 브라우저에 평문 노출한다. v2 환경별 설정 책임은 다음과 같다.

| 환경 | 구성 기준 |
|---|---|
| 로컬 개발 | 개발용 origin과 격리된 데이터·계정을 사용한다. 개발 사용자 주입은 명시적인 로컬 설정에서만 허용한다. |
| CI 검증 | 테스트 전용 서비스 주소와 자격 증명을 주입한다. 운영 DB·버킷·큐에 연결하지 않도록 대상 주소를 검사한다. |
| 홈랩 | 공개 Web origin(`https://my-space.homelab.local`), 서비스 DNS, 버킷·큐·모델 식별자, 업무별 한도와 이미지 버전을 배포 설정으로 관리한다. 계정·연결 비밀은 INF-007을 따른다. |

v2 컷오버 시 프론트 런타임 공개 설정은 별도 API 호스트 대신 동일 출처 API 경로(`/api` 또는 `https://my-space.homelab.local`)로 전환한다. `frontend-secret`은 이름과 달리 브라우저에 그대로 공개되는 설정이므로 사용자에게 공개 가능한 값만 넣고 비밀은 절대 포함하지 않는다. **v2 구현 요구사항**으로 api와 ai는 시작 시 필수 설정의 존재·형식·환경 조합을 검증해야 하며, 비로컬 환경에서 공유 비밀이나 허용 origin이 없거나 개발 인증 우회가 활성화되면 보호 경로를 열지 않도록(Fail-Closed) 구현한다([BE-004](../backend/be-004-authn-authz.md)). 검증 로그에는 실패한 설정 이름만 기록한다.

환경 추가 시 origin·데이터 저장소·서비스 계정·배포 대상·시크릿 참조를 한 세트로 검토한다. 로컬·CI의 구체적인 인스턴스와 주소는 해당 환경 구성 시 확정한다.

근거: [배포 대상 선언](../../homelab-infra/k8s/argocd/applications/my-space-api.yaml), [프론트 배포 설정](../../homelab-infra/k8s/apps/frontend/deployment.yaml), [백엔드 인증 계약](../backend/be-004-authn-authz.md), [v2 인프라 전환 가이드](../../homelab-infra/docs/my-space-v2-infra-guide.md).
