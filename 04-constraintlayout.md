# ConstraintLayout i elementy interfejsu

**Technik programista | klasa 5 | aplikacje mobilne**

Pozycjonowanie względem siebie, edytor graficzny, komponenty UI i ich atrybuty.

---

## 1. Problem, który ConstraintLayout rozwiązuje

Ekranów telefonów są setki rozmiarów. Twoja aplikacja ma wyglądać dobrze na małym
telefonie z 2019 roku, na dzisiejszym flagowcu, na tablecie i po obróceniu każdego
z nich. Nie da się tego zrobić, ustawiając elementy na sztywnych współrzędnych.

ConstraintLayout rozwiązuje to przez **relacje**. Nie mówisz „przycisk ma być
420 pikseli od góry". Mówisz: „przycisk ma być **pod polem tekstowym**, **wyśrodkowany
w poziomie**, z odstępem 16dp". System sam wylicza współrzędne — dla każdego ekranu inne,
ale zawsze zgodne z tym, co opisałeś.

```
   ┌─────────────────┐        ┌───────────────────────────┐
   │   [ Tytuł ]     │        │        [ Tytuł ]          │
   │                 │        │                           │
   │ [___pole____]   │        │ [________pole__________]  │
   │                 │        │                           │
   │   [ Wyślij ]    │        │        [ Wyślij ]         │
   └─────────────────┘        └───────────────────────────┘
      mały telefon                     tablet
   ten sam XML, te same relacje, inne wyliczone pozycje
```

### Skąd nazwa

**Constraint** to ograniczenie, a praktyczniej: **powiązanie**. Wyobraź sobie,
że każdy element wisi na sznurkach przyczepionych do krawędzi ekranu albo do innych
elementów. Sznurek napina się i ciągnie element w swoją stronę. Dwa sznurki z przeciwnych
stron o równej sile ustawiają element dokładnie pośrodku. To nie jest przenośnia
dydaktyczna — edytor graficzny rysuje te powiązania jako sprężynki i dokładnie tak
się zachowują.

### Dlaczego akurat ten layout

| Kontener | Jak układa | Kiedy używamy |
|---|---|---|
| **ConstraintLayout** | przez powiązania między elementami | **domyślnie, prawie zawsze** |
| LinearLayout | jeden za drugim, w rzędzie albo kolumnie | proste listy elementów – następna lekcja |
| FrameLayout | wszystko na sobie, warstwami | tło + element na wierzchu |
| RelativeLayout | podobnie do Constraint, ale słabiej | starszy kod, nie piszemy w nim nowego |
| TableLayout / GridLayout | siatka | rzadko, np. klawiatura kalkulatora |

Najważniejsza przewaga ConstraintLayout jest niewidoczna gołym okiem: **płaska
hierarchia**. Ten sam ekran w LinearLayoutach wymaga zagnieżdżania kontenerów
w kontenerach, po trzy–cztery poziomy. Każdy poziom to praca dla systemu przy
każdym odrysowaniu ekranu. W ConstraintLayout wszystkie elementy są na jednym poziomie,
rodzeństwem, a złożone ułożenie osiąga się powiązaniami, nie zagnieżdżaniem.

---

## 2. Żelazna zasada

> **Każdy element musi mieć co najmniej dwa powiązania: jedno poziome i jedno pionowe.**

Bez tego system nie wie, gdzie go narysować, i umieszcza go w lewym górnym rogu (0,0).
Najgorsze jest to, że **w edytorze wygląda dobrze** — bo edytor pamięta, gdzie go
upuściłeś — a rozjeżdża się dopiero po uruchomieniu.

Rozpoznasz problem po trzech znakach:

- czerwony albo żółty trójkąt ostrzeżenia przy elemencie w **Component Tree**,
- w XML-u pojawiają się atrybuty `tools:layout_editor_absoluteX` i `absoluteY`
  (przedrostek `tools:` oznacza: tylko podgląd, w aplikacji tego nie ma),
- element „wskakuje" w lewy górny róg po uruchomieniu.

Zobaczysz to w tym semestrze wielokrotnie. Za każdym razem przyczyna jest ta sama:
brakujące powiązanie.

---

## 3. Osiem podstawowych powiązań

Wszystkie mają przedrostek `app:` i budowę: **`layout_constraint`** + *moja krawędź* +
**`_to`** + *cudza krawędź* + **`Of`**.

`app:layout_constraintTop_toBottomOf="@id/etName"` czyta się: *moja górna krawędź
do dolnej krawędzi elementu `etName`*. Czyli: pod tym polem.

### Pionowe

| Atrybut | Znaczenie |
|---|---|
| `layout_constraintTop_toTopOf` | moja góra do góry celu |
| `layout_constraintTop_toBottomOf` | moja góra do dołu celu → **jestem pod nim** |
| `layout_constraintBottom_toTopOf` | mój dół do góry celu → **jestem nad nim** |
| `layout_constraintBottom_toBottomOf` | mój dół do dołu celu |

### Poziome

| Atrybut | Znaczenie |
|---|---|
| `layout_constraintStart_toStartOf` | mój lewy brzeg do lewego brzegu celu |
| `layout_constraintStart_toEndOf` | mój lewy brzeg do prawego brzegu celu → **jestem po prawej** |
| `layout_constraintEnd_toStartOf` | mój prawy brzeg do lewego brzegu celu → **jestem po lewej** |
| `layout_constraintEnd_toEndOf` | mój prawy brzeg do prawego brzegu celu |

Celem jest `"parent"` (krawędź ekranu) albo `"@id/costam"` (inny element).

> **`Start`/`End`, nie `Left`/`Right`.** Istnieją też `layout_constraintLeft_toLeftOf`
> i spółka, spotkasz je w starszym kodzie. Nie używaj ich. `Start` i `End` odwracają się
> automatycznie w językach pisanych od prawej do lewej (arabski, hebrajski) — układ
> Twojej aplikacji dostosowuje się sam. `Left` i `Right` zostają na miejscu i psują ekran.
> Tak samo `layout_marginStart` zamiast `layout_marginLeft`.

### Dziewiąte: linia bazowa tekstu

```xml
app:layout_constraintBaseline_toBaselineOf="@id/tvLabel"
```

Wyrównuje **linię, na której siedzą litery** — nie górę i nie dół elementu.
Kiedy stawiasz obok siebie napis 14sp i napis 24sp, wyrównanie do góry albo do środka
wygląda źle; wyrównanie linii bazowej wygląda dobrze. To jedyny sensowny sposób
ustawiania w rzędzie tekstów o różnej wielkości. Powiązanie bazowe zastępuje pionowe,
więc nie dodaje się do niego drugiego.

