# Universeller Prompt für KI: OOP-Paketstruktur nach axiomatischem, indexbasiertem Regelsystem

## DEFINITIONEN
- **s[0]** = com.company.<app> = globaler Namespace (Ebene n = 0)
- **a[n]** = Abstraktion single Entity auf Ebene n > 0 (Interface/abstrakte Klasse)
- **aa[n.m]** = Abstraktion Collection Entity auf Ebene n, abgeleitet aus a[n] (z.B. Collection/Aggregate Interface)
- **p[n]** = Package mit Implementierungen von a[n] und aa[n.m]

---

## AXIOME

**1. Existenzregel für Abstraktionen:**  
 a[n] ∈ {n[0], n[0].*}  
 aa[n.m] ∈ {n[0], n[0].*}  
 (Jede Einzel- und Aggregat-Abstraktion existiert auf Ebene n im Namespace.)

**2. Existenzregel für Packages:**  
 ∃ a[n] ∨ ∃ aa[n.m] : a[n], aa[n.m] ∈ {n[0], n[0].*} ⇒ p[n] darf existieren  
 (Ein Package entsteht auf gleicher Ebene wie die zugehörigen Abstraktionen und enthält nur deren Implementierungen.)

**3. Inhaltsregel:**  
 ∀ c ∈ p[n] : c ⊑ a[n] ∨ c ⊑ aa[n.m]  
 (Ein Package enthält NUR Implementierungen/Spezialisierungen der zugehörigen Einzel- und Aggregat-Abstraktionen.)

**4. Indexregel für Implementierungen:**  
 Jede Implementierung erhält als Prefix die vollständige Indexkette ihrer Herkunftsabstraktion bzw. des Aggregats:  
 - Einzelabstraktion: `ia[n.m.k].java`  
 - Aggregat: `iaa[n.m.k.l].java`  
 (Indexkette ist rekursiv und eindeutig.)

**5. Rekursionsregel:**  
 Weitere Abstraktionen und deren Implementierungen werden rekursiv nach demselben Schema in Unterpackages `p[n.m]/` abgelegt.  
 (Indexkette erweitert sich bei jeder Ebene.)

---

## Regeln (indexbasiert und rekursiv)

**1. Namespace**
- Der globale Namespace ist `n[0]` <=> p[k=0] (z. B. `com.company.[name]`).

**2. Abstraktionen**
- Jede Abstraktion wird als `a[k].java` bezeichnet und liegt direkt im Namespace der Ebene *k*.
- Aggregat-Abstraktionen werden als `aa[k.n].java` bezeichnet und bekommen als Index die Herkunftsabstraktion *k.n*.

**3. Implementierungen**
- Implementierungen erhalten als Prefix die vollständige Indexkette ihrer Herkunftsabstraktion:
    - Einzelabstraktion: `ia[k.n.m].java`
    - Aggregat: `iaa[k.n.m.l].java`
- Die Indizes werden rekursiv bei tieferen Ebenen erweitert.

**4. Packages für Implementierungen**
- Für jede Abstraktion der Ebene *k* existiert ein (meist gleichnamiges) Package `p[k]/` auf derselben Ebene wie die Abstraktion.
- Das Package `p[k]/` mit k > 0 enthält nur Implementierungen von `a[k]` und Aggregate `aa[k.n]`.

**5. Rekursion und Unterabstraktionen**
- Weitere Abstraktionen (z. B. Unterabstraktionen) werden rekursiv nach dem gleichen Schema in Unterpackages (`p[k.n]/`) abgelegt.
- Die Indexkette wird für jede Unterabstraktion und deren Implementierungen weitergeführt.

---

## Beispielstruktur (beliebige Tiefe)

```
n[0]                                   # globaler Namespace, z.B. com.company.[name]
├── a[1].java                          # Abstraktion Ebene 1
├── aa[1.1].java                       # Aggregat-Abstraktion zu a[1]
├── p[1]/                              # Package für Implementierungen von a[1] und aa[1.1]
│   ├── ia[1.1.1].java                 # 1. Implementierung von a[1.1]
│   ├── ia[1.1.2].java                 # 2. Implementierung von a[1.1]
│   ├── iaa[1.1.1.1].java              # 1. Implementierung von aa[1.1]
│   ├── iaa[1.1.1.2].java              # 2. Implementierung von aa[1.1]
│   ├── a[1.2].java                    # Abstraktion Ebene 2 (Prefix 1)
│   ├── aa[1.2].java                   # Aggregat-Abstraktion Ebene 2
│   └── p[1.2]/                        # Package für Implementierungen von a[1.2] und aa[1.2]
│       ├── ia[1.2.1].java             # 1. Implementierung von a[1.2]
│       ├── iaa[1.2.1.1].java          # 1. Implementierung von aa[1.2]
│       └── ...                        # weitere Tiefe analog
├── p[2]/  
```

---

## Ziel
Gib für beliebige Projektstrukturen (Java, C#, Python, etc.) eine Paketstruktur nach diesen Axiomen und Regeln aus (Syntax an Zielsprache anpassen). Die Regeln sind universell, rekursiv und formal für beliebige OOP-Domänen anwendbar.
