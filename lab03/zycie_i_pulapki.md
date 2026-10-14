# Ćw #12 - życie gracza i pułapki: sprawdzanie skryptu bez budowania gry

> [!NOTE]
> **Cel:** dodać do kulki system życia, pułapki i apteczki, a potem **sprawdzić, czy to działa**, bez grania od początku i bez budowania na gogle. Użyjesz narzędzi z tego laboratorium: `[SerializeField]`, `[Range]`, `[ContextMenu]`, właściwości, `Mathf`, korutyn i `Debug.Log`.
>
> **Scena:** kopia sceny z Ćw #3 (kulka + płaszczyzna + `PlayerController`).
>
> **Czas:** 45 min.

---

## Po co to?

Żeby sprawdzić, co się dzieje, gdy kulka straci całe życie, nie trzeba za każdym razem turlać jej w pułapkę pięć razy. Dodasz do skryptu **przyciski testowe** w menu Inspektora i przejdziesz listę scenariuszy. To podstawowy sposób sprawdzania logiki w Unity. Przy grze VR ma to szczególne znaczenie, bo każdy build na gogle trwa kilka minut.

## Plan

| # | Krok | Czas |
|---|------|-----:|
| 1 | [Skrypt `PlayerHealth`](#1-skrypt-playerhealth) | 12 min |
| 2 | [Przyciski testowe `[ContextMenu]`](#2-przyciski-testowe-contextmenu) | 8 min |
| 3 | [Pułapka i apteczka](#3-pułapka-i-apteczka) | 10 min |
| 4 | [Nietykalność po trafieniu (korutyna)](#4-nietykalność-po-trafieniu-korutyna) | 7 min |
| 5 | [Protokół testów](#5-protokół-testów) | 8 min |

---

## 1. Skrypt `PlayerHealth`

1. Zduplikuj scenę z Ćw #3 (**Ctrl+D** na pliku sceny) i nazwij ją `Lab03_Health`.
2. Dodaj do kulki nowy skrypt `PlayerHealth`:

```csharp
using UnityEngine;

[RequireComponent(typeof(Renderer))]
public class PlayerHealth : MonoBehaviour
{
    [SerializeField, Range(1, 200)]
    [Tooltip("Maksymalna liczba punktów życia")]
    private int maxHealth = 100;

    [SerializeField]
    private int health;

    private Renderer rend;

    public bool IsDead => health <= 0;

    public int Health
    {
        get => health;
        private set
        {
            // TODO 1: ogranicz wartość do przedziału 0..maxHealth (Mathf.Clamp)
            health = value;

            UpdateColor();
            Debug.Log($"[{name}] HP: {health}/{maxHealth}");

            // TODO 2: jeśli gracz zginął, wywołaj Die()
        }
    }

    void Awake()
    {
        rend = GetComponent<Renderer>();
    }

    void Start()
    {
        Health = maxHealth;
    }

    public void TakeDamage(int amount)
    {
        if (IsDead) return;
        Health -= amount;
    }

    public void Heal(int amount)
    {
        // TODO 3: martwego gracza nie leczymy
        Health += amount;
    }

    private void UpdateColor()
    {
        // 1 = pełne życie (zielony), 0 = brak życia (czerwony)
        float t = (float)health / maxHealth;
        rend.material.color = Color.Lerp(Color.red, Color.green, t);
    }

    private void Die()
    {
        Debug.Log($"[{name}] GAME OVER");
        GetComponent<PlayerController>().enabled = false;   // koniec sterowania
        GetComponent<Rigidbody>().linearVelocity = Vector3.zero;
    }
}
```

3. Uzupełnij **TODO 1-3**.

> [!TIP]
> Pole `health` ma `[SerializeField]` tylko po to, żeby **w trakcie gry podglądać wartość w Inspektorze**. Zmienia się wyłącznie przez właściwość `Health`, więc każda zmiana przechodzi przez `Clamp`, kolor i log.

---

## 2. Przyciski testowe `[ContextMenu]`

Dopisz do `PlayerHealth`:

```csharp
    [ContextMenu("TEST / Obrażenia -10")]
    private void TestDamage10() => TakeDamage(10);

    [ContextMenu("TEST / Obrażenia -250")]
    private void TestDamage250() => TakeDamage(250);

    [ContextMenu("TEST / Leczenie +25")]
    private void TestHeal25() => Heal(25);

    [ContextMenu("TEST / Reset")]
    private void TestReset()
    {
        // TODO 4: przywróć pełne życie i włącz z powrotem PlayerController
    }
```

Uruchom **Play**, zaznacz kulkę i w Inspektorze kliknij **⋮** (albo PPM na nagłówku komponentu `PlayerHealth`). Na dole menu będą Twoje przyciski.

> [!IMPORTANT]
> Przyciski działają też **bez Play**, ale wtedy `Start()` jeszcze się nie wykonał, a zmiany koloru materiału zostają w scenie. Testuj **w trybie Play**.

---

## 3. Pułapka i apteczka

1. Dodaj na płaszczyznę płaski **Cube** (np. skala `2 × 0.1 × 2`), nazwij go `Spikes`, nadaj mu czerwony materiał i zaznacz **Is Trigger** w *Box Collider*.
2. Dodaj do niego skrypt `DamageZone`:

```csharp
using UnityEngine;

public class DamageZone : MonoBehaviour
{
    [SerializeField, Range(1, 100)] private int damage = 20;

    void OnTriggerEnter(Collider other)
    {
        // TODO 5: jeśli obiekt ma PlayerHealth - zadaj mu obrażenia
        // podpowiedź: other.TryGetComponent(out PlayerHealth hp)
    }

    void OnDrawGizmos()
    {
        // Strefa widoczna w oknie Scene nawet po wyłączeniu renderera
        Gizmos.color = new Color(1, 0, 0, 0.4f);
        Gizmos.matrix = transform.localToWorldMatrix;
        Gizmos.DrawCube(Vector3.zero, Vector3.one);
    }
}
```

3. **Samodzielnie** zrób zielony `Medkit` (np. kapsuła z triggerem) ze skryptem `HealZone`. Leczy o `[Range(1,100)] heal` i **znika po użyciu** (`Destroy(gameObject)`). Uwaga: apteczka nie powinna zniknąć, jeśli gracz jest martwy.

---

## 4. Nietykalność po trafieniu (korutyna)

Problem: kulka może podskakiwać na krawędzi pułapki i kilka razy pod rząd wchodzić w trigger, tracąc całe życie w ułamku sekundy.

Dodaj do `PlayerHealth`:

```csharp
    [SerializeField] private float invulnerableTime = 1f;
    private bool invulnerable;

    private System.Collections.IEnumerator InvulnerabilityRoutine()
    {
        invulnerable = true;
        // TODO 6: miganie - przez invulnerableTime co 0.1 s przełączaj rend.enabled
        yield return new WaitForSeconds(invulnerableTime);
        rend.enabled = true;
        invulnerable = false;
    }
```

Zmodyfikuj `TakeDamage`: jeśli `invulnerable`, nic nie rób. W przeciwnym razie zadaj obrażenia i uruchom `StartCoroutine(InvulnerabilityRoutine())`.

---

## 5. Protokół testów

Przejdź scenariusze w trybie **Play** i uzupełnij tabelę. Większość wykonasz przyciskami z kroku 2, bez sterowania kulką.

| # | Kroki | Oczekiwany wynik | Wynik |
|---|-------|------------------|:-----:|
| T1 | Start gry | HP 100/100, kulka zielona | |
| T2 | `Obrażenia -10` ×3 | HP 70, kolor przesunięty w stronę czerwieni | |
| T3 | `Obrażenia -250` | HP **0** (nie ujemne), `GAME OVER` **jeden raz**, kulka stoi | |
| T4 | Po T3: `Obrażenia -10` | brak zmian, brak drugiego `GAME OVER` | |
| T5 | Po T3: `Leczenie +25` | HP nadal 0 | |
| T6 | `Reset`, potem `Leczenie +25` | HP 100 (nie 125) | |
| T7 | Dwa razy `Obrażenia -10` w ciągu 1 s | odjęte tylko 10 HP, kulka miga | |
| T8 | W Inspektorze zmień `Max Health` suwakiem na 50 i kliknij `Reset` | HP 50/50 | |
| T9 | Wjedź kulką w `Spikes` | -20 HP, miganie | |
| T10 | Wjedź w `Medkit` przy 60 HP | HP 80, apteczka znika | |
| T11 | Wjedź w `Medkit` jako martwy (przesuń apteczkę pod kulkę w oknie Scene) | apteczka zostaje | |

> [!TIP]
> Konsola: włącz **Collapse**, żeby powtarzające się logi były zgrupowane z licznikiem. To najprostszy sposób, żeby zobaczyć, czy `GAME OVER` pojawił się raz, czy kilka razy (T3/T4).

**Jeśli któryś scenariusz nie przechodzi**, popraw skrypt i powtórz **wszystkie** scenariusze, nie tylko ten jeden. Poprawka jednego błędu potrafi zepsuć coś innego.

---


<details>
<summary><b>Typowe błędy</b></summary>

- **T3/T4:** brak `Clamp` → HP ujemne; `Die()` wywołane bez warunku „właśnie zginął” → kilka razy `GAME OVER`. Poprawnie: w setterze `bool wasDead = IsDead; ... if (!wasDead && IsDead) Die();`.
- **T5:** brak `if (IsDead) return;` w `Heal`.
- **T6:** brak `Clamp` od góry.
- **T7:** `StartCoroutine` uruchamiane, ale brak sprawdzania `invulnerable` na początku `TakeDamage`.
- **T8:** `Reset` ustawia `Health = 100` „na sztywno” zamiast `maxHealth`.
- **T11:** `HealZone` niszczy apteczkę bez sprawdzenia `IsDead` (warto dodać publiczną właściwość `IsDead` lub zwracać `bool` z `Heal`).
- **TODO 4:** po `Reset` trzeba ponownie włączyć `PlayerController`, inaczej kulka dalej stoi.
- Przy `TakeDamage(250)` w trakcie nietykalności test T3 „nie działa”. To dobra okazja, żeby pokazać, że kolejność testów ma znaczenie i że przed scenariuszem trzeba zrobić `Reset`.

</details>
