# The right System Composition "leaves Nobody behind…"

## 1. Einleitung und das Problem mit DI-Containern

In der modernen objektorientierten Softwareentwicklung ist Dependency Injection (DI) längst ein etabliertes Konzept. Die Grundidee ist simpel und elegant: Objekte sollen ihre Abhängigkeiten nicht selbst erstellen, sondern von außen erhalten. Doch während die Technik selbst wertvoll ist, haben viele Frameworks und DI-Container das ursprüngliche Konzept in ein Anti-Pattern verwandelt.

### Die Probleme mit DI-Containern

Betrachten wir eine Payment-Applikation mit DI-Container. Wir bauen sie schrittweise auf und beobachten, wie die Probleme mit wachsenden Anforderungen entstehen.

#### Schritt 1: Invoice und Payment - Einfache Anforderungen

**Anforderung:** Die Applikation soll Rechnungen erstellen und Zahlungen verarbeiten können.

```mermaid
graph TB
    subgraph Container["Spring Container - verwaltet alles"]
        App[SpringPaymentApp]
        
        subgraph L3["Layer 3: Business Logic"]
            Invoice[InvoiceService]
            Payment[PaymentService]
        end
        
        subgraph L2["Layer 2: Data Access"]
            InvoiceRepo[InvoiceRepository]
            PaymentRepo[PaymentRepository]
        end
        
        subgraph L1["Layer 1: Infrastructure"]
            DB[(Database)]
        end
    end
    
    Invoice --> InvoiceRepo
    Payment --> PaymentRepo
    InvoiceRepo --> DB
    PaymentRepo --> DB
    
    style App fill:#e8f4f8
    style Container fill:#f0f0f0,stroke:#666,stroke-width:3px
```

```java
@Component
public class SpringPaymentApp {
    @Inject private InvoiceService invoiceService;
    @Inject private PaymentService paymentService;
    // App nutzt Services, keine direkten Dependencies sichtbar
}

@Service
public class InvoiceService {
    @Inject private InvoiceRepository invoiceRepo;
    
    public Invoice create(Customer customer, List<Item> items) {
        // Business Logic
    }
}

@Service
public class PaymentService {
    @Inject private PaymentRepository paymentRepo;
    
    public void process(Payment payment) {
        // Business Logic
    }
}
```

**Problem:** Bereits hier ist unklar, wie die Objekte wirklich zusammenhängen. Die `SpringPaymentApp` schwebt isoliert - der Container verwaltet alles im Hintergrund.

---

#### Schritt 2: Customer hinzufügen - Noch ohne Zyklen

**Neue Anforderung:** Kunden sollen verwaltet werden. Beim Erstellen einer Rechnung muss der Kunde validiert werden.

```mermaid
graph TB
    subgraph Container["Spring Container - Komplexität steigt"]
        App[SpringPaymentApp]
        
        subgraph L3["Layer 3: Business Logic"]
            Invoice[InvoiceService]
            Payment[PaymentService]
            Customer[CustomerService]
        end
        
        subgraph L2["Layer 2: Data Access"]
            InvoiceRepo[InvoiceRepository]
            PaymentRepo[PaymentRepository]
            CustomerRepo[CustomerRepository]
        end
        
        subgraph L1["Layer 1: Infrastructure"]
            DB[Database]
        end
    end
    
    Invoice --> InvoiceRepo
    Invoice -.->|horizontal| Customer
    
    Payment --> PaymentRepo
    
    Customer --> CustomerRepo
    
    InvoiceRepo --> DB
    PaymentRepo --> DB
    CustomerRepo --> DB
    
    style App fill:#e8f4f8
    style Invoice fill:#ffe6e6
    style Container fill:#f0f0f0,stroke:#666,stroke-width:3px
```

