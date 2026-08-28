---
project: "Ewaluator"
version: 2
status: draft
created: 2026-08-27
updated: 2026-08-28
context_type: greenfield
product_type: web-app
target_scale:
  users: small
  qps: low
  data_volume: large
timeline_budget:
  mvp_weeks: 1
  hard_deadline: null
  after_hours_only: null
---

## Vision & Problem Statement

Fundacja Życie Warte Jest Rozmowy (ZWJR) pomaga osobom w kryzysie samobójczym i ich bliskim — rocznie dziesiątki tysięcy osób, ok. 50 osób personelu. Część działań finansuje z grantów (np. Ministerstwo Edukacji Narodowej) i rozlicza się z nich wynikami ankiet i testów wiedzy. W ramach programu „Wspierająca Szkoła" fundacja prowadzi szkolenia dla kadry oświaty, webinary i spotkania edukacyjne; każde szkolenie poprzedza i kończy ankieta sprawdzająca wiedzę, a po szkoleniu wypełniana jest ankieta ewaluacyjna. W zeszłym roku jeden projekt wymagał raportów z ankiety „klimat szkoły" dla 55 placówek — po 52 pytania (połowa otwartych), ok. 100 odpowiedzi na szkołę.

Dziś zespół raportujący ręcznie liczy wyniki, kopiuje wykresy z formularzy ankietowych i pisze pod każdym wykresem opis w języku formalnym, ale przystępnym. Do rozliczenia grantu trzeba wykazać, że min. 75% uczestników przekroczyło próg punktowy oraz przedstawić wzrost wiedzy w punktach procentowych między pomiarem „przed" a „po". Przy większych zestawach (np. 55 szkół × dziesiątki pytań) narzędzia czatu „zacinają się" z każdym kolejnym pytaniem. Zespół spędził ok. trzech miesięcy pracy dwóch osób na rozliczenie ankiet z jednego projektu — dokumenty te nie służą potem nikomu, a praca jest powtarzalna i podatna na błędy liczbowe grożące korektą od grantodawcy.

Ewaluator ma zdjąć najbardziej powtarzalną część tej pracy: policzyć wyniki deterministycznie, narysować wykres, napisać opis zgodny z wytycznymi tonu i wygenerować edytowalny dokument raportu — bez zmyślania danych i bez wyglądu eksportu z czatu.

## User & Persona

### Primary persona

**Koordynator raportów ZWJR** (np. Sekretarz Zarządu / członek zespołu raportującego) — pracownik fundacji odpowiedzialny za rozliczanie grantów. Przy każdym rozliczeniu (kilka projektów naraz w szczytowych okresach) musi przygotować raporty z wykresami i opisami dla grantodawcy. Jeden duży projekt to ok. 3 miesiące pracy 2 osób w roku; problem dotyka min. 5 osób w fundacji. Osoba nie jest programistką — oczekuje wgrywania plików i pobierania gotowego dokumentu.

### Secondary persona

**Opiekun merytoryczny grantu** — weryfikuje poprawność liczb, progu 75% i tonu wniosków przed wysłaniem raportu do grantodawcy. Potrzebuje możliwości ręcznej korekty wygenerowanego dokumentu.

## Success Criteria

### Primary

