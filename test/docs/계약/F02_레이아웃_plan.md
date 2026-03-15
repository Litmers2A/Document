# F02: 레이아웃

## 1. Feature Overview

**Purpose**: 앱 전체의 레이아웃 구조(Root, Public, Auth, Creator, Admin)와 공통 네비게이션 컴포넌트(Bottom Nav, Side Nav, Header)를 제공하여 일관된 UI 구조와 역할 기반 접근 제어를 구현한다.

**Scope Boundary**:
- IN: Root Layout, Route Group Layouts (public/auth/creator/admin), Bottom Navigation, Side Navigation, Header
- OUT: 개별 페이지 콘텐츠, 인증 로직 구현, 권한 체크 API 연동

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | 독자 | 하단 네비게이션으로 주요 화면 이동 | 홈/탐색/마이페이지 탭 선택 → 해당 화면 이동 |
| UF-02 | 작가 | 사이드 네비게이션으로 스튜디오 기능 접근 | 스튜디오 진입 → 사이드 메뉴에서 기능 선택 |
| UF-03 | 관리자 | 사이드 네비게이션으로 관리 기능 접근 | 관리자 페이지 진입 → 사이드 메뉴에서 기능 선택 |

Source: ia_structure.md - Navigation Structure

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| Root Layout | / (전역) | Provider 통합, 전역 스타일 적용 |
| Public Layout | /login, /signup, /terms | 비인증 사용자용 레이아웃 |
| Auth Layout | /home, /explore, /mypage 등 | 인증된 사용자용 레이아웃 + 인증 체크 |
| Creator Layout | /studio/* | 작가 전용 레이아웃 + 권한 체크 |
| Admin Layout | /admin/* | 관리자 전용 레이아웃 + 권한 체크 |

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| User | id, role (READER/CREATOR/ADMIN), status | → 레이아웃 접근 권한 결정 |

Source: conceptual_model.json

## 5. Component Strategy

**Layouts**:
- `app/layout.tsx` - Root Layout, Provider 통합 (TanStack Query, Zustand)
- `app/(public)/layout.tsx` - 비인증 영역 레이아웃
- `app/(auth)/layout.tsx` - 인증 영역 레이아웃, BottomNav 포함
- `app/(creator)/layout.tsx` - 작가 영역 레이아웃, SideNav 포함
- `app/(admin)/layout.tsx` - 관리자 영역 레이아웃, SideNav 포함

**Components**:
- `BottomNav` - 독자용 하단 3탭 네비게이션 (홈/탐색/마이페이지)
- `SideNav` - 작가/관리자용 사이드 네비게이션
- `Header` - 공통 헤더 컴포넌트

**State**:
- AuthStore에서 user.role 참조하여 레이아웃 접근 권한 결정
- UIStore에서 현재 활성 네비게이션 탭 관리

Source: logical_architecture.md

## 6. Task Mapping

| Task ID | Title | Implements | Files | Status |
|---------|-------|------------|-------|--------|
| task-007 | Root Layout 및 Provider 통합 | 전역 Provider 래핑 | app/layout.tsx | completed |
| task-008 | Public Layout (비인증 영역) | 비인증 사용자용 레이아웃 | app/(public)/layout.tsx | pending |
| task-009 | Auth Layout (인증 체크) | 인증 사용자용 레이아웃 | app/(auth)/layout.tsx | completed |
| task-010 | Creator Layout (작가 전용) | 작가 권한 체크 + SideNav | app/(creator)/layout.tsx | pending |
| task-011 | Admin Layout (관리자 전용) | 관리자 권한 체크 + SideNav | app/(admin)/layout.tsx | pending |
| task-012 | Bottom Navigation 컴포넌트 | 3탭 하단 네비게이션 | components/layout/BottomNav.tsx | in_progress |
| task-013 | Side Navigation 컴포넌트 | 작가/관리자용 사이드 메뉴 | components/layout/SideNav.tsx | pending |
| task-014 | Header 컴포넌트 | 공통 헤더 | components/layout/Header.tsx | pending |

Source: tasks.json

## 7. Execution Order

의존성 기반 실행 순서:
1. task-008: Public Layout (task-007 완료 의존)
2. task-012: Bottom Navigation (task-003 shadcn 의존, 변경사항 반영 필요)
3. task-010: Creator Layout (task-009 완료 의존)
4. task-011: Admin Layout (task-009 완료 의존)
5. task-013: Side Navigation (task-003 의존)
6. task-014: Header (task-003 의존)

## 8. Change Notes

| Task | Change | Source |
|------|--------|--------|
| task-012 | 4탭→3탭으로 수정 필요. /shop 탭 제거 | change-2026-01-07-1200 |

## 9. Navigation Structure Reference

### Bottom Navigation (3탭)
```
┌───────────┬───────────┬───────────┐
│    홈     │   탐색    │ 마이페이지 │
│   /home   │ /explore  │  /mypage  │
└───────────┴───────────┴───────────┘
```

### Side Navigation (Creator)
```
크리에이터 스튜디오
├── 대시보드 (/studio)
├── 에피소드 관리 (/studio/episodes)
├── 제안 관리 (/studio/proposals)
└── 정산 관리 (/studio/settlements)
```

### Side Navigation (Admin)
```
관리자
├── 대시보드 (/admin)
├── 회원 관리 (/admin/users)
├── 콘텐츠 관리 (/admin/contents)
├── 결제 관리 (/admin/payments)
├── 정산 관리 (/admin/settlements)
└── 신고 관리 (/admin/reports)
```

## 10. Open Questions

- 없음 (모든 정보가 문서에 명시됨)
