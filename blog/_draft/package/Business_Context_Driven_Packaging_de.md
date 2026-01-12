**Business Context-Driven Package Structure: Der fehlende Link zwischen Business und Code**

**Warum Ihre Package-Struktur das Business Context-Diagramm Ihres Systems sein sollte**

**Das Problem: Die Übersetzung-Lücke**

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
**Beispiel 2: Clean Architecture** (nach Robert C. Martin)
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
**Beispiel 3: DDD Structure** (nach Eric Evans)
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
Wo ist Payment?

Wo ist das Inventory?

Wo ist Shipping?

Wo ist User aus dem Context-Diagramm?

Sie müssen mental übersetzen:

* Payment ist vermutlich im service Package versteckt? (Layered)  
* Oder vielleicht in infrastructure? (CleanArchitecture)  
* Könnte auch in domain oder application liegen?" (DDD)  
* Wahrscheinlich in adapter oder port? (HexagonalArchitecture)  
* Vielleicht in features? oder shared? (VerticalSlices)

Man muss raten, ob die z. B. PayPal-Anbindung in infrastructure, external oder einem generischen service-Paket liegt.

Selbst moderne Patterns wie Vertical Slices konzentrieren sich häufig primär auf technische Funktionalitäten und Prozesslogik (Verben), anstatt auf Domänenobjekte (Dinge) und externe Kontexte als zentrale Bestandteile der Paketstruktur unmittelbar abzubilden.

## **Die Lösung: Context-Driven Packaging**

Die Lösung ist verblüffend einfach: Ihre Package-Struktur sollte in erster Linie das Context-Diagramm widerspiegeln.
```
com.company.shop  
├── payment/  
├── inventory/  
├── shipping/  
└── user/
```
Dies ist ein entscheidender **strategischer Aspekt** der Softwarearchitektur: Durch die direkte Abbildung der fachlichen Kontexte im Code wenden wir die **Ubiquitous Language** (allgegenwärtige Sprache) konsequent auf jeder Ebene der Systemorganisation an.

**Vorher/Nachher: Echtes Projekt-Beispiel**

**Vorher (Technische Pakete)**

**Aufgabe:** "Behebe den Bug in der PayPal-Anbindung"

⏱️ 15 Minuten Code-Navigation

🔍 Suche in: service/, integration/, client/, external/

🤔 "Ist es PaymentService? PayPalClient? PaymentProcessor? PaymentAdapter?"

**Nachher (Context-Driven)**

**Aufgabe:** "Behebe den Bug in der PayPal-Anbindung"

⏱️ 30 Sekunden Code-Navigation

🎯 **Direkt zu:** com.company.shop.payment/PayPalPayment.java

✅ **Sofort klar:** Das ist die PayPal-Implementierung. Keine Übersetzung nötig.

**Messbare Vorteile**

**1. Code-Navigation**

* **Vorher:** 1 bis 2 Minuten für Feature-Lokalisierung  
* **Nachher:** 10 bis 30 Sekunden

**Verbesserung:** ca. 80-90% weniger Zeit

**2. Onboarding neuer Entwickler**

* **Vorher:** "Wo finde ich die Versand-Logik?" → 5 Minuten Erklärung  
* **Nachher:** "Schau ins shipping Package" → 30 Sekunden

**Verbesserung:** ca. 99% weniger Erklärungsaufwand

**3. Business-Developer Kommunikation**

* **Vorher:** Business Analyst sagt "Versand-Problem" → Developer übersetzt mental zu technischen Packages  
* **Nachher:** Business Analyst sagt "Versand-Problem" → Developer geht direkt zu com.company.shop.shipping/

**Verbesserung:** Kein Übersetzungsaufwand

## **Die drei goldenen Regeln**

**Regel 1: Pakete sollten niemals von Sub-Paketen abhängen**

Root Package = Core Domäne, unabhängig von allem.

* z. B. Payment.java, Order.java , OrderId.java

als Interfaces , (abstrakte) Klassen oder Value-Objects

Sub-Pakete = Implementierungen, abhängig vom Core.

* z. B. payment/PayPalPayment.java implementiert Payment.java

**Regel 2: Sub-Pakete führen keine neuen Konzepte ein, nur Details**

Keine neuen fachlichen Konzepte in Sub-Packages, die nicht im Root als Interface existieren.

* z. B. payment/PayPalPayment.java = Detail der Payment-Integration.  
* z. B. application/SpringShop.java oder main/ShopMain.java

für die Software selbst als Root und Entry-Point der App

**Regel 3: Pakete und Klassen spiegeln Business-Kontexte wider, nicht technische Rollen**

✅ **Richtig: Paketnamen aus Context-Diagramm**

* payment/, inventory/, shipping/ (business concepts or external systems)  
* application/ oder main/ für die Software selbst als Root und Entry-Point der App  
* user/ oder human/ (GUI interfaces or REST interfaces for GUI e.g. React)  
* exchange/ endpoint/(HTTP / REST interfaces)  
* storage/ or database/ (interfaces)