---

## 4. Trzy wzorce, które pokrywają większość ekranów

Poniżej **trzy kompletne pliki**. Każdy z nich wklejasz do `activity_main.xml`
w miejsce całej dotychczasowej zawartości, klikasz *Run* i widzisz efekt.
Nie są to urywki do sklejania — każdy działa sam z siebie.

Punktem wyjścia jest to, co generuje szablon *Empty Views Activity*:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

</androidx.constraintlayout.widget.ConstraintLayout>
```

Wszystko, co dopisujesz, ląduje **między znacznikiem otwierającym a zamykającym**.

> **Nie zmieniaj `android:id="@+id/main"` na kontenerze głównym.** Wygenerowana
> `MainActivity` zawiera linię `ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), …)`.
> Jeśli zmienisz albo usuniesz to id, `findViewById` zwróci `null` i **aplikacja zgaśnie
> zaraz po uruchomieniu** z `NullPointerException`. Dokładnie ten błąd omawialiśmy
> w materiale 03. Kontener ma zostać `main`, dopisujesz tylko elementy w środku.

> **Dwie rzeczy, które w tych trzech przykładach robię inaczej niż w prawdziwym projekcie.**
>
> **Teksty są wpisane wprost**, a nie przez `@string/`. Dzięki temu wklejasz plik
> i od razu działa, bez dopisywania czegokolwiek w `strings.xml`. Android Studio
> podkreśli je na żółto (*Hardcoded string*) — to **ostrzeżenie**, nie błąd,
> aplikacja się zbuduje i uruchomi. W swoim projekcie teksty przenosisz
> do `strings.xml`, tak jak w przykładzie z rozdziału 12.
>
> **Nie ma `android:padding`** na kontenerze głównym — zostawiam Twój szablon
> bez zmian. Przez to elementy dotykają krawędzi ekranu. W prawdziwym ekranie
> dodajesz `android:padding="16dp"` do `ConstraintLayout`.

### Wzorzec 1: jeden pod drugim

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/etName"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="16dp"
        android:hint="Wpisz swoje imię"
        android:inputType="textPersonName"
        android:maxLines="1"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/btnSend"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Wyślij"
        app:layout_constraintStart_toStartOf="@id/etName"
        app:layout_constraintTop_toBottomOf="@id/etName" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Pole ma szerokość `0dp` i powiązania do obu krawędzi ekranu, więc rozciąga się
na całą szerokość minus marginesy 16dp.

Przycisk wisi **pod polem** (`Top_toBottomOf="@id/etName"`) i jest z nim wyrównany
**do lewej** (`Start_toStartOf="@id/etName"`). Zwróć uwagę, że celem jest tu
`@id/etName`, a nie `parent` — dlatego przycisk trzyma się lewej krawędzi *pola*,
a nie lewej krawędzi ekranu. Gdyby pole miało inny margines, przycisk pojechałby razem z nim.

**Spróbuj:** zamień `Start_toStartOf="@id/etName"` na `End_toEndOf="@id/etName"`.
Przycisk przeskoczy pod prawy brzeg pola.

### Wzorzec 2: obok siebie

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btnCancel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:text="Anuluj"
        app:layout_constraintEnd_toStartOf="@+id/btnOk"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/btnOk"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="OK"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/btnCancel"
        app:layout_constraintTop_toTopOf="@id/btnCancel" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Powiązania poziome są **wzajemne**: „Anuluj" wskazuje prawym brzegiem na „OK",
a „OK" lewym brzegiem na „Anuluj". Tak powstaje **łańcuch** (rozdział 8) —
dwa przyciski przestają być niezależne i rozkładają się równo w dostępnej szerokości.

Drugi przycisk nie jest przypięty do góry ekranu, tylko do **góry pierwszego przycisku**
(`Top_toTopOf="@id/btnCancel"`). Dzięki temu oba stoją w jednej linii i wystarczy zmienić
margines w jednym miejscu, żeby przesunąć cały rząd.

> **Dlaczego `@+id/btnOk` z plusem, a `@id/btnCancel` bez?**
> Pierwszy przycisk odwołuje się do elementu, który w pliku jest **niżej** i jeszcze
> nie istnieje w chwili czytania tej linii. Plus oznacza „utwórz ten identyfikator,
> jeśli go jeszcze nie ma" i załatwia problem. Odwołania **w górę** pliku plusa
> nie potrzebują. Edytor graficzny wstawia plus wszędzie, na wszelki wypadek —
> i to też jest poprawne.

### Wzorzec 3: na środku

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btnCenter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Na środku"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Cztery sznurki o równej sile → dokładny środek ekranu.

Same powiązania `Start` + `End` to wyśrodkowanie **tylko w poziomie** i to jest
najczęstszy przypadek w prawdziwych ekranach: tytuł na górze wyśrodkowany w poziomie,
ale przypięty do góry, a nie pływający na środku.

**Spróbuj:** usuń `layout_constraintBottom_toBottomOf` i zobacz, co się stanie
(przycisk wskoczy pod górną krawędź — został mu tylko jeden sznurek pionowy).
Potem przywróć i dopisz `app:layout_constraintVertical_bias="0.2"` — przycisk
przesunie się na 20% wysokości. To jest temat następnego rozdziału.

---

## 5. Bias, czyli przesuwanie środka

Element przyciągany z obu stron domyślnie ląduje pośrodku. **Bias** przesuwa go
wzdłuż tej osi, wartością od `0.0` do `1.0`:

```xml
app:layout_constraintHorizontal_bias="0.2"   <!-- 20% od lewej -->
app:layout_constraintVertical_bias="0.9"     <!-- 90% w dół, prawie na dole -->
```

`0.5` to środek i taka jest wartość domyślna. `0.0` dosuwa do lewej (albo do góry),
`1.0` do prawej (albo do dołu).

Bias działa **tylko wtedy, gdy element ma powiązania z obu stron** i ma rozmiar
mniejszy niż dostępne miejsce. Jeśli przeciągasz element myszą w edytorze, to właśnie
bias się zmienia — i dlatego w XML-u pojawiają się dziwne wartości typu `0.497`.
Warto po takim przeciąganiu wejść w tryb Code i zaokrąglić albo usunąć.

---

## 6. Rozmiary: `wrap_content`, `dp` i magiczne `0dp`

Tu jest największa różnica względem tego, co znasz z innych kontenerów.

| Wartość | Znaczenie |
|---|---|
| `wrap_content` | tyle, ile zajmuje zawartość |
| `120dp` | dokładnie tyle |
| **`0dp`** | **rozciągnij się między swoimi powiązaniami** |
| `match_parent` | działa, ale **nie używamy go** w dzieciach ConstraintLayout |

`0dp` nie znaczy „zero". W ConstraintLayout to skrót od **`match_constraint`** —
tak zresztą nazywa się to w edytorze graficznym. Element rozciąga się od jednego
swojego powiązania do drugiego.

```xml
<EditText
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_marginStart="16dp"
    android:layout_marginEnd="16dp"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

