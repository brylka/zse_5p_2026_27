# Agenci LLM – jak z nich korzystać na tych zajęciach

**Technik programista | klasa 5 | aplikacje mobilne**

---

## Stanowisko na wstępie

**Wolno Wam używać modeli językowych.** Nie udaję, że ich nie ma, i nie zamierzam
sprawdzać, czy kod napisała Twoja ręka. W firmie, do której pójdziesz, wszyscy ich
używają. Zabranianie tego byłoby uczeniem Was zawodu, który już nie istnieje.

Warunek jest jeden i nie ma od niego odstępstw:

> **Na kolejnych zajęciach dostajesz pytania o swój kod. Każdą linię masz umieć
> wyjaśnić: co robi, dlaczego jest, co się stanie, jak ją usunę.**
> Wiesz – super, nieważne kto ją napisał. Nie wiesz – zadanie do poprawy.

To nie jest podchwytliwe. Pytania są proste: „po co tu jest `findViewById`?",
„dlaczego `setContentView` jest przed tą linią, a nie po?", „co to jest `R`?".
Jeśli przeczytałeś kod, który wygenerował agent, odpowiesz bez problemu.
Jeśli tylko go skopiowałeś – od razu widać.

Dodatkowo: **co jakiś czas jedno zadanie robisz bez AI**, z oznaczeniem `[bez AI]`
w treści commita. W styczniu na egzaminie zawodowym agenta nie będzie, klawiatura
i dokumentacja będą.

---

## Dwa rodzaje narzędzi

Warto rozróżnić, bo działają zupełnie inaczej.

