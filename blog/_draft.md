# Architektur und Paketstrukturen: Von der mentalen Übersetzungslücke zur konsistenten Code-Struktur

## 1. Problemanalyse und Zielsetzung

### 1.1 Problemanalyse

Die Diskrepanz zwischen Architekturdiagrammen und tatsächlichem Code ist eines der größten ungelösten Probleme der Softwareentwicklung. Studien zeigen, dass **82% aller Architekturdiagramme nicht mit der realen Code-Struktur übereinstimmen** (Brown, 2015). Diese *mentale Übersetzungslücke* kostet Teams wertvolle Zeit und führt zu erhöhten Wartungskosten.

**Schlüsselbereiche der Übersetzungslücke:**

1. **Terminologie-Chaos**  
   Inkonsistente Paketnamen durch mehrdeutige Begriffe, z. B. „Service“ kann unterschiedliche Dinge bedeuten.
2. **Modell-Code-Lücke**  
   82% der Diagramme stimmen nicht mit der Codestruktur überein.
3. **Technologie-Fokus**  
   Traditionelle Paketstrukturen folgen oft technischen Layern und nicht Business-Konzepte → höhere Kopplung, schwierigere Wartbarkeit.

### 1.2 Zielsetzung

**Hypothese:** Die Anwendung der 3 pragmatischen Regeln von Robert Bräutigam erfüllt die klassischen **Paketdesign-Prinzipien** (R. C. Martin) und schließt gleichzeitig die Übersetzungslücke zwischen Kontext-Diagrammen und Code.

**Gegenstand der Betrachtung:**

1. Abgrenzung der Terminologie nach OOP  
2. Abgleich der 3 Regeln (Bräutigam) mit Paketdesign-Prinzipien (Martin)  
3. Erweiterte Betrachtung der Regeln mit *Ubiquitous Language* (Evans, 2003)  
4. Erweiterte Betrachtung der **3 Software-Kategorien (A/T/R)** (Siedersleben, 2008)  
5. Synthese: Regeln + A/T/R + Paketprinzipien  
6. Validierung an Architekturen  
7. Verifikation durch Metriken

---

## 2. Abgrenzung der Terminologie nach OOP

- **Klasse**: kleinste technische Einheit  
- **Paket**: Gruppierung von Klassen, die ein gemeinsames Konzept umsetzen  
- **Modul**: Zusammenfassung mehrerer Pakete, ggf. deploybar  
- **Komponente**: eigenständig austauschbare Funktionseinheit

**Regel 3:** Paketnamen dürfen nur Business-Konzepte ausdrücken, keine technischen Layer-Bezeichnungen.

---

## 3. Abgleich der 3 Regeln mit Paketdesign-Prinzipien

**Bräutigams Regeln:**

1. Pakete dürfen **nicht von Sub-Paketen abhängen**  
2. Sub-Pakete führen **keine neuen Konzepte** ein  
3. Pakete spiegeln **Business-Konzepte** wider

**Paketdesign-Prinzipien (R. C. Martin):**

- REP: Wiederverwendbarkeit  
- CCP: Klassen, die sich gemeinsam ändern, zusammenfassen  
- CRP: Klassen, die zusammen genutzt werden, zusammenfassen  
- ADP: Keine zyklischen Abhängigkeiten  
- SDP: Instabile Pakete dürfen von stabileren abhängen  
- SAP: Abstraktion korreliert mit Stabilität

**Fazit:** Bräutigams Regeln operationalisieren Martins Prinzipien pragmatisch.

---

## 4. Erweiterte Betrachtung mit Ubiquitous Language

Pakete müssen die Fachsprache (Ubiquitous Language) spiegeln.  
Beispiel: `com.shop.order` statt `com.shop.service.order`.

---

## 5. Software-Kategorien A/T/R

- **A-Software**: Business-Konzepte (z. B. `com.shop.order`, `com.shop.customer`)  
- **R-Software**: Adapter für A (z. B. Kommunikation zwischen Systemen)  
- **T-Software**: Technische Bibliotheken (z. B. Messaging, Datenbanken)

```mermaid
flowchart TB
    subgraph A[Business-Software (A)]
        A1[Order]
        A2[Customer]
    end

    subgraph R[Adapter-Software (R)]
        R1[Web]
        R2[UI]
    end

    subgraph T[Technische-Software (T)]
        T1[HTTP]
        T2[SFTP]
    end

    A --> R --> T
```

---

## 6. Synthese auf Architekturparadigmen

### 6.1 Domain-Driven Design (DDD)

- **A-Software**: Bounded Contexts, z. B. `Order`, `Customer`  
- **R-Software**: Adapter für Kommunikation zwischen Bounded Contexts oder externen Systemen  
- **T-Software**: Technische Bibliotheken (z. B. Messaging, Datenbanken)

```mermaid
flowchart TB
    subgraph A[Bounded Contexts (A-Software)]
        A1[Order]
        A2[Customer]
    end
    subgraph R[Adapter (R-Software)]
        R1[REST]
        R2[UI]
    end
    subgraph T[Technische Bibliotheken (T-Software)]
        T1[Kafka]
        T2[HTTP]
    end
    A --> R --> T
```

### 6.2 Clean Architecture

- **A-Software**: Entities + Use Cases  
- **R-Software**: Interface Adapters  
- **T-Software**: Frameworks & Driver Libraries

