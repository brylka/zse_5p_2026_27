# Obsługa zdarzeń – kalkulator, który liczy

**Technik programista | klasa 5 | aplikacje mobilne**

Pięć sposobów podpięcia kliknięcia, droga od kopiuj-wklej do jednej metody,
stan aplikacji i obsługa błędów.

---

## 1. Punkt wyjścia

Na poprzednich zajęciach zbudowałeś układ kalkulatora: wyświetlacz, cztery operacje,
dziesięć cyfr, przecinek, równa się i czyszczenie. Siedemnaście przycisków, które
**nic nie robią**.

Dziś je uruchamiamy. Po drodze zobaczysz, dlaczego pierwszy pomysł, który przychodzi
do głowy, prowadzi do dwustu linii kodu, i jak z tych dwustu zrobić dwadzieścia.

W materiale 03 podpinałeś jeden przycisk. Siedemnaście przycisków to **inny problem**,
nie ten sam siedemnaście razy – i to jest właściwa treść tej lekcji.

---

## 2. Pięć sposobów podpięcia kliknięcia

Wszystkie działają. Spotkasz każdy z nich w cudzym kodzie, w tutorialach i w tym,
co wygeneruje agent. Musisz je rozpoznawać, nawet jeśli sam używasz jednego.

### 2.1. `android:onClick` w pliku XML

```xml
<Button
    android:id="@+id/btnHello"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="onHelloClick"
    android:text="Kliknij" />
```

```java
public void onHelloClick(View view) {
    tvResult.setText("Kliknięto");
}
```

Metoda musi być **publiczna**, zwracać `void` i przyjmować dokładnie jeden parametr
typu `View`. Nazwa w XML-u i w Javie musi się zgadzać co do znaku.

Wada jest poważna: powiązanie istnieje wyłącznie jako **napis w pliku XML**.
Zmienisz nazwę metody w Javie – kompilator niczego nie zauważy, a aplikacja wywali się
dopiero w chwili kliknięcia. Google odradza to podejście. Znaj je, bo jest w starszych
materiałach i w arkuszach egzaminacyjnych, ale sam go nie używaj.

### 2.2. Klasa anonimowa

```java
btnHello.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        tvResult.setText("Kliknięto");
    }
});
```

Tak wyglądał cały kod Androida przed Javą 8 i tak wygląda większość starszych
tutoriali. Działa bez zarzutu, tylko jest rozwlekłe.

Jedna pułapka: wewnątrz klasy anonimowej `this` oznacza **tę klasę anonimową**,
nie Twoją aktywność. Jeśli potrzebujesz aktywności – na przykład w `Toast` –
piszesz `MainActivity.this`.

### 2.3. Lambda

```java
btnHello.setOnClickListener(v -> tvResult.setText("Kliknięto"));
```

Dokładnie to samo co wyżej, zapisane krócej. Kompilator wie, o którą metodę chodzi,
bo interfejs `OnClickListener` ma tylko jedną. W lambdzie `this` dalej oznacza
aktywność, więc problem z punktu 2.2 znika.

**To jest nasz sposób domyślny.**

### 2.4. Jeden obiekt listenera dla wielu przycisków

```java
View.OnClickListener sharedListener =
        v -> tvResult.setText("Kliknięto " + ((Button) v).getText());

btnA.setOnClickListener(sharedListener);
btnB.setOnClickListener(sharedListener);
btnC.setOnClickListener(sharedListener);
```

Zamiast tworzyć listener przy każdym przycisku, tworzysz **jeden obiekt** i podpinasz
go pod wiele przycisków. Parametr `v` mówi, który z nich został kliknięty.

To jest klucz do kalkulatora i do niego wrócimy w rozdziale 3.

