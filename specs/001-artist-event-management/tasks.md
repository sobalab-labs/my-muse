# Tasks: Artist & Concert Event Management Hub

**Feature Branch**: `001-artist-event-management` | **Spec**: [spec.md](./spec.md) | **Plan**: [plan.md](./plan.md) | **IA**: [ia.md](./ia.md) | **UI Foundation**: [ui/_shared.md](./ui/_shared.md) | **UI Specs**: [R-01 Login](./ui/R-01-login.md), [R-02 Home](./ui/R-02-home.md), [R-03 Search](./ui/R-03-search.md), [R-04 Artist Detail](./ui/R-04-artist-detail.md), [R-05 Event Detail](./ui/R-05-event-detail.md), [R-06 My Artists](./ui/R-06-my-artists.md), [R-07 Settings](./ui/R-07-settings.md), [R-08 Onboarding](./ui/R-08-onboarding.md), [R-09 Legal](./ui/R-09-legal.md)

## Execution Phases

- **Phase 1**: Setup (Project structure & TypeScript contracts)
- **Phase 2**: Foundational (Layout shell, navigation, i18n provider, error/not-found handling)
- **Phase 3**: User Story 1 - Third-Party Authentication & Localization (P1)
- **Phase 4**: User Story 2 - Artist Search & Following Management (P2)
- **Phase 5**: User Story 3 - Event Search (P3)
- **Phase 6**: User Story 4 - Future Events Feed & Filtering/Sorting (P3)
- **Phase 7**: User Story 5 & 6 - Notification Subscriptions (P4 & P5)
- **Phase 8**: Polish & Cross-Cutting Concerns (Legal pages & final validation)

---

## Phase 1: Setup

- [ ] T001 Create directory structure for app router locale segments (`app/[locale]/(app)/home`, `app/[locale]/(app)/search`, `app/[locale]/(app)/artists/[id]`, `app/[locale]/(app)/artists/followed`, `app/[locale]/(app)/events/[id]`, `app/[locale]/(app)/settings`, `app/[locale]/(app)/onboarding`, `app/[locale]/(app)/legal/[doc]`, `app/[locale]/login`)
- [ ] T002 [P] Create TypeScript types and API client contract in `types/index.ts` and `services/api.ts` based on `contracts/api-client.ts`
- [ ] T003 [P] Configure SEED Design provider and global theme styles in `app/globals.css` and root layout wrapper

## Phase 2: Foundational

- [ ] T004 Implement internationalized routing wrapper and locale persistence cookie handler in `app/[locale]/layout.tsx`
- [ ] T005 [P] Implement localized Not Found page component in `app/[locale]/not-found.tsx`
- [ ] T006 Implement persistent application navigation shell and header with language switcher in `components/shared/AppShell.tsx` and `components/shared/Header.tsx`
- [ ] T007 [P] Implement auth context provider and session state management hook in `hooks/useAuth.ts`
- [ ] T008 [P] Implement infinite scroll custom hook with scroll position restoration in `hooks/useInfiniteScroll.ts`

---

## Phase 3: User Story 1 - Third-Party Authentication & Localization (P1)

- [ ] T009 [US1] Implement third-party OAuth login page (Google, Kakao) with redirect handling in `app/[locale]/login/page.tsx`
- [ ] T010 [P] [US1] Implement global settings page with language switcher (Japanese/Korean) in `app/[locale]/(app)/settings/page.tsx`
- [ ] T011 [US1] Implement first-time onboarding artist-picking flow (skippable) in `app/[locale]/(app)/onboarding/page.tsx`
- [ ] T012 [US1] Implement sign-out and permanent account deletion actions in `app/[locale]/(app)/settings/page.tsx`

---

## Phase 4: User Story 2 - Artist Search & Following Management (P2)

- [ ] T013 [US2] Implement integrated artist search component with keyword input and infinite scroll in `app/[locale]/(app)/search/page.tsx`
- [ ] T014 [P] [US2] Implement artist detail view with metadata (genre, nation, SNS URLs) and follow/unfollow actions in `app/[locale]/(app)/artists/[id]/page.tsx`
- [ ] T015 [US2] Implement "My Artists" followed artists management hub with quick unfollow actions in `app/[locale]/(app)/artists/followed/page.tsx`
- [ ] T016 [US2] Implement in-page OAuth sign-in prompting for unauthenticated users attempting artist follow/unfollow actions

