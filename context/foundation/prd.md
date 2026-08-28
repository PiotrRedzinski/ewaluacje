---
project: "Ewaluator"
version: 1
status: draft
created: 2026-08-27
context_type: greenfield
product_type: other
target_scale:
  users: small
  qps: low
  data_volume: medium
timeline_budget:
  mvp_weeks: 1
  hard_deadline: null
  after_hours_only: null
---

## Vision & Problem Statement

Fundacja Życie Warte Jest Rozmowy (ZWJR) pomaga osobom w kryzysie samobójczym i ich bliskim — rocznie dziesiątki tysięcy osób, ok. 50 osób personelu. Część działań finansuje z grantów (np. Ministerstwo Edukacji Narodowej) i rozlicza się z nich wynikami ankiet i testów wiedzy. W zeszłym roku jeden projekt wymagał raportów z ankiety „klimat szkoły" dla 55 placówek — po 52 pytania (połowa otwartych), ok. 100 odpowiedzi na szkołę.

Fundacja prowadzi m.in. szkolenia dla nauczycieli z grantów. Każde zaczyna się testem wiedzy (pytania zamknięte); po szkoleniu ten sam test wraca plus ankieta ewaluacyjna z pytaniami otwartymi. Do rozliczenia trzeba wykazać, że min. 75% uczestników przekroczyło próg punktowy — ktoś ręcznie liczy, kto ile poprawił, i wzrost w procentach. Do każdego pytania dołącza się wykres i osobny opis, co na nim widać. „Każdy widzi, co jest na tym wykresie, ale musi być i wykres, i tekst" — mówi Konstancja Szymacha, Sekretarz Zarządu ZWJR. Przy większych zestawach fundacja wspomaga się narzędziami czatu, które zawodzą przy dużej liczbie pytań naraz — przy raporcie z 55 szkół „z każdym kolejnym pytaniem coraz bardziej się zacinał". Wykresy dziś kopiuje się ręcznie z formularzy ankietowych. Zespół raportujący spędził w zeszłym roku ok. trzech miesięcy pracy dwóch osób na rozliczenie ankiet z jednego projektu. „Tworzenie tych dokumentów, one niczemu nikomu potem nie służą" — mówi Konstancja Szymacha.

## User & Persona

### Primary persona

**Konstancja Szymacha i zespół raportujący ZWJR** — pracownicy fundacji odpowiedzialni za rozliczanie grantów na podstawie wyników szkoleń i ankiet. Przy każdym rozliczeniu grantu (kilka naraz w niektórych okresach) muszą przygotować raporty z wykresami i opisami dla grantodawcy. Jeden duży projekt to ok. 3 miesiące pracy 2 osób w roku; problem dotyka min. 5 osób w fundacji.

## Success Criteria

### Primary

- # TODO: mierzalne kryterium sukcesu demo — see Open Questions
- Po wgraniu wyników „przed" i „po" dla jednego pytania zamkniętego narzędzie generuje wykres słupkowy porównujący odpowiedzi oraz opis zmiany (np. wzrost o X punktów procentowych) zgodnie z wyznaczonymi wymaganiami.
- Zespół pobiera plik wyglądający jak zwykły dokument, nie eksport z czatu.

### Secondary

- # TODO: kryteria sukcesu poza rdzeniem demo — see Open Questions
- [jeśli czas] to samo dla całej ankiety i pogrupowane odpowiedzi otwarte.

### Guardrails

- Błędnie policzony wzrost wiedzy albo zmyślony opis wykresu trafia do raportu dla grantodawcy i grozi żądaniem korekty — liczby muszą w 100% zgadzać się z danymi.
- Gdy coś jest niejednoznaczne, narzędzie ma to oznaczyć, nie zgadywać.
- Ton ma pokazywać osiągnięcia, ale to nie znaczy zmyślanie danych.
- Format nie może wyglądać jak eksport z czatu — to praca nad stylem dokumentu, nie tylko treścią.
- Duże wolumeny łamią podejście „wrzuć wszystko naraz" — raport z 55 szkół już się tak zacinał. Projektuj pod porcje od początku.

## User Stories

### US-01: Raport z jednego pytania zamkniętego (demo)

- **Given** członek zespołu raportującego ma plik z wynikami „przed" i „po" dla jednego pytania zamkniętego
- **When** wgrywa plik i uruchamia generowanie raportu
- **Then** narzędzie generuje wykres słupkowy porównujący odpowiedzi
- **And** pod wykresem pojawia się opis zmiany, np. wzrost o X punktów procentowych
- **And** zespół pobiera plik wyglądający jak zwykły dokument, nie eksport z czatu

#### Acceptance Criteria

- # TODO: szczegółowe kryteria akceptacji (format pliku wejściowego, struktura wykresu, wymagania opisu) — see Open Questions

### US-02: Cała ankieta i odpowiedzi otwarte (jeśli czas)

- **Given** członek zespołu ma wyniki dla wielu pytań, w tym otwartych
- **When** wgrywa dane całej ankiety
- **Then** narzędzie przetwarza wiele pytań naraz
- **And** grupuje podobne odpowiedzi otwarte z udziałem procentowym dla każdej grupy

#### Acceptance Criteria

- # TODO: kryteria akceptacji dla grupowania odpowiedzi otwartych — see Open Questions

## Functional Requirements

### Raportowanie rdzeniowe (demo)

- FR-001: Członek zespołu raportującego can wgrać wyniki „przed" i „po" dla jednego pytania zamkniętego. Priority: must-have
- FR-002: Członek zespołu raportującego can wygenerować wykres słupkowy porównujący odpowiedzi. Priority: must-have
- FR-003: Członek zespołu raportującego can zobaczyć pod wykresem krótki opis zmiany w procentach zgodnie z wyznaczonymi wymaganiami. Priority: must-have
- FR-004: Członek zespołu raportującego can pobrać gotowy dokument raportu wyglądający jak zwykły dokument. Priority: must-have

