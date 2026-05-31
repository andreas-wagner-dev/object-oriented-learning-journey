# The Decorator Pattern — Where Business Logic Meets Clean Code

> How a simple pattern transforms code from technical plumbing into business storytelling.

## The Problem Every Developer Faces

We're building a car rental system. Simple enough, right?

A car gets rented, we **store** it to the database, **log** it, **cache** it for performance, **publish** an event for analytics, and **validate** the input. So we often write this:

```java
public class CarService {
  public void rentCar(String carId, Customer customer,
                      LocalDate from, LocalDate to) {
    // Validation
    if (customer == null)
      throw new IllegalArgumentException("Customer required");
    if (from.isBefore(LocalDate.now()))
      throw new IllegalArgumentException("Date cannot be in the past");

    // Caching
    cache.invalidate(carId);

    // Logging
    log.info("Renting car {} to customer {}", carId, customer.getId());

    // Database
    CarEntity entity = repository.findById(carId).orElseThrow();
    entity.setRented(true);
    entity.setCustomerId(customer.getId());
    repository.save(entity);

    // Events
    kafka.publish(new CarRentedEvent(carId, customer.getId()));

    log.info("Car {} successfully rented", carId);
  }

  public void returnCar(String carId, Customer customer) {
    // Validation, Caching, Logging, Database, Events, Logging...
  }
}
```

Every developer has written code like this. And every developer knows the pain:

**Want to test validation?** → Good luck mocking the database, cache, Kafka…  
**Need to change the logging format?** → Touch many lines of mixed concerns…  
**Want to reuse caching logic elsewhere?** → Copy-paste or refactor everything…

**There's a better way. And it's hiding in plain sight.**

---

## The Decorator Pattern: Business Logic as Code

Here's the radical idea: what if our code structure looked exactly like the business process?

In a car rental business, a customer who rents a car triggers the following steps:

1. ✅ Validate the rental request
2. 💾 Persist it to storage
3. 🗑️ Cache it for performance
4. 📝 Log it for audit
5. 📡 Publish events for downstream systems

Why shouldn't our code reflect this exact flow?

```java
// This IS the business process
Customer customer = new ValidCustomer(       // 1. Validate first
    new PersistentCustomer(repository,       // 2. Then persist
        new CachedCustomer(cache,            // 3. Then cache
            new LoggedCustomer(log,          // 4. Then log
                new PublishedCustomer(queue, // 5. Finally publish events
                    baseCustomer
                )
            )
        )
    )
);

// rent
customer.rentCar(car, from, to);  
// or return
customer.returnCar(car);
// Clean. Simple. Business-focused.
```

**This is the Decorator Pattern** — and it's not just a technical pattern. It's business logic made visible.

---

## Six Transformative Benefits

### 1. Clean Separation of Concerns

Each decorator has *one job*. Just one.

```java
// ONLY validates
public class ValidCustomer implements Customer {

    private final Customer origin;

    public ValidCustomer(Customer origin) {
        this.origin = origin;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        if (car == null)
            throw new IllegalArgumentException("Car required");
        if (from.isBefore(LocalDate.now()))
            throw new IllegalArgumentException("Date cannot be in the past");
        if (to.isBefore(from))
            throw new IllegalArgumentException("Invalid date range");

        origin.rentCar(car, from, to);  // Delegate to next layer
    }

    @Override
    public void returnCar(Car car) {
        if (car == null)
            throw new IllegalArgumentException("Car required");

        origin.returnCar(car);  // Delegate to next layer
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}

// ONLY logs
public class LoggedCustomer implements Customer {

    private final Customer origin;
    private final Logger log;

    public LoggedCustomer(Customer origin, Logger log) {
        this.origin = origin;
        this.log = log;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        log.info("RENT: Starting rental process");
        try {
            origin.rentCar(car, from, to);
            log.info("RENT: Successfully completed");
        } catch (Exception e) {
            log.error("RENT: Failed", e);
            throw e;
        }
    }

    @Override
    public void returnCar(Car car) {
        log.info("RETURN: Starting return process");
        try {
            origin.returnCar(car);
            log.info("RETURN: Successfully returned");
        } catch (Exception e) {
            log.error("RETURN: Failed", e);
            throw e;
        }
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}
```

