# Logical Architecture - 쟈근친구들 V2 (Conture)

> Generated from: user_stories_data.json, conceptual_model.json, ia_structure.md
Date: 2026-01-06

---

## Overview

### Architecture Pattern

**Modular Frontend Architecture** with domain-driven component design for a Next.js 14 application.

### Core Principles

1. $1
2. $1
3. $1
4. $1

### Architecture Layers

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Presentation Layer                          │
│  (Pages, Layouts, Navigation)                                       │
├─────────────────────────────────────────────────────────────────────┤
│                         Component Layer                             │
│  (Domain Components, UI Components, Modals)                         │
├─────────────────────────────────────────────────────────────────────┤
│                         Business Logic Layer                        │
│  (Custom Hooks, Store Actions)                                      │
├─────────────────────────────────────────────────────────────────────┤
│                         Data Access Layer                           │
│  (Services, API Client)                                             │
├─────────────────────────────────────────────────────────────────────┤
│                         Type Definition Layer                       │
│  (Types, Schemas, Validators)                                       │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Component Identification

### Actor-Action Analysis

ActorActionsDerived ComponentsReader회원가입, 로그인, 프로필 설정AuthenticationService, UserProfileServiceReaderInk 잔액 확인, 트랜잭션 조회, 충전WalletService, TransactionService, PaymentServiceReader작품 탐색, 검색, 필터링StoryDiscoveryService, SearchServiceReader에피소드 열람EpisodeViewerService, ContentRendererReader제안 생성, 지지, 직접 후원ProposalService, BackingServiceReader크레딧 확인CreditDisplayServiceCreator에피소드 업로드, 관리EpisodeManagementService, ContentUploadServiceCreator제안 Deal/Drop 결정ProposalDecisionServiceCreator정산 조회, 출금 요청SettlementService, WithdrawalServiceAdmin회원 관리, 제재UserAdminService, SanctionServiceAdmin콘텐츠 관리ContentModerationServiceAdmin결제 내역 조회PaymentAdminServiceAdmin정산 요청 승인SettlementApprovalServiceSystem에스크로 처리EscrowServiceSystem크레딧 생성CreditGenerationServiceReader/Creator신고 제출ReportService, ReportModalAdmin신고 처리ReportAdminService, ReportTable, ReportDetailModal

---

## Component Specifications

### 1. Authentication Domain

#### 

- **Primary Responsibility**: Handles user authentication including email login, social OAuth, session management, password reset, and account withdrawal.

- **Type**: Service

- **User Stories**: US-001, US-002, US-002-1, US-002-2

- **Dependencies**: API Client, AuthStore

- **Interfaces**:

`login(email, password): Promise<AuthResult>`

- `socialLogin(provider): Promise<AuthResult>`

- `signup(userData): Promise<AuthResult>`

- `logout(): Promise<void>`

- `verifyEmail(token): Promise<boolean>`

- `resetPassword(phone, newPassword): Promise<void>` (change-2026-01-14-1500)

- `withdrawAccount(password): Promise<void>` (change-2026-01-14-1500)

**Validation**: ✓ Validated - Single responsibility (authentication), specific name

#### 

- **Primary Responsibility**: Manages user profile data including nickname validation, profile updates, and identity verification status.

- **Type**: Service

- **User Stories**: US-001

- **Dependencies**: API Client

- **Interfaces**:

`getProfile(): Promise<User>`

- `updateProfile(data): Promise<User>`

- `checkNicknameAvailability(nickname): Promise<boolean>`

- `verifyIdentity(data): Promise<VerificationResult>`

**Validation**: ✓ Validated - Separated from auth (profile vs authentication)

####

