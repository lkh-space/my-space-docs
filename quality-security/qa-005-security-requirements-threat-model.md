---
id: QA-005
title: 보안 요구사항과 위협 검증
part: quality-security
tier: conditional
condition: 배포
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-26'
depends_on: [ARC-007, BE-004, QA-001]
related: [BE-013, INF-003, QA-006, QA-007]
superseded_by: null
traces: [NFR-006]
---

# QA-005 보안 요구사항과 위협 검증

## 목표와 신뢰 경계

로그인·권한은 Authelia와 Traefik, 리소스 소유권은 각 앱에서 검증한다. 브라우저, 공개 Ingress, 서비스 간 저장, DB·객체·검색 저장소를 별도 경계로 본다. 적용 목표는 인증된 개인 데이터의 격리다. ASVS 인증 등급 달성 여부는 별도로 평가한다. [경계와 데이터 흐름](../architecture/arc-007-security-architecture.md) · [권한표](../backend/be-004-authn-authz.md)

## 위협과 보안 수용 기준

| 위협 | 검증과 기대 결과 | 근거 |
|---|---|---|
| 계정 혼동·권한 상승 | 주 사용자와 체험 계정으로 같은 개인 화면에 진입한다. 체험 계정의 개인 API는 403, 다른 소유자 리소스는 404. 역할 전환 후 이전 응답은 렌더링되지 않는다. | TC-V2-01, BE-004 |
| 브라우저 출처 혼동 | 허용한 Web origin으로 쓰기를 확인하고, 허용되지 않은 origin은 JSON·multipart·SSE 처리 전에 403인지 확인한다. | BE-004, TC-V2-01·05 |
| 내부 경로 노출 | Ingress 경로표와 네트워크 정책을 대조하고 브라우저 경계에서 내부 문서 저장·상세 readiness가 제공되지 않는지 확인한다. | INF-003, TC-V2-03 |
| 검색·첨부의 잔존 정보 | 다른 소유자 첨부와 삭제된 검색 결과가 노출되지 않고, RAG 근거가 변경된 스트림은 최신 상태를 다시 확인하는지 검증한다. | TC-V2-02·03 |
| 비밀·본문 로그 유출 | 합성 요청의 추적 ID로 API·AI 로그를 조회한다. 비밀번호·쿠키·본문·녹음이 없고 HTTP/SSE 오류에는 안전한 코드와 설명이 있다. | BE-013, QA-007 |

## ASVS 적용 범위와 갱신

인증·세션·권한·파일 처리·API·데이터 보호·보안 로그 영역을 위 표와 연결한다. 인증 제공자의 비밀번호·세션 정책은 인프라 설정에서, 앱의 인가·오류 처리는 앱 테스트에서 검증한다. 세부 ASVS 항목별 등급·충족률은 미평가다. 인증 경계, 저장 대상, 외부 AI 공급자, Ingress가 바뀌면 위협과 관련 TC를 다시 검토한다. [검증 전략](qa-001-test-strategy.md) · [네트워크와 접근 제어](../infrastructure/inf-003-network-access.md)

## 변경 이력

| 날짜 | 버전 | 변경 내용 |
|---|---|---|
| 2026-11-26 | 0.1.0 | v2 검토 초안 작성 |
