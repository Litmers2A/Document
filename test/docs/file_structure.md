# File Structure Plan - 쟈근친구들 V2 (Conture)

> Generated from: ia_structure.md, conceptual_model.json
> Date: 2026-01-06

## Technology Stack

| Category | Technology | Version |
|----------|------------|---------|
| Framework | Next.js (App Router) | 14.x |
| Language | TypeScript | 5.x |
| Styling | Tailwind CSS | 3.x |
| State Management | Zustand | 4.x |
| Data Fetching | TanStack Query (React Query) | 5.x |
| Form Handling | React Hook Form + Zod | 7.x / 3.x |
| HTTP Client | Axios | 1.x |
| UI Components | Radix UI + shadcn/ui | - |
| Authentication | NextAuth.js | 4.x |
| Icons | Lucide React | - |

---

## Directory Structure

```
project-root/
├── app/                          # Next.js App Router
│   ├── (public)/                 # Public routes group
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Splash → redirect
│   │   ├── login/
│   │   │   └── page.tsx          # Login
│   │   ├── signup/
│   │   │   ├── page.tsx          # Signup [DEPRECATED - change-2026-02-04-1450]
│   │   │   └── complete/
│   │   │       └── page.tsx      # Signup Complete [DEPRECATED - change-2026-02-05-0918]
│   │   ├── onboarding/           # Onboarding Flow [NEW - change-2026-02-05-0918]
│   │   │   ├── page.tsx          # Onboarding entry (redirects to /nickname)
│   │   │   ├── nickname/
│   │   │   │   └── page.tsx      # Nickname Setup
│   │   │   └── phone/
│   │   │       └── page.tsx      # Phone Verification
│   │   ├── password-reset/       # Password Reset Flow (change-2026-01-14-1500)
│   │   │   ├── page.tsx          # Step 1: Phone Input
│   │   │   ├── verify/
│   │   │   │   └── page.tsx      # Step 2: Verification Code
│   │   │   └── new/
│   │   │       └── page.tsx      # Step 3: New Password
│   │   └── terms/
│   │       └── page.tsx          # Terms
│   │
│   ├── (auth)/                   # Authenticated routes group
│   │   ├── layout.tsx            # Auth check layout
│   │   ├── profile/
│   │   │   └── setup/
│   │   │       └── page.tsx      # Profile Setup
│   │   ├── mypage/
│   │   │   ├── page.tsx          # MyPage
│   │   │   ├── wallet/
│   │   │   │   └── page.tsx      # Wallet
│   │   │   ├── participation-history/  # Participation History [RENAMED from proposals - change-2026-02-04-1450]
│   │   │   │   ├── page.tsx      # Participation History List
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx  # Participation Detail (Timeline)
│   │   │   ├── settings/
│   │   │   │   └── page.tsx      # Settings
│   │   │   └── withdraw/
│   │   │       └── page.tsx      # Withdraw (change-2026-01-14-1500)
│   │   ├── home/
│   │   │   └── page.tsx          # Home
│   │   ├── explore/
│   │   │   └── page.tsx          # Explore
│   │   ├── search/
│   │   │   └── page.tsx          # Search Results
│   │   ├── recently-viewed/      # [NEW - change-2026-02-04-1450]
│   │   │   └── page.tsx          # Recently Viewed Works
│   │   ├── stories/
│   │   │   └── [storyId]/
│   │   │       ├── page.tsx      # Story Detail
│   │   │       └── episodes/
│   │   │           └── [episodeId]/
│   │   │               └── page.tsx  # Episode Viewer
│   │   ├── creators/
│   │   │   └── [creatorId]/
│   │   │       └── page.tsx      # Creator Detail
│   │   └── shop/
│   │       ├── page.tsx          # Ink Shop
│   │       └── payment/          # Payment flow (change-2026-01-13-1640)
│   │           ├── page.tsx      # Payment Page
│   │           └── complete/
│   │               └── page.tsx  # Payment Complete Page
│   │
│   ├── (creator)/                # Creator routes group
│   │   ├── layout.tsx            # Creator role check
│   │   └── studio/
│   │       ├── page.tsx          # Studio Dashboard
│   │       ├── episodes/
│   │       │   ├── page.tsx      # Episode Management
│   │       │   └── [episodeId]/
│   │       │       └── edit/
│   │       │           └── page.tsx  # Episode Editor
│   │       ├── proposals/
│   │       │   └── page.tsx      # Proposal Management
│       ├── settlement/        # Page 분리 (change-2026-01-21-1135)
│       │   └── page.tsx      # Settlement (보유 내역 전용)
│       └── settlement-history/    # NEW (change-2026-01-21-1135)
│           └── page.tsx      # Settlement History (정산 내역 전용)│   │
│   ├── (admin)/                  # Admin routes group
│   │   ├── layout.tsx            # Admin role check
│   │   └── admin/
│   │       ├── page.tsx          # Admin Dashboard
│   │       ├── users/
│   │       │   └── page.tsx      # User Management
│   │       ├── contents/
│   │       │   └── page.tsx      # Content Management
│   │       ├── payments/
│   │       │   └── page.tsx      # Payment Management
│   │       └── settlements/
│   │           └── page.tsx      # Settlement Management
│   │
│   ├── api/                      # API Routes
│   │   ├── auth/
│   │   │   └── [...nextauth]/
│   │   │       └── route.ts      # NextAuth handler
│   │   └── ...
│   │
│   ├── layout.tsx                # Root layout
│   ├── globals.css               # Global styles
│   └── providers.tsx             # Client providers
│
├── middleware.ts                 # Next.js middleware (onboarding guard, change-2026-02-05-0918)
│
├── components/                   # Shared components
│   ├── ui/                       # Base UI components (shadcn/ui)
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── dialog.tsx
│   │   ├── input.tsx
│   │   ├── modal.tsx
│   │   ├── table.tsx
│   │   ├── tabs.tsx
│   │   ├── toast.tsx
│   │   └── ...
│   │
│   ├── layout/                   # Layout components
│   │   ├── Header.tsx            # Generic header (back, close buttons)
│   │   ├── Footer.tsx
│   │   ├── TopNav.tsx            # Global top navigation (3-zone: hamburger+logo/search/account)
│   │   ├── SearchBar.tsx         # Search input with real-time popup + mobile icon mode
│   │   ├── SearchPopup.tsx       # Real-time search results popup (change-2026-01-09-1137)
│   │   ├── BottomNav.tsx         # Mobile bottom navigation (Reader)
│   │   ├── SideNav.tsx           # Desktop side navigation (Creator/Admin)
│   │   ├── ReaderSideNav.tsx     # Reader desktop side navigation (no logo, no shop tab)
│   │   ├── AdminLayout.tsx
│   │   └── CreatorLayout.tsx
│   │
│   ├── auth/                     # Auth components
│   │   ├── LoginForm.tsx              # [UPDATED - change-2026-02-04-1450: 소셜 전용]
│   │   ├── SignupForm.tsx             # [DEPRECATED - change-2026-02-04-1450]
│   │   ├── NicknameInput.tsx          # [DEPRECATED - onboarding으로 이동, change-2026-02-05-0918]
│   │   ├── SocialLoginButtons.tsx
│   │   ├── ProfileSetupForm.tsx       # [DEPRECATED - onboarding으로 대체, change-2026-02-05-0918]
│   │   ├── PhoneVerification.tsx      # [MOVED to onboarding, change-2026-02-05-0918]
│   │   ├── PasswordRuleChecker.tsx    # Password rules display (change-2026-01-14-1500)
│   │   ├── PasswordResetForm.tsx      # Password reset form (change-2026-01-14-1500)
│   │   └── WithdrawForm.tsx           # Account withdrawal form (change-2026-01-14-1500)
│   │
│   ├── onboarding/               # Onboarding components [NEW - change-2026-02-05-0918]
│   │   ├── OnboardingFlow.tsx         # Onboarding flow orchestrator
│   │   ├── NicknameSetup.tsx          # Nickname setup form (2-12 chars, unique)
│   │   ├── PhoneVerification.tsx      # Phone verification form (SMS OTP 6 digits)
│   │   └── OnboardingProgressBar.tsx  # Progress indicator (Step 1/2, Step 2/2)
│   │
│   ├── story/                    # Story domain components
│   │   ├── StoryCard.tsx
│   │   ├── StoryList.tsx
│   │   ├── StoryDetail.tsx
│   │   └── StoryBanner.tsx
│   │
│   ├── creator/                  # Creator domain components
│   │   ├── CreatorProfile.tsx
│   │   ├── CreatorStoryList.tsx   # Story-first 에피소드 관리 진입점 (change-2026-01-13-1430)
│   │   ├── CreatorStoryCard.tsx   # Story 카드 (Navigate/Edit/Preview) (change-2026-01-13-1430)
│   │   └── EpisodeSelector.tsx    # Episode 선택기 (OPEN only, 검색) (change-2026-01-13-1430)
│   │
│   ├── episode/                  # Episode domain components
│   │   ├── EpisodeList.tsx
│   │   ├── EpisodeCard.tsx
│   │   ├── EpisodeViewer.tsx
│   │   ├── EpisodeEditor.tsx
│   │   ├── ContentUploader.tsx
│   │   └── CreditSection.tsx
│   │
│   ├── proposal/                 # Proposal domain components
│   │   ├── ProposalCard.tsx
│   │   ├── ProposalList.tsx
│   │   ├── ProposalModal.tsx
│   │   ├── BackingModal.tsx
│   │   ├── DirectBackingModal.tsx
│   │   ├── ProposalHistoryCard.tsx   # Proposal history card (change-2026-01-19-1100)
│   │   ├── ProposalStatusBadge.tsx   # Status badge component (change-2026-01-19-1100)
│   │   ├── ProposalStageIndicator.tsx # 3-step stage indicator (change-2026-01-19-1100)
│   │   ├── ProposalTimeline.tsx      # Timeline component (change-2026-01-19-1100)
│   │   └── Top3Proposals.tsx         # 상위 3개 Proposal 블록 [NEW - change-2026-02-04-1450]
│   │
│   ├── participation/            # Participation domain components [NEW - change-2026-02-04-1450]
│   │   ├── ParticipationTypeBadge.tsx     # 참여 유형 뱃지 (PROPOSER/BACKER/SPONSOR)
│   │   └── ParticipationNameSelector.tsx  # 참여 이름 선택 UI (기본/익명/별칭)
│   │
│   ├── work/                     # Work domain components [NEW - change-2026-02-04-1450]
│   │   ├── RecentlyViewedWorks.tsx        # 최근 본 작품 섹션
│   │   ├── MainProducers.tsx              # [UPDATED - change-2026-02-04-1450: 뱃지, 순위, 모달]
│   │   └── SeriesContributors.tsx         # [UPDATED - change-2026-02-04-1450: 진행중/완료 분리]
│   │
│   ├── rank/                     # Rank domain components [NEW - change-2026-02-04-1450]
│   │   └── RankCrown.tsx                  # 순위 아이콘 (금/은/동 왕관)
│   │
│   ├── modal/                    # Modal components [NEW - change-2026-02-04-1450]
│   │   └── BackersModal.tsx               # 지지자 목록 모달
│   │
│   ├── category/                 # Category domain components [NEW - change-2026-02-04-1450]
│   │   └── CategoryCard.tsx               # [UPDATED - change-2026-02-04-1450: [19] 뱃지]
│   │
│   ├── wallet/                   # Wallet domain components
│   │   ├── BalanceCard.tsx
│   │   ├── TransactionList.tsx
│   │   └── InkShop.tsx
│   │
│   ├── payment/                  # Payment domain components (change-2026-01-13-1640)
│   │   ├── OrderInfoCard.tsx     # 주문 상품/가격 정보 카드
│   │   ├── PaymentMethodSelector.tsx  # 결제 수단 Radio 선택
│   │   ├── PaymentTermsAgreement.tsx  # 약관 동의 체크박스
│   │   ├── PaymentCTA.tsx        # 결제하기 버튼 (금액 표시)
│   │   ├── PaymentSuccessStatus.tsx   # 결제 성공 상태
│   │   ├── PaymentReceiptCard.tsx     # 결제 내역 카드
│   │   ├── ChargeResultCard.tsx  # 충전 결과 (보유 Ink) 카드
│   │   └── PaymentErrorModal.tsx # 결제 실패 에러 모달
│   │
│   ├── settlement/               # Settlement domain components
│   │   ├── SettlementSummary.tsx
│   │   ├── SettlementList.tsx    # [REMOVED] SettlementHistoryPage로 전환 (change-2026-01-21-1135)
│   │   ├── SettlementHistoryPage.tsx  # [NEW] Settlement history page (change-2026-01-21-1135)
│   │   ├── WithdrawalModal.tsx
│   │   └── SettlementDetailModal.tsx
│   │
│   ├── report/                   # Report domain components
│   │   ├── ReportButton.tsx      # [NEW] Report button with text + icon (change-2026-01-21-1135)
│   │   ├── ReportModal.tsx
│   │   ├── ReportTable.tsx       # Mock data display (change-2026-01-21-1135)
│   │   ├── ReportDetailModal.tsx # Mock data display (change-2026-01-21-1135)
│   │   └── MyReportList.tsx
│   │
│   └── admin/                    # Admin domain components
│   └── admin/                    # Admin domain components
│       ├── UserTable.tsx
│       ├── ContentTable.tsx
│       ├── PaymentTable.tsx
│       ├── SettlementTable.tsx
│       ├── SanctionModal.tsx
│       ├── DeleteConfirmModal.tsx
│       └── DashboardStats.tsx
│
├── hooks/                        # Custom React hooks
│   ├── useAuth.ts                # Authentication hook
│   ├── useAuthRedirect.ts        # Auth redirect based on onboarding status [NEW - change-2026-02-05-0918]
│   ├── useOnboardingGuard.ts     # Onboarding completion guard [NEW - change-2026-02-05-0918]
│   ├── useNicknameValidation.ts  # Nickname validation and availability check [NEW - change-2026-02-05-0918]
│   ├── usePhoneVerification.ts   # Phone verification (change-2026-01-14-1500)
│   ├── useWallet.ts              # Wallet operations
│   ├── useProposals.ts           # Proposal operations
│   ├── useMyProposals.ts         # My proposal history (change-2026-01-19-1100)
│   ├── useEpisodes.ts            # Episode operations
│   ├── useCreatorStories.ts      # Creator 본인 Story 목록 (change-2026-01-13-1430)
│   ├── useStoryEpisodes.ts       # Story ID 기반 Episode 목록 (change-2026-01-13-1430)
│   ├── usePaymentProcess.ts      # 결제 처리 로직 (PG 연동, 중복 방지) (change-2026-01-13-1640)
│   ├── useReceiptDownload.ts     # 영수증 PDF 다운로드 (change-2026-01-13-1640)
│   ├── useSettlements.ts         # Settlement operations
│   ├── useRecentlyViewed.ts      # 최근 본 작품 hook [NEW - change-2026-02-04-1450]
│   ├── useAgeVerification.ts     # 성인 콘텐츠 접근 제어 hook [NEW - change-2026-02-04-1450]
│   ├── useInfiniteScroll.ts      # Infinite scroll
│   └── useDebounce.ts            # Debounce utility
│
├── services/                     # API service layer
│   ├── api.ts                    # Axios instance
│   ├── authService.ts            # Auth API calls
│   ├── userService.ts            # User API calls
│   ├── walletService.ts          # Wallet API calls
│   ├── transactionService.ts     # Transaction API calls
│   ├── paymentService.ts         # Payment API calls
│   ├── storyService.ts           # Story API calls
│   ├── creatorService.ts         # Creator API calls
│   ├── episodeService.ts         # Episode API calls
│   ├── proposalService.ts        # Proposal API calls
│   ├── backingService.ts         # Backing API calls
│   ├── creditService.ts          # Credit API calls
│   ├── settlementService.ts      # Settlement API calls
│   └── adminService.ts           # Admin API calls
│
├── stores/                       # Zustand stores
│   ├── authStore.ts              # Auth state (includes onboardingCompleted field, change-2026-02-05-0918)
│   ├── onboardingStore.ts        # Onboarding progress state [NEW - change-2026-02-05-0918]
│   ├── walletStore.ts            # Wallet state
│   ├── workAliasStore.ts         # 작품별 별칭 store [NEW - change-2026-02-04-1450]
│   ├── uiStore.ts                # UI state (modals, toasts)
│   └── index.ts                  # Store exports
│
├── types/                        # TypeScript types
│   ├── user.ts                   # User types
│   ├── wallet.ts                 # Wallet types
│   ├── transaction.ts            # Transaction types
│   ├── payment.ts                # Payment types
│   ├── story.ts                  # Story types
│   ├── episode.ts                # Episode types
│   ├── episodeContent.ts         # EpisodeContent types
│   ├── proposal.ts               # Proposal types
│   ├── backing.ts                # Backing types
│   ├── credit.ts                 # Credit types
│   ├── escrow.ts                 # Escrow types
│   ├── withdrawal.ts             # WithdrawalRequest types
│   ├── sanction.ts               # Sanction types
│   ├── api.ts                    # API response types
│   └── index.ts                  # Type exports
│
├── lib/                          # Utility libraries
│   ├── utils/                    # Utility functions
│   │   ├── generateNickname.ts   # 닉네임 자동 생성 (형용사+명사+#숫자) [NEW - change-2026-02-04-1450]
│   │   └── ...
│   ├── utils.ts                  # General utilities
│   ├── cn.ts                     # className utility
│   ├── validators.ts             # Zod schemas
│   ├── constants.ts              # App constants
│   └── formatters.ts             # Date/number formatters
│
├── config/                       # Configuration
│   ├── site.ts                   # Site config
│   ├── navigation.ts             # Navigation config
│   └── routes.ts                 # Route constants
│
├── public/                       # Static assets
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── styles/                       # Additional styles
│   └── components/               # Component-specific styles
│
├── .env.local                    # Environment variables
├── .env.example                  # Environment example
├── next.config.js                # Next.js config
├── tailwind.config.ts            # Tailwind config
├── tsconfig.json                 # TypeScript config
├── package.json
└── README.md
```

