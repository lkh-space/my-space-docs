---
id: DSN-004
title: 와이어프레임과 화면 명세
part: design
tier: conditional
status: approved
version: 1.2.2
owner: "@bob (Keunhyeok Lim)"
last_reviewed: '2026-11-22'
depends_on: [DSN-003, PRD-005]
related: [PRD-006]
superseded_by: null
traces: [REQ-031]
---

# DSN-004 와이어프레임과 화면 명세

> v2 정식 승인본. Figma v2(디자인 시스템 및 제품 화면) 확정 시안 기준.

## Figma 탐색

전체 화면군은 [02 · 제품 화면](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=7-3)의 `DSN-004-01~10`이다. 아래 화면 목록의 직접 링크와 **이전 문서 번호** 열을 함께 사용한다. `SCR-002` 알림 및 `SCR-090` 체험 안내는 독립 프레임을 이번 검토에서 확인하지 못했으므로 임의의 node-id를 연결하지 않는다.

2026-11-14 정합성 작업: 모바일과 프론트 DESIGN.md는 비교 대상에서 제외한다. 전체 탐색 규칙은 [design README](README.md)를 따른다.

## 화면 목록

아래 SCR ID는 현재 Figma 프레임 이름을 따른다. 분류 식별자(`DSN-004-XX`)와 화면 식별자(`SCR-*`)를 구분한다. 이전 번호는 이력 대조용이며 새 연결에 사용하지 않는다.

| 현재 SCR ID | 화면 | 직접 링크 | 스토리 | 이전 문서 번호 |
|---|---|---|---|---|
| `SCR-001` | 홈 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-35565) | US-001, US-002 | 변경 없음 |
| `SCR-010` | 노트 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-35851) | US-003 | 변경 없음 |
| `SCR-020` | 피드 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-34021) | US-004, US-005 | 변경 없음 |
| `SCR-030` | 관리 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-21099) | US-006~008 | 변경 없음 |
| `SCR-040` | AI 어시스턴트 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-21895) | US-009 | 변경 없음 |
| `SCR-050` | 도구 목록 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-34965) | US-001 | 변경 없음 |
| `SCR-051` | PDF 병합 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-34439) | US-010 | 변경 없음 |
| `SCR-051A` | PDF 페이지 추출·분할 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-32071) | US-010 | SCR-051에 포함 |
| `SCR-051B` | PDF 암호 해제 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-22607) | US-010 | SCR-051에 포함 |
| `SCR-052` | 마크다운 변환 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-22802) | US-011 | 변경 없음 |
| `SCR-053` | 이미지 압축 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-28785) | US-012 | SCR-060에 포함 |
| `SCR-054` | 이미지 크기 조절 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-32869) | US-012 | SCR-060에 포함 |
| `SCR-055` | 이미지 포맷 변환 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-23044) | US-012 | SCR-060에 포함 |
| `SCR-056` | EXIF 조회·제거 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-23441) | US-013 | SCR-060에 포함 |
| `SCR-057` | RDB·NoSQL 스키마 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-24495) | US-014 | SCR-070 |
| `SCR-058` | Cron 해석·생성 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-29110) | US-015 | SCR-071 |
| `SCR-059` | 정규식 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-30772) | US-016 | SCR-072 |
| `SCR-060` | JWT | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-23744) | US-017 | SCR-073 |
| `SCR-061` | 타임스탬프 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-31178) | US-018 | SCR-074 |
| `SCR-062` | 더치페이 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-26464) | US-019 | SCR-080 |
| `SCR-063` | 급여·실수령액 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-27307) | US-020 | SCR-081 |
| `SCR-064` | 대출·예적금 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-24111) | US-021 | SCR-082 |
| `SCR-065` | 단가 비교 | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-26934) | US-022 | SCR-083 |
| `SCR-066` | 날짜·D-day | [프레임 열기](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=30-27674) | US-023 | SCR-084 |

`SCR-002` 알림, `SCR-090` 체험 안내는 PRD 요구사항과 연결을 유지하되, 이번 검토에서 독립된 Figma 프레임 링크는 확인하지 못했다. 기능이 없다는 판정과는 구분한다.

