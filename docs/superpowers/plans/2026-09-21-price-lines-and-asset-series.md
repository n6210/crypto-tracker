# Linie cen aktywów i etykiety wartości — plan implementacji

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Dodać do wykresu ATOM/DOT osobne szeregi cen ATOM i DOT na prawej osi USDT oraz liczbowe etykiety aktualnej i średniej ceny na istniejących liniach pomocniczych.

**Architecture:** `loadData()` zbuduje wspólną oś czasu z timestampów obu k-line, zapisze ceny i kurs z dopasowaniem po timestampach oraz obsłuży braki jako `null`. Konfiguracja Chart.js doda dwa szeregi cenowe z osobną osią `yPrice`, a `priceLinesPlugin` obliczy poprawne wartości, narysuje linie i etykiety oraz zachowa aktywny punkt głównego kursu.

**Tech Stack:** HTML, CSS, JavaScript, Chart.js, Binance K-lines API, Node.js do testów statycznych/mock.

---

### Task 1: Przygotować test RED

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html:172-371`
- Test: `/tmp/opencode/verify-chart.js` (tymczasowy test poza repozytorium)

- [ ] **Step 1: Napisać test mock**

Test odczyta `<script>` z `index.html`, uruchomi go z mockami `document`, `Chart`, `fetch` i `setInterval`, a następnie sprawdzi:
- istnienie dokładnie trzech datasetów;
- etykiety i kolory `ATOM`/`DOT`, `yAxisID: 'yPrice'`, `pointRadius: 2`, `pointHoverRadius: 4`, `spanGaps: false`, `fill: false`, `borderWidth: 2` i dokładne półprzezroczyste `borderColor`;
- prawą oś `yPrice` z `title.display: true`, `title.text: 'USDT'`, `grid.drawOnChartArea: false` i etykietami `x.xx USDT`;
- legendę zwracającą dokładnie `ATOM` i `DOT`;
- `tooltip.filter` ograniczony do `datasetIndex === 0` oraz `tooltip: { enabled: false }` i `pointHitRadius: 0` dla szeregów cen;
- dopasowanie timestampów w `loadData()`, `null` dla braków i resetowanie `hidden` po zmianie danych;
- plugin rysujący etykiety z `fillText`, `textAlign: 'left'`, `textBaseline: 'middle'`, `toFixed(2)`, dokładnym tłem i odsunięciem przy kolizji.

- [ ] **Step 2: Uruchomić test i potwierdzić RED**

Run: `node /tmp/opencode/verify-chart.js`

Expected: FAIL, ponieważ `index.html` zawiera obecnie tylko jeden dataset i nie ma osi `yPrice`, dopasowania timestampów ani etykiet.

### Task 2: Przygotować dopasowane dane cenowe

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html:294-354`

- [ ] **Step 1: Dodać parser poprawnych cen**

Dodać pomocniczą funkcję `parseClose(value)`, która zwraca skończoną liczbę albo `null`, oraz `lastValidValue(data)`, która zwraca ostatnią poprawną liczbę z tablicy.

- [ ] **Step 2: Zbudować dane po timestampach**

W `loadData()` utworzyć mapy timestamp → cena dla ATOM i DOT, posortowaną unikalną listę timestampów oraz tablice `labels`, `ratios`, `atomPrices`, `dotPrices`. Dla każdego timestampu zapisać `null`, gdy cena lub drugi szereg jest niedostępny; kurs zapisywać tylko wtedy, gdy obie ceny są poprawne.

- [ ] **Step 3: Przygotować aktualizację nagłówka**

Obliczyć ostatnie poprawne ceny ATOM i DOT oraz zapisać je w nagłówku; nie odwoływać się jeszcze do datasetów, które zostaną dodane w Task 3.

- [ ] **Step 4: Uruchomić test dopasowania danych**

Run: `node /tmp/opencode/verify-chart.js --data-only`

Expected: PASS dla map timestampów, posortowanej osi czasu, `null`, `ratios` oraz ostatnich poprawnych cen; test nie wymaga jeszcze konfiguracji wykresu.

### Task 3: Skonfigurować wykres z osobną osią USDT

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html:193-292`
- Modify: `/home/gozdekt/Pobrane/kurs/index.html:294-354`

- [ ] **Step 1: Dodać datasety cen**

W konfiguracji `initChart()` dodać dataset `ATOM` i `DOT` z `yAxisID: 'yPrice'`, `showLine: true`, `fill: false`, `spanGaps: false`, `borderWidth: 2`, `pointBackgroundColor` odpowiednio `#2ea043` i `#bc8cff`, `pointRadius: 2`, `pointHoverRadius: 4`, `tooltip: { enabled: false }` i `pointHitRadius: 0`. Ustawić `borderColor` odpowiednio na `rgba(46, 160, 67, 0.58)` i `rgba(188, 140, 255, 0.58)`; główny dataset pozostaje niebieski.

- [ ] **Step 2: Dodać prawą oś USDT**

Dodać `yPrice` z `position: 'right'`, `title.display: true`, `title.text: 'USDT'`, `grid.drawOnChartArea: false` i callbackiem formatującym `x.xx USDT`; zachować istniejącą lewą oś `y` dla kursu.

