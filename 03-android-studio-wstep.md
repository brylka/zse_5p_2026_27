# Android Studio – wstęp

**Technik programista | klasa 5 | aplikacje mobilne**

Pierwszy projekt: `MainActivity.java`, `activity_main.xml`, manifest, kliknięcie przycisku.

---

## 1. Co się właściwie zmienia względem czwartej klasy

Java zostaje. Klasy, metody, `if`, pętle, `String`, typy – wszystko to samo.
Zmieniają się trzy rzeczy i warto je nazwać od razu, bo inaczej pierwsze dwa tygodnie
są jednym wielkim „ale dlaczego".

**Nie ma `main()`.** W aplikacji konsolowej Ty decydujesz, co się dzieje i kiedy:
program startuje od `main`, leci w dół, kończy się. Aplikacja mobilna nie ma początku
i końca w tym sensie. System Android ją tworzy, pokazuje, chowa, wznawia i zabija –
a Ty tylko piszesz metody, które system wywoła, **kiedy uzna za stosowne**.
Pierwsza z nich nazywa się `onCreate()`.

**Wygląd jest w osobnym pliku.** W konsoli `System.out.println` był i logiką,
i interfejsem. Tutaj wygląd ekranu opisujesz w XML-u, a Java tylko reaguje.
Dwa pliki, dwa języki, jeden ekran.

**Program czeka.** Aplikacja po uruchomieniu nic nie robi. Stoi. Dopiero kliknięcie,
przesunięcie palcem czy obrót telefonu wywołuje Twój kod. To się nazywa **programowanie
sterowane zdarzeniami** i jest to najważniejsza koncepcja tego roku.

```
   uruchomienie ──> onCreate() ──> [ ekran stoi i czeka ]
                                          │
                        klik ─────────────┤──> Twoja metoda
                        klik ─────────────┤──> Twoja metoda
                     zamknięcie ──────────┘
```

---

## 2. Android Studio – co to jest i co się instaluje

Android Studio to oficjalne IDE Google'a. Pod spodem to IntelliJ IDEA (ten sam silnik,
co PyCharm), więc skróty klawiszowe i wygląd znasz. Do tego dochodzą rzeczy specyficzne
dla Androida:

| Składnik | Do czego |
|---|---|
| **Android SDK** | biblioteki Androida – klasy `Activity`, `Button`, `TextView`... |
| **Gradle** | system budowania: zamienia Twoje pliki w gotowy plik `.apk` |
| **Emulator (AVD)** | wirtualny telefon na Twoim komputerze |
| **JDK** | maszyna Javy, instaluje się razem ze Studio, nie musisz nic dobierać |
| **Logcat** | strumień komunikatów z urządzenia – tu widać błędy |

Aktualna wersja stabilna na wrzesień 2026 to **Android Studio Quail 4**.
Nazwy wydań zmieniają się co kilka miesięcy (Ladybug, Meerkat, Narwhal, Panda, Quail...) –
nie przywiązuj się do nazwy, w materiałach z internetu spotkasz każdą z nich
i wszystkie działają tak samo w zakresie, który nas interesuje.

Pobranie: https://developer.android.com/studio

> **Gradle.** Przy pierwszym otwarciu projektu na dole zobaczysz pasek postępu
> i napis *Gradle Sync*. Gradle sprawdza wtedy, jakich bibliotek potrzebuje projekt,
> i ściąga je z internetu. Za pierwszym razem potrafi to trwać kilka minut.
> **Nie klikaj nic, dopóki nie skończy** – projekt w połowie synchronizacji wygląda
> jak zepsuty, choć zepsuty nie jest.

---

## 3. Nowy projekt krok po kroku

*File -> New -> New Project*

### Krok 1: szablon

Zobaczysz kafelki. **Wybierasz „Empty Views Activity".**

To jest miejsce, w którym myli się mniej więcej każdy, bo tuż obok jest kafelek
o bardzo podobnej nazwie:

| Szablon | Co daje | Czy nasz |
|---|---|---|
| **Empty Views Activity** | layout w XML, `MainActivity`, `findViewById` | **TAK** |
| Empty Activity | Jetpack Compose – interfejs pisany w Kotlinie, bez XML | nie |
| No Activity | pusty projekt bez żadnego ekranu | nie |
| Basic Views Activity | XML + gotowa nawigacja między dwoma ekranami | nie teraz |

Słowo **„Views"** w nazwie oznacza właśnie ten klasyczny, XML-owy sposób budowania
interfejsu. Jetpack Compose to nowsze podejście Google'a – nie jest gorsze, ale
jest w Kotlinie i wymaga wiedzy, której jeszcze nie masz. Wrócimy do niego pod koniec
roku porównawczo.

### Krok 2: ustawienia projektu

| Pole | Co wpisać |
|---|---|
| **Name** | `Pierwsza Aplikacja` – to zobaczy użytkownik pod ikoną |
| **Package name** | `com.example.pierwszaaplikacja` – małe litery, bez polskich znaków |
| **Save location** | domyślna jest OK, zapamiętaj ścieżkę – tam robisz `git init` |
| **Language** | **Java** ← koniecznie sprawdź, domyślnie bywa Kotlin |
| **Minimum SDK** | **API 24 (Android 7.0)** |
| **Build configuration language** | Kotlin DSL albo Groovy DSL – obojętne, zostaw domyślne |

