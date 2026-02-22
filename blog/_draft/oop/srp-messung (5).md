# Das Single Responsibility Principle objektiv messen

## Gegenstand der Betrachtung und Zielsetzung

### Gegenstand

Betrachtet wird das **Single Responsibility Principle (SRP)** als Entwurfsprinzip der objektorientierten Softwareentwicklung – insbesondere die Frage, ob und wie es in der täglichen Programmierpraxis operationalisierbar ist. Im Mittelpunkt steht dabei nicht die philosophische Frage, *was* eine Verantwortlichkeit ist, sondern die technische Frage, *wie* sich SRP-Konformität einer Klasse objektiv feststellen lässt.

Als Vergleichsobjekte dienen zwei verbreitete Entwurfsansätze in Java: das **Service-Pattern nach DDD** (fachlich orientierter Klassenschnitt) und das **Decorator-Pattern nach OOP** (kompositionsbasierter Klassenschnitt). Beide Ansätze werden anhand derselben Domäne – einer Bestellverwaltung – implementiert und gegenübergestellt.

### Zielsetzung

Ziel ist es, SRP auf eine **pragmatische und objektive Weise zu verifizieren und im Entwickleralltag einzusetzen**, damit es direkt in unsere tägliche Programmplanung integriert werden kann. Die Definition selbst stammt von **Robert Bräutigam** (MATHEMA Software GmbH), der SRP nicht über subjektive Verantwortlichkeitsbegriffe, sondern über die messbaren Eigenschaften **Kohäsion** und **Kopplung** operationalisiert: `SRP ≡ MAXIMIZE COHESION ∧ MINIMIZE COUPLING`. Dieser Artikel übernimmt Bräutigams Formulierung als Grundlage und zeigt, wie sie mit konkreten Metriken nachweisbar und damit im Code-Review, im CI/CD-Prozess und beim Klassendesign unmittelbar anwendbar wird.

### Methodik und Messverfahren

Um SRP objektiv bewertbar zu machen, werden zwei etablierte Metriken der objektorientierten Softwaremesstechnik eingesetzt:

**LCOM4** (*Lack of Cohesion of Methods, Version 4*) misst die interne Kohäsion einer Klasse über Graphenanalyse: Methoden bilden Knoten, Zugriffe auf gemeinsame Felder oder gegenseitige Aufrufe bilden Kanten. Der LCOM4-Wert entspricht der Anzahl zusammenhängender Teilgraphen. Ein Wert von 1 zeigt maximale Kohäsion an; Werte größer 1 signalisieren, dass die Klasse in mehrere unabhängige Einheiten zerfällt.

**CBO** (*Coupling Between Objects*, Chidamber & Kemerer 1994) zählt die Anzahl externer Klassen, zu denen eine Klasse eine direkte Abhängigkeit unterhält – über Feldtypen, Methodenparameter, Rückgabetypen oder direkte Aufrufe. Ein niedriger CBO-Wert steht für geringe Kopplung und hohe Austauschbarkeit.

Beide Metriken werden für jede Beispielklasse manuell hergeleitet und in Messtabellen dokumentiert. Die Ergebnisse werden abschließend in einer strukturierten Gegenüberstellung konsolidiert, die konzeptionelle SRP-Kriterien direkt mit den gemessenen Metrikwerten verknüpft.

---

## 1. Einleitung: SRP und seine Bedeutung

