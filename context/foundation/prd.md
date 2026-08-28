---
project: Ewaluator
version: 3
status: approved
created: 2026-08-27
last_updated: 2026-08-28
context_type: greenfield
product_type: cli_and_engine
target_scale:
  users: small (reporting team of 5)
  qps: batch
  data_volume: 55 schools, ~1000-2000 responses total
timeline_budget:
  mvp_weeks: 1
---

# PRD: Ewaluator — Automated Grant Evaluation & Knowledge Test Report Generator

## 1. Vision & Problem Statement

**Fundacja Życie Warte Jest Rozmowy (ZWJR)** provides crucial assistance to individuals in suicidal crisis and their relatives. A major portion of their educational initiatives is funded through public grants (such as the Ministry of National Education — MEN).

To settle and account for these grants, the foundation must produce comprehensive analytical reports detailing pre- and post-training knowledge test results and evaluation surveys across **55 educational facilities** (schools).

### Core Problem & Reporting Requirements:
1. **School-Level & Global Aggregation**: Reporting is conducted **per facility / school** (Szkoła:) as well as **globally** across all 55 schools.
2. **Anonymous Cohort Data (No Linked IDs)**: Participants fill out Google Forms anonymously ({pre} \neq N_{post}$, e.g. 35 pre vs 33 post in a school; 1065 pre vs 882 post globally). The system performs cross-sectional cohort comparison without requiring 1-to-1 participant IDs.
3. **Official Knowledge Test Answer Key**: The test comprises 5 core knowledge questions:
   - **Q1: Co jest prawdą o emocjach?** $\rightarrow$ *Obie odpowiedzi są poprawne.*
   - **Q2: Z psychologicznego punktu widzenia emocje:** $\rightarrow$ *Są normalne i potrzebne, ponieważ realizują szereg ważnych funkcji.*
   - **Q3: Co może pomóc w KONSTRUKTYWNYM ukojeniu nieprzyjemnych emocji?** $\rightarrow$ *Przyjęcie odpowiedniej postawy ciała lub wykonanie prostych ruchów.*
   - **Q4: Jak wykonuje się poprawny oddech przeponowy?** $\rightarrow$ *Wdech odbywa się przez nos, a wydech – przez usta.*
   - **Q5: Co jest sposobem na zmniejszenie podatności na odczuwanie nieprzyjemnych stanów emocjonalnych?** $\rightarrow$ *Codzienna troska o fizyczność, opierająca się na trzech głównych filarach: właściwej diecie, odpowiedniej ilości snu i ruchu.*
4. **Grant Threshold ($\ge 75\%$)**: Grant compliance requires demonstrating that $\ge 75\%$ of participants passed the knowledge test in the post-test measurement ($\ge 4$ out of 5 questions correct).

---

## 2. Key Capabilities & Scope

### Ingestion & Filtering Engine
* Load Google Forms .xlsx / .csv exports for Pre- and Post-surveys.
* Extract all 55 distinct schools from the Szkoła: column.
* Support 3 execution modes:
  1. **Single School Report**: --school I Liceum Ogólnokształcące... (Generates report for that school).
  2. **Global Report**: --global (Generates aggregated report for all 55 schools combined).
  3. **Batch Mode**: --batch (Generates individual reports for all 55 schools + 1 master global report).

### Statistical & Scoring Core
* **Per-Question Calculations**:
  * Response counts and percentage distributions before and after training for that school/scope.
  * Growth in percentage points: $\Delta = \%_{post} - \%_{pre}$.
* **School-Level $\ge 75\%$ Pass Rate**:
  * Grades each post-test participant row against the 5 answer keys.
  * Calculates the percentage of participants achieving $\ge 75\%$ (e.g. 29/33 = 87.9%).

### Visuals & Document Styling (.docx)
* **3-Part Per-Question Structure**:
  1. **Question Title**
  2. **High-Resolution Comparison Bar Chart** (Matplotlib, Times New Roman, percentage labels)
  3. **Standard Polish Narrative Paragraph**: Mentions question, correct answer, post %, pre %, p.p. delta, notes {pre} \neq N_{post}$ difference, and states evidence-based conclusion.
* **Executive Summary**:
  * Training effectiveness highlights.
  * Overall grant settlement compliance statement with the exact $\ge 75\%$ pass rate.
