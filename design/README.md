# 디자인 문서와 Figma 탐색

검토 완료된 Figma v2를 시각 디자인의 기준으로 사용하고, 이 폴더의 DSN 문서를 해당 디자인과 연결한다. 문서와 시안이 다를 때 추정으로 새 디자인이나 기능 정책을 만들지 않고 불일치를 기록한다. 제품 기능·데이터 정책은 승인된 PRD를 따른다.

## 문서 승인 상태

2026-11-16 사용자 지시에 따라 피그마 관련 디자인 문서 **DSN-001~009의 현재 수정본을 승인(`approved`) 상태로 확정**했다. 이번 승인은 비모바일 Figma와 문서의 정합성 검토·수정 결과에 대한 승인이다. 모바일은 이번 검토에서 제외했으며, 실제 제품의 API·키보드·접근성 동작 검증 완료를 의미하지 않는다. Stitch 기록의 과거 초안·미승인 표기는 이력으로 유지한다.

## 이번 정합성 작업 범위

2026-11-14~15: 비모바일 디자인 시스템과 데스크톱 제품 화면의 번호·명칭·구조를 검토하고 탐색 식별자를 연결했다. 후속 정합성 항목의 아이콘·안내 문구·버튼·노트 저장 표시·피드 팝업·프레임 경계를 수정하고, 피드 팝업의 상태 전환 및 닫기를 미리보기에서 확인했다. 모바일과 프론트엔드 저장소의 DESIGN.md는 이번 비교·수정 대상에서 제외한다. 프로토타입의 모든 클릭·호버, 실제 API 처리, 접근성 구현이 검증되었다는 의미는 아니다.

## AI가 디자인을 찾는 순서

1. 관련 DSN 문서의 **Figma 탐색** 링크 또는 아래 대응표를 연다.
2. 문서 ID는 `DSN-NNN`, Figma 분류는 `DSN-NNN-XX`, 하위 프레임은 `DSN-NNN-XX-YY`로 구분한다. 뒤의 숫자는 문서의 하위 탐색 식별자이며 별도 DSN 문서를 뜻하지 않는다.
3. 제품 화면은 `DSN-004-XX` 화면군 안의 `SCR-*` 프레임을 찾는다. SCR은 화면 ID이므로 DSN으로 대체하지 않는다. 기본 화면과 프로토타입·변경 후 상태는 서로 구분한다.
4. 링크의 file key와 node-id를 우선 사용한다. 제목의 유사성이나 화면 번호만으로 추정하지 않는다. `SCR-060`은 현재 JWT이며, 과거 문서에서 이미지 화면군을 의미한 번호와 혼동하지 않는다.
5. 화면별 직접 링크와 과거 번호 대응은 [DSN-004](dsn-004-wireframes-screen-spec.md), 수정 결과와 검증 범위는 같은 문서의 **비모바일 Figma 재검토 결과**를 참조한다.

## 문서 목록

- [DSN-001 디자인 브리프](dsn-001-design-brief.md)
- [DSN-002 정보 구조와 사이트맵](dsn-002-information-architecture.md)
- [DSN-003 사용자 흐름](dsn-003-user-flows.md)
- [DSN-004 와이어프레임과 화면 명세](dsn-004-wireframes-screen-spec.md)
- [DSN-005 디자인 시스템과 디자인 토큰](dsn-005-design-system-tokens.md)
- [DSN-006 컴포넌트 가이드](dsn-006-component-guide.md)
- [DSN-007 반응형 디자인](dsn-007-responsive.md)
- [DSN-008 접근성 가이드](dsn-008-accessibility.md)
- [DSN-009 UI 상태와 오류 상태](dsn-009-ui-states-errors.md)

## 디자인 시스템 대응표

페이지: [01 · 디자인 시스템](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=7-2)

| Figma 식별자 | 내용 | 이전 분류 | 직접 링크 |
|---|---|---|---|
| `DSN-005-01` | 색상 | 01 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-6) |
| `DSN-005-02` | 글꼴 | 02 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-12) |
| `DSN-005-03` | 간격과 형태 | 03 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-18) |
| `DSN-006-01` | 내비게이션 | 04 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-24) |
| `DSN-006-02` | 상단바 | 05 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-30) |
| `DSN-006-03` | 버튼 | 06 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-36) |
| `DSN-006-04` | 입력 요소 | 07 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-42) |
| `DSN-006-05` | 탐색 요소 | 08 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-48) |
| `DSN-006-06` | 데이터 표시 | 09 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-54) |
| `DSN-006-07` | 피드백 | 10 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-60) |
| `DSN-006-08` | 오버레이 | 11 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-66) |
| `DSN-006-09` | 파일 입력 | 12 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-72) |
| `DSN-009-01` | 화면 상태 | 13 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-78) |
| `DSN-007-01` | 화면 골격 | 14 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=9-84) |

## 제품 화면 대응표

페이지: [02 · 제품 화면](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=7-3)

| Figma 식별자 | 화면군 | 직접 링크 |
|---|---|---|
| `DSN-004-01` | 홈 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-6) |
| `DSN-004-02` | 노트 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-12) |
| `DSN-004-03` | 피드 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-18) |
| `DSN-004-04` | 관리 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-24) |
| `DSN-004-05` | AI 어시스턴트 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-30) |
| `DSN-004-06` | 도구 목록 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-36) |
| `DSN-004-07` | 문서 도구 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-42) |
| `DSN-004-08` | 이미지 도구 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-60) |
| `DSN-004-09` | 개발자 도구 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-78) |
| `DSN-004-10` | 계산기 | [열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-100) |

## 식별자와 이력의 해석

- `CMP-*`, `NAV-*`, `HDR-*`, `DS-*`, `SPEC-*` 등 보드 내부에 남은 식별자는 과거 시안의 컴포넌트·표 항목 별칭이다. 새 DSN 문서 ID로 해석하지 않는다. 문서와 연결하는 기준은 위 DSN 식별자와 Figma node-id다.
- [Stitch 검토 기록](stitch-review.md)은 이전 생성·수정 이력이다. 당시의 검토 초안·미승인 표기는 현재 Figma의 승인 상태를 대체하지 않는다.
- 승인된 시각 디자인과 실제 동작 검증은 구분한다. 시안의 예시 저장 문구나 계산 결과만으로 새로운 저장·계산 정책을 정하지 않는다.
- DSN-001~009 문서 번호와 파일명은 유지한다. 기존 번호를 재사용해 다른 문서로 바꾸지 않는다.
