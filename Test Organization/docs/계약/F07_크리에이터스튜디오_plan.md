# Feature Plan: F07 크리에이터 스튜디오

## Feature Overview

**Feature ID**: F07
**Feature Name**: 크리에이터 스튜디오
**Priority**: P1 (Must Have - MVP 필수)
**User Stories**: US-014, US-015, US-016, US-017, US-018, US-019, US-020, US-021

### Purpose
작가가 에피소드를 제작/관리하고, 독자의 제안을 검토하여 Deal/Drop 결정을 내리며, 정산금을 조회하고 출금 요청을 수행하는 크리에이터 전용 작업 공간.

### Scope Boundary
**포함:**
- 에피소드 업로드 및 관리 (콘티/완성본)
- 제안(Proposal) 조회 및 Deal/Drop 결정
- 정산 현황 조회 및 출금 요청
- 에피소드별 크레딧 자동 생성

**제외:**
- 작품(Story) CRUD (별도 feature로 관리)
- 독자 관점 콘텐츠 열람 (독자 코어 feature)
- 관리자의 정산 승인 프로세스 (관리자 feature)

---

## User Flows

### Flow 1: 에피소드 콘티 업로드 (US-014)
```
작가 로그인 → 크리에이터 스튜디오 (/studio)
→ 에피소드 관리 (/studio/episodes)
→ 새 에피소드 추가 버튼 클릭
→ 에피소드 편집 페이지 (/studio/episodes/new)
→ 이미지 다중 업로드 (드래그 앤 드롭)
→ 이미지 순서 변경
→ 텍스트 입력
→ 저장/공개 버튼 클릭
→ Episode 상태: OPEN
→ "에피소드가 등록되었습니다" 확인 메시지
```

**Acceptance Criteria:**
- AC-1: 이미지 다중 업로드 및 순서 변경 가능
- AC-2: 텍스트 입력 영역 제공
- AC-3: 업로드 완료 후 에피소드 상태 OPEN으로 자동 변경
- AC-4: 업로드 완료 시 확인 메시지 표시

### Flow 2: 에피소드 목록 관리 (US-015)
```
작가 로그인 → 크리에이터 스튜디오 (/studio)
→ 에피소드 관리 (/studio/episodes)
→ 에피소드 리스트 확인
  - 각 에피소드: 제목, 상태(OPEN/PUBLISHED), 총 모금액 표시
→ 에피소드 편집 버튼 클릭
→ 에피소드 편집 페이지로 이동
```

**Acceptance Criteria:**
- AC-1: 에피소드 상태(OPEN/PUBLISHED) 표시
- AC-2: 에피소드별 총 모금액 표시
- AC-3: 에피소드 편집 페이지로 이동 가능

### Flow 3: 제안 Deal/Drop 결정 (US-016, US-017, US-018)
```
작가 로그인 → 크리에이터 스튜디오 (/studio)
→ 제안 관리 (/studio/proposals)
→ 에피소드별 제안 목록 확인 (모금액 순 정렬)
→ 제안 선택 → Deal 또는 Drop 버튼 클릭

[Deal 선택 시]
→ DealConfirmModal 표시 (영향 미리보기)
→ 확인 클릭
→ Proposal.status → DEAL
→ Escrow.totalAmount → Creator Wallet.payableBalance 이동
→ "제안이 채택되었습니다" 확인 메시지

[Drop 선택 시]
→ DropConfirmModal 표시 (환불 미리보기)
→ 확인 클릭
→ Proposal.status → DROP
→ 모든 Backer에게 Ink 100% 자동 반환
→ "제안이 거절되었습니다" 확인 메시지
```

**Acceptance Criteria:**
- US-016:
  - AC-1: 제안 목록 모금액 순 정렬
  - AC-2: 각 제안에 내용, total_bounty, backer_count 표시
  - AC-3: 제안별 Deal/Drop 액션 버튼 제공
- US-017:
  - AC-1: Deal 버튼 클릭 시 확인 모달 표시
  - AC-2: 확인 후 Proposal 상태 DEAL로 변경
  - AC-3: 에스크로 된 Ink가 작가 payable_balance로 이동
  - AC-4: Deal 완료 시 확인 메시지 표시