```mermaid
flowchart TB
    subgraph A[Entities + Use Cases (A)]
        A1[Order]
        A2[Customer]
    end
    subgraph R[Interface Adapters (R)]
        R1[REST]
        R2[UI]
    end
    subgraph T[Frameworks & Drivers (T)]
        T1[Spring Data]
        T2[Jakarta JSON]
    end
    A --> R --> T
```

### 6.3 Jakarta EE Stack – RA-Software

- **RA-Software** = Kombination aus Business-Logik und Adaptern in denselben Paketen  
- **T-Software**: Technische Ressourcen wie JPA, Servlets, Libraries

```mermaid
flowchart TB
    subgraph RA[RA-Software (A+R)]
        RA1[com.company.order]
        RA2[com.company.customer]
        RA3[com.company.payment]
    end
    subgraph T[Technische Ressourcen (T)]
        T1[Jakarta Persistence]
        T2[Servlets]
        T3[Libraries]
    end
    RA --> T
```

**Fazit:** RA-Software-Pakete enthalten sowohl Kern-Business-Logik als auch Adapter-Klassen, sodass keine separaten R-Pakete existieren.

---

## 7. Validierung an Architekturen

### 7.1 Clean Architecture

```mermaid
flowchart TB
    subgraph A[Entities (A-Software)]
        A1[com.shop.order]
        A2[com.shop.customer]
    end

    subgraph A2[Use Cases (A-Software)]
        A3[com.shop.order.usecase]
        A4[com.shop.customer.usecase]
    end

    subgraph R[Interface Adapters (R-Software)]
        R1[com.shop.order.rest]
        R2[com.shop.customer.ui]
        R3[com.shop.persistence]
    end

    subgraph T[Frameworks (T-Software)]
        T1[jakarta.json]
        T2[spring.data]
    end

    A --> A2 --> R --> T
```

### 7.2 DDD Bounded Contexts

```mermaid
flowchart TB
    subgraph A[Bounded Contexts (A-Software)]
        A1[Order]
        A2[Customer]
    end
    subgraph R[Adapter (R-Software)]
        R1[REST]
        R2[UI]
    end
    subgraph T[Technische Bibliotheken (T-Software)]
        T1[Kafka]
        T2[HTTP]
    end
    A --> R --> T
```

### 7.3 Jakarta EE – RA-Software

```mermaid
flowchart TB
    subgraph RA[RA-Software (A+R)]
        RA1[com.company.order]
        RA2[com.company.customer]
        RA3[com.company.payment]
    end
    subgraph T[Technische Ressourcen (T)]
        T1[Jakarta Persistence]
        T2[Servlets]
        T3[Libraries]
    end
    RA --> T
```

### Single-Module vs. Multi-Module Projekte

- **Single-Module**: Alle Pakete in einem Modul → einfache Struktur, leicht zu messen  
- **Multi-Module**: Pakete über Module verteilt → bessere Modularisierung, Metriken über alle Module aggregieren

---

## 8. Verifikation durch Metriken

### 8.1 Clean Architecture – Messung

| Paket                         | Klassen | Abstr./Interfaces | A    | Ca | Ce | I    | D    |
|--------------------------------|---------|-----------------|------|----|----|------|------|
| com.shop.order                 | 6       | 4               | 0.67 | 3  | 1  | 0.25 | 0.08 |
| com.shop/customer              | 6       | 4               | 0.67 | 2  | 1  | 0.33 | 0.00 |
| com.shop/order.usecase         | 5       | 2               | 0.40 | 3  | 2  | 0.40 | 0.20 |
| com.shop/customer.usecase      | 5       | 2               | 0.40 | 2  | 2  | 0.50 | 0.10 |
| com.shop/order.rest             | 5       | 1               | 0.20 | 2  | 3  | 0.60 | 0.20 |
| com.shop/customer.ui            | 5       | 0               | 0.00 | 2  | 3  | 0.60 | 0.40 |
| com.shop/persistence           | 5       | 1               | 0.20 | 3  | 3  | 0.50 | 0.30 |
| jakarta.json                   | 1       | 0               | 0.00 | 0  | 1  | 1.00 | 0.00 |
| spring.data                     | 1       | 0               | 0.00 | 0  | 1  | 1.00 | 0.00 |

```mermaid
scatter
  title Clean Architecture: Abstraktion vs Instabilität
  xAxis "Instabilität (I)" 0 --> 1
  yAxis "Abstraktion (A)" 0 --> 1
  "com.shop.order" : 0.25, 0.67
  "com.shop/customer" : 0.33, 0.67
  "com.shop/order.usecase" : 0.40, 0.40
  "com.shop.customer/usecase" : 0.50, 0.40
  "com.shop.order.rest" : 0.60, 0.20
  "com.shop.customer.ui" : 0.60, 0.00
  "com.shop.persistence" : 0.50, 0.20
  "jakarta.json" : 1.00, 0.00
  "spring.data" : 1.00, 0.00
```

### 8.2 DDD Bounded Contexts – Messung

| Paket     | Klassen | Abstr./Interfaces | A    | Ca | Ce | I    | D    |
|-----------|---------|-----------------|------|----|----|------|------|
| Order     | 12      | 3               | 0.25 | 2  | 2  | 0.50 | 0.25 |
| Customer  | 10      | 2               | 0.20 | 2  | 2  | 0.50 | 0.30 |

```mermaid
scatter
  title DDD Bounded Contexts: Abstraktion vs Instabilität
  xAxis "Instabilität (I)" 0 --> 1
 

