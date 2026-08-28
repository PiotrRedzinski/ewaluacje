---
project: Ewaluator
version: 2
status: approved
created: 2026-08-27
last_updated: 2026-08-28
context_type: greenfield
product_type: cli_and_engine
target_scale:
  users: small (reporting team of 5)
  qps: batch
  data_volume: ~100-2000 responses per survey, up to 55 schools
timeline_budget:
  mvp_weeks: 1
  hard_deadline: null
  after_hours_only: null
---

# PRD: Ewaluator — Automated Grant Evaluation & Knowledge Test Report Generator

## 1. Vision & Problem Statement

**Fundacja Życie Warte Jest Rozmowy (ZWJR)** provides crucial assistance to individuals in suicidal crisis and their relatives, operating with approx. 50 staff members and helping tens of thousands of people annually. A major portion of their training and educational initiatives is funded through public grants (such as the Ministry of National Education — MEN).

To settle and account for these grants, the foundation is required to produce comprehensive analytical reports detailing pre- and post-training knowledge test results and evaluation surveys. For instance, a single project involved evaluating **55 educational facilities** (schools), each with 52 survey questions (~50% open-ended) and ~100 responses per school.

### Current Workflow & Bottlenecks:
1. **Manual Data Aggregation**: Each training begins with a pre-training knowledge test (closed questions) and concludes with a post-training test plus an evaluation survey.
2. **Grant Threshold Requirement**: The grant settlement requires demonstrating that **at least 75% of participants scored over the required knowledge threshold** in the post-test.
3. **Manual Calculations & Chart Copying**: Staff members manually calculate percentage improvements, copy charts from Google Forms, and write individual descriptive paragraphs for every single question.
4. **Chat Interface Failures**: Attempting to paste large survey datasets into standard web chat tools (ChatGPT/Gemini) causes failures, context overflow, and hallucinations on large batches.
5. **Time Sink**: In the previous cycle, two team members spent approximately **3 months of manual work** compiling these reports.

**Core Goal**: Automate the intake of Google Forms survey data (Pre/Post .xlsx/.csv), compute 100% accurate statistics (frequencies, percentage point deltas, individual $\ge 75\%$ pass rates), generate publication-ready bar charts, and compile an official, professionally styled Microsoft Word (.docx) report.

---

## 2. User & Persona

### Primary Persona
* **Konstancja Szymacha (Secretary of the Board, ZWJR) & the ZWJR Reporting Team**:
  * Needs to reliably generate compliant evaluation reports for grant settlement under strict deadlines.
  * Cannot risk calculation errors or AI hallucinations that could lead to grant audit corrections or funding disputes.
  * Requires output in standard, editable .docx office documents adhering to ministerial formatting rules (Times New Roman 12pt, clean structure, no recognizable AI hallmarks).

---

## 3. Scope & Milestones

### Phase 1: Happy Path MVP (Current Target)
* Automatic ingestion of Google Forms Pre- and Post-training survey exports (.xlsx / .csv).
* Automated alignment of shared Pre/Post knowledge test questions.
* Computation of exact answer choice distributions, sample counts ({pre}$, {post}$), and percentage point differences ($\Delta$ p.p.).
* Evaluation against the Answer Key to grade each participant's post-test and compute the **$\ge 75\%$ Grant Settlement Pass Rate**.
* High-resolution Matplotlib chart generation matching reference aesthetics.
* Standardized, formal Polish narrative generation for each question following the 3-part guideline structure.
* Compilation into an editable .docx document with section summary.
* Standalone Python CLI execution (generate_report.py).

### Phase 2: Secondary Features & Extensions (Next Iterations)
* Processing of Post-only evaluation questions (Likert ratings, single-choice satisfaction questions).
* Thematic grouping & clustering of open-ended text answers with percentage shares and anonymized representative quotes.
* Batch multi-school processing (generating 55 individual school reports + 1 global summary report).
* Direct Google Forms / Google Drive API connector.

---

## 4. Success Criteria & Guardrails

### Primary Success Metrics
* **100% Numerical Precision**: Every count, percentage, and percentage point delta in charts and text matches the raw source data with zero hallucinations.
* **Format Compliance**: Output document matches the visual and typographical requirements of Wytyczne dotyczące tonu i formatu raportu.docx and the reference PDF reports.
* **Deterministic Calculation**: Calculations of knowledge gains and the 75% threshold are performed by a verifiable Python engine.
* **Speed**: End-to-end report generation for a full survey dataset in $< 15$ seconds.

