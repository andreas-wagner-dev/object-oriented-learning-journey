# Business Context-Driven Package Structure: Der fehlende Link zwischen Business und Code

## Warum Ihre Package-Struktur das Business Context-Diagramm Ihres Systems sein sollte

### Das Problem: Die Übersetzungslücke

Stellen Sie sich vor: Ein Business Analyst zeigt Ihnen ein System Context Diagramm von einem E-Commerce Shop, das mit PayPal/Stripe, einem Warehouse (Inventory) und Versanddienstleistern (Shipping) integriert ist:

```
                          User
                            │
                            ▼
     ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
     │ Payment │    │                 │    │  Inventory  │
     │ (PayPal)│────│      Shop       │────│ (Warehouse) │
     │         │    │                 │    │             │
     └─────────┘    └─────────────────┘    └─────────────┘
                            │
                            ▼
                    ┌───────────────┐
                    │               │
                    │   Shipping    │
                    │  (DHL/UPS)    │
                    └───────────────┘
```

Dann öffnen Sie den Code und finden:

**Beispiel 1: Klassische Layered Architecture**

```
com.company.shop
├── controller/
├── service/
├── repository/
├── dto/
├── entity/
└── config/
```

**Beispiel 2: Clean Architecture (nach Robert C. Martin)**

```
com.company.shop
├── domain/
│   ├── entity/
│   ├── usecase/
│   └── port/
├── application/
├── infrastructure/
│   ├── adapter/
│   ├── database/
│   └── external/
└── presentation/
```

**Beispiel 3: DDD Structure (nach Eric Evans)**

```
com.company.shop
├── aggregate/
├── entity/
├── valueobject/
├── repository/
├── service/
├── factory/
└── specification/
```

**Beispiel 4: Hexagonal Architecture**
Quelle: Alistair Cockburn's Hexagonal Architecture

```
com.company.shop
├── domain/
├── port/
│   ├── in/
│   └── out/
├── adapter/
│   ├── web/
│   ├── database/
│   └── messaging/
└── application/
```

**Beispiel 5: Vertical Slices Architecture**
Quelle: Jimmy Bogard's Vertical Slices

```
com.company.shop
├── features/
│   ├── process-order/
│   ├── manage-stock/
│   └── calculate-shipping/
├── shared/
└── infrastructure/
```

**Wo ist Payment?**

**Wo ist das Inventory?**

**Wo ist Shipping aus dem Context-Diagramm?**

Das Problem ist die mentale Übersetzung:

Man muss raten, ob die z. B. PayPal-Anbindung in infrastructure, external oder einem generischen service-Paket liegt.

Selbst moderne Patterns wie Vertical Slices konzentrieren sich häufig primär auf technische Funktionalitäten und Prozesslogik (Verben), anstatt auf Domänenobjekte (Dinge) und externe Kontexte als zentrale Bestandteile der Paketstruktur unmittelbar abzubilden.

### Die Lösung: Context-Driven Packaging

Basierend auf den drei goldenen Regeln des leserfreundlichen Paketierens gilt: Ihre Package-Struktur sollte das Context-Diagramm widerspiegeln.

```
com.company.shop
├── app/              → Framework (nur Main, DI, Config)
├── payment/          → PayPalPayment, StripePayment
├── inventory/        → WarehouseProducts, DbProducts
├── shipping/         → DhlShipment, UpsShipment
├── user/             → WebOrders, ApiOrders
├── Product.java      → Domain Interface
├── Products.java     → Sammlung (kein Repository!)
├── Payment.java      → Domain Interface
├── Order.java        → Domain Interface
└── Orders.java       → Sammlung (kein Repository!)
```

### Vorher/Nachher: Echtes Projekt-Beispiel

**Vorher (Technische Pakete)**

Aufgabe: "Behebe den Bug in der PayPal-Anbindung"

⏱️ 15 Minuten Code-Navigation

🔍 Suche in: service/, integration/, client/, external/

🤔 "Ist es PaymentService? PayPalClient? PaymentProcessor? PaymentAdapter?"

**Nachher (Context-Driven)**

Aufgabe: "Behebe den Bug in der PayPal-Anbindung"

⏱️ 30 Sekunden Code-Navigation

🎯 Direkt zu: com.company.shop.payment/PayPalPayment.java

✅ Sofort klar: Das ist die PayPal-Implementierung. Keine Übersetzung nötig.

### Messbare Vorteile

**1. Code-Navigation**

Vorher: 1-2 Minuten für Feature-Lokalisierung

Nachher: 10 Sekunden bis 30 Sekunden

Verbesserung: ca. 80-90% weniger Zeit

**2. Onboarding neuer Entwickler**

Vorher: "Wo finde ich die Versand-Logik?" → 5 Minuten Erklärung

Nachher: "Schau ins shipping Package" → 30 Sekunden

Verbesserung: ca. 99% weniger Erklärungsaufwand

**3. Business-Developer Kommunikation**

Vorher: Business Analyst sagt "Versand-Problem" → Developer übersetzt mental zu technischen Packages

Nachher: Business Analyst sagt "Versand-Problem" → Developer geht direkt zu com.company.shop.shipping/

Verbesserung: Kein Übersetzungsaufwand

### Die drei goldenen Regeln

**Regel 1: Pakete sollen niemals von Sub-Paketen abhängen**

Root Package = Domain Core, unabhängig von allem.
- z.B. Payment.java, Order.java als Interfaces oder abstrakte Klassen

Sub-Pakete = Implementierungen (Adapter), abhängig vom Core.
- z.B. payment/PayPalPayment.java implementiert Payment

**Regel 2: Sub-Pakete führen keine neuen Konzepte ein, nur Details**

payment/PayPalPayment.java = Detail der Payment-Integration.

Keine neuen fachlichen Konzepte in Sub-Packages, die nicht im Root als Interface existieren.

Das Paket `app/` ist der einzige Sonderfall für technische Infrastruktur.

**Regel 3: Pakete und Klassen spiegeln Business-Kontexte wider, nicht technische Rollen**

✅ **Richtig: Nomen (Dinge) mit beschreibenden Präfixen**
- payment/PayPalPayment, payment/StripePayment
- inventory/WarehouseProducts, inventory/DbProducts
- shipping/DhlShipment, shipping/UpsShipment

✅ **Richtig: Paketnamen aus Context-Diagramm**
- payment/, inventory/, shipping/ (externe Systeme)
- user/ (Schnittstellen)

❌ **Falsch: Verben oder technische Suffixe**
- payment/PaymentProcessor, payment/PaymentManager
- inventory/StockRepository, inventory/InventoryService
- shipping/ShippingHandler, shipping/ShipmentValidator

❌ **Falsch: Technische Paketnamen**
- service/, repository/, controller/, presentation/
- common/, shared/, util/, helper/
- adapter/, client/, wrapper/, facade/

### Implementierung: Schritt für Schritt zum Context-Driven Code