### 2.5. Aktywność jako listener

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.btnA).setOnClickListener(this);
        findViewById(R.id.btnB).setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.btnA) {
            tvResult.setText("A");
        } else if (v.getId() == R.id.btnB) {
            tvResult.setText("B");
        }
    }
}
```

Klasa deklaruje, że **sama jest** listenerem, więc podpinasz `this`. Cała obsługa
kliknięć ląduje w jednej metodzie `onClick`. Spotkasz to często w starszych projektach.

### Porównanie

| Sposób | Zalety | Wady | Kiedy używać |
|---|---|---|---|
| `android:onClick` | najkrócej w Javie | brak kontroli przy kompilacji, awaria dopiero przy kliknięciu | nigdy; rozpoznawać w cudzym kodzie |
| Klasa anonimowa | działa wszędzie, także przed Javą 8 | rozwlekłe, pułapka z `this` | gdy interfejs ma więcej niż jedną metodę |
| Lambda | krótko, czytelnie, `this` bez niespodzianek | tylko dla interfejsów z jedną metodą | **domyślnie** |
| Wspólny obiekt | jeden kod dla wielu przycisków | trzeba rozpoznać, który kliknięto | przy grupach podobnych przycisków |
| Aktywność jako listener | wszystko w jednym miejscu | przy wielu przyciskach `onClick` puchnie | gdy przycisków jest kilka i robią różne rzeczy |

---

## 3. Droga do kalkulatora – trzy etapy

Dziesięć przycisków z cyframi robi dokładnie to samo: dopisuje swój znak
do wyświetlacza. Zobaczmy trzy sposoby zapisania tego samego.

### Etap 1 – osobna metoda do każdego przycisku

Pierwszy pomysł, jaki przychodzi do głowy, i pomysł, który podsunie Ci agent,
jeśli nie powiesz mu nic więcej:

```java
Button btn7 = findViewById(R.id.btn7);
Button btn8 = findViewById(R.id.btn8);
Button btn9 = findViewById(R.id.btn9);

btn7.setOnClickListener(v -> append7());
btn8.setOnClickListener(v -> append8());
btn9.setOnClickListener(v -> append9());

// ... i tak dalej, jeszcze siedem razy

private void append7() {
    tvDisplay.setText(tvDisplay.getText().toString() + "7");
}

private void append8() {
    tvDisplay.setText(tvDisplay.getText().toString() + "8");
}

private void append9() {
    tvDisplay.setText(tvDisplay.getText().toString() + "9");
}
```

Działa. I jest **nie do utrzymania**. Dziesięć metod różniących się jedną cyfrą.
Kiedy dołożysz warunek „nie pozwalaj na drugi przecinek", musisz go wpisać
w dziesięciu miejscach. Kiedy poprawisz błąd, poprawiasz go dziesięć razy –
albo dziewięć, bo o jednym zapomnisz, i właśnie tak powstają błędy, których nie da się
znaleźć.

> Jeśli piszesz kod przez kopiuj-wklej i zmieniasz w kopii jedną rzecz,
> to ta jedna rzecz powinna być **parametrem**. To nie jest reguła Androida,
> tylko reguła programowania w ogóle.

### Etap 2 – jedna metoda z parametrem

```java
btn7.setOnClickListener(v -> appendSymbol("7"));
btn8.setOnClickListener(v -> appendSymbol("8"));
btn9.setOnClickListener(v -> appendSymbol("9"));

private void appendSymbol(String symbol) {
    tvDisplay.setText(tvDisplay.getText().toString() + symbol);
}
```

Dziesięć metod zamieniło się w jedną. Poprawka wchodzi w jednym miejscu.
Zostało jednak dziesięć wywołań `findViewById` i dziesięć `setOnClickListener`.

### Etap 3 – jeden listener dla wszystkich

Listener dostaje w parametrze `v` ten przycisk, który został kliknięty.
A przycisk z cyfrą **ma swoją cyfrę napisaną na sobie**:

```java
View.OnClickListener symbolListener =
        v -> appendSymbol(((Button) v).getText().toString());

int[] symbolIds = {
        R.id.btn0, R.id.btn1, R.id.btn2, R.id.btn3, R.id.btn4,
        R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9,
        R.id.btnComma
};