- US-018:
  - AC-1: Drop 버튼 클릭 시 확인 모달 표시
  - AC-2: 확인 후 Proposal 상태 DROP으로 변경
  - AC-3: 모든 Backer에게 Ink 100% 자동 반환
  - AC-4: Drop 완료 시 확인 메시지 표시

### Flow 4: 완성 원고 업로드 (US-019)
```
작가 로그인 → 에피소드 관리 (/studio/episodes)
→ OPEN 상태 에피소드 선택 → 편집
→ 최종 원고 업로드 (isFinal: true)
→ 공개 버튼 클릭
→ Episode.status → PUBLISHED
→ CreditGenerationService: Credit 자동 생성 (기여자 집계)
→ Wallet: payableBalance → withdrawableBalance 전환 (수수료 차감)
→ "원고가 공개되었습니다" 확인 메시지
```

**Acceptance Criteria:**
- AC-1: 최종 원고 업로드 시 에피소드 상태 PUBLISHED로 변경
- AC-2: 업로드 시 다이내믹 크레딧 자동 생성
- AC-3: 예정 정산금이 출금 가능 금액으로 전환(수수료 차감)
- AC-4: 업로드 완료 시 확인 메시지 표시

### Flow 5: 정산 및 출금 요청 (US-020, US-021)
```
작가 로그인 → 크리에이터 스튜디오 (/studio)
→ 정산 관리 (/studio/settlements)
→ 정산 현황 확인
  - 예정 정산금 (payable_balance)
  - 출금 가능 금액 (withdrawable_balance)
  - 에피소드별 정산 내역 리스트
→ 출금 요청 버튼 클릭
→ WithdrawalModal 표시
→ 출금 금액 입력 (withdrawable_balance 초과 시 에러)
→ 은행 정보 입력 (은행명, 계좌번호, 예금주명)
→ 요청 제출
→ WithdrawalRequest 생성 (status: PENDING)
→ "출금 요청이 접수되었습니다" 확인 메시지
```

**Acceptance Criteria:**
- US-020:
  - AC-1: 예정 정산금 금액 표시
  - AC-2: 출금 가능 금액 별도 표시
  - AC-3: 에피소드별 정산 내역 리스트 제공
- US-021:
  - AC-1: 출금 요청 버튼 클릭 시 출금 금액 입력 모달 표시
  - AC-2: 출금 가능 금액 초과 시 에러 표시
  - AC-3: 요청 완료 시 확인 메시지 표시

---

## Screens & Routes

### 1. Creator Studio Dashboard
- **Route**: `/studio`
- **Layout**: Creator Layout (with SideNav)
- **Purpose**: 작가 대시보드, 주요 기능 진입점
- **Components**:
  - 작품 목록 요약
  - 에피소드 관리 링크 → `/studio/episodes`
  - 제안 관리 링크 → `/studio/proposals`
  - 정산 현황 요약
- **User Stories**: US-014, US-015

### 2. Episode Management Page
- **Route**: `/studio/episodes`
- **Layout**: Creator Layout
- **Purpose**: 에피소드 목록 조회 및 관리
- **Components**:
  - CreatorEpisodeList 컴포넌트
    - 에피소드 제목, 상태(OPEN/PUBLISHED), 총 모금액 표시
    - 편집/삭제 액션 버튼
  - 새 에피소드 추가 버튼
- **User Stories**: US-015

### 3. Episode Editor Page
- **Route**: `/studio/episodes/:episodeId/edit`
- **Layout**: Creator Layout
- **Purpose**: 콘티/완성본 업로드 및 편집
- **Components**:
  - EpisodeEditor 컴포넌트
    - 제목 입력
    - 이미지 업로드 영역 (ContentUploader)
    - 텍스트 입력 영역
    - 콘티/완성본 구분 토글
    - 저장/공개 버튼
  - ContentUploader 컴포넌트
    - 드래그 앤 드롭 이미지 업로드
    - 이미지 순서 변경 (Sortable)
    - 이미지 미리보기
    - 개별 이미지 삭제
- **User Stories**: US-014, US-019