- Po wgraniu eksportu wyników „przed" i „po" dla jednego pytania zamkniętego (test wiedzy) narzędzie generuje wykres słupkowy z liczbami i odsetkami oraz opis zmiany w **punktach procentowych** — zgodny z wytycznymi ZWJR (formalny ton, ostrożne wnioski, bez zmyślania).
- Wygenerowane liczby w opisie w 100% zgadzają się z danymi źródłowymi (zweryfikowalne na przykładowym eksporcie „Dobrostan psychiczny nauczyciela").
- Zespół pobiera w pełni edytowalny dokument raportu wyglądający jak standardowy dokument biurowy ZWJR (A4, numeracja stron, sekcja per pytanie: treść pytania + wykres + opis).

### Secondary

- Narzędzie przetwarza całą ankietę (wiele pytań) porcjami bez „zacinania się" na dużych wolumenach (referencja: 1000+ respondentów × wiele pytań).
- Dla pytań otwartych: grupowanie tematyczne z liczbą i odsetkiem odpowiedzi w każdej kategorii plus anonimowe cytaty z ankiet.
- Raport końcowy zawiera podsumowanie sekcji (największy/najmniejszy wzrost, odsetek powyżej progu) — wzorowane na istniejących raportach referencyjnych.

### Guardrails

- Błędnie policzony wzrost wiedzy lub zmyślony opis wykresu trafia do raportu grantodawcy i grozi korektą — liczby muszą w 100% zgadzać się z danymi.
- Gdy dane są niepełne lub niejednoznaczne, narzędzie oznacza problem zamiast uzupełniać braki.
- Opisy nie stwierdzają przyczynowości („szkolenie spowodowało…"), gdy dane tego nie uzasadniają — stosować formuły: „wyniki wskazują", „odnotowano wzrost", „wyniki mogą sugerować".
- Gdy liczba respondentów przed ≠ po, opis informuje o różnicy i opiera porównanie na odsetkach, nie na samych liczbach bezwzględnych.
- Tekst nie nosi rozpoznawalnych znamion automatycznego generowania; format nie wygląda jak eksport z czatu.
- Duże wolumeny — projektować pod przetwarzanie porcjami od początku.

## User Stories

### US-01: Raport z jednego pytania testu wiedzy (demo)

- **Given** koordynator ma dwa eksporty ankiet (preankieta i postankieta) z jednym wspólnym pytaniem zamkniętym jednokrotnego wyboru
- **When** wgrywa oba pliki i wskazuje pytanie do raportu
- **Then** narzędzie generuje wykres słupkowy z rozkładem odpowiedzi przed i po (spójne kolory „przed"/„po")
- **And** pod wykresem pojawia się opis: treść pytania, liczba i odsetek każdej odpowiedzi, wskazanie odpowiedzi prawidłowej, porównanie przed/po w punktach procentowych, krótki wniosek
- **And** koordynator pobiera fragment raportu jako edytowalny dokument

#### Acceptance Criteria

- Opis dla pytania „Które stwierdzenie jest nieprawdziwe?" daje te same odsetki co ręczne przeliczenie z pliku referencyjnego (np. 69,2% → 75,5% = wzrost o 6,3 p.p.)
- Wykres pokazuje wartości liczbowe i procentowe
- Opis nie powtarza mechanicznie wszystkich wartości z wykresu — skupia się na najważniejszych wynikach i różnicach

### US-02: Pełny raport z testu wiedzy dla jednego szkolenia

- **Given** koordynator ma eksporty pre- i postankiety dla jednego szkolenia (np. „Uczeń w kryzysie psychicznym")
- **When** uruchamia generowanie pełnego raportu
- **Then** dokument zawiera sekcje: wstęp programu, lista szkół/uczestników (jeśli dane dostępne), metryczka respondentów, każde pytanie wiedzy z wykresem i opisem, podsumowanie sekcji
- **And** dla każdego pytania widoczna jest liczba udzielonych odpowiedzi i zwrot (np. „1073 osób, ok. 85,0% zwrotu")

### US-03: Raport z ankiety ewaluacyjnej (pytania otwarte)

- **Given** koordynator ma postankietę ewaluacyjną z pytaniami otwartymi (np. „Co należałoby zmienić?", „Co było najcenniejsze?")
- **When** generuje raport
- **Then** odpowiedzi otwarte są pogrupowane według najczęstszych tematów
- **And** przy każdej kategorii podana jest liczba przypisanych wypowiedzi i odsetek
- **And** pojawiają się krótkie anonimowe cytaty respondentów; pojedyncze głosy oznaczone jako głos jednostkowy

#### Acceptance Criteria

- Kategorie i podsumowania wynikają wyłącznie z treści odpowiedzi — narzędzie nie tworzy cytatów ani nie zmienia ich znaczenia
- Pojedyncze wypowiedzi (1–2 razy) nie są prezentowane jako stanowisko całej grupy

### US-04: Analiza progu 75% dla rozliczenia grantu

- **Given** koordynator ma wyniki pre- i postankiety z oceną poprawności odpowiedzi per uczestnik
- **When** uruchamia obliczenie progu grantowego
- **Then** raport pokazuje odsetek uczestników, którzy przekroczyli próg punktowy
- **And** wynik jest oznaczony jako „spełniony/niespełniony" względem wymogu 75%

#### Acceptance Criteria

- Obliczenie jest deterministyczne i audytowalne — każda liczba da się prześledzić do danych źródłowowych
- # TODO: dokładna definicja progu per uczestnik — see Open Questions

### US-05: Kwestionariusz wielowymiarowy (klimat szkoły)

- **Given** koordynator ma eksport kwestionariusza ze skalą Likerta (1–5) i pytaniami otwartymi (referencja: 52 pytania, ~100 odpowiedzi na szkołę)
- **When** generuje raport porcjami
- **Then** pytania skalarne dostają wykresy rozkładu ze średnią/mediana; pytania otwarte — grupowanie tematyczne
- **And** przetwarzanie nie wymaga wrzucenia wszystkich pytań naraz

## Functional Requirements

### Import i rozpoznawanie danych

- FR-001: Koordynator raportów can wgrać surowy eksport CSV z Google Forms, bez wcześniejszej agregacji — jedyna czynność użytkownika to wskazanie kolumny odpowiadającej pytaniu do raportu. Priority: must-have
- FR-002: Koordynator raportów can wgrać parę surowych eksportów CSV „przed" i „po" dla testu wiedzy (bez wspólnego identyfikatora uczestnika — patrz Business Logic). Priority: must-have
- FR-003: Narzędzie can rozpoznać kolumny metadanych (sygnatura czasowa, zgoda RODO, stanowisko, staż pracy, szkoła) i oddzielić je od kolumn pytań. Priority: must-have
- FR-004: Narzędzie can rozpoznać typ pytania: zamknięte jednokrotnego wyboru, skala oceny, otwarte. Priority: must-have

### Wykresy i opisy — rdzeń demo

- FR-005: Koordynator raportów can wygenerować wykres słupkowy porównujący odpowiedzi „przed" i „po" dla jednego pytania zamkniętego. Priority: must-have
- FR-006: Narzędzie can wyświetlić pod wykresem opis zawierający: treść pytania, liczbę i odsetek odpowiedzi, odpowiedź prawidłową (dla testów wiedzy), porównanie przed/po w punktach procentowych, krótki wniosek. Priority: must-have
- FR-007: Narzędzie can oznaczyć kolorystycznie wyniki „przed" i „po" spójnie w całym raporcie. Priority: must-have
- FR-008: Koordynator raportów can pobrać wygenerowany fragment lub pełny raport jako edytowalny dokument biurowy. Priority: must-have

### Testy wiedzy (pre/post)

- FR-009: Narzędzie can policzyć odsetek odpowiedzi prawidłowych, błędnych i „Nie wiem" przed i po szkoleniu dla każdego pytania. Priority: must-have
- FR-010: Narzędzie can obliczyć różnicę odsetków w punktach procentowych (nie procentach względnych). Priority: must-have
- FR-011: Narzędzie can wygenerować metryczkę respondentów (struktura stanowisk, staż pracy) z tabelą liczbową i opisem. Priority: nice-to-have
- FR-012: Narzędzie can wygenerować podsumowanie sekcji szkolenia (największy/najmniejszy wzrost, obszary bez poprawy). Priority: nice-to-have
- FR-013: Narzędzie can obliczyć odsetek uczestników przekraczających próg punktowy (wymóg grantowy 75%). Priority: must-have

### Ankiety ewaluacyjne

- FR-014: Narzędzie can przetworzyć pytania skalarne (ocena 1–5) z rozkładem i opisem. Priority: nice-to-have
- FR-015: Narzędzie can pogrupować odpowiedzi otwarte według tematów z liczbą i odsetkiem per kategoria. Priority: nice-to-have
- FR-016: Narzędzie can dołączyć anonimowe cytaty respondentów z oznaczeniem głosów jednostkowych. Priority: nice-to-have

### Struktura dokumentu wyjściowego

- FR-017: Dokument raportu can zawierać osobną sekcję per pytanie: pełna treść pytania, wykres, liczba odpowiedzi, opis wyników. Priority: must-have
- FR-018: Dokument raportu can zawierać numerację stron, miejsce na stronę tytułową i spis treści. Priority: nice-to-have
- FR-019: Dokument raportu can być formatowany zgodnie z dostarczonymi wytycznymi ZWJR (A4, pion, spójne marginesy i odstępy, czcionka i rozmiar z wytycznych). Priority: must-have

### Obsługa błędów i skala

- FR-020: Narzędzie can oznaczyć niepełne lub niejednoznaczne dane zamiast je uzupełniać. Priority: must-have
- FR-021: Narzędzie can informować w opisie, gdy liczba respondentów przed ≠ po. Priority: must-have
- FR-022: Narzędzie can przetwarzać ankiety porcjami (pytanie po pytaniu lub partia pytań). Priority: must-have

### Rozszerzenia (poza MVP)

- FR-023: Koordynator raportów can połączyć narzędzie z zewnętrznym źródłem ankiet (integracja z formularzami). Priority: nice-to-have
- FR-024: Narzędzie can wygenerować wnioski i rekomendacje na końcu raportu. Priority: nice-to-have
- FR-025: Narzędzie can dopasować styl raportu do wymagań różnych grantodawców (szablony). Priority: nice-to-have

## Non-Functional Requirements

- Liczby w raporcie muszą w 100% zgadać się z danymi źródłowymi; każde obliczenie musi być audytowalne.
- Narzędzie przetwarza duże ankiety porcjami — referencja skali: 1000+ respondentów, 50+ pytań, 55 placówek bez utraty stabilności.
- Wygenerowany dokument jest w pełni edytowalny przez koordynatora po pobraniu (nie „zablokowany" eksport).
- Tekst opisów brzmi naturalnie — bez powtarzalnych szablonów, przesadnie rozbudowanych podsumowań i zbyt stanowczych stwierdzeń bez podstaw w danych.
- Czas generowania jednego pytania (wykres + opis) ≤ 30 s na referencyjnym eksporcie demo.
- # TODO: wymagania dostępności i RODO (dane osobowe w eksportach) — see Open Questions

## Business Logic

Rozliczenie grantu wymaga wykazania, że co najmniej 75% uczestników przekroczyło próg punktowy oraz przedstawienia wzrostu wiedzy między pomiarem „przed" a „po".

**Rozstrzygnięcie: brak dopasowania per uczestnik.** Surowe eksporty CSV z Google Forms (preankieta i postankieta) nie zawierają wspólnego identyfikatora uczestnika (brak imienia/e-maila/ID łączącego wiersz „przed" z wierszem „po"). Dlatego zarówno wzrost wiedzy, jak i próg 75% liczone są **agregatowo na poziomie pytania/grupy, nie per osoba**: dla każdego pytania porównuje się % odpowiedzi poprawnych w „przed" vs % odpowiedzi poprawnych w „po" w obrębie całej grupy (np. szkoły lub szkolenia); próg 75% odnosi się do odsetka poprawnych odpowiedzi w agregacie postankiety, a nie do dopasowanego wzrostu każdej pojedynczej osoby. Jeśli w przyszłości pojawi się plik z faktycznym identyfikatorem uczestnika, dopasowanie per osoba stanie się możliwe — ale MVP zakłada wejście bez takiego klucza.

Dla każdego pytania zamkniętego w teście wiedzy: raport podaje liczbę i odsetek każdej odpowiedzi przed i po, wskazuje odpowiedź prawidłową, oblicza różnicę odsetka odpowiedzi prawidłowych w **punktach procentowych** (np. wzrost z 69,2% do 75,5% = +6,3 p.p., nie „wzrost o 9%"). Gdy liczba respondentów przed ≠ po, opis informuje o różnicy i opiera porównanie na odsetkach.

Opis pod wykresem: formalny i rzeczowy, ale przystępny; skupia się na najważniejszych wynikach (nie powtarza wszystkich wartości z wykresu); kończy krótkim wnioskiem wynikającym bezpośrednio z danych. Wnioski formułowane ostrożnie — bez stwierdzeń przyczynowych wobec szkolenia, chyba że dane to uzasadniają. Granica tonu „pokazywanie osiągnięć" vs zmyślanie jest w pełni domknięta wytycznymi w `context/foundation/sources/Wytyczne dotyczące tonu i formatu raportu.md` (sformułowania typu „wyniki wskazują", „może to świadczyć o…") — nie wymaga dalszych ustaleń.

Dla pytań otwartych: grupowanie według najczęstszych tematów; liczba i odsetek wypowiedzi per kategoria; cytaty anonimowe i dosłowne z ankiet; pojedyncze głosy oznaczone jako jednostkowe.

Do każdego pytania w raporcie: pełna treść pytania + wykres + liczba odpowiedzi + opis. „Musi być i wykres, i tekst."

## Access Control

MVP: narzędzie dla wewnętrznego zespołu ZWJR (~5 osób); bez logowania — dostęp ograniczony do zaufanych użytkowników (lokalnie lub w zamkniętym środowisku). Eksporty ankiet zawierają dane osobowe (imię, nazwisko, e-mail w metryczce) — narzędzie nie powinno przechowywać ich dłużej niż potrzeba do generowania raportu.

# TODO: model auth i RODO dla wersji produkcyjnej — see Open Questions

## Non-Goals

- Nie budujemy rozwiązania opartego na wrzuceniu całego zestawu pytań naraz do narzędzia czatu.
- Integracja z zewnętrznym źródłem ankiet — poza MVP (FR-023).
- Automatyczne pisanie wniosków końcowych i rekomendacji bez weryfikacji — poza MVP (FR-024).
- Szablony per grantodawca — poza MVP (FR-025).
- Pełny raport wieloszkoleniowy (113 stron jak referencja) — poza rdzeniem demo; MVP dowodzi jednego pytania, następny krok to cała ankieta jednego szkolenia.
- Kwestionariusz klimatu szkoły (52 pytania × 55 placówek) — poza MVP demo; architektura porcjowa od początku.

## Open Questions

1. **Odpowiedź prawidłowa per pytanie** — Skąd narzędzie bierze klucz odpowiedzi (konfiguracja ręczna, plik metadanych, wykrywanie z treści pytania)? Owner: user. Block: yes (demo testu wiedzy).
2. **Boilerplate wstępu programu** — Czy generować stały blok opisowy programu „Wspierająca Szkoła" z szablonu, czy użytkownik wkleja ręcznie? Owner: user. Block: no.
3. **RODO i przechowywanie danych** — Jak długo trzymać wgrywane pliki z danymi osobowymi? Czy anonimizować przed zapisem? Owner: user / DPO. Block: yes (produkcja).
4. **Model auth w wersji produkcyjnej** — Logowanie Google Workspace ZWJR? Owner: user. Block: no (MVP bez auth).
5. **Grupowanie odpowiedzi otwartych — algorytm vs. ręczna weryfikacja** — Czy koordynator zatwierdza kategorie przed exportem? Owner: user. Block: no (nice-to-have).

### Resolved

- **Próg punktowy i mapowanie uczestników pre/post** — Surowe eksporty CSV nie mają wspólnego identyfikatora uczestnika; próg 75% i wzrost wiedzy liczone są agregatowo na poziomie pytania/grupy, nie per osoba. Zapisane w `## Business Logic`.
- **Format pliku wejściowego** — Dwa surowe eksporty CSV z Google Forms (bez wcześniejszej agregacji przez użytkownika, poza wskazaniem kolumny pytania). Zapisane w FR-001/FR-002.
- **Granica tonu „pokazywania osiągnięć" vs zmyślanie** — W pełni domknięta przez `context/foundation/sources/Wytyczne dotyczące tonu i formatu raportu.md`; nie wymaga dodatkowych ustaleń.
