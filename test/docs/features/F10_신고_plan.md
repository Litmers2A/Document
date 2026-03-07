# F10: 신고 (Report)

## 1. Feature Overview

**Purpose**: 사용자가 부적절한 콘텐츠나 다른 사용자를 신고하고, 관리자가 해당 신고를 검토하여 적절한 조치를 취할 수 있는 기능.

**Scope Boundary**:
- IN: 사용자 신고 제출, 내 신고 내역 조회, 관리자 신고 목록 조회, 관리자 신고 처리 (기각/경고/정지)
- OUT: 신고 기반 자동 콘텐츠 숨김, 신고 통계 대시보드, 신고 알림 시스템

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | 독자/작가 | 신고 제출 | 콘텐츠 확인 → 신고 버튼 클릭 → 유형 선택 (6가지) → 사유 입력 (10자+) → 제출 → 완료 메시지 |
| UF-02 | 독자/작가 | 신고 내역 확인 | 마이페이지 → 신고 내역 → 리스트 확인 → 항목 클릭 → 상세/피드백 확인 |
| UF-03 | 관리자 | 신고 목록 조회 | 관리자 → 신고 관리 → 필터/정렬 → 리스트 확인 |
| UF-04 | 관리자 | 신고 처리 | 신고 상세 → 내용 검토 → 과거 이력 확인 → 처리 선택 (기각/경고/정지) → 메모 입력 → 완료 |

Source: user_stories.md (US-029, US-030, US-031, US-032)

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| MyReports | `/mypage/reports` | 내가 신고한 내역 및 처리 상태 조회 |
| ReportManagement | `/admin/reports` | 관리자 신고 목록 조회 및 처리 |

**Modals**:
| Modal | Trigger Location | Purpose |
|-------|------------------|---------|
| ReportModal | StoryDetail, ProposalCard, EpisodeViewer | 신고 제출 |
| ReportDetailModal | MyReports (user), ReportManagement (admin) | 신고 상세 확인 및 처리 |

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| Report | id, reporterId, reportedUserId, type (6 enum), reason, referenceType, referenceId, status (4 enum), adminId, adminFeedback, adminNote, sanctionId | → User (reporter), → User (reported), → User (admin), → Sanction |
| Sanction | id, userId, adminId, reportId, reason, type (WARNING/SUSPENSION/PERMANENT_BAN), startAt, endAt | → User, → Report |

**Report Types** (type enum):
- `SPAM` - 스팸
- `ABUSE` - 욕설/비방
- `INAPPROPRIATE_CONTENT` - 부적절한 콘텐츠
- `COPYRIGHT` - 저작권 침해
- `MISINFORMATION` - 허위 정보
- `OTHER` - 기타

**Report Status** (status enum):
- `PENDING` - 접수됨
- `REVIEWING` - 검토 중
- `RESOLVED` - 처리 완료
- `DISMISSED` - 기각

**Reference Types** (referenceType enum):
- `STORY` - 스토리 신고
- `PROPOSAL` - 제안 작성자 신고
- `BACKING` - 후원자 신고
- `COMMENT` - 댓글 신고
- `PROFILE` - 프로필 신고

Source: conceptual_model.json

## 5. Component Strategy

**Services**:
- `ReportService` - 신고 생성, 내 신고 내역 조회
- `ReportAdminService` - 관리자 신고 목록 조회, 신고 처리, Sanction 연동

**Pages**:
- `MyReportsPage` - 사용자 신고 내역 페이지
- `AdminReportsPage` - 관리자 신고 관리 페이지

**Components**:
- `ReportModal` - 신고 제출 모달 (유형 선택, 사유 입력)
- `MyReportList` - 사용자 신고 내역 리스트
- `ReportTable` - 관리자 신고 목록 테이블
- `ReportDetailModal` - 신고 상세 모달 (사용자/관리자 모드)

**Hooks**:
- `useReportSubmit` - 신고 제출 mutation
- `useMyReports` - 내 신고 내역 query
- `useAdminReports` - 관리자 신고 목록 query (필터, 페이지네이션)