### 4. Proposal Management Page
- **Route**: `/studio/proposals`
- **Layout**: Creator Layout
- **Purpose**: 받은 제안 목록 조회 및 Deal/Drop 결정
- **Components**:
  - ProposalManagementList 컴포넌트
    - 에피소드별 제안 목록 (모금액 순 정렬)
    - 제안 카드: 내용, total_bounty, backer_count 표시
    - Deal/Drop 버튼
  - DealConfirmModal 컴포넌트
    - Deal 확인 모달
    - 영향 미리보기 (에스크로 금액 → payable_balance)
    - 확인/취소 버튼
  - DropConfirmModal 컴포넌트
    - Drop 확인 모달
    - 환불 미리보기 (Backer별 환불 금액)
    - 확인/취소 버튼
- **User Stories**: US-016, US-017, US-018

### 5. Settlement Management Page
- **Route**: `/studio/settlements`
- **Layout**: Creator Layout
- **Purpose**: 정산 현황 조회 및 출금 요청
- **Components**:
  - SettlementSummary 컴포넌트
    - 예정 정산금 (payable_balance) 카드
    - 출금 가능 금액 (withdrawable_balance) 카드
    - 출금 요청 버튼 → WithdrawalModal
  - SettlementList 컴포넌트
    - 에피소드별 정산 내역 리스트
    - 에피소드 제목, 공개일, 정산 금액, 수수료 표시
  - WithdrawalModal 컴포넌트
    - 출금 금액 입력 (max: withdrawable_balance)
    - 은행 정보 입력 (은행명, 계좌번호, 예금주명)
    - 요청 제출 버튼
- **User Stories**: US-020, US-021

---

## Data Model (Relevant Entities)

### Episode
```typescript
{
  id: UUID
  storyId: UUID
  episodeNumber: Integer
  title: String
  status: "DRAFT" | "OPEN" | "PUBLISHED" | "HIDDEN" | "DELETED"
  totalBounty: Integer  // 해당 회차 총 모금액
  createdAt: DateTime
  publishedAt: DateTime?
}
```

**Relationships:**
- N:1 → Story
- 1:N → EpisodeContent
- 1:N → Proposal
- 1:1 → Credit

**Domain Rules:**
- DR-012: OPEN 상태에서만 제안/후원 가능
- DR-008: 완성 원고 업로드 시 payable_balance → withdrawable_balance 전환
- DR-009: 에피소드 공개 시 크레딧 영구 박제

### EpisodeContent
```typescript
{
  id: UUID
  episodeId: UUID
  contentType: "IMAGE" | "TEXT"
  contentUrl: String?  // IMAGE 유형
  textContent: String?  // TEXT 유형
  orderIndex: Integer
  isFinal: Boolean  // true: 완성본
  createdAt: DateTime
}
```

### Proposal
```typescript
{
  id: UUID
  episodeId: UUID
  proposerId: UUID
  description: String
  seedMoney: Integer  // 최초 베팅액
  totalBounty: Integer  // 총 모금액
  backerCount: Integer  // 지지자 수
  status: "PENDING" | "DEAL" | "DROP"
  createdAt: DateTime
  decidedAt: DateTime?
}
```

**Relationships:**
- N:1 → Episode
- 1:N → Backing
- 1:1 → Escrow

**Domain Rules:**
- DR-006: Deal 시 에스크로 금액이 작가의 payable_balance로 이동
- DR-005: Drop 시 모든 Backer에게 100% 환불

### Credit
```typescript
{
  id: UUID
  episodeId: UUID
  contributors: JSON  // [{nickname, amount, isProposer, rank}]
  mainProducers: Array<UUID>  // 상위 3인 Main Producer
  createdAt: DateTime  // 영구 박제
}
```

**Domain Rules:**
- DR-009: 크레딧 영구 박제 (Episode.status = PUBLISHED 이후 변경 불가)
- DR-010: 정렬 규칙 - 채택된 제안자 우선, 그 다음 금액순, 상위 3인 Main Producer 강조

### WithdrawalRequest
```typescript
{
  id: UUID
  creatorId: UUID
  amount: Integer
  bankName: String
  accountNumber: String
  accountHolder: String
  status: "PENDING" | "APPROVED" | "REJECTED" | "COMPLETED"
  adminNote: String?
  createdAt: DateTime
  processedAt: DateTime?
}
```

