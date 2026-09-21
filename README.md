# Materiały – Programowanie aplikacji mobilnych

**Technik programista | klasa 5 | rok szkolny 2026/2027**
Przedmioty: *Programowanie aplikacji mobilnych* + *Pracownia programowania aplikacji mobilnych*

Repozytorium z materiałami do zajęć. Kolejne pliki dochodzą **z zajęć na zajęcia** –
zaglądaj tu regularnie, `git pull` przed każdą lekcją.

---

## Co robimy w tym roku

**Android Studio, język Java, interfejs w XML.** Piszemy prawdziwe aplikacje na telefon –
takie, które uruchamiasz na emulatorze albo na własnym sprzęcie, klikasz i one reagują.

W czwartej klasie mieliście Javę konsolową. To nie jest strata: składnia, klasy, metody,
pętle i tablice to jest dokładnie ten sam język, którego użyjemy. Zmienia się jedna rzecz,
ale zasadnicza – **program nie wykonuje się od `main()` do końca i nie kończy**.
Aplikacja mobilna czeka. Reaguje na kliknięcie, na obrót ekranu, na przyjście SMS-a,
na to, że użytkownik ją zminimalizował. To jest programowanie sterowane zdarzeniami
i to jest cała różnica, którą musisz przyswoić w pierwszych tygodniach.

```
XML (wygląd)  <---- findViewById() ---->  Java (co się dzieje po kliknięciu)
     |                                              |
     └────────── jedna Activity = jeden ekran ──────┘
```

Droga na cały rok:

```
pierwszy ekran ─> layouty ─> elementy UI ─> wiele ekranów ─> listy
   ─> zapis danych ─> baza ─> internet ─> czujniki i lokalizacja ─> publikacja
```

Każdy wybiera na początku roku **własny temat aplikacji** (notatnik, lista zakupów,
dzienniczek treningowy, katalog płyt, cokolwiek Twojego) i rozbudowuje go przez cały rok.
Ćwiczenia z lekcji robimy na małych, osobnych projektach – ale co jakiś czas nowa
umiejętność ląduje w Twojej aplikacji głównej.

---

## Materiały

| Nr | Dokument | Temat |
|---|---|---|
| 01 | [Git i GitHub – powtórka i zasady](01-git-github.md) | repozytorium projektu Android, `.gitignore`, mikro-commity, `gh`, czyszczenie komputera |
| 02 | [Agenci LLM – jak z nich korzystać](02-agenci-llm.md) | Claude Code, Codex CLI, Gemini CLI, `AGENTS.md`, typowe wpadki AI w Androidzie |
| 03 | [Android Studio – wstęp](03-android-studio-wstep.md) | pierwszy projekt, `MainActivity.java`, `activity_main.xml`, manifest, `findViewById`, kliknięcie |
| 04 | [ConstraintLayout i elementy UI](04-constraintlayout.md) | powiązania, bias, łańcuchy, guideline, barrier, edytor graficzny, komponenty i ich atrybuty |

Kolejne dochodzą na bieżąco.

---

## Plan na rok a podstawa programowa

Kwalifikacja **INF.04**, jednostka **INF.04.6 – Programowanie aplikacji mobilnych**.
Poniżej mapa: co robimy i który punkt podstawy to realizuje. Trzymaj to pod ręką –
w styczniu przy powtórce do egzaminu ta tabela jest listą kontrolną.

| Temat zajęć | Punkt INF.04.6 |
|---|---|
| Android Studio, SDK, emulator, urządzenie fizyczne | 1) środowisko programistyczne; 11) uruchamianie aplikacji |
| ConstraintLayout, LinearLayout, inne kontenery | 4) 6) elementy UI |
| TextView, EditText, Button, ImageView, CheckBox, RadioButton, Spinner, Switch | 4) 6) elementy UI |
| Toast, Snackbar, AlertDialog | 4) okna dialogowe |
| `Intent`, wiele ekranów, Toolbar i menu | 4) nawigacja, paski narzędziowe |
| RecyclerView i adaptery | 4) listy |
| Cykl życia Activity, zapis stanu | 3) przechowywanie danych |
| `SharedPreferences` | 3) preferencje użytkownika |
| SQLite / Room, CRUD na telefonie | 9) aplikacja z bazą danych |
| HTTP + JSON, pobieranie danych z API | 8) pobieranie i wysyłanie danych |
| Stoper, budzik, powiadomienia, kalendarz | 7) proste aplikacje typu zegar, powiadamianie, kalendarz |
| GPS i lokalizacja | 7) lokalizacja / nawigacja satelitarna |
| Grafika, animacje, dźwięk | 4) grafika, animacje, dźwięk |
| Telefon vs tablet, orientacja, zasoby alternatywne | 10) dostosowanie do platformy |
| Podpisany plik AAB/APK, konto i publikacja w Google Play | 12) przygotowanie do publikacji |
| .NET MAUI i XAML – lekcja porównawcza | 5) interfejs w języku XAML |

Dwie uwagi do tej tabeli:

**Punkt 2 podstawy** dopuszcza Objective-C, Swift, Javę lub C#. Wybieramy **Javę**,
bo ją znacie z czwartej klasy i bo cały ekosystem Androida stoi na JVM. Kotlin pokażę
porównawczo – w firmach nowy kod Androida pisze się dziś głównie w Kotlinie i musisz
umieć taki kod przeczytać, nawet jeśli sam piszesz w Javie.

**Punkt 5 – XAML** – to jest język interfejsu ze świata Microsoftu (.NET MAUI, dawniej
Xamarin). Nie budujemy na tym projektu, ale robimy jedną–dwie lekcje porównawcze:
ten sam ekran raz w androidowym XML-u, raz w XAML-u. Chodzi o rozpoznanie
i zrozumienie różnicy, nie o biegłość.

---

## Zasady

Pełne wersje w dokumentach 01 i 02, tutaj skrót:

1. **Każdy temat -> osobne, publiczne repozytorium** na Twoim GitHubie. Nazwa mówi, co to jest.
2. **Commitujesz każdy krok**, nie całą lekcję na końcu. Działa layout – commit. Działa
   przycisk – commit. Historia commitów ma pokazywać, jak aplikacja rosła.
3. **Koniec zajęć: `git push`.** Brak pusha = brak dowodu, że pracowałeś. Sprawdzam
   po lekcji, nie w klasie.
4. Każde repo ma `README.md` (co to za aplikacja, jak uruchomić, zrzut ekranu) i `.gitignore`.
5. **Możesz używać LLM.** Ale na kolejnych zajęciach dostajesz pytania o ten kod.
   Wiesz, co robi każda linia – świetnie, nieważne kto ją napisał. Nie wiesz – do poprawy.
6. Co jakiś czas jedno zadanie robisz **bez AI**, z oznaczeniem `[bez AI]` w commicie.
   W styczniu na egzaminie agenta nie będzie.
7. Komputery w pracowni są wspólne – na koniec zajęć **usuwasz swoje poświadczenia**
   (procedura w dokumencie 01).

---

## Zanim zaczniemy

Sprawdź w terminalu:

```bash
git --version         # jeśli nie ma: https://git-scm.com/download/win
java -version         # JDK w komplecie z Android Studio, ale warto wiedzieć, że jest
node --version        # potrzebny tylko do agentów CLI (dokument 02), wersja 22+
```

Do tego:

- **Android Studio** – https://developer.android.com/studio (kilka GB, na szkolnych
  komputerach powinno już być zainstalowane; w domu zarezerwuj sobie godzinę i 15 GB miejsca).
- **Konto na GitHubie** z rozsądną nazwą użytkownika – to zobaczy pracodawca.
  `jan-kowalski` tak, `xXx_destroyer_xXx` nie.
- **Wnioski o darmowe licencje studenckie** – rozdział niżej. Złóż je w pierwszym
  tygodniu, bo weryfikacja trwa.
- **Telefon z Androidem** – opcjonalnie, ale bardzo się przydaje. Emulator działa,
  tylko wolno. Jak masz telefon, weź kabel USB.

---

## Darmowe licencje dla uczniów – załatw to w pierwszym tygodniu

Jesteś uczniem technikum i z tego tytułu przysługuje Ci **pakiet narzędzi, za które
zawodowy programista płaci kilkaset dolarów rocznie**. Za darmo, legalnie, na czas nauki.
Większość osób dowiaduje się o tym na ostatnim roku studiów i szczerze żałuje.

Trzy wnioski, w tej kolejności. Pierwszy odblokowuje dwa pozostałe.

### 1. GitHub Student Developer Pack

https://education.github.com/pack

To jest **wniosek numer jeden**, bo w środku siedzą oferty kilkudziesięciu firm
i większość pozostałych rzeczy z tej listy dostajesz właśnie przez niego.

**Czego potrzebujesz:**

- konta na GitHubie (tego samego, na którym oddajesz zadania),
- dowodu, że jesteś uczniem: szkolnego adresu e-mail **albo** zdjęcia legitymacji
  z widoczną datą ważności, **albo** zaświadczenia ze szkoły – do wzięcia w sekretariacie,
- prawdziwych danych. Nazwa szkoły ma się zgadzać, imię i nazwisko też.

**Ile to trwa:** od kilkunastu minut (gdy szkolny e-mail weryfikuje się automatycznie)
do kilku dni przy weryfikacji ręcznej. Dlatego składasz na początku roku, a nie wtedy,
gdy nagle będzie potrzebne.

**Co jest w środku – rzeczy, które faktycznie wykorzystasz:**

| Oferta | Co daje |
|---|---|
| **JetBrains All Products Pack** | wszystkie IDE JetBrains (patrz punkt 2) |
| **GitHub Pro** | prywatne repozytoria bez limitów, więcej minut GitHub Actions |
| **Microsoft Azure for Students** | 100 USD kredytu na chmurę (patrz punkt 3) |
| **DigitalOcean** | ok. 200 USD kredytu – serwer pod projekt |
| **Namecheap / .tech** | własna domena na rok, np. `jankowalski.tech` |
| **MongoDB Atlas** | kredyty na bazę w chmurze + darmowe certyfikaty MongoDB University |
| **GitHub Codespaces** | środowisko programistyczne w przeglądarce |
| **DataCamp, Educative, Frontend Masters** | kursy, zwykle na kilka miesięcy |
| **Canva Pro, Figma** | grafika i projektowanie interfejsów – przyda się przy layoutach |

**Oferta z Copilotem zmieniała się w 2026 roku** – w pewnym momencie GitHub wstrzymał
nowe zapisy na plan studencki. Sprawdź aktualny stan na stronie pakietu; niezależnie
od tego my na zajęciach używamy agentów opisanych w dokumencie 02.

Lista partnerów **zmienia się kilka razy w roku** – jedne oferty znikają, dochodzą nowe.
Po zatwierdzeniu wniosku wejdź na stronę pakietu i przejrzyj ją całą; to dziesięć minut,
a niektóre rzeczy trzeba aktywować osobno i przepadają, jeśli tego nie zrobisz.

**Ważne:** weryfikacja nie jest wieczna. GitHub co jakiś czas prosi o jej odnowienie.
Kiedy skończysz szkołę i nie potwierdzisz statusu, GitHub Pro wraca do wersji darmowej,
a oferty partnerów przestają się odnawiać. Repozytoria zostają – nic nie znika.

### 2. Konto edukacyjne JetBrains

https://www.jetbrains.com/community/education/

Najcenniejsza pozycja z całego pakietu. **JetBrains All Products Pack** to komplet
profesjonalnych IDE, w cenie katalogowej rzędu kilkuset dolarów rocznie:

| Narzędzie | Do czego |
|---|---|
| **IntelliJ IDEA Ultimate** | Java na poważnie – Spring, serwery aplikacji, bazy |
| **DataGrip** | klient baz danych – przyda się przy SQLite w telefonie |
| **PyCharm Professional** | Python, Django |
| **WebStorm** | JavaScript, React, Node |
| **Rider** | C# i .NET – **tym zrobimy lekcję o XAML i .NET MAUI** |
| **Fleet, RustRover, CLion, GoLand...** | reszta pakietu |

Dwie drogi: przez GitHub Student Pack (jeden klik po zatwierdzeniu wniosku)
albo bezpośrednio na stronie JetBrains, na podstawie szkolnego e-maila lub zaświadczenia.
Licencja jest roczna i **odnawia się co roku**, dopóki potwierdzasz status ucznia.

> **Android Studio jest darmowe dla każdego** i licencji nie wymaga – jest zbudowane
> na darmowej IntelliJ IDEA Community. Konto JetBrains przyda Ci się poza naszym
> przedmiotem: przy projektach w Javie, przy bazach danych i przy porównawczej lekcji
> o .NET. Ale skoro można mieć za darmo, to się bierze.

### 3. Microsoft Azure for Students

https://azure.microsoft.com/free/students

**100 USD kredytu na 12 miesięcy, bez podawania karty płatniczej.**
Do tego zestaw usług w wersjach darmowych, które nie zjadają kredytu.

Na naszym przedmiocie nie jest to konieczne – tego wymaga podstawa programowa,
ale dopiero przy zadaniach z pobieraniem danych z internetu. Wtedy jednak dochodzimy
do pytania „skąd aplikacja mobilna ma brać dane" i mieć **własny serwer w chmurze**
jest w tym momencie bardzo wygodne:

- postawienie prostego API, z którego Twoja aplikacja pobiera dane przez HTTP,
- baza danych dostępna z telefonu, nie tylko z emulatora,
- statyczna strona-wizytówka projektu,
- ćwiczenia z rzeczami, których nie zrobisz na szkolnym komputerze – maszyna wirtualna,
  konteneryzacja, kolejki.

Brak karty płatniczej oznacza, że **nic Ci się nie naliczy**. Po wyczerpaniu kredytu
usługi zostają wstrzymane, nie wystawia się rachunku. Mimo to nabierz nawyku
**wyłączania zasobów, których nie używasz** – kredyt schodzi za każdą godzinę
działania serwera, także w nocy i w wakacje.

Uwaga wiekowa: pełny kredyt dostają osoby pełnoletnie; osoby 13–17 lat dostają
ograniczoną wersję konta bez kredytu. Jeśli masz teraz 17 lat, złóż wniosek
po urodzinach.

*Poza konkursem, do wiadomości:* jeśli szkoła ma program **Azure Dev Tools for Teaching**
albo dostęp do Microsoft 365 dla edukacji, to jest osobna ścieżka – pytaj w sekretariacie
albo u administratora sieci.

### Dwie uwagi na koniec

**Nie kombinuj przy weryfikacji.** W sieci krąży sporo poradników „jak obejść sprawdzanie
statusu ucznia". Nie idź tą drogą – konto GitHub, na którym budujesz portfolio na kolejne
lata, jest zbyt cenne, żeby ryzykować jego zablokowaniem dla darmowej licencji, która
i tak Ci się należy. Jeśli automat Cię odrzuci, przyjdź do mnie: zaświadczenie ze szkoły
załatwia sprawę.

**Konto dewelopera Google Play kosztuje.** Jednorazowo około 25 USD – i to jest jedyna
rzecz w całym roku, która nie jest darmowa. Dojdziemy do tego przy temacie publikacji
aplikacji; **nie zakładaj konta na własną rękę**, najpierw porozmawiamy o tym, co daje
i czy w Twoim przypadku ma sens. Przygotowanie aplikacji do publikacji (podpisany plik,
opis, zrzuty ekranu, zasady prywatności) przećwiczymy niezależnie od tego,
czy kupisz konto.

---

*Opracował: Bartosz Bryniarski*