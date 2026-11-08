---
id: OPS-001
title: 용어집
part: operations
tier: required
status: approved
version: 1.0.0
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-01
depends_on: []
related: [PRD-001, PRD-002, PRD-003, PRD-004, PRD-005, PRD-006, PRD-007, OPS-002]
superseded_by: null
traces: []
---

# OPS-001 용어집

## 이 문서는

- **목적**: `my-space` 프로젝트에서 사용하는 도메인 용어와 기술 용어의 정의, 영문 표기, 코드 식별자를 한곳에 정한다. 용어와 정의의 **기준 문서**다.
- **대상 독자**: 프로젝트 소유자, AI 에이전트
- **작성 시점**: 프로젝트 기획 시작 시점부터 작성하며 새 용어가 나올 때마다 갱신한다.
- **등급**: 필수 — 모든 프로젝트

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 관련 | [PRD-001 제품 개요와 비전](../product/prd-001-product-overview.md) | 제품 이름과 공간·도구 분류 개념의 출발점 |
| 관련 | [PRD-002 요구사항과 범위](../product/prd-002-requirements-scope.md) | 요구사항에 등장하는 도메인 명사의 출처 |
| 관련 | [PRD-005 기능 명세](../product/prd-005-functional-spec.md) | 기능별 입력·처리에서 이 문서의 표준 용어를 사용한다 |
| 관련 | [OPS-002 문서 인덱스](ops-002-document-index.md) | 프로젝트 전체 문서 목록 |

## 도메인 용어 [필수]

