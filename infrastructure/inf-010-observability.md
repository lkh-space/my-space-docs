---
id: INF-010
title: 모니터링·로그·추적·알림
part: infrastructure
tier: conditional
condition: 운영
status: approved
version: 1.0.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-24'
depends_on: [INF-001, PRD-006, BE-013]
related: [ARC-007, ARC-008, BE-005, INF-003, INF-009, INF-011]
superseded_by: null
traces: [NFR-003, NFR-004, NFR-006]
---

# INF-010 모니터링·로그·추적·알림

운영자가 구별해야 하는 세 가지 증상은 **노트 저장 실패**, **저장 후 검색 반영 지연**, **원본 데이터 손상**이다. DB 커밋에 성공한 요청은 저장 성공으로 집계한다. 검색과 RAG의 지연은 대상별로 측정한다. [ADR-0008](../architecture/adr/adr-0008-note-write-consistency.md)

## 현재 수집 경로와 v2 계측 범위

| 신호 | 홈랩 저장소에 선언된 경로 | v2 운영 전 확인할 항목 |
|---|---|---|
| 인프라 지표 | node-exporter·kube-state-metrics·cAdvisor → Prometheus → Grafana. 수집 간격 15초, Prometheus 보관 7일. [수집 설정](../../homelab-infra/k8s/prometheus/prometheus-config.yaml) · [보관 설정](../../homelab-infra/k8s/prometheus/prometheus.yaml) | 대상이 실제 수집되는지 확인한다. `my-space-dashboard.yaml`은 `my-space-api`·`my-space-ai`·`my-space-frontend` 각각의 Ready·재시작·자원 지표로 갱신·적용한 뒤 실수집을 검증한다. [대시보드](../../homelab-infra/k8s/grafana/my-space-dashboard.yaml) |
| 앱·작업 로그 | 전 Pod stdout/stderr → Alloy(현재 무필터) → Loki(보관 168시간, **RustFS `loki-data`·`rustfs-secret` 의존**) → Grafana. [Alloy 설정](../../homelab-infra/k8s/alloy/alloy-config.yaml) · [Loki 설정](../../homelab-infra/k8s/loki/loki-config.yaml) · [Loki 매니페스트](../../homelab-infra/k8s/loki/loki.yaml) · [Grafana 데이터 소스](../../homelab-infra/k8s/grafana/grafana-datasources.yaml) | ① 앱에서 서비스·작업명, 결과 코드, 소요 시간, 안전한 추적 ID(`traceId`, `requestId`, `eventId`)를 선별 출력하고 이 ID는 지표 라벨에서 제외한다([BE-013](../backend/be-013-error-handling.md)). ② 대시보드 Loki 로그 패널(`app=~"my-space-api\|my-space-ai"`)을 실제 반영 후 확인한다. ③ **현행 제약**: Loki가 RustFS를 저장소로 쓰므로 RustFS 장애·비밀 변경 시 Loki도 동시 중단되며(1차 진단은 `kubectl logs` 사용), `loki.yaml`의 `clean-corrupt-wal` initContainer가 재기동마다 미플러시 WAL을 삭제하므로 v2 운영 전 제거/조건부화 또는 위험 수용을 결정한다. |
| 앱 지표·알림 | 현재 Prometheus 수집 대상은 인프라 4종뿐이며, `alerting:`·Alertmanager·Grafana Alerting 프로비저닝·분산 추적 저장소는 미선언 상태다. | 앱 `/metrics` 수집, 합성 프로브, 알림 엔진(예: 기존 Grafana 11.5.2 내장 Unified Alerting + 운영 전용 비공개 Webhook 채널)을 구성하고 모의 실패로 경보 발신·복구 통지까지 시험한다. 개별 요청 조사는 우선 로그의 추적 ID를 사용한다. |

대시보드·로그·백업 결과는 인증된 운영자만 조회한다(현재 `grafana.homelab.local`은 Authelia가 아닌 Grafana 자체 계정 `grafana-secret`으로 인증한다). 메트릭 라벨은 서비스, 라우트 템플릿, 상태 계열, 색인 대상 등 값의 종류가 한정된 필드를 사용한다. 원문 URL·쿼리, 쿠키·인증 헤더, PDF·노트 본문, 프롬프트, 비밀번호, Webhook 주소는 **2중 방어(1차: 앱 로거 redact `BE-013`, 2차: v2 운영 전 `alloy-config.yaml`의 `loki.process` `stage.replace` 마스킹 안전망 추가)**로 수집 전에 제거한다. 감사 기록은 [ARC-007](../architecture/arc-007-security-architecture.md)의 항목과 접근 통제를 따른다. Loki의 7일 설정은 일반 운영 로그 기준이며, 별도 감사 기록의 보존 기간과 파기 기준은 후속 QA-007에서 확정한다.

## 측정과 판정

