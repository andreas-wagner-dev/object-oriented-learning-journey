# Akademische Betrachtung von Software-Paketstrukturen

*Andy, Datum: …*

## Einleitung

Die Organisation von Software in Paketen beeinflusst maßgeblich Wartbarkeit, Verständlichkeit und Erweiterbarkeit.  
In der Theorie finden sich dazu die von Robert C. Martin formulierten **Package Design Principles** (OODPP). In der Praxis habe ich in meinem Projekt **[Context Driven Packaging](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/001_context_driven_packaging_de.md)** Beobachtungen gesammelt.  
Dieser Beitrag untersucht, wie sich Theorie und Praxis zueinander verhalten.




---

## Theoretische Grundlagen: OO Package Design Principles

Die Präsentation „OO Package Design Principles“:contentReference[oaicite:2]{index=2} nennt sechs zentrale Prinzipien:

1. **REP – Reuse-Release Equivalency Principle**  
   > „The unit of reuse is the unit of release.“  
   → Wiederverwendbare Komponenten sollen als Paket gebündelt und versioniert veröffentlicht werden.

2. **CCP – Common Closure Principle**  
   > „Classes which change together belong together.“  
   → Klassen, die gemeinsam verändert werden, gehören ins gleiche Paket, um Änderungsaufwand zu minimieren.

3. **CRP – Common Reuse Principle**  
   > „Classes in packages should be reused together.“  
   → Pakete sollen nur Klassen enthalten, die auch tatsächlich gemeinsam genutzt werden, um unnötige Abhängigkeiten zu vermeiden.

4. **ADP – Acyclic Dependencies Principle**  
   → Paketabhängigkeiten müssen einen gerichteten azyklischen Graphen (DAG) bilden; keine Zyklen.

5. **SDP – Stable Dependencies Principle**  
   → Abhängigkeiten sollen immer in Richtung stabilerer Pakete zeigen.

6. **SAP – Stable Abstractions Principle**  
   → Stabile Pakete sollen abstrakt sein, instabile Pakete konkret.

Diese Prinzipien bilden zusammen ein Regelwerk für **kohäsive Pakete mit klarer Kopplung**.

---

## Praktische Beobachtung: Context Driven Packaging

In meiner praktischen Arbeit habe ich festgestellt:

- **Kontextabhängigkeit:** Paketstrukturen entstehen oft nicht aus reiner Theorie, sondern durch Projektumstände (Teamgröße, Release-Zyklen, Technologie).  
- **Einfache Anfänge:** Kleine Systeme starten mit groben Paketen; klare Schichtentrennung wird später oft verwischt.  
- **Wachstumsprobleme:** Mit wachsender Komplexität treten „Monolith-Pakete“ und chaotische Abhängigkeiten auf.  
- **Explizite Kontextentscheidungen:** Bewusste Zuweisung von Verantwortlichkeiten („dieses Paket = Datenzugriff“, „jenes = Geschäftslogik“) erhöht Stabilität.

👉 Siehe meine ausführliche Dokumentation: [Context Driven Packaging](https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/001_context_driven_packaging_de.md)

---

## Theorie vs. Praxis: Beleg oder Widerspruch?

Die OODPP-Prinzipien *belegen* meine Beobachtungen:

| Prinzip (Theorie) | Beobachtung (Praxis) |
|-------------------|-----------------------|
| **REP** – Pakete sollen die kleinste wiederverwendbare Einheit sein | Wenn Pakete ohne klare Release- und Reuse-Grenzen wachsen, entstehen unhandliche Monolithen – genau meine Erfahrung. |
| **CCP** – Klassen, die zusammen geändert werden, gehören zusammen | Änderungen betreffen in der Praxis oft mehrere Pakete, weil diese Regel nicht konsequent beachtet wurde. |
| **CRP** – Nur Klassen bündeln, die wirklich zusammen genutzt werden | „Utility-Pakete“ in meinem Projekt führten zu unnötigen Abhängigkeiten – ein klassischer Verstoß gegen CRP. |
| **ADP** – Keine Zyklusabhängigkeiten | Zyklische Abhängigkeiten („Morning-After-Syndrome“) traten mehrfach auf, wenn Struktur nicht kontrolliert wurde. |
| **SDP** – Abhängigkeiten sollen Richtung stabiler Pakete gehen | In gewachsenen Projekten beobachtete ich das Gegenteil: instabile Pakete wurden Basis anderer, was Änderungen blockierte. |
| **SAP** – Stabile Pakete sollten abstrakt sein | In der Praxis waren stabile Pakete oft zu konkret, wodurch sie schwer änderbar wurden – genau das Problem, vor dem die Theorie warnt. |

**Fazit:**  
Die Theorie bestätigt meine Praxisbeobachtungen – Probleme entstehen typischerweise dort, wo Prinzipien verletzt werden. Das spricht dafür, dass die OODPP-Regeln nicht nur „akademisch“ sind, sondern reale Muster erklären.

---

## Schlussfolgerung

