---
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
---

## Why this stack

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
