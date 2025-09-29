# 1. Layer-OOP

Objektorientierte Systeme bestehen aus Objekten, die reale Dinge oder Vorgänge repräsentieren.  
Ein Layer beschreibt dabei **nicht technische Schichten**, sondern **Abstraktionsebenen und Verantwortungsbereiche zwischen Objekten**.

---

## 1.2 Vertikale Layer (Abstraktionshöhe)

Vertikale Schichtung trennt Objekte nach ihrem **Abstraktionsgrad**:

- **Oben:** Allgemeine Regeln, Verträge, Begriffe (stabile Abstraktionen)
- **Mitte:** Konkretisierungen dieser Regeln
- **Unten:** Technische Details und austauschbare Mittel

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
| Utility Objects | Tragen Daten oder einfache Operationen | – | Core, Orchestrators |
| Core Objects | Repräsentieren stabile Konzepte/Realitäten | Utilities | Orchestrators |
| Orchestrating Objects | Reale Vorgänge / Abläufe | Core + Utilities | (keine Abhängigkeit nach oben) |

### Beispiel (OOP-konform benannt)

```mermaid
flowchart LR

  subgraph H1["Utility Objects"]
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
- **Alle Objekte müssen reale Dinge oder Vorgänge repräsentieren.**
- **„Service“, „Manager“, „Controller“ sind verboten — außer sie benennen echte Rollen.**

✅ **Merksatz:**

> *Vertikale Layer trennen **Ideen von Details**.  
> Horizontale Layer trennen **Zustände von Abläufen**.  
> Nur reale Dinge dürfen als Objekte existieren.*


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
