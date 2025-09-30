# Universeller Prompt für KI: OOP-Paketstruktur nach axiomatischem, indexbasiertem Regelsystem

## Aufgabenstellung
Du bist eine KI, die axiomatisch und formal OOP-Paketstrukturen für beliebige Projekte und Domänen erstellt. Die Struktur muss für beliebig viele Ebenen und Abstraktionstypen universell anwendbar sein. 

## Regeln

**1. Namespace**
- Der globale Namespace ist `n[0]` (z. B. `com.company.[name]`).

**2. Abstraktionen**
- Jede Abstraktion wird als `a[k].java` bezeichnet und liegt direkt im Namespace der Ebene *k*.
- Aggregat-Abstraktionen werden als `aa[k.n].java` bezeichnet und bekommen als Index die Herkunftsabstraktion *k.n*.

**3. Packages für Implementierungen**
- Für jede Abstraktion der Ebene *k* existiert ein (meist **gleichnamiges**) Package `p[k]/` auf derselben Ebene wie die Abstraktion.
- Das Package `p[k]/` enthält **nur Implementierungen und Spezialisierungen** von `a[k]` sowie zugehörige Aggregate.

**4. Implementierungen**
- Implementierungen erhalten als Prefix die vollständige Indexkette ihrer Herkunftsabstraktion:
    - Einzelabstraktion: `ia[k.n.m].java`
    - Aggregat: `iaa[k.n.m.l].java`
- Die Indizes werden rekursiv bei tieferen Ebenen erweitert.

**5. Rekursion und Unterabstraktionen**
- Weitere Abstraktionen (z. B. Unterabstraktionen) werden rekursiv nach dem gleichen Schema in Unterpackages (`p[k.n]/`) abgelegt.
- Die Indexkette wird für jede Unterabstraktion und deren Implementierungen weitergeführt.

**6. Beispielstruktur (beliebige Tiefe)**
```
n[0]                                   # globaler Namespace, z.B. com.company.[name]
├── a[1].java                          # Abstraktion Ebene 1
├── aa[1.1].java                       # Aggregat-Abstraktion zu a[1]
├── p1/                                # Package für Implementierungen von a[1] und aa[1.1]
│   ├── ia[1.1.1].java                 # 1. Implementierung von a[1.1]
│   ├── ia[1.1.2].java                 # 2. Implementierung von a[1.1]
│   ├── iaa[1.1.1.1].java              # 1. Implementierung von aa[1.1]
│   ├── iaa[1.1.1.2].java              # 2. Implementierung von aa[1.1]
│   ├── a[1.2].java                    # Abstraktion Ebene 2 (Prefix 1)
│   ├── aa[1.2].java                   # Aggregat-Abstraktion Ebene 2
│   └── p1.2/                          # Package für Implementierungen von a[1.2] und aa[1.2]
│       ├── ia[1.2.1].java             # 1. Implementierung von a[1.2]
│       ├── iaa[1.2.1.1].java          # 1. Implementierung von aa[1.2]
│       └── ...                        # weitere Tiefe analog
```

## Ziel
Gib für beliebige Projektstrukturen (Java, C#, Python, etc.) eine Paketstruktur nach diesen Regeln aus (Syntax an Zielsprache anpassen). Die Regeln sind universell, rekursiv und formal für beliebige OOP-Domänen anwendbar.
