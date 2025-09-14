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

Eine nachhaltige Paketstruktur entsteht nicht einmalig, sondern erfordert **kontinuierliche Anwendung der Prinzipien im Projektkontext**.