---

## File Mapping

### Pages (from IA Structure)

| Route | File Path | Component Name |
|-------|-----------|----------------|
| `/` | `app/(public)/page.tsx` | SplashPage |
| `/login` | `app/(public)/login/page.tsx` | LoginPage |
| `/signup` | `app/(public)/signup/page.tsx` | SignupPage |
| `/signup/complete` | `app/(public)/signup/complete/page.tsx` | SignupCompletePage (change-2026-01-14-1500) |
| `/password-reset` | `app/(public)/password-reset/page.tsx` | PasswordResetPage (change-2026-01-14-1500) |
| `/password-reset/verify` | `app/(public)/password-reset/verify/page.tsx` | PasswordResetVerifyPage (change-2026-01-14-1500) |
| `/password-reset/new` | `app/(public)/password-reset/new/page.tsx` | PasswordResetNewPage (change-2026-01-14-1500) |
| `/terms` | `app/(public)/terms/page.tsx` | TermsPage |
| `/profile/setup` | `app/(auth)/profile/setup/page.tsx` | ProfileSetupPage |
| `/mypage` | `app/(auth)/mypage/page.tsx` | MyPage |
| `/mypage/wallet` | `app/(auth)/mypage/wallet/page.tsx` | WalletPage |
| `/mypage/settings` | `app/(auth)/mypage/settings/page.tsx` | SettingsPage |
| `/mypage/withdraw` | `app/(auth)/mypage/withdraw/page.tsx` | WithdrawPage (change-2026-01-14-1500) |
| `/mypage/participation-history` | `app/(common)/mypage/participation-history/page.tsx` | ParticipationHistoryPage [RENAMED from proposals - change-2026-02-04-1450] |
| `/mypage/participation-history/:id` | `app/(common)/mypage/participation-history/[id]/page.tsx` | ParticipationDetailPage [RENAMED from proposals - change-2026-02-04-1450] |
| `/recently-viewed` | `app/(reader)/recently-viewed/page.tsx` | RecentlyViewedPage [NEW - change-2026-02-04-1450] |
| `/home` | `app/(auth)/home/page.tsx` | HomePage |
| `/explore` | `app/(auth)/explore/page.tsx` | ExplorePage |
| `/search` | `app/(auth)/search/page.tsx` | SearchPage |
| `/stories/:storyId` | `app/(auth)/stories/[storyId]/page.tsx` | StoryDetailPage |
| `/creators/:creatorId` | `app/(auth)/creators/[creatorId]/page.tsx` | CreatorDetailPage |
| `/stories/:storyId/episodes/:episodeId` | `app/(auth)/stories/[storyId]/episodes/[episodeId]/page.tsx` | EpisodeViewerPage |
| `/shop` | `app/(auth)/shop/page.tsx` | ShopPage |
| `/shop/payment` | `app/(auth)/shop/payment/page.tsx` | PaymentPage |
| `/shop/payment/complete` | `app/(auth)/shop/payment/complete/page.tsx` | PaymentCompletePage |
| `/studio` | `app/(creator)/studio/page.tsx` | StudioPage |
| `/studio/episodes` | `app/(creator)/studio/episodes/page.tsx` | EpisodeManagementPage |
| `/studio/episodes/:episodeId/edit` | `app/(creator)/studio/episodes/[episodeId]/edit/page.tsx` | EpisodeEditorPage |
| `/studio/proposals` | `app/(creator)/studio/proposals/page.tsx` | ProposalManagementPage |
| `/studio/settlements` | `app/(creator)/studio/settlements/page.tsx` | SettlementPage |
| `/admin` | `app/(admin)/admin/page.tsx` | AdminDashboardPage |
| `/admin/users` | `app/(admin)/admin/users/page.tsx` | UserManagementPage |
| `/admin/contents` | `app/(admin)/admin/contents/page.tsx` | ContentManagementPage |
| `/admin/payments` | `app/(admin)/admin/payments/page.tsx` | PaymentManagementPage |
| `/admin/settlements` | `app/(admin)/admin/settlements/page.tsx` | SettlementManagementPage |