```java
@Component
public class SpringPaymentApp {
    @Inject private InvoiceService invoiceService;
    @Inject private PaymentService paymentService;
    @Inject private CustomerService customerService;
    // Noch mehr versteckte Dependencies
}

@Service
public class InvoiceService {
    @Inject private InvoiceRepository invoiceRepo;
    @Inject private CustomerService customerService;  // ⚠️ Horizontale Dependency!
    
    public Invoice create(Customer customer, List<Item> items) {
        customerService.validate(customer);  // Braucht CustomerService
        // Business Logic
    }
}

@Service
public class CustomerService {
    @Inject private CustomerRepository customerRepo;
    
    public void validate(Customer customer) {
        // Validation Logic
    }
}
```

**Problem:** Horizontale Abhängigkeiten innerhalb des Business-Logic-Layers entstehen. Die Layer-Architektur wird komplexer.

---

#### Schritt 3: Zyklische Abhängigkeit - Das System bricht

**Neue Anforderung:** Kunden sollen ihre offenen Rechnungen sehen können. `CustomerService` muss jetzt `InvoiceService` kennen.

```mermaid
graph TB
    subgraph Container["🔲 Spring Container - ZYKLUS entsteht!"]
        App[SpringPaymentApp]
        
        subgraph L3["Layer 3: Business Logic"]
            Invoice[InvoiceService]
            Payment[PaymentService]
            Customer[CustomerService]
        end
        
        subgraph L2["Layer 2: Data Access"]
            InvoiceRepo[InvoiceRepository]
            PaymentRepo[PaymentRepository]
            CustomerRepo[CustomerRepository]
        end
        
        subgraph L1["Layer 1: Infrastructure"]
            DB[Database]
        end
    end
    
    Invoice --> InvoiceRepo
    Invoice -.->|horizontal| Customer
    
    Payment --> PaymentRepo
    Payment -.->|horizontal| Customer
    
    Customer --> CustomerRepo
    Customer -.->|🔴 ZYKLUS!| Invoice
    
    InvoiceRepo --> DB
    PaymentRepo --> DB
    CustomerRepo --> DB
    
    style App fill:#e8f4f8
    style Invoice fill:#ff6b6b
    style Customer fill:#ff6b6b
    style Payment fill:#ffe6e6
    style Container fill:#ffe0e0,stroke:#ff0000,stroke-width:3px
```

```java
@Component
public class SpringPaymentApp {
    @Inject private InvoiceService invoiceService;
    @Inject private PaymentService paymentService;
    @Inject private CustomerService customerService;
    // Container versteckt den Zyklus komplett!
}

@Service
public class InvoiceService {
    @Inject private InvoiceRepository invoiceRepo;
    @Inject private CustomerService customerService;  // → Customer
    
    public Invoice create(Customer customer, List<Item> items) {
        customerService.validate(customer);
        // Business Logic
    }
}

@Service
public class PaymentService {
    @Inject private PaymentRepository paymentRepo;
    @Inject private CustomerService customerService;  // → Customer
    
    public void process(Payment payment) {
        customerService.updateBalance(payment.getCustomer());
        // Business Logic
    }
}

@Service
public class CustomerService {
    @Inject private CustomerRepository customerRepo;
    @Inject private InvoiceService invoiceService;  // 🔴 → Invoice (ZYKLUS!)
    
    public List<Invoice> getOpenInvoices(Customer customer) {
        return invoiceService.findOpenByCustomer(customer);  // Braucht InvoiceService
    }
    
    public void validate(Customer customer) {
        // Validation Logic
    }
}
```

**Das Problem wird "gelöst" durch Spring:**
```java
// Spring erstellt Proxies und initialisiert lazy
@Service
@Lazy  // Spring's "Lösung" für Zyklen
public class CustomerService {
    @Inject private InvoiceService invoiceService;  // Wird als Proxy injiziert
    // ...
}
```

---

#### Die resultierenden Probleme - Zusammenfassung

**Diese schrittweise Entwicklung zeigt:**

1. **Unübersichtliche Abhängigkeiten** - `SpringPaymentApp` zeigt keine echten Dependencies. Wo ist die Objektstruktur?
   
2. **Schwere Wartbarkeit** - Um zu verstehen was `CustomerService` braucht, muss man:
   - Alle `@Inject` Felder durchsuchen
   - Prüfen ob `@Lazy` verwendet wird
   - Verstehen wie Spring die Proxies auflöst
   
