# F01: 프로젝트 설정

> **Feature Type:** Foundation / Infrastructure
> **Status:** In Progress
> **Tasks:** task-001, task-002, task-003, task-004, task-005, task-006
> **Dependencies:** None (Foundation layer)

---

## 1. Feature Overview

### Purpose
Establish the foundational Next.js 14 application structure with essential tooling, styling framework, state management, and TypeScript type definitions required for all subsequent feature development.

### Scope
- Next.js 14 App Router configuration
- Tailwind CSS 4 setup with design tokens
- shadcn/ui component library integration
- TypeScript type definitions from conceptual model
- Zustand client state management
- TanStack Query server state management

### Out of Scope
- Feature-specific business logic
- Page/component implementation (covered in feature-specific plans)
- API integration (mock services only)

---

## 2. Data Model

### Core Entities (from conceptual_model.json)

The following entities require TypeScript type definitions:

1. **User** - Platform users (Reader, Creator, Admin)
   - Attributes: id, email, nickname, role, authProvider, status, isVerified
   - Role enum: READER | CREATOR | ADMIN
   - AuthProvider enum: EMAIL | KAKAO | GOOGLE
   - Status enum: ACTIVE | SUSPENDED | DELETED

2. **Wallet** - User Ink balance management
   - Attributes: id, userId, currentBalance, payableBalance, withdrawableBalance

3. **Transaction** - Immutable Ink flow log
   - Type enum: CHARGE | USE_PROPOSAL | USE_BACKING | USE_DIRECT | REFUND | SETTLEMENT | WITHDRAWAL
   - Direction enum: IN | OUT

4. **Payment** - Ink purchase records
   - Status enum: PENDING | COMPLETED | FAILED | CANCELLED
   - PaymentMethod enum: CARD | KAKAO_PAY | TOSS | PAYPLE

5. **Story** - Creator's serialized work
   - Status enum: DRAFT | ACTIVE | HIDDEN | DELETED

6. **Episode** - Individual story chapter
   - Status enum: DRAFT | OPEN | PUBLISHED | HIDDEN | DELETED

7. **EpisodeContent** - Episode content items
   - ContentType enum: IMAGE | TEXT
   - isFinal: boolean (draft vs final)

8. **Proposal** - Reader's story intervention request
   - Status enum: PENDING | DEAL | DROP

9. **Backing** - Support for proposal or direct backing
   - Type enum: PROPOSAL_BACKING | DIRECT_BACKING
   - Status enum: ESCROWED | SETTLED | REFUNDED

10. **Credit** - Immutable contributor credits
    - contributors: JSON array
    - mainProducers: UUID array (top 3)

11. **Escrow** - Proposal fund holding account
    - Status enum: HOLDING | RELEASED | REFUNDED

12. **WithdrawalRequest** - Creator withdrawal request
    - Status enum: PENDING | APPROVED | REJECTED | COMPLETED

13. **Sanction** - Admin-imposed user sanctions
    - Type enum: WARNING | SUSPENSION | PERMANENT_BAN

---

## 3. Architecture Context

### Tech Stack (from logical_architecture.md)
- **Framework:** Next.js 14 (App Router)
- **UI:** React 19, TypeScript
- **Styling:** Tailwind CSS 4, shadcn/ui
- **State:** TanStack Query (server), Zustand (client)
- **Icons:** Lucide React

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

### Core Principles (from logical_architecture.md)
1. **Single Responsibility Principle (SRP):** Each component handles exactly one cohesive responsibility
2. **Domain Separation:** Components grouped by business domain
3. **Layered Architecture:** Pages → Components → Hooks → Services → Types
4. **Unidirectional Data Flow:** Props down, events up, state managed via hooks/stores

---

## 4. Design Requirements

From `CLAUDE-FE-DEV.md`:

### Design System
- **Monochrome only** - no accent colors
- **Images → gray boxes** - use `bg-muted` placeholder divs
- **Minimal & reactive** - focus on layout, hierarchy, spacing
- **Follow globals.css** - use existing color tokens (foreground, background, muted, border)

### Code Patterns
- **Service**: `export const xxxService = { async method(): Promise<Type> {...} }`
- **Hook**: TanStack Query wrapper returning `{ data, isLoading, isError }`
- **Component**: `'use client'` directive, PascalCase, `cn()` for classnames
- **Page**: Uses custom hooks, skeleton loading, `max-w-3xl` container

