# UI Spec: R-08 First-Time Artist Onboarding

**Path**: `/[locale]/(app)/onboarding` | **IA row**: ia.md §3 R-08 | **Spec stories**: US1, FR-011, FR-012 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Guide first-time authenticated users through selecting favorite artists to follow (or skipping) before reaching Home (`R-02`). Success signal: user selects favorite artists and clicks Complete, or clicks Skip, marking onboarding as complete and redirecting to Home.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees onboarding header with title, guiding instructions, and step indicator | show | US1, FR-011, ia.md R-08 |
| N-02 | User sees search input field to quickly search and find specific artists | input / act | FR-011, ia.md R-08 |
| N-03 | User sees suggested/trending artist cards (avatar, name, genre, nation) with follow toggle button | show / act | FR-011, FR-012, ia.md R-08 |
| N-04 | User sees selection counter indicating number of selected artists | show | FR-011, ia.md R-08 |
| N-05 | User sees "Complete" primary CTA button to mark onboarding finished and enter Home | act | FR-011, ia.md R-08 |
| N-06 | User sees "Skip for now" secondary text/button to bypass onboarding immediately | act | FR-011, D-9, ia.md R-08 |
| N-07 | Unauthenticated visitor sees in-page sign-in prompt card explaining onboarding purpose | feedback / act | ia.md §3.2, D-13 |
| N-08 | User sees skeleton loaders while suggested artists are loading | feedback | ia.md §3.2 loading pattern |
| N-09 | User sees error banner with retry option on API failure | feedback | ia.md §3.2 API failure |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `VStack` + `Heading` + `Text` | shared (`_shared.md` §3) | centered title & subtitle | SEED layout and typography for welcoming onboarding header. |
| N-02 | `TextFieldInput` | list (`components/text-field-input`) | search icon prefix + clear action | SEED input field for filtering suggested artists. |
| N-03 | `List` + `Avatar` + `ActionButton` | list (`components/list`, `components/avatar`, `components/action-button`) | avatar pill rows + follow toggle | SEED components for artist discovery and selection during onboarding. |
| N-04 | `Badge` | list (`components/badge`) | pill variant | SEED badge for indicating count of followed artists. |
| N-05 | `ActionButton` | list (`components/action-button`) | `variant="brand"`, full-width | SEED primary action button for completing onboarding flow. |
| N-06 | `ActionButton` | list (`components/action-button`) | `variant="outline"`, `tone="neutral"` | SEED secondary button for skippable onboarding. |
| N-07 | `ContentPlaceholder` | shared (`_shared.md` §3) | user icon + Sign-in CTA | SEED placeholder for unauthenticated in-page sign-in prompt. |
| N-08 | `Skeleton` | shared (`_shared.md` §3) | artist card skeleton | SEED skeleton loader during suggested artist list retrieval. |
| N-09 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry | SEED callout banner for network/API failures. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [N-01] "Pick Your Favorite Artists"   |
|  [N-01] "Follow artists to get event   |
|          updates and ticketing alerts" |
+----------------------------------------+
|  [N-02] [Search artists...           ] |
+----------------------------------------+
|  [N-09] (Error Banner if API fails)    |
|  [N-07] (Auth Prompt if Unauth)        |
+----------------------------------------+
|  Suggested Artists:                    |
|  [N-03] [Avatar] Artist 1  [ + Follow] |
|  [N-03] [Avatar] Artist 2  [ Following]|
|  [N-03] [Avatar] Artist 3  [ + Follow] |
|  (Infinite scroll trigger at bottom)   |
+----------------------------------------+
|  Sticky Bottom Action Bar:             |
|  [N-04] [Selected: 2 artists]          |
|  [N-05] [ Complete & Go to Home (CTA) ]|
|  [N-06] [ Skip for now ]               |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  Onboarding Container (max-w 720px centered)                |
|  [N-01] "Pick Your Favorite Artists"                        |
|  [N-01] Subtitle                                            |
|                                                             |
|  [N-02] Search Input Field                                  |
|                                                             |
|  Suggested / Trending Artists:                              |
|  +-------------------------------------------------------+  |
|  | [N-03] Artist 1: Avatar | Name | Genre | [Follow]     |  |
|  | [N-03] Artist 2: Avatar | Name | Genre | [Following]  |  |
|  +-------------------------------------------------------+  |
|                                                             |
|  Actions: [N-04] Selected: 2                                |
|  [N-05] [ Complete & Start ]      [N-06] [ Skip for now ]   |
+-------------------------------------------------------------+
```

**Regions**:
1. **Onboarding Header (`N-01`)**: Welcome title and explanation -- priority 1 (first viewport).
2. **Search & Filter Bar (`N-02`)**: Search input for artists -- priority 1 (first viewport).
3. **Suggested Artists List (`N-03`, `N-07`, `N-08`, `N-09`)**: Artist cards with follow buttons -- priority 2.
4. **Bottom Completion Actions (`N-04`, `N-05`, `N-06`)**: Selected counter, Complete CTA, Skip button -- priority 1 (sticky bottom on mobile).

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Artist name & avatar | N-03 | Avatar image + artist name string | Truncate name after 1 line |
| Genre & Nation | N-03 | Subtitle string (e.g., `K-Pop · KR`) | N/A |
| Selected count | N-04 | Badge integer (e.g., `3`) | N/A |
| Onboarding state | N-05, N-06 | Boolean flag (`onboardingCompleted: true`) | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Search Artist Input (`N-02`) | Debounced input (300ms) | Filters suggested artists list | Shows empty search placeholder |
| Follow Button Click (`N-03`) | Immediate toggle state & icon update | Persists follow, increments selected counter (`N-04`) | Rolls back toggle, shows error toast |
| Complete Button Click (`N-05`) | Button loading spinner | Updates onboarding state to completed; navigates to Home (`R-02`) | Displays error callout |
| Skip Button Click (`N-06`) | Navigation transition | Updates onboarding state to completed; navigates to Home (`R-02`) | Displays error callout |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load | Skeleton artist cards (`N-08`) | Auto-resolves on fetch |
| Empty (first use) | Normal onboarding start | Suggested/trending artists with zero selected | User follows artists or clicks Skip |
| Empty (filtered to zero) | Search yields no matching suggested artists | `ContentPlaceholder` with "No artists found" | User clears search input |
| Error (recoverable) | Network error saving onboarding | Critical callout banner (`N-09`) with retry | User clicks "Retry" |
| Error (auth/session) | Unauthenticated visitor lands on onboarding | `ContentPlaceholder` (`N-07`) with Sign-in CTA | User signs in via `R-01` |
| Partial / stale | Slow artist pagination | Bottom loader indicator | Auto-retries or user scrolls |
| Success | Onboarding completed or skipped | Redirection to Home (`R-02`) | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Title, subtitle, and CTA button labels fit comfortably in mobile and desktop viewports.
- Date / time / d-day formatting per locale: N/A.
- Copy keys:
  - `onboarding.title` -> JP: "お気に入りのアーティストを選んでください" / KR: "좋아하는 아티스트를 선택하세요"
  - `onboarding.subtitle` -> JP: "フォローすると公演情報やチケット通知をいち早く受け取れます" / KR: "팔로우하면 공연 소식과 티켓팅 알림을 가장 먼저 받아볼 수 있습니다"
  - `onboarding.complete` -> JP: "選択を完了して始める" / KR: "선택 완료하고 시작하기"
  - `onboarding.skip` -> JP: "今はスキップする" / KR: "지금은 건너뛰기"
  - `onboarding.selectedCount` -> JP: "{count}組 選択中" / KR: "{count}명 선택됨"

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Header -> Search Bar -> Artist Cards -> Complete CTA -> Skip Link.
- Landmarks / headings: `<main>` landmark with `<h1>` for onboarding headline.
- Non-visual feedback for async changes: `aria-live="polite"` announcing artist selection and count updates.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US1, FR-011: Complete first-time onboarding by picking artists | Following artists and clicking Complete (`N-05`) marks onboarding done and routes to Home |
| D-9: Skippable onboarding | Clicking Skip (`N-06`) bypasses selection and marks onboarding complete |
| D-13: In-page auth prompt for unauthenticated visitors | Visiting without session shows in-page card (`N-07`) |
| FR-012: Cursor-based infinite scroll | Scrolling suggested list fetches additional trending artists |
