# F08: 관리자

## 1. Feature Overview

**Purpose**: 플랫폼 운영자가 회원, 콘텐츠, 결제, 정산 요청을 관리하고 전체 플랫폼 운영 현황을 파악할 수 있는 관리자 페이지 및 대시보드 제공.

**Scope Boundary**:
- IN: 회원 조회/제재, 콘텐츠 숨김/삭제, 결제 내역 조회, 정산 요청 승인/거절, 대시보드 통계
- OUT: 신고 관리 기능 (별도 feature), 실시간 알림, 정산 실제 이체 처리 (외부 시스템)

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | Admin | 회원 관리 | 회원 목록 조회 → 검색/필터 적용 → 회원 상세 확인 → 제재 적용 |
| UF-02 | Admin | 콘텐츠 관리 | 콘텐츠 목록 조회 → 유해 콘텐츠 선택 → 숨김/삭제 처리 |
| UF-03 | Admin | 결제 조회 | 결제 목록 조회 → 날짜 필터 적용 → 결제 상세 확인 |
| UF-04 | Admin | 정산 승인 | 정산 요청 목록 조회 → 요청 상세 확인 → 승인/거절 결정 |
| UF-05 | Admin | 대시보드 확인 | 대시보드 접속 → 주요 지표(회원/작품/거래) 확인 → 가입 추이 그래프 확인 |

Source: user_stories.md (US-022, US-023, US-024, US-025, US-026, US-027, US-028)

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| AdminDashboard | /admin | 플랫폼 운영 현황 개요 (통계, 지표, 추이) |
| UserManagement | /admin/users | 회원 목록 조회, 검색, 제재 |
| ContentManagement | /admin/contents | 작품/에피소드 관리, 숨김/삭제 |
| PaymentManagement | /admin/payments | Ink 충전 결제 내역 조회 |
| SettlementManagement | /admin/settlements | 출금 요청 목록 조회 및 승인/거절 |

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| User | id, nickname, email, role, status, createdAt | → Sanction (1:N) |
| Sanction | id, userId, adminId, reason, type, startAt, endAt | → User (N:1) |
| Story | id, creatorId, title, status, totalBounty | → Episode (1:N) |
| Episode | id, storyId, title, status, totalBounty | → Story (N:1) |
| Payment | id, userId, inkAmount, paymentAmount, paymentMethod, status, createdAt | → User (N:1) |
| WithdrawalRequest | id, creatorId, amount, bankName, status, adminNote, createdAt, processedAt | → User (N:1) |

Source: conceptual_model.json

## 5. Component Strategy

**Pages**:
- `AdminDashboard (task-075)` - 대시보드 통계 및 주요 지표 표시
- `UserManagement (task-077)` - 회원 리스트 테이블 및 검색/필터
- `ContentManagement (task-080)` - 작품/에피소드 리스트 및 관리 액션
- `PaymentManagement (task-083)` - 결제 내역 리스트 및 필터
- `SettlementManagement (task-085)` - 정산 요청 리스트 및 승인/거절

**Components**:
- `DashboardStats (task-076)` - 통계 카드 및 차트 렌더링
- `UserTable (task-078)` - 회원 테이블 with 페이지네이션
- `SanctionModal (task-079)` - 제재 생성 모달 (사유 입력)
- `ContentTable (task-081)` - 콘텐츠 테이블 with 액션 버튼
- `DeleteConfirmModal (task-082)` - 삭제 확인 모달
- `PaymentTable (task-084)` - 결제 테이블 with 필터
- `SettlementTable (task-086)` - 정산 요청 테이블
- `SettlementDetailModal (task-087)` - 정산 상세 및 승인/거절 모달

**Services**:
- `AdminService (task-088)` - 관리자 전용 API 호출 (통합)

**State**:
- TanStack Query for server state (lists, filters, pagination)
- Local state for modals and form inputs
- No global state required (read-only operations mostly)

Source: logical_architecture.md

## 6. Task Mapping

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-075 | Admin Dashboard Page UI | 대시보드 페이지 레이아웃 | app/(admin)/admin/page.tsx |
| task-076 | DashboardStats 컴포넌트 | 통계 카드/차트 UI | components/admin/DashboardStats.tsx |
| task-077 | User Management Page UI | 회원 관리 페이지 레이아웃 | app/(admin)/admin/users/page.tsx |
| task-078 | UserTable 컴포넌트 | 회원 테이블 렌더링 | components/admin/UserTable.tsx |
| task-079 | SanctionModal 컴포넌트 | 제재 모달 (사유 입력) | components/admin/SanctionModal.tsx |
| task-080 | Content Management Page UI | 콘텐츠 관리 페이지 레이아웃 | app/(admin)/admin/contents/page.tsx |
| task-081 | ContentTable 컴포넌트 | 콘텐츠 테이블 렌더링 | components/admin/ContentTable.tsx |
| task-082 | DeleteConfirmModal 컴포넌트 | 삭제 확인 모달 | components/admin/DeleteConfirmModal.tsx |
| task-083 | Payment Management Page UI | 결제 관리 페이지 레이아웃 | app/(admin)/admin/payments/page.tsx |
| task-084 | PaymentTable 컴포넌트 | 결제 테이블 렌더링 | components/admin/PaymentTable.tsx |
| task-085 | Settlement Management Page UI | 정산 관리 페이지 레이아웃 | app/(admin)/admin/settlements/page.tsx |
| task-086 | SettlementTable 컴포넌트 | 정산 요청 테이블 렌더링 | components/admin/SettlementTable.tsx |
| task-087 | SettlementDetailModal 컴포넌트 | 정산 상세/승인 모달 | components/admin/SettlementDetailModal.tsx |
| task-088 | Admin Service (Mock API) | Mock API service layer | services/adminService.ts |

Source: tasks/tasks.json

## 7. Design & Implementation Notes

**Design Principles** (from CLAUDE-FE-DEV.md):
- Monochrome design: blacks, whites, grays only
- No colors except semantic states (success, error)
- Images as gray placeholder boxes (bg-muted)
- Typography-driven hierarchy

**Data Fetching Pattern**:
- Use TanStack Query hooks for all server data
- Service layer (adminService.ts) returns mock data for now
- Pagination handled by service layer (offset/limit pattern)

**Access Control**:
- Pages under /admin route protected by (admin) layout
- Layout checks user.role === 'ADMIN' before rendering
- Redirect to /home if not authorized

**Modal Management**:
- Use controlled modals with local state (isOpen)
- Pass callbacks for submit actions (onSanction, onApprove, onReject, onDelete)
- Close on successful action completion

**Table Patterns**:
- All tables use consistent shadcn/ui Table component
- Pagination: 50 items per page for admin pages
- Search/filter: debounced input (300ms delay)
- Loading states: Skeleton loaders during fetch

**Component Dependencies**:
- Pages depend on completed layouts (task-011)
- Tables depend on shadcn/ui components (task-003)
- Services depend on type definitions (task-004)

## 8. Open Questions

- ❓ Should admin actions (sanction, delete) require additional confirmation beyond modal?
- ❓ Export functionality needed for payment/settlement reports?
- ❓ Audit log tracking for admin actions?
- ❓ Multi-admin role permissions (super admin vs. moderator)?

**Resolution**: Implement basic functionality first. Advanced features (audit log, export) can be P2.
