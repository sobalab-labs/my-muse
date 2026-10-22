<!--
Sync Impact Report:
- Version change: 1.1.0 → 1.2.0
- Modified Sections: Spec-Kit Prompt Recording & Traceability (record format externalized to template; directory structure, timestamp, and enforcement rules added)
- Added Templates: .specify/templates/prompt-record-template.md
- Templates verified:
  - .specify/templates/plan-template.md (✅ aligned)
  - .specify/templates/spec-template.md (✅ aligned)
  - .specify/templates/tasks-template.md (✅ aligned)
  - .specify/templates/prompt-record-template.md (✅ new)
- Follow-up TODOs:
  - ⚠ Add the recording step to every command template, including the custom `ia` command
-->

# Band-Aid Frontend Constitution

## Core Principles

### I. Framework Best Practices & Modern Conventions
When working with any framework (e.g., React, Next.js, Angular, Vue), developers MUST follow the framework's most recent versions' official, standard best practices and coding conventions unless a specific framework version is explicitly stated in the project configuration. Outdated antipatterns, deprecated APIs, or custom workarounds must be avoided in favor of idiomatic, standard-compliant patterns recommended by the framework maintainers.

### II. Component-Driven & Modular Architecture
All UI elements and business logic must be structured around a modular, component-driven architecture. Components must be self-contained, highly cohesive, loosely coupled, and independently testable. Clear separation of concerns between presentation, state management, and data fetching must be maintained at all times.

### III. Type Safety & Strict Code Quality
Strong typing and static analysis are mandatory. Code must compile/type-check cleanly with zero suppressions or type bypasses (e.g., `any` casts, `@ts-ignore`) unless strictly unavoidable and formally documented. Linters and formatting rules must be strictly adhered to.

### IV. Test-First & Automated Validation
Test-driven development (TDD) or rigorous automated test coverage is required for all features and bug fixes. Tests must verify behavioral correctness, edge cases, and component rendering. No change is considered complete without passing automated test suites, linting, and type-checking.

### V. Performance, Accessibility & Simplicity
Deliverables must prioritize high performance (optimized bundle sizes, fast render times), robust accessibility (WCAG compliance), and radical simplicity. Avoid premature optimization and unnecessary abstractions (YAGNI).

## Technical Standards & Stack

- **Language / Framework**: TypeScript, React / Next.js (latest stable versions unless specified otherwise)
- **Styling & Assets**: Consistent design tokens, modular CSS or approved styling solution
- **Quality Assurance**: Automated testing (Vitest/Jest/React Testing Library), strict TypeScript checking, and linting (`eslint`, `prettier`)

## Spec-Kit Prompt Recording & Traceability

Every `/speckit.*` invocation, **including custom commands** (e.g., `/speckit.ia`), MUST be recorded as its **first step**, before any other work. A command with no record is considered not to have run.

- **Template**: Fill in `.specify/templates/prompt-record-template.md`. It defines the record's format, fields, and file-naming rules.
- **Location**: `specs/<feature>/prompts/<command>/<YYYYMMDDTHHMMSS±HHMM>.md`, grouped by command name without the `speckit.` prefix. Custom commands get their own folder.
- **Timestamp**: Taken from the system clock in the user's local timezone, never estimated.
- **Integrity**: One file per invocation. Records are never overwritten, and user input is kept verbatim. Everything else stays brief.
- **Enforcement**: Every command template, built-in or custom, MUST contain the recording step. If one does not, this section still applies and the agent MUST record anyway. Adding a custom command requires adding the step to its template as part of the amendment checklist.
- **Commit**: Records are committed together with the artifacts they produced.

## Development Workflow & Quality Gates

1. **Research & Planning**: Define technical scope, record planning prompts under `specs/<feature>/prompts/`, and verify framework standards before implementation.
2. **Implementation**: Build modular components following official documentation and best practices.
3. **Validation Gates**: Every pull request and change must pass type checking, linting, and test suites prior to merge.
4. **Documentation**: Keep architectural decisions, prompt logs, and component APIs documented.

## Governance

- The Constitution supersedes all other informal practices or ad-hoc conventions.
- Amendments require documented rationale, semantic version increments, and verification across dependent templates.
- All code reviews and contributions must explicitly verify compliance with these constitutional principles.
- Use project guidance documents (`README.md` or scoped guides) for runtime development workflows.

**Version**: 1.2.0 | **Ratified**: 2026-10-10 | **Last Amended**: 2026-10-15