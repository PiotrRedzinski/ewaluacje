# Ewaluator — Happy Path Specification & Architecture Summary

## 1. Executive Overview & Scope
**Project**: Automated Evaluation Report Generator for *Fundacja Życie Warte Jest Rozmowy* (ZWJR).  
**Scope for Happy Path MVP**: Automated processing and report generation for **Pre vs Post Knowledge Test Surveys** (Google Forms exports in .xlsx or .csv).

---

## 2. Input Data & Matching Pipeline
* **Inputs**:
  * Preankieta: Baseline knowledge survey filled by participants before training.
  * Postankieta: Follow-up knowledge survey + evaluation filled after training.
* **Auto-Discovery & Question Alignment**:
  * The engine identifies shared questions between Pre and Post datasets (e.g. Columns 5–9 in the training datasets).
  * Unique post-training evaluation questions (e.g. Likert satisfaction scales, open feedback) are scoped for subsequent iterations.
* **Respondent Count Handling**:
  * Dynamically accounts for differences in sample sizes ({pre}$ vs {post}$).
  * Explicitly includes sample size context in statistical reporting without skewing percentage point deltas.

---

## 3. Statistical Calculation & Business Logic
* **Per-Question Calculations**:
  * Frequency distribution (counts and percentages) for all answer options in both Pre and Post datasets.
  * **Percentage Point Delta ($\Delta$ p.p.)**: Exact calculation of change for each answer choice:
    \Delta = \%_{post} - \%_{pre}
  * Identification of the designated **correct answer** (via answer key).
* **Grant Settlement Threshold Calculation ($\ge 75\%$)**:
  * Scores each individual respondent across all post-test knowledge questions.
  * Calculates the exact percentage of participants who achieved the grant threshold ($\ge 75\%$ correct answers, e.g. $\ge 4/5$ questions).

---

## 4. Visuals & Document Styling (.docx)
* **Format**: Fully editable .docx document, A4 portrait, Times New Roman (12pt body text, consistent headings and margins).
* **Charts**: High-resolution Matplotlib bar charts embedded per question:
  * Consistent corporate color palette for Pre vs Post answers.
  * Data labels showing exact percentage shares on bars.
* **3-Part Per-Question Structure**:
  1. **Question Title**: Clean heading with full question text.
  2. **Comparison Chart**: High-resolution embedded visualization.
  3. **Standard Narrative Paragraph**: Formal, objective Polish language adhering to grant guidelines:
     * States question topic.
     * Identifies correct answer (*„Poprawną odpowiedzią było stwierdzenie: ...”*).
     * Reports Post-training percentage and count ({post}$).
     * Compares with Pre-training baseline percentage and count ({pre}$).
     * Reports growth in percentage points (*„Odsetek poprawnych odpowiedzi wzrósł o X,X punktu procentowego...”*).
     * Formulates evidence-based conclusions with cautious phrasing (*„wyniki wskazują”*, *„odnotowano wzrost”*).
* **Summary Section**:
  * Overview of training effectiveness across all knowledge areas.
  * Highlight of questions with highest knowledge gain.
  * Formal grant compliance statement with the percentage of participants passing the $\ge 75\%$ threshold.

---

## 5. Delivery & Execution
* Standalone modular Python CLI tool (generate_report.py) with configurable file arguments (--pre, --post, --output).
