# F05: 지갑/결제

## 1. Feature Overview

**Purpose**: 독자가 Ink 재화를 관리하고 충전할 수 있는 기능. 잔액 조회, 트랜잭션 내역 확인, PG 결제를 통한 Ink 구매를 포함한다.

**Scope Boundary**:
- **IN**:
  - Ink 잔액 조회 (Available, Pending, Total)
  - Ink 트랜잭션 내역 조회
  - 진행 중인 제안에 묶인 Ink 상세 내역
  - 완료된 제안의 정산 내역
  - PG를 통한 Ink 충전 (토스/페이플)
  - 마이페이지 UI 및 Ink 충전 바로가기
- **OUT**:
  - 작가 정산금 관리 (별도 feature: 크리에이터 스튜디오)
  - 에스크로 관리 로직 (별도 feature: 인터벤션)
  - 관리자 결제 내역 조회 (별도 feature: 관리자)

---

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | 독자 | Ink 잔액 확인 | 마이페이지 → 내 지갑 → 잔액 표시 (Available/Pending/Total) |
| UF-02 | 독자 | 진행 중인 제안 내역 확인 | 내 지갑 → Pending Ink 클릭 → 제안 내역 리스트 → 항목 클릭 시 에피소드로 이동 |
| UF-03 | 독자 | 완료된 제안 정산 내역 확인 | 내 지갑 → 정산 내역 탭/필터 → 채택/미채택 결과 확인 |
| UF-04 | 독자 | 트랜잭션 내역 조회 | 내 지갑 → 트랜잭션 리스트 (무한 스크롤) → 충전/사용/반환 내역 확인 |
| UF-05 | 독자 | Ink 충전 | 마이페이지 → Ink 충전 바로가기 → Shop 페이지 → 충전 옵션 선택 → PG 결제 → 잔액 증가 |

Source: user_stories.md (US-003, US-003-1, US-003-2, US-004, US-005)

---

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| MyPage | `/mypage` | 사용자 프로필 및 주요 기능 진입점 (Ink 충전 바로가기 포함) |
| Wallet | `/mypage/wallet` | Ink 잔액 및 트랜잭션 내역 조회 |
| PendingProposals | `/mypage/wallet/pending` | 에스크로에 묶인 Ink 상세 내역 조회 |
| InkShop | `/shop` | Ink 충전 및 PG 결제 |

Source: ia_structure.md

---

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| **Wallet** | userId, currentBalance, payableBalance (작가용), withdrawableBalance (작가용) | → User (N:1), → Transaction (1:N) |
| **Transaction** | walletId, type (CHARGE/USE_PROPOSAL/USE_BACKING/USE_DIRECT/REFUND/SETTLEMENT/WITHDRAWAL), amount, direction (IN/OUT), referenceType, referenceId | → Wallet (N:1) |
| **Payment** | userId, inkAmount, paymentAmount, paymentMethod (CARD/KAKAO_PAY/TOSS/PAYPLE), pgTransactionId, status (PENDING/COMPLETED/FAILED/CANCELLED) | → User (N:1) |

**Domain Rules**:
- **DR-003**: 잔액 초과 사용 불가 (Transaction.amount <= Wallet.currentBalance)
- **Pending Ink**: Proposal/Backing으로 에스크로에 묶여있는 금액 합계 (별도 계산)
- **Total Ink**: Available Ink + Pending Ink

Source: conceptual_model.json

---

## 5. Component Strategy

**Pages**:
- `MyPage` (`app/(auth)/mypage/page.tsx`) - 프로필 정보 및 Ink 충전 바로가기 UI 제공
- `WalletPage` (`app/(auth)/mypage/wallet/page.tsx`) - 잔액 표시, 트랜잭션 내역, 정산 내역 탭
- `PendingProposalsPage` (`app/(auth)/mypage/wallet/pending/page.tsx`) - 진행 중인 제안 내역 리스트 (미구현, 향후 확장)
- `ShopPage` (`app/(auth)/shop/page.tsx`) - Ink 충전 옵션 및 PG 결제

**Components**:
- `BalanceCard` (`components/wallet/BalanceCard.tsx`) - 잔액 표시 카드 (Available/Pending/Total)
- `TransactionList` (`components/wallet/TransactionList.tsx`) - 트랜잭션 내역 리스트 (무한 스크롤, 필터링)
- `InkShop` (`components/wallet/InkShop.tsx`) - Ink 구매 옵션 카드 및 결제 플로우 시작

**Services**:
- `WalletService` (`services/walletService.ts`) - 지갑 잔액 조회 (getBalance, getCreatorBalance)
- `TransactionService` (`services/transactionService.ts`) - 트랜잭션 내역 조회 (getTransactions, getTransactionById)
- `PaymentService` (`services/paymentService.ts`) - Ink 충전 결제 처리 (createPayment, verifyPayment, getPaymentHistory)

**State**:
- TanStack Query hooks (`useWallet`, `useTransactions`, `usePayment`)로 데이터 fetching 및 캐싱
- Zustand 스토어 불필요 (서버 상태 관리만 사용)

Source: logical_architecture.md

---

## 6. Task Mapping

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| **task-040** | MyPage UI | 마이페이지 화면 (프로필 정보, Ink 충전 바로가기 UI) | `app/(auth)/mypage/page.tsx` |
| **task-041** | Wallet Page UI | 내 지갑 화면 (잔액 표시, 트랜잭션 내역) | `app/(auth)/mypage/wallet/page.tsx` |
| **task-042** | BalanceCard 컴포넌트 | 잔액 표시 카드 (Available/Pending/Total Ink) | `components/wallet/BalanceCard.tsx` |
| **task-043** | TransactionList 컴포넌트 | 트랜잭션 내역 리스트 (무한 스크롤, 필터) | `components/wallet/TransactionList.tsx` |
| **task-044** | Shop Page UI | Ink 충전 상점 화면 | `app/(auth)/shop/page.tsx` |
| **task-045** | InkShop 컴포넌트 | Ink 충전 옵션 카드 및 결제 플로우 | `components/wallet/InkShop.tsx` |
| **task-046** | Wallet Service (Mock API) | 지갑 잔액 조회 API (getBalance, getCreatorBalance) | `services/walletService.ts` |
| **task-047** | Transaction Service (Mock API) | 트랜잭션 내역 조회 API (getTransactions, getTransactionById) | `services/transactionService.ts` |
| **task-048** | Payment Service (Mock API) | 결제 처리 API (createPayment, verifyPayment, getPaymentHistory) | `services/paymentService.ts` |

Source: tasks.json

---

## 7. Open Questions

1. **Pending Ink 계산 로직**: Proposal/Backing으로 에스크로에 묶인 금액을 백엔드에서 집계하는지, 프론트에서 계산하는지 확인 필요
2. **PG 결제 연동**: 토스/페이플 중 어떤 PG를 우선 구현할지, 테스트 환경 설정은 어떻게 할지 확인 필요
3. **트랜잭션 내역 필터**: 충전/사용/반환/정산 등 유형별 필터링 UI를 제공할지 확인 필요
4. **진행 중인 제안 내역 페이지**: US-003-1에 명시된 `/mypage/wallet/pending` 페이지를 이번 feature에서 구현할지, 향후 인터벤션 feature와 함께 구현할지 확인 필요

---

## Implementation Notes

### Development Sequence (권장)
1. **Service Layer 먼저**: task-046 (WalletService) → task-047 (TransactionService) → task-048 (PaymentService)
2. **Component Layer**: task-042 (BalanceCard) → task-043 (TransactionList) → task-045 (InkShop)
3. **Page Layer**: task-040 (MyPage) → task-041 (Wallet) → task-044 (Shop)

### Design Considerations
- **Monochrome Design**: CLAUDE-FE-DEV.md에 따라 흑백 디자인 적용
- **Mock Data**: 모든 Service는 Mock API로 구현 (실제 PG 연동 제외)
- **Image Handling**: 썸네일 등 이미지는 회색 박스(bg-muted)로 표시

### Integration Points
- **BottomNav**: MyPage는 하단 네비게이션 "마이페이지" 탭에서 접근
- **Ink 충전 바로가기**: MyPage에서 `/shop`으로 이동하는 UI 제공 (change-2026-01-07-1200)
- **Pending Ink 클릭**: BalanceCard의 Pending Ink 영역 클릭 시 향후 `/mypage/wallet/pending`으로 이동 (현재는 placeholder)
