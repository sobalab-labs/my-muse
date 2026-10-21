# Route & Navigation Contract

**Branch**: `001-artist-event-management`

## 1. Route Table

| Route ID | Path Pattern | Access Model | Primary Purpose |
|----------|--------------|--------------|-----------------|
| R-01 | `/[locale]/login` | Public (redirects to Home if authenticated) | Third-party OAuth authentication (Google, Kakao) |
| R-02 | `/[locale]/(app)/home` | Public | Future events feed (Following / Trending scopes, list/calendar view, filters & sorting) |
| R-03 | `/[locale]/(app)/search` | Public | Integrated artist & event keyword search |
| R-04 | `/[locale]/(app)/artists/[id]` | Public | Artist profile metadata and associated upcoming events |
| R-05 | `/[locale]/(app)/events/[id]` | Public | Event detail view with calendar export, sharing, and ticketing link |
| R-06 | `/[locale]/(app)/artists/followed` | Public (in-page sign-in prompt for auth-only content) | My Artists hub: manage favorite followed artists |
| R-07 | `/[locale]/(app)/settings` | Public (in-page sign-in prompt for notification/account sections) | Language preference, notification subscriptions, sign-out, account deletion |
| R-08 | `/[locale]/(app)/onboarding` | Public (in-page sign-in prompt for first-time setup) | First-time artist selection onboarding flow |
| R-09 | `/[locale]/(app)/legal/[doc]` | Public | Terms of Service & Privacy Policy documents |

## 2. Navigation & Redirect Rules

- **Default Locale**: Unlocalized root paths redirect to the default locale (`ja` or `ko`) or user preference cookie.
- **Auth Guard**: All `(app)` routes are publicly accessible. Auth-critical actions (following artists, configuring notifications, viewing My Artists) display an in-page sign-in prompt leading to `R-01` with `?next=` parameter.
- **Following Scope Guard**: Unauthenticated access to `?scope=following` on Home (`R-02`) is canonicalized server-side to `?scope=trending`, and clicking Following prompts sign-in.
- **Post-Login Routing**: Successful OAuth sign-in routes first-time users to Onboarding (`R-08`) or returning users to `?next=` / Home (`R-02`).