**Nazwa pakietu** to unikalny identyfikator Twojej aplikacji w całym świecie Androida.
Konwencja to odwrócony adres domeny: firma `example.com` z aplikacją `Kalkulator`
daje `com.example.kalkulator`. W sklepie Google Play nie mogą istnieć dwie aplikacje
o tej samej nazwie pakietu. Na razie `com.example.cokolwiek` wystarczy, ale kiedy
będziemy publikować, wrócimy do tematu.

**Minimum SDK** mówi, na jak starych telefonach aplikacja się zainstaluje.
Im niżej, tym więcej urządzeń, ale tym więcej rzeczy trzeba obsługiwać po staremu.
API 24 to rozsądny kompromis i pod tym oknem Android Studio sam pokazuje,
ilu procent urządzeń to dotyczy.

Klikasz **Finish** i czekasz na Gradle Sync.

---

## 4. Co się wygenerowało

W panelu po lewej u góry jest lista rozwijana z widokiem projektu. Domyślnie stoi na
**Android** – to widok uproszczony, ukrywa część katalogów i grupuje pliki tematycznie.
Przełącznik na **Project** pokazuje prawdziwą strukturę katalogów na dysku.
Na początek pracuj w widoku **Android**, ale wiedz, że to nie jest to samo,
co zobaczysz w eksploratorze plików.

Widok **Android** wygląda tak:

```
app
├── manifests
│   └── AndroidManifest.xml           ← metryczka aplikacji
├── java
│   └── com.example.pierwszaaplikacja
│       └── MainActivity.java         ← Twój kod
└── res                               ← zasoby (resources)
    ├── drawable                      ← grafiki
    ├── layout
    │   └── activity_main.xml         ← wygląd ekranu
    ├── mipmap                        ← ikony aplikacji
    ├── values
    │   ├── colors.xml
    │   ├── strings.xml               ← teksty
    │   └── themes.xml                ← motyw, kolory systemowe
    └── xml
Gradle Scripts
├── build.gradle.kts (Project)
├── build.gradle.kts (Module :app)    ← minSdk, zależności
├── gradle.properties
└── libs.versions.toml                ← wersje bibliotek
```

Trzy pliki z tej listy musisz rozumieć dobrze: **`activity_main.xml`**,
**`MainActivity.java`** i **`AndroidManifest.xml`**. Resztą zajmiemy się w swoim czasie.

> **Dlaczego layout nazywa się `activity_main.xml`, a klasa `MainActivity.java`?**
> Bo to dwie różne konwencje nazewnicze. Klasy w Javie: `PascalCase`, rzeczownik na
> końcu mówi, czym to jest – `MainActivity`, `SettingsActivity`. Pliki zasobów:
> małe litery i podkreślniki, a **kategoria na początku**, żeby pliki tego samego typu
> stały obok siebie na liście – `activity_main`, `activity_settings`, `dialog_potwierdzenie`.
> To nie jest przypadek ani niekonsekwencja, tylko dwie różne konwencje, których
> Android trzyma się od lat. Nazwa pliku zasobu **musi** być z małych liter, cyfr
> i podkreślników – wielka litera to błąd kompilacji.

---

## 5. `activity_main.xml` – wygląd ekranu

Szablon generuje coś takiego (skracam pusty `TextView` do sedna):

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvHello"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Linia po linii:

**`<?xml version...?>`** – nagłówek każdego pliku XML. Nie ruszasz.

**`ConstraintLayout`** – element **korzeniowy**, czyli pojemnik na wszystko inne.
W pliku layoutu może być tylko jeden taki element najwyższego poziomu, a wszystkie
widoki są w środku. ConstraintLayout układa elementy według **powiązań** –
„ten przycisk przyklejony do lewej krawędzi ekranu i pod tym polem tekstowym".
Szczegółowo zajmiemy się nim na następnej lekcji; dziś traktujemy go jak pudełko.

**`xmlns:android`, `xmlns:app`, `xmlns:tools`** – deklaracje **przestrzeni nazw**.
To one nadają sens przedrostkom w atrybutach:

| Przedrostek | Skąd atrybut | Przykład |
|---|---|---|
| `android:` | wbudowany w system Android | `android:text` |
| `app:` | z biblioteki dołączonej do projektu | `app:layout_constraintTop_toTopOf` |
| `tools:` | **tylko dla podglądu w Android Studio**, znika przy budowaniu | `tools:text` |

Ten trzeci jest sprytny: `tools:text="Przykładowy wynik"` pokazuje tekst w podglądzie,
żeby móc ocenić wygląd, ale w działającej aplikacji tego tekstu nie ma.

**`android:id="@+id/main"`** – identyfikator. Wrócimy do tego za chwilę,
bo to najważniejszy atrybut w całym dokumencie.

**`android:layout_width` / `android:layout_height`** – **każdy** widok w Androidzie
musi je mieć. Zawsze. Trzy możliwe wartości:

