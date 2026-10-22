# Information Architecture: [FEATURE]

**Branch**: `[###-feature-name]` | **Date**: [DATE] | **Spec**: [link] | **Status**: Draft | Approved
**Scope of this document**: WHAT routes exist, WHAT each owns, HOW they connect. Nothing about layout, components, or visual design (that is `ui/<route-id>.md`).

## 1. Object Inventory (from spec.md Key Entities)

<!-- OOUX-style: objects first, then relationships, then actions, then attributes. -->

| Object | Core attributes (from spec) | Relationships | Actions users perform (CTAs) |
|--------|-----------------------------|---------------|------------------------------|
| [e.g., Artist] | [...] | [e.g., has many Events] | [create, edit, delete, ...] |

## 2. User Tasks (from spec.md User Stories)

| Task ID | Task (verb + object) | Source (US-#, FR-#) | Frequency (daily/weekly/rare) | Entry point |
|---------|----------------------|---------------------|-------------------------------|-------------|
| T-01 | [e.g., Browse upcoming events] | US3, FR-004 | daily | [...] |

## 3. Route Map

<!-- One row per route. IDs are stable (R-01...) and are the argument to /speckit.ui. -->

| Route ID | Path (App Router) | Route group / layout | Purpose (one sentence) | Primary object | Tasks served | Auth | Data read / mutated | URL state (params / searchParams) | Rendering intent | Links in → out |
|----------|-------------------|----------------------|------------------------|----------------|--------------|------|---------------------|-----------------------------------|------------------|----------------|
| R-01 | `/[locale]/login` | `(auth)` | [...] | User | T-0x | public | [...] | `?next=` | static | [...] |

### 3.1 Layout & Segment Tree

```text
app/
└── [locale]/
    ├── (auth)/...
    └── (app)/...
```

### 3.2 Shared Concerns (cross-route)

- **Navigation model**: [primary nav items → route IDs]
- **Locale handling**: [segment strategy, switcher placement as a *requirement*, not a design]
- **Auth boundary**: [where protection is enforced, redirect targets]
- **Filter/sort state convention**: [which state lives in URL searchParams, naming of params]
- **Global states needing a route-level answer**: [empty account, expired session, backend failure]

## 4. Traceability Matrix

| Requirement | Covered by route(s) | Notes |
|-------------|--------------------|-------|
| FR-001 | R-0x | |

**Orphan check**: Requirements with no route: [none | list]. Routes with no requirement: [none | list].

## 5. Decision Log

| # | Decision | Alternatives considered | Why this one | Reversal cost |
|---|----------|-------------------------|--------------|---------------|
| D-1 | [...] | [...] | [...] | low/med/high |

## 6. Open Questions (max 5, each with a recommended default)

- [Q + recommended default + impact if wrong]

## 7. Route Status (maintained by /speckit.ui)

| Target | File | Status (pending / drafted) |
|--------|------|--------|
| shared | `ui/_shared.md` | pending |
| R-01 | `ui/R-01-login.md` | pending |