| 용어 | 정의 | 영문 표기 | 코드 식별자 | 동의어 | 금지어 | 관련 문서 |
|---|---|---|---|---|---|---|
| 사용자 | `my-space` 워크스페이스의 실제 데이터를 소유하고 일상·업무에서 사용하는 주 사용자 본인 | User | `user` | 주 사용자, 본인 | 고객, 판매자, 팀원 — 상용 다중 사용자 서비스 용어는 쓰지 않는다 | [PRD-001](../product/prd-001-product-overview.md), [PRD-003](../product/prd-003-user-stories.md) |
| 외부 방문자 | 외부 접속이 열려 있을 때 전용 테스트 계정으로 로그인해 샘플 데이터와 도구를 체험하는 이용자 | Visitor | `visitor` / `testAccount` | 테스트 계정 이용자 | 협업 멤버 — 주 사용자와 데이터를 공유하지 않는다 | [PRD-001](../product/prd-001-product-overview.md), [PRD-003](../product/prd-003-user-stories.md) US-024 |
| 공간 | 사용할수록 데이터가 백엔드 DB에 쌓이고 반복해서 돌아와 관리·열람하는 기능 영역 (노트, 피드, 관리, AI) | Space | `space` | 워크스페이스 | 유틸(공간을 가리킬 때) — 일회성 도구와 혼동된다 | [PRD-001](../product/prd-001-product-overview.md), [PRD-002](../product/prd-002-requirements-scope.md) |
| 도구 | 입력을 넣으면 즉시 변환·계산·파싱 결과가 나오고 끝나는 일회성 기능 영역 (문서, 이미지, 개발, 계산) | Tool | `tool` | 유틸리티 | 공간(도구를 가리킬 때) | [PRD-001](../product/prd-001-product-overview.md), [PRD-002](../product/prd-002-requirements-scope.md) |
| 노트 | 마크다운 문서를 폴더·태그·버전 이력과 함께 관리하는 공간 (`공간 › 노트`) | Note | `note` (또는 기존 코드 `markdownDoc`) | 마크다운 문서 | 블로그 포스트 — 외부 공개 퍼블리싱 기능이 아니다 | [PRD-002](../product/prd-002-requirements-scope.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 리비전 | 노트를 저장·수정할 때마다 누적되는 버전 스냅샷. Diff 비교와 복원의 단위다 | Revision | `revision` | 버전 이력 | 커밋(Git 용어와 혼동) | [PRD-005](../product/prd-005-functional-spec.md) |
| 피드 | 기술 블로그·뉴스의 RSS/Atom 구독원과 수집된 글 목록을 다루는 공간 (`공간 › 피드`) | Feed | `feed` | RSS 피드 | 뉴스레터(이메일 구독과 혼동) | [PRD-002](../product/prd-002-requirements-scope.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 피드 글 | RSS/Atom 피드에서 수집되어 중복 제거된 개별 아티클 | Feed item | `feedItem` | 아티클, 포스트 | — | [PRD-005](../product/prd-005-functional-spec.md) |
| 관심 키워드 | 피드 글 제목·본문에 포함되면 해당 글을 목록 최상단에 우선 노출시키는 규칙 단어 | Priority keyword | `priorityKeyword` | 포함 키워드 | — | [PRD-003](../product/prd-003-user-stories.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 제외 키워드 | 피드 글 제목·본문에 포함되면 해당 글을 기본 목록에서 숨기는 규칙 단어 (관심 키워드보다 우선 적용) | Excluded keyword | `excludedKeyword` | 차단 키워드 | — | [PRD-003](../product/prd-003-user-stories.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 관리 공간 | 놓치면 곤란한 날짜(결제·만료·기념일)를 모아 관리하는 공간 (`공간 › 관리`) | Management space | `management` | 만료·갱신 관리 | 어드민(시스템 관리자 화면과 혼동) | [PRD-002](../product/prd-002-requirements-scope.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 공통 날짜 모델 | 관리 공간의 날짜 기반 기능들이 공유하는 `기준 날짜 + 주기 + 알림` 데이터 모델. `정기 결제`, `만료`, `단일 날짜` 3유형으로 나뉜다 | Schedule item | `scheduleItem` | 날짜 모델 | — | [PRD-002](../product/prd-002-requirements-scope.md), [PRD-005](../product/prd-005-functional-spec.md) |
| 정기 결제 | 시작일과 고정 주기로 다음 결제일을 계산하고 월 합계를 산출하는 날짜 유형 (구독 서비스 등) | Recurring payment | `recurringPayment` (유형값 `RECURRING_PAYMENT`) | 구독 결제 | — | [PRD-005](../product/prd-005-functional-spec.md) FN-006 |
| 고정 만료일 | 정해진 만료일을 추적하고 연장·갱신 시 새 만료일을 입력받는 날짜 유형 (도메인, 인증서, 여권, 보험 등) | Expiration | `expiration` (유형값 `EXPIRATION`) | 만료 일정 | — | [PRD-005](../product/prd-005-functional-spec.md) FN-007 |
| 단일 날짜 | 특정 고정 날짜(매년 반복 선택 가능)의 남은 일수(`D-N`) 또는 경과 일수(`D+N`)를 추적하는 날짜 유형 | Single date | `singleDate` (유형값 `SINGLE_DATE`) | D-day, 기념일 | — | [PRD-005](../product/prd-005-functional-spec.md) FN-008 |

## 기술 용어 [선택]

| 용어 | 정의 | 영문 표기 | 이 프로젝트에서의 의미 | 혼동하기 쉬운 말 | 관련 문서 |
|---|---|---|---|---|---|
| 누락 스케줄 보정 (Catch-up) | 중단되었던 스케줄 작업을 재기동 시 소급 실행하는 처리 | Catch-up execution | 홈랩 서버가 꺼져 있다가 다시 부팅될 때 DB의 `lastRunAt`과 현재 시각을 비교해 꺼져 있던 동안 지나간 만료 판별과 RSS 수집을 즉시 실행하는 메커니즘 | 고정 시각 크론(서버가 꺼져 있으면 누락됨) | [PRD-004](../product/prd-004-user-journeys.md), [PRD-005](../product/prd-005-functional-spec.md) FN-009 |
| 하이브리드 PDF 처리 | PDF 작업 성격에 따라 브라우저와 백엔드를 나누어 쓰는 구조 | Hybrid PDF processing | 비암호화 일반 PDF의 정보 확인·단순 병합/분할은 브라우저에서 처리하고, 순수 JS로 복호화가 불가능한 암호화 PDF 해제·조작은 백엔드 네이티브 `qpdf`로 처리하는 방식 | — | [PRD-004](../product/prd-004-user-journeys.md), [PRD-005](../product/prd-005-functional-spec.md) FN-011 |
| 원문 본문 추출 | 웹페이지 HTML에서 광고·메뉴를 걷어내고 본문 아티클만 추출하는 기술 | Readability extraction | RSS 피드가 짧은 발췌만 제공할 때 Mozilla Readability 알고리즘으로 원문 본문을 추출해 리더 뷰에 보여주는 처리 | — | [PRD-005](../product/prd-005-functional-spec.md) FN-004 |
| 테스트 계정 격리 | 체험용 계정과 실제 사용자 계정의 데이터 접근 범위를 분리하는 구조 | Test account isolation | 외부 방문자가 사용하는 테스트 계정 세션이 주 사용자의 개인 데이터(노트, 피드, 관리, AI 이력)를 조회·변경할 수 없도록 계정 스코프를 분리하는 처리 | — | [PRD-005](../product/prd-005-functional-spec.md) FN-025, [PRD-006](../product/prd-006-nfr.md) NFR-006 |

## 약어 [선택]

| 약어 | 풀어 쓴 말 | 뜻 | 관련 용어 |
|---|---|---|---|
| DBML | Database Markup Language | 데이터베이스 스키마 정의용 마크업 언어 | 도구 › 개발 |
| ERD | Entity-Relationship Diagram | 테이블 간 개체-관계 다이어그램 | DBML |
| EXIF | Exchangeable Image File Format | 디지털 사진의 촬영 기기·노출·GPS 위치 메타데이터 규격 | 도구 › 이미지 |
| AST | Abstract Syntax Tree | 정규식·cron 표현식을 파싱해 계층 구조로 표현한 추상 구문 트리 | 도구 › 개발 |
| PTT | Push-to-Talk | 스페이스바 등을 누르고 있는 동안만 음성을 수음하는 발화 방식 | 공간 › AI |
| PWA | Progressive Web App | Service Worker 캐싱을 통해 모바일 설치 및 오프라인 실행을 지원하는 웹앱 기술 | 단가 비교기, NFR-005 |

## 코드 식별자 표기 [필수]

| 계층 | 표기 형식 | 기본 식별자에서 바꾸는 방법 | 기준 문서 |
|---|---|---|---|
| TypeScript 변수·함수·API JSON 필드 | `camelCase` | `scheduleItem`, `priorityKeyword`, `lastRunAt` | 프론트엔드·백엔드 코드 컨벤션 |
| TypeScript 타입·인터페이스·클래스·React 컴포넌트 | `PascalCase` | `ScheduleItem`, `PriorityKeyword`, `PdfToolsPage` | 프론트엔드·백엔드 코드 컨벤션 |
| 도메인 상태·유형 상수(Enum) | `UPPER_SNAKE_CASE` | `RECURRING_PAYMENT`, `EXPIRATION`, `SINGLE_DATE` | 백엔드 도메인 모델 |
| URL 라우트 및 문서 파일명 | `kebab-case` | `/tools/unit-price`, `prd-001-product-overview.md` | `AGENTS.md` |

## 검토 체크리스트

- [x] 모든 도메인 용어에 한 문장 정의와 코드 식별자가 있다
- [x] 한 개념에 표준 용어가 하나뿐이고 혼동되는 표현은 동의어·금지어로 구분했다
- [x] 처음 나오는 약어가 약어 표에 정리되어 있다

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-01 | 1.0.0 | 사용자 검토 완료에 따라 approved(승인) 상태로 확정 | Antigravity (사용자 승인 완료) |
| 2026-10-31 | 0.1.0 | my-space v2 용어집 최초 작성 | Antigravity (사용자 검토 대기) |
| 2026-10-31 | 0.1.1 | 순차 문서 번호(`PRD-001`~`PRD-007`) 링크 반영 및 테스트 계정·외부 방문자 용어 정리 | Antigravity (사용자 검토 대기) |
| 2026-11-01 | 0.1.2 | 요구사항·스토리·기능 번호 순차 재조정(`US-024`, `FN-025`) 반영 | Antigravity (사용자 검토 대기) |