| Wartość | Znaczenie |
|---|---|
| `match_parent` | zajmij tyle, ile daje rodzic (zwykle: cały ekran wszerz) |
| `wrap_content` | zajmij tyle, ile trzeba na zawartość |
| `120dp` | konkretny rozmiar |

**`tools:context=".MainActivity"`** – informacja dla edytora, do której klasy należy
ten layout. Znowu `tools:`, więc w aplikacji nie ma znaczenia – ale dzięki temu
Studio wie, jaki motyw i pasek narzędzi narysować w podglądzie.

### `dp` i `sp` – dlaczego nie piksele

Telefony mają różne ekrany. Ten sam przycisk o szerokości 200 pikseli na tanim
telefonie zajmie połowę ekranu, a na dobrym – jedną czwartą.

**`dp`** (*density-independent pixel*) to jednostka niezależna od gęstości.
System sam przelicza ją na piksele danego ekranu, więc `48dp` wygląda tak samo
na każdym urządzeniu. Wszystkie rozmiary, marginesy i odstępy podajesz w `dp`.

**`sp`** (*scale-independent pixel*) to to samo, ale dodatkowo skalowane ustawieniem
rozmiaru czcionki w systemie. Ktoś, kto słabo widzi, powiększa sobie tekst w ustawieniach
telefonu i Twoja aplikacja się dostosowuje. **Rozmiary tekstu podajesz w `sp`, resztę w `dp`.**

Pikseli (`px`) nie używasz nigdy.

---

## 6. `MainActivity.java` – kod ekranu

```java
package com.example.pierwszaaplikacja;

import android.os.Bundle;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });
    }
}
```

Wygląda gęsto, ale połowa to szablon, którego nie ruszasz. Po kolei:

**`extends AppCompatActivity`** – Twoja klasa **dziedziczy** po klasie systemowej.
`Activity` to jeden ekran aplikacji. `AppCompatActivity` to jej wersja zgodna
ze starszymi Androidami – dzięki niej nowoczesny wygląd działa też na telefonie
z 2018 roku. Dziedziczenie z czwartej klasy – tutaj widzisz, po co ono w praktyce jest:
dostajesz za darmo kilkadziesiąt metod obsługujących cykl życia ekranu.

**`@Override protected void onCreate(...)`** – to jest ta metoda, którą **wywołuje
system**, nie Ty. Android tworzy ekran i mówi „proszę, przygotuj się".
Adnotacja `@Override` oznacza, że nadpisujesz metodę z klasy nadrzędnej.
Tu wpisujesz wszystko, co ma się wydarzyć raz, przy starcie ekranu.

**`super.onCreate(savedInstanceState)`** – wywołanie wersji z klasy nadrzędnej.
**Bez tego aplikacja nie wystartuje** – to `AppCompatActivity` wykonuje tu całe
przygotowanie ekranu. Zawsze pierwsza linia.

**`Bundle savedInstanceState`** – „paczka" z zapisanym stanem ekranu.
Kiedy obrócisz telefon, Android niszczy ekran i tworzy go od nowa; tutaj wraca to,
co zdążyłeś zapisać. Na razie ignorujemy, wrócimy przy cyklu życia Activity.

**`EdgeToEdge.enable(this)`** – aplikacja rysuje się pod paskiem stanu i paskiem
nawigacji, na całym ekranie. Tak wyglądają współczesne aplikacje.

**`setContentView(R.layout.activity_main)`** – **najważniejsza linia w tym pliku.**
Mówi: „wyglądem tego ekranu jest layout `activity_main`". Dopiero po jej wykonaniu
elementy z XML-a w ogóle istnieją. Wszystko, co robisz z widokami, robisz **po** niej.

**`ViewCompat.setOnApplyWindowInsetsListener(...)`** – konsekwencja edge-to-edge.
Skoro aplikacja rysuje się pod paskiem stanu, trzeba dołożyć wewnętrzny odstęp,
żeby Twój tekst nie schował się pod zegarkiem i baterią. Ten blok robi dokładnie to
i **zostawiasz go w spokoju**. Wystarczy, że wiesz, po co jest – bo za tydzień zapytam,
a odpowiedź „żeby zawartość nie wchodziła pod pasek systemowy" jest w zupełności wystarczająca.

### Czym jest `R`

`R` to klasa **generowana automatycznie** przez Gradle przy każdym budowaniu.
Zawiera numeryczne identyfikatory wszystkiego, co leży w katalogu `res/`.

```
res/layout/activity_main.xml    ->   R.layout.activity_main
android:id="@+id/btnAdd"      ->   R.id.btnAdd
res/values/strings.xml          ->   R.string.app_name
res/drawable/logo.png           ->   R.drawable.logo
```

Nigdy nie edytujesz `R` ręcznie. Ale rozumiesz mechanizm: **nazwa z XML-a staje się
polem w Javie.** Stąd bierze się jedna z najczęstszych awarii początkującego –
jeśli `R` świeci się na czerwono i „nie można rozwiązać symbolu", to prawie zawsze
znaczy, że **gdzieś w plikach XML jest błąd składni** i generator nie mógł się
uruchomić. Nie szukaj wtedy błędu w Javie. Szukaj w XML-u.

---

## 7. `AndroidManifest.xml` – metryczka aplikacji

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.PierwszaAplikacja">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

    </application>