- **Ja, die Theorie belegt meine praktische Beobachtung.**  
- Viele Schwierigkeiten (Monolith-Pakete, Abhängigkeitschaos, Package Drift) sind direkt als Verstöße gegen OODPP-Prinzipien erklärbar.  
- Paketarchitektur ist damit kein Randthema, sondern ein zentraler Qualitätsfaktor – sowohl in akademischen Konzepten als auch in meiner täglichen Praxis.  










\`

# Business Concept-Driven Package Structure

*Synthese aus Kontext-Diagrammen und Business-Konzeptgetriebenen Paketstrukturen im Code*

*Von Andreas Wagner | 20. September 2025*

---

## 1. Problemanalyse und Zielsetzung

### 1.1 Problemanalyse

Die Diskrepanz zwischen Architekturdiagrammen und tatsächlichem Code ist eines der größten ungelösten Probleme der Softwareentwicklung. Studien zeigen, dass 82% aller Architekturdiagramme nicht mit der realen Code-Struktur übereinstimmen (Brown, 2015). Diese "mentale Übersetzungslücke" kostet Teams wertvolle Zeit und führt zu erhöhten Wartungskosten.

Die **mentale Übersetzungslücke** zwischen Architekturdiagrammen und Code-Struktur manifestiert sich in drei Schlüsselbereichen:

2. **Modell-Code-Lücke** (Brown, 2015):
   * **Empirisch**: 82% der Diagramme stimmen nicht mit der Codestruktur überein
   * "Our architecture diagrams don't match the code"
     
1. **Terminologie-Chaos** (Brown, 2015):
   * **Beobachtung**: Inkonsistente (Paket-)namen durch mehrdeutige Terminologie
   * z. B. "Service" kann bedeuten:
     * Spring-Service (`@Service`)
     * Business-Logik-Klasse
     * Deployierbares Artefakt

3. **Fehlende Business-Ausrichtung**:
   * **Beobachtung**: Package-by-Layer Ansätze erschweren Business-Feature-Entwicklung (Hauer, 2020)
   * Traditionelle Paketstrukturen folgen oft technischen Schichten und reflectieren oft nicht Business-Konzepte
   * **Folge**: Höhere Kopplungsmetriken und schwierigere Wartbarkeit

### 1.2 Zielsetzung

**Beweis der Hypothese** die Anwendung der 3 pragmatischen Regeln von Robert Bräutigam zum Strukturieren von Paketen die Paketdesign-Prinzipien (Robert Cecil Martin, Kluth 2010) erfühlt und gleichzeitig die Übersetzungslücke zwischen den Kontext-Diagrammen und der Code-Struktur schließt.

**Synthese** ausgewählter Ansätzen:

- Arc42-/C4-/DDD-Diagrammen (C4 nach Brown 2015) für Architekturvisualisierung
- DDD Ubiquitous Language (Evans 2003) für die Ermittlung und fachliche Abgrenzung
- Quasar (Siedersleben 2008) für die fachliche und technische Kategorisierung
- 3 pragmatische Regeln (Robert Bräutigam 2017) für die praktische Realiesierung
- Paketdesign-Prinzipien (Robert Cecil Martin, Kluth 2010) für Qualitätssicherung

**Gegenstand der Betrachtung**:

1. **Abgrenzung der Terminologie nach OOP**
2. **Abgleich mit fundierten Paketdesign-Prinzipien** (von R. C. Martin)
5. **Erweiterte Betrachtung der 3 Regeln** (nach R. Bräutigam) um die **3 Software-Kategorien (A/T/R)** nach Quasar (Siedersleben 2008) für *Pakete* mit klarer Trennung: 
   * A-Software = Business-Concepts = Bounded Contexts (Order, Customer, Payment, Car, Money)
   * R-Software = Adapter Code für Business-Concepts (web, app, ui, database, service, resorce)
   * T-Software = Reine technische Bibliotheken (keine Business oder Adapter-Logik) (http, sftp, kafka, service)
6. **Synthese** der 3 Regeln* (nach R. Bräutigam), 3 Software-Kategorien und Paketdesign-Prinzipien** (von R. C. Martin)
7. **Validierung der Synthese** an verschiedenen Architekturen (DDD, Clean Architecture oder only Java EE-Stack)  
8. **Verifikation der Synthese durch Messwerte** und Vergleiche bei Anwendung auf verschiedenen Architekturen.

---


---

## 7. Referenzen

### Quellen

* **Siedersleben, J.** (2008). *Moderne Softwarearchitektur - Umsichtig planen, robust bauen mit Quasar*. dpunkt.verlag
* **Bräutigam, R.** (2017). *Three Rules for Package Design*. [javadevguy.com](https://javadevguy.com/2017/04/03/three-rules-for-package-design/)
* **Kluth, O.** (2010). *Object-Oriented Design Quality Assessment*

### Tools

* [DesigniteJava](https://www.designite-tools.com/) (Quasar-Kategorien + I-Metrik)
* [ArchUnit](https://www.archunit.org/) (AT-Kombinationsprüfung)
* [JDepend](https://www.clarkware.com/software/JDepend.html) (Abhängigkeitsanalyse) \`



Eine nachhaltige Paketstruktur entsteht nicht einmalig, sondern erfordert **kontinuierliche Anwendung der Prinzipien im Projektkontext**.

