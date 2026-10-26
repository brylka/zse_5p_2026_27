# Formularz: odczyt, walidacja i okna dialogowe

**Technik programista | klasa 5 | aplikacje mobilne**

Odczyt stanu kontrolek w Javie, `Spinner` z adapterem, nasłuchiwanie zmian,
wzorzec walidacji, `Toast`, `Snackbar` i `AlertDialog`.

---

## 1. Punkt wyjścia

W materiale o ConstraintLayout poznałeś katalog kontrolek: `CheckBox`, `Switch`,
`RadioGroup`, `SeekBar`, `Spinner`. Umiesz je narysować, ustawić im atrybuty
i rozmieścić na ekranie.

Nie umiesz ich jeszcze **odczytać**. Dotąd wszystko, co aplikacja wiedziała
o użytkowniku, pochodziło z jednego `EditText`. Formularz to coś innego: osiem pól
różnego typu, które trzeba odczytać, sprawdzić i dopiero wtedy wykorzystać.

Dochodzą do tego trzy rzeczy, których jeszcze nie było:

- **Adapter** – `Spinner` nie bierze listy z pliku XML tak jak `RadioGroup`.
  Dostaje ją w Javie, przez pośrednika zwanego adapterem. Ten sam wzorzec wróci
  przy listach.
- **Nasłuchiwanie zmian**, a nie tylko kliknięć. Przycisk się klika, ale suwak
  się przesuwa, a przełącznik zmienia stan.
- **Rozmowa z użytkownikiem** – komunikat o błędzie, pytanie o potwierdzenie,
  wybór z listy. Czyli okna dialogowe.

---

## 2. Odczyt stanu kontrolki

Każda kontrolka ma swoją metodę odczytu. To jest tabela, do której będziesz wracał.

| Kontrolka | Odczyt w Javie | Zwraca |
|---|---|---|
| `EditText` | `et.getText().toString().trim()` | `String` |
| `CheckBox`, `Switch` | `cb.isChecked()` | `boolean` |
| `RadioGroup` | `rg.getCheckedRadioButtonId()` | `int` – identyfikator albo `-1` |
| `RadioButton` | `rb.isChecked()` | `boolean` |
| `SeekBar` | `sb.getProgress()` | `int` |
| `Spinner` – wartość | `sp.getSelectedItem().toString()` | `String` |
| `Spinner` – pozycja | `sp.getSelectedItemPosition()` | `int`, liczony od zera |

Dwie rzeczy warto wyróżnić od razu.

**`getCheckedRadioButtonId()` zwraca `-1`, gdy nic nie jest zaznaczone.**
To jest Twój sposób na sprawdzenie, czy użytkownik dokonał wyboru:

```java
int checkedId = rgMode.getCheckedRadioButtonId();
if (checkedId == -1) {
    // użytkownik nic nie wybrał
}
```

Żeby dowiedzieć się **co** wybrał, porównujesz identyfikator – tak samo jak przy
rozpoznawaniu przycisków w kalkulatorze:

```java
String mode;
if (checkedId == R.id.rbOnsite) {
    mode = "stacjonarny";
} else {
    mode = "zdalny";
}
```

**`Spinner` nie ma stanu „nic nie wybrano".** Zawsze pokazuje którąś pozycję,
domyślnie pierwszą. Standardowym rozwiązaniem jest wstawienie na pozycję zerową
napisu w rodzaju `-- wybierz kurs --` i uznanie pozycji `0` za brak wyboru.

---

## 3. `Spinner` i adapter

`RadioGroup` ma swoje opcje wypisane w pliku XML jako kolejne `RadioButton`.
`Spinner` tak nie działa – listę dostaje z Javy.

Między danymi a kontrolką stoi **adapter**. Jego zadanie: wziąć zbiór danych
i zamienić każdy element na widok, który da się pokazać na ekranie. Lista ma tysiąc
pozycji, a na ekranie mieści się osiem – adapter tworzy tylko te osiem i podmienia
w nich treść przy przewijaniu.

```
   dane            adapter              kontrolka
  (tablica) ----> (tworzy widok ) ---->  Spinner
                   dla pozycji
```

Na razie potrzebujesz najprostszego adaptera, `ArrayAdapter`, i listy w `strings.xml`.

### Lista w zasobach

```xml
<string-array name="courses">
    <item>-- wybierz kurs --</item>
    <item>Programowanie w Javie</item>
    <item>Aplikacje mobilne</item>
    <item>Bazy danych</item>
    <item>Sieci komputerowe</item>
</string-array>
```

