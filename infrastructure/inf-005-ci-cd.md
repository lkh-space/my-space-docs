---
id: INF-005
title: CI/CD 파이프라인
part: infrastructure
tier: conditional
condition: 실무
status: approved
version: 1.0.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-23'
depends_on: [INF-001]
related: []
superseded_by: null
---

# INF-005 CI/CD 파이프라인

현재 v1 frontend는 검증 단계 없이 Docker publish 워크플로만 사용한다. 백엔드는 CI(`ci.yml`)와 api·ai 이미지 발행(`docker-publish*.yml`)을 별도 워크플로로 동시 실행하며 Git SHA와 `latest` 태그를 생성한다. Argo CD 앱 3건은 자동 동기화·prune·selfHeal을 선언하며 Image Updater는 `latest` 전략과 `argocd` write-back(파라미터 오버라이드)을 사용한다.

| v2 단계 | 통과 기준 및 전환 설정 |
|---|---|
| 변경 검증 | frontend 워크플로에 타입 검사·테스트·빌드·린트 검증 잡을 선행 추가(`needs`)하고, backend는 CI 통과 후에만 이미지 발행 잡이 실행되도록 워크플로를 통합하거나 종속성을 강제한다. 검증 결과와 API 계약 검사를 같은 소스 커밋에 연결한다. |
| 이미지 발행·승격 | CI를 통과한 커밋의 이미지 digest(`@sha256:...`)와 소스 SHA를 기록하고, `homelab-infra`의 `deployment.yaml`에 승인된 digest와 `imagePullPolicy: IfNotPresent`를 직접 반영한다. `latest` 자동 덮어쓰기를 막기 위해 Argo CD Application 3건의 `argocd-image-updater.*` 어노테이션을 제거하고 기존 `argocd` 파라미터 오버라이드를 초기화한다. |
| 동기화 확인 | Git의 원하는 digest와 클러스터 실제 실행 digest가 일치하고 세 앱이 Ready인지 확인한다. 동일 출처 진입·인증 및 주요 API 조회가 성공해야 배포 완료로 기록한다. |
| 전환 및 복귀 | **v1 → v2 최초 전환**은 개인 홈랩 특성상 v1을 중단(`scale to 0`)하고 PostgreSQL·RustFS를 백업한 뒤 설정·시크릿·Ingress·이미지를 일괄 교체(Cutover)한다. **v2 운영 중 복귀**는 직전 호환 digest를 Git 원하는 상태에 다시 지정해 동기화하며(DB 변경이 이전 v2 앱과 호환되는지 확인), 복귀 후 자동 갱신이 이를 덮어쓰지 않아야 한다. |

현행 v1 워크플로 파일의 분리는 CI 성공이 이미지 발행의 필수 선행 조건이라는 증거가 아니다. 배포 전 실패한 커밋의 발행 차단, Image Updater 해제 후 파라미터 오버라이드 잔존 여부, 복귀 상태 유지를 검증한다.

근거: [프론트 이미지 발행](../../my-space-front/.github/workflows/docker-publish.yml), [백엔드 CI·이미지 발행](../../my-space-backend/.github/workflows), [Argo CD 앱 선언](../../homelab-infra/k8s/argocd/applications), [v2 인프라 전환 가이드](../../homelab-infra/docs/my-space-v2-infra-guide.md).
