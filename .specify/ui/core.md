# UI spec procedure (shared core)

This file is the full procedure for the UI spec commands. The invoking command supplies the user input and a DESIGN SYSTEM BLOCK.

Grammar of the user input: `shared [notes]` or `<Route ID> [notes]` (e.g., `R-03`). If the first token is neither, list the valid targets from ia.md §7 and stop.

The DESIGN SYSTEM BLOCK is whatever the invoking command defines: systems, MCP servers, tool names, fallback pools, precedence, styling wishes. This procedure does not interpret or restructure it; it follows it.

## Pre-Execution Checks

**Check for extension hooks (before UI specification)**:
- Check if `.specify/extensions.yml` exists in the project root.
- If it exists, read it and look for entries under the `hooks.before_ui` key
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue normally
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Optional hook** (`optional: true`):
```
    ## Extension Hooks

    **Optional Pre-Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
```
  - **Mandatory hook** (`optional: false`):
```
    ## Extension Hooks

    **Automatic Pre-Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}

    Wait for the result of the hook command before proceeding to the Outline.
```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
- If no hooks are registered or `.specify/extensions.yml` does not exist, skip silently

## Role

You are a product/interaction designer writing build-ready screen specs. Your design-system job is narrow: for each UI need, choose the best component from the pool the DESIGN SYSTEM BLOCK points to, and only then compose the layout from those components.

## Outline

1. **Setup**: Run `.specify/scripts/bash/check-prerequisites.sh --json --paths-only` from repo root and parse its JSON for FEATURE_DIR (paths-only mode, so it does not require plan.md). If the script fails or does not support that flag, use `specs/<current branch name>/` (or the single `specs/*` directory containing spec.md) and say which you used. Require `FEATURE_DIR/ia.md` with `Status: Approved`; otherwise stop and tell the user to run `/speckit.ia` and approve it.

2. **Load context**: Load ONLY what the target section below lists. Never load other routes' ui files or plan.md.

3. **Work in this order, never reversed**: (a) UI needs, (b) component choices, (c) composition, (d) everything else.

4. **Check and save**: After writing, run the target's checklist as a separate step, fix defects, save.

5. **Update status**: Set the target's row in ia.md §7 to `drafted`. This is the ONLY edit allowed in ia.md.

## Step a: UI needs

State each need as what the user must see, do, input, or be told, never as a component. Derive them only from the sources the target loads (spec stories, FRs, edge cases, IA row data, ia.md §3.2). Give each a Need ID and its source. Edge cases (zero items, zero filter results, failed requests) are needs of type `feedback`.

## Step b: Selecting components

1. **Rules = the DESIGN SYSTEM BLOCK, as written.** For the `shared` target this is the block supplied by the invoking command; for a Route ID target it is `ui/_shared.md` §1. Follow any precedence, scope limit or styling statement in it. Do not reinterpret, extend, or fill gaps with your own design-system knowledge.
2. **The primary pool is the list result.** At this step, not earlier, call the list tool the block names once, and treat what it returns as the entire primary pool. Do not name components or props from memory. For a Route ID target, first reuse `ui/_shared.md` §3 choices for the needs they already cover.
3. **Decide the set from the list.** With all needs from step a in view, choose for each need the component that clearly covers it, judging from the list entries. Where the fit is clear, decide without fetching details. Choose the set as a whole: avoid two components doing the same job, and prefer one component serving several needs.
4. **Fetch details only for ambiguity.** If a need has no clear fit, several list entries could fit, or a list entry is too thin to judge, name the 1-3 candidates, then call the detail tool the block names for those candidates only, and choose from the result. Never call the detail tool for needs already decided in item 3.
5. **Fallback pool.** If a need has no fit in the primary pool and the block defines a fallback pool, look there only for that need, using the source the block names, with the same rules (decide from the index when clear; fetch details only for 1-3 ambiguous candidates). Never consult the fallback pool for a need the primary pool covers. If the block defines no fallback pool, skip this item.
6. If nothing fits in the primary pool or the fallback pool, write `No fit in pool`, say what is missing, and apply the block's fallback rule if it gives one; otherwise state a plain proposal. Do not invent components. If the primary list tool is unreachable, say so and write generic roles (for example "dialog") marked `UNVERIFIED` instead of component names.
7. Use components as provided. No restyling, token mapping, or wrappers in the spec; those belong to the plan phase.
8. Record choices as a table with columns `Need ID | Component | Source | Props/variants | Reason`. Source is `list` (primary list result only), `list+get` (primary details were fetched), `fallback:<name>` as the block names it (add `+get` if details were fetched), or `UNVERIFIED`. Props/variants is `not fetched` for `list` rows. For `list+get` rows, Reason states the ambiguity and why the chosen candidate won. For fallback rows, Reason states why the primary pool has no fit.

## Step c: Composition

Arrange only the chosen components, plus plain layout containers, into regions: mobile first, then desktop. Label every block with its Need ID. Decide priority order (primary action in the first viewport). If composition reveals a missing need or a poor choice, go back to step a or b and fix it there; do not patch it in the layout.

## Target: shared

Load: `ia.md` §3 (route names and §3.2), the DESIGN SYSTEM BLOCK supplied by the invoking command, existing `ui/_shared.md` if any.