To pole zajmie całą szerokość ekranu minus marginesy — i będzie się poprawnie
skalować na każdym urządzeniu. Z `match_parent` dostałbyś to samo na telefonie,
ale w łańcuchach i przy powiązaniach z innymi elementami `match_parent` zachowuje się
nieprzewidywalnie. Dlatego reguła jest prosta: **w ConstraintLayout `0dp`, nie `match_parent`.**

### Procent szerokości

```xml
android:layout_width="0dp"
app:layout_constraintWidth_percent="0.6"      <!-- 60% szerokości rodzica -->
```

### Proporcje

```xml
android:layout_width="0dp"
android:layout_height="0dp"
app:layout_constraintDimensionRatio="16:9"
```

Jeden wymiar jest wyliczany z drugiego. Przydaje się przy `ImageView` z miniaturą
filmu albo przy kwadratowych kafelkach (`1:1`). Wystarczy, że jeden z wymiarów jest `0dp`.

### Ograniczenia rozmiaru

```xml
app:layout_constraintWidth_min="120dp"
app:layout_constraintWidth_max="400dp"
```

Działa tylko przy `0dp`. Na tablecie pole nie rozciągnie się na absurdalną szerokość.

---

## 7. Marginesy, padding i `goneMargin`

**Margines** to odstęp **na zewnątrz** elementu — między nim a sąsiadem.
**Padding** to odstęp **wewnątrz** — między krawędzią elementu a jego zawartością.

```
   margin
  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ┐
    ┌─────────────┐
  │ │   padding   │ │
    │  ┌───────┐  │
  │ │  │ tekst │  │ │
    │  └───────┘  │
  │ │             │ │
    └─────────────┘
  └ ─ ─ ─ ─ ─ ─ ─ ─ ┘
```

```xml
android:layout_marginTop="16dp"
android:layout_marginStart="16dp"
android:layout_marginEnd="16dp"
android:layout_marginBottom="8dp"

android:padding="12dp"                 <!-- ze wszystkich stron -->
android:paddingHorizontal="16dp"       <!-- lewo i prawo -->
```

Przycisk z paddingiem ma większe pole kliknięcia — a palec to nie kursor myszy.
Wytyczne Google mówią o minimum **48dp** na element dotykalny i to jest sensowna miara.

Padding całego ekranu ustawia się raz, na kontenerze głównym:
`android:padding="16dp"` w `ConstraintLayout`.

**Margines ma znaczenie tylko wtedy, gdy po tej stronie jest powiązanie.**
`layout_marginTop` bez `constraintTop_to...` nic nie robi. To częsta pomyłka:
element „nie chce się przesunąć", bo margines nie ma się od czego odbić.

### `goneMargin`

```xml
app:layout_goneMarginTop="24dp"
```

Kiedy element, do którego jesteś przypięty, zniknie (`android:visibility="gone"`),
Twoje powiązanie przeskakuje na jego poprzednika — i wtedy obowiązuje margines `goneMargin`
zamiast zwykłego. Bez tego ekran po ukryciu czegoś potrafi się nieładnie zbić do kupy.

### Trzy stany widoczności

| Wartość | Efekt |
|---|---|
| `visible` | widoczny (domyślnie) |
| `invisible` | niewidoczny, **ale zajmuje miejsce** |
| `gone` | niewidoczny i **nie zajmuje miejsca** – reszta się przesuwa |

W Javie: `tvResult.setVisibility(View.GONE);`

---

## 8. Łańcuchy (chains)

Łańcuch powstaje, gdy **dwa lub więcej elementów wskazuje na siebie nawzajem**
powiązaniami w jednej osi. Wtedy przestają być niezależne i rozkładają się jako grupa.

Warunek jest ścisły: **pierwszy element musi być przypięty do lewej krawędzi,
ostatni do prawej, a wszystkie po drodze — do siebie nawzajem.** Jeśli choć jedno
ogniwo wskazuje w próżnię, łańcuch się nie tworzy i elementy zachowują się jak
niezależne widoki.

```
   parent                                              parent
     |                                                    |
     └──> [ A ] <──> [ B ] <──> [ C ] <───────────────────┘
        Start     End↔Start   End↔Start                End
```

Styl łańcucha ustawia się **tylko na pierwszym elemencie** (głowie łańcucha).
Wpisany na drugim czy trzecim nie robi nic — to najczęstsza pomyłka przy łańcuchach.

| Styl | Efekt |
|---|---|
| `spread` | równe odstępy wokół elementów (domyślny) |
| `spread_inside` | skrajne przy krawędziach, odstępy tylko w środku |
| `packed` | elementy zbite razem, całość wyśrodkowana |

```
spread          |  [A]   [B]   [C]  |
spread_inside   |[A]     [B]     [C]|
packed          |    [A][B][C]      |
```

### Przykład: trzy przyciski, jeden atrybut do zmiany

Kompletny plik — wklej do `activity_main.xml` i uruchom:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <!-- GŁOWA łańcucha - tylko tutaj ustawiamy chainStyle -->
    <Button
        android:id="@+id/btnOne"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:text="Jeden"
        app:layout_constraintEnd_toStartOf="@+id/btnTwo"
        app:layout_constraintHorizontal_chainStyle="spread"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/btnTwo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Dwa"
        app:layout_constraintEnd_toStartOf="@+id/btnThree"
        app:layout_constraintStart_toEndOf="@id/btnOne"
        app:layout_constraintTop_toTopOf="@id/btnOne" />

    <Button
        android:id="@+id/btnThree"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Trzy"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/btnTwo"
        app:layout_constraintTop_toTopOf="@id/btnOne" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Prześledź powiązania poziome — to jest cały mechanizm:

| Przycisk | Lewy brzeg przypięty do | Prawy brzeg przypięty do |
|---|---|---|
| `btnOne` | `parent` (krawędź ekranu) | `btnTwo` |
| `btnTwo` | `btnOne` | `btnThree` |
| `btnThree` | `btnTwo` | `parent` (krawędź ekranu) |

