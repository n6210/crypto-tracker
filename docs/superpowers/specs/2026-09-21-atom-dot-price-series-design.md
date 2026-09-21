# Szeregi cen ATOM i DOT — specyfikacja

## Cel
Dodać do wykresu ATOM/DOT dwa dodatkowe szeregi cen w USDT: ATOM i DOT. Każdy szereg ma półprzezroczystą linię łączącą punkty oraz mniejsze, wypełnione punkty w osobnym kolorze.

## Zatwierdzony wygląd
- Wariant A: kurs ATOM/DOT korzysta z lewej osi, a ceny ATOM i DOT z osobnej prawej osi `USDT`.
- ATOM: zielony `#2ea043`, punkty wypełnione, promień 3; linia `rgba(46, 160, 67, 0.58)`, szerokość 2.
- DOT: fioletowy `#bc8cff`, punkty wypełnione, promień 3; linia `rgba(188, 140, 255, 0.58)`, szerokość 2.
- Istniejący szereg kursu ATOM/DOT pozostaje niebieski i zachowuje swoje punkty oraz pionową linię aktywnego punktu.
- Legenda pokazuje ATOM i DOT; nie zmienia działania dymka z kursami.

## Dane i skala
- Do wykresu przekazywane są tablice `atomPrices` i `dotPrices` z cen zamknięcia odpowiednich k-line.
- Punkty są dopasowane indeksami do wspólnych etykiet czasowych.
- Prawa oś `yPrice` ma etykiety w USDT i niezależny zakres, aby ceny nie zniekształcały skali kursu ATOM/DOT.
- Dla brakujących lub niepoprawnych danych szereg cenowy nie jest rysowany; pozostałe szeregi działają niezależnie.

## Implementacja
- Zmiana ograniczona do `index.html`.
- W `loadData()` pobrane ceny są zapisywane do `atomPrices` i `dotPrices`.
- W konfiguracji Chart.js dodane są dwa zestawy danych z `yAxisID: 'yPrice'`, `showLine: true`, `fill: false`, `borderColor`, `pointBackgroundColor`, `pointRadius: 3` i `pointHoverRadius: 5`.
- W `options.scales` dodana jest prawa oś `yPrice` z `position: 'right'`, `grid.drawOnChartArea: false` i etykietami `$x.xx`.
- Legenda jest włączona i pokazuje wyłącznie ATOM oraz DOT; główny szereg kursu pozostaje oznaczony niebieskim kolorem, ale nie duplikuje etykiety w legendzie.

## Testowanie
- Statyczna kontrola składni JavaScript.
- Test mock pluginu/configuracji sprawdzający obecność obu szeregów, `yAxisID`, kolory, promienie punktów, przezroczystość linii i prawą oś.
- Ręczna weryfikacja w przeglądarce po załadowaniu danych: trzy szeregi są widoczne, linie łączą punkty, a lewa i prawa oś mają czytelne zakresy.
