# F09: 설정 (Settings)

## 1. Feature Overview

**Purpose**: 로그인한 사용자가 프로필 정보(닉네임, 프로필 이미지)를 수정하고 약관 동의 상태를 관리할 수 있는 설정 페이지를 제공한다.

**Scope Boundary**:
- IN: 프로필 이미지 업로드, 닉네임 변경 및 중복 확인, 선택 약관(마케팅) 동의/철회
- OUT: 비밀번호 변경, 계정 삭제, 알림 설정, 이메일 변경

## 2. User Flows

| Flow ID | Actor | Goal | Key Steps |
|---------|-------|------|-----------|
| UF-01 | 독자/작가 | 프로필 이미지 변경 | 마이페이지 → 설정 → 이미지 업로드 (드래그앤드롭 또는 클릭) → 미리보기 확인 → 저장 |
| UF-02 | 독자/작가 | 닉네임 변경 | 마이페이지 → 설정 → 닉네임 입력 → 실시간 중복 확인 (500ms debounce) → 저장 |
| UF-03 | 독자/작가 | 약관 동의 변경 | 마이페이지 → 설정 → 마케팅 동의 토글 → 저장 |

Source: ia_structure.md, task notes

## 3. Screens & Routes

| Screen | Route | Purpose |
|--------|-------|---------|
| Settings Page | /settings | 프로필 수정 및 약관 동의 관리 통합 페이지 |

**Entry Points**:
- Primary: MyPage → Settings 링크
- Secondary: TopNav 프로필 드롭다운 → Settings (if implemented)
- Mobile: 햄버거 메뉴 → MyPage → Settings

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

| Entity | Key Attributes | Relations |
|--------|----------------|-----------|
| User | nickname (unique, 2-12자, 한글/영문/숫자/`_`/`.`), profileImageUrl, consents (JSON: {serviceTerms, privacyPolicy, communityRules, marketingConsent}) | → Wallet (1:1) |

**Domain Rules**:
- DR-001: 닉네임 고유성 (플랫폼 전체에서 중복 불가, 크레딧 표기용)
- 닉네임 검증: 2~12자, 한글/영문/숫자/`_`/`.`만 허용
- 프로필 이미지: JPG/PNG/WEBP, 최대 3MB
- 필수 약관(serviceTerms, privacyPolicy, communityRules): 고정, 변경 불가
- 선택 약관(marketingConsent): 토글 가능

Source: conceptual_model.json, ia_structure.md

## 5. Component Strategy

**Pages**:
- `SettingsPage` (`/settings`) - 프로필 수정 및 약관 동의 통합 설정 페이지

**Components**:
- `ProfileSection` - 프로필 이미지 업로드 + 닉네임 입력 섹션 컨테이너
  - `ProfileImageUpload` - 드래그앤드롭/클릭 업로드 UI, 미리보기
  - `NicknameInput` - 실시간 중복 확인 (500ms debounce) 입력 필드
- `ConsentSettings` - 약관 동의 체크박스 그룹 (필수 약관 고정, 선택 약관 토글 가능)

**Hooks**:
- `useProfileUpdate` - 프로필 이미지 업로드 및 닉네임 업데이트 mutation
- `useNicknameValidation` - 닉네임 중복 확인 API 호출 (debounce 적용)
- `useConsentUpdate` - 약관 동의 상태 업데이트 mutation

**Services**:
- `ProfileService` (Mock API) - 프로필 수정 API (이미지 업로드, 닉네임 변경, 중복 확인)
- `ConsentService` (Mock API) - 약관 동의 관리 API

**State**:
- AuthStore 동기화: 프로필 업데이트 성공 시 authStore의 user 정보 갱신
- Form state: React Hook Form 사용 (validation, error handling)
- Upload state: 이미지 업로드 진행 상태 (loading, preview URL)

Source: logical_architecture.md, tasks.json

## 6. Task Mapping

| Task ID | Title | Implements | Files |
|---------|-------|------------|-------|
| task-096 | Settings Page UI | 설정 페이지 레이아웃 및 통합 | `app/(auth)/settings/page.tsx` |
| task-097 | ProfileSection 컴포넌트 | 프로필 섹션 컨테이너 | `components/settings/ProfileSection.tsx` |
| task-098 | ProfileImageUpload 컴포넌트 | 프로필 이미지 업로드 UI | `components/settings/ProfileImageUpload.tsx` |
| task-099 | NicknameInput 컴포넌트 | 닉네임 입력 + 중복 확인 | `components/settings/NicknameInput.tsx` |
| task-100 | ConsentSettings 컴포넌트 | 약관 동의 체크박스 그룹 | `components/settings/ConsentSettings.tsx` |
| task-101 | useProfileUpdate Hook | 프로필 업데이트 mutation | `hooks/useProfileUpdate.ts` |
| task-102 | useNicknameValidation Hook | 닉네임 중복 확인 (debounce) | `hooks/useNicknameValidation.ts` |
| task-103 | useConsentUpdate Hook | 약관 동의 상태 업데이트 | `hooks/useConsentUpdate.ts` |
| task-104 | Profile Service (Mock API) | 프로필 수정 API 모듈 | `services/profileService.ts` |
| task-105 | Consent Service (Mock API) | 약관 동의 관리 API 모듈 | `services/consentService.ts` |

**Dependencies**:
- task-096 depends on: task-009 (Auth Layout), task-097, task-100
- task-097 depends on: task-003 (shadcn/ui), task-098, task-099
- task-099 depends on: task-003, task-102
- task-100 depends on: task-003, task-103
- task-101 depends on: task-104
- task-102 depends on: task-104
- task-103 depends on: task-105
- task-104, task-105 depend on: task-004 (TypeScript types)

**Execution Order**:
1. Phase 1 (Services): task-104, task-105
2. Phase 2 (Hooks): task-101, task-102, task-103
3. Phase 3 (Components): task-098, task-099, task-100, task-097
4. Phase 4 (Page): task-096

Source: tasks.json

## 7. Open Questions

1. **프로필 이미지 저장 방식**: Mock API에서 이미지 파일을 어떻게 처리할지 결정 필요 (Base64 인코딩? URL mock?)
2. **약관 전문 보기 링크**: 약관 전문 페이지(/terms)로 이동할지, 모달로 표시할지 결정 필요
3. **저장 버튼 동작**: 프로필 섹션과 약관 섹션을 하나의 저장 버튼으로 통합할지, 각각 별도 저장할지 결정 필요 (현재 IA 문서에는 통합 저장 버튼으로 기술됨)
4. **AuthStore 동기화 시점**: 프로필 업데이트 성공 직후 즉시 authStore를 갱신할지, 페이지 리로드 시 서버에서 다시 가져올지 결정 필요

---

**문서 정보**:
- **생성일**: 2026-01-09
- **기반 문서**: tasks.json, ia_structure.md, conceptual_model.json, logical_architecture.md
