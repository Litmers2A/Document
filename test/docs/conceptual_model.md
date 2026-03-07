# Conceptual Model - 쟈근친구들 V2 (Conture)

## 프로젝트 개요

**프로젝트명:** 쟈근친구들 V2 (Conture)
**설명:** 작가가 스토리 초안(콘티)을 올리면, 팬들이 창작에 참여하고 기여를 크레딧으로 인정받는 참여형 플랫폼

---

## Entity Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                  USER DOMAIN                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    1:1    ┌──────────┐    1:N    ┌─────────────┐             │
│  │   User   │──────────▶│  Wallet  │──────────▶│ Transaction │             │
│  └──────────┘           └──────────┘           └─────────────┘             │
│       │                                                                     │
│       ├─── 1:N ──▶ ┌──────────┐                                            │
│       │            │ Sanction │                                            │
│       │            └──────────┘                                            │
│       │                 ▲                                                   │
│       │                 │ N:1 (연동)                                        │
│       │                 │                                                   │
│       └─── 1:N ──▶ ┌──────────┐                                            │
│        (reporter)  │  Report  │◀── N:1 ── User (reported)                  │
│                    └──────────┘                                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                                CONTENT DOMAIN                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    1:N    ┌──────────┐    1:N    ┌────────────────┐          │
│  │  Story   │──────────▶│ Episode  │──────────▶│ EpisodeContent │          │
│  └──────────┘           └──────────┘           └────────────────┘          │
│       ▲                      │                                              │
│       │                      │ 1:1                                          │
│  N:1  │                      ▼                                              │
│  ┌──────────┐           ┌──────────┐                                        │
│  │   User   │           │  Credit  │                                        │
│  │(Creator) │           └──────────┘                                        │
│  └──────────┘                                                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                             INTERVENTION DOMAIN                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    1:N    ┌──────────┐    1:1    ┌──────────┐                │
│  │ Episode  │◀─────────│ Proposal │──────────▶│  Escrow  │                │
│  └──────────┘           └──────────┘           └──────────┘                │
│                              │                                              │
│                              │ 1:N                                          │
│                              ▼                                              │
│                         ┌──────────┐                                        │
│                         │ Backing  │                                        │
│                         └──────────┘                                        │
│                              ▲                                              │
│                              │ N:1                                          │
│                         ┌──────────┐                                        │
│                         │   User   │                                        │
│                         │ (Reader) │                                        │
│                         └──────────┘                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                              PAYMENT DOMAIN                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    N:1    ┌──────────┐                                        │
│  │ Payment  │──────────▶│   User   │                                        │
│  └──────────┘           └──────────┘                                        │
│                                                                             │
│  ┌───────────────────┐    N:1    ┌──────────┐                              │
│  │ WithdrawalRequest │──────────▶│   User   │                              │
│  └───────────────────┘           │(Creator) │                              │
│                                  └──────────┘                              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Entity Definitions

### 1. User (사용자)

플랫폼을 사용하는 모든 사용자 (독자, 작가, 관리자)

AttributeTypeRequiredDescriptionidUUIDO사용자 고유 식별자emailStringO이메일 주소 (소셜 로그인 시 제공되는 경우에만 저장, change-2026-02-04-1450)passwordStringX암호화된 비밀번호 (소셜 로그인 전용, change-2026-02-04-1450)nicknameStringO닉네임 (온보딩 필수 입력: 2-12자, 한글/영문/숫자, 중복 불가, change-2026-02-05-0918)profileImageUrlStringX프로필 이미지 URL (미설정 시 기본 아바타)creatorBioStringX작가 소개글 (작가 전용, 500~1000자, Plain Text)roleEnumO사용자 역할authProviderEnumO인증 방식 (소셜 로그인 전용, change-2026-02-04-1450)statusEnumO계정 상태onboardingCompletedBooleanO온보딩 완료 여부 (닉네임 + 휴대폰 인증 완료 시 true, change-2026-02-05-0918)isVerifiedBooleanO본인인증 완료 여부phoneNumberStringO휴대폰 번호 (온보딩 필수, 중복 불가, change-2026-02-05-0918)phoneVerifiedAtDateTimeX휴대폰 인증 완료 일시 (change-2026-02-05-0918)isPhoneVerifiedBooleanO휴대폰 인증 완료 여부 (change-2026-01-14-1500)ageVerifiedBooleanO성인 인증 완료 여부 (휴대폰 인증 기반, 성인 콘텐츠 접근용, change-2026-02-04-1450)consentsJSONO약관 동의 상태 {serviceTerms, privacyPolicy, communityRules, marketingConsent}createdAtDateTimeO가입일시updatedAtDateTimeO정보 수정일시

