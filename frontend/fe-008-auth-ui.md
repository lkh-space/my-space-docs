---
id: FE-008
title: 인증·권한에 따른 UI
part: frontend
tier: conditional
condition: UI·인증
status: review
version: 0.1.1
owner: "@bob (Keunhyeok Lim)"
last_reviewed: 2026-11-22
depends_on: [FE-001, ARC-007, ADR-0007]
related: [FE-003, FE-005, PRD-001]
superseded_by: null
traces: [REQ-026, REQ-030, NFR-006]
---

# FE-008 인증·권한에 따른 UI

## 이 문서는

- **목적**: Authelia ForwardAuth 게이트웨이 인증을 기반으로, 주 사용자(`owner`)와 외부 방문자 체험 계정(`visitor`) 간의 UI 차이, 세션 초기화 흐름, 방문자 모드 시각 피드백 배너 및 인메모리 샌드박스 초기화 인터랙션을 정의한다.
- **대상 독자**: 프론트엔드 개발자, 보안 담당자, AI 에이전트
- **작성 시점**: 프론트엔드 기술 구조([FE-001](fe-001-tech-stack.md)) 및 라우팅([FE-003](fe-003-routing-pages.md)) 작성 직후.
- **등급**: 조건부 — UI·인증 (게이트웨이 인증 및 방문자 모드가 존재하는 프로젝트)

## 선행 문서와 관련 문서

| 관계 | 문서 | 이유 |
|---|---|---|
| 선행 | [FE-001 프론트엔드 기술 구조](fe-001-tech-stack.md) | 클라이언트 실행 환경 및 네트워크 요청 규격 |
| 선행 | [ARC-007 보안 아키텍처](../architecture/arc-007-security-architecture.md) | ForwardAuth 인증 헤더 및 주 사용자/방문자 격리 원칙 |
| 선행 | [ADR-0007 ForwardAuth 및 샌드박스](../architecture/adr/adr-0007-visitor-browser-state.md) | 인증 위임 및 방문자 인메모리 샌드박스 결정 |
| 관련 | [PRD-001 제품 개요와 비전](../product/prd-001-product-overview.md) | 외부 방문자 체험 계정의 목적과 범위 |
| 관련 | [FE-003 라우팅과 페이지 구성](fe-003-routing-pages.md) | 체험 안내 화면(`/guest`) 라우트 |
| 관련 | [FE-005 상태 관리](fe-005-state-management.md) | 방문자 샌드박스 상태(`GuestSandboxStore`) 관리 |

## 인증 상태 확인과 방문자 진입

백엔드 인증 기준은 [BE-004](../backend/be-004-authn-authz.md), 신원 응답은 [OpenAPI](../backend/openapi.yaml)의 `getCurrentUser`다. 앱 진입 시 `/api/v1/auth/me`를 조회한다. 401은 미인증이므로 로그인 안내로 이동하며 방문자로 자동 전환하지 않는다. 성공 응답의 `role=GUEST`는 인증된 테스트 계정이며 체험 UI를 사용한다. OWNER는 개인 공간을 사용한다. `/guest` 진입 경로가 있더라도 인증·서버 인가를 우회하지 않는다.

클라이언트의 owner/visitor 표현은 서버 OWNER/GUEST와 명시적으로 매핑한다. 방문자는 samples API로 정적 샘플을 읽고 변경은 브라우저 메모리에서 처리한다. 실제 외부 AI 호출·RAG·개인 공간 쓰기는 서버에서 거부한다. 프로필은 검증된 응답을 사용하고 브라우저가 Remote-User 헤더를 만들어 전송하지 않는다.

## 사용자 역할별 UI 차이 [필수]

