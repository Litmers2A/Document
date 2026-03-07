# Information Architecture - 쟈근친구들 V2 (Conture)

> Generated from: user_stories_data.json, conceptual_model.json
Date: 2026-01-06

## Screen Hierarchy Overview

```
Root
├── Public (비인증)
│   ├── 스플래시 (/)
│   ├── 로그인 (/login)
│   ├── 회원가입 (/signup) [DEPRECATED - 소셜 로그인 자동 가입으로 대체, change-2026-02-04-1450]
│   ├── 온보딩 (/onboarding) [NEW - change-2026-02-05-0918]
│   │   ├── 닉네임 설정 (/onboarding/nickname)
│   │   └── 휴대폰 인증 (/onboarding/phone)
│   └── 약관 동의 (/terms)
│
├── Common (인증 공통)
│   ├── 프로필 설정 (/profile/setup) [DEPRECATED - 온보딩으로 통합, change-2026-02-05-0918]
│   ├── 마이페이지
│   │   ├── 내 정보 (/mypage)
│   │   ├── 내 지갑 (/mypage/wallet)
│   │   │   └── 진행 중인 제안 내역 (/mypage/wallet/pending) → /mypage/participation-history?filter=pending으로 리다이렉트
│   │   ├── 참여 내역 (/mypage/participation-history) ← RENAMED from /mypage/proposals (change-2026-02-04-1450)
│   │   │   └── 제안 상세 (/mypage/proposals/[id]) ← NEW (change-2026-01-19-1100)
│   │   ├── 신고 내역 (/mypage/reports)
│   │   ├── 크리에이터 프로필 설정 (/mypage/creator/setup) ← NEW (change-2026-01-15-1000)
│   │   └── 크리에이터 프로필 수정 (/mypage/creator/edit) ← NEW (change-2026-01-15-1000)
│   ├── 설정 (/settings)
│   └── Ink 충전 (/shop) ← 마이페이지에서 진입
│
├── Reader (독자)
│   ├── 홈 (/home)
│   ├── 최근 본 작품 전체 (/recently-viewed) ← NEW (change-2026-02-04-1450)
│   ├── 탐색 (/explore)
│   ├── 검색 결과 (/search)
│   ├── 작품 상세 (/stories/:storyId)
│   ├── 작가 상세 (/creators/:creatorId)
│   └── 에피소드 뷰어 (/stories/:storyId/episodes/:episodeId)
│
├── Creator (작가)
│   ├── 크리에이터 스튜디오 (/studio)
│   │   ├── 에피소드 관리 (/studio/episodes)
│   │   ├── 에피소드 편집 (/studio/episodes/:episodeId/edit)
│   │   └── 제안 관리 (/studio/proposals)
│   ├── 정산 관리 (/studio/settlement) ← 페이지 분리 (change-2026-01-21-1135)
│   │   └── 정산 내역 (/studio/settlement-history) ← NEW (change-2026-01-21-1135)
│
└── Admin (관리자)
    ├── 대시보드 (/admin)
    ├── 회원 관리 (/admin/users)
    ├── 콘텐츠 관리 (/admin/contents)
    ├── 결제 관리 (/admin/payments)
    ├── 정산 관리 (/admin/settlements)
    └── 신고 관리 (/admin/reports)
```

---

## Detailed Screen Definitions

### Public Pages (비인증 영역)

#### Page: Splash

- **Route**: `/`

- **Purpose**: 앱 진입점, 인증 상태에 따라 리다이렉트

- **User Stories**: -

- **Access**: 모든 사용자

- **Redirects**:

로그인 상태 → `/home`

- 비로그인 상태 → `/login`

#### Page: Login (로그인)

- **Route**: `/login`

- **Purpose**: 소셜 로그인 전용 (change-2026-02-04-1450)

- **User Stories**: US-002

- **Access**: 비로그인 사용자

- **Components**: (change-2026-02-04-1450, change-2026-02-05-0918 업데이트)

  - 카카오 소셜 로그인 버튼
  - 구글 소셜 로그인 버튼
  - 첫 로그인 시: 약관 동의 모달 → 자동 가입 완료 → 온보딩 페이지로 리다이렉트

- **Redirects** (change-2026-02-05-0918):
  - 온보딩 미완료 → `/onboarding`
  - 온보딩 완료 → `/home`

#### Page: Signup (회원가입)

- **Route**: `/signup`

- **Purpose**: [DEPRECATED] 소셜 로그인 자동 가입으로 대체됨 (change-2026-02-04-1450)

- **User Stories**: US-001

- **Access**: 비로그인 사용자

- **Components**: [DEPRECATED - change-2026-02-04-1450]

이메일 입력 필드

- 비밀번호 입력 필드 + 실시간 규칙 체크 UI

- 비밀번호 확인 필드

- 휴대폰 번호 입력 + 인증번호 전송 버튼

- 인증번호 입력 필드 + 타이머 (5분)

- 소셜 회원가입 버튼

#### Page: Onboarding (온보딩) [NEW - change-2026-02-05-0918]

- **Route**: `/onboarding`

- **Purpose**: 소셜 로그인 후 필수 온보딩 플로우 (닉네임 설정 + 휴대폰 인증)

- **User Stories**: US-001-1

- **Access**: 로그인 완료 + 온보딩 미완료 사용자

- **Components**:
  - 온보딩 진행률 표시 (Step 1/2, Step 2/2)
  - OnboardingFlow 컴포넌트
  - 단계별 네비게이션

- **Entry Point**: 소셜 로그인 성공 후 자동 리다이렉트 (onboardingCompleted = false)

- **Exit**: 온보딩 완료 후 `/home`으로 리다이렉트

#### Page: Onboarding Nickname (닉네임 설정) [NEW - change-2026-02-05-0918]

