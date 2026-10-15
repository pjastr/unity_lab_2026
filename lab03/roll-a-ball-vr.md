# Pierwszy projekt VR: Roll-a-Ball na Meta Quest 3S

> [!NOTE]
> **Cel:** zbudować w Unity 6.3 grę w stylu klasycznego *Roll-a-Ball* w wersji VR i uruchomić ją na goglach Meta Quest 3S.
> Plansza leży przed Tobą na stole jak makieta. Kulką sterujesz **gałką lewego kontrolera**, a zebrane znajdźki pokazuje licznik zawieszony nad planszą.
>
> **Platforma:** wyłącznie **Meta Quest** — gra działa i jest testowana tylko w goglach.
>
> **Sterowanie:** **OVRInput** (Meta XR Core SDK) — bezpośredni odczyt gałki kontrolera.
>
> **Czas:** ok. 90–120 min.

---

## Spis kroków

| # | Krok | Czas |
|---|------|-----:|
| 0 | [Zanim zaczniesz](#0-zanim-zaczniesz) | 5 min |
| 1 | [Ustawienia projektu](#1-ustawienia-projektu) | 5 min |
| 2 | [Scena i Camera Rig](#2-scena-i-camera-rig) | 10 min |
| 3 | [Stół i plansza](#3-stół-i-plansza) | 15 min |
| 4 | [Kula gracza](#4-kula-gracza) | 5 min |
| 5 | [Skrypt PlayerController](#5-skrypt-playercontroller) | 15 min |
| 6 | [Znajdźki](#6-znajdźki) | 10 min |
| 7 | [Licznik i napis „Wygrana”](#7-licznik-i-napis-wygrana) | 10 min |
| 8 | [Build And Run na gogle](#8-build-and-run-na-gogle) | 10 min |
| 9 | [Test w goglach](#9-test-w-goglach) | 10 min |

Dodatki: [Ściąga OVRInput](#ściąga-ovrinput) · [Rozwiązywanie problemów](#rozwiązywanie-problemów) · [Zadania dodatkowe](#zadania-dodatkowe)

### Jak wygląda scena

```text
                  [ Licznik: Zebrane 3/8 ]        ← panel nad tylną krawędzią planszy
              ┌──────────────────────────────┐
              │  ■        ■        ■         │
              │                              │    ← plansza 1 × 1 m na stole (wys. 0,75 m)
              │  ■        ●        ■         │       ● kula 5 cm, ■ znajdźki
              │                              │
              │  ■        ■        ■         │
              └──────────────────────────────┘
                            ▲
                         GRACZ                    ← stoi/siedzi w punkcie (0, 0, 0), patrzy w +Z
```

> [!IMPORTANT]
> **Kamera się nie rusza.** W oryginalnym tutorialu kamera jedzie za kulą — w goglach powoduje to mdłości. Tutaj patrzysz na planszę z góry, jak na zabawkę, i możesz się nad nią pochylić.

---

## 0. Zanim zaczniesz

- [ ] W goglach musi być włączone **debugowanie USB**. Jeśli po podłączeniu kabla gogle nie pytają o zgodę na debugowanie lub nie widać ich w Unity — **zgłoś to prowadzącemu** w celu aktywacji.
- [ ] Gogle naładowane (> 30%), kontrolery z bateriami.
- [ ] Kabel **USB-C z transmisją danych**.
- [ ] Projekt otwarty w **Unity 6.3 LTS**.
- [ ] Wymyśl unikalną nazwę pakietu aplikacji, np. `com.kowalski.rollaballvr` (przyda się w kroku 8 — na wspólnych goglach aplikacje kursantów nie będą się nadpisywać).

---

## 1. Ustawienia projektu

### 1.1 Project Setup Tool

1. **Window → Meta → Meta XR SDK** → **Project Setup Tool** (w starszych wersjach SDK: *Meta → Tools → Project Setup Tool*).
2. Na zakładce **Android** kliknij **Fix All** w sekcjach *Required* i *Recommended*.
3. Powtarzaj, aż lista *Required* będzie pusta.

### 1.2 Fizyka dla małej skali

Kula będzie miała tylko 5 cm średnicy. Domyślne ustawienia fizyki są dobrane do obiektów ~1 m.

1. **Edit → Project Settings → Physics** → **Default Contact Offset** → `0.002`.
2. **Edit → Project Settings → Time** → **Fixed Timestep** → `0.0138889` (= 1/72 s, tyle co odświeżanie gogli).

---

## 2. Scena i Camera Rig

### 2.1 Nowa scena

1. **File → New Scene** → szablon **Basic (URP)** → **Create**.
2. **File → Save As** → `Assets/RollABallVR/Scenes/RollABallVR.unity` (utwórz foldery `RollABallVR/Scenes`, `RollABallVR/Scripts`, `RollABallVR/Materials`, `RollABallVR/Prefabs`).
3. W **Hierarchy** zaznacz **Main Camera** → **Delete**. W VR kamerą jest głowa gracza.

### 2.2 Camera Rig

1. Otwórz **Building Blocks**: **Window → Meta → Meta XR SDK → Building Blocks** (w starszych wersjach SDK: *Meta → Tools → Building Blocks*).
2. Znajdź blok **Camera Rig** → przeciągnij do sceny (lub kliknij **Add**).
3. W Hierarchy pojawi się `[BuildingBlock] Camera Rig`. Sprawdź, że jego **Transform** to pozycja `(0, 0, 0)`, rotacja `(0, 0, 0)`.
4. Zaznacz go → w Inspectorze znajdź komponent **OVR Manager** → **Tracking Origin Type** → **Floor Level**.
5. Dodaj blok **Controller Tracking** — w goglach zobaczysz modele kontrolerów.

Struktura rigu, której będziesz używać:

```text
[BuildingBlock] Camera Rig          ← zawiera OVR Manager (wymagany przez OVRInput)
└── TrackingSpace
    ├── CenterEyeAnchor             ← głowa gracza (kamera) — potrzebna w kroku 5
    ├── LeftHandAnchor
    └── RightHandAnchor
```

> [!IMPORTANT]
> **OVRInput działa tylko wtedy, gdy w scenie jest komponent OVR Manager.** Znajduje się on na Camera Rig — nie usuwaj go i nie wyłączaj.

> [!TIP]
> **Floor Level** oznacza, że `y = 0` w scenie to prawdziwa podłoga. Dzięki temu stół o wysokości 0,75 m będzie miał w goglach realną wysokość stołu.

---

## 3. Stół i plansza

Wszystkie wymiary są w metrach (1 jednostka Unity = 1 m).

### 3.1 Materiały

W `Assets/RollABallVR/Materials` utwórz (**Create → Material**, shader *Universal Render Pipeline/Lit*) i nadaj kolory (*Base Map*):

| Materiał | Kolor | Do czego |
|----------|-------|----------|
| `M_Floor` | ciemnoszary | podłoga |
| `M_Table` | brązowy | stół |
| `M_Board` | jasnoszary | plansza |
| `M_Wall` | granatowy | bandy |
| `M_Player` | niebieski / czerwony | kula |
| `M_PickUp` | żółty | znajdźki |

Materiał przypisujesz, przeciągając go na obiekt w Scene lub Hierarchy.

### 3.2 Podłoga i stół

| Obiekt (GameObject → 3D Object) | Nazwa | Position | Scale | Materiał |
|-------------------------------|-------|----------|-------|----------|
| Plane | `Floor` | `(0, 0, 0)` | `(2, 1, 2)` | `M_Floor` |
| Cube | `Table` | `(0, 0.375, 0.9)` | `(1.2, 0.75, 1.2)` | `M_Table` |

### 3.3 Plansza

1. **GameObject → Create Empty** → nazwa `Board` → Position `(0, 0.75, 0.9)`, Rotation `(0, 0, 0)`, Scale `(1, 1, 1)`.
   To punkt na środku blatu — **wszystkie kolejne elementy planszy będą jego dziećmi**, więc ich pozycje są liczone względem blatu.
2. Utwórz poniższe obiekty i przeciągnij każdy **na `Board`** w Hierarchy (stają się dziećmi). Wartości to **pozycje lokalne**:

| Obiekt | Nazwa | Position (lokalna) | Scale | Materiał |
|--------|-------|-------------------|-------|----------|
| Cube | `Ground` | `(0, 0.01, 0)` | `(1, 0.02, 1)` | `M_Board` |
| Cube | `Wall_North` | `(0, 0.05, 0.51)` | `(1.04, 0.06, 0.02)` | `M_Wall` |
| Cube | `Wall_South` | `(0, 0.05, -0.51)` | `(1.04, 0.06, 0.02)` | `M_Wall` |
| Cube | `Wall_East` | `(0.51, 0.05, 0)` | `(0.02, 0.06, 1.04)` | `M_Wall` |
| Cube | `Wall_West` | `(-0.51, 0.05, 0)` | `(0.02, 0.06, 1.04)` | `M_Wall` |

> [!TIP]
> Jeśli po przeciągnięciu na `Board` wartości Position się zmieniły — wpisz je ponownie. Unity przelicza pozycję tak, żeby obiekt „nie przeskoczył” w świecie.

3. Zaznacz `Board` → w prawym górnym rogu Inspectora zaznacz **Static** → na pytanie o dzieci wybierz **Yes, change children**. Plansza się nie rusza, więc Unity może ją lepiej zoptymalizować.

---

## 4. Kula gracza

1. **GameObject → 3D Object → Sphere** → nazwa `Player` → przeciągnij na `Board`.
2. **Transform:** Position (lokalna) `(0, 0.045, 0)`, Scale `(0.05, 0.05, 0.05)` (średnica 5 cm, leży na planszy).
3. Materiał `M_Player`.
4. W prawym górnym rogu Inspectora **odznacz Static** (kula się porusza).
5. **Add Component → Rigidbody** i ustaw:

| Pole | Wartość | Dlaczego |
|------|---------|----------|
| Mass | `1` | |
| Linear Damping | `0.5` | kula sama wyhamowuje — łatwiej sterować |
| Angular Damping | `0.5` | |
| Interpolate | **Interpolate** | płynny ruch w goglach (fizyka i obraz mają różny takt) |
| Collision Detection | **Continuous Dynamic** | mała, szybka kula nie przeniknie przez bandy |

---

## 5. Skrypt PlayerController

### 5.1 Kod

`Assets/RollABallVR/Scripts` → **Create → MonoBehaviour Script** → nazwa `PlayerControllerVR` → wklej:

```csharp
using TMPro;
using UnityEngine;

[RequireComponent(typeof(Rigidbody))]
public class PlayerControllerVR : MonoBehaviour
{
    [Header("Sterowanie")]
    [Tooltip("CenterEyeAnchor z Camera Rig – ruch jest liczony względem kierunku patrzenia")]
    [SerializeField] private Transform head;

    [Tooltip("Przyspieszenie kuli w m/s²")]
    [SerializeField] private float acceleration = 1.5f;

    [Tooltip("Maksymalna prędkość kuli w m/s")]
    [SerializeField] private float maxSpeed = 0.6f;

    [Header("Interfejs")]
    [SerializeField] private TMP_Text countText;
    [SerializeField] private GameObject winText;

    private Rigidbody rb;
    private Vector2 moveInput;
    private Vector3 startLocalPosition;
    private int count;
    private int totalPickUps;

    private void Awake()
    {
        rb = GetComponent<Rigidbody>();
        startLocalPosition = transform.localPosition;
    }

    private void Start()
    {
        totalPickUps = GameObject.FindGameObjectsWithTag("PickUp").Length;
        count = 0;
        winText.SetActive(false);
        UpdateCountText();
    }

    private void Update()
    {
        // Gałka lewego kontrolera (Primary = lewy kontroler Touch)
        moveInput = OVRInput.Get(OVRInput.Axis2D.PrimaryThumbstick);

        // Zabezpieczenie: kula spadła z planszy → wraca na start
        if (transform.localPosition.y < startLocalPosition.y - 0.5f)
            ResetBall();
    }

    private void FixedUpdate()
    {
        // "Przód" = kierunek patrzenia gracza rzutowany na poziom
        Vector3 forward = Vector3.ProjectOnPlane(head.forward, Vector3.up);

        // Gdy gracz patrzy prosto w dół, forward jest bliski zeru – wtedy bierzemy "górę" głowy
        if (forward.sqrMagnitude < 0.01f)
            forward = Vector3.ProjectOnPlane(head.up, Vector3.up);

        forward.Normalize();
        Vector3 right = Vector3.Cross(Vector3.up, forward);
        Vector3 direction = forward * moveInput.y + right * moveInput.x;

        rb.AddForce(direction * acceleration, ForceMode.Acceleration);

        // Ograniczenie prędkości w poziomie
        Vector3 velocity = rb.linearVelocity;
        Vector3 flat = new(velocity.x, 0f, velocity.z);
        if (flat.magnitude > maxSpeed)
        {
            flat = flat.normalized * maxSpeed;
            rb.linearVelocity = new Vector3(flat.x, velocity.y, flat.z);
        }
    }

    private void OnTriggerEnter(Collider other)
    {
        if (!other.CompareTag("PickUp")) return;

        other.gameObject.SetActive(false);
        count++;
        UpdateCountText();

        if (count >= totalPickUps)
            winText.SetActive(true);
    }

    private void ResetBall()
    {
        rb.linearVelocity = Vector3.zero;
        rb.angularVelocity = Vector3.zero;
        transform.localPosition = startLocalPosition;
    }

    private void UpdateCountText()
    {
        countText.text = $"Zebrane: {count}/{totalPickUps}";
    }
}
```

### 5.2 Jak działa odczyt gałki

```csharp
moveInput = OVRInput.Get(OVRInput.Axis2D.PrimaryThumbstick);
```

- `OVRInput.Get(...)` zwraca **aktualny stan** elementu kontrolera — dla gałki `Vector2` z zakresu −1…1 (`x` = lewo/prawo, `y` = przód/tył).
- **Primary** oznacza lewy kontroler, **Secondary** — prawy.
- OVRInput sam pomija drobne wychylenia gałki (martwa strefa), więc kula nie toczy się, gdy nie dotykasz gałki.
- Odczyt jest w `Update` (co klatkę), a siła jest przykładana w `FixedUpdate` (co krok fizyki).

### 5.3 Co jest inaczej niż w oryginalnym Roll-a-Ball

| Oryginał (monitor) | Wersja VR | Powód |
|--------------------|-----------|-------|
| Kamera jedzie za kulą (`CameraController`) | Kamera = głowa gracza, nie rusza się | Komfort — brak mdłości |
| Ruch względem osi świata | Ruch względem **kierunku patrzenia** | Po obróceniu się „gałka do przodu” nadal oznacza „ode mnie” |
| Klawiatura / gamepad (`PlayerInput` + `OnMove`) | Gałka kontrolera Quest (`OVRInput.Get(...)`) | Bezpośredni odczyt kontrolera Meta, bez konfigurowania pliku akcji |
| `ForceMode.Force`, prędkość ~10 | `ForceMode.Acceleration`, 1,5 m/s² + limit 0,6 m/s | Plansza ma 1 m, kula 5 cm |
| Napisy na ekranie (Screen Space) | Panel w przestrzeni (World Space) | W VR nie ma „ekranu” |

### 5.4 Podpięcie skryptu

1. Przeciągnij `PlayerController` na obiekt `Player`.
2. W Inspectorze uzupełnij:
   - **Head** → przeciągnij `CenterEyeAnchor` (Camera Rig → TrackingSpace → CenterEyeAnchor).
   - **Count Text** i **Win Text** — uzupełnisz w kroku 7.

---

## 6. Znajdźki

### 6.1 Tag

1. Zaznacz dowolny obiekt → Inspector → **Tag → Add Tag…** → **+** → `PickUp` → **Save**.

### 6.2 Skrypt obracania

`Assets/RollABallVR/Scripts` → **Create → MonoBehaviour Script** → `Rotator`:

```csharp
using UnityEngine;

public class Rotator : MonoBehaviour
{
    [SerializeField] private Vector3 degreesPerSecond = new(15f, 30f, 45f);

    private void Update()
    {
        transform.Rotate(degreesPerSecond * Time.deltaTime);
    }
}
```

### 6.3 Pierwsza znajdźka i prefab

1. **GameObject → 3D Object → Cube** → nazwa `PickUp` → przeciągnij na `Board`.
2. Position (lokalna) `(0, 0.06, 0.3)`, Rotation `(45, 45, 45)`, Scale `(0.03, 0.03, 0.03)`.
3. Materiał `M_PickUp`.
4. Odznacz **Static** (znajdźka się obraca).
5. **Box Collider** → zaznacz **Is Trigger**.
6. **Tag** → `PickUp`.
7. **Add Component** → `Rotator`.
8. Przeciągnij `PickUp` z Hierarchy do `Assets/RollABallVR/Prefabs` → powstaje prefab.

### 6.4 Rozmieszczenie

Zduplikuj znajdźkę (**Ctrl/Cmd + D**) do 8 sztuk i ustaw pozycje lokalne na okręgu wokół środka:

| # | Position (lokalna) |
|---|-------------------|
| 1 | `(0, 0.06, 0.3)` |
| 2 | `(0.21, 0.06, 0.21)` |
| 3 | `(0.3, 0.06, 0)` |
| 4 | `(0.21, 0.06, -0.21)` |
| 5 | `(0, 0.06, -0.3)` |
| 6 | `(-0.21, 0.06, -0.21)` |
| 7 | `(-0.3, 0.06, 0)` |
| 8 | `(-0.21, 0.06, 0.21)` |

> [!TIP]
> Dla porządku w Hierarchy utwórz w `Board` pusty obiekt `PickUps` (pozycja lokalna `0, 0, 0`) i przenieś do niego wszystkie znajdźki.

---

## 7. Licznik i napis „Wygrana”

### 7.1 Canvas w przestrzeni

1. **GameObject → UI → Canvas** → nazwa `ScoreCanvas`.
   Jeśli Unity zapyta o **TMP Essentials** — kliknij **Import TMP Essentials**.
2. Komponent **Canvas** → **Render Mode: World Space**.
3. **Rect Transform**:

| Pole | Wartość |
|------|---------|
| Pos X / Y / Z | `0` / `1.05` / `1.5` |
| Width / Height | `400` / `200` |
| Rotation | `(0, 0, 0)` |
| Scale | `(0.001, 0.001, 0.001)` |

   Panel ma więc 40 × 20 cm i wisi nad tylną krawędzią planszy, przodem do gracza.

> [!NOTE]
> Obiekt `EventSystem`, który Unity mogło dodać razem z Canvas, nie przeszkadza — możesz go zostawić.

### 7.2 Teksty

1. Prawy klik na `ScoreCanvas` → **UI → Text - TextMeshPro** → nazwa `CountText`:
   - Pos `(0, 50, 0)`, Width `380`, Height `80`
   - Text: `Zebrane: 0/8`, **Font Size** `48`, **Alignment**: środek, kolor biały.
2. Prawy klik na `ScoreCanvas` → **UI → Text - TextMeshPro** → nazwa `WinText`:
   - Pos `(0, -40, 0)`, Width `380`, Height `100`
   - Text: `Wygrana!`, **Font Size** `72`, pogrubienie, kolor zielony / żółty, wyśrodkowany.
3. *(Opcjonalnie)* tło: prawy klik na `ScoreCanvas` → **UI → Image**, przesuń je w Hierarchy na pierwsze miejsce (nad teksty), rozciągnij na cały Canvas, kolor czarny z przezroczystością ~60%.

### 7.3 Podpięcie do skryptu

Zaznacz `Player` → w komponencie **Player Controller**:
- **Count Text** → przeciągnij `CountText`,
- **Win Text** → przeciągnij `WinText`.

---

## 8. Build And Run na gogle

### 8.1 Nazwa aplikacji

**Edit → Project Settings → Player**:
- **Company Name** → Twoje nazwisko, **Product Name** → `RollABallVR`.
- Zakładka Android / Meta Quest → **Other Settings → Override Default Package Name** → zaznacz → `com.nazwisko.rollaballvr` (bez polskich znaków i spacji).

### 8.2 Build Profile

1. Sprawdź okno **Console** — nie może być **czerwonych błędów kompilacji** (inaczej build się nie uruchomi).
2. **File → Build Profiles** → zaznacz profil **Meta Quest**.
3. **Scene List** → **Add Open Scenes**. Odznacz wszystkie inne sceny — zostaje tylko `RollABallVR`.
4. Zaznacz **Development Build**.
5. Podłącz gogle kablem USB-C. Jeśli w goglach pojawi się pytanie o zezwolenie na debugowanie USB → **Zawsze zezwalaj z tego komputera** → **Zezwól**.
6. **Run Device** → kliknij **Refresh** → wybierz gogle z listy.
7. Kliknij **Build And Run** → zapisz jako `Builds/RollABallVR.apk` (folder `Builds` utwórz poza `Assets`, obok folderu projektu).

> [!NOTE]
> Pierwszy build trwa długo (5–15 min). Kolejne są znacznie szybsze. Postęp widać na pasku w prawym dolnym rogu edytora.

Po zakończeniu aplikacja **uruchomi się sama** w goglach.

---

## 9. Test w goglach

1. Załóż gogle i weź **kontrolery do rąk** (przy odłożonych kontrolerach gogle przechodzą na śledzenie dłoni, a dłonie nie mają gałki).
2. Stań (lub usiądź) i przytrzymaj **przycisk Meta** na prawym kontrolerze, aby wycentrować widok — stół powinien być przed Tobą.
3. Graj **lewą gałką**.

Lista kontrolna:

- [ ] Stół stoi na podłodze i ma realistyczną wysokość.
- [ ] Widzę modele kontrolerów.
- [ ] Gałka do przodu toczy kulę „ode mnie” — także po obróceniu się w bok.
- [ ] Ruch kuli jest płynny (bez drgania).
- [ ] Znajdźki znikają, licznik rośnie, po zebraniu wszystkich pojawia się „Wygrana!”.
- [ ] Licznik jest czytelny.

Aplikację uruchomisz ponownie z **Biblioteki** w goglach → filtr **Nieznane źródła**.

---

## Ściąga OVRInput

Najczęściej używane odczyty dla kontrolerów Touch Plus (Quest 3S):

| Element | Kod | Zwraca |
|---------|-----|--------|
| Lewa gałka | `OVRInput.Get(OVRInput.Axis2D.PrimaryThumbstick)` | `Vector2` (−1…1) |
| Prawa gałka | `OVRInput.Get(OVRInput.Axis2D.SecondaryThumbstick)` | `Vector2` (−1…1) |
| Lewy spust | `OVRInput.Get(OVRInput.Axis1D.PrimaryIndexTrigger)` | `float` (0…1) |
| Prawy spust | `OVRInput.Get(OVRInput.Axis1D.SecondaryIndexTrigger)` | `float` (0…1) |
| Lewy grip | `OVRInput.Get(OVRInput.Axis1D.PrimaryHandTrigger)` | `float` (0…1) |
| Przycisk A / B | `OVRInput.GetDown(OVRInput.Button.One)` / `Button.Two` | `bool` |
| Przycisk X / Y | `OVRInput.GetDown(OVRInput.Button.Three)` / `Button.Four` | `bool` |
| Wciśnięcie lewej gałki | `OVRInput.GetDown(OVRInput.Button.PrimaryThumbstick)` | `bool` |
| Wibracja | `OVRInput.SetControllerVibration(częstotliwość, siła, OVRInput.Controller.LTouch)` | — |

- `Get` — stan teraz (trzymany), `GetDown` — tylko w klatce naciśnięcia, `GetUp` — tylko w klatce puszczenia.
- Wywołuj w `Update`, nie w `FixedUpdate` (`GetDown` w `FixedUpdate` może „zgubić” naciśnięcie).

---

## Rozwiązywanie problemów

| Objaw | Przyczyna | Rozwiązanie |
|-------|-----------|-------------|
| Kula nie reaguje na gałkę | Brak OVR Manager w scenie lub nieaktualna konfiguracja | Sprawdź, czy Camera Rig jest w scenie i ma aktywny **OVR Manager** (krok 2.2); **Project Setup Tool → Fix All** (krok 1.1); zbuduj ponownie. |
| Gałka nie działa, a w goglach widać dłonie zamiast kontrolerów | Gogle przeszły na śledzenie dłoni | Weź kontrolery do rąk i poruszaj nimi albo naciśnij dowolny przycisk. |
| Aplikacja się uruchamia, ale nic nie działa / licznik się nie zmienia | `NullReferenceException` — niepodpięte pole w Inspectorze | Uzupełnij **Head**, **Count Text**, **Win Text** w komponencie Player Controller. |
| Licznik pokazuje `0/0` | Znajdźki nie mają tagu | Tag `PickUp` na każdej znajdźce (lub na prefabie). |
| Znajdźki nie znikają | Collider nie jest wyzwalaczem | **Box Collider → Is Trigger** na znajdźkach; kula musi mieć **Rigidbody**. |
| Kula przenika przez bandy | Za duża prędkość / zła detekcja kolizji | **Continuous Dynamic** na Rigidbody, zmniejsz `maxSpeed`. |
| Kula toczy się w złą stronę | Zły obiekt w polu Head | Pole **Head** = `CenterEyeAnchor`. |
| Kula drga / „klatkuje” | Brak interpolacji | Rigidbody → **Interpolate**, Fixed Timestep z kroku 1.2. |
| Kula stoi w miejscu, choć kontrolery są widoczne i śledzone | Kula oznaczona jako Static | Odznacz **Static** na `Player` (krok 4). |
| Stół za wysoko / za nisko | Zły punkt odniesienia | **Tracking Origin Type = Floor Level** (krok 2.2); w goglach ustaw granicę ponownie. |
| Stół jest za mną / z boku | Gogle niewycentrowane | Przytrzymaj przycisk Meta na prawym kontrolerze. |
| Gogli nie ma w **Run Device** | Kabel, port lub debugowanie USB | Inny kabel (z danymi) lub port, **Refresh**. Jeśli nie pomaga — zgłoś prowadzącemu (debugowanie USB). |
| Aplikacja w goglach jest płaskim oknem 2D | Konfiguracja XR | **Project Setup Tool → Fix All** (krok 1.1), zbuduj ponownie. |

---

## Zadania dodatkowe

Po ukończeniu podstawowej wersji wybierz jedno lub więcej:

1. **Reset gry przyciskiem** — w `Update` sprawdzaj `OVRInput.GetDown(OVRInput.Button.One)` (przycisk A). Po naciśnięciu: kula na start, wszystkie znajdźki z powrotem aktywne, licznik `0`, `WinText` ukryty.
2. **Dźwięk** — `AudioSource` na kuli; przy zebraniu znajdźki odtwórz krótki dźwięk (`PlayOneShot`). Ustaw **Spatial Blend = 1**, żeby dźwięk dochodził z miejsca kuli.
3. **Wibracja** — przy zebraniu znajdźki krótki impuls na lewym kontrolerze:

   ```csharp
   private System.Collections.IEnumerator Buzz()
   {
       OVRInput.SetControllerVibration(1f, 0.5f, OVRInput.Controller.LTouch);
       yield return new WaitForSeconds(0.1f);
       OVRInput.SetControllerVibration(0f, 0f, OVRInput.Controller.LTouch);
   }
   // w OnTriggerEnter: StartCoroutine(Buzz());
   ```

4. **Turbo** — gdy wciśnięty jest lewy spust (`Axis1D.PrimaryIndexTrigger`), zwiększ `acceleration` i `maxSpeed` proporcjonalnie do siły nacisku.
5. **Stoper** — mierz czas od startu do zebrania wszystkich znajdźek i pokaż go w `WinText`.
6. **Przeszkody** — dodaj w planszy kilka ścianek (dzieci `Board`, Static), tworząc prosty labirynt.
7. **Sterowanie dowolną ręką** — jeśli lewa gałka jest w spoczynku, odczytaj prawą (`Axis2D.SecondaryThumbstick`).