3. **Erzwungene Layer-Trennung** - Alle Klassen haben `-Service` oder `-Repository` Suffix nur wegen der Layer

4. **Zyklische Abhängigkeiten** - `InvoiceService` ⇄ `CustomerService` - Spring versteckt das Problem mit Proxies statt es zu lösen

5. **Code Pollution** - Überall `@Service`, `@Repository`, `@Inject`, `@Lazy` Annotations

**Die Layer-Architektur entstand automatisch durch:**
- Die Verwendung von `@Service` und `@Repository` Stereotypen
- Die Notwendigkeit, Business-Logic von Data-Access zu trennen
- Die Spring-Best-Practices, die diese Struktur empfehlen

### Warum führen DI-Container zwangsläufig zu Layer-Architektur?

DI-Frameworks sind so konzipiert, dass sie Layer-Architektur aktiv fördern und teilweise erzwingen:

DI-Container fördern Layer-Architektur durch:

- **Stereotype-Annotations** (`@Service`, `@Repository`, `@Controller`) - die explizit Layer definieren
- **Scan-Mechanismen**, die nach Package-Strukturen suchen (z.B. `com.example.service.*`, `com.example.repository.*`)
- **Best-Practice-Guides** der Frameworks, die Layer-Trennung empfehlen
- **Proxy-Mechanismen** für Transactions (`@Transactional`) - die Layer-Grenzen voraussetzen
- **Dependency-Rules**, die nur "nach unten" zeigen dürfen - was Layer-Hierarchien erzwingt

### Das Problem bei Verwendung von DI-Containern

Betrachten wir eine typische Rechnungsanwendung mit DI-Container:

**Visualisierung der Layer-Problematik:**
```
┌────────────────────────────────────────────────────────┐
│ L4: Application Layer                                  │
│     [MainApplication]                                  │
├════════════════════════════════════════════════════════┤
│ L3: Business Logic Layer                               │
│     [InvoiceService] ←→ [PaymentService] ←→ [Customer] │
│            ↑                                      ↓     │
│            └──────── 🔴 Zyklische Abhängigkeit ───┘     │
├════════════════════════════════════════════════════════┤
│ L2: Data Access Layer                                  │
│     [InvoiceRepo]    [PaymentRepo]    [CustomerRepo]   │
├════════════════════════════════════════════════════════┤
│ L1: Infrastructure Layer                               │
│     [Database]                                         │
└────────────────────────────────────────────────────────┘

Legende:
→ : Erlaubte Abhängigkeit (nach unten)
↔ : Problematische horizontale Abhängigkeit
🔴: Zyklische Abhängigkeit
```

So sieht der Code dazu aus:

```java
// ❌ FALSCH: Mit DI-Container und Layers

// Layer 4 - Application Layer
@Component
public class MainApplication {
    @Inject private InvoiceService invoiceService;  // Abhängigkeit zu Layer 3
    @Inject private PaymentService paymentService;  // Abhängigkeit zu Layer 3
    @Inject private CustomerService customerService; // Abhängigkeit zu Layer 3
}

// Layer 3 - Business Logic Layer
@Service
public class InvoiceService {
    @Inject private InvoiceRepository invoiceRepo;  // Abhängigkeit zu Layer 2
    @Inject private TaxCalculator taxCalculator;    // Abhängigkeit zu Layer 2
    @Inject private PaymentService paymentService;  // ⚠️ Horizontale Abhängigkeit!
}

@Service
public class PaymentService {
    @Inject private PaymentRepository paymentRepo;  // Abhängigkeit zu Layer 2
    @Inject private CustomerService customerService; // ⚠️ Horizontale Abhängigkeit!
}

@Service
public class CustomerService {
    @Inject private CustomerRepository customerRepo; // Abhängigkeit zu Layer 2
    @Inject private InvoiceService invoiceService;   // 🔴 ZYKLISCHE ABHÄNGIGKEIT!
}

// Layer 2 - Data Access Layer
@Repository
public class InvoiceRepository {
    @Inject private Database database;              // Abhängigkeit zu Layer 1
}

@Repository
public class PaymentRepository {
    @Inject private Database database;              // Abhängigkeit zu Layer 1
}

// Layer 1 - Infrastructure Layer
@Component
public class Database {
    // Lowest layer
}
```