- [ ] **Step 3: Ograniczyć legendę i interakcję do głównego kursu**

Włączyć legendę i filtrować ją do etykiet `ATOM` i `DOT`. Przed utworzeniem wykresu zarejestrować niestandardowy tryb interakcji `primaryIndex`, który przekazuje do wbudowanego trybu `index` wyłącznie metadane datasetu `0`; ustawić `interaction: { mode: 'primaryIndex', intersect: false }`. Dodać `plugins.tooltip.filter` ograniczający elementy do `datasetIndex === 0`, a w pluginie wybierać wyłącznie aktywne elementy z `datasetIndex === 0`; `tooltip: { enabled: false }` i `pointHitRadius: 0` dla cen dodatkowo ograniczają ich udział w hoverze, filtr tooltipa gwarantuje, że dymek zawiera tylko główny kurs, a test aktywnych elementów potwierdza ignorowanie szeregów cen.

- [ ] **Step 4: Aktualizować wszystkie dane i widoczność szeregów**

Po pobraniu danych przypisywać `ratios`, `atomPrices` i `dotPrices` do odpowiednich datasetów, ustawiać `hidden` na `false` dla datasetu z chociażby jednym poprawnym punktem i na `true` dla datasetu bez poprawnych punktów, a następnie wywołać `chartInstance.update()`.

- [ ] **Step 5: Uruchomić test konfiguracji i danych**

Run: `node /tmp/opencode/verify-chart.js --config-only`

Expected: PASS dla trzech datasetów, dokładnych kolorów, promieni, `spanGaps`, `fill`, `borderWidth`, prawej osi `USDT`, legendy, tooltipa/hovera oraz resetowania `hidden`; test pomija plugin do Task 4.

### Task 4: Rozszerzyć plugin etykiet i linii

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html:193-228`

- [ ] **Step 1: Dodać obliczenia poprawnych wartości**

W `priceLinesPlugin` obliczać `latestPrice` jako ostatnią poprawną wartość głównego datasetu i `averagePrice` jako średnią arytmetyczną wszystkich poprawnych wartości; dla braku danych nie rysować linii ani etykiet.

- [ ] **Step 2: Zachować pionową linię aktywnego punktu**

Wybierać aktywny element wyłącznie z datasetu głównego (`datasetIndex === 0`), a bez aktywnego punktu używać ostatniego punktu z poprawną wartością.

- [ ] **Step 3: Narysować etykiety liczbowe**

Po liniach rysować etykiety `latestPrice.toFixed(2)` i `averagePrice.toFixed(2)` przy `x = chartArea.left + 6`, `textAlign = 'left'`, `textBaseline = 'middle'`; `y` pochodzi z piksela odpowiedniej linii, a etykiety są sortowane od mniejszego `y` do większego (`top-to-bottom`). W razie kolizji zielona etykieta jest przesuwana w dół o 16 px. Tło ma dokładnie `rgba(14, 17, 23, 0.88)`, padding 3 px, a kolory tekstu to odpowiednio `#ffd700` i `#2ea043`.

- [ ] **Step 4: Uruchomić test mock pluginu i potwierdzić PASS**

Run: `node /tmp/opencode/verify-chart.js`

Expected: PASS; test sprawdza `fillText`, kolory, dokładne współrzędne, `textAlign`, `textBaseline`, `toFixed(2)`, tło, padding, odsunięcie przy kolizji oraz zmianę tekstu po aktualizacji danych.

### Task 5: Wykonać weryfikację statyczną i ręczną

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html`

- [ ] **Step 1: Sprawdzić składnię JavaScript**

Wyodrębnić zawartość `<script>` z `index.html` do `/tmp/opencode/chart-script.js`, następnie uruchomić: `node --check /tmp/opencode/chart-script.js`

Expected: brak błędów składniowych.

- [ ] **Step 2: Sprawdzić różnice i nie śledzone pliki**

Run: `git diff --check && git status --short`

Expected: `git diff --check` bez outputu; zmieniony jest tylko `index.html`, a nieśledzone `.superpowers/`, `docs/superpowers/plans/` i `k.py` pozostają poza committem.

- [ ] **Step 3: Zweryfikować zachowanie w przeglądarce**

Otworzyć `/home/gozdekt/Pobrane/kurs/index.html` w przeglądarce, poczekać na dane Binance i potwierdzić trzy widoczne szeregi, prawą oś `USDT`, brak interpolacji braków, legendę `ATOM`/`DOT` oraz etykiety aktualnej i średniej ceny.

### Task 6: Wykonać lokalny commit implementacji

**Files:**
- Modify: `/home/gozdekt/Pobrane/kurs/index.html`

- [ ] **Step 1: Zacommitować tylko plik produkcyjny**

Run: `git add index.html && git commit -m "feat: add asset price lines and value labels"`

Expected: commit zawiera wyłącznie `index.html`; pliki `.superpowers/`, `docs/superpowers/plans/` i `k.py` pozostają nieśledzone.
