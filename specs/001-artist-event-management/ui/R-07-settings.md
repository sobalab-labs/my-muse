# UI Spec: R-07 Global Settings, Notifications & Account

**Path**: `/[locale]/(app)/settings` | **IA row**: ia.md §3 R-07 | **Spec stories**: US1, US5, US6, FR-002, FR-008, FR-009, FR-014 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Manage application interface language preferences, notification subscriptions for followed artists and general updates, sign out, and permanent account deletion. Success signal: user successfully updates preferences, configures notification toggles, or executes account actions with immediate feedback.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees Language Setting section to switch app language between Japanese (`ja`) and Korean (`ko`) | input / act | US1, FR-002, ia.md R-07 |
| N-02 | User sees Followed Artist Notification toggles with priorities 1 > 2 > 3 (new event, ticket open bundle, discography) | input / act | US5, FR-008, ia.md R-07 |
| N-03 | User sees General Weekly Event Update notification toggle and delivery channel checkboxes (email, Kakao message) | input / act | US6, FR-009, ia.md R-07 |
| N-04 | Unauthenticated visitor sees in-page sign-in prompt for notifications and account management | feedback / act | ia.md §3.2, D-13 |
| N-05 | Authenticated user sees Sign Out action button with confirmation dialog | act | US1, FR-014, ia.md R-07 |
| N-06 | Authenticated user sees Delete Account action button with destructive confirmation dialog | act | US1, FR-014, ia.md R-07 |
| N-07 | User sees links to Legal documents (Terms of Service, Privacy Policy at `R-09`) | show / act | FR-015, ia.md R-07 |
| N-08 | User sees skeleton loaders while fetching profile and notification preferences | feedback | ia.md §3.2 loading pattern |
| N-09 | User sees feedback snackbar/callout when preferences update or on API failure | feedback | ia.md §3.2 |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `SegmentedControl` | list (`components/segmented-control`) | full-width, options: `ja`, `ko` | SEED segmented control for language selection. |
| N-02 | `Switch` | list (`components/switch`) | labelled toggle switches | SEED switch primitive for notification preference toggles. |
| N-03 | `Switch` + `Checkbox` | list (`components/switch`, `components/checkbox`) | weekly toggle + channel checkboxes | SEED switches and checkboxes for weekly digest and delivery channels. |
| N-04 | `ContentPlaceholder` | shared (`_shared.md` §3) | lock icon + sign-in CTA button | SEED content placeholder for unauthenticated in-page prompt. |
| N-05 | `ActionButton` + `AlertDialog` | list (`components/action-button`, `components/alert-dialog`) | `variant="outline"`, `tone="neutral"` | SEED button and alert dialog for sign-out confirmation. |
| N-06 | `ActionButton` + `AlertDialog` | list (`components/action-button`, `components/alert-dialog`) | `variant="outline"`, `tone="critical"` | SEED destructive action button and confirmation modal for account deletion. |
| N-07 | `List` + `Link` | list (`components/list`, layout) | navigation list items | SEED list rows linking to terms and privacy documents. |
| N-08 | `Skeleton` | shared (`_shared.md` §3) | card & toggle skeletons | SEED skeleton loader during async preference fetching. |
| N-09 | `Snackbar` + `Callout` | shared (`_shared.md` §3) | toast notification / critical callout | SEED snackbar and callout for preference update confirmation and error rollback. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [SN-02] Navigation Bar (Settings)     |
+----------------------------------------+
|  Settings (H1)                         |
+----------------------------------------+
|  Language Preferences:                 |
|  [N-01] [ Japanese (ja) | Korean (ko) ]|
+----------------------------------------+
|  [N-04] (If Unauthenticated: Sign-in   |
|          card to manage notifications) |
+----------------------------------------+
|  Artist Notifications (Auth Only):     |
|  [N-02] New Event Announcements  [ON]  |
|  [N-02] Ticket Open Bundle       [ON]  |
|  [N-02] Discography Releases     [OFF] |
+----------------------------------------+
|  General Updates (Auth Only):          |
|  [N-03] Weekly Digest            [ON]  |
|  Channels: [x] Email  [x] Kakao        |
+----------------------------------------+
|  Legal Documents:                      |
|  [N-07] Terms of Service >             |
|  [N-07] Privacy Policy >               |
+----------------------------------------+
|  Account Actions (Auth Only):          |
|  [N-05] [ Sign Out ]                   |
|  [N-06] [ Delete Account ]             |
+----------------------------------------+
|  [SN-02] Bottom Navigation Bar         |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+-------------------------------------------------------------+
|  [SN-02] Persistent Top Navigation Bar & Locale Switcher   |
+-------------------------------------------------------------+
|  Settings (max-w 800px centered)                            |
|                                                             |
|  Language: [N-01] [ ja | ko ]                               |
|                                                             |
|  Notification Subscriptions:                                |
|  +-------------------------------------------------------+  |
|  | [N-02] New Event Announcements                 [Toggle]| |
|  | [N-02] Ticket Open Bundle (1d/30m/open)        [Toggle]| |
|  | [N-02] Discography Releases                    [Toggle]| |
|  | [N-03] General Weekly Event Digest             [Toggle]| |
|  | Delivery Channels: [Checkbox] Email  [Checkbox] Kakao |  |
|  +-------------------------------------------------------+  |
|                                                             |
|  Legal & Account:                                           |
|  [N-07] Terms of Service | [N-07] Privacy Policy            |
|  [N-05] [ Sign Out ]        [N-06] [ Delete Account ]       |
+-------------------------------------------------------------+
```

**Regions**:
1. **Header & Navigation (`SN-02`)**: Persistent nav bar -- priority 3.
2. **Language Preference Section (`N-01`)**: Language switcher -- priority 1 (first viewport).
3. **Notification Configuration Section (`N-02`, `N-03`, `N-04`, `N-08`)**: Toggles and channels or sign-in prompt -- priority 2.
4. **Legal & Account Section (`N-05`, `N-06`, `N-07`)**: Legal links, sign-out, and account deletion -- priority 3.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Current locale | N-01 | Selected tab (`ja` / `ko`) | N/A |
| Followed artist notification toggles | N-02 | Priority 1 (New event), Priority 2 (Ticketing), Priority 3 (Discography) | N/A |
| Weekly digest toggle | N-03 | Boolean toggle state | N/A |
| Delivery channels | N-03 | Multi-select checkboxes (email, Kakao) | N/A |
| Legal paths | N-07 | Links to `/[locale]/legal/terms` & `privacy` | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Language Segment Click (`N-01`) | Immediate active tab transition | Navigates to updated locale prefix route | Fallback to current locale |
| Notification Toggle Click (`N-02`, `N-03`) | Switch visual toggle (optimistic) | Mutates subscription config via API; shows toast (`N-09`) | Rolls back toggle, shows critical callout |
| Delivery Channel Checkbox Click (`N-03`) | Checkbox checked toggle | Mutates channel preferences | Rolls back check, shows error snackbar |
| Sign Out Click (`N-05`) | Opens `AlertDialog` | On confirmation, ends session and redirects to Home (`R-02`, trending) | Displays error banner |
| Delete Account Click (`N-06`) | Opens destructive `AlertDialog` | On confirmation, deletes account data and redirects to Home | Displays error banner |
| Legal Link Click (`N-07`) | Navigation transition | Navigates to `R-09` legal document | N/A |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load | Skeleton toggle list (`N-08`) | Auto-resolves on fetch |
| Empty (first use) | Normal authenticated state | Default system notification toggles enabled | User customizes toggles |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | Network failure updating subscription | Switch reverts, error snackbar (`N-09`) | User retries toggle |
| Error (auth/session) | Unauthenticated visitor lands on settings | Language section active; notification card shows in-page sign-in prompt (`N-04`) | User signs in via `R-01` |
| Partial / stale | Slow settings save | Save indicator / subtle spinner on toggle | Auto-resolves |
| Success | Preferences updated | Immediate UI update + "Settings saved" snackbar | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Setting labels and descriptions have ample horizontal room in list item cards.
- Date / time / d-day formatting per locale: N/A.
- Copy keys:
  - `settings.title` -> JP: "設定" / KR: "설정"
  - `settings.language` -> JP: "言語設定" / KR: "언어 설정"
  - `settings.notifications` -> JP: "通知設定" / KR: "알림 설정"
  - `settings.newEvent` -> JP: "新しい公演情報の通知" / KR: "새 공연 소식 알림"
  - `settings.ticketing` -> JP: "チケットオープン通知 (前日・30分前・開始時)" / KR: "티켓 오픈 알림 (전일/30분전/오픈)"
  - `settings.discography` -> JP: "音源・リリース通知" / KR: "신규 음원/앨범 알림"
  - `settings.weeklyDigest` -> JP: "週間イベントまとめ通知" / KR: "주간 이벤트 소식 알림"
  - `settings.signOut` -> JP: "ログアウト" / KR: "로그아웃"
  - `settings.deleteAccount` -> JP: "アカウント削除" / KR: "회원 탈퇴"

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows Header -> Language Switcher -> Notification Toggles -> Legal Links -> Sign Out -> Delete Account.
- Landmarks / headings: `<main>` landmark with `<h1>` for page heading and `<h2>` for each section.
- Non-visual feedback for async changes: `aria-checked` states on switches and checkboxes; `role="alertdialog"` for destructive modals.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US1, FR-002: Language setting configuration | Selecting JA or KO (`N-01`) updates application locale |
| US5, FR-008: Followed artist notification subscriptions with priorities 1 > 2 > 3 | Toggling switches (`N-02`) updates notification subscriptions |
| US6, FR-009: General weekly event update subscriptions and channels | Toggling weekly switch and channel checkboxes (`N-03`) persists preferences |
| US1, FR-014: Sign-out and permanent account deletion | Clicking Sign Out (`N-05`) or Delete Account (`N-06`) triggers confirmation dialog and session cleanup |
| D-13: In-page sign-in prompt for notification and account sections | Unauthenticated visitors see in-page sign-in card (`N-04`) while still accessing language settings |