| 대상 | 관측할 값과 판정 기준 |
|---|---|
| API·PDF | 서비스별 요청 수, 5xx/요청 수, 성공·실패별 응답 시간 P95, CPU·메모리·재시작을 본다. 일반 CRUD·검색과 qpdf는 별도로 집계한다. [NFR-003](../product/prd-006-nfr.md)의 데이터 규모·입력 조건에서 시험한 P95로 합격을 판정한다. 운영 5분 창에는 표본 수를 함께 표시한다. |
| 재기동·Catch-up | 기동별 API Ready·보정 시작·종료 시각, 중복·누락 건수와 마지막 체크포인트를 기록한다. [NFR-004와 복구 목표](../product/prd-006-nfr.md#복구-목표-선택)에 따라 개시와 완료를 각각 판정한다. 폐쇄 중이던 호스트의 기동 시각도 따로 남긴다. |
| 검색·AI | api의 OpenSearch 반영과 RabbitMQ 발행, ai의 Qdrant 적용을 나눠 미처리 수·최고 대기 시간·실패 수를 본다. 큐 깊이·미확인 메시지·소비율, 현재 리비전과 대상별 적용 리비전의 차이를 함께 본다. 빈 큐만으로 재색인 완료를 판정할 수 없다. [BE-005](../backend/be-005-domain-model-rules.md) |
| 원본·백업 | 노드 디스크 여유량, PVC 연결, DB 커밋 오류, RustFS 읽기·쓰기 실패(장애 시 Loki 동시 영향 유의), 마지막 **완전한 세트**의 시점과 검증 실패 수를 본다. 볼륨 단위 용량이 수집되지 않으면 노드 파일시스템을 직접 점검한다. [INF-009](inf-009-backup-recovery.md) |

## 운영 알림 초안

수신자는 홈랩 운영자다. 알림 엔진은 v2 운영 전 구성하며(초안: Grafana 11.5.2 내장 Unified Alerting + 운영 전용 비공개 Webhook 채널), 즉시 대응 항목은 비공개 알림 채널로 전달하고 점검 항목은 다음 가동 점검 목록에 남긴다. 채널 연결과 아래 초기 임계값은 첫 훈련에서 확인한다. 제품의 인앱·Slack 알림 발송 성공은 인프라 경보 수신의 증거로 사용하지 않는다.

| 조건 | 감지 수단(원천) | 우선순위 | 첫 조치 |
|---|---|---|---|
| 가동 예정 시간에 보호 API 점검이 1분 간격으로 2회 연속 실패하거나 배포 직후 저장 실패가 반복됨 | 합성 HTTP 프로브(Blackbox/외부 점검기) + `api` `/metrics` 5xx·저장 실패 카운터 | 즉시 | [RB-001 재기동 점검](inf-011-ops-procedures.md#rb-001), 배포 직후면 [RB-004 롤백](inf-011-ops-procedures.md#rb-004) |
| DB가 참조하는 본문·첨부 누락 또는 체크섬 불일치 1건 이상 | 백업·무결성 검증 스크립트 결과 지표/알림 + `api`·`ai` 객체 조회 오류 로그 | 즉시 | 쓰기·GC를 중지하고 [RB-002 원본 복구](inf-011-ops-procedures.md#rb-002)로 범위 확인 |
| Catch-up 개시·완료 목표 초과 또는 중복 생성 발견 | `api` 스케줄러·Catch-up 메트릭 및 구조화 로그(`ENT-011`) | 가동 시 점검, 누락·중복 지속 시 즉시 | [RB-001](inf-011-ops-procedures.md#rb-001) |
| 대상별 최고 대기 시간이 10분을 넘고 2회 연속 증가하거나 재시도 소진 건 발생 | `api` Outbox(`ENT-014`)·`ai` 소비 지표 및 RabbitMQ 큐 깊이 | 점검 | [RB-003 색인 재생성](inf-011-ops-procedures.md#rb-003) |
| 백업 실패·검증 불일치 또는 가동일의 백업 회차 누락 | 백업 스크립트 종료 코드·마지막 성공 세트 타임스탬프 지표 | 점검, 유효한 세트가 없으면 즉시 | [RB-005 백업·관측 점검](inf-011-ops-procedures.md#rb-005) |
| 가동 중 수집 누락이 5분간 지속되거나 시험 알림이 수신되지 않음 | Prometheus `up`·scrape 실패 지표, Alloy/Loki 헬스 및 주기적 시험 경보 | 점검 | [RB-005](inf-011-ops-procedures.md#rb-005) |

의도한 전원 종료·유지보수 시간에는 가용성 경보를 유예하고 재기동 때 해제한다. 호스트와 함께 멈춘 수집기는 전체 호스트 장애를 알릴 수 없다. 계획 외 정전까지 즉시 감지하려면 별도 전원에서 실행하는 점검기와 수신 경로가 필요하다. 첫 운영 점검에는 가동 일정, 시험 알림 발신·수신 시각, 수집 중단 중 비어 있던 구간을 남긴다.
