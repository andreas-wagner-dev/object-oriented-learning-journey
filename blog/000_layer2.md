# 1. Layer-OOP

Objektorientierte Systeme bestehen aus Objekten, die reale Dinge oder Vorgänge repräsentieren.  
Ein Layer beschreibt dabei **nicht technische Schichten**, sondern **Abstraktionsebenen und Verantwortungsbereiche zwischen Objekten**.

---

## 1.2 Vertikale Layer (Abstraktionshöhe)

Vertikale Schichtung 'L' trennt Objekte nach ihrem **Abstraktionsgrad** 'n':

-  **Oben:** Allgemeine Regeln, Verträge, Begriffe (stabile Abstraktionen) -  L[n]
-  **Mitte:** Konkretisierungen dieser Regeln -  IL[n + 1]
-  **Unten:** Technische Details und austauschbare Mittel - IL[n + 2]

```mermaid
flowchart TB
  subgraph L1["Top-Layer (Abstraktionen)"]
    Rule["Rule"]
    App["App"]
  end

  subgraph L2["Middle-Layer (Spezialisierungen)"]
    CycleRule["CycleRule"]
    PDCApp["PDCApp"]
  end

  subgraph L3["Bottom-Layer (Details)"]
    ConsolePDCApp["ConsolePDCApp"]
  end

  L1 -->|depends on| L2
  L2 -->|depends on| L3

  ConsolePDCApp -.->|implements| App
  CycleRule -.->|implements| Rule
```

---

## 1.3 Horizontale Layer (Verantwortungsarten auf gleicher Ebene)

Innerhalb **derselben Abstraktionsebene** dürfen Objekte **unterschiedliche Rollen** haben.  
Diese Rollen folgen **strikten Abhängigkeitsregeln**:

| Layer | Bedeutung | Darf benutzen | Darf nicht benutzen |
|--------|-----------|----------------|---------------------|
| Value Objects | Tragen Daten oder einfache Operationen | – | Core, Orchestrators |
| Core Objects | Repräsentieren stabile Konzepte/Realitäten | Value | Orchestrators |
| Orchestrating Objects | Reale Vorgänge / Abläufe | Core + Value | (keine Abhängigkeit nach oben) |

### Beispiel (OOP-konform benannt)

```mermaid
flowchart LR

  subgraph H1["Value Objects"]
    U1["Date"]
    U2["Money"]
  end

  subgraph H2["Core Objects"]
    C1["Invoice"]
    C2["Customer"]
  end

  subgraph H3["Orchestrating Objects"]
    O1["Invoicement"]
    O2["Registration"]
  end

  %% Allowed
  H2 -->|uses| H1
  H3 -->|uses| H2
  H3 -->|uses| H1

  %% Forbidden
  H1 -.->|forbidden| H2
  H1 -.->|forbidden| H3
  H2 -.->|forbidden| H3
```

---

## 1.4 Zusammenhänge & Zusammenfassung

- **Vertikal ordnet nach Abstraktion (Was ist allgemeiner vs. spezieller?).**
- **Horizontal ordnet nach Verantwortung (Welche Rolle übernimmt ein Objekt auf derselben Höhe?).**

✅ **Merksatz:**

> *Vertikale* Layer trennen **Ideen von Details**.  
> *Horizontale* Layer trennen **Zustände von Abläufen**.  

## OOP-Layering-Gesetz (Implementierungsabhängigkeiten)

| Beziehung                                   | Erlaubt? | Bedingung                                                                                  |
| ------------------------------------------- | -------- | ------------------------------------------------------------------------------------------ |
| **IL[n] → L[n]**                            | ✅        | Implementierung kennt ihre eigene Abstraktion                                              |
| **IL[n] → L[n-1] oder L[n-2] ...**          | ✅        | Implementierung darf höhere Abstraktionen (nach oben) verwenden                            |
| **IL[n] → IL[n] (Seitlich, gleiche Ebene)** | ✅        | Solange **keine zyklische Abhängigkeit** entsteht                                          |
| **IL[n] → IL[m] (m > n, nach unten)**       | ✅        | Implementierung darf konkrete Implementierung tieferer Ebene direkt instanziieren / nutzen |
| **IL[n] → IL[m] (m < n, nach oben)**        | ❌        | Keine Abhängigkeit von Detail zu höher liegendem Detail                                    |
| **L[n] → IL[n] oder IL[m]**                 | ❌        | Abstraktionen kennen niemals Implementierungen                                             |