| UI 요소 | 주 사용자 (`owner`) | 외부 방문자 체험 모드 (`visitor`) |
|---|---|---|
| 상단 글로벌 배너 | 미노출 (일반 화면) | **상단 노란색 알림 배너 상시 노출**: "현재 체험 모드입니다. 변경 사항은 브라우저 새로고침 시 초기화됩니다." |
| 데이터 저장 대상 | 홈랩 백엔드 영속 DB (`PostgreSQL`, `RustFS`) | 브라우저 인메모리 샌드박스 (`GuestSandboxStore`) |
| 상단 헤더 액션 | 알림함 아이콘, 사용자 프로필 | **'샘플 데이터 초기화' 버튼** 추가 노출, 알림함 아이콘 |
| 저장/삭제 시 토스트 | "저장이 완료되었습니다." | "체험 데이터가 임시 반영되었습니다. (서버 미저장)" |
| 백엔드 AI 모델 호출 | 실제 개인 AI 비서 (Gemini / Ollama) | 사전 정의된 시나리오 기반 모의 응답 또는 쿼터 제한 모의 스트리밍 |
| 민감 로컬 도구 | 정상 사용 (브라우저 로컬 완결) | 정상 사용 (브라우저 로컬 완결 - 동일) |

## 방문자 모드 안내 컴포넌트 (`GuestNoticeBar.tsx`)

상단 헤더 바로 아래에 얇은 배너 형태로 고정 노출되며, 방문자가 자신이 테스트 계정임을 명확히 인지하게 돕는다:

```typescript
// GuestNoticeBar 시각 규격
export const GuestNoticeBar = () => {
  const { isGuest, resetSandbox } = useAuthStore();
  if (!isGuest) return null;

  return (
    <aside 
      role="status" 
      aria-label="체험 모드 안내"
      style={{
        backgroundColor: '#FEF3C7',
        color: '#92400E',
        padding: '8px 16px',
        display: 'flex',
        alignItems: 'center',
        justifyContent: 'space-between',
        fontSize: '13px',
        borderBottom: '1px solid #FCD34D',
      }}
    >
      <div style={{ display: 'flex', alignItems: 'center', gap: '8px' }}>
        <span aria-hidden="true">💡</span>
        <span>외부 방문자 체험 모드로 둘러보는 중입니다. 입력한 데이터는 서버에 저장되지 않습니다.</span>
      </div>
      <button 
        type="button" 
        onClick={resetSandbox}
        style={{
          backgroundColor: '#FFFFFF',
          border: '1px solid #D97706',
          borderRadius: '4px',
          padding: '4px 8px',
          fontSize: '12px',
          fontWeight: 600,
          cursor: 'pointer',
        }}
      >
        샘플 데이터 초기화
      </button>
    </aside>
  );
};
```

## 세션 만료 및 인증 실패 흐름

- **주 사용자 세션 만료 (401 수신)**: API 요청 중 `401 Unauthorized`가 반환되면, 안내 모달("세션이 만료되었습니다. 다시 로그인해 주세요.")을 띄우고 Authelia 포털 URL로 리다이렉트한다.
- **인가 실패 (403 수신)**: 권한이 없는 자원에 접근 시 토스트 오류를 출력하고 이전 화면으로 복귀한다.

## 검토 체크리스트

- [x] 프론트엔드 내에 독립 로그인 폼을 두지 않고 Authelia ForwardAuth와 연동하도록 규정되어 있다
- [x] 주 사용자와 외부 방문자 체험 모드의 UI 분기점이 명확히 표로 기술되어 있다
- [x] 방문자 모드 상단 배너 및 '샘플 데이터 초기화' 인터랙션이 정의되어 있다
- [x] 방문자의 모든 수정 액션이 서버에 저장되지 않고 샌드박스로 가두어지는 흐름이 기술되어 있다
- [x] 세션 만료(`401`) 및 인가 실패(`403`)에 대한 처리 방안이 명시되어 있다

## 작성 예시

예시 없음 — 실무 프로젝트 문서 본문

## 변경 이력

| 날짜 | 버전 | 변경 내용 | 작성자 |
|---|---|---|---|
| 2026-11-12 | 0.1.0 | my-space v2 인증·권한에 따른 UI(FE-008) 최초 작성 | @bob (Keunhyeok Lim) |

| 2026-11-22 | 0.1.1 | BE-004와 신원 API·401 처리·방문자 판정 정합성 반영 | AI 에이전트 |
