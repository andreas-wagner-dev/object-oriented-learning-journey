# Das Single Responsibility Principle objektiv messen

## Gegenstand, Zielsetzung und Methodik

Betrachtet wird das **Single Responsibility Principle (SRP)** als Entwurfsprinzip der objektorientierten Softwareentwicklung – insbesondere die Frage, *wie* sich SRP-Konformität einer Klasse objektiv feststellen lässt. Als Vergleichsobjekte dienen vier Entwurfsansätze in Java, alle anhand derselben Domäne – einer Bestellverwaltung – implementiert: ein monolithischer DDD-Service, derselbe Service aufgespalten in drei Klassen, ein vertikaler Decorator-Schnitt und ein horizontaler Decorator-Schnitt nach Bugayenko.

Ziel ist es, SRP auf eine **pragmatische und objektive Weise zu verifizieren und im Entwickleralltag einzusetzen**. Die Definition stammt von **Robert Bräutigam** (MATHEMA Software GmbH), der SRP nicht über subjektive Verantwortlichkeitsbegriffe, sondern über die messbaren Eigenschaften **Kohäsion** und **Kopplung** operationalisiert: `SRP ≡ MAXIMIZE COHESION ∧ MINIMIZE COUPLING`. Dieser Artikel übernimmt Bräutigams Formulierung als Grundlage und zeigt, wie sie mit konkreten Metriken im Code-Review, im CI/CD-Prozess und beim Klassendesign unmittelbar anwendbar wird.

Dazu werden zwei etablierte Metriken eingesetzt: **LCOM4** misst interne Kohäsion über Graphenanalyse – Zielwert ist 1. **CBO** zählt externe Abhängigkeiten – Zielwert ist so niedrig wie möglich. Beide Metriken werden für jede Beispielklasse explizit hergeleitet und in einer abschließenden Gegenüberstellung konsolidiert.

---

## 1. Das Problem: SRP ist nicht messbar

Das SRP ist das erste der fünf SOLID-Prinzipien (Robert C. Martin, 2003). Im Laufe der Zeit haben sich mehrere Definitionen herausgebildet, die alle dieselbe Grundidee umkreisen:

> **(1)** „Jedes Softwaremodul sollte genau einen Grund für eine Änderung haben."

> **(2)** „Sammelt die Dinge zusammen, die sich aus denselben Gründen verändern."

> **(3)** „‚Derselbe Grund' bedeutet, dass er vom selben Geschäftsmann stammt."

Alle drei Definitionen teilen dasselbe Grundproblem: Sie sind subjektiv. Was ist „ein Grund"? Ist ein `OrderService`, der Bestellungen erstellt und persistiert, eine oder zwei Verantwortlichkeiten? Die Diskussion bleibt kontext-abhängig und wird in Code-Reviews oft zum Glaubenskrieg. Definition (3) – „derselbe Geschäftsmann" – mag in der Domänenmodellierung hilfreich sein, ist aber als tägliches Programmierkriterium unbrauchbar.

Hinter SRP steckt ein konkretes Ziel: **Wartungsfreundlichkeit** – kleine Klassen, hohe Wahrscheinlichkeit lokaler Änderungen, geringe Ausbreitung von Änderungen auf andere Klassen. Für dieses Ziel brauchen wir einen objektiven Maßstab, keinen interpretationsbedürftigen Begriff.

---

## 2. Die Formel: Kohäsion und Kopplung

Bräutigam fasst SRP in einer Formel zusammen, die beide Ziele benennt:

```
SRP ≡ MAXIMIZE COHESION ∧ MINIMIZE COUPLING
```

**Kohäsion** beschreibt Abhängigkeiten *innerhalb* eines Objekts: Methoden und Felder beziehen sich aufeinander. Je stärker sie verbunden sind, desto kohäsiver ist die Klasse – und desto eher tut sie tatsächlich nur eine Sache. **Kopplung** beschreibt das Gegenteil: Abhängigkeiten *zwischen* Objekten. Je mehr externe Klassen eine Klasse kennen muss, desto höher ist das Risiko, dass eine Änderung anderswo auch hier Anpassungen erfordert.

Beide Konzepte drehen sich um Abhängigkeiten, und Abhängigkeiten treten in zwei Formen auf. **Physikalische Abhängigkeiten** – direkte Methodenaufrufe, Feldtypen – sind durch statische Analyse messbar. **Semantische Abhängigkeiten** sind implizites Wissen über die Struktur eines anderen Objekts, das sich meist über Getter ausdrückt. Letztere sind gefährlicher, weil sie für den Compiler unsichtbar sind: Änderungen propagieren durch sie auf subtile Weise, die kein Werkzeug automatisch erkennt. Jeder Getter ist ein potenzieller Einstiegspunkt für semantische Kopplung.

Die Formel hat entscheidende Vorteile: Sie ist **messbar**, **werkzeugunterstützt** (SonarQube, JDepend) und **designagnostisch** – sie funktioniert unabhängig vom verwendeten Architekturmuster.

---

## 3. Messverfahren: LCOM4 und CBO

### LCOM4 – Kohäsion messen