**Kurzform:**
- Abstraktionen dürfen niemals Implementierungen kennen.
- Implementierungen dürfen beliebig nach unten und seitlich greifen (ohne Zyklen) – aber niemals nach oben.

```mermaid
flowchart TB

    %% Abstraktionsebene 1
    subgraph L1["L1: Abstraktionen"]
      A_App["App"]
      A_Rule["Rule"]
      A_SubRule["SubRule"]
    end

    %% Implementierungsebene 1
    subgraph IL1["IL1: Implementierungen zu L1"]
      I_HttpRule["HttpRule (implements Rule)"]
      I_FileApp["FileApp (implements App)"]
    end

    %% Abstraktionsebene 2
    subgraph L2["L2: Abstraktionen"]
      A_ConcreteSubRule["ConcreteSubRule"]
    end

    %% Implementierungsebene 2
    subgraph IL2["IL2: Implementierungen zu L2"]
      I_SubRuleImpl["SubRuleImpl (implements ConcreteSubRule)"]
    end

    %% Erlaubte Abhängigkeiten (grün)
    IL1 -->|uses| L1
    IL1 -->|uses| L2
    IL1 -->|uses| IL2

    IL2 -->|uses| L2
    IL2 -->|uses| L1

    IL1 -->|uses same-level| IL1

    %% Implements-Beziehungen (blau gestrichelt)
    I_HttpRule -.->|implements| A_Rule
    I_FileApp -.->|implements| A_App
    I_SubRuleImpl -.->|implements| A_ConcreteSubRule

    %% Verbotene Abhängigkeiten (rot gestrichelt)
    L1 -.->|forbidden| IL1
    L2 -.->|forbidden| IL2
    IL2 -.->|forbidden no upward impl| IL1

    %% Styling
    linkStyle 0 stroke:green,stroke-width:2px
    linkStyle 1 stroke:green,stroke-width:2px
    linkStyle 2 stroke:green,stroke-width:2px
    linkStyle 3 stroke:green,stroke-width:2px
    linkStyle 4 stroke:green,stroke-width:2px
    linkStyle 5 stroke:green,stroke-width:2px

    linkStyle 6 stroke:blue,stroke-width:2px,stroke-dasharray:3 3
    linkStyle 7 stroke:blue,stroke-width:2px,stroke-dasharray:3 3
    linkStyle 8 stroke:blue,stroke-width:2px,stroke-dasharray:3 3

    linkStyle 9 stroke:red,stroke-width:2px,stroke-dasharray:5 5
    linkStyle 10 stroke:red,stroke-width:2px,stroke-dasharray:5 5
    linkStyle 11 stroke:red,stroke-width:2px,stroke-dasharray:5 5

```




# Formales Layering-Gesetz
## Definitionen
L[n] = Layer der Abstraktionsebene n
(enthält ausschließlich Abstraktionen dieser Ebene, z. B. Interfaces, Policies, Verträge, Domänenbegriffe).

IL[n] = Implementierungslayer zu L[n]
(enthält ausschließlich Objekte, die Abstraktionen aus L[n] konkretisieren oder spezialisieren).
```
Namespace = com.company.<app> = globaler Einstiegspunkt der Architektur (Top-Level).
```
**Axiome**
Existenz von Layers
Jeder Layer L[n] darf nur existieren, wenn er eine wohldefinierte Abstraktionsebene beschreibt.
```
∃ L[n] : L[n] ∈ {Namespace, Namespace.*}
```
Existenz von Implementierungslayern
Ein Implementierungslayer IL[n] darf nur existieren, wenn eine gleichnamige Abstraktion L[n] existiert.
```
∃ L[n] ⇒ ∃ IL[n]
```
Inhaltsregel für Implementierungslayer
Alle Objekte in IL[n] müssen Implementierungen oder Spezialisierungen von L[n] sein.
```
∀ x ∈ IL[n] : x ⊑ L[n]
```
Abhängigkeitsregel
L[n] darf nur Abhängigkeiten zu L[n-1] oder darunter haben.
IL[n] darf nur Abhängigkeiten zu L[n] oder darunter haben.
Formal:
```
L[n] → L[n-1]
IL[n] → {L[n], L[n-1], …}
```
## Implikationen