Pionowo każdy przycisk ma własne powiązanie: pierwszy do góry ekranu, dwa pozostałe
do **góry pierwszego przycisku**. Łańcuch zajmuje się wyłącznie osią poziomą,
o pion musisz zadbać osobno — to kolejne miejsce, w którym łatwo zapomnieć
i zobaczyć elementy w lewym górnym rogu.

**Spróbuj:** zmieniaj wartość `layout_constraintHorizontal_chainStyle` w `btnOne`
na `spread_inside` i `packed`. Uruchamiaj po każdej zmianie. Potem wpisz ten sam
atrybut w `btnTwo` zamiast w `btnOne` i zobacz, że nic się nie dzieje — bo głową
łańcucha jest pierwszy element.

### `packed` + bias

`packed` zbija elementy w jeden blok i stawia go na środku. Ten blok możesz przesunąć
biasem — wpisanym, tak jak styl, **w głowie łańcucha**:

```xml
app:layout_constraintHorizontal_chainStyle="packed"
app:layout_constraintHorizontal_bias="0.0"
```

`0.0` dosuwa cały blok do lewej, `1.0` do prawej. To najwygodniejszy sposób
na rząd przycisków wyrównany do jednej strony ekranu.

### Wagi

Gdy elementy w łańcuchu mają rozmiar `0dp`, przestają zajmować tyle, ile potrzebują,
i **dzielą dostępne miejsce według wag**. Kompletny przykład — trzy przyciski
w proporcji 1 : 2 : 1:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btnBack"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="48dp"
        android:text="Wstecz"
        app:layout_constraintEnd_toStartOf="@+id/btnSave"
        app:layout_constraintHorizontal_weight="1"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/btnSave"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Zapisz"
        app:layout_constraintEnd_toStartOf="@+id/btnNext"
        app:layout_constraintHorizontal_weight="2"
        app:layout_constraintStart_toEndOf="@id/btnBack"
        app:layout_constraintTop_toTopOf="@id/btnBack" />

    <Button
        android:id="@+id/btnNext"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginEnd="16dp"
        android:text="Dalej"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_weight="1"
        app:layout_constraintStart_toEndOf="@id/btnSave"
        app:layout_constraintTop_toTopOf="@id/btnBack" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Suma wag to 1 + 2 + 1 = 4, więc środkowy przycisk dostaje połowę szerokości,
a skrajne po jednej czwartej. Jeśli znasz `layout_weight` z `LinearLayout`,
to jest dokładnie ten sam pomysł — tylko nazwa atrybutu inna.

> **Dwie rzeczy, które trzeba tu wiedzieć.**
>
> **`0dp` jest warunkiem.** Waga przy `wrap_content` nie robi nic — element i tak
> zajmie tyle, ile ma zawartości. To samo pytanie w drugą stronę pojawi się
> na sprawdzianie: „dlaczego moje wagi nie działają?" — bo szerokość nie jest `0dp`.
>
> **Przy samych `0dp` styl łańcucha przestaje mieć znaczenie.** Elementy wypełniają
> całą dostępną szerokość, więc nie ma wolnego miejsca do rozłożenia i `spread`
> wygląda identycznie jak `packed`. Dlatego w tym przykładzie `chainStyle`
> w ogóle nie występuje — byłby atrapą.

### Łańcuchy pionowe

Wszystko powyżej działa tak samo w pionie, z inną nazwą atrybutu:

```xml
app:layout_constraintVertical_chainStyle="packed"
app:layout_constraintVertical_weight="1"
```

Ogniwa buduje się wtedy przez `Top_toBottomOf` i `Bottom_toTopOf`, a głową łańcucha
jest element **najwyższy**. Typowe zastosowanie: trzy sekcje ekranu dzielące wysokość
w proporcji 2 : 1 : 1.

### W edytorze graficznym

Zaznacz elementy, które mają tworzyć łańcuch (`Ctrl` + klik na każdym),
**prawy przycisk → Chains → Create Horizontal Chain**. Edytor sam dopisze powiązania
wzajemne i `chainStyle` w pierwszym elemencie.

Między elementami pojawi się wtedy **ikona ogniwa łańcucha**. Kliknięcie w nią
przełącza styl po kolei: `spread` → `spread_inside` → `packed` → z powrotem.
To najszybszy sposób zobaczenia różnicy — szybszy niż przepisywanie atrybutu
i uruchamianie aplikacji.

---

## 9. Elementy pomocnicze

Trzy rzeczy, które są w layoucie, ale **nie są widoczne na ekranie**. Każda z nich
oszczędza sporo kombinowania.

### Guideline — niewidoczna linia pomocnicza

```xml
<androidx.constraintlayout.widget.Guideline
    android:id="@+id/guidelineCenter"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_percent="0.5" />
```

Do takiej linii przypinasz elementy jak do krawędzi ekranu. `orientation` to `vertical`
albo `horizontal`. Pozycję podajesz na jeden z trzech sposobów:

- `app:layout_constraintGuide_percent="0.5"` — połowa ekranu (najbardziej odporne
  na zmianę rozmiaru),
- `app:layout_constraintGuide_begin="120dp"` — 120dp od lewej/góry,
- `app:layout_constraintGuide_end="80dp"` — 80dp od prawej/dołu.

Klasyczne zastosowanie: ekran podzielony na pół, w lewej połowie jedno, w prawej drugie.
Albo margines, który ma być identyczny dla dziesięciu elementów — przypinasz wszystkie
do jednej linii zamiast wpisywać margines dziesięć razy.

### Barrier — bariera, która ustępuje najdłuższemu

```xml
<androidx.constraintlayout.widget.Barrier
    android:id="@+id/barrierLabels"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:barrierDirection="end"
    app:constraint_referenced_ids="tvLabelName,tvLabelEmail,tvLabelPhone" />
```

