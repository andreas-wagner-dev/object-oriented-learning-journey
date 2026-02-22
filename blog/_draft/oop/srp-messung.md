# Das Single Responsibility Principle objektiv messen

## 1. Einleitung: SRP und seine Bedeutung

Das **Single Responsibility Principle (SRP)** ist das erste der fünf SOLID-Prinzipien, die Robert C. Martin (auch bekannt als „Uncle Bob") zu Beginn der 2000er Jahre formuliert hat. Die ursprüngliche Definition lautet:

> *„A class should have only one reason to change."*

Der Zweck des SRP ist es, Software modular, wartbar und verständlich zu halten. Eine Klasse, die nur für eine Sache verantwortlich ist, ist leichter zu testen, einfacher zu ändern und klarer in ihrer Absicht. SRP ist damit nicht nur ein Designprinzip – es ist ein Qualitätsmerkmal, das langfristig über die Gesundheit einer Codebasis entscheidet.

---

## 2. Das Problem mit der gängigen Interpretation

Die intuitive Interpretation des SRP lautet oft: „Eine Klasse soll nur *eine Sache* tun." Doch diese Formulierung ist gefährlich vage. Was ist „eine Sache"?

- Ist ein `OrderService`, der Bestellungen erstellt, validiert und persistiert, eine oder drei Sachen?
- Ist ein `ReportGenerator`, der Daten aggregiert und formatiert, eine oder zwei Sachen?

Die gängige Interpretation führt zu zwei typischen Extremen:

**Zu grob:** Klassen mit Dutzenden von Methoden werden mit dem Argument gerechtfertigt, sie hätten „nur eine fachliche Verantwortung" (z. B. alles rund um Bestellungen).

**Zu fein:** Im anderen Extrem entstehen Nano-Klassen, die je nur eine einzige Methode enthalten – was zu einer explosionsartigen Proliferation von Klassen führt und die Lesbarkeit ebenfalls zerstört.

Das Kernproblem: **SRP in seiner gängigen Form ist nicht messbar.** Es gibt keinen objektiven Maßstab, der entscheidet, ob eine Klasse zu viel oder zu wenig tut. Die Diskussion bleibt subjektiv, kontext-abhängig und wird in Code-Reviews oft zum Glaubenskrieg.

---

## 3. Die Lösung: Eine objektive Formel für SRP

Um SRP operationalisierbar zu machen, braucht es eine formale Definition, die auf messbaren Größen beruht. Diese lässt sich wie folgt ausdrücken:

$$
\text{SRP} \equiv \text{Maximale Kohäsion} \wedge \text{Minimale Kopplung}
$$

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

### 4.2 Kopplung messen: CBO

Die Standardmetrik für Kopplung ist **CBO** (*Coupling Between Objects*), definiert nach Chidamber & Kemerer (1994).

CBO zählt die **Anzahl der Klassen**, mit denen eine gegebene Klasse eine direkte Abhängigkeit hat – sei es durch Methodenaufrufe, Feldtypen, Parameter oder Rückgabetypen.

| CBO-Wert | Interpretation |
|---|---|
| **0–5** | Geringe Kopplung – wartungsfreundlich |
| **6–10** | Moderate Kopplung – beobachtenswert |
| **> 10** | Hohe Kopplung – Redesign empfohlen |

Ergänzend kann **Ca** (*Afferent Coupling*) und **Ce** (*Efferent Coupling*) aus dem Package-Level betrachtet werden, um systemweite Abhängigkeiten zu beurteilen. Die Instability-Metrik `I = Ce / (Ca + Ce)` gibt an, wie stabil ein Modul gegenüber Änderungen ist.

---

## 5. Was bedeutet „Maximale Kohäsion" und „Minimale Kopplung"?

### Maximale Kohäsion

Maximale Kohäsion bedeutet, dass **jede Methode der Klasse auf (nahezu) alle Instanzvariablen der Klasse zugreift**. Die Klasse ist dann eine untrennbare Einheit – ihre Methoden sind nicht unabhängig voneinander lauffähig und adressieren gemeinsam genau einen Zweck.

Praktisch gesprochen: Wenn man eine Methode einer kohäsiven Klasse entfernt oder verschiebt, verliert die Klasse an Vollständigkeit. Die Methoden sind keine zufällige Sammlung, sondern ein kohärentes Protokoll.

Maximale Kohäsion schließt auch aus, dass eine Klasse versteckte Unter-Klassen enthält: Wenn zwei Methodengruppen völlig unabhängig voneinander agieren (LCOM4 > 1), dann hat die Klasse de facto zwei Verantwortlichkeiten.

### Minimale Kopplung

Minimale Kopplung bedeutet, dass die Klasse **so wenig wie möglich von der Außenwelt wissen muss**. Sie kennt nur die Abstraktionen, die sie wirklich braucht – keine konkreten Implementierungen, keine transitiven Abhängigkeiten, keine God-Objects.

Minimale Kopplung ist dabei nicht gleichbedeutend mit **keiner Kopplung** (CBO = 0 ist in realer Software nicht erreichbar). Es geht darum, Abhängigkeiten bewusst zu minimieren und ausschließlich über stabile Interfaces zu kommunizieren.

Zusammen erzwingen beide Bedingungen eine Klasse, die:
- **intern konsistent** ist (tut tatsächlich nur eine Sache),
- **extern abgegrenzt** ist (kennt nur das, was sie braucht),
- und damit **testbar, austauschbar und verständlich** ist.

---

## 6. Java-Beispiele mit Messung

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
        inventoryService.reserve(cart);                 // nutzt inventoryService
        Order order = new Order(cart, customer);
        orderRepository.save(order);                    // nutzt orderRepository
        auditLogger.log("Order created: " + order.getId()); // nutzt auditLogger
        return order;
    }

    public void processPayment(Order order) {
        paymentGateway.charge(order);                   // nutzt paymentGateway
        order.markAsPaid();
        orderRepository.save(order);                    // nutzt orderRepository
        emailNotifier.sendConfirmation(order);          // nutzt emailNotifier
        auditLogger.log("Payment processed: " + order.getId()); // nutzt auditLogger
    }

    public void cancelOrder(Order order) {
        inventoryService.release(order);                // nutzt inventoryService
        order.cancel();
        orderRepository.save(order);                    // nutzt orderRepository
        emailNotifier.sendCancellation(order);          // nutzt emailNotifier
        auditLogger.log("Order cancelled: " + order.getId()); // nutzt auditLogger
    }
}
```

**Messung:**

| Metrik | Wert | Bewertung |
|---|---|---|
| CBO | **5** (OrderRepository, InventoryService, PaymentGateway, EmailNotifier, AuditLogger) | Moderat – aber jede Methode zieht andere Dependencies |
| LCOM4 | **3** | 3 unabhängige Methodengruppen (Erstellen / Bezahlen / Stornieren) |

**Analyse:** Obwohl der `OrderService` fachlich „alles rund um Bestellungen" macht, zeigt LCOM4 = 3, dass die Klasse in drei unabhängige Einheiten zerfällt. `createOrder` und `processPayment` teilen sich nur `orderRepository` und `auditLogger` – sie sind keine kohäsive Einheit. **SRP ist verletzt.**

---

### 6.2 Decorator Pattern (OOP) – SRP-konform

Das Decorator-Pattern löst das Problem durch **Separation of Concerns über Komposition**. Jede Klasse hat genau eine Verantwortung, und Querschnittsbelange (Logging, Benachrichtigung) werden als Dekoratoren umgesetzt.

```java
// Kern-Verantwortung: nur Bestelllogik
public class DefaultOrderService implements OrderService {

