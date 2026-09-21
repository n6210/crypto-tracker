# Etykiety wartości na liniach ceny — specyfikacja

## Cel
Dodać na wykresie ATOM/DOT dwie czytelne etykiety liczbowe: wartość aktualnej ceny na żółtej linii oraz wartość średniej ceny na zielonej linii.

## Zatwierdzony wygląd
- Wariant B: etykiety umieszczone przy lewej krawędzi obszaru wykresu.
- Etykieta aktualnej ceny: kolor `#ffd700`, format wyniku `toFixed(2)`.
- Etykieta średniej ceny: kolor `#2ea043`, format wyniku `toFixed(2)`.
- Etykiety zawierają wyłącznie liczby, bez tekstu `DOT`.
- Linie pozostają cienkie; istniejąca pionowa linia aktywnego punktu i dymek nie zmieniają działania.

## Implementacja
- Zmiana ograniczona do `index.html`, w pluginie `priceLinesPlugin`.
- Po narysowaniu linii plugin wywołuje `ctx.fillText()` dla obu wartości.
- Pozycja etykiet: `x = chartArea.left + 6`, `textAlign = 'left'`, `textBaseline = 'middle'`; `y` pochodzi z piksela odpowiedniej linii. Etykiety są rysowane od góry do dołu, a w razie kolizji zielona etykieta jest przesuwana w dół o 16 px.
- Tekst ma ciemne tło `rgba(14, 17, 23, 0.88)` i padding 3 px; bez cienia.
- `latestPrice` to ostatnia poprawna liczba z bieżącego zbioru, a `averagePrice` to średnia arytmetyczna poprawnych liczb z tego zbioru. Dla pustego zbioru lub braku poprawnych wartości plugin nie rysuje linii ani etykiet.

## Testowanie
- Statyczna kontrola składni JavaScript.
- Test mock pluginu sprawdzający wywołania `fillText`, kolory, dokładne współrzędne przy lewej krawędzi, format `toFixed(2)`, odsunięcie przy kolizji oraz aktualizację po zmianie danych.
- Ręczna weryfikacja w przeglądarce: etykiety nie nachodzą na siebie i są widoczne po załadowaniu danych.