**Schritt 1: Kontext-Analyse**

Nehmen Sie das offizielle Context-Diagramm Ihres Systems zur Hand. Identifizieren Sie:

- Die zentrale Domäne (z.B. "Shop")
- Alle externen Systeme (z.B. PayPal, Warehouse, DHL)
- Alle Akteure/Schnittstellen (z.B. User, Admin-API)
- Die Kernkonzepte der Domäne (z.B. Product, Order, Payment)

Beispiel-Analyse:
```
Hauptdomäne: Shop
Externe Systeme: PayPal, Stripe, Warehouse, DHL, UPS
Schnittstellen: Web-User, REST-API
Kernkonzepte: Product, Order, Payment, Shipment
```

**Schritt 2: Root-Package anlegen**

Legen Sie alle zentralen Domänen-Klassen direkt in das Root-Package:

```
com.company.shop/
├── Product.java       → Domain Entity (Interface oder Abstract)
├── Products.java      → Sammlung von Product
├── Order.java         → Domain Entity
├── Orders.java        → Sammlung von Order
├── Payment.java       → Domain Entity
└── Shipment.java      → Domain Entity
```

**Wichtig:** Nur Interfaces oder abstrakte Klassen! Keine Implementierungen im Root.

**Schritt 3: Kontext-Kapselung**

Erstellen Sie für jeden externen Partner oder Fachbereich ein eigenes Paket auf der ersten Ebene:

```
com.company.shop/
├── payment/           → PayPal, Stripe Implementierungen
├── inventory/         → Warehouse Integration
├── shipping/          → DHL, UPS Integration
└── user/              → Web, API Schnittstellen
```

**Schritt 4: Framework-Isolierung**

Schieben Sie allen technischen "Lärm" in ein dediziertes `app/`-Paket:

```
com.company.shop/
└── app/
    ├── ShopApplication.java    → Main-Klasse
    ├── Dependencies.java       → Dependency Injection
    ├── WebConfig.java          → Framework-Config
    └── application.yml         → Properties
```

**Schritt 5: Implementierung mit richtiger Benennung**

❌ **FALSCH: Technische Namen und Verben**

```java
// Technische Suffixe
payment/PayPalClient.java
payment/PaymentProcessor.java
payment/PaymentValidator.java
payment/PaymentManager.java

// Technische Präfixe
inventory/StockRepository.java
inventory/WarehouseService.java
```

✅ **RICHTIG: Nur Nomen mit beschreibenden Präfixen**

```java
// payment/ - Implementierungen von Payment
payment/PayPalPayment.java          → PayPal-Implementierung
payment/StripePayment.java          → Stripe-Implementierung
payment/KafkaReceivedPayment.java   → Kafka Consumer
payment/KafkaPaymentMessage.java    → Kafka Producer (Event)
payment/CachedPayment.java          → Cache-Decorator
payment/LoggedPayment.java          → Logging-Decorator

// inventory/ - Implementierungen von Products
inventory/WarehouseProducts.java    → Warehouse-Anbindung
inventory/DbProducts.java           → Datenbank-Implementierung
inventory/CachedProducts.java       → Cache-Decorator
inventory/KafkaReceivedProducts.java → Kafka Consumer

// shipping/ - Implementierungen von Shipment
shipping/DhlShipment.java           → DHL-Implementierung
shipping/UpsShipment.java           → UPS-Implementierung
shipping/KafkaShipmentMessage.java  → Kafka Producer (Event)

// user/ - Implementierungen von Orders (API)
user/ApiOrders.java                 → REST API
user/WebOrders.java                 → Web Interface
```

**Schritt 6: Decorator-Pattern für Verantwortlichkeiten**

Nutzen Sie Prefixe, um verschiedene Aspekte zu beschreiben:

```java
// Root: Interface
public interface Payment {
    Receipt process(Money amount);
}

// payment/PayPalPayment.java - Kern-Implementierung
public class PayPalPayment implements Payment {
    public Receipt process(Money amount) {
        // PayPal API call
    }
}

// payment/KafkaReceivedPayment.java - Kafka Consumer
public class KafkaReceivedPayment implements Payment {
    private final Payment origin;
    
    @KafkaListener(topics = "payment-commands")
    public Receipt onPaymentCommand(PaymentCommand cmd) {
        return origin.process(cmd.amount());
    }
}

// payment/KafkaPaymentMessage.java - Kafka Producer (Event)
public class KafkaPaymentMessage implements Payment {
    private final Payment origin;
    private final KafkaTemplate<String, Event> kafka;
    
    public Receipt process(Money amount) {
        Receipt receipt = origin.process(amount);
        
        // Event publizieren
        kafka.send("payment-events", 
            new PaymentCompleted(receipt.id(), amount)
        );
        
        return receipt;
    }
}

// payment/DbPayment.java - Datenbank-Persistierung
public class DbPayment implements Payment {
    private final Payment origin;
    private final Payments payments;  // Zugriff auf storage/
    
    public Receipt process(Money amount) {
        Receipt receipt = origin.process(amount);
        payments.save(receipt);
        return receipt;
    }
}

// payment/ValidPayment.java - Validierungs-Decorator
public class ValidPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Money amount) {
        if (amount.isNegative()) throw new IllegalArgumentException();
        return origin.process(amount);
    }
}

// payment/LoggedPayment.java - Logging-Decorator
public class LoggedPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Money amount) {
        log.info("Processing payment: {}", amount);
        return origin.process(amount);
    }
}

// payment/NotifiedPayment.java - Email-Decorator
public class NotifiedPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Money amount) {
        Receipt receipt = origin.process(amount);
        emailService.send("Payment processed");
        return receipt;
    }
}
```

**Schritt 7: Storage-Paket für ORM-Isolation**

Wenn Sie ORMs wie JPA, jOOQ oder JDBC verwenden, isolieren Sie diese im `storage/` Paket:

```java
// payment/storage/PaymentEntity.java - JPA Entity
@Entity
@Table(name = "payments")
public class PaymentEntity {
    @Id private String id;
    @Column private BigDecimal amount;
    @Column private String status;
    // Getters, Setters
}

// payment/storage/PaymentJpa.java - JPA Repository Interface
public interface PaymentJpa extends JpaRepository<PaymentEntity, String> {
    List<PaymentEntity> findByStatus(String status);
}

// payment/DbPayments.java - Domain Adapter (außerhalb storage/)
public class DbPayments implements Payments {
    private final PaymentJpa jpa;
    
    public void save(Receipt receipt) {
        PaymentEntity entity = new PaymentEntity();
        entity.setId(receipt.id());
        entity.setAmount(receipt.amount().value());
        entity.setStatus("COMPLETED");
        jpa.save(entity);
    }
    
    public Optional<Receipt> find(String id) {
        return jpa.findById(id)
            .map(this::toReceipt);
    }
    
    private Receipt toReceipt(PaymentEntity entity) {
        return new Receipt(
            entity.getId(),
            Money.of(entity.getAmount())
        );
    }
}
```