---

## 5. Task Mapping

### Task-001: Next.js 14 프로젝트 초기화 및 기본 설정
**Status:** pending
**Files:** next.config.js, package.json, tsconfig.json
**Deliverable:**
- Initialize Next.js 14 with App Router
- Configure TypeScript strict mode
- Set up basic project structure (app/, components/, lib/, types/)
- Install base dependencies (React 19, Next.js 14, TypeScript)

### Task-002: Tailwind CSS 설정 및 디자인 토큰 정의
**Status:** pending
**Dependencies:** task-001
**Files:** tailwind.config.ts, app/globals.css
**Deliverable:**
- Install and configure Tailwind CSS 4
- Define monochrome design tokens in globals.css
- Configure color palette (foreground, background, muted, border)
- Set up typography scale and spacing system

### Task-003: shadcn/ui 설치 및 기본 UI 컴포넌트 설정
**Status:** pending
**Dependencies:** task-002
**Files:** components/ui/*, components.json
**Deliverable:**
- Install shadcn/ui CLI
- Add essential UI components:
  - Button
  - Card
  - Dialog
  - Input
  - Select
  - Skeleton
  - Toast
- Configure components.json with project paths

### Task-004: TypeScript 타입 정의 (Conceptual Model 기반)
**Status:** in_progress
**Dependencies:** task-001
**Files:** types/*.ts, types/index.ts
**Deliverable:**
- Define TypeScript interfaces/types for all 13 entities from conceptual model
- Create type exports in types/index.ts
- Include all enums (Role, AuthProvider, EpisodeStatus, ProposalStatus, etc.)
- Add utility types for API responses (PaginatedList, ApiResponse, etc.)

**Current Status:** user.ts, story.ts, index.ts completed. Remaining: wallet.ts, episode.ts, proposal.ts, backing.ts

### Task-005: Zustand 스토어 설정 (Auth, UI)
**Status:** completed ✅
**Dependencies:** task-004
**Files:** stores/authStore.ts, stores/uiStore.ts, stores/index.ts
**Deliverable:**
- authStore: User session, authentication state
- uiStore: UI state (modals, toasts, navigation)

### Task-006: TanStack Query Provider 설정
**Status:** completed ✅
**Dependencies:** task-001
**Files:** app/providers.tsx, lib/queryClient.ts
**Deliverable:**
- Configure QueryClient with default options
- Create Providers wrapper component
- Set up React Query DevTools (development only)

---

## 6. Acceptance Criteria

### task-001
- [x] `npm run dev` starts development server without errors
- [x] TypeScript strict mode enabled in tsconfig.json
- [x] App Router structure created (app/ directory)

### task-002
- [x] Tailwind CSS classes work in components
- [x] Design tokens defined in globals.css (--foreground, --background, --muted, --border)
- [x] `cn()` utility function available for classname merging

### task-003
- [x] shadcn/ui components installable via CLI
- [x] Essential components (Button, Card, Dialog, Input) render correctly
- [x] Components follow monochrome design system

### task-004
- [ ] All 13 entity types defined with correct attributes
- [ ] All enums exported from types/index.ts
- [ ] No TypeScript errors in type definitions
- [ ] Type imports work: `import { User, Story, Episode } from '@/types'`

### task-005
- [x] authStore maintains user session across page reloads
- [x] uiStore triggers reactive UI updates

### task-006
- [x] Providers component wraps app in app/layout.tsx
- [x] React Query DevTools visible in development mode
- [x] Query caching works (visible in DevTools)

---

## 7. Dependencies

### External
- Next.js 14+
- React 19
- TypeScript 5+
- Tailwind CSS 4
- shadcn/ui
- TanStack Query v5
- Zustand
- Lucide React (icons)

### Internal
None (foundation layer)

---

## 8. Notes

- This feature is purely infrastructure - no user-facing functionality
- All subsequent features depend on this foundation
- Type definitions must match conceptual_model.json exactly
- Follow CLAUDE-FE-DEV.md guidelines for all code patterns
- Monochrome design system is non-negotiable

---

## 9. Related Documents

- Source: `docs/conceptual_model.json` - Entity definitions
- Source: `docs/logical_architecture.md` - Architecture patterns
- Guidance: `CLAUDE-FE-DEV.md` - Development guidelines
- Guidance: `docs/design-requirements.md` - Design system rules