✅ **Richtig: Nomen (Dinge) mit beschreibenden Präfixen**

* Shipment, Payment, Order  
* ValidDhlShipment, CachedDhlShipment, DbDhlShipment  
* PayPalPayment, StripePayment, PayPal (use HttpClient), Stripe (...Http)  
* Products, ProductStock,WerehouseProducts, DbProducts  
* WerehouseProducts, WebUser, DbUser  
* Shop, oder ShopApplication (use HttpClient), Stripe (...Http)

❌ **Vermeiden: Technische Paketnamen**

* service/, repository/, controller/, presentation/, persistence/  
* common/, shared/, util/, helper/, interactors/, model/, domain/  
* adapter/, client/, wrapper/, facade/, usecases/, dto/

❌ **Vermeiden: Verben und technische Suffixe**
```
* -Contorller, -Service,-Repository, -Calculator, -Validator  
* -Processor, -Manager, -Validator, -Builder, -Client, -Proxy  
* -Consumer, -Producer, -Publisher, -Broker, -Listener, -Observer  
* -Util(s), -Utilities, -Helper, -Tool, -Factory
```
## **Implementierung: Schritt für Schritt zum Context-Driven Code**

**Schritt 1: Kontext-Analyse**

Nehmen Sie das offizielle Context-Diagramm Ihres Systems zur Hand. Identifizieren Sie:

* Die zentrale Domäne (z.B. "Shop")  
* Alle externen Systeme (z.B. PayPal, Warehouse, DHL)  
* Alle Akteure/Schnittstellen (z.B. User, API)  
* Die Kernkonzepte der Domäne (z.B. Product, Order, Payment)

**Beispiel-Analyse:**

* Hauptdomäne: Shop - Composition Root Pattern  
* Externe Systeme: PayPal, Stripe, Warehouse, DHL, UPS  
* Schnittstellen: Web-User, REST-API  
* Kernkonzepte: Product, Order, Payment, Shipment

—

**Schritt 2: Root-Package und Root-Klasse anlegen**

Legen Sie alle zentralen Domänen-Klassen direkt in das Root-Package.

* Die allerwichtigsten Konzepte und Ideen sollten "am Anfang" stehen, das heißt im obersten "Package" der Software.  
* Die Software selbst sollte vorzugsweise durch eine zentrale Komposition (als Interface oder abstrakte) Klasse repräsentiert werde, die den Einstiegspukt einer Applikation darstellt. Es stellt den eindeutigen Ort in einer Anwendung, an dem alle fachlichen Konzepte (Module) und technischen Aspekte mittels Decorator-Pattern zusammengefügt werden.
```
com.company.shop/  
├── Product.java              → Domain Entity  
├── Products.java             → Sammlung von Product  
├── Order.java                → Domain Entity  
├── Orders.java               → Sammlung von Order  
├── Payment.java              → Domain Entity  
├── Shipment.java             → Domain Entity  
└── ShopApplication.java      → Composition Root (Interface oder Abstract)
```
**Wichtig:** Nur Interfaces oder abstrakte Klassen! Keine Implementierungen im Root.

**Schritt 3: Kontext-Kapselung**

* Erstellen Sie für jedes fachliches Konzept ein eigenes Paket auf der ersten Ebene: Unterpakete sollten mehr und mehr Details preisgeben aber keine höheren Konzepte mehr einführen.  
* Legen Sie für die zentrale Hauptklasse (System-Komposition) ein Unterpaket z. B. application/ oder main/ an. Dient zu Implmentierung der System-Komposition Klasse aus dem Root-Paket und unteranderem für die Verwenung von DI-Container.
```
com.company.shop/  
├── application/       → Implementierung der System-Komposition  
├── payment/           → Implementierungen: PayPal, Stripe  
├── inventory/         → Integration: Warehouse  
├── shipping/          → DHL, UPS Integration  
├── user/              → Umsetzung der Web, API Schnittstellen  
├── Product.java              → Domain Entity  
├── ProductId.java            → Value Object  
├── Products.java             → Sammlung von Product  
├── Order.java                → Domain Entity  
├── OrderId.java              → Value Object  
├── Orders.java               → Sammlung von Order  
├── Payment.java              → Domain Entity  
├── Shipment.java             → Domain Entity  
└── ShopApplication.java      → System Root (Interface oder Abstract)
```
Diese Vorgehensweise erlaubt den Lesern, sich gleich einen Überblick zu verschaffen und auf jeder Stufe der Package-Hierarchie sich zu entscheiden, ob und welche Details noch wichtig wären.

**Schritt 4: Framework-Isolierung**

Bei der Verwendung von Ökosystem wie Jakarta EE oder des Spring Frameworks werden oft zusätliche Daten-Transfer-Objekete (DTOs) z. B. für die Anbindung von Datenbanken, Umsetzung von REST-Api benötigt oder Serialisierung von Nachrichten und Ereignissen benötigt.

