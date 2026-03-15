# F04: 독자 코어 - task-031 Story Detail Page - UI/UX

## 0. UX Insight Summary

### Matched Insights

- No UX insight files found in `design-lib/insights/`

### Design System Rules (from globals.css)

- **Color Palette**: Monochrome only (oklch with 0 chroma)

Background: oklch(0.9911 0 0) - near white

- Foreground: oklch(0 0 0) - black

- Muted: oklch(0.9702 0 0) - light gray

- Muted Foreground: oklch(0.4386 0 0) - dark gray

- Border: oklch(0.9189 0 0) - light gray

**Typography**:

- Font Family: Noto Sans KR, ui-sans-serif, sans-serif, system-ui

- Letter Spacing: 0em (normal)

**Spacing**: 0.21rem base spacing

**Border Radius**: 0.5rem

**Shadows**: Very subtle (0.02 opacity)

- shadow-sm, shadow-md for cards

### UI Implications

- **Information Hierarchy**: Use font weight and size variation (not color)

- **Card Design**: Subtle shadow (shadow-sm or shadow-md), rounded corners (radius)

- **Text Legibility**: Sufficient contrast between foreground and muted-foreground

- **Spacing**: Consistent use of spacing multiples (px-4, py-2, gap-4, etc.)

- **Interactive Elements**: Clear focus states with ring (black)

### Non-Goals

- No colorful accent colors for decoration

- No heavy shadows or 3D effects

- No centered layouts (left-aligned content)

### Conflicts with Existing Design System

- None

### Developer Notes

- Use existing StoryCard component pattern for consistency

- Episode list should follow similar visual hierarchy as story list

- Author link should be clearly clickable (underline or hover state)

## 1. User Flow

### Flow 1: 작품 상세 조회 및 에피소드 선택

1. $1
2. $1
3. $1
4. $1
5. $1
6. $1

### Flow 2: 에러 처리

1. $1
2. $1

## 2. 화면별 구성

### /stories/[storyId] (Story Detail Page)

#### 레이아웃

```
┌─────────────────────────────────┐
│ Header (뒤로가기)                │
├─────────────────────────────────┤
│ Story Info Section              │
│  ┌───────┐                      │
│  │ 썸네일  │ 제목                 │
│  │       │ 작가명 → (클릭 가능)  │
│  └───────┘ 총 모금액             │
│            설명 (optional)       │
├─────────────────────────────────┤
│ Episode List Section            │
│  ┌──────────────────────────┐  │
│  │ Episode Card 1           │  │
│  │ #1 | 제목 | OPEN | 1000Ink│ │
│  └──────────────────────────┘  │
│  ┌──────────────────────────┐  │
│  │ Episode Card 2           │  │
│  └──────────────────────────┘  │
│  ...                            │
└─────────────────────────────────┘
```
#### 컴포넌트 구성

- **Header**:

뒤로가기 버튼 (좌측 상단)

- 페이지 제목 "작품 상세" (선택)

**StoryDetail 컴포넌트**:

- 썸네일 이미지 (aspect-ratio: 16/9 또는 1/1)

- 작품 제목 (text-xl font-bold)

- 작가 정보 (Link 컴포넌트, underline on hover)

- 총 모금액 (text-muted-foreground)

- 작품 설명 (text-sm text-muted-foreground)

**EpisodeList 컴포넌트**:

- 섹션 제목 "에피소드" (text-lg font-semibold)

- EpisodeCard 배열 (flex flex-col gap-3)

#### 상태별 UI

**Loading State**:

```
{/* Story Info Skeleton */}
  
    
    
      
      
      
    
  

  {/* Episode List Skeleton */}
  {[1, 2, 3].map((i) => (
    
  ))}
```
**Empty State** (에피소드 없음):

```
등록된 에피소드가 없습니다.
```
**Error State**:

```
{error.message || "작품을 찾을 수 없습니다"}
  

   router.push('/home')}>
    홈으로 돌아가기
```
## 3. 반응형 규칙

### Mobile (< 768px)

- 썸네일 크기: 80px × 80px

- 작품 정보 세로 정렬 (flex-col)

- 에피소드 카드: 전체 너비 (w-full)

- Padding: px-4

### Tablet/Desktop (≥ 768px)

- 썸네일 크기: 120px × 120px

- 작품 정보 가로 정렬 (flex-row gap-6)

- 에피소드 카드: 최대 너비 제한 (max-w-2xl)

- Padding: px-6

### 컨테이너

- 모바일: full width (w-full)

- 데스크톱: 중앙 정렬 max-width (max-w-4xl mx-auto)

## 4. 피드백/에러

### 성공 케이스

- 페이지 로드 완료: 즉시 콘텐츠 표시 (toast 없음)

- 작가 링크 클릭: 페이지 전환 (toast 없음)

- 에피소드 클릭: 뷰어로 이동 (toast 없음)

### 에러 케이스

상황피드백 방식메시지Story not foundError UI (화면 중앙)"작품을 찾을 수 없습니다"Network errorError UI + retry button"작품 정보를 불러오는 중 오류가 발생했습니다"Episodes load failInline error (리스트 영역)"에피소드 목록을 불러올 수 없습니다"

### 로딩 피드백

- **First Load**: Skeleton UI (헤더 + 리스트)

- **Retry**: Button disabled + spinner

- **Duration**: 2초 이내 목표

### 인터랙션 피드백

- **작가 링크 hover**: underline, text-primary

- **에피소드 카드 hover**: background-muted transition

- **클릭 시**: active state (scale-95 or bg-accent)

## 5. 접근성

- **헤딩 구조**: h1(작품 제목), h2(에피소드 섹션)

- **링크**: aria-label for 작가 링크 ("작가 {nickname} 페이지로 이동")

- **이미지**: alt text for 썸네일

- **키보드 네비게이션**: Tab으로 작가 링크, 에피소드 카드 순회

- **Focus visible**: ring-2 ring-ring on focus

## 6. 데이터 표시 규칙

### 총 모금액 (totalBounty)

- Format: `{amount.toLocaleString()} Ink`

- 예시: "1,234 Ink"

- 색상: text-muted-foreground

- 위치: 작가명 아래 또는 우측

### 에피소드 상태 (status)

- **OPEN**: Badge with "진행 중" (bg-secondary text-secondary-foreground)

- **PUBLISHED**: Badge with "완성됨" (bg-muted text-muted-foreground)

- **DRAFT**: 표시하지 않음 (독자에게 비공개)

### 에피소드 번호

- Format: `#{episodeNumber}`

- 예시: "#1", "#2"

- font-mono for consistent width

### 텍스트 말줄임 (Ellipsis)

- 작품 제목: 2줄 초과 시 말줄임 (line-clamp-2)

- 작품 설명: 3줄 초과 시 말줄임 (line-clamp-3)

- 에피소드 제목: 1줄 말줄임 (truncate)

## Checklist

- UX Insight 매칭 및 요약 포함됨 (No insights found - noted)

- 사용자 Flow가 화면 요소와 연결됨

- empty/loading/error 상태 정의됨

- 재사용 컴포넌트 중심 설계 (StoryDetail, EpisodeList)

- 모바일 동작 규칙 정의됨

- 디자인 시스템과 충돌 없음