**Wichtig:** 
- `storage/` Klassen sind ORM-spezifisch (@Entity, @Repository)
- Nur die Domain-Adapter (wie DbPayments) greifen auf `storage/` zu
- Domain-Interfaces kennen niemals ORM-Klassen

**Schritt 8: Zusammensetzen im app/ Package**

```java
// app/Dependencies.java
public class Dependencies {
    
    public Payment payment() {
        Payment core = new PayPalPayment();
        
        return new KafkaPaymentMessage(      // Event Publishing
            new NotifiedPayment(              // Email
                new DbPayment(                // Persistierung
                    new LoggedPayment(        // Logging
                        new ValidPayment(     // Validierung
                            core
                        )
                    )
                )
            )
        );
    }
    
    public Products products() {
        return new CachedProducts(
            new DbProducts(
                new WarehouseProducts()
            )
        );
    }
}

// app/KafkaConfig.java - Framework-Konfiguration
@Configuration
public class KafkaConfig {
    
    @Bean
    public KafkaTemplate<String, Event> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
    
    @Bean
    public ProducerFactory<String, Event> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        return new DefaultKafkaProducerFactory<>(config);
    }
}
```

### Naming-Regeln im Detail

**✅ Erlaubte Präfixe (beschreiben WAS es ist):**

```
PayPal*, Stripe*, Dhl*, Ups*      → Externe Systeme
Db*, Cached*, Logged*             → Technische Aspekte
Kafka*                            → Messaging (KafkaReceivedPayment, KafkaPaymentMessage)
Valid*, Checked*, Secured*        → Validierung/Security
Api*, Web*, Json*, Xml*           → Schnittstellen/Formate
Notified*, Emailed*               → Benachrichtigungen
Fake*, Mock*, Test*               → Testing
```

**❌ Verbotene Suffixe (beschreiben technische Rollen):**

```
*Manager, *Handler, *Processor    → Zu generisch
*Service, *Repository, *DAO       → Layer-Denken
*Client, *Adapter, *Wrapper       → Technische Details
*Validator, *Creator, *Builder    → Verben
*Model, *DTO, *VO                 → Technische Klassifikation
*Controller, *Facade, *Proxy      → Pattern-Namen
*Helper, *Util, *Tool             → Müllhalde-Begriffe
*Consumer, *Producer, *Publisher  → Technische Rollen (nutze Kafka* Präfix)
```

**❌ Verbotene Package-Namen:**

```
common/, shared/, util/           → Wird zur Müllhalde
service/, repository/, dao/       → Layer-Denken
client/, adapter/, wrapper/       → Technische Details
helper/, tools/, core/            → Zu generisch
consumer/, producer/, listener/   → Technische Rollen
```

**✅ Erlaubte spezielle Package-Namen:**

```
app/                              → Framework-Code (Main, DI, Config)
storage/                          → ORM-spezifische Klassen (@Entity, @Repository)
```

### Beispiel: Vollständige Implementierung Payment

```
com.company.shop/
├── Payment.java                      → Interface im Root
│
└── payment/
    ├── PayPalPayment.java           → Haupt-Implementierung
    ├── StripePayment.java           → Alternative Implementierung
    ├── KafkaReceivedPayment.java    → Kafka Consumer
    ├── KafkaPaymentMessage.java     → Kafka Producer (Event)
    ├── DbPayment.java               → Datenbank-Decorator
    ├── ValidPayment.java            → Validierungs-Decorator
    ├── LoggedPayment.java           → Logging-Decorator
    ├── CachedPayment.java           → Cache-Decorator
    ├── NotifiedPayment.java         → Email-Decorator
    ├── FakePayment.java             → Test-Implementierung
    └── storage/                     → ORM-spezifische Klassen
        ├── PaymentEntity.java       → JPA Entity
        ├── PaymentJpa.java          → JPA Repository
        └── PaymentRecord.java       → jOOQ Record (optional)
```

```java
// Payment.java (Root)
public interface Payment {
    Receipt process(Money amount, String orderId);
}

// payment/PayPalPayment.java
public final class PayPalPayment implements Payment {
    private final String apiKey;
    
    public Receipt process(Money amount, String orderId) {
        // PayPal API Integration
        String transactionId = payPalApi.charge(amount);
        return new Receipt(transactionId, amount);
    }
}

// payment/KafkaReceivedPayment.java - Kafka Consumer
public final class KafkaReceivedPayment implements Payment {
    private final Payment origin;
    
    @KafkaListener(topics = "payment-commands")
    public Receipt onCommand(PaymentCommand cmd) {
        log.info("Received payment command from Kafka: {}", cmd);
        return origin.process(cmd.amount(), cmd.orderId());
    }
}

// payment/KafkaPaymentMessage.java - Kafka Producer
public final class KafkaPaymentMessage implements Payment {
    private final Payment origin;
    private final KafkaTemplate<String, Event> kafka;
    
    public Receipt process(Money amount, String orderId) {
        Receipt receipt = origin.process(amount, orderId);
        
        // Event nach Kafka publizieren
        kafka.send("payment-events", 
            new PaymentCompleted(receipt.id(), amount, orderId)
        );
        
        return receipt;
    }
}

// payment/DbPayment.java - Datenbank-Decorator
public final class DbPayment implements Payment {
    private final Payment origin;
    private final PaymentJpa jpa;  // Zugriff auf storage/
    
    public Receipt process(Money amount, String orderId) {
        Receipt receipt = origin.process(amount, orderId);
        
        // In Datenbank speichern
        PaymentEntity entity = new PaymentEntity();
        entity.setId(receipt.id());
        entity.setAmount(amount.value());
        entity.setOrderId(orderId);
        entity.setStatus("COMPLETED");
        jpa.save(entity);
        
        return receipt;
    }
}

// payment/storage/PaymentEntity.java - ORM Entity
@Entity
@Table(name = "payments")
public class PaymentEntity {
    @Id 
    private String id;
    
    @Column(nullable = false)
    private BigDecimal amount;
    
    @Column(nullable = false)
    private String orderId;
    
    @Column(nullable = false)
    private String status;
    
    @Column(nullable = false)
    private LocalDateTime createdAt;
    
    // Getters, Setters
}

// payment/storage/PaymentJpa.java - JPA Repository
public interface PaymentJpa extends JpaRepository<PaymentEntity, String> {
    List<PaymentEntity> findByOrderId(String orderId);
    List<PaymentEntity> findByStatus(String status);
}

// payment/ValidPayment.java
public final class ValidPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Money amount, String orderId) {
        if (amount.isNegative()) {
            throw new IllegalArgumentException("Negative amount");
        }
        if (orderId == null || orderId.isEmpty()) {
            throw new IllegalArgumentException("OrderId required");
        }
        return origin.process(amount, orderId);
    }
}

// payment/LoggedPayment.java
public final class LoggedPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Money amount, String orderId) {
        log.info("Processing payment for order {}: {}", orderId, amount);
        Receipt receipt = origin.process(amount, orderId);
        log.info("Payment completed: {}", receipt.id());
        return receipt;
    }
}
```