LCOM4 modelliert eine Klasse als Graphen: Jede Methode ist ein Knoten, zwei Methoden sind verbunden, wenn sie auf dasselbe Instanzfeld zugreifen oder sich gegenseitig aufrufen. LCOM4 ist die **Anzahl der zusammenhängenden Teilgraphen** dieses Graphen. Ein Wert von 1 bedeutet, dass alle Methoden miteinander verbunden sind – die Klasse ist eine kohäsive Einheit. Werte größer 1 zeigen an, dass die Klasse in unabhängige Teile zerfällt und aufgeteilt werden sollte:

| LCOM4 | Interpretation |
|---|---|
| **1** | Maximale Kohäsion – alle Methoden sind verbunden |
| **> 1** | Die Klasse zerfällt – sollte in *n* Klassen aufgeteilt werden |

Das folgende Beispiel macht die Graphenanalyse konkret. Die Klasse `OrderData` hält vier Felder, deren Methoden sich in zwei vollständig unabhängige Gruppen teilen – keine gemeinsamen Felder, kein gemeinsamer Knoten, LCOM4 = 2:

```java
// LCOM4 = 2 — zwei unabhängige Teilgraphen ❌
public class OrderData {
    private Cart cart;             // Feld A
    private Customer customer;     // Feld B
    private int paymentAmount;     // Feld C
    private String paymentStatus;  // Feld D

    // Gruppe 1: nutzt A und B
    public String summarize() {
        return customer.getName() + ": " + cart.itemCount() + " items"; // → A, B
    }

    // Gruppe 2: nutzt C und D — keine Verbindung zu Gruppe 1
    public void recordPayment(int amount) {
        this.paymentAmount = amount;   // → C
        this.paymentStatus = "PAID";   // → D
    }
}
// summarize()      → Teilgraph {A, B}
// recordPayment()  → Teilgraph {C, D}
// Keine gemeinsamen Felder → LCOM4 = 2 ❌
// Lösung: aufteilen in OrderIdentity und OrderPayment
```

### CBO – Kopplung messen

Während LCOM4 die interne Struktur einer Klasse bewertet, misst CBO (*Coupling Between Objects*, Chidamber & Kemerer 1994) ihre externe Verflechtung: Es zählt die Anzahl externer Typen, zu denen eine Klasse eine direkte Abhängigkeit hat – über Feldtypen, Methodenparameter, Rückgabetypen oder direkte Aufrufe. Primitive Typen (`int`, `String`) zählen dabei nicht.

| CBO | Interpretation |
|---|---|
| **0–5** | Geringe Kopplung – wartungsfreundlich |
| **6–10** | Moderat – beobachtenswert |
| **> 10** | Hoch – Redesign empfohlen |

Das wichtigste Werkzeug zur CBO-Reduktion ist die Abhängigkeitsumkehr: Statt auf konkrete Klassen zu zeigen, bindet sich eine Klasse an stabile Interfaces. Das folgende Beispiel zeigt denselben `ReportService` einmal mit konkreten Typen (CBO = 5) und einmal hinter Interfaces (CBO = 2) – die Funktionalität bleibt identisch, die Kopplung halbiert sich:

```java
// CBO = 5 ❌ — konkrete Typen in Feldern und Signaturen
public class ReportService {
    private final ReportRepository repository; // CBO +1
    private final PdfExporter pdfExporter;     // CBO +1

    public Report generateReport(DataQuery q) { // DataQuery → CBO +1, Report → CBO +1
        List<DataRow> rows = repository.fetch(q); // DataRow → CBO +1
        return pdfExporter.export(rows);
    }
}
// Gezählte Typen: ReportRepository, PdfExporter, DataQuery, Report, DataRow → CBO = 5

// CBO = 2 ✅ — Abhängigkeitsumkehr hinter stabile Interfaces
public class ReportService {
    private final ReportRepository repository; // Interface → CBO +1
    private final Exporter exporter;           // Interface → CBO +1

    public void generateReport(Query q) {
        exporter.export(repository.fetch(q));
    }
}
// Konkrete Typen hinter Interfaces verborgen → CBO sinkt von 5 auf 2
```

---

## 4. Beispiele: DDD-Service vs. Decorator

Mit LCOM4 und CBO als Werkzeugen lässt sich nun dieselbe Domäne in vier verschiedenen Entwurfsansätzen analysieren – und die Unterschiede werden messbar.

### Anwendungsfall: Bestellverwaltung

Ein Online-Shop benötigt drei Kernoperationen für Bestellungen: **Anlegen**, **Bezahlen** und **Stornieren**.

Beim **Anlegen** einer Bestellung reserviert das System die bestellten Artikel im Lager und legt die Bestellung persistent ab. Beim **Bezahlen** wird der Betrag über ein externes Zahlungssystem eingezogen und die Bestellung als bezahlt markiert; der Kunde erhält eine Bestätigungs-E-Mail. Beim **Stornieren** werden die reservierten Artikel wieder freigegeben und die Bestellung als storniert markiert; der Kunde erhält eine Stornierungs-E-Mail. Alle drei Operationen werden zusätzlich für Revisionszwecke in einem Audit-Log protokolliert.

Daraus ergeben sich folgende beteiligte Komponenten:

| Komponente | Rolle |
|---|---|
| `Cart` | Eingabe: die zu bestellenden Artikel |
| `Customer` | Eingabe: der bestellende Kunde |
| `OrderRepository` | Persistenz: Bestellung speichern und Status aktualisieren |
| `InventoryApi` | Lagerverwaltung: Artikel reservieren und freigeben |
| `PaymentApi` | Zahlung: Betrag einziehen |
| `Email` | Benachrichtigung: Bestätigung und Stornierung versenden |
| `Audit` | Protokollierung: alle Operationen im Audit-Log festhalten |

