# Git i GitHub – powtórka i zasady

**Technik programista | klasa 5 | aplikacje mobilne**

---

## Po co to tutaj

Git znasz. Ten dokument nie uczy Cię Gita od zera – ustala **jak pracujemy w tym roku**
i pokazuje trzy rzeczy, które w projekcie androidowym wyglądają inaczej niż w projekcie
konsolowym w Javie:

1. Projekt Android Studio ma dużo plików, których **nie wolno** wysyłać na GitHuba.
   Więcej niż w zwykłym projekcie w Javie i łatwiej się pomylić.
2. Commitujesz **znacznie częściej** niż dotąd – po każdym działającym kroku,
   a nie raz na lekcję.
3. Twoje repozytoria to portfolio. Rekruter na stanowisko juniora patrzy na GitHuba
   przed CV, a aplikacja mobilna ze zrzutem ekranu w README wygląda tam znacznie
   lepiej niż wypisywanie tablicy w konsoli.

> Wszystko, co robisz na zajęciach, ląduje na Twoim GitHubie. To jednocześnie ocena,
> portfolio i Twoje własne archiwum na czas, gdy w styczniu będziesz sobie
> przypominał, jak się podpina `OnClickListener`.

---

## Szybka powtórka (jeśli to masz – przeskocz)

### Konfiguracja (na początku każdych zajęć, bo komputery są wspólne)

```bash
git config --global user.name "Imię Nazwisko"
git config --global user.email "twoj@email.com"   # TEN SAM e-mail co na GitHubie
git config --global init.defaultBranch main
git config --global --list                         # sprawdzenie
```

Jeśli e-mail się nie zgadza z tym z GitHuba, commity nie liczą się do Twojego profilu –
zielone kwadraciki nie urosną, a repozytorium będzie pokazywało jakiegoś anonima.

### Model: cztery miejsca

```
 katalog roboczy --git add--> poczekalnia --git commit--> historia lokalna
   (edytujesz)                 (staging)                             |
                                                            git push | git pull
                                                                     ▼
                                                                   GitHub
```

### Codzienna pętla

```bash
git status                      # co się zmieniło
git add .                       # do poczekalni
git commit -m "Co zrobiłem"     # zapis w historii
git push                        # wysyłka na GitHub
```

Cztery komendy, 90% pracy. Reszta dokumentu to pozostałe 10%, które akurat w Androidzie
robi różnicę.

---

## Zakładanie repozytorium projektu Android

Android Studio przy tworzeniu nowego projektu **samo nie robi `git init`** – musisz to
zrobić ręcznie albo z menu *Git -> Enable Version Control Integration*.
Robimy z terminala, żeby było wiadomo, co się dzieje.

Kolejność jest ważna: **najpierw `.gitignore`, potem pierwszy `add`.**
Jeśli zrobisz odwrotnie, wciągniesz do historii katalog `build/` i potem trzeba go
stamtąd wyciągać.

```bash
cd ~/AndroidStudioProjects/PierwszaAplikacja

# 1. sprawdź, czy szablon wygenerował .gitignore (zwykle tak)
cat .gitignore

# 2. dopiero teraz
git init
git add .
git status --short          # PRZECZYTAJ tę listę, zanim zacommitujesz
git commit -m "chore: pusty projekt Empty Views Activity (Java)"
git branch -M main
git remote add origin https://github.com/TWOJ_LOGIN/pierwsza-aplikacja.git
git push -u origin main
```

Z narzędziem `gh` (rozdział na końcu) trzy ostatnie linie zamieniają się w jedną:

```bash
gh repo create pierwsza-aplikacja --public --source=. --push
```

### Co powinno być w `git status --short` przy pierwszym commicie

Kilkanaście–kilkadziesiąt plików: `app/src/main/java/...`, `app/src/main/res/...`,
`AndroidManifest.xml`, `build.gradle.kts` (albo `build.gradle`), `settings.gradle.kts`,
`gradle/libs.versions.toml`, `gradlew`, `gradlew.bat`, `gradle/wrapper/`.

Jeśli widzisz **setki** plików albo cokolwiek z `build/`, `.gradle/`, `.idea/` –
stop, popraw `.gitignore` i dopiero commituj.

---

## `.gitignore` dla Androida

Projekt Android Studio generuje przy każdym uruchomieniu ogromną ilość plików
tymczasowych. Skompilowane klasy, pliki pośrednie, plik APK, cache Gradle'a, ustawienia
IDE, ścieżka do SDK na Twoim komputerze. Nic z tego nie ma prawa trafić na GitHuba –
odtwarza się jednym kliknięciem *Build*.

```gitignore
# --- Gradle i build ---
.gradle/
build/
app/build/
captures/
.externalNativeBuild/
.cxx/

# --- Android Studio / IntelliJ ---
*.iml
.idea/
*.hprof

# --- ścieżki i klucze specyficzne dla komputera (NIGDY do repo) ---
local.properties
*.jks
*.keystore
keystore.properties

# --- system ---
.DS_Store
Thumbs.db
```

Dwa wpisy z tej listy warto zrozumieć, a nie tylko przekleić:

**`local.properties`** zawiera ścieżkę do Android SDK **na tym konkretnym komputerze**
(`sdk.dir=C\:\\Users\\uczen\\AppData\\Local\\Android\\Sdk`). U kolegi ta ścieżka jest
inna. Jeśli wypchniesz ten plik, u kolegi projekt się nie zbuduje. Plik generuje się
sam przy pierwszym otwarciu projektu.

**`*.jks` / `*.keystore`** to klucz, którym podpisuje się aplikację przed wysłaniem do
Google Play. Kto ma Twój klucz, ten może wydać aktualizację Twojej aplikacji.
Dojdziemy do tego pod koniec roku, ale reguła niech siedzi w `.gitignore` od początku.

Sprawdzenie, czy działa:

```bash
git status --short            # nie powinno tu być nic z build/ ani .gradle/
git check-ignore -v app/build # pokaże, która reguła to ignoruje
```

> **Jeśli już wypchnąłeś `build/`:**
> `git rm -r --cached app/build` -> commit -> push. Pliki zostają na dysku,
> znikają z repozytorium. To samo z `local.properties`.

---

## Mikro-commity – główna zasada tego roku

W czwartej klasie wystarczał jeden commit na lekcję. W tym roku **nie wystarcza.**
Commitujesz po każdym kroku, który da się opisać jednym zdaniem.

Przykładowa lekcja o kalkulatorze wygląda w historii tak:

```
feat: dwa pola EditText i przycisk w layoucie
feat: podpięcie widoków przez findViewById
feat: dodawanie po kliknięciu przycisku
fix: obsługa pustego pola - Toast zamiast wyjątku
docs: README z opisem i zrzutem ekranu
```

Pięć commitów, pięć czytelnych kroków, dwie godziny lekcji. Nie:
`git commit -m "kalkulator"` o 14:58.

**Dlaczego akurat w Androidzie to takie ważne:**

- Aplikacja albo się buduje, albo nie. Kiedy po dwudziestu zmianach naraz Gradle
  nagle wyrzuca błąd, nie wiesz, która zmiana go wywołała. Przy mikro-commitach
  masz `git diff` z ostatnim działającym stanem – zwykle 5 linii do sprawdzenia.
- Agent LLM potrafi przy jednym poleceniu przepisać trzy pliki. Jeśli przed jego
  uruchomieniem zrobiłeś commit, wycofanie się to jedna komenda. Jeśli nie –
  odtwarzasz z pamięci.
- Ja z historii commitów widzę, **czy rozumiałeś, co robisz**. Pięć commitów opisujących
  kolejne kroki mówi, że budowałeś aplikację. Jeden commit na 400 linii mówi,
  że coś wkleiłeś.

### Nazewnictwo commitów

Schemat `typ: opis` – krótko, po polsku albo po angielsku, ale konsekwentnie.

| Typ | Kiedy | Przykład |
|---|---|---|
| `feat` | nowa funkcja | `feat: przycisk losowania kości` |
| `fix` | naprawa błędu | `fix: crash przy pustym polu imienia` |
| `style` | wygląd, layout | `style: marginesy i kolor tła ekranu` |
| `docs` | dokumentacja | `docs: README z instrukcją uruchomienia` |
| `refactor` | porządki bez zmiany działania | `refactor: wydzielenie metody calculateResult()` |
| `chore` | konfiguracja, zależności | `chore: podniesienie minSdk do 24` |

| Dobrze | Źle |
|---|---|
| `feat: zapis wyniku w SharedPreferences` | `zmiany` |
| `fix: NullPointerException po obrocie ekranu` | `poprawki` |
| `style: przyciski wyrównane do środka` | `dziala` |
| `feat: przejście na drugi ekran przez Intent` | `final final` |

Zasada bez wyjątków: **jeden commit = jedna rzecz.**

---

## README Twojego projektu

Każde repozytorium ma `README.md`. To jest dokumentacja i tak ją oceniam.
Minimum dla aplikacji mobilnej:

````markdown
# Kalkulator BMI

Aplikacja na Androida licząca wskaźnik BMI z wagi i wzrostu.
Projekt na zajęcia z programowania aplikacji mobilnych, klasa 5 TP.

## Zrzut ekranu

![Ekran główny](docs/ekran.png)

## Technologie

Java 17, Android SDK, minSdk 24, ConstraintLayout

## Uruchomienie

1. Sklonuj repozytorium
2. Otwórz w Android Studio (*File -> Open*)
3. Poczekaj na *Gradle Sync*
4. *Run* -> emulator albo telefon z włączonym debugowaniem USB

## Funkcje

- [x] Obliczanie BMI
- [x] Walidacja pustych pól
- [ ] Zapis historii pomiarów

## Autor

Jan Kowalski, 5 TP
````

**Zrzut ekranu jest obowiązkowy** i w aplikacji mobilnej robi największą różnicę.
W Android Studio: przycisk aparatu w panelu *Running Devices*. Zapisz plik
w `docs/` i podlinkuj – wtedy każdy, kto wejdzie na Twoje repo, w dwie sekundy widzi,
co zrobiłeś, bez uruchamiania czegokolwiek.

---

## Praca z Gitem z poziomu Android Studio

Wszystko powyżej da się wyklikać. Android Studio to IntelliJ, więc skróty są te same
co w PyCharmie:

| Skrót | Co robi |
|---|---|
| `Ctrl + K` | okno Commit (`add` + `commit` naraz) |
| `Ctrl + Shift + K` | Push |
| `Ctrl + T` | Update project (`pull`) |
| `Alt + 9` | panel Git – historia, gałęzie, diff |

W oknie Commit widzisz **listę zmienionych plików i dokładny diff**. To jest najlepszy
moment, żeby zauważyć, że agent przy okazji zmienił coś, o co go nie prosiłeś.
Przeglądaj tę listę, zanim klikniesz *Commit*.

Ale komendy w terminalu musisz znać. Na rozmowie o pracę nikt nie zapyta,
gdzie jest przycisk.

---

## Gałęzie – kiedy warto

W tym roku pracujemy głównie na `main`, bo projekty są małe i pracujesz sam.
Gałęzie przydają się w jednej konkretnej sytuacji i warto ją znać:

**Chcesz spróbować czegoś, co może rozwalić działającą aplikację.**

```bash
git switch -c proba/animacje    # nowa gałąź, main zostaje nietknięty
# ... eksperymentujesz, commitujesz ...

# wyszło:
git switch main
git merge proba/animacje

# nie wyszło:
git switch main
git branch -D proba/animacje    # gałąź do kosza, main dalej działa
```

Typowe zastosowanie u nas: przebudowa layoutu, podmiana biblioteki, duża zmiana
zaproponowana przez agenta. Przy większych projektach zespołowych dochodzą Pull
Requesty i review – w klasie piątej na webowych to standard, tutaj wracamy do tematu
przy projekcie końcowym.

---

## Ratunek, kiedy coś pójdzie źle

| Sytuacja | Komenda |
|---|---|
| Zepsułem plik, chcę wersję z ostatniego commita | `git restore app/src/main/java/.../MainActivity.java` |
| Dodałem plik do poczekalni przez pomyłkę | `git restore --staged plik` |
| Zły opis ostatniego commita | `git commit --amend -m "poprawny opis"` |
| Ostatni commit do cofnięcia, zmiany zostawić | `git reset --soft HEAD~1` |
| Chcę zobaczyć, co zmienił dany commit | `git show <hash>` |
| Muszę na chwilę odłożyć zmiany | `git stash` / `git stash pop` |
| Wypchnąłem coś złego, ale inni już to pobrali | `git revert <hash>` |

**Uwaga na `git reset --hard`.** Kasuje zmiany bez pytania i bez kosza.
Zanim go użyjesz, zapytaj mnie albo zrób `git stash` – to samo, ale odwracalne.

---

## Koniec zajęć: usuwanie poświadczeń z komputera

> Komputery w pracowni są wspólne. Po pierwszym pushu Windows zapamiętuje Twoje
> logowanie do GitHuba. Jeśli go nie usuniesz, następna osoba przy tym komputerze
> wypchnie swoje (albo cudze) pliki na Twoje konto, pod Twoim nazwiskiem.

### 1. Poświadczenie GitHub z Windows

`Win + R`:

```
control /name Microsoft.CredentialManager
```

-> *Poświadczenia systemu Windows* -> wpis `git:https://github.com` -> **Usuń**.

To samo z PowerShella:

```powershell
cmdkey /delete:git:https://github.com
cmdkey /list | findstr github        # pusta odpowiedź = OK
```

### 2. Dane z konfiguracji Gita

```bash
git config --global --unset user.name
git config --global --unset user.email
```

Inaczej commity następnej osoby będą podpisane Twoim nazwiskiem.

### 3. Jeśli używałeś `gh`

```bash
gh auth logout
```

### 4. Jeśli używałeś agenta LLM

W sesji agenta `/logout` (Claude Code, Codex) albo usuń katalog z tokenem:
`C:\Users\NAZWA\.claude`, `.codex`, `.gemini`. Szczegóły w dokumencie 02.

### 5. Przeglądarka

Wyloguj się z GitHuba w przeglądarce. Jeśli ktoś przed Tobą tego nie zrobił –
nie korzystaj z tego, tylko go wyloguj. Zasada działa w obie strony.

> Na początku każdych zajęć: `git config` od nowa, przy pierwszym pushu logowanie
> przez przeglądarkę. To 30 sekund, a chroni Twoje konto.

---

## `gh` – GitHub z terminala

Nieobowiązkowe, ale oszczędza sporo klikania.

```bash
winget install GitHub.cli                    # instalacja (PowerShell)
gh auth login                                # logowanie

gh repo create kalkulator-bmi --public --source=. --push
gh repo view --web                           # otwórz repo w przeglądarce
gh issue create --title "Brak walidacji wzrostu"
gh issue list
```

**Issues** przydają się bardziej, niż się wydaje. Kiedy w trakcie lekcji zauważysz błąd,
którego nie masz teraz czasu naprawić – zakładasz zgłoszenie zamiast obiecywać sobie,
że zapamiętasz. Dobre zgłoszenie ma trzy zdania: **kroki odtworzenia**,
**czego się spodziewałeś**, **co się stało**. Dokładnie tak wygląda to w pracy.

---

## Ściąga

| Komenda | Co robi |
|---|---|
| `git status --short` | krótka lista zmian |
| `git diff` | które linie się zmieniły (przed `add`) |
| `git diff --staged` | co jest w poczekalni |
| `git log --oneline --graph` | historia, jedna linia na commit |
| `git add .` | wszystko do poczekalni |
| `git commit -m "opis"` | zapis w historii |
| `git push` | wysyłka na GitHub |
| `git pull` | pobranie zmian |
| `git clone URL` | pobranie cudzego repozytorium |
| `git switch -c nazwa` | nowa gałąź + przejście |
| `git restore plik` | cofnij niezapisane zmiany |
| `git rm -r --cached katalog` | wypisz z repo, zostaw na dysku |
| `git show <hash>` | co zmienił dany commit |
| `git check-ignore -v plik` | dlaczego ten plik jest ignorowany |
| `gh repo create ... --push` | repo na GitHubie jedną komendą |

---

## Zasady w tej klasie

1. **Każdy temat -> osobne, publiczne repozytorium** na Twoim GitHubie.
   Nazwa mówi, co to jest: `kalkulator-bmi`, nie `projekt2`.
2. **Commitujesz każdy krok.** Działa layout – commit. Działa przycisk – commit.
   Jeden commit na koniec lekcji jest traktowany jak brak commitów.
3. **Koniec zajęć: push.** Brak pusha = brak dowodu, że pracowałeś.
4. Każde repo ma `README.md` (ze zrzutem ekranu) i `.gitignore`.
5. W repozytorium **nie ma** `build/`, `.gradle/`, `local.properties`, `.idea/`
   ani żadnego pliku `.jks`.
6. **Możesz używać LLM.** Ale na kolejnych zajęciach dostajesz pytania o kod.
   Wiesz, co robi każda linia – świetnie. Nie wiesz – do poprawy.
7. Na koniec zajęć usuwasz poświadczenia z komputera. Jeśli ktoś wypchnie coś
   na Twoje konto, bo zostawiłeś logowanie – to Twój problem, nie jego.

---

## Zadania

**Zadanie 1 – repozytorium od zera**
Utwórz w Android Studio pusty projekt (*Empty Views Activity*, Java – szczegóły
w dokumencie 03). Zainicjuj repozytorium, sprawdź `.gitignore`, zrób pierwszy commit
i wypchnij na GitHuba. W `git status --short` przed commitem nie może być nic
z `build/` ani `.gradle/`.

**Zadanie 2 – co ignorujemy i dlaczego**
Uruchom *Build -> Make Project*. Potem `git status`. Wypisz w pliku `docs/ignore.md`
trzy katalogi, które powstały, a nie pojawiły się w `git status`, i **jednym zdaniem
do każdego** napisz, dlaczego nie trafiają do repozytorium.

**Zadanie 3 – mikro-commity**
Dodaj do layoutu przycisk i pole tekstowe – commit. Podepnij je w Javie przez
`findViewById` – commit. Dodaj obsługę kliknięcia – commit. Trzy commity, trzy sensowne
opisy. `git log --oneline` ma to pokazywać.

**Zadanie 4 – awaria**
Zepsuj celowo `MainActivity.java` (usuń nawias klamrowy, zapisz). Przywróć plik
z ostatniego commita jedną komendą. Opisz w `docs/ratunek.md`, jakiej użyłeś
i dlaczego akurat tej, a nie `git reset --hard`.

**Zadanie 5 – README**
Napisz `README.md` swojego projektu według wzoru z tego dokumentu. Zrób zrzut ekranu
aplikacji z emulatora, wrzuć do `docs/` i podlinkuj. Sprawdź na GitHubie,
czy obrazek się wyświetla.

**Zadanie 6 – wyciąganie z historii**
Zacommituj celowo plik `local.properties`. Push. Potem usuń go z repozytorium
tak, żeby **został na dysku**. Push. Opisz w commicie, co zrobiłeś. Zastanów się:
czy ten plik zniknął z historii repozytorium, czy tylko z najnowszej wersji?

**Zadanie 7 – sprzątanie**
Wykonaj procedurę z rozdziału o poświadczeniach. Pokaż mi pusty wynik
`cmdkey /list | findstr github`.