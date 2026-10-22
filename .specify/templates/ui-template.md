# UI Spec: [ROUTE-ID] [route name]

**Path**: `[path from ia.md]` | **IA row**: ia.md §3 [Route ID] | **Spec stories**: [US-#] | **Status**: Draft | Approved
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) → component choices (§3) → composition (§4) → the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

[The one task this screen exists for; what "done" looks like for the user.]

## 2. UI Needs

<!-- Derived from the spec stories, FRs, edge cases and the IA row for this route. State each need as what the user must see or do, NOT as a component. -->

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | | | |

## 3. Component Choices

<!-- One row per need. Rules come ONLY from _shared.md §1; the primary pool is the list tool's result named there. Reuse _shared.md §3 choices first (Source: shared). Decide from the list when the fit is clear (Source: list). Fetch details only for ambiguous needs (Source: list+get). Use the fallback pool only when the primary pool has no fit (Source: fallback:<name>, add +get if details were fetched). Write "No fit in pool" in the Component cell instead of inventing or customizing. Mark a name UNVERIFIED in Source if the source could not be reached. -->

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|

## 4. Composition (mobile-first, then desktop)

<!-- Arrange ONLY the components chosen in §3 (plus plain layout containers). Label each block with its Need ID. -->

```text
[ASCII wireframe — mobile ≤ 640px]
```

```text
[ASCII wireframe — desktop ≥ 1024px]
```

**Regions** (name → purpose → Need IDs → priority order on mobile):

## 5. Data → UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|

<!-- For list screens with filter/sort: controls shown, default state, applied-filter display, clear/reset, result count, zero-result state, which state is mirrored in URL (per ia.md §3.2). -->

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | | | |
| Empty (first use) | | | |
| Empty (filtered to zero) | | | |
| Error (recoverable) | | | |
| Error (auth/session) | | | |
| Partial / stale | | | |
| Success | | | |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance:
- Date / time / d-day formatting per locale:
- Copy keys (namespace.key → JP intent / KR intent; final strings are translation work):

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management:
- Landmarks / headings:
- Non-visual feedback for async changes:

## 10. IA Change Requests (leave empty if none)

- [What IA assumption broke, evidence, proposed change. Do not apply it.]

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
