---
id: ADR-0004
title: Authelia ForwardAuth 게이트웨이 인증 위임 및 외부 방문자 테스트 계정 인메모리 샌드박스 격리
part: architecture
tier: required
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-05
depends_on: [ARC-001, ARC-002]
related: [ARC-003, ARC-005, ARC-006, ARC-007, ARC-008, PRD-006]
superseded_by: null
traces: [REQ-026, REQ-030, NFR-006]
---

# ADR-0004 Authelia ForwardAuth 게이트웨이 인증 위임 및 외부 방문자 테스트 계정 인메모리 샌드박스 격리

## 이 문서는

- **목적**: `my-space` v2의 사용자 인증 방식과, 외부 방문자(`visitor`)가 테스트 계정으로 공간 기능을 체험할 때 주 사용자의 영속 데이터를 완벽히 보호하는 격리 아키텍처를 결정한다. 전체 ADR 목록은 [ARC-006](../arc-006-adr.md)에 있다.
- **대상 독자**: 백엔드·프론트엔드·보안·인프라 개발자, AI 에이전트
- **작성 시점**: 아키텍처 설계 단계(2026-11-05)
- **등급**: 필수

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [ARC-001 시스템 개요](../arc-001-system-overview.md) | 시스템 구성 요약 |
| 선행 | [ARC-002 아키텍처 원칙과 제약](../arc-002-principles-constraints.md) | 테스트 계정 격리 제약(`CON-03`)과 Authelia 인증 위임 제약(`CON-05`) |
| 관련 | [ARC-006 아키텍처 의사결정 기록(ADR)](../arc-006-adr.md) | ADR 색인 |
| 관련 | [ARC-007 보안 아키텍처](../arc-007-security-architecture.md) | 신뢰 경계 및 인증·인가 위치 |
| 관련 | [PRD-006 비기능 요구사항](../../product/prd-006-nfr.md) | 주 사용자·테스트 계정 간 교차 접근 `0건` 목표(`NFR-006`) |

## 결정 상태 [필수]

| 항목 | 값 |
|---|---|
| 결정 상태 | 제안됨 |
| 결정일 | 2026-11-05 (제안일) |
| 결정자 | `@bob (Keunhyeok Lim)` |
| 자문한 사람 | AI 에이전트 |
| 대체 관계 | 없음 |

## 맥락과 문제 [필수]

`my-space`는 홈랩 리버스 프록시(`Traefik`)와 `Authelia` SSO 게이트웨이 뒤에서 운영된다. 동시에 `v2`에서는 외부 방문자가 테스트 계정으로 로그인하여 `공간 › 노트·피드·관리·AI`의 샘플 데이터를 열람하고 직접 노트 작성, 일정 등록, 키워드 필터링을 시험해 볼 수 있어야 한다(`REQ-026`, `US-024`).

만약 외부 방문자의 쓰기 요청이 주 사용자와 동일한 `PostgreSQL`·`RustFS`·`OpenSearch`·`Qdrant`에 저장되거나 단순 조건문 실수로 `ownerId` 필터가 누락되면, 주 사용자의 개인 데이터가 노출되거나 디스크 볼륨이 외부 방문자의 임시 데이터로 오염되고 외부 Slack 웹훅이 오발송될 위험이 있다. 인증과 테스트 계정 데이터 격리를 어떻게 설계할 것인가?

## 결정 동인 [필수]

| 동인 | 출처 |
|---|---|
| 주 사용자와 테스트 계정 간 데이터 교차 접근은 `0건`이어야 하며, 테스트 계정의 변경이 영속 DB·스토리지를 오염시키지 않아야 한다 | [ARC-002 CON-03](../arc-002-principles-constraints.md), [PRD-006 NFR-006](../../product/prd-006-nfr.md) |
| 테스트 계정에서도 읽기 전용이 아니라 노트 생성·수정, 일정 추가·갱신 등 실제 기능 흐름을 그대로 체험하고 초기 샘플 상태로 즉시 리셋할 수 있어야 한다 | [PRD-003 US-024](../../product/prd-003-user-stories.md), [PRD-005 FN-025](../../product/prd-005-functional-spec.md) |
| 홈랩 공통 SSO(`Authelia`)와 일관된 인증 체계를 유지하고 포트 우회 접근을 차단해야 한다 | [ARC-002 CON-05](../arc-002-principles-constraints.md) |

## 고려한 대안 [필수]

