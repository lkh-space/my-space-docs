---
id: DSN-002
title: 정보 구조와 사이트맵
part: design
tier: conditional
status: approved
version: 1.2.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-16
depends_on: [PRD-003, DSN-001]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-002 정보 구조와 사이트맵

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

데스크톱 탐색은 [DSN-006-01 · 내비게이션](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-24), 화면군은 [02 · 제품 화면](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=7-3)을 참조한다. 현재 Figma 화면 번호는 [DSN-004](dsn-004-wireframes-screen-spec.md)가 기준이다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 정보 구조

- 홈: 요약과 빠른 실행
- 공간: 노트 / 피드 / 관리 / AI
- 도구: 문서 / 이미지 / 개발 / 계산
- 공통: 알림 / 체험 계정 안내

## 레이블과 탐색

데스크톱 좌측 탐색에 공간·도구를 명확히 묶고 상단에는 현재 위치와 알림을 둔다. 모바일은 홈·공간·도구·알림 진입과 하위 목록을 제공한다. 노트 검색을 전역 통합 검색으로 확대하지 않는다. 화면 목록과 SCR ID는 DSN-004가 기준이다.

## 접근 범위

주 사용자는 개인 데이터를 사용한다. 체험 계정은 샘플과 메모리 변경을 사용하며 저장 안내를 구분한다. 로그인 세부 방식은 인증 계약을 참조할 때 확정한다. 공간 간 협업·공유 메뉴는 만들지 않는다.


## 검토 체크리스트

- [x] 사용자가 시안과 규칙을 검토했다
- [x] 이번 비모바일 정합성 수정 범위를 대조했다 (수정 결과·검증 범위는 DSN-004 참조)
- [x] 미결정 항목을 확정하거나 후속 작업으로 구분했다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-01 | 0.1.0 | v2 디자인 검토 초안 | Codex (사용자 검토 대기) |
| 2026-11-12 | 1.0.0 | Figma v2 최종 확정 시안 반영 및 사용자 승인 완료 | @bob (Keunhyeok Lim) |
| 2026-11-14 | 1.1.0 | 비모바일 Figma DSN 탐색 식별자·직접 링크 연결 | Codex (사용자 지시) |
| 2026-11-15 | 1.2.0 | 비모바일 정합성 수정 결과 반영 및 검증 범위 명시 | Codex (사용자 지시) |
| 2026-11-16 | 1.2.0 | 비모바일 Figma 정합성 검토·수정본 사용자 승인 확정 (문서 승인, 실제 동작 검증과 구분) | @bob (Keunhyeok Lim) |