- **Route**: `/onboarding/nickname`

- **Purpose**: 닉네임 입력 및 중복 검사

- **User Stories**: US-001-1

- **Access**: 온보딩 진행 중 사용자

- **Components**:
  - NicknameSetup 컴포넌트
  - 닉네임 입력 필드 (2-12자, 한글/영문/숫자)
  - 실시간 중복 검사 (500ms debounce)
  - 검증 피드백 메시지
  - 다음 단계 버튼

#### Page: Onboarding Phone (휴대폰 인증) [NEW - change-2026-02-05-0918]

- **Route**: `/onboarding/phone`

- **Purpose**: 휴대폰 번호 인증 (SMS OTP)

- **User Stories**: US-001-1

- **Access**: 온보딩 진행 중 사용자 (닉네임 설정 완료)

- **Components**:
  - PhoneVerification 컴포넌트
  - 휴대폰 번호 입력 (010XXXXXXXX)
  - SMS 발송 버튼
  - OTP 입력 필드 (6자리)
  - 타이머 (3분)
  - 재전송 버튼
  - 완료 버튼

#### Page: SignupComplete (회원가입 완료) [DEPRECATED - change-2026-02-05-0918]

- **Route**: `/signup/complete`

- **Purpose**: [DEPRECATED] 온보딩 플로우로 대체됨

- **User Stories**: US-001

- **Access**: -

- **Components**: -

#### Page: ParticipationHistory (참여 내역) ← NEW (change-2026-02-04-1450)

- **Route**: `/mypage/participation-history`

- **Purpose**: 제안/지지/후원 통합 내역 조회

- **User Stories**: US-TBD

- **Access**: 로그인한 독자/작가

- **Components**:

  - 참여 내역 리스트
    - 작품명
    - 참여 유형 뱃지 (PROPOSER/BACKER/SPONSOR)
    - 금액
    - 참여일
    - 상태 (채택 / 진행중 / 미채택)
  - 필터: 전체 / 제안 / 지지 / 후원

#### Page: RecentlyViewed (최근 본 작품) ← NEW (change-2026-02-04-1450)

- **Route**: `/recently-viewed`

- **Purpose**: 최근 본 작품 전체 리스트 (메인 홈에서 더보기 클릭 시)

- **User Stories**: US-TBD

- **Access**: 로그인한 독자/작가

- **Components**:

  - 최근 본 작품 리스트 (전체)
    - 최근 열람 순 DESC
    - 작품 상세 진입 시 기록
    - 에피소드 열람 시 업데이트

#### Page: PasswordReset (비밀번호 재설정) (change-2026-01-14-1500)

- **Route**: `/password-reset`

- **Purpose**: 비밀번호 재설정 Step 1 - 휴대폰 번호 입력

- **User Stories**: US-002-1

- **Access**: 비로그인 사용자

- **Components**:

휴대폰 번호 입력

- 인증번호 전송 버튼

#### Page: PasswordResetVerify (인증번호 확인) (change-2026-01-14-1500)

- **Route**: `/password-reset/verify`

- **Purpose**: 비밀번호 재설정 Step 2 - 인증번호 입력

- **User Stories**: US-002-1

- **Access**: Step 1 완료 사용자

- **Components**:

인증번호 입력 필드 + 타이머

#### Page: PasswordResetNew (새 비밀번호 설정) (change-2026-01-14-1500)

- **Route**: `/password-reset/new`

- **Purpose**: 비밀번호 재설정 Step 3 - 새 비밀번호 설정

- **User Stories**: US-002-1

- **Access**: Step 2 완료 사용자

- **Components**:

새 비밀번호 입력 + 규칙 체크 UI

- 비밀번호 확인 입력

- 재설정 완료 버튼

#### Page: Terms (약관 동의)

- **Route**: `/terms`

- **Purpose**: 서비스 이용 약관 동의

- **User Stories**: US-001

- **Access**: 회원가입 진행 중 사용자

---

### Common Pages (인증 공통)

#### Page: Profile Setup (프로필 설정)

- **Route**: `/profile/setup`

- **Purpose**: 닉네임 설정 및 본인인증

- **User Stories**: US-001

- **Access**: 로그인 사용자 (프로필 미설정)

- **Components**:

닉네임 입력 및 중복 확인

- 본인인증 모듈 (NHN)

#### Page: MyPage (마이페이지 - 내 정보)

- **Route**: `/mypage`

- **Purpose**: 사용자 프로필 정보 조회/수정 및 주요 기능 진입점

- **User Stories**: US-001, US-002, US-005

- **Access**: 로그인 사용자

- **Components**:

프로필 정보 카드 (프로필 사진 설정, 닉네임, 소개글)

- 닉네임 수정

- 계정 설정

- Ink 충전 바로가기 (→ `/shop`)

- **「크리에이터 되기」 버튼** (change-2026-01-15-1000)

독자(READER) 역할일 때만 표시

- 클릭 시 `/mypage/creator/setup`으로 이동

- 크리에이터(CREATOR) 역할이면 숨김

#### Page: Wallet (내 지갑)

- **Route**: `/mypage/wallet`

- **Purpose**: Ink 잔액 및 트랜잭션 내역 조회

- **User Stories**: US-003, US-003-1, US-003-2, US-004

- **Access**: 로그인 사용자

- **Components**:

잔액 표시 카드

사용 가능한 Ink (Available Ink)

- 진행 중인 제안 Ink (Pending Ink) - 클릭 시 `/mypage/wallet/pending`으로 이동

- 총 보유 Ink (Total Ink)

트랜잭션 내역 리스트 (무한 스크롤)

정산 내역 탭/필터 (완료된 제안 결과)