### Checkliste für saubere Implementierung

- [ ] Alle Domain-Entities liegen im Root als Interfaces/Abstract Classes
- [ ] Jeder externe Kontext hat ein eigenes Package auf Level 1
- [ ] Das `app/` Package enthält NUR Framework-Code (Main, DI, Config)
- [ ] Das `storage/` Package enthält NUR ORM-spezifische Klassen (@Entity, @Repository)
- [ ] Alle Klassen nutzen Nomen (Substantive), keine Verben
- [ ] Alle Klassen nutzen beschreibende Präfixe (PayPal*, Db*, Valid*, Kafka*)
- [ ] KEINE technischen Suffixe (*Manager, *Service, *Processor, *Consumer, *Producer)
- [ ] KEINE technischen Package-Namen (common/, util/, service/, consumer/, producer/)
- [ ] Decorator-Pattern für Cross-Cutting Concerns (Logging, Caching, Validation)
- [ ] Kafka-Integration mit Kafka*-Präfix (KafkaReceivedPayment, KafkaPaymentMessage)
- [ ] Abhängigkeiten zeigen nur vom Package zum Root, nie umgekehrt
- [ ] ORM-Klassen sind isoliert in storage/ und werden nur von Db*-Decorators verwendet

### Template: E-Commerce

Jetzt spiegeln wir das Context-Diagramm direkt im Code wider:

```
com.company.shop
├── app/                   → Startup, DI, Config (z.B. ShopApplication.java)
├── payment/               → PayPalPayment, StripePayment, KafkaPaymentMessage
│   └── storage/           → PaymentEntity, PaymentJpa (ORM)
├── inventory/             → WarehouseProducts, DbProducts, KafkaReceivedProducts
│   └── storage/           → ProductEntity, ProductJpa (ORM)
├── shipping/              → DhlShipment, UpsShipment, KafkaShipmentMessage
│   └── storage/           → ShipmentEntity, ShipmentJpa (ORM)
├── user/                  → WebOrders, ApiOrders, SecuredOrders
├── Product.java           → Interface: scream at Level 0 what the app is about! 
├── Products.java          → Sammlung von Product (kein Repository!)
├── Payment.java           → Interface: tell at Level 0 what the app is about! 
├── Order.java             → Interface: scream at Level 0 what the app is about! 
└── Orders.java            → Sammlung von Order (kein Repository!)
```

### Die Synthese: Kombination bewährter Erkenntnisse

Die Erkenntnis lautet: Package-Struktur = ausführbares Context-Diagramm.

Wir kombinieren:
- **javadevguy's Regeln** für lesbare Strukturen und klare Benennung (nur Nomen, keine Verben, beschreibende Präfixe)
- **Simon Browns Modularität** für klare Grenzen und Package-Unabhängigkeit
- **Philipp Hauers Feature-Orientierung** für Business-fokussierte Organisation
- **Event-Driven Architecture** mit Kafka für lose gekoppelte Services (KafkaReceivedPayment, KafkaPaymentMessage)
- **ORM-Isolation** im storage/ Package für saubere Trennung von Persistierung und Domain-Logik

Diese Synthese schließt die mentale Lücke zwischen Business Analysten und Entwicklern endgültig:
- Business sagt "Payment" → Code zeigt `payment/PayPalPayment.java`
- Business sagt "Shipping" → Code zeigt `shipping/DhlShipment.java`
- Business sagt "Inventory" → Code zeigt `inventory/WarehouseProducts.java`

Keine Übersetzung mehr nötig. Der Code spricht die Sprache des Business.

**Von Packages über Module zu Services:**
Die Context-Driven Struktur funktioniert auf jeder Ebene:
1. **Packages**: payment/, inventory/, shipping/ (Monolith)
2. **Module**: shop-payment/, shop-inventory/, shop-shipping/ (Modular Monolith)
3. **Services**: payment-service/, inventory-service/, shipping-service/ (Microservices)

Jede Ebene behält die gleichen Prinzipien bei - nur die Deployment-Grenzen ändern sich.

## Der nächste Schritt: Modularisierung

### Wann macht Modularisierung Sinn?

Sobald Ihre Anwendung wächst oder Sie eines der folgenden Ziele haben:

**Technische Gründe:**
- Klare Abhängigkeitsgrenzen erzwingen (compile-time statt nur package-private)
- Separate Deployment-Einheiten ermöglichen
- Build-Zeiten optimieren (nur geänderte Module bauen)
- Wiederverwendung von Kontexten in anderen Projekten

**Organisatorische Gründe:**
- Verschiedene Teams arbeiten an verschiedenen Kontexten
- Unterschiedliche Release-Zyklen für verschiedene Bereiche
- Klare Code-Ownership etablieren

### Von Packages zu Modulen

Die gute Nachricht: Ihre Context-Driven Package-Struktur ist die perfekte Vorbereitung für Modularisierung. Jeder Business-Kontext wird zu einem eigenen Modul.

**Vorher: Monolith mit Context-Driven Packages**

```
com.company.shop
├── app/         
├── payment/
├── inventory/
├── shipping/
├── user/
├── Product.java
├── Products.java
├── Payment.java
├── Order.java 
└── Orders.java
```

**Nachher: Multi-Module Projekt**

```
shop/
├── shop/                         → Domain Core Module (kein -core Suffix!)
│   └── src/main/java/com/company/shop/
│       ├── Product.java
│       ├── Products.java
│       ├── Payment.java
│       ├── Order.java
│       ├── Orders.java
│       ├── Money.java            → Shared Value Object
│       └── OrderId.java          → Shared Value Object
│
├── shop-payment/                 → Payment Context Module
│   └── src/main/java/com/company/shop/payment/
│       ├── PayPalPayment.java
│       ├── StripePayment.java
│       ├── KafkaPaymentMessage.java
│       ├── LoggedPayment.java
│       └── storage/
│           ├── PaymentEntity.java
│           └── PaymentJpa.java
│
├── shop-inventory/               → Inventory Context Module
│   └── src/main/java/com/company/shop/inventory/
│       ├── WarehouseProducts.java
│       ├── DbProducts.java
│       └── storage/
│           ├── ProductEntity.java
│           └── ProductJpa.java
│
├── shop-shipping/                → Shipping Context Module
│   └── src/main/java/com/company/shop/shipping/
│       ├── DhlShipment.java
│       ├── UpsShipment.java
│       └── storage/
│           ├── ShipmentEntity.java
│           └── ShipmentJpa.java
│
├── shop-user/                    → User Interface Module
│   └── src/main/java/com/company/shop/user/
│       ├── ApiOrders.java
│       └── WebOrders.java
│
└── shop-app/                     → Application Module
    └── src/main/java/com/company/shop/app/
        ├── ShopApplication.java
        ├── Dependencies.java
        └── KafkaConfig.java
```