</manifest>
```

Manifest to dokument, który system Android czyta **przed** uruchomieniem aplikacji.
Odpowiada na pytania: jak się nazywa, jaką ma ikonę, z jakich ekranów się składa,
od którego zacząć, czego potrzebuje.

| Element | Znaczenie |
|---|---|
| `android:label="@string/app_name"` | nazwa pod ikoną, pobierana z `strings.xml` |
| `android:icon` | ikona z katalogu `mipmap` |
| `android:theme` | motyw – kolory, czcionki, wygląd przycisków |
| `<activity android:name=".MainActivity">` | **deklaracja ekranu** |
| `android:exported="true"` | czy inne aplikacje mogą ten ekran uruchomić |
| `<intent-filter>` z `MAIN` + `LAUNCHER` | **to jest ekran startowy** |

Dwie rzeczy zapamiętaj na przyszłość, bo obie się zemszczą:

**Każdy nowy ekran musi być wpisany w manifeście.** Kiedy dodasz drugą Activity,
a zapomnisz o wpisie, aplikacja skompiluje się bez problemu i wywali się dopiero
przy próbie przejścia, z komunikatem `ActivityNotFoundException`. Android Studio
zwykle dopisuje to samo, gdy tworzysz ekran przez *New -> Activity* – ale agent LLM
tworzący plik `.java` ręcznie już nie.

**Uprawnienia też są tutaj.** Internet, lokalizacja, aparat, pliki – wszystko wymaga
wpisu `<uses-permission>` przed `<application>`. Bez tego dostaniesz `SecurityException`.
Dojdziemy do tego, gdy będziemy pobierać dane z sieci.

Para `MAIN` + `LAUNCHER` w `<intent-filter>` mówi systemowi: „ten ekran pokaż,
gdy użytkownik dotknie ikony". Jeśli usuniesz ten blok, aplikacja się zainstaluje,
ale nie będzie miała ikony w menu i nie da się jej uruchomić. Dokładnie jeden ekran
w aplikacji ma taki wpis.

---

## 8. Gdzie tu jest MVC

Znasz podział MVC z aplikacji webowych: model – dane, widok – wygląd, kontroler –
logika łącząca jedno z drugim. W Androidzie ten podział da się zobaczyć, ale
**jest niepełny** i uczciwiej powiedzieć to od razu, niż udawać.

| Warstwa | W naszym projekcie | Plik |
|---|---|---|
| **View** – wygląd | layout XML, zasoby | `res/layout/activity_main.xml` |
| **Controller** – reakcja na zdarzenia | Activity | `MainActivity.java` |
| **Model** – dane i logika | Twoje własne klasy, później baza | jeszcze nie ma |

Analogia działa dobrze w jednym miejscu: **XML jest widokiem, tak jak szablon HTML
jest widokiem w Django czy we Flasku.** Opisuje, co ma być na ekranie, i nie zawiera
logiki. To jest ten sam podział, który znasz, i to jest powód, dla którego warto
o MVC w ogóle tu wspominać.

A gdzie się rozjeżdża:

**Activity jest kontrolerem i częścią widoku naraz.** Kiedy piszesz
`tvResult.setText("Cześć")`, kontroler bezpośrednio maluje po ekranie. W czystym MVC
kontroler tego nie robi – przekazuje dane widokowi i widok się sam odrysowuje.
Przy dużej aplikacji Activity puchnie do tysiąca linii, w których wszystko jest ze
wszystkim wymieszane. To znany problem i ma nazwę: *God Activity*.

**Model na początku nie istnieje.** W naszej pierwszej aplikacji cała „logika"
to dwa dodawania w metodzie obsługi kliknięcia. Model pojawi się, kiedy zaczniemy
robić własne klasy (`Produkt`, `Zadanie`, `Pomiar`) i bazę danych.

**Android oficjalnie zaleca dziś MVVM**, nie MVC – z osobnym `ViewModel`, który
przechowuje dane niezależnie od ekranu i przeżywa obrót telefonu. Zobaczysz to
nazewnictwo w każdej dokumentacji Google'a i w każdym ogłoszeniu o pracę.

Czego się trzymać na teraz:

> **Wygląd w XML. Reakcje w Javie. Nie mieszamy.**
> Nie ustawiamy rozmiarów i kolorów z poziomu Javy, jeśli da się to zrobić w XML-u.
> Nie wpisujemy tekstów na sztywno w kodzie, jeśli mogą być w `strings.xml`.
> Ta jedna zasada załatwia 80% tego, po co MVC w ogóle wymyślono.

---

## 9. Uruchamianie aplikacji

### Emulator

*Tools -> Device Manager -> Create Virtual Device*. Wybierasz model telefonu
(np. Pixel), wersję Androida (pobierze się przy pierwszym razie, kilka GB),
klikasz *Finish*.

Emulator to pełna maszyna wirtualna z Androidem. Wymaga włączonej wirtualizacji
w BIOS-ie i sporo pamięci. Pierwsze uruchomienie potrafi trwać dwie–trzy minuty;
**nie zamykaj go między testami**, kolejne uruchomienia aplikacji to już kilka sekund.

### Telefon fizyczny

Szybszy, wygodniejszy i po prostu przyjemniejszy w pracy. Trzy kroki:

1. *Ustawienia -> Informacje o telefonie* -> stuknij **siedem razy** w „Numer kompilacji".
   Pojawi się komunikat o odblokowaniu opcji programisty.
2. *Ustawienia -> Opcje programistyczne* -> włącz **Debugowanie USB**.
3. Podłącz kabel, na telefonie potwierdź „Zezwól na debugowanie USB".

Urządzenie pojawi się na liście u góry Android Studio, obok przycisku *Run*.

### Uruchomienie

Zielona strzałka **Run** (`Shift + F10`). Gradle zbuduje aplikację, wgra ją
na urządzenie i włączy. Postęp widzisz w panelu *Build* na dole.

### Logcat

Panel *Logcat* to strumień komunikatów z urządzenia – ze wszystkich aplikacji
i z samego systemu, więc jest tego dużo. Filtruj po nazwie swojego pakietu.
Kiedy aplikacja gaśnie, tutaj jest odpowiedź dlaczego: czerwony blok zaczynający się
od `FATAL EXCEPTION`. Czytaj od góry, szukaj linii z nazwą swojego pliku i numerem linii.

Własne komunikaty wypisujesz tak:

```java
import android.util.Log;