- **Primary Responsibility**: 소셜 로그인 전용 폼 (이메일/비밀번호 제거됨) [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-002

- **Dependencies**: SocialLoginButtons, TermsAgreement (첫 로그인 시)

- **Validation**: ✓ Validated - Form rendering only

####

- **Primary Responsibility**: [DEPRECATED] 회원가입 폼 (소셜 로그인 자동 가입으로 대체) [DEPRECATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-001

- **Dependencies**: AuthenticationService, UserProfileService

- **Validation**: ✓ Validated - Signup form rendering only

#### 

- **Primary Responsibility**: Renders OAuth provider buttons and initiates social authentication flows.

- **Type**: Component

- **User Stories**: US-001, US-002

- **Dependencies**: AuthenticationService

- **Validation**: ✓ Validated - Social button rendering only

####

- **Primary Responsibility**: 닉네임 입력 필드 (중복 검사 제거, 자동 생성 규칙: 형용사+명사+#숫자) [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-001

- **Dependencies**: generateNickname util (자동 생성)

- **Validation**: ✓ Validated - Profile setup form only

#### (change-2026-01-14-1500)

- **Primary Responsibility**: Renders phone number input with verification code sending/validation and countdown timer.

- **Type**: Component

- **User Stories**: US-001, US-002-1

- **Dependencies**: usePhoneVerification hook

- **Interfaces**:

`onVerified(phone: string): void`

- `initialPhone?: string`

**Validation**: ✓ Validated - Phone verification UI only

#### (change-2026-01-14-1500)

- **Primary Responsibility**: Renders real-time password validation indicators (8+ chars, letters, numbers, special chars optional).

- **Type**: Component

- **User Stories**: US-001, US-002-1

- **Dependencies**: None (presentational)

- **Interfaces**:

`password: string`

- `showOptionalRules?: boolean`

**Validation**: ✓ Validated - Password rule display only

#### (change-2026-01-14-1500)

- **Primary Responsibility**: Renders the 3-step password reset flow UI (phone input → verification → new password).

- **Type**: Component

- **User Stories**: US-002-1

- **Dependencies**: PhoneVerification, PasswordRuleChecker, AuthenticationService

- **Validation**: ✓ Validated - Password reset form only

#### (change-2026-01-14-1500)

- **Primary Responsibility**: Renders account withdrawal form with confirmation steps (password, confirmation text, agreement).

- **Type**: Component

- **User Stories**: US-002-2

- **Dependencies**: AuthenticationService

- **Interfaces**:

`onWithdraw(): void`

**Validation**: ✓ Validated - Withdrawal form only

#### (change-2026-01-14-1500)

- **Primary Responsibility**: Custom hook for managing phone verification state, sending codes, and countdown timer (5 min).

- **Type**: Hook

- **User Stories**: US-001, US-002-1

- **Dependencies**: API Client

- **Interfaces**:

`sendCode(phone: string): Promise<void>`

- `verifyCode(code: string): Promise<boolean>`

- `timer: number`

- `isCodeSent: boolean`

- `isVerified: boolean`

**Validation**: ✓ Validated - Phone verification logic only

---

### 1.5 Onboarding Domain (change-2026-02-05-0918)

####

- **Primary Responsibility**: Manages the entire onboarding flow including step progression, state tracking, and completion.

- **Type**: Component

- **User Stories**: US-001-1

- **Dependencies**: NicknameSetup, PhoneVerification, onboardingStore, useAuthRedirect

- **Interfaces**:

`currentStep: "nickname" | "phone"`

- `onComplete(): void`

**Validation**: ✓ Validated - Onboarding flow orchestration

####

- **Primary Responsibility**: Renders nickname input form with real-time duplicate checking (500ms debounce).

- **Type**: Component

- **User Stories**: US-001-1

- **Dependencies**: useNicknameValidation, API Client

- **Interfaces**:

`onNext(nickname: string): void`

- `initialNickname?: string`

**Validation**: ✓ Validated - Nickname setup UI only

####

- **Primary Responsibility**: Renders phone verification form with SMS OTP (6 digits, 3 min validity).

- **Type**: Component

- **User Stories**: US-001-1

- **Dependencies**: usePhoneVerification hook

- **Interfaces**:

`onComplete(phoneNumber: string): void`

**Validation**: ✓ Validated - Phone verification UI for onboarding

####

- **Primary Responsibility**: Displays onboarding progress indicator (Step 1/2, Step 2/2).

- **Type**: Component

- **User Stories**: US-001-1

- **Dependencies**: None (presentational)

- **Interfaces**:

`currentStep: number`

- `totalSteps: number`

**Validation**: ✓ Validated - Progress bar display only

####

- **Primary Responsibility**: Custom hook for validating nickname rules (2-12 chars) and checking availability with debounce (500ms).

- **Type**: Hook

- **User Stories**: US-001-1

- **Dependencies**: API Client

- **Interfaces**:

`validateNickname(nickname: string): { valid: boolean, message: string }`

- `checkAvailability(nickname: string): Promise<boolean>`

- `isChecking: boolean`

**Validation**: ✓ Validated - Nickname validation logic only

####

- **Primary Responsibility**: Custom hook for redirecting users after login based on onboarding status.

- **Type**: Hook

- **User Stories**: US-002

- **Dependencies**: authStore, router

- **Interfaces**:

`redirect(): void`

- `checkOnboardingStatus(): Promise<boolean>`

**Validation**: ✓ Validated - Auth redirect logic only

####

- **Primary Responsibility**: Custom hook for protecting routes that require onboarding completion.

- **Type**: Hook

- **User Stories**: US-001-1

- **Dependencies**: authStore, router

- **Interfaces**:

`isOnboardingRequired: boolean`

- `redirectToOnboarding(): void`

**Validation**: ✓ Validated - Onboarding guard logic only

####

- **Primary Responsibility**: Zustand store for tracking onboarding progress (current step, completed steps).

- **Type**: Store

- **User Stories**: US-001-1

- **Dependencies**: None

- **State**:

`currentStep: "nickname" | "phone"`

- `nicknameCompleted: boolean`

- `phoneCompleted: boolean`

- `setCurrentStep(step): void`

- `markStepCompleted(step): void`

- `reset(): void`

**Validation**: ✓ Validated - Onboarding state management only

---

### 2. Wallet Domain

#### 

- **Primary Responsibility**: Retrieves and manages user wallet balance including current balance and creator settlement balances.

- **Type**: Service

- **User Stories**: US-003, US-020

- **Dependencies**: API Client

- **Interfaces**:

`getBalance(): Promise<Wallet>`

- `getCreatorBalance(): Promise<CreatorWallet>`

**Validation**: ✓ Validated - Balance retrieval only

#### 

- **Primary Responsibility**: Retrieves and filters Ink transaction history for user wallet.

- **Type**: Service

- **User Stories**: US-004

- **Dependencies**: API Client

- **Interfaces**:

`getTransactions(filter?): Promise<PaginatedList<Transaction>>`

- `getTransactionById(id): Promise<Transaction>`

**Validation**: ✓ Validated - Transaction reading only

#### 

- **Primary Responsibility**: Processes Ink purchase payments through PG integration and handles payment callbacks.

- **Type**: Service

- **User Stories**: US-005

- **Dependencies**: API Client, PG SDK (Toss/Payple)

- **Interfaces**:

`createPayment(amount): Promise<PaymentIntent>`

- `verifyPayment(pgTransactionId): Promise<PaymentResult>`

- `getPaymentHistory(): Promise<PaginatedList<Payment>>`

**Validation**: ✓ Validated - Payment processing only

#### 

- **Primary Responsibility**: Displays user's current Ink balance with formatting and visual styling.

- **Type**: Component

- **User Stories**: US-003

- **Dependencies**: WalletService (via hook)

- **Validation**: ✓ Validated - Balance display only

#### 

- **Primary Responsibility**: Renders paginated list of transactions with infinite scroll and filtering.

- **Type**: Component

- **User Stories**: US-004

- **Dependencies**: TransactionService (via hook)

- **Validation**: ✓ Validated - Transaction list rendering only

#### 

- **Primary Responsibility**: Displays Ink purchase options in vertical list layout and initiates payment flow.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: PaymentService (via hook)

- **UI Layout** (change-2026-01-12-1444): **Vertical 1-column list** (changed from grid 2-column)

Card structure: Left (coin amount + bonus), Right (discount rate + price)

- Consistent card height, dense spacing for comparison UX

**Navigation** (change-2026-01-13-1640): 결제하기 버튼 클릭 → `/shop/payment` 페이지로 이동

**Validation**: ✓ Validated - Shop UI only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Displays order information including Ink amount, bonus, and payment total.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None (props only)

- **Props**:

`inkAmount: number` - 충전 Ink 수량

- `bonusInkAmount?: number` - 보너스 Ink 수량

- `originalPrice: number` - 기본 금액

- `discountRate?: number` - 할인율

- `finalPrice: number` - 최종 결제 금액

**Validation**: ✓ Validated - Order info display only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Renders payment method radio options and handles selection.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None (controlled component)

- **Props**:

`value: PaymentMethod` - 현재 선택된 결제 수단

- `onChange: (method: PaymentMethod) => void` - 선택 변경 핸들러

**Options**: CARD (기본값), BANK_TRANSFER, VIRTUAL_ACCOUNT, TOSS

**Validation**: ✓ Validated - Payment method selection only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Renders terms agreement checkbox and handles agreement state.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None (controlled component)

- **Props**:

`checked: boolean` - 동의 여부

- `onChange: (checked: boolean) => void` - 동의 변경 핸들러

**Validation**: ✓ Validated - Terms agreement UI only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Renders the payment action button with dynamic amount display.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None (controlled component)

- **Props**:

`amount: number` - 결제 금액

- `disabled: boolean` - 비활성화 여부 (약관 미동의 시)

- `loading: boolean` - 결제 처리 중 상태

- `onClick: () => void` - 클릭 핸들러

**Validation**: ✓ Validated - CTA button only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Displays payment success state with icon and message.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None

- **Display**: 체크 아이콘, "결제가 완료되었습니다", 서브 메시지

- **Validation**: ✓ Validated - Success status display only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Displays payment receipt details including transaction info.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: None (props only)

- **Props**:

`payment: Payment` - 결제 정보 (Ink 수량, 보너스, 결제 수단, 결제 일시, 거래 번호, 결제 금액)

**Validation**: ✓ Validated - Receipt display only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Displays current Ink balance after charging.

- **Type**: Component

- **User Stories**: US-005

- **Dependencies**: WalletService (via hook)

- **Display**: 현재 보유 Ink 수량 (카드 형태로 시각적 구분)

- **Validation**: ✓ Validated - Balance result display only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Displays payment error with retry option.

- **Type**: Component (Modal)

- **User Stories**: US-005

- **Dependencies**: uiStore (modal control)

- **Props**:

`errorMessage: string` - PG 응답 기반 에러 메시지

- `onRetry: () => void` - 재시도 핸들러

- `onClose: () => void` - 닫기 핸들러

**Validation**: ✓ Validated - Error modal only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Manages payment flow including PG integration, state management, and duplicate prevention.

- **Type**: Hook

- **User Stories**: US-005

- **Dependencies**: PaymentService, walletStore

- **Interfaces**:

`processPayment(paymentData): Promise<PaymentResult>`

- `isProcessing: boolean` - 중복 결제 방지

- `error: PaymentError | null`

**Validation**: ✓ Validated - Payment process logic only

#### - NEW (change-2026-01-13-1640)

- **Primary Responsibility**: Handles receipt PDF download functionality.

- **Type**: Hook

- **User Stories**: US-005

- **Dependencies**: PaymentService

- **Interfaces**:

`downloadReceipt(paymentId): Promise<void>`

- `isDownloading: boolean`

**Validation**: ✓ Validated - Receipt download only

---

### 3. Story Discovery Domain

#### 

- **Primary Responsibility**: Retrieves story listings with filtering, sorting, and recommendation logic.

- **Type**: Service

- **User Stories**: US-006, US-007

- **Dependencies**: API Client

- **Interfaces**:

`getFeaturedStories(): Promise<Story[]>`

- `getPopularStories(): Promise<Story[]>`

- `getLatestStories(): Promise<Story[]>`

- `getStories(filter, sort, page): Promise<PaginatedList<Story>>`

- `getStoryById(id): Promise<Story>`

**Validation**: ✓ Validated - Story retrieval only

#### 

- **Primary Responsibility**: Handles search queries for stories and creators with debouncing and result ranking.

- **Type**: Service

- **User Stories**: US-007

- **Dependencies**: API Client

- **Interfaces**:

`search(query): Promise<SearchResults>`

- `getSuggestions(query): Promise<string[]>`

**Validation**: ✓ Validated - Search functionality only

#### 

- **Primary Responsibility**: Renders featured story carousel/banner on home page.

- **Type**: Component

- **User Stories**: US-006

- **Dependencies**: StoryDiscoveryService (via hook)

- **Validation**: ✓ Validated - Banner rendering only

#### 

- **Primary Responsibility**: Renders grid/list of story cards with responsive layout.

- **Type**: Component

- **User Stories**: US-006, US-007

- **Dependencies**: None (receives stories as props)

- **Validation**: ✓ Validated - List layout only

####

- **Primary Responsibility**: 카테고리 카드 ([19] 성인 뱃지 표시 포함) [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-006

- **Dependencies**: useAgeVerification hook (성인 콘텐츠 접근)

- **Validation**: ✓ Validated - Card rendering only

#### 

- **Primary Responsibility**: Renders complete story information page with episode list.

- **Type**: Component

- **User Stories**: US-006, US-008

- **Dependencies**: StoryDiscoveryService, EpisodeService (via hooks)

- **Validation**: ✓ Validated - Story detail page only

---

### 4. Episode Viewer Domain

#### 

- **Primary Responsibility**: Retrieves episode content and metadata for viewing.

- **Type**: Service

- **User Stories**: US-008

- **Dependencies**: API Client

- **Interfaces**:

`getEpisode(storyId, episodeId): Promise<Episode>`

- `getEpisodeContent(episodeId): Promise<EpisodeContent[]>`

**Validation**: ✓ Validated - Episode data retrieval only

#### 

- **Primary Responsibility**: Renders episode content (images/text) with scroll tracking and lazy loading.

- **Type**: Component

- **User Stories**: US-008

- **Dependencies**: EpisodeContent data

- **Validation**: ✓ Validated - Content rendering only

#### 

- **Primary Responsibility**: Composes episode viewing experience including content, action bar, and proposal list.

- **Type**: Component (Container)

- **User Stories**: US-008, US-012, US-013

- **Dependencies**: ContentRenderer, ActionBar, ProposalList, CreditSection

- **Validation**: ✓ Validated - Viewer composition only

#### 

- **Primary Responsibility**: Renders fixed bottom action bar with proposal and backing buttons (OPEN state only).

- **Type**: Component

- **User Stories**: US-008, US-009, US-011

- **Dependencies**: Episode status

- **Validation**: ✓ Validated - Action buttons only

#### 

- **Primary Responsibility**: Renders contributor credits including Main Producers and sponsor list.

- **Type**: Component

- **User Stories**: US-012

- **Dependencies**: CreditDisplayService

- **Validation**: ✓ Validated - Credit display only

#### 

- **Primary Responsibility**: Retrieves and formats credit data for episode display.

- **Type**: Service

- **User Stories**: US-012

- **Dependencies**: API Client

- **Interfaces**:

`getCredits(episodeId): Promise<Credit>`

**Validation**: ✓ Validated - Credit retrieval only

---

### 5. Intervention Domain (Proposal & Backing)

#### 

- **Primary Responsibility**: Handles proposal CRUD operations including creation with escrow initialization.

- **Type**: Service

- **User Stories**: US-009, US-013, US-003-1

- **Dependencies**: API Client, EscrowService

- **Interfaces**:

`createProposal(episodeId, data): Promise<Proposal>`

- `getProposals(episodeId, sort?): Promise<Proposal[]>`

- `getProposalById(id): Promise<Proposal>`

- `getMyProposals(filter?): Promise<ProposalHistory[]>` ← NEW (change-2026-01-19-1100)

- `getMyProposalDetail(id): Promise<ProposalDetail>` ← NEW (change-2026-01-19-1100)

**Validation**: ✓ Validated - Proposal operations only

#### 

- **Primary Responsibility**: Handles backing operations for both proposal backing and direct backing.

- **Type**: Service

- **User Stories**: US-010, US-011, US-003-1

- **Dependencies**: API Client, EscrowService

- **Interfaces**:

`createBacking(proposalId, amount): Promise<Backing>`

- `createDirectBacking(episodeId, amount): Promise<Backing>`

- `getMyBackings(filter?): Promise<BackingHistory[]>` ← NEW (change-2026-01-19-1100)

- `getBackingTimeline(backingId): Promise<TimelineEvent[]>` ← NEW (change-2026-01-19-1100)

**Validation**: ✓ Validated - Backing operations only

#### 

- **Primary Responsibility**: Manages escrow state for proposal funds including hold, release, and refund operations.

- **Type**: Service (Internal)

- **User Stories**: US-009, US-010, US-017, US-018

- **Dependencies**: API Client

- **Interfaces**:

`holdFunds(proposalId, amount): Promise<Escrow>`

- `releaseFunds(proposalId): Promise<void>`

- `refundFunds(proposalId): Promise<void>`

**Validation**: ✓ Validated - Escrow management only

#### 

- **Primary Responsibility**: Renders list of proposals with sorting and backing actions.

- **Type**: Component

- **User Stories**: US-013

- **Dependencies**: ProposalService (via hook)

- **Validation**: ✓ Validated - Proposal list rendering only

#### 

- **Primary Responsibility**: Renders individual proposal with proposer profile, content, Backers summary, and back button.

- **Type**: Component

- **User Stories**: US-013

- **Dependencies**: UserProfile (presentational)

- **Enhanced Features** (change-2026-01-12-1444):

Proposer profile display (avatar, nickname, "제안자" label)

- Backers summary (backer count, total Ink) - clickable to open BackersDetailModal

- Interaction: Backers summary area onClick → trigger modal

**Validation**: ✓ Validated - Card rendering only

#### 

- **Primary Responsibility**: Renders proposal creation modal with form validation and submission.

- **Type**: Component

- **User Stories**: US-009

- **Dependencies**: ProposalService, React Hook Form

- **Validation**: ✓ Validated - Modal form only

#### 

- **Primary Responsibility**: Renders proposal backing modal with amount input and confirmation.

- **Type**: Component

- **User Stories**: US-010

- **Dependencies**: BackingService

- **Validation**: ✓ Validated - Modal form only

#### 

- **Primary Responsibility**: Renders direct backing modal with amount input and confirmation.

- **Type**: Component

- **User Stories**: US-011

- **Dependencies**: BackingService

- **Validation**: ✓ Validated - Modal form only

#### 

- **Primary Responsibility**: Displays detailed list of all backers for a specific proposal in modal format.

- **Type**: Component (Modal)

- **User Stories**: US-010

- **Dependencies**: BackersList, BackingService (via hook)

- **Added** (change-2026-01-12-1444): New modal component

- **Trigger**: ProposalCard Backers summary area click

- **Content**: Full list of backers with profile, nickname, Ink amount, backing timestamp

- **Validation**: ✓ Validated - Modal rendering only

#### 

- **Primary Responsibility**: Renders paginated list of backers with profile information and contribution amounts.

- **Type**: Component

- **User Stories**: US-010

- **Dependencies**: UserProfile (presentational)

- **Added** (change-2026-01-12-1444): New list component for displaying backer details

- **Features**: Avatar, nickname, Ink amount, backing timestamp

- **Validation**: ✓ Validated - List rendering only

#### 

- **Primary Responsibility**: Renders paginated list of direct supporters with profile and support details.

- **Type**: Component

- **User Stories**: US-011

- **Dependencies**: UserProfile (presentational)

- **Added** (change-2026-01-12-1444): New list component for Supports tab

- **Features**: Supporter profile, Ink amount, support message, timestamp, pagination (page numbers)

- **Validation**: ✓ Validated - List rendering only

#### 

- **Primary Responsibility**: Reusable user profile display component (avatar + nickname) used across proposers, backers, supporters.

- **Type**: Component (Common/Shared)

- **User Stories**: US-010, US-011, US-013

- **Dependencies**: None (presentational)

- **Added** (change-2026-01-12-1444): New shared component for consistent profile display

- **Usage**: ProposalCard, BackersList, SupportsList, TopProducersCard, ProducersList

- **Props**: `user: {avatar, nickname}`, `label?: string`, `size?: 'small' | 'medium' | 'large'`, `interactive?: boolean`

- **Validation**: ✓ Validated - Profile display only

####

- **Primary Responsibility**: 작품 기여자 Top 리스트 (전원 뱃지 표시, Top 3 순위 아이콘, 클릭 시 모달) [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-012

- **Dependencies**: ParticipationTypeBadge, RankCrown, BackersModal

- **Added** (change-2026-01-12-1444): New component for credit display

- **Features**: Large profile image, nickname, PROPOSER label (1 person only), contribution Ink, rank badge (#1/#2/#3)

- **Layout**: 3 horizontal cards, sorted by total contribution Ink (proposal + backing) descending

- **Interactive**: 클릭 시 BackersModal 표시 [UPDATED - change-2026-02-04-1450]

- **Validation**: ✓ Validated - Card rendering only

####

- **Primary Responsibility**: 시리즈 기여자 표시 (진행중/완료 분리) [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-012

- **Dependencies**: ParticipationTypeBadge

- **Added** (change-2026-01-12-1444): New component for credit display

- **Features**: Small avatar, nickname, contribution Ink (right-aligned), optional progress bar

- **Layout**: Vertical list, sorted by contribution Ink descending, starting from 4th rank

- **Interactive**: Non-interactive (no click), display only

- **Validation**: ✓ Validated - List rendering only

#### 

- **Primary Responsibility**: Renders Sponsored by contributors in compact list or pill format with lower visual priority.

- **Type**: Component

- **User Stories**: US-012

- **Dependencies**: None (simple display)

- **Added** (change-2026-01-12-1444): New component for secondary sponsor display

- **Features**: Name/logo, Ink amount (secondary), compact format

- **Layout**: Compact list or pill style, visually de-emphasized

- **Interactive**: Non-interactive (no click), display only

- **Validation**: ✓ Validated - List rendering only

### ParticipationTypeBadge
**Responsibility**: 참여 유형 뱃지 표시 (PROPOSER/BACKER/SPONSOR) [NEW - change-2026-02-04-1450]
**Dependencies**: None
**Used By**: ParticipationHistoryList, MainProducers, SeriesContributors

- **Primary Responsibility**: 참여 유형 뱃지 표시 (PROPOSER/BACKER/SPONSOR) [NEW - change-2026-02-04-1450]

- **Type**: Component (Presentational)

- **User Stories**: US-003-1

- **Dependencies**: None

- **Props**:

`type: 'PROPOSER' | 'BACKER' | 'SPONSOR'` - 참여 유형

- `size?: 'small' | 'medium' | 'large'` - 뱃지 크기

**Validation**: ✓ Validated - Badge rendering only

#### ← UPDATED (change-2026-02-04-1450)

- **Primary Responsibility**: 제안/지지/후원 통합 내역 리스트 표시 [UPDATED - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-003-1

- **Dependencies**: ParticipationTypeBadge, ParticipationHistoryCard

- **Features**:

작품명 / 에피소드 제목

- 제안 유형 (Proposal / Backing / Sponsor) - ParticipationTypeBadge 사용

- 후원 금액

- 상태 뱃지

**Interactive**: Click navigates to /mypage/proposals/[id]

**Validation**: ✓ Validated - Card rendering only

#### ← NEW (change-2026-01-19-1100)

- **Primary Responsibility**: Displays proposal status as colored badge based on proposal status and work status combination.

- **Type**: Component (Presentational)

- **User Stories**: US-003-1

- **Dependencies**: None

- **Status Mapping**:

검토 중 (null + open): neutral color

- 성사 대기 (deal + open): info color

- 거절됨 (drop + open): warning color

- 후원 성공 (deal + published): success color

- 환불 완료 (drop/null + published): muted color

**Validation**: ✓ Validated - Badge rendering only

#### ← NEW (change-2026-01-19-1100)

- **Primary Responsibility**: Displays 3-step progress indicator showing proposal lifecycle stage.

- **Type**: Component (Presentational)

- **User Stories**: US-003-1

- **Dependencies**: None

- **Stages**:

STEP 1: 작가 확인 중 (status: null)

- STEP 2: 작가 수락/거절 완료 (status: deal or drop)

- STEP 3: 정산 및 환불 완료 (work: published)

**Validation**: ✓ Validated - Indicator rendering only

#### ← NEW (change-2026-01-19-1100)

- **Primary Responsibility**: Renders vertical timeline of proposal status change events.

- **Type**: Component

- **User Stories**: US-003-1

- **Dependencies**: BackingService (via hook)

- **Timeline Events**:

후원 완료: "YYYY-MM-DD - 제안에 X Ink를 후원했습니다."

- 의사결정 (deal): "작가가 제안을 수락했습니다! 작품 공개 시 Ink가 사용됩니다."

- 의사결정 (drop): "작가가 제안을 거절했습니다. 작품 공개 시 Ink가 반환됩니다."

- 최종 확정 (deal + published): "작품이 공개되었습니다. 후원하신 Ink의 사용이 최종 확정되었습니다."

- 최종 확정 (drop/null + published): "작품이 공개되었습니다. 제안이 채택되지 않아 Ink가 내 지갑으로 반환되었습니다."

**Validation**: ✓ Validated - Timeline rendering only

### ParticipationNameSelector
**Responsibility**: 참여 시 이름 선택 UI (기본 닉네임/익명/작품별 별칭) [NEW - change-2026-02-04-1450]
**Dependencies**: workAliasStore
**Used By**: ProposalForm, BackingForm, CommentForm

- **Primary Responsibility**: 참여 시 이름 선택 UI (기본 닉네임/익명/작품별 별칭) [NEW - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-009, US-010

- **Dependencies**: workAliasStore

- **Props**:

`workId: string` - 작품 ID

- `value: string` - 현재 선택된 이름

- `onChange: (name: string) => void` - 선택 변경 핸들러

**Options**: 기본 닉네임 / 익명 / 작품별 별칭 (입력 가능)

**Validation**: ✓ Validated - Name selection UI only

### RecentlyViewedWorks
**Responsibility**: 최근 본 작품 섹션 (홈: 최대 5개, 전체 페이지: 무제한) [NEW - change-2026-02-04-1450]
**Dependencies**: useRecentlyViewed hook
**Used By**: HomePage, RecentlyViewedPage

- **Primary Responsibility**: 최근 본 작품 섹션 (홈: 최대 5개, 전체 페이지: 무제한) [NEW - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-006

- **Dependencies**: useRecentlyViewed hook

- **Props**:

`limit?: number` - 표시 개수 제한 (홈: 5, 전체 페이지: 무제한)

**Features**: 작품 상세 페이지 접속 시 자동 저장, 최신순 정렬

**Validation**: ✓ Validated - Recently viewed display only

### RankCrown
**Responsibility**: 순위 아이콘 (금/은/동 왕관) [NEW - change-2026-02-04-1450]
**Dependencies**: None
**Used By**: MainProducers

- **Primary Responsibility**: 순위 아이콘 (금/은/동 왕관) [NEW - change-2026-02-04-1450]

- **Type**: Component (Presentational)

- **User Stories**: US-012

- **Dependencies**: None

- **Props**:

`rank: 1 | 2 | 3` - 순위

**Validation**: ✓ Validated - Rank icon rendering only

### BackersModal
**Responsibility**: 지지자 목록 모달 (참여자, 금액, 유형, 시점) [NEW - change-2026-02-04-1450]
**Dependencies**: ParticipationTypeBadge
**Used By**: MainProducers

- **Primary Responsibility**: 지지자 목록 모달 (참여자, 금액, 유형, 시점) [NEW - change-2026-02-04-1450]

- **Type**: Component (Modal)

- **User Stories**: US-010, US-012

- **Dependencies**: ParticipationTypeBadge

- **Props**:

`episodeId: string` - 에피소드 ID

- `onClose: () => void` - 닫기 핸들러

**Features**: 참여자 프로필, 참여 유형 뱃지, 금액, 참여 시점 표시

**Validation**: ✓ Validated - Backers modal only

### Top3Proposals
**Responsibility**: 상위 3개 Proposal 블록 (Main Producers 위) [NEW - change-2026-02-04-1450]
**Dependencies**: ProposalListModal
**Used By**: StoryDetailPage

- **Primary Responsibility**: 상위 3개 Proposal 블록 (Main Producers 위) [NEW - change-2026-02-04-1450]

- **Type**: Component

- **User Stories**: US-013

- **Dependencies**: ProposalListModal

- **Features**: Top 3 Proposal 표시, "모두 보기" 버튼 클릭 시 ProposalListModal 표시

**Validation**: ✓ Validated - Top proposals display only

#### ← NEW (change-2026-01-19-1100)

- **Primary Responsibility**: Custom hook for fetching and managing user's proposal history data.

- **Type**: Hook

- **User Stories**: US-003-1, US-003-2

- **Dependencies**: ProposalService, BackingService, TanStack Query

- **Interfaces**:

`useMyProposals(filter?): { data, isLoading, error }`

- `useMyProposalDetail(id): { data, timeline, isLoading, error }`

**Validation**: ✓ Validated - Data fetching only

### useRecentlyViewed
**Responsibility**: 최근 본 작품 저장/조회 (작품 상세, 에피소드 트리거) [NEW - change-2026-02-04-1450]
**Type**: Hook
**Dependencies**: API /api/user/recently-viewed

- **Primary Responsibility**: 최근 본 작품 저장/조회 (작품 상세, 에피소드 트리거) [NEW - change-2026-02-04-1450]

- **Type**: Hook

- **User Stories**: US-006

- **Dependencies**: API /api/user/recently-viewed

- **Interfaces**:

`useRecentlyViewed(limit?): { works, isLoading, error, addView }`

**Validation**: ✓ Validated - Recently viewed data management only

### workAliasStore
**Responsibility**: 작품별 별칭 관리 (작품 단위 저장) [NEW - change-2026-02-04-1450]
**Type**: Store
**Dependencies**: API /api/user/work-aliases

- **Primary Responsibility**: 작품별 별칭 관리 (작품 단위 저장) [NEW - change-2026-02-04-1450]

- **Type**: Store

- **User Stories**: US-009, US-010

- **Dependencies**: API /api/user/work-aliases

- **Interfaces**:

`getAlias(workId): string | null`

- `setAlias(workId, alias): void`

**Validation**: ✓ Validated - Work alias state management only

### useAgeVerification
**Responsibility**: 성인 콘텐츠 접근 제어 (휴대폰 인증 기반) [NEW - change-2026-02-04-1450]
**Type**: Hook
**Dependencies**: User.ageVerified, User.isPhoneVerified

- **Primary Responsibility**: 성인 콘텐츠 접근 제어 (휴대폰 인증 기반) [NEW - change-2026-02-04-1450]

- **Type**: Hook

- **User Stories**: US-006

- **Dependencies**: User.ageVerified, User.isPhoneVerified

- **Interfaces**:

`useAgeVerification(): { canAccessAdultContent, verifyAge, isVerifying }`

**Validation**: ✓ Validated - Age verification logic only

### generateNickname
**Responsibility**: 닉네임 자동 생성 (형용사+명사+#숫자) [NEW - change-2026-02-04-1450]
**Type**: Utility
**Dependencies**: None

- **Primary Responsibility**: 닉네임 자동 생성 (형용사+명사+#숫자) [NEW - change-2026-02-04-1450]

- **Type**: Utility

- **User Stories**: US-001

- **Dependencies**: None

- **Interfaces**:

`generateNickname(): string` - 예: "멋진사자#1234"

**Validation**: ✓ Validated - Nickname generation utility only

---

### 6. Creator Studio Domain

#### 

- **Primary Responsibility**: Handles episode CRUD operations for creators including status management.

- **Type**: Service

- **User Stories**: US-014, US-015, US-019

- **Dependencies**: API Client

- **Interfaces**:

`getCreatorEpisodes(): Promise<Episode[]>`

- `createEpisode(storyId, data): Promise<Episode>`

- `updateEpisode(id, data): Promise<Episode>`

- `deleteEpisode(id): Promise<void>`

- `publishEpisode(id): Promise<Episode>`

**Validation**: ✓ Validated - Episode management only

#### 

- **Primary Responsibility**: Handles multi-image upload, ordering, and content management for episodes.

- **Type**: Service

- **User Stories**: US-014, US-019

- **Dependencies**: API Client, Storage API

- **Interfaces**:

`uploadImages(files): Promise<UploadResult[]>`

- `reorderContent(episodeId, order): Promise<void>`

- `deleteContent(contentId): Promise<void>`

**Validation**: ✓ Validated - Content upload only

#### 

- **Primary Responsibility**: Handles creator's Deal/Drop decisions with escrow settlement.

- **Type**: Service

- **User Stories**: US-016, US-017, US-018

- **Dependencies**: API Client, EscrowService

- **Interfaces**:

`getCreatorProposals(): Promise<Proposal[]>`

- `dealProposal(proposalId): Promise<DealResult>`

- `dropProposal(proposalId): Promise<DropResult>`

**Validation**: ✓ Validated - Decision processing only

#### 

- **Primary Responsibility**: Generates immutable credit data when episode is published.

- **Type**: Service (Internal)

- **User Stories**: US-019

- **Dependencies**: API Client

- **Interfaces**:

`generateCredits(episodeId): Promise<Credit>`

**Validation**: ✓ Validated - Credit generation only

#### (change-2026-01-13-1430)

- **Primary Responsibility**: Renders creator's story list as primary entry point for Episode Management.

- **Type**: Component

- **User Stories**: US-015

- **Dependencies**: useCreatorStories hook

- **Features**:

Story 목록 표시 (대량 데이터 10~100+ 고려)

- Search/Filter/Pagination 지원

- 리스트 스크롤과 페이지 스크롤 분리

**Validation**: ✓ Validated - Story list rendering only

#### (change-2026-01-13-1430)

- **Primary Responsibility**: Renders individual story card with Navigate/Edit/Preview actions for creators.

- **Type**: Component

- **User Stories**: US-015

- **Dependencies**: None (presentational)

- **Actions**:

Navigate: Story 클릭 → Episode List 표시

- Edit: Story 메타데이터 편집

- Preview: 독자 관점 미리보기

**Validation**: ✓ Validated - Story card UI only

#### (change-2026-01-13-1430)

- **Primary Responsibility**: Renders episode selector for Proposal Management (OPEN episodes only, searchable).

- **Type**: Component

- **User Stories**: US-016

- **Dependencies**: useStoryEpisodes hook

- **Features**:

동일 Story 내 OPEN 상태 Episode만 표시

- Desktop: Vertical list / Mobile: Dropdown

- Episode 개수 많을 경우 검색 지원

**Validation**: ✓ Validated - Episode selection UI only

#### (change-2026-01-13-1430)

- **Primary Responsibility**: Fetches and manages creator's own story list for Episode Management.

- **Type**: Hook

- **User Stories**: US-015

- **Dependencies**: StoryService, TanStack Query

- **Interfaces**:

`useCreatorStories(options?): { stories, isLoading, error, refetch }`

**Validation**: ✓ Validated - Creator story data fetching only

#### (change-2026-01-13-1430)

- **Primary Responsibility**: Fetches episodes by story ID for Story-context rendering.

- **Type**: Hook

- **User Stories**: US-015, US-016

- **Dependencies**: EpisodeService, TanStack Query

- **Interfaces**:

`useStoryEpisodes(storyId, options?): { episodes, isLoading, error, refetch }`

- `options.status`: Filter by episode status (e.g., 'OPEN' for Proposal Management)

**Validation**: ✓ Validated - Story-scoped episode data fetching only

#### (Creator)

- **Primary Responsibility**: Renders creator's episode list with status and bounty information.

- **Type**: Component

- **User Stories**: US-015

- **Dependencies**: EpisodeManagementService (via hook)

- **Validation**: ✓ Validated - List rendering only

#### 

- **Primary Responsibility**: Renders episode editing interface with content upload and ordering.

- **Type**: Component

- **User Stories**: US-014, US-019

- **Dependencies**: ContentUploadService, EpisodeManagementService

- **Validation**: ✓ Validated - Editor UI only

#### 

- **Primary Responsibility**: Renders drag-and-drop image upload interface with preview and reordering.

- **Type**: Component

- **User Stories**: US-014

- **Dependencies**: ContentUploadService

- **Validation**: ✓ Validated - Upload UI only

#### 

- **Primary Responsibility**: Renders creator's received proposals with Deal/Drop actions.

- **Type**: Component

- **User Stories**: US-016

- **Dependencies**: ProposalDecisionService (via hook)

- **Validation**: ✓ Validated - Management list only

#### 

- **Primary Responsibility**: Renders Deal confirmation modal with impact preview.

- **Type**: Component

- **User Stories**: US-017

- **Dependencies**: ProposalDecisionService

- **Validation**: ✓ Validated - Confirmation modal only

#### 

- **Primary Responsibility**: Renders Drop confirmation modal with refund preview.

- **Type**: Component

- **User Stories**: US-018

- **Dependencies**: ProposalDecisionService

- **Validation**: ✓ Validated - Confirmation modal only

---

### 7. Settlement Domain

#### 

- **Primary Responsibility**: Retrieves creator settlement status including payable and withdrawable balances.

- **Type**: Service

- **User Stories**: US-020

- **Dependencies**: API Client

- **Interfaces**:

`getSettlementSummary(): Promise<SettlementSummary>`

- `getSettlementHistory(): Promise<SettlementRecord[]>`

**Validation**: ✓ Validated - Settlement retrieval only

#### 

- **Primary Responsibility**: Handles withdrawal request creation and status tracking.

- **Type**: Service

- **User Stories**: US-021

- **Dependencies**: API Client

- **Interfaces**:

`createWithdrawalRequest(amount, bankInfo): Promise<WithdrawalRequest>`

- `getWithdrawalRequests(): Promise<WithdrawalRequest[]>`

**Validation**: ✓ Validated - Withdrawal operations only

#### 

- **Primary Responsibility**: Renders settlement balance summary cards with link to settlement history (change-2026-01-21-1135).

- **Type**: Component

- **User Stories**: US-020

- **Dependencies**: SettlementService (via hook), Router (for history link)

- **Features**:

요약 카드 (총 수익, 총 출금액, 정산 대기 건수)

- 예정/출금 가능 금액 표시

- "정산 내역 전체 보기" 링크 → `/studio/settlement-history` (change-2026-01-21-1135)

**Validation**: ✓ Validated - Summary display only

#### ← REMOVED (change-2026-01-21-1135)

- **Primary Responsibility**: [DEPRECATED] 정산 내역은 별도 페이지로 분리됨 (SettlementHistoryPage로 전환)

- **Type**: Component (Deprecated)

- **User Stories**: US-020

- **Dependencies**: SettlementService (via hook)

- **Status**: ❌ 제거 예정 - SettlementHistoryPage로 전환

#### ← NEW (change-2026-01-21-1135)

- **Primary Responsibility**: Renders settlement history as dedicated page (not within main settlement page).

- **Type**: Page Component (Presentation Layer)

- **Route**: `/studio/settlement-history`

- **User Stories**: US-021

- **Dependencies**: SettlementService, SettlementList (또는 RefactoredSettlementListComponent)

- **Features**:

과거 정산 히스토리 조회 (에피소드 단위)

- 검색/필터 기능 (필터링 범위 TBD)

- 페이지네이션

- 정산 내역 상세 보기 모달

**Validation**: ✓ Validated - Settlement history page dedicated view

#### 

- **Primary Responsibility**: Renders withdrawal request form with amount and bank info.

- **Type**: Component

- **User Stories**: US-021

- **Dependencies**: WithdrawalService

- **Validation**: ✓ Validated - Modal form only

---

### 8. Admin Domain

#### 

- **Primary Responsibility**: Retrieves and manages user data for admin operations.

- **Type**: Service

- **User Stories**: US-022

- **Dependencies**: API Client

- **Interfaces**:

`getUsers(filter, page): Promise<PaginatedList<User>>`

- `getUserById(id): Promise<User>`

- `searchUsers(query): Promise<User[]>`

**Validation**: ✓ Validated - User admin retrieval only

#### 

- **Primary Responsibility**: Handles user sanction operations including suspension and ban.

- **Type**: Service

- **User Stories**: US-023

- **Dependencies**: API Client

- **Interfaces**:

`createSanction(userId, data): Promise<Sanction>`

- `getSanctions(userId): Promise<Sanction[]>`

- `revokeSanction(sanctionId): Promise<void>`

**Validation**: ✓ Validated - Sanction operations only

#### 

- **Primary Responsibility**: Handles content hiding and deletion for moderation.

- **Type**: Service

- **User Stories**: US-024

- **Dependencies**: API Client

- **Interfaces**:

`getContents(filter, page): Promise<PaginatedList<Content>>`

- `hideContent(contentId): Promise<void>`

- `deleteContent(contentId): Promise<void>`

- `restoreContent(contentId): Promise<void>`

**Validation**: ✓ Validated - Moderation operations only

#### 

- **Primary Responsibility**: Retrieves payment history for admin reporting.

- **Type**: Service

- **User Stories**: US-027

- **Dependencies**: API Client

- **Interfaces**:

`getPayments(filter, page): Promise<PaginatedList<Payment>>`

- `getPaymentById(id): Promise<Payment>`

**Validation**: ✓ Validated - Payment admin retrieval only

#### 

- **Primary Responsibility**: Handles withdrawal request approval and rejection by admin.

- **Type**: Service

- **User Stories**: US-025, US-026

- **Dependencies**: API Client

- **Interfaces**:

`getWithdrawalRequests(filter, page): Promise<PaginatedList<WithdrawalRequest>>`

- `approveWithdrawal(requestId): Promise<WithdrawalRequest>`

- `rejectWithdrawal(requestId, reason): Promise<WithdrawalRequest>`

- `markAsCompleted(requestId): Promise<WithdrawalRequest>`

**Validation**: ✓ Validated - Approval operations only

#### 

- **Primary Responsibility**: Retrieves aggregated statistics for admin dashboard.

- **Type**: Service

- **User Stories**: US-028

- **Dependencies**: API Client

- **Interfaces**:

`getOverviewStats(): Promise<DashboardStats>`

- `getSignupTrend(days): Promise<TrendData[]>`

**Validation**: ✓ Validated - Stats retrieval only

#### 

- **Primary Responsibility**: Renders paginated user list table with search and filter.

- **Type**: Component

- **User Stories**: US-022

- **Dependencies**: UserAdminService (via hook)

- **Validation**: ✓ Validated - Table rendering only

#### 

- **Primary Responsibility**: Renders sanction creation modal with reason input.

- **Type**: Component

- **User Stories**: US-023

- **Dependencies**: SanctionService

- **Validation**: ✓ Validated - Modal form only

#### 

- **Primary Responsibility**: Renders content list table with moderation actions.

- **Type**: Component

- **User Stories**: US-024

- **Dependencies**: ContentModerationService (via hook)

- **Validation**: ✓ Validated - Table rendering only

#### 

- **Primary Responsibility**: Renders delete confirmation modal for destructive actions.

- **Type**: Component

- **User Stories**: US-024

- **Dependencies**: ContentModerationService

- **Validation**: ✓ Validated - Confirmation modal only

#### 

- **Primary Responsibility**: Renders payment history table with filters.

- **Type**: Component

- **User Stories**: US-027

- **Dependencies**: PaymentAdminService (via hook)

- **Validation**: ✓ Validated - Table rendering only

#### 

- **Primary Responsibility**: Renders withdrawal request table with approval actions.

- **Type**: Component

- **User Stories**: US-025

- **Dependencies**: SettlementApprovalService (via hook)

- **Validation**: ✓ Validated - Table rendering only

#### 

- **Primary Responsibility**: Renders withdrawal request detail with approve/reject actions.

- **Type**: Component

- **User Stories**: US-025, US-026

- **Dependencies**: SettlementApprovalService

- **Validation**: ✓ Validated - Detail modal only

#### 

- **Primary Responsibility**: Renders dashboard statistics cards with management CTA buttons (change-2026-01-21-1135).

- **Type**: Component

- **User Stories**: US-028

- **Dependencies**: DashboardStatsService (via hook), Router (for CTA navigation)

- **Features**:

총 회원 수 카드 + 회원 관리 버튼 → /admin/users (change-2026-01-21-1135)

- 총 작품 수 카드 + 콘텐츠 관리 버튼 → /admin/content (change-2026-01-21-1135)

- 총 거래액 카드 + 결제 관리 버튼 → /admin/payments (change-2026-01-21-1135)

- 최근 7일 가입자 추이 그래프

- 대기중 정산 요청 건수

**Note**: 하단 관리 카드 영역 (ManagementCardsSection)은 제거됨 - 기능이 상단 카드로 통합 (change-2026-01-21-1135)

**Validation**: ✓ Validated - Stats display with action buttons

---

### 9. Report Domain (change-2026-01-12-1530)

#### 

- **Primary Responsibility**: Handles report creation and user's own report history retrieval.

- **Type**: Service

- **User Stories**: US-029, US-030

- **Dependencies**: API Client

- **Interfaces**:

`createReport(data: CreateReportRequest): Promise<Report>`

- `getMyReports(): Promise<PaginatedList<Report>>`

**Validation**: ✓ Validated - Report submission and retrieval only

#### 

- **Primary Responsibility**: Handles admin operations for report management including listing, filtering, and processing.

- **Type**: Service

- **User Stories**: US-031, US-032

- **Dependencies**: API Client, SanctionService

- **Interfaces**:

`getReports(filter, page): Promise<PaginatedList<Report>>`

- `getReportById(id): Promise<Report>`

- `processReport(id, decision): Promise<Report>`

- `createSanctionFromReport(reportId, sanctionData): Promise<Sanction>`

**Validation**: ✓ Validated - Admin report operations only

#### 

- **Primary Responsibility**: Renders report creation modal with target type selection, reason selection (6 types), and detail input.

- **Type**: Component

- **User Stories**: US-029

- **Dependencies**: ReportService, React Hook Form

- **Props**:

`targetType: 'STORY' | 'PROPOSAL_AUTHOR'`

- `targetId: string`

- `targetUserId?: string` (for PROPOSAL_AUTHOR)

- `onClose: () => void`

- `onSuccess?: () => void`

**Report Types**: 스팸, 욕설/비방, 부적절한 콘텐츠, 저작권 침해, 허위 정보, 기타 (6가지)

**Trigger Locations**: StoryDetail, ProposalCard, EpisodeViewer, UserProfile

**Permission**:

- 독자: 스토리/Proposal 작성자 신고 가능

- 작가: 본인 스토리에 달린 Proposal 작성자만 신고 가능

**Validation**: ✓ Validated - Report modal form only

#### 

- **Primary Responsibility**: Renders user's own report history with status badges and detail modal trigger.

- **Type**: Component

- **User Stories**: US-030

- **Dependencies**: ReportService (via useMyReports hook)

- **Features**:

Report list with: target, type, date, status badge (접수됨/검토 중/처리 완료/기각)

- Click → ReportDetailModal (user version)

- Empty state handling

**Validation**: ✓ Validated - User report list only

#### 

- **Primary Responsibility**: Renders admin report list table with filtering and sorting. Displays mock data (change-2026-01-21-1135).

- **Type**: Component

- **User Stories**: US-031

- **Dependencies**: ReportAdminService (via useAdminReports hook)

- **Features**:

Columns: 신고 일시, 신고 유형, 신고 대상, 신고자, 처리 상태, 작업 (Mock 데이터)

- Filters: 상태별 (전체/접수됨/검토 중/처리 완료/기각), 유형별 (6가지)

- Sort: 최신순/오래된순

- Row click → ReportDetailModal

**Note**: 실제 데이터 연동 전 Mock 데이터로 화면 확인 (change-2026-01-21-1135)

**Validation**: ✓ Validated - Admin report table only

#### 

- **Primary Responsibility**: Renders report detail with processing actions (admin) or status view (user). Displays mock data (change-2026-01-21-1135).

- **Type**: Component

- **User Stories**: US-030, US-031, US-032

- **Dependencies**: ReportAdminService, SanctionService

- **Props**:

`reportId: string`

- `mode: 'admin' | 'user'`

- `onClose: () => void`

**Admin Features**:

- Report content display (Mock 데이터)

- Process actions: 기각, 경고, 정지 (Sanction 연동)

- Admin note input

**User Features**:

- Report content display

- Status display

- Admin feedback (처리 완료 시)

**Validation**: ✓ Validated - Report detail modal only

#### ← NEW (change-2026-01-21-1135)

- **Primary Responsibility**: Renders report button with text and icon for improved UX and larger click area.

- **Type**: UI Component (Reusable)

- **User Stories**: US-029

- **Dependencies**: ReportModal, useReportSubmit

- **Props**:

`targetId: string` (story/user id)

- `targetType: 'story' | 'user'`

- `className?: string` (optional styling)

**Features**:

- 텍스트 + 아이콘 표시 (change-2026-01-21-1135)

- 버튼 형태의 클릭 영역 확대 (change-2026-01-21-1135)

- ReportModal 트리거

**Used By**: StoryDetail, ProposalCard

**Validation**: ✓ Validated - Report button component only

#### 

- **Primary Responsibility**: Custom hook for report submission with loading, error, and success states.

- **Type**: Hook

- **User Stories**: US-029

- **Dependencies**: ReportService

- **Returns**: `{ submit, isLoading, error, isSuccess }`

- **Validation**: ✓ Validated - Report submission hook only

#### 

- **Primary Responsibility**: Custom hook for fetching and managing user's own report history.

- **Type**: Hook

- **User Stories**: US-030

- **Dependencies**: ReportService

- **Returns**: `{ reports, isLoading, error, refetch }`

- **Validation**: ✓ Validated - User reports hook only

#### 

- **Primary Responsibility**: Custom hook for admin report management with filtering, pagination, and processing.

- **Type**: Hook

- **User Stories**: US-031, US-032

- **Dependencies**: ReportAdminService

- **Returns**: `{ reports, filters, setFilters, processReport, isLoading, error }`

- **Validation**: ✓ Validated - Admin reports hook only

---

## Phase 2: Requirement Mapping

### User Story to Component Mapping

User StoryPrimary Component(s)Supporting Component(s)US-001AuthenticationService, UserProfileServiceSignupForm, ProfileSetupForm, SocialLoginButtonsUS-002AuthenticationServiceLoginForm, SocialLoginButtonsUS-003WalletServiceBalanceCardUS-004TransactionServiceTransactionListUS-005PaymentServiceInkShopUS-006StoryDiscoveryServiceStoryBanner, StoryList, StoryCard, StoryDetailUS-007SearchService, StoryDiscoveryServiceStoryListUS-008EpisodeViewerServiceEpisodeViewer, ContentRenderer, ActionBarUS-009ProposalService, EscrowServiceProposalModalUS-010BackingService, EscrowServiceBackingModalUS-011BackingServiceDirectBackingModalUS-012CreditDisplayServiceCreditSectionUS-013ProposalServiceProposalList, ProposalCardUS-014EpisodeManagementService, ContentUploadServiceEpisodeEditor, ContentUploaderUS-015EpisodeManagementServiceEpisodeList (Creator)US-016ProposalDecisionServiceProposalManagementListUS-017ProposalDecisionService, EscrowServiceDealConfirmModalUS-018ProposalDecisionService, EscrowServiceDropConfirmModalUS-019EpisodeManagementService, CreditGenerationServiceEpisodeEditorUS-020SettlementServiceSettlementSummary, SettlementListUS-021WithdrawalServiceWithdrawalModalUS-022UserAdminServiceUserTableUS-023SanctionServiceSanctionModalUS-024ContentModerationServiceContentTable, DeleteConfirmModalUS-025SettlementApprovalServiceSettlementTable, SettlementDetailModalUS-026SettlementApprovalServiceSettlementDetailModalUS-027PaymentAdminServicePaymentTableUS-028DashboardStatsServiceDashboardStatsUS-029ReportServiceReportModal, useReportSubmitUS-030ReportServiceMyReportList, ReportDetailModal, useMyReportsUS-031ReportAdminServiceReportTable, ReportDetailModal, useAdminReportsUS-032ReportAdminService, SanctionServiceReportDetailModal

---

### Component Dependency Graph

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SERVICE DEPENDENCIES                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  AuthenticationService ──────────────────────────┐                          │
│         │                                        │                          │
│         ▼                                        │                          │
│  UserProfileService                              │                          │
│                                                  │                          │
│  WalletService ◄──────────────────────────────── │                          │
│         │                                        │                          │
│         ├──► TransactionService                  │                          │
│         │                                        │                          │
│         └──► PaymentService                      │                          │
│                                                  │                          │
│  ProposalService ────┬──► EscrowService ◄──── BackingService               │
│                      │                                                      │
│                      └──► ProposalDecisionService                          │
│                                     │                                       │
│                                     └──► EscrowService                      │
│                                     └──► CreditGenerationService            │
│                                                                             │
│  EpisodeManagementService ──► ContentUploadService                          │
│                                                                             │
│  SettlementService ◄──────────────── WithdrawalService                     │
│                                                                             │
│  SettlementApprovalService (Admin) ──► WithdrawalService (internal)         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Phase 3: Data Flow Diagrams

### Flow 1: User Authentication

```
┌──────────────┐    ┌─────────────┐    ┌───────────────────────┐    ┌─────────┐
│  LoginForm   │───►│ useAuth     │───►│ AuthenticationService │───►│   API   │
│  [DEPRECATED │    │ hook        │    │                       │    │         │
│  SignupForm] │    │             │    │                       │    │         │
└──────────────┘    └─────────────┘    └───────────────────────┘    └─────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  AuthStore  │
                    └─────────────┘

[DEPRECATED - change-2026-02-04-1450: SignupForm replaced by social login auto-signup]
```
### Flow 2: Proposal Creation & Backing

```
┌───────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   ProposalModal   │───►│ useProposal hook │───►│ ProposalService │
└───────────────────┘    └──────────────────┘    └────────┬────────┘
                                                          │
                                                          ▼
┌───────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   BackingModal    │───►│ useBacking hook  │───►│ BackingService  │
└───────────────────┘    └──────────────────┘    └────────┬────────┘
                                                          │
                                                          ▼
                                                 ┌─────────────────┐
                                                 │  EscrowService  │
                                                 └─────────────────┘
                                                          │
                                                          ▼
                                                 ┌─────────────────┐
                                                 │  WalletService  │ (balance update)
                                                 └─────────────────┘
```
### Flow 3: Deal/Drop Decision

```
┌───────────────────────┐    ┌────────────────────┐    ┌─────────────────────────┐
│ ProposalManagementList│───►│ useProposalDecision│───►│ ProposalDecisionService │
└───────────────────────┘    │ hook               │    └───────────┬─────────────┘
         │                   └────────────────────┘                │
         ▼                                                         │
┌───────────────────────┐                                          │
│ DealConfirmModal      │◄─────────────────────────────────────────┤
│ DropConfirmModal      │                                          │
└───────────────────────┘                                          │
                                                                   ▼
                                              ┌─────────────────────────────────┐
                                              │        EscrowService            │
                                              │ (release for Deal / refund Drop)│
                                              └─────────────────────────────────┘
                                                                   │
                                       ┌───────────────────────────┼───────────────────────────┐
                                       ▼                           ▼                           ▼
                              ┌─────────────────┐         ┌─────────────────┐         ┌─────────────────┐
                              │  WalletService  │         │ CreditGeneration│         │ TransactionLog  │
                              │ (creator balance)│         │ (if publish)    │         │                 │
                              └─────────────────┘         └─────────────────┘         └─────────────────┘
```
### Flow 4: Episode Publishing

```
┌───────────────────┐    ┌─────────────────────┐    ┌──────────────────────────┐
│   EpisodeEditor   │───►│ useEpisodeManagement│───►│ EpisodeManagementService │
└───────────────────┘    │ hook                │    └────────────┬─────────────┘
         │               └─────────────────────┘                 │
         ▼                                                       │ (publish)
┌───────────────────┐                                            │
│  ContentUploader  │───►ContentUploadService                    │
└───────────────────┘                                            ▼
                                                   ┌──────────────────────────────┐
                                                   │   CreditGenerationService    │
                                                   │ (auto-generate credits)      │
                                                   └──────────────────────────────┘
                                                                 │
                                                                 ▼
                                                   ┌──────────────────────────────┐
                                                   │      SettlementService       │
                                                   │ (payable → withdrawable)     │
                                                   └──────────────────────────────┘
```

---

## Phase 2 Continued: Additional Components

### 11. Layout & Navigation Domain

#### 

- **Primary Responsibility**: Renders the global top navigation bar with 3-zone layout (Left: hamburger+logo / Center: search / Right: account button).

- **Type**: Component

- **User Stories**: Navigation structure (change-2026-01-09-1137)

- **Dependencies**: SearchBar, useAuth, uiStore, next/link

- **Props**:

None (global component)

**State**:

- `sidebarOpen: boolean` (from uiStore) - 사이드바 토글 상태

- `isAuthenticated: boolean` (from useAuth) - 로그인 상태

**Interfaces**:

- Hamburger button → toggles sidebar via uiStore

- Logo (small.png) → routes to `/home`

- SearchBar integration (center, Desktop/Tablet only)

- Account button → `/login` (비로그인) or `/mypage` (로그인)

- Responsive: Desktop/Tablet shows search input, Mobile shows search icon

- Sticky positioning across all screen sizes

**Validation**: ✓ Validated - Single responsibility (global navigation bar with 3-zone layout)

#### 

- **Primary Responsibility**: Renders search input with real-time search popup and responsive icon mode for mobile.

- **Type**: Component

- **User Stories**: Navigation structure (change-2026-01-09-1137)

- **Dependencies**: useRouter, useSearch, SearchPopup

- **Props**:

`placeholder?: string` - Placeholder text (default: "검색어를 입력하세요")

- `className?: string` - Additional styling

- `mode?: 'input' | 'icon'` - Display mode (Desktop: input, Mobile: icon)

**State**:

- `query: string` - Current search input

- `showPopup: boolean` - Real-time search popup visibility

- `showMobileInput: boolean` - Mobile slide-down input visibility

**Interfaces**:

- 1자 이상 입력 → SearchPopup 표시 (debounce 적용)

- Enter key → `/search?q={query}` 라우팅, popup 닫힘

- Mobile icon 클릭 → 슬라이드다운 검색 input 표시

- 외부 클릭 → popup 닫힘

**Validation**: ✓ Validated - Single responsibility (search input with preview popup)

#### 

- **Primary Responsibility**: Renders real-time search results popup below search input with preview items.

- **Type**: Component

- **User Stories**: Navigation structure (change-2026-01-09-1137)

- **Dependencies**: useSearch, StoryCard (mini variant)

- **Props**:

`query: string` - Current search query

- `onSelect?: (story: Story) => void` - Item selection handler

- `onClose?: () => void` - Close handler

**State**:

- None (controlled component, data from useSearch)

**Interfaces**:

- 최대 5~10개 결과 표시

- 각 아이템: 썸네일, 작품명, 작가명, 조회수

- 결과 없음: "검색 결과가 없습니다" 문구

- 아이템 클릭 → 해당 작품 상세 페이지로 이동

**Validation**: ✓ Validated - Single responsibility (real-time search results preview)

#### 

- **Primary Responsibility**: Renders bottom navigation for mobile devices with responsive visibility (hidden on desktop).

- **Type**: Component

- **User Stories**: Navigation structure

- **Dependencies**: next/link, next/navigation

- **Props**:

None

**State**:

- `pathname: string` - Current route for active state

**Interfaces**:

- Three nav items: Home, Explore, MyPage

- Active state indication

- Mobile-only display (`lg:hidden`)

**Validation**: ✓ Validated - Single responsibility (mobile bottom navigation)

#### 

- **Primary Responsibility**: Renders side navigation for desktop devices with role-specific menu items.

- **Type**: Component

- **User Stories**: Navigation structure

- **Dependencies**: next/link, next/navigation

- **Props**:

`title: string` - Navigation title

- `items: NavItem[]` - Navigation menu items

- `backHref?: string` - Back link destination

- `backLabel?: string` - Back link label

**State**:

- `pathname: string` - Current route for active state

**Interfaces**:

- Flexible nav items configuration

- Desktop-only display (`hidden lg:flex`)

- Supports Reader, Creator, and Admin modes

**Validation**: ✓ Validated - Single responsibility (desktop side navigation)

---

## Validation Summary

### Component Count by Domain

DomainServicesComponentsHooksTotalAuthentication24-6Wallet33-6Story Discovery25-7Episode Viewer25-7Intervention36-9Creator Studio47-11Settlement23-5Admin68-14Report (change-2026-01-12-1530)2439Layout & Navigation05-5**Total2650379**

### Validation Checklist

- All 3 phases executed in order

- Every component marked "✓ Validated"

- Every Primary Responsibility follows one-sentence active-verb rule

- All US-XXX identifiers mapped to components (32/32)

- Data flows complete and accurate

- No component has >5 responsibilities

- All component names are specific nouns

### User Story Coverage

- **Total User Stories**: 32

- **Mapped to Components**: 32

- **Coverage**: 100%

---

## 문서 정보

- **생성일:** 2026-01-06

- **기반 문서:** user_stories_data.json, conceptual_model.json, ia_structure.md