# Research & Technical Decisions: Artist & Concert Event Management Hub

**Branch**: `001-artist-event-management` | **Date**: 2026-10-21

## 1. Next.js 16 App Router Internationalized Routing & Locale Persistence

- **Decision**: Use `[locale]` dynamic route segment (`ja`, `ko`) combined with cookie persistence and server-side layout resolution.
- **Rationale**: Ensures SEO-friendly, shareable localized URLs while allowing fast client-side locale switching via cookie update and router refresh.
- **Alternatives Considered**:
  - Subdomain routing (`ja.band-aid.com`, `ko.band-aid.com`): Rejected due to infrastructure overhead for a single application frontend.
  - Client-only state localization: Rejected because it breaks server-side rendering (SSR) and initial HTML localization.

## 2. SEED Design Component Integration with Next.js 19 & App Router

- **Decision**: Use `@seed-design/react` v3 and `@seed-design/css` with explicit `'use client'` boundaries for interactive widgets (tabs, dialogs, snackbars) while keeping server components for data-fetching pages.
- **Rationale**: Maximizes performance of Server Components while leveraging SEED Design's accessible, robust React components for UI primitives.
- **Alternatives Considered**:
  - TailwindCSS-only custom components: Rejected because SEED Design is the mandated design system (`ui/_shared.md`).
  - Shadcn/UI: Reserved as a fallback pool only when no SEED component fits.

## 3. OAuth Authentication & Public-First Route Access Model

- **Decision**: Implement public-first access where all core routes (`R-02` Home, `R-03` Search, `R-04` Artist Detail, `R-05` Event Detail) are accessible to both unauthenticated and authenticated users. Auth-critical actions (following artists, configuring notifications, viewing My Artists) trigger an in-page sign-in prompt directing to OAuth login (`R-01`) with `?next=` redirection.
- **Rationale**: Lowers friction for new visitors exploring trending events and artists, while protecting user-specific data and mutation actions.
- **Alternatives Considered**:
  - Hard redirection walls on protected routes: Rejected because it creates a poor user experience when sharing links or browsing publicly.

## 4. Cursor-Based Infinite Scroll & Scroll Position Restoration

- **Decision**: Implement cursor-based pagination for Home feed, Search results, My Artists, and Onboarding suggestions, utilizing React state and sessionStorage to restore scroll position upon returning from detail views.
- **Rationale**: Matches native mobile app behavior, maintains high performance with large datasets, and avoids URL bloat from page/cursor parameters.
- **Alternatives Considered**:
  - Page-based pagination (`?page=`): Rejected because infinite scroll provides superior UX for feed and discovery interfaces.

## 5. Event Calendar View Implementation

- **Decision**: Implement the month calendar view (`?view=calendar`) as an alternative display mode on Home (`R-02`) using URL search params (`?view=calendar&month=YYYY-MM`).
- **Rationale**: Reuses the existing feed data fetching and filtering logic while offering users a calendar-centric overview.
- **Alternatives Considered**:
  - Dedicated calendar route: Rejected per IA Decision D-11 to keep the feed on a single route.
