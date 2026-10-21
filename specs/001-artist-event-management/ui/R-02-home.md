# UI Spec: R-02 Future Events Feed (Home)

**Path**: `/[locale]/(app)/home` | **IA row**: ia.md §3 R-02 | **Spec stories**: US4, FR-005, FR-006, FR-007, FR-010, FR-012, FR-013 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Display the future events feed under explicit scopes (Following for authenticated users, Trending for everyone) with a followed artists strip, list/calendar view modes, and filter/sort controls. Success signal: user successfully browses, filters, and inspects upcoming concert events or switches between Following and Trending scopes and view modes.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees followed artists horizontal strip at top of feed for quick access to artist details | show / act | US4, FR-010, ia.md R-02 |
| N-02 | User sees scope tabs to toggle between "Following" (auth only) and "Trending" (all visitors) | act | US4, FR-005, FR-010, ia.md R-02 |
| N-03 | User sees view mode toggle between List view and Month Calendar view (`?view=list`/`calendar`) | act | US4, FR-013, ia.md R-02 |
| N-04 | User sees filter and sort controls (artist, region [Japan/Korea], d-day chronological sort) | input / act | FR-006, FR-007, ia.md R-02 |
| N-05 | User sees future events feed list with event cards (artist name, event title, date, venue, d-day badge) | show / act | US4, FR-005, FR-012 |
| N-06 | User sees month calendar view grid showing event density and dates when calendar view is selected | show / act | US4, FR-013 |
| N-07 | User sees empty state when Following scope has zero followed artists (onboarding skipped) prompting search | feedback | ia.md §3.2 empty account |
| N-08 | User sees empty state when filtered events result in zero items | feedback | ia.md §3.2 empty filtered results |
| N-09 | User sees inline callout prompting sign-in when unauthenticated visitor attempts to select Following scope | feedback | ia.md R-02 auth behavior |
| N-10 | User sees loading skeleton loaders for event feed items and followed artists strip during async loading | feedback | ia.md §3.2 loading pattern |
| N-11 | User sees error banner with retry action on backend API failure | feedback | ia.md §3.2 API failure |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `Avatar` + `HStack` / `ScrollFog` | list (`components/avatar`, `components/scroll-fog`) | circular avatar, horizontal scroll | SEED avatar and horizontal scroll container for followed artists strip. |
| N-02 | `Tabs` / `ChipTabs` | list (`components/chip-tabs`) | segment tab variant | SEED chip tabs for switching between Following and Trending scopes. |
| N-03 | `SegmentedControl` | list (`components/segmented-control`) | list / calendar toggle | SEED segmented control for switching view modes. |
| N-04 | `Select` / `Chip` / `ChipTabs` | shared (`_shared.md` §3) | region / artist / sort filters | SEED select and chip components for filtering and sorting controls. |
| N-05 | `List` + `Box` | shared (`_shared.md` §3) | cursor-paginated card list | SEED list primitive and layout boxes for event feed cards. |
| N-06 | `Grid` + `Button` / `Box` | shared (`_shared.md` §3) | 7-column calendar grid | SEED grid layout primitive for month calendar view structure. |
| N-07 | `ContentPlaceholder` | shared (`_shared.md` §3) | illustration + search action button | SEED content placeholder for zero followed artists empty state. |
| N-08 | `ContentPlaceholder` | shared (`_shared.md` §3) | reset filters action button | SEED content placeholder for zero filtered search results. |
| N-09 | `Callout` | shared (`_shared.md` §3) | `tone="informative"` with sign-in action | SEED callout banner prompting sign-in for Following scope. |
| N-10 | `Skeleton` | shared (`_shared.md` §3) | card skeleton / avatar skeleton | SEED skeleton loaders for feed and artist strip loading states. |
| N-11 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry button | SEED callout banner for backend API failure notification. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Navigation Bar (Home active)  |
+----------------------------------------+
|  [N-01] Followed Artists Strip (Avatar)|
+----------------------------------------+
|  [N-02] Scope Tabs: [Following|Trending]|
+----------------------------------------+
|  [N-03] View Toggle: [List | Calendar] |
+----------------------------------------+
|  [N-04] Filter & Sort Chips            |
+----------------------------------------+
|  [N-11] (Error Banner if API fails)    |
|  [N-09] (Auth Prompt if Following/Anon)|
+----------------------------------------+
|  [N-05] Event Card 1 (Artist, Date)    |
|  [N-05] Event Card 2 (Artist, Date)    |
|  [N-05] Event Card 3 (Artist, Date)    |
|  (Infinite scroll trigger at bottom)   |
+----------------------------------------+
|  [SN-02] Bottom Navigation Bar         |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  [N-01] Followed Artists Horizontal Strip                   |
+-------------------------------------------------------------+
|  [N-02] Scope Tabs       [N-03] View Toggle     [N-04] Filters|
+-------------------------------------------------------------+
|  [N-11 / N-09] Banners / Prompts                            |
|                                                             |
|  +-----------------------+  +----------------------------+  |
|  | Event Card 1          |  | Event Card 2               |  |
|  +-----------------------+  +----------------------------+  |
|  +-----------------------+  +----------------------------+  |
|  | Event Card 3          |  | Event Card 4               |  |
|  +-----------------------+  +----------------------------+  |
+-------------------------------------------------------------+
```

**Regions**:
1. **Header & Navigation (`SN-02`)**: App shell header / bottom nav -- priority 3.
2. **Followed Artists Strip (`N-01`)**: Horizontal avatar scroll -- priority 2.
3. **Controls & Filters (`N-02`, `N-03`, `N-04`)**: Scope tabs, view mode toggle, filter chips -- priority 1 (first viewport).
4. **Feed Main Content (`N-05` / `N-06` / `N-07` / `N-08` / `N-09` / `N-10` / `N-11`)**: Event cards list or calendar grid, empty/loading/error states -- priority 1.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Artist name & avatar | N-01, N-05 | Text + circular image | Truncate artist name to 1 line with ellipsis |
| Event title & venue | N-05 | Title string + venue location | Truncate title to 2 lines |
| Event date & d-day | N-05 | `YYYY년 M월 일 (요일)` + `D-Day` badge | N/A |
| Feed scope (`?scope=`) | N-02 | Tab active state (`following` / `trending`) | N/A |
| View mode (`?view=`) | N-03 | Toggle active state (`list` / `calendar`) | N/A |
| Filters (`?artist=`, `?region=`, `?sort=`) | N-04 | Chip active states / dropdown selections | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Scope Tab Click (`N-02`) | Tab selection state update | Updates URL `?scope=` and reloads feed data | Reverts tab, shows inline error callout |
| View Toggle Click (`N-03`) | Toggle animation | Updates URL `?view=` (list vs calendar) | N/A |
| Filter/Sort Chip Click (`N-04`) | Chip active toggle / dropdown | Updates URL `searchParams` and refetches feed | Shows error toast/banner |
| Followed Artist Avatar Click (`N-01`) | Scale feedback | Navigates to Artist Detail (`R-04`) | N/A |
| Event Card Click (`N-05`) | Scale feedback | Navigates to Event Detail (`R-05`) | N/A |
| Infinite Scroll Trigger (`N-05`) | Appends skeleton loaders at list bottom | Loads next cursor batch of future events | Displays retry footer button |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load or scope switch | Skeleton cards (`N-10`) for feed & artist strip | Auto-resolves upon data fetch |
| Empty (first use) | Following scope with zero followed artists | `ContentPlaceholder` (`N-07`) with CTA to Search (`R-03`) | User clicks search discovery button |
| Empty (filtered to zero) | Filters yield no matching events | `ContentPlaceholder` (`N-08`) with reset filters CTA | User clicks "Reset Filters" |
| Error (recoverable) | Backend API failure | Critical callout banner (`N-11`) with retry button | User clicks "Retry" |
| Error (auth/session) | Unauthenticated user clicks Following scope | Informational callout (`N-09`) with sign-in CTA (`R-01`) | User clicks sign-in |
| Partial / stale | Slow pagination fetch | Bottom loading indicator spinner | Auto-retries or user scrolls again |
| Success | Events loaded successfully | Full event feed list or month calendar view | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Feed cards use responsive layout with flexible text wrapping for event titles and venue names.
- Date / time / d-day formatting per locale:
  - JA: `2026年10月15日 (水)` / D-Day: `D-15`
  - KR: `2026년 10월 15일 (수)` / D-Day: `D-15`
- Copy keys:
  - `home.following` -> JP: "フォロー中" / KR: "팔로잉"
  - `home.trending` -> JP: "トレンド" / KR: "트렌딩"
  - `home.emptyFollowing` -> JP: "フォローしているアーティストがいません。検索してアーティストを追加しましょう！" / KR: "팔로잉 중인 아티스트가 없습니다. 검색하여 아티스트를 추가해보세요!"
  - `home.emptyFiltered` -> JP: "条件に一致する公演イベントがありません。" / KR: "조건에 일치하는 공연 이벤트가 없습니다."
  - `home.signInPrompt` -> JP: "フォロー中のイベントを表示するにはログインが必要です。" / KR: "팔로잉 중인 이벤트를 보려면 로그인이 필요합니다."

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows from Header -> Followed Artists Strip -> Scope Tabs -> View Toggle -> Filters -> Event Feed Cards. Focus indicator visible on all elements.
- Landmarks / headings: `<nav>` for navigation, `<main>` for feed content, `<h1>` for page title.
- Non-visual feedback for async changes: `aria-live="polite"` region announcing filter changes and new event count updates.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US4, FR-005: Future events feed displays correctly under Following and Trending scopes | Switching scopes via tabs (`N-02`) updates URL `?scope=` and feed data |
| FR-006, FR-007: Filtering and sorting events feed | Selecting artist, region, or d-day sort (`N-04`) updates URL `searchParams` and filters feed |
| FR-010: Followed artists strip at top of feed | Horizontal avatar list (`N-01`) displays followed artists and links to `R-04` |
| FR-012: Cursor-based infinite scroll with scroll restoration | Scrolling down feed triggers pagination (`N-05`), and back-navigation restores position |
| FR-013: Month calendar view mode | Selecting calendar view (`N-03`) renders calendar grid (`N-06`) |