**Enum Values:**

- `role`: READER, CREATOR, ADMIN

- `authProvider`: KAKAO, GOOGLE (change-2026-02-04-1450: EMAIL 제거, 소셜 전용)

- `status`: ACTIVE, SUSPENDED, DELETED

**Relationships:**

- 1:1 → Wallet (사용자는 하나의 지갑을 보유)

- 1:N → Story (작가는 여러 작품을 소유)

- 1:N → Proposal (독자는 여러 제안을 생성)

- 1:N → Backing (독자는 여러 지지/후원을 수행)

- 1:N → WithdrawalRequest (작가는 여러 출금 요청을 생성)

**관련 User Story:** US-001, US-002, US-022, US-023, US-033, US-034, US-035

---

### 2. Wallet (지갑)

사용자의 Ink 재화를 관리하는 지갑

AttributeTypeRequiredDescriptionidUUIDO지갑 고유 식별자userIdUUIDO소유자 사용자 IDavailableBalanceIntegerO사용 가능한 Ink 잔액 (즉시 사용 가능)pendingBalanceIntegerO진행 중인 제안에 묶인 Ink (Proposal/Backing 에스크로)totalBalanceIntegerO총 보유 Ink (availableBalance + pendingBalance, 계산 필드)payableBalanceIntegerO정산 예정금 (작가용)withdrawableBalanceIntegerO출금 가능 금액 (작가용)updatedAtDateTimeO잔액 갱신일시

**Balance 계산 규칙:**

- `availableBalance`: 후원/제안에 즉시 사용할 수 있는 Ink

- `pendingBalance`: Proposal 생성 또는 Backing 시 에스크로로 이동한 Ink의 합계

- `totalBalance` = `availableBalance` + `pendingBalance`

- Proposal/Backing 생성 시: `availableBalance` 감소, `pendingBalance` 증가

- Proposal 채택/미채택 정산 시: `pendingBalance` 감소, 결과에 따라 `availableBalance` 증가 또는 작가에게 전달

**Relationships:**

- N:1 → User (지갑은 하나의 사용자에 속함)

- 1:N → Transaction (지갑은 여러 트랜잭션 내역을 보유)

**관련 User Story:** US-003, US-003-1, US-003-2, US-004, US-020

---

### 3. Transaction (트랜잭션)

Ink 재화의 모든 흐름을 기록하는 불변 로그

AttributeTypeRequiredDescriptionidUUIDO트랜잭션 고유 식별자walletIdUUIDO관련 지갑 IDtypeEnumO트랜잭션 유형amountIntegerO금액 (양수)directionEnumO입출금 방향referenceTypeEnumX참조 엔티티 유형referenceIdUUIDX참조 엔티티 IDdescriptionStringX트랜잭션 설명createdAtDateTimeO발생일시

**Enum Values:**

- `type`: CHARGE, USE_PROPOSAL, USE_BACKING, USE_DIRECT, REFUND, SETTLEMENT, WITHDRAWAL

- `direction`: IN, OUT

- `referenceType`: PAYMENT, PROPOSAL, BACKING, EPISODE, WITHDRAWAL_REQUEST

**관련 User Story:** US-004

---

### 4. Payment (결제)

실제 화폐로 Ink를 구매한 결제 내역. 1 Ink = 100원 고정 (change-2026-01-15-1000)

