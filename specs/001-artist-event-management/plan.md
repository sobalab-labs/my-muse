# Implementation Plan: Artist & Concert Event Management Hub

**Branch**: `001-artist-event-management` | **Date**: 2026-10-21 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-artist-event-management/spec.md`, Information Architecture from `/specs/001-artist-event-management/ia.md`, and UI Foundation from `/specs/001-artist-event-management/ui/_shared.md`.

## Summary

Build a localized web application (supporting Japanese and Korean) for managing favorite artists, searching artists and events, viewing future event feeds (Following vs Trending scopes) with filters and sorting, configuring multi-channel notification subscriptions (priority 1 > 2 > 3 for followed artists, plus general weekly updates), authenticating via Google/Kakao OAuth, and managing user account lifecycle (onboarding, settings, sign-out, account deletion). Designed using SEED Design (`@seed-design/react`) and Next.js 16 App Router.

## Technical Context

**Language/Version**: TypeScript 5 / Node.js runtime (Next.js 16)

**Primary Dependencies**: Next.js 16 (App Router), React 19, `@seed-design/react` v3, `@seed-design/css`, TailwindCSS v4

**Storage**: Client state (React hooks / context), browser cookies/storage for locale/session state, backed by remote REST/GraphQL API service

**Testing**: Jest / React Testing Library for component testing, Playwright for end-to-end user flows

**Target Platform**: Modern desktop and mobile web browsers (responsive layout: <768px mobile, >=768px desktop)

**Project Type**: Web application frontend (`frontend`)

**Performance Goals**: <200ms perceived latency for filters/sorts and infinite scroll pagination; <5s initial OAuth sign-in and redirect

**Constraints**: Strict adherence to SEED Design system components (`@seed-design/react`), localization for Japanese (`ja`) and Korean (`ko`) with dedicated format rules, public-first access model with uniform in-page auth prompting for auth-critical actions, and zero backend mutations for artist master data.

**Scale/Scope**: 10 route segments (`/[locale]/login`, `/[locale]/(app)/home`, `/[locale]/(app)/search`, `/[locale]/(app)/artists/[id]`, `/[locale]/(app)/events/[id]`, `/[locale]/(app)/events/bookmarked`, `/[locale]/(app)/artists/followed`, `/[locale]/(app)/settings`, `/[locale]/(app)/onboarding`, `/[locale]/(app)/legal/[doc]`) with i18n routing, cursor-based infinite scroll, scroll position restoration, and event calendar view.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] **Tech stack compliance**: Uses Next.js 16 App Router, TypeScript, and SEED Design (`@seed-design/react`) as specified.
- [x] **Architecture alignment**: Adheres to Information Architecture (`ia.md`) route map and shared UI design principles (`ui/_shared.md`).
- [x] **Localization support**: Enforces Japanese (`ja`) and Korean (`ko`) localization across all routes and views.
- [x] **Security & Auth standards**: Implements third-party OAuth (Google, Kakao) with public-first route access and uniform in-page sign-in prompting for auth-restricted actions.

## Project Structure

### Documentation (this feature)

```text
specs/001-artist-event-management/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output
│   ├── api-client.ts    # API client contract & types
│   └── routes.md        # Route & navigation contract
└── tasks.md             # Phase 2 output (created later via /speckit.tasks)
```

### Source Code (repository root)

```text
app/
├── [locale]/
│   ├── not-found.tsx
│   ├── login/
│   │   └── page.tsx
│   └── (app)/
│       ├── layout.tsx
│       ├── home/
│       │   └── page.tsx
│       ├── search/
│       │   └── page.tsx
│       ├── artists/
│       │   ├── [id]/
│       │   │   └── page.tsx
│       │   └── followed/
│       │       └── page.tsx
│       ├── events/
│       │   ├── [id]/
│       │   │   └── page.tsx
│       │   └── bookmarked/
│       │       └── page.tsx
│       ├── onboarding/
│       │   └── page.tsx
│       ├── legal/
│       │   └── [doc]/
│       │       └── page.tsx
│       └── settings/
│           └── page.tsx
```
components/
├── shared/              # SEED Design wrapper components (Navigation, Header, SnackbarProvider, etc.)
└── ...
services/                # API client & data fetchers
hooks/                   # Custom hooks (infinite scroll, locale, auth state)
types/                   # TypeScript interfaces for User, Artist, Event, NotificationSubscription
```

**Structure Decision**: Option 2 (Web application - Next.js App Router structure under `app/[locale]/` with shared components, services, and types).