**Result:** No ripple effects.  
Change validation? → Touch only **ValidCustomer**.  
Change logging? → Touch only **LoggedCustomer**.

---

### 🎯 Bonus: Printers Instead of Getters

Notice the `print(Media media)` method? This is Yegor Bugayenko's ["Printers Instead of Getters"](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html) concept.

❌ **Traditional approach:** Getters expose internals

```java
public interface Customer {
    String getId();      // Exposes data
    String getName();    // Exposes data
    void rentCar(Car car, LocalDate from, LocalDate to);
    void returnCar(Car car);
}

// Controller builds JSON manually
String json = String.format(
    "{\"id\":\"%s\",\"name\":\"%s\"}", customer.getId(), customer.getName()
);
```

✅ **Printers approach:** Objects print themselves

```java
public interface Customer {
    void rentCar(Car car, LocalDate from, LocalDate to);
    void returnCar(Car car);
    void print(Media media);  // Customer decides how to represent itself
}

// Customer prints itself to media
Media media = new JsonMedia();
customer.print(media);
String json = media.json();
```

**Why this matters:**

- 🔒 **Encapsulation:** `Customer` doesn’t expose internal data
- 🧠 **Smart Objects:** `Customer` controls its own persistence and representation
- 🎨 **Flexibility:** Same `Customer` can print to JSON, XML, HTML
- 📦 **No DTOs needed:** Objects print directly to HTTP responses

---

### 2. Flexible Composition

Different scenarios need different combinations of concerns. Decorators make this trivial:

```java
// Production: Full chain
Customer production = new ValidCustomer(
    new PublishedCustomer(queue,
        new LoggedCustomer(log,
            new CachedCustomer(cache,
                new PersistentCustomer(baseCustomer, repository)
            )
        )
    )
);

// Admin tool: Skip validation
Customer admin = new PublishedCustomer(queue,
    new LoggedCustomer(log,
        new PersistentCustomer(baseCustomer, repository)
    )
);

// Testing: Minimal
Customer test = new ValidCustomer(
    new PersistentCustomer(baseCustomer, repository)
);
```

**Same decorators. Different combinations. Zero duplication.**

---

### 3. Simplified Testing

Each decorator tests in complete isolation:

```java
@Test
public void validCustomer_shouldRejectPastDates() {
    Customer mockCustomer = mock(Customer.class);
    Customer validCustomer = new ValidCustomer(mockCustomer);
    Car mockCar = mock(Car.class);
    LocalDate pastDate = LocalDate.now().minusDays(1);

    assertThrows(IllegalArgumentException.class, () ->
        validCustomer.rentCar(mockCar, pastDate, LocalDate.now())
    );

    // Mock was never called — validation stopped it
    verify(mockCustomer, never()).rentCar(any(), any(), any());
}

@Test
public void loggedCustomer_shouldLogSuccessAndFailure() {
    Customer mockCustomer = mock(Customer.class);
    Logger mockLogger = mock(Logger.class);
    Customer loggedCustomer = new LoggedCustomer(mockCustomer, mockLogger);

    loggedCustomer.rentCar(mockCar, from, to);

    verify(mockLogger).info(contains("RENT: Starting"));
    verify(mockLogger).info(contains("Successfully completed"));
}
```

No complex setup. No mocking of unrelated dependencies. Pure, focused tests.

---

### 4. Single Responsibility Principle (SRP)