Das **Single Responsibility Principle (SRP)** ist das erste der fünf SOLID-Prinzipien, die Robert C. Martin (auch bekannt als „Uncle Bob") zu Beginn der 2000er Jahre formuliert hat.

Im Laufe der Zeit haben sich mehrere **traditionelle Definitionen** herausgebildet:

> **Definition 1:** „Jedes Softwaremodul sollte genau eine Aufgabe haben."

> **Definition 2:** „Jedes Softwaremodul sollte genau einen Grund für eine Änderung haben."

> **Definition 3:** „Sammelt die Dinge zusammen, die sich aus denselben Gründen verändern. Trennt die Dinge, die sich aus unterschiedlichen Gründen verändern."

> **Definition 4:** „‚Derselbe Grund' bedeutet, dass er vom selben Geschäftsmann stammt."

**Wirklich???** Diese Definitionen sind subjektiv und schwer anzuwenden!

### Warum wollen wir SRP überhaupt?

Das eigentliche Ziel hinter SRP ist **Wartungsfreundlichkeit**. Um diese zu erreichen, müssen wir:

- den **Arbeitsaufwand für eine gegebene Änderungsmenge minimieren**,
- die **Menge an zu lesendem und schreibendem Code minimieren**.

Daraus folgen konkrete, wünschenswerte Eigenschaften unserer Klassen:

- ✅ Kleine Klassen
- ✅ Hohe Wahrscheinlichkeit lokaler Veränderungen (eine Änderung betrifft nur eine Klasse)
- ✅ Geringe Wahrscheinlichkeit, dass sich eine Änderung auf andere Klassen ausbreitet

SRP ist damit nicht nur ein Designprinzip – es ist ein Qualitätsmerkmal, das langfristig über die Gesundheit einer Codebasis entscheidet.

---

## 2. Das Problem mit der gängigen Interpretation

Die intuitive Interpretation des SRP lautet oft: „Eine Klasse soll nur *eine Sache* tun." Doch diese Formulierung ist gefährlich vage. Was ist „eine Sache"?

- Ist ein `OrderService`, der Bestellungen erstellt, validiert und persistiert, eine oder drei Sachen?
- Ist ein `ReportGenerator`, der Daten aggregiert und formatiert, eine oder zwei Sachen?

Die gängige Interpretation führt zu zwei typischen Extremen:

**Zu grob:** Klassen mit Dutzenden von Methoden werden mit dem Argument gerechtfertigt, sie hätten „nur eine fachliche Verantwortung" (z. B. alles rund um Bestellungen).

**Zu fein:** Im anderen Extrem entstehen Nano-Klassen, die je nur eine einzige Methode enthalten – was zu einer explosionsartigen Proliferation von Klassen führt und die Lesbarkeit ebenfalls zerstört.

Das Kernproblem: **SRP in seiner gängigen Form ist nicht messbar.** Es gibt keinen objektiven Maßstab, der entscheidet, ob eine Klasse zu viel oder zu wenig tut. Die Diskussion bleibt subjektiv, kontext-abhängig und wird in Code-Reviews oft zum Glaubenskrieg. Definition 4 – „derselbe Geschäftsmann" – mag in der Domänenmodellierung hilfreich sein, ist aber als tägliches Programmierkriterium praktisch unbrauchbar.

---

## 3. Die Lösung: Eine objektive Formel für SRP

```
SRP ≡ MAXIMIZE COHESION ∧ MINIMIZE COUPLING
```

Das ist es! **Bei SRP geht es um Kohäsion und Kopplung** – und beide sind messbar.

### Kohäsion und Kopplung – es geht um Abhängigkeiten

Beide Konzepte drehen sich um dasselbe Grundprinzip: **Abhängigkeiten**. Der entscheidende Unterschied ist ihre Richtung:

**Kohäsion** beschreibt Abhängigkeiten *innerhalb* eines Objekts:
- Methoden und Felder beziehen sich aufeinander.
- Abhängigkeiten innerhalb eines Objekts erhöhen dessen Zusammenhalt.

**Kopplung** beschreibt Abhängigkeiten *zwischen* Objekten:
- Objekte hängen von anderen Objekten ab.
- Abhängigkeiten zwischen Objekten führen zu stärkerer Kopplung.

### Was sind Abhängigkeiten?

Es gibt zwei Arten von Abhängigkeiten, die es zu beachten gilt:

**1. Physikalische Beziehungen:** Eine Methode, die eine andere Methode aufruft oder auf eine Variable verweist. Diese Abhängigkeiten sind im Code direkt sichtbar und durch statische Analyse messbar.

**2. Semantische Beziehungen:** Implizites Wissen über die Struktur – zum Beispiel wenn eine Methode bestimmte Annahmen über den Zustand eines anderen Objekts trifft, ohne dass dies explizit im Typsystem ausgedrückt ist. Diese Abhängigkeiten sind schwerer zu erkennen, aber oft die gefährlicheren.

Eine Klasse erfüllt das Single Responsibility Principle genau dann, wenn sie **intern maximal kohäsiv** ist (alle ihre Teile arbeiten eng zusammen an einem gemeinsamen Zweck) **und extern minimal gekoppelt** ist (sie weiß so wenig wie möglich über andere Klassen).

Diese Formel hat entscheidende Vorteile:

- Sie ist **messbar** – es gibt etablierte Metriken für Kohäsion und Kopplung.
- Sie ist **werkzeugunterstützt** – statische Analysetools wie SonarQube, JDepend oder Checkstyle können diese Werte automatisch berechnen.
- Sie ist **designagnostisch** – sie funktioniert unabhängig vom verwendeten Architekturmuster.

---

## 4. Messung von Kohäsion und Kopplung

### 4.1 Kohäsion messen: LCOM

Die gebräuchlichste Metrik für Kohäsion ist **LCOM** (*Lack of Cohesion of Methods*). Es gibt verschiedene Varianten; weit verbreitet ist **LCOM4**.

**LCOM4** modelliert eine Klasse als Graphen: Jede Methode ist ein Knoten, und zwei Methoden sind verbunden, wenn sie auf dasselbe Instanzfeld zugreifen oder sich gegenseitig aufrufen. LCOM4 ist die **Anzahl der zusammenhängenden Komponenten** dieses Graphen.

| LCOM4-Wert | Interpretation |
|---|---|
| **1** | Maximale Kohäsion – alle Methoden sind verbunden |
| **> 1** | Die Klasse zerfällt in mehrere unabhängige Teile |
| **n** | Die Klasse sollte in *n* separate Klassen aufgeteilt werden |

Ein LCOM4-Wert von 1 ist das Ziel: die Klasse ist in sich geschlossen und verhält sich wie eine logische Einheit.

**Beispiel: LCOM4 an einem einfachen Java-Snippet nachvollziehen**

```java
// LCOM4 = 2 — zwei unabhängige Teilgraphen
public class UserManager {

    private String username;   // Feld A
    private String email;      // Feld B
    private int loginCount;    // Feld C
    private Date lastLogin;    // Feld D

    // Gruppe 1: arbeitet nur mit username und email
    public void updateUsername(String newName) {
        this.username = newName;                           // → Feld A
    }

    public String getContactInfo() {
        return username + " <" + email + ">";             // → Feld A, B
    }

    // Gruppe 2: arbeitet nur mit loginCount und lastLogin
    // — keine Verbindung zu Gruppe 1!
    public void recordLogin() {
        this.loginCount++;                                // → Feld C
        this.lastLogin = new Date();                      // → Feld D
    }

    public String getLoginSummary() {
        return "Logins: " + loginCount + ", last: " + lastLogin; // → Feld C, D
    }
}
// updateUsername/getContactInfo → Teilgraph {A,B}
// recordLogin/getLoginSummary  → Teilgraph {C,D}
// Keine Verbindung zwischen den Gruppen → LCOM4 = 2 ❌
// Lösung: aufteilen in UserContactManager und UserLoginTracker
```

```java
// LCOM4 = 1 — alle Methoden sind verbunden ✅
public class UserLoginTracker {

    private int loginCount;  // Feld C
    private Date lastLogin;  // Feld D

    public void recordLogin() {
        this.loginCount++;          // → Feld C
        this.lastLogin = new Date(); // → Feld D
    }

    public String getLoginSummary() {
        return "Logins: " + loginCount + ", last: " + lastLogin; // → Feld C, D
    }
}
// Beide Methoden greifen auf loginCount und lastLogin zu
// → ein zusammenhängender Graph → LCOM4 = 1 ✅
```

---

### 4.2 Kopplung messen: CBO

Die Standardmetrik für Kopplung ist **CBO** (*Coupling Between Objects*), definiert nach Chidamber & Kemerer (1994).

CBO zählt die **Anzahl der Klassen**, mit denen eine gegebene Klasse eine direkte Abhängigkeit hat – sei es durch Methodenaufrufe, Feldtypen, Parameter oder Rückgabetypen.

| CBO-Wert | Interpretation |
|---|---|
| **0–5** | Geringe Kopplung – wartungsfreundlich |
| **6–10** | Moderate Kopplung – beobachtenswert |
| **> 10** | Hohe Kopplung – Redesign empfohlen |

Ergänzend kann **Ca** (*Afferent Coupling*) und **Ce** (*Efferent Coupling*) aus dem Package-Level betrachtet werden, um systemweite Abhängigkeiten zu beurteilen. Die Instability-Metrik `I = Ce / (Ca + Ce)` gibt an, wie stabil ein Modul gegenüber Änderungen ist.

**Beispiel: CBO an einem Java-Snippet nachvollziehen**

```java
// CBO = 5 — direkte Abhängigkeiten auf 5 externe Typen ❌
public class ReportService {

    private final ReportRepository repository;  // Abhängigkeit 1 → CBO +1
    private final PdfExporter pdfExporter;      // Abhängigkeit 2 → CBO +1

    // Parameter- und Rückgabetypen zählen ebenfalls!
    public Report generateReport(DataQuery query) {      // DataQuery → CBO +1
        List<DataRow> rows = repository.fetch(query);    // DataRow   → CBO +1
        return pdfExporter.export(rows);                 // Report    → CBO +1
    }
}
// Gezählte Typen: ReportRepository, PdfExporter, DataQuery, DataRow, Report → CBO = 5
```

Zum Vergleich eine entkoppeltere Variante durch Abhängigkeitsumkehr (DIP):

```java
// CBO = 2 — Abhängigkeit nur auf Interface-Abstraktionen ✅
public class ReportService {

    private final ReportRepository repository; // Interface → CBO +1
    private final Exporter exporter;           // Interface → CBO +1

    public void generateReport(Query query) {
        exporter.export(repository.fetch(query));
    }
}
// Konkrete Typen (DataRow, Report, DataQuery) sind hinter Interfaces verborgen
// → CBO sinkt von 5 auf 2, ohne Funktionalitätsverlust ✅
```

Die Reduktion von CBO = 5 auf CBO = 2 entsteht nicht durch weniger Funktionalität, sondern durch **Abhängigkeitsumkehr**: Statt auf konkrete Klassen zeigt `ReportService` nur noch auf stabile Interfaces.

---

## 5. Was bedeutet „Maximale Kohäsion" und „Minimale Kopplung"?

### Maximale Kohäsion

Maximale Kohäsion bedeutet, dass **jede Methode der Klasse auf (nahezu) alle Instanzvariablen der Klasse zugreift**. Die Klasse ist dann eine untrennbare Einheit – ihre Methoden sind nicht unabhängig voneinander lauffähig und adressieren gemeinsam genau einen Zweck.

Praktisch gesprochen: Wenn man eine Methode einer kohäsiven Klasse entfernt oder verschiebt, verliert die Klasse an Vollständigkeit. Die Methoden sind keine zufällige Sammlung, sondern ein kohärentes Protokoll.

Maximale Kohäsion schließt auch aus, dass eine Klasse versteckte Unter-Klassen enthält: Wenn zwei Methodengruppen völlig unabhängig voneinander agieren (LCOM4 > 1), dann hat die Klasse de facto zwei Verantwortlichkeiten – unabhängig davon, wie man sie fachlich benennt.

### Minimale Kopplung

Minimale Kopplung bedeutet, dass die Klasse **so wenig wie möglich von der Außenwelt wissen muss**. Sie kennt nur die Abstraktionen, die sie wirklich braucht – keine konkreten Implementierungen, keine transitiven Abhängigkeiten, keine God-Objects.

Minimale Kopplung ist dabei nicht gleichbedeutend mit **keiner Kopplung** (CBO = 0 ist in realer Software nicht erreichbar). Es geht darum, Abhängigkeiten bewusst zu minimieren und ausschließlich über stabile Interfaces zu kommunizieren.

Zusammen erzwingen beide Bedingungen eine Klasse, die **intern konsistent** ist (tut tatsächlich nur eine Sache), **extern abgegrenzt** ist (kennt nur das, was sie braucht), und damit **testbar, austauschbar und verständlich** ist.

---

## 6. Java-Beispiele mit Messung (Service Pattern vs. Decorator Pattern)

Um die Formel greifbar zu machen, vergleichen wir zwei gängige Entwurfsmuster: das **Service-Pattern nach DDD** und das **Decorator-Pattern nach OOP**.

### 6.1 Service Pattern (DDD) – häufig SRP-verletzend

```java
// OrderService nach typischem DDD-Service-Schnitt
public class OrderService {

    private final OrderRepository orderRepository;      // Abhängigkeit 1
    private final InventoryService inventoryService;    // Abhängigkeit 2
    private final PaymentGateway paymentGateway;        // Abhängigkeit 3
    private final EmailNotifier emailNotifier;          // Abhängigkeit 4
    private final AuditLogger auditLogger;              // Abhängigkeit 5

    public Order createOrder(Cart cart, Customer customer) {
        inventoryService.reserve(cart);                      // nutzt inventoryService
        Order order = new Order(cart, customer);
        orderRepository.save(order);                         // nutzt orderRepository
        auditLogger.log("Order created: " + order.getId()); // nutzt auditLogger
        return order;
    }

    public void processPayment(Order order) {
        paymentGateway.charge(order);                            // nutzt paymentGateway
        order.markAsPaid();
        orderRepository.save(order);                             // nutzt orderRepository
        emailNotifier.sendConfirmation(order);                   // nutzt emailNotifier
        auditLogger.log("Payment processed: " + order.getId()); // nutzt auditLogger
    }

    public void cancelOrder(Order order) {
        inventoryService.release(order);                       // nutzt inventoryService
        order.cancel();
        orderRepository.save(order);                           // nutzt orderRepository
        emailNotifier.sendCancellation(order);                 // nutzt emailNotifier
        auditLogger.log("Order cancelled: " + order.getId()); // nutzt auditLogger
    }
}
```

**Messung:**

| Metrik | Wert | Bewertung |
|---|---|---|
| CBO | **5** | Moderat – aber jede Methode zieht andere Dependencies |
| LCOM4 | **3** | 3 unabhängige Methodengruppen (Erstellen / Bezahlen / Stornieren) |

**Analyse:** Obwohl der `OrderService` fachlich „alles rund um Bestellungen" macht, zeigt LCOM4 = 3, dass die Klasse in drei unabhängige Einheiten zerfällt. `createOrder` und `processPayment` teilen sich nur `orderRepository` und `auditLogger` – sie sind keine kohäsive Einheit. **SRP ist verletzt.**

---

### 6.2 Decorator Pattern (OOP) – SRP-konform

Das Decorator-Pattern löst das Problem durch **Separation of Concerns über Komposition**. Jede Klasse hat genau eine Verantwortung, und Querschnittsbelange (Logging, Benachrichtigung) werden als Dekoratoren umgesetzt. Beachte die Benennung: Das Interface heißt `Order` (fachlicher Begriff), die Implementierungen tragen einen beschreibenden Präfix – kein `*Service`-Suffix, das eine technische Schicht suggeriert.

```java
// Fachliches Interface: vertikaler Schnitt entlang der Domäne
public interface Order {
    Order create(Cart cart, Customer customer);
    void processPayment(PaymentGateway gateway);
    void cancel();
}
```

```java
// Kern-Verantwortung: nur SQL / Persistenzlogik
public class DefaultOrder implements Order {

    private final OrderRepository orderRepository;    // Abhängigkeit 1
    private final InventoryService inventoryService;  // Abhängigkeit 2

    public Order create(Cart cart, Customer customer) {
        inventoryService.reserve(cart);
        Order order = new DefaultOrder(cart, customer, orderRepository, inventoryService);
        orderRepository.save(order);
        return order;
    }

    public void processPayment(PaymentGateway gateway) {
        gateway.charge(this);
        orderRepository.save(this);
    }

    public void cancel() {
        inventoryService.release(this);
        orderRepository.save(this);
    }
}
```

```java
// Dekorator: nur Audit-Logging-Verantwortung
public class AuditingOrder implements Order {

    private final Order delegate;           // Abhängigkeit 1
    private final AuditLogger auditLogger; // Abhängigkeit 2

    public Order create(Cart cart, Customer customer) {
        Order order = delegate.create(cart, customer);
        auditLogger.log("Order created: " + order.getId());
        return new AuditingOrder(order, auditLogger);
    }

    public void processPayment(PaymentGateway gateway) {
        delegate.processPayment(gateway);
        auditLogger.log("Payment processed: " + delegate.getId());
    }

    public void cancel() {
        delegate.cancel();
        auditLogger.log("Order cancelled: " + delegate.getId());
    }
}
```

```java
// Dekorator: nur E-Mail-Benachrichtigungs-Verantwortung
public class NotifiedOrder implements Order {

    private final Order delegate;              // Abhängigkeit 1
    private final EmailNotifier emailNotifier; // Abhängigkeit 2

    public Order create(Cart cart, Customer customer) {
        return new NotifiedOrder(delegate.create(cart, customer), emailNotifier);
    }

    public void processPayment(PaymentGateway gateway) {
        delegate.processPayment(gateway);
        emailNotifier.sendConfirmation(this);
    }

    public void cancel() {
        delegate.cancel();
        emailNotifier.sendCancellation(this);
    }
}
```

**Messung:**

| Klasse | CBO | LCOM4 | Bewertung |
|---|---|---|---|
| `DefaultOrder` | **2** | **1** | ✅ Maximale Kohäsion, minimale Kopplung |
| `AuditingOrder` | **2** | **1** | ✅ Alle Methoden nutzen `delegate` und `auditLogger` |
| `NotifiedOrder` | **2** | **1** | ✅ Alle Methoden nutzen `delegate` und `emailNotifier` |

**Analyse:** Jede Klasse hat LCOM4 = 1 und CBO = 2. Die Methoden jeder Klasse sind kohäsiv verbunden – sie teilen sich alle verfügbaren Felder. Die Kopplung ist auf das absolute Minimum reduziert. **SRP ist in allen drei Klassen erfüllt.**

Die Komposition erfolgt z. B. im DI-Container oder einer Factory-Methode:

```java
Order order = new NotifiedOrder(
    new AuditingOrder(
        new DefaultOrder(orderRepository, inventoryService),
        auditLogger
    ),
    emailNotifier
);
```

---

## 7. Gegenüberstellung: Konzept (Abschnitt 5) vs. Messung (Abschnitt 6)

Die folgende Tabelle verbindet die konzeptionellen Anforderungen aus Abschnitt 5 mit den konkreten Messergebnissen aus Abschnitt 6 und zeigt, wie Theorie und Praxis zusammenpassen:

| Konzept (Abschnitt 5) | Metrik | Zielwert | OrderService (DDD) | Decorator-Klassen (OOP) |
|---|---|---|---|---|
| **Maximale Kohäsion:** Alle Methoden greifen auf alle Felder zu | LCOM4 | = 1 | ❌ LCOM4 = **3** (3 Teilgraphen) | ✅ LCOM4 = **1** (je Klasse) |
| **Minimale Kopplung:** Nur notwendige externe Abhängigkeiten | CBO | ≤ 5 | ⚠️ CBO = **5** (alle 5 Felder je Methode gemischt) | ✅ CBO = **2** (je Klasse) |
| **Hohe Wahrscheinlichkeit lokaler Änderung** | LCOM4 | = 1 | ❌ Logging-Änderung betrifft alle 3 Methoden des Service | ✅ `AuditingOrder` isoliert diese Änderung vollständig |
| **Geringe Ausbreitung von Änderungen** | CBO | niedrig | ❌ Änderung an `PaymentGateway` trifft den gesamten `OrderService` | ✅ Nur `DefaultOrder` ist betroffen |
| **Kleine, verständliche Klassen** | Methoden × Felder | wenige | ⚠️ 3 Methoden × 5 Felder = 15 Kombinationen | ✅ 3 Methoden × 2 Felder = 6 Kombinationen |
| **Testbarkeit** | Mocks pro Unit-Test | gering | ❌ 5 Mocks nötig, um eine einzelne Methode zu testen | ✅ 2 Mocks pro Unit-Test ausreichend |

**Fazit der Gegenüberstellung:** Der DDD-Service-Schnitt liefert zwar eine fachlich verständliche Benennung, versagt aber auf allen messbaren Achsen. Die Decorator-Zerlegung erfüllt dagegen jedes konzeptionelle Kriterium aus Abschnitt 5 – und das ist durch die Metriken objektiv nachweisbar, nicht nur gefühlt.

---

## 8. Zusammenfassung und Fazit

Die gängige SRP-Definition „eine Klasse soll nur eine Sache tun" ist ein nützlicher Einstieg, aber kein operationalisierbarer Maßstab. Sie lädt zu Interpretation ein und führt in der Praxis zu Designstreitigkeiten ohne objektiven Ausweg.

Die Formel **SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung** überbrückt diese Lücke. Sie übersetzt ein qualitatives Prinzip in zwei quantitativ messbare Größen: **LCOM4 = 1** signalisiert maximale Kohäsion – die Klasse ist eine geschlossene Einheit. **CBO so niedrig wie möglich** signalisiert minimale Kopplung – die Klasse kennt nur das, was sie wirklich braucht.

Das Decorator-Pattern zeigt exemplarisch, wie konsequente OOP-Komposition zu Klassen führt, die beide Bedingungen erfüllen – während der monolithische DDD-Service-Schnitt trotz fachlicher Einheitlichkeit messbar gegen SRP verstößt (LCOM4 = 3).

Der praktische Mehrwert dieser Sichtweise ist dreifach: Sie ermöglicht **automatisierte Qualitätssicherung** (LCOM4 und CBO als CI/CD-Quality-Gates in SonarQube), macht **Code Reviews produktiver** (konkrete Zahlen statt subjektiver Debatten) und fördert **besseres Design von Anfang an** (wer Kohäsion und Kopplung im Blick hat, denkt automatisch in kleinen, abgegrenzten Einheiten).

SRP ist kein Dogma, sondern ein Werkzeug. Mit der richtigen Metrik wird es zu einem, das man tatsächlich benutzen kann.

---

## Quellen

### Hauptquelle

Die Kernthese dieses Artikels – SRP als Äquivalenz von maximaler Kohäsion und minimaler Kopplung – basiert auf dem Vortrag von **Robert Bräutigam** (MATHEMA Software GmbH):

- Bräutigam, R. *Single Responsibility Principle*. SpeakerDeck. https://speakerdeck.com/robertbraeutigam/single-responsibility-principle

Der Vortrag entwickelt die pragmatische SRP-Definition, die Kritik an traditionellen Definitionen sowie die Implikationen für MVC, Layered Architectures und DDD – allesamt als Grundlage für die in diesem Artikel präsentierten Überlegungen.

### Bücher und weitere Quellen

- Martin, R. C. (2003). *Agile Software Development: Principles, Patterns, and Practices*. Prentice Hall. *(Erstformulierung der SOLID-Prinzipien inkl. SRP)*
- Martin, R. C. (2008). *Clean Code: A Handbook of Agile Software Craftsmanship*. Prentice Hall. *(Kapitel zu Klassen und SRP)*
- Martin, R. C. (2017). *Clean Architecture: A Craftsman's Guide to Software Structure and Design*. Prentice Hall. *(Erweiterung von SRP auf Architekturebene, Definition 3 und 4)*
- Chidamber, S. R., & Kemerer, C. F. (1994). *A Metrics Suite for Object Oriented Design*. IEEE Transactions on Software Engineering, 20(6), 476–493. *(Originaldefinition von CBO und weiteren OO-Metriken)*
- Martin, R. C. (2002). *Agile Principles, Patterns, and Practices in C#*. Prentice Hall. *(Package-Metriken Ca, Ce, Instability)*

### Metrikdefinitionen und Werkzeuge

- Hitz, M., & Montazeri, B. (1995). *Measuring Coupling and Cohesion in Object-Oriented Systems*. Proceedings of the International Symposium on Applied Corporate Computing. *(Grundlage für LCOM-Varianten)*
- Briand, L. C., Daly, J. W., & Wüst, J. (1999). *A Unified Framework for Coupling Measurement in Object-Oriented Systems*. IEEE Transactions on Software Engineering, 25(1), 91–121.
- SonarQube Dokumentation: *Cognitive Complexity and LCOM metrics*. https://docs.sonarsource.com
- JDepend: *Package Dependency Analysis for Java*. https://github.com/clarkware/jdepend

### Entwurfsmuster

- Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley. *(Decorator Pattern – Kapitel 4)*
- Evans, E. (2003). *Domain-Driven Design: Tackling Complexity in the Heart of Software*. Addison-Wesley. *(Service Pattern im DDD-Kontext)*

### Weiterführende Artikel

- Martin, R. C. (2014). *The Single Responsibility Principle*. https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html
- Fowler, M. *BoundedContext*. Martin Fowler Blog. https://martinfowler.com/bliki/BoundedContext.html