## 대표 화면 명세

- **홈 (SCR-001)**: 좌측 240px 공통 탐색(L1 사이드바), 상단 제목/알림 바, 본문 상단 인사·요약 영역, 그 아래 최근 기록과 피드의 2개 패널(최근 기록을 더 넓게 배치), 하단 일정·갱신 / AI 상태 / 빠른 도구 실행의 3개 카드. 빠른 도구 실행은 별도 고정 바가 아닌 카드 내부의 2×2 진입 그리드다. 각 요약 카드 로딩 실패 시 독립적 재시도 버튼 제공.
- **노트 (SCR-010)**: 공통 탐색 옆에 폴더·태그·문서 목록을 세로로 묶은 패널과 편집/미리보기 작업 패널을 배치한다. 상단 뷰 모드 토글로 에디터 단독 / 스플릿 뷰(에디터+미리보기 동시) / 미리보기 단독 3종 모드 전환 및 단축키 안내. 현재 시안의 저장 표시는 `서버 저장됨`이며 마크다운 툴바를 제공한다. 서버 성공 응답 이후에만 완료로 표시하며 저장 중·실패·체험 상태의 의미는 [DSN-009](dsn-009-ui-states-errors.md)의 노트 저장 상태를 따른다. 모바일에서는 목록·편집·미리보기를 1열 탭 패널로 전환. 복원은 버전별 diff 비교 모달 후 확인.
- **피드 (SCR-020)**: 공통 탐색을 제외한 본문은 피드 목록과 아티클 리더의 2열 구조이며 구독 소스는 상단 필터로 선택한다. '모두 읽음' 일괄 처리 버튼. 키워드 필터 설정에서 가중치·숨김·제외 규칙을 편집하고 키워드 삭제 후 상태를 같은 팝업에 표시한다. 설정 변경 전·후 프레임은 동일 팝업의 상태 시안이며 별도 모달 종류로 세지 않는다. 태그는 매칭된 활성 관심 키워드 배지이며 노트 태그와 별개다. 해당 키워드로 필터링하고, 모두 읽음은 현재 필터와 목록 수집 시각 상한에 해당하는 전체 글에 적용한다(현재 페이지에 한정하지 않음).
- **도구 탐색 (SCR-050)**: 카테고리 탭(문서, 이미지, 개발, 계산), 목적을 직관적으로 설명하는 진입 카드 그리드. 마케팅 배너·유료 플랜 배제.
- **PDF 도구 (SCR-051)**: 파일 드롭존, 선택된 파일 리스트 카드(파일명, 용량, 페이지 수, 드래그 앤 드롭 재정렬 핸들 `:::`, 위/아래 이동 버튼, 개별 삭제), 작업 완료 후 하단 고정 다운로드 패널(`Merge Result Panel`). 암호화된 파일의 경우 백엔드(qpdf) 처리 사전 안내.
- **Cron 생성기 (SCR-058)**: 자연어/주기 선택 기반 양방향 빌더(15분마다, 매시간 특정 분, 매월 특정일, 고급 커스텀) 카드 및 실시간 Cron 식 변환 + 다음 실행 시각 5회 미리보기.

## 시안과 연동

