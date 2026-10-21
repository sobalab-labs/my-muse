# UI Spec: R-04 Artist Profile & Detail View

**Path**: `/[locale]/(app)/artists/[id]` | **IA row**: ia.md §3 R-04 | **Spec stories**: US2, FR-003 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Display comprehensive artist profile metadata (genre, nation, website/SNS URLs), associated upcoming events, and provide follow/unfollow action (prompting sign-in for unauthenticated visitors). Success signal: user successfully reviews artist details, toggles follow status, or navigates to an upcoming event detail.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees artist hero section with artist image/avatar, name, nationality/country, and genre badges | show | US2, FR-003, ia.md R-04 |
| N-02 | User sees artist external links (official website, SNS profiles such as Twitter, YouTube, Instagram) | show / act | US2, FR-003, ia.md R-04 |
| N-03 | User sees follow/unfollow action button (prompts sign-in if unauthenticated) | act | US2, FR-003, ia.md R-04 |
| N-04 | User sees section listing upcoming concert events featuring this artist (date, title, venue, d-day) | show / act | US2, FR-003, ia.md R-04 |
| N-05 | User sees empty state when artist has zero upcoming concert events | feedback | ia.md §3.2 empty state |
| N-06 | User sees loading skeleton during async artist data fetching | feedback | ia.md §3.2 loading pattern |
| N-07 | User sees error banner with retry or not-found redirection if artist ID is invalid or API fails | feedback | ia.md §3.2 API failure / not-found |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `Avatar` + `Badge` + `VStack` | list (`components/avatar`, `components/badge`) | large circular avatar, genre/nation badges | SEED avatar and badges for rich artist profile presentation. |
| N-02 | `HStack` + `Link` | list (`components/typography/text`, layout) | icon link buttons | SEED layout and links for external official website and SNS URLs. |
| N-03 | `ActionButton` / `ToggleButton` | list (`components/action-button`) | `variant="brand"`, follow/unfollow toggle | SEED action button for toggling follow status with feedback. |
| N-04 | `List` + `Box` | shared (`_shared.md` §3) | structured event card rows | SEED list primitive and box containers for upcoming concert events. |
| N-05 | `ContentPlaceholder` | shared (`_shared.md` §3) | calendar illustration + notification CTA | SEED placeholder for zero upcoming concert events state. |
| N-06 | `Skeleton` | shared (`_shared.md` §3) | profile header and event list skeletons | SEED skeleton loader during data retrieval. |
| N-07 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry button | SEED callout banner for network or server fetch errors. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Nav / Back Header             |
+----------------------------------------+
|  [N-01] Artist Avatar (centered)       |
|  [N-01] Artist Name (H1)               |
|  [N-01] [Nation Badge] [Genre Badge]   |
|                                        |
|  [N-02] [Web] [Twitter] [YouTube]      |
|                                        |
|  [N-03] [ + Follow Artist Button ]     |
+----------------------------------------+
|  Upcoming Events Section               |
|  [N-04] Event 1 (Date, Venue, D-Day)   |
|  [N-04] Event 2 (Date, Venue, D-Day)   |
|  [N-05] (Empty Placeholder if none)    |
+----------------------------------------+
|  [SN-02] Bottom Navigation Bar         |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  +-------------------------------------------------------+  |
|  | [N-01] Avatar   [N-01] Artist Name (H1)               |  |
|  |                 [N-01] [Nation] [Genre]               |  |
|  |                 [N-02] External Links                 |  |
|  |                 [N-03] [ Follow Button ]              |  |
|  +-------------------------------------------------------+  |
|                                                             |
|  Upcoming Concert Events                                    |
|  +-----------------------+  +----------------------------+  |
|  | [N-04] Event Card 1   |  | [N-04] Event Card 2        |  |
|  +-----------------------+  +----------------------------+  |
+-------------------------------------------------------------+
```

**Regions**:
1. **Navigation Header (`SN-02`)**: Back button / top navigation -- priority 3.
2. **Artist Profile Hero (`N-01`, `N-02`, `N-03`)**: Avatar, name, genre/nation badges, external links, follow button -- priority 1 (first viewport).
3. **Upcoming Events Section (`N-04`, `N-05`, `N-06`)**: Event cards list or empty state -- priority 2.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Artist name (`name`) | N-01 | Main heading string | Truncate after 2 lines |
| Nation & Genre | N-01 | Text in badge format (e.g., `JP`, `Rock`) | N/A |
| SNS / Web URLs | N-02 | External anchor links with platform icons | Wrap to multiple lines if needed |
| Follow status | N-03 | Active boolean toggle ("Following" vs "Follow") | N/A |
| Upcoming event items | N-04 | Event title, date, venue, D-Day | Truncate title after 2 lines |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Follow Button Click (`N-03`) | Immediate toggle state & icon update | Persists follow relation; opens sign-in prompt if unauthenticated | Rolls back state and displays error toast |
| External Link Click (`N-02`) | Opens new browser tab | Navigates to official external URL | N/A |
| Event Card Click (`N-04`) | Scale feedback | Navigates to Event Detail (`R-05`) | N/A |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load or route transition | Skeleton profile hero & event rows (`N-06`) | Auto-resolves on data load |
| Empty (first use) | Artist has no upcoming events scheduled | `ContentPlaceholder` (`N-05`) indicating no upcoming events | Prompt user to follow for future alerts |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | Network failure fetching upcoming events | Inline error callout (`N-07`) in events section with retry | User clicks "Retry" |
| Error (auth/session) | Unauthenticated user clicks Follow | In-page sign-in prompt modal / banner leading to `R-01` | User signs in |
| Partial / stale | Slow events list fetch | Profile hero loaded; event list shows spinner | Auto-resolves |
| Success | Data fully loaded | Full artist hero, external links, and upcoming events | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Badges and external link titles accommodate Japanese kanji/kana and Korean hangul comfortably.
- Date / time / d-day formatting per locale:
  - JA: `2026年10月15日` / D-Day: `D-10`
  - KR: `2026년 10월 15일` / D-Day: `D-10`
- Copy keys:
  - `artist.follow` -> JP: "フォローする" / KR: "팔로우"
  - `artist.following` -> JP: "フォロー中" / KR: "팔로잉"
  - `artist.upcomingEvents` -> JP: "開催予定の公演" / KR: "예정된 공연"
  - `artist.noEvents` -> JP: "現在予定されている公演はありません。" / KR: "현재 예정된 공연이 없습니다."

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Header -> External Links -> Follow Button -> Event Cards.
- Landmarks / headings: `<main>` landmark with `<h1>` for artist name and `<h2>` for upcoming events.
- Non-visual feedback for async changes: `aria-live="polite"` announcing follow state toggles.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US2, FR-003: View artist detail with metadata and upcoming events | Viewing profile displays artist info (`N-01`, `N-02`) and events list (`N-04`) |
| US2, FR-003: Follow/unfollow artist | Clicking follow button (`N-03`) toggles follow state or prompts sign-in |
| D-3: Dedicated artist detail route deep-linking | Visiting `/[locale]/artists/[id]` directly resolves artist metadata |
