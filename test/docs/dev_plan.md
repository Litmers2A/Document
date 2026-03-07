# Frontend Development Plan - 쟈근친구들 V2 (Conture)

> Generated from: ia_structure.md, logical_architecture.md, file_structure.md
Date: 2026-01-06

---

## Overview

### Project Summary

- **Framework**: Next.js 14 (App Router)

- **Styling**: Tailwind CSS + shadcn/ui

- **State Management**: Zustand + TanStack Query

- **Form Handling**: React Hook Form + Zod

### Scope

- **In Scope**: UI 컴포넌트, 페이지, 클라이언트 로직, Mock 데이터 기반 개발

- **Out of Scope**: API 엔드포인트, 데이터베이스, 서버 사이드 로직

---

## Development Phases

### Phase A: Project Setup (프로젝트 설정)

1. $1
2. $1
3. $1
4. $1
5. $1
6. $1

### Phase B: Layout & Navigation (레이아웃)

1. $1
2. $1
3. $1
4. $1
5. $1
6. $1
7. $1

### Phase C: Authentication (인증)

1. $1
2. $1
3. $1
4. $1
5. $1

### Phase D: Reader Core (독자 핵심)

1. $1
2. $1
3. $1
4. $1
5. $1

### Phase E: Wallet & Payment (지갑/결제)

1. $1
2. $1
3. $1
4. $1

### Phase F: Intervention (제안/후원)

1. $1
2. $1
3. $1
4. $1
5. $1

### Phase G: Creator Studio (작가 스튜디오)

1. $1
2. $1
3. $1
4. $1
5. $1
6. $1

### Phase H: Admin (관리자)

1. $1
2. $1
3. $1
4. $1
5. $1

---

## Task Breakdown

### Phase A: Project Setup