Layers sind stabil, Implementierungslayer sind variabel
L[n] beschreibt das „Was“ (Vertrag).
IL[n] beschreibt das „Wie“ (Umsetzung).

Hierarchie
Abstraktionen L[n] bilden die Architektur-Hierarchie.
Implementierungslayer IL[n] folgen strikt dieser Hierarchie und dürfen nicht „hochklettern“.

Kein Wildwuchs
Es gibt keine „freien“ Implementierungslayer ohne dazugehörige Abstraktion.
Jede Implementierung ist eindeutig rückführbar auf ihre Abstraktion.

## Diagramm Layering-Gesetz
Beispielabbildung (angepasst an das Paket Beispiel)
```
L1: App, Rule, Package
IL1: app/, rule/
L2: SubRule
IL2: subrule/
L3: (keine weiteren Abstraktionen)
IL3: MySubRule
```

```mermaid
flowchart TD

    subgraph L1["Layer 1: Abstraktionen (L1)"]
      A1_App["App (a[1])"]
      A1_Rule["Rule (a[1])"]
      A1_Package["Package (a[1])"]
    end

    subgraph IL1["Implementierungslayer (IL1)"]
      I1_PDCApp["PDCApp (i[2])"]
      I1_Console["ConsolePDCApp (i[2])"]
      I1_CycleRule["CycleRule (i[2])"]
    end

    subgraph L2["Layer 2: Abstraktionen (L2)"]
      A2_SubRule["SubRule (a[2])"]
    end

    subgraph IL2["Implementierungslayer (IL2)"]
      I2_Subrule["MySubRule (i[3])"]
    end

    L1 --> IL1
    L2 --> IL2

    IL1 --> L1
    L2 --> L1
    IL2 --> L2
    IL2 --> L1
```


## Kombiniertes Vertikal + Horizontal Layering (3D-Sicht)

```mermaid
flowchart TD

    %% Vertikale Abstraktionsebenen
    subgraph L1["L1 – Höchste Abstraktion (Policies / Verträge)"]
      A1["App"]
      B1["Rule"]
    end

    subgraph L2["L2 – Fachliche Spezialisierung"]
      A2["SubRule"]
      B2["Policy"]
    end

    subgraph L3["L3 – Konkrete Objekte"]
      A3["MySubRule"]
    end

    %% Horizontale Rollen innerhalb jeder Ebene
    A1 ---|"Core Object"| B1
    A2 ---|"Core Object"| B2
    A3 ---|"Core Object"| A3

    %% Orchestrator-Schicht (horizontal querlaufend)
    subgraph Orchestrators["Horizontale Schicht: Orchestrierende Objekte (nur bei Bedarf)"]
      O1["Session"]
      O2["Conversation"]
    end

    %% Implementierungslayer seitlich
    A1 -.->|"IL1"| I1["ConsoleApp"]
    A2 -.->|"IL2"| I2["ConcreteSubRule"]
    A3 -.->|"IL3"| I3["ConcreteMySubRule"]

    %% Abhängigkeitsregeln
    L2 --> L1
    L3 --> L2
    O1 --> L2
    O2 --> L1

    %% Styling
    classDef core fill:#d4f1ff,stroke:#0077b6;
    classDef orch fill:#ffe8d6,stroke:#d68c45;
    classDef impl fill:#e2e2e2,stroke:#555;

    class A1,B1,A2,B2,A3 core;
    class O1,O2 orch;
    class I1,I2,I3 impl;
```

| Ebene                          | Bedeutung                               | Farbe                      |
| ------------------------------ | --------------------------------------- | -------------------------- |
| **Abstraktion (L1–L3)**        | Verträge / Policies                     | 🔵 Blau                    |
| **Implementierung (IL1–IL3)**  | Konkrete Ausprägungen                   | ⚪ Grau                     |
| **Horizontale Orchestratoren** | rein koordinierend (falls unvermeidbar) | 🟠 Orange                  |
| **Verbotene Abhängigkeit**     | Darf nicht passieren                    | 🔴 Rote gestrichelte Linie |