Genau diese sieben Komponenten tauchen in allen vier Implementierungen wieder auf. Die Frage ist allein, wie die Verantwortlichkeiten auf Klassen verteilt werden.

---

### 4.1 Service Pattern (DDD) – monolithisch

Die intuitivste Umsetzung bündelt alle drei Operationen in einem einzigen `OrderService`:

```java
public class OrderService {
    private final OrderRepository orderRepository;   // Feld 1
    private final InventoryApi inventoryService;     // Feld 2
    private final PaymentApi paymentGateway;         // Feld 3
    private final Email emailNotifier;               // Feld 4
    private final Audit auditLogger;                 // Feld 5

    public Order createOrder(Cart cart, Customer customer) {
        inventoryService.reserve(cart);              // → Feld 2
        Order order = new Order(cart, customer);
        orderRepository.save(order);                 // → Feld 1
        auditLogger.log("Order created: " + order.getId()); // → Feld 5
        return order;
    }

    public void processPayment(Order order) {
        paymentGateway.charge(order);                // → Feld 3
        order.markAsPaid();
        orderRepository.save(order);                 // → Feld 1
        emailNotifier.sendConfirmation(order);       // → Feld 4
        auditLogger.log("Payment processed: " + order.getId()); // → Feld 5
    }

    public void cancelOrder(Order order) {
        inventoryService.release(order);             // → Feld 2
        order.cancel();
        orderRepository.save(order);                 // → Feld 1
        emailNotifier.sendCancellation(order);       // → Feld 4
        auditLogger.log("Order cancelled: " + order.getId()); // → Feld 5
    }
}
```

Um LCOM4 herzuleiten, schauen wir, welche Methode welche Felder berührt:

| Methode | Felder |
|---|---|
| `createOrder` | 1 (repo), 2 (inv), 5 (logger) |
| `processPayment` | 1 (repo), 3 (gateway), 4 (notifier), 5 (logger) |
| `cancelOrder` | 1 (repo), 2 (inv), 4 (notifier), 5 (logger) |

Alle drei Methoden teilen Feld 1 (`orderRepository`) und Feld 5 (`auditLogger`) – der Graph hat damit einen einzigen Teilgraphen: LCOM4 = 1. Das klingt zunächst nach Kohäsion, ist es aber nicht. Die Verbindung entsteht ausschließlich durch technische Querschnittsfelder (Persistenz, Logging), nicht durch fachliche Zusammengehörigkeit. Jede Methode bedient einen völlig anderen Belang – sie sind zufällig in derselben Klasse gelandet, weil sie zur Domäne „Bestellung" gehören. Das eigentliche Ausmaß zeigt CBO: mit fünf externen Abhängigkeiten braucht jede einzelne Methode für einen Unit-Test alle 5 Mocks, obwohl `createOrder` weder `PaymentApi` noch `Email` jemals anfasst.

| Metrik | Wert | Befund |
|---|---|---|
| CBO | **5** | Alle 5 Felder sind externe Abhängigkeiten – jede Methode zieht andere |
| LCOM4 | **1** | Technisch verbunden über repo und logger – nicht fachlich kohäsiv |

Der `OrderService` ist damit kein Beispiel für Kohäsion, sondern für **erzwungene Verbindung durch Querschnittsfelder**. LCOM4 allein reicht nicht zur Diagnose – erst in Kombination mit CBO und der Frage *„Warum sind diese Methoden verbunden?"* wird das Problem sichtbar.

In der Verwendung zeigt sich das Symptom unmittelbar: Beim Instanziieren des Service müssen alle fünf Abhängigkeiten übergeben werden – auch dann, wenn nur eine einzelne Operation aufgerufen wird:

```java
OrderService service = new OrderService(
    orderRepository, inventoryApi, paymentApi, email, audit);

Order order = service.createOrder(cart, customer);

// Bezahlen — service kennt alle 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.processPayment(order);

// Stornieren — service kennt alle 5 Abhängigkeiten, auch wenn nur 3 gebraucht werden
service.cancelOrder(order);
```

---

### 4.2 Service Pattern (DDD) – aufgespalten

Der naheliegende Refactoring-Schritt besteht darin, den `OrderService` in drei kleinere Services aufzuteilen – einen pro Operation. Damit sinkt CBO pro Klasse, und jede Klasse enthält nur die Methode, die sie wirklich braucht:

```java
// Verantwortlichkeit: Bestellung anlegen
public class OrderService {
    private final OrderRepository orderRepository;   // Feld 1
    private final InventoryApi inventoryApi;         // Feld 2
    private final Audit audit;                       // Feld 3

    public Order createOrder(Cart cart, Customer customer) {
        inventoryApi.reserve(cart);                  // → Feld 2
        Order order = new Order(cart, customer);
        orderRepository.save(order);                 // → Feld 1
        audit.log("Order created: " + order.getId()); // → Feld 3
        return order;
    }
}

// Verantwortlichkeit: Zahlung abwickeln
public class OrderPaymentService {
    private final OrderRepository orderRepository;   // Feld 1
    private final PaymentApi paymentApi;             // Feld 2
    private final Email email;                       // Feld 3
    private final Audit audit;                       // Feld 4

    public void processPayment(Order order) {
        paymentApi.charge(order);                    // → Feld 2
        order.markAsPaid();
        orderRepository.save(order);                 // → Feld 1
        email.sendConfirmation(order);               // → Feld 3
        audit.log("Payment processed: " + order.getId()); // → Feld 4
    }
}

// Verantwortlichkeit: Bestellung stornieren
public class OrderCancelService {
    private final OrderRepository orderRepository;   // Feld 1
    private final InventoryApi inventoryApi;         // Feld 2
    private final Email email;                       // Feld 3
    private final Audit audit;                       // Feld 4

    public void cancelOrder(Order order) {
        inventoryApi.release(order);                 // → Feld 2
        order.cancel();
        orderRepository.save(order);                 // → Feld 1
        email.sendCancellation(order);               // → Feld 3
        audit.log("Order cancelled: " + order.getId()); // → Feld 4
    }
}
```

Da jeder Service genau eine Methode hat, ist LCOM4 = 1 per Definition – eine Klasse mit einer einzigen Methode kann nicht zerfallen. Das ist keine Kohäsion, sondern eine Trivialität.

| Klasse | Felder | CBO | LCOM4 |
|---|---|---|---|
| `OrderService` | repo, inventoryApi, audit | **3** | **1** – trivial |
| `OrderPaymentService` | repo, paymentApi, email, audit | **4** | **1** – trivial |
| `OrderCancelService` | repo, inventoryApi, email, audit | **4** | **1** – trivial |

Die Aufspaltung senkt CBO von 5 auf 3–4 und macht jede Klasse kleiner – das ist eine echte Verbesserung. Das Grundproblem bleibt jedoch bestehen: `audit` und `orderRepository` erscheinen in allen drei Klassen. Eine Änderung an der Logging-Strategie betrifft nicht mehr eine Methode, sondern drei Klassen. Die Querschnittsbelange wurden nicht isoliert – sie wurden nur verteilt. SRP ist nicht durch mechanisches Aufteilen zu erreichen, sondern durch den richtigen Schnitt.

In der Verwendung ist der Unterschied zum Monolithen zwar sichtbar – drei separate Objekte statt einem – doch die doppelt vorhandenen Abhängigkeiten (`orderRepository`, `audit`) müssen weiterhin an jedes Objekt einzeln übergeben werden:

```java
OrderService createSvc         = new OrderService(orderRepository, inventoryApi, audit);
OrderPaymentService paymentSvc = new OrderPaymentService(orderRepository, paymentApi, email, audit);
OrderCancelService cancelSvc   = new OrderCancelService(orderRepository, inventoryApi, email, audit);

Order order = createSvc.createOrder(cart, customer);
paymentSvc.processPayment(order);
cancelSvc.cancelOrder(order);
```

---

### 4.3 Vertikaler Decorator (OOP) – SRP-konform

Der Decorator-Schnitt geht einen anderen Weg: Statt Methoden auf Klassen zu verteilen, werden Verantwortlichkeiten durch Umhüllen komponiert. Das Interface heißt `Order` – ein fachlicher Begriff, kein `*Service`-Suffix. `PaymentApi` entfällt als Methodenparameter, weil `process()` eine reine Verhaltensaufforderung an das Objekt sein soll; die Abhängigkeit wird stattdessen direkt in die zuständige Klasse injiziert.

```java
public interface Order {
    void process();
    void cancel();
    String getId();
}
```

`StoredOrder` bildet den Kern: Es kapselt `OrderRepository` als privates Implementierungsdetail ein (nach Žurauskas 2018) und erhält die ID bereits im Konstruktor – direkte Instantiierung genügt.

```java
// Kern: Persistenz — Repository eingekapselt, ID im Konstruktor
public final class StoredOrder implements Order {
    private final String id;             // Feld 1
    private final Cart cart;             // Feld 2
    private final OrderRepository repo;  // Feld 3

    public StoredOrder(String id, Cart cart, OrderRepository repo) {
        this.id = id; this.cart = cart; this.repo = repo;
    }

    @Override
    public String getId() {
        return this.id;                              // → Feld 1
    }

    @Override
    public void process() {
        repo.updateStatus(this.id, "PROCESSING");    // → Feld 3, 1
    }

    @Override
    public void cancel() {
        repo.updateStatus(this.id, "CANCELLED");     // → Feld 3, 1
    }
}
```

| Methode | Felder |
|---|---|
| `getId` | 1 |
| `process` | 1, 3 |
| `cancel` | 1, 3 |

Alle Methoden teilen Feld 1 (`id`) → **LCOM4 = 1** ✅ — **CBO:** Cart, OrderRepository → **CBO = 2** ✅

Auf `StoredOrder` legen sich die horizontalen Dekoratoren: `PaidOrder` trägt ausschließlich die Zahlungsverantwortung und verwendet `PaymentApi` in `process()`, `InventedOrder` trägt ausschließlich die Lagerverwaltung und verwendet `InventoryApi` in `cancel()`:

```java
// Horizontaler Dekorator: Zahlung — injiziert PaymentApi, verwendet es in process()
public final class PaidOrder implements Order {
    private final Order delegate;            // Feld 1
    private final PaymentApi gateway;        // Feld 2

    public PaidOrder(Order delegate, PaymentApi gateway) {
        this.delegate = delegate; this.gateway = gateway;
    }

    @Override
    public String getId() {
        return delegate.getId();             // → Feld 1
    }

    @Override
    public void process() {
        gateway.charge(delegate);            // → Feld 2, 1
        delegate.process();                  // → Feld 1
    }

    @Override
    public void cancel() {
        delegate.cancel();                   // → Feld 1
    }
}
```

| Methode | Felder |
|---|---|
| `getId` | 1 |
| `process` | 1, 2 |
| `cancel` | 1 |

Alle Methoden teilen Feld 1 (`delegate`) → **LCOM4 = 1** ✅ — **CBO:** Order, PaymentApi → **CBO = 2** ✅

```java
// Horizontaler Dekorator: Lagerverwaltung — injiziert InventoryApi, verwendet es in cancel()
public final class InventedOrder implements Order {
    private final Order delegate;            // Feld 1
    private final InventoryApi inv;          // Feld 2

    public InventedOrder(Order delegate, InventoryApi inv) {
        this.delegate = delegate; this.inv = inv;
    }

    @Override
    public String getId() {
        return delegate.getId();             // → Feld 1
    }

    @Override
    public void process() {
        delegate.process();                  // → Feld 1
    }

    @Override
    public void cancel() {
        inv.release(delegate);               // → Feld 2, 1
        delegate.cancel();                   // → Feld 1
    }
}
```

| Methode | Felder |
|---|---|
| `getId` | 1 |
| `process` | 1 |
| `cancel` | 1, 2 |

Alle Methoden teilen Feld 1 (`delegate`) → **LCOM4 = 1** ✅ — **CBO:** Order, InventoryApi → **CBO = 2** ✅

Die Querschnittsbelange Logging und Benachrichtigung folgen demselben Muster: Jeder Dekorator kennt nur seinen `delegate` und genau ein weiteres Werkzeug.

```java
// Dekorator: Audit-Logging — beide Methoden betroffen
public final class AuditingOrder implements Order {
    private final Order delegate;          // Feld 1
    private final Audit auditLogger;       // Feld 2

    public AuditingOrder(Order delegate, Audit auditLogger) {
        this.delegate = delegate; this.auditLogger = auditLogger;
    }

    @Override
    public String getId() {
        return delegate.getId();                           // → Feld 1
    }

    @Override
    public void process() {
        delegate.process();                                // → Feld 1
        auditLogger.log("Processed: " + delegate.getId()); // → Feld 2, 1
    }

    @Override
    public void cancel() {
        delegate.cancel();                                 // → Feld 1
        auditLogger.log("Cancelled: " + delegate.getId()); // → Feld 2, 1
    }
}

// Dekorator: E-Mail-Benachrichtigung — beide Methoden betroffen
public final class NotifiedOrder implements Order {
    private final Order delegate;              // Feld 1
    private final Email emailNotifier;         // Feld 2

    public NotifiedOrder(Order delegate, Email emailNotifier) {
        this.delegate = delegate; this.emailNotifier = emailNotifier;
    }

    @Override
    public String getId() {
        return delegate.getId();                                  // → Feld 1
    }

    @Override
    public void process() {
        delegate.process();                                       // → Feld 1
        emailNotifier.sendConfirmation(delegate.getId());         // → Feld 2, 1
    }

    @Override
    public void cancel() {
        delegate.cancel();                                        // → Feld 1
        emailNotifier.sendCancellation(delegate.getId());         // → Feld 2, 1
    }
}
// Gleiche Struktur wie AuditingOrder → LCOM4 = 1, CBO = 2 ✅
```

Jede Klasse hat CBO = 2 und LCOM4 = 1 – und beide Werte sind fachlich begründet, nicht trivial oder zufällig. `StoredOrder` hat genau zwei Felder, weil Persistenz genau zwei Dinge braucht: die Entität (`id`, `cart`) und das Werkzeug dafür (`repo`). Dasselbe gilt für jeden weiteren Dekorator. Im Gegensatz zu den aufgespaltenen Services erscheinen `audit` und `orderRepository` hier jeweils nur in einer einzigen Klasse. Die Querschnittsbelange sind nicht verteilt – sie sind isoliert.

Die folgende Tabelle fasst die Messungen aller fünf Klassen zusammen:

| Klasse | Verantwortlichkeit | Felder | CBO | LCOM4 |
|---|---|---|---|---|
| `StoredOrder` | Persistenz | id, cart, repo | **2** | **1** ✅ |
| `PaidOrder` | Zahlung | delegate, gateway | **2** | **1** ✅ |
| `InventedOrder` | Lagerverwaltung | delegate, inv | **2** | **1** ✅ |
| `AuditingOrder` | Logging | delegate, auditLogger | **2** | **1** ✅ |
| `NotifiedOrder` | Benachrichtigung | delegate, emailNotifier | **2** | **1** ✅ |

In der Komposition werden alle fünf Klassen ineinander verschachtelt – von außen nach innen, von Querschnitt zu Kern:

```java
Order order = new NotifiedOrder(
    new AuditingOrder(
        new InventedOrder(
            new PaidOrder(
                new StoredOrder(id, cart, repo),
                paymentGateway),
            inventoryService),
        auditLogger),
    emailNotifier);

order.process();
// → NotifiedOrder → AuditingOrder → InventedOrder → PaidOrder → StoredOrder
// PaidOrder zieht Zahlung ein, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Bestätigungs-E-Mail

order.cancel();
// → NotifiedOrder → AuditingOrder → InventedOrder → PaidOrder → StoredOrder
// InventedOrder gibt Lagerbestand frei, StoredOrder persistiert,
// AuditingOrder loggt, NotifiedOrder sendet Stornierungs-E-Mail
```

Der vertikale Decorator erfüllt SRP und OCP: Eine neue Anforderung – etwa SMS-Benachrichtigung – erfordert einen neuen Dekorator `SmsOrder` und keinen Eingriff in bestehenden Code. Der Preis ist eine verschachtelte Kompositionskette, deren Tiefe mit jeder neuen Verantwortlichkeit wächst. Genau hier setzt der horizontale Decorator an.

---

### 4.4 Horizontaler Decorator (Bugayenko 2015) – SRP-konform

Bugayenko beschreibt in *Vertical and Horizontal Decorating* (2015) das Problem der wachsenden Tiefe: Statt Dekoratoren ineinander zu verschachteln, trägt ein einziges Wrapper-Objekt eine **flache Liste** gleichrangiger Aktionen. Das Muster verlangt ein zweites Interface – hier `OrderAct` – das die eigentliche Verantwortlichkeit kapselt. Der Wrapper `Orders` iteriert über alle `OrderAct`-Objekte und ruft sie der Reihe nach auf.

```java
public interface OrderAct {
    void process(String id, Cart cart);
    void cancel(String id, Cart cart);
}

public final class Orders implements Order {
    private final String id;                    // Feld 1
    private final Cart cart;                    // Feld 2
    private final List<OrderAct> acts;          // Feld 3

    public Orders(String id, Cart cart, List<OrderAct> acts) {
        this.id = id; this.cart = cart; this.acts = acts;
    }

    @Override public String getId() { return this.id; }

    @Override
    public void process() {
        acts.forEach(a -> a.process(this.id, this.cart)); // → Feld 3, 1, 2
    }

    @Override
    public void cancel() {
        acts.forEach(a -> a.cancel(this.id, this.cart));  // → Feld 3, 1, 2
    }
}
```

| Methode | Felder |
|---|---|
| `getId` | 1 |
| `process` | 1, 2, 3 |
| `cancel` | 1, 2, 3 |

Alle Methoden teilen Feld 1 (`id`) → **LCOM4 = 1** ✅ — **CBO:** Cart, List, OrderAct → **CBO = 3** ✅

Die einzelnen `OrderAct`-Implementierungen kennen jeweils nur ihr eigenes Werkzeug – kein `delegate`-Feld, kein Umhüllen:

```java
// Persistenz
public final class Persist implements OrderAct {
    private final OrderRepository repo; // Feld 1

    public Persist(OrderRepository repo) { this.repo = repo; }

    @Override public void process(String id, Cart cart) {
        repo.updateStatus(id, "PAID");       // → Feld 1
    }
    @Override public void cancel(String id, Cart cart) {
        repo.updateStatus(id, "CANCELLED");  // → Feld 1
    }
}

// Zahlung — nur process() relevant, cancel() ist leer
public final class Pay implements OrderAct {
    private final PaymentApi paymentApi;    // Feld 1

    public Pay(PaymentApi paymentApi) { this.paymentApi = paymentApi; }

    @Override public void process(String id, Cart cart) {
        paymentApi.charge(id);              // → Feld 1
    }
    @Override public void cancel(String id, Cart cart) { }
}

// Lagerverwaltung — nur cancel() relevant, process() ist leer
public final class Invent implements OrderAct {
    private final InventoryApi inventoryApi; // Feld 1

    public Invent(InventoryApi inventoryApi) { this.inventoryApi = inventoryApi; }

    @Override public void process(String id, Cart cart) { }
    @Override public void cancel(String id, Cart cart) {
        inventoryApi.release(cart);          // → Feld 1
    }
}

// Logging
public final class AuditDiff implements OrderAct {
    private final Audit audit;              // Feld 1

    public AuditDiff(Audit audit) { this.audit = audit; }

    @Override public void process(String id, Cart cart) {
        audit.log("Processed: " + id);      // → Feld 1
    }
    @Override public void cancel(String id, Cart cart) {
        audit.log("Cancelled: " + id);      // → Feld 1
    }
}

// Benachrichtigung
public final class NotifyDiff implements OrderAct {
    private final Email email;              // Feld 1

    public NotifyDiff(Email email) { this.email = email; }

    @Override public void process(String id, Cart cart) {
        email.sendConfirmation(id);         // → Feld 1
    }
    @Override public void cancel(String id, Cart cart) {
        email.sendCancellation(id);         // → Feld 1
    }
}
```

Die `OrderAct`-Klassen erreichen CBO = 1, weil kein `delegate`-Feld mehr nötig ist – die Weitergabe übernimmt `Orders` durch die Iteration. `audit` und `orderRepository` erscheinen weiterhin in genau einer Klasse. Eine neue Verantwortlichkeit ist ein neues `OrderAct`-Objekt und ein weiterer Listeneintrag, ohne dass bestehender Code berührt wird.

Die Messungen im Überblick:

| Klasse | Verantwortlichkeit | Felder | CBO | LCOM4 |
|---|---|---|---|---|
| `Orders` | Wrapper | id, cart, acts | **3** | **1** ✅ |
| `Persist` | Persistenz | repo | **1** | **1** ✅ |
| `Pay` | Zahlung | paymentApi | **1** | **1** ✅ |
| `Invent` | Lagerverwaltung | inventoryApi | **1** | **1** ✅ |
| `AuditDiff` | Logging | audit | **1** | **1** ✅ |
| `NotifyDiff` | Benachrichtigung | email | **1** | **1** ✅ |

Die Komposition ist flach – alle Verantwortlichkeiten stehen gleichrangig nebeneinander, keine Klasse umhüllt eine andere:

```java
Order order = new Orders(id, cart, List.of(
    new Persist(repo),
    new Pay(paymentApi),
    new Invent(inventoryApi),
    new AuditDiff(audit),
    new NotifyDiff(email)
));

order.process();
// → Orders iteriert alle acts: Persist, Pay, AuditDiff, NotifyDiff aktiv
// Pay zieht Zahlung ein, Persist persistiert,
// AuditDiff loggt, NotifyDiff sendet Bestätigungs-E-Mail

order.cancel();
// → Orders iteriert alle acts: Persist, Invent, AuditDiff, NotifyDiff aktiv
// Invent gibt Lagerbestand frei, Persist persistiert,
// AuditDiff loggt, NotifyDiff sendet Stornierungs-E-Mail
```

Verglichen mit der vertikalen Kette ist die Komposition flach und gleichrangig – jeder Eintrag in der Liste ist eine eigenständige Verantwortlichkeit, keine Umhüllung der nächsten. Nach Bugayenko ist vertikales Dekorieren der einfachere Einstieg; horizontales Dekorieren ist die skalierbarere Form, sobald die Kompositionskette tiefer wird als sie lesbar bleibt.

---

## 5. Gegenüberstellung

Die folgende Tabelle fasst die Messergebnisse aller vier Varianten zusammen und macht die Progression auf einen Blick sichtbar:

| Konzept | Metrik | OrderService (DDD) | Services aufgespalten | Vertikaler Decorator (4.3) | Horizontaler Decorator (4.4) |
|---|---|---|---|---|---|
| Kohäsion | LCOM4 | ⚠️ LCOM4 = **1** — Querschnittsfelder | ⚠️ LCOM4 = **1** — trivial (1 Methode) | ✅ LCOM4 = **1** — fachlich kohäsiv | ✅ LCOM4 = **1** — fachlich kohäsiv |
| Kopplung | CBO | ❌ CBO = **5** | ⚠️ CBO = **3–4** je Klasse | ✅ CBO = **2** je Klasse | ✅ CBO = **1–3** je Klasse |
| Lokale Änderbarkeit | LCOM4 / CBO | ❌ Logging betrifft alle 3 Methoden | ❌ Logging betrifft alle 3 Klassen | ✅ nur `AuditingOrder` | ✅ nur `AuditDiff` |
| Änderungsausbreitung | CBO | ❌ `PaymentApi` trifft gesamten Service | ⚠️ nur `OrderPaymentService` | ✅ nur `PaidOrder` | ✅ nur `Pay` |
| Testbarkeit | Mocks pro Test | ❌ 5 Mocks pro Methode | ⚠️ 3–4 Mocks pro Klasse | ✅ 2 Mocks pro Klasse | ✅ 1 Mock pro Klasse |
| Erweiterbarkeit (OCP) | neue Anforderung | ❌ bestehende Methoden ändern | ❌ neue Klasse + bestehende ändern | ✅ neuer Dekorator | ✅ neues `OrderAct` + Listeneintrag |
| Lesbarkeit Komposition | — | ✅ eine Klasse | ✅ drei Klassen | ⚠️ verschachtelte Kette | ✅ flache Liste |

Die vier Varianten lassen sich als Progression lesen. Der monolithische `OrderService` schneidet *horizontal nach Fachdomäne* – alles rund um Bestellungen landet in einer Klasse und erzeugt Methoden mit völlig unterschiedlichen Querschnittsbelangen, die nur durch technische Felder wie `orderRepository` und `audit` zusammengehalten werden. Diese Verbindung ist zufällig, nicht fachlich: Jede Methode braucht für einen Unit-Test alle 5 Mocks, obwohl sie inhaltlich nichts miteinander zu tun haben. Das Aufteilen in drei Services senkt CBO und macht jede Klasse kleiner, löst das Problem jedoch nicht grundlegend, weil `audit` und `orderRepository` weiterhin in allen drei Klassen erscheinen – die Querschnittsbelange wurden nicht isoliert, sondern nur verteilt.

Erst der vertikale Decorator durchbricht dieses Muster, indem er *vertikal nach Verantwortlichkeit* schneidet. Jede Klasse kennt ausschließlich ihren eigenen Belang und trägt genau die zwei Felder, die sie dafür braucht. Eine Änderung am Logging-Format betrifft ausschließlich `AuditingOrder`, eine Änderung an der Zahlungslogik ausschließlich `PaidOrder`. Dieser Schnitt ist nicht kostenlos: Fünf Klassen statt einer bedeuten mehr Dateien und eine verschachtelte Kompositionskette. Außerdem muss das Interface `Order` stabil bleiben – jede neue Methode zwingt alle Implementierungen zur Anpassung. Dieser Aufwand ist jedoch die direkte Einmalzahlung für SRP und OCP: Änderungen bleiben lokal, Erweiterungen berühren keinen bestehenden Code.

