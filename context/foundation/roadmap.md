---
project: "Ewaluator"
version: 1
status: draft
created: 2026-08-28
updated: 2026-08-28
prd_version: 2
main_goal: speed
top_blocker: time
---

# Roadmap: Ewaluator

> Derived from `context/foundation/prd.md` (v2) + auto-researched codebase baseline.
> Edit-in-place; archive when superseded.
> Slices below are listed in dependency order. The "At a glance" table is the index.

## Vision recap

Fundacja ZWJR ręcznie liczy wyniki ankiet szkoleniowych i pisze pod wykresami opisy w formalnym tonie, żeby rozliczyć się z grantodawcami — jeden duży projekt kosztował trzy miesiące pracy dwóch osób, a błędnie policzony wynik grozi korektą od grantodawcy. Ewaluator ma zdjąć tę powtarzalną pracę: policzyć wyniki deterministycznie, narysować wykres i napisać opis zgodny z wytycznymi tonu — bez zmyślania danych i bez wyglądu eksportu z czatu.

## North star

**S-01: Koordynator wgrywa dwa eksporty (przed/po) dla jednego pytania testu wiedzy i pobiera gotowy fragment raportu z wykresem i opisem** — to dokładnie scenariusz, który PRD nazywa wprost „demo" i który jest treścią Primary Success Criterion; jeśli to nie zadziała end-to-end, żadna inna część produktu nie ma znaczenia na prezentacji.

> Gwiazda przewodnia (ang. north star) — najmniejszy kompletny fragment produktu, który jeśli zadziała, dowodzi, że cała koncepcja (dane → wykres → opis → dokument) ma sens. Umieszczona jak najwcześniej w kolejności, bo reszta roadmapy ma sens tylko wtedy, gdy ten scenariusz działa.

*Uwaga o pominiętym pytaniu:* PRD nazywa US-01 wprost „(demo)" i jego treść pokrywa się słowo w słowo z Primary Success Criterion — to jednoznaczne, więc nie pytałem o wybór gwiazdy przewodniej osobno.

## At a glance

| ID | Change ID | Outcome (user can …) | Prerequisites | PRD refs | Status |
|---|---|---|---|---|---|
| F-01 | csv-import-foundation | (foundation) surowy eksport CSV Google Forms jest wgrywany, kolumny metadanych oddzielone od pytań, typ pytania rozpoznany | — | FR-001, FR-002, FR-003, FR-004 | ready |
| F-02 | deploy-to-ai-studio | (foundation) aplikacja jest wdrażana i widoczna pod adresem w AI Studio, CI sprawdza build przy każdym mergu | — | tech-stack.md: deployment_target=ai-studio, ci_provider=github-actions | ready |
| S-01 | single-question-knowledge-report | koordynator wgrywa parę CSV przed/po dla jednego pytania testu wiedzy i pobiera edytowalny fragment raportu z wykresem i opisem | F-01 | US-01, FR-005, FR-006, FR-007, FR-008, FR-009, FR-010 | blocked |
| S-02 | full-training-knowledge-report | koordynator generuje pełny raport testu wiedzy dla jednego szkolenia (wszystkie pytania, metryczka, podsumowanie) | S-01, F-01 | US-02, FR-011, FR-012, FR-017, FR-018, FR-019, FR-020, FR-021, FR-022 | proposed |
| S-03 | evaluation-survey-report | koordynator generuje raport z ankiety ewaluacyjnej z pogrupowanymi odpowiedziami otwartymi i cytatami | F-01 | US-03, FR-015, FR-016 | proposed |
| S-04 | grant-threshold-analysis | koordynator sprawdza, czy odsetek uczestników przekraczających próg 75% jest spełniony | S-01, F-01 | US-04, FR-013 | proposed |

## Streams

Navigation aid — grupuje pozycje po wspólnym łańcuchu zależności. Kanoniczna kolejność to graf zależności poniżej; ta tabela to proponowana kolejność czytania dla równoległych wątków (np. do rozdzielenia pracy między programistów).

| Stream | Theme | Chain | Note |
|---|---|---|---|
| A | Ścieżka testu wiedzy (gwiazda przewodnia) | `F-01` → `S-01` → `S-02` | Główny wątek — main_goal: speed, więc ten łańcuch idzie pierwszy i bez przerw. |
| B | Próg grantowy | `S-04` | Dołącza do Stream A przy `S-01` — może ruszyć równolegle do S-02/S-03 od razu po S-01. |
| C | Pętla ewaluacyjna (pytania otwarte) | `S-03` | Dołącza do Stream A przy `F-01` — inna ścieżka pytań, można robić równolegle do S-01/S-02 od startu (po F-01). |
| D | Wdrożenie i CI | `F-02` | Bez zależności — osobny wątek od pierwszej minuty; top_blocker: time, więc problemy z hostingiem trzeba wyłapać zanim zabraknie czasu na prezentację. |

## Baseline

Stan na `2026-08-28` (auto-research + potwierdzone przez użytkownika: „Auth nie robimy. Scaffold zrobiony. Na razie 0 logiki. Zero CI. Dane mamy w context/foundation/sources/").
Fundamenty poniżej zakładają ten stan i go nie odtwarzają.

- **Frontend:** partial — szkielet React Router (`app/root.tsx`, `app/routes.ts`, `app/welcome/welcome.tsx`), brak UI do uploadu/raportu.
- **Backend / API:** partial — routing React Router gotowy do użycia (loadery/akcje), brak endpointów przetwarzania danych.
- **Data:** absent — brak bazy danych, celowo (MVP bez trwałego przechowywania danych osobowych z eksportów); przykładowe dane referencyjne leżą w `context/foundation/sources/`.
- **Auth:** absent — celowo, MVP bez logowania (`tech-stack.md`: `has_auth: false`).
- **Deploy / infra:** partial — `Dockerfile` obecny ze scaffoldu, brak workflow CI, brak potwierdzonego podłączenia do AI Studio.
- **Observability:** absent.

## Foundations

### F-01: Import i rozpoznawanie CSV

- **Outcome:** (foundation) surowy eksport CSV z Google Forms można wgrać; kolumny metadanych (sygnatura czasowa, RODO, stanowisko, staż, szkoła) są oddzielone od kolumn pytań; typ każdego pytania (zamknięte jednokrotnego wyboru / skala / otwarte) jest rozpoznany.
- **Change ID:** csv-import-foundation
- **PRD refs:** FR-001, FR-002, FR-003, FR-004
- **Unlocks:** S-01, S-02, S-03, S-04 — żaden z tych slice'ów nie może policzyć ani narysować niczego, dopóki nie wie, które kolumny to pytania i jakiego są typu.
- **Prerequisites:** —
- **Parallel with:** F-02
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Musi poprawnie rozpoznać realny eksport Google Forms (przykłady w `context/foundation/sources/*.csv`) — błąd w rozpoznaniu kolumn/typu pytania psuje całą resztę potoku dla wszystkich czterech slice'ów naraz, więc warto zweryfikować od razu na obu przykładowych plikach.
- **Status:** ready

### F-02: Wdrożenie do AI Studio i CI

- **Outcome:** (foundation) aplikacja jest wdrożona i osiągalna pod adresem w Google AI Studio; GitHub Actions sprawdza build/lint przy każdym mergu do main.
- **Change ID:** deploy-to-ai-studio
- **PRD refs:** tech-stack.md: `deployment_target: ai-studio`, `ci_provider: github-actions`, `ci_default_flow: auto-deploy-on-merge`
- **Unlocks:** ścieżka weryfikacji „można to pokazać na żywo" dla S-01 (gwiazda przewodnia) i dla prezentacji kończącej hackathon; bez tego demo istnieje tylko lokalnie na jednym laptopie.
- **Prerequisites:** —
- **Parallel with:** F-01, S-01, S-02, S-03, S-04
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Sekwencjonowany od pierwszej minuty i równolegle do reszty prac, bo top_blocker to czas — problem z hostingiem wykryty w dniu prezentacji jest najdroższy do naprawienia; nie blokuje merytorycznie żadnego slice'a, więc jeden programista może zająć się tym w izolacji.
- **Status:** ready

## Slices

### S-01: Raport z jednego pytania testu wiedzy (demo)

- **Outcome:** koordynator wgrywa dwa eksporty (przed/po) dla jednego wspólnego pytania zamkniętego, wskazuje pytanie, i pobiera edytowalny fragment raportu z wykresem słupkowym (spójne kolory przed/po) i opisem (treść pytania, liczby i odsetki, odpowiedź prawidłowa, różnica w punktach procentowych, krótki wniosek).
- **Change ID:** single-question-knowledge-report
- **PRD refs:** US-01, FR-005, FR-006, FR-007, FR-008, FR-009, FR-010
- **Prerequisites:** F-01
- **Parallel with:** S-03 (po ukończeniu F-01), F-02
- **Blockers:** —
- **Unknowns:**
  - Skąd narzędzie bierze klucz odpowiedzi prawidłowej per pytanie (konfiguracja ręczna, plik metadanych, wykrywanie z treści pytania)? — Owner: user. Block: yes.
- **Risk:** To gwiazda przewodnia — zablokowana wyłącznie decyzją o źródle klucza odpowiedzi, nie brakiem możliwości technicznych. Rozstrzygnięcie tego pytania równolegle z pracą nad F-01 (nie po niej) to najszybsza droga do działającego demo, biorąc pod uwagę main_goal: speed.
- **Status:** blocked

### S-02: Pełny raport z testu wiedzy dla jednego szkolenia

- **Outcome:** koordynator uruchamia generowanie pełnego raportu dla jednego szkolenia — dokument zawiera wstęp programu, metryczkę respondentów, każde pytanie wiedzy z wykresem i opisem (w tym liczbę odpowiedzi i zwrot), i podsumowanie sekcji; przetwarzanie idzie porcjami, nie wymaga wrzucenia wszystkich pytań naraz.
- **Change ID:** full-training-knowledge-report
- **PRD refs:** US-02, FR-011, FR-012, FR-017, FR-018, FR-019, FR-020, FR-021, FR-022
- **Prerequisites:** S-01, F-01
- **Parallel with:** S-03, S-04 (po ukończeniu S-01)
- **Blockers:** —
- **Unknowns:**
  - Czy stały blok opisowy programu „Wspierająca Szkoła" generować z szablonu, czy użytkownik wkleja go ręcznie? — Owner: user. Block: no.
- **Risk:** Rozszerza potok obliczeń/opisu z S-01 na wiele pytań i dodaje przetwarzanie porcjami (FR-022, referencja skali: 1000+ respondentów) — sekwencjonowany po S-01, żeby nie budować tego samego dwa razy.
- **Status:** proposed

### S-03: Raport z ankiety ewaluacyjnej (pytania otwarte)

- **Outcome:** koordynator generuje raport z postankiety ewaluacyjnej, w którym odpowiedzi otwarte są pogrupowane według najczęstszych tematów, z liczbą i odsetkiem wypowiedzi per kategoria oraz krótkimi anonimowymi cytatami (pojedyncze głosy oznaczone jako jednostkowe).
- **Change ID:** evaluation-survey-report
- **PRD refs:** US-03, FR-015, FR-016
- **Prerequisites:** F-01
- **Parallel with:** S-01, S-02, S-04
- **Blockers:** —
- **Unknowns:**
  - Czy koordynator zatwierdza kategorie tematyczne przed eksportem, czy grupowanie jest w pełni automatyczne? — Owner: user. Block: no.
- **Risk:** Inna ścieżka danych niż test wiedzy (grupowanie tematyczne zamiast porównania przed/po) — dzieli z resztą roadmapy tylko F-01, więc to naturalny drugi równoległy wątek pracy od samego startu.
- **Status:** proposed

### S-04: Analiza progu 75% dla rozliczenia grantu

- **Outcome:** koordynator uruchamia obliczenie progu grantowego i widzi odsetek uczestników, którzy przekroczyli próg punktowy, oznaczony jako „spełniony/niespełniony" względem wymogu 75% — obliczenie audytowalne, agregatowe na poziomie pytania/grupy (zgodnie z rozstrzygnięciem w `## Business Logic` PRD: brak wspólnego identyfikatora uczestnika w eksportach).
- **Change ID:** grant-threshold-analysis
- **PRD refs:** US-04, FR-013
- **Prerequisites:** S-01, F-01
- **Parallel with:** S-02, S-03
- **Blockers:** —
- **Unknowns:** —
- **Risk:** Wymaga tej samej klasyfikacji odpowiedź poprawna/błędna co S-01, więc czeka na S-01 — ale nie na S-02 ani S-03, więc to dobry osobny wątek pracy dla czwartego programisty od razu po S-01.
- **Status:** proposed

## Backlog Handoff

| Roadmap ID | Change ID | Suggested issue title | Ready for `/10x-plan` | Notes |
|---|---|---|---|---|
| F-01 | csv-import-foundation | Import i rozpoznawanie CSV z Google Forms | yes | Zrób jako pierwsze — odblokowuje wszystkie 4 slice'y. |
| F-02 | deploy-to-ai-studio | Wdrożenie do AI Studio + CI (GitHub Actions) | yes | Może iść od razu, równolegle, jako osobny wątek. |
| S-01 | single-question-knowledge-report | Raport z jednego pytania testu wiedzy (demo) | no | Zablokowany decyzją o źródle klucza odpowiedzi — rozstrzygnąć równolegle z F-01. |
| S-02 | full-training-knowledge-report | Pełny raport testu wiedzy dla jednego szkolenia | no | Po S-01. |
| S-03 | evaluation-survey-report | Raport z ankiety ewaluacyjnej (pytania otwarte) | no | Po F-01, może iść równolegle do S-01/S-02. |
| S-04 | grant-threshold-analysis | Analiza progu 75% dla rozliczenia grantu | no | Po S-01, może iść równolegle do S-02/S-03. |

## Open Roadmap Questions

1. **Ile czasu przechowywać wgrywane pliki z danymi osobowymi (imię, nazwisko, e-mail w metryczce)? Czy anonimizować przed zapisem?** — Owner: user / DPO. Block: roadmap-wide dla wersji produkcyjnej (nie blokuje MVP — narzędzie ma nie przechowywać danych dłużej niż potrzeba).
2. **Model auth dla wersji produkcyjnej (np. logowanie Google Workspace ZWJR)?** — Owner: user. Block: nie blokuje MVP (celowo bez auth), ale trzeba rozstrzygnąć przed udostępnieniem szerszemu gronu niż 5 osób z zespołu.

## Parked

- **Podejście oparte na wrzuceniu całego zestawu pytań naraz do narzędzia czatu** — Why parked: PRD Non-Goals — to właśnie problem, który Ewaluator ma rozwiązać (narzędzia czatu „zacinają się" przy dużych zestawach).
- **Integracja z zewnętrznym źródłem ankiet (FR-023)** — Why parked: PRD Non-Goals, nice-to-have poza MVP.
- **Automatyczne wnioski i rekomendacje na końcu raportu (FR-024)** — Why parked: PRD Non-Goals; ryzyko fabrykacji treści bez weryfikacji człowieka.
- **Szablony per grantodawca (FR-025)** — Why parked: PRD Non-Goals, nice-to-have poza MVP.
- **Pełny raport wieloszkoleniowy (113 stron, referencja historyczna)** — Why parked: PRD Non-Goals — poza rdzeniem demo; MVP dowodzi jednego pytania (S-01), potem całej ankiety jednego szkolenia (S-02).
- **Kwestionariusz wielowymiarowy „klimat szkoły" (US-05, 52 pytania × 55 placówek)** — Why parked: PRD Non-Goals — jawnie poza MVP demo; architektura porcjowa (F-01, S-02/FR-022) ma to jednak uwzględniać od początku, żeby nie trzeba było przepisywać potoku, gdy ten slice kiedyś wróci.

## Done