AttributeTypeRequiredDescriptionidUUIDO결제 고유 식별자userIdUUIDO결제자 IDinkAmountIntegerO충전된 Ink 양 (구매 옵션: 10, 20, 30, 100 Ink)paymentAmountIntegerO결제 금액 (원). inkAmount × 100paymentMethodEnumO결제 수단pgTransactionIdStringOPG사 거래 IDstatusEnumO결제 상태createdAtDateTimeO결제 요청일시completedAtDateTimeX결제 완료일시

**Enum Values:**

- `paymentMethod`: CARD, BANK_TRANSFER, VIRTUAL_ACCOUNT, TOSS, KAKAO_PAY, PAYPLE

- `status`: PENDING, COMPLETED, FAILED, CANCELLED

> ⚠️ **변경 이력 (change-2026-01-15-1000):** bonusInkAmount 필드 삭제. 보너스/할인/추천/이벤트 전부 제거. 1 Ink = 100원 고정, 구매 옵션 10/20/30/100 Ink로 변경.

**관련 User Story:** US-005, US-027

---

### 5. Story (작품)

작가가 연재하는 작품 (웹툰/웹소설). Episode를 묶는 컨테이너 역할만 수행 (change-2026-01-15-1000)

AttributeTypeRequiredDescriptionidUUIDO작품 고유 식별자creatorIdUUIDO작가 IDtitleStringO작품 제목descriptionStringX작품 소개thumbnailUrlStringX썸네일 이미지 URLtotalBountyIntegerO전체 에피소드 총 모금액createdAtDateTimeO생성일시updatedAtDateTimeO수정일시

> ⚠️ **변경 이력 (change-2026-01-15-1000):** Story에서 status 필드 완전 제거. Story는 Episode를 묶는 컨테이너 역할만 수행하며, 상태는 Episode 단위로만 관리됨. 기존 "연재중/휴재/완결" 개념 삭제.

**관련 User Story:** US-006, US-007, US-024

---

### 6. Episode (에피소드)

작품의 개별 회차 (콘티 또는 완성본). 상태의 유일한 기준 단위 (change-2026-01-15-1000)

AttributeTypeRequiredDescriptionidUUIDO에피소드 고유 식별자storyIdUUIDO소속 작품 IDepisodeNumberIntegerO회차 번호titleStringO에피소드 제목statusEnumO에피소드 상태totalBountyIntegerO해당 회차 총 모금액createdAtDateTimeO생성일시publishedAtDateTimeX공개일시

**Enum Values:**

- `status`: OPEN, PUBLISHED

> ⚠️ **변경 이력 (change-2026-01-15-1000):** Episode 상태 단순화. DRAFT/HIDDEN/DELETED 제거, OPEN(참여 가능)과 PUBLISHED(참여 종료+결과 공개)만 유지. 임시저장(Draft) 기능 삭제, OPEN 상태에서 저장 시 즉시 독자 화면에 반영.

**관련 User Story:** US-008, US-012, US-014, US-015, US-019

---

### 7. EpisodeContent (에피소드 콘텐츠)

에피소드를 구성하는 개별 콘텐츠 (이미지/텍스트)

AttributeTypeRequiredDescriptionidUUIDO콘텐츠 고유 식별자episodeIdUUIDO소속 에피소드 IDcontentTypeEnumO콘텐츠 유형contentUrlStringX이미지 URL (IMAGE 유형)textContentStringX텍스트 내용 (TEXT 유형)orderIndexIntegerO표시 순서isFinalBooleanO최종 원고 여부createdAtDateTimeO업로드일시

**Enum Values:**

- `contentType`: IMAGE, TEXT

**관련 User Story:** US-008, US-014, US-019

---

### 8. Proposal (제안)

독자가 스토리 전개에 개입하기 위해 생성한 제안

AttributeTypeRequiredDescriptionidUUIDO제안 고유 식별자episodeIdUUIDO대상 에피소드 IDproposerIdUUIDO제안자 (독자) IDdescriptionStringO제안 내용seedMoneyIntegerO최초 베팅액totalBountyIntegerO총 모금액backerCountIntegerO지지자 수statusEnumO제안 상태createdAtDateTimeO생성일시decidedAtDateTimeXDeal/Drop 결정일시