Problem, który to rozwiązuje: masz trzy etykiety („Imię", „E-mail", „Numer telefonu")
i obok nich trzy pola. Etykiety mają różną długość. Do której przypiąć pola, żeby były
równo? Do żadnej — bo po zmianie języka albo rozmiaru czcionki najdłuższa będzie inna.

Bariera stoi zawsze za **najdłuższym** z wymienionych elementów i sama się przesuwa.
Pola przypinasz do bariery: `app:layout_constraintStart_toEndOf="@id/barrierLabels"`.

`barrierDirection` to `start`, `end`, `top` albo `bottom`.

### Group — ukrywanie wielu elementów naraz

```xml
<androidx.constraintlayout.widget.Group
    android:id="@+id/groupResult"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:visibility="gone"
    app:constraint_referenced_ids="tvResultLabel,tvResult,btnShare" />
```

Grupa nic nie pozycjonuje — tylko **przełącza widoczność** wymienionych elementów naraz.
W Javie jedna linia zamiast trzech:

```java
findViewById(R.id.groupResult).setVisibility(View.VISIBLE);
```

Typowo: sekcja wyniku ukryta do momentu, aż użytkownik coś policzy.

---

## 10. Edytor graficzny krok po kroku

Wszystko powyżej da się wyklikać i na początku **tak właśnie pracuj** — ale
w trybie **Split**, z okiem na XML. To najszybszy sposób nauczenia się nazw atrybutów:
przeciągasz, widzisz, co się dopisało.

### Co gdzie jest

| Obszar | Do czego |
|---|---|
| **Palette** (lewy górny) | elementy do przeciągnięcia, pogrupowane kategoriami |
| **Component Tree** (lewy dolny) | drzewo elementów + ostrzeżenia |
| **Design / Blueprint** (środek) | podgląd realistyczny i schemat powiązań |
| **Attributes** (prawy) | wszystkie atrybuty zaznaczonego elementu |
| pasek nad podglądem | tryby, urządzenie, orientacja, motyw, narzędzia constraints |

**Blueprint** to widok schematyczny — bez kolorów i grafik, za to z wyraźnie
widocznymi powiązaniami. Przełącznik na pasku pozwala pokazać oba widoki obok siebie.
Przy debugowaniu układu blueprint jest czytelniejszy niż podgląd.

### Tworzenie powiązania myszą

Zaznacz element. Na jego krawędziach pojawią się **okrągłe uchwyty** (po jednym
na każdym boku) i **kwadratowe** w rogach.

- **Okrągły uchwyt** → przeciągnij go na krawędź ekranu albo na inny element.
  Powstanie powiązanie (sprężynka).
- **Kwadratowy róg** → zmiana rozmiaru na sztywne `dp`. Używaj oszczędnie.
- **Uchwyt z literkami** pod elementem tekstowym → powiązanie linii bazowej.
- **Kliknięcie w istniejący uchwyt** → usuwa to powiązanie.

Po zaznaczeniu elementu w panelu **Attributes** masz u góry **kwadrat z suwakami** —
to jest sterownia rozmiarem i biasem:

- **falista linia** = `wrap_content`,
- **prosta linia ze znacznikami** = sztywne `dp`,
- **sprężynka** = `0dp` / `match_constraint`.

Klikasz w bok kwadratu, żeby przełączyć tryb. Suwaki na brzegach ustawiają bias.

### Narzędzia na pasku

| Ikona | Nazwa | Co robi |
|---|---|---|
| magnes | **Autoconnect** | sam dopina powiązania przy upuszczaniu |
| różdżka | **Infer Constraints** | dorabia powiązania do wszystkiego naraz |
| przekreślony magnes | **Clear All Constraints** | kasuje wszystkie powiązania |

**Autoconnect trzymaj wyłączony** (domyślnie jest). Zgaduje, i zwykle nie to,
o co Ci chodziło — a potem szukasz w XML-u, skąd się wzięło powiązanie, którego
nie robiłeś.

**Infer Constraints** to ratunek awaryjny: dorobi powiązania tak, żeby układ nie
rozjechał się natychmiast. Wynik jest brzydki — pełno przypadkowych biasów i marginesów
typu `37dp` — ale działa. Traktuj to jak pierwszą pomoc, nie jak metodę pracy.
Po użyciu wejdź w Code i posprzątaj.

### Przydatne skróty i sztuczki

- **Prawy przycisk na elemencie → Center → Horizontally** — wyśrodkowanie dwoma klikami.
- **Ctrl + klik** zaznacza kilka elementów, wtedy prawy przycisk → **Chains** albo **Align**.
- Pasek nad podglądem pozwala zmienić **urządzenie** i **orientację**. Zanim uznasz
  ekran za skończony, przełącz na najmniejszy telefon z listy i na poziomą orientację.
  Dwa kliknięcia, a wyłapuje większość problemów.
- Ikona z okiem → podgląd tekstów „długich" i „RTL" (język pisany od prawej).
- **Component Tree** pokazuje czerwone i żółte znaczniki. Najedź na nie — opis mówi
  dokładnie, czego brakuje.

---

## 11. Komponenty i ich atrybuty

Atrybuty, które ma **każdy** element (i których nie będę powtarzał niżej):

| Atrybut | Znaczenie |
|---|---|
| `android:id` | identyfikator, most do Javy |
| `android:layout_width` / `layout_height` | **obowiązkowe** |
| `android:layout_margin*` | odstępy zewnętrzne |
| `android:padding*` | odstępy wewnętrzne |
| `android:visibility` | `visible` / `invisible` / `gone` |
| `android:background` | kolor albo grafika tła |
| `android:enabled` | czy element reaguje na dotyk |
| `android:contentDescription` | opis dla czytnika ekranu (dostępność) |
| `android:alpha` | przezroczystość, `0.0`–`1.0` |

### TextView — tekst do czytania

```xml
<TextView
    android:id="@+id/tvTitle"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/screen_title"
    android:textSize="24sp"
    android:textStyle="bold"
    android:textColor="@color/black"
    android:gravity="center"
    android:maxLines="2"
    android:ellipsize="end"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

| Atrybut | Znaczenie |
|---|---|
| `android:text` | treść — **z `@string/`**, nie na sztywno |
| `android:textSize` | rozmiar w **`sp`** |
| `android:textStyle` | `normal`, `bold`, `italic`, `bold\|italic` |
| `android:textColor` | kolor tekstu |
| `android:textAlignment` | `textStart`, `center`, `textEnd` |
| `android:gravity` | jak ułożyć **zawartość wewnątrz** elementu |
| `android:maxLines` | maksymalna liczba linii |
| `android:ellipsize="end"` | za długi tekst kończy się wielokropkiem |
| `android:fontFamily` | krój pisma |

> **`gravity` kontra `layout_gravity`.** `gravity` ustawia **zawartość wewnątrz**
> elementu (tekst w środku etykiety). `layout_gravity` ustawia **element wewnątrz
> rodzica** — i w ConstraintLayout go nie używamy, bo od pozycjonowania są powiązania.
> Mylenie tych dwóch to klasyk; nazwa `layout_` zawsze oznacza „rozmawiam z rodzicem".

### EditText — tekst do wpisywania

```xml
<EditText
    android:id="@+id/etEmail"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:hint="@string/hint_email"
    android:inputType="textEmailAddress"
    android:maxLines="1"
    android:imeOptions="actionNext"
    android:autofillHints="emailAddress"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintTop_toBottomOf="@id/tvTitle" />
```

W palecie szukaj kategorii **Text** — `EditText` występuje tam pod nazwami opisującymi
typ: *Plain Text*, *Password*, *E-mail*, *Number*. To wszystko ten sam `EditText`,
różniący się wyłącznie atrybutem `inputType`.

| Atrybut | Znaczenie |
|---|---|
| `android:hint` | szara podpowiedź, znika po rozpoczęciu pisania |
| `android:inputType` | **rodzaj klawiatury i walidacja wejścia** |
| `android:maxLines="1"` | pole jednolinijkowe |
| `android:imeOptions` | co robi klawisz Enter (`actionNext`, `actionDone`, `actionSearch`) |
| `android:text` | wartość początkowa (zwykle pusta) |

Wartości `inputType`, które będą Ci potrzebne:

| Wartość | Klawiatura |
|---|---|
| `text` | zwykła |
| `textPersonName` | zwykła, z podpowiedziami imion |
| `textEmailAddress` | z `@` i `.com` |
| `textPassword` | kropki zamiast znaków |
| `textMultiLine` | wielolinijkowa, Enter robi nową linię |
| `number` | tylko cyfry |
| `numberDecimal` | cyfry z przecinkiem |
| `numberSigned` | cyfry z minusem |
| `phone` | układ telefonu |

**`inputType` nie zwalnia z walidacji.** Klawiatura numeryczna nie gwarantuje,
że pole nie jest puste, a użytkownik może wkleić cokolwiek. `Integer.parseInt("")`
rzuca `NumberFormatException` i aplikacja gaśnie. Zawsze sprawdzasz w Javie.

### Button — przycisk

```xml
<Button
    android:id="@+id/btnCalculate"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/btn_calculate"
    android:textSize="16sp"
    android:backgroundTint="@color/purple_500"
    android:enabled="true"
    app:icon="@drawable/ic_check"
    app:layout_constraintTop_toBottomOf="@id/etEmail"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

| Atrybut | Znaczenie |
|---|---|
| `android:text` | napis |
| `android:backgroundTint` | kolor tła (**nie** `background` — ten kasuje efekt dotyku) |
| `android:enabled="false"` | wyszarzony, nie reaguje |
| `app:icon` | ikona obok napisu (przycisk Material) |
| `android:textAllCaps="false"` | wyłącza automatyczne wersaliki |

Twój `<Button>` w projekcie z domyślnym motywem jest w rzeczywistości **przyciskiem
Material** — stąd zaokrąglone rogi, cień i atrybuty z przedrostkiem `app:`.
W palecie znajdziesz też warianty: *Button (Outlined)*, *Button (Text)*.

### ImageView — obrazek

```xml
<ImageView
    android:id="@+id/ivLogo"
    android:layout_width="120dp"
    android:layout_height="120dp"
    android:src="@drawable/logo"
    android:scaleType="centerCrop"
    android:contentDescription="@string/desc_logo"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

| `scaleType` | Zachowanie |
|---|---|
| `fitCenter` | zmieści cały obraz, może zostać puste miejsce (domyślne) |
| `centerCrop` | wypełni całe pole, przytnie nadmiar — **najczęściej ten** |
| `center` | oryginalny rozmiar, wyśrodkowany |
| `fitXY` | rozciągnie na siłę — **deformuje obraz, nie używaj** |

Grafikę wrzucasz do `res/drawable`. Nazwa pliku: małe litery i podkreślniki,
bez polskich znaków. Ikony systemowe dodaje się przez prawy przycisk na `res`
→ *New → Vector Asset*.

**`contentDescription` jest obowiązkowy** dla obrazków niosących treść — czyta go
czytnik ekranu osobom niewidomym. Dla czysto dekoracyjnych wpisujesz
`android:importantForAccessibility="no"`. Android Studio ostrzega o braku
i ma rację.

### CheckBox, Switch — włącz/wyłącz

```xml
<CheckBox
    android:id="@+id/cbTerms"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/accept_terms"
    android:checked="false"
    app:layout_constraintTop_toBottomOf="@id/btnCalculate"
    app:layout_constraintStart_toStartOf="parent" />

<com.google.android.material.materialswitch.MaterialSwitch
    android:id="@+id/swDarkMode"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/dark_mode"
    app:layout_constraintTop_toBottomOf="@id/cbTerms"
    app:layout_constraintStart_toStartOf="parent" />
```

Różnią się tylko wyglądem i zwyczajem: **CheckBox** do zaznaczania pozycji na liście
(„dodatki: mleko, cukier"), **Switch** do włączania ustawienia, które działa od razu
(„tryb ciemny"). Oba mają `android:checked` i w Javie `isChecked()` / `setChecked()`.

### RadioGroup i RadioButton — jedno z kilku

```xml
<RadioGroup
    android:id="@+id/rgSize"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintTop_toBottomOf="@id/cbTerms"
    app:layout_constraintStart_toStartOf="parent">

    <RadioButton
        android:id="@+id/rbSmall"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/size_small"
        android:checked="true" />

    <RadioButton
        android:id="@+id/rbLarge"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/size_large" />
</RadioGroup>
```

`RadioButton` **musi** siedzieć w `RadioGroup` — to ona pilnuje, żeby zaznaczona była
tylko jedna opcja. Sam `RadioGroup` jest zwykłym elementem ConstraintLayout i potrzebuje
swoich dwóch powiązań; wewnątrz układa dzieci jak `LinearLayout`, stąd `android:orientation`.

To jedyny przypadek na tej lekcji, w którym zagnieżdżamy kontener w kontenerze —
i jest uzasadniony, bo grupa nie jest tylko pojemnikiem, tylko pilnuje logiki wyboru.

### SeekBar i ProgressBar — suwak i pasek postępu

```xml
<SeekBar
    android:id="@+id/sbTip"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:max="30"
    android:progress="10"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintTop_toBottomOf="@id/rgSize" />

<ProgressBar
    android:id="@+id/pbLoading"
    style="?android:attr/progressBarStyleLarge"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:visibility="gone"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```

`SeekBar` to suwak, który przesuwa użytkownik. `ProgressBar` to wskaźnik, którym
sterujesz Ty — kręcące się kółko na czas ładowania (domyślnie) albo pasek z procentem
(`style="?android:attr/progressBarStyleHorizontal"`). Trzymaj go z `visibility="gone"`
i pokazuj tylko wtedy, gdy coś faktycznie trwa.

### Pozostałe, na zapoznanie

| Element | Do czego | Uwaga |
|---|---|---|
| `Spinner` | lista rozwijana | wymaga adaptera w Javie |
| `RatingBar` | ocena gwiazdkami | `android:numStars`, `android:rating` |
| `Space` | pusty odstęp | rzadko potrzebny, są marginesy |
| `View` | kolorowa kreska/separator | `layout_height="1dp"` + `background` |
| `ScrollView` | przewijanie długiej treści | dokładniej przy layoutach |
| `RecyclerView` | długie listy danych | osobna lekcja |

---

## 12. Kompletny przykład: kalkulator napiwku

Ekran wykorzystuje: guideline, łańcuch, `0dp`, bias, grupę i bazową linię.

### `res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="@string/screen_title"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/etAmount"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:hint="@string/hint_amount"
        android:inputType="numberDecimal"
        android:maxLines="1"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTitle" />

    <TextView
        android:id="@+id/tvPercentLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="@string/label_percent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/etAmount" />

    <!-- wyrównanie do linii bazowej etykiety, mimo innego rozmiaru tekstu -->
    <TextView
        android:id="@+id/tvPercentValue"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:textStyle="bold"
        app:layout_constraintBaseline_toBaselineOf="@id/tvPercentLabel"
        app:layout_constraintEnd_toEndOf="parent"
        tools:text="10%" />

    <SeekBar
        android:id="@+id/sbPercent"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:max="30"
        android:progress="10"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvPercentLabel" />

    <CheckBox
        android:id="@+id/cbRound"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:text="@string/round_up"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/sbPercent" />

    <!-- dwa przyciski w łańcuchu: 1/3 i 2/3 szerokości -->
    <Button
        android:id="@+id/btnClear"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:layout_marginEnd="8dp"
        android:text="@string/btn_clear"
        app:layout_constraintEnd_toStartOf="@id/btnCalculate"
        app:layout_constraintHorizontal_chainStyle="spread_inside"
        app:layout_constraintHorizontal_weight="1"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/cbRound" />

    <Button
        android:id="@+id/btnCalculate"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="@string/btn_calculate"
        app:layout_constraintBottom_toBottomOf="@id/btnClear"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_weight="2"
        app:layout_constraintStart_toEndOf="@id/btnClear"
        app:layout_constraintTop_toTopOf="@id/btnClear" />

    <TextView
        android:id="@+id/tvResultLabel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="40dp"
        android:text="@string/label_result"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/btnClear" />

    <TextView
        android:id="@+id/tvResult"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:textSize="32sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvResultLabel"
        tools:text="27,50 zł" />

    <androidx.constraintlayout.widget.Group
        android:id="@+id/groupResult"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="gone"
        app:constraint_referenced_ids="tvResultLabel,tvResult" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### `res/values/strings.xml`

```xml
<resources>
    <string name="app_name">Napiwek</string>
    <string name="screen_title">Kalkulator napiwku</string>
    <string name="hint_amount">Kwota rachunku</string>
    <string name="label_percent">Napiwek</string>
    <string name="round_up">Zaokrąglij w górę</string>
    <string name="btn_clear">Wyczyść</string>
    <string name="btn_calculate">Policz</string>
    <string name="label_result">Do zapłaty</string>
    <string name="percent_format">%1$d%%</string>
    <string name="result_format">%1$.2f zł</string>
    <string name="error_empty_amount">Podaj kwotę rachunku</string>
</resources>
```

### `MainActivity.java`

```java
package com.example.napiwek;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.EditText;
import android.widget.SeekBar;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.constraintlayout.widget.Group;

public class MainActivity extends AppCompatActivity {

    private EditText etAmount;
    private SeekBar sbPercent;
    private TextView tvPercentValue;
    private TextView tvResult;
    private CheckBox cbRound;
    private Group groupResult;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etAmount = findViewById(R.id.etAmount);
        sbPercent = findViewById(R.id.sbPercent);
        tvPercentValue = findViewById(R.id.tvPercentValue);
        tvResult = findViewById(R.id.tvResult);
        cbRound = findViewById(R.id.cbRound);
        groupResult = findViewById(R.id.groupResult);

        Button btnCalculate = findViewById(R.id.btnCalculate);
        Button btnClear = findViewById(R.id.btnClear);

        showPercent(sbPercent.getProgress());

        sbPercent.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                showPercent(progress);
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) { }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) { }
        });

        btnCalculate.setOnClickListener(v -> calculate());
        btnClear.setOnClickListener(v -> clear());
    }

    private void showPercent(int percent) {
        tvPercentValue.setText(getString(R.string.percent_format, percent));
    }

    private void calculate() {
        String text = etAmount.getText().toString().trim();

        if (text.isEmpty()) {
            Toast.makeText(this, R.string.error_empty_amount, Toast.LENGTH_SHORT).show();
            return;
        }

        double amount = Double.parseDouble(text.replace(',', '.'));
        double total = amount + amount * sbPercent.getProgress() / 100.0;

        if (cbRound.isChecked()) {
            total = Math.ceil(total);
        }

        tvResult.setText(getString(R.string.result_format, total));
        groupResult.setVisibility(View.VISIBLE);
    }

    private void clear() {
        etAmount.setText("");
        sbPercent.setProgress(10);
        cbRound.setChecked(false);
        groupResult.setVisibility(View.GONE);
    }
}
```

Trzy rzeczy warte zauważenia w kodzie:

**`SeekBar.OnSeekBarChangeListener` to klasa anonimowa, nie lambda** — interfejs
ma trzy metody, więc skrót lambdą nie zadziała. Dwie z nich zostawiamy puste
i to jest w porządku; musimy je napisać, bo interfejs tego wymaga.

**`text.replace(',', '.')`** — na polskiej klawiaturze numerycznej użytkownik wpisze
przecinek, a `Double.parseDouble` rozumie tylko kropkę. Bez tej zamiany aplikacja
gaśnie z `NumberFormatException` przy pierwszej kwocie z groszami.

**`groupResult.setVisibility(...)`** — jedna linia przełącza dwa elementy naraz.
To po to jest `Group`.

---

## 13. Częste błędy

| Objaw | Przyczyna | Rozwiązanie |
|---|---|---|
| Element w lewym górnym rogu po uruchomieniu | brak powiązania w jednej z osi | dodaj brakujące, sprawdź Component Tree |
| Margines „nie działa" | nie ma powiązania po tej stronie | margines działa tylko wzdłuż powiązania |
| Element zniknął z ekranu | `layout_width="0dp"` bez powiązań z obu stron | `0dp` wymaga dwóch powiązań w tej osi |
| Układ inny w edytorze niż na telefonie | atrybuty `tools:` albo brak powiązań | szukaj `tools:layout_editor_absoluteX` |
| Elementy nachodzą na siebie | dwa przypięte do tej samej krawędzi | jeden przypnij **do drugiego**, nie do rodzica |
| Po obróceniu telefonu bałagan | wszystko przypięte do góry, brak łańcuchów | przetestuj w edytorze w poziomie |
| `Cannot resolve symbol '@id/...'` | literówka albo odwołanie do elementu zdefiniowanego niżej | w odwołaniach do elementów niżej użyj `@+id/` |
| Tekst ucina się w połowie | `wrap_content` w za wąskim miejscu | `0dp` + `maxLines` + `ellipsize` |
| Ekran nie mieści się w poziomie | za dużo treści, brak przewijania | `ScrollView` — następna lekcja |
| Przycisk trudno kliknąć | za mały | minimum 48dp, dodaj padding |

> **Nietypowa, ale częsta:** odwołanie do elementu, który w pliku XML jest **niżej**.
> Android Studio czasem podkreśla `@id/cosNizej` na czerwono. Zapis `@+id/cosNizej`
> w odwołaniu rozwiązuje problem (plus tworzy identyfikator, jeśli go jeszcze nie ma).
> Dlatego edytor graficzny wszędzie wstawia `@+id/`.

---

## 14. Ściąga

**Powiązania**

```xml
app:layout_constraintTop_toBottomOf="@id/x"      pod elementem x
app:layout_constraintBottom_toTopOf="@id/x"      nad elementem x
app:layout_constraintStart_toEndOf="@id/x"       po prawej od x
app:layout_constraintEnd_toStartOf="@id/x"       po lewej od x
app:layout_constraintStart_toStartOf="parent"    do lewej krawędzi ekranu
app:layout_constraintBaseline_toBaselineOf="@id/x"   wspólna linia tekstu
```

**Wyśrodkowanie w poziomie** = `Start_toStartOf="parent"` + `End_toEndOf="parent"`
**Wyśrodkowanie w pionie** = `Top_toTopOf="parent"` + `Bottom_toBottomOf="parent"`

**Rozmiary**

| Zapis | Znaczenie |
|---|---|
| `wrap_content` | do zawartości |
| `48dp` | sztywno |
| `0dp` | rozciągnij między powiązaniami |
| `0dp` + `layout_constraintWidth_percent="0.6"` | 60% rodzica |
| `0dp` + `layout_constraintDimensionRatio="1:1"` | kwadrat |

**Pozostałe**

| Zapis | Znaczenie |
|---|---|
| `layout_constraintHorizontal_bias="0.2"` | przesunięcie środka |
| `layout_constraintHorizontal_chainStyle="packed"` | styl łańcucha |
| `layout_constraintHorizontal_weight="2"` | udział w łańcuchu |
| `layout_goneMarginTop="16dp"` | margines, gdy sąsiad zniknął |
| `Guideline` + `layout_constraintGuide_percent` | niewidoczna linia |
| `Barrier` + `barrierDirection` | ustępuje najdłuższemu |
| `Group` + `constraint_referenced_ids` | ukrywa kilka naraz |

**Edytor:** Split zamiast Design · Autoconnect wyłączony · Infer Constraints tylko awaryjnie ·
prawy przycisk → Center → Horizontally · zanim skończysz: przełącz na mały telefon i na poziom.

---

## 15. Zadania

Jak zawsze: osobne repozytorium, `README.md` ze zrzutem ekranu, commit po każdym kroku,
push na koniec zajęć.

**Zadanie 1 — sześć ustawień**
Nowy projekt. Umieść na ekranie sześć przycisków, **wyłącznie powiązaniami**,
bez sztywnych współrzędnych: w czterech rogach ekranu, jeden dokładnie na środku,
jeden w 1/4 wysokości i 3/4 szerokości (użyj biasu).
Po każdym przycisku commit. Sprawdź w edytorze na małym telefonie i na tablecie.

**Zadanie 2 — czytanie XML-a**
Weź layout z zadania 1 i w `docs/opis.md` opisz **własnymi słowami**, po jednym zdaniu,
co robi każdy atrybut przy przycisku środkowym i przy tym z biasem.
Bez kopiowania z tego dokumentu.

**Zadanie 3 — formularz z barierą**
Ekran rejestracji: trzy etykiety o wyraźnie różnej długości („Imię", „E-mail",
„Numer telefonu komórkowego") i obok każdej pole `EditText`.
Pola mają zaczynać się w jednej linii pionowej **niezależnie od długości etykiet** —
użyj `Barrier`. Pola rozciągnięte do prawej krawędzi (`0dp`).
Każde pole z właściwym `inputType`. Etykiety wyrównane do linii bazowej swoich pól.

**Zadanie 4 — łańcuch i style**
Trzy przyciski w rzędzie u dołu ekranu, w łańcuchu poziomym.
Zrób **trzy zrzuty ekranu** — po jednym dla `spread`, `spread_inside` i `packed` —
wrzuć do `docs/` i opisz w `README.md`, czym się różnią.
Na koniec zostaw `spread` z wagami 1 : 2 : 1.

**Zadanie 5 — ekran logowania**
Kompletny ekran: logo (`ImageView`, kwadrat przez `DimensionRatio`), pole e-mail,
pole hasła (`textPassword`), `CheckBox` „Zapamiętaj mnie", przycisk „Zaloguj"
na całą szerokość minus marginesy, pod nim mniejszy przycisk tekstowy
„Nie pamiętam hasła".
Wszystkie teksty w `strings.xml`. Sprawdź wygląd w orientacji poziomej i opisz
w `README.md`, co się psuje (bo coś się zepsuje — to jest część zadania).

**Zadanie 6 — kalkulator napiwku**
Zbuduj ekran z rozdziału 12. Najpierw sam layout, commit. Potem podpięcie widoków,
commit. Potem `SeekBar`, commit. Potem obliczenia i `Group`, commit.
Dodaj od siebie jedną rzecz, której nie ma w przykładzie: podział rachunku
na kilka osób (`EditText` z `inputType="number"` i druga linia wyniku „na osobę").

**Zadanie 7 — `[bez AI]`**
Dostajesz ode mnie zrzut ekranu gotowej aplikacji. Odtwórz ten układ w ConstraintLayout
bez pomocy agenta i bez czatu — dokumentacja i ten materiał wolno.
Commit z prefiksem `[bez AI]`. Na następnych zajęciach opowiadasz, przy którym
elemencie się zaciąłeś i jak to rozwiązałeś.