### Abhängigkeitsregeln für Module

Die goldenen Regeln bleiben bestehen, werden aber durch das Build-System erzwungen:

**1. Core-Modul ist unabhängig**

```xml
<!-- shop/pom.xml -->
<dependencies>
    <!-- KEINE Abhängigkeiten zu anderen shop-* Modulen! -->
    <!-- Nur Standard-Libraries -->
</dependencies>
```

**2. Kontext-Module abhängig nur vom Core**

```xml
<!-- shop-payment/pom.xml -->
<dependencies>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop</artifactId>          <!-- ✅ Erlaubt -->
        <version>${project.version}</version>
    </dependency>
    
    <!-- Spring Boot für Kafka, JPA etc. -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.kafka</groupId>
        <artifactId>spring-kafka</artifactId>
    </dependency>
    
    <!-- ❌ VERBOTEN! Keine Abhängigkeit zu anderen Kontext-Modulen -->
    <!-- <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop-inventory</artifactId>
    </dependency> -->
</dependencies>
```

**3. App-Modul orchestriert alle**

```xml
<!-- shop-app/pom.xml -->
<dependencies>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop</artifactId>
        <version>${project.version}</version>
    </dependency>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop-payment</artifactId>
        <version>${project.version}</version>
    </dependency>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop-inventory</artifactId>
        <version>${project.version}</version>
    </dependency>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop-shipping</artifactId>
        <version>${project.version}</version>
    </dependency>
    <dependency>
        <groupId>com.company</groupId>
        <artifactId>shop-user</artifactId>
        <version>${project.version}</version>
    </dependency>
</dependencies>
```

### Konkrete Vorteile der Modularisierung

**1. Erzwungene Grenzen**

Vorher: Entwickler können versehentlich `inventory` von `payment` verwenden

Nachher: Compile-Fehler! Das Build-System verhindert unerlaubte Abhängigkeiten

**2. Paralleles Bauen**

```bash
# Nur geänderte Module werden gebaut
./gradlew :shop-payment:build  # 5 Sekunden
# Statt
./gradlew build                # 2 Minuten
```

**3. Klare Verantwortung**

```
Team Payment    → shop-payment/
Team Logistics  → shop-inventory/ + shop-shipping/
Team Frontend   → shop-user/
```

**4. Wiederverwendung**

```gradle
// Anderes Projekt kann Payment-Modul nutzen
dependencies {
    implementation 'com.company:shop-payment:1.0.0'
    implementation 'com.company:shop-core:1.0.0'
}
```

### Migrations-Strategie

**Phase 1: Vorbereitung (1-2 Tage)**
- Stellen Sie sicher, dass Ihre Package-Struktur bereits Context-Driven ist
- Identifizieren Sie alle Abhängigkeiten zwischen Kontexten
- Brechen Sie zirkuläre Abhängigkeiten auf

**Phase 2: Core extrahieren (1 Tag)**
- Erstellen Sie das `shop` Modul (nicht shop-core!)
- Verschieben Sie alle Root-Level Klassen (Product.java, Order.java, etc.)
- Verschieben Sie alle geteilten Value Objects (Money.java, OrderId.java)
- Verifizieren Sie: Core hat KEINE Abhängigkeiten zu anderen Modulen

**Phase 3: Kontext für Kontext (pro Kontext 1-2 Tage)**
- Beginnen Sie mit dem unabhängigsten Kontext (z.B. payment)
- Erstellen Sie das Modul `shop-payment`
- Erstellen Sie `storage/` Package für ORM-Klassen
- Verschieben Sie den Code
- Verifizieren Sie die Abhängigkeiten im pom.xml

**Phase 4: App-Modul (1 Tag)**
- Erstellen Sie `shop-app`
- Verschieben Sie Startup-Code (ShopApplication.java)
- Verschieben Sie Dependency Injection (Dependencies.java)
- Verschieben Sie Framework-Konfiguration (KafkaConfig.java)
- Fügen Sie alle Module als Abhängigkeiten hinzu

### Beispiel: Maven Multi-Module Setup

```xml
<!-- Root pom.xml -->
<project>
    <groupId>com.company</groupId>
    <artifactId>shop-parent</artifactId>
    <packaging>pom</packaging>
    <version>1.0.0</version>
    
    <modules>
        <module>shop</module>
        <module>shop-payment</module>
        <module>shop-inventory</module>
        <module>shop-shipping</module>
        <module>shop-user</module>
        <module>shop-app</module>
    </modules>
    
    <properties>
        <java.version>17</java.version>
        <spring.boot.version>3.2.0</spring.boot.version>
    </properties>
    
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

```xml
<!-- shop/pom.xml - Domain Core -->
<project>
    <parent>
        <groupId>com.company</groupId>
        <artifactId>shop-parent</artifactId>
        <version>1.0.0</version>
    </parent>
    
    <artifactId>shop</artifactId>
    
    <dependencies>
        <!-- KEINE Abhängigkeiten zu anderen shop-* Modulen! -->
        <!-- Nur minimale Standard-Libraries -->
    </dependencies>
</project>
```

```xml
<!-- shop-payment/pom.xml -->
<project>
    <parent>
        <groupId>com.company</groupId>
        <artifactId>shop-parent</artifactId>
        <version>1.0.0</version>
    </parent>
    
    <artifactId>shop-payment</artifactId>
    
    <dependencies>
        <!-- Domain Core -->
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <!-- Spring Data JPA für storage/ -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <!-- Kafka für KafkaPaymentMessage -->
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
        
        <!-- H2 Database für Tests -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

