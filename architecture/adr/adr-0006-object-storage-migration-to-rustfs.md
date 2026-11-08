---
id: ADR-0006
title: 오브젝트 스토리지를 MinIO에서 RustFS(Apache 2.0)로 전환
part: architecture
tier: required
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-08
depends_on: [ARC-001, ARC-002]
related: [ARC-004, ARC-005, ARC-006, ARC-007, PRD-006]
superseded_by: null
traces: [REQ-003, REQ-028, NFR-006]
---

# ADR-0006 오브젝트 스토리지를 MinIO에서 RustFS(Apache 2.0)로 전환

## 이 문서는

- **목적**: 마크다운 노트 원문(`.md`) 및 이미지 에셋을 저장하는 홈랩 오브젝트 스토리지를 기존 MinIO에서 영구 무료 오픈소스인 **RustFS**로 전환하는 아키텍처 의사결정을 기록한다. 전체 ADR 목록은 [ARC-006](../arc-006-adr.md)에 있다.
- **대상 독자**: 백엔드·인프라 개발자, 시스템 소유자, AI 에이전트
- **작성 시점**: 아키텍처 설계 및 인프라 보안 개선 단계(2026-11-08)
- **등급**: 필수

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [ARC-001 시스템 개요](../arc-001-system-overview.md) | 시스템 구성 요약 및 저장소 목록 |
| 선행 | [ARC-002 아키텍처 원칙과 제약](../arc-002-principles-constraints.md) | 홈랩 자원 효율(`AP-05`) 및 보안 격리 원칙 |
| 관련 | [ARC-004 컨테이너와 주요 컴포넌트](../arc-004-containers-components.md) | `RustFS` 컨테이너 및 S3 API 통신 경로 |
| 관련 | [ARC-005 데이터 흐름](../arc-005-data-flow.md) | `DF-03` 마크다운 본문 영속화 흐름 |
| 관련 | [ARC-006 아키텍처 의사결정 기록(ADR)](../arc-006-adr.md) | ADR 색인 |
| 관련 | [homelab-infra 전환 가이드](file:///Users/limkeunhyeok/workspace/homelab-infra/docs/rustfs-migration-guide.md) | 인프라 클러스터 리소스 명세 및 전환·롤백 런북 |

## 결정 상태 [필수]

| 항목 | 값 |
|---|---|
| 결정 상태 | 제안됨 |
| 결정일 | 2026-11-08 (제안일) |
| 결정자 | `@bob (Keunhyeok Lim)` |
| 자문한 사람 | AI 에이전트 |
| 대체 관계 | MinIO 스토리지 채택 결정을 대체 |

## 맥락과 문제 [필수]

`my-space`는 마크다운 노트의 버전별 원문(`.md`)과 본문 내 첨부 이미지를 S3 호환 오브젝트 스토리지에 영속화한다(`REQ-003`, `REQ-028`).

초기 아키텍처는 널리 쓰이는 오픈소스인 **MinIO**를 저장소로 검토·선정하였으나, 최근 MinIO의 정책 변경으로 다음과 같은 심각한 운영 위험이 발생했다:
1. **무료 커뮤니티 배포판의 보안(CVE) 패치 중단**: 상용 엔터프라이즈 중심 정책으로 전환되면서 무료 오픈소스 버전에 대한 CVE 보안 패치 지원이 중단되었고 웹 콘솔 기능이 대폭 축소되었다.
2. **AGPLv3 라이선스 및 상용화 리스크**: 장기적인 홈랩 무중단 운영과 보안 유지를 위해 영구 무료 오픈소스(Permissive License) 대안으로의 전환이 필수적이다.

백엔드 코드 변경을 최소화하면서도 보안 패치와 자원 효율성을 모두 만족하는 오브젝트 스토리지를 어떻게 선정할 것인가?

## 결정 동인 [필수]

| 동인 | 출처 |
|---|---|
| 보안 취약점(CVE) 패치가 지속적으로 지원되고 상용 라이선스 리스크가 없는 완전 무료 오픈소스(Permissive License)여야 한다 | [homelab-infra 전환 가이드](file:///Users/limkeunhyeok/workspace/homelab-infra/docs/rustfs-migration-guide.md), [PRD-006 NFR-006](../../product/prd-006-nfr.md) |
| 표준 AWS S3 SDK(`@aws-sdk/client-s3`) 및 Signature V4 기반 Presigned URL과 100% 호환되어 백엔드 애플리케이션 코드를 재사용할 수 있어야 한다 | [ARC-002 AP-05](../arc-002-principles-constraints.md), `REQ-003` |
| 홈랩 단일 노드(k3s) 환경에서 메모리와 CPU를 최소한으로 사용하는 경량 시스템이어야 한다 | [ARC-002 AP-05](../arc-002-principles-constraints.md) |

## 고려한 대안 [필수]

| 대안 | 장점 | 단점 | 결정 동인 충족 |
|---|---|---|---|
| **A. RustFS (Apache 2.0, Rust 초경량, S3 SigV4 지원, 포트 9000/9001 1:1 대체)** | 완전 무료(Apache 2.0), Rust 기반 무-GC 초경량 메모리 사용, 웹 콘솔 기본 내장(`:9001`), MinIO와 동일한 포트 체계로 인프라 충격 `0%` | 신생 프로젝트로 대규모 엔터프라이즈 레퍼런스가 상대적으로 적음 | 모두 충족 |
| B. MinIO 최신 이미지 유지 | 가장 익숙한 에코시스템 | 무료 커뮤니티 배포판 보안 패치 중단으로 CVE 취약점 노출, AGPLv3 라이선스 | 불충족 (`NFR-006`, 장기 유지보수 불가) |
| C. SeaweedFS / Garage | 오픈소스 S3 호환 | Master/Volume 분리로 k3s 단일 노드 배포 복잡도 증가 또는 레이아웃 복잡 | 부분 충족 (`AP-05` 단일 노드 간결성 저하) |

## 결정 [필수]

**대안 A**를 채택한다. `my-space`의 모든 오브젝트 스토리지를 **`RustFS` (`rustfs/rustfs:latest`)**로 전환한다.
- 인프라 네임스페이스(`infra`)의 `rustfs-service`(`:9000` S3 API, `:9001` Web Console)와 `rustfs-pvc`를 사용한다.
- 백엔드(`api`, `ai`)는 표준 S3 호환 엔드포인트를 유지하므로 코드 변경 없이 환경변수(`RUSTFS_*` / `S3_*`) 연결만으로 즉시 연동한다.

## 결과 [필수]

- **좋아지는 점**:
  - Apache 2.0 라이선스로 보안 패치 및 상용화 리스크가 원천 제거된다.
  - Rust 언어 특유의 초경량 메모리/CPU 점유율로 홈랩 자원을 대폭 절약한다.
  - 기존 MinIO S3 API와 100% 호환되므로 백엔드 모듈(`MarkdownModule`, `SearchModule`)의 S3 업로드/다운로드 로직을 수정할 필요가 없다.
- **나빠지는 점과 트레이드오프**:
  - 기존 MinIO 영속 볼륨(`minio-pvc`)에 데이터가 존재할 경우 `rclone` 또는 동기화 스크립트를 통한 1회성 데이터 마이그레이션이 필요하다([homelab-infra 가이드](file:///Users/limkeunhyeok/workspace/homelab-infra/docs/rustfs-migration-guide.md) 참조).
- **후속 작업**:
  - [ARC-001](../arc-001-system-overview.md), [ARC-004](../arc-004-containers-components.md), [ARC-005](../arc-005-data-flow.md), [ARC-007](../arc-007-security-architecture.md) 내 오브젝트 스토리지 표기 일괄 반영

## 검토 체크리스트

- [x] 결정 하나만 다룬다
- [x] 대안이 둘 이상이고 대안마다 장점과 단점이 있다
- [x] 결정이 "…한다"로 끝나는 능동문이다
- [x] 결과에 나빠지는 점과 트레이드오프가 명시되어 있다
- [x] [ARC-001](../arc-001-system-overview.md) 및 [ARC-006](../arc-006-adr.md)의 표에 같은 결정 상태로 등록되어 있다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-08 | 0.1.0 | MinIO CVE 보안 패치 중단 및 라이선스 이슈 대응을 위한 RustFS 전환 ADR 신규 작성 | Antigravity (사용자 검토 대기) |