Log.d("MainActivity", "kliknięto przycisk, wartość = " + value);
```

To jest odpowiednik `System.out.println` z czwartej klasy i będziesz go używał
dokładnie tak samo często.

---

## 10. Edytor layoutu – przeciąganie elementów

Otwórz `activity_main.xml`. W prawym górnym rogu są trzy tryby:

| Tryb | Kiedy |
|---|---|
| **Code** | sam XML – najszybszy, gdy wiesz, co piszesz |
| **Split** | XML i podgląd obok siebie – **domyślnie pracuj tu** |
| **Design** | sam podgląd, przeciąganie myszką |

Elementy interfejsu przeciągasz z panelu **Palette** (po lewej) na podgląd ekranu.
Pod paletą jest **Component Tree** – drzewo elementów, przydatne, gdy coś schowa się
pod czymś innym. Po prawej **Attributes** – wszystkie atrybuty zaznaczonego elementu.

Na dziś wystarczy Ci pięć pozycji z palety:

| Element | Co to |
|---|---|
| `TextView` | etykieta – tekst do wyświetlenia |
| `EditText` (*Plain Text* w palecie) | pole do wpisywania |
| `Button` | przycisk |
| `ImageView` | obrazek |
| `CheckBox` | pole wyboru |

> **Pułapka ConstraintLayout.** Element przeciągnięty na podgląd i niepowiązany
> z niczym wygląda w edytorze dobrze – ale po uruchomieniu **wskakuje w lewy górny róg**,
> bo system nie wie, gdzie go umieścić. Studio ostrzega o tym żółtym trójkątem.
> Doraźne rozwiązanie: przycisk **Infer Constraints** (magiczna różdżka na pasku
> nad podglądem) dorabia powiązania automatycznie. Porządne rozwiązanie – następna lekcja.

**Pracuj w trybie Split i patrz, co się dzieje w XML-u**, gdy coś przeciągasz albo
zmieniasz w panelu Attributes. To najszybszy sposób nauczenia się nazw atrybutów:
klikasz w interfejsie, widzisz linijkę XML-a, która się dopisała.

---

## 11. `id` – most między XML-em a Javą

Żeby dostać się do elementu z kodu, element musi mieć identyfikator.

```xml
android:id="@+id/btnGreet"
```

Rozbiór tego zapisu:

| Fragment | Znaczenie |
|---|---|
| `@` | „odwołaj się do zasobu" |
| `+` | „**utwórz** nowy identyfikator, jeszcze go nie ma" |
| `id/` | rodzaj zasobu |
| `btnGreet` | nazwa, którą wymyślasz Ty |

**Plus jest tylko przy tworzeniu.** Gdy później się do tego id odwołujesz –
np. w powiązaniu ConstraintLayout – piszesz bez plusa: `app:layout_constraintTop_toBottomOf="@id/btnGreet"`.
(Studio często wstawia plus również tam i to działa, ale wiedz, jaka jest różnica.)

### Konwencja nazw, której się trzymamy

| Przedrostek | Element | Przykład |
|---|---|---|
| `tv` | TextView | `tvResult` |
| `et` | EditText | `etName` |
| `btn` | Button | `btnGreet` |
| `iv` | ImageView | `ivLogo` |
| `cb` | CheckBox | `cbTerms` |

Nie jest to wymóg Androida, tylko powszechna konwencja. Sens jest praktyczny:
w Javie piszesz `btn` i podpowiadanie kodu pokazuje Ci wszystkie przyciski w projekcie.

> **Nazwy w kodzie piszemy po angielsku.** Klasy, metody, zmienne, id widoków,
> nazwy w `strings.xml` – wszystko. `btnGreet`, nie `btnPrzywitaj`.
> Po polsku zostają dwie rzeczy: **teksty widoczne dla użytkownika** (czyli wartości
> w `strings.xml`) i **komentarze**.
>
> To nie jest moje widzimisię. Cały Android – nazwy klas, metod, atrybutów XML –
> jest po angielsku, więc mieszanka `etImie.setText()` czyta się fatalnie.
> Do tego kod z polskimi nazwami jest niepokazywalny: rekruter, kolega z zespołu
> i model językowy, którego o coś pytasz, rozumieją `etName` bez tłumaczenia.
> W ogłoszeniach o pracę „kod po angielsku" jest wymogiem, nie preferencją.
>
> Polskie znaki w identyfikatorach są dodatkowo zakazane technicznie – nazwa pliku
> zasobu i id widoku mogą zawierać tylko małe litery, cyfry i podkreślnik.
> `@+id/przyciskWyślij` to błąd kompilacji.

### Pobranie elementu w Javie

```java
Button btnGreet = findViewById(R.id.btnGreet);
```

`findViewById` przeszukuje aktualnie ustawiony layout i zwraca znaleziony widok.
Dwie zasady, których złamanie kończy się tym samym błędem:

1. **Zawsze po `setContentView`.** Przed nią żadnego layoutu jeszcze nie ma
   i metoda zwróci `null`.
2. **Id musi się zgadzać co do znaku.** Literówka nie jest błędem kompilacji,
   jeśli takie id gdzieś istnieje – jest awarią przy uruchomieniu.

Objaw w obu przypadkach identyczny:

```
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.TextView.setText(java.lang.CharSequence)'
on a null object reference
```

„Na obiekcie, który jest `null`" – czyli `findViewById` nic nie znalazł.
Ten wyjątek zobaczysz w tym roku wielokrotnie; naucz się go rozpoznawać od razu.

---

## 12. Obsługa kliknięcia

Są trzy sposoby. Używamy pierwszego, ale znać trzeba wszystkie, bo w kodzie z internetu
i od agentów spotkasz każdy.

### Sposób 1: lambda – nasz domyślny

```java
btnGreet.setOnClickListener(v -> {
    tvResult.setText("Kliknięto!");
});
```

Czytaj to jako: „przycisk `btnGreet`, weź sobie tę instrukcję i wykonaj ją,
kiedy ktoś Cię kliknie". `v` to widok, który został kliknięty – przydaje się,
gdy jedna metoda obsługuje kilka przycisków.

### Sposób 2: klasa anonimowa – to samo, dłużej

```java
btnGreet.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        tvResult.setText("Kliknięto!");
    }
});
```

Tak wyglądał kod Androida przed Javą 8 i tak wygląda większość starszych tutoriali.
Robi dokładnie to samo. Lambda ze sposobu 1 to skrócony zapis właśnie tej konstrukcji –
`OnClickListener` ma tylko jedną metodę, więc kompilator wie, o którą chodzi.

Jedna pułapka: wewnątrz klasy anonimowej `this` oznacza **tę klasę anonimową**,
nie Twoją Activity. Jeśli potrzebujesz Activity (np. w `Toast`), piszesz `MainActivity.this`.
W lambdzie tego problemu nie ma – `this` dalej oznacza Activity.

### Sposób 3: `android:onClick` w XML

```xml
<Button
    android:id="@+id/btnGreet"
    android:onClick="onGreetClick"
    ... />