### Die Probleme dieser Architektur

DI-Frameworks wie Spring IoC, Google Guice, Java EE6 CDI und Dagger machen aus einer natürlichen OOP-Technik ein Anti-Pattern. **Sie führen zwangsläufig zu einer Layer-Architektur**, die wiederum folgende Probleme verursacht:

1. **Unübersichtliche Abhängigkeiten durch Layer-Denken**: Die wahre Objektstruktur versteckt sich hinter `@Inject`-Annotations und Layer-Grenzen. Wo wird `InvoiceService` eigentlich erstellt? Wer injiziert was? Die Layer-Abstraktion verschleiert die tatsächlichen Objektbeziehungen.

2. **Schwere Wartbarkeit durch Layer-Indirektion**: Um zu verstehen, welche Abhängigkeiten ein Objekt hat, muss man:
   - Die Klasse selbst durchsuchen (nach `@Inject`)
   - Verstehen, in welchem Layer sie sich befindet
   - Konfigurationsdateien durchforsten
   - Den DI-Container-Configuration-Code finden
   - Die Layer-Regeln und erlaubten Abhängigkeitsrichtungen kennen
   - Die Laufzeit-Behavior des Containers verstehen

3. **Erzwungene Layered Architecture**: DI-Container fördern aktiv eine künstliche Trennung in Service-, Repository- und Controller-Layer. Diese Layer-Abstraktion ist oft unnötig und führt zu:
   - `-Service` Klassen, die nur als Delegator zwischen Layern fungieren
   - `-Repository` Klassen, die einfache CRUD-Operationen wrappen, nur um Layer-Grenzen einzuhalten
   - Artificielle Interfaces "für die Testbarkeit" und Layer-Entkopplung
   - Einer starren Hierarchie, die natürliche Objektkomposition verhindert

4. **Zyklische Abhängigkeiten innerhalb der Layer**: Die Layer-Architektur verhindert zwar vertikale Zyklen, aber **horizontale Abhängigkeiten innerhalb eines Layers** führen zu schwer zu entwirrenden Dependency-Kreisen:
   - `InvoiceService` braucht `PaymentService` (beide in Layer 3)
   - `PaymentService` braucht `CustomerService` (beide in Layer 3)
   - `CustomerService` braucht `InvoiceService` (beide in Layer 3) → 🔴 **Zyklus!**
   
   Der DI-Container "löst" dies durch Proxies und Lazy Initialization – versteckt aber das eigentliche Design-Problem, das durch die künstliche Layer-Trennung erst entsteht.

5. **Code Pollution durch Layer-Annotations**: Der eigentliche Business-Code wird mit Framework-spezifischen und Layer-definierenden Annotations überfrachtet:
   ```java
   @Service              // Layer-Definition: "Ich bin Business Logic"
   @Transactional
   @Scope("prototype")
   public class InvoiceService {
       @Inject @Named("primary")
       private InvoiceRepository repo;  // Abhängigkeit zum Data-Layer
       
       @PostConstruct
       public void init() { /* ... */ }
   }
   ```

### Warum führen DI-Container zu Layern?

DI-Container fördern Layer-Architektur durch:

- **Stereotype-Annotations** (`@Service`, `@Repository`, `@Controller`) - die explizit Layer definieren
- **Scan-Mechanismen**, die nach Package-Strukturen suchen (z.B. `com.example.service.*`, `com.example.repository.*`)
- **Best-Practice-Guides** der Frameworks, die Layer-Trennung empfehlen
- **Proxy-Mechanismen** für Transactions (`@Transactional`) - die Layer-Grenzen voraussetzen
- **Dependency-Rules**, die nur "nach unten" zeigen dürfen - was Layer-Hierarchien erzwingt

### Die Illusion der Entkopplung