1. Write `FEATURE_DIR/ui/_shared.md`:
   - **§1 Design system instruction**: paste the DESIGN SYSTEM BLOCK VERBATIM, including links, MCP references and tool names, followed by any extra notes the user wrote. If the block is `none`, write `none`; component choices then stay generic roles.
   - **§2 Shared needs**: app shell, navigation, locale switcher, global feedback (toast, inline error, confirmation), list/filter/form/empty/loading/error patterns that several routes need (from ia.md §3.2).
   - **§3 Component choices** for those needs, per step b.
   - **§4 Composition**: app shell and navigation, mobile + desktop, built from the §3 choices.
   - **§5 Conventions**: breakpoints, spacing/density rules stated in plain words, list/filter/form/empty/loading/error behaviour. **§6** JP/KR formatting rules.
2. If the block changed from the existing file's §1, tell the user which route specs may need a component-choice refresh; do not rewrite them.
3. **Checklist**:
   - [ ] §1 is verbatim.
   - [ ] Every shared need has a chosen component or `No fit in pool`.
   - [ ] §4 uses only §3 components.
   - [ ] No component name lacks a source or an UNVERIFIED mark.
   - [ ] Every `list+get` row states its ambiguity; no details were fetched for clear fits.
   - [ ] Every fallback row states why the primary pool has no fit.
   - [ ] ia.md §3.2 is covered.

## Target: Route ID

Load: `ia.md` §3.2, the route's row in §3 and its §4 rows, the spec stories/FRs/edge cases that row cites, `ui/_shared.md` (required), `.specify/templates/ui-template.md`. Notes after the Route ID may add detail for this screen but cannot override `_shared.md` §1. If the invoking command's DESIGN SYSTEM BLOCK differs from `_shared.md` §1, follow §1 and tell the user.

1. Fill `ui-template.md` into `FEATURE_DIR/ui/<route-id>-<slug>.md`, in template order.
   - Frozen inputs (path, purpose, data, URL state, auth) are copied from ia.md, never altered.
   - §2 needs, then §3 choices (reuse `_shared.md` §3 choices before choosing again), then §4 composition.
   - Every spec acceptance scenario and edge case cited by the IA row appears as a need and in §7 or §11.
   - IA problems go to §10; do not fix ia.md.
2. **Checklist**:
   - [ ] Every need traces to a spec story, FR, edge case or IA data item; no need is phrased as a component.
   - [ ] Every need has a chosen component or `No fit in pool`; every name has a source or UNVERIFIED.
   - [ ] Every `list+get` row states its ambiguity; no details were fetched for clear fits.
   - [ ] Every fallback row states why the primary pool has no fit.
   - [ ] §4 uses only §3 components plus plain containers; every block carries a Need ID.
   - [ ] Mobile layout shows the primary action in the first viewport.
   - [ ] State matrix has no blank cell; "n/a" needs a reason.
   - [ ] Every interactive element has a failure behaviour.
   - [ ] Every user-visible string has a copy key; JP/KR risks named.
   - [ ] Keyboard path for every pointer action; async changes announced non-visually.
   - [ ] No new route, path, or URL param.
   - [ ] Every cited FR/US/edge case maps to a state or acceptance check.
3. Report: file written, §10 items, `No fit in pool` / UNVERIFIED items, which needs used fallback or required detail lookups, next suggested target.

## When all targets are drafted

Print this for the user to paste when running the plan step (the plan command itself is not modified):

`/speckit.plan Read ia.md and ui/ in the feature directory first. Treat them as frozen: use the route tree from ia.md §3.1, the design system instruction in ui/_shared.md §1, and the component choices in ui/. Record any conflict in Complexity Tracking instead of changing them.`

## Mandatory Post-Execution Hooks

**You MUST complete this section before reporting completion to the user.**

Check if `.specify/extensions.yml` exists in the project root.
- If it does not exist, or no hooks are registered under `hooks.after_ui`, skip to the Completion Report.
- If it exists, read it and look for entries under the `hooks.after_ui` key.
- If the YAML cannot be parsed or is invalid, skip hook checking silently and continue to the Completion Report.
- Filter out hooks where `enabled` is explicitly `false`. Treat hooks without an `enabled` field as enabled by default.
- For each remaining hook, do **not** attempt to interpret or evaluate hook `condition` expressions:
  - If the hook has no `condition` field, or it is null/empty, treat the hook as executable
  - If the hook defines a non-empty `condition`, skip the hook and leave condition evaluation to the HookExecutor implementation
- For each executable hook, output the following based on its `optional` flag:
  - **Mandatory hook** (`optional: false`) — **You MUST emit `EXECUTE_COMMAND:` for each mandatory hook**:
```
    ## Extension Hooks

    **Automatic Hook**: {extension}
    Executing: `/{command}`
    EXECUTE_COMMAND: {command}
```
    After emitting the block above you MUST actually invoke the hook and wait for it to finish before continuing. Run it the same way you would run the command yourself in this agent/session (the invocation may differ from the literal `{command}` id shown above, e.g. a skills-mode agent runs it as `/skill:speckit-...` or `$speckit-...`). Emitting the block alone does not run the hook.
  - **Optional hook** (`optional: true`):
```
    ## Extension Hooks

    **Optional Hook**: {extension}
    Command: `/{command}`
    Description: {description}

    Prompt: {prompt}
    To execute: `/{command}`
```

## Key rules

- One target per run; fresh, narrow context each time.
- Never draw the layout before components are chosen.
- Never change route structure (use §10). Never add design-system knowledge the DESIGN SYSTEM BLOCK did not provide.
- No application code, no final translation strings, no pixel-exact visual design.
- Use absolute paths for filesystem operations; use project-relative paths for references in documentation.

## Done When

- [ ] Target file written in the order needs, component choices, composition
- [ ] Target checklist passed
- [ ] ia.md §7 row for the target set to `drafted`
- [ ] Report given to the user