for (int id : symbolIds) {
    findViewById(id).setOnClickListener(symbolListener);
}
```

Jedenaście przycisków, sześć linii. Dodanie dwunastego to dopisanie jednego
identyfikatora do tablicy.

Tablica i pętla `for` to wiedza z klasy czwartej. Tutaj widać, po co były.

### Ile to kosztuje linii

Dla samych dziesięciu cyfr, orientacyjnie:

| Etap | Linie | Co trzeba zmienić przy poprawce |
|---|---|---|
| 1 – metoda na przycisk | ok. 50 | dziesięć miejsc |
| 2 – metoda z parametrem | ok. 25 | jedno miejsce |
| 3 – wspólny listener | ok. 12 | jedno miejsce |

Nie chodzi o to, żeby pisać najkrócej. Chodzi o to, żeby **poprawka wchodziła
w jednym miejscu**.

---

## 4. `getText()` czy `getId()`

W etapie 3 odczytaliśmy z przycisku jego napis. To działa, bo **napis na przycisku
jest jednocześnie daną** – przycisk z napisem „7" ma dopisać siódemkę.

Przy operacjach jest inaczej. Napis „+" jest ozdobą, a działanie trzeba rozpoznać
po tym, **który** przycisk kliknięto:

```java
View.OnClickListener operationListener = v -> {
    if (v.getId() == R.id.btnAdd) {
        setOperation("+");
    } else if (v.getId() == R.id.btnSub) {
        setOperation("-");
    } else if (v.getId() == R.id.btnMul) {
        setOperation("*");
    } else {
        setOperation("/");
    }
};
```

Reguła:

| Sytuacja | Czego użyć |
|---|---|
| Napis na przycisku **jest** daną (cyfry, litery) | `((Button) v).getText()` |
| Napis jest ozdobą, liczy się który przycisk | `v.getId()` |

W kalkulatorze przyda się jedno i drugie – i właśnie dlatego jest dobrym ćwiczeniem.

> **Dlaczego nie `switch`.** Każdy starszy tutorial i każdy agent LLM napisze
> `switch (v.getId()) { case R.id.btnAdd: ... }`. W nowych projektach
> **to się nie kompiluje** – dostaniesz błąd *constant expression required*.
> Od wersji 8.0 narzędzia budującego pola w klasie `R` nie są już stałymi
> (`final`), a `switch` w Javie wymaga stałych. Rozwiązaniem jest zwykły
> łańcuch `if / else if`, tak jak wyżej.
>
> To jest konkretny przykład sytuacji, w której agent podaje kod poprawny
> trzy lata temu. Komunikat kompilatora mówi Ci prawdę, model – nie.

---

## 5. Stan kalkulatora

Kalkulator musi coś **pamiętać między kliknięciami**. Gdy klikasz `7`, `+`, `3`, `=`,
aplikacja w chwili naciśnięcia `=` musi wiedzieć, że wcześniej było siedem i plus.

Zmienna lokalna w metodzie znika, gdy metoda się kończy. Dlatego stan trzymamy
w **polach klasy**:

```java
private double storedValue = 0;          // pierwsza liczba działania
private String operation = "";           // "+", "-", "*", "/" albo pusty napis
private boolean startNewNumber = true;   // czy kolejna cyfra zaczyna nową liczbę
```

Trzecie pole jest nieoczywiste, a bez niego kalkulator nie działa. Po naciśnięciu `+`
wyświetlacz dalej pokazuje `7`. Kolejna cyfra ma **zastąpić** siódemkę, a nie dopisać
się do niej. `startNewNumber` zapamiętuje, w którym z tych dwóch trybów jesteśmy.

Przebieg działania `7 + 3 =`:

| Klik | `storedValue` | `operation` | `startNewNumber` | Wyświetlacz |
|---|---|---|---|---|
| start | 0 | – | `true` | `0` |
| `7` | 0 | – | `false` | `7` |
| `+` | 7 | `+` | `true` | `7` |
| `3` | 7 | `+` | `false` | `3` |
| `=` | 7 | – | `true` | `10` |

Prześledź tę tabelę zanim przeczytasz kod. Ona jest całym kalkulatorem.

---

## 6. Kompletny kod

Układ bez zmian – ten z poprzednich zajęć. Do `strings.xml` dochodzi jeden wpis:

```xml
<string name="error_divide_zero">Nie dzielimy przez zero</string>
```

### `MainActivity.java`

```java
package com.example.kalkulator;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private TextView tvDisplay;

    private double storedValue = 0;          // pierwsza liczba działania
    private String operation = "";           // "+", "-", "*", "/" albo pusty napis
    private boolean startNewNumber = true;   // czy kolejna cyfra zaczyna nową liczbę

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvDisplay = findViewById(R.id.tvDisplay);

        // cyfry i przecinek - jeden listener dla jedenastu przycisków
        View.OnClickListener symbolListener =
                v -> appendSymbol(((Button) v).getText().toString());

        int[] symbolIds = {
                R.id.btn0, R.id.btn1, R.id.btn2, R.id.btn3, R.id.btn4,
                R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9,
                R.id.btnComma
        };
        for (int id : symbolIds) {
            findViewById(id).setOnClickListener(symbolListener);
        }

        // operacje - jeden listener, rozpoznanie po identyfikatorze
        View.OnClickListener operationListener = v -> {
            if (v.getId() == R.id.btnAdd) {
                setOperation("+");
            } else if (v.getId() == R.id.btnSub) {
                setOperation("-");
            } else if (v.getId() == R.id.btnMul) {
                setOperation("*");
            } else {
                setOperation("/");
            }
        };
        findViewById(R.id.btnAdd).setOnClickListener(operationListener);
        findViewById(R.id.btnSub).setOnClickListener(operationListener);
        findViewById(R.id.btnMul).setOnClickListener(operationListener);
        findViewById(R.id.btnDiv).setOnClickListener(operationListener);

        // pojedyncze przyciski - własne lambdy
        findViewById(R.id.btnEquals).setOnClickListener(v -> calculate());
        findViewById(R.id.btnClear).setOnClickListener(v -> clearAll());
    }

    private void appendSymbol(String symbol) {
        String current = tvDisplay.getText().toString();

        if (startNewNumber) {
            tvDisplay.setText(symbol.equals(",") ? "0," : symbol);
            startNewNumber = false;
            return;
        }
        if (symbol.equals(",") && current.contains(",")) {
            return;                        // drugi przecinek w tej samej liczbie
        }
        tvDisplay.setText(current + symbol);
    }

    private void setOperation(String newOperation) {
        storedValue = readDisplay();
        operation = newOperation;
        startNewNumber = true;
    }

    private void calculate() {
        if (operation.isEmpty()) {
            return;                        // nie wybrano jeszcze działania
        }

        double second = readDisplay();

        if (operation.equals("/") && second == 0) {
            Toast.makeText(this, R.string.error_divide_zero, Toast.LENGTH_SHORT).show();
            clearAll();
            return;
        }

        double result;
        if (operation.equals("+")) {
            result = storedValue + second;
        } else if (operation.equals("-")) {
            result = storedValue - second;
        } else if (operation.equals("*")) {
            result = storedValue * second;
        } else {
            result = storedValue / second;
        }

        showResult(result);
        operation = "";
        startNewNumber = true;
    }

    private double readDisplay() {
        String text = tvDisplay.getText().toString().replace(',', '.');
        try {
            return Double.parseDouble(text);
        } catch (NumberFormatException e) {
            return 0;
        }
    }

    private void showResult(double result) {
        if (result == Math.rint(result) && Math.abs(result) < 1e15) {
            tvDisplay.setText(String.valueOf((long) result));
        } else {
            String text = String.format(Locale.US, "%.2f", result);
            tvDisplay.setText(text.replace('.', ','));   // zawsze przecinek
        }
    }

    private void clearAll() {
        tvDisplay.setText(R.string.display_start);
        storedValue = 0;
        operation = "";
        startNewNumber = true;
    }
}
```

Osiem metod, żadna dłuższa niż dwadzieścia linii. `onCreate` tylko podpina zdarzenia
i nie zawiera żadnych obliczeń – to jest wzorzec, którego trzymamy się do końca roku.

---

## 7. Cztery rzeczy, które psują kalkulator

Każda z nich wysypie aplikację albo da zły wynik. Wszystkie są w kodzie wyżej
rozwiązane – zobacz gdzie.

**Przecinek kontra kropka.** Polska klawiatura daje przecinek, a `Double.parseDouble`
rozumie wyłącznie kropkę. Bez `replace(',', '.')` pierwsza liczba z groszami kończy się
wyjątkiem `NumberFormatException` i zgaśnięciem aplikacji.

**Pusty albo niepoprawny wyświetlacz.** `Double.parseDouble("")` też rzuca wyjątek.
Dlatego odczyt jest w bloku `try`, a w razie problemu zwracamy zero zamiast przerywać
działanie programu.

**Dzielenie przez zero.** W typie `double` nie ma wyjątku – dostajesz `Infinity`
albo `NaN` i taki napis ląduje na wyświetlaczu. Trzeba to sprawdzić samemu,
**przed** wykonaniem dzielenia.

**Wynik `8.0` zamiast `8`.** Typ `double` zawsze ma część ułamkową. `Math.rint`
sprawdza, czy liczba jest całkowita, i wtedy pokazujemy ją bez ogona.

**Kropka w wyniku mimo przycisku z przecinkiem.** `String.format` używa separatora
zależnego od ustawień regionalnych. Gdybyśmy wpisali `Locale.getDefault()`, wynik
na polskim telefonie miałby przecinek, a na emulatorze ustawionym domyślnie
na angielski – kropkę. Ta sama aplikacja zachowywałaby się różnie na różnych
urządzeniach, a uczeń widziałby kropkę zaraz po naciśnięciu klawisza z przecinkiem.
Dlatego formatujemy przez `Locale.US`, a potem jawnie zamieniamy kropkę na przecinek:
wynik jest **zawsze** taki sam i zawsze zgodny z klawiaturą kalkulatora.

### Piąta rzecz, której jeszcze nie naprawiamy

Obróć telefon na bok. **Wszystko znika** – wyświetlacz wraca do zera, zapamiętana
liczba przepada.

To nie jest błąd w Twoim kodzie. Android przy zmianie orientacji **niszczy aktywność
i tworzy ją od nowa**, a pola klasy powstają od zera. Rozwiązaniem jest zapis stanu,
którym zajmiemy się na osobnych zajęciach. Na razie zapamiętaj objaw – będziesz
go widział w każdej aplikacji, którą napiszesz.

---

## 8. Dodatek: długie kliknięcie

```java
findViewById(R.id.btnClear).setOnLongClickListener(v -> {
    clearAll();
    Toast.makeText(this, "Wyczyszczono wszystko", Toast.LENGTH_SHORT).show();
    return true;        // true = obsłużyłem, nie wywołuj zwykłego kliknięcia
});
```

Różnica wobec `OnClickListener`: metoda **zwraca wartość logiczną**. `true` oznacza
„zdarzenie obsłużone". Gdy zwrócisz `false`, system potraktuje dotyk dodatkowo jako
zwykłe kliknięcie i wykona oba listenery.

---

## 9. Częste błędy

| Objaw | Przyczyna | Rozwiązanie |
|---|---|---|
| *constant expression required* przy `case R.id.x` | pola `R` nie są stałymi | zamień `switch` na `if / else if` |
| `NullPointerException` przy pierwszym kliknięciu | `findViewById` przed `setContentView` albo zła nazwa identyfikatora | sprawdź kolejność w `onCreate` |
| `ClassCastException` przy `(Button) v` | listener podpięty pod element, który nie jest przyciskiem | rzutuj na `TextView` albo sprawdź typ |
| Cyfry dopisują się po wyniku | brak ustawienia `startNewNumber` po `=` | ustaw flagę na końcu `calculate()` |
| `NumberFormatException` | przecinek, pusty wyświetlacz | `replace` plus blok `try` |
| Na wyświetlaczu `Infinity` | dzielenie przez zero | sprawdź dzielnik przed działaniem |
| Aplikacja gaśnie po kliknięciu, choć kompiluje się bez błędu | literówka w `android:onClick` | przestań używać `android:onClick` |
| Po obrocie ekranu wszystko znika | aktywność tworzona od nowa | temat kolejnych zajęć |
| Kliknięcie nie robi nic | listener podpięty, ale pod inny przycisk | sprawdź identyfikator w `findViewById` |

---

## 10. Ściąga

| Zapis | Znaczenie |
|---|---|
| `btn.setOnClickListener(v -> metoda());` | lambda, sposób domyślny |
| `View.OnClickListener x = v -> {...};` | wspólny listener dla wielu przycisków |
| `((Button) v).getText().toString()` | napis z klikniętego przycisku |
| `v.getId() == R.id.btnAdd` | rozpoznanie, który przycisk kliknięto |
| `btn.setOnLongClickListener(v -> {... return true;});` | długie przytrzymanie |
| `Double.parseDouble(s)` | napis na liczbę, rzuca `NumberFormatException` |
| `s.replace(',', '.')` | przecinek na kropkę przed zamianą na liczbę |
| `String.format(Locale.US, "%.2f", x)` | liczba na napis, dwa miejsca po przecinku, zawsze z kropką |
| `Math.rint(x) == x` | sprawdzenie, czy liczba jest całkowita |

**Stan między kliknięciami trzymasz w polach klasy, nie w zmiennych lokalnych.**
**`onCreate` podpina zdarzenia. Obliczenia są w osobnych metodach.**

---

## 11. Zadania

### Zadanie 1 – kalkulator

Dokończ kalkulator z poprzednich zajęć według tego materiału. Oddajesz go
**w trzech commitach pokazujących trzy etapy** z rozdziału 3:

1. `feat: cyfry - osobna metoda do kazdego przycisku` – wersja z etapu 1,
   wystarczy dla trzech cyfr,
2. `refactor: jedna metoda z parametrem` – etap 2,
3. `refactor: sharedListener listener dla wszystkich cyfr` – etap 3.

Dalej dokładasz operacje, `=`, czyszczenie i obsługę błędów z rozdziału 7.

W `README.md` odpowiedz: **ile miejsc w kodzie trzeba poprawić po etapie 1,
a ile po etapie 3**, jeśli zdecydujesz, że wyświetlacz ma pokazywać maksymalnie
dziesięć znaków?

**Dla chętnych.** Kalkulator ma drobny błąd kosmetyczny: po naciśnięciu `0`,
a potem `5`, wyświetlacz pokazuje `05`. Znajdź w `appendSymbol` miejsce, w którym
można to poprawić, i popraw.

### Zadanie 2 – własna aplikacja z przyciskami

Wybierz **jeden temat z listy** albo zaproponuj własny.
Aplikacje są celowo banalne – oceniam sposób obsługi zdarzeń, nie pomysł.

| Temat | Co ćwiczysz | Trudność |
|---|---|---|
| **Licznik kliknięć** – `+1`, `−1`, `Reset` | stan w polu klasy, blokada ujemnych wartości | najłatwiejszy |
| **Rzut kostką** – losuje 1–6, liczy rzuty | `Random`, dwa pola stanu | najłatwiejszy |
| **Rzut monetą** – orzeł albo reszka, statystyka obu | `Random`, dwa liczniki | łatwy |
| **Licznik punktów dwóch drużyn** – `+1`, `+2`, `+3` dla każdej, reset | sześć przycisków, wspólny listener | łatwy |
| **Licznik kalorii** – `+100`, `+250`, `+500`, suma, cofnij | `getText()` jako dana, historia | łatwy |
| **Przelicznik jednostek** – pole i trzy przyciski: °C na °F, km na mile, kg na funty | `getId()`, walidacja pustego pola | średni |
| **Kalkulator napiwku** – kwota i przyciski 10%, 15%, 20% | `getText()` jako dana, parsowanie | średni |
| **Zgadywanka** – aplikacja losuje 1–100, Ty zgadujesz | stan, porównania, komunikaty | średni |

**Wymagania – te same dla każdego tematu:**

1. Układ w LinearLayout albo ConstraintLayout, do wyboru. **Minimum trzy przyciski.**
2. Zastosuj **co najmniej dwa różne sposoby** podpięcia kliknięcia z rozdziału 2
   i wymień w `README.md`, które i w którym miejscu kodu.
3. **Przynajmniej jeden wspólny listener** obsługujący dwa lub więcej przycisków.
4. Obsłuż **co najmniej jedną sytuację błędną**: puste pole, wartość ujemna,
   dzielenie przez zero, brak wyboru – cokolwiek pasuje do Twojego tematu.
   Aplikacja ma w takiej sytuacji pokazać `Toast`, a nie zgasnąć.
5. Wszystkie teksty w `strings.xml`.
6. `onCreate` podpina zdarzenia i nic nie liczy.
7. Minimum cztery commity, `README.md` ze zrzutem ekranu.

### Zadanie 3 – przeróbka `[bez AI]`

Bez agenta i bez czatu, wyłącznie ten materiał i dokumentacja.

Weź gotową aplikację z zadania 2 i **przepisz obsługę kliknięć na inny sposób**
z rozdziału 2 – jeśli używałeś lambd, zrób to przez aktywność jako listener,
i odwrotnie. Działanie aplikacji ma pozostać identyczne.

W `docs/przerobka.md` opisz, co się zmieniło, która wersja jest krótsza
i którą łatwiej byłoby rozbudować o kolejne pięć przycisków.