Viele Entwickler glauben, dass DI-Container für "loose coupling" sorgen. Doch in Wirklichkeit:
- Sind die Abhängigkeiten nur **versteckt**, nicht entkoppelt
- Wird die **Komplexität erhöht** statt reduziert
- Entsteht eine **Kopplung an den Framework-Container**
- Wird **echte Objekt-Komposition** durch Service-Lokalisierung ersetzt

Der eigentliche Wert liegt in der Dependency Injection selbst – nicht im Container.

## 2. Der richtige Weg: Pure Composition

Die Lösung ist überraschend einfach: Verzichte auf DI-Container und komponiere deine Objekte explizit mit dem `new`-Operator.

### Die richtige Komposition: Ein konkretes Beispiel

Kehren wir zurück zu unserer Rechnungsanwendung. So sollte die richtige Komposition aussehen:

```java
App.java                  # Abstraktion für die Anwendung
app/                      # Package für Details der App-Abstraktion
├── ConsoleApp.java       # Einstiegspunkt, implementiert App
│       └── (in einer 'main' oder 'startup' Methode:)
│           new ConsoleApp(
│               new InvoiceBook(
│                   new Invoices(),
│                   new CalculatedTax()
│               ),
│               new Payment(
│                   new Payer("Alice"),
│                   new Recipient("Bob"),
│                   new Amount(100, new Currency("EUR"))
│               ),
│               new CustomerDirectory(...)
│           )
```

**Visualisierung der richtigen Komposition:**
```
┌─────────────────────────────────────────────┐
│           ConsoleApp                        │
│  ┌──────────────┐  ┌────────┐  ┌─────────┐ │
│  │ InvoiceBook  │  │Payment │  │Customer │ │
│  │ ┌──────────┐ │  │┌──────┐│  │Directory│ │
│  │ │Invoices  │ │  ││Payer ││  └─────────┘ │
│  │ │Calculated│ │  ││Recip.││              │
│  │ │Tax       │ │  ││Amount││              │
│  │ └──────────┘ │  │└──────┘│              │
│  └──────────────┘  └────────┘              │
└─────────────────────────────────────────────┘
```

**Legende:**
- Das größte Rechteck ist `ConsoleApp` - die äußere Komposition
- Darin: Rechtecke für `InvoiceBook`, `Payment`, `CustomerDirectory`
- Weiter verschachtelt: `Invoices`, `CalculatedTax`, `Payer`, `Recipient`, `Amount`, `Currency`

Beachte: **Keine Layers, keine Annotations, keine versteckten Abhängigkeiten** – nur pure Objekt-Komposition durch explizite Constructor-Aufrufe.

### Ein weiteres echtes Beispiel

Yegor Bugayenko zeigt in seinem rultor.com-Projekt, wie echte Objekt-Komposition aussieht:

```java
public final class Agents {
    public Agents(
        final Logs logs,
        final Sttc sttc,
        final Talks talks
    ) {
        return new Agents.Iterative(
            Arrays.asList(
                new Iterative.Constant<Agent>(
                    new IndexesProfile(sttc)
                ),
                new Iterative.Constant<Agent>(
                    new RemovesProfile(talks)
                ),
                new Iterative.Sticky<Agent>(
                    new Iterative.Func<>(
                        talk -> new StartsRequest(
                            new Req.Simple(
                                talk,
                                new Question.Wrap(
                                    new QnSince(
                                        new QnParametrized(
                                            new QnConfig(talk)
                                        )
                                    )
                                )
                            )
                        )
                    )
                ),
                // ... weitere Agents
            )
        );
    }
}
```

### Die Vorteile dieser Herangehensweise

1. **Vollständige Transparenz**: Jeder kann sofort sehen, wie das System zusammengesetzt ist
2. **Keine versteckten Abhängigkeiten**: Alle Dependencies sind explizit im Code sichtbar
3. **Einfaches Refactoring**: IDE-Support funktioniert perfekt, da alles statisch typisiert ist
4. **Testbarkeit**: Test-Doubles können einfach injiziert werden, ohne Mock-Frameworks
5. **Keine Framework-Kopplung**: Der Code ist unabhängig von DI-Containern

### Das Composition-Prinzip