    private final OrderRepository orderRepository;    // Abhängigkeit 1
    private final InventoryService inventoryService;  // Abhängigkeit 2

    public Order createOrder(Cart cart, Customer customer) {
        inventoryService.reserve(cart);
        Order order = new Order(cart, customer);
        orderRepository.save(order);
        return order;
    }

    public void processPayment(Order order, PaymentGateway gateway) {
        gateway.charge(order);
        order.markAsPaid();
        orderRepository.save(order);
    }

    public void cancelOrder(Order order) {
        inventoryService.release(order);
        order.cancel();
        orderRepository.save(order);
    }
}
```

```java
// Dekorator: nur Logging-Verantwortung
public class AuditingOrderService implements OrderService {

    private final OrderService delegate;    // Abhängigkeit 1
    private final AuditLogger auditLogger; // Abhängigkeit 2

    public Order createOrder(Cart cart, Customer customer) {
        Order order = delegate.createOrder(cart, customer);
        auditLogger.log("Order created: " + order.getId());
        return order;
    }

    public void processPayment(Order order, PaymentGateway gateway) {
        delegate.processPayment(order, gateway);
        auditLogger.log("Payment processed: " + order.getId());
    }

    public void cancelOrder(Order order) {
        delegate.cancelOrder(order);
        auditLogger.log("Order cancelled: " + order.getId());
    }
}
```

```java
// Dekorator: nur Benachrichtigungs-Verantwortung
public class NotifyingOrderService implements OrderService {

