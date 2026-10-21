# Shared UI Foundation & Design System Guidelines

## 1. Design system instruction

Design system: SEED Design (Daangn), with shadcn/ui as the fallback.

Primary pool: MCP server `seed-docs` (`@seed-design/react`, `seed-design/ui/*`).
- List tool: `list_docs` with `section: "react"` and `category: "components"`.
- Detail tool: `get_doc` with `section: "react"` and `path` (e.g. `components/snackbar`).

Fallback pool: shadcn/ui, via skill `shadcn`.
- Use it only for a need that has no fit in the primary pool, never because a shadcn component seems nicer.

If a need fits neither pool, write `No fit in pool` and propose a plain layout container or a plain description of the missing behaviour. Do not invent components.

## 2. Shared needs

| Need ID | Description | Source |
|---------|-------------|--------|
| SN-01 | App shell / root layout container for responsive mobile and desktop viewports | ia.md §3.1, §3.2 |
| SN-02 | Persistent navigation bar providing access to Home, Search, My Artists, and Settings | ia.md §3.2 |
| SN-03 | Locale switcher allowing users to toggle between Japanese (`ja`) and Korean (`ko`) | ia.md §3.2, FR-002 |
| SN-04 | Global feedback / toast notification for success, error, and informational messages | ia.md §3.2 |
| SN-05 | Inline error banner for backend API failures and session expiration notices | ia.md §3.2 |
| SN-06 | Confirmation modal dialog for destructive actions such as account deletion and sign-out | ia.md §3.2, FR-014 |
| SN-07 | List/feed container supporting cursor-based infinite scroll and scroll position restoration | ia.md §3.2, FR-012 |
| SN-08 | Filter and sort controls for feeds (artist, region, d-day chronological sort) | ia.md §3.2, FR-006, FR-007 |
| SN-09 | Form input / search query control with clear action | ia.md §3.2, FR-003, FR-004 |
| SN-10 | Empty state pattern (zero favorite artists, zero search results, empty onboarding suggestions) | ia.md §3.2 |
| SN-11 | Loading skeleton/spinner pattern for async data loading | ia.md §3.2 |

## 3. Component choices

| Need ID | Component | Source | Props/variants | Reason |
|---------|-----------|--------|----------------|--------|
| SN-01 | `Box` / `Flex` / `VStack` / `HStack` | `@seed-design/react` | full-width / responsive layout primitives | Core SEED layout primitives for structure and spacing. |
| SN-02 | `Tabs` / `Chip Tabs` / Custom Navigation Bar | `seed-design/ui/tabs`, `seed-design/ui/chip-tabs` | bottom-nav (mobile) / top-nav (desktop) | SEED design tab/navigation primitives or structured Flex layout. |
| SN-03 | `Select` / `SegmentedControl` | `seed-design/ui/select`, `seed-design/ui/segmented-control` | locale-switcher variant (`ja` / `ko`) | Native SEED selector for language/locale toggle. |
| SN-04 | `Snackbar` + `SnackbarProvider` | `seed-design/ui/snackbar` | `variant="positive"`, `variant="critical"`, `strategy="queued"` | SEED transient feedback component with provider adapter. |
| SN-05 | `Callout` / `DismissibleCallout` | `seed-design/ui/callout` | `tone="critical"`, `tone="warning"`, `tone="informative"` | SEED inline messaging component for API failures and alerts. |
| SN-06 | `Dialog` / `AlertDialog` | `seed-design/ui/dialog`, `seed-design/ui/alert-dialog` | confirmation modal variant | SEED modal dialog for critical and destructive actions. |
| SN-07 | `List` | `seed-design/ui/list` | cursor-paginated feed container | SEED list primitive for structured feed representation. |
| SN-08 | `Chip` / `ChipTabs` / `Select` | `seed-design/ui/chip`, `seed-design/ui/chip-tabs`, `seed-design/ui/select` | artist/region/sort filter options | SEED filter and categorization chips/selectors. |
| SN-09 | `TextFieldInput` | `seed-design/ui/text-field-input` | keyword input with clear action | SEED text field input for search queries. |
| SN-10 | `ContentPlaceholder` | `seed-design/ui/content-placeholder` | illustration + call-to-action button | SEED empty state placeholder for zero results / onboarding. |
| SN-11 | `Skeleton` | `seed-design/ui/skeleton` | list / card skeleton loader | SEED skeleton loader to prevent layout shifts during async loads. |

## 4. Composition

### Mobile Layout
- **Header**: Sticky top header containing branding and locale switcher (`SN-03`).
- **Main Viewport (`SN-01`)**: Content area displaying active route views (Home, Search, My Artists, Settings, etc.).
- **Bottom Navigation Bar (`SN-02`)**: Fixed bottom bar with 4 primary destination tabs (Home `R-02`, Search `R-03`, My Artists `R-06`, Settings `R-07`).
- **Overlay**: Snackbar notifications (`SN-04`) and confirmation modals (`SN-06`) rendered via portal (`SnackbarProvider` / `Dialog`).

### Desktop Layout
- **Header / Navigation (`SN-02`)**: Persistent top navigation bar or sidebar with destination links, locale switcher (`SN-03`), and user authentication/profile state.
- **Main Viewport (`SN-01`)**: Centered max-width container (max 1200px) holding page content.
- **Overlay**: Snackbar notifications (`SN-04`) and confirmation modals (`SN-06`).

## 5. Conventions

- **Breakpoints**: Mobile-first design; Mobile (< 768px), Desktop (>= 768px).
- **Spacing/Density**: 8pt grid system (spacing increments of 4, 8, 12, 16, 24, 32, 48px). Minimum touch target size of 44x44px for all interactive elements.
- **List/Filter/Form/Empty/Loading/Error behaviour**:
  - Infinite scroll triggers automatically when scrolling within 80% of list bottom.
  - Filter and sort changes update URL `searchParams` immediately with shallow routing.
  - Loading states display skeleton loaders (`SN-11`) to prevent layout shifts.
  - Errors display inline callout alert banners (`SN-05`) with retry actions.
  - Empty states (`SN-10`) provide clear descriptive text and guidance/CTA via `ContentPlaceholder`.

## 6. JP/KO Formatting Rules

- **Japanese (`ja`)**:
  - Date formatting: `YYYY年M月D日 (W)` (e.g., `2026年10月15日 (木)`)
  - Time formatting: `HH:mm` (24-hour)
  - Honorifics & Naming: Respect standard artist/event naming and natural particle spacing.
- **Korean (`ko`)**:
  - Date formatting: `YYYY년 M월 D일 (W)` (e.g., `2026년 10월 15일 (목)`)
  - Time formatting: `HH:mm` (24-hour)
  - Postpositions & Particle handling: Ensure correct spacing and natural localization phrasing.