Ink 충전 버튼 (→ `/shop`)

#### Page: Pending Proposals (진행 중인 제안 내역) - DEPRECATED

- **Route**: `/mypage/wallet/pending` → `/mypage/proposals?filter=pending`으로 리다이렉트

- **Note**: change-2026-01-19-1100에서 /mypage/proposals로 통합됨

#### Page: Proposal History (제안 내역) ← NEW (change-2026-01-19-1100)

- **Route**: `/mypage/proposals`

- **Purpose**: 독자가 제안/후원한 모든 내역 조회 (진행 중 + 완료)

- **User Stories**: US-003-1, US-003-2

- **Access**: 로그인 사용자

- **Entry Points**:

마이페이지 → "제안 내역" 메뉴

- 내 지갑 → BalanceCard "진행 중" 버튼 클릭

**Components**:

- 필터 탭 (전체 / 진행 중 / 완료)

- 제안 내역 리스트 (ProposalHistoryCard)

작품명 / 에피소드 제목

- 제안 유형 (Proposal / Backing / Sponsor)

- 후원 금액

- 상태 뱃지 (ProposalStatusBadge)

검토 중 (null + open)

- 성사 대기 (deal + open)

- 거절됨 (drop + open)

- 후원 성공 (deal + published)

- 환불 완료 (drop/null + published)

빈 상태 안내

페이지네이션 또는 무한 스크롤

#### Page: Proposal Detail (제안 상세) ← NEW (change-2026-01-19-1100)

- **Route**: `/mypage/proposals/[id]`

- **Purpose**: 개별 제안의 상세 정보 및 상태 변경 이력(타임라인) 조회

- **User Stories**: US-003-1

- **Access**: 로그인 사용자 (본인 제안만)

- **Components**:

상단 요약 영역

스테이지 표시기 (ProposalStageIndicator)

STEP 1: 작가 확인 중 (status: null)

- STEP 2: 작가 수락/거절 완료 (status: deal or drop)

- STEP 3: 정산 및 환불 완료 (work: published)

금액 정보 (후원 Ink + 결과 문구)

타임라인 (ProposalTimeline)

- 후원 완료: "YYYY-MM-DD - 제안에 X Ink를 후원했습니다."

- 의사결정: deal/drop/null 상태에 따른 메시지

- 최종 확정: published 전환 시 정산/환불 결과 메시지

작품으로 이동 버튼 (TBD)

#### Page: My Reports (신고 내역)

- **Route**: `/mypage/reports`

- **Purpose**: 내가 신고한 내역 및 처리 상태 조회

- **User Stories**: US-030

- **Access**: 로그인 사용자

- **Components**:

신고 리스트 테이블

신고 대상 (닉네임)

- 신고 유형

- 신고일

- 처리 상태 배지 (접수됨, 검토 중, 처리 완료, 기각)

리스트 항목 클릭 → 신고 상세 모달

- 신고 내용 상세

- 관리자 피드백 (처리 완료 시)

빈 상태 안내 ('신고 내역이 없습니다')

#### Page: Settings (설정)

- **Route**: `/settings`

- **Purpose**: 프로필 정보 수정 및 약관 동의 관리

- **User Stories**: US-033, US-034

- **Access**: 로그인 사용자

- **Entry Point**:

Primary: MyPage → Settings 링크

- Secondary: TopNav 프로필 드롭다운 → Settings

- Mobile: 햄버거 메뉴 → MyPage → Settings

**Components**:

- **프로필 섹션** (ProfileSection)

현재 프로필 이미지 표시 및 업로드 (ProfileImageUpload)

드래그앤드롭 또는 클릭 업로드

- 미리보기 제공

- JPG/PNG/WEBP, 최대 3MB 제한

닉네임 입력 (NicknameInput)

- 실시간 중복 확인 (500ms debounce)

- 2~12자, 한글/영문/숫자/`_`/`.`만 허용

- 중복 시 에러 표시

**약관 동의 섹션** (ConsentSettings)

- 필수 약관: 서비스 이용약관, 개인정보 처리방침, 커뮤니티 규칙 (고정, 변경 불가)

- 선택 약관: 마케팅 정보 수신 (토글 가능)

- 각 약관 항목 옆 '전문 보기' 링크

저장 버튼

- 성공 시 '프로필이 업데이트되었습니다' 메시지

- authStore 동기화

**계정 관리 섹션** (change-2026-01-14-1500)

- 비밀번호 변경 버튼 (→ /password-reset)

- 회원 탈퇴 버튼 (→ /mypage/withdraw)

#### Page: Withdraw (회원 탈퇴) (change-2026-01-14-1500)

- **Route**: `/mypage/withdraw`

- **Purpose**: 회원 탈퇴 진행

- **User Stories**: US-002-2

- **Access**: 로그인 사용자

- **Entry Point**: Settings → 회원 탈퇴 버튼

- **Components**:

탈퇴 유의사항 안내 박스

개인정보 삭제 안내

- 게시글 처리 방식 안내

- 재가입 불가 안내

- 포인트/혜택 소멸 안내

- 복구 불가 강조

본인 확인 영역

- 현재 비밀번호 입력

- 확인 문구 입력 ('회원탈퇴')

동의 체크박스

회원 탈퇴 버튼 (조건 미충족 시 비활성)

하단 경고 문구

#### Page: Creator Setup (크리에이터 프로필 설정) - NEW (change-2026-01-15-1000)

- **Route**: `/mypage/creator/setup`

- **Purpose**: 독자에서 크리에이터로 전환하기 위한 프로필 설정

- **User Stories**: -

- **Access**: 로그인 사용자 (READER 역할)

- **Entry Point**: MyPage → 「크리에이터 되기」 버튼

- **Components**:

페이지 헤더: "크리에이터 되기"

- 프로필 사진 업로드 (ProfileImageUpload)

드래그앤드롭 또는 클릭 업로드

- JPG/PNG/WEBP, 최대 3MB

닉네임 입력 (기존 닉네임 사용 가능)

작가 소개글 입력 (Plain Text, 500~1000자)

설정 완료 버튼

**Flow**: 설정 완료 → User.role = CREATOR 즉시 전환 (심사 없음) → 크리에이터 스튜디오 접근 권한 활성화

#### Page: Creator Profile Edit (크리에이터 프로필 수정) - NEW (change-2026-01-15-1000)

- **Route**: `/mypage/creator/edit`

- **Purpose**: 크리에이터 프로필 정보 수정

- **User Stories**: -

- **Access**: 로그인 사용자 (CREATOR 역할)

- **Entry Point**: CreatorPage 또는 Settings → 크리에이터 프로필 수정

- **Components**:

페이지 헤더: "크리에이터 프로필 수정"

- 프로필 사진 변경 (ProfileImageUpload)

- 닉네임 수정

- 작가 소개글 수정

- 저장 버튼

#### Page: Ink Shop (Ink 충전)

- **Route**: `/shop`

- **Purpose**: Ink 결제 및 충전

- **User Stories**: US-005

- **Access**: 로그인 사용자

- **Entry Point**: 마이페이지 → Ink 충전 바로가기, 또는 Ink 부족 시 충전하기 버튼

- **Components**: (change-2026-01-15-1000 업데이트)

페이지 헤더: "참여를 위한 Ink 구매"

- 충전 옵션 카드 - **단순 카드형, 동일 시각적 위계**

모든 카드 동일한 높이와 레이아웃

- 카드당: Ink 수량, 결제 금액 (inkAmount × 100원)

- ❌ 보너스/할인/추천/이벤트 표기 없음

**충전 패키지: 10 / 20 / 30 / 100 Ink** (1 Ink = 100원 고정)

결제하기 버튼 → `/shop/payment`로 이동

#### Page: Payment (결제하기) - (change-2026-01-13-1640, change-2026-01-15-1000 업데이트)

- **Route**: `/shop/payment`

- **Purpose**: 결제 수단 선택 및 결제 진행

- **User Stories**: US-005

- **Access**: 로그인 사용자

- **Entry Point**: Ink Shop에서 금액 선택 후 `결제하기` 클릭

- **Components**:

**헤더**: 뒤로가기 버튼, 타이틀 "결제하기"

- **OrderInfoCard**: 주문 상품/가격 정보 (change-2026-01-15-1000 업데이트)

Ink 수량

- 최종 결제 금액 (inkAmount × 100원)

- ❌ 보너스/할인 표기 없음

**PaymentMethodSelector**: 결제 수단 Radio 선택

- 카드 결제 (기본값), 계좌이체, 무통장 입금, 간편결제(토스페이, 카카오페이)

**PaymentTermsAgreement**: 전자금융거래 이용약관 동의 체크박스

**PaymentCTA**: `₩{금액} 결제하기` 버튼 (약관 동의 시 활성화)

**Flow**: 결제 버튼 클릭 → PG 연동 → 성공 시 `/shop/payment/complete`

#### Page: Payment Complete (결제 완료) - (change-2026-01-13-1640, change-2026-01-15-1000 업데이트)

- **Route**: `/shop/payment/complete`

- **Purpose**: 결제 완료 상태 및 결과 확인

- **User Stories**: US-005

- **Access**: 로그인 사용자

- **Entry Point**: Payment 페이지에서 결제 성공

- **Components**:

**PaymentSuccessStatus**: 성공 아이콘, "결제가 완료되었습니다" 메시지

- **PaymentReceiptCard**: 결제 내역 (change-2026-01-15-1000 업데이트)

Ink 수량

- 결제 수단, 결제 일시, 거래 번호

- 결제 금액 강조

- ❌ 보너스 Ink 표기 없음

**ChargeResultCard**: 현재 보유 Ink 표시

**CTA 영역**:

- `영수증 다운로드` 버튼 (PDF)

- `확인` 버튼 → 이전 화면/메인 이동

---

### Reader Pages (독자 영역)

#### Page: Home (홈)

- **Route**: `/home`

- **Purpose**: 추천/인기 작품 표시

- **User Stories**: US-006

- **Access**: 로그인 사용자

- **Components**:

추천 배너 캐러셀

- 인기 작품 리스트

- 최신 작품 리스트

- 최근 본 작품 섹션 ← NEW (change-2026-02-04-1450)
  - 최대 5개 표시
  - 5개 초과 시 [더보기] 버튼 → /recently-viewed

- 하단 네비게이션 바 (change-2026-01-15-1000: 삭제)

#### Page: Explore (탐색)

- **Route**: `/explore`

- **Purpose**: 전체 작품 리스트 및 필터

- **User Stories**: US-006, US-007

- **Access**: 로그인 사용자

- **Components**:

검색 입력 필드

- 정렬 옵션 (최신순/Bounty순)

- 작품 카드 그리드

- OPEN 상태 표시 배지

#### Page: Search Results (검색 결과)

- **Route**: `/search?q={query}`

- **Purpose**: 검색 결과 표시

- **User Stories**: US-007

- **Access**: 로그인 사용자

- **Components**:

페이지 헤더: "검색 결과" (검색창 없음)

- 검색 결과 개수 표시

- 결과 리스트 (Grid/List)

**Note**: 페이지 내 검색창 없음. 재검색은 상단바(TopNav) 검색을 통해서만 수행 (change-2026-01-09-1137)

#### Page: Story Detail (작품 상세)

- **Route**: `/stories/:storyId`