| 대안 | 장점 | 단점 | 결정 동인 충족 |
|---|---|---|---|
| **A. Authelia ForwardAuth 헤더(`X-Forwarded-Auth-Secret` 검증) 인증 + 테스트 계정(`visitor`) 요청의 백엔드 세션별 인메모리 샌드박스 스토어 격리** | 주 사용자의 `PostgreSQL`·`RustFS`·`OpenSearch`·`Qdrant`에 테스트 계정 쿼리가 물리적으로 닿지 않음. 세션별 독립 체험 및 1클릭 초기화 가능. 디스크 오염 및 외부 웹훅 오발송 `0%` | 공간 서비스/리포지토리 계층에 샌드박스 분기 어댑터를 두어야 하고 세션당 소량의 메모리를 사용한다 | 모두 충족 |
| B. 동일한 `PostgreSQL`·`RustFS`·`OpenSearch`에 `ownerId = 'visitor'`로 함께 저장하고 주기적 크론으로 삭제 | 별도 인메모리 스토어 없이 기존 Prisma 쿼리를 그대로 쓴다 | 여러 방문자가 동시에 접속하면 서로의 수정 내용을 덮어쓰게 되고, 대량 입력 시 디스크/DB가 오염되며 쿼리 실수 시 주 사용자 데이터와 섞일 위험이 있다 | 불충족 (`CON-03`, 동시 체험 격리 취약) |
| C. 테스트 계정을 위한 별도 PostgreSQL·RustFS·OpenSearch 컨테이너 세트를 복제 운영 | 주 사용자 DB와 물리적으로 분리된다 | 홈랩 서버 메모리를 2배로 소비하며 방문자 세션 간 데이터 충돌 문제는 여전히 남는다 | 불충족 (`AP-05` 자원 효율 위배) |

## 결정 [필수]

**대안 A**를 채택한다. 모든 요청의 신원 확인은 `Traefik + Authelia`가 주입하는 ForwardAuth 헤더(`Remote-User`, `Remote-Groups`)와 프록시 공유 시크릿(`X-Forwarded-Auth-Secret`)으로 검증하고, 식별된 계정이 테스트 계정(`visitor`)일 경우 백엔드는 영속 저장소(`PostgreSQL`, `RustFS`, `OpenSearch`, `Qdrant`) 및 외부 웹훅(`Slack`)을 호출하지 않고 **초기 샘플 시드를 복제한 세션별 인메모리 샌드박스 스토어(TTL 자동 만료 및 수동 초기화 지원)**에서만 읽기·쓰기를 처리한다.

## 결과 [필수]

- **좋아지는 점**:
  - 외부 방문자가 어떤 조작(삭제·대량 생성 등)을 하더라도 주 사용자의 `PostgreSQL`, `RustFS`, `OpenSearch`, `Qdrant` 데이터와 외부 Slack 채널은 100% 안전하게 보호된다.
  - 방문자마다 독립된 인메모리 시드 복제본을 받아 체험하고 언제든 `샘플 데이터 초기화` 버튼으로 초기 상태로 되돌릴 수 있다.
- **나빠지는 점과 트레이드오프**:
  - 메모리 고갈(DoS)을 막기 위해 인메모리 샌드박스의 최대 활성 세션 수(LRU)와 세션당 최대 항목 수 상한, 유휴 TTL(예: 30분~1시간)을 설정해야 한다.
- **후속 작업**:
  - [ARC-005 데이터 흐름](../arc-005-data-flow.md)의 `DF-06` 및 [ARC-007 보안 아키텍처](../arc-007-security-architecture.md)의 인증·인가 위치에 반영

## 검토 체크리스트

- [x] 결정 하나만 다룬다
- [x] 대안이 둘 이상이고 대안마다 장점과 단점이 있다
- [x] 결정이 "…한다"로 끝나는 능동문이다
- [x] 결과에 나빠지는 점과 트레이드오프가 명시되어 있다
- [x] 결정 동인의 `REQ-`·`NFR-` ID가 [PRD-002](../../product/prd-002-requirements-scope.md), [PRD-006](../../product/prd-006-nfr.md)에 존재한다
- [x] [ARC-001](../arc-001-system-overview.md) 및 [ARC-006](../arc-006-adr.md)의 표에 같은 결정 상태로 등록되어 있다

## 작성 예시

예시 없음 — 실무 프로젝트 ADR 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-05 | 0.1.0 | ADR-0004 최초 작성 | Antigravity (사용자 검토 대기) |
