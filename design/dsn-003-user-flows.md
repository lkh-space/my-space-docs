---
id: DSN-003
title: 사용자 흐름
part: design
tier: conditional
status: approved
version: 1.2.1
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-16
depends_on: [DSN-002, PRD-004]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-003 사용자 흐름

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

기록은 [DSN-004-02 · 노트](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-12), 피드는 [DSN-004-03 · 피드](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-18), 일정은 [DSN-004-04 · 관리](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-24), AI는 [DSN-004-05 · AI 어시스턴트](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-30), PDF는 [DSN-004-07 · 문서 도구](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=10-42)에서 찾는다. `FL-*`는 사용자 흐름 ID이며 Figma의 `Flow 1~4` 프로토타입 번호와 같은 체계가 아니다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 주요 흐름

| 흐름 | 화면 순서 | 예외·복구 |
|---|---|---|
| FL-001 기록 | SCR-001 → SCR-010 검색·선택 → 편집 → 저장 | 저장 실패 시 입력 유지·재시도, 버전 복원은 확인 후 실행 |
| FL-002 피드 | SCR-020 → 구독 등록 → 글 선택 → 본문 | 수집 실패·본문 추출 실패 안내, 원문 열기 |
| FL-003 일정 | SCR-030 → 유형 선택 → 등록 → 목록 → 갱신 | 필드 오류·서버 오류 구분, 날짜 유지 |
| FL-004 AI | SCR-040 → 텍스트 또는 음성 입력 → 응답 → 노트 저장 | 마이크 거절은 텍스트 입력 제공, 연결 실패·저장 실패 구분 |
| FL-005 PDF | SCR-050 → SCR-051 파일 선택 → 순서 조정 → 병합 → 다운로드 | 암호 입력 오류·서버 중단·용량 초과 안내 |
| FL-006 로컬 도구 | SCR-050 → SCR-053~066 → 입력 → 결과 → 복사·다운로드 | 입력 유지, 오류 위치와 수정 방법 표시 |
| FL-007 체험 | SCR-090 → 각 공간 → 임시 편집 | 서버 저장 아님과 새로고침 초기화 안내 |
| FL-008 PDF 추출·분할 | SCR-050 → SCR-051A 파일 선택 → 페이지 선택·범위 입력 → 추출/분할 방식 선택 → PDF 또는 ZIP 다운로드 | 페이지 범위 오류를 입력 옆에 표시하고 선택 유지 |
| FL-009 PDF 암호 해제 | SCR-050 → SCR-051B 파일 선택 → 암호 입력 → 해제 → 결과 다운로드 | 잘못된 암호 안내·재입력, 서버 처리 실패 시 재시도 |
| FL-010 마크다운 변환 | SCR-050 → SCR-052 파일 불러오기 또는 직접 입력 → PDF/HTML 선택 → 미리보기 → 변환·다운로드 | 입력 유지·변환 실패 안내. 노트의 영속 저장 흐름과 구분 |

화면 링크는 [DSN-004 화면 목록](dsn-004-wireframes-screen-spec.md#화면-목록)을 따른다. 위 흐름은 화면의 진입·입력·결과를 설명하며 각 단계의 프로토타입 연결 또는 실제 처리 검증 완료를 뜻하지 않는다. 상태 표시는 DSN-009를 따른다. 브라우저 뒤로 이동 시 목록 문맥 보존을 제안하며 미저장 이탈 처리는 저장 정책과 함께 상세화한다.


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