- **Purpose**: 작품 정보, 에피소드 목록, 제안/후원 내역

- **User Stories**: US-006, US-008, US-035

- **Access**: 로그인 사용자

- **Components**: (change-2026-01-15-1000 업데이트)

작품 정보 헤더 (썸네일, 제목, 작가, 총 모금액)

작가 정보 클릭 시 작가 상세 페이지(`/creators/:creatorId`)로 이동

- ❌ "연재중/완결" UI 제거

에피소드 리스트

에피소드별 상태(OPEN/PUBLISHED) 표시

**참여 리스트 (통합)** - Proposals & Supports 탭 구조 (change-2026-01-15-1000: 탭 제거)

- Proposal / Backing / Sponsor **통합 리스트**

- **정렬**: 기여 금액(Ink) 내림차순

- 각 참여 항목: 프로필 avatar, 닉네임, 참여 타입 배지 (Proposer/Backer/Sponsor), 기여 금액

**Main Producers 섹션** (change-2026-01-15-1000 업데이트)

- **노출 대상: Proposer + Backer + Sponsor 전원**

- Top 3: 가로 카드 3개 (프로필 이미지 큼, 닉네임, 기여 타입, 기여 Ink, 순위 배지 #1/#2/#3)

- 그 외: 세로 리스트 (작은 avatar, 닉네임, 기여 Ink 우측 정렬)

- **정렬**: 총 기여 금액 내림차순, 동일 금액 시 참여 시점 순

- **클릭 불가**: 모든 유저 카드/리스트 아이템 non-interactive

**Sponsored by 섹션** (change-2026-01-15-1000: Main Producers에 통합)

#### Page: Creator Detail (작가 상세)

- **Route**: `/creators/:creatorId`

- **Purpose**: 작가 프로필 및 작품 목록 조회

- **User Stories**: US-035

- **Access**: 로그인 사용자

- **Components**:

작가 프로필 헤더

프로필 사진

- 작가명 (닉네임)

- 작가소개글

작품 목록 섹션

- 작가의 전체 작품 리스트 (StoryCard 그리드)

- 각 작품별 썸네일, 제목, 총 모금액 표시

빈 상태 안내 ('등록된 작품이 없습니다')

#### Page: Episode Viewer (에피소드 뷰어)

- **Route**: `/stories/:storyId/episodes/:episodeId`

- **Purpose**: 에피소드 콘텐츠 열람 및 참여

- **User Stories**: US-008, US-009, US-010, US-011, US-012, US-013

- **Access**: 로그인 사용자

- **Components**: (change-2026-01-15-1000 업데이트)

콘텐츠 뷰어 (이미지/텍스트 스크롤)

- **액션 바** (OPEN 상태만)

제안하기 버튼 → Proposal Modal

- 지지하기 버튼 → Backing Modal

- 후원하기 버튼 → DirectBacking Modal

- **Ink 부족 시**: 충전하기 버튼 노출 → `/shop` 연결

**참여 리스트 (OPEN 상태)** - 제안 목록 섹션 (change-2026-01-15-1000: 통합)

- Proposal / Backing / Sponsor **통합 리스트** (탭 분리 없음)

- **정렬**: 기여 금액(Ink) 내림차순

- 각 참여 항목:

프로필 avatar, 닉네임

- 참여 타입 배지 (Proposer/Backer/Sponsor)

- 기여 금액

- **철회하기 버튼** (조건 충족 시만 표시)

Episode.status === OPEN

- participation.userId === 현재 로그인 유저

- participation.type ∈ { Proposal, Backing }

- **Sponsor 철회 불가**

철회하기 버튼 클릭 → WithdrawModal

**크레딧 섹션 (PUBLISHED 상태)** (change-2026-01-15-1000 업데이트)

- **Main Producers 영역: Proposer + Backer + Sponsor 전원 노출**

- **Main Producer Top 3**: 가로 카드 3개

프로필 이미지 (상대적으로 큼)

- 닉네임

- 기여 타입 배지 (PROPOSER/BACKER/SPONSOR)

- 기여 Ink 수

- 순위 배지 (#1, #2, #3)

**Main Producer 그 외**: 세로 리스트 (4위~)

- 작은 avatar

- 닉네임

- 기여 타입

- 기여 Ink 수 (우측 정렬)

**Sponsored by** (change-2026-01-15-1000: Main Producers에 통합)

**정렬**: 총 기여 금액 내림차순, 동일 금액 시 참여 시점 순

**클릭 불가**: 모든 유저 카드/리스트 아이템 non-interactive

❌ 제거: Proposal/Backing CTA, 철회 버튼 (PUBLISHED 상태에서)

---

### Creator Pages (작가 영역)

#### Page: Creator Studio (크리에이터 스튜디오)

- **Route**: `/studio`

- **Purpose**: 작가 대시보드

- **User Stories**: US-014, US-015

- **Access**: CREATOR 역할

- **Components**:

작품 목록

- 에피소드 관리 링크

- 제안 관리 링크

- 정산 현황 요약

#### Page: Episode Management (에피소드 관리)

- **Route**: `/studio/episodes`

- **Purpose**: Story-first 레이아웃으로 에피소드 목록 조회 및 관리

- **User Stories**: US-015

- **Access**: CREATOR 역할

- **Layout Structure** (change-2026-01-13-1430):

```
Primary View: Story List
└── Story 선택 시 → Episode List (해당 Story만)
```
- **Components**:

**Story List (Primary View)**:

CreatorStoryList 컴포넌트 (크리에이터 본인 Story 목록)

- CreatorStoryCard (Navigate/Edit/Preview 액션)

- Search/Filter/Pagination 지원

- 리스트 스크롤과 페이지 스크롤 분리

**Episode List (Story Context)**:

- 선택된 Story의 Episode만 표시

- 에피소드 상태/모금액 표시

- 새 에피소드 추가 버튼

- 에피소드별 편집/삭제 액션

- Pagination/Infinite scroll 지원

**Navigation Flow**: Story → Episode → Proposal (단방향)

**Data Scale**: Story 10~100+, Episode 100+/Story

#### Page: Episode Editor (에피소드 편집)

- **Route**: `/studio/episodes/:episodeId/edit`

- **Purpose**: 콘티/완성본 업로드

- **User Stories**: US-014, US-019

- **Access**: CREATOR 역할

- **Components**: (change-2026-01-15-1000 업데이트)

이미지 다중 업로드 (드래그 앤 드롭)

- 이미지 순서 변경

- 텍스트 입력 영역

- 콘티/완성본 구분 토글

- **저장 버튼** (OPEN 상태 → 즉시 독자 화면 반영)

- 임시저장(Draft) 버튼 (change-2026-01-15-1000: 삭제)

**Note (change-2026-01-15-1000)**: Episode가 OPEN 상태일 때 저장하면 즉시 독자 화면에 반영됩니다. 별도의 발행/확정 단계 없음.

#### Page: Proposal Management (제안 관리)

- **Route**: `/studio/proposals`

- **Purpose**: Episode-scoped view로 받은 제안 목록 조회 및 Deal/Drop 결정

- **User Stories**: US-016, US-017, US-018

- **Access**: CREATOR 역할

- **Layout Structure** (change-2026-01-13-1430):

```
┌─────────────────────────────────────────────────────┐
│  Left/Top: Episode Selector                         │
│  └── OPEN 상태 Episode만 표시                       │
│  └── 검색 가능 (Episode 수 많을 때)                 │
│                                                     │
│  Main: Proposal List (선택된 Episode만)             │
│  └── Sorting/Filtering/Pagination                   │
└─────────────────────────────────────────────────────┘
```
- **Components**:

**Episode Selector (Left/Top)**:

EpisodeSelector 컴포넌트

- 동일 Story 내 OPEN 상태 Episode만 표시

- Desktop: Vertical list / Mobile: Dropdown

- Episode 개수 많을 경우 검색 지원

**Proposal List (Main)**:

- 선택된 Episode의 Proposal만 렌더링

- 제안 카드 (내용, total_bounty, backer_count)

- Sorting/Filtering/Pagination or Virtualized list

- Deal 버튼 → 확인 모달

- Drop 버튼 → 확인 모달

**Navigation Flow**: Story → Episode → Proposal (단방향)

**Data Scale**: Proposal 100+/Episode

**Performance**: 한 번에 하나의 Episode 컨텍스트만 렌더링

#### Page: Settlement (정산 관리 - 보유 내역)

- **Route**: `/studio/settlement`

- **Purpose**: 정산 현황 조회 및 출금 요청 (change-2026-01-21-1135)

- **User Stories**: US-020, US-021

- **Access**: CREATOR 역할

- **Components**:

요약 카드 (총 수익, 총 출금액, 정산 대기 건수)

- 예정 정산금 표시 (Expected)

- 출금 가능 금액 표시 (Available)

- 출금 요청 버튼 → 출금 모달

- 정산 프로세스 안내 텍스트

- 정산 내역 전체 보기 링크 → `/studio/settlement-history`

#### Page: Settlement History (정산 내역)

- **Route**: `/studio/settlement-history` ← NEW (change-2026-01-21-1135)

- **Purpose**: 과거 정산 히스토리 조회 (에피소드 단위)

- **User Stories**: US-021

- **Access**: CREATOR 역할

- **Components**:

정산 내역 리스트 (에피소드별, 상태, 정산금액)

- 검색/필터 기능

- 페이지네이션

- 정산 내역 상세 보기 → 정산 상세 모달

---

### Admin Pages (관리자 영역)

#### Page: Admin Dashboard (관리자 대시보드)

- **Route**: `/admin`

- **Purpose**: 플랫폼 운영 현황 개요

- **User Stories**: US-028

- **Access**: ADMIN 역할

- **Components**:

총 회원 수 카드 + 회원 관리 버튼 (change-2026-01-21-1135)

- 총 작품 수 카드 + 콘텐츠 관리 버튼 (change-2026-01-21-1135)

- 총 거래액 카드 + 결제 관리 버튼 (change-2026-01-21-1135)

- 최근 7일 가입자 추이 그래프

- 대기중 정산 요청 건수

- 최근 활동 로그

**Navigation**: 상단 지표 카드의 버튼으로 회원/콘텐츠/결제 관리 페이지 진입

#### Page: User Management (회원 관리)

- **Route**: `/admin/users`

- **Purpose**: 회원 목록 조회 및 제재

- **User Stories**: US-022, US-023

- **Access**: ADMIN 역할

- **Components**:

회원 검색/필터

- 회원 리스트 테이블 (닉네임, 이메일, 가입일, 역할)

- 페이지네이션 (50개/페이지)

- 상세 보기 → 회원 상세 모달

- 제재 버튼 → 제재 모달 (사유 입력)

#### Page: Content Management (콘텐츠 관리)

- **Route**: `/admin/contents`

- **Purpose**: 작품/에피소드 관리

- **User Stories**: US-024

- **Access**: ADMIN 역할

- **Components**:

작품/에피소드 탭

- 콘텐츠 리스트 테이블

- 숨김/삭제 액션 버튼

- 삭제 확인 모달

#### Page: Payment Management (결제 관리)

- **Route**: `/admin/payments`

- **Purpose**: Ink 충전 결제 내역 조회

- **User Stories**: US-027

- **Access**: ADMIN 역할

- **Components**:

날짜 범위 필터

- 결제 리스트 테이블 (회원명, 금액, 결제일, 결제수단)

- 상세 보기 링크

#### Page: Settlement Management (정산 관리 - 관리자)

- **Route**: `/admin/settlements`

- **Purpose**: 출금 요청 목록 조회 및 승인

- **User Stories**: US-025, US-026

- **Access**: ADMIN 역할

- **Components**:

상태별 필터 (대기중/승인/거절/완료)

- 정산 요청 리스트 테이블 (작가명, 요청 금액, 요청일, 상태)

- 상세 보기 → 정산 상세 모달

- 승인/거절 버튼

#### Page: Report Management (신고 관리 - 관리자)

- **Route**: `/admin/reports`

- **Purpose**: 신고 목록 조회 및 처리

- **User Stories**: US-031, US-032

- **Access**: ADMIN 역할

- **Components**:

처리 상태별 필터 (전체, 접수됨, 검토 중, 처리 완료, 기각)

- 신고 유형별 필터 (스팸, 욕설/비방, 부적절한 콘텐츠, 저작권 침해, 허위 정보, 기타) - 6가지 (change-2026-01-12-1530)

- 정렬 옵션 (최신순/오래된순)

- 신고 리스트 테이블

신고자 닉네임

- 피신고자 닉네임

- 신고 유형

- 신고일

- 처리 상태 배지

상세 보기 → 신고 처리 모달

---

## Route Table

RoutePage NameUser StoriesAccessNotes`/`Splash-Public리다이렉트 전용`/login`LoginUS-002Public이메일/소셜 로그인`/signup`SignupUS-001Public회원가입`/signup/complete`SignupCompleteUS-001Public회원가입 완료 (change-2026-01-14-1500)`/password-reset`PasswordResetUS-002-1Public비밀번호 재설정 Step1 (change-2026-01-14-1500)`/password-reset/verify`PasswordResetVerifyUS-002-1Public비밀번호 재설정 Step2 (change-2026-01-14-1500)`/password-reset/new`PasswordResetNewUS-002-1Public비밀번호 재설정 Step3 (change-2026-01-14-1500)`/terms`TermsUS-001Public약관 동의`/profile/setup`ProfileSetupUS-001Auth닉네임/본인인증`/mypage`MyPageUS-001, US-002Auth내 정보`/mypage/wallet`WalletUS-003, US-003-1, US-003-2, US-004Auth내 지갑`/mypage/wallet/pending`PendingProposalsUS-003-1Auth진행 중인 제안 내역 (DEPRECATED → /mypage/proposals)`/mypage/proposals`ProposalHistoryUS-003-1, US-003-2Auth제안 내역 (change-2026-01-19-1100)`/mypage/proposals/[id]`ProposalDetailUS-003-1Auth제안 상세 (change-2026-01-19-1100)`/mypage/reports`MyReportsUS-030Auth신고 내역`/mypage/withdraw`WithdrawUS-002-2Auth회원 탈퇴 (change-2026-01-14-1500)`/mypage/creator/setup`CreatorSetup-Auth (READER)크리에이터 프로필 설정 (change-2026-01-15-1000)`/mypage/creator/edit`CreatorProfileEdit-Auth (CREATOR)크리에이터 프로필 수정 (change-2026-01-15-1000)`/settings`SettingsUS-033, US-034, US-002-2Auth프로필 수정 및 약관 동의 관리`/shop`ShopUS-005AuthInk 충전 (마이페이지에서 진입)`/shop/payment`PaymentUS-005Auth결제하기 (change-2026-01-13-1640)`/shop/payment/complete`PaymentCompleteUS-005Auth결제 완료 (change-2026-01-13-1640)`/home`HomeUS-006Auth메인 홈`/explore`ExploreUS-006, US-007Auth작품 탐색`/search`SearchUS-007Auth검색 결과`/stories/:storyId`StoryDetailUS-006, US-008, US-035Auth작품 상세`/creators/:creatorId`CreatorDetailUS-035Auth작가 상세`/stories/:storyId/episodes/:episodeId`EpisodeViewerUS-008013Auth에피소드 뷰어`/studio`StudioUS-014, US-015Creator크리에이터 홈`/studio/episodes`EpisodeManagementUS-015Creator에피소드 관리`/studio/episodes/:episodeId/edit`EpisodeEditorUS-014, US-019Creator에피소드 편집`/studio/proposals`ProposalManagementUS-016018Creator제안 관리`/studio/settlements`SettlementUS-020, US-021Creator정산 관리`/admin`AdminDashboardUS-028Admin대시보드`/admin/users`UserManagementUS-022, US-023Admin회원 관리`/admin/contents`ContentManagementUS-024Admin콘텐츠 관리`/admin/payments`PaymentManagementUS-027Admin결제 관리`/admin/settlements`SettlementManagementUS-025, US-026Admin정산 관리`/admin/reports`ReportManagementUS-031, US-032Admin신고 관리

---

## Modal/Overlay Components

Modal NameTrigger LocationPurposeUser StoriesProposalModalEpisodeViewer제안 생성US-009BackingModalEpisodeViewer제안 지지US-010DirectBackingModalEpisodeViewer직접 후원US-011**BackersDetailModalEpisodeViewer, StoryDetailBackers 상세 목록US-010WithdrawModalEpisodeViewer, PendingProposals참여 철회 확인-** (change-2026-01-15-1000)DealConfirmModalProposalManagementDeal 확인US-017DropConfirmModalProposalManagementDrop 확인US-018WithdrawalModalSettlement출금 요청US-021SanctionModalUserManagement회원 제재US-023DeleteConfirmModalContentManagement삭제 확인US-024SettlementDetailModalSettlementManagement정산 상세US-025, US-026ReportModalStoryDetail, ProposalCard, EpisodeViewer, UserProfile사용자/콘텐츠 신고US-029ReportDetailModalMyReports신고 상세 (사용자용)US-030ReportProcessModalReportManagement신고 처리 (관리자용)US-031, US-032**PaymentErrorModalPayment결제 실패 에러 표시 및 재시도US-005**

> **Note (change-2026-01-13-1640)**: PaymentErrorModal은 결제 실패 시 PG 응답 기반 에러 메시지를 표시하고 재시도 버튼을 제공합니다.

> **Note (change-2026-01-12-1444)**: BackersDetailModal은 Proposal 카드의 Backers 요약 영역 클릭 시 열리며, 해당 제안을 지지한 모든 Backer 목록(프로필, 닉네임, Ink 수량, 시점)을 표시합니다.

---

## Navigation Structure

### Global Top Navigation (Reader/Common)

**모든 화면 크기에서 표시 (Sticky)** - (change-2026-01-09-1137)

#### Desktop/Tablet (≥768px)

```
┌────────────────────────────────────────────────────────────┐
│ [☰][로고]        [      검색바      ]        [계정버튼] │
│  Left                 Center                    Right    │
└────────────────────────────────────────────────────────────┘
```
#### Mobile (≤767px)

```
┌────────────────────────────────────────────────────────────┐
│    [☰]              [로고]              [🔍][계정버튼]    │
│   Left              Center                   Right        │
└────────────────────────────────────────────────────────────┘
```
**3영역 구조 (Left / Center / Right)**

- **Left 영역**:

햄버거 버튼: 사이드바 토글 (모든 해상도에서 노출)

- 로고: 이미지 로고 (small.png), 클릭 시 `/home`으로 이동

**Center 영역**:

- Desktop/Tablet: 검색 입력창 (정확히 중앙 정렬)

- Mobile: 로고 (검색창 대신 로고 배치)

**Right 영역**:

- Mobile: 검색 아이콘 (클릭 시 슬라이드다운 검색 UI)

- 비로그인: `로그인` 버튼

- 로그인: `마이페이지` 버튼 또는 프로필 아이콘

**검색 UX**:

- 입력 중: 실시간 검색 팝업 (Preview, 최대 5~10개 결과)

- Enter 입력: `/search?q={query}` 페이지로 이동

- 실시간 팝업 구성: 썸네일, 작품명, 작가명, 조회수

### Bottom Navigation (Reader/Common - Mobile Only) (change-2026-01-15-1000: 삭제)

> **삭제됨 (change-2026-01-15-1000)**: 플랫폼 탐색형에서 콘텐츠 몰입형으로 전환. 하단 네비게이션 바 전면 삭제.

### Side Navigation (Reader/Common - Desktop Only) (change-2026-01-15-1000: 삭제)

> **삭제됨 (change-2026-01-15-1000)**: 독자용 사이드바 전면 삭제. 크리에이터 스튜디오 내부에서만 사이드바 허용.

### Side Navigation (Creator - Studio)

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
└── 정산 관리 (/admin/settlements)
```

---

## Access Control Matrix

Page GroupREADERCREATORADMINPublic (/, /login, /signup)OOOOnboarding (/onboarding/*)O (미완료만)O (미완료만)O (미완료만)Common (/mypage, /profile, /shop)O (완료만)O (완료만)O (완료만)Reader (/home, /explore, /stories/*, /creators/*)O (완료만)O (완료만)O (완료만)Creator Studio (/studio/*)XO (완료만)OAdmin (/admin/*)XXO

**Legend:** O = 접근 가능, X = 접근 불가 (리다이렉트)

**Important (change-2026-02-05-0918):**
- "완료만" = onboardingCompleted = true인 사용자만 접근 가능
- "미완료만" = onboardingCompleted = false인 사용자만 접근 가능
- 온보딩 미완료 사용자가 주요 페이지 접근 시 → `/onboarding`으로 강제 리다이렉트
- 온보딩 완료 사용자가 `/onboarding` 접근 시 → `/home`으로 리다이렉트

---

## User Story Coverage

### Coverage Summary

- **Total User Stories**: 31

- **Mapped to Screens**: 31

- **Coverage**: 100%

### Coverage Details

User StoryMapped ScreensUS-001/signup, /terms, /profile/setup, /mypageUS-002/login, /mypageUS-003/mypage/walletUS-004/mypage/walletUS-005/mypage, /shopUS-006/home, /explore, /stories/:storyIdUS-007/explore, /searchUS-008/stories/:storyId, /stories/:storyId/episodes/:episodeIdUS-035/stories/:storyId, /creators/:creatorIdUS-009/stories/:storyId/episodes/:episodeId (ProposalModal)US-010/stories/:storyId/episodes/:episodeId (BackingModal)US-011/stories/:storyId/episodes/:episodeId (DirectBackingModal)US-012/stories/:storyId/episodes/:episodeId (Credit Section)US-013/stories/:storyId/episodes/:episodeId (Proposal List)US-014/studio, /studio/episodes/:episodeId/editUS-015/studio/episodesUS-016/studio/proposalsUS-017/studio/proposals (DealConfirmModal)US-018/studio/proposals (DropConfirmModal)US-019/studio/episodes/:episodeId/editUS-020/studio/settlementsUS-021/studio/settlements (WithdrawalModal)US-022/admin/usersUS-023/admin/users (SanctionModal)US-024/admin/contentsUS-025/admin/settlementsUS-026/admin/settlements (SettlementDetailModal)US-027/admin/paymentsUS-028/adminUS-033/settingsUS-034/settings

---

## 문서 정보

- **생성일:** 2026-01-06

- **기반 문서:** user_stories_data.json, conceptual_model.json