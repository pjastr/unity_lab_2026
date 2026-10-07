# ProBuilder w Unity – akcje: opis i polskie tłumaczenia

*Materiały częściowo wygenerowane za pomocą Claude.*

> Notatka opracowana na podstawie dokumentacji **ProBuilder 6.1** ([Action reference](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/ref_actions.html) oraz podstron poszczególnych akcji).
>
> **Ważne o tłumaczeniach:** ProBuilder nie ma polskiej wersji językowej, więc w interfejsie zawsze zobaczysz nazwy angielskie. Polskie nazwy w tej notatce to tłumaczenia robocze. Przy każdej akcji podaję oryginał, proponowany polski odpowiednik i krótkie wyjaśnienie, skąd się wziął. Pomaga to zapamiętać, co dana akcja robi.

---

## 1. Czym jest ProBuilder

ProBuilder to pakiet Unity (`com.unity.probuilder`). Służy do budowania, edytowania i teksturowania własnej geometrii 3D bezpośrednio w edytorze Unity. Używa się go głównie do:

- projektowania poziomów (*level design*) i szybkiego szkicowania przestrzeni (*blockout*),
- prototypowania i testów rozgrywki,
- tworzenia uproszczonych siatek kolizji.

Ma też API, które pozwala pisać własne narzędzia.

---

## 2. Słowniczek podstawowych pojęć

| Angielski | Polski | Wyjaśnienie |
|---|---|---|
| **Mesh** | siatka | Zbiór wierzchołków, krawędzi i ścian tworzących model. W polskim gamedevie często mówi się po prostu „mesh”. |
| **Vertex** (l.mn. *vertices*) | wierzchołek | Punkt w przestrzeni, najmniejszy element siatki. |
| **Edge** | krawędź | Odcinek łączący dwa wierzchołki. |
| **Face** | ściana (poligon) | Wielokąt rozpięty na krawędziach. **Nie „twarz”**: to częsty błąd tłumaczeń maszynowych. *Face* to tu ściana bryły, tak jak „ściana sześcianu” w geometrii. |
| **Quad** | czworokąt | Ściana o czterech bokach. Wiele akcji (pętle, pierścienie, *Flip Face Edge*) działa tylko na czworokątach. |
| **Triangle / tri** | trójkąt | Najprostsza ściana. Karta graficzna i tak renderuje wszystko jako trójkąty. |
| **Normal** | normalna (wektor normalny) | Wektor prostopadły do ściany, wskazujący jej „przód”. Ściana jest zwykle widoczna tylko od strony, w którą wskazuje normalna. |
| **Pivot** | punkt obrotu, punkt odniesienia (pivot) | Punkt, wokół którego obiekt się obraca i skaluje. Jego położenie pokazuje komponent Transform. |
| **Transform** | transformacja | Pozycja, obrót i skala obiektu. |
| **Submesh** | podsiatka | Wydzielona część siatki w tym samym obiekcie, zwykle z osobnym materiałem. |
| **Edge loop** | pętla krawędzi | Ciąg krawędzi połączonych „koniec do końca”, zwykle okrążający obiekt i wracający do punktu startu. |
| **Edge ring** | pierścień krawędzi | Ciąg „równoległych” krawędzi leżących na kolejnych ścianach. Krawędzie nie stykają się ze sobą, jak szczeble drabiny. |
| **Open edge** | krawędź otwarta (brzegowa) | Krawędź, która ma ścianę tylko po jednej stronie. Kilka takich krawędzi tworzy dziurę (*hole*). |
| **Non-manifold** | geometria nierozmaitościowa | Siatka niebędąca „szczelną” powierzchnią, np. z krawędzią wspólną dla więcej niż dwóch ścian. *Manifold* to w topologii „rozmaitość”. |
| **UV** | współrzędne UV | Współrzędne tekstury na siatce. Litery U i V oznaczają osie tekstury, bo X, Y i Z są już zajęte przez przestrzeń 3D. |
| **Lightmap** | mapa światła (lightmapa) | Tekstura z „wypalonym”, czyli wcześniej obliczonym, oświetleniem. |
| **Collider** | collider (bryła kolizji) | Komponent fizyki określający kształt używany do wykrywania kolizji. |
| **Trigger** | trigger (wyzwalacz) | Collider, przez który da się przejść. Zamiast zderzenia wywołuje zdarzenia, np. `OnTriggerEnter`. |
| **Handle** | uchwyt (manipulator) | Strzałki i okręgi w Scene view służące do przesuwania, obracania i skalowania. |
| **T-junction** | połączenie typu T | Wierzchołek leżący na krawędzi sąsiedniej ściany, ale z nią niepołączony. Może powodować szczeliny i artefakty. |

---

## 3. Narzędzia a akcje, konteksty i tryby edycji

### Narzędzia (*Tools*) a akcje (*Actions*)

- **Tools** (narzędzia) działają w osobnym trybie, aktywnym aż do jego zamknięcia, np. *New Shape*, *Poly Shape*, *Cut*.
- **Actions** (akcje) wykonują się **od razu** na bieżącym zaznaczeniu. Ich ustawienia otwierają się w osobnym panelu, który nie blokuje pracy. To, które akcje są dostępne, zależy od trybu edycji i od tego, co jest zaznaczone.

### Konteksty i tryby edycji (*Edit modes*)

| Angielski | Polski | Do czego służy |
|---|---|---|
| **GameObject context** | kontekst obiektu | Tryb domyślny, praca na całych obiektach. W tabelach poniżej oznaczony jako **O** (*Object*). |
| **ProBuilder context** | kontekst ProBuildera | Praca na elementach siatki. Dostępny tylko dla obiektów z komponentem ProBuilder. |
| **Vertex Selection** (**V**) | tryb wierzchołków | Precyzyjna edycja, np. dzielenie i łączenie wierzchołków. |
| **Edge Selection** (**E**) | tryb krawędzi | Edycja średnio złożona, modelowanie pętlami krawędzi. |
| **Face Selection** (**F**) | tryb ścian | Podstawowe operacje: przesuwanie, wyciąganie, usuwanie. |

**Skróty ogólne:**

