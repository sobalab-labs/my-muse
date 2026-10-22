# UI Spec: R-10 My Bookmarked Events

**Path**: `/[locale]/(app)/events/bookmarked` | **IA row**: ia.md §3 R-10 | **Spec stories**: T-18, T-19 | **Status**: Draft | Approved
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) → component choices (§3) → composition (§4) → the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

The user can view and manage their list of bookmarked concert events, with easy access to event details or unbookmarking actions. Success is a list of events with active unbookmarking capability, or a clear guide if the list is empty.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | Display list of bookmarked events | show | FR-017 |
| N-02 | Quick unbookmark action | act | T-18 |
| N-03 | Navigate to event detail | act | T-19 |
| N-04 | Empty state for no bookmarks | feedback | Edge case |
| N-05 | Loading/Error states | feedback | Edge case |
| N-06 | Auth prompt for unauthenticated users | feedback | D-13 |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `List` | shared | cursor-paginated | Shared list container for structured feed. |
| N-02 | `Button` | shadcn | variant="ghost" | Fallback for unbookmark action. |
| N-03 | `Link` / `Box` | shared | clickable-card | Navigation to event detail. |
| N-04 | `ContentPlaceholder` | shared | illustration+cta | Standard empty state for zero results. |
| N-05 | `Skeleton` / `Callout` | shared | - | Shared loading/error feedback. |
| N-06 | `Callout` / `Button` | shared | tone="informative" | In-page auth sign-in prompt pattern. |

## 4. Composition (mobile-first, then desktop)

```text
[ Mobile: Persistent Nav(Bottom) + Title + List(N-01) ]
```

```text
[ Desktop: Persistent Nav(Top) + Main Content(Title + List(N-01)) ]
```

**Regions** (name → purpose → Need IDs → priority order on mobile):
- Header: Title "Bookmarked Events" -> N-01
- Main: List of events -> N-01, N-02, N-03

## 5. Data → UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Event Name | N-01 | Text | Truncate at 2 lines |
| Event Date/Time | N-01 | Localized Date | - |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| N-02 (Unbookmark) | Optimistic update | Bookmark removed | Rollback + Error banner |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial load | Skeleton (`SN-11`) | - |
| Empty | 0 bookmarks | Placeholder (`SN-10`) | Browse events |
| Success | Bookmarks exist | List of events | - |
| Error | API fail | Callout (`SN-05`) | Retry |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Use flexible spacing.
- Date / time / d-day formatting per locale: Per `_shared.md` §6.

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Standard list navigation.
- Landmarks / headings: Page title `<h1>`.

## 10. IA Change Requests (leave empty if none)

-

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| Unbookmark event | Unbookmark button click, optimistic remove, API request success |
| View detail | Event card click navigation |
| Empty list | Placeholder displayed |
