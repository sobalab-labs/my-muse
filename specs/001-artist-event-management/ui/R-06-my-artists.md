# UI Spec: R-06 My Artists Hub

**Path**: `/[locale]/(app)/artists/followed` | **IA row**: ia.md §3 R-06 | **Spec stories**: US2, FR-003, FR-010, FR-012 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Manage the authenticated user's personal list of followed favorite artists with quick unfollow actions and navigation to artist details (with unauthenticated visitors greeted by an in-page sign-in prompt). Success signal: user easily reviews followed artists, navigates to artist profiles, or removes artists from their followed list.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees page header with "My Artists" title and total count badge of followed artists | show | US2, FR-010, ia.md R-06 |
| N-02 | User sees scrollable list of followed artist cards (avatar, name, genre, nation, upcoming event count) | show / act | US2, FR-003, FR-010, FR-012 |
| N-03 | User sees quick unfollow action button on each artist card with optimistic removal | act | US2, FR-003, FR-010 |
| N-04 | User sees empty state when zero artists are followed, with CTA to discover artists via Search (`R-03`) | feedback / act | ia.md §3.2 empty account |
| N-05 | Unauthenticated visitor sees in-page sign-in prompt card explaining benefits and linking to Login (`R-01`) | feedback / act | ia.md §3.2, D-13 |
| N-06 | User sees skeleton loaders during data fetch and cursor-based infinite scroll | feedback | FR-012, ia.md §3.2 |
| N-07 | User sees error banner with retry option on API failure | feedback | ia.md §3.2 API failure |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `VStack` + `Badge` + `Text` | list (`components/badge`, layout) | header container + count badge | SEED badge and typography for title and followed artist counter. |
| N-02 | `List` + `Avatar` + `Box` | list (`components/list`, `components/avatar`) | cursor-paginated list rows | SEED list and avatar primitives for structured artist items. |
| N-03 | `ActionButton` | list (`components/action-button`) | `variant="outline"`, `tone="neutral"` | SEED action button for quick unfollow with rollback safety. |
| N-04 | `ContentPlaceholder` | shared (`_shared.md` §3) | heart illustration + Search CTA | SEED placeholder for empty followed artists state. |
| N-05 | `ContentPlaceholder` | shared (`_shared.md` §3) | lock/user illustration + Sign-in CTA | SEED placeholder for unauthenticated visitor in-page prompt. |
| N-06 | `Skeleton` | shared (`_shared.md` §3) | artist list item skeleton | SEED skeleton loader during fetch and infinite scroll pagination. |
| N-07 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry button | SEED callout banner for network/API failures. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Navigation Bar (My Artists)   |
+----------------------------------------+
|  [N-01] My Artists (Title + [Count])   |
+----------------------------------------+
|  [N-07] (Error Banner if API fails)    |
|  [N-05] (Auth Prompt if Unauthenticated)|
+----------------------------------------+
|  [N-02] Artist Item 1                  |
|         [Avatar] Artist Name           |
|         Genre / Nation / 2 Events      |
|         [N-03] [ Unfollow Button ]     |
|  ------------------------------------  |
|  [N-02] Artist Item 2                  |
|         [Avatar] Artist Name           |
|         [N-03] [ Unfollow Button ]     |
|  (Infinite scroll trigger at bottom)   |
|  [N-04] (Empty Placeholder if 0 items) |
+----------------------------------------+
|  [SN-02] Bottom Navigation Bar         |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  [N-01] My Artists Heading + [Count Badge]                  |
|  [N-07 / N-05] Banners / Prompts                            |
|                                                             |
|  +-------------------------------------------------------+  |
|  | [N-02] Artist Row 1: Avatar | Info | [N-03] Unfollow  |  |
|  +-------------------------------------------------------+  |
|  | [N-02] Artist Row 2: Avatar | Info | [N-03] Unfollow  |  |
|  +-------------------------------------------------------+  |
+-------------------------------------------------------------+
```

**Regions**:
1. **Header & Navigation (`SN-02`)**: Persistent nav bar -- priority 3.
2. **Page Header (`N-01`)**: Title and count badge -- priority 1 (first viewport).
3. **Artists List / In-Page Auth (`N-02`, `N-03`, `N-04`, `N-05`, `N-06`, `N-07`)**: Artist items, unfollow actions, empty state or sign-in prompt -- priority 1.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Followed count | N-01 | Integer badge (e.g., `12`) | N/A |
| Artist name & avatar | N-02 | Avatar image + bold name | Truncate name after 1 line |
| Genre & Nation | N-02 | Subtitle string (e.g., `J-Rock · JP`) | N/A |
| Upcoming event count | N-02 | e.g. "예정된 공연 2건" / "予定公演 2件" | N/A |
| Follow status | N-03 | Button label ("Following" / "Unfollow") | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Artist Row Click (`N-02`) | Scale feedback | Navigates to Artist Detail (`R-04`) | N/A |
| Unfollow Button Click (`N-03`) | Optimistic removal from list | Removes artist from followed list, updates count | Restores item, shows error toast (`SN-04`) |
| Discover Artists CTA (`N-04`) | Navigation transition | Navigates to Search (`R-03`) | N/A |
| Sign-In CTA (`N-05`) | Navigation transition | Navigates to Login (`R-01?next=/artists/followed`) | N/A |
| Infinite Scroll Trigger (`N-02`) | Skeleton rows appended | Fetches next batch of followed artists | Displays retry footer |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load | Skeleton artist list rows (`N-06`) | Auto-resolves on fetch |
| Empty (first use) | Authenticated user follows zero artists | `ContentPlaceholder` (`N-04`) with CTA to Search | User clicks "Discover Artists" |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | Network failure fetching list | Inline callout banner (`N-07`) with retry | User clicks "Retry" |
| Error (auth/session) | Unauthenticated visitor lands on route | `ContentPlaceholder` (`N-05`) with Sign-in CTA | User signs in via `R-01` |
| Partial / stale | Slow pagination | Bottom loading spinner | Auto-retries or user scrolls |
| Success | Followed artists loaded | Complete list of followed artists with unfollow CTAs | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Artist rows accommodate names in Korean, Japanese kanji, and English; buttons maintain fixed min-width.
- Date / time / d-day formatting per locale: N/A.
- Copy keys:
  - `myArtists.title` -> JP: "マイアーティスト" / KR: "마이 아티스트"
  - `myArtists.unfollow` -> JP: "フォロー解除" / KR: "팔로우 취소"
  - `myArtists.empty` -> JP: "まだフォローしているアーティストがいません。" / KR: "아직 팔로우한 아티스트가 없습니다."
  - `myArtists.discoverCTA` -> JP: "アーティストを探す" / KR: "아티스트 탐색하기"
  - `myArtists.authPrompt` -> JP: "マイアーティストを管理するにはログインしてください。" / KR: "마이 아티스트를 관리하려면 로그인하세요."

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Header -> Artist Row Links -> Unfollow Action Buttons.
- Landmarks / headings: `<main>` landmark with `<h1>` for page heading.
- Non-visual feedback for async changes: `aria-live="polite"` announcing artist removal from followed list.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US2, FR-003, FR-010: View and manage followed artists list | Viewing route displays followed artists list (`N-02`) with unfollow buttons (`N-03`) |
| D-5: Dedicated "My Artists" hub navigation | Accessible from main navigation tab bar |
| D-13: In-page sign-in prompt for unauthenticated visitors | Visiting without session shows in-page sign-in card (`N-05`) instead of redirect |
| FR-012: Cursor-based infinite scroll with position restoration | Scrolling loads paginated items with scroll preservation |
