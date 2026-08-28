MVP Checklist and Technical Approach

Goal
- Deliver a demo that, given CSV input for one closed question (pre/post), produces a Word report (.docx) containing a bar chart and an accurate textual description of change.

MVP Checklist
- [ ] Agree input CSV schema (columns for respondent ID, pre_answer, post_answer, timestamps, optional metadata).
- [ ] Define scoring rules and the 75% threshold calculation (per-participant scoring vs. aggregate pass-rate).
- [ ] Implement CSV upload endpoint and validation.
- [ ] Implement calculation module (Pandas) that computes counts, percentages, and absolute percentage-point changes.
- [ ] Render bar chart (Matplotlib or Plotly static image) for inclusion in .docx.
- [ ] Generate .docx (python-docx) with chart image and a short human-like paragraph describing the change.
- [ ] Add clear flags in the report where data are ambiguous or insufficient.

Suggested Input Schema (example CSV)
- respondent_id, pre_score, post_score, pre_choice, post_choice
- For closed questions with choices, either use per-respondent choice columns or aggregated counts per choice.

Calculation Rules (proposal, confirm with stakeholder)
- Compute per-choice percentages for pre and post: pct = count(choice)/total_responses * 100.
- Absolute change (percentage points) = pct_post - pct_pre for each choice.
- For the 75% threshold: define a pass threshold (e.g., score >= X); compute percent of respondents passing before and after; report the percent-point increase and whether the 75% target was achieved.
- Mark any respondents with missing pre or post as "incomplete" and exclude from paired-change calculations; surface counts of excluded cases.

Processing Design
- API: small web service (FastAPI) with endpoints: `/upload-csv`, `/generate-report`, `/status`.
- Processing: validate CSV → compute stats with Pandas → generate chart (Matplotlib/Plotly) → compose .docx with python-docx.
- Batch handling: process large datasets in chunks and stream results; keep memory usage bounded by aggregating counts rather than holding full-text answers when possible.

Open Questions / Guardrails
- Confirm exact CSV format and column names.
- Confirm the numerical/score threshold rules for "pass" and how to treat partial or missing data.
- Agree on Word template (fonts, headings, required sections) so output matches grantor expectations and avoids "AI-like" phrasing.

Extensions (post-MVP)
- Bulk processing of whole surveys and multiple questions.
- Grouping/clustering of open-text responses (embedding + k-means or semantic clustering) with human-in-the-loop review.
- Integration with Google Forms API and Google Workspace for direct imports.
- Customizable report templates per grantor.

Suggested Tech Stack
- Python 3.11+, FastAPI, Uvicorn
- Pandas for data processing
- Matplotlib or Plotly for charts (export to PNG)
- python-docx for .docx generation
- Optional: sentence tuning using a local template engine + small LLM calls with strict numeric-safety checks for narrative phrasing

Developer notes
- Ensure calculations are unit-tested with sample CSVs to guarantee numeric correctness.
- Add explicit warnings in the UI and in generated documents where data were excluded or calculations are ambiguous.

Run / dev commands (example)

1. Create venv and install:

```bash
python -m venv .venv
source .venv/bin/activate
pip install fastapi uvicorn pandas matplotlib python-docx
```

2. Run dev server:

```bash
uvicorn app.main:app --reload
```


