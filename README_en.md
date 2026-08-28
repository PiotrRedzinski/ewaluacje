Ewaluator — Project Summary

Purpose
- Automate generation of grant-report-ready documents from training and survey results for Fundacja Życie Warte Jest Rozmowy (ZWJR).

Problem
- Reporting requires per-question charts and textual descriptions for pre/post tests and surveys. Manual work consumed ~3 months of two people for one large project (55 schools).

MVP (demo)
- Input: CSV with "before" and "after" results for a single closed question.
- Output: A bar chart comparing responses, a short accurate description of change (e.g., "increase of X percentage points"), and a downloadable .docx report that looks like a regular Word document (Times New Roman), not an AI export.
- Guardrails: All numeric calculations must exactly match source data; ambiguous cases are flagged and not guessed.

Primary users
- ZWJR reporting team (small number of internal users who prepare grant reports).

Why it matters
- Removes repetitive, low-value work and frees staff to focus on mission-critical activities helping people in crisis.

Next steps
- Define input CSV schema and exact calculation rules for thresholds and percent-change.
- Implement a minimal backend to accept CSV, compute, render chart, generate .docx.
- Add batching and open-ended response grouping for larger surveys.

Contact
- See the project PRD files in this folder for detailed context and open questions.
