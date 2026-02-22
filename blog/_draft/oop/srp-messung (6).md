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

**Kohäsion** beschreibt Abhängigkeiten *innerhalb* eines Objekts: Methoden und Felder beziehen sich aufeinander, Abhängigkeiten innerhalb eines Objekts erhöhen dessen Zusammenhalt.

**Kopplung** beschreibt Abhängigkeiten *zwischen* Objekten: Objekte hängen von anderen Objekten ab, Abhängigkeiten zwischen Objekten führen zu stärkerer Kopplung.

### Was sind Abhängigkeiten?

Es gibt zwei Arten von Abhängigkeiten, die es zu beachten gilt:

**1. Physikalische Beziehungen:** Eine Methode, die eine andere Methode aufruft oder auf eine Variable verweist. Diese Abhängigkeiten sind im Code direkt sichtbar und durch statische Analyse messbar.

**2. Semantische Beziehungen:** Implizites Wissen über die Struktur – zum Beispiel wenn eine Klasse die interne Repräsentation einer anderen kennt und darauf operiert, ohne dass dies explizit im Typsystem ausgedrückt ist. Semantische Kopplung ist laut Bräutigam *deutlich gefährlicher* als physikalische Kopplung, da sie für den Compiler unsichtbar ist – Änderungen propagieren durch sie hindurch auf subtile und unerwartete Weise, fast immer begünstigt durch Getter-Methoden (siehe Abschnitt 3.1).

Eine Klasse erfüllt das Single Responsibility Principle genau dann, wenn sie **intern maximal kohäsiv** ist (alle ihre Teile arbeiten eng zusammen an einem gemeinsamen Zweck) **und extern minimal gekoppelt** ist (sie weiß so wenig wie möglich über andere Klassen).

Diese Formel hat entscheidende Vorteile: Sie ist **messbar** (es gibt etablierte Metriken für physikalische Kohäsion und Kopplung), **werkzeugunterstützt** (SonarQube, JDepend und Checkstyle können diese Werte automatisch berechnen) und **designagnostisch** (sie funktioniert unabhängig vom verwendeten Architekturmuster).

### 3.1 Exkurs: Semantische Kopplung und warum Getter ein Code Smell sind

Semantische Kopplung entzieht sich der automatischen Messung durch LCOM4 oder CBO – sie ist dennoch die häufigere und gefährlichere Kopplungsform in der Praxis. Ein klassisches Beispiel:

```java
// Semantische Kopplung: CurrencyConverter kennt zu viel über Amount
public final class CurrencyConverter {
    public Amount convert(Amount amount, Currency currency) {
        return new Amount(
            amount.getValue() *                          // kennt interne Repräsentation
                exchange.getDailyRate(
                    amount.getCurrency(), currency),     // kennt interne Repräsentation
            currency);
    }
}
```

`CurrencyConverter` greift über Getter auf die interne Struktur von `Amount` zu. Ändert sich `Amount` – etwa durch eine neue interne Darstellung oder einen geänderten Getter-Vertrag – muss auch `CurrencyConverter` angepasst werden, obwohl kein direkter physikalischer Aufruf vorliegt. Die Klassen sind **semantisch gekoppelt**, auch wenn CBO dies nicht vollständig erfasst.

Die SRP-konforme Alternative: Die Konvertierungslogik gehört in `Amount` selbst oder wird über ein Interface delegiert – so bleibt das Wissen über die interne Struktur gekapselt.

> **Faustregel:** Jeder Getter ist ein potenzieller Einstiegspunkt für semantische Kopplung. Getter sind kein Zeichen guten Designs, sondern ein Warnsignal, dass Verantwortlichkeiten möglicherweise falsch verteilt sind.

---

## 4. Messung von Kohäsion und Kopplung

### 4.1 Kohäsion messen: LCOM4

Die gebräuchlichste Metrik für Kohäsion ist **LCOM** (*Lack of Cohesion of Methods*). Es gibt verschiedene Varianten; weit verbreitet ist **LCOM4**.

**LCOM4** modelliert eine Klasse als Graphen: Jede Methode ist ein Knoten, und zwei Methoden sind verbunden, wenn sie auf dasselbe Instanzfeld zugreifen oder sich gegenseitig aufrufen. LCOM4 ist die **Anzahl der zusammenhängenden Komponenten** dieses Graphen.

| LCOM4-Wert | Interpretation |
|---|---|
| **1** | Maximale Kohäsion – alle Methoden sind verbunden |
| **> 1** | Die Klasse zerfällt in mehrere unabhängige Teile |
| **n** | Die Klasse sollte in *n* separate Klassen aufgeteilt werden |