The SRP can be formalized as: [**SRP ≡ max(COHESION) ∧ min(COUPLING)**](https://medium.com/@andreas.wagner.info/the-single-responsibility-principle-beyond-myths-to-metrics-602d1a3efb49)

Each decorator class strictly uses only two components: a delegate object and one tool.  
The delegate object ensures maximum cohesion, and coupling is reduced due to the use of a single tool.

```
ValidCustomer        → Validates business rules
PersistentCustomer   → Persists to database (e.g. via Spring Data JPA)
CachedCustomer       → Manages cache
LoggedCustomer       → Writes audit logs
PublishedCustomer    → Publishes domain events
```

Not:

```
CustomerService → Validates + Persists + Caches + Logs + Publishes  ❌
```

When we need to change the caching strategy, we open **CachedCustomer** — not many mixed line service class.

---

### 5. Open/Closed Principle

Need to add metrics? Don't modify existing code. Add a decorator:

```java
public class MetricsCustomer implements Customer {

    private final Customer origin;
    private final MeterRegistry metrics;

    public MetricsCustomer(Customer origin, MeterRegistry metrics) {
        this.origin = origin;
        this.metrics = metrics;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        Timer.Sample sample = Timer.start(metrics);
        try {
            origin.rentCar(car, from, to);
            sample.stop(metrics.timer("customer.rent.success"));
        } catch (Exception e) {
            metrics.counter("customer.rent.failure").increment();
            throw e;
        }
    }

    @Override
    public void returnCar(Car car) {
        Timer.Sample sample = Timer.start(metrics);
        try {
            origin.returnCar(car);
            sample.stop(metrics.timer("customer.return.success"));
        } catch (Exception e) {
            metrics.counter("customer.return.failure").increment();
            throw e;
        }
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}

// Compose it in — zero changes to existing code
Customer customer = new MetricsCustomer(metric,  // NEW: Metrics
    new ValidCustomer(
        new PublishedCustomer(queue,
            new LoggedCustomer(log,
                new CachedCustomer(cache,
                    new PersistentCustomer(baseCustomer, repository)
                )
            )
        )
    )
);
```

**Open for extension. Closed for modification.**

---

### 6. Business Concepts in Code

Our package structure can and should mirror the business domain.

Packages are **hierarchically organized** according to domain concepts — not technical layers. Three rules by [Robert Bräutigam](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/) govern this:

**Rule 1: Packages must never depend on sub-packages.**
The root package contains the most abstract concepts (interfaces), without knowing implementation details. Changes in sub-packages cannot affect the parent.

**Rule 2: Sub-packages must not introduce new concepts.**
All logical features of the application are already visible in the parent package as interfaces. Sub-packages only provide implementations and specializations.

**Rule 3: Packages must reflect business concepts, not technical ones.**

| ❌ Technical grouping | ✅ Domain language |
|---|---|
| `service/`, `repository/`, `controller/` | `carpool/`, `customer/`, `payment/` |
| `dto/`, `entity/`, `aggregate/` | `exchange/`, `storage/`, `messaging/` |
| `util/`, `config/`, `adapter/` | `audit/`, `notification/`, `billing/` |

```
carrental/
├── application/
│   └── SpringCarrentalApp.java     → Main, DI, Config
├── carpool/
│   ├── CachedCar.java
│   ├── LoggedCar.java
│   ├── PersistentCar.java
│   ├── PersistentCarPool.java
│   ├── PublishedCar.java
│   ├── SimpleCar.java
│   └── ValidCar.java
├── customer/
│   ├── CachedCustomer.java
│   ├── LoggedCustomer.java
│   ├── PublishedCustomer.java
│   ├── ServedCustomerPool.java      → REST Controller
│   ├── ServedCustomer.java          → expose data as REST
│   ├── SimpleCustomer.java
│   ├── PersistentCustomer.java
│   └── ValidCustomer.java
├── payment/
│   ├── StripePayment.java
│   ├── PayPalPayment.java
│   └── ValidPayment.java
├── rental/
│   ├── ServedRentals.java          → REST Controller
│   ├── ...java
│   ...
├── exchange/
│   ├── paypal/                     → Paypal REST + DTOs
│   ├── resource/                   → REST DTOs
│   ├── stripe/                     → Stripe REST + DTOs
│   ├── storage/                    → JPA Entities
│   ├── messaging/                  → Queue: Kafka + AVRO DTOs
│   └── JsonMedia.java
│
├── Car.java                        → Domain Entity interface
├── CarPool.java                    → Domain Aggregate interface
├── Customer.java                   → Domain Entity interface
├── CustomerPool.java                  → Domain Group interface
├── CarrentalApp.java               → Root Composition interface
└── Media.java                      → Printer interface
```

- When business says *"there's a problem with car rentals"* → go to **carpool/**.
- When they say *"payment processing is slow"* → go to **payment/**.

**The code structure is the business context diagram.**

Compare this to the traditional layered approach:

```
carrental/
├── service/      ← Where's the car?
├── repository/   ← Where's the customer?
├── controller/   ← Where's the payment?
└── dto/          ← Where's the business?
```

---

## The Core Message: Decorators ARE Your Business Process

**Traditional thinking:**
> "Decorators are a technical pattern for adding functionality."

**Business-driven thinking:**
> "Decorators ARE the business process, made executable."

```
Validation → Events → Logging → Caching → Persistence
    ↓           ↓        ↓         ↓           ↓
 Business    Domain    Audit   Performance   Data
  Rules      Events    Trail   Optimization  Storage
```

- When Product Owner says *"we need to validate rental dates"* → add **ValidCustomer**.
- When Compliance says *"we need audit trails"* → add **LoggedCustomer**.
- When Operations says *"we need event-driven architecture"* → add **PublishedCustomer**.

Each business requirement = one decorator. Clear. Traceable. Maintainable.

---

## Complete Example: REST API with Printers

### Step 1: Define Your Domain Interfaces (No Getters!)

```java
// ROOT: Customer as the actor who rents cars
public interface Customer {
    void rentCar(Car car, LocalDate from, LocalDate to);
    void returnCar(Car car);
    void print(Media media);
}

// ROOT: Car as a rentable asset
public interface Car {
    boolean available();
    BigDecimal price(LocalDate from, LocalDate to);
    void print(Media media);
}

// ROOT: Factory for decorated car instances
public interface CarPool {
    Car carOf(String carId);
}

// ROOT: Printer interface
public interface Media {
    Media with(String name, String value);
    Media with(String name, BigDecimal value);
    Media with(String name, LocalDate value);
    Media with(String name, boolean value);
}

// ROOT: Application composition
public interface CarrentalApp {
    void run();
    CarPool carPool();
    CustomerPool CustomerPool();
}
```

### Step 2: Create Your First Decorator

```java
// Validation
public class ValidCustomer implements Customer {

    private final Customer origin;

    public ValidCustomer(Customer origin) {
        this.origin = origin;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        if (car == null)
            throw new IllegalArgumentException("Car required");
        if (from.isBefore(LocalDate.now()))
            throw new IllegalArgumentException("Date cannot be in the past");
        if (to.isBefore(from))
            throw new IllegalArgumentException("Invalid date range");

        origin.rentCar(car, from, to);
    }

    @Override
    public void returnCar(Car car) {
        if (car == null)
            throw new IllegalArgumentException("Car required");
        origin.returnCar(car, from, to);
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}
```

### Step 3: Create the Storage Decorator

```java
// PersistentCustomer.java
public class PersistentCustomer implements Customer {

    private final String id;
    private final CustomerRepository repository;

    public PersistentCustomer(String id, CustomerRepository repository) {
        this.id = id;
        this.repository = repository;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        CustomerEntity customerEntity = repository.findCustomerById(id)
            .orElseThrow(() -> new EntityNotFoundException("Customer [" + id + "] not found"));

        CarEntity carEntity = repository.findCarById(car.id())
            .orElseThrow(() -> new EntityNotFoundException("Car [" + car.id() + "] not found"));

        repository.saveRental(new RentalEntity(customerEntity, carEntity, from, to));
    }

    @Override
    public void returnCar(Car car) {
        RentalEntity entity = repository.findCustomerRentalByCarId(id, car.id())
            .orElseThrow(() -> new EntityNotFoundException("Rental of Custormer not found"));

        repository.remove(entity);
    }

    @Override
    public void print(Media media) {
        CustomerEntity entity = repository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Customer [" + id + "] not found"));

        // Customer prints itself to media — NO GETTERS in domain code!
        media.with("customerId", entity.getId())
             .with("name", entity.getName())
             .with("email", entity.getEmail());
    }
}
```

### Step 4: Compose at the Root

```java
// Persistent- factory for decorated customers
public class PersistentCustomerPool {

    private final CustomerRepository repository;
    private final CacheManager cache;
    private final Logger log;
    private final KafkaTemplate<String, String> kafka;

    public Customer customerOf(String customerId) {
        Customer customer = new PersistentCustomer(customerId, repository);
        customer = new CachedCustomer(customer, cache);
        customer = new LoggedCustomer(customer, log);
        customer = new PublishedCustomer(customer, kafka);
        customer = new ValidCustomer(customer);
        return customer;
    }
}

// Spring based — composition root
@Configuration
@SpringBootApplication
@ComponentScan(basePackages = {"com.company.**"})
public class SpringCarrentalApp implements CarrentalApp {

    private final CarRepository carRepository;
    private final CustomerRepository customerRepository;
    private final CacheManager cache;
    private final Logger log;
    private final KafkaTemplate<String, String> kafka;

    public static void main(String[] args) {
        SpringApplication.run(SpringCarrentalApp.class, args);
    }

    @Bean
    @Override
    public CarPool carPool() {
        return new PersistentCarPool(carRepository, cache, log, kafka);
    }

    @Bean
    @Override
    public CustomerPool customers() {
        return new PersistentCustomerPool(customerRepository, cache, log, kafka);
    }
}
```

### Step 5: Use It in the REST Controller

```java
@RestController
@RequestMapping("/api/rentals")
public class ServedRentals {

    @Autowired
    private CarPool carPool;

    @Autowired
    private PersistentCustomerPool customers;

    @PostMapping
    public ResponseEntity<String> rentCar(@RequestBody RentCarRequest request) {
        // Resolve decorated objects
        Customer customer = customerPool.customerOf(request.getCustomerId());
        Car car = carPool.carOf(request.getCarId());

        // Execute — the decorator chain handles validation, logging, caching, events
        customer.rentCar(car, request.getRentFrom(), request.getRentTo());

        // Print to JSON — NO GETTERS!
        Media customerMedia = new JsonMedia();
        customer.print(customerMedia);

        Media carMedia = new JsonMedia();
        car.print(carMedia);

        String response = String.format(
            "{\"success\":true,\"customer\":%s,\"car\":%s,\"from\":\"%s\",\"to\":\"%s\"}",
            customerMedia.json(), carMedia.json(),
            request.getRentFrom(), request.getRentTo()
        );

        return ResponseEntity.ok()
            .contentType(MediaType.APPLICATION_JSON)
            .body(response);
    }
}
```

**What happens when `POST /api/rentals` is called?**

```
HTTP POST /api/rentals
    ↓
ServedRentals.rentCar()
    ↓
customer.rentCar(car, from, to)
   ├─ ValidCustomer:     ✅ Validates car, dates
   ├─ PublishedCustomer: 📡 Prepares Kafka event
   ├─ LoggedCustomer:    📝 Logs "RENT: Starting..."
   ├─ CachedCustomer:    🗑️ Invalidates cache
   └─ PersistentCustomer:    💾 Saves via Spring Data JPA
    ↓
customer.print(new JsonMedia())   // Customer prints itself
car.print(new JsonMedia())        // Car prints itself
    ↓
HTTP Response (JSON) — no getters used
```

---

## God Service vs. Decorators

### ❌ The God Service (Traditional)

```java
public class CarService {

    public void rentCar(String carId, Customer customer,
                        LocalDate from, LocalDate to) {
        // Validation
        if (customer == null) throw new IllegalArgumentException("...");
        if (from.isBefore(LocalDate.now())) throw new IllegalArgumentException("...");

        // Database
        CarEntity entity = repository.findById(carId).orElseThrow();
        cache.evict("cars", carId);
        log.info("Renting car {} to customer {}", carId, customer.getId());
        entity.setRented(true);
        repository.save(entity);
        kafka.send("car-events", new CarRentedEvent(carId));
        log.info("Car {} successfully rented", carId);
        // Impossible to test in isolation
        // Changes to one concern affect everything
    }

    public void returnCar(String carId, Customer customer) {
        // ...
    }

}
```

🛑 Testing requires mocking everything  
🛑 Changes ripple through unrelated code  
🛑 Concerns cannot be reused independently  
🛑 Violates all SOLID principles  
🛑 Getters break encapsulation

### ✅ Decorators + Printers (Business-Driven)

```java
// Each class: ONE responsibility, NO getters
// ValidCustomer     → ONLY validates
// PublishedCustomer → ONLY publishes events
// LoggedCustomer    → ONLY logs
// CachedCustomer    → ONLY caches
// PersistentCustomer    → ONLY persists

Customer customer = new ValidCustomer(
    new PublishedCustomer(queue,
        new LoggedCustomer(log,
            new CachedCustomer(cache,
                new PersistentCustomer(customerId, repository)
            )
        )
    )
);

customer.rentCar(car, from, to);  // Executes the entire chain
customer.print(new JsonMedia());
```

✅ Test each concern in isolation  
✅ Change one concern without affecting others  
✅ Reuse concerns in different combinations  
✅ Code structure reveals the business process  
✅ Respects all SOLID principles  
✅ Perfect encapsulation — no getters

---

## When to Use Decorators

**Perfect for:**
- Cross-cutting concerns (logging, caching, validation, metrics)
- Business processes with clear, ordered stages
- Systems requiring flexible composition
- Code that must be highly testable
- Domain-driven design implementations

**Not ideal for:**
- Simple CRUD with no cross-cutting concerns
- Tight performance constraints (minimal overhead exists)
- Teams unfamiliar with composition patterns (requires onboarding)

---

## Key Takeaways

**Decorators ≠ Technical Pattern** — They are our business process made executable.

**Code Structure = Business Context** — Package by domain (`carpool/`, `payment/`, `customer/`), not by layer (`service/`, `repository/`).

**One Decorator = One Concern** — `ValidCustomer` validates. `LoggedCustomer` logs. `PersistentCustomer` persists. Period.

**Composition > Inheritance** — Build complex behavior by composing simple decorators.

**Printers > Getters** — Objects print themselves to media instead of exposing data. Perfect encapsulation + flexible output formats (JSON, XML, HTML).

**SOLID Principles Emerge Naturally** — SRP, OCP, DIP all respected by default.

---

## The Bottom Line

🛑 Stop writing *God Classes* that do everything.  
🛑 Stop hiding business logic in *technical layers*.

**Start thinking in decorators:**

- When someone asks *"Where's the validation logic?"* → **ValidCustomer**.
- When they ask *"Where's the caching?"* → **CachedCustomer**.
- When they ask *"How does the rental process work?"* → show them the **decorator chain**.

Your code becomes self-documenting. Your architecture becomes business-driven. Your tests become trivial.

> That's the power of the Decorator Pattern.

---

## Resources

- [Happy Packaging](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/) — Robert Bräutigam
- [Composition Root Pattern](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) — Mark Seemann
- [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html) — Yegor Bugayenko
- [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html) — Yegor Bugayenko
- [Composable Decorators vs. Imperative Utility Methods](https://www.yegor256.com/2015/02/26/composable-decorators.html) — Yegor Bugayenko
- [Defensive Programming via Validating Decorators](https://www.yegor256.com/2016/01/26/defensive-programming.html) — Yegor
Bugayenko
- [Maintaining Model Integrity](https://www.vzurauskas.com/2018/07/24/maintaining-model-integrity) - Vytautas Žurauskas
- Gang of Four — *Design Patterns* (1994)
- Robert C. Martin — *Clean Code*
- Yegor Bugayenko — *Elegant Objects*, Vol. 1 & 2

---

*What patterns have transformed your code structure? Have you tried the "Printers Instead of Getters" approach? Feel free to comment.*