---

## Phase 5: User Story 3 - Event Search (P3)

- [ ] T017 [US3] Implement integrated event search component with keyword matching for event name or participating artist name in `app/[locale]/(app)/search/page.tsx`
- [ ] T018 [P] [US3] Implement event detail view with associated artists, date, place, duration, ticketing URL, and official announcement URL in `app/[locale]/(app)/events/[id]/page.tsx`
- [ ] T019 [US3] Implement event actions for add to calendar (ICS export), share link, and opening ticketing URL in `app/[locale]/(app)/events/[id]/page.tsx`

---

## Phase 6: User Story 4 - Future Events Feed & Filtering/Sorting (P3)

- [ ] T020 [US4] Implement Home future events feed with explicit scopes (`?scope=following` for authenticated users, `?scope=trending` for all) and followed artists quick-access strip in `app/[locale]/(app)/home/page.tsx`
- [ ] T021 [P] [US4] Implement industry-standard filtering controls (by specific artist, by region: Japan/Korea) in `app/[locale]/(app)/home/page.tsx`
- [ ] T022 [P] [US4] Implement sorting controls (by d-day / chronological proximity) in `app/[locale]/(app)/home/page.tsx`
- [ ] T023 [US4] Implement alternative month calendar view mode (`?view=calendar`) for events feed in `app/[locale]/(app)/home/page.tsx`
- [ ] T024 [US4] Implement server-side canonicalization of unauthenticated `?scope=following` to trending and in-page sign-in prompting for Following scope selection in `app/[locale]/(app)/home/page.tsx`

---

## Phase 7: User Story 5 & 6 - Notification Subscriptions (P4 & P5)

- [ ] T025 [US5] Implement artist-specific notification subscription management UI in Settings with priority order (1. new events, 2. ticket open bundle day-before/30min/at-open, 3. discography updates) constrained to followed artists in `app/[locale]/(app)/settings/page.tsx`
- [ ] T026 [US6] Implement general weekly event update notification subscription toggles (email and Kakao message) in `app/[locale]/(app)/settings/page.tsx`

---

## Phase 8: Polish & Cross-Cutting Concerns

- [ ] T027 Implement static legal documents page (Terms of Service and Privacy Policy) in `app/[locale]/(app)/legal/[doc]/page.tsx`
- [ ] T028 [P] Verify responsive layout across mobile (<768px) and desktop (>=768px) breakpoints using SEED Design primitives
- [ ] T029 [P] Perform accessibility and keyboard navigation audit across all interactive flows
- [ ] T030 Validate end-to-end user flows and localization rendering for Japanese (`ja`) and Korean (`ko`)

---

## Dependency Graph & Execution Order

```
Phase 1 (Setup) ──> Phase 2 (Foundational)
                        │
                        ├──> Phase 3 (US1: Auth & Localization) ──> Phase 4 (US2: Artist Search & Following)
                        │                                                   │
                        │                                                   v
                        │                                          Phase 5 (US3: Event Search)
                        │                                                   │
                        │                                                   v
                        └──> Phase 6 (US4: Events Feed & Filtering) <──────┘
                                    │
                                    v
                           Phase 7 (US5 & US6: Notifications)
                                    │
                                    v
                           Phase 8 (Polish & Validation)
```

## Parallel Execution Examples

- **Phase 1**: T002 and T003 can run in parallel after T001.
- **Phase 2**: T005, T007, and T008 can run in parallel after T004 and T006.
- **Phase 3**: T010 can run in parallel with T009.
- **Phase 4**: T014 can run in parallel with T013.
- **Phase 5**: T018 can run in parallel with T017.
- **Phase 6**: T021 and T022 can run in parallel after T020.
- **Phase 8**: T028 and T029 can run in parallel after T027.

## Suggested MVP Scope

- **MVP Scope**: Phase 1, Phase 2, Phase 3 (US1), Phase 4 (US2), and Phase 6 (US4 - Core Feed). This delivers OAuth login, language settings, artist discovery & following, and the core future events feed.