**Enum Values:**

- `status`: PENDING, DEAL, DROP

**관련 User Story:** US-009, US-013, US-016, US-017, US-018

---

### 9. Backing (지지/후원)

독자가 제안에 지지(Backer)하거나 직접 후원(Sponsor)한 기록. OPEN 상태에서 철회 가능 (change-2026-01-15-1000)

AttributeTypeRequiredDescriptionidUUIDO지지/후원 고유 식별자backerIdUUIDO지지자/후원자 (독자) IDtypeEnumO유형 (PROPOSAL_BACKING=Backer, DIRECT_BACKING=Sponsor)proposalIdUUIDX지지 대상 제안 ID (PROPOSAL_BACKING)episodeIdUUIDX직접 후원 대상 에피소드 ID (DIRECT_BACKING)amountIntegerO지지/후원 금액statusEnumO상태createdAtDateTimeO생성일시

**Enum Values:**

- `type`: PROPOSAL_BACKING (Backer), DIRECT_BACKING (Sponsor)

- `status`: ESCROWED, SETTLED, REFUNDED

> ⚠️ **변경 이력 (change-2026-01-15-1000):** Sponsor 용어 추가 (제안 없이 순수 후원). OPEN 상태에서 Proposal/Backing 철회 가능, Sponsor(DIRECT_BACKING)는 철회 불가.

**관련 User Story:** US-010, US-011

---

### 10. Credit (크레딧)

완성된 에피소드 하단에 표시되는 기여자 크레딧 데이터. Proposer+Backer+Sponsor 전원 노출 (change-2026-01-15-1000)

AttributeTypeRequiredDescriptionidUUIDO크레딧 고유 식별자episodeIdUUIDO소속 에피소드 IDcontributorsJSONO기여자 리스트 (nickname, amount, type: PROPOSER|BACKER|SPONSOR, rank). 전원 노출, 기여금액 내림차순mainProducersArray[UUID]O상위 3인 Main Producer 사용자 ID (기여금액 기준)createdAtDateTimeO생성일시 (영구 박제)

> ⚠️ **변경 이력 (change-2026-01-15-1000):** Proposer, Backer, Sponsor 전원 노출 (기존 Proposer/Backer만). 기여금액 내림차순 정렬.

**관련 User Story:** US-012, US-019

---

### 11. Escrow (에스크로)

제안/지지 시 시스템이 보관하는 에스크로 계정

AttributeTypeRequiredDescriptionidUUIDO에스크로 고유 식별자proposalIdUUIDO관련 제안 IDtotalAmountIntegerO보관 중인 총 금액statusEnumO에스크로 상태createdAtDateTimeO생성일시resolvedAtDateTimeX해제/환불 처리일시

**Enum Values:**

- `status`: HOLDING, RELEASED, REFUNDED

**관련 User Story:** US-009, US-010, US-017, US-018

---

### 12. WithdrawalRequest (출금 요청)

작가가 출금 가능 금액을 실제 계좌로 출금 요청한 내역

AttributeTypeRequiredDescriptionidUUIDO출금 요청 고유 식별자creatorIdUUIDO요청자 (작가) IDamountIntegerO출금 요청 금액bankNameStringO은행명accountNumberStringO계좌번호accountHolderStringO예금주명statusEnumO요청 상태adminNoteStringX관리자 메모createdAtDateTimeO요청일시processedAtDateTimeX처리 완료일시

**Enum Values:**

- `status`: PENDING, APPROVED, REJECTED, COMPLETED

**관련 User Story:** US-021, US-025, US-026

---

### 13. Sanction (제재)

관리자가 악성 유저에게 부과한 제재 기록

