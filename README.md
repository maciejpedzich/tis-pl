# Tis PL

Oryginaly projekt autorstwa Thomasa Ten Cate znajdziesz [tutaj](https://github.com/ttencate/tis). Ja go tylko przełożyłem na język polski. Dalsze tłumaczenie README już wkrótce.

[**Wypróbuj demo Tis PL** z kodem źródłowym w tle.](https://maciejpedzich.github.io/tis-pl)

Tis PL to spolszczony, _samowystarczalny_ (tj. bez bibliotek) klon gry Tetris® w niecałych 4 kB czystego kodu JavaScript (ECMAScript 5), włącznie z generowaniem niezbędnych znaczników HTML oraz styli CSS.

Tis PL może być dołączony do każdej strony internetowej poprzez dodanie znacznika `<script>`. Można go aktywować jako tzw. easter egga, wprowadzając kod Konami.

## Funkcje

Tis posiada niemal wszystkie funkcje, których możesz się spodziewać po nowoczesnym Tetrisie:

- Wszystkie siedem kształtów tetromino.
- Możliwość ich poruszania oraz obracania.
- Powtarzanie klawiszy.
- Przyspieszanie i upuszczanie bloków.
- _Blok duch_, czyli podgląd miejsca tetromino.
- Opóźnienie zablokowania pozycji bloku.
- Losowy wybór kolejnych bloków.
- Animacja usunięcia linii.
- Podgląd następnego tetromino.
- Nieskończona liczba poziomów, im wyższy poziom tym bloki spadają szybciej.
- Wynik zależny od obecnego poziomu oraz liczby usuniętych linii.
- Animowany ekran końca gry.
- Efekty dźwiękowe.
- Muzyka z sopranem i basem.

## Brakujące funkcje

- Przyznawanie punktów za tzw. _T-spiny_ oraz usuwanie linii odzdzielonych przez inną linię.
- Miejsce na tetromino _na później_.
- Podgląd kilku kolejnych bloków jednocześnie.

## Stosowanie

Po prostu pobierz `tis.min.js` z tego repozytorium, umieść plik gdziekolwiek chcesz na serwerze i wstaw poniższe przed znacznikiem `</body>` w kodzie HTML:

    <script src="/sciezka/do/tis.min.js"></script>

Teraz, odwiedzający stronę otrzymają miłą niespodziankę po wprowadzeniu [kodu Konami](https://pl.wikipedia.org/wiki/Konami_Code).

## Uwagi odnośnie implementacji

Aby kod był w miarę _rozsądny_, musi polegać na [UglifyJS](https://github.com/mishoo/UglifyJS) w celu nadawania nowych nazw zmiennym, usuwania klamr i innych niuansów. Mimo to, wiele rzeczy należało napisać własnoręcznie. W tej sekcji opisano wszystkie wykorzystane w projekcie sztuczki.

### HTML/CSS

- Wyodrębnianie wspólnych części kodu HTML i CSS do łańcuchów, na przykład:
  `'<div style="margin:'`.
- Używanie `pc` zamiast `px` w stylach CSS; 1 pica odpowiada 16 pikselom.

### Dane gry

- Muzyka została zakodowana jako łańcuch znaków, gdzie każdy znak reprezentuje zarówno tonację, jak i czas odgrywania nuty
- Kształty tetromino w poszczególnych obrotach przedstawiono w postaci mask bitowych, ale ponieważ nie da się w sposób efektywny zakodować bajtów powyżej 127 w UTF-8, zakodowano je w Base64.
- Tabele kolizji bloku ze ścianą zakodowano jako łańcuchy, gdzie każdy znak odpowiada pojedyńczemu przesunięciu wzdłuż osi x oraz y.
- Kolory tetromino również przedstawiono w postaci łańcuchów w stylu `#fff`
  (oczywiście bez `#`), oddzielonych znakiem `9`. Korzystamy z cyfry,
  ponieważ nie musimy jej wtedy otaczać cudzysłowami w metodzie `Array.split()`.
- Efekty dźwiękowe zakodowano jako pojedyńcze liczby, przeznaczając kilka bitów dla szybkości zanikania, kilka kolejnych dla pierwotnej częstotliwości, a pozostałe przeznaczono na częstotliwość po odtworzeniu 1000 (`1e3`) próbek.

### JavaScript

- Nazwy globalnych obiektów (takich jak `window`, `document`) oraz często wykorzystywanych daych/metod przechowywane są w zmiennych dla krótszego dostępu.
- Zamiast `document.getElementById(...)`, korzysta się z faktu, że elementy są z przypisanym id są zarejestrowane w obiekcie `window`, a elementy te odczytujemy poprzez `window[...]`.
- Ponieważ deklaracje zmiennych są kosztowne, należy je wykonywać w zasięgu globalnym i wykorzystywać je ponownie tyle razy, ile to możliwe. Utrudnia to bezpieczną inwokację funkcji.
- Należy zastępować wywołania funkcji jej ciałem, ponieważ słowo `function` jest okropnie długie.
- Niech `undefined` będzie pożądaną pierwotną wartością każdej możliwej zmienniej, w celu uniknięcia konieczności ich inicjalizacji.
- Trzeba mieć świadomość, że zapis `for(i in a)` stanowi alternatywę dla `for(i=0;i<n;i++)`. Jednakże nie jest to zawsze zapis krótszy, bo tradycyjna pętla `for` pozwala na umieszczenie warunku wykonywania instrukcji oraz wyrażenie zwiększenia/zmniejszenia wartości zmiennej
- Umieszczaj przyporządkowania wartości wewnątrz wyrażeń gdziekolwiek to możliwe: zamiast `x++;y=2*x`,
  pisz `y=2*x++`.
- `x>=0&&x<4` możesz zastąpić `!(x&~3)`. Działa to nawet wtedy, gdy `x` jest ujemne.
- Korzystaj z `~~(a+b)` zamiast `Math.floor(a+b)` aby otrzymać liczbę całkowitą. `0|(a+b)` również zadziała.
- Dla arbitralnych stałych, `9` jest lepsze niż `10`, a `99` lepsze niż
  `100`.
- `switch`/`case` jest strasznie rozwlekłe, szczególnie kiedy potrzebujesz `break` (czyli praktycznie zawsze). Po prostu używaj zamiast tego `if`/`else if`.
- Istnieje również etykieta (label) działający jak `goto`, aby przerwać dwie pętle `for` jednocześnie. To chyba jedyne sensowne zastosowanie etykiet w języku JavaScript.
