---
id: INF-009
title: 백업과 복구
part: infrastructure
tier: conditional
condition: 운영
status: approved
version: 1.0.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-24'
depends_on: [INF-001, PRD-006, BE-006]
related: [ADR-0008, ADR-0010, INF-005, INF-007, INF-010, INF-011]
superseded_by: null
traces: [NFR-004, NFR-006]
---

# INF-009 백업과 복구

v2 공간 데이터의 복원 단위는 **같은 쓰기 중단 구간에서 확보한 PostgreSQL 덤프와 RustFS 객체 사본 한 세트**다. 현재 두 서비스의 PVC는 `infra` 네임스페이스에서 `local-path`를 사용한다. 같은 호스트에 남긴 복사본만으로는 호스트 소실에 대비할 수 없다. [PostgreSQL 선언](../../homelab-infra/k8s/postgres/postgres.yaml) · [RustFS 선언](../../homelab-infra/k8s/rustfs/rustfs.yaml)

## 복구 목표와 백업 주기

| 상황 | 복구 기준 | 확인 시점 |
|---|---|---|
| 원래 볼륨을 유지한 재기동 | 마지막 완료 트랜잭션까지 공간 데이터와 참조 객체를 읽을 수 있어야 한다. DB 복귀 시간, Catch-up 개시·보정 완료 시간, 중복·유실 허용치는 [PRD-006 복구 목표](../product/prd-006-nfr.md#복구-목표-선택)를 따른다. | 마일스톤마다 재기동 시험, FN-009 통합 시험 때 스케줄 결과 확인 |
| 디스크 소실·오삭제·데이터 손상 | 장애 또는 손상 **이전**의 온전한 세트를 고른다. 해당 시점 이후의 커밋은 유실될 수 있다. 이 시나리오의 RPO·RTO는 미승인이다. 훈련 시 복원 지점부터 장애까지의 간격, 실제 누락 건수, 원본 서비스 재개 시간과 색인 복구 완료 시간을 각각 기록한다. | 첫 v2 운영 전 격리 복원, 이후 분기마다 및 스키마·스토리지 변경 후 재시험하는 안을 제안한다. |

**운영 초안**은 쓰기가 있는 가동일마다 1회, 스키마·스토리지 변경과 컷오버 직전에 추가 1회 백업하는 것이다. 비-상시 홈랩 특성상 고정 시각 Cron 대신 **작업 종료(`make sleep`) 직전 또는 다음 기동(`make wake`) 직후 쓰기 유입을 열기 전**에 백업 스크립트를 실행하며, 마지막 성공 세트 이후 DB·버킷 변경이 없으면 건너뛴다. 다른 물리 장치에 최근 가동일 7일분의 성공 세트를 암호화해 보관하고 변경 직전 세트는 롤백 기간 종료까지 별도 보관한다. 종료 중 놓친 회차는 다음 기동 때 확인한다. 만료 처리는 새 세트의 무결성 및 이전 복원 가능 세트를 확인한 뒤 수행한다. 운영자는 첫 훈련에서 용량, 복사 중 쓰기 중단 시간, 허용 손실을 확인해 주기·보관 기간과 디스크 소실 목표를 확정한다.

## 무엇을 보존하는가

| 대상 | 백업 또는 복구 방식 |
|---|---|
| PostgreSQL의 my-space DB | 현행 `postgres:18`의 앱 DB(`homelab_db`) 스키마와 데이터를 논리 덤프한다(`authelia_db` 등 타 인프라는 별도 백업 경계). 노트·리비전·첨부 연결뿐 아니라 피드·일정·AI 이력, 멱등 키, 체크포인트, 알림 전송 상태, Outbox와 대상별 색인 적용 기록도 같은 덤프에 포함한다. 복원에 필요한 DB 소유자·권한과 호환 메이저 버전(`postgres:18`)을 별도로 기록한다. [BE-006](../backend/be-006-database-erd.md) |
| RustFS의 본문·첨부 | 실제 앱 설정에서 본문·에셋 버킷 2종(`my-space-markdown`, `my-space-assets`)을 식별해 복사한다(`loki-data` 제외). 현재 본문 외에 보관 중인 과거 리비전과 그 첨부도 대상이다. 객체별 키·크기·바이트 체크섬을 보호된 목록에 보관한다. [저장소 계약](../architecture/adr/adr-0010-rustfs-v2-adoption.md) |
| OpenSearch·Qdrant·RabbitMQ | 검색·벡터 색인과 브로커 메시지는 복원 원본에서 제외한다. 빈 대상에 DB의 현재 상태와 RustFS 원문으로 다시 색인하고, 큐 손실 시 이미 발행 완료된 건까지 대조한다. [색인 재생성 절차](inf-011-ops-procedures.md#rb-003) |
| 환경·비밀 | 세트에 Git 리비전, 실행 이미지 digest, DB 스키마 버전, 버킷 식별자를 기록한다. 키와 자격 증명은 호스트와 물리적으로 분리된 보안 경로(비밀번호 관리자·별도 매체)에서 복구한다. 복원 담당자가 호스트 소실 후에도 복호화 키를 사용할 수 있어야 하며, 훈련에서 키 인출·복호화를 직접 검증한다. [INF-007](inf-007-secrets.md) |

방문자 브라우저 메모리, PDF 작업 임시 파일, 처리 중 입력·비밀번호는 백업 범위에서 제외한다. 백업·복원 계정은 용도별로 분리하고(v2 전환 시 [`account-security-guide.md`](../../homelab-infra/docs/account-security-guide.md) 및 `audit-users.sh` Allowlist에 반영), 덤프·객체 목록·검증 결과의 상세 식별자는 운영자만 조회한다.

## 한 세트를 만드는 순서

1. 세트 ID와 UTC 시작 시각을 기록한다. Argo CD의 `automated` 동기화를 해제(`syncPolicy: null`, `make stop-apps` 패턴)한 뒤 `api`·`ai`를 0으로 축소한다. `kubectl -n apps get pods`에서 `api`·`ai` 파드가 `Terminating` 포함 **0건**인지, `pg_stat_activity`에서 앱 계정의 활성 쓰기 트랜잭션이 **0건**인지 확인해 진행 중인 저장·스케줄러·Outbox·첨부 GC가 모두 멈췄음을 검증한다. 백업 구간 동안 `NodePort`·`s3.homelab.local`을 통한 운영자 직접 쓰기도 금지한다.
2. DB와 RustFS는 가동한 채 앱 DB를 덤프하고 같은 구간의 앱 버킷 2종을 독립 매체로 복사한다. 덤프·객체 목록·체크섬·버전 정보를 같은 세트 ID로 묶는다. 복사가 끝나면 원래 쓰기 경로와 Argo CD 자동 동기화를 재개할 수 있다.
3. 덤프의 읽기 가능 여부와 전송 후 체크섬을 확인한다. `BE-006` 기준으로 ① `documents(deleted_at IS NULL)` 및 보존 `document_revisions.object_key`, ② `document_revision_assets`가 참조하는 첨부와 `note_assets(status='AVAILABLE')`의 `object_key`·`size_bytes`를 사본과 대조해 누락을 검사한다(`DELETING` 상태는 정리 진행 중으로 분리 표기). 성공 세트만 복원 후보로 표시한다. 복사 중단·불일치 시 부분 세트를 격리하고, 새 쓰기 중단 구간에서 새 ID로 다시 받는다.
4. 마지막 성공 시각과 검증 결과를 [INF-010](inf-010-observability.md)에 반영한다. 운영 전에는 별도 매체에서 사본과 복호화 키를 실제로 읽어 [격리 복원 절차](inf-011-ops-procedures.md#rb-002)를 끝까지 시험한다.

훈련 기록에는 세트 ID, 장애 가정, 백업·복원 시각, 검증한 리비전·첨부 수와 누락·손상 수, 소유권·삭제 검증 결과, Catch-up과 색인 복구 결과, 후속 조치를 남긴다. 첫 v2 복원 훈련 결과와 실측 시간은 아직 없다.