```xml
<!-- shop-app/pom.xml - Orchestrator -->
<project>
    <parent>
        <groupId>com.company</groupId>
        <artifactId>shop-parent</artifactId>
        <version>1.0.0</version>
    </parent>
    
    <artifactId>shop-app</artifactId>
    <packaging>jar</packaging>
    
    <dependencies>
        <!-- Alle Module einbinden -->
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop-payment</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop-inventory</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop-shipping</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>com.company</groupId>
            <artifactId>shop-user</artifactId>
            <version>${project.version}</version>
        </dependency>
        
        <!-- Spring Boot Starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### Anti-Patterns vermeiden

**❌ Technische Module**
```
shop-domain/
shop-infrastructure/
shop-application/
```
→ Bringt Sie zurück zum Ausgangsproblem!

**❌ Shared-/Common-Module**
```
shop-shared/
shop-common/
```
→ Wird schnell zur Müllhalde für alles

**❌ Zu frühe Modularisierung**
- Bei < 10.000 LOC: Bleiben Sie bei Packages
- Bei < 3 Entwicklern: Bleiben Sie bei Packages
- Erst wenn echte Grenzen entstehen: Modularisieren

**✅ Context-Driven Module**
```
shop-core/       → Domain
shop-payment/    → Business Context
shop-inventory/  → Business Context
shop-shipping/   → Business Context
```

### Checkliste: Sind Sie bereit für Module?

- [ ] Package-Struktur ist bereits Context-Driven
- [ ] Keine zirkulären Abhängigkeiten zwischen Kontexten
- [ ] Core-Entities sind klar definiert
- [ ] Teams haben klare Verantwortungsbereiche
- [ ] Build-Zeiten werden zum Problem (> 2 Minuten)
- [ ] Mehrere Deployment-Einheiten gewünscht

Wenn Sie 4+ Punkte abhaken können: Modularisierung lohnt sich!

## Von Modulen zu Microservices

### Wann macht der Schritt zu Microservices Sinn?

Microservices sind KEIN automatischer nächster Schritt. Sie bringen erhebliche Komplexität mit sich. Erwägen Sie Microservices nur wenn:

**Organisatorische Trigger:**
- Mehrere autonome Teams (5+ Teams)
- Teams benötigen unabhängige Deployment-Zyklen
- Unterschiedliche Technology-Stacks erforderlich
- Klare Ownership-Grenzen etabliert

**Technische Trigger:**
- Unterschiedliche Skalierungsanforderungen (Payment braucht 10x mehr Instanzen als Inventory)
- Einzelne Kontexte verursachen System-weite Ausfälle
- Deployment-Prozess dauert > 30 Minuten
- Module sind bereits sauber getrennt und stabil

**Business Trigger:**
- Compliance-Anforderungen (z.B. Payment-Daten isolieren)
- Multi-Tenancy mit Kontext-spezifischer Isolation
- Verschiedene SLAs für verschiedene Kontexte

**⚠️ Warnung:** Wenn Sie < 20 Entwickler haben oder Ihre Module noch nicht stabil sind, bleiben Sie beim Modular Monolith!

### Die natürliche Evolution: Context = Service

Die gute Nachricht: Ihre Context-Driven Module sind bereits perfekte Service-Kandidaten!

**Evolution im Überblick:**

```
Packages (Phase 1)
    ↓
Module (Phase 2)
    ↓
Services (Phase 3)
```

**Der Übergang:**

```
Multi-Module Monolith:
shop/
├── shop-core/
├── shop-payment/
├── shop-inventory/
├── shop-shipping/
└── shop-app/              → Orchestriert alles in einem Prozess
```

```
Microservices:
├── payment-service/       → Eigenständiger Service
│   ├── src/
│   ├── Dockerfile
│   └── deployment.yaml
│
├── inventory-service/     → Eigenständiger Service
│   ├── src/
│   ├── Dockerfile
│   └── deployment.yaml
│
├── shipping-service/      → Eigenständiger Service
│   ├── src/
│   ├── Dockerfile
│   └── deployment.yaml
│
└── api-gateway/          → Einstiegspunkt
    ├── src/
    ├── Dockerfile
    └── deployment.yaml
```

### Service-Grenzen definieren

**1. Ein Kontext = Ein Service**

Ihre bestehenden Module werden zu Services:

```
shop-payment/     →  payment-service/
shop-inventory/   →  inventory-service/
shop-shipping/    →  shipping-service/
shop-user/        →  api-gateway/
```

**2. Core-Module aufteilen**

Der `shop-core` mit den Domain-Entities muss repliziert werden:

```
payment-service/
└── src/main/java/com/company/payment/
    ├── Payment.java              ← Von shop-core kopiert
    ├── PayPalPayment.java        ← Von shop-payment kopiert
    └── LoggedPayment.java

inventory-service/
└── src/main/java/com/company/inventory/
    ├── Product.java              ← Kopie aus shop-core
    ├── Products.java
    ├── Order.java                ← Nur relevante Felder
    └── WarehouseProducts.java
```

**3. Shared Kernel Pattern**

Für wirklich geteilte Konzepte:

```
// Shared Library (Maven/Gradle Dependency)
shop-common/
└── src/main/java/com/company/common/
    ├── OrderId.java        → Value Object
    ├── Money.java          → Value Object
    └── CustomerId.java     → Value Object
```

### Service-Kommunikation

**Synchrone Kommunikation: REST/gRPC**

```java
// payment-service ruft inventory-service auf
public final class PayPalPayment implements Payment {
    private final Products products;  // HTTP Client zum Inventory Service
    
    public Receipt process(Order order) {
        // 1. Prüfe Verfügbarkeit
        boolean available = products
            .checkAvailability(order.productIds());
        
        if (!available) {
            throw new IllegalStateException("Products not available");
        }
        
        // 2. Verarbeite Zahlung
        String transactionId = payPalApi.charge(order.amount());
        
        // 3. Reserviere Produkte
        products.reserve(order.productIds());
        
        return new Receipt(transactionId, order.amount());
    }
}
```

**Asynchrone Kommunikation: Event-Driven mit Kafka**

```java
// payment-service publiziert Event via Kafka
public final class KafkaPaymentMessage implements Payment {
    private final Payment origin;
    private final KafkaTemplate<String, PaymentEvent> kafka;
    
    public Receipt process(Order order) {
        Receipt receipt = origin.process(order);
        
        // Event zu Kafka Topic publizieren
        kafka.send("payment-events", 
            new PaymentCompleted(
                receipt.id(), 
                order.amount(), 
                order.id()
            )
        );
        
        return receipt;
    }
}

// payment-service/app/KafkaConfig.java
@Configuration
public class KafkaConfig {
    
    @Bean
    public KafkaTemplate<String, PaymentEvent> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
    
    @Bean
    public ProducerFactory<String, PaymentEvent> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, 
                  "localhost:9092");
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, 
                  StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
                  JsonSerializer.class);
        return new DefaultKafkaProducerFactory<>(config);
    }
}

// inventory-service reagiert auf Event via Kafka
public final class KafkaReceivedProducts implements Products {
    private final Products origin;
    
    @KafkaListener(topics = "payment-events", 
                   groupId = "inventory-service")
    public void onPaymentCompleted(PaymentCompleted event) {
        log.info("Received payment event from Kafka: {}", event);
        origin.reserve(event.orderId());
    }
    
    // Andere Methoden delegieren an origin...
    public boolean checkAvailability(List<String> productIds) {
        return origin.checkAvailability(productIds);
    }
}

// inventory-service/app/KafkaConfig.java
@Configuration
@EnableKafka
public class KafkaConfig {
    
