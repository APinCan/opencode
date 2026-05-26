# AGENTS.md

## Project Goal

Build a new VS Code extension GUI at `apps/vscode-gui` that provides a more user-friendly interface for OpenCode, while keeping **opencode CLI as the execution engine**.

This project uses Codex to accelerate extension development, but the architecture must remain:

- VS Code extension = interface/orchestration layer
- opencode CLI = backend engine/source of truth

---

## Repository Strategy

- Work on a dedicated branch: `feat/vscode-gui-wrapper`
- Implement new work inside: `apps/vscode-gui`
- Existing repo code is available for reference and integration understanding

---

## Scope Rules

### Read scope (allowed)
Codex may read any files in this repository to understand:
- CLI behavior
- existing protocols
- current extension patterns
- shared conventions

### Write scope (strict)
Codex may modify files **only** in:

- `apps/vscode-gui/**`

Optional extra write scope (only if explicitly requested in task):
- project-level docs that describe this app (e.g., README references)

### Forbidden write scope
Do not modify:
- `packages/opencode/**` (CLI internals are reference-only)
- `packages/sdk/**`
- `sdks/vscode/**` (legacy/current extension)
- unrelated repo areas

If a task appears to require CLI/internal changes, stop and propose alternatives first.

---

## Platform Constraint

- Target platform: **Windows only**
- Prioritize Windows process, terminal, and path semantics
- Do not spend effort on macOS/Linux compatibility unless explicitly requested

---

## Codex Tasking Protocol

For each Codex implementation task, include:

1. Goal
2. Constraints
3. Acceptance criteria
4. Allowed file paths (must remain under `apps/vscode-gui/**`)

Codex must:
- produce minimal, focused diffs
- avoid unrelated refactors
- preserve CLI-as-engine boundary
- summarize changes and verification steps

---

## Architectural Principles

1. Keep clear separation of concerns:
   - UI/Webview layer
   - Extension host layer
   - CLI bridge/orchestration layer
2. Keep business logic out of presentational UI components
3. Keep one clear runtime state model for session/execution state
4. Prefer simple, debuggable flows over clever abstractions

Suggested structure (guideline):

- `apps/vscode-gui/src/extension.ts` (activation + command wiring)
- `apps/vscode-gui/src/webview/*` (UI)
- `apps/vscode-gui/src/bridge/*` (CLI process + transport)
- `apps/vscode-gui/src/context/*` (file/selection/workspace context)
- `apps/vscode-gui/src/session/*` (history/state persistence)
- `apps/vscode-gui/src/config/*` (settings schema/defaults)

---

## CLI Boundary Contract

Treat opencode CLI as external engine contract:

- Validate CLI availability before execution flows
- Handle spawn/startup failures gracefully
- Support streaming output and cancellation
- Prevent duplicate/conflicting sessions unless intentionally supported
- Never silently swallow errors
- Surface actionable user-facing error messages

If missing capability is discovered:
1. Try extension-side workaround/adapter first
2. Document limitation
3. Request explicit approval before proposing CLI code changes

---

## UX Requirements (Cline-like direction)

Prioritize:
- fast interaction loop (submit -> immediate pending -> streaming output)
- explicit execution states (idle/starting/running/failed)
- keyboard-first operations
- discoverable GUI settings
- clear retry/recover actions for common failures

The UI should reduce manual terminal friction while preserving CLI power.

---

## Code Quality Standards

- TypeScript strict mode
- Avoid `any`
- Prefer early returns and small functions
- Use structured logs (e.g., `[vscode-gui][bridge]`)
- Add comments only for non-obvious behavior

---

## Testing & Verification

Focus tests on extension-specific behavior:

- bridge lifecycle/state transitions
- webview <-> host messaging contract
- context extraction utilities
- error/recovery paths

For each feature PR, include a short Windows manual QA checklist:
- launch
- send prompt
- stream output
- insert file/selection context
- recover from CLI-not-found or startup failure

---

## Commit & PR Conventions

Use conventional commit style, e.g.:

- `feat(vscode-gui): add streaming chat panel`
- `fix(vscode-gui): handle cli startup timeout`
- `refactor(vscode-gui): simplify bridge state machine`
- `test(vscode-gui): cover webview message routing`
- `docs(vscode-gui): document setup and run flow`

PRs must include:
- user-visible changes
- verification performed
- screenshots/GIFs for UI updates (when applicable)

Keep commits scoped to `apps/vscode-gui` work.

---

## Definition of Done

A feature is done when:

1. Implemented under `apps/vscode-gui/**`
2. Works in VS Code Extension Development Host on Windows
3. Preserves CLI-as-engine architecture
4. Has clear success and failure UX
5. Includes verification steps and updated docs as needed

---

## Guardrails Summary

- Read whole repo freely, write only in `apps/vscode-gui/**`
- Do not modify opencode CLI internals
- Build a better GUI, not a replacement engine
- Stay Windows-focused
- Keep changes minimal, explicit, and testable