**Domain Rules:**
- DR-011: 출금 요청 금액은 withdrawable_balance를 초과할 수 없음

### Wallet (Creator)
```typescript
{
  id: UUID
  userId: UUID
  currentBalance: Integer  // 독자만 사용
  payableBalance: Integer  // 정산 예정금 (작가용)
  withdrawableBalance: Integer  // 출금 가능 금액 (작가용)
  updatedAt: DateTime
}
```

---

## Component Strategy

### Services (Data Access Layer)

#### 1. EpisodeManagementService
- **Primary Responsibility**: 에피소드 CRUD 및 상태 관리
- **Type**: Service
- **Interfaces**:
  - `getCreatorEpisodes(): Promise<Episode[]>`
  - `createEpisode(storyId, data): Promise<Episode>`
  - `updateEpisode(id, data): Promise<Episode>`
  - `deleteEpisode(id): Promise<void>`
  - `publishEpisode(id): Promise<Episode>` → status: PUBLISHED, 크레딧 생성 트리거

#### 2. ContentUploadService
- **Primary Responsibility**: 다중 이미지 업로드 및 콘텐츠 관리
- **Type**: Service
- **Interfaces**:
  - `uploadImages(files): Promise<UploadResult[]>`
  - `reorderContent(episodeId, order): Promise<void>`
  - `deleteContent(contentId): Promise<void>`

#### 3. ProposalDecisionService
- **Primary Responsibility**: Deal/Drop 결정 및 에스크로 정산
- **Type**: Service
- **Dependencies**: EscrowService
- **Interfaces**:
  - `getCreatorProposals(): Promise<Proposal[]>`
  - `dealProposal(proposalId): Promise<DealResult>`
    - Proposal.status → DEAL
    - Escrow.totalAmount → Creator Wallet.payableBalance
  - `dropProposal(proposalId): Promise<DropResult>`
    - Proposal.status → DROP
    - 모든 Backer에게 Ink 100% 반환

#### 4. CreditGenerationService
- **Primary Responsibility**: 에피소드 공개 시 크레딧 자동 생성
- **Type**: Service (Internal)
- **Interfaces**:
  - `generateCredits(episodeId): Promise<Credit>`
  - 기여자 집계 로직:
    1. 채택된 제안자 우선
    2. 금액순 정렬
    3. 상위 3인 Main Producer 지정

#### 5. SettlementService
- **Primary Responsibility**: 작가 정산 현황 조회
- **Type**: Service
- **Interfaces**:
  - `getSettlementSummary(): Promise<SettlementSummary>`
    - payableBalance
    - withdrawableBalance
  - `getSettlementHistory(): Promise<SettlementRecord[]>`
    - 에피소드별 정산 내역

#### 6. WithdrawalService
- **Primary Responsibility**: 출금 요청 생성 및 상태 추적
- **Type**: Service
- **Interfaces**:
  - `createWithdrawalRequest(amount, bankInfo): Promise<WithdrawalRequest>`
  - `getWithdrawalRequests(): Promise<WithdrawalRequest[]>`

### Components (Presentation Layer)

#### 1. CreatorEpisodeList (task-061)
- **Type**: Component
- **Purpose**: 작가의 에피소드 목록 표시
- **Props**: episodes (from useEpisodeManagement hook)
- **Features**:
  - 에피소드 카드: 제목, 상태 배지(OPEN/PUBLISHED), 총 모금액
  - 편집 버튼 → `/studio/episodes/:episodeId/edit`
  - 삭제 버튼 (확인 모달)

#### 2. EpisodeEditor (task-063)
- **Type**: Component
- **Purpose**: 에피소드 편집 인터페이스
- **Props**: episodeId (URL param)
- **Features**:
  - 제목 입력 필드
  - ContentUploader 통합
  - 텍스트 입력 영역
  - 콘티/완성본 구분 토글
  - 저장/공개 버튼

#### 3. ContentUploader (task-064)
- **Type**: Component
- **Purpose**: 드래그 앤 드롭 이미지 업로드
- **Dependencies**: ContentUploadService
- **Features**:
  - 드래그 앤 드롭 영역
  - 다중 파일 선택
  - 이미지 미리보기 (sortable)
  - 이미지 순서 변경
  - 개별 이미지 삭제

#### 4. ProposalManagementList (task-066)
- **Type**: Component
- **Purpose**: 작가가 받은 제안 목록 표시
- **Props**: proposals (from useProposalDecision hook)
- **Features**:
  - 제안 카드: 내용, total_bounty, backer_count
  - 모금액 순 정렬
  - Deal/Drop 버튼

#### 5. DealConfirmModal (task-067)
- **Type**: Modal Component
- **Purpose**: Deal 확인 모달
- **Props**: proposal, onConfirm, onCancel
- **Features**:
  - 제안 정보 요약
  - 영향 미리보기 (에스크로 금액 → payable_balance)
  - 확인/취소 버튼

#### 6. DropConfirmModal (task-068)
- **Type**: Modal Component
- **Purpose**: Drop 확인 모달
- **Props**: proposal, onConfirm, onCancel
- **Features**:
  - 제안 정보 요약
  - 환불 미리보기 (Backer별 환불 금액)
  - 확인/취소 버튼

#### 7. SettlementSummary (task-070)
- **Type**: Component
- **Purpose**: 정산 현황 요약 카드
- **Props**: settlementSummary (from useSettlement hook)
- **Features**:
  - 예정 정산금 카드 (payableBalance)
  - 출금 가능 금액 카드 (withdrawableBalance)
  - 출금 요청 버튼 → WithdrawalModal

#### 8. SettlementList (task-071)
- **Type**: Component
- **Purpose**: 에피소드별 정산 내역 리스트
- **Props**: settlementHistory (from useSettlement hook)
- **Features**:
  - 에피소드 제목, 공개일, 정산 금액, 수수료 표시
  - 페이지네이션 또는 무한 스크롤

#### 9. WithdrawalModal (task-072)
- **Type**: Modal Component
- **Purpose**: 출금 요청 폼
- **Props**: withdrawableBalance, onSubmit, onCancel
- **Features**:
  - 출금 금액 입력 (max: withdrawableBalance)
  - 은행 정보 입력 (은행명, 계좌번호, 예금주명)
  - 검증 로직 (금액 초과 체크)
  - 요청 제출 버튼

---

## Task Mapping

| Task ID | Title | Component/Service | Purpose |
|---------|-------|-------------------|---------|
| task-059 | Studio Dashboard Page UI | `/studio` page | 작가 대시보드 |
| task-060 | Episode Management Page UI | `/studio/episodes` page | 에피소드 목록 |
| task-061 | CreatorEpisodeList 컴포넌트 | CreatorEpisodeList | 에피소드 리스트 렌더링 |
| task-062 | Episode Editor Page UI | `/studio/episodes/:episodeId/edit` page | 에피소드 편집 페이지 |
| task-063 | EpisodeEditor 컴포넌트 | EpisodeEditor | 에피소드 편집 UI |
| task-064 | ContentUploader 컴포넌트 | ContentUploader | 이미지 업로드 UI |
| task-065 | Proposal Management Page UI | `/studio/proposals` page | 제안 관리 페이지 |
| task-066 | ProposalManagementList 컴포넌트 | ProposalManagementList | 제안 목록 렌더링 |
| task-067 | DealConfirmModal 컴포넌트 | DealConfirmModal | Deal 확인 모달 |
| task-068 | DropConfirmModal 컴포넌트 | DropConfirmModal | Drop 확인 모달 |
| task-069 | Settlement Page UI | `/studio/settlements` page | 정산 관리 페이지 |
| task-070 | SettlementSummary 컴포넌트 | SettlementSummary | 정산 요약 카드 |
| task-071 | SettlementList 컴포넌트 | SettlementList | 정산 내역 리스트 |
| task-072 | WithdrawalModal 컴포넌트 | WithdrawalModal | 출금 요청 모달 |
| task-073 | Episode Management Service (Mock API) | EpisodeManagementService | 에피소드 CRUD 및 상태 관리 |
| task-074 | Settlement Service (Mock API) | SettlementService | 정산 조회 |

### Task Dependencies (Sequential Order)

```
Phase 1: Foundation
├── task-059 (Studio Dashboard) [Depends: task-010, task-013]
├── task-073 (EpisodeManagementService) [Depends: task-039]
└── task-074 (SettlementService) [Depends: task-004]

Phase 2: Episode Management
├── task-060 (Episode Management Page) [Depends: task-059]
├── task-061 (CreatorEpisodeList) [Depends: task-033]
├── task-062 (Episode Editor Page) [Depends: task-060]
├── task-063 (EpisodeEditor) [Depends: task-003]
└── task-064 (ContentUploader) [Depends: task-003]

Phase 3: Proposal Management
├── task-065 (Proposal Management Page) [Depends: task-059]
├── task-066 (ProposalManagementList) [Depends: task-049]
├── task-067 (DealConfirmModal) [Depends: task-003]
└── task-068 (DropConfirmModal) [Depends: task-003]

Phase 4: Settlement Management
├── task-069 (Settlement Page) [Depends: task-059]
├── task-070 (SettlementSummary) [Depends: task-003]
├── task-071 (SettlementList) [Depends: task-003]
└── task-072 (WithdrawalModal) [Depends: task-003]
```

---

## Implementation Notes

### Development Guidelines

1. **Monochrome Design Only**
   - 모든 UI는 흑백 컬러 스킴 사용
   - 이미지는 회색 박스(bg-muted)로 표시
   - 상태 배지는 border/text로만 구분

2. **TanStack Query Pattern**
   - 모든 데이터 패칭은 useQuery/useMutation 사용
   - Service 함수는 순수 Promise 반환
   - Hook에서 TanStack Query 래핑

3. **File Structure Pattern**
   ```
   services/
     ├── episodeService.ts
     └── settlementService.ts
   hooks/
     ├── useEpisodeManagement.ts
     └── useSettlement.ts
   components/
     ├── episode/
     │   ├── CreatorEpisodeList.tsx
     │   ├── EpisodeEditor.tsx
     │   └── ContentUploader.tsx
     ├── proposal/
     │   ├── ProposalManagementList.tsx
     │   ├── DealConfirmModal.tsx
     │   └── DropConfirmModal.tsx
     └── settlement/
         ├── SettlementSummary.tsx
         ├── SettlementList.tsx
         └── WithdrawalModal.tsx
   app/(creator)/studio/
     ├── page.tsx
     ├── episodes/
     │   ├── page.tsx
     │   └── [episodeId]/edit/page.tsx
     ├── proposals/page.tsx
     └── settlements/page.tsx
   ```

4. **Mock Data Requirements**
   - EpisodeManagementService: CRUD 작동 가능한 mock
   - SettlementService: payableBalance, withdrawableBalance 계산 로직 포함
   - ProposalDecisionService: Deal/Drop 시뮬레이션

5. **Type Definitions**
   - 모든 타입은 `types/` 폴더에서 import
   - Episode, EpisodeContent, Proposal, Credit, WithdrawalRequest 사용

---

## Testing Considerations

### Key Test Scenarios

1. **에피소드 업로드**
   - 이미지 다중 업로드 성공
   - 이미지 순서 변경 정상 작동
   - OPEN 상태 전환 확인

2. **제안 Deal/Drop**
   - Deal 시 에스크로 → payableBalance 이동 확인
   - Drop 시 Backer에게 100% 환불 확인
   - 상태 변경 후 UI 업데이트 확인

3. **완성 원고 공개**
   - PUBLISHED 상태 전환 확인
   - Credit 자동 생성 확인
   - payableBalance → withdrawableBalance 전환 확인

4. **출금 요청**
   - withdrawableBalance 초과 시 에러 표시 확인
   - 은행 정보 입력 검증 확인
   - 요청 생성 후 PENDING 상태 확인

---

## 문서 정보

- **생성일**: 2026-01-08
- **기반 문서**:
  - tasks/tasks.json
  - docs/user_stories.md
  - docs/ia_structure.md
  - docs/conceptual_model.json
  - docs/logical_architecture.md