### Rozszerzenia (następne kroki)

- FR-005: Członek zespołu raportującego can przetworzyć całą ankietę na raz (wiele pytań). Priority: nice-to-have
- FR-006: Członek zespołu raportującego can zobaczyć pogrupowane podobne odpowiedzi otwarte z udziałem procentowym dla każdej grupy. Priority: nice-to-have
- FR-007: Członek zespołu raportującego can połączyć narzędzie z zewnętrznym źródłem ankiet. Priority: nice-to-have
- FR-008: Członek zespołu raportującego can wygenerować wnioski i rekomendacje w raporcie. Priority: nice-to-have
- FR-009: Członek zespołu raportującego can dopasować styl raportu do wymagań grantodawcy. Priority: nice-to-have

## Non-Functional Requirements

- Liczby w raporcie muszą w 100% zgadać się z danymi źródłowymi; gdy obliczenie lub interpretacja jest niejednoznaczna, narzędzie oznacza to wyraźnie zamiast zgadywać.
- Narzędzie obsługuje duże wolumeny danych przez przetwarzanie porcjami — nie wymaga wrzucenia całego zestawu naraz.
- Wygenerowany dokument wygląda jak zwykły dokument biurowy, a nie eksport z narzędzia czatu; tekst nie nosi rozpoznawalnych znamion bycia wygenerowanym automatycznie.
- # TODO: konkretne cele wydajnościowe i limity wolumenu — see Open Questions

## Business Logic

Rozliczenie grantu wymaga wykazania, że co najmniej 75% uczestników przekroczyło próg punktowy, przy czym dla każdego uczestnika liczy się wzrost wiedzy wyrażony w punktach procentowych między wynikiem „przed" a „po".

Do każdego pytania w raporcie dołącza się wykres i osobny opis, co na nim widać — „musi być i wykres, i tekst". Opis zmiany musi odzwierciedlać poprawny procent wzrostu zgodnie z danymi; wygląda prosto, bo wykres widać od razu, ale to dwa zadania naraz: wykres i opis z poprawnym procentem.

# TODO: szczegóły progu punktowego i reguł liczenia wzrostu — see Open Questions

## Access Control

# TODO: Access Control — see Open Questions

## Non-Goals

- Nie budujemy rozwiązania opartego na wrzuceniu całego zestawu pytań naraz do narzędzia czatu — duże wolumeny łamią to podejście.
- Połączenie z zewnętrznym źródłem ankiet — poza rdzeniem demo (nice-to-have, FR-007).
- Pisanie wniosków i rekomendacji — poza rdzeniem demo (nice-to-have, FR-008).
- Dopasowanie stylu pod grantodawcę — poza rdzeniem demo (nice-to-have, FR-009).
- Przetwarzanie całej ankiety i grupowanie odpowiedzi otwartych — poza rdzeniem demo, chyba że starczy czasu w dniu demo.

## Open Questions

1. **Jaka jest oficjalna nazwa projektu/produktu?** — Notatki nie nadają nazwy narzędziu; roboczo „Ewaluator" z nazwy repozytorium. Owner: user. Block: no.
2. **Jaki typ produktu (web-app, desktop, inne)?** — Notatki mówią o „narzędziu" z wgrywaniem plików i pobieraniem dokumentu, bez precyzji formy. Owner: user. Block: no.
3. **Czy praca odbywa się wyłącznie po godzinach (`after_hours_only`)?** — TBD. Owner: user. Block: no.
4. **Twardy termin (`hard_deadline`) dla demo/MVP?** — Notatki wskazują „demo na koniec dnia", bez daty kalendarzowej. Owner: user. Block: no.
5. **Mierzalne kryteria sukcesu Primary poza opisem demo?** — TBD. Owner: user. Block: no.
6. **Szczegóły progu punktowego i reguł liczenia wzrostu wiedzy** — Notatki wspominają próg i wzrost procentowy, ale bez pełnej specyfikacji obliczeń. Owner: user (opiekun grantu). Block: yes (błędne liczby grożą korektą od grantodawcy).
7. **Format pliku wejściowego i struktura danych „przed"/„po"** — Notatki wspominają CSV i przykładowe wyniki (min. 3), bez schematu kolumn. Owner: user. Block: yes (demo wymaga pliku).
8. **Wymagania dotyczące wykresu i opisu pod wykresem** — „zgodnie z wyznaczonymi wymaganiami" — jakie dokładnie? Owner: user. Block: yes.
9. **Wytyczne odnośnie tonu/formatu dokumentu wyjściowego** — Notatki wspominają format zwykłego dokumentu i brak znamion AI; szczegóły (czcionka, szablon grantodawcy) TBD. Owner: user. Block: yes (format raportu).
10. **Model dostępu: kto może wgrywać dane i pobierać raporty?** — Notatki wspominają „zespół", bez reguł auth. Owner: user. Block: no (MVP może być single-team).
11. **Integracja ze źródłem ankiet** — Notatki wskazują formularze ankietowe jako źródło danych dziś; integracja to następny krok. Owner: user. Block: no (poza MVP).
12. **Grupowanie odpowiedzi otwartych — definicja „podobnych" odpowiedzi** — TBD. Owner: user. Block: no (poza rdzeniem demo).
13. **Granica tonu „pokazywania osiągnięć" vs. zmyślania danych** — Notatki: „Ustal granicę z opiekunem przed promptami." Owner: user (opiekun). Block: yes (ryzyko raportu dla grantodawcy).
