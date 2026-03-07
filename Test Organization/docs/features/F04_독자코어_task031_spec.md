# F04: 독자 코어 - task-031 Story Detail Page - 개발 명세

## 1. Goal
작품 상세 정보(썸네일, 제목, 작가, 설명, 총 모금액)와 에피소드 목록을 표시하고, 작가 정보 클릭 시 작가 상세 페이지로 이동하는 기능 구현

## 2. Requirements

### 관련 User Story
- US-006: 홈에서 추천/인기 작품을 확인하다
- US-008: 에피소드 콘티를 열람하다
- US-035: 작가 상세 페이지를 조회하다

### Acceptance Criteria

| AC | 내용 | 구현 방법 | 검증 방법 |
|----|------|----------|----------|
| AC-1 | 작품 정보 헤더에 썸네일, 제목, 작가명, 총 모금액 표시 | StoryDetail 컴포넌트에서 Story 엔티티 데이터 렌더링 | 화면에 모든 정보가 정확히 표시되는지 확인 |
| AC-2 | 작가 정보 클릭 시 /creators/:creatorId로 이동 | Link 컴포넌트로 creatorId 동적 라우팅 | 클릭 시 작가 상세 페이지로 네비게이션 확인 |
| AC-3 | 에피소드 리스트에 각 에피소드의 번호, 제목, 상태(OPEN/PUBLISHED), 모금액 표시 | EpisodeList 컴포넌트에서 Episode[] 데이터 렌더링 | 에피소드별 정보가 정확히 표시되는지 확인 |
| AC-4 | 에피소드 클릭 시 /stories/:storyId/episodes/:episodeId로 이동 | Link 컴포넌트로 에피소드 뷰어로 라우팅 | 클릭 시 에피소드 뷰어 페이지로 네비게이션 확인 |
| AC-5 | 페이지 로드 시 2초 이내 작품 정보 및 에피소드 목록 표시 | TanStack Query로 서버 상태 관리, 로딩 스켈레톤 표시 | 로딩 시간 측정, 스켈레톤 UI 확인 |
| AC-6 | 작품이 존재하지 않을 경우 404 안내 메시지 표시 | 에러 상태 처리, 빈 상태 UI | storyId가 유효하지 않을 때 에러 UI 확인 |

## 3. Screens

| 화면 | 경로 | 핵심 기능 |
|------|------|----------|
| Story Detail | /stories/[storyId] | 작품 정보 표시, 작가 링크, 에피소드 리스트 |

## 4. Data & API

### Entities
- **Story**: id, title, description, thumbnailUrl, totalBounty, creatorId, status
- **Episode**: id, storyId, episodeNumber, title, status (OPEN/PUBLISHED), totalBounty
- **User** (Creator): id, nickname

### API Endpoints

| Method | Endpoint | 설명 | Response |
|--------|----------|------|----------|
| GET | /api/stories/:storyId | 작품 상세 정보 조회 | Story 객체 (creator 정보 포함) |
| GET | /api/stories/:storyId/episodes | 작품의 에피소드 목록 조회 | Episode[] 배열 |

### Error Cases
- 404: storyId가 존재하지 않음
- 403: 숨김/삭제된 작품 (status = HIDDEN/DELETED)
- 500: 서버 에러

## 5. Components & Hooks

### Feature Components (새로 생성)
- **StoryDetailPage** (`app/(auth)/stories/[storyId]/page.tsx`)
  - 작품 상세 페이지 라우트
  - useStory, useEpisodes 훅 사용
  - StoryDetail, EpisodeList 컴포넌트 조합

### Composite Components (task-032, task-034에서 생성)
- **StoryDetail** (`components/story/StoryDetail.tsx`)
  - 작품 정보 헤더 렌더링
  - 작가 정보 클릭 → /creators/:creatorId 링크
- **EpisodeList** (`components/episode/EpisodeList.tsx`)
  - 에피소드 카드 리스트 렌더링
  - EpisodeCard 컴포넌트 사용

### UI Components (재사용)
- **EpisodeCard** (`components/episode/EpisodeCard.tsx`) - task-033에서 생성
  - 에피소드 번호, 제목, 상태 배지, 모금액 표시

### Hooks
- **useStory** (신규 생성 필요)
  - TanStack Query 기반
  - 작품 상세 정보 조회
  - `getStoryById(storyId)` 서비스 호출
- **useEpisodes** (신규 생성 필요)
  - TanStack Query 기반
  - 작품의 에피소드 목록 조회
  - `getEpisodesByStoryId(storyId)` 서비스 호출

### Services
- **storyService** (`services/storyService.ts`) - task-038에서 생성
  - `getStoryById(storyId: string): Promise<Story>`
  - `getEpisodesByStoryId(storyId: string): Promise<Episode[]>`

## 6. State Management

### Server State (TanStack Query)
- Query Key: `['story', storyId]`
- Query Key: `['episodes', storyId]`

### Client State
- 없음 (서버 상태만 사용)

## 7. Dependencies

- task-009: Auth Layout (완료)
- task-027: StoryCard 컴포넌트 (완료)
- task-032: StoryDetail 컴포넌트 (pending - 이번 태스크에서 함께 구현)
- task-033: EpisodeCard 컴포넌트 (pending - 이번 태스크에서 함께 구현)
- task-034: EpisodeList 컴포넌트 (pending - 이번 태스크에서 함께 구현)
- task-038: Story Service (완료)

## Checklist
- [x] AC가 모두 구현/검증 방법에 매핑됨
- [x] 화면 범위(라우트)가 명확함
- [x] API 목록이 누락 없이 정의됨
- [x] 컴포넌트 전략이 기존 설계와 충돌 없음