```mermaid
flowchart TD

    %% Vertikale Abstraktionsebenen
    subgraph L1["L1 – Abstraktion (Policies / Verträge)"]
      A1["App"]
      B1["Rule"]
    end

    subgraph L2["L2 – Fachliche Spezialisierung"]
      A2["SubRule"]
    end

    subgraph L3["L3 – Konkrete Abstraktionen"]
      A3["Request"]
    end

    %% Implementierungslayer (seitlich)
    subgraph IL1["IL1 – Implementierungen von L1"]
      I1["ConsoleApp"]
      I2["HttpRule"]
    end

    subgraph IL2["IL2 – Implementierungen von L2"]
      I3["ConcreteSubRule"]
    end

    subgraph IL3["IL3 – Implementierungen von L3"]
      I4["WebRequest"]
    end

    %% Horizontale Koordinatoren
    subgraph Orchestrators["Horizontale Schicht (optional)"]
      O1["Session"]
      O2["Conversation"]
    end

    %% Erlaubte Abhängigkeiten (grün)
    L2 -->|allowed| L1
    L3 -->|allowed| L2
    IL1 -->|allowed| L1
    IL2 -->|allowed| L2
    IL3 -->|allowed| L3
    O1 -->|allowed| L2
    O2 -->|allowed| L1

    %% Verbotene Abhängigkeiten (rot gestrichelt)
    L1 -.->|forbidden| L2
    IL1 -.->|forbidden| IL2
    IL3 -.->|forbidden| L1

    %% Styling
    classDef abstraction fill:#d4f1ff,stroke:#0077b6,stroke-width:2px;
    classDef implementation fill:#e0e0e0,stroke:#666,stroke-width:1px;
    classDef orchestrator fill:#ffe5cc,stroke:#cc7a00,stroke-width:2px;
    classDef forbidden stroke:red,stroke-width:2px,stroke-dasharray:5 5;

    class A1,B1,A2,A3 abstraction;
    class I1,I2,I3,I4 implementation;
    class O1,O2 orchestrator;

    linkStyle 6 stroke:red,stroke-width:2px,stroke-dasharray:5 5;
    linkStyle 7 stroke:red,stroke-width:2px,stroke-dasharray:5 5;
    linkStyle 8 stroke:red,stroke-width:2px,stroke-dasharray:5 5;
```

| Farbe / Stil              | Bedeutung                                        |
| ------------------------- | ------------------------------------------------ |
| **Grün (normaler Pfeil)** | Erlaubte Abhängigkeit (`depends on`)             |
| **Blau gestrichelt**      | Implementierung / Spezialisierung (`implements`) |
| **Rot gestrichelt**       | Verbotene Abhängigkeit                           |


```mermaid
flowchart TD

    %% Layer 1 Abstraktionen
    subgraph L1["Layer 1: Abstraktionen (L1)"]
      A1_Rule["Rule"]
      A1_App["App"]
      A1_Package["Package"]
    end

    %% Layer 1 Implementierungen
    subgraph IL1["IL1: Implementierungen von L1"]
      I1_HttpRule["HttpRule"]
      I1_ConsoleApp["ConsoleApp"]
      I1_FileStorage["FileStorage"]
    end

    %% Layer 2 Abstraktion
    subgraph L2["Layer 2: Abstraktionen (L2)"]
      A2_SubRule["SubRule"]
    end

    %% Layer 2 Implementierungen
    subgraph IL2["IL2: Implementierungen von L2"]
      I2_ConcreteSubRule["ConcreteSubRule"]
    end

    %% Allowed Abhängigkeiten
    L1 -->|allowed| IL1
    L2 -->|allowed| IL2
    IL1 -->|allowed| L1
    IL2 -->|allowed| L2

    %% Cross-Abhängigkeit erlaubt
    I1_HttpRule -->|allowed special| I2_ConcreteSubRule

    %% Implements-Beziehungen blau
    I1_HttpRule -.->|implements Rule| A1_Rule
    I1_ConsoleApp -.->|implements App| A1_App
    I2_ConcreteSubRule -.->|implements SubRule| A2_SubRule

    %% Forbidden Abhängigkeiten
    IL2 -.->|forbidden| IL1
    L2 -.->|forbidden| L1
```
