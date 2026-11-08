---
id: DSN-005
title: 디자인 시스템과 디자인 토큰
part: design
tier: optional
status: review
version: 0.1.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-01
depends_on: [DSN-001]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-005 디자인 시스템과 디자인 토큰

> v2 검토 초안. 사용자 승인 전이며 구현 완료 또는 접근성 검증 완료를 뜻하지 않는다.

## 디자인 시스템 기반

my-space 전용 시스템 초안. 외부 UI 라이브러리 채택이나 추가 설치를 의미하지 않는다. 디자인 원칙·스타일·컴포넌트·사용 패턴을 함께 제공한다. 토큰은 검토 후 기계 판독 파일로 확정하며 현재는 아래 표가 제안의 기준이다.

## 토큰 계층과 이름 규칙

원시 색상은 참조 토큰, 역할은 의미 토큰으로 분리한다. 화면은 color.action.primary 같은 의미 이름을 사용한다. 필요성이 확인된 컴포넌트별 변형만 추가한다.

## 시스템 토큰

| 토큰 | 제안 값 | 역할 |
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
| radius.control / panel | 8px / 12px | 조작 요소 / 패널 |
| space.1~8 | 4, 8, 12, 16, 24, 32, 48, 64px | 간격 척도 |

## 타이포그래피·움직임

한국어 Noto Sans 계열/시스템 sans-serif, 코드 monospace. 본문 16/24px, 보조 14/20px, 페이지 제목 32/40px, 섹션 제목 22/30px 제안. 모바일 제목은 26/34px. 본문 정보를 과도하게 작게 만들지 않는다. 움직임은 상태 전환에 한정하고 감소 설정을 존중한다. 그림자는 떠 있는 메뉴/모달에만 약하게 사용한다.

## 색 대비 확인

sRGB 상대 휘도 공식으로 흰 배경 대비를 계산했다: 본문 15.55:1, 보조 글자 6.14:1, 주요 버튼 6.16:1, 입력 경계 4.17:1, 오류 글자 6.57:1. 이는 제안 토큰 조합의 계산값이며 실제 생성 시안 전체의 준수 여부는 별도 검증 대상이다. 옅은 구분선을 입력 요소 식별의 유일한 수단으로 쓰지 않는다. DSN-008을 충족하지 않는 생성 결과는 수정한다.

## 테마와 구현 연결

1차는 라이트 모드 시안. 다크 모드는 사용자 검토 후 범위 확정. 승인 이후 프론트 DESIGN.md를 v2 기준으로 갱신하고 실제 CSS·컴포넌트에 연결한다. 지금은 v1 구현 기준을 덮어쓰지 않는다.


## 검토 체크리스트

- [ ] 사용자가 시안과 규칙을 검토했다
- [ ] 관련 화면과 문서의 내용이 일치한다
- [ ] 미결정 항목을 확정하거나 후속 작업으로 구분했다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-01 | 0.1.0 | v2 디자인 검토 초안 | Codex (사용자 검토 대기) |
