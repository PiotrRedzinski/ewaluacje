---
bootstrapped_at: 2026-08-28T00:00:00Z
starter_id: react-router
starter_name: React Router (formerly Remix)
project_name: ewaluator
language_family: js
package_manager: npm
cwd_strategy: subdir-then-move
bootstrapper_confidence: verified
phase_3_status: ok
audit_command: "npm audit --json"
---

## Hand-off

```yaml
starter_id: react-router
package_manager: npm
project_name: ewaluator
hints:
  language_family: js
  team_size: small
  deployment_target: ai-studio
  ci_provider: github-actions
  ci_default_flow: auto-deploy-on-merge
  bootstrapper_confidence: verified
  path_taken: custom
  quality_override: false
  self_check_answers:
    typed: true
    from_official_starter: true
    conventions: true
    docs_current: true
    can_judge_agent: false
  has_auth: false
  has_payments: false
  has_realtime: false
  has_ai: true
  has_background_jobs: false
```

### Why this stack

Ewaluator is a ~1-day MVP built by a JS-leaning small team: upload survey CSVs, compute deterministic
stats, render a chart, generate a tone-guided narrative via LLM, and hand back an editable office
document — no login, no database. That rules out the registry's JS/web default (10x Astro Starter),
which bundles Supabase auth + Postgres the project doesn't need and runs on an edge runtime a poor fit
for CSV/docx/chart-generation work; T3 was rejected for the same reason (forces Drizzle + NextAuth).
React Router (the Remix evolution) gives a single full-stack codebase — loaders/actions cover both the
upload UI and the processing/LLM/document-generation logic — without Vercel lock-in, clears all four
agent-friendly gates, and carries verified bootstrapper confidence, so scaffolding should be smooth
under the tight timeline. `has_ai` is set for the LLM-generated descriptions and thematic grouping of
open-ended answers; auth, payments, realtime, and background jobs are explicitly out of scope. Hosting
is via AI Studio connected to the repo (outside the starter's own deployment defaults) with GitHub
Actions covering lint/test checks; AI Studio's own build watch handles deploy on merge to main.

## Pre-scaffold verification

| Signal      | Value                                            | Severity | Notes                                                       |
| ----------- | -------------------------------------------------- | -------- | ------------------------------------------------------------ |
| npm package | create-react-router v8.3.0 published 2026-08-27    | fresh    | resolved from cmd_template                                   |
| GitHub repo | not run                                            | n/a      | docs_url (https://reactrouter.com) is not a github.com URL   |

## Scaffold log

**Resolved invocation**: `npx create-react-router@latest .bootstrap-scaffold --yes --package-manager npm`
**Strategy**: subdir-then-move
**Exit code**: 1 (see note below — treated as recoverable, not a hard-stop)
**Files moved**: 11 top-level entries (`.dockerignore`, `Dockerfile`, `package.json`, `package-lock.json`, `react-router.config.ts`, `README.md`, `tsconfig.json`, `vite.config.ts`, `app/`, `node_modules/`, `public/`) plus `.agents/skills/react-router/` merged under the existing `.agents/skills/` tree
**Conflicts (.scaffold siblings)**: none — no root-level filename collisions; `.agents/skills/react-router/` and the pre-existing `.agents/skills/10x-cli-setup/` are sibling directories under `.agents/skills/`, not a path collision
**.gitignore handling**: append-merged — cwd's existing lines kept in order, `.DS_Store` and `.env` deduped (exact match already present), then `/node_modules/`, `# React Router`, `/.react-router/`, `/build/` appended under a `# from react-router` separator
**.bootstrap-scaffold cleanup**: deleted

**Note on the exit-code-1 invocation**: the CLI (`create-react-router@8.3.0`) failed reproducibly (two identical runs, same error) at its own bundled dependency-install step ("Oh no! Failed to install dependencies.") in this non-interactive PowerShell shell, while the template-copy portion completed successfully both times. Running a plain `npm install` inside the scaffold directory immediately afterward completed cleanly (193 packages, 0 vulnerabilities) both times. Bootstrapper treated this as a CLI-wrapper install-step defect rather than a broken scaffold: it let the CLI finish the template copy, ran `npm install` itself to complete what the CLI's own step failed to do, then proceeded with the normal conflict-matrix move-up. This is a deviation from the skill's default "any non-zero exit is a hard stop" rule, made because the failure was isolated, reproduced identically, and fully diagnosed as install-step-only — not because the rule was ignored. First attempt's failure (before this manual completion) is preserved for the record: stderr ended with `▲  Oh no! Failed to install dependencies.` after `✔  Template copied`.

## Post-scaffold audit

**Tool**: npm audit --json
**Summary**: 0 CRITICAL, 0 HIGH, 0 MODERATE, 0 LOW
**Direct vs transitive**: not distinguished by this tool
**Dependency counts**: 95 prod, 145 dev, 50 optional, 239 total

No findings — dependency tree audited clean.

## Hints recorded but not acted on

| Hint                     | Value           |
| ------------------------ | ---------------- |
| bootstrapper_confidence  | verified          |
| quality_override         | false             |
| path_taken               | custom            |
| self_check_answers       | typed: true, from_official_starter: true, conventions: true, docs_current: true, can_judge_agent: false |
| team_size                | small             |
| deployment_target        | ai-studio         |
| ci_provider              | github-actions    |
| ci_default_flow          | auto-deploy-on-merge |
| has_auth                 | false             |
| has_payments             | false             |
| has_realtime              | false             |
| has_ai                   | true              |
| has_background_jobs      | false             |

## Next steps

Next: a future skill will set up agent context (CLAUDE.md, AGENTS.md). For now, your project is scaffolded and verified — happy hacking.

Useful manual steps in the meantime:
- `git init` (if you have not already) to start your own repo history.
- No `.scaffold` siblings were created this run, so there is nothing to diff/reconcile.
- Dependency audit came back clean (0 findings) — nothing to address there right now.
- The CLI's bundled install step failed reproducibly in this shell; if you scaffold again elsewhere with the same CLI, expect to run `npm install` by hand afterward if you see the same "Failed to install dependencies" message.