---

### Services (from Conceptual Model)

| Concept | Service File | Methods |
|---------|--------------|---------|
| User | `services/userService.ts` | getProfile, updateProfile, checkNickname |
| Wallet | `services/walletService.ts` | getBalance, getTransactions |
| Transaction | `services/transactionService.ts` | getAll, getById |
| Payment | `services/paymentService.ts` | createPayment, verifyPayment, getHistory |
| Story | `services/storyService.ts` | getAll, getById, create, update, delete, search |
| Creator | `services/creatorService.ts` | getById, getStories |
| Episode | `services/episodeService.ts` | getAll, getById, create, update, delete, publish |
| EpisodeContent | `services/episodeService.ts` | uploadContent, reorderContent, deleteContent |
| Proposal | `services/proposalService.ts` | getAll, getById, create, deal, drop |
| Backing | `services/backingService.ts` | createBacking, createDirectBacking |
| Credit | `services/creditService.ts` | getByEpisode |
| Escrow | - | (Backend internal, no frontend service) |
| WithdrawalRequest | `services/settlementService.ts` | requestWithdrawal, getRequests |
| Sanction | `services/adminService.ts` | createSanction, getSanctions |

---

### Types (from Conceptual Model)

| Concept | Type File | Exports |
|---------|-----------|---------|
| User | `types/user.ts` | User, UserRole, AuthProvider, UserStatus |
| Wallet | `types/wallet.ts` | Wallet |
| Transaction | `types/transaction.ts` | Transaction, TransactionType, TransactionDirection |
| Payment | `types/payment.ts` | Payment, PaymentMethod, PaymentStatus |
| Story | `types/story.ts` | Story, StoryStatus |
| Episode | `types/episode.ts` | Episode, EpisodeStatus |
| EpisodeContent | `types/episodeContent.ts` | EpisodeContent, ContentType |
| Proposal | `types/proposal.ts` | Proposal, ProposalStatus |
| Backing | `types/backing.ts` | Backing, BackingType, BackingStatus |
| Credit | `types/credit.ts` | Credit, Contributor |
| Escrow | `types/escrow.ts` | Escrow, EscrowStatus |
| WithdrawalRequest | `types/withdrawal.ts` | WithdrawalRequest, WithdrawalStatus |
| Sanction | `types/sanction.ts` | Sanction, SanctionType |