Ein LCOM4-Wert von 1 ist das Ziel: die Klasse ist in sich geschlossen und verhält sich wie eine logische Einheit.

**Beispiel: LCOM4 an der Order-Domäne nachvollziehen**

```java
// LCOM4 = 2 — zwei unabhängige Teilgraphen in einer Klasse
public class OrderData {

    private Cart cart;            // Feld A
    private Customer customer;    // Feld B
    private int paymentAmount;    // Feld C
    private String paymentStatus; // Feld D

    // Gruppe 1: arbeitet nur mit cart und customer
    public String getOrderSummary() {
        return customer.getName() + ": " + cart.itemCount() + " items"; // → Feld A, B
    }

    public void updateCustomer(Customer c) {
        this.customer = c; // → Feld B
    }

    // Gruppe 2: arbeitet nur mit paymentAmount und paymentStatus
    // — keine Verbindung zu Gruppe 1!
    public void recordPayment(int amount) {
        this.paymentAmount = amount;      // → Feld C
        this.paymentStatus = "PAID";      // → Feld D
    }

    public String getPaymentInfo() {
        return paymentStatus + ": " + paymentAmount; // → Feld C, D
    }
}
// getOrderSummary/updateCustomer → Teilgraph {A, B}
// recordPayment/getPaymentInfo   → Teilgraph {C, D}
// Keine Verbindung zwischen den Gruppen → LCOM4 = 2 ❌
// Lösung: aufteilen in OrderIdentity und OrderPayment
```

```java
// LCOM4 = 1 — alle Methoden sind verbunden ✅
public class OrderPayment {

    private int paymentAmount;    // Feld C
    private String paymentStatus; // Feld D

    public void recordPayment(int amount) {
        this.paymentAmount = amount;  // → Feld C
        this.paymentStatus = "PAID";  // → Feld D
    }

    public String getPaymentInfo() {
        return paymentStatus + ": " + paymentAmount; // → Feld C, D
    }
}
// Beide Methoden greifen auf paymentAmount und paymentStatus zu
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

## 6. Exkurs: Don't do Utils – das `PasswordHash`-Beispiel

Ein häufig übersehener SRP-Verstoß ist die Verwendung statischer Utility-Klassen. Sie wirken harmlos, sind aber ein klares Zeichen dafür, dass Verantwortlichkeiten nicht bei den Objekten liegen, sondern aus ihnen herausgezogen wurden.

```java
// ❌ Typisches Anti-Pattern: HashUtils als statische Utility-Klasse
public final class HashUtils {
    public static String hash(String clearText) { ... }
    public static boolean match(String clearText, String hash) { ... }
}

// Verwendung: Wissen über Hashing liegt AUSSERHALB des Passwort-Kontexts
String hash = HashUtils.hash(password);
boolean valid = HashUtils.match(input, storedHash);
```

Diese Klasse hat **keine Kohäsion** im LCOM4-Sinne (keine Instanzfelder, nur statische Methoden) und erzeugt **semantische Kopplung** überall dort, wo sie verwendet wird. Der Aufrufer muss wissen, wie Passwörter gehasht werden – das ist eine versteckte Verantwortlichkeit.

Die SRP-konforme Alternative überführt das Verhalten in ein echtes Objekt:

```java
// ✅ SRP-konform: PasswordHash als eigenständiges Wertobjekt
public final class PasswordHash {

    private final byte[] salt;   // Feld A
    private final byte[] hash;   // Feld B

    public static PasswordHash compute(String clearText) {
        byte[] randomSalt = ...;
        return compute(randomSalt, clearText);    // → Feld A, B
    }

    public static PasswordHash compute(byte[] salt, String clearText) {
        byte[] calculatedHash = ...;
        return new PasswordHash(salt, calculatedHash); // → Feld A, B
    }

    public boolean matches(String clearText) {
        return this.equals(new PasswordHash(salt, clearText)); // → Feld A, B
    }