Vorzugsweise sollten soche technischen Aspekte bzw. Abhängigkeiten von Frameworks in eigene Projekte ausgelagert werden und als “Dependencies“ in das eingentliche Projekt eingebunden.
```
com.company.shop     → depend on *-endpoint, *-resource, *-storage *-messaging  
com.company.shop-endpoint  → HTTP Client mit JSON DTOs  
com.company.shop-resource  → REST Service mit JSON DTOs  
com.company.shop-storage   → ORM DTOs Klassen mit @Entity, @Repository  
com.company.shop-messaging → AVRO Klassen für Kafka-Integration
```
Ein **Real-World-Beispiel** als empirischer Beweis ist [Self-XDSD](https://github.com/self-xdsd). Es handelt sich um ein großes, produktives Business-System, welches genau diesen Ansatz verfolgt und seit 2019 in Production betrieben wird. Ein weterse Beispiel ist "Gerec" eine REST-Client-Bibliothek.

**Alternativ (für Projekt mit kleiner Codebasen geeignet):**

Isolieren Sie alle technischen Belange in ein dediziertes Paket z. B. exchange/ gefolgt von weiteren Unterpaketen wie:
```
* endpoint/ (für HTTP Client und Hilfskalsse),  
* ressource/ (für HTTP REST Controller mit JSON DTOs und Hilfsklassen),  
* storage/, (ORM Klassen mit @Entity, @Repository und Hilfsklassen)  
* messaging/ (AVRO Klassen für Kafka-Integration und Hilfsklassen),
```
Die Klassen in diesen techischen Paketen können dann in den fachlichen Paketen auf der ersten Ebene verwendet werden (beachte die Regel 1). Die Domain-Interfaces sollten niemals soche DTO-Klassen kennen.
```
com.company.shop/  
├── application/                        → Root Composition (nur Main, DI, Config)  
│     ├── SpringShopApplication.java    → Main-Klasse  
│     ├── SpringShopConfig.java         → Dependency Injection  
│     ├── SpringKafkaConfig.java        → Kafka-Config  
│     └── SpringWebConfig.java          → Web-Config, Securety-Config  
├── exchange  
│     ├── endpoint                      → HTTP Client mit JSON DTOs  
│     ├── resource                      → REST Service mit JSON DTOs  
│     ├── storage                       → ORM Klassen mit @Entity, @Repository  
│     └── messaging                     → AVRO Klassen für Kafka-Integration  
...
```
**Schritt 5: Decorator-Pattern für Verantwortlichkeiten und Cross-Cutting Concerns**

Verwenden Sie als konkrete Implementierungsstrategie für Verantwortlichkeiten und Cross-Cutting Concerns das Decorator-Pattern an.   

Nutzen Sie Präfixe im Namen der Klassen (z. B. 'PayPal', 'Db' oder 'Cached') Dies reduziert die kognitive Last, da Entwickler nicht erst die Datei öffnen müssen, um das 'Wie' der Umsetzung zu verstehen.
```
com.company.shop/  
├── application/                → Root Composition (Main, DI, Config)  
├── payment/  
│    ├── CachedPayment.java     → Cache-Decorator  
│    ├── DbPayment.java         → Datenbank-Decorator (JpaPayment)  
│    ├── DbPayments.java        → Datenbank-Decorator (JpaPayments)  
│    ├── PaymentMessage.java    → Kafka Consumer (Message)  
│    ├── PaymentEvent.java      → Kafka Producer (Event)  
│    ├── PayPalPayment.java     → Haupt-Implementierung  
│    ├── StripePayment.java     → Alternative Implementierung  
│    ├── LoggedPayment.java           → Logging-Decorator  
│    ├── NotifiedPayment.java         → Email-Decorator  
│    ├── ValidPayment.java            → Validierungs-Decorator  
│    └── FakePayment.java             → Test-Implementierung  
├── Payment.java  
├── PaymentId.java  
...

com.company.shop-endpoint  
├── paypal/  
│    ├── PayPalEndpoint.java     → HTTP Client  
│    ├── PayPalRequest.java      → JSON DTO Request  
│    ├── PayPalResponse.java     → JSON DTO Response  
│    ...  
├── warehouse  
│    ├── Warehouse.java          → HTTP Client  
│    ├── WarehouseRequest.java   → JSON DTO Request  
│    ├── WarehouseResponse.java  → JSON DTO Response  
...  ...

com.company.shop-resource     → JSON DTOs for REST Services  
├── order/  
│    ├── OrderRequest.java   → JSON DTO Request  
│    ├── OrderResponse.java  → JSON DTO Response  
│    ...  
├── product  
│    ├── ProductRequest.java   → JSON DTO Request  
│    ├── ProductResponse.java  → JSON DTO Response  
...  ...

com.company.shop-storage   → ORM DTOs for Database integration  
├── ProductEntity.java     → @Entity  
├── ProductJpa.java        → @Repository  
├── ShipmentEntity.java    → @Entity  
├── ShipmentJpa.java       → @Repository  
├── ProductEntity.java     → @Entity  
└── ProductJpa.java        → @Repository

com.company.shop-messaging → AVRO DTO's for Kafka integration  
├── order/  
│    ├── OrderEvent.java    → AVRO DTO Request  
│    ├── OrdersEvent.java  → AVRO DTO Response  
│    ...  
├── product  
│    ├── ProductEvent.java   → AVRO DTO Request  
│    ├── ProductsEvent.java   → AVRO DTO Response  
...  ...
```
Definieren Sie diese Klassen klar als reine 'Infrastructure Details' oder Datencontainer ohne Geschäftslogik; idealerweise sollten sie über eine eingeschränkte Sichtbarkeit verfügen (z. B. package-private in Java), um eine versehentliche Nutzung außerhalb des Adapters zu verhindern.

**Schritt 6: Dependency Injection und Composition Root Pattern**

Implementieren Sie die Composition Root nach dem Prinzip der Pure DI **im application/ Paket**: Der Anwendungscode sollte ausschließlich Constructor Injection nutzen, damit bleibt er frei von Framework-Wissen. Die Komposition des Objektgraphen erfolgt gesammelt am Einstiegspunkt:

* Spring Boot: main-Methode oder @Configuration-Klassen  
* Jakarta EE (Quarkus): @Observes StartupEvent oder eine @Singleton / @Startup EJB  
* CDI: Programmatische Komposition in einer Factory-Klasse
```java
@Configuration  
@SpringBootApplication  
@ComponentScan(basePackages = {"com.company.shop**"})  
public class SpringShopApplication implements ShopApplication {

  @Value("...pay.pal.url")    
  private String payPalUrl;

  @Value("...warehouse.url")    
  private String warehouseUrl;    
    
  // Entry point of the application    
  public static void main(String[] args) {    
    SpringApplication.run(SpringShopApplication.class, args);    
  }

  @Bean    
  @Override    
  public Payment payment() {

    Payment payment = new PayPalPayment(    
        new PayPalEndpoint(payPalUrl)     // HTTP Client     
    );

    return new PaymentEvent(              // use Kafka producer    
        new NotifiedPayment(              // Email Notification    
            new DbPayment(                // use JPA Storage    
                new LoggedPayment(        // use Log4j Logger    
                    new ValidPayment(     // Valididation  
                       payment    
                    )    
                )    
            )    
        )    
    );  
  }

  @Bean  
  @Override  
  public Products products() {  
    // composable Decorators  
    return new CachedProducts(             // use guava Cache  
        new DbProducts(                    // use JPA Storage  
            new WarehouseProducts(         // translate to Products  
                new Warehouse(             // use HTTP Client  
                    warehouseUrl  
                )  
             )  
         )  
    );  
  }

//...

}
```
**Checkliste für saubere Implementierung**

* Alle Domain-Entities liegen im Root als Interfaces/Abstract Classes  
* Jeder externe Kontext hat ein eigenes Paket auf der ersten Ebene  
* Das app/ Package enthält NUR Framework-Code (Main, DI, Config)  
* Das storage/ Package enthält NUR ORM-spezifische Klassen (@Entity, @Repository)  
* Alle Klassen nutzen Nomen (Substantive), keine Verben  
* Alle Klassen nutzen beschreibende Präfixe (PayPal**, Db**, Valid**, Kafka**)  
* KEINE technischen Suffixe (**Manager, **Service, **Processor, **Consumer, *Producer)  
* KEINE technischen Package-Namen (common/, util/, service/, consumer/, producer/)  
* Decorator-Pattern für Cross-Cutting Concerns (Logging, Caching, Validation)  
* Kafka-Integration (PaymentEvent, ReceivedPayment, Created**, Changed**,…)  
* Abhängigkeiten zeigen nur vom Package zum Root, nie umgekehrt  
* Frameworks sind in separaten Projekte isoliert und werden nur von Decorators verwendet

**Template: E-Commerce**

Jetzt spiegeln wir das Context-Diagramm direkt im Code wider:
```
com.company.shop  
├── application/                    → Startup, DI, Config  
│   ├── SpringKafkaShopConfig.java  
│   └── SpringShopApplication.java  
├── payment/  
│   ├── PayPalPayment.java  
│   ├── StripePayment.java  
│   ├── PaymentEvent.java           → Kafka producer  
│   └── ReceivedPayment.java        → Kafka consumer  
├── inventory/  
│   ├── DbProducts.java  
│   ├── ShipmentEntity.java  
│   ├── ReceivedProducts.java       → Kafka consumer  
│   └── WarehouseProducts.java  
├── shipping/  
│   ├── DhlShipment.java  
│   ├── UpsShipment.java  
│   ├── ReceivedShipment.java       → Kafka  consumer  
├── user/                           → API use exchange/resource/  
│   ├── ApiOrders.java  
│   ├── SecuredOrders.java  
│   └── WebOrders.java  
├── exchange/  
│   ├── storage/  
│   │     ├── ProductEntity.java  
│   │     ├── ProductJpa.java  
│   │     ├── ShipmentEntity.java  
│   │     ├── ShipmentJpa.java  
│   │     ├── ProductEntity.java  
│   │     └── ProductJpa.java  
│   ...  
├── Product.java        
├── ProductId.java   → Value Object  
├── Products.java          → Collection of Product (no Repository!)  
├── Payment.java       
├── Order.java               
├── OrderId.java           → Value Object  
├── Orders.java            → Collection of Product (no Repository!)  
└── ShopApplication.java
```
## **Von Monolithen zu Modulithen**

**Phase 1: Vorbereitung (1-2 Tage)**

* Stellen Sie sicher, dass Ihre Package-Struktur bereits Context-Driven ist  
* Identifizieren Sie alle Abhängigkeiten zwischen Kontexten  
* Brechen Sie zirkuläre Abhängigkeiten auf

**Phase 2: Core extrahieren (1 Tag)**

* Erstellen Sie das shop Modul (nicht shop-core!)  
* Verschieben Sie alle Root-Level Klassen (Product.java, Order.java, etc.)  
* Verschieben Sie alle geteilten Value Objects (Money.java, OrderId.java)  
* Verifizieren Sie: Core hat KEINE Abhängigkeiten zu anderen Modulen

**Phase 3: Kontext für Kontext (pro Kontext 1-2 Tage)**

* Beginnen Sie mit dem unabhängigsten Kontext (z.B. payment)  
* Erstellen Sie das Modul shop-payment  
* Erstellen Sie exchange/storage/ Package für ORM-Klassen  
* Verschieben Sie den Code  
* Verifizieren Sie die Abhängigkeiten im pom.xml

**Phase 4: App-Modul (1 Tag)**

* Erstellen Sie shop-app  
* Verschieben Sie Startup-Code (SpringShopApplication.java)  
* Verschieben Sie Dependency Injection (SpringShopApplicationConfig.java)  
* Verschieben Sie Framework-Konfiguration (SpringKafkaShopConfig.java)  
* Fügen Sie alle Module als Abhängigkeiten hinzu

**Anti-Patterns vermeiden**

**❌ Technische Module**

* shop-domain/, shop-infrastructure/, shop-application/

→ Bringt Sie zurück zum Ausgangsproblem!

**❌ Shared-/Common-Module**

* shop-shared/, shop-common/

→ Wird schnell zur Müllhalde für alles

**❌ Zu frühe Modularisierung**

* Bei < 10.000 LOC: Bleiben Sie bei Packages  
* Bei < 3 Entwicklern: Bleiben Sie bei Packages  
* Erst wenn echte Grenzen entstehen: Modularisieren

**✅ Context-Driven Module**

* shop/ → Core Domain Interfaces  
* shop-payment/ → Business Context  
* shop-inventory/ → Business Context  
* shop-shipping/ → Business Context

**Checkliste: Sind Sie bereit für Module?**

* Package-Struktur ist bereits Context-Driven  
* Keine zirkulären Abhängigkeiten zwischen Kontexten  
* Core-Entities sind klar definiert  
* Teams haben klare Verantwortungsbereiche  
* Build-Zeiten werden zum Problem (> 2 Minuten)  
* Mehrere Deployment-Einheiten gewünscht

**Wenn Sie 4+ Punkte abhaken können: Modularisierung lohnt sich!**

### **Vorlage für eine kompakte Projektstruktur**

Diese folgende Aufteilung ist die logische Konsequenz der **Context-Driven Architecture**. Sie trennt nicht nur die fachlichen Kontexte (Bounded Contexts), sondern isoliert auch die technischen Infrastruktur-Abhängigkeiten pro Kontext.
```
com.company.shop                     → Common Modul (Core Domain Interfaces)  
com.company.shop-application         → Root Composition of Projects as modules

com.company.shop-payment             → Business (Bounded) Context  
com.company.shop-payment-endpoint    → Http Clients with JSON DTOs  
com.company.shop-payment-resource    → REST Services with JSON DTOs  
com.company.shop-payment-storage     → ORM Entity DTOs with Repositories  
com.company.shop-payment-messaging   → AVRO Schema generation of DTOs

com.company.shop-inventory           → Business (Bounded) Context  
com.company.shop-inventory-endpoint  
com.company.shop-inventory-resource  
com.company.shop-inventory-storage  
com.company.shop-inventory-messaging

com.company.shop-shipping             → Business (Bounded) Context  
com.company.shop-shipping-endpoint  
com.company.shop-shipping-resource  
com.company.shop-shipping-storage
```
### **Alternative: Vorlage für eine kompakte Projektstruktur**

Wenn die Anzahl der Projekte zu unübersichtlich wird, kann man die technischen Aspekte innerhalb der Kontext-Module kapseln, anstatt für jeden Aspekt ein eigenes Top-Level-Modul anzulegen:
```
com.company.shop (optional) → Shared kernel (Interfaces & Value Objects)  
com.company.shop-app        → Root Module-Composition of all Projects  
com.company.shop-payment    → Module-Group - MAVEN Parent Project  
├── inventory               → Bounded Context Module  
├── inventory-endpoint  
├── inventory-resource  
├── inventory-storage  
└── inventory-messaging

com.company.shop-inventory   → Module-Group - MAVEN Parent Project  
├── inventory                → Bounded Context Module  
├── inventory-endpoint  
├── inventory-resource  
├── inventory-storage  
└── inventory-messaging

com.company.shop-shipping     → Module-Group - MAVEN Parent Project  
├── shipping                  → Bounded Context Module  
├── shipping-endpoint  
├── shipping-resource  
├── shipping-storage  
└── shipping-messaging
```
Die gezeigten modularen Projektstrukturen folgen dem Prinzip: **"Fachliche Trennung auf Modulebene, technische Trennung auf Sub-Modulebene."**

**Vorteile:**

* **Kein Dependency-Hell:** Der payment-storage zieht keine JPA-Abhängigkeiten in den inventory-resource (REST) Bereich.  
* **Klare Ownership:** Ein Team kann den kompletten payment-Stack (von DB bis API) unabhängig von anderen entwickeln.  
* **Microservice-Ready:** Jede dieser Modul-Gruppen (shop-payment-*) könnte mit minimalem Aufwand in einen eigenen Microservice ausgegliedert werden.

Es ist nicht einfach zu entscheiden, wann es sinnvoller ist, Code in ein gemeinsames Paket (com.company.shop) zu verschieben und ihn wiederzuverwenden.

**Vermeidung von Kopplung:** Wenn alle Module das gleiche OrderId-Objekt aus einem zentralen Paket nutzen, müssen bei einer Änderung an dieser Klasse (z. B. Wechsel von Long zu UUID) alle Module gleichzeitig angefasst werden. Duplikation erlaubt es jedem Kontext, sich in seinem eigenen Tempo zu entwickeln.

**Strategischer Hinweis zur Entkopplung:**

Vorzugsweise sollte das gemeinsame Paket com.company.shop komplett eliminiert werden, indem die notwendigen Value Objects wie OrderId, PaymentId und ProductId in den jeweiligen Kontexten dupliziert werden. Dies verhindert, dass ein "Common"-Modul zur unkontrollierten Müllhalde für alles wird und stellt sicher, dass jeder Bounded Context autonom bleibt. „Lieber Duplikation als die falsche Abstraktion.“ (Sandi Metz, siehe „The Wall Of Coding Wisdom“).

## **Von Modulen zu Microservices**

**Wann macht der Schritt zu Microservices Sinn?**

Microservices sind KEIN automatischer nächster Schritt. Sie bringen erhebliche Komplexität mit sich. Erwägen Sie Microservices nur wenn:

**Organisatorische Trigger:**

* Mehrere autonome Teams (5+ Teams)  
* Teams benötigen unabhängige Deployment-Zyklen  
* Unterschiedliche Technology-Stacks erforderlich  
* Klare Ownership-Grenzen etabliert

**Technische Trigger:**

* Unterschiedliche Skalierungsanforderungen  
  (Payment braucht 10x mehr Instanzen als Inventory)  
* Einzelne Kontexte verursachen System-weite Ausfälle  
* Deployment-Prozess dauert > 30 Minuten  
* Module sind bereits sauber getrennt und stabil

**Business Trigger:**

* Compliance-Anforderungen (z.B. Payment-Daten isolieren)  
* Multi-Tenancy mit Kontext-spezifischer Isolation  
* Verschiedene SLAs für verschiedene Kontexte

**⚠️ Warnung:** Wenn Sie < 20 Entwickler haben oder Ihre Module noch nicht stabil sind, bleiben Sie beim Modular Monolith!

**Die natürliche Evolution: Context = Service**

**Die gute Nachricht:** Ihre Context-Driven Module sind bereits perfekte Service-Kandidaten!

**Anti-Patterns vermeiden**

**❌ Distributed Monolith**

* Services teilen eine Datenbank  
* Synchrone Ketten: A → B → C → D  
* Tightly coupled: Jede Änderung betrifft alle Services

**❌ Zu feine Granularität**

* User-Service  
* User-Profile-Service  
* User-Preferences-Service  
* User-Settings-Service

**→ Zu viel Overhead, zu wenig Nutzen**

**❌ Falsche Service-Grenzen (technisch statt fachlich)**

Service nach Layern:

* API-Service  
* Business-Logic-Service  
* Data-Service

**→ Zurück zu Layer-Pattern!**

Oder mit technischen Suffixen:

* PaymentProcessor-Service  
* InventoryManager-Service  
* ShippingHandler-Service

**→ Technische Namen statt Business-Kontexte!**

**✅ Richtige Service-Grenzen (Context-Driven)**

Services nach Business Context:

* payment-service (PayPalPayment, StripePayment)  
* inventory-service (WarehouseProducts, DbProducts)  
* shipping-service (DhlShipment, UpsShipment)

→ Jeder Service = vollständiger Business-Kontext

→ Namen describe DINGE, keine Aktionen

**Checkliste: Sind Sie bereit für Microservices?**

* Module sind seit > 6 Monaten stabil  
* Klare Service-Grenzen identifiziert (Context-Driven!)  
* Team-Ownership etabliert (min. 1 Team pro Service)  
* CI/CD Pipeline vorhanden  
* Monitoring & Logging Infrastructure bereit  
* Container-Orchestrierung verfügbar (K8s, ECS, etc.)  
* Kafka oder anderes Messaging-System für Event-Driven Communication  
* Datenbank-Migration-Strategie definiert (Database per Service)  
* **storage/** Pakete in allen Services für ORM-Isolation  
* **application/** Pakete mit Main-Klasse, DI und Configs in allen Services  
* Entwickler verstehen verteilte Systeme (CAP, Eventual Consistency)  
* Budget für erhöhten Ops-Aufwand vorhanden  
* Feature Toggles für schrittweisen Rollout implementiert

**Wenn Sie < 8 Punkte abhaken können: Bleiben Sie beim Modular Monolith!**

Diese folgende Aufteilung ist die logische Konsequenz der **Context-Driven Architecture**. Sie trennt fachlichen Kontexte (Bounded Contexts) samt ihren technischen Infrastruktur-Abhängigkeiten zu einzelnen Mircoservicen.
```
com.company.shop-gateway-service      → gateway for all Projects

com.company.shop-payment-service       → Service - MAVEN Parent Project  
├── payment                            → Bounded Context - MAVEN Project Module    
│   ├── application/                   → Startup, Root Composition, DI, Config  
│   │   ├── PaymentApplication.java  
│   │   ├── PaymentKafkaConfig.java  
│   │   └── PaymentWebConfig.java  
│   ├── user/                          → User Interfaces  
│   ├── OrderId.java                   → Value Object for Order Reference  
│   ├── ProductId.java                 → Value Object for Product Reference  
│   ├── PaymentId.java                 → Value Object  
│   ...  
│  
├── payment-endpoint                   → MAVEN Project Module  
├── payment-resource                   → MAVEN Project Module   
├── payment-storage                    → MAVEN Project Module  
└── payment-messaging                  → MAVEN Project Module

com.company.shop-inventory-service     → MAVEN Parent Project  
├── inventory                          → MAVEN Module Project   
│   ├── application/                   → Startup, Root Composition, DI, Config  
│   │   ├── InventoryApplication.java  
│   │   ├── InventoryKafkaConfig.java  
│   │   └── InventoryWebConfig.java  
│   ├── user/                           → User Interfaces  
│   ├── OrderId.java                    → Value Object  
│   ├── ProductId.java                  → Value Object  
│   ├── PaymentId.java                  → Value Object for Payment Reference  
│   ...  
│  
├── inventory-endpoint                  → MAVEN Module Project  
├── inventory-resource                  → MAVEN Module Project  
├── inventory-storage                   → MAVEN Module Project  
└── inventory-messaging                 → MAVEN Module Project

com.company.shop-shipping-service       → MAVEN Parent Project  
├── shipping                            → MAVEN Module Project  
│   ├── application/                    → Startup, Root Composition, DI, Config  
│   │   ├── ShippingApplication.java  
│   │   ├── ShippingKafkaConfig.java  
│   │   └── ShippingWebConfig.java  
│   ├── user/                           → User Interfaces, API's  
│   ├── OrderId.java                    → Value Object for Inventory Reference  
│   ├── ProductId.java                  → Value Object for Inventory Reference  
│   ├── PaymentId.java                  → Value Object for Payment Reference  
│   ...  
│  
├── shipping-endpoint                   → MAVEN Module Project  
├── shipping-resource                   → MAVEN Module Project  
├── shipping-storage                    → MAVEN Module Project  
└── shipping-messaging                  → MAVEN Module Project
```
1. **Vermeidung von Kopplung:** Wenn alle Microservices das gleiche OrderId-Objekt aus einem zentralen Projekt nutzen, müssen bei einer Änderung an dieser Klasse (z. B. Wechsel von Long zu UUID) alle Projekte/Module gleichzeitig angefasst werden. Duplikation erlaubt es jedem Kontext, sich in seinem eigenen Tempo zu entwickeln.  
2. **Sandi Metz' Regel:** "Duplikation ist weitaus günstiger als die falsche Abstraktion." Ein zentrales Projekte/Module suggeriert eine Gemeinsamkeit, die fachlich oft gar nicht existiert (eine OrderId im Versand muss nicht dieselben Anforderungen haben wie eine OrderId im Payment).  
3. **Eliminierung der Kopplungen :** Ohne das Projekt com.company.*.common gibt es keinen ort mehr, an dem "schnell mal" technischer oder fachlicher Ballast abgelegt werden kann.

**Frontend als eigenständiger Microservice (BFF-Pattern)**

In modernen Cloud-nativen Architekturen ist es oft sinnvoll, das Frontend nicht nur als statisches Asset, sondern als eigenständigen Service “Backend-for-Frontend” (**BFF**) zu behandeln. Dies ermöglicht eine strikte Trennung von Präsentationslogik und Business-Schnittstellen.
```
com.company.shop-gateway-service     → API Gateway (Auth, Rate Limiting)

com.company.shop-inventory-service   → autonomous Business Service  
com.company.shop-payment-service     → autonomous Business Service  
com.company.shop-shipping-service    → autonomous Business Service

com.company.shop-user-service    → Frontend Project / BFF Service Project  
├── app/                         → Node.js/SSR Startup oder Java/Spring-BFF  
├── endpoint/                    → consumes Data for UI from Business Services
├── picture/                     
│   ├── icon/                    → Icons of the Application 
│   ├── image/                   → image for the Application 
│   ├── ...png                   → pictures for user contents [.jpg, png,...]  
├── style/                       → CSS templates for layouts
├── user/                        → UI components of Business (data models)   
│   ├── control/                 → Common UI components/templates .js  
│   ├── layout/                  → Common UI layouts/templates .js 
│   ├── page/                    → Custom UI [pages].js use components in user/, control/, layout/ 
│   │   ├── admin.js  
│   │   ├── productlist.js  
│   │   ├── ordertable.js  
│   │   ├── ordermenu.js  
│   │   ├── paymentform.js  
│   │   ├── userprofile.js  
│   │   ├── ...  
│   │   ...  
│   ├── Control.js               → abstract Component (Composition Pattern) 
│   ├── User.js                  → User Concept (Model) 
│   ├── Page.js                  → abstract Page extends Control  
│   ├── List.js                  → abstract List extends Control  
│   ├── Table.js                 → abstract Table extends Control  
│   ├── Menu.js                  → abstract Menu extends Control  
│   ...

...
```
**Warum dieser Ansatz?**

* **Optimierte Daten für die UI:** Das Frontend-Service aggregiert Daten aus mehreren Business-Services (z.B. Payment + Inventory) und sendet nur die benötigten Dateninformationen (Felder) an den Client.  
* **Technologische Unabhängigkeit:** Das Frontend kann in einem Framework (z.B. React/Next.js) entwickelt werden, während das Backend in Java/Spring oder Go bleibt.  
* **Sicherheit:** Sensible Geschäftslogik bleibt im internen Netzwerk; der Frontend-Service fungiert als kontrollierter Ausgangspunkt zum Benutzer.

**Der Weg zurück: Reverse-Migration**

Microservices können zurück zu Monolithen migriert werden, wenn nötig:

Services → Module → Packages

Ihre Context-Driven Struktur macht auch das einfach, da die Grenzen klar sind!

## **Die Synthese: Kombination bewährter Erkenntnisse**

Die Erkenntnis lautet: Package-Struktur = ausführbares Context-Diagramm.

We kombinieren:

* **Robert Bräutigam's** Regeln für lesbare Strukturen und klare Benennung  
* **Yegor Bugayenko** Regeln für Benennung nur Nomen, keine Verben, beschreibende Präfixe  
* **Simon Browns Modularität** für klare Grenzen und Package-Unabhängigkeit  
* **Mark Seemann** Composition Root Pattern  
* **Philipp Hauers Feature-Orientierung** für Business-fokussierte Organisation  
* **Framework-Isolierung** saubere Trennung von Technik und Domain-Logik  
* **Event-Driven Architecture** mit Kafka für lose gekoppelte Services  
* Backend-for-Frontend Chris Richardson

Diese Synthese schließt die mentale Lücke zwischen Business Analysten und Entwicklern endgültig:

## **Fazit**

Business Context-Driven Packaging macht Ihr Context-Diagramm im Code sichtbar. Business Analysten und Entwickler sprechen endlich dieselbe Sprache. Der Code wird zum lebenden Architektur-Dokument.

Haben Sie Context-Driven Packaging ausprobiert? Teilen Sie Ihre Erfahrungen in den Kommentaren!

## **Weiterführende Links**

### **Grundlegende Artike**

* Happy Packaging! (Robert Bräutigam)  
  https://javadevguy.wordpress.com/2017/12/18/happy-packaging/  
* Modular Monoliths - (Simon Brown)  
  https://www.google.com/search?q=https://static.codingthearchitecture.com/presentations/sa2015-modular-monoliths.pdf  
* Package by Feature (Philipp Hauer)  
  https://phauer.com/2020/package-by-feature/  
* Composition Root with Pure DI (Mark Seemann)  
  https://blog.ploeh.dk/2011/07/28/CompositionRoot/  
* Vertical and Horizontal Decorating (Yegor Bugayenko)  
  https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html

### **Architektur-Pattern Quellen**

* Object-Oriented Domain Driven Design (Robert Bräutigam)  
  https://speakerdeck.com/robertbraeutigam/object-oriented-domain-driven-design  
* Package by Feature (Philipp Hauer)  
  https://phauer.com/2020/package-by-feature/  
* Backend-for-Frontend (Chris Richardson)  
   https://microservices.io/patterns/apigateway.html

### **Architektur-Anti-Pattern Quellen**

* Clean Architecture (Robert C. Martin)  
* Domain Driven Design Structure (Eric Evans)  
* Hexagonal Architecture (Alistair Cockburn)  
* Vertical Slice Architecture (Jimmy Bogard)