```

```java
public void onGreetClick(View view) {
    tvResult.setText("Kliknięto!");
}
```

Wygląda najprościej i dlatego jest w wielu starszych materiałach – ale ma wadę:
powiązanie istnieje tylko jako **napis w XML-u**. Jeśli zmienisz nazwę metody w Javie,
kompilator nic nie zauważy, a aplikacja wywali się przy kliknięciu.
Do tego metoda musi być publiczna i przyjmować `View`. Google odradza to podejście.
Znaj je, nie używaj.

---

## 13. Pełny przykład

Ekran: pole na imię, przycisk, etykieta z powitaniem. Walidacja pustego pola.

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
    android:padding="24dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:text="@string/screen_title"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/etName"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:hint="@string/hint_name"
        android:inputType="textPersonName"
        android:textSize="18sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTitle" />

    <Button
        android:id="@+id/btnGreet"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="24dp"
        android:text="@string/btn_greet"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/etName" />

    <TextView
        android:id="@+id/tvResult"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="32dp"
        android:textSize="20sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/btnGreet"
        tools:text="Cześć, Anna!" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

Dwie rzeczy warte zauważenia:

`android:layout_width="0dp"` przy `EditText` **nie oznacza zerowej szerokości**.
W ConstraintLayout `0dp` znaczy „rozciągnij się między swoimi powiązaniami" –
tutaj od lewej krawędzi do prawej. To jedna z tych rzeczy, które trzeba po prostu
zapamiętać; szerzej na następnej lekcji.

`tools:text` przy `tvResult` daje przykładowy tekst w podglądzie. W uruchomionej
aplikacji etykieta jest pusta, dopóki nie klikniesz przycisku.

### `res/values/strings.xml`

```xml
<resources>
    <string name="app_name">Pierwsza Aplikacja</string>
    <string name="screen_title">Powitanie</string>
    <string name="hint_name">Wpisz swoje imię</string>
    <string name="btn_greet">Przywitaj się</string>
    <string name="greeting">Cześć, %1$s!</string>
    <string name="error_empty_name">Najpierw wpisz imię</string>