Das horizontale Muster geht in Richtung CBO und Testbarkeit noch einen Schritt weiter, führt aber eigene Schwächen ein. `OrderAct` erzwingt, dass jede Implementierung beide Methoden deklariert – auch wenn nur eine relevant ist. `Pay.cancel()` ist leer, `Invent.process()` ist leer. Das ist keine LCOM4-Verletzung, aber eine strukturelle Lüge: Das Interface behauptet eine Fähigkeit, die die Klasse nicht besitzt. In größeren Systemen häufen sich solche leeren Methoden, die still ignoriert werden und dennoch kompiliert und gepflegt werden müssen. Dazu kommt, dass die Ausführungsreihenfolge der `OrderAct`-Objekte implizit durch die Listenreihenfolge bestimmt wird – beim vertikalen Decorator ist sie durch die Verschachtelung explizit sichtbar, beim horizontalen liegt sie in der `List.of(...)`-Deklaration, die leicht übersehen wird. Bugayenkos Empfehlung lautet daher: mit vertikalem Dekorieren beginnen und zu horizontalem migrieren, wenn die Kompositionskette tiefer wird als sie lesbar bleibt.

---

## 6. Fazit

Die gängigen SRP-Definitionen laden zu Interpretation ein und führen in Code-Reviews zu Debatten ohne objektiven Ausweg. Die Formel **SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung** nach Robert Bräutigam macht das Prinzip messbar: **LCOM4 = 1** signalisiert, dass eine Klasse intern zusammenhängend ist; **CBO so niedrig wie möglich** signalisiert, dass sie nach außen minimal exponiert ist.

Der Vergleich der vier Designvarianten zeigt eine klare Progression – vom monolithischen `OrderService`, der fünf Verantwortlichkeiten durch Querschnittsfelder verbindet, über die aufgespaltenen Services, die CBO senken aber Querschnittsbelange nur verteilen, bis zum vertikalen Decorator mit CBO = 2 je Klasse und schließlich zum horizontalen Decorator mit CBO = 1 je `OrderAct`-Klasse, flacher Komposition und maximaler Erweiterbarkeit – auf Kosten leerer Interface-Methoden bei partiellen Verantwortlichkeiten.

Dabei ist LCOM4 = 1 allein kein Beweis für SRP-Konformität. Wie der `OrderService` zeigt, kann LCOM4 = 1 durch Querschnittsfelder entstehen, ohne echte fachliche Kohäsion. Erst die Kombination aus LCOM4 und CBO, zusammen mit der Frage *„Warum sind diese Methoden verbunden?"*, ergibt ein vollständiges Bild.

Für die tägliche Praxis lassen sich daraus drei Leitlinien ableiten: LCOM4 > 1 ist ein direkter Refactoring-Auftrag, der keiner Interpretation bedarf. CBO sollte auf stabile Interfaces begrenzt werden – konkrete Typen in Feldern sind ein Warnsignal. Und Getter sollten vermieden werden, da sie der Hauptweg für semantische Kopplung sind, die keine Metrik automatisch erfasst.

SRP ist kein Dogma, sondern ein Werkzeug. Mit der richtigen Metrik wird es zu einem, das man tatsächlich benutzen kann.

---

## Quellen

### Hauptquelle

- Bräutigam, R. *Single Responsibility Principle*. MATHEMA Software GmbH. SpeakerDeck. https://speakerdeck.com/robertbraeutigam/single-responsibility-principle

### Bücher

- Martin, R. C. (2003). *Agile Software Development: Principles, Patterns, and Practices*. Prentice Hall.
- Martin, R. C. (2008). *Clean Code*. Prentice Hall.
- Martin, R. C. (2017). *Clean Architecture*. Prentice Hall.
- Gamma, E. et al. (1994). *Design Patterns*. Addison-Wesley. *(Decorator Pattern)*
- Evans, E. (2003). *Domain-Driven Design*. Addison-Wesley.

### Metriken und Werkzeuge

- Chidamber, S. R., & Kemerer, C. F. (1994). *A Metrics Suite for Object Oriented Design*. IEEE TSE 20(6).
- Hitz, M., & Montazeri, B. (1995). *Measuring Coupling and Cohesion in Object-Oriented Systems*.
- SonarQube: https://docs.sonarsource.com
- JDepend: https://github.com/clarkware/jdepend

### Weiterführende Artikel

- Žurauskas, V. (2018). *Maintaining model integrity in service orchestrating applications*. https://www.vzurauskas.com/2018/07/24/maintaining-model-integrity
- Bugayenko, Y. (2015). *Vertical and Horizontal Decorating*. https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html
- Bugayenko, Y. (2014). *ORM Is an Offensive Anti-Pattern*. https://www.yegor256.com/2014/12/01/orm-offensive-anti-pattern.html
- Bugayenko, Y. (2025). *remove(42) vs. find(42).remove()*. https://www.yegor256.com/2025/06/22/retrieve-or-remove.html
- Martin, R. C. (2014). *The Single Responsibility Principle*. https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html