- **Figma v2 (`my-space-v2`) 시안**: `01 · 디자인 시스템` 및 `02 · 제품 화면` (노트 뷰 전환, 피드 필터 설정·키워드 삭제 후 상태, Cron 빌더, PDF 병합 작업의 화면 및 상태 시안). 프로토타입 연결과 실제 호버·키보드·저장 동작의 검증 완료를 이 문서만으로 보증하지 않는다.
- 레거시 Stitch 검토 프로젝트([Stitch](https://stitch.withgoogle.com/projects/18082781618690839750)) 대비 Figma v2 확정 시안으로 전면 이관됨. API operationId와 오류 코드는 후속 계약 확인 후 연결한다.


## 비모바일 Figma 재검토 결과

2026-11-14~15 브라우저에서 확인하고 수정한 결과다. 아래에 기록한 정합성 미해결 항목은 수정했다. 디자인 방향은 검토 완료된 시안을 유지하며, 모든 하위 레이어·모든 프로토타입 행동의 검증 완료를 의미하지 않는다.

| 항목 | 수정 결과 | 확인 범위 |
|---|---|---|
| 문서 탐색 번호 | DSN-001~009 문서 번호를 유지하고 Figma 상위 분류·비모바일 규격 프레임·제품 화면군/안내 프레임에 DSN 탐색 번호 적용 | README의 14개 디자인 시스템 분류·10개 제품 화면군과 이 문서의 24개 SCR 직접 링크 연결 |
| 상태 보드 번호 | 로딩·빈 상태·오류·완료 순서를 유지하고 내부 번호를 1~4로 수정 | DSN-009-01 상태 보드 |
| 아이콘 예시 | 간격·형태 및 탐색 요소 보드의 노트·피드·문서 도구를 노트북·RSS·텍스트 도구 아이콘으로 정리하고 숫자 배지 12 제거 | 내비게이션 샘플과 아이콘 설명 대조 |
| 상태·이력 문구 | 디자인 이전 대기·검토 초안·제안값 등의 현재 상태와 맞지 않는 안내를 정리. 제품 안내 카드 24곳과 디자인 시스템 안내·푸터 정리 | 콘텐츠 자체의 상태와 접근성 구현 검증 상태는 구분. 노트 예시 콘텐츠 상태는 초안으로 표기 |
| 문서·시안 규격 | 버튼 문서의 공통 높이를 시안의 32/40px에 맞춤. 불러오기·영구 삭제 레이블의 줄바꿈 및 눌림 상태의 잔여 글자 정리 | DSN-006-03 버튼 보드. 48px 공통 규격을 새로 추가하지 않음 |
| 노트 상단 | 3개 뷰의 내보내기·경로·상태 배지 줄바꿈 정리. 저장 완료·저장 시각 표시를 서버 기준으로 수정 | 편집/분할/미리보기 시안. 서버 응답·실패 시 편집 내용 유지·체험 구분은 DSN-009에 명시 |
| 피드 팝업 | 대괄호 텍스트를 테두리 있는 선택 필드·체크박스·입력·추가/삭제 조작으로 교체. 제목/설명과 저장 버튼 정렬·서체·모서리 정리 | 4개 상태를 편집 가능한 텍스트·벡터 레이어로 구성. 재사용 메인 컴포넌트 등록이나 실제 폼 데이터 처리를 뜻하지 않음 |
| 프레임·섹션 경계 | 10개 제품 섹션을 실제 자식 레이어 경계에 맞춤. 관리 루트를 1280×1360으로 늘리고 사이드바 높이도 맞춤 | 노트/피드 경계와 팝업 소속, 관리 하단 포함 여부 확인. PDF 분할 1280×1440 및 마크다운 1280×1165 루트 안의 본문 확인. 캔버스 프레임 높이는 제품의 고정 뷰포트 규격이 아님 |
| 식별자 이력 | CMP/NAV/HDR/DS/SPEC 별칭은 이력으로 구분 | AI 탐색은 README의 DSN 분류와 직접 node-id를 우선 사용 |

### 피드 팝업 상태와 검증

| 상태 | 직접 링크 | 미리보기에서 확인한 전환 |
|---|---|---|
| 기본 | [기본 설정](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=97-1766) | 피드의 키워드 필터 설정에서 열기 |
| 관심 키워드 변경 후 | [가중치·키워드 변경](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=119-2) | 관심 키워드 영역 클릭으로 전환 |
| 제외 키워드 변경 후 | [제외 규칙 변경](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=127-2) | 제외 키워드 영역 클릭으로 전환 |
| 키워드 삭제 후 | [삭제 후 상태](https://www.figma.com/design/i4UZw9LDRw0JLSWWbrmdik/my-space-v2?node-id=127-10) | 삭제 후 상태로 전환하고 저장하고 닫기로 피드 복귀 |

브라우저 미리보기에서 기본 → 관심 키워드 변경 → 제외 키워드 변경 → 삭제 후 → 닫기 경로를 확인했다. 영역 단위 클릭으로 상태를 교체하는 시연이므로 개별 입력·선택·삭제 조작을 처리하는 실제 애플리케이션 구현과 구분한다. 전체 화면의 호버·키보드·API 저장·접근성 구현은 이번 정합성 검증 범위에 포함하지 않는다.

문서의 일반 상태 목록이 있다는 이유만으로 각 제품 화면에 해당 상태 시안이 모두 있다고 판단하지 않는다. 알림·체험·입력 실패·저장 실패 등의 화면별 근거와 구현 검증은 개별적으로 확인한다.

## 후속 정합성 수정 (2026-11-15~16)

앞선 수정 목록 밖에서 추가 확인된 다음 불일치를 수정했다. 이번 범위도 모바일 및 프론트 DESIGN.md는 제외한다.

| 항목 | 반영 결과 |
|---|---|
| 홈 구조 설명 | 상단 최근 기록·피드 2개 패널, 하단 일정·AI·빠른 도구 3개 카드 및 도구 카드 내부 2×2 진입 그리드로 문서 수정 |
| 확정 상태 표현 | DSN-001·005의 과거 초안·제안·Stitch 중심 산출물 설명을 현재 Figma 승인 범위와 구분 |
| 글꼴·형태·그림자 | DSN-005에 카드 제목·레이블·캡션, 0/8/12/9999px 모서리 및 그림자 0~3단계를 명시. 카드 제목 굵기 주석의 Medium(600)은 SemiBold(600)으로 정리 |
| 문서 도구 흐름 | DSN-003에 SCR-051A·051B·052의 추출/분할·암호 해제·마크다운 변환 흐름 추가. 프로토타입·실제 처리 검증 완료와 구분 |
| 제품 초안 표기 | 비모바일 제품의 Review Draft 배지 4곳을 디자인 기준으로 정리하고 SCR-061 제목의 검토 초안 제거. 모바일 표기는 유지 |
| 화면 골격 아이콘 | DSN-007-01-02·03의 노트·피드·문서 도구 아이콘을 기준 내비게이션의 노트북·RSS·텍스트 도구 벡터로 교체 |
| 색상 대비 주석 | HEX와 흰색 배경의 재계산값으로 보드의 9개 주석 수정. 캔버스 1.06, 표면/본문 15.55, 보조 6.14, 주요 행동 6.16, 입력 경계 4.17, 성공 5.41, 주의 6.33, 오류 6.57:1. 전체 레이어 접근성 통과 선언과 구분 |

## 검토 체크리스트

- [x] 사용자가 시안과 규칙을 검토했다
- [x] 이번 비모바일 정합성 수정 범위를 대조했다 (수정 결과·검증 범위는 DSN-004 참조)
- [x] 미결정 항목을 확정하거나 후속 작업으로 구분했다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-22 | 1.2.2 | API·화면·검증 범위 계약 불일치 보완 | AI 에이전트 |
| 2026-11-01 | 0.1.0 | v2 디자인 검토 초안 | Codex (사용자 검토 대기) |
| 2026-11-12 | 1.0.0 | Figma v2 최종 확정 시안 반영 및 사용자 승인 완료 | @bob (Keunhyeok Lim) |
| 2026-11-14 | 1.1.0 | 비모바일 Figma DSN 탐색 식별자·직접 링크 연결 | Codex (사용자 지시) |
| 2026-11-15 | 1.2.0 | 비모바일 정합성 수정 결과 반영 및 검증 범위 명시 | Codex (사용자 지시) |
| 2026-11-16 | 1.2.1 | 승인된 Figma 대조 후 화면 설명·규격·흐름 보완 | Codex (사용자 지시) |
| 2026-11-16 | 1.2.1 | 비모바일 Figma 정합성 검토·수정본 사용자 승인 확정 (문서 승인, 실제 동작 검증과 구분) | @bob (Keunhyeok Lim) |