AttributeTypeRequiredDescriptionidUUIDO제재 고유 식별자userIdUUIDO제재 대상 사용자 IDadminIdUUIDO제재 처리 관리자 IDreportIdUUIDX연동된 신고 ID (신고 기반 제재 시)reasonStringO제재 사유typeEnumO제재 유형startAtDateTimeO제재 시작일시endAtDateTimeX제재 종료일시createdAtDateTimeO생성일시

**Enum Values:**

- `type`: WARNING, SUSPENSION, PERMANENT_BAN

**관련 User Story:** US-023, US-032

---

### 14. Report (신고)

사용자가 다른 사용자를 신고한 기록

AttributeTypeRequiredDescriptionidUUIDO신고 고유 식별자reporterIdUUIDO신고자 사용자 IDreportedUserIdUUIDO피신고자 사용자 IDtypeEnumO신고 유형reasonStringO신고 사유 상세 (최소 10자)referenceTypeEnumX신고 맥락 참조 유형referenceIdUUIDX신고 맥락 참조 IDstatusEnumO처리 상태adminIdUUIDX처리 담당 관리자 IDadminFeedbackStringX신고자에게 공개되는 처리 결과 피드백adminNoteStringX내부 관리자 메모 (비공개)sanctionIdUUIDX연동된 제재 ID (제재 조치 시)createdAtDateTimeO신고 접수일시processedAtDateTimeX처리 완료일시

**Enum Values:**

- `type`: SPAM, ABUSE, INAPPROPRIATE_CONTENT, COPYRIGHT, MISINFORMATION, OTHER (6가지)

- `referenceType`: STORY, PROPOSAL, BACKING, COMMENT, PROFILE (신고가 발생한 맥락)

- `status`: PENDING, REVIEWING, RESOLVED, DISMISSED

**Business Rules:**

- 동일 신고자가 동일 피신고자를 중복 신고할 수 없음 (PENDING/REVIEWING 상태일 때)

- 신고 처리 시 제재 조치를 선택하면 Sanction 엔티티와 연동

- 처리 완료 시 신고자에게 결과 알림 발송 가능

**Relationships:**

- N:1 → User (reporterId: 신고자)

- N:1 → User (reportedUserId: 피신고자)

- N:1 → User (adminId: 처리 관리자)

- 1:1 → Sanction (제재 조치 시 연동, 선택적)

**관련 User Story:** US-029, US-030, US-031, US-032

---

## Domain Rules (비즈니스 규칙)

IDRule NameDescriptionDR-001닉네임 중복 불가 (온보딩 필수)닉네임은 플랫폼 전체에서 중복 불가. 온보딩 시 필수 입력 (2-12자, 한글/영문/숫자, change-2026-02-05-0918)DR-002최소 제안 금액제안 생성 시 최소 100 Ink 이상 베팅해야 함DR-003잔액 초과 사용 불가제안/지지/후원 시 보유 Ink보다 많은 금액 사용 불가DR-004에스크로 보관제안/지지 시 Ink는 시스템 에스크로에 보관되며, Deal 또는 Drop 시까지 유지DR-005Drop 시 전액 환불제안이 Drop되면 해당 제안의 모든 Backer에게 100% 환불DR-006Deal 시 정산 이관제안이 Deal되면 에스크로 금액이 작가의 payable_balance로 이동DR-007직접 후원 즉시 정산직접 후원은 에스크로 없이 즉시 작가의 정산 예정금으로 이동DR-008최종 원고 업로드 시 정산 전환완성 원고 업로드 시 payable_balance가 withdrawable_balance로 전환 (수수료 차감)DR-009크레딧 영구 박제에피소드 공개 시 생성된 크레딧 데이터는 이후 변경 불가DR-010크레딧 정렬 규칙채택된 제안자 우선 표시 후 금액순 정렬, 상위 3인 Main Producer 강조DR-011출금 가능 금액 초과 불가출금 요청 금액은 withdrawable_balance를 초과할 수 없음DR-012OPEN 상태에서만 제안/후원 가능에피소드 상태가 OPEN일 때만 제안 생성, 지지, 직접 후원 가능DR-013제재된 사용자 로그인 차단SUSPENDED 또는 PERMANENT_BAN 제재 적용 시 해당 사용자 로그인 차단DR-014철회 가능 조건 (change-2026-01-15-1000)OPEN 상태 Episode에서 본인의 Proposal/Backing만 철회 가능. Sponsor(직접후원)는 철회 불가DR-015Ink 부족 시 충전 유도 (change-2026-01-15-1000)제안/지지/후원 시 Ink 잔액 부족하면 충전하기 버튼 노출 → /shop 연결DR-016크리에이터 전환 즉시 반영 (change-2026-01-15-1000)크리에이터 프로필 설정 완료 시 심사 없이 즉시 CREATOR 역할 부여DR-017OPEN 상태 저장 즉시 반영 (change-2026-01-15-1000)Episode가 OPEN 상태일 때 작가가 저장하면 즉시 독자 화면에 반영. 임시저장(Draft) 없음DR-018온보딩 필수 통과 (change-2026-02-05-0918)소셜 로그인 후 온보딩(닉네임 설정 + 휴대폰 인증) 완료 전까지 주요 서비스 페이지 접근 차단DR-019휴대폰 번호 1계정 1번호 제한 (change-2026-02-05-0918)하나의 휴대폰 번호는 하나의 계정에만 등록 가능DR-020닉네임 실시간 중복 검사 (change-2026-02-05-0918)닉네임 입력 시 500ms debounce 후 실시간 중복 검사 수행

