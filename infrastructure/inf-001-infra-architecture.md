---
id: INF-001
title: 인프라 아키텍처
part: infrastructure
tier: conditional
condition: 배포
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-23'
depends_on: [ARC-004]
related: []
superseded_by: null
---

# INF-001 인프라 아키텍처

홈랩 k3s의 `apps` 네임스페이스에서 frontend, api, ai를 독립 Deployment와 Service로 선언한다. 기존 Argo CD Application 세 개는 homelab-infra의 main 브랜치에서 각 앱 디렉터리를 동기화한다. 이 문서의 현황은 저장소 선언 기준이다.

| 구성 요소 | v2 책임과 데이터 경계 |
|---|---|
| Web | 정적 앱과 오프라인 도구를 제공한다. 개인 공간 API는 동일 출처 게이트웨이를 거친다. |
| api | PostgreSQL에 노트 메타데이터·리비전·Outbox를 커밋하고 RustFS에 불변 본문을 저장한다. OpenSearch 반영과 RabbitMQ 발행 확인은 커밋 이후 처리한다. |
| ai | RabbitMQ 이벤트를 소비하고 원문과 현재 버전을 검증해 Qdrant에 벡터를 반영한다. AI의 노트 저장은 api 내부 경로를 사용한다. |
| 영속 서비스 | PostgreSQL과 RustFS는 함께 보존할 원본이다. OpenSearch와 Qdrant는 원본에서 재생성할 수 있다. RabbitMQ는 파생 작업의 전달을 맡는다. |

배포 전 세 앱의 서비스 연결, RustFS 고정 이미지와 사용 S3 연산, 볼륨 재연결을 확인한다. ai가 중단돼도 api의 노트 저장 성공 기준은 DB 커밋이며 색인 지연은 별도로 관찰한다.

근거: [앱 매니페스트](../../homelab-infra/k8s/apps), [영속 서비스 매니페스트](../../homelab-infra/k8s), [컨테이너 설계](../architecture/arc-004-containers-components.md), [RustFS 채택 결정](../architecture/adr/adr-0010-rustfs-v2-adoption.md).