Task IDTitlePriorityDependenciesFilestask-001Next.js 14 프로젝트 초기화 및 기본 설정high-next.config.js, package.jsontask-002Tailwind CSS 설정 및 디자인 토큰 정의hightask-001tailwind.config.ts, app/globals.css**Typography 위계 강화 (2026-01-08)**: globals.css에 size + weight 조합 정의. Page Title (font-bold), Section Header (font-semibold), Table Header (font-medium), Body (font-normal) 시스템 확립task-003shadcn/ui 설치 및 기본 UI 컴포넌트 설정hightask-002components/ui/*.tsxtask-004TypeScript 타입 정의 (Conceptual Model 기반)hightask-001types/*.tstask-005Zustand 스토어 설정 (Auth, UI)hightask-004stores/*.tstask-006TanStack Query Provider 설정hightask-001app/providers.tsx

### Phase B: Layout & Navigation

Task IDTitlePriorityDependenciesFilestask-007Root Layout 및 Provider 통합hightask-005, task-006app/layout.tsxtask-008Public Layout (비인증 영역)hightask-007app/(public)/layout.tsxtask-009Auth Layout (인증 체크)hightask-007app/(auth)/layout.tsxtask-010Creator Layout (작가 전용)mediumtask-009app/(creator)/layout.tsxtask-011Admin Layout (관리자 전용)mediumtask-009app/(admin)/layout.tsxtask-012Bottom Navigation 컴포넌트hightask-003components/layout/BottomNav.tsxtask-013Side Navigation 컴포넌트 (Creator/Admin)mediumtask-003components/layout/SideNav.tsxtask-014Header 컴포넌트hightask-003components/layout/Header.tsx

### Phase C: Authentication

Task IDTitlePriorityDependenciesFilestask-015Login Page UIhightask-008app/(public)/login/page.tsxtask-016LoginForm 컴포넌트hightask-003components/auth/LoginForm.tsxtask-017SocialLoginButtons 컴포넌트hightask-003components/auth/SocialLoginButtons.tsxtask-018Signup Page UIhightask-008app/(public)/signup/page.tsxtask-019SignupForm 컴포넌트hightask-003components/auth/SignupForm.tsxtask-020Terms Page UImediumtask-008app/(public)/terms/page.tsxtask-021ProfileSetup Page UIhightask-009app/(auth)/profile/setup/page.tsxtask-022ProfileSetupForm 컴포넌트hightask-003components/auth/ProfileSetupForm.tsxtask-023useAuth Hook 구현hightask-005hooks/useAuth.tstask-024Auth Service (Mock API)hightask-004services/authService.ts

### Phase D: Reader Core

Task IDTitlePriorityDependenciesFilestask-025Home Page UIhightask-009, task-012app/(auth)/home/page.tsxtask-026StoryBanner 컴포넌트hightask-003components/story/StoryBanner.tsxtask-027StoryCard 컴포넌트hightask-003components/story/StoryCard.tsxtask-028StoryList 컴포넌트hightask-027components/story/StoryList.tsxtask-029Explore Page UIhightask-009, task-028app/(auth)/explore/page.tsxtask-030Search Page UImediumtask-009, task-028app/(auth)/search/page.tsxtask-031Story Detail Page UIhightask-009app/(auth)/stories/[storyId]/page.tsxtask-032StoryDetail 컴포넌트hightask-027components/story/StoryDetail.tsxtask-033EpisodeCard 컴포넌트hightask-003components/episode/EpisodeCard.tsxtask-034EpisodeList 컴포넌트hightask-033components/episode/EpisodeList.tsxtask-035Episode Viewer Page UIhightask-009app/(auth)/stories/[storyId]/episodes/[episodeId]/page.tsxtask-036ContentRenderer 컴포넌트hightask-003components/episode/ContentRenderer.tsxtask-037ActionBar 컴포넌트hightask-003components/episode/ActionBar.tsxtask-038Story Service (Mock API)hightask-004services/storyService.tstask-039Episode Service (Mock API)hightask-004services/episodeService.ts

### Phase E: Wallet & Payment

Task IDTitlePriorityDependenciesFilestask-040MyPage UIhightask-009, task-012app/(auth)/mypage/page.tsxtask-041Wallet Page UIhightask-040app/(auth)/mypage/wallet/page.tsxtask-042BalanceCard 컴포넌트hightask-003components/wallet/BalanceCard.tsxtask-043TransactionList 컴포넌트hightask-003components/wallet/TransactionList.tsxtask-044Shop Page UIhightask-009app/(auth)/shop/page.tsxtask-045InkShop 컴포넌트hightask-003components/wallet/InkShop.tsxtask-046Wallet Service (Mock API)hightask-004services/walletService.tstask-047Transaction Service (Mock API)mediumtask-004services/transactionService.tstask-048Payment Service (Mock API)hightask-004services/paymentService.ts

### Phase F: Intervention

Task IDTitlePriorityDependenciesFilestask-049ProposalCard 컴포넌트hightask-003components/proposal/ProposalCard.tsxtask-050ProposalList 컴포넌트hightask-049components/proposal/ProposalList.tsxtask-051ProposalModal 컴포넌트hightask-003components/proposal/ProposalModal.tsxtask-052BackingModal 컴포넌트hightask-003components/proposal/BackingModal.tsxtask-053DirectBackingModal 컴포넌트hightask-003components/proposal/DirectBackingModal.tsxtask-054CreditSection 컴포넌트hightask-003components/episode/CreditSection.tsxtask-055Proposal Service (Mock API)hightask-004services/proposalService.tstask-056Backing Service (Mock API)hightask-004services/backingService.tstask-057useProposals Hook 구현hightask-055hooks/useProposals.tstask-058Episode Viewer 통합 (Proposal + Credit)hightask-035, task-050, task-054app/(auth)/stories/[storyId]/episodes/[episodeId]/page.tsx

### Phase G: Creator Studio

Task IDTitlePriorityDependenciesFilestask-059Studio Dashboard Page UIhightask-010, task-013app/(creator)/studio/page.tsxtask-060Episode Management Page UIhightask-059app/(creator)/studio/episodes/page.tsxtask-061CreatorEpisodeList 컴포넌트hightask-033components/episode/CreatorEpisodeList.tsxtask-062Episode Editor Page UIhightask-060app/(creator)/studio/episodes/[episodeId]/edit/page.tsxtask-063EpisodeEditor 컴포넌트hightask-003components/episode/EpisodeEditor.tsxtask-064ContentUploader 컴포넌트hightask-003components/episode/ContentUploader.tsxtask-065Proposal Management Page UIhightask-059app/(creator)/studio/proposals/page.tsxtask-066ProposalManagementList 컴포넌트hightask-049components/proposal/ProposalManagementList.tsxtask-067DealConfirmModal 컴포넌트hightask-003components/proposal/DealConfirmModal.tsxtask-068DropConfirmModal 컴포넌트hightask-003components/proposal/DropConfirmModal.tsxtask-069Settlement Page UIhightask-059app/(creator)/studio/settlements/page.tsxtask-070SettlementSummary 컴포넌트hightask-003components/settlement/SettlementSummary.tsxtask-071SettlementList 컴포넌트hightask-003components/settlement/SettlementList.tsxtask-072WithdrawalModal 컴포넌트hightask-003components/settlement/WithdrawalModal.tsxtask-073Episode Management Service (Mock API)hightask-039services/episodeService.tstask-074Settlement Service (Mock API)hightask-004services/settlementService.ts

### Phase H: Admin

Task IDTitlePriorityDependenciesFilestask-075Admin Dashboard Page UImediumtask-011, task-013app/(admin)/admin/page.tsxtask-076DashboardStats 컴포넌트mediumtask-003components/admin/DashboardStats.tsxtask-077User Management Page UImediumtask-075app/(admin)/admin/users/page.tsxtask-078UserTable 컴포넌트mediumtask-003components/admin/UserTable.tsxtask-079SanctionModal 컴포넌트mediumtask-003components/admin/SanctionModal.tsxtask-080Content Management Page UImediumtask-075app/(admin)/admin/contents/page.tsxtask-081ContentTable 컴포넌트mediumtask-003components/admin/ContentTable.tsxtask-082DeleteConfirmModal 컴포넌트mediumtask-003components/admin/DeleteConfirmModal.tsxtask-083Payment Management Page UImediumtask-075app/(admin)/admin/payments/page.tsxtask-084PaymentTable 컴포넌트mediumtask-003components/admin/PaymentTable.tsxtask-085Settlement Management Page UImediumtask-075app/(admin)/admin/settlements/page.tsxtask-086SettlementTable 컴포넌트mediumtask-003components/admin/SettlementTable.tsxtask-087SettlementDetailModal 컴포넌트mediumtask-003components/admin/SettlementDetailModal.tsxtask-088Admin Service (Mock API)mediumtask-004services/adminService.ts

---

## Task Statistics

PhaseTotal TasksHigh PriorityMedium PriorityA. Project Setup660B. Layout & Navigation853C. Authentication1082D. Reader Core15141E. Wallet & Payment972F. Intervention10100G. Creator Studio16160H. Admin14014**Total886622**

---

## Recommended Execution Order

### Sprint 1: Foundation (task-001 ~ task-014)

- 프로젝트 설정

- 타입 정의

- 레이아웃 구조

### Sprint 2: Auth & Core Reader (task-015 ~ task-039)

- 인증 플로우

- 홈/탐색/검색

- 에피소드 뷰어 기본

### Sprint 3: Wallet & Intervention (task-040 ~ task-058)

- 지갑/결제

- 제안/지지/후원

- 크레딧 표시

### Sprint 4: Creator Studio (task-059 ~ task-074)

- 크리에이터 스튜디오

- 에피소드 관리

- 정산 관리

### Sprint 5: Admin (task-075 ~ task-088)

- 관리자 대시보드

- 회원/콘텐츠/결제/정산 관리

---

## Mock Data Strategy

각 서비스는 `services/mock/` 디렉토리에 Mock 데이터를 정의하고, 실제 API 연동 전까지 Mock을 사용합니다.

```
// services/mock/users.ts
export const mockUsers: User[] = [
  { id: '1', nickname: 'reader1', role: 'READER', ... },
  { id: '2', nickname: 'creator1', role: 'CREATOR', ... },
];
```

---

## 문서 정보

- **생성일:** 2026-01-06

- **기반 문서:** ia_structure.md, logical_architecture.md, file_structure.md