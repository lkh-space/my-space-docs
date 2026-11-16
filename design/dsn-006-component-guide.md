---
id: DSN-006
title: 컴포넌트 가이드
part: design
tier: optional
status: approved
version: 1.2.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-16
depends_on: [DSN-005]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-006 컴포넌트 가이드

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

- [DSN-006-01 · 내비게이션](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-24)
- [DSN-006-02 · 상단바](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-30)
- [DSN-006-03 · 버튼](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-36)
- [DSN-006-04 · 입력 요소](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-42)
- [DSN-006-05 · 탐색 요소](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-48)
- [DSN-006-06 · 데이터 표시](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-54)
- [DSN-006-07 · 피드백](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-60)
- [DSN-006-08 · 오버레이](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-66)
- [DSN-006-09 · 파일 입력](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-72)

하위 규격·샘플 프레임은 `DSN-006-XX-YY`로 연결한다. 과거 `DS-*`/`CMP-*`는 이력상의 별칭이다. 공통 보드와 제품 화면의 정합성 검토 기록은 [DSN-004 재검토 결과](dsn-004-wireframes-screen-spec.md#비모바일-figma-재검토-결과)를 참조한다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 컴포넌트 목록과 사용 기준

| 요소 | 용도·변형 | 상태·키보드 |
|---|---|---|
| 버튼 | 주요/보조/위험, 이동은 링크 | 기본·호버·포커스·비활성·처리 중, Enter/Space |
| 입력 | 항상 보이는 레이블·도움말 | 기본·입력·오류·읽기 전용, 오류 시 값 보존 |
| 선택 | 체크박스·라디오·선택창 | 선택/미선택·포커스, 네이티브 동작 우선 |
| 탭 | 같은 문맥의 패널 전환 | 현재 탭 표시·방향키 탐색, 페이지 탐색과 구분 |
| 카드·목록 | 요약·도구 진입 | 제목·설명·행동 계층, 중첩 클릭 혼동 방지 |
| 배지 | 상태 보조 | 색과 텍스트를 함께 사용 |
| 모달 | 삭제·복원 확인 | 제목·취소·실행, 포커스 진입/가두기/복귀, Escape |
| 토스트·알림 | 결과·지속 문제 | 성공은 상태 알림, 조치 필요한 오류는 지속 표시 |
| 파일 선택 | 업로드·순서 조정 | 키보드 선택, 드래그 외 이동 버튼, 파일별 오류 |
| 데이터 표 | 금액·회차·비교 | 헤더 명확화, 모바일 카드 또는 영역 스크롤 |

## 공통 패턴

검색·필터: 조건 유지, 결과 없음에서 초기화 제공. 파일 작업: 선택 → 설정 → 실행 → 결과. 계산: 입력 → 결과 → 산출 근거. 파괴적 행동: 대상·영향 설명 후 확인. 미저장 이탈은 보존 정책과 함께 상세화한다.

내비게이션 아이콘은 노트=링이 있는 노트북, 피드=RSS, 관리=폴더, AI=로봇, 문서 도구=텍스트 도구, 이미지=이미지, 개발=터미널, 계산기=계산기로 구분한다. 메뉴에는 숫자 배지를 사용하지 않는다. 활성 상태의 텍스트·아이콘은 Primary 색을 사용한다.

## 시각 자료

Figma v2 `DSN-006-03` 버튼 보드의 크기는 Small 32px와 Default 40px의 2종이다. 48px 버튼은 현행 시안의 공통 규격에 포함하지 않는다. Primary·Secondary·Ghost·Danger·아이콘 전용 변형과 기본·호버·눌림·포커스·비활성·처리 중 상태를 참조한다. 입력·선택·탭·배지·오버레이·토스트·파일 입력은 각 DSN 분류의 직접 링크에서 찾는다. 시각 샘플이 있다는 사실과 제품 전체의 구현·키보드·프로토타입 검증 완료를 구분한다.

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