---

### Component Mapping (from User Stories)

| User Story | Component | Description |
|------------|-----------|-------------|
| US-001 | SignupForm, ProfileSetupForm | 회원가입/프로필 설정 |
| US-002 | LoginForm, SocialLoginButtons | 로그인 |
| US-003 | BalanceCard | 잔액 표시 |
| US-004 | TransactionList | 트랜잭션 내역 |
| US-005 | InkShop, OrderInfoCard, PaymentMethodSelector, PaymentTermsAgreement, PaymentCTA, PaymentSuccessStatus, PaymentReceiptCard, ChargeResultCard, PaymentErrorModal | 충전 옵션 선택, 결제 처리, 결제 완료 (change-2026-01-13-1640) |
| US-006 | StoryBanner, StoryList, StoryCard | 홈/탐색 |
| US-007 | SearchInput, FilterOptions | 검색/필터 |
| US-035 | CreatorProfile, CreatorStoryList, StoryCard | 작가 상세 |
| US-008 | EpisodeViewer | 콘텐츠 뷰어 |
| US-009 | ProposalModal | 제안 생성 모달 |
| US-010 | BackingModal | 지지 모달 |
| US-011 | DirectBackingModal | 직접 후원 모달 |
| US-012 | CreditSection | 크레딧 표시 |
| US-013 | ProposalList, ProposalCard | 제안 목록 |
| US-014 | EpisodeEditor, ContentUploader | 에피소드 편집 |
| US-015 | EpisodeList, EpisodeCard | 에피소드 관리 |
| US-016 | ProposalList (Creator) | 제안 관리 |
| US-017 | DealConfirmModal | Deal 확인 |
| US-018 | DropConfirmModal | Drop 확인 |
| US-019 | EpisodeEditor (Final) | 완성본 업로드 |
| US-020 | SettlementSummary, SettlementList | 정산 현황 |
| US-021 | WithdrawalModal | 출금 요청 |
| US-022 | UserTable | 회원 목록 |
| US-023 | SanctionModal | 회원 제재 |
| US-024 | ContentTable, DeleteConfirmModal | 콘텐츠 관리 |
| US-025 | SettlementTable | 정산 요청 목록 |
| US-026 | SettlementDetailModal | 정산 승인 |
| US-027 | PaymentTable | 결제 내역 |
| US-028 | DashboardStats | 대시보드 통계 |

---

## Implementation Notes

### Routing Strategy

- **Route Groups**: `(public)`, `(auth)`, `(creator)`, `(admin)` for layout separation
- **Dynamic Routes**: `[storyId]`, `[episodeId]` for resource pages
- **Middleware**: Auth check in layout.tsx for each group

### State Management

- **Server State**: TanStack Query for API data (stories, episodes, proposals)
- **Client State**: Zustand for UI state (modals, auth)
- **Form State**: React Hook Form for forms

### Component Architecture

- **Page Components**: Minimal logic, composition of domain components
- **Domain Components**: Business logic, API calls via hooks
- **UI Components**: Stateless, styled with Tailwind

### Performance

- **Lazy Loading**: Dynamic imports for heavy components
- **Infinite Scroll**: TransactionList, ProposalList
- **Image Optimization**: next/image for all images
- **Caching**: TanStack Query caching strategy

### Error Handling

- **API Errors**: Global error boundary + toast notifications
- **Form Validation**: Zod schemas with React Hook Form
- **Auth Errors**: Redirect to login with return URL

---

## 문서 정보

- **생성일:** 2026-01-06
- **기반 문서:** ia_structure.md, conceptual_model.json