**Czat** (Claude, ChatGPT, Gemini w przeglądarce) – rozmawiasz, dostajesz tekst,
kopiujesz do edytora. Model nie widzi Twojego projektu. Wszystko, co ma wiedzieć,
musisz wkleić. Dobre do pytań („czym się różni `gravity` od `layout_gravity`?"),
słabe do zmian w kodzie.

**Agent** (Claude Code, Codex CLI, Gemini CLI, Gemini wbudowany w Android Studio) –
działa **w katalogu Twojego projektu**. Sam czyta pliki, sam je zmienia, sam uruchamia
komendy. Nie kopiujesz nic. Mówisz „dodaj przycisk czyszczący pola" i za chwilę
`activity_main.xml` i `MainActivity.java` są zmienione.

Ta druga kategoria jest znacznie potężniejsza i znacznie bardziej niebezpieczna.
Agent, któremu każesz „posprzątaj projekt", może usunąć pliki, których potrzebowałeś.
Dlatego **commitujesz przed każdym większym poleceniem dla agenta.** Wtedy powrót
to jedna komenda, a nie odtwarzanie z pamięci.

---

## Instalacja agentów CLI

Wszystkie trzy instalują się przez `npm`, więc potrzebny jest Node.js w wersji 22 lub nowszej.

```bash
node --version        # jeśli brak: https://nodejs.org (LTS)
```

```bash
# Claude Code (Anthropic)
npm install -g @anthropic-ai/claude-code
claude                       # uruchomienie w katalogu projektu

# Codex CLI (OpenAI)
npm install -g @openai/codex
codex

# Gemini CLI (Google)
npm install -g @google/gemini-cli
gemini
```

Każdy przy pierwszym uruchomieniu otworzy przeglądarkę i poprosi o zalogowanie.
Uruchamiasz je **w katalogu projektu** – agent widzi to, co jest poniżej katalogu,
w którym go włączyłeś.

Do tego Android Studio ma **Gemini wbudowane w IDE** (panel z boku, ikona iskierki).
Nie trzeba nic instalować, zna kontekst otwartego pliku i potrafi wstawiać zmiany
bezpośrednio do edytora. Na szkolnych komputerach to zwykle najprostsza droga.

> **Komputery są wspólne.** Na koniec zajęć: `/logout` w sesji agenta albo usunięcie
> katalogu z tokenem (`C:\Users\NAZWA\.claude`, `.codex`, `.gemini`). W Android Studio
> wyloguj się z konta Google. Cudzy token to cudzy limit i cudze rozmowy.

---

## `AGENTS.md` – instrukcja dla agenta

Agent domyślnie nie wie nic o zasadach naszych zajęć. Zapytany o kod Androida,
z dużym prawdopodobieństwem odpowie w **Kotlinie** i użyje **Jetpack Compose** –
bo tak wygląda dziś większość dokumentacji Google'a. My piszemy w **Javie**
z layoutami **XML**. Musisz mu to powiedzieć.

Zamiast powtarzać to w każdej rozmowie, kładziesz w katalogu głównym projektu plik
`AGENTS.md`. Agenci CLI czytają go automatycznie przy starcie.

```markdown
# Zasady projektu

Projekt szkolny – Android, klasa 5 technikum programisty.

## Technologie – obowiązkowe

- Język: **Java** (nie Kotlin)
- Interfejs: **layouty XML** (nie Jetpack Compose)
- Dostęp do widoków: **findViewById** (nie View Binding, nie Data Binding)
- minSdk 24, kompilacja pod aktualne SDK z szablonu

## Jak masz pracować

- Małe kroki. Jedna zmiana naraz, potem czekasz na moje potwierdzenie.
- Nie dodawaj bibliotek bez pytania.
- Nie zmieniaj plików Gradle bez pytania.
- Nie zmieniaj plików, o które nie prosiłem.
- Komentarze w kodzie po polsku, krótkie, tylko tam gdzie coś nieoczywistego.
- Nazwy w kodzie **po angielsku** – klasy, metody, zmienne, id w XML, nazwy zasobów.

## Czego NIE robisz

- Nie commitujesz. Commity robię sam.
- Nie uruchamiasz `git push`, `git reset --hard`, `rm -rf`.
- Nie generujesz całych ekranów naraz – mam to rozumieć linia po linii.

## Konwencja nazw

- Wszystkie identyfikatory po angielsku: klasy, metody, zmienne, id widoków,
  nazwy zasobów. Po polsku tylko teksty widoczne dla użytkownika i komentarze.
- id widoków: `btnSave`, `tvResult`, `etName`, `ivLogo`
- pola klasy odpowiadają id: `private Button btnSave;`
- nazwy w `strings.xml` po angielsku, wartości po polsku:
  `<string name="error_empty_name">Najpierw wpisz imię</string>`
```

Ten plik **jest w repozytorium** i jest częścią projektu. Traktuj go jak konfigurację –
kiedy zauważysz, że agent uparcie robi coś nie tak, dopisujesz linijkę zamiast
poprawiać go za każdym razem.

*Uwaga techniczna:* część narzędzi historycznie czytała własne nazwy plików
(`CLAUDE.md`, `GEMINI.md`). Jeśli Twój agent nie reaguje na `AGENTS.md`, zrób drugi
plik o jego nazwie z jedną linią: `Zasady projektu opisane są w AGENTS.md – przeczytaj ten plik.`

---

## Jak pisać polecenia

Zła prośba i dobra prośba różnią się jedną rzeczą: **ilością kontekstu**.

**Źle:**
> zrób kalkulator

Dostaniesz 200 linii Kotlina w Compose, z bibliotekami, których nie znasz,
i strukturą, której nie rozumiesz. Formalnie „działa". Do niczego Ci to nie służy.

**Dobrze:**
> W `activity_main.xml` mam już dwa pola `EditText` o id `etNumber1` i `etNumber2`,
> przycisk `btnAdd` i `TextView` o id `tvResult`. Dodaj w `MainActivity.java`
> obsługę kliknięcia przycisku: pobierz obie liczby, dodaj je, wpisz wynik do `tvResult`.
> Java, `findViewById`, `setOnClickListener`. Nie zmieniaj XML-a.
> Wyjaśnij mi przy okazji, dlaczego `getText()` trzeba zamieniać na `String`.

Dobre polecenie zawiera cztery rzeczy:

1. **Stan wyjściowy** – co już jest, jak się nazywa.
2. **Cel** – co ma być po zmianie.
3. **Ograniczenia** – w czym, czego nie ruszać, czego nie używać.
4. **Prośbę o wyjaśnienie** – bo za tydzień będę pytał.

Punkt 4 jest tym, który zamienia agenta z automatu do przepisywania w korepetytora.
Dopisuj go zawsze.

---

## Typowe wpadki LLM w Androidzie

To nie są rzadkie przypadki. To są rzeczy, które zdarzą Ci się **w pierwszym miesiącu**.

| Objaw | Co się stało | Co zrobić |
|---|---|---|
| Kod z `fun`, `val`, `?:` | To jest Kotlin, nie Java | „Przepisz w Javie" + wpis w `AGENTS.md` |
| `@Composable`, `setContent { }` | Jetpack Compose zamiast XML | „Layout ma być w XML, dostęp przez `findViewById`" |
| `AsyncTask`, `startActivityForResult` | Przestarzałe API sprzed lat | Zapytaj o współczesny odpowiednik i **sprawdź w dokumentacji** |
| Czerwona nazwa atrybutu w XML | Atrybut wymyślony albo z innej biblioteki | Skasuj, poszukaj prawdziwego w panelu *Attributes* |
| `ClassNotFoundException` po dodaniu ekranu | Nowa Activity bez wpisu w `AndroidManifest.xml` | Dopisz `<activity android:name=".NazwaActivity" />` |
| `SecurityException` przy internecie/GPS | Brak uprawnienia w manifeście | Dodaj `<uses-permission>`, przy GPS też prośbę w czasie działania |
| „Sync now" i błąd Gradle | Agent dopisał zależność w złej wersji | `git restore` na plikach Gradle, dodaj ręcznie |
| Aplikacja się buduje i od razu gaśnie | Wyjątek w `onCreate` – zwykle `findViewById` zwrócił `null` | **Logcat**, filtr po nazwie pakietu, czytaj pierwszą linię `Caused by:` |

Wzorzec jest jeden: **agent nie widzi ekranu Twojego telefonu.**
Nie wie, że przycisk wyszedł poza ekran, że tekst jest biały na białym, że aplikacja
gaśnie po dwóch sekundach. Zna kod, nie zna efektu. Testowanie należy do Ciebie
i tego się nie da oddelegować.

---

## Logcat, czyli jak w ogóle rozmawiać o błędzie

Kiedy aplikacja gaśnie, nie pisz agentowi „nie działa". To nic nie znaczy.
Otwórz **Logcat** (dolny panel w Android Studio), znajdź czerwony blok, skopiuj go
w całości – od linii `FATAL EXCEPTION` w dół, razem z `Caused by:` – i wklej.

```
FATAL EXCEPTION: main
Process: com.example.kalkulator, PID: 12345
java.lang.NullPointerException: Attempt to invoke virtual method
'void android.widget.TextView.setText(java.lang.CharSequence)'
on a null object reference
    at com.example.kalkulator.MainActivity.onCreate(MainActivity.java:31)
```

Ten tekst mówi wszystko: co (`NullPointerException`), gdzie (`MainActivity.java:31`),
na czym (`TextView` jest `null`, czyli `findViewById` nic nie znalazł – zwykle literówka
w id albo wywołanie przed `setContentView`).

Nauka czytania Logcatu jest bardziej wartościowa niż jakikolwiek agent.
Zanim wkleisz błąd komukolwiek, **przeczytaj go sam**. W połowie przypadków
zrozumiesz go szybciej, niż zdążysz sformułować pytanie.

---

## Bezpieczeństwo

- **Nie wklejaj danych osobowych** – swoich, kolegów, rodziny. Ani do czatu,
  ani do agenta. Treść rozmowy wychodzi poza szkolny komputer.
- **Nie dawaj agentowi tokenów ani haseł.** Jeśli projekt czegoś takiego wymaga,
  trzymasz to w pliku ignorowanym przez Gita i mówisz agentowi, że taki plik istnieje –
  bez pokazywania zawartości.
- **Uważaj z trybami automatycznymi.** Każdy agent ma przełącznik „nie pytaj mnie
  o zgodę" (`--yolo`, „auto-approve", „full access"). Na zajęciach go nie używamy.
  Chcesz widzieć każdą zmianę, zanim się wykona.
- **Commituj przed poleceniem dla agenta.** To jest Twoja kopia zapasowa
  i to jest powód, dla którego zasada mikro-commitów z dokumentu 01 jest tak ważna.
- **Na koniec zajęć wyloguj się** z każdego agenta, którego uruchomiłeś.

---

## Jak to oceniam

| Sytuacja | Ocena |
|---|---|
| Kod napisał agent, Ty rozumiesz każdą linię i umiesz ją zmienić | Pełna liczba punktów |
| Kod napisał agent, umiesz opowiedzieć ogólnie, gubisz się w szczegółach | Do uzupełnienia, pytam ponownie |
| Kod napisał agent, nie umiesz wyjaśnić podstaw | Zadanie do poprawy, następne bez AI |
| Kod napisałeś sam, są błędy, ale rozumiesz, co robiłeś | Punkty + omawiamy błędy |
| Zadanie `[bez AI]` zrobione z AI | Zero punktów |

Historia commitów jest częścią oceny. Pięć commitów opisujących kolejne kroki mówi mi,
że budowałeś aplikację i rozumiałeś, co dodajesz. Jeden commit na 400 linii, trzy minuty
przed dzwonkiem, mówi mi coś zupełnie innego – i wtedy pytania są dłuższe.

---

## Zadania

**Zadanie 1 – `AGENTS.md`**
Załóż w swoim projekcie plik `AGENTS.md` według wzoru z tego dokumentu. Dopisz do niego
przynajmniej dwie **własne** reguły, których nie ma we wzorze. Commit z opisem,
push.

**Zadanie 2 – ten sam prompt, dwa razy**
Poproś agenta o obsługę kliknięcia przycisku najpierw jednym zdaniem („dodaj przycisk
który wyświetla tekst"), potem pełnym poleceniem z kontekstem i ograniczeniami.
Zapisz oba wyniki i porównaj w `docs/prompty.md`: co się różniło, ile linii, czy
trzeba było poprawiać, czy było w Javie.

**Zadanie 3 – łapanie Kotlina**
Poproś agenta **bez** `AGENTS.md` o „prosty licznik kliknięć w Android Studio".
Jeśli odpowie w Kotlinie albo w Compose – zapisz odpowiedź, potem popraw polecenie
tak, żeby dostać Javę z XML-em. Opisz w `docs/kotlin.md`, po czym poznałeś,
że to nie jest Java.

**Zadanie 4 – Logcat**
Zepsuj celowo aplikację: w `findViewById(R.id.tvResult)` zmień id na nieistniejące
albo przenieś tę linię przed `setContentView`. Uruchom, złap wyjątek w Logcacie.
Wklej do `docs/blad.md` **pierwsze pięć linii** stosu i napisz własnymi słowami,
co się stało. Dopiero potem napraw.

**Zadanie 5 – ratunek**
Zrób commit. Potem każ agentowi zrobić w projekcie coś dużego (np. „przebuduj cały
layout na LinearLayout"). Obejrzyj diff w oknie Commit. Cofnij wszystko jedną komendą
i opisz w commicie, czego użyłeś.

**Zadanie 6 – `[bez AI]`**
Napisz od zera, bez żadnego agenta i bez czatu (dokumentacja i te materiały wolno):
ekran z polem tekstowym, przyciskiem i etykietą, która po kliknięciu pokazuje
wpisany tekst dużymi literami. Commit z prefiksem `[bez AI]` w opisie.
Na następnych zajęciach opowiadasz, gdzie się zaciąłeś.