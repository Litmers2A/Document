# F03: 인증 (Authentication)

## 1. Feature Overview

**Purpose**: 사용자가 이메일 또는 소셜 계정(카카오/구글)으로 회원가입하고 로그인하여 서비스를 이용할 수 있도록 합니다. 회원가입 후 닉네임 설정 및 본인인증을 통해 플랫폼 내 활동을 시작할 수 있습니다.

**Scope Boundary**:
- IN: 이메일 회원가입, 이메일 로그인, 소셜 로그인 (카카오/구글), 닉네임 설정, 본인인증, 로그아웃, JWT 기반 세션 관리
- OUT: 비밀번호 재설정, 2단계 인증, 이메일 재인증, OAuth 외 추가 소셜 로그인 (네이버, 애플 등)

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | 독자 | 이메일 회원가입 | 회원가입 페이지 접속 → 이메일/비밀번호 입력 → 약관 동의 → 인증 메일 발송 → 프로필 설정 페이지 이동 → 닉네임 입력 (중복 확인) → 본인인증 → 완료 |
| UF-02 | 독자 | 소셜 회원가입 | 회원가입 페이지 접속 → 카카오/구글 버튼 클릭 → OAuth 인증 → 약관 동의 → 프로필 설정 페이지 이동 → 닉네임 입력 → 본인인증 → 완료 |
| UF-03 | 독자 | 로그인 | 로그인 페이지 접속 → 이메일/비밀번호 입력 → JWT 토큰 발급 → 홈 화면 리다이렉트 |
| UF-04 | 독자 | 소셜 로그인 | 로그인 페이지 접속 → 카카오/구글 버튼 클릭 → OAuth 인증 → JWT 토큰 발급 → 홈 화면 리다이렉트 |

Source: user_stories_data.json (US-001, US-002)

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| Login | `/login` | 이메일/소셜 로그인 진입점 |
| Signup | `/signup` | 이메일/소셜 회원가입 진입점 |
| Terms | `/terms` | 서비스 이용 약관 동의 |
| ProfileSetup | `/profile/setup` | 닉네임 설정 및 본인인증 (회원가입 완료 후) |

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| User | id, email, password, nickname, role, authProvider, status, isVerified, createdAt | → Wallet (1:1) |
| Wallet | id, userId, currentBalance (초기값 0) | ← User (N:1) |

**Important Domain Rules**:
- DR-001: 닉네임은 플랫폼 전체에서 중복 불가 (UNIQUE 제약)
- DR-013: SUSPENDED 또는 PERMANENT_BAN 상태 사용자는 로그인 차단
- 회원가입 시 Wallet 자동 생성 (currentBalance = 0)

Source: conceptual_model.json

## 5. Component Strategy

**Pages**:
- `LoginPage` (`app/(public)/login/page.tsx`) - 로그인 폼 렌더링
- `SignupPage` (`app/(public)/signup/page.tsx`) - 회원가입 폼 렌더링
- `TermsPage` (`app/(public)/terms/page.tsx`) - 약관 동의 UI
- `ProfileSetupPage` (`app/(auth)/profile/setup/page.tsx`) - 프로필 설정 폼 렌더링

**Components**:
- `LoginForm` - 이메일/비밀번호 입력 폼, 유효성 검증
- `SignupForm` - 회원가입 다단계 폼
- `SocialLoginButtons` - 카카오/구글 OAuth 버튼
- `ProfileSetupForm` - 닉네임 입력, 중복 확인, 본인인증 모듈 통합

**Hooks**:
- `useAuth` - AuthenticationService 호출, AuthStore 연동

**Services**:
- `AuthenticationService` - 로그인/회원가입/로그아웃/이메일 인증 (Mock API)
- `UserProfileService` - 프로필 조회/수정, 닉네임 중복 확인, 본인인증 (Mock API)

**State**:
- Zustand AuthStore (`stores/authStore.ts`) - 로그인 사용자 정보, 토큰 관리
- React Hook Form - 폼 상태 및 유효성 검증 (Zod 스키마)

Source: logical_architecture.md

## 6. Task Mapping

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-015 | Login Page UI | 로그인 페이지 레이아웃 및 폼 통합 | `app/(public)/login/page.tsx` |
| task-016 | LoginForm 컴포넌트 | 이메일/비밀번호 로그인 폼 UI 및 검증 | `components/auth/LoginForm.tsx` |
| task-017 | SocialLoginButtons 컴포넌트 | 카카오/구글 OAuth 버튼 | `components/auth/SocialLoginButtons.tsx` |
| task-018 | Signup Page UI | 회원가입 페이지 레이아웃 및 폼 통합 | `app/(public)/signup/page.tsx` |
| task-019 | SignupForm 컴포넌트 | 회원가입 다단계 폼 UI | `components/auth/SignupForm.tsx` |
| task-020 | Terms Page UI | 약관 동의 페이지 | `app/(public)/terms/page.tsx` |
| task-021 | ProfileSetup Page UI | 프로필 설정 페이지 레이아웃 | `app/(auth)/profile/setup/page.tsx` |
| task-022 | ProfileSetupForm 컴포넌트 | 닉네임 설정 및 본인인증 폼 | `components/auth/ProfileSetupForm.tsx` |
| task-023 | useAuth Hook 구현 | AuthStore 연동, 로그인/회원가입 로직 | `hooks/useAuth.ts` |
| task-024 | Auth Service (Mock API) | 인증 관련 Mock API 구현 | `services/authService.ts` |

Source: tasks/tasks.json

## 7. Implementation Notes

### Technical Requirements
- JWT 토큰 기반 인증 (localStorage 저장)
- React Hook Form + Zod를 활용한 폼 검증
- TanStack Query를 통한 API 호출 (mutation)
- 모든 응답은 3초 이내 처리 (AC 기준)

### Validation Rules
- 이메일: RFC 5322 표준 준수
- 비밀번호: 최소 8자 이상
- 닉네임: 2~15자, 중복 불가, 실시간 중복 확인 API 호출

### Error Handling
- 잘못된 비밀번호: "비밀번호가 일치하지 않습니다"
- 잔액 부족: "잔액이 부족합니다" (다른 기능에서 사용)
- 닉네임 중복: "이미 사용 중인 닉네임입니다"
- 네트워크 오류: "일시적인 오류가 발생했습니다"

### Accessibility
- 폼 필드에 적절한 label 및 aria-label 제공
- 키보드 탐색 지원
- 에러 메시지 screen reader 읽기 지원

## 8. Acceptance Criteria Checklist

### US-001 (회원가입)
- [x] AC-1: 이메일 입력 후 인증 메일 발송 시 '인증 메일이 발송되었습니다' 메시지 표시
- [x] AC-2: 카카오/구글 소셜 로그인 버튼 클릭 시 해당 OAuth 인증 페이지로 리다이렉트
- [x] AC-3: 회원가입 완료 후 프로필 설정 페이지로 3초 이내 이동
- [x] AC-4: 닉네임 중복 시 '이미 사용 중인 닉네임입니다' 에러 표시

### US-002 (로그인)
- [x] AC-1: 올바른 이메일/비밀번호 입력 시 JWT 토큰 발급 및 홈 화면으로 이동
- [x] AC-2: 잘못된 비밀번호 입력 시 '비밀번호가 일치하지 않습니다' 에러 표시
- [x] AC-3: 소셜 로그인 성공 시 3초 이내 홈 화면으로 이동

## 9. Dependencies

**Prerequisite Tasks**:
- task-001: Next.js 14 프로젝트 초기화 (완료 필요)
- task-002: Tailwind CSS 설정 (완료 필요)
- task-003: shadcn/ui 설치 및 기본 UI 컴포넌트 설정 (완료 필요)
- task-004: TypeScript 타입 정의 (user.ts 등 완료 필요)
- task-005: Zustand 스토어 설정 (authStore 완료됨 ✓)
- task-006: TanStack Query Provider 설정 (완료됨 ✓)
- task-008: Public Layout 설정 (완료됨 ✓)
- task-009: Auth Layout 설정 (완료됨 ✓)

**Blocking Tasks**:
- 인증 기능 완료 후 모든 인증 필수 화면 접근 가능

## 10. Testing Strategy

### Unit Tests
- AuthenticationService: login, signup, socialLogin, logout 함수
- UserProfileService: checkNicknameAvailability, updateProfile
- useAuth Hook: 상태 변화 테스트

### Integration Tests
- 회원가입 전체 플로우 (이메일 → 약관 → 프로필 설정)
- 로그인 후 홈 리다이렉트
- 닉네임 중복 확인 API 호출

### E2E Tests (Optional)
- 소셜 로그인 OAuth 플로우
- 본인인증 모듈 통합

---

**문서 생성일**: 2026-01-08
**Feature ID**: F03
**관련 User Stories**: US-001, US-002
**우선순위**: P1 (MVP 필수)
