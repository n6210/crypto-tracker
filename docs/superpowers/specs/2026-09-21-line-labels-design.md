# Etykiety wartości na liniach ceny — specyfikacja

## Cel
Dodać na wykresie ATOM/DOT dwie czytelne etykiety liczbowe: wartość aktualnej ceny na żółtej linii oraz wartość średniej ceny na zielonej linii.

## Zatwierdzony wygląd
- Wariant B: etykiety umieszczone przy lewej krawędzi obszaru wykresu.
- Etykieta aktualnej ceny: kolor `#ffd700`, format `x.xx`.
- Etykieta średniej ceny: kolor `#2ea043`, format `x.xx`.
- Etykiety zawierają wyłącznie liczby, bez tekstu `DOT`.
- Linie pozostają cienkie; istniejąca pionowa linia aktywnego punktu i dymek nie zmieniają działania.

## Implementacja
- Zmiana ograniczona do `index.html`, w pluginie `priceLinesPlugin`.
- Po narysowaniu linii plugin wywołuje `ctx.fillText()` dla obu wartości.
- Pozycja etykiet: `chartArea.left + 6`, odpowiednio przy pikselu żółtej i zielonej linii; tekst może mieć ciemne tło lub cień, aby pozostał czytelny na siatce.
- Wartości pochodzą z `latestPrice` oraz `averagePrice`, obliczanych z aktualnego zbioru danych wykresu.
- Dla pustego zbioru danych plugin kończy działanie bez rysowania etykiet.

## Testowanie
- Statyczna kontrola składni JavaScript.
- Test mock pluginu sprawdzający wywołania `fillText`, kolory, pozycje przy lewej krawędzi i format dwóch miejsc po przecinku.
- Ręczna weryfikacja w przeglądarce: etykiety nie nachodzą na siebie i są widoczne po załadowaniu danych.