    public boolean equals(Object o) {
        ...
        return Arrays.equals(this.hash, ((PasswordHash) o).hash); // → Feld B
    }
}
```

**Messung:**

| Metrik | Wert | Bewertung |
|---|---|---|
| LCOM4 | **1** | ✅ Alle Methoden operieren auf `salt` und/oder `hash` |
| CBO | **1** | ✅ Abhängigkeit nur auf `Arrays` (JDK) |

Das Wissen über Passwort-Hashing ist vollständig in `PasswordHash` gekapselt. Kein Aufrufer muss noch wissen, wie ein Hash berechnet oder verglichen wird – semantische Kopplung ist eliminiert.

> **Regel:** Wenn eine Klasse das Wort *Utils*, *Helper* oder *Manager* im Namen trägt und ausschließlich statische Methoden enthält, ist sie fast immer ein SRP-Verstoß. Das Verhalten gehört in das Objekt, das die Daten besitzt.

---

## 7. Java-Beispiele mit Messung (Service Pattern vs. Decorator Pattern)

Um die Formel greifbar zu machen, vergleichen wir zwei gängige Entwurfsmuster: das **Service-Pattern nach DDD** und das **Decorator-Pattern nach OOP**.

### 7.1 Service Pattern (DDD) – häufig SRP-verletzend

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

### 7.2 Decorator Pattern (OOP) – SRP-konform

Das Decorator-Pattern löst das Problem durch **Separation of Concerns über Komposition**. Jede Klasse hat genau eine Verantwortung, und Querschnittsbelange (Logging, Benachrichtigung) werden als Dekoratoren umgesetzt.

> **Hinweis zur Benennung:** Das Interface heißt `Order` – ein fachlicher Begriff ohne technisches Suffix. Die Implementierungen tragen einen beschreibenden Präfix (`Default`, `Auditing`, `Notified`). Kein `*Service`-Suffix, das eine technische Schicht suggeriert und den Blick auf die eigentliche Verantwortlichkeit verstellt.

```java
// Fachliches Interface: vertikaler Schnitt entlang der Domäne
public interface Order {
    Order create(Cart cart, Customer customer);
    void processPayment(PaymentGateway gateway);
    void cancel();
}
```

```java
// Kern-Verantwortung: nur Persistenz- und Inventarlogik
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

### 7.3 Gegenüberstellung: Konzept vs. Messung

Die folgende Tabelle verbindet die konzeptionellen Anforderungen aus Abschnitt 5 direkt mit den Messergebnissen aus Abschnitt 7.1 und 7.2:

| Konzept | Metrik | Zielwert | OrderService (DDD) | Decorator-Klassen (OOP) |
|---|---|---|---|---|
| **Maximale Kohäsion:** Alle Methoden greifen auf alle Felder zu | LCOM4 | = 1 | ❌ LCOM4 = **3** (3 Teilgraphen) | ✅ LCOM4 = **1** (je Klasse) |
| **Minimale Kopplung:** Nur notwendige externe Abhängigkeiten | CBO | ≤ 5 | ⚠️ CBO = **5** (gemischt je Methode) | ✅ CBO = **2** (je Klasse) |
| **Hohe Wahrscheinlichkeit lokaler Änderung** | LCOM4 | = 1 | ❌ Logging-Änderung betrifft alle 3 Methoden | ✅ `AuditingOrder` isoliert diese Änderung vollständig |
| **Geringe Ausbreitung von Änderungen** | CBO | niedrig | ❌ Änderung an `PaymentGateway` trifft den gesamten Service | ✅ Nur `DefaultOrder` ist betroffen |
| **Kleine, verständliche Klassen** | Methoden × Felder | wenige | ⚠️ 3 Methoden × 5 Felder = 15 Kombinationen | ✅ 3 Methoden × 2 Felder = 6 Kombinationen |
| **Testbarkeit** | Mocks pro Unit-Test | gering | ❌ 5 Mocks nötig pro Methode | ✅ 2 Mocks pro Unit-Test ausreichend |

**Fazit:** Der DDD-Service-Schnitt liefert zwar eine fachlich verständliche Benennung, versagt aber auf allen messbaren Achsen. Die Decorator-Zerlegung erfüllt dagegen jedes konzeptionelle Kriterium – objektiv nachweisbar, nicht nur gefühlt.

---

## 8. Konsequenzen für Architekturmuster

SRP ist keine reine Klassen-Mikro-Entscheidung – es hat systemweite Auswirkungen auf Architekturmuster. Zwei Beispiele, bei denen die pragmatische SRP-Definition zu überraschenden Schlussfolgerungen führt:

### 8.1 Layered Architecture

Die klassische Schichtenarchitektur (UI → Application → Domain → Infrastructure) trennt technische Belange in horizontale Schichten. Das klingt nach SRP, ist es aber nicht: Schichten sind zwar thematisch getrennt, aber **strukturell stark gekoppelt**. DTOs und Datentransfer-Objekte reichen Daten von Schicht zu Schicht – jede Schicht kennt die Datenstrukturen der nächsten.