    @Bean
    public ConsumerFactory<String, PaymentEvent> consumerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, 
                  "localhost:9092");
        config.put(ConsumerConfig.GROUP_ID_CONFIG, 
                  "inventory-service");
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, 
                  StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, 
                  JsonDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(config);
    }
    
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, PaymentEvent> 
           kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, PaymentEvent> factory 
            = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```

### Daten-Management Strategien

**1. Database per Service**

Jeder Service hat seine eigene Datenbank:

```
payment-service     → payment-db (PostgreSQL)
inventory-service   → inventory-db (PostgreSQL)
shipping-service    → shipping-db (MongoDB)
```

**2. Shared Database (Anti-Pattern, aber manchmal pragmatisch)**

```
# Nur während der Migration
payment-service  ──┐
inventory-service ─┼──→ shop-db
shipping-service ──┘
```

⚠️ Ziel: Jeder Service hat eigene DB, aber Migration kann schrittweise erfolgen.

**3. Event Sourcing für Konsistenz**

```
Order Created
    ↓
Payment Processing
    ↓
Payment Completed Event
    ↓
├─→ Inventory: Reserve Products
└─→ Shipping: Prepare Shipment
```

### Migrations-Strategie: Strangler Fig Pattern

**Phase 1: Modular Monolith beibehalten (Woche 1-2)**

```
┌─────────────────────────────────┐
│     Monolithic Application       │
│  ┌────────┐  ┌──────────┐       │
│  │Payment │  │Inventory │       │
│  └────────┘  └──────────┘       │
└─────────────────────────────────┘
```

**Phase 2: Ersten Service extrahieren (Woche 3-6)**

```
┌──────────────────────────────┐
│   Monolithic Application     │
│  ┌──────────┐                │
│  │Inventory │                │    ┌─────────────────┐
│  └──────────┘                │───→│ Payment Service │
│                               │    └─────────────────┘
└──────────────────────────────┘
```

Beginnen Sie mit dem am besten isolierten Kontext (meist Payment oder Shipping).

**Phase 3: Schrittweise weitere Services (Woche 7-20)**

```
┌──────────────────────┐
│ Remaining Monolith   │
│                      │
└──────────────────────┘
         │
    ┌────┴────┬────────────┬─────────────┐
    ↓         ↓            ↓             ↓
┌─────────┐ ┌──────────┐ ┌─────────┐ ┌────────┐
│Payment  │ │Inventory │ │Shipping │ │Gateway │
│Service  │ │Service   │ │Service  │ │        │
└─────────┘ └──────────┘ └─────────┘ └────────┘
```

**Phase 4: Monolith eliminiert (Optional)**

```
        ┌────────────────┐
        │   API Gateway   │
        └────────────────┘
                │
    ┌───────────┼───────────┬───────────┐
    ↓           ↓           ↓           ↓
┌─────────┐ ┌──────────┐ ┌─────────┐ ┌──────┐
│Payment  │ │Inventory │ │Shipping │ │User  │
│Service  │ │Service   │ │Service  │ │Svc   │
└─────────┘ └──────────┘ └─────────┘ └──────┘
```

### Praktisches Beispiel: Payment Service extrahieren

**Schritt 1: Service-Projekt aufsetzen**

```
payment-service/
├── src/main/java/com/company/payment/
│   ├── app/
│   │   ├── PaymentApplication.java     → Main-Klasse
│   │   ├── Dependencies.java           → DI
│   │   └── KafkaConfig.java            → Kafka-Konfiguration
│   ├── Payment.java                    → Von shop/ kopiert
│   ├── PayPalPayment.java              → Von shop-payment kopiert
│   ├── StripePayment.java
│   ├── KafkaPaymentMessage.java        → Kafka Producer
│   ├── ApiPayments.java                → REST API
│   └── storage/
│       ├── PaymentEntity.java          → JPA Entity
│       └── PaymentJpa.java             → JPA Repository
├── src/main/resources/
│   └── application.yml
├── Dockerfile
└── pom.xml
```

**Schritt 2: Main-Klasse und Dependency Injection**

```java
// app/PaymentApplication.java
@SpringBootApplication
public class PaymentApplication {
    public static void main(String[] args) {
        SpringApplication.run(PaymentApplication.class, args);
    }
}

// app/Dependencies.java
@Configuration
public class Dependencies {
    
    @Bean
    public Payment payment(
            PaymentJpa jpa,
            KafkaTemplate<String, PaymentEvent> kafka) {
        
        Payment core = new PayPalPayment();
        
        return new KafkaPaymentMessage(      // Kafka Events
            kafka,
            new DbPayment(                    // Persistierung
                jpa,
                new LoggedPayment(            // Logging
                    new ValidPayment(         // Validierung
                        core
                    )
                )
            )
        );
    }
    
    @Bean
    public ApiPayments apiPayments(Payment payment) {
        return new ApiPayments(payment);
    }
}

// app/KafkaConfig.java
@Configuration
public class KafkaConfig {
    
    @Bean
    public KafkaTemplate<String, PaymentEvent> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
    
    @Bean
    public ProducerFactory<String, PaymentEvent> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, 
                  "localhost:9092");
        return new DefaultKafkaProducerFactory<>(config);
    }
}
```

**Schritt 3: REST API definieren**

```java
// ApiPayments.java - keine technischen Suffixe!
@RestController
@RequestMapping("/api/payments")
public final class ApiPayments {
    private final Payment payment;
    
    public ApiPayments(Payment payment) {
        this.payment = payment;
    }
    
    @PostMapping
    public Receipt processPayment(@RequestBody PaymentRequest request) {
        return payment.process(
            new Order(request.orderId(), request.amount())
        );
    }
    
    @GetMapping("/{id}")
    public Receipt getPayment(@PathVariable String id) {
        return payment.find(id);
    }
}
```

**Schritt 4: Storage-Implementierung**

```java
// storage/PaymentEntity.java
@Entity
@Table(name = "payments")
public class PaymentEntity {
    @Id
    private String id;
    
    @Column(nullable = false)
    private BigDecimal amount;
    
    @Column(nullable = false)
    private String orderId;
    
    @Column(nullable = false)
    private String status;
    
    // Getters, Setters
}

// storage/PaymentJpa.java
public interface PaymentJpa extends JpaRepository<PaymentEntity, String> {
    List<PaymentEntity> findByOrderId(String orderId);
}

// DbPayment.java (außerhalb storage/)
public final class DbPayment implements Payment {
    private final Payment origin;
    private final PaymentJpa jpa;
    
    public Receipt process(Order order) {
        Receipt receipt = origin.process(order);
        
        PaymentEntity entity = new PaymentEntity();
        entity.setId(receipt.id());
        entity.setAmount(order.amount().value());
        entity.setOrderId(order.id());
        entity.setStatus("COMPLETED");
        jpa.save(entity);
        
        return receipt;
    }
}
```

**Schritt 5: Monolith umleiten**

```java
// Im Monolith: shop-app
public final class RemotePayment implements Payment {
    private final RestTemplate http;
    
    public Receipt process(Order order) {
        // Alt: Lokale Implementierung
        // Neu: Delegiere an Microservice
        return http.postForObject(
            "http://payment-service/api/payments",
            new PaymentRequest(order.id(), order.amount()),
            Receipt.class
        );
    }
}
```

**Schritt 6: Feature Toggle für schrittweisen Rollout**

```java
// app/Dependencies.java im Monolith
@Configuration
public class Dependencies {
    
