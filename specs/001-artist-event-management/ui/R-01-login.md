# UI Spec: R-01 OAuth Login

**Path**: `/[locale]/login` | **IA row**: ia.md §3 R-01 | **Spec stories**: US1, FR-001 | **Status**: Draft
**Design system instruction**: see `ui/_shared.md` §1 (user's words, unchanged)
**Frozen inputs**: route path, purpose, data, URL state, auth come from ia.md and MUST NOT be changed here. If a change is needed, fill §10 and stop.
**Order of work**: needs (§2) -> component choices (§3) -> composition (§4) -> the rest. Do not draw the layout before the components are chosen.

## 1. Primary Task & Success Signal

Authenticate users via third-party OAuth providers (Google, Kakao) and redirect first-time users to Onboarding (`R-08`) or returning users to Home (`R-02`) / `?next=` destination upon successful session creation. Success signal: session cookie established and user redirected to target destination.

## 2. UI Needs

| Need ID | Need (what the user must see / do / be told) | Type (show / act / input / feedback) | Source (US / FR / edge case / ia.md data) |
|---------|-----------------------------------------------|--------------------------------------|-------------------------------------------|
| N-01 | User sees brand header, welcome message, and app description explaining the artist & concert event management hub | show | ia.md R-01 purpose, US1 |
| N-02 | User sees Google OAuth sign-in button to initiate authentication | act | US1, FR-001 |
| N-03 | User sees Kakao OAuth sign-in button to initiate authentication | act | US1, FR-001 |
| N-04 | User sees links to Terms of Service and Privacy Policy (`R-09`) acknowledging login agreement | show / act | FR-015, ia.md R-01 links |
| N-05 | Authenticated user visiting login page is automatically redirected to Home (`R-02`) or `?next=` destination | feedback | ia.md R-01 auth behavior |
| N-06 | User sees error feedback (inline callout or toast) when OAuth sign-in fails | feedback | edge case / API error |
| N-07 | User sees loading skeleton or spinner during OAuth redirect / token exchange | feedback | edge case / async state |

## 3. Component Choices

| Need ID | Component | Source | Props / variants | Reason (one line) |
|---------|-----------|--------|------------------|-------------------|
| N-01 | `Box` / `VStack` / `Text` / `Heading` | shared (`_shared.md` §3) | layout container + typography | SEED layout primitives for structured header and copy. |
| N-02 | `ActionButton` | list | `variant="solid"`, brand style | SEED action button for Google OAuth sign-in action. |
| N-03 | `ActionButton` | list | `variant="solid"`, brand style (Kakao yellow) | SEED action button for Kakao OAuth sign-in action. |
| N-04 | `Text` / `Link` | list (`components/typography/text`) | secondary text style | SEED typography for legal terms and privacy notices. |
| N-05 | `Box` / `Skeleton` | shared (`_shared.md` §3) | full-screen container / skeleton loader | SEED skeleton loader during auth session verification and redirect. |
| N-06 | `Callout` | shared (`_shared.md` §3) | `tone="critical"` | SEED inline callout banner for OAuth authentication failures. |
| N-07 | `Skeleton` / `ProgressCircle` | list (`components/skeleton`) | loading spinner variant | SEED loading indicator during asynchronous sign-in request. |

## 4. Composition (mobile-first, then desktop)

```text
[Mobile Viewport - 360px x 640px]
+----------------------------------------+
|  [N-03] Locale Switcher (ja / ko)      |
|                                        |
|  [N-01] Brand Logo / Icon              |
|  [N-01] Welcome: "Artist & Concert Hub" |
|  [N-01] Subtitle: "Sign in to follow"  |
|                                        |
|  [N-06] (Error Banner if OAuth fails)  |
|                                        |
|  [N-02] [ Google Sign-In Button ]      |
|  [N-03] [ Kakao Sign-In Button  ]      |
|                                        |
|  [N-04] By signing in, you agree to    |
|         Terms of Service & Privacy     |
+----------------------------------------+
```

```text
[Desktop Viewport - 1024px+]
+---------------------------------------------------+
|  [N-03] Locale Switcher (ja / ko)                 |
|                                                   |
|             +-----------------------+             |
|             | [N-01] Brand Logo     |             |
|             | [N-01] Welcome Title  |             |
|             | [N-01] Subtitle       |             |
|             |                       |             |
|             | [N-06] Error Banner   |             |
|             |                       |             |
|             | [N-02] Google Button  |             |
|             | [N-03] Kakao Button   |             |
|             |                       |             |
|             | [N-04] Legal Notice   |             |
|             +-----------------------+             |
+---------------------------------------------------+
```

**Regions**:
1. **Header**: Locale switcher (`SN-03`) -- top-right or top-bar -- priority 2.
2. **Hero / Authentication Card**: Brand branding (`N-01`), OAuth buttons (`N-02`, `N-03`), and error message (`N-06`) -- center viewport -- priority 1 (first viewport).
3. **Footer / Legal**: Terms of Service and Privacy Policy links (`N-04`) -- bottom of card -- priority 3.

## 5. Data & UI Mapping

| Field (from ia.md / spec) | Where shown (Need ID) | Format rule (e.g., date, d-day, duration, URL) | Truncation / overflow rule |
|---------------------------|-----------------------|-----------------------------------------------|----------------------------|
| Auth session status | N-05 | Redirect URL (`?next=` or default Home) | N/A |
| OAuth provider configs | N-02, N-03 | Button label & icon asset | N/A |
| Legal document links | N-04 | Hyperlinks to `/[locale]/legal/terms` & `/[locale]/legal/privacy` | N/A |

## 6. Interaction Specification

| Trigger (Need ID) | Immediate feedback | Result | Failure behaviour |
|-------------------|--------------------|--------|-------------------|
| Google Sign-In Click (`N-02`) | Button loading state (`N-07`), spinner | Opens Google OAuth popup/redirect | Display inline error callout (`N-06`), reset button state |
| Kakao Sign-In Click (`N-03`) | Button loading state (`N-07`), spinner | Opens Kakao OAuth redirect | Display inline error callout (`N-06`), reset button state |
| Locale Switcher (`SN-03`) | Immediate UI language toggle | Reloads route with updated locale segment (`ja` / `ko`) | Fallback to default locale (`ko`) |
| Legal Link Click (`N-04`) | Navigation transition | Navigates to `R-09` legal document | N/A |

## 7. State Matrix

| State | Trigger | What the user sees | Recovery action |
|-------|---------|--------------------|-----------------|
| Loading | Initial page load or session check | Full screen skeleton / spinner (`N-05`, `N-07`) | Auto-resolves upon session check |
| Empty (first use) | Normal state for unauthenticated visitor | Clean login card with Google & Kakao buttons | User clicks sign-in button |
| Empty (filtered to zero) | N/A | N/A | N/A |
| Error (recoverable) | OAuth popup closed, network failure, or API error | Inline critical callout (`N-06`) above sign-in buttons | User retries sign-in |
| Error (auth/session) | Invalid session token or expired redirect | Error banner + clean login buttons | User re-authenticates |
| Partial / stale | Slow network response during OAuth handshake | Button disabled with progress spinner (`N-07`) | Timeout / abort or retry |
| Success | Successful OAuth callback & session creation | Automatic redirect to `?next=` or Home (`R-02`) | N/A |

## 8. i18n (JP / KR)

- Text-expansion risks and layout tolerance: Japanese and Korean button labels and welcome copy are concise; flex containers must handle vertical stacking gracefully without text wrapping issues.
- Date / time / d-day formatting per locale: N/A for login screen.
- Copy keys:
  - `login.welcome` -> JP: "アーティスト＆公演イベントハブへようこそ" / KR: "아티스트 및 공연 이벤트 허브에 오신 것을 환영합니다"
  - `login.subtitle` -> JP: "ログインして、お気に入りのアーティストをフォローしましょう" / KR: "로그인하고 좋아하는 아티스트를 팔로우하세요"
  - `login.google` -> JP: "Googleでログイン" / KR: "Google로 로그인"
  - `login.kakao` -> JP: "Kakaoでログイン" / KR: "Kakao로 로그인"
  - `login.legalNotice` -> JP: "ログインすることで、利用規約およびプライバシーポリシーに同意したことになります。" / KR: "로그인함으로써 이용약관 및 개인정보처리방침에 동의하게 됩니다."

## 9. Accessibility (WCAG 2.2 AA)

- Keyboard order & focus management: Tab order flows from Locale Switcher -> Google Sign-In -> Kakao Sign-In -> Legal Links. Focus rings clearly visible on all interactive elements.
- Landmarks / headings: `<main>` landmark with `<h1>` heading for welcome title.
- Non-visual feedback for async changes: `aria-busy="true"` on buttons during OAuth redirect; screen reader announcements for sign-in failures via `role="alert"`.

## 10. IA Change Requests (leave empty if none)

- None.

## 11. Acceptance Checks

| Spec scenario | Verifiable on this screen by |
|---------------|------------------------------|
| US1: Unauthenticated visitor can sign in with Google or Kakao | Clicking Google or Kakao button triggers OAuth flow (`N-02`, `N-03`) |
| R-01: Authenticated user visiting login page is redirected to Home / `?next=` | Landing on `/[locale]/login` with active session redirects immediately (`N-05`) |
| FR-015: Legal documents reachable from login screen | Clicking terms or privacy links navigates to `R-09` (`N-04`) |