</resources>
```

Wszystkie teksty widoczne dla użytkownika trzymamy tutaj, nie na sztywno w XML-ie
ani w Javie. Android Studio podkreśla tekst wpisany na sztywno żółtym ostrzeżeniem
(*Hardcoded string*) i ma rację. Powody są dwa: tłumaczenie aplikacji na inny język
sprowadza się wtedy do dodania drugiego pliku, a poprawka literówki – do zmiany
w jednym miejscu zamiast szukania po całym projekcie.

### `MainActivity.java`

```java
package com.example.pierwszaaplikacja;

import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

public class MainActivity extends AppCompatActivity {

    // pola klasy - dostępne we wszystkich metodach
    private EditText etName;
    private Button btnGreet;
    private TextView tvResult;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        // połączenie pól z elementami layoutu - ZAWSZE po setContentView
        etName = findViewById(R.id.etName);
        btnGreet = findViewById(R.id.btnGreet);
        tvResult = findViewById(R.id.tvResult);

        // co ma się stać po kliknięciu
        btnGreet.setOnClickListener(v -> greet());
    }

    private void greet() {
        String name = etName.getText().toString().trim();

        if (name.isEmpty()) {
            Toast.makeText(this, R.string.error_empty_name, Toast.LENGTH_SHORT).show();
            return;
        }

        tvResult.setText(getString(R.string.greeting, name));
    }
}
```

Fragmenty, o które będę pytał:

**`private EditText etName;`** – pole klasy, nie zmienna lokalna. Gdybyś napisał
`EditText etName = findViewById(...)` wewnątrz `onCreate`, zmienna zniknęłaby po
zakończeniu tej metody i w `greet()` nie byłoby do niej dostępu. To jest zwykły
zasięg zmiennych z czwartej klasy, tylko w nowym kontekście.

**`etName.getText().toString()`** – `getText()` **nie zwraca `String`**, tylko obiekt
typu `Editable` (tekst, który da się edytować w locie). Do porównań i działań
potrzebny jest `String`, stąd `.toString()`. To jest jedna z tych rzeczy, które trzeba
zobaczyć raz i zapamiętać.

**`.trim()`** – obcina spacje z początku i końca. Bez tego samo naciśnięcie spacji
przechodzi walidację jako „niepuste".

**`getString(R.string.greeting, name)`** – tekst powitania też jest w `strings.xml`,
a nie sklejany w Javie. `%1$s` w zasobie to **miejsce na pierwszy argument typu `String`**;
`getString` wstawia w to miejsce zmienną `name`. Efekt ten sam co `"Cześć, " + name + "!"`,
ale tekst zostaje tam, gdzie jego miejsce – i przy tłumaczeniu aplikacji nie trzeba
grzebać w kodzie. Przy liczbie użyłbyś `%1$d`.

**`Toast`** – krótki komunikat, który sam znika. Trzy argumenty: kontekst (`this`,
czyli ta Activity), treść i czas (`LENGTH_SHORT` albo `LENGTH_LONG`).
**`.show()` na końcu jest obowiązkowe** – bez niego `Toast` się tworzy i nic nie robi.
To najczęściej zapominana kropka w całym Androidzie.

**`return;` w środku metody** – przerywa jej wykonanie. Wynik nie zostanie ustawiony,
bo dane były złe. Prosty wzorzec walidacji, którego będziemy używać stale.

**`btnGreet.setOnClickListener(v -> greet());`** – logika jest w osobnej metodzie,
a nie wpisana w lambdę. Przy trzech linijkach nie robi to różnicy, ale nawyk jest dobry:
`onCreate` ma być krótkie i czytelne jak spis treści.

---

## 14. Częste błędy

| Objaw | Przyczyna | Rozwiązanie |
|---|---|---|
| `R` na czerwono, „cannot resolve symbol R" | błąd składni w którymś pliku XML | popraw XML, potem *Build -> Clean Project* i *Rebuild* |
| `NullPointerException` przy pierwszym użyciu widoku | `findViewById` przed `setContentView` albo zła nazwa id | sprawdź kolejność i przepisz id znak po znaku |
| Aplikacja gaśnie od razu po uruchomieniu | wyjątek w `onCreate` | **Logcat**, pierwsza linia `Caused by:` |
| Przycisk wskoczył w lewy górny róg | brak powiązań w ConstraintLayout | *Infer Constraints* albo dodaj je ręcznie |
| Zmieniłem XML, nic się nie zmieniło | aplikacja nie została przebudowana | *Run* jeszcze raz, nie odświeżenie emulatora |
| „Sync now" na żółtym pasku | zmiana w plikach Gradle | kliknij *Sync Now* i poczekaj |
| Nowy ekran: `ActivityNotFoundException` | brak wpisu `<activity>` w manifeście | dopisz do `AndroidManifest.xml` |
| Emulator nie startuje | wyłączona wirtualizacja w BIOS-ie | użyj telefonu przez USB, zgłoś się do mnie |
| Kod z `fun` i `val`, nie kompiluje się | to Kotlin, nie Java | patrz dokument 02 |

**Gdy nic nie pomaga, kolejność jest zawsze taka:**
1. *Build -> Clean Project*
2. *Build -> Rebuild Project*
3. *File -> Sync Project with Gradle Files*
4. *File -> Invalidate Caches / Restart*

Punkt 4 rozwiązuje zaskakująco dużo dziwnych problemów i zajmuje minutę.

---

## 15. Ściąga

| Rzecz | Zapis |
|---|---|
| Ustawienie layoutu | `setContentView(R.layout.activity_main);` |
| Nowe id w XML | `android:id="@+id/btnOk"` |
| Odwołanie do istniejącego id | `@id/btnOk` |
| Pobranie widoku | `Button btn = findViewById(R.id.btnOk);` |
| Kliknięcie | `btn.setOnClickListener(v -> { ... });` |
| Odczyt pola | `String s = etName.getText().toString().trim();` |
| Zapis etykiety | `tvResult.setText("tekst");` |
| Liczba z pola | `int n = Integer.parseInt(etNumber.getText().toString());` |
| Komunikat | `Toast.makeText(this, "tekst", Toast.LENGTH_SHORT).show();` |
| Log | `Log.d("MainActivity", "wartość = " + x);` |
| Tekst z zasobów | `@string/btn_greet` w XML, `R.string.btn_greet` w Javie |
| Tekst z zasobów z parametrem | `getString(R.string.greeting, name)` |
| Uruchomienie | `Shift + F10` |

Rozmiary: **`dp`** dla wymiarów i odstępów, **`sp`** dla tekstu, `px` nigdy.

Szerokość i wysokość: `match_parent`, `wrap_content`, konkretne `dp`,
a w ConstraintLayout dodatkowo `0dp` = „rozciągnij między powiązaniami".

---

## 16. Zadania

Każde zadanie to **osobne repozytorium** na Twoim GitHubie, z `README.md`
i zrzutem ekranu. Commitujesz każdy krok, na koniec zajęć push.

**Zadanie 1 – pierwszy projekt**
Utwórz projekt *Empty Views Activity* w Javie, minSdk 24, nazwa `Powitanie`.
Uruchom na emulatorze lub telefonie – ma się pokazać „Hello World!".
Zainicjuj repozytorium (patrz dokument 01) i zrób pierwszy commit.
W `README.md` napisz, jakiego szablonu użyłeś i **dlaczego nie „Empty Activity"**.

**Zadanie 2 – rozbiór na części**
W pliku `docs/opis.md` opisz własnymi słowami, **po jednym zdaniu**, do czego służy:
`setContentView`, `findViewById`, `R`, `onCreate`, `super.onCreate`,
`AndroidManifest.xml`, `@+id/`, `match_parent`, `dp`, `sp`.
Bez kopiowania z tego dokumentu – swoimi słowami. To jest zadanie na zrozumienie,
nie na przepisywanie.

**Zadanie 3 – powitanie**
Zbuduj ekran z przykładu z rozdziału 13: pole na imię, przycisk, etykieta z powitaniem,
`Toast` przy pustym polu. Wszystkie teksty w `strings.xml`.
Minimum cztery commity opisujące kolejne kroki.

**Zadanie 4 – przeciąganie**
Dołóż do ekranu z zadania 3, **przeciągając z palety w trybie Design**:
drugi przycisk „Wyczyść" i `CheckBox` „Krzycz" (`cbShout`).
Przycisk „Wyczyść" czyści pole i etykietę. Gdy `CheckBox` jest zaznaczony,
powitanie wyświetla się wielkimi literami (`toUpperCase()`).
Po każdym przeciągnięciu **zajrzyj w tryb Code** i sprawdź, co dopisało się w XML-u.

**Zadanie 5 – kalkulator BMI**
Nowy projekt. Dwa pola (waga w kg, wzrost w cm), przycisk, etykieta z wynikiem.
BMI = waga / (wzrost w metrach)². Wynik z dokładnością do jednego miejsca po przecinku
(`String.format("%.1f", bmi)`). Puste pole albo zero – `Toast`, nie awaria.
Pod wynikiem druga etykieta z kategorią: niedowaga / norma / nadwaga.

**Zadanie 6 – manifest**
W projekcie z zadania 5 zmień nazwę aplikacji widoczną pod ikoną na „Kalkulator BMI"
(przez `strings.xml`, nie na sztywno w manifeście). Sprawdź na urządzeniu.
Potem **usuń tymczasowo** blok `<intent-filter>`, uruchom aplikację i opisz
w `docs/manifest.md`, co się stało i dlaczego. Przywróć i zacommituj obie wersje
jako osobne commity.

**Zadanie 7 – łapanie błędu `[bez AI]`**
Zepsuj celowo aplikację na trzy sposoby, za każdym razem uruchamiając ją i notując
komunikat z Logcatu:
1. `findViewById` z nieistniejącym id,
2. `findViewById` przed `setContentView`,
3. `Toast` bez `.show()`.

W `docs/bledy.md` wklej po pięć pierwszych linii z Logcatu dla przypadków 1 i 2
oraz opisz, co się dzieje w przypadku 3 (podpowiedź: aplikacja **nie** wywala się).
Commit z prefiksem `[bez AI]`.