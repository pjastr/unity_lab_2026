# Wytyczne: tekst *Credits* w projekcie zaliczeniowym


---

## 1. Gdzie umieścić credits (wszystkie trzy miejsca)

1. **W grze:** osobny ekran *Credits* dostępny z menu głównego (i/lub napisy po ukończeniu gry). Musi dać się przewinąć i zamknąć.
2. **W repozytorium:** plik `CREDITS.md` w katalogu głównym, z pełnym rejestrem zasobów (tabela, pkt 8).
3. **Obok buildu:** plik `THIRD-PARTY-NOTICES.txt` z pełną treścią licencji, które tego wymagają (MIT, Apache 2.0, SIL OFL itd.). Na itch.io wpisz te same informacje w opisie strony gry.

> Płatnych assetów z Unity Asset Store **nie wrzucaj** do publicznego repozytorium (licencja zabrania udostępniania „surowych” plików). Dodaj je do `.gitignore` i wymień w `CREDITS.md`.

---

## 2. Pzykładowa struktura ekranu Credits

Kolejność sekcji:

1. **Tytuł gry, wersja i rok**
2. **Autorzy i role.** Imię i nazwisko **albo numer albumu** (do wyboru). Przy projekcie zespołowym podaj krótko rolę każdej osoby, np. „programowanie”, „level design, grafika 3D”, „UI, dźwięk”. Wystarczy jedna linijka na osobę, a szczegółowy podział pracy omawia się podczas prezentacji projektu.
3. **Kontekst akademicki.** Przedmiot, uczelnia/wydział, prowadzący, rok akademicki.
4. **Zasoby zewnętrzne**, pogrupowane według rodzaju:
   - Modele 3D / grafika 2D / tekstury
   - Animacje
   - Muzyka
   - Efekty dźwiękowe
   - Fonty
   - Kod, pakiety i biblioteki (w tym tutoriale, na których bazuje projekt)
5. **Narzędzia AI.** Czym, do czego, w jakim zakresie i kiedy (pkt 4 i 5 niżej).
6. **Silnik i narzędzia.** „Made with Unity” plus opcjonalnie Blender, GIMP, Audacity.
7. **Podziękowania** (opcjonalnie): testerzy, konsultanci.

---

## 3. Co musi zawierać każdy wpis o zasobie

Model **TASL** (wymagany przez licencje Creative Commons BY, stosuj go do wszystkich zasobów):

| Element | Przykład |
|---|---|
| **T**ytuł zasobu | „Nature Kit”, „Sneaky Snitch” |
| **A**utor (imię/nazwisko, pseudonim, studio) | Kenney, Kevin MacLeod, Unity Technologies |
| **S**ource – źródło (link do konkretnej strony zasobu, nie do strony głównej serwisu) | `https://kenney.nl/assets/nature-kit` |
| **L**icencja (nazwa + wersja, najlepiej z linkiem) | CC0 1.0, CC BY 4.0, Standard Unity Asset Store EULA |
| **+ Zmiany** (wymóg CC BY, ważne też dla oceny) | „przycięto do 30 s, zapętlono”, „zmieniono teksturę” |
| **+ Data korzystania** (pkt 5) | „pobrano: 2026-11-04”, „dostęp: 2026-10-20” |

**Zasady:**
- Pisz konkretnie: nie „modele z internetu”, tylko nazwa paczki, autor i link.
- **Kopiuj tekst atrybucji 1:1**, jeśli autor go podaje (np. Freesound, incompetech).
- Podawaj zasoby CC0 i domeny publicznej, choć licencja tego nie wymaga. Regulamin wymaga oznaczenia **wszystkich** cudzych elementów, a w Polsce autorskie prawa osobiste i tak pozostają.
- Tutorial, na którym bazuje projekt, **też jest zasobem zewnętrznym**. Podaj go i opisz, co dodałeś sam.
- Unikaj licencji **NC** przy publikacji na itch.io z opcją płatności lub donacji. Licencja **ND** zabrania jakichkolwiek modyfikacji (nawet przycięcia utworu).
- „Darmowe” ≠ „bez licencji”, a „dostępne w Google Grafika” ≠ „wolne”. Bez licencji nie używaj.

---

## 4. Deklaracja użycia AI

Regulamin ogranicza udział gotowców i AI do 50%, a od 2 sierpnia 2026 obowiązuje art. 50 AI Act (przejrzystość). Dlatego w credits podaj:

- **narzędzie** (nazwa i ewentualnie wersja),
- **do czego zostało użyte** (kod, grafika, tekst, dźwięk, tłumaczenie),
- **zakres** (które pliki/elementy) i **co zmieniłeś ręcznie**,
- **okres korzystania** (od–do, pkt 5).

Przykład:

```
Narzędzia AI
  GitHub Copilot – podpowiedzi kodu w EnemyPatrol.cs i SaveSystem.cs,
                   kod przejrzany i przepisany przez autora.
                   Okres: 2026-11-03 – 2026-12-15
  ChatGPT (OpenAI) – pomoc w redakcji tekstów dialogów (PL),
                     wszystkie teksty zredagowane ręcznie.
                     Okres: 2026-12-01 – 2026-12-05
  Nie użyto AI do generowania grafiki ani muzyki.
```

Jeśli w grze jest NPC sterowany modelem językowym, gracz musi o tym wiedzieć już w grze, a nie tylko z credits.

---

## 5. Daty korzystania ze źródeł i AI

Każdy wpis o zasobie zewnętrznym i każde narzędzie AI musi mieć **datę**:

| Co | Jaka data | Przykład |
|---|---|---|
| Asset pobrany z sieci (model, tekstura, dźwięk, muzyka, font, pakiet) | data pobrania | `pobrano: 2026-11-04` |
| Tutorial, dokumentacja, artykuł, film | data dostępu (pierwszego korzystania) | `dostęp: 2026-10-20` |
| Narzędzie AI | okres korzystania od–do (+ wersja/model, jeśli znana) | `GitHub Copilot, 2026-11-03 – 2026-12-15` |
| Zasób zaktualizowany w trakcie projektu | data ostatniej wersji | `aktualizacja: 2026-12-02 (v1.3)` |

**Zasady:**
- Format daty: `RRRR-MM-DD`.
- W `CREDITS.md` data jest **obowiązkowa przy każdej pozycji**. Na ekranie w grze można ją skrócić do miesiąca i roku (np. `11.2026`).
- Daty powinny zgadzać się z historią commitów w repozytorium, np. asset nie może być „pobrany” później niż commit, który go dodał.
- Daty mają znaczenie, bo licencje, regulaminy narzędzi AI i same modele zmieniają się w czasie. Liczą się warunki z dnia, w którym korzystałeś z zasobu, więc zachowaj zrzut strony z licencją z tego dnia.

---

## 6. Przykład 1 – ekran Credits w grze (projekt indywidualny, 3D)

Wszystkie wymienione zasoby i licencje są realne.

```
══════════════════════════════════════════════
              LATARNIK  v1.2  (2026)
══════════════════════════════════════════════

AUTOR
  Jan Kowalski – projekt, programowanie, grafika 3D, UI
  (zamiast imienia i nazwiska można podać: nr albumu 123456)

Projekt zaliczeniowy z przedmiotu
„Projektowanie gier w środowisku Unity”
Wydział Matematyki i Informatyki UWM w Olsztynie
Prowadzący: dr Piotr Jastrzębski · rok akad. 2026/2027

─── MODELE 3D I TEKSTURY ──────────────────────
  „Nature Kit” – Kenney (kenney.nl)
     Licencja: CC0 1.0 – drzewa i kamienie, zmienione kolory materiałów
     Pobrano: 11.2026
  Tekstury skał – Poly Haven (polyhaven.com)
     Licencja: CC0 1.0 · Pobrano: 11.2026

─── ANIMACJE ──────────────────────────────────
  Postać „Y Bot” i animacje „Walking”, „Idle” – Mixamo (Adobe)
     Na warunkach Adobe Mixamo (bez opłat licencyjnych, bez redystrybucji
     surowych plików) · Pobrano: 11.2026

─── MUZYKA ────────────────────────────────────
  „Sneaky Snitch” Kevin MacLeod (incompetech.com)
  Licensed under Creative Commons: By Attribution 4.0 License
  http://creativecommons.org/licenses/by/4.0/
     Zmiany: przycięcie i zapętlenie · Pobrano: 11.2026

─── EFEKTY DŹWIĘKOWE ──────────────────────────
  „Interface Sounds” – Kenney (kenney.nl) – CC0 1.0 · Pobrano: 11.2026
  [efekt z Freesound – tekst atrybucji skopiowany z jego strony, np.:]
  "<tytuł dźwięku>" by <nazwa użytkownika> – https://freesound.org/s/<ID>/
     – License: Attribution 4.0 · Pobrano: <MM.RRRR>

─── FONTY ─────────────────────────────────────
  „Press Start 2P” – CodeMan38 (Google Fonts)
     SIL Open Font License 1.1 – treść licencji w THIRD-PARTY-NOTICES.txt
     Pobrano: 11.2026

─── KOD I PAKIETY ─────────────────────────────
  Newtonsoft.Json 13.0.3 – James Newton-King – MIT License · 11.2026
  TextMesh Pro, Input System – Unity Technologies
     (Unity Companion License)
  Bazowy tutorial: „Roll-a-Ball” – Unity Learn
     (learn.unity.com/project/roll-a-ball) · Dostęp: 10.2026
     Z tutorialu pochodzi sterowanie kulą; własne: 3 poziomy, latarnia,
     system świateł, menu, zapis gry.

─── NARZĘDZIA AI ──────────────────────────────
  GitHub Copilot – podpowiedzi kodu (LightBeam.cs), kod zweryfikowany.
     Okres: 11.2026 – 12.2026
  Nie użyto AI do grafiki, muzyki ani tekstów.

─── NARZĘDZIA ─────────────────────────────────
  Made with Unity 6 · Blender · Audacity · GIMP

Gra przeznaczona dla graczy 12+ (orientacyjnie wg kryteriów PEGI).

Podziękowania: testerom z grupy laboratoryjnej 3.
══════════════════════════════════════════════
```