**State Considerations**:
- 신고 모달은 targetType, targetId, targetUserId를 props로 받음
- 관리자 처리 시 SanctionService와 연동하여 제재 생성
- 신고 처리 후 리스트 refetch 필요

Source: logical_architecture.md

## 6. Task Mapping

### Types & Services (기반 레이어)

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-109 | Report 타입 정의 | Report 관련 TypeScript 타입 | `types/report.ts`, `types/index.ts` |
| task-110 | Report Service (Mock API) | createReport, getMyReports, getReports, processReport | `services/reportService.ts` |

### Hooks (비즈니스 로직 레이어)

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-111 | useReportSubmit Hook | 신고 제출 mutation | `hooks/useReportSubmit.ts` |
| task-115 | useMyReports Hook | 내 신고 내역 조회 | `hooks/useMyReports.ts` |
| task-119 | useAdminReports Hook | 관리자 신고 목록 조회 (필터, 페이지네이션) | `hooks/useAdminReports.ts` |

### Components (UI 레이어)

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-112 | ReportModal 컴포넌트 | 신고 제출 모달 UI | `components/report/ReportModal.tsx` |
| task-116 | MyReportList 컴포넌트 | 사용자 신고 내역 리스트 | `components/report/MyReportList.tsx` |
| task-120 | ReportTable 컴포넌트 | 관리자 신고 목록 테이블 | `components/admin/ReportTable.tsx` |
| task-121 | ReportDetailModal 컴포넌트 | 신고 상세 모달 (Sanction 연동) | `components/admin/ReportDetailModal.tsx` |

### Integration (통합 레이어)

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-113 | StoryDetail 신고 버튼 추가 | 스토리 상세에서 신고 진입점 | `app/(auth)/stories/[storyId]/page.tsx` |
| task-114 | ProposalCard 신고 버튼 추가 | 제안 카드에서 신고 진입점 | `components/proposal/ProposalCard.tsx` |
| task-117 | MyPage 신고내역 메뉴 추가 | 마이페이지 → 신고 내역 링크 | `app/(auth)/mypage/page.tsx` |

### Pages (페이지 레이어)

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-118 | MyReports Page UI | 내 신고 내역 페이지 | `app/(auth)/mypage/reports/page.tsx` |
| task-122 | Admin Reports Page UI | 관리자 신고 관리 페이지 | `app/(admin)/admin/reports/page.tsx` |

Source: tasks/tasks.json

## 7. Dependency Graph

```
task-109 (Types)
    ↓
task-110 (Service)
    ↓
┌───────┴───────┐
↓               ↓
task-111        task-115        task-119
(useReportSubmit) (useMyReports) (useAdminReports)
↓                    ↓               ↓
task-112 ←───────────┘               ↓
(ReportModal)                        ↓
↓                                    ↓
┌─────┬──────┐                 ┌─────┴─────┐
↓     ↓      ↓                 ↓           ↓
task-113  task-114         task-120   task-121
(Story)   (Proposal)       (Table)    (DetailModal)
                               ↓           ↓
task-116 ←─ task-115           └─────┬─────┘
(MyReportList)                       ↓
    ↓                            task-122
task-117 ←─ task-116           (Admin Page)
(MyPage Menu)
    ↓
task-118
(MyReports Page)
```

## 8. Implementation Notes

### Permission Logic (US-029)
- **독자**: 스토리/Proposal 작성자 신고 가능 (항상)
- **작가**: 본인 스토리에 달린 Proposal 작성자만 신고 가능

### Validation Rules
- 신고 사유 최소 10자 이상 필수
- 동일 대상 중복 신고 방지 (API에서 체크)

### Sanction Integration (US-032)
- 신고 처리 시 경고/정지 선택 → SanctionService.createSanction() 호출
- Report.sanctionId에 생성된 Sanction ID 연동

### UI Patterns
- ReportModal: 기존 모달 패턴 (Dialog 컴포넌트 활용)
- ReportTable: ContentTable 패턴 참고
- Status badges: 접수됨(gray), 검토 중(yellow), 처리 완료(green), 기각(red)

## 9. Open Questions

None - 모든 요구사항이 user_stories.md 및 logical_architecture.md에서 명확히 정의됨.