---

## Data Flows (주요 흐름)

### 1. 독자 회원가입 및 Ink 충전

```
휴대폰 인증 → User 생성 (phone, isPhoneVerified=true) → 프로필 설정 (nickname) → Wallet 자동 생성 → Payment (PG 결제) → Transaction 생성 → Wallet.currentBalance 증가
```
**회원가입 필수 단계 (change-2026-01-14-1500):**

1. $1
2. $1
3. $1
4. $1
5. $1

### 2. 제안 생성 플로우

```
Episode.status=OPEN 확인 → 잔액 확인 → Proposal 생성 → Escrow 생성 → Backing 생성 → Transaction → Wallet.currentBalance 감소
```
### 3. 제안 지지 플로우

```
Proposal.status=PENDING 확인 → 잔액 확인 → Backing 생성 → Escrow.totalAmount 증가 → Proposal 업데이트 → Transaction → Wallet 감소
```
### 4. 직접 후원 플로우

```
Episode.status=OPEN 확인 → 잔액 확인 → Backing 생성 (SETTLED) → Transaction → 독자 Wallet 감소 → 작가 payableBalance 증가
```
### 5. Deal (채택) 플로우

```
Proposal.status → DEAL → Escrow → RELEASED → Backing → SETTLED → 작가 payableBalance 증가 → Transaction
```
### 6. Drop (거절) 플로우

```
Proposal.status → DROP → Escrow → REFUNDED → Backing → REFUNDED → 각 Backer Wallet 복구 → Transaction (REFUND)
```
### 7. 완성 원고 공개 플로우

```
EpisodeContent (isFinal) → Episode.status → PUBLISHED → Credit 자동 생성 → payableBalance → withdrawableBalance (수수료 차감)
```
### 8. 출금 요청 플로우

```
잔액 확인 → WithdrawalRequest (PENDING) → 관리자 검토 → APPROVED/REJECTED → COMPLETED → Wallet.withdrawableBalance 감소 → Transaction
```
### 9. 비밀번호 재설정 플로우 (change-2026-01-14-1500)

```
휴대폰 번호 입력 → 인증번호 전송 → 인증번호 검증 (5분 유효) → 새 비밀번호 설정 → User.password 업데이트
```
**비밀번호 재설정 단계:**

1. $1
2. $1
3. $1
4. $1
5. $1

### 10. 회원 탈퇴 플로우 (change-2026-01-14-1500)

```
마이페이지 → 계정 설정 → 회원 탈퇴 → 유의사항 확인 → 본인 확인 (비밀번호 + "회원탈퇴" 입력) → 동의 체크 → User.status → DELETED
```
**회원 탈퇴 조건:**

