# Ćw #12 - testy automatyczne skryptów (Unity Test Framework)

> [!NOTE]
> **Cel:** napisać testy jednostkowe (EditMode) i testy w trybie gry (PlayMode), które **znajdą prawdziwe błędy** w kodzie gry, a potem te błędy naprawić.
>
> **Narzędzia:** Unity 6.3 LTS, pakiet *Test Framework* (instalowany domyślnie), NUnit.
>
> **Czas:** 45 min.

---

## Scenariusz

Tester z zespołu zgłosił trzy problemy z systemem życia gracza w Roll-a-Ball:

> 1. „Po kilku trafieniach życie pokazuje **-30**.”
> 2. „Ekran *Game Over* pojawia się **kilka razy** po kolei.”
> 3. „Po śmierci zebrałem apteczkę i **kulka ożyła**.”

Zamiast klikać w Play i szukać błędu „na oko”, zapiszesz wymagania jako testy. Test, który świeci na czerwono, wskazuje błąd. Gdy błąd naprawisz, test zostaje w projekcie i pilnuje, żeby błąd nie wrócił.

## Plan

| # | Krok | Czas |
|---|------|-----:|
| 0 | [Po co to wszystko](#0-po-co-to-wszystko) | 3 min |
| 1 | [Konfiguracja: asmdef i Test Runner](#1-konfiguracja-asmdef-i-test-runner) | 7 min |
| 2 | [Część A - testy EditMode klasy `Health`](#2-część-a---testy-editmode-klasy-health) | 18 min |
| 3 | [Część B - test PlayMode generatora kostek](#3-część-b---test-playmode-generatora-kostek) | 14 min |
| 4 | [Oddanie](#4-oddanie) | 3 min |

---

## 0. Po co to wszystko

Unity udostępnia dwa rodzaje testów:

| | **EditMode** | **PlayMode** |
|---|---|---|
| Kiedy działa | w edytorze, bez wciskania Play | w uruchomionej scenie testowej |
| Co testujemy | czystą logikę C# (klasy bez `MonoBehaviour`) | komponenty, `Update`, fizykę, korutyny, czas |
| Szybkość | milisekundy | sekundy |
| Atrybut | `[Test]`, `[TestCase]` | `[UnityTest]` + `IEnumerator` + `yield return` |

> [!TIP]
> **Wniosek projektowy:** im więcej logiki wyciągniesz z `MonoBehaviour` do zwykłych klas, tym łatwiej ją przetestować. Wymusza to przy okazji porządek w kodzie, bo `MonoBehaviour` zajmuje się wtedy tylko „sklejaniem” logiki z silnikiem.

---

## 1. Konfiguracja: asmdef i Test Runner

Testy w Unity muszą leżeć w osobnym **assembly** (`.asmdef`) i **nie widzą** kodu z domyślnego `Assembly-CSharp`. Dlatego kod gry, który chcesz testować, też musi trafić do własnego assembly.

1. Utwórz strukturę folderów:

   ```text
   Assets/
   └── Lab03/
       ├── Scripts/          ← kod gry (Lab03.Game.asmdef)
       └── Tests/
           ├── EditMode/     ← testy EditMode
           └── PlayMode/     ← testy PlayMode
   ```

2. W `Assets/Lab03/Scripts`: **PPM → Create → Scripting → Assembly Definition**, nazwa: `Lab03.Game`.
3. Otwórz **Window → General → Test Runner**.
4. Zaznacz folder `Tests/EditMode`, w Test Runnerze przejdź na zakładkę **EditMode** i kliknij **Create a new Test Assembly Folder in the active path**. Powstanie plik `.asmdef` z ustawionymi referencjami do NUnit.
5. To samo zrób dla `Tests/PlayMode` w zakładce **PlayMode**.
6. W **obu** testowych `.asmdef` dodaj w *Assembly Definition References* referencję do **`Lab03.Game`** i kliknij **Apply**.

> [!IMPORTANT]
> Jeśli test nie widzi Twojej klasy (`The type or namespace name 'Health' could not be found`), to prawie zawsze brakuje referencji z kroku 6 albo skrypt leży poza folderem `Lab03/Scripts`.

**Test „dymny”:** w folderze `Tests/EditMode` utwórz **Create → Testing → C# Test Script** i uruchom go w Test Runnerze (**Run All**). Zielony znaczek oznacza, że konfiguracja działa. Potem możesz ten plik usunąć.

---

## 2. Część A - testy EditMode klasy `Health`

### 2.1 Kod do przetestowania

Logika życia z `PlayerController` (Ćw #3) została wyciągnięta do osobnej klasy. Utwórz plik `Assets/Lab03/Scripts/Health.cs`:

```csharp
using System;
using UnityEngine;

namespace Lab03
{
    public class Health
    {
        public int Max { get; }
        public int Current { get; private set; }
        public bool IsDead => Current <= 0;

        public event Action Died;

        public Health(int max)
        {
            Max = max;
            Current = max;
        }

        public void TakeDamage(int amount)
        {
            Current -= amount;
            if (Current <= 0)
                Died?.Invoke();
        }

        public void Heal(int amount)
        {
            Current = Mathf.Min(Current + amount, Max);
        }
    }
}
```

> [!WARNING]
> **Nie poprawiaj kodu od razu**, nawet jeśli widzisz błąd. Najpierw napisz test, który go wykaże (najpierw czerwony, potem zielony).

### 2.2 Wymagania (specyfikacja)

| ID | Wymaganie |
|----|-----------|
| W1 | Nowy obiekt `Health(max)` ma `Current == max` i nie jest martwy. |
| W2 | `TakeDamage(x)` zmniejsza życie o `x`, ale **nigdy poniżej 0**. |
| W3 | `Heal(x)` zwiększa życie o `x`, ale **nigdy powyżej `Max`**. |
| W4 | Zdarzenie `Died` jest wywoływane **dokładnie raz**, w chwili gdy życie spada do 0. Kolejne obrażenia już go nie wywołują. |
| W5 | **Martwego gracza nie da się uleczyć**: `Heal` po śmierci nic nie zmienia. |
| W6 | Ujemna wartość w `TakeDamage` lub `Heal` rzuca `ArgumentOutOfRangeException`. |

### 2.3 Zadanie

W folderze `Tests/EditMode` utwórz `HealthTests.cs`. Pierwszy test masz gotowy:

```csharp
using System;
using NUnit.Framework;
using Lab03;

public class HealthTests
{
    [Test]
    public void NewHealth_StartsFull_AndAlive()      // W1
    {
        var hp = new Health(100);

        Assert.AreEqual(100, hp.Current);
        Assert.IsFalse(hp.IsDead);
    }

    // TODO: testy dla W2-W6
}
```

Dopisz **co najmniej po jednym teście dla każdego wymagania W2-W6**. Przydatne konstrukcje:

| Potrzeba | Konstrukcja NUnit |
|----------|-------------------|
| jeden test, wiele danych | `[TestCase(30, 70)]` nad metodą z parametrami `(int dmg, int expected)` |
| sprawdzenie wyjątku | `Assert.Throws<ArgumentOutOfRangeException>(() => hp.TakeDamage(-10));` |
| zliczanie zdarzeń | `int calls = 0; hp.Died += () => calls++;` |

> [!TIP]
> Nazywaj testy według schematu `Metoda_Sytuacja_OczekiwanyWynik`, np. `TakeDamage_MoreThanCurrent_ClampsToZero`. Gdy test się wywali, sama nazwa w Test Runnerze powie, co jest zepsute.

### 2.4 Napraw i powtórz

1. Uruchom testy: część powinna być **czerwona**. Zapisz, które wymagania nie są spełnione.
2. Popraw `Health.cs` tak, żeby wszystkie testy były zielone.
3. Uruchom testy jeszcze raz. Upewnij się, że naprawiając jeden błąd, nie zepsułeś innego wymagania.

---

## 3. Część B - test PlayMode generatora kostek

Generator z Ćw #8 ma pilnować, żeby na scenie nie było więcej niż `maxCubes` kostek. Tego nie da się sprawdzić testem EditMode, bo potrzebne są `Update`, `Time.deltaTime` i `Destroy`.

### 3.1 Przygotuj klasę do testowania

Przenieś `RandomCubeGenerator.cs` do `Assets/Lab03/Scripts` (**przenieś w oknie Project, nie kopiuj**, wtedy scena z Ćw #8 dalej działa). Następnie wprowadź zmiany:

```csharp
using System.Collections.Generic;
using UnityEngine;

namespace Lab03
{
    public class RandomCubeGenerator : MonoBehaviour
    {
        [SerializeField] private int maxCubes = 10;
        [SerializeField] private float spawnInterval = 1f;

        private readonly List<GameObject> cubes = new List<GameObject>();
        private float timer;

        // Do odczytu w testach (i np. w UI)
        public int CubeCount => cubes.Count;

        // Pozwala testowi ustawić parametry bez Inspektora
        public void Configure(int maxCubes, float spawnInterval)
        {
            this.maxCubes = maxCubes;
            this.spawnInterval = spawnInterval;
        }

        // ... Update() i GenerateCube() bez zmian
    }
}
```

> [!NOTE]
> Wcześniej `maxCubes` i `spawnInterval` były prywatne i „zaszyte” w kodzie, więc test nie miał jak ich zmienić. Zmiana kodu po to, żeby dało się go przetestować, jest normalną częścią pracy. Pola nadal są prywatne, a z zewnątrz dostępny jest tylko kontrolowany interfejs.

### 3.2 Testy

W folderze `Tests/PlayMode` utwórz `RandomCubeGeneratorTests.cs`:

```csharp
using System.Collections;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
using Lab03;

public class RandomCubeGeneratorTests
{
    private GameObject generatorGo;

    [SetUp]
    public void SetUp()
    {
        generatorGo = new GameObject("Generator");
    }

    [TearDown]
    public void TearDown()
    {
        Object.Destroy(generatorGo);
        foreach (var rb in Object.FindObjectsByType<Rigidbody>(FindObjectsSortMode.None))
            Object.Destroy(rb.gameObject);
    }

    [UnityTest]
    public IEnumerator Generator_SpawnsCubesOverTime()
    {
        var gen = generatorGo.AddComponent<RandomCubeGenerator>();
        gen.Configure(maxCubes: 5, spawnInterval: 0.1f);

        Assert.AreEqual(0, gen.CubeCount, "Przed pierwszą klatką nie powinno być kostek");

        yield return new WaitForSeconds(0.35f);

        Assert.GreaterOrEqual(gen.CubeCount, 2);
    }

    [UnityTest]
    public IEnumerator Generator_NeverExceedsMaxCubes()
    {
        // TODO
        yield return null;
    }
}
```

**Zadanie:** dokończ test `Generator_NeverExceedsMaxCubes`:

1. Ustaw `maxCubes = 3` i bardzo krótki `spawnInterval` (np. `0.05f`).
2. Odczekaj tyle, żeby generator spróbował utworzyć **wyraźnie więcej** niż 3 kostki.
3. Sprawdź **dwie rzeczy**:
   - `gen.CubeCount == 3` (stan listy w skrypcie),
   - liczba obiektów z `Rigidbody` na scenie `== 3` (stan faktycznej sceny).

> [!IMPORTANT]
> **Pułapka:** `Destroy()` nie usuwa obiektu od razu, tylko **na końcu klatki**. Jeśli policzysz obiekty na scenie zaraz po `WaitForSeconds`, możesz dostać 4 zamiast 3 i test będzie raz zielony, raz czerwony (*flaky test*).
> Zastanów się, jak „zamrozić” generator przed liczeniem i ile klatek trzeba odczekać.

**Pytanie kontrolne:** czym różni się sprawdzenie `CubeCount` od policzenia obiektów na scenie? Który z tych testów złapałby błąd, w którym ktoś usuwa kostkę z listy, ale zapomina o `Destroy()`?

---

## 4. Oddanie

- [ ] Zrzut ekranu Test Runnera: **wszystkie testy EditMode i PlayMode zielone**.
- [ ] Poprawiony `Health.cs` + `HealthTests.cs` (min. 6 testów, w tym min. jeden `[TestCase]` i jeden `Assert.Throws`).
- [ ] `RandomCubeGeneratorTests.cs` z dokończonym drugim testem.
- [ ] W README / komentarzu: lista błędów znalezionych w `Health` (które wymaganie łamały).

## Zadania dodatkowe

1. Podłącz klasę `Health` do `PlayerController` (pole `private Health health;`, inicjalizacja w `Awake`). Po `Died` wyłącz sterowanie kulką.
2. Wyciągnij logikę licznika znajdziek z Roll-a-Ball (`Zebrane 3/8`, „Wygrana”) do klasy `PickupCounter` i napisz dla niej testy EditMode, w tym: zdarzenie `Won` wywołuje się raz, licznik nie przekracza liczby znajdziek.
3. Przyspiesz test PlayMode, ustawiając `Time.timeScale = 10f` (pamiętaj, żeby przywrócić `1f` w `TearDown`!).

---

<details>
<summary><b>Dla prowadzącego - błędy i przykładowe rozwiązanie</b></summary>

**Błędy w `Health`:**

| Wymaganie | Błąd |
|-----------|------|
| W2 | brak ograniczenia do 0: `TakeDamage(250)` daje `-150` |
| W4 | `Died` wywołuje się przy każdym trafieniu po śmierci |
| W5 | `Heal` po śmierci „ożywia” gracza |
| W6 | brak walidacji, `TakeDamage(-50)` leczy ponad `Max` |

W1 i W3 są spełnione. To celowe, żeby kursanci zobaczyli, że zielony test też niesie informację.

**Poprawiony `Health`:**

```csharp
public void TakeDamage(int amount)
{
    if (amount < 0) throw new ArgumentOutOfRangeException(nameof(amount));
    if (IsDead) return;

    Current = Mathf.Max(Current - amount, 0);
    if (Current == 0)
        Died?.Invoke();
}

public void Heal(int amount)
{
    if (amount < 0) throw new ArgumentOutOfRangeException(nameof(amount));
    if (IsDead) return;

    Current = Mathf.Min(Current + amount, Max);
}
```

**Przykładowe testy:**

```csharp
[TestCase(30, 70)]
[TestCase(100, 0)]
[TestCase(250, 0)]
public void TakeDamage_ReducesHealth_NotBelowZero(int dmg, int expected)
{
    var hp = new Health(100);
    hp.TakeDamage(dmg);
    Assert.AreEqual(expected, hp.Current);
}

[Test]
public void Heal_DoesNotExceedMax()
{
    var hp = new Health(100);
    hp.TakeDamage(20);
    hp.Heal(50);
    Assert.AreEqual(100, hp.Current);
}

[Test]
public void Died_IsRaisedExactlyOnce()
{
    var hp = new Health(50);
    int calls = 0;
    hp.Died += () => calls++;

    hp.TakeDamage(50);
    hp.TakeDamage(10);

    Assert.AreEqual(1, calls);
}

[Test]
public void Heal_WhenDead_StaysDead()
{
    var hp = new Health(100);
    hp.TakeDamage(100);
    hp.Heal(50);

    Assert.AreEqual(0, hp.Current);
    Assert.IsTrue(hp.IsDead);
}

[Test]
public void NegativeAmount_Throws()
{
    var hp = new Health(100);
    Assert.Throws<ArgumentOutOfRangeException>(() => hp.TakeDamage(-10));
    Assert.Throws<ArgumentOutOfRangeException>(() => hp.Heal(-10));
}
```

**Test PlayMode:**

```csharp
[UnityTest]
public IEnumerator Generator_NeverExceedsMaxCubes()
{
    var gen = generatorGo.AddComponent<RandomCubeGenerator>();
    gen.Configure(maxCubes: 3, spawnInterval: 0.05f);

    yield return new WaitForSeconds(1f);   // ~15-20 prób utworzenia kostki

    gen.enabled = false;                   // zamrażamy generator
    yield return null;                     // czekamy, aż Destroy() się wykona

    Assert.AreEqual(3, gen.CubeCount);
    int inScene = Object.FindObjectsByType<Rigidbody>(FindObjectsSortMode.None).Length;
    Assert.AreEqual(3, inScene);
}
```

Odpowiedź na pytanie kontrolne: `CubeCount` sprawdza tylko stan wewnętrzny listy. Wycieku obiektów (usunięcie z listy bez `Destroy()`) nie zobaczy. Złapie go dopiero policzenie obiektów na scenie.

</details>
