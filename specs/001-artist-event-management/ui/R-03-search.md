# UI Spec: R-03 Integrated Artist & Event Search

**Path**: `/[locale]/(app)/search` | **IA row**: ia.md §3 R-03 | **Spec stories**: US2, US3, FR-003, FR-004, FR-012 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Provide integrated keyword search matching artists and events simultaneously with filtering options (all, artist-only, event-only) and artist follow/unfollow actions (prompting sign-in for unauthenticated visitors). Success signal: user successfully searches for keywords, filters results by type/region, follows/unfollows artists, and navigates to artist or event detail pages.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees search input text field with keyword query (`?q=`) and clear action | input / act | US2, US3, FR-003, FR-004, ia.md R-03 |
| N-02 | User sees type filter tabs (`?type=all` / `artist` / `event`) to filter search results | act | FR-003, FR-004, ia.md D-1 |
| N-03 | User sees trending/recent featured suggestions when search query is empty | show / act | ia.md §6 Open Questions default |
| N-04 | User sees search results list combining matching artists and events with metadata | show / act | US2, US3, FR-003, FR-004, FR-012 |
| N-05 | User sees follow/unfollow action button on artist results (auth required, prompts sign-in if unauthenticated) | act | US2, FR-003 |
| N-06 | User sees empty state when search query or filters yield zero results | feedback | ia.md §3.2 empty results |
| N-07 | User sees loading skeleton loaders during search query execution | feedback | ia.md §3.2 loading pattern |
| N-08 | User sees error banner with retry action on backend search API failure | feedback | ia.md §3.2 API failure |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `TextFieldInput` | list (`components/text-field-input`) | search icon prefix + clear suffix | SEED text field input for search query entry. |
| N-02 | `ChipTabs` / `Tabs` | list (`components/chip-tabs`) | filter pill variant (`all` / `artist` / `event`) | SEED chip tabs for switching search result category filter. |
| N-03 | `Box` / `List` / `Text` | shared (`_shared.md` §3) | suggestion cards container | SEED layout and typography for empty-query trending suggestions. |
| N-04 | `List` + `Avatar` + `Box` | list (`components/list`, `components/avatar`) | result item cards (artist & event) | SEED list primitive and avatar for combined search result rows. |
| N-05 | `ActionButton` / `ToggleButton` | list (`components/action-button`, `components/toggle-button`) | `variant="outline"`, follow state | SEED button component for follow/unfollow artist action. |
| N-06 | `ContentPlaceholder` | shared (`_shared.md` §3) | zero results illustration + guidance | SEED content placeholder for empty search results. |
| N-07 | `Skeleton` | shared (`_shared.md` §3) | search result skeleton rows | SEED skeleton loaders to prevent layout shift during search. |
| N-08 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry button | SEED callout banner for search API failure notification. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Navigation Bar (Search active)|
+----------------------------------------+
|  [N-01] Search TextFieldInput (?q=...) |
+----------------------------------------+
|  [N-02] Type Filter Tabs: [All|Artist|Ev]|
+----------------------------------------+
|  [N-08] (Error Banner if API fails)    |
+----------------------------------------+
|  [N-04 / N-03] Search Results / Sugg.  |
|  - Artist Result 1 [Follow Button]     |
|  - Event Result 1                      |
|  - Artist Result 2 [Follow Button]     |
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
|  [N-01] Search TextFieldInput (centered max-w 720px)        |
|  [N-02] Type Filter Tabs                                    |
+-------------------------------------------------------------+
|  [N-08] Error Banner                                        |
|                                                             |
|  [N-04 / N-03] Search Results Grid / List                   |
|  +-------------------------------------------------------+  |
|  | Artist / Event Result Card with Actions               |  |
|  +-------------------------------------------------------+  |
+-------------------------------------------------------------+
```

**Regions**:
1. **Header & Navigation (`SN-02`)**: Persistent nav bar -- priority 3.
2. **Search Header (`N-01`, `N-02`)**: Search input and type filter tabs -- priority 1 (first viewport).
3. **Search Results Main (`N-03`, `N-04`, `N-05`, `N-06`, `N-07`, `N-08`)**: Results list or suggestions, follow buttons, empty/loading/error states -- priority 1.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Search query (`?q=`) | N-01 | Input value string | N/A |
| Result type (`?type=`) | N-02 | Active tab (`all`, `artist`, `event`) | N/A |
| Artist item (name, genre, nation) | N-04 | Text + avatar image | Truncate genre/nation text |
| Event item (title, date, venue) | N-04 | Title + date format (`YYYY년 M월 일`) | Truncate title to 2 lines |
| Follow status | N-05 | "Follow" vs "Following" button state | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Type Keyword (`N-01`) | Input updates, debounce 300ms | Updates URL `?q=` and fetches matching results | Displays error callout (`N-08`) |
| Clear Query Button (`N-01`) | Clears input | Resets `?q=` and shows trending suggestions | N/A |
| Type Filter Click (`N-02`) | Tab active state update | Updates URL `?type=` and filters results | N/A |
| Artist Result Click (`N-04`) | Scale feedback | Navigates to Artist Detail (`R-04`) | N/A |
| Event Result Click (`N-04`) | Scale feedback | Navigates to Event Detail (`R-05`) | N/A |
| Follow Button Click (`N-05`) | Button state toggle (optimistic) | Mutates follow status; prompts sign-in if unauthenticated | Rolls back optimistic update on API failure |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Typing query or fetching results | Skeleton result rows (`N-07`) | Auto-resolves upon fetch |
| Empty (first use) | Empty query (`?q=`) | Trending / recent suggestions (`N-03`) | User types query or clicks suggestion |
| Empty (filtered to zero) | Search query returns no matches | `ContentPlaceholder` (`N-06`) with search tips | User modifies query or filters |
| Error (recoverable) | Backend search API failure | Critical callout banner (`N-08`) with retry | User clicks "Retry" |
| Error (auth/session) | Unauthenticated user clicks Follow | In-page sign-in prompt modal / banner | User completes sign-in (`R-01`) |
| Partial / stale | Slow pagination fetch | Bottom loading indicator spinner | Auto-retries or user scrolls |
| Success | Search results rendered | Combined list of matching artists and events | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Search placeholder and filter tabs are localized for Japanese and Korean; results list wraps gracefully.
- Date / time / d-day formatting per locale:
  - JA: `2026年10月15日`
  - KR: `2026년 10월 15일`
- Copy keys:
  - `search.placeholder` -> JP: "アーティスト名やイベント名で検索" / KR: "아티스트 또는 이벤트 이름 검색"
  - `search.tabAll` -> JP: "すべて" / KR: "전체"
  - `search.tabArtist` -> JP: "アーティスト" / KR: "아티스트"
  - `search.tabEvent` -> JP: "イベント" / KR: "이벤트"
  - `search.emptyResults` -> JP: "検索結果がありません。" / KR: "검색 결과가 없습니다."

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows from Header -> Search Input -> Type Tabs -> Result Items. Focus visible on all focusable nodes.
- Landmarks / headings: `<search>` landmark for search form, `<main>` for results.
- Non-visual feedback for async changes: `aria-live="polite"` region announcing result count ("Found X results").

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US2, US3, FR-003, FR-004: Integrated keyword search matching artists and events simultaneously | Typing query in `N-01` updates `?q=` and renders mixed artist and event results (`N-04`) |
| D-1: Filter toggle ("all", "artist only", "event only") | Clicking filter tabs (`N-02`) updates `?type=` and filters results |
| US2, FR-003: Follow/unfollow artist action with auth prompt | Clicking follow on artist (`N-05`) toggles follow state or prompts sign-in if unauthenticated |
| FR-012: Cursor-based infinite scroll | Scrolling to bottom of search results triggers cursor pagination |