`string-array` to typ zasobu, którego jeszcze nie używałeś. Leży w tym samym pliku
`strings.xml`, co zwykłe napisy, i odwołujesz się do niego przez `R.array.courses`.

### Podpięcie w Javie

```java
ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
        this,
        R.array.courses,
        android.R.layout.simple_spinner_item);

adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
spCourse.setAdapter(adapter);
```

Trzy argumenty `createFromResource`: kontekst, identyfikator tablicy i **układ
pojedynczej pozycji**. Ten ostatni to gotowy layout wbudowany w system – stąd
przedrostek `android.R`, a nie Twoje `R`.

Dwa różne układy, bo `Spinner` wygląda inaczej w stanie zamkniętym
(`simple_spinner_item`) i po rozwinięciu listy (`simple_spinner_dropdown_item`).
Pominięcie drugiej linii działa, ale rozwinięta lista wygląda wtedy źle.

> **`android.R` kontra `R`.** `R` to zasoby Twojej aplikacji. `android.R` to zasoby
> systemu – gotowe układy, kolory i napisy dostępne w każdej aplikacji.
> Jeśli w kodzie z internetu widzisz `android.R.layout.something`, to znaczy, że
> autor skorzystał z gotowca systemowego i nie musisz szukać tego pliku u siebie.

---

## 4. Nasłuchiwanie zmian

Przycisk ma `OnClickListener`. Pozostałe kontrolki mają własne interfejsy,
bo „zmiana stanu" to nie to samo co „kliknięcie".

| Kontrolka | Interfejs | Metod | Lambda? |
|---|---|---|---|
| `Button` | `View.OnClickListener` | 1 | tak |
| `CheckBox`, `Switch` | `CompoundButton.OnCheckedChangeListener` | 1 | tak |
| `RadioGroup` | `RadioGroup.OnCheckedChangeListener` | 1 | tak |
| `SeekBar` | `SeekBar.OnSeekBarChangeListener` | **3** | **nie** |
| `Spinner` | `AdapterView.OnItemSelectedListener` | **2** | **nie** |

Zasada z poprzednich zajęć obowiązuje bez zmian: **lambda działa tylko wtedy,
gdy interfejs ma dokładnie jedną metodę.** Przy `SeekBar` i `Spinner` piszesz
klasę anonimową, nawet jeśli dwie z trzech metod zostaną puste.

### Przełącznik i pole wyboru

```java
cbTerms.setOnCheckedChangeListener((buttonView, isChecked) ->
        btnSubmit.setEnabled(isChecked));
```

Przycisk wysyłania jest aktywny tylko wtedy, gdy regulamin jest zaakceptowany.
To jest **zależność między polami** – jedno pole steruje drugim.

Dwa parametry lambdy: `buttonView` to kontrolka, która się zmieniła,
`isChecked` to jej nowy stan. Zwykle korzystasz tylko z drugiego.

### Grupa przycisków radiowych

```java
rgMode.setOnCheckedChangeListener((group, checkedId) -> {
    if (checkedId == R.id.rbRemote) {
        tvHint.setText(R.string.hint_remote);
    } else {
        tvHint.setText(R.string.hint_onsite);
    }
});
```

### Suwak – trzy metody

```java
sbLevel.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
    @Override
    public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
        tvLevelValue.setText(String.valueOf(progress));
    }

    @Override
    public void onStartTrackingTouch(SeekBar seekBar) { }

    @Override
    public void onStopTrackingTouch(SeekBar seekBar) { }
});
```

Interesuje Cię pierwsza metoda. Dwie pozostałe muszą istnieć, bo wymaga tego
interfejs, i zostają puste – to normalne i poprawne.

Parametr `fromUser` mówi, czy zmiana pochodzi od palca użytkownika, czy z kodu
(`setProgress`). Przydaje się, gdy chcesz uniknąć pętli: kod zmienia suwak,
suwak wywołuje listener, listener zmienia kod.

### Lista rozwijana – dwie metody

```java
spCourse.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        if (position > 0) {
            tvHint.setText(parent.getItemAtPosition(position).toString());
        }
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) { }
});
```

> **Uwaga.** `onItemSelected` wywołuje się **także przy pierwszym wyświetleniu
> ekranu**, dla pozycji zerowej. Jeśli wstawisz tam `Toast`, zobaczysz go
> natychmiast po uruchomieniu aplikacji, zanim użytkownik czegokolwiek dotknie.
> Stąd warunek `position > 0` w przykładzie.

---

## 5. Walidacja – wzorzec

Walidacja to sprawdzenie, czy dane nadają się do użycia, **zanim** aplikacja
spróbuje z nich skorzystać. Wzorzec jest zawsze ten sam:

> Sprawdzaj pola po kolei. Na pierwszym błędzie **wskaż winne pole, przerwij
> i wyjdź**. Nie sprawdzaj dalej i nie pokazuj pięciu komunikatów naraz.

```java
private boolean validateForm() {
    String name = etName.getText().toString().trim();
    if (name.isEmpty()) {
        etName.setError(getString(R.string.error_name));
        etName.requestFocus();
        return false;
    }

    String email = etEmail.getText().toString().trim();
    if (!Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
        etEmail.setError(getString(R.string.error_email));
        etEmail.requestFocus();
        return false;
    }

    // ... kolejne pola ...

    return true;
}
```

Metoda zwraca `boolean`, więc użycie jest czytelne:

```java
btnSubmit.setOnClickListener(v -> {
    if (validateForm()) {
        askForConfirmation();
    }
});
```

### `setError` zamiast `Toast`

`setError` na polu `EditText` pokazuje czerwony wykrzyknik w polu i dymek
z komunikatem. Ma trzy przewagi nad `Toast`:

| | `setError` | `Toast` |
|---|---|---|
| Wskazuje **które** pole jest złe | tak | nie |
| Znika, gdy użytkownik poprawi wpis | tak | nie, znika po chwili |
| Widoczny, gdy użytkownik wróci do pola | tak | nie |

`requestFocus()` dodatkowo przewija ekran do błędnego pola i stawia tam kursor.
W długim formularzu, w którym błędne pole jest poza widokiem, bez tego użytkownik
widzi komunikat i nie wie, gdzie szukać.

Kontrolki bez `setError` – `CheckBox`, `Spinner`, `RadioGroup` – obsługujesz
`Toastem` albo `Snackbarem`.

### Sprawdzanie adresu e-mail

```java
Patterns.EMAIL_ADDRESS.matcher(email).matches()
```

`android.util.Patterns` to zestaw gotowych wzorców wbudowanych w system:
adres e-mail, numer telefonu, adres internetowy. Nie wymyślaj własnego wyrażenia
regularnego do e-maila – ten wzorzec jest przetestowany na milionach urządzeń,
a Twój nie będzie.

---

## 6. Trzy sposoby odezwania się do użytkownika

| | `Toast` | `Snackbar` | `AlertDialog` |
|---|---|---|---|
| Blokuje aplikację | nie | nie | **tak** |
| Użytkownik musi zareagować | nie | nie | **tak** |
| Może mieć przycisk akcji | nie | **tak** | tak |
| Da się zamknąć gestem | nie | tak, przesunięciem | przyciskiem |
| Typowe zastosowanie | krótka informacja | informacja z cofnięciem | pytanie, decyzja |

Reguła doboru: **czy aplikacja potrzebuje odpowiedzi?**

- Nie potrzebuje, tylko informuje → `Toast`.
- Nie potrzebuje, ale wypadałoby dać możliwość cofnięcia → `Snackbar`.
- Potrzebuje decyzji, zanim pójdzie dalej → `AlertDialog`.

### `Toast` – znasz z poprzednich zajęć

```java
Toast.makeText(this, R.string.saved, Toast.LENGTH_SHORT).show();
```

### `Snackbar` – pasek na dole z akcją

```java
Snackbar.make(findViewById(R.id.main), R.string.sent, Snackbar.LENGTH_LONG)
        .setAction(R.string.undo, v -> clearForm())
        .show();
```

Pierwszy argument to **dowolny widok z Twojego ekranu** – `Snackbar` sam znajdzie
po nim odpowiednie miejsce do wyświetlenia. Zwykle podaje się kontener główny.

`Snackbar` pochodzi z biblioteki Material Components, więc potrzebuje importu
`com.google.android.material.snackbar.Snackbar`. Biblioteka jest w projekcie
od początku, nic nie trzeba dodawać.

Wzorzec „wykonaj od razu, daj cofnąć" jest w aplikacjach mobilnych standardem –
lepszy niż pytanie „czy na pewno?" przy każdej drobnej czynności.

### `AlertDialog` – pytanie z decyzją

```java
new AlertDialog.Builder(this)
        .setTitle(R.string.confirm_title)
        .setMessage(R.string.confirm_message)
        .setPositiveButton(R.string.yes, (dialog, which) -> submitForm())
        .setNegativeButton(R.string.no, null)
        .show();
```

Okno budujesz **krok po kroku** – to wzorzec zwany budowniczym. Każda metoda
zwraca ten sam obiekt budowniczego, więc wywołania da się połączyć w łańcuch,
a `show()` na końcu tworzy i pokazuje gotowe okno.

| Metoda | Znaczenie |
|---|---|
| `setTitle` | nagłówek |
| `setMessage` | treść |
| `setPositiveButton` | przycisk potwierdzenia, zwykle po prawej |
| `setNegativeButton` | przycisk odrzucenia |
| `setNeutralButton` | trzeci przycisk, np. „Przypomnij później" |
| `setCancelable(false)` | blokuje zamknięcie przyciskiem Wstecz |

`null` zamiast listenera znaczy „zamknij okno i nie rób nic więcej".
To najczęstsza zawartość przycisku odrzucenia.

> **Importuj `androidx.appcompat.app.AlertDialog`**, nie `android.app.AlertDialog`.
> Obie klasy istnieją i obie się kompilują, ale tylko wersja z `androidx` wygląda
> tak samo na starszych wersjach Androida. Android Studio przy podpowiadaniu
> zaproponuje obie – wybierasz tę z `androidx`.

### `AlertDialog` z listą wyboru

```java
String[] options = getResources().getStringArray(R.array.courses);

new AlertDialog.Builder(this)
        .setTitle(R.string.pick_course)
        .setItems(options, (dialog, which) -> spCourse.setSelection(which))
        .show();
```

Parametr `which` to **numer pozycji** na liście, liczony od zera.

### `DatePickerDialog` – wybór daty

```java
private void showDatePicker() {
    Calendar calendar = Calendar.getInstance();

    DatePickerDialog dialog = new DatePickerDialog(
            this,
            (view, year, month, dayOfMonth) -> {
                selectedDate = String.format(Locale.US, "%04d-%02d-%02d",
                        year, month + 1, dayOfMonth);
                btnBirthDate.setText(selectedDate);
            },
            calendar.get(Calendar.YEAR),
            calendar.get(Calendar.MONTH),
            calendar.get(Calendar.DAY_OF_MONTH));

    dialog.getDatePicker().setMaxDate(System.currentTimeMillis());
    dialog.show();
}
```

> **Miesiące liczone są od zera.** `Calendar.JANUARY` to `0`, a grudzień to `11`.
> Dlatego przy wyświetlaniu daty dodajesz `month + 1`, a przy ustawianiu
> wartości początkowej – nie. To jest jedna z najstarszych pułapek w Javie
> i źródło błędów typu „urodziny o miesiąc za wcześnie".

`setMaxDate(System.currentTimeMillis())` blokuje wybór daty z przyszłości –
tańsze niż sprawdzanie tego potem w walidacji.

> **Dlaczego `Calendar`, a nie `LocalDate`.** Nowoczesne klasy dat z pakietu
> `java.time` wymagają Androida w wersji 8.0 (API 26). Nasz projekt ma `minSdk 24`,
> więc na starszych urządzeniach ich po prostu nie ma. Da się to obejść dodatkową
> konfiguracją budowania, ale na tym etapie prościej użyć `Calendar`, który działa
> wszędzie. To jest typowa decyzja w Androidzie: **nowsze API kontra zasięg urządzeń**.

---

## 7. `ScrollView` – formularz nie mieści się na ekranie

Osiem pól plus przycisk nie zmieści się na telefonie, a w orientacji poziomej
nie zmieści się nawet połowa. Bez przewijania dolna część formularza jest
niedostępna – łącznie z przyciskiem wysyłania.

`ScrollView` rozwiązuje to w jednej linii, ale ma **jedno ograniczenie**:

> **`ScrollView` może mieć dokładnie jedno dziecko.**

Więc nie owijasz nim pojedynczych pól, tylko cały pionowy `LinearLayout`:

```xml
<ScrollView ... >
    <LinearLayout android:orientation="vertical" ... >
        <!-- wszystkie pola formularza -->
    </LinearLayout>
</ScrollView>
```

Dziecko `ScrollView` ma `android:layout_height="wrap_content"` – ma być tak wysokie,
jak jego zawartość, a nie jak ekran. Wysokość `match_parent` oznaczałaby
„dokładnie tyle, co ekran", czyli nie byłoby czego przewijać.

Przydatny atrybut: `android:fillViewport="true"` na `ScrollView` sprawia,
że przy krótkiej treści dziecko i tak rozciąga się na cały ekran. Bez tego
tło formularza kończy się tam, gdzie kończą się pola.

---

## 8. Kompletny przykład – zapisy na kurs

Formularz z ośmioma polami, pełną walidacją, potwierdzeniem i podsumowaniem.

### `res/values/strings.xml`

```xml
<resources>
    <string name="app_name">Zapisy na kurs</string>

    <string name="header">Zapisy na kurs</string>
    <string name="label_name">Imię i nazwisko</string>
    <string name="label_email">Adres e-mail</string>
    <string name="label_birth_date">Data urodzenia</string>
    <string name="label_course">Kurs</string>
    <string name="label_mode">Tryb zajęć</string>
    <string name="label_level">Poziom zaawansowania</string>

    <string name="hint_name">Jan Kowalski</string>
    <string name="hint_email">jan@przyklad.pl</string>
    <string name="btn_pick_date">Wybierz datę</string>
    <string name="mode_onsite">Stacjonarny</string>
    <string name="mode_remote">Zdalny</string>
    <string name="newsletter">Chcę dostawać newsletter</string>
    <string name="terms">Akceptuję regulamin</string>
    <string name="btn_submit">WYŚLIJ ZGŁOSZENIE</string>

    <string name="error_name">Podaj imię i nazwisko</string>
    <string name="error_email">Niepoprawny adres e-mail</string>
    <string name="error_date">Wybierz datę urodzenia</string>
    <string name="error_course">Wybierz kurs z listy</string>
    <string name="error_mode">Zaznacz tryb zajęć</string>

    <string name="confirm_title">Wysłać zgłoszenie?</string>
    <string name="confirm_message">Sprawdź dane przed wysłaniem. Tej operacji nie da się cofnąć po zamknięciu aplikacji.</string>
    <string name="yes">Tak, wyślij</string>
    <string name="no">Popraw</string>

    <string name="sent">Zgłoszenie wysłane</string>
    <string name="undo">Cofnij</string>

    <string name="summary_format">%1$s\n%2$s\nUr. %3$s\nKurs: %4$s\nTryb: %5$s\nPoziom: %6$d/10\nNewsletter: %7$s</string>
    <string name="yes_short">tak</string>
    <string name="no_short">nie</string>

    <string-array name="courses">
        <item>-- wybierz kurs --</item>
        <item>Programowanie w Javie</item>
        <item>Aplikacje mobilne</item>
        <item>Bazy danych</item>
        <item>Sieci komputerowe</item>
    </string-array>
</resources>
```

### `res/layout/activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fillViewport="true"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:padding="16dp">

        <TextView
            android:id="@+id/tvHeader"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="#008080"
            android:padding="12dp"
            android:text="@string/header"
            android:textColor="#FFFFFF"
            android:textSize="28sp" />

        <TextView
            android:id="@+id/tvLabelName"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:text="@string/label_name" />

        <EditText
            android:id="@+id/etName"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/hint_name"
            android:inputType="textPersonName"
            android:maxLines="1" />

        <TextView
            android:id="@+id/tvLabelEmail"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/label_email" />

        <EditText
            android:id="@+id/etEmail"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/hint_email"
            android:inputType="textEmailAddress"
            android:maxLines="1" />

        <TextView
            android:id="@+id/tvLabelBirthDate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/label_birth_date" />

        <Button
            android:id="@+id/btnBirthDate"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/btn_pick_date" />

        <TextView
            android:id="@+id/tvLabelCourse"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/label_course" />

        <Spinner
            android:id="@+id/spCourse"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <TextView
            android:id="@+id/tvLabelMode"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/label_mode" />

        <RadioGroup
            android:id="@+id/rgMode"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <RadioButton
                android:id="@+id/rbOnsite"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/mode_onsite" />

            <RadioButton
                android:id="@+id/rbRemote"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginStart="16dp"
                android:text="@string/mode_remote" />
        </RadioGroup>

        <LinearLayout
            android:id="@+id/rowLevel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:orientation="horizontal">

            <TextView
                android:id="@+id/tvLabelLevel"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="@string/label_level" />

            <TextView
                android:id="@+id/tvLevelValue"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textStyle="bold"
                tools:text="5" />
        </LinearLayout>

        <SeekBar
            android:id="@+id/sbLevel"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:max="10"
            android:progress="0" />

        <com.google.android.material.materialswitch.MaterialSwitch
            android:id="@+id/swNewsletter"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="12dp"
            android:text="@string/newsletter" />

        <CheckBox
            android:id="@+id/cbTerms"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/terms" />

        <Button
            android:id="@+id/btnSubmit"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:enabled="false"
            android:text="@string/btn_submit" />

        <TextView
            android:id="@+id/tvSummary"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="16dp"
            android:textSize="16sp"
            tools:text="Jan Kowalski\njan@przyklad.pl" />

    </LinearLayout>
</ScrollView>
```

### `MainActivity.java`

```java
package com.example.zapisy;

import android.app.DatePickerDialog;
import android.os.Bundle;
import android.util.Patterns;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.EditText;
import android.widget.RadioGroup;
import android.widget.SeekBar;
import android.widget.Spinner;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

import com.google.android.material.materialswitch.MaterialSwitch;
import com.google.android.material.snackbar.Snackbar;

import java.util.Calendar;
import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private EditText etName, etEmail;
    private Button btnBirthDate, btnSubmit;
    private Spinner spCourse;
    private RadioGroup rgMode;
    private SeekBar sbLevel;
    private TextView tvLevelValue, tvSummary;
    private MaterialSwitch swNewsletter;
    private CheckBox cbTerms;

    private String birthDate = "";      // pusty = nie wybrano

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        etEmail = findViewById(R.id.etEmail);
        btnBirthDate = findViewById(R.id.btnBirthDate);
        btnSubmit = findViewById(R.id.btnSubmit);
        spCourse = findViewById(R.id.spCourse);
        rgMode = findViewById(R.id.rgMode);
        sbLevel = findViewById(R.id.sbLevel);
        tvLevelValue = findViewById(R.id.tvLevelValue);
        tvSummary = findViewById(R.id.tvSummary);
        swNewsletter = findViewById(R.id.swNewsletter);
        cbTerms = findViewById(R.id.cbTerms);

        setUpCourseSpinner();
        setUpListeners();
        tvLevelValue.setText(String.valueOf(sbLevel.getProgress()));
    }

    private void setUpCourseSpinner() {
        ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
                this, R.array.courses, android.R.layout.simple_spinner_item);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spCourse.setAdapter(adapter);
    }

    private void setUpListeners() {
        // regulamin odblokowuje przycisk wysyłania
        cbTerms.setOnCheckedChangeListener((buttonView, isChecked) ->
                btnSubmit.setEnabled(isChecked));

        btnBirthDate.setOnClickListener(v -> showDatePicker());
        btnSubmit.setOnClickListener(v -> {
            if (validateForm()) {
                askForConfirmation();
            }
        });

        // suwak - trzy metody, więc klasa anonimowa zamiast lambdy
        sbLevel.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                tvLevelValue.setText(String.valueOf(progress));
            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) { }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) { }
        });

        // lista - dwie metody, też klasa anonimowa
        spCourse.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
                if (position > 0) {
                    tvSummary.setText("");
                }
            }

            @Override
            public void onNothingSelected(AdapterView<?> parent) { }
        });
    }

    private void showDatePicker() {
        Calendar calendar = Calendar.getInstance();

        DatePickerDialog dialog = new DatePickerDialog(
                this,
                (view, year, month, dayOfMonth) -> {
                    birthDate = String.format(Locale.US, "%04d-%02d-%02d",
                            year, month + 1, dayOfMonth);     // miesiące od zera
                    btnBirthDate.setText(birthDate);
                },
                calendar.get(Calendar.YEAR),
                calendar.get(Calendar.MONTH),
                calendar.get(Calendar.DAY_OF_MONTH));

        dialog.getDatePicker().setMaxDate(System.currentTimeMillis());
        dialog.show();
    }

    private boolean validateForm() {
        String name = etName.getText().toString().trim();
        if (name.isEmpty()) {
            etName.setError(getString(R.string.error_name));
            etName.requestFocus();
            return false;
        }

        String email = etEmail.getText().toString().trim();
        if (!Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
            etEmail.setError(getString(R.string.error_email));
            etEmail.requestFocus();
            return false;
        }

        if (birthDate.isEmpty()) {
            Toast.makeText(this, R.string.error_date, Toast.LENGTH_SHORT).show();
            return false;
        }

        if (spCourse.getSelectedItemPosition() == 0) {       // pozycja 0 = brak wyboru
            Toast.makeText(this, R.string.error_course, Toast.LENGTH_SHORT).show();
            return false;
        }

        if (rgMode.getCheckedRadioButtonId() == -1) {        // -1 = nic nie zaznaczono
            Toast.makeText(this, R.string.error_mode, Toast.LENGTH_SHORT).show();
            return false;
        }

        return true;
    }

    private void askForConfirmation() {
        new AlertDialog.Builder(this)
                .setTitle(R.string.confirm_title)
                .setMessage(R.string.confirm_message)
                .setPositiveButton(R.string.yes, (dialog, which) -> submitForm())
                .setNegativeButton(R.string.no, null)
                .show();
    }

    private void submitForm() {
        tvSummary.setText(buildSummary());

        Snackbar.make(findViewById(R.id.main), R.string.sent, Snackbar.LENGTH_LONG)
                .setAction(R.string.undo, v -> tvSummary.setText(""))
                .show();
    }

    private String buildSummary() {
        String mode = rgMode.getCheckedRadioButtonId() == R.id.rbOnsite
                ? getString(R.string.mode_onsite)
                : getString(R.string.mode_remote);

        String newsletter = swNewsletter.isChecked()
                ? getString(R.string.yes_short)
                : getString(R.string.no_short);

        return getString(R.string.summary_format,
                etName.getText().toString().trim(),
                etEmail.getText().toString().trim(),
                birthDate,
                spCourse.getSelectedItem().toString(),
                mode,
                sbLevel.getProgress(),
                newsletter);
    }
}
```

Zwróć uwagę na podział: `onCreate` tylko podpina widoki i woła dwie metody
konfigurujące. Walidacja, potwierdzenie, wysyłka i budowanie podsumowania
to cztery osobne metody. Ten sam wzorzec co w kalkulatorze – **`onCreate`
jest spisem treści, nie treścią**.

---

## 9. Częste błędy

| Objaw | Przyczyna | Rozwiązanie |
|---|---|---|
| `Toast` pokazuje się zaraz po starcie aplikacji | `onItemSelected` wywołuje się przy pierwszym wyświetleniu | warunek `position > 0` |
| `Spinner` pusty | brak `setAdapter` albo zły identyfikator tablicy | sprawdź `R.array.*` i wywołanie adaptera |
| Rozwinięta lista wygląda źle | brak `setDropDownViewResource` | dopisz drugą linię konfiguracji adaptera |
| Nie da się podać lambdy przy `SeekBar` | interfejs ma trzy metody | klasa anonimowa |
| Data o miesiąc wcześniejsza | miesiące liczone od zera | `month + 1` przy wyświetlaniu |
| `AlertDialog` wygląda inaczej na starym telefonie | zaimportowany `android.app.AlertDialog` | zamień import na `androidx.appcompat.app.AlertDialog` |
| Dolna część formularza niedostępna | brak `ScrollView` | owiń cały `LinearLayout` |
| `ScrollView` nie przewija | dziecko ma `layout_height="match_parent"` | zmień na `wrap_content` |
| Błąd: `ScrollView can host only one direct child` | dwa elementy w `ScrollView` | włóż je do wspólnego `LinearLayout` |
| Przycisk wysyłania zawsze nieaktywny | `android:enabled="false"` i brak listenera na `CheckBox` | podepnij `setOnCheckedChangeListener` |
| Pięć komunikatów o błędach naraz | walidacja nie przerywa po pierwszym błędzie | `return false` w każdej gałęzi |
| `NullPointerException` w `getSelectedItem()` | adapter jeszcze nie ustawiony | ustaw adapter przed odczytem |

---

## 10. Ściąga

**Odczyt**

| Zapis | Zwraca |
|---|---|
| `et.getText().toString().trim()` | tekst z pola |
| `cb.isChecked()` | `boolean` |
| `rg.getCheckedRadioButtonId()` | identyfikator albo `-1` |
| `sb.getProgress()` | liczba z suwaka |
| `sp.getSelectedItemPosition()` | pozycja od zera |
| `sp.getSelectedItem().toString()` | wybrany tekst |

**Nasłuchiwanie**

| Zapis | Metod |
|---|---|
| `cb.setOnCheckedChangeListener((b, isChecked) -> ...)` | 1, lambda |
| `rg.setOnCheckedChangeListener((group, id) -> ...)` | 1, lambda |
| `sb.setOnSeekBarChangeListener(new ...)` | 3, klasa anonimowa |
| `sp.setOnItemSelectedListener(new ...)` | 2, klasa anonimowa |

**Komunikaty**

| Zapis | Kiedy |
|---|---|
| `Toast.makeText(this, R.string.x, Toast.LENGTH_SHORT).show()` | informacja |
| `Snackbar.make(view, R.string.x, Snackbar.LENGTH_LONG).setAction(...).show()` | informacja z cofnięciem |
| `new AlertDialog.Builder(this)...show()` | pytanie, decyzja |
| `et.setError(getString(R.string.x))` | błąd w konkretnym polu |

**Adapter listy**

```java
ArrayAdapter<CharSequence> a = ArrayAdapter.createFromResource(
        this, R.array.nazwa, android.R.layout.simple_spinner_item);
a.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
spinner.setAdapter(a);
```

**Wzorzec walidacji:** sprawdzaj po kolei, przy pierwszym błędzie `setError`
plus `requestFocus` plus `return false`.

---

## 11. Zadania

Jak zawsze: osobne repozytorium, `README.md` ze zrzutem ekranu, commit po każdym
kroku, push na koniec zajęć, teksty w `strings.xml`.

### Zadanie 1 – formularz zapisu

Zbuduj formularz z rozdziału 8. Oddajesz w pięciu commitach:

1. `feat: uklad formularza w ScrollView`
2. `feat: odczyt pol i podsumowanie bez walidacji`
3. `feat: spinner z lista kursow`
4. `feat: walidacja pol z setError`
5. `feat: potwierdzenie i snackbar`

W `README.md` odpowiedz: **dlaczego przy `SeekBar` nie dało się użyć lambdy,
a przy `CheckBox` tak?**

### Zadanie 2 – własny formularz

Wybierz jeden temat albo zaproponuj własny:

| Temat | Pola, które wymusza |
|---|---|
| **Zamówienie pizzy** | rozmiar (`RadioGroup`), dodatki (kilka `CheckBox`), ilość (`SeekBar`), adres (`EditText`) |
| **Rezerwacja wizyty** | usługa (`Spinner`), data (`DatePickerDialog`), godzina, dane kontaktowe |
| **Zgłoszenie usterki** | kategoria (`Spinner`), priorytet (`RadioGroup`), opis wielolinijkowy, zgoda na kontakt |
| **Ankieta satysfakcji** | pięć pytań na `SeekBar`, komentarz, zgoda na publikację |
| **Zapisy na wycieczkę** | kierunek (`Spinner`), liczba osób (`SeekBar`), ubezpieczenie (`Switch`), data wyjazdu |

**Wymagania – niezależnie od tematu:**

1. Minimum **pięć kontrolek różnego typu**, w tym `Spinner` zasilany z `string-array`.
2. `ScrollView` jako kontener główny.
3. Walidacja **co najmniej trzech pól**, przerywana na pierwszym błędzie,
   z `setError` tam, gdzie to możliwe.
4. **Okno dialogowe z potwierdzeniem** przed wykonaniem akcji.
5. **`Snackbar` z akcją cofnięcia** po wykonaniu.
6. Przynajmniej jedna **zależność między polami** – coś, co odblokowuje się,
   zmienia albo ukrywa w zależności od innej kontrolki.
7. `onCreate` tylko podpina i konfiguruje, logika w osobnych metodach.

**O co zapytam:** który interfejs nasłuchiwania ma więcej niż jedną metodę
i co z tego wynika dla zapisu kodu.

### Zadanie 3 – sprzątanie po sobie

Do formularza z zadania 2 dołóż przycisk **„Wyczyść formularz"**, który:

- pyta o potwierdzenie przez `AlertDialog`,
- po potwierdzeniu zeruje **wszystkie** kontrolki – pola tekstowe, `Spinner`
  na pozycję zero, `RadioGroup` przez `clearCheck()`, `SeekBar` na zero,
  `CheckBox` i `Switch` na niezaznaczone,
- usuwa komunikaty błędów z pól (`setError(null)`),
- pokazuje `Snackbar` z możliwością cofnięcia.

Cofnięcie jest tu trudniejsze, niż wygląda – musisz gdzieś zapamiętać poprzedni
stan formularza. Opisz w `README.md`, jak to rozwiązałeś.

### Zadanie 4 – `[bez AI]`

Bez agenta i bez czatu, wyłącznie ten materiał i dokumentacja.

Napisz ekran z **trzema polami** i walidacją:

- waga w kilogramach (`EditText`, `numberDecimal`),
- wzrost w centymetrach (`EditText`, `number`),
- płeć (`RadioGroup`).

Przycisk liczy BMI i pokazuje wynik razem z kategorią. Walidacja ma wychwycić:
puste pole, wartość zero, wzrost powyżej 300 cm i brak wyboru płci.
Każdy błąd wskazuje właściwe pole.

Commit z prefiksem `[bez AI]`. Na następnych zajęciach opowiadasz, który błąd
najtrudniej było wychwycić.
