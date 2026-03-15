# F04: 독자 코어

## 1. Feature Overview

**Purpose**: 독자가 작품을 탐색하고, 에피소드를 열람하며, 작가 정보를 조회할 수 있는 핵심 읽기 경험을 제공합니다. 홈/탐색/검색을 통해 작품을 발견하고, 작품 상세에서 에피소드 목록을 확인하며, 에피소드 뷰어에서 콘텐츠를 소비합니다.

**Scope Boundary**:

- IN: 작품 탐색(홈/탐색/검색), 작품 상세, 작가 상세, 에피소드 뷰어(콘텐츠 렌더링, 액션바), 완성 에피소드 크레딧 표시

- OUT: 제안/후원 모달(인터벤션 피처), 지갑 관련 기능(지갑/결제 피처), 작가 스튜디오 기능(크리에이터 스튜디오 피처)

## 2. User Flows

Flow IDActorGoalKey StepsUF-01독자추천/인기 작품 탐색홈 진입 → 추천 배너 확인 → 인기 작품 리스트 확인 → 작품 카드 클릭 → 작품 상세 이동UF-02독자작품 검색 및 필터링탐색 페이지 진입 → 검색어 입력 OR 필터/정렬 선택 → 작품 리스트 확인 → 작품 선택UF-03독자작품 정보 및 에피소드 확인작품 상세 진입 → 작품 정보 확인 → 작가 정보 클릭(선택) → 에피소드 리스트 확인 → 에피소드 선택UF-04독자작가 페이지 조회작품 상세에서 작가 클릭 → 작가 프로필 확인 → 작가의 다른 작품 목록 확인 → 작품 선택UF-05독자에피소드 콘텐츠 열람에피소드 뷰어 진입 → 이미지/텍스트 스크롤 → OPEN 상태 시 액션바(제안/후원) 확인 → 완료 후 뒤로가기UF-06독자완성 에피소드 크레딧 확인PUBLISHED 에피소드 뷰어 → 콘텐츠 스크롤 → 최하단 크레딧 섹션 확인 → Main Producer 및 기여자 리스트 확인

Source: user_stories.md (US-006, US-007, US-008, US-012, US-035)

## 3. Screens & Routes

ScreenRoutePurposeHome/home추천/인기 작품 배너 및 리스트 표시Explore/explore전체 작품 리스트 및 필터/정렬 옵션 제공Search/search?q={query}검색 결과 표시Story Detail/stories/:storyId작품 정보, 작가 링크, 에피소드 목록 표시Creator Detail/creators/:creatorId작가 프로필 및 작품 목록 표시Episode Viewer/stories/:storyId/episodes/:episodeId에피소드 콘텐츠 열람, 액션바(OPEN), 크레딧(PUBLISHED)

Source: ia_structure.md

## 4. Data Model

**Relevant Entities**:

EntityKey AttributesRelationsStoryid, title, description, thumbnailUrl, totalBounty, creatorId→ User (creator), → Episode (1:N)Episodeid, storyId, episodeNumber, title, status (OPEN/PUBLISHED), totalBounty→ Story (N:1), → EpisodeContent (1:N), → Credit (1:1)EpisodeContentid, episodeId, contentType (IMAGE/TEXT), contentUrl, textContent, orderIndex, isFinal→ Episode (N:1)Userid, nickname, role (CREATOR)→ Story (1:N)Creditid, episodeId, contributors (JSON), mainProducers (Array[UUID])→ Episode (N:1)

Source: conceptual_model.json

## 5. Component Strategy

**Pages**:

- `Home Page (/home)` - 추천/인기 작품 배너 및 리스트 표시

- `Explore Page (/explore)` - 작품 탐색 및 필터/정렬

- `Search Page (/search)` - 검색 결과 표시

- `Story Detail Page (/stories/:storyId)` - 작품 상세 정보 및 에피소드 목록

- `Creator Detail Page (/creators/:creatorId)` - 작가 프로필 및 작품 목록

- `Episode Viewer Page (/stories/:storyId/episodes/:episodeId)` - 에피소드 콘텐츠 뷰어

**Components**:

- `StoryBanner` - 추천 작품 캐러셀 배너 렌더링

- `StoryCard` - 작품 썸네일, 제목, 작가명, 모금액 표시

- `StoryList` - StoryCard 그리드/리스트 레이아웃

- `StoryDetail` - 작품 상세 정보 및 에피소드 리스트 (작가 링크 포함)

- `EpisodeCard` - 에피소드 카드 (번호, 제목, 상태, 모금액)

- `EpisodeList` - 에피소드 카드 리스트

- `ContentRenderer` - 이미지/텍스트 콘텐츠 스크롤 뷰어

- `ActionBar` - 제안하기/후원하기 버튼 (OPEN 상태만)

- `CreditSection` - Main Producer 및 기여자 리스트 (PUBLISHED 상태)

- `CreatorProfile` - 작가 프로필 정보 표시

- `CreatorStoryList` - 작가의 작품 목록

**State**:

- Server State (TanStack Query):

Featured/Popular stories (home)

- Story list with filters (explore/search)

- Story detail with episodes (story detail)

- Creator profile with stories (creator detail)

- Episode content and credits (episode viewer)

Client State: 필터/정렬 옵션, 검색어 (탐색/검색 페이지)

Source: logical_architecture.md (Story Discovery Domain, Episode Viewer Domain)

## 6. Task Mapping

Task IDTitleImplementsFilestask-025Home Page UI홈 페이지 UI (추천/인기 배너, 리스트)app/(auth)/home/page.tsxtask-026StoryBanner 컴포넌트추천 작품 캐러셀 배너components/story/StoryBanner.tsxtask-027StoryCard 컴포넌트작품 카드 (썸네일, 제목, 작가, 모금액)components/story/StoryCard.tsxtask-028StoryList 컴포넌트작품 리스트 그리드/레이아웃components/story/StoryList.tsxtask-029Explore Page UI탐색 페이지 (필터/정렬 옵션)app/(auth)/explore/page.tsxtask-030Search Page UI검색 결과 페이지app/(auth)/search/page.tsxtask-031Story Detail Page UI작품 상세 페이지 (작가 링크, 에피소드 목록)app/(auth)/stories/[storyId]/page.tsxtask-032StoryDetail 컴포넌트작품 상세 정보 (작가 클릭 → /creators/:creatorId)components/story/StoryDetail.tsxtask-033EpisodeCard 컴포넌트에피소드 카드 (번호, 제목, 상태)components/episode/EpisodeCard.tsxtask-034EpisodeList 컴포넌트에피소드 리스트components/episode/EpisodeList.tsxtask-035Episode Viewer Page UI에피소드 뷰어 페이지app/(auth)/stories/[storyId]/episodes/[episodeId]/page.tsxtask-036ContentRenderer 컴포넌트이미지/텍스트 콘텐츠 스크롤 렌더링components/episode/ContentRenderer.tsxtask-037ActionBar 컴포넌트제안/후원 버튼 (OPEN 상태만 표시)components/episode/ActionBar.tsxtask-038Story Service (Mock API)작품 조회 API 서비스 (featured, popular, search)services/storyService.tstask-039Episode Service (Mock API)에피소드 조회 및 콘텐츠 API 서비스services/episodeService.tstask-089Creator Detail Page UI작가 상세 페이지app/(auth)/creators/[creatorId]/page.tsxtask-090CreatorProfile 컴포넌트작가 프로필 정보 표시components/creator/CreatorProfile.tsxtask-091CreatorStoryList 컴포넌트작가의 작품 목록components/creator/CreatorStoryList.tsxtask-092Creator Service (Mock API)작가 조회 API 서비스services/creatorService.ts

Source: tasks.json

## 7. Open Questions

- 검색 결과 페이지(/search)와 탐색 페이지(/explore)의 UI 차이점은? → 검색은 검색어 표시 + 결과 리스트, 탐색은 전체 리스트 + 필터/정렬 옵션

- PUBLISHED 상태 에피소드의 크레딧 섹션 위치는? → 에피소드 콘텐츠 최하단에 CreditSection 컴포넌트로 표시 (US-012)

- 작품 상세 페이지에서 작가 정보 클릭 시 `/creators/:creatorId`로 이동하는 것 확인됨 (task-032 notes, US-035)