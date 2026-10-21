# UI Spec: R-05 Event Detail View

**Path**: `/[locale]/(app)/events/[id]` | **IA row**: ia.md §3 R-05 | **Spec stories**: US3, FR-004, FR-005, FR-013 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Display complete concert event details (participating artists, date, venue, duration, official announcements) and provide ticketing link navigation, add-to-calendar (ICS), and event sharing. Success signal: user successfully views event information and executes external ticketing, calendar export, or sharing actions.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees event header: title, date & time, venue name & address, duration, and D-Day badge | show | US3, FR-004, FR-013, ia.md R-05 |
| N-02 | User sees participating artist(s) card/avatar list linking to Artist Detail (`R-04`) | show / act | US3, FR-004, ia.md R-05 |
| N-03 | User sees primary CTA button to open external ticketing URL in a new window | act | US3, FR-013, ia.md R-05 |
| N-04 | User sees secondary action button to view official announcement URL | act | FR-013, ia.md R-05 |
| N-05 | User sees action button to add event to personal calendar (ICS download) | act | FR-013, ia.md R-05 |
| N-06 | User sees action button to share event (Web Share API or copy link with toast confirmation) | act / feedback | FR-013, ia.md R-05 |
| N-07 | User sees loading skeleton during async event data retrieval | feedback | ia.md §3.2 loading pattern |
| N-08 | User sees error banner with retry or not-found redirection if event ID does not exist | feedback | ia.md §3.2 API failure / not-found |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `VStack` + `Badge` + `Text` | list (`components/badge`, layout) | D-day badge, responsive title & venue typography | SEED badge and typography for clear event metadata hierarchy. |
| N-02 | `HStack` + `Avatar` + `Link` | list (`components/avatar`, layout) | clickable artist avatar + name pill | SEED avatar and layout for participating artist navigation. |
| N-03 | `ActionButton` | list (`components/action-button`) | `variant="brand"`, full-width on mobile | SEED primary action button for ticketing conversion. |
| N-04 | `ActionButton` | list (`components/action-button`) | `variant="outline"` | SEED secondary action button for official announcement source. |
| N-05 | `ActionButton` | list (`components/action-button`) | `variant="neutral"`, calendar icon | SEED utility button for ICS calendar export. |
| N-06 | `ActionButton` + `Snackbar` | shared (`_shared.md` §3) | share icon + toast feedback | SEED button and snackbar provider for copy link/share confirmation. |
| N-07 | `Skeleton` | shared (`_shared.md` §3) | event detail layout skeleton | SEED skeleton loader during page fetch. |
| N-08 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` with retry button | SEED callout banner for network/API failures. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Nav / Back Header             |
+----------------------------------------+
|  [N-01] [D-Day Badge]                  |
|  [N-01] Event Title (H1)               |
|  [N-01] Date & Time / Duration         |
|  [N-01] Venue & Location               |
+----------------------------------------+
|  Participating Artists:                |
|  [N-02] [Avatar] Artist Name (-> R-04) |
+----------------------------------------+
|  Actions Bar:                          |
|  [N-05] [ Add to Calendar (ICS) ]      |
|  [N-06] [ Share Event ]                |
+----------------------------------------+
|  [N-04] [ View Official Announcement ] |
+----------------------------------------+
|  Sticky Bottom Bar:                    |
|  [N-03] [ Open Ticketing Page (CTA) ]  |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  +-------------------------------------+ +---------------+  |
|  | [N-01] [D-Day Badge]                | | [N-03] Tickets |  |
|  | [N-01] Event Title (H1)             | | [N-04] Notice  |  |
|  | [N-01] Date, Time, Venue            | | [N-05] Cal ICS |  |
|  |                                     | | [N-06] Share   |  |
|  | Participating Artists:              | +---------------+  |
|  | [N-02] [Avatar] Artist Name         |                    |
|  +-------------------------------------+                    |
+-------------------------------------------------------------+
```

**Regions**:
1. **Navigation Header (`SN-02`)**: Back button / top navigation -- priority 3.
2. **Event Primary Details (`N-01`, `N-02`)**: Title, D-Day, date/venue, participating artists -- priority 1 (first viewport).
3. **Action Cluster / Sticky CTA (`N-03`, `N-04`, `N-05`, `N-06`)**: Ticketing CTA, announcement, calendar, share -- priority 1 (first viewport).

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Event title (`title`) | N-01 | Large heading string | Truncate after 3 lines |
| Date & Time | N-01 | `YYYY년 M월 일 (요일) HH:mm` | N/A |
| Venue / Place | N-01 | Venue name + address string | Wrap to multiple lines |
| Duration | N-01 | e.g. "약 120분" / "約120分" | N/A |
| D-Day badge | N-01 | `D-Day`, `D-7`, `종료` | N/A |
| Participating artists | N-02 | Clickable avatar pills linking to `R-04` | Wrap to multiple rows |
| Ticketing URL | N-03 | External URL hyperlink (`target="_blank"`) | N/A |
| Official URL | N-04 | External URL hyperlink (`target="_blank"`) | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Ticketing Button Click (`N-03`) | Scale feedback | Opens ticketing vendor in new tab | N/A |
| Announcement Button Click (`N-04`) | Scale feedback | Opens official announcement URL | N/A |
| Add to Calendar Click (`N-05`) | Immediate file download | Downloads `.ics` file with concert event details | Displays error toast |
| Share Button Click (`N-06`) | Native share sheet or clipboard copy | Displays "Link copied" snackbar (`SN-04`) | Displays error toast |
| Participating Artist Click (`N-02`) | Scale feedback | Navigates to Artist Detail (`R-04`) | N/A |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load or navigation | Full event detail skeleton (`N-07`) | Auto-resolves on data load |
| Empty (first use) | N/A | N/A | N/A |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | Network error during event load | Inline callout banner (`N-08`) with retry | User clicks "Retry" |
| Error (auth/session) | N/A (Public page) | N/A | N/A |
| Partial / stale | Slow asset fetch | Detail layout rendered, skeleton on artist avatar | Auto-resolves |
| Success | Data fully loaded | Complete event view with active CTA buttons | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Venue names and concert titles can be long in both languages; action buttons maintain responsive minimum sizing.
- Date / time / d-day formatting per locale:
  - JA: `2026年10月15日 (水) 18:00` / `約120分`
  - KR: `2026년 10월 15일 (수) 18:00` / `약 120분`
- Copy keys:
  - `event.tickets` -> JP: "チケットを購入する" / KR: "예매처 바로가기"
  - `event.officialNotice` -> JP: "公式案内を見る" / KR: "공식 공지 보기"
  - `event.calendar` -> JP: "カレンダーに追加" / KR: "캘린더에 추가"
  - `event.share` -> JP: "共有する" / KR: "공유하기"
  - `event.copied` -> JP: "リンクをコピーしました" / KR: "링크가 복사되었습니다"

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Header -> Event Details -> Artists -> Ticketing CTA -> Other Actions.
- Landmarks / headings: `<main>` landmark with `<h1>` for event title.
- Non-visual feedback for async changes: Screen reader notification when ICS is generated or link is copied.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US3, FR-004: Event detail view displays complete event metadata | Visiting page renders title, date, venue, duration, artists (`N-01`, `N-02`) |
| FR-013: Add to calendar (ICS), share, and open ticketing | Clicking ticketing (`N-03`), calendar (`N-05`), and share (`N-06`) performs expected actions |
| D-4: Dedicated event detail route | Accessible via deep link at `/[locale]/events/[id]` |