### Guardrails
* **No AI Markers**: Narrative phrasing must sound natural, professional, and formal — avoiding generic chat-assistant clichés.
* **Cautious Tone**: Conclusions must use cautious, evidence-based language (*„wyniki wskazują”*, *„odnotowano wzrost”*, *„może to świadczyć o...”*) and never claim unverified causation.
* **Explicit Sample Size Handling**: Whenever {pre} \neq N_{post}$, the report text must explicitly disclose the participant count difference while comparing proportional percentages.
* **Uncertainty Flagging**: Any ambiguous or corrupted response data must be clearly flagged rather than guessed.

---

## 5. Functional Requirements (FR)

### Ingestion & Data Alignment
* **FR-001**: System must load Pre-survey and Post-survey datasets from .xlsx or .csv files.
* **FR-002**: System must automatically identify and align shared knowledge questions between Pre and Post files.
* **FR-003**: System must extract total participant counts ({pre}$, {post}$) and demographic metadata (occupation, seniority, school name).

### Statistical & Scoring Engine
* **FR-004**: System must compute response frequencies and percentage distributions for each answer choice in Pre and Post.
* **FR-005**: System must calculate the delta in percentage points: $\Delta = \%_{post} - \%_{pre}$.
* **FR-006**: System must accept an Answer Key specifying the correct option for each knowledge question.
* **FR-007**: System must grade each participant row in the post-test and compute the exact percentage of participants who achieved $\ge 75\%$ correct answers (grant settlement threshold).

### Visualization & Document Compilation
* **FR-008**: System must render high-resolution bar charts for each question showing Pre vs Post percentage shares with visible data labels.
* **FR-009**: System must construct a formal Polish narrative paragraph for each question adhering to the reference structure:
  1. Question introduction and theme.
  2. Identification of the correct answer (*„Poprawną odpowiedzią było stwierdzenie: ...”*).
  3. Post-training result (% and count).
  4. Pre-training comparison (% and count).
  5. Percentage point difference (*„Odsetek poprawnych odpowiedzi wzrósł o X,X punktu procentowego...”*).
  6. Sample size difference acknowledgment ({pre}$ vs {post}$) and cautious takeaway.
* **FR-010**: System must compile all questions, charts, narrative paragraphs, and an overall summary section into a formatted Word .docx file (A4, Times New Roman 12pt).

---

## 6. Non-Functional Requirements (NFR)

* **NFR-001 (Portion-based Batch Processing)**: Processing must occur in discrete, question-by-question pipeline steps to prevent memory bloat or token exhaustion when scaling to 55+ schools.
* **NFR-002 (Platform Compatibility)**: Must run on standard Python 3.10+ environments across Windows, macOS, and Linux without proprietary OS dependencies.
* **NFR-003 (Modularity)**: Statistical engine, chart renderer, narrative generator, and document builder must be decoupled to enable future UI integration.

---

## 7. User Stories & Acceptance Criteria

### US-01: Generate Knowledge Test Comparison Report
* **Given** a user has Pre-training and Post-training survey files (Preankieta.xlsx and Postankieta.xlsx).
* **When** the user runs python generate_report.py --pre Preankieta.xlsx --post Postankieta.xlsx --out Raport_Wiedza.docx.
* **Then** the system aligns all shared knowledge questions, calculates all percentage distributions and p.p. deltas, embeds high-res comparison bar charts, generates compliant Polish narrative descriptions, and saves the final .docx report.

### US-02: Compute and Report 75% Grant Settlement Threshold
* **Given** the post-survey responses and the answer key for the 5 knowledge questions.
* **When** the statistical engine evaluates post-test scores.
* **Then** the system calculates the percentage of participants scoring $\ge 75\%$ (e.g. $\ge 4/5$ correct) and records this metric in the summary section of the report.

---

## 8. Resolved Decisions from Grill-Me Analysis

| # | Question / Topic | Resolution |
|---|---|---|
| 1 | **Product Type & Interface** | Standalone Python CLI and modular analytics engine. |
| 2 | **Happy Path Scope** | Shared Pre/Post Knowledge Test questions (Cols 5–9). Post-only evaluation questions deferred to Phase 2. |
| 3 | **Sample Size Variance** | Calculate proportional percentages for comparison and explicitly disclose {pre} \neq N_{post}$ in narrative. |
| 4 | **Correct Answer Identification** | Configurable Answer Key mapping each question to its correct option. |
| 5 | **75% Pass Rate Calculation** | Evaluated on individual respondent rows in Postankieta across all knowledge questions. |
| 6 | **Chart Rendering** | High-resolution Matplotlib bar charts styled with Times New Roman and clean percentage labels. |
| 7 | **Document Styling** | Exact replica of reference Word/PDF layout: Title $\rightarrow$ Chart $\rightarrow$ Formal Polish narrative paragraph. |