---

## 7. Przykład 2 – projekt zespołowy VR (3 osoby, Meta Quest)

Role wystarczy opisać krótko, a szczegóły omówić podczas prezentacji. Zamiast imion i nazwisk można podać numery albumów:

```
ZESPÓŁ
  nr albumu 123456 – programowanie, interakcje VR
  nr albumu 123789 – level design, modele 3D
  nr albumu 124001 – UI, dźwięk, testy na goglach

PAKIETY VR
  Meta XR Core SDK, Meta XR Interaction SDK – Meta Platforms
     Na warunkach Meta Platforms Technologies SDK License Agreement
     Pobrano: 10.2026
  Bazowy przykład: „Hello VR” – Meta Developers (developers.meta.com)
     Dostęp: 10.2026
```

> ⚠️ Assety ze **skryptami, fizyką lub animacjami** (np. *Starter Assets – Third Person* od Unity Technologies, Standard Unity Asset Store EULA) wolno użyć, ale trzeba je podać w credits. **Obniżają ocenę**, bo kryteria premiują projekty bez takich gotowców. Grafika i muzyka z zewnątrz nie obniżają oceny.

---

## 8. Przykład 3 – plik `CREDITS.md` w repozytorium (rejestr zasobów)

Wzór z wykładu 4b, rozszerzony o ścieżkę w projekcie i datę korzystania:

```markdown
# Credits – Latarnik

## Autorzy
| Osoba (imię i nazwisko lub nr albumu) | Rola |
|---|---|
| Jan Kowalski | projekt, programowanie, grafika 3D, UI |

## Rejestr zasobów zewnętrznych
| Zasób | Ścieżka w projekcie | Autor | Źródło | Licencja | Obowiązki | Zmiany | Data korzystania |
|---|---|---|---|---|---|---|---|
| Nature Kit | Assets/ThirdParty/Kenney/NatureKit | Kenney | https://kenney.nl/assets/nature-kit | CC0 1.0 | brak (podano z grzeczności) | kolory materiałów | 2026-11-04 |
| Y Bot + Walking, Idle | Assets/ThirdParty/Mixamo | Adobe Mixamo | https://www.mixamo.com | Adobe Mixamo terms | brak redystrybucji plików | – | 2026-11-06 |
| Sneaky Snitch | Assets/Audio/Music | Kevin MacLeod | https://incompetech.com | CC BY 4.0 | atrybucja w grze | przycięcie, pętla | 2026-11-10 |
| Press Start 2P | Assets/Fonts | CodeMan38 | https://fonts.google.com/specimen/Press+Start+2P | SIL OFL 1.1 | dołączyć licencję | konwersja do TMP SDF | 2026-11-10 |
| Newtonsoft.Json 13.0.3 | Packages | James Newton-King | https://www.newtonsoft.com/json | MIT | dołączyć copyright i licencję | – | 2026-11-12 |
| Roll-a-Ball (tutorial) | Assets/Scripts/PlayerController.cs | Unity Technologies | https://learn.unity.com/project/roll-a-ball | Unity Learn | wskazanie źródła | rozbudowa sterowania | dostęp: 2026-10-20 |

## Praca własna
- Skrypty: LightBeam.cs, ShipSpawner.cs, SaveSystem.cs, MenuController.cs, …
- Modele: latarnia, klify, łodzie (Blender)
- Poziomy 2–4, całe UI, splash screen

## AI
| Narzędzie | Do czego | Zakres | Okres korzystania |
|---|---|---|---|
| GitHub Copilot | podpowiedzi kodu | LightBeam.cs (zweryfikowane i przepisane) | 2026-11-03 – 2026-12-15 |

## Assety płatne (nie ma ich w repozytorium – .gitignore)
- brak
```

