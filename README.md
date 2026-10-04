# crypto-tracker

Wykres kursu pary walutowej z Binance, w jednym pliku HTML, bez buildu.

Domyślnie śledzi **ATOM / SOL** w kwotowaniu **USDC**. Główny wykres pokazuje kurs
`1 ATOM = x SOL`, a pod nim dwa osobne wykresy cen `ATOM / USDC` i `SOL / USDC` —
osobne, bo ATOM kosztuje ułamek dolara, a SOL ponad sto, więc na wspólnej skali
cena ATOM przykleiłaby się do zera.

## Uruchomienie

Otwórz `index.html` w przeglądarce. Nie ma zależności do zainstalowania ani
kompilowania. Chart.js ładowany jest z CDN, więc wymagane jest połączenie
z internetem.

## Funkcje

- **Oś kursu po prawej** — opisy wartości `0.0148`–`0.0155` nie kolidują z
  linią danych u lewej krawędzi.
- **Dwie linie pomocnicze** — żółta to ostatnia cena, zielona średnia arytmetyczna
  z całego zakresu. Etykiety liczbowe startują na swoich liniach i przeskakują
  w bok, gdyby znalazły się na linii wykresu; w razie braku miejsca zostają
  na środku.
- **Strzałki kierunku** — `↑` zielona, `↓` czerwona, `→` żółta, liczone
  z open do close ostatniej świecy, osobno dla każdej waluty. `ATOM ↑` przy
  `SOL ↓` to normalny stan — kurs pary wtedy rośnie.
- **Dwa wykresy cen** — każdy ze swoją skalą, aktualna cena w nagłówku karty.
- **Osiem interwałów** od 1h do 1y, dobieranych do liczby świec, żeby wykres
  zawsze miał tyle punktów, ile szerokość potrafi czytelnie pokazać.
- **Auto-odświeżanie** co 30 sekund, z odrzuceniem odpowiedzi, jeśli w między
  czasie zmieniono interwał.
- **Tooltip wyłącznie dla kursu** — ceny w USDC mają własne wykresy, więc dymek
  na głównym wykresie pokazuje tylko `ATOM/SOL` i `SOL/ATOM`.

## Źródło danych

Publiczne REST API Binance, endpoint `klines`, osobne zapytanie dla
`ATOMUSDC` i `SOLUSDC`. Kurs liczony jest z dopasowanych timestampów close,
nie z odrębnych ostatnich cen — inaczej wykres skakałby przy rozjechanych
świecach.

## Zmiana pary

Kliknij lewy lub prawy symbol w nagłówku, wpisz nowy (np. `BTC` lub `ETH`)
i zatwierdź przyciskiem **Zapisz** lub Enter. Aplikacja sprawdza w Binance
`exchangeInfo`, czy istnieje aktywny rynek spot danego aktywa w USDC.
Nieprawidłowy symbol lub błąd połączenia pozostawia dotychczasową parę.
**Anuluj** lub Escape zamyka edycję.

Oba symbole są zapisywane w `localStorage` przeglądarki i przywracane po
odświeżeniu. Kurs, kurs odwrotny, opisy i wszystkie trzy wykresy korzystają
z wybranej pary. Nie jest wymagana bezpośrednia para między aktywami —
przeliczenie odbywa się przez ich ceny w USDC. Jeśli przeglądarka blokuje
zapis, aplikacja wyświetla informację, a wybór działa do zamknięcia strony.
Selektor `TIMEFRAMES` na górze skryptu steruje interwałami i liczbą świec.

## Struktura

```
index.html                                  cała aplikacja: HTML, CSS i JS
docs/superpowers/specs/                     specyfikacje funkcji
docs/superpowers/plans/                     plany wdrożeniowe
```

## Uwagi

Strona to statyczny plik, więc hostuje się ją na GitHub Pages albo dowolnym
serwerze plików statycznych. Włączenie Pages: Settings → Pages → Source
`Deploy from a branch`, gałąź `main`, folder `/`.