- 현재 비밀번호 입력

- 확인 문구 "회원탈퇴" 정확히 입력

- 동의 체크박스 체크

**탈퇴 시 처리:**

- 개인정보 삭제 (또는 법적 보관 기간 후 삭제)

- 게시글 처리 방식 (TBD)

- 포인트/혜택 소멸

- 복구 불가

### 11. 참여 철회 플로우 (change-2026-01-15-1000)

```
Episode.status = OPEN 확인 → 참여 유형 확인 (Proposal/Backing만 철회 가능, Sponsor 불가) → 본인 참여 여부 확인 → 철회하기 버튼 클릭 → 확인 모달 표시 → Backing.status → REFUNDED → Escrow.totalAmount 감소 (해당 시) → Transaction 생성 (type: REFUND) → Wallet.currentBalance 복구 → 참여 리스트 즉시 반영
```
### 12. 크리에이터 전환 플로우 (change-2026-01-15-1000)

```
마이페이지 → 「크리에이터 되기」 버튼 클릭 → /mypage/creator/setup 페이지 이동 → 프로필 사진 업로드 → 닉네임 입력 (기존 닉네임 사용 가능) → 작가 소개글 입력 → 설정 완료 버튼 클릭 → User.role → CREATOR (즉시, 심사 없음) → 크리에이터 스튜디오 접근 권한 활성화
```

---

## Glossary (용어 사전)

용어정의**Ink**플랫폼 내 유료 재화. 1 Ink = 100원 고정. 구매 옵션: 10/20/30/100 Ink. 보너스/할인 없음 (change-2026-01-15-1000)**콘티**작가가 업로드하는 스토리 초안 (이미지/텍스트). OPEN 상태에서 후원/제안을 받음**제안 (Proposal)독자가 스토리 전개에 개입하기 위해 Ink를 걸고 생성하는 요청. 제안자는 Proposer로 표시지지 (Backing)다른 독자의 제안에 추가로 Ink를 더해 채택 확률을 높이는 행위. 지지자는 Backer로 표시직접 후원 (Sponsor)제안 없이 순수하게 창작을 지원하는 후원 방식. 후원자는 Sponsor로 표시. 철회 불가 (change-2026-01-15-1000)Bounty제안에 모인 총 금액 (seedMoney + 모든 backing 금액)에스크로 (Escrow)제안/지지 시 시스템이 Ink를 임시 보관하는 계정. Deal 또는 Drop 시 해제Deal작가가 제안을 채택하는 것. 에스크로 금액이 작가에게 이동Drop**작가가 제안을 거절하는 것. 에스크로 금액이 참여자에게 100% 환불**크레딧 (Credit)완성된 에피소드 하단에 표시되는 기여자 명단. 영구 박제됨Main Producer**기여 금액 상위 3인에게 부여되는 특별 타이틀**payable_balance**정산 예정금. Deal 또는 직접 후원 시 누적되는 금액**withdrawable_balance**출금 가능 금액. 완성 원고 공개 후 수수료 차감된 금액**OPEN**에피소드 상태. 후원 및 제안을 받을 수 있는 상태**PUBLISHED**에피소드 상태. 완성 원고가 공개된 상태. 제안/후원 불가. Ink 정산 확정 시점 (change-2026-01-19-1100)**의사결정 알림**작가가 deal/drop 선택 시 독자에게 발송되는 알림. 금전 이동은 발생하지 않음 (change-2026-01-19-1100)**정산 확정 알림**published 전환 시 발송되는 알림. Ink 출금/환불이 최종 확정됨을 고지 (change-2026-01-19-1100)**제안 내역**독자가 제안/후원한 기록의 목록. 마이페이지에서 조회 가능 (change-2026-01-19-1100)**진행 중 (Pending)**work.status === 'open'인 모든 후원 건. Ink가 에스크로에 보관된 상태 (change-2026-01-19-1100)

---

## 문서 정보

- **생성일:** 2026-01-06

- **기반 문서:** user_stories_data.json, seed-docs/*