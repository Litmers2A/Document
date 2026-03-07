# F06: 인터벤션

> **Feature Type:** Core Interaction / Monetization
> **Status:** Pending
> **Tasks:** task-049, task-050, task-051, task-052, task-053, task-054, task-055, task-056, task-057, task-058
> **Dependencies:** F04 (독자 코어 - Episode Viewer)

---

## 1. Feature Overview

### Purpose
독자가 스토리 전개에 직접 개입할 수 있는 참여형 크라우드펀딩 시스템을 제공합니다. 제안(Proposal) 생성, 지지(Backing), 직접 후원(Direct Backing)을 통해 독자가 창작에 기여하고, 완성 에피소드의 크레딧에 이름을 남길 수 있습니다.

### Scope Boundary
- **IN**:
  - 제안 생성/조회/목록 (OPEN 상태 에피소드)
  - 제안 지지 (다른 사람의 제안 지원)
  - 직접 후원 (제안 없이 순수 후원)
  - 크레딧 표시 (PUBLISHED 상태 에피소드)
  - 에스크로 처리 (시스템 내부 로직)
- **OUT**:
  - 작가의 Deal/Drop 결정 (크리에이터 스튜디오 피처)
  - Ink 잔액 관리 (지갑/결제 피처)
  - 정산 및 출금 (정산 피처)

---

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-09 | 독자 | 제안 생성 | OPEN 에피소드 진입 → 제안하기 클릭 → ProposalModal → 내용/금액 입력 → Ink 차감 + 에스크로 처리 → 제안 등록 완료 |
| UF-10 | 독자 | 제안 지지 | OPEN 에피소드 → 제안 목록 확인 → 제안 카드 지지하기 클릭 → BackingModal → 금액 입력 → Ink 차감 + 에스크로 추가 → 지지 완료 |
| UF-11 | 독자 | 직접 후원 | OPEN 에피소드 → 후원하기 클릭 → DirectBackingModal → 금액 입력 → Ink 차감 + 작가 정산금 즉시 이동 → 후원 완료 |
| UF-12 | 독자 | 크레딧 확인 | PUBLISHED 에피소드 → 콘텐츠 스크롤 → 최하단 CreditSection → Main Producer 3인 + 기여자 리스트 확인 |
| UF-13 | 독자 | 제안 목록 조회 | OPEN 에피소드 → 콘텐츠 하단 ProposalList → 모금액 순 정렬된 제안 확인 → 지지 결정 |

Source: user_stories.md (US-009, US-010, US-011, US-012, US-013)

---

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| Episode Viewer (OPEN) | `/stories/:storyId/episodes/:episodeId` | 콘텐츠 + ActionBar + ProposalList 통합 |
| Episode Viewer (PUBLISHED) | `/stories/:storyId/episodes/:episodeId` | 콘텐츠 + CreditSection 통합 |

**Modals (Overlay Components)**:
- **ProposalModal**: 제안 생성 폼 (내용, 초기 베팅액)
- **BackingModal**: 제안 지지 폼 (금액 입력)
- **DirectBackingModal**: 직접 후원 폼 (금액 입력)

Source: ia_structure.md

---

## 4. Data Model

### Relevant Entities

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| Proposal | id, episodeId, proposerId, description, seedMoney, totalBounty, backerCount, status (PENDING/DEAL/DROP) | → Episode (N:1), → User (N:1), → Backing (1:N), → Escrow (1:1) |
| Backing | id, backerId, type (PROPOSAL_BACKING/DIRECT_BACKING), proposalId?, episodeId?, amount, status (ESCROWED/SETTLED/REFUNDED) | → User (N:1), → Proposal (N:1), → Episode (N:1) |
| Credit | id, episodeId, contributors (JSON), mainProducers (Array[UUID]) | → Episode (N:1) |
| Escrow | id, proposalId, totalAmount, status (HOLDING/RELEASED/REFUNDED) | → Proposal (N:1) |

### Domain Rules

| Rule ID | Description |
|---------|-------------|
| DR-002 | 최소 제안 금액: seedMoney >= 100 Ink |
| DR-003 | 잔액 초과 사용 불가: Transaction.amount <= Wallet.currentBalance |
| DR-004 | 제안/지지 시 Ink는 에스크로 보관 (Deal/Drop 시까지) |
| DR-005 | Drop 시 모든 Backer에게 100% 환불 |
| DR-006 | Deal 시 에스크로 → 작가 payable_balance 이동 |
| DR-007 | 직접 후원은 에스크로 없이 즉시 작가 payable_balance로 이동 |
| DR-009 | 크레딧은 에피소드 공개 시 영구 박제 (변경 불가) |
| DR-010 | 크레딧 정렬: 채택된 제안자 우선 → 금액순 DESC, 상위 3인 Main Producer 강조 |
| DR-012 | OPEN 상태에서만 제안/지지/후원 가능 |

Source: conceptual_model.json

---

## 5. Component Strategy

### Services (Data Access Layer)

| Service | Methods | User Stories |
|---------|---------|--------------|
| **ProposalService** | - `createProposal(episodeId, data): Promise<Proposal>`<br>- `getProposals(episodeId, sort?): Promise<Proposal[]>`<br>- `getProposalById(id): Promise<Proposal>` | US-009, US-013 |
| **BackingService** | - `createBacking(proposalId, amount): Promise<Backing>`<br>- `createDirectBacking(episodeId, amount): Promise<Backing>` | US-010, US-011 |

### Components (UI Layer)

| Component | Responsibility | Type |
|-----------|----------------|------|
| **ProposalCard** | 개별 제안 카드 렌더링 (내용, 모금액, 지지자 수, 지지하기 버튼) | Presentational |
| **ProposalList** | 제안 목록 렌더링 (모금액 순 정렬, 빈 상태 처리) | Container |
| **ProposalModal** | 제안 생성 모달 (내용 입력, 초기 베팅액, 폼 검증) | Modal |
| **BackingModal** | 제안 지지 모달 (금액 입력, 잔액 확인) | Modal |
| **DirectBackingModal** | 직접 후원 모달 (금액 입력, 잔액 확인) | Modal |
| **CreditSection** | 크레딧 섹션 (Main Producer 3인 강조 + 전체 기여자 리스트) | Presentational |

### Hooks (Business Logic Layer)

| Hook | Purpose |
|------|---------|
| **useProposals(episodeId)** | 에피소드의 제안 목록 조회 (TanStack Query) |
| **useCreateProposal()** | 제안 생성 뮤테이션 |
| **useCreateBacking()** | 제안 지지 뮤테이션 |
| **useCreateDirectBacking()** | 직접 후원 뮤테이션 |

Source: logical_architecture.md (Intervention Domain)

---

## 6. Task Mapping

| Task ID | Title | Implements | Files | Dependencies |
|---------|-------|------------|-------|--------------|
| task-049 | ProposalCard 컴포넌트 | 제안 카드 UI | components/proposal/ProposalCard.tsx | task-003 |
| task-050 | ProposalList 컴포넌트 | 제안 목록 렌더링 + 정렬 | components/proposal/ProposalList.tsx | task-049 |
| task-051 | ProposalModal 컴포넌트 | 제안 생성 모달 폼 | components/proposal/ProposalModal.tsx | task-003 |
| task-052 | BackingModal 컴포넌트 | 제안 지지 모달 폼 | components/proposal/BackingModal.tsx | task-003 |
| task-053 | DirectBackingModal 컴포넌트 | 직접 후원 모달 폼 | components/proposal/DirectBackingModal.tsx | task-003 |
| task-054 | CreditSection 컴포넌트 | 크레딧 섹션 (Main Producer + 기여자) | components/episode/CreditSection.tsx | task-003 |
| task-055 | Proposal Service | 제안 생성/조회 API 서비스 (Mock) | services/proposalService.ts | task-004 |
| task-056 | Backing Service | 지지/후원 API 서비스 (Mock) | services/backingService.ts | task-004 |
| task-057 | useProposals Hook | TanStack Query 훅 (제안 조회/생성/지지) | hooks/useProposals.ts | task-055 |
| task-058 | Episode Viewer 통합 | ProposalList + CreditSection 통합 | app/(auth)/stories/[storyId]/episodes/[episodeId]/page.tsx | task-035, task-050, task-054 |

Source: tasks.json

---

## 7. Integration Points

### With Episode Viewer (F04)
- **OPEN 상태**: ActionBar → ProposalModal/DirectBackingModal 트리거
- **OPEN 상태**: 콘텐츠 하단 ProposalList 섹션 추가
- **PUBLISHED 상태**: 콘텐츠 최하단 CreditSection 표시

### With Wallet (지갑/결제 피처)
- 제안/지지/후원 시 `Wallet.currentBalance` 확인 필요
- 잔액 부족 시 에러 처리 (`'잔액이 부족합니다'`)

### With Creator Studio (크리에이터 스튜디오 피처)
- 작가의 Deal/Drop 결정은 이 피처 범위 밖
- ProposalService는 PENDING 상태 제안만 반환

---

## 8. Acceptance Criteria Summary

### US-009 (제안 생성)
- ✅ 최소 100 Ink 베팅 검증
- ✅ 잔액 부족 시 에러 표시
- ✅ 제안 생성 시 Ink 즉시 차감 + 에스크로 처리
- ✅ 성공 시 확인 메시지 표시

### US-010 (제안 지지)
- ✅ 금액 입력 가능
- ✅ 지지 시 Ink 차감 + 에스크로 추가
- ✅ total_bounty, backer_count 실시간 업데이트

### US-011 (직접 후원)
- ✅ 금액 선택 가능
- ✅ 에스크로 없이 즉시 작가 정산 예정금으로 이동
- ✅ 성공 시 확인 메시지 표시

### US-012 (크레딧 확인)
- ✅ 에피소드 최하단에 크레딧 섹션 표시
- ✅ 상위 3인 Main Producer 별도 UI 강조
- ✅ 채택된 제안자 우선 표시 후 금액순 정렬
- ✅ 크레딧 데이터 영구 저장 (변경 불가)

### US-013 (제안 목록)
- ✅ 모금액 순 정렬
- ✅ 각 제안에 내용, total_bounty, backer_count 표시
- ✅ 지지하기 버튼 제공

---

## 9. Design Requirements

From `CLAUDE-FE-DEV.md`:

### Modal Design
- 모달 배경: `bg-background/95 backdrop-blur-sm`
- 모달 컨테이너: `max-w-md mx-auto p-6 rounded-lg border border-border shadow-lg`
- 입력 필드: shadcn/ui `<Input>` 컴포넌트 사용
- 버튼: shadcn/ui `<Button>` 컴포넌트 (variant="outline" / "default")

### Proposal Card Design
- 카드 레이아웃: `border border-border rounded-sm p-4 space-y-3`
- 제안 내용: `text-foreground text-sm leading-relaxed`
- 모금액/지지자 수: `text-xs font-mono text-muted-foreground`
- 지지하기 버튼: `<Button size="sm" variant="outline">`

### Credit Section Design
- Main Producer 강조: `text-2xl font-serif font-bold`
- 기여자 리스트: `grid grid-cols-2 gap-x-6 gap-y-2 text-sm`
- 섹션 구분: `border-t-2 border-border pt-12 mt-24`

---

## 10. Mock Data Requirements

### Proposal Mock Data
```typescript
{
  id: 'proposal-1',
  episodeId: 'ep-1-3',
  proposerId: 'user-101',
  description: '두 사람이 만나는 장면에서 비가 내렸으면 좋겠어요.',
  seedMoney: 1000,
  totalBounty: 4500,
  backerCount: 3,
  status: 'PENDING',
  createdAt: '2024-12-02T10:00:00Z'
}
```

### Credit Mock Data
Already exists in episodeService.ts (`mockCredits`)

---

## 11. Related Documents

- Source: `docs/user_stories.md` - US-009, US-010, US-011, US-012, US-013
- Source: `docs/conceptual_model.json` - Proposal, Backing, Credit, Escrow entities
- Source: `docs/logical_architecture.md` - Intervention Domain components
- Source: `docs/ia_structure.md` - Episode Viewer integration
- Guidance: `CLAUDE-FE-DEV.md` - Modal/form patterns, monochrome design