Die Komposition sollte so nah wie möglich am Entry-Point der Applikation stattfinden. Diese "Composition Root" ist verantwortlich für:

- Das Erstellen des kompletten Object-Graphs
- Die Konfiguration aller Abhängigkeiten
- Die Übergabe der fertigen Objekte an die Applikationslogik

Alle anderen Klassen nutzen ausschließlich Constructor Injection und überlassen die Objekterstellung ihrem Consumer.

## 3. Richtiger Umgang bei Framework-Verwendung

In der Praxis arbeiten viele Teams mit Frameworks wie Spring oder Java EE CDI, die DI-Container mitbringen. Wie geht man damit um?

### Die One-Class-Regel

Wenn du einen DI-Container verwenden musst (oder willst), dann beschränke seine Nutzung auf **genau eine Stelle** in deinem Code:

```java
@ApplicationScoped
public class MainApplication {
    
    @Inject
    private DatabaseConnection db;
    
    @Inject
    private MessageQueue queue;
    
    @Inject
    private ExternalApiClient api;
    
    @Produces
    @ApplicationScoped
    public Application createApplication() {
        return new Application(
            new DataService(
                new TransactionalDB(db),
                new CachedDB(
                    new TransactionalDB(db)
                )
            ),
            new MessageProcessor(
                queue,
                new ErrorHandler(
                    new LoggingErrorHandler(),
                    new NotificationErrorHandler(api)
                )
            ),
            new ReportGenerator(
                new PdfRenderer(),
                new CsvRenderer()
            )
        );
    }
}
```

### Kernprinzipien

1. **Container-Isolation**: Nur die MainApplication-Klasse darf `@Inject` verwenden
2. **Explizite Komposition**: Die gesamte Objektstruktur wird manuell komponiert
3. **Framework-Adaption**: Der Container liefert nur primitive Dependencies (DB-Connection, Config, etc.)
4. **Business-Logic-Freiheit**: Keine Business-Klasse kennt den DI-Container

Jegliche DI-Container sollten auf die Composition Root beschränkt sein. Der Rest der Applikation verwendet pure Constructor Injection.

### Migration bestehender Systeme

Bei Legacy-Code mit verstreutem `@Inject`:

1. Erstelle eine zentrale MainApplication-Klasse
2. Verschiebe schrittweise die Objekterstellung dorthin
3. Entferne `@Inject` aus Business-Klassen
4. Mache Constructor-Dependencies explizit

## 4. Fazit mit Beispiel-Struktur

Die richtige System-Komposition "leaves nobody behind" – sie macht die Struktur für alle Entwickler sofort verständlich und nachvollziehbar.

### Ideale Projektstruktur

```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           ├── app/
│   │           │   └── Main.java                    // Entry Point
│   │           ├── composition/
│   │           │   └── MainApplication.java         // Einzige Stelle mit DI-Container
│   │           ├── domain/
│   │           │   ├── User.java                    // Pure Business Objects
│   │           │   ├── Order.java
│   │           │   └── Payment.java
│   │           ├── services/
│   │           │   ├── UserService.java             // Constructor Injection
│   │           │   ├── OrderService.java
│   │           │   └── PaymentService.java
│   │           └── infrastructure/
│   │               ├── database/
│   │               │   ├── PostgresConnection.java
│   │               │   └── TransactionalDB.java
│   │               └── messaging/
│   │                   └── RabbitMQQueue.java
```

### Beispiel einer Service-Klasse

```java
public final class OrderService {
    private final UserService users;
    private final PaymentService payments;
    private final OrderRepository repository;
    private final EventPublisher events;
    
    // Constructor Injection - keine Annotations!
    public OrderService(
        final UserService users,
        final PaymentService payments,
        final OrderRepository repository,
        final EventPublisher events
    ) {
        this.users = users;
        this.payments = payments;
        this.repository = repository;
        this.events = events;
    }
    
    public Order placeOrder(
        final User user, 
        final List<Item> items
    ) {
        // Business Logic
    }
}
```

### Komposition im Main