Die Konsequenz: Ändert sich ein Domain-Objekt, breitet sich die Änderung durch alle darüberliegenden Schichten aus. **Eine einzelne Änderung betrifft zwangsläufig mehrere Klassen** – ein direkter Verstoß gegen das SRP-Ziel lokaler Änderbarkeit. Die Antwort auf die Frage „Wie viele Klassen müssen sich ändern, wenn ein Domain-Objekt leicht modifiziert wird?" ist in einer Layered Architecture fast immer: mehr als eine.

### 8.2 MVC und die Frage der UI-Verantwortung

Das klassische MVC-Pattern trennt Model, View und Controller – scheinbar sauber. Doch in der gängigen Interpretation ist das **Model ein passives Datenobjekt mit Gettern und Settern**, auf das sowohl Controller als auch View direkt zugreifen. Beide sind damit semantisch an das Model gekoppelt: Ändert sich das Model, ändern sich Controller und View mit.

Die SRP-konforme Alternative: Das Model-Objekt selbst trägt die Verantwortung, sich darzustellen – es liefert eine UI-Komponente zurück, ohne deren Implementierungsdetails zu kennen:

```java
public final class Order {
    private final Cart cart;
    private final Customer customer;

    // Order weiß, wie es sich darstellt — aber nicht wie die Komponente intern aussieht
    public Component displaySummary() {
        return new InfoPanel()
            .addInfo("Kunde", customer.getName())
            .addInfo("Artikel", cart.itemCount());
    }
}
```

`Order` kennt keine HTML-Tags, keine CSS-Klassen – es delegiert die Darstellung an stabile UI-Abstraktionen. Die Verantwortung bleibt beim Objekt, das die Daten besitzt. Änderungen an der Darstellung sind lokal zu `Order` – keine Ausbreitung in Controller oder View.

---

## 9. Zusammenfassung und Fazit

Die gängige SRP-Definition „eine Klasse soll nur eine Sache tun" ist ein nützlicher Einstieg, aber kein operationalisierbarer Maßstab. Sie lädt zu Interpretation ein und führt in der Praxis zu Designstreitigkeiten ohne objektiven Ausweg.

Die Formel **SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung** nach Robert Bräutigam überbrückt diese Lücke. Sie übersetzt ein qualitatives Prinzip in zwei quantitativ messbare Größen: **LCOM4 = 1** signalisiert maximale Kohäsion, **CBO so niedrig wie möglich** signalisiert minimale Kopplung.

Vier praktische Leitlinien lassen sich daraus ableiten:

- **Physikalische Kohäsion sicherstellen:** Methoden und Felder sollen sich aufeinander beziehen. LCOM4 > 1 ist ein direkter Refactoring-Auftrag.
- **Physikalische Kopplung begrenzen:** CBO möglichst niedrig halten. Abhängigkeiten nur auf stabile Interfaces, nicht auf konkrete Implementierungen.
- **Semantische Kopplung vermeiden:** Getter sind ein Warnsignal. Wer Daten aus einem Objekt zieht, um extern darauf zu operieren, verletzt SRP auf einer Ebene, die kein Tool misst.
- **Keine Utils-Klassen:** Statische Hilfsklassen sind Verantwortlichkeiten, die aus Objekten herausgezogen wurden – sie gehören zurück in die Objekte, die die Daten besitzen.

Das Decorator-Pattern zeigt exemplarisch, wie konsequente OOP-Komposition zu Klassen führt, die alle Bedingungen erfüllen. Layered Architectures und klassisches MVC hingegen erzeugen strukturell fast zwangsläufig SRP-Verletzungen – nicht weil sie schlecht gedacht sind, sondern weil sie nach einem anderen Trennungsprinzip arbeiten als SRP es fordert.

SRP ist kein Dogma, sondern ein Werkzeug. Mit der richtigen Metrik wird es zu einem, das man tatsächlich benutzen kann.

---

## Quellen

### Hauptquelle

Die Kernthese dieses Artikels – SRP als Äquivalenz von maximaler Kohäsion und minimaler Kopplung – basiert auf dem Vortrag von **Robert Bräutigam** (MATHEMA Software GmbH):

- Bräutigam, R. *Single Responsibility Principle*. SpeakerDeck. https://speakerdeck.com/robertbraeutigam/single-responsibility-principle

Der Vortrag entwickelt die pragmatische SRP-Definition, die Kritik an traditionellen Definitionen, das `PasswordHash`-Beispiel, das semantische Kopplungsproblem sowie die Implikationen für MVC, Layered Architectures und DDD.

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