    @Value("${feature.remote-payment:false}")
    private boolean useRemotePayment;
    
    @Bean
    public Payment payment(RestTemplate http) {
        Payment payment = useRemotePayment 
            ? new RemotePayment(http)          // Microservice
            : new PayPalPayment();             // Monolith
            
        return new LoggedPayment(
            new ValidPayment(payment)
        );
    }
}
```

```yaml
# application.yml - Schrittweise aktivieren
feature:
  remote-payment: ${REMOTE_PAYMENT:false}  # Start: false
```

### Infrastructure & Deployment

**Docker Compose für lokale Entwicklung:**

```yaml
version: '3.8'
services:
  # Kafka & Zookeeper
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  
  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
  
  # Databases
  payment-db:
    image: postgres:15
    environment:
      POSTGRES_DB: payment
      POSTGRES_USER: payment
      POSTGRES_PASSWORD: payment
    ports:
      - "5432:5432"
  
  inventory-db:
    image: postgres:15
    environment:
      POSTGRES_DB: inventory
      POSTGRES_USER: inventory
      POSTGRES_PASSWORD: inventory
    ports:
      - "5433:5432"
  
  # Services
  payment-service:
    build: ./payment-service
    ports:
      - "8081:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://payment-db:5432/payment
      - SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
    depends_on:
      - payment-db
      - kafka
      
  inventory-service:
    build: ./inventory-service
    ports:
      - "8082:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://inventory-db:5432/inventory
      - SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
    depends_on:
      - inventory-db
      - kafka
      
  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    depends_on:
      - payment-service
      - inventory-service
```

**Kubernetes Deployment:**

```yaml
# payment-service-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: payment-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: payment-service
  template:
    metadata:
      labels:
        app: payment-service
    spec:
      containers:
      - name: payment-service
        image: company/payment-service:1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_DATASOURCE_URL
          valueFrom:
            secretKeyRef:
              name: payment-db-secret
              key: url
        - name: SPRING_KAFKA_BOOTSTRAP_SERVERS
          value: "kafka:9092"
---
apiVersion: v1
kind: Service
metadata:
  name: payment-service
spec:
  selector:
    app: payment-service
  ports:
  - port: 80
    targetPort: 8080
```

**pom.xml für Payment Service:**

```xml
<project>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
    </parent>
    
    <groupId>com.company</groupId>
    <artifactId>payment-service</artifactId>
    <version>1.0.0</version>
    
    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <!-- Spring Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        
        <!-- PostgreSQL -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
        </dependency>
        
        <!-- Kafka -->
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
        
        <!-- Testing -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### Anti-Patterns vermeiden

**❌ Distributed Monolith**
```
Services teilen eine Datenbank
Synchrone Ketten: A → B → C → D
Tightly coupled: Jede Änderung betrifft alle Services
```

**❌ Zu feine Granularität**
```
User-Service
User-Profile-Service
User-Preferences-Service
User-Settings-Service
→ Zu viel Overhead, zu wenig Nutzen
```

**❌ Falsche Service-Grenzen (technisch statt fachlich)**
```
Service nach Layern:
- API-Service
- Business-Logic-Service
- Data-Service
→ Zurück zu Layer-Pattern!

Oder mit technischen Suffixen:
- PaymentProcessor-Service
- InventoryManager-Service
- ShippingHandler-Service
→ Technische Namen statt Business-Kontexte!
```

**✅ Richtige Service-Grenzen (Context-Driven)**
```
Services nach Business Context:
- payment-service (PayPalPayment, StripePayment)
- inventory-service (WarehouseProducts, DbProducts)
- shipping-service (DhlShipment, UpsShipment)
→ Jeder Service = vollständiger Business-Kontext
→ Namen beschreiben DINGE, keine Aktionen
```

### Monitoring & Observability

```java
// Distributed Tracing mit OpenTelemetry
public final class TracedPayment implements Payment {
    private final Payment origin;
    
    public Receipt process(Order order) {
        Span.current().setAttribute("payment.amount", 
                                   order.amount());
        Span.current().setAttribute("payment.orderId", 
                                   order.id());
        
        return origin.process(order);
    }
}
```

**Logging-Strategie:**

```java
// Korrelations-IDs über Services hinweg
public final class TrackedRequest implements Filter {
    
    public void doFilter(ServletRequest request, 
                        ServletResponse response, 
                        FilterChain chain) {
        String correlationId = getOrCreateCorrelationId(request);
        MDC.put("correlationId", correlationId);
        
        try {
            chain.doFilter(request, response);
        } finally {
            MDC.remove("correlationId");
        }
    }
}
```

### Checkliste: Sind Sie bereit für Microservices?

- [ ] Module sind seit > 6 Monaten stabil
- [ ] Klare Service-Grenzen identifiziert (Context-Driven!)
- [ ] Team-Ownership etabliert (min. 1 Team pro Service)
- [ ] CI/CD Pipeline vorhanden
- [ ] Monitoring & Logging Infrastructure bereit
- [ ] Container-Orchestrierung verfügbar (K8s, ECS, etc.)
- [ ] Kafka oder anderes Messaging-System für Event-Driven Communication
- [ ] Datenbank-Migration-Strategie definiert (Database per Service)
- [ ] storage/ Pakete in allen Services für ORM-Isolation
- [ ] app/ Pakete mit Main-Klasse, DI und Configs in allen Services
- [ ] Entwickler verstehen verteilte Systeme (CAP, Eventual Consistency)
- [ ] Budget für erhöhten Ops-Aufwand vorhanden
- [ ] Feature Toggles für schrittweisen Rollout implementiert

**Wenn Sie < 8 Punkte abhaken können: Bleiben Sie beim Modular Monolith!**

### Der Weg zurück: Reverse-Migration

Microservices können zurück zu Monolithen migriert werden, wenn nötig:

```
Services → Module → Packages
```

Ihre Context-Driven Struktur macht auch das einfach, da die Grenzen klar sind!

## Fazit

Business Context-Driven Packaging macht Ihr Context-Diagramm im Code sichtbar. Business Analysten und Entwickler sprechen endlich dieselbe Sprache. Der Code wird zum lebenden Architektur-Dokument.

Haben Sie Context-Driven Packaging ausprobiert? Teilen Sie Ihre Erfahrungen in den Kommentaren!

## Weiterführende Links

**Grundlegende Artikel**

Happy Packaging! (javadevguy)

Modular Monoliths - Simon Brown (Slides)

**Architektur-Pattern Quellen**

Clean Architecture Blog (Robert C. Martin)

Hexagonal Architecture (Alistair Cockburn)

Vertical Slice Architecture (Jimmy Bogard)

Package by Feature (Philipp Hauer)