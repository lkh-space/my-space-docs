---
id: DSN-005
title: 디자인 시스템과 디자인 토큰
part: design
tier: optional
status: approved
version: 1.2.1
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-16
depends_on: [DSN-001]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-005 디자인 시스템과 디자인 토큰

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

- [DSN-005-01 · 색상](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-6)
- [DSN-005-02 · 글꼴](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-12)
- [DSN-005-03 · 간격과 형태](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-18)

각 분류의 하위 프레임은 `DSN-005-XX-YY`로 찾는다. 이 문서의 의미 토큰과 Figma 보드의 예시 클래스명은 자동으로 같은 식별자가 되지 않는다. 실제 측정값과 보드에 적힌 규격이 다를 때 별도로 기록한다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 디자인 시스템 기반

승인된 Figma v2를 기준으로 하는 my-space 전용 디자인 시스템이다. 외부 UI 라이브러리 채택이나 추가 설치를 의미하지 않는다. 디자인 원칙·스타일·컴포넌트·사용 패턴을 함께 제공한다. 현재 규격은 아래 표와 연결된 Figma 보드를 기준으로 한다. 기계 판독 토큰 파일의 생성·바인딩 완료를 의미하지 않는다.

## 토큰 계층과 이름 규칙

원시 색상은 참조 토큰, 역할은 의미 토큰으로 분리한다. 화면은 color.action.primary 같은 의미 이름을 사용한다. 필요성이 확인된 컴포넌트별 변형만 추가한다.

## 시스템 토큰

| 토큰 | 기준 값 | 역할 |
|---|---|---|
| color.surface.canvas | #F7F8FA | 전체 배경 |
| color.surface.panel | #FFFFFF | 읽기·작업 패널 |
| color.text.primary | #20242C | 본문 |
| color.text.secondary | #596273 | 보조 설명 |
| color.action.primary | #2457D6 | 주요 행동 |
| color.action.onPrimary | #FFFFFF | 주요 버튼 글자 |
| color.border.subtle | #DDE2EA | 장식 구분선 |
| color.border.control | #737D8C | 입력 경계 |
| color.status.danger | #B42318 | 오류·삭제 |
| color.status.success | #18794E | 성공 |
| color.status.warning | #8A5300 | 주의 |
| radius.none / control / panel / full | 0 / 8 / 12 / 9999px | 구분선·코드 외곽 / 조작 요소 / 패널 / 필·원형 요소 |
| space.1~8 | 4, 8, 12, 16, 24, 32, 48, 64px | 간격 척도 |

## 타이포그래피·움직임

한국어 기본 글꼴은 Noto Sans KR이며 시스템 sans-serif를 대체 글꼴로 사용한다. 코드는 monospace를 사용한다. 다음은 DSN-005-02 보드의 역할별 규격이다.

| 역할 | 크기 / 줄 높이 | 굵기 |
|---|---|---|
| 페이지 제목 | 32 / 40px | Bold 700 |
| 섹션 제목 | 22 / 30px | SemiBold 600 |
| 카드·항목 제목 | 18 / 26px | SemiBold 600 |
| 본문 | 16 / 24px | Regular 400 |
| 레이블 | 13 / 18px | Medium 500 |
| 작은 캡션 | 11 / 16px | Medium 500 |

14/20px 보조 문구는 제품 화면 및 컴포넌트별 규격과 구분한다. 모바일 제목의 기존 26/34px 규칙은 이번 검토 대상에서 제외한다. 본문 정보를 과도하게 작게 만들지 않는다. 움직임은 상태 전환에 한정하고 감소 설정을 존중한다.

### 그림자 단계

DSN-005-03의 그림자는 다음 용도를 따른다. 문서의 단계명과 보드 예시 클래스명은 실제 CSS 구현 완료를 뜻하지 않는다.

| 단계 | 용도 | 보드 표기 |
|---|---|---|
| 0 | 캔버스·평면 배경 | shadow-none |
| 1 | 기본 카드·패널, 1px 경계와 얕은 그림자 | shadow-sm |
| 2 | 호버 카드·선택 시트·콘텍스트 메뉴 | shadow-md |
| 3 | 모달·팝오버, 배경 scrim과 구분 | shadow-lg |

## 색 대비 확인

sRGB 상대 휘도 공식으로 흰 배경 대비를 계산했다: 본문 15.55:1, 보조 글자 6.14:1, 주요 버튼 6.16:1, 입력 경계 4.17:1, 오류 글자 6.57:1. 이는 위 HEX 값과 흰색(#FFFFFF) 조합의 계산값이며 실제 생성 시안 전체의 준수 여부는 별도 검증 대상이다. 옅은 구분선을 입력 요소 식별의 유일한 수단으로 쓰지 않는다. DSN-008을 충족하지 않는 생성 결과는 수정한다. 성공 #18794E는 흰색 대비 5.41:1, 주의 #8A5300은 6.33:1이다. Figma 보드의 대비 주석도 이 계산 기준을 따르며, 실제 레이어의 색·불투명도·배경 조합에 대한 전체 검증과 구분한다.

## 테마와 구현 연결

현재 확정 시안은 라이트 모드다. 다크 모드는 사용자 검토 후 범위 확정. 승인 이후 프론트 DESIGN.md를 v2 기준으로 갱신하고 실제 CSS·컴포넌트에 연결한다. 지금은 v1 구현 기준을 덮어쓰지 않는다.


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
| 2026-11-16 | 1.2.1 | 승인된 Figma 대조 후 화면 설명·규격·흐름 보완 | Codex (사용자 지시) |
| 2026-11-16 | 1.2.1 | 비모바일 Figma 정합성 검토·수정본 사용자 승인 확정 (문서 승인, 실제 동작 검증과 구분) | @bob (Keunhyeok Lim) |
