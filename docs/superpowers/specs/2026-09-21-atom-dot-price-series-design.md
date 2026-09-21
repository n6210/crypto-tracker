# Szeregi cen ATOM i DOT — specyfikacja

## Cel
Dodać do wykresu ATOM/DOT dwa dodatkowe szeregi cen w USDT: ATOM i DOT. Każdy szereg ma półprzezroczystą linię łączącą punkty oraz mniejsze, wypełnione punkty w osobnym kolorze.

## Zatwierdzony wygląd
- Wariant A: kurs ATOM/DOT korzysta z lewej osi, a ceny ATOM i DOT z osobnej prawej osi `USDT`.
- ATOM: zielony `#2ea043`, punkty wypełnione, promień 2 (mniejsze od istniejących punktów kursu o promieniu 3); linia `rgba(46, 160, 67, 0.58)`, szerokość 2.
- DOT: fioletowy `#bc8cff`, punkty wypełnione, promień 2; linia `rgba(188, 140, 255, 0.58)`, szerokość 2.
- Istniejący szereg kursu ATOM/DOT pozostaje niebieski i zachowuje swoje punkty oraz pionową linię aktywnego punktu.
- Legenda zawiera dokładnie `ATOM` i `DOT`; istniejący niebieski szereg ATOM/DOT pozostaje poza legendą. Dymek i pionowa linia aktywnego punktu zachowują obecne zachowanie i dotyczą głównego kursu.

## Dane i skala
- Do wykresu przekazywane są tablice `atomPrices` i `dotPrices` z cen zamknięcia (`close`, pole 4) k-line pobranych tym samym interwałem i limitem co główny wykres.
- Punkty są dopasowywane po timestampach; każdy brakujący timestamp jest zapisywany jako `null` na właściwym indeksie wspólnej osi czasu.
- Prawa oś `yPrice` ma tytuł `USDT`, niezależny zakres i etykiety w formacie `x.xx USDT`, aby ceny nie zniekształcały skali kursu ATOM/DOT.
- Brakujące lub niepoprawne wartości tworzą przerwę (`spanGaps: false`) bez interpolacji; dataset jest ukrywany tylko wtedy, gdy nie ma żadnego poprawnego punktu.

## Implementacja
- Zmiana ograniczona do `index.html`.
- W `loadData()` pobrane ceny są zapisywane do `atomPrices` i `dotPrices`.
- W konfiguracji Chart.js dodane są dwa zestawy danych z `yAxisID: 'yPrice'`, `showLine: true`, `fill: false`, `spanGaps: false`, `borderColor`, `pointBackgroundColor`, `pointRadius: 2` i `pointHoverRadius: 4`.
- Szeregi cen są wyłączone z tooltipa i hovera, aby istniejący dymek ATOM/DOT oraz pionowa linia aktywnego punktu działały bez zmian.
- W `options.scales` dodana jest prawa oś `yPrice` z `position: 'right'`, `title.text: 'USDT'`, `grid.drawOnChartArea: false` i etykietami `x.xx USDT`.
- Legenda jest włączona i pokazuje wyłącznie ATOM oraz DOT; główny szereg kursu pozostaje oznaczony niebieskim kolorem, ale nie duplikuje etykiety w legendzie.

## Testowanie
- Statyczna kontrola składni JavaScript.
- Test mock konfiguracji sprawdzający dopasowanie timestampów i `null`, obecność obu szeregów, `yAxisID`, kolory, promienie punktów, przezroczystość linii, `spanGaps: false`, prawą oś `USDT`, wyłączenie szeregów cen z tooltipa/hovera oraz legendę zawierającą dokładnie ATOM i DOT.
- Ręczna weryfikacja w przeglądarce po załadowaniu danych: trzy szeregi są widoczne, linie łączą punkty bez interpolacji braków, a lewa i prawa oś mają czytelne zakresy.