    private final OrderService delegate;       // Abhängigkeit 1
    private final EmailNotifier emailNotifier; // Abhängigkeit 2

    public Order createOrder(Cart cart, Customer customer) {
        return delegate.createOrder(cart, customer);
    }

    public void processPayment(Order order, PaymentGateway gateway) {
        delegate.processPayment(order, gateway);
        emailNotifier.sendConfirmation(order);
    }

    public void cancelOrder(Order order) {
        delegate.cancelOrder(order);
        emailNotifier.sendCancellation(order);
    }
}
```

**Messung:**

| Klasse | CBO | LCOM4 | Bewertung |
|---|---|---|---|
| `DefaultOrderService` | **2** | **1** | ✅ Maximale Kohäsion, minimale Kopplung |
| `AuditingOrderService` | **2** | **1** | ✅ Alle Methoden nutzen `delegate` und `auditLogger` |
| `NotifyingOrderService` | **2** | **1** | ✅ Alle Methoden nutzen `delegate` und `emailNotifier` |

**Analyse:** Jede Klasse hat LCOM4 = 1 und CBO = 2. Die Methoden jeder Klasse sind kohäsiv verbunden – sie teilen sich alle verfügbaren Felder. Die Kopplung ist auf das absolute Minimum reduziert. **SRP ist in allen drei Klassen erfüllt.**

Die Komposition erfolgt z. B. im DI-Container:

```java
OrderService service = new NotifyingOrderService(
    new AuditingOrderService(
        new DefaultOrderService(orderRepository, inventoryService),
        auditLogger
    ),
    emailNotifier
);
```

---

## 7. Zusammenfassung und Fazit

Die gängige SRP-Definition „eine Klasse soll nur eine Sache tun" ist ein nützlicher Einstieg, aber kein operationalisierbarer Maßstab. Sie lädt zu Interpretation ein und führt in der Praxis zu Designstreitigkeiten ohne objektiven Ausweg.

Die Formel **SRP ≡ Maximale Kohäsion ∧ Minimale Kopplung** überbrückt diese Lücke. Sie übersetzt ein qualitatives Prinzip in zwei quantitativ messbare Größen:

- **LCOM4 = 1** signalisiert maximale Kohäsion – die Klasse ist eine geschlossene Einheit.
- **CBO so niedrig wie möglich** signalisiert minimale Kopplung – die Klasse kennt nur das, was sie wirklich braucht.

Das Decorator-Pattern zeigt exemplarisch, wie konsequente OOP-Komposition zu Klassen führt, die beide Bedingungen erfüllen – während der monolithische DDD-Service-Schnitt trotz fachlicher Einheitlichkeit messbar gegen SRP verstößt (LCOM4 = 3).

**Der praktische Mehrwert dieser Sichtweise:**

Erstens ermöglicht sie **automatisierte Qualitätssicherung**: Tools wie SonarQube können LCOM4 und CBO als Quality Gates in CI/CD-Pipelines integrieren. Zweitens macht sie **Code Reviews produktiver**: Statt subjektiver Debatten über „zu viele Verantwortlichkeiten" gibt es konkrete Zahlen als Gesprächsgrundlage. Drittens fördert sie **besseres Design von Anfang an**: Wer Kohäsion und Kopplung im Blick hat, denkt automatisch in kleinen, abgegrenzten Einheiten – und landet damit genau dort, wo SOLID-Prinzipien hinführen sollen.

SRP ist kein Dogma, sondern ein Werkzeug. Mit der richtigen Metrik wird es zu einem, das man tatsächlich benutzen kann.