- **G** przełącza po kolei tryby edycji.
- **Esc** wraca do kontekstu obiektu.
- **`** (backtick) otwiera menu overlayów w Scene view.

---

## 4. Gdzie znaleźć akcje

- **Menu kontekstowe:** kliknij prawym przyciskiem myszy na obiekt (macOS: Ctrl + klik). W kontekście obiektu pojawiają się akcje dla całej siatki, w kontekście ProBuildera akcje pasujące do zaznaczenia i trybu.
- **Menu `Tools > ProBuilder`:** zawiera te same akcje, w podmenu m.in. *Object*, *Geometry*, *Selection*, *Interaction* i *Export*.
- **Overlaye w Scene view:**
  - *Tools* przełącza kontekst i uruchamia narzędzia,
  - *Tool Settings* pozwala wybrać tryb edycji i ustawić część przełączników,
  - *Tool Options* zawiera ustawienia konkretnej akcji.
- **Zmiana skrótów:** `Edit > Shortcuts` (macOS: `Unity > Shortcuts`), w wyszukiwarce wpisz „ProBuilder”.

---

## 5. Tabela zbiorcza akcji

Legenda: **O** to obiekt, **V** wierzchołki, **E** krawędzie, **F** ściany. Znak ✔ oznacza, że akcja jest dostępna w danym trybie.

| Akcja (EN) | Tłumaczenie (PL) | O | V | E | F |
|---|---|:-:|:-:|:-:|:-:|
| Bevel (Edges) | fazowanie krawędzi | | | ✔ | |
| Bevel (Faces) | fazowanie ścian | | | | ✔ |
| Bridge Edges | zmostkowanie krawędzi | | | ✔ | |
| Center Pivot | wyśrodkowanie pivota | ✔ | | | |
| Collapse Vertices | ściągnięcie wierzchołków do punktu | | ✔ | | |
| Conform Normals (Faces) | ujednolicenie normalnych ścian | | | | ✔ |
| Conform Normals (Objects) | ujednolicenie normalnych obiektu | ✔ | | | |
| Connect Edges | połączenie krawędzi | | | ✔ | |
| Connect Vertices | połączenie wierzchołków | | ✔ | | |
| Delete Faces | usunięcie ścian | | | | ✔ |
| Detach Faces | odłączenie ścian | | | | ✔ |
| Duplicate Faces | powielenie ścian | | | | ✔ |
| Export | eksport | ✔ | | | |
| Extrude Edges | wyciągnięcie krawędzi | | | ✔ | |
| Extrude Faces | wyciągnięcie ścian | | | | ✔ |
| Fill Hole (Edges) | wypełnienie dziury | | | ✔ | |
| Fill Hole (Vertices) | wypełnienie dziury | | ✔ | | |
| Flip Face Edge (Turn Edges) | odwrócenie krawędzi ściany | | | | ✔ |
| Flip Face Normals | odwrócenie normalnych ścian | | | | ✔ |
| Flip Normals | odwrócenie normalnych obiektu | ✔ | | | |
| Freeze Transform | zamrożenie transformacji | ✔ | | | |
| Grow Selection | powiększenie zaznaczenia | | ✔ | ✔ | ✔ |
| Insert Edge Loop | wstawienie pętli krawędzi | | | ✔\* | |
| Inset | wstawka (wcięcie ściany) | | | | ✔ |
| Lightmap UVs | UV dla mapy światła | ✔ | | | |
| Merge Faces | scalenie ścian | | | | ✔ |
| Merge Objects | scalenie obiektów | ✔ | | | |
| Mirror Objects | lustrzane odbicie obiektów | ✔ | | | |
| Offset Elements | przesunięcie elementów o wartość | | ✔ | ✔ | ✔ |
| Orientation (Handle) | orientacja uchwytów | ✔ | ✔ | ✔ | ✔ |
| ProBuilderize | konwersja do ProBuildera | ✔ | | | |
| Rect | tryb prostokąta zaznaczania | | | ✔ | ✔ |
| Select by Colors | zaznaczenie wg koloru wierzchołków | | ✔ | ✔ | ✔ |
| Select by Material | zaznaczenie wg materiału | | | | ✔ |
| Select Edge Loop | zaznaczenie pętli krawędzi | | | ✔ | |
| Select Edge Ring | zaznaczenie pierścienia krawędzi | | | ✔ | |
| Select Face Loop | zaznaczenie pętli ścian | | | | ✔ |
| Select Face Ring | zaznaczenie pierścienia ścian | | | | ✔ |
| Select Hidden | zaznaczanie ukrytych elementów | ✔ | ✔ | ✔ | ✔ |
| Select Holes | zaznaczenie dziur | | ✔ | ✔ | |
| Select Path | zaznaczenie ścieżki | | | | ✔ |
| Set Collider | ustaw jako collider | ✔ | | | |
| Set Pivot | ustawienie pivota | | ✔ | ✔ | ✔ |
| Set Trigger | ustaw jako trigger | ✔ | | | |
| Shift | tryb klawisza Shift | | ✔ | ✔ | ✔ |
| Shrink Selection | zmniejszenie zaznaczenia | | ✔ | ✔ | ✔ |
| Split Vertices | rozdzielenie wierzchołków | | ✔ | | |
| Subdivide Edges | podział krawędzi | | | ✔ | |
| Subdivide Faces | podział ścian | | | | ✔ |
| Subdivide Object | podział (zagęszczenie) obiektu | ✔ | | | |
| Triangulate (Object) | triangulacja obiektu | ✔ | | | |
| Triangulate Faces | triangulacja ścian | | | | ✔ |
| Weld Vertices | zespawanie wierzchołków | | ✔ | | |

\* W tabeli w dokumentacji przy *Insert Edge Loop* nie zaznaczono żadnego trybu. Ta akcja działa jednak na zaznaczonych krawędziach i jest opisana razem z akcjami krawędzi.

---

## 6. Akcje w trybie obiektu (Object)

### Center Pivot – wyśrodkuj pivot

> **Tłumaczenie:** *center* to „wyśrodkuj”, a *pivot* to „punkt obrotu”. Słowo „pivot” zwykle zostawia się bez tłumaczenia.

- **Co robi:** przenosi pivot siatki na środek prostopadłościanu otaczającego obiekt (*bounds*). Przydaje się, gdy większość wierzchołków leży po jednej stronie, przez co obiekt obraca się wokół punktu poza bryłą.
- **Kilka obiektów:** każdy dostaje pivot na środku **własnej** bryły.
- **Menu:** `Tools > ProBuilder > Object > Center Pivot`

### Conform Normals (Objects) – ujednolić normalne obiektu

> **Tłumaczenie:** *to conform* znaczy „dostosować się, podporządkować się”. Normalne „podporządkowują się” większości, dlatego najlepiej oddaje to słowo „ujednolić”.

- **Co robi:** ustawia normalne wszystkich ścian obiektu w tym samym kierunku względnym. Wybiera kierunek, w który już teraz wskazuje większość ścian.
- **Menu:** `Tools > ProBuilder > Object > Conform Object Normals`

### Export – eksport

> **Tłumaczenie:** dosłowne, „eksportuj”.

- **Co robi:** zapisuje zaznaczone obiekty ProBuildera do pliku modelu 3D. Format wybierasz z listy *Export Format*.
- **Opcje wspólne:** *Include Children* (uwzględnij dzieci) eksportuje też obiekty podrzędne.
- **Asset / Prefab:** *Replace Source* (zastąp źródło). Gdy jest włączona, `MeshFilter.sharedMesh` wskazuje na wyeksportowaną siatkę. Gdy jest wyłączona, wskazuje na oryginał.
- **STL:** *STL Format*, do wyboru ASCII (tekstowy) lub Binary (binarny).
- **PLY i OBJ:**
  - *Export As Group* (eksportuj jako grupę) zapisuje wszystkie obiekty w jednym pliku zamiast osobnych plików.
  - *Apply Transforms* (zastosuj transformacje) przed eksportem przenosi transformację obiektu na siatkę.
  - *Right Handed* (prawoskrętny układ współrzędnych): Unity używa układu lewoskrętnego, a większość programów 3D prawoskrętnego.
  - *Quads* (PLY) i *Export Quads* (OBJ) zachowują czworokąty tam, gdzie się da.
- **Tylko OBJ:**
  - *Copy Textures* (kopiuj tekstury) kopiuje tekstury obok pliku i używa ścieżek lokalnych. Gdy opcja jest wyłączona, ścieżki są bezwzględne.
  - *Vertex Colors* (kolory wierzchołków) zapisuje je w nieoficjalnym rozszerzeniu formatu MeshLab.
  - *Texture Offset, Scale* (przesunięcie i skala tekstury) zapisuje je w pliku `.mtl`. Niektóre programy, np. Blender, je wczytują.
- ⚠️ *Vertex Colors* oraz *Texture Offset, Scale* mogą zepsuć import w niektórych programach.
- 💡 Gdy włączysz razem *Apply Transforms* i *Export As Group*, możesz wyeksportować całą scenę, edytować ją w innym programie i zaimportować z powrotem z zachowaniem pozycji obiektów.

### Flip Normals – odwróć normalne (obiektu)

> **Tłumaczenie:** *to flip* znaczy „przerzucić, odwrócić na drugą stronę”, jak naleśnik na patelni.

- **Co robi:** odwraca normalne **wszystkich** ścian zaznaczonych obiektów. Przydaje się zwłaszcza do zamiany bryły oglądanej z zewnątrz we wnętrze, np. sześcianu w pokój.
- **Menu:** `Tools > ProBuilder > Object > Flip Object Normals`

### Freeze Transform – zamroź transformację

> **Tłumaczenie:** *freeze* znaczy „zamrozić”. Nazwa pochodzi z programu Maya (*Freeze Transformations*). Bieżący stan zostaje „zamrożony” w geometrii. W Blenderze ta sama operacja nazywa się *Apply*, czyli „zastosuj”.

- **Co robi:** kasuje wartości Transform i przenosi pivot do początku układu współrzędnych świata (0, 0, 0). Wierzchołki zostają na swoich miejscach, więc rozmiar, kształt i położenie obiektu na scenie się nie zmieniają.
- **Menu:** `Tools > ProBuilder > Object > Freeze Transform`

### Lightmap UVs – UV dla mapy światła

> **Tłumaczenie:** *lightmap* to „mapa światła”, czyli tekstura z wypalonym oświetleniem. *UVs* to współrzędne tekstury. Razem: „rozkład UV dla lightmapy”.

- **Co robi:** ręcznie generuje UV do lightmap dla **wszystkich siatek we wszystkich otwartych scenach**, które ich jeszcze nie mają.
- 💡 Ustawienia lightmapy dla pojedynczego obiektu zmienisz w komponencie *ProBuilderMesh*. Domyślne wartości są w `Preferences > ProBuilder > Auto UVs`.

### Merge Objects – scal obiekty

> **Tłumaczenie:** *to merge* znaczy „scalić, połączyć w jedno”.

- **Co robi:** łączy dwa lub więcej zaznaczonych obiektów ProBuildera w jeden. Przykład z dokumentacji: butelka i nakrętka.
- ⚠️ Scalanie obiektów, które się przenikają, może dać nakładające się UV.
- **Menu:** `Tools > ProBuilder > Object > Merge Objects`

### Mirror Objects – odbicie lustrzane obiektów

> **Tłumaczenie:** *mirror* znaczy „lustro”, a *to mirror* „odbić lustrzanie”.

- **Co robi:** tworzy lustrzane odbicia zaznaczonych obiektów. Przydaje się przy elementach symetrycznych. Po ustawieniu opcji kliknij **Mirror**.
- **Opcje:**
  - *X / Y / Z* to osie odbicia. Można wybrać kilka naraz.
  - *Duplicate* (powiel) odbija kopię i zostawia oryginał bez zmian.
- 💡 Wymodeluj połowę, odbij ją, a potem połącz obie połówki (np. *Merge Objects*, a następnie *Weld Vertices*). Dostaniesz idealnie symetryczny model.
- **Menu:** `Tools > ProBuilder > Object > Mirror Objects`

### ProBuilderize – konwertuj do ProBuildera

> **Tłumaczenie:** to neologizm, czyli nazwa marki z angielską końcówką *-ize* (po polsku „-izować”). Dosłownie byłoby „ProBuilderyzuj”, opisowo „przekształć w obiekt ProBuildera”.

- **Co robi:** zamienia zwykły GameObject z *Mesh Filter* i *Mesh Renderer*, np. zaimportowany model, w edytowalny obiekt ProBuildera.
- **Opcje:**
  - *Import Quads* (importuj czworokąty) buduje ściany z czworokątów. Opcja jest domyślnie włączona; po wyłączeniu ściany będą trójkątami.
  - *Import Smoothing* (importuj wygładzanie) wygładza przejścia między ścianami.
  - *Smoothing Threshold* (próg wygładzania) przyjmuje wartości od 0.0001 do 45° i decyduje, które sąsiednie ściany trafią do jednej grupy wygładzania. 💡 Ustaw próg wyższy niż kąt między ścianami, które chcesz wygładzić razem.
- ⚠️ Oryginalny model składa się z trójkątów, więc czworokąty mogą nie odpowiadać jego topologii. Skutkiem bywają artefakty i deformacje. Wtedy wyłącz *Import Quads*.
- **Menu:** `Tools > ProBuilder > Object > Pro Builderize` albo prawy przycisk myszy, a potem `MeshFilter > ProBuilderize`.

### Set Collider – ustaw jako collider

> **Tłumaczenie:** *to set* znaczy „ustawić”, *collider* to „bryła kolizji”. Chodzi o to, by obiekt stał się niewidzialną przeszkodą.

- **Co robi:**
  - dodaje *MeshCollider*, jeśli obiekt nie ma collidera,
  - przypisuje materiał *Collider* z ProBuildera,
  - wyłącza *Mesh Renderer* w trybie Play i w buildzie, przez co obiekt jest niewidoczny, ale blokuje ruch.
- **Menu:** `Tools > ProBuilder > Object > Set Collider`

### Set Trigger – ustaw jako trigger

> **Tłumaczenie:** *trigger* to „spust, wyzwalacz”: strefa, której przekroczenie coś uruchamia, np. otwarcie drzwi albo start cutscenki.

- **Co robi:**
  - dodaje *MeshCollider*, jeśli go brak, i włącza w nim *Convex*,
  - zaznacza *Is Trigger*,
  - przypisuje materiał *Trigger*,
  - wyłącza *Mesh Renderer* w trybie Play i w buildzie.
- **Skrót:** **T** (według dokumentacji).
- **Menu:** `Tools > ProBuilder > Object > Set Trigger`

### Subdivide Object – podziel (zagęść) obiekt

> **Tłumaczenie:** *to subdivide* znaczy „podzielić na mniejsze części”. W modelowaniu mówi się też o „zagęszczaniu siatki”.

- **Co robi:** dzieli **każdą** ścianę obiektu. Na środku każdej krawędzi dodaje wierzchołek i łączy te wierzchołki w środku ściany, więc z jednego czworokąta powstają cztery. Daje to więcej geometrii do dalszej obróbki.
- **Menu:** `Tools > ProBuilder > Object > Subdivide Object`

### Triangulate (Object) – trianguluj obiekt

> **Tłumaczenie:** „triangulacja” to termin z matematyki i geodezji oznaczający podział figury na trójkąty.

- **Co robi:** rozbija wszystkie wielokąty obiektu na trójkąty. Obiekt wygląda wtedy ostro i fasetkowo.
- 💡 Twarde krawędzie złagodzisz, dodając ściany do grup wygładzania (*smoothing groups*).
- **Menu:** `Tools > ProBuilder > Object > Triangulate`

---

## 7. Akcje wspólne dla wierzchołków, krawędzi i ścian

### Set Pivot – ustaw pivot (V / E / F)

> **Tłumaczenie:** „ustaw punkt obrotu”.

- **Co robi:** przenosi pivot siatki do **średniego środka zaznaczonych elementów**, czyli wierzchołków, krawędzi lub ścian. Pivot zostaje w nowym miejscu także po powrocie do trybu obiektu.
- **Przykład z dokumentacji:** zaznacz krawędź drzwi po stronie zawiasów i ustaw tam pivot. Drzwi będą się wtedy obracać jak prawdziwe.
- **Skrót:** **Ctrl/Cmd + J**
- **Menu:** `Tools > ProBuilder > Geometry > Set Pivot`

### Fill Hole – wypełnij dziurę (V / E)

> **Tłumaczenie:** *to fill* znaczy „wypełnić”, *hole* to „dziura, otwór w siatce”.

- **Co robi:** tworzy nową ścianę zamykającą dziurę, której dotykają zaznaczone wierzchołki lub krawędzie.
- **Opcja:** *Fill Entire Hole* (wypełnij całą dziurę), domyślnie **włączona**.
  - Gdy jest włączona, zamyka cały otwór.
  - Gdy jest wyłączona, buduje ścianę tylko między zaznaczonymi otwartymi elementami. Na przykład trzy wierzchołki brakującego czworokąta dają trójkąt zakrywający połowę dziury.
- **Menu:** `Tools > ProBuilder > Geometry > Fill Hole`

### Offset Elements – przesuń elementy o wartość (V / E / F)

> **Tłumaczenie:** *offset* znaczy „przesunięcie, odsunięcie o określoną wartość”. Na pasku akcja nazywa się zależnie od trybu *Offset Vertices*, *Offset Edges* albo *Offset Faces*.

- **Co robi:** przesuwa zaznaczone elementy o dokładnie podaną odległość, bez przeciągania myszą.
- **Opcje:**
  - *Coordinate space* (układ współrzędnych):
    - *World* (świat) – domyślny,
    - *Local* (lokalny, względem obiektu),
    - *Element* (względem samego elementu),
    - *Handle* (względem uchwytu).
  - *Translate* (przesunięcie) to wartość dla każdej osi. Domyślnie X = 0, Y = 1, Z = 0.
- **Menu:** `Tools > ProBuilder > Geometry > Offset Elements`

---

## 8. Akcje wierzchołków (Vertex)

### Collapse Vertices – ściągnij wierzchołki do punktu

> **Tłumaczenie:** *to collapse* znaczy „zapaść się, złożyć się”. Wszystkie zaznaczone wierzchołki „zapadają się” w jeden punkt. Po polsku: „ściągnij” albo „zwiń do punktu”.

- **Co robi:** łączy wszystkie zaznaczone wierzchołki w jeden, **niezależnie od odległości między nimi**. Przykład z dokumentacji: z sześcianu powstaje ostrosłup.
- **Opcja:** *Collapse To First* (do pierwszego), domyślnie wyłączona.
  - Gdy jest włączona, punkt ląduje w miejscu pierwszego zaznaczonego wierzchołka.
  - Gdy jest wyłączona, ląduje w środku wszystkich zaznaczonych wierzchołków.
- **Skrót:** **Alt/Opt + C**
- **Menu:** `Tools > ProBuilder > Geometry > Collapse Vertices`

### Connect Vertices – połącz wierzchołki

> **Tłumaczenie:** dosłowne, „połącz”. Chodzi o połączenie wierzchołków nową krawędzią.

- **Co robi:** tworzy krawędź między zaznaczonymi wierzchołkami. Przy więcej niż dwóch wierzchołkach dodaje tyle krawędzi, ile trzeba, a w razie potrzeby także nowe wierzchołki, żeby geometria była poprawna. Przykład: łącząc trzy wierzchołki czworokąta, dostaniesz dodatkowy wierzchołek w środku.
- Można łączyć wierzchołki przez kilka ścian, jeśli te ściany mają wspólny zaznaczony wierzchołek.
- **Skrót:** **Alt/Opt + E** (*Smart Connect*, „inteligentne łączenie”, czyli jedna komenda, która dopasowuje się do trybu).
- **Menu:** *Smart Connect*. Dokumentacja podaje ścieżkę `Tools > ProBuilder > Selection > Smart Connect`.

### Split Vertices – rozdziel wierzchołki

> **Tłumaczenie:** *to split* znaczy „rozszczepić, rozdzielić”.

- **Co robi:** rozbija wierzchołek na osobne wierzchołki, po jednym dla każdej przylegającej ściany. Ściany można potem przesuwać niezależnie. Przykład: z jednego wierzchołka powstają cztery.
- Nowe wierzchołki zostają **w tym samym miejscu**. Na ilustracji w dokumentacji rozsunięto je tylko po to, by było widać efekt.
- **Skrót:** **Alt/Opt + X**
- **Menu:** `Tools > ProBuilder > Geometry > Split Vertices`

### Weld Vertices – zespawaj wierzchołki

> **Tłumaczenie:** *to weld* znaczy „spawać”. Tak jak spaw łączy dwa kawałki metalu, ta akcja łączy w jeden wierzchołki leżące blisko siebie. W Blenderze odpowiednikiem jest *Merge by Distance*.

- **Co robi:** scala zaznaczone wierzchołki, które leżą w zadanej odległości od siebie. **Różnica względem *Collapse*:** *Weld* łączy tylko wierzchołki bliskie, a *Collapse* wszystkie zaznaczone.
- **Opcja:** *Weld Distance* (odległość spawania), domyślnie **0.001**. Niska wartość łączy tylko wierzchołki, które niemal się pokrywają. Wyższa obejmuje więcej wierzchołków.
- 💡 Przy niskim progu możesz spokojnie zaznaczyć więcej wierzchołków, niż trzeba. Zespawają się tylko te bliskie.
- **Skrót:** **Alt/Opt + V**
- **Menu:** `Tools > ProBuilder > Geometry > Weld Vertices`

---

## 9. Akcje krawędzi (Edge)

### Bevel (Edges) – fazuj krawędzie

> **Tłumaczenie:** *bevel* znaczy „faza, skos”. To termin ze stolarstwa i obróbki metalu, gdzie „fazowanie” oznacza ścięcie ostrej krawędzi pod kątem. Tak samo mówi się w CAD i programach 3D. Nie myl z zaokrągleniem (*fillet*).

- **Co robi:** dzieli każdą zaznaczoną krawędź na dwie i wstawia między nimi nową ścianę. Ostra krawędź staje się ścięta.
- **Opcja:** *Distance* (odległość) określa, jak daleko nowe krawędzie odsuwają się od oryginału, czyli szerokość fazy.
- **Menu:** `Tools > ProBuilder > Geometry > Bevel Edges`

### Bridge Edges – zmostkuj krawędzie

> **Tłumaczenie:** *bridge* znaczy „most”. Nowa ściana zostaje „przerzucona” między dwiema krawędziami jak most między brzegami rzeki. Po polsku: „mostkowanie”.

- **Co robi:** tworzy nową ścianę między dwiema zaznaczonymi krawędziami, np. łączy dwie osobne płaszczyzny.
- Domyślnie działa tylko na **krawędziach otwartych**. Ograniczenie znosi preferencja *Allow non-manifold actions* (pozwól na operacje nierozmaitościowe).
- **Skrót:** **Alt/Opt + B**
- **Menu:** `Tools > ProBuilder > Geometry > Bridge Edges`

### Connect Edges – połącz krawędzie

> **Tłumaczenie:** dosłowne, „połącz”.

- **Co robi:** wstawia nową krawędź łączącą środki zaznaczonych krawędzi. Przy więcej niż dwóch krawędziach tworzy ich tyle, ile się da bez psucia geometrii. Działa przez kilka ścian, jeśli mają wspólną zaznaczoną krawędź.
- **Skrót:** **Alt/Opt + E** (*Smart Connect*)
- **Menu:** *Smart Connect*. Dokumentacja podaje ścieżkę `Tools > ProBuilder > Selection > Smart Connect`.

### Extrude Edges – wyciągnij krawędzie

> **Tłumaczenie:** *to extrude* pochodzi od łacińskiego *extrudere*, „wypychać”. W przemyśle to „ekstruzja” albo „wytłaczanie”: przeciskanie materiału przez matrycę, jak pasty z tubki. W programach 3D mówi się „wyciągnięcie” (w CAD „wyciągnięcie proste”), a potocznie „ekstrudowanie”.

- **Co robi:** z każdej zaznaczonej krawędzi „wypycha” nową krawędź i łączy je nową ścianą. Nowe ściany biegną zgodnie z normalną ściany przylegającej do krawędzi.
- Domyślnie działa tylko na **krawędziach otwartych**, chyba że włączysz *Allow non-manifold actions*.
- **Opcje** (działają tylko przy użyciu przycisku lub skrótu):
  - *As Group* (jako grupa) – przy kilku krawędziach boki wyciągniętych krawędzi pozostają połączone.
  - *Distance* (odległość), domyślnie **0.5**. Można podać wartość ujemną.
- 💡 **Szybciej:** przytrzymaj **Shift** podczas przesuwania, obracania lub skalowania krawędzi. Ten sposób ignoruje opcje, ale daje pełną kontrolę nad kierunkiem.
- **Skrót:** **Ctrl/Cmd + E**
- **Menu:** `Tools > ProBuilder > Geometry > Extrude`

### Insert Edge Loop – wstaw pętlę krawędzi

> **Tłumaczenie:** *to insert* znaczy „wstawić”, *loop* to „pętla”. Nowa krawędź „obiega” obiekt dookoła i wraca do punktu startu.

- **Co robi:** dodaje nową pętlę krawędzi przechodzącą przez zaznaczone krawędzie.
- Pętla przechodzi **tylko przez czworokąty**, nie przez trójkąty.
- **Porównanie z *Connect*:** pętla tworzy więcej geometrii niż *Connect Edges* lub *Connect Vertices*, ale czystszą. Pojedyncza krawędź na jednej ścianie zostawia połączenie typu T, a pętla daje spójną siatkę dookoła.
- 💡 Używaj pętli podczas modelowania, a na koniec usuń zbędne krawędzie, żeby zoptymalizować siatkę.
- **Skrót:** **Alt/Opt + U**
- **Menu:** `Tools > ProBuilder > Geometry > Insert Edge Loop`

### Subdivide Edges – podziel krawędzie

> **Tłumaczenie:** „podziel na mniejsze odcinki”.

- **Co robi:** dzieli zaznaczone krawędzie na kilka krawędzi, domyślnie każdą na dwie. Przykład z dokumentacji: podziel krawędź, a potem wyciągnij każdy fragment osobno.
- **Opcje:**
  - *Subdivisions* (liczba podziałów), domyślnie **1**, czyli jedna krawędź staje się dwiema. Wartość 3 zamienia jedną krawędź w cztery.
  - *Range* (zakres) to dozwolony zakres wartości, domyślnie **1–32**. Zmienisz go po rozwinięciu sekcji *Subdivisions*.
- **Skrót:** **Alt/Opt + S** (*Smart Subdivide*)
- **Menu:** `Tools > ProBuilder > Geometry > Smart Subdivide`

---

## 10. Akcje ścian (Face)

### Bevel (Faces) – fazuj ściany

> **Tłumaczenie:** jak przy krawędziach, „fazowanie”, tylko obejmuje wszystkie krawędzie ściany.

- **Co robi:** wykonuje *Bevel Edges* na **wszystkich krawędziach** zaznaczonych ścian. Przykład z dokumentacji: sfazowany wierzch sześcianu.
- **Opcja:** *Distance*, czyli szerokość fazy.

### Conform Normals (Faces) – ujednolić normalne ścian

> **Tłumaczenie:** jak w wersji dla obiektu, ale działa tylko na zaznaczone ściany.

- **Co robi:** ustawia normalne zaznaczonych ścian w tym samym kierunku względnym, który wybiera zgodnie z większością zaznaczonych ścian.
- **Menu:** `Tools > ProBuilder > Geometry > Conform Face Normals`

### Delete Faces – usuń ściany

> **Tłumaczenie:** dosłowne, „usuń”.

- **Co robi:** usuwa zaznaczone ściany z siatki. Można to cofnąć przez Undo.
- **Skrót:** **Backspace**. Na macOS działa też klawisz **Delete**.
- ⚠️ **Uwaga na Windowsie:** klawisz **Delete** usuwa **cały obiekt**, a nie tylko zaznaczone ściany.
- **Menu:** `Tools > ProBuilder > Geometry > Delete Faces`

### Detach Faces – odłącz ściany

> **Tłumaczenie:** *to detach* znaczy „odczepić, odłączyć”.

- **Co robi:** oddziela zaznaczone ściany od reszty siatki. Odłączone ściany zostają w miejscu. Na ilustracji w dokumentacji podniesiono je tylko dla czytelności.
- **Opcja:** *Detach To* (odłącz do):
  - *Game Object* przenosi ściany do nowego, osobnego obiektu,
  - *Submesh* tworzy z nich podsiatkę w tym samym obiekcie.
- ⚠️ Dokumentacja sama sobie przeczy: lista opcji podaje jako domyślne *Game Object*, a tekst mówi o podsiatce. Sprawdź ustawienie przed użyciem.
- **Menu:** `Tools > ProBuilder > Geometry > Detach Faces`

### Duplicate Faces – powiel ściany

> **Tłumaczenie:** „zduplikuj, powiel”.

- **Co robi:** kopiuje zaznaczone ściany. Przykład z dokumentacji: kopia ścian walca daje zakrzywiony panel.
- **Opcja:** *Duplicate To* (powiel do). Domyślnie kopia trafia do nowego obiektu, a przy *Submesh* staje się podsiatką w tym samym obiekcie.
- **Menu:** `Tools > ProBuilder > Geometry > Duplicate Faces`

### Extrude Faces – wyciągnij ściany

> **Tłumaczenie:** „wyciągnięcie (ekstruzja)”. Skąd ta nazwa, opisano przy *Extrude Edges*.

- **Co robi:** „wypycha” zaznaczoną ścianę i dobudowuje boczne ściany przy każdej jej krawędzi. Tak powstają np. kominy, wypustki i korytarze.
- **Opcje** (działają tylko przy użyciu przycisku lub skrótu):
  - *Extrude By* (wyciągaj według):
    - *Face Normals* (normalnych ścian) – każda ściana idzie w swoim kierunku, a sąsiednie pozostają połączone,
    - *Vertex Normals* (normalnych wierzchołków) – **domyślnie**; sąsiednie ściany pozostają połączone,
    - *Individual Faces* (pojedynczych ścian) – każda ściana jest wyciągana osobno i sąsiednie ściany się rozłączają.
  - *Distance* (odległość), domyślnie **0.5**. Można podać wartość ujemną.
- 💡 **Szybciej:** przytrzymaj **Shift** podczas przesuwania, obracania lub skalowania ściany. **Shift + skalowanie** daje *Inset* zamiast zwykłego wyciągnięcia.
- **Skrót:** **Ctrl/Cmd + E**
- **Menu:** `Tools > ProBuilder > Geometry > Extrude`

### Flip Face Edge (Turn Edges) – odwróć (obróć) krawędź ściany

> **Tłumaczenie:** czworokąt składa się z dwóch trójkątów rozdzielonych ukrytą przekątną. *Flip Face Edge* znaczy „przerzuć krawędź ściany”. W innych programach 3D ta sama operacja nazywa się *Turn Edges*, czyli „obróć krawędzie”: przekątna czworokąta zostaje zamieniona na drugą.

- **Co robi:** zamienia układ trójkątów w zaznaczonych czworokątach, odwracając kierunek środkowej krawędzi. Przydaje się do wygładzenia „grzbietu” na czworokącie, którego narożniki leżą na różnych wysokościach, np. w terenie.
- Działa **tylko na czworokątach**.
- **Menu:** `Tools > ProBuilder > Geometry > Flip Face Edge`

### Flip Face Normals – odwróć normalne ścian

> **Tłumaczenie:** „odwróć normalne” (zob. *Flip Normals*), ale tylko dla zaznaczonych ścian.

- **Co robi:** odwraca normalne tylko zaznaczonych ścian. *Flip Normals* w trybie obiektu odwraca je dla całej siatki.
- **Skrót:** **Alt/Opt + N**
- **Menu:** `Tools > ProBuilder > Geometry > Flip Face Normals`

### Inset – wstawka (wcięcie ściany)

> **Tłumaczenie:** *inset* znaczy „wstawka, element osadzony wewnątrz”. Chodzi o nową, mniejszą ścianę wstawioną do środka istniejącej, otoczoną pasem nowych ścian. Najlepiej oddaje to „wstawienie ściany do wewnątrz” albo „wcięcie”.

- **Co robi:** tworzy wewnątrz zaznaczonej ściany nowy zestaw ścian przez wyciągnięcie i przeskalowanie. Taką wstawkę można potem wyciągnąć do środka (np. wnęka, ściany wewnętrzne) albo na zewnątrz (np. bogatsze detale fasady).
- **To nie przycisk, tylko technika:**
  1. Przejdź do trybu ścian i zaznacz ścianę.
  2. Wybierz narzędzie *Scale*.
  3. Przytrzymaj **Shift** i skaluj wzdłuż jednej osi ściany, potem puść.
  4. Przeskaluj wzdłuż drugiej osi, żeby dokończyć wstawkę.

### Merge Faces – scal ściany

> **Tłumaczenie:** „scal w jedną”.

- **Co robi:** łączy zaznaczone ściany w jedną i usuwa krawędzie, które je rozdzielały.
- ⚠️ Scalanie niepołączonych ścian może dać nieoczekiwane mapowanie tekstur.
- ⚠️ Mogą też powstać artefakty: połączenia typu T oraz „pływające” wierzchołki, czyli nieużywane wierzchołki na krawędzi, przez które nie przechodzi żadna inna krawędź. Dokumentacja radzi scalać tylko wtedy, gdy to konieczne.
- **Menu:** `Tools > ProBuilder > Geometry > Merge Faces`

### Subdivide Faces – podziel ściany

> **Tłumaczenie:** „podziel na mniejsze ściany”.

- **Co robi:** dzieli zaznaczone ściany: dodaje wierzchołek na środku każdej krawędzi i łączy je w środku ściany. Dodaje sporo szczegółów do geometrii.
- 💡 Jeśli chcesz dokładnie kontrolować kształt nowych ścian, użyj narzędzia *Cut* (cięcie).
- **Skrót:** **Alt/Opt + S** (*Smart Subdivide*)
- **Menu:** `Tools > ProBuilder > Geometry > Smart Subdivide`

### Triangulate Faces – trianguluj ściany

> **Tłumaczenie:** „podziel na trójkąty”.

- **Co robi:** rozbija zaznaczone ściany na trójkąty, co daje fasetkowy, niewygładzony wygląd.
- 💡 Twarde krawędzie wygładzisz w oknie *Smooth Group Editor* (edytor grup wygładzania).
- **Menu:** `Tools > ProBuilder > Geometry > Triangulate`

---

## 11. Akcje zaznaczania (Selection)

### Grow Selection – powiększ zaznaczenie (V / E / F)

> **Tłumaczenie:** *to grow* znaczy „rosnąć”. Zaznaczenie „rozrasta się” na sąsiednie elementy.

- **Co robi:** rozszerza zaznaczenie o sąsiednie wierzchołki, krawędzie lub ściany.
- **Opcje:**
  - *Restrict To Angle* (ogranicz kątem) – zaznaczenie rośnie tylko na ściany nachylone w granicach zadanego kąta.
  - *Max Angle* (maksymalny kąt) – działa tylko przy włączonym *Restrict To Angle*.
  - *Iterative* (krokowo) – każde użycie dodaje jeden „pierścień” sąsiadów. Gdy *Restrict To Angle* jest wyłączone, ta opcja jest zawsze włączona.
- **Skrót:** **Alt/Opt + G**
- **Menu:** `Tools > ProBuilder > Selection > Grow Selection`

### Shrink Selection – zmniejsz zaznaczenie (V / E / F)

> **Tłumaczenie:** *to shrink* znaczy „kurczyć się”. To odwrotność *Grow*.

- **Co robi:** usuwa z zaznaczenia elementy na jego obrzeżu. Przykład z dokumentacji: z pięciu zaznaczonych ścian (krzyża) zostaje środkowy kwadrat.
- **Skrót:** **Alt/Opt + Shift + G**
- **Menu:** `Tools > ProBuilder > Selection > Shrink Selection`

### Select Edge Loop – zaznacz pętlę krawędzi (E)

> **Tłumaczenie:** „pętla” to ciąg krawędzi połączonych końcami, zobacz słowniczek.

- **Co robi:** z każdej zaznaczonej krawędzi zaznacza całą pętlę, czyli ciąg bezpośrednio połączonych krawędzi.
- Działa tylko na czworokątach.
- **Skrót:** **Alt/Opt + L**
- **Menu:** `Tools > ProBuilder > Selection > Select Loop`

### Select Edge Ring – zaznacz pierścień krawędzi (E)

> **Tłumaczenie:** *ring* znaczy „pierścień”: krawędzie „równoległe”, które leżą na wspólnych ścianach, ale się nie stykają, jak szczeble drabiny.

- **Co robi:** z każdej zaznaczonej krawędzi zaznacza pierścień krawędzi.
- Działa tylko na czworokątach.
- **Skrót:** **Alt/Opt + R**
- **Menu:** `Tools > ProBuilder > Selection > Select Ring`

### Select Face Loop / Select Face Ring – zaznacz pętlę / pierścień ścian (F)

> **Tłumaczenie:** „pętla ścian” i „pierścień ścian”. To pas kolejnych czworokątów biegnący dookoła obiektu. Pętla i pierścień różnią się kierunkiem.

- **Co robi:** z każdej zaznaczonej ściany zaznacza pas ścian w jednym kierunku (*Loop*) lub w kierunku prostopadłym (*Ring*). Dokumentacja upraszcza to tak: pętla biegnie zwykle wzdłuż osi Z, a pierścień wzdłuż osi X.
- Liczą się tylko czworokąty.
- **Skróty:** **Alt/Opt + L** (*Loop*) i **Alt/Opt + R** (*Ring*), te same co dla krawędzi.
- **Menu:** `Tools > ProBuilder > Selection > Select Loop` / `Select Ring`

### Select Holes – zaznacz dziury (V / E)

> **Tłumaczenie:** „zaznacz otwory w siatce”.

- **Co robi:** zaznacza wszystkie elementy wzdłuż otwartego brzegu, na którym leży zaznaczony wierzchołek lub krawędź. To szybki sposób na złapanie obrzeża po usuniętej ścianie. **Bez zaznaczenia** wybiera wszystkie dziury obiektu i podaje, ile ich jest.
- **Menu:** `Tools > ProBuilder > Selection > Select Hole`

### Select Path – zaznacz ścieżkę (F)

> **Tłumaczenie:** *path* znaczy „ścieżka”: najkrótszy ciąg ścian między dwoma punktami na powierzchni.

- **Co robi:** zaznacza ciąg ścian od już zaznaczonej ściany do ściany pod kursorem.
- **Jak użyć:** w trybie ścian zaznacz ścianę, przytrzymaj **Ctrl/Cmd + Shift** i ruszaj kursorem, żeby zobaczyć podgląd ścieżki. Kliknij, żeby ją zaznaczyć.
- Nie ma przycisku ani pozycji w menu. Skrótu nie da się zmienić, a akcja działa tylko przy **zamkniętym UV Editorze**.

### Select by Colors – zaznacz według koloru wierzchołków (V / E / F)

> **Tłumaczenie:** „zaznacz według kolorów”. Chodzi o kolory wierzchołków (*vertex colors*), czyli kolor zapisany w siatce, a nie w teksturze.

- **Co robi:** zaznacza wszystkie ściany o tym samym kolorze wierzchołków co zaznaczona ściana. Działa także wtedy, gdy kolor nie jest widoczny, np. pod materiałem w szachownicę. Przydaje się do grupowania części siatki kolorami.
- **Opcja:** *Current Selection* (bieżące zaznaczenie), domyślnie **wyłączona**. Gdy jest włączona, szuka tylko w zaznaczonych obiektach. Gdy jest wyłączona, szuka w całej scenie.
- **Menu:** `Tools > ProBuilder > Selection > Select Vertex Color`

### Select by Material – zaznacz według materiału (F)

> **Tłumaczenie:** dosłowne, „według materiału”.

- **Co robi:** zaznacza wszystkie ściany z tym samym materiałem co zaznaczona ściana. Przydaje się do szybkiej podmiany materiału na złożonym obiekcie lub w całej scenie.
- **Opcja:** *Current Selection*, domyślnie **wyłączona**, działa tak samo jak wyżej.
- **Menu:** `Tools > ProBuilder > Selection > Select Material`

> W menu `Selection` jest też **Select Smoothing Group** (zaznacz grupę wygładzania), ale nie ma jej w tabeli *Action reference*.

---

## 12. Przełączniki interakcji (Interaction)

Te akcje nie zmieniają geometrii. To **przełączniki**, które zmieniają sposób zaznaczania lub działania uchwytów. Ikona albo napis na przycisku pokazuje aktualny stan.

### Orientation (Handle) – orientacja uchwytów (O / V / E / F)

> **Tłumaczenie:** *orientation* to „orientacja, ułożenie”, *handle* to „uchwyt”. Chodzi o to, w którą stronę są skierowane strzałki manipulatora.

- **Stany** (kliknięcie przełącza je po kolei):
  - *Global* (globalna) – osie świata, niezależnie od obrotu obiektu, „jak kompas”,
  - *Local* (lokalna) – osie obrócone razem z obiektem,
  - *Normal* (wg normalnej) – oś dokładnie wzdłuż normalnej zaznaczonej ściany, wygodna np. do wyciągania pochyłych ścian.
- **Skrót:** **P**
- **Menu:** `Tools > ProBuilder > Interaction > Toggle Handle Orientation`

### Rect – tryb prostokąta zaznaczania (E / F)

> **Tłumaczenie:** *rect* to skrót od *rectangle*, „prostokąt”. Chodzi o prostokąt, który rysujesz, przeciągając myszą. *Complete* znaczy „w całości”, *Intersect* „przecina się”.

- **Stany:**
  - *Complete* – zaznacza tylko elementy leżące **w całości** w prostokącie,
  - *Intersect* – zaznacza też elementy, które są w prostokącie **tylko częściowo**.
- **Menu:** `Tools > ProBuilder > Interaction > Toggle Drag Rect Mode`

### Select Hidden – zaznaczaj ukryte (O / V / E / F)

> **Tłumaczenie:** *hidden* znaczy „ukryty”. Chodzi o elementy niewidoczne z bieżącego punktu widzenia, np. tylne ściany bryły.

- **Stany:**
  - *On* – zaznaczanie prostokątem łapie wszystko, także elementy po niewidocznej stronie,
  - *Off* – łapie tylko to, co aktualnie widać.
- **Menu:** `Tools > ProBuilder > Interaction > Toggle Select Back Faces` (nazwa w menu różni się od nazwy akcji).

### Shift – tryb klawisza Shift (V / E / F)

> **Tłumaczenie:** nazwa pochodzi od klawisza **Shift**. Akcja określa, co robi Shift + klik lub Shift + przeciągnięcie. Nazwy stanów pochodzą z teorii zbiorów: *Add* to „dodaj”, *Subtract* „odejmij”, *Difference* „różnica (symetryczna)”.

- **Stany:**
  - *Add* – dodaje klikane elementy do zaznaczenia,
  - *Subtract* – usuwa je z zaznaczenia,
  - *Difference* – przełącza: niezaznaczone dodaje, zaznaczone usuwa.
- Ten przełącznik jest teraz w overlayu *Tool Settings*.
- **Menu:** `Tools > ProBuilder > Interaction > Toggle Drag Selection Mode`

---

## 13. Typowe zastosowania (przykłady z dokumentacji)

| Cel | Jak to zrobić |
|---|---|
| Szybkie wyciąganie | Zaznacz ściany lub krawędzie i przesuń je z wciśniętym **Shift** (*Extrude*). |
| Wnęka, okno, panel | **Shift + skalowanie** ściany (*Inset*), potem wyciągnięcie do środka. |
| Wnętrze pomieszczenia | Sześcian, a potem **Flip Normals**: widać ściany od środka. |
| Model symetryczny | Wymodeluj połowę, potem **Mirror Objects**, **Merge Objects** i **Weld Vertices**. |
| Drzwi na zawiasach | Zaznacz krawędź po stronie zawiasu i użyj **Set Pivot** (Ctrl/Cmd + J). |
| Czysta topologia | Zamiast *Connect Edges* użyj **Insert Edge Loop**, żeby uniknąć połączeń T. Zbędne pętle usuń na końcu. |
| Model z innego programu | Użyj **ProBuilderize**. Przy artefaktach wyłącz *Import Quads*. |
| Niewidzialna ściana lub strefa | **Set Collider** dla przeszkody, **Set Trigger** dla strefy zdarzeń. |

---

## 14. Pułapki i niespójności w dokumentacji

- ⚠️ **Windows + Delete:** w trybie ścian klawisz **Delete** usuwa **cały obiekt**. Do usuwania ścian używaj **Backspace**.
- ⚠️ **Detach Faces:** dokumentacja podaje dwie różne wartości domyślne (*Game Object* i *Submesh*). Sprawdź opcję przed użyciem.
- **Insert Edge Loop:** w tabeli zbiorczej nie zaznaczono żadnego trybu, ale akcja działa w trybie krawędzi.
- **Connect Edges / Connect Vertices:** dokumentacja podaje ścieżkę menu `Selection > Smart Connect`, choć pozostałe akcje geometrii są w `Geometry`.
- **Pętle, pierścienie i Flip Face Edge** działają tylko na czworokątach. Na trójkątach pętla się „urywa”.
- **Extrude Edges i Bridge Edges** domyślnie działają tylko na krawędziach otwartych. Zmienisz to preferencją *Allow non-manifold actions*.

---

## 15. Ściąga skrótów

| Skrót | Akcja |
|---|---|
| **G** | przełączanie trybów edycji (V/E/F) |
| **Esc** | powrót do kontekstu obiektu |
| **P** | orientacja uchwytów (Global / Local / Normal) |
| **T** | Set Trigger |
| **Ctrl/Cmd + E** | Extrude (krawędzie i ściany) |
| **Ctrl/Cmd + J** | Set Pivot |
| **Ctrl/Cmd + Shift + klik** | Select Path |
| **Alt/Opt + B** | Bridge Edges |
| **Alt/Opt + C** | Collapse Vertices |
| **Alt/Opt + E** | Smart Connect (Connect Vertices / Edges) |
| **Alt/Opt + G** | Grow Selection |
| **Alt/Opt + Shift + G** | Shrink Selection |
| **Alt/Opt + L** | Select Loop (krawędzie i ściany) |
| **Alt/Opt + R** | Select Ring (krawędzie i ściany) |
| **Alt/Opt + N** | Flip Face Normals |
| **Alt/Opt + S** | Smart Subdivide (krawędzie i ściany) |
| **Alt/Opt + U** | Insert Edge Loop |
| **Alt/Opt + V** | Weld Vertices |
| **Alt/Opt + X** | Split Vertices |
| **Backspace** | Delete Faces |
| **Shift + przesuń/obróć/skaluj** | Extrude (przy skalowaniu ścian: Inset) |

---

## Źródła

- [ProBuilder 6.1 – Action reference](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/ref_actions.html)
- [ProBuilder 6.1 – Tool contexts and edit modes](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/modes.html)
- [ProBuilder 6.1 – Tools vs actions](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/tools.html)
- [ProBuilder 6.1 – ProBuilder menu](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/menu.html)
- [ProBuilder 6.1 – strona główna dokumentacji](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/index.html)
- Strony poszczególnych akcji, podlinkowane w tabeli *Action reference*, np. [Extrude Faces](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/Face_Extrude.html), [Weld Vertices](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/Vert_Weld.html), [Object Export](https://docs.unity3d.com/Packages/com.unity.probuilder@6.1/manual/Object_Export.html).
