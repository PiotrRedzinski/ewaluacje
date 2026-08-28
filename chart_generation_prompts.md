# Prompty do generowania wykresów z ankiet ewaluacyjnych (LLM API)

## Założenia projektowe

- **Wejście**: pojedyncze pytanie ankietowe + jego wyniki (etykiety odpowiedzi, liczby/procenty, n).
- **Wyjście**: JSON zawierający (1) specyfikację wykresu (typ, etykiety, wartości) — do narysowania przez dowolną bibliotekę wykresów (matplotlib / Chart.js / python-docx), oraz (2) krótki opis tekstowy do wstawienia pod wykresem w raporcie Word.
- **Twarda zasada**: LLM nigdy nie wymyśla ani nie doszacowuje liczb, których nie dostał. Jeśli dane są niepełne (np. podano tylko procenty, bez liczb bezwzględnych) albo sumy się nie zgadzają, model ma to zgłosić w polu `warnings`, a nie zgadywać.
- **Ton opisu**: zwykły, rzeczowy język raportu ewaluacyjnego (jak w źródłowym dokumencie), bez fraz charakterystycznych dla tekstu pisanego przez AI ("podsumowując", "warto zauważyć", list punktowanych w opisie, emoji).
- Font (Times New Roman) i styl dokumentu Word to sprawa warstwy renderowania `.docx`, nie promptu — nie umieszczamy tego w JSON-ie, tylko stosujemy downstream.

Jeśli wolisz, żeby model od razu zwracał gotowy kod (np. matplotlib) albo konfigurację Chart.js zamiast surowego JSON-u ze specyfikacją — daj znać, podmienię schemat wyjścia.

---

## 1. SYSTEM PROMPT (stały, wysyłany przy każdym wywołaniu)

```
Jesteś modułem generującym wykresy i opisy do raportów z ankiet ewaluacyjnych szkoleń
i webinarów fundacji edukacyjnej. Otrzymujesz dane jednego pytania ankietowego
(etykiety odpowiedzi, liczby i/lub procenty, liczbę respondentów n) i zwracasz WYŁĄCZNIE
poprawny JSON zgodny z podanym schematem — bez żadnego tekstu poza JSON-em.

Zasady bezwzględne:
1. Używaj wyłącznie liczb podanych w danych wejściowych. Nie zaokrąglaj, nie przeliczaj,
   nie "dopasowuj" wartości, żeby się sumowały do 100% — jeśli się nie sumują, zgłoś to
   w polu "warnings", ale wartości przepisz dokładnie takie, jakie dostałeś.
2. Jeśli podano tylko procenty bez liczb bezwzględnych (lub odwrotnie), NIE dolicz
   brakującej wartości samodzielnie z n (bo to obarczone błędem zaokrągleń) — użyj
   tylko tego, co dostałeś, i zaznacz brak w "warnings".
3. Pole "opis" to 2-4 zdania w języku polskim, w tonie formalnego raportu ewaluacyjnego
   (jak w dokumentach źródłowych fundacji). Zakaz: słów "podsumowując", "warto
   zauważyć", "generalnie", zwrotów w stylu czatbota, list punktowanych, emoji,
   nadmiernych przymiotników. Liczby w opisie muszą być identyczne z danymi wejściowymi.
4. Dobierz typ wykresu do typu pytania:
   - pytanie tak/nie lub jednokrotnego wyboru z 2-3 opcjami → "pie"
   - pytanie skalowe (4-punktowe: zdecydowanie tak / raczej tak / raczej nie /
     zdecydowanie nie) → "bar" (poziomy lub pionowy)
   - pytanie na skali 1-5 lub 0-10 → "bar" (słupki po jednej wartości skali)
   - porównanie tego samego pytania między kilkoma grupami/szkoleniami → "grouped_bar"
5. Zwracaj TYLKO JSON. Żadnego markdownu, żadnych komentarzy przed/po.
```

---

## 2. SCHEMAT WYJŚCIOWY (JSON)

```json
{
  "chart": {
    "type": "pie | bar | grouped_bar",
    "title": "string",
    "labels": ["string", "..."],
    "series": [
      {
        "name": "string (np. nazwa szkolenia, jeśli grouped_bar; inaczej pomiń)",
        "values": [0, 0, "..."],
        "value_type": "count | percentage"
      }
    ],
    "n": 0
  },
  "opis": "string (2-4 zdania, PL)",
  "warnings": ["string", "..."]
}
```

---

## 3. GOTOWE PROMPTY UŻYTKOWNIKA (wypełnione realnymi danymi z raportu)

Każdy z poniższych to gotowy `user` message do wysłania razem z powyższym `system` promptem.

### Przykład 1 — pytanie tak/nie (wykres kołowy)
Źródło: część I, „Uczeń w kryzysie psychicznym…"

```
Pytanie ankietowe: "Czy forma przekazywania treści podczas spotkania była dla Pani/Pana odpowiednia?"
Typ pytania: jednokrotny wybór (Tak / Nie)
Szkolenie: "Uczeń w kryzysie psychicznym – jak go rozpoznać i wspierać?"
Liczba respondentów (n): 976

Wyniki:
Tak: 965 (98,9%)
Nie: 11 (1,1%)

Wygeneruj JSON zgodny ze schematem.
```

### Przykład 2 — skala 4-punktowa, tylko procenty podane (test na "nie zgaduj")
Źródło: część I, to samo szkolenie