Dla każdego zasobu zachowaj **zrzut strony z licencją** z dnia pobrania (pkt 5).

---

## 9. Przykład 4 – `THIRD-PARTY-NOTICES.txt` (dołączany do buildu)

```
THIRD-PARTY NOTICES – Latarnik

== Newtonsoft.Json 13.0.3 ==
License: MIT
Copyright (c) 2007 James Newton-King
Permission is hereby granted, free of charge, to any person obtaining
a copy of this software ... [pełna treść licencji MIT]

== Press Start 2P ==
Copyright 2012 The Press Start 2P Project Authors (CodeMan38)
License: SIL Open Font License, Version 1.1
[pełna treść OFL 1.1 – plik OFL.txt z paczki fontu]

== Nature Kit – Kenney ==
License: CC0 1.0 (attribution not required, given as courtesy)

== "Sneaky Snitch" – Kevin MacLeod (incompetech.com) ==
License: CC BY 4.0 – https://creativecommons.org/licenses/by/4.0/
```

---

## 10. Najczęstsze błędy (do poprawy przed oddaniem)

| ❌ Źle | ✅ Dobrze |
|---|---|
| „Grafiki: internet”, „muzyka z YouTube” | tytuł, autor, link do strony zasobu, licencja |
| „Assety z Asset Store” | nazwa paczki, wydawca, Standard Unity Asset Store EULA |
| Pominięty tutorial bazowy | tutorial podany plus opis własnych zmian |
| Pominięte zasoby CC0 („bo nie trzeba”) | podane wszystkie cudze elementy (wymóg regulaminu) |
| Brak informacji o AI | deklaracja: narzędzie, zakres, własna weryfikacja |
| Zespół: „wszyscy robili wszystko” | krótka rola każdej osoby; szczegóły na prezentacji |
| Brak dat przy zasobach i AI | data pobrania/dostępu przy każdym zasobie, okres korzystania z AI |
| Credits tylko w README | ekran w grze + `CREDITS.md` + plik z licencjami przy buildzie |
| Muzyka komercyjna (np. z radia), postacie z cudzych marek | zasoby z jasną licencją lub własne; fikcyjne marki |
| Licencja NC/ND przy publikacji z płatnością lub przy przeróbkach | CC0 / CC BY / OFL / MIT |
| Font „desktop” osadzony w grze | font na SIL OFL (np. Google Fonts) |

---

## 11. Lista kontrolna przed oddaniem projektu

- [ ] Ekran **Credits** dostępny z menu głównego, czytelny i przewijany
- [ ] Autor/autorzy (imię i nazwisko **lub nr albumu**) z krótko opisanymi **rolami**; przedmiot, uczelnia, prowadzący, rok
- [ ] **Każdy** cudzy zasób (grafika, model, animacja, dźwięk, muzyka, font, kod, pakiet, tutorial) z tytułem, autorem, źródłem i licencją
- [ ] Zaznaczone **zmiany** w cudzych zasobach
- [ ] **Daty** pobrania/dostępu przy każdym zasobie i **okres korzystania** z każdego narzędzia AI
- [ ] Tekst atrybucji skopiowany 1:1 tam, gdzie autor go podaje
- [ ] **Deklaracja AI** (lub informacja, że AI nie użyto)
- [ ] `CREDITS.md` z rejestrem zasobów w repozytorium
- [ ] `THIRD-PARTY-NOTICES.txt` z pełnymi licencjami (MIT, OFL…) obok buildu
- [ ] Płatne assety poza publicznym repozytorium (`.gitignore`)
- [ ] Informacja o **przeznaczeniu wiekowym** i **splash image**
- [ ] Gotowce + AI ≤ 50% projektu; jesteś w stanie wyjaśnić każdy fragment kodu na „odpytce”
- [ ] Przy publikacji na itch.io te same credits w opisie strony gry
