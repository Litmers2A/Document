# F11: 결제

## 1. Feature Overview

**Purpose**: Ink 충전 결제 플로우에서 결제하기 화면과 결제 완료 화면을 제공하여, 사용자가 다양한 결제 수단으로 Ink를 충전하고 충전 결과 및 영수증을 확인할 수 있게 한다.

**Scope Boundary**:
- IN: 결제하기 페이지 UI, 결제 완료 페이지 UI, 결제 수단 선택, 약관 동의, PG 연동 Mock, 영수증 다운로드
- OUT: 실제 PG 결제 연동 (Mock만 구현), 결제 내역 히스토리 페이지, 자동 충전 기능

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | Reader | Ink 충전 결제 | Shop에서 금액 선택 → 결제하기 클릭 → Payment 페이지 → 결제수단 선택 → 약관 동의 → 결제 버튼 클릭 → PG 처리 → 완료 페이지 |
| UF-02 | Reader | 결제 결과 확인 | 결제 완료 후 → 충전 결과 확인 → 영수증 다운로드 (선택) → 확인 버튼으로 이동 |
| UF-03 | Reader | 결제 실패 처리 | 결제 시도 → 실패 → 에러 모달 표시 → 재시도 또는 취소 |

Source: user_stories_data.json (US-005)

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| Payment | /shop/payment | 결제 수단 선택, 약관 동의, 결제 진행 |
| Payment Complete | /shop/payment/complete | 결제 성공 상태, 결제 내역, 충전 결과 표시 |
| Shop (수정) | /shop | InkShop 컴포넌트에서 결제 페이지 연결 추가 |

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| Payment | id, userId, inkAmount, bonusInkAmount, paymentAmount, paymentMethod, pgTransactionId, status, createdAt, completedAt | → User |
| Wallet | currentBalance, updatedAt | ← User (1:1) |
| Transaction | type (CHARGE), amount, direction (IN), referenceType (PAYMENT) | → Wallet |

**Payment Method Enum**: CARD, BANK_TRANSFER, VIRTUAL_ACCOUNT, TOSS, KAKAO_PAY, PAYPLE

**Payment Status Enum**: PENDING, COMPLETED, FAILED, CANCELLED

Source: conceptual_model.json

## 5. Component Strategy

**Pages**:
- `PaymentPage` (/shop/payment) - 결제 정보 입력 및 결제 처리 페이지
- `PaymentCompletePage` (/shop/payment/complete) - 결제 완료 상태 및 결과 확인 페이지

**Components**:
- `OrderInfoCard` - 주문 상품/가격 정보 카드 (Ink 수량, 보너스, 최종 금액)
- `PaymentMethodSelector` - 결제 수단 Radio 선택 (카드/계좌이체/무통장/간편결제)
- `PaymentTermsAgreement` - 결제 약관 동의 체크박스
- `PaymentCTA` - 금액 표시 결제 버튼 (조건부 활성화)
- `PaymentSuccessStatus` - 결제 성공 상태 표시 (체크 아이콘, 완료 문구)
- `PaymentReceiptCard` - 결제 내역 카드 (Ink, 결제수단, 일시, 거래번호)
- `ChargeResultCard` - 충전 결과 카드 (현재 보유 Ink)
- `PaymentErrorModal` - 결제 실패 에러 모달 (재시도 버튼)
- `InkShop` (수정) - 결제하기 버튼 → /shop/payment 이동 연결

**Hooks**:
- `usePaymentProcess` - 결제 처리 로직 (PG 연동, 상태 관리, 중복 방지)
- `useReceiptDownload` - 영수증 PDF 다운로드

**State**:
- 결제 완료 후 walletStore Ink 잔액 동기화
- 결제 처리 중 isProcessing 상태로 중복 결제 방지

Source: logical_architecture.md