```
Pytanie ankietowe: "Czy treści przekazywane były w sposób zrozumiały?"
Typ pytania: skala 4-punktowa
Szkolenie: "Uczeń w kryzysie psychicznym – jak go rozpoznać i wspierać?"
Liczba respondentów (n): 976

Wyniki (podano wyłącznie procenty, liczby bezwzględne nie zostały przekazane):
Zdecydowanie tak: 85,0%
Raczej tak: 14,8%
Raczej nie: 0,1%
Zdecydowanie nie: 0,1%

Wygeneruj JSON zgodny ze schematem.
```
*(To celowy przypadek testujący regułę 2 z system promptu — model powinien użyć `value_type: "percentage"` i dopisać w `warnings`, że liczb bezwzględnych nie podano, zamiast je wyliczać z n.)*

### Przykład 3 — skala 1-5 z pełnymi liczbami
Źródło: część I

```
Pytanie ankietowe: "Dzięki spotkaniu lepiej rozumiem problemy, z jakimi borykają się nastolatki."
Typ pytania: skala 1-5 (1 = zdecydowanie się nie zgadzam, 5 = zdecydowanie się zgadzam)
Szkolenie: "Uczeń w kryzysie psychicznym – jak go rozpoznać i wspierać?"
Liczba respondentów (n): 976

Wyniki:
1: 10
2: 7
3: 39
4: 221
5: 699

Wygeneruj JSON zgodny ze schematem.
```

### Przykład 4 — skala 0-10 (ocena ogólna spotkania)
Źródło: część I

```
Pytanie ankietowe: "Ogólna ocena spotkania" (skala 0-10, 0 = bardzo negatywna, 10 = bardzo pozytywna)
Szkolenie: "Uczeń w kryzysie psychicznym – jak go rozpoznać i wspierać?"
Liczba respondentów (n): 976

Wyniki:
1: 1
2: 0
3: 3
4: 2
5: 7
6: 9
7: 23
8: 99
9: 198
10: 634

Wygeneruj JSON zgodny ze schematem.
```

### Przykład 5 — webinar, mała próba, skala 1-5
Źródło: część V, webinar „Pierwsza pomoc emocjonalna…"

```
Pytanie ankietowe: "Poleciłabym/poleciłbym to spotkanie innym nauczycielom"
Typ pytania: skala 1-5
Wydarzenie: webinar "Pierwsza pomoc emocjonalna: jak rozpoznać kryzys samobójczy u ucznia
i jak powołać zespół kryzysowy w szkole?"
Liczba respondentów (n): 259

Wyniki:
1: 0
2: 0
3: 13
4: 31
5: 215

Wygeneruj JSON zgodny ze schematem.
```

### Przykład 6 — porównanie między szkoleniami (grouped_bar)
Źródło: części I-IV, ten sam wskaźnik ("ocena 10/10") w czterech różnych szkoleniach — pokazuje, jak łączyć wiele ankiet w jeden wykres porównawczy.

```
Pytanie porównawcze: "Odsetek respondentów, którzy ocenili spotkanie na 10/10"
Typ wykresu: porównanie między szkoleniami (grouped_bar)

Dane:
- "Uczeń w kryzysie psychicznym" (n=976): 65,0% (634 osoby)
- "Rodzice ucznia w kryzysie psychicznym" (n=964): 55,9% (539 osób)
- "Uczeń po próbie samobójczej" (n=950): 60,6% (567 osób)
- "Dobrostan psychiczny nauczyciela" (n=882): 59,4% (524 osoby)

Wygeneruj JSON zgodny ze schematem, gdzie każde szkolenie to osobna seria/słupek.
```

---

## 4. BONUS — szablon pod właściwy „rdzeń" projektu (test wiedzy przed/po)

To nie pochodzi z ankiet ewaluacyjnych z raportu (tam nie ma danych przed/po), ale odpowiada głównemu przypadkowi użycia z PRD: wykres słupkowy porównujący wynik testu wiedzy przed i po szkoleniu, z automatycznym wyliczeniem zmiany w punktach procentowych. Struktura promptu jest analogiczna, tylko z dwiema seriami danych i dodatkowym polem `zmiana_pp` w opisie.

```
Pytanie testu wiedzy: "{treść pytania zamkniętego}"
Typ pytania: jednokrotny wybór, poprawna odpowiedź: "{poprawna odpowiedź}"
Liczba respondentów PRZED: {n_przed}
Liczba respondentów PO: {n_po}

Wyniki PRZED:
{opcja_1}: {liczba} ({procent}%)
{opcja_2}: {liczba} ({procent}%)
...

Wyniki PO:
{opcja_1}: {liczba} ({procent}%)
{opcja_2}: {liczba} ({procent}%)
...

Wygeneruj JSON zgodny ze schematem (chart.type = "grouped_bar", dwie serie: "przed" i "po"),
a w polu "opis" podaj zmianę w punktach procentowych dla poprawnej odpowiedzi, wyliczoną
WYŁĄCZNIE z podanych liczb (procent_po - procent_przed). Jeśli n_przed ≠ n_po, zaznacz to
w "warnings" — nie zakładaj, że próby są porównywalne bez ostrzeżenia.
```

---

## 5. Uwaga dot. wdrożenia

- Jeśli dane wejściowe pochodzą z CSV (jak w PRD — eksport z Google Forms), warto zbudować mały parser, który sam wylicza `n`, liczby i procenty z surowych odpowiedzi, zamiast przepisywać je ręcznie do promptu jak w powyższych przykładach — ograniczy to ryzyko literówek trafiających do raportu.
- Ponieważ liczby muszą się zgadzać w 100% z danymi (koszt błędu z PRD), warto po stronie aplikacji **zwalidować** JSON zwrócony przez model: porównać `chart.series[].values` z danymi wejściowymi 1:1, i odrzucić/przegenerować odpowiedź, jeśli się nie zgadzają — nie polegać wyłącznie na instrukcji w prompcie.
