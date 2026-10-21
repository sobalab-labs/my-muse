# UI Spec: R-09 Legal Documents (Terms of Service / Privacy Policy)

**Path**: `/[locale]/(app)/legal/[doc]` | **IA row**: ia.md §3 R-09 | **Spec stories**: FR-015 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Display terms of service and privacy policy documents with clear typography and document switching. Success signal: user easily reads legal terms or toggles between terms and privacy policies.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees legal document header (document title and effective/last updated date) | show | FR-015, ia.md R-09 |
| N-02 | User sees document selector toggle to switch between Terms of Service (`terms`) and Privacy Policy (`privacy`) | act | FR-015, ia.md R-09 |
| N-03 | User sees structured, readable article body with sections, clauses, and lists | show | FR-015, ia.md R-09 |
| N-04 | User sees back button to return to previous route (Login or Settings) | act | ia.md R-09 links |
| N-05 | User sees not found view if route param `[doc]` is invalid | feedback | ia.md §3.2 not found |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `VStack` + `Heading` + `Text` | shared (`_shared.md` §3) | document title + updated date | SEED layout and typography for legal header. |
| N-02 | `SegmentedControl` | list (`components/segmented-control`) | options: `terms`, `privacy` | SEED segmented control for toggling active legal document. |
| N-03 | `Article` + `Box` | list (`components/article`, layout) | structured typography prose | SEED article component for long-form legal documentation. |
| N-04 | `ActionButton` | list (`components/action-button`) | `variant="outline"`, back arrow icon | SEED button for back navigation to referring route. |
| N-05 | `ContentPlaceholder` | shared (`_shared.md` §3) | document error placeholder | SEED placeholder for nonexistent document slug. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [N-04] [< Back]  Legal Documents      |
+----------------------------------------+
|  [N-02] [ Terms of Service | Privacy ] |
+----------------------------------------+
|  [N-01] Terms of Service (H1)          |
|  [N-01] Last Updated: 2026-10-15       |
+----------------------------------------+
|  [N-03] Article Prose:                 |
|  Article 1 (Purpose)                   |
|  These terms govern your access to...  |
|                                        |
|  Article 2 (User Accounts)             |
|  Users may authenticate via Google...  |
|                                        |
|  Article 3 (Privacy & Notifications)   |
|  Notification preferences may be...    |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  Legal Documents Container (max-w 840px centered)           |
|  [N-04] [< Back]                                            |
|  [N-02] [ Terms of Service | Privacy Policy ]               |
|                                                             |
|  [N-01] Document Title (H1)                                 |
|  [N-01] Effective Date: October 15, 2026                    |
|                                                             |
|  [N-03] Article Long-Form Typography Content                |
|  - Section 1: Overview & Definitions                        |
|  - Section 2: OAuth & Account Usage                         |
|  - Section 3: Notification Services                         |
|  - Section 4: Data Retention & Account Deletion             |
+-------------------------------------------------------------+
```

**Regions**:
1. **Header & Navigation (`N-04`, `SN-02`)**: Back button / nav shell -- priority 1.
2. **Document Switcher (`N-02`)**: Segmented control (`terms` vs `privacy`) -- priority 1 (first viewport).
3. **Legal Content Area (`N-01`, `N-03`, `N-05`)**: Title, last updated date, full article body -- priority 1.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Document type (`[doc]`) | N-01, N-02 | Slug (`terms` or `privacy`) | N/A |
| Effective date | N-01 | `YYYY-MM-DD` | N/A |
| Document body | N-03 | Formatted markdown/rich text sections | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Document Switcher Click (`N-02`) | Segment animation | Navigates to `/[locale]/legal/[doc]` | N/A |
| Back Button Click (`N-04`) | Navigation transition | Returns to referring page (e.g. `R-01` or `R-07`) | Fallback to Home (`R-02`) |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Static SSR (instant render) | Fully rendered article | N/A |
| Empty (first use) | Normal static render | Complete legal text | N/A |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | N/A (Static page) | N/A | N/A |
| Error (auth/session) | Invalid `[doc]` param | Not-found placeholder (`N-05`) | User clicks Back to Home |
| Partial / stale | N/A | N/A | N/A |
| Success | Page rendered | Formatted legal article with active document switcher | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Legal terms are localized into complete Japanese and Korean articles; line-height and paragraph spacing adhere to readability standards.
- Date / time / d-day formatting per locale:
  - JA: `最終改定日: 2026年10月15日`
  - KR: `최종 개정일: 2026년 10월 15일`
- Copy keys:
  - `legal.terms` -> JP: "利用規約" / KR: "이용약관"
  - `legal.privacy` -> JP: "プライバシーポリシー" / KR: "개인정보처리방침"
  - `legal.lastUpdated` -> JP: "最終改定日" / KR: "최종 개정일"

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Back Button -> Document Switcher -> Document Body Links.
- Landmarks / headings: `<main>` landmark with `<h1>` for document title and `<h2>` for each article section.
- Non-visual feedback for async changes: N/A (static content).

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| FR-015: Terms of service and privacy policy documents display | Visiting `/[locale]/legal/terms` or `/[locale]/legal/privacy` renders document (`N-01`, `N-03`) |
| D-12: Single dynamic route for legal documents | Switching between `terms` and `privacy` via `N-02` updates route param `[doc]` |