## 6. Task Mapping

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-045 | InkShop 컴포넌트 재작업 | 결제하기 버튼 → /shop/payment 연결 | components/wallet/InkShop.tsx |
| task-048 | Payment Service 확장 | PG 결제 Mock API 확장 | services/paymentService.ts |
| task-133 | OrderInfoCard 컴포넌트 | 주문 상품/가격 정보 카드 | components/payment/OrderInfoCard.tsx |
| task-134 | PaymentMethodSelector 컴포넌트 | 결제 수단 Radio 선택 | components/payment/PaymentMethodSelector.tsx |
| task-135 | PaymentTermsAgreement 컴포넌트 | 결제 약관 동의 체크박스 | components/payment/PaymentTermsAgreement.tsx |
| task-136 | PaymentCTA 컴포넌트 | 금액 표시 결제 버튼 | components/payment/PaymentCTA.tsx |
| task-137 | Payment Complete Page UI | 결제 완료 화면 | app/(auth)/shop/payment/complete/page.tsx |
| task-138 | PaymentSuccessStatus 컴포넌트 | 결제 성공 상태 표시 | components/payment/PaymentSuccessStatus.tsx |
| task-139 | PaymentReceiptCard 컴포넌트 | 결제 내역 카드 | components/payment/PaymentReceiptCard.tsx |
| task-140 | ChargeResultCard 컴포넌트 | 충전 결과 카드 | components/payment/ChargeResultCard.tsx |
| task-141 | PaymentErrorModal 컴포넌트 | 결제 실패 에러 모달 | components/payment/PaymentErrorModal.tsx |
| task-142 | usePaymentProcess Hook 구현 | 결제 처리 로직 | hooks/usePaymentProcess.ts |
| task-143 | useReceiptDownload Hook 구현 | 영수증 PDF 다운로드 | hooks/useReceiptDownload.ts |
| task-144 | Payment Service 확장 (PG Mock) | PG Mock API | services/paymentService.ts |
| task-132 | Payment Page UI | 결제하기 화면 | app/(auth)/shop/payment/page.tsx |
| task-145 | InkShop 결제 페이지 연결 수정 | InkShop 버튼 연결 | components/wallet/InkShop.tsx |

Source: tasks.json

## 7. Dependencies

**Task Execution Order** (respecting dependencies):
1. **Phase 1 - Independent Components** (병렬 가능):
   - task-133 (OrderInfoCard)
   - task-134 (PaymentMethodSelector)
   - task-135 (PaymentTermsAgreement)
   - task-136 (PaymentCTA)
   - task-138 (PaymentSuccessStatus)
   - task-139 (PaymentReceiptCard)
   - task-140 (ChargeResultCard)
   - task-141 (PaymentErrorModal)

2. **Phase 2 - Service Layer**:
   - task-048 → task-144 (Payment Service 확장)

3. **Phase 3 - Hooks** (depends on task-144):
   - task-142 (usePaymentProcess)
   - task-143 (useReceiptDownload)

4. **Phase 4 - Pages** (depends on components + hooks):
   - task-132 (Payment Page) - depends on task-133, 134, 135, 136
   - task-137 (Payment Complete Page) - depends on task-138, 139, 140

5. **Phase 5 - Integration**:
   - task-045 + task-145 (InkShop 연결) - depends on task-132

## 8. Design Guidelines

From DESIGN.md:
- Monochrome 컬러 시스템 적용
- 금액/Ink 수량 Bold 처리 (typo-card-title)
- CTA 버튼: Black Fill (bg-foreground text-background)
- 카드형 UI: Card 컴포넌트 사용 (rounded-lg border)
- 최대 가로폭: 1439px

## 9. Open Questions

- [x] TBD-001: PG 연동 방식 → Mock 시뮬레이션으로 결정
- [ ] TBD-002: 결제 완료 후 리다이렉트 경로 (이전 화면 vs 메인 vs Shop) → 확인 버튼으로 Shop 이동
- [ ] TBD-003: 영수증 PDF 생성 방식 → 클라이언트 사이드 Mock으로 구현
- [ ] TBD-004: 결제 상태 polling 주기 → Mock이므로 즉시 응답
