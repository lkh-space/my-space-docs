---
id: DSN-008
title: 접근성 가이드
part: design
tier: conditional
status: approved
version: 1.2.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-16
depends_on: [PRD-006]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-008 접근성 가이드

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

접근성 시각 규칙은 [레이아웃 및 키보드 접근성 가이드](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=33-50392), [입력 요소](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-42), [버튼](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-36), [오버레이](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-66)에서 찾는다. DSN-008 전용 독립 보드를 새로 만든 것은 아니다. Figma node-id는 검사 대상을 가리키며 접근성 통과 증거를 뜻하지 않는다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 적용 기준과 목표 수준

PRD-006 NFR-007의 WCAG 2.2 AA를 따른다. 전체 v2 화면이 대상이다. 국내 기준 대응표와 법적 적용 여부는 확인 전 단정하지 않는다.

## 디자인 규칙

| 영역 | 규칙 | 기준 |
|---|---|---|
| 대비 | 본문 4.5:1, 큰 글자 3:1, 의미 있는 UI 3:1 | 1.4.3, 1.4.11 |
| 재배치 | 320 CSS px에서 일반 콘텐츠 사용 | 1.4.10 |
| 키보드 | 핵심 기능 키보드 조작·명확한 포커스 | 2.1.1, 2.4.7, 2.4.11 |
| 드래그 | 파일 순서·캔버스 조작에 대안 | 2.5.7 |
| 터치 | 최소 기준 준수, 주요 조작은 44px 높이 제안 | 2.5.8 |
| 입력 | 레이블·오류 원인·수정 방법 | 3.3.1, 3.3.2 |
| 알림 | 저장·처리 상태를 포커스 강탈 없이 전달 | 4.1.3 |

## 확인 방법

디자인 단계: 대비 계산, 포커스 순서, 모바일 재배치, 오류 문구 검토. 구현 단계: 자동 검사와 키보드·스크린리더 수동 검사. 시안 이미지가 접근성 통과의 증거는 아니다. 담당은 구현 시 정한다.

## 예외와 개선 계획

ERD와 정규식 구조는 텍스트 결과를 함께 설계한다. AI 음성에는 텍스트 입력을 제공한다. 구체적인 예외 승인 없이 기능을 접근성 대상에서 제외하지 않는다.


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