```java
public final class Main {
    public static void main(String... args) {
        // Infrastructure
        final var database = new PostgresDB(
            "jdbc:postgresql://localhost/mydb"
        );
        final var queue = new RabbitMQ("localhost:5672");
        final var cache = new RedisCache("localhost:6379");
        
        // Repositories
        final var userRepo = new CachedUserRepository(
            new PostgresUserRepository(database),
            cache
        );
        final var orderRepo = new PostgresOrderRepository(database);
        
        // Services
        final var paymentService = new PaymentService(
            new StripePaymentGateway(),
            new PaymentLogger()
        );
        
        final var userService = new UserService(
            userRepo,
            new EmailValidator(),
            new PasswordHasher()
        );
        
        final var orderService = new OrderService(
            userService,
            paymentService,
            orderRepo,
            new EventPublisher(queue)
        );
        
        // Application
        final var app = new Application(
            orderService,
            userService,
            new WebServer(8080)
        );
        
        app.start();
    }
}
```

### Die Vorteile zusammengefasst

✅ **Lesbarkeit**: Jeder kann die Systemstruktur sofort verstehen  
✅ **Wartbarkeit**: Änderungen sind lokal und überschaubar  
✅ **Testbarkeit**: Dependencies sind explizit und austauschbar  
✅ **Refactoring-Sicherheit**: Compiler und IDE unterstützen vollständig  
✅ **Keine zyklischen Dependencies**: Die Komposition erzwingt einen gerichteten Graphen  
✅ **Framework-Unabhängigkeit**: Business-Code bleibt rein  

## 5. Quellen

### Primärquellen

1. Bugayenko, Y. (2014). **"Dependency Injection Containers are Code Polluters"**  
   https://www.yegor256.com/2014/10/03/di-containers-are-evil.html  
   _Grundlegende Kritik an DI-Containern und Demonstration echter Objekt-Komposition_

2. Bugayenko, Y. (2015). **"Don't Create Objects That End With -ER"**  
   https://www.yegor256.com/2015/03/09/objects-end-with-er.html  
   _Über deklaratives vs. imperatives Design in OOP_

3. Bugayenko, Y. (2016). **"Who Is an Object?"**  
   https://www.yegor256.com/2016/07/14/who-is-object.html  
   _Konzeptuelle Definition von Objekten als Repräsentanten von Daten_

4. Bugayenko, Y. **"Elegant Objects" (Vol. 1 & 2)**  
   _Umfassende Darstellung moderner OOP-Prinzipien_

### Weiterführende Literatur

5. van Deursen, S. & Seemann, M. **"Dependency Injection Principles, Patterns, and Practices"**  
   https://freecontent.manning.com/dependency-injection-in-net-2nd-edition-understanding-the-composition-root/  
   _Detaillierte Erklärung des Composition Root Patterns_

6. **"Design Patterns Explained – Dependency Injection"** (Stackify)  
   https://stackify.com/dependency-injection/  
   _Einführung in DI-Patterns und deren Anwendung_

7. **"Java Dependency Injection - DI Design Pattern"** (DigitalOcean)  
   https://www.digitalocean.com/community/tutorials/java-dependency-injection-design-pattern-example-tutorial  
   _Praktische Beispiele für DI in Java_

### Projektbeispiele

8. **Rultor - Agents.java**  
   https://github.com/yegor256/rultor  
   _Real-world Beispiel für Pure DI ohne Container_

### Verwandte Konzepte

- **SOLID Principles** (insbesondere Dependency Inversion Principle)
- **Composition over Inheritance**
- **Constructor Injection Pattern**
- **Service Locator Anti-Pattern**
- **Factory Pattern**

---

**Fazit**: Die richtige System-Komposition macht Dependencies explizit sichtbar und lässt niemanden im Unklaren darüber, wie das System strukturiert ist. DI-Container mögen in bestimmten Situationen ihren Platz haben, aber sie sollten niemals das grundlegende Prinzip der expliziten Objekt-Komposition ersetzen. Ein gut komponiertes System ist ein verständliches System – und Verständlichkeit ist die Grundlage für Wartbarkeit, Erweiterbarkeit und langfristigen Erfolg.
