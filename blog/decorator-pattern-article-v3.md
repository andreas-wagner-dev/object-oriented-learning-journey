# The Decorator Pattern — Where Business Logic Meets Clean Code

> How a simple pattern transforms code from technical plumbing into business storytelling.

## The Problem Every Developer Faces

We're building a car rental system. Simple enough, right?

A car gets rented, we **store** it to the database, **log** it, **cache** it for performance, **publish** an event for analytics, **validate** the input, and eventually **charge** the customer. So we often write something like this:

```java
public class CarRentalService {

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

  }

  public void payRental(String rentalId, Customer customer) {

    // Validation
    RentalEntity rental = rentalRepository.findById(rentalId).orElseThrow();
    PaymentEntity payment = paymentRepository.findPreferredBy(customer.getId()).orElseThrow();

    // Calculate
    long days = rental.getRentFrom().until(rental.getRentTo(), ChronoUnit.DAYS);
    BigDecimal amount = rental.getDailyRate().multiply(BigDecimal.valueOf(days));

    // Payment
    if ("stripe".equals(entity.getType()))
      new StripeClient().charge(payment.getToken(), amount);
    if ("paypal".equals(entity.getType()))
      new PayPalClient().charge(payment.getEmail(), amount);

    rental.setPaid(true);
    rentalRepository.save(rental);

    log.info("Rental {} successfully paid", rentalId);
  }
}
```

Every developer has written code like this. And every developer knows the pain:

**Want to test validation?** → Good luck mocking the database, cache, Kafka…  
**Need to change the logging format?** → Touch many lines of mixed concerns…  
**Want to reuse caching logic elsewhere?** → Copy-paste or refactor everything…

Splitting of this one Service class into `CarRentalService`, `CarReturnService` and `CarPaymentService` solve not realy the problem. The cross-cutting concerns are steel mixed and now we have to touch more classes for changes on that.
So maybe we can splitt also the cross-cutting concerns and put they `CarRentalValidationService`, `CarRentalCachingService` and `CarRentalLoggingService`. Is this realy helpfull?

**There's a better way. And it's hiding in plain sight.**

---

## The Decorator Pattern: Business Logic as Code

**Here's the radical idea:** what if our code structure looked exactly like the business process?

In a car rental business, a customer who rents or returns a car triggers the following steps:

1. ✅ Validate the rental request
2. 💾 Persist it to storage
3. 🗑️ Invalidate the cache
4. 📝 Log it for audit
5. 📡 Publish events for downstream systems

Why shouldn't our code reflect these exact flows?

```java
// This IS the rental business process
Customer customer = new ValidCustomer(       // 1. Validate first
    new PersistentCustomer(                  // 2. Then persist
        new CachedCustomer(                  // 3. Then invalidate cache
            new LoggedCustomer(              // 4. Then log
                new PublishedCustomer(       // 5. Finally publish events
                    baseCustomer,
                    queue
                ),
                log
            ),
            cache
        ),
        repository
    )
);

customer.rentCar(car, from, to);  // executes the full chain
customer.returnCar(car);          // same chain, different operation
```

And when a customer pays an open rental:

6. 💳 Charge the preferred payment method
7. 💾 Mark the rental as paid in storage


We can extend the Customer concept horizontally `PayableCustomer`:

```java
// It IS a Customer AND additionally knows how to pay rentals.
PayableCustomer payer =
    new PayableCustomer(customer, payments, rentalRepository);

payer.payRental(rental);  // extra method, not in Customer
```

**This is the Decorator Pattern** — and it's not just a technical pattern. It's business logic made visible.

---

### Business Concepts in Code

To achieve a progressive business flow of information, we must abandon the traditional layered mindset of Clean Architecture or DDD. 

**The traditional layered approach:**

```
carrental/
├── service/      ← Where's the car?
├── repository/   ← Where's the customer?
├── controller/   ← Where's the payment?
└── dto/          ← Where's the business?
```

Instead, we must consistently mirror business concepts: the package structure is organized hierarchically, following the real-world domain. This design is governed by three rules outlined by  [Robert Bräutigam](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/):

**Rule 1: Packages must never depend on sub-packages.**  
The root package contains the most abstract concepts (interfaces). Changes in sub-packages cannot affect the parent.

**Rule 2: Sub-packages should not introduce new concepts, just more details.**  
All features are already visible in the parent as interfaces. Sub-packages only implement them.

**Rule 3: Packages must reflect business concepts, not technical ones.**  
Always use the language of the domain - not that of the framework or an architectural pattern - in package names (strictly avoid technical grouping).

```
carrental/
├── application/
│   └── SpringCarrentalApp.java       → Main, DI, Composition Root
├── carfleet/
│   ├── PersistentCar.java
│   ├── PersistentCarFleet.java
│   ├── SimpleCar.java
│   └── ValidCar.java
├── customer/
│   ├── CachedCustomer.java
│   ├── PayableCustomer.java      ← horizontal decorator: payRental()
│   ├── LoggedCustomer.java
│   ├── MetricsCustomer.java
│   ├── PersistentCustomer.java
│   ├── PersistentCustomerPool.java
│   ├── PublishedCustomer.java
│   ├── SimpleCustomer.java
│   └── ValidCustomer.java
├── payment/
│   ├── PersistentPayments.java       ← resolves preferred Payment per customer
│   ├── StripePayment.java
│   ├── PayPalPayment.java
│   └── ValidPayment.java
├── rental/
│   ├── PersistentRentals.java
│   ├── ServedRentals.java            → REST Controller
│   └── SimpleRental.java
├── exchange/
│   ├── paypal/                       → PayPal HTTP client + DTOs
│   ├── resource/                     → REST request/response DTOs
│   ├── stripe/                       → Stripe HTTP client + DTOs
│   ├── storage/                      → JPA Entities + Repositories
│   ├── messaging/                    → Kafka + AVRO DTOs
│   └── JsonMedia.java
│
├── Car.java                          → Domain interface (ROOT)
├── CarFleet.java                     → Domain interface (ROOT)
├── CarrentalApp.java                 → Composition interface (ROOT)
├── Customer.java                     → Domain interface (ROOT)
├── CustomerPool.java                 → Domain interface (ROOT)
├── Media.java                        → Printer interface (ROOT)
├── Payment.java                      → Domain interface (ROOT)
├── Payments.java                     → Domain interface (ROOT)
├── Rental.java                       → Domain interface (ROOT)
└── Rentals.java                      → Domain interface (ROOT)
```

- When business says *"there's a problem with rentals"* → **rental/**.
- When they say *"payment processing is slow"* → **payment/**.
- When they ask *"how does charging work?"* → **PayableCustomer** + **PersistentPayments**.


## The Domain Interfaces

Every interface lives at the **root package** — the most abstract layer. Sub-packages only implement, never redefine.

```java
// ROOT: Customer is the actor who rents and returns cars
public interface Customer {
    void rentCar(Car car, LocalDate from, LocalDate to);
    void returnCar(Car car);
    void print(Media media);
}

// ROOT: A single rentable car
public interface Car {
    boolean available();
    void print(Media media);
}

// ROOT: An open or closed rental — the rentable contract
public interface Rental {
    BigDecimal price();
    void print(Media media);
}

// ROOT: All open rentals belonging to a customer
public interface Rentals {
    Rental rentalOf(String rentalId);
    void print(Media media);
}

// ROOT: A single payment method
public interface Payment {
    void charge(BigDecimal amount);
    void print(Media media);
}

// ROOT: All payment methods of a customer
public interface Payments {
    Payment preferred();
    void print(Media media);
}

// ROOT: Factories
public interface CarFleet {
    Car carOf(String carId);
}

public interface CustomerPool {
    Customer customerOf(String customerId);
}

// ROOT: Printer — objects print themselves, no getters
public interface Media {
    Media with(String name, String value);
    Media with(String name, BigDecimal value);
    Media with(String name, LocalDate value);
    Media with(String name, boolean value);
}

// ROOT: Application composition
public interface CarrentalApp {
    void run();
    CarFleet carFleet();
    CustomerPool customerPool();
}
```

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

// Customer prints itself to any medium
Media media = new JsonMedia();
customer.print(media);
String json = media.json();
```

**Why this matters:**

- 🔒 **Encapsulation:** `Customer` doesn't expose internal data
- 🧠 **Smart Objects:** `Customer` controls its own representation
- 🎨 **Flexibility:** Same object can print to JSON, XML, HTML
- 📦 **No DTOs needed:** Objects print directly to HTTP responses

The same principle applies to `Rental` and `Payment` — they print themselves, hiding every implementation detail.

---

## Six Transformative Benefits

### 1. Clean Separation of Concerns

Each decorator has *one job*. Just one.

```java
// ValidCustomer: ONLY validates
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

        origin.returnCar(car);
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}

// LoggedCustomer: ONLY logs
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


### 2. Flexible Composition

Different scenarios need different combinations of concerns:

```java
// Production: Full chain
Customer production = new ValidCustomer(
    new PersistentCustomer(
        new CachedCustomer(
            new LoggedCustomer(
                new PublishedCustomer(baseCustomer, queue),
                log
            ),
            cache
        ),
        repository
    )
);

// Admin override: Skip validation
Customer admin = new PersistentCustomer(
    new LoggedCustomer(
        new PublishedCustomer(baseCustomer, queue),
        log
    ),
    repository
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

    // Delegation never reached — validation stopped it
    verify(mockCustomer, never()).rentCar(any(), any(), any());
}

@Test
public void loggedCustomer_shouldLogSuccessAndFailure() {
    Customer mockCustomer = mock(Customer.class);
    Logger mockLogger = mock(Logger.class);
    Car mockCar = mock(Car.class);
    LocalDate from = LocalDate.now();
    LocalDate to = from.plusDays(3);

    Customer loggedCustomer = new LoggedCustomer(mockCustomer, mockLogger);
    loggedCustomer.rentCar(mockCar, from, to);

    verify(mockLogger).info(contains("RENT: Starting"));
    verify(mockLogger).info(contains("Successfully completed"));
}

@Test
public void payer_shouldChargePreferredPaymentOnPayRental() {
    Customer mockCustomer = mock(Customer.class);
    Payment mockPayment = mock(Payment.class);
    Payments mockPayments = mock(Payments.class);
    Rental mockRental = mock(Rental.class);
    BigDecimal price = new BigDecimal("267.00");

    when(mockPayments.preferred()).thenReturn(mockPayment);
    when(mockRental.price()).thenReturn(price);

    PayableCustomer cwr =
        new PayableCustomer(mockCustomer, mockPayments, rentalRepository);
    cwr.payRental(mockRental);

    verify(mockPayment).charge(price);
}
```

No complex setup. No mocking of unrelated dependencies. Pure, focused tests.

---

### 4. Single Responsibility Principle (SRP)

The SRP can be formalized as: [**SRP ≡ max(COHESION) ∧ min(COUPLING)**](https://medium.com/@andreas.wagner.info/the-single-responsibility-principle-beyond-myths-to-metrics-602d1a3efb49)

Each decorator class strictly uses only two components: a delegate object and one collaborator. The delegate ensures maximum cohesion; the single collaborator minimizes coupling.

```
ValidCustomer        → Validates business rules
PersistentCustomer   → Persists to database (via Spring Data JPA)
CachedCustomer       → Manages cache
LoggedCustomer       → Writes audit logs
PublishedCustomer    → Publishes domain events
PayableCustomer     → Charges payment for a rental (horizontal extension)
```

Not:

```
CustomerService → Validates + Persists + Caches + Logs + Publishes + Charges  ❌
```

When we need to change the caching strategy, we open **CachedCustomer** — not a 400-line mixed-concern service class.

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
Customer customer = new MetricsCustomer(
    new ValidCustomer(
        new PersistentCustomer(
            new CachedCustomer(
                new LoggedCustomer(
                    new PublishedCustomer(baseCustomer, queue),
                    log
                ),
                cache
            ),
            repository
        )
    ),
    meterRegistry  // NEW — everything else untouched
);
```

**Open for extension. Closed for modification.**


## The Core Message: Decorators ARE Our Business Process

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

And for payment — a horizontal extension that enriches `Customer` without modifying it:

```
PayableCustomer.payRental(rental)
    → payments.preferred().charge(rental.price())
    → rental marked as paid in storage
```

- Product Owner says *"validate rental dates"* → add **ValidCustomer**
- Compliance says *"we need audit trails"* → add **LoggedCustomer**
- Finance says *"charge on return"* → add **PayableCustomer**

Each business requirement = one class. Clear. Traceable. Maintainable.

---

## Vertical vs. Horizontal Decorating

Yegor Bugayenko distinguishes two kinds of decorator extension. Understanding both is key to the `PayableCustomer` design.

### Vertical Decorators — same interface, new behavior

A **vertical decorator** wraps an existing object and implements the *same* interface. Every decorator we have seen so far (`ValidCustomer`, `LoggedCustomer`, `PersistentCustomer`) is vertical. The caller never needs to know which concrete type it holds.

```java
Customer customer = new ValidCustomer(       // same interface: Customer
    new LoggedCustomer(                      // same interface: Customer
        new PersistentCustomer(id, repo)     // same interface: Customer
    )
);
customer.rentCar(car, from, to);  // any Customer works here
```

### Horizontal Decorators — same interface + new methods

A **horizontal decorator** also implements the base interface, but *adds* methods that are not part of it. This is exactly how `java.io` is designed:

```java
// BufferedReader IS-A Reader (vertical) AND adds readLine() (horizontal)
BufferedReader reader = new BufferedReader(new FileReader(path));
String line = reader.readLine();  // not in Reader — only on BufferedReader
```

`PayableCustomer` follows the same principle:

```java
// PayableCustomer IS-A Customer (vertical) AND adds payRental() (horizontal)
PayableCustomer payer = new PayableCustomer(customer, payments, rentals);
payer.rentCar(car, from, to);  // Customer behaviour — full chain
payer.payRental(rental);       // horizontal extension — not in Customer
```

The caller (`ServedRentals`) holds the concrete type `PayableCustomer` deliberately — it needs the extra method. All other call sites continue to use `Customer` and are unaffected.

---

## Complete Example: REST API with Printers

### Step 1: Domain Interfaces (No Getters!)

Already defined at the root package above.

### Step 2: Payment Implementations

```java
// StripePayment.java — in payment/
public class StripePayment implements Payment {

    private final String token;
    private final StripeClient stripe;

    public StripePayment(String token, StripeClient stripe) {
        this.token = token;
        this.stripe = stripe;
    }

    @Override
    public void charge(BigDecimal amount) {
        stripe.charge(token, amount);
    }

    @Override
    public void print(Media media) {
        media.with("paymentType", "stripe")
             .with("last4", token.substring(token.length() - 4));
    }
}

// PayPalPayment.java — in payment/
public class PayPalPayment implements Payment {

    private final String email;
    private final PayPalClient paypal;

    public PayPalPayment(String email, PayPalClient paypal) {
        this.email = email;
        this.paypal = paypal;
    }

    @Override
    public void charge(BigDecimal amount) {
        paypal.createOrder(email, amount);
    }

    @Override
    public void print(Media media) {
        media.with("paymentType", "paypal")
             .with("email", email);
    }
}

// ValidPayment.java — in payment/
public class ValidPayment implements Payment {

    private final Payment origin;

    public ValidPayment(Payment origin) {
        this.origin = origin;
    }

    @Override
    public void charge(BigDecimal amount) {
        if (amount == null || amount.compareTo(BigDecimal.ZERO) <= 0)
            throw new IllegalArgumentException("Charge amount must be positive");
        origin.charge(amount);
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}

// PersistentPayments.java — in payment/
// Resolves the preferred Payment for a given customer from the database.
public class PersistentPayments implements Payments {

    private final String customerId;
    private final PaymentRepository repository;
    private final StripeClient stripe;
    private final PayPalClient paypal;

    public PersistentPayments(String customerId, PaymentRepository repository,
                               StripeClient stripe, PayPalClient paypal) {
        this.customerId = customerId;
        this.repository = repository;
        this.stripe = stripe;
        this.paypal = paypal;
    }

    @Override
    public Payment preferred() {
        PaymentEntity entity = repository
            .findPreferredByCustomerId(customerId)
            .orElseThrow(() -> new EntityNotFoundException(
                "No preferred payment for customer [" + customerId + "]"
            ));

        Payment payment = switch (entity.getType()) {
            case "stripe"  -> new StripePayment(entity.getToken(), stripe);
            case "paypal"  -> new PayPalPayment(entity.getEmail(), paypal);
            default        -> throw new IllegalStateException(
                                    "Unknown payment type: " + entity.getType());
        };

        return new ValidPayment(payment);
    }

    @Override
    public void print(Media media) {
        preferred().print(media);
    }
}
```

### Step 3: Rental and the Horizontal Decorator

```java
// SimpleRental.java — in rental/
// Wraps a persisted RentalEntity and makes it a domain object.
public class SimpleRental implements Rental {

    private final RentalEntity entity;

    public SimpleRental(RentalEntity entity) {
        this.entity = entity;
    }

    @Override
    public BigDecimal price() {
        long days = entity.getRentFrom().until(entity.getRentTo(), ChronoUnit.DAYS);
        return entity.getDailyRate().multiply(BigDecimal.valueOf(days));
    }

    @Override
    public void print(Media media) {
        media.with("rentalId",  entity.getId())
             .with("carId",     entity.getCarId())
             .with("from",      entity.getRentFrom())
             .with("to",        entity.getRentTo())
             .with("price",     price());
    }
}

// PersistentRentals.java — in rental/
// Resolves open rentals of a customer from the database.
public class PersistentRentals implements Rentals {

    private final String customerId;
    private final RentalRepository repository;

    public PersistentRentals(String customerId, RentalRepository repository) {
        this.customerId = customerId;
        this.repository = repository;
    }

    @Override
    public Rental rentalOf(String rentalId) {
        RentalEntity entity = repository
            .findByIdAndCustomerId(rentalId, customerId)
            .orElseThrow(() -> new EntityNotFoundException(
                "Rental [" + rentalId + "] not found for customer [" + customerId + "]"
            ));
        return new SimpleRental(entity);
    }

    @Override
    public void print(Media media) {
        // could list all open rentals — omitted for brevity
    }
}
```

Now the horizontal decorator — the `BufferedReader` of our domain:

```java
// PayableCustomer.java — in customer/
//
// Horizontal decorator: IS-A Customer (full vertical chain delegated)
// AND adds payRental(Rental) — a method not present in the Customer interface.
//
// Analogy: java.io.BufferedReader extends Reader and adds readLine().
// Callers that need payRental() hold PayableCustomer explicitly.
// All other callers continue to use the Customer interface unchanged.
public class PayableCustomer implements Customer {

    private final Customer origin;
    private final Payments payments;
    private final RentalRepository rentalRepository;

    public PayableCustomer(Customer origin,
                                Payments payments,
                                RentalRepository rentalRepository) {
        this.origin = origin;
        this.payments = payments;
        this.rentalRepository = rentalRepository;
    }

    // ── Customer interface — fully delegated to the vertical chain ──────────

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        origin.rentCar(car, from, to);
    }

    @Override
    public void returnCar(Car car) {
        origin.returnCar(car);
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }

    // ── Horizontal extension — not in the Customer interface ────────────────

    /**
     * Charges the customer's preferred payment method for the given rental
     * and marks the rental as paid in storage.
     *
     * Analogous to BufferedReader.readLine() — an operation that only makes
     * sense at this enriched level, not in the base interface.
     */
    public void payRental(Rental rental) {
        Payment payment = payments.preferred();
        payment.charge(rental.price());

        rentalRepository.markAsPaid(rental);
    }
}
```

### Step 4: Customer Decorators

```java
// ValidCustomer.java — in customer/
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

        origin.returnCar(car);
    }

    @Override
    public void print(Media media) {
        origin.print(media);
    }
}

// PersistentCustomer.java — in customer/
public class PersistentCustomer implements Customer {

    private final String id;
    private final CustomerRepository repository;

    public PersistentCustomer(String id, CustomerRepository repository) {
        this.id = id;
        this.repository = repository;
    }

    @Override
    public void rentCar(Car car, LocalDate from, LocalDate to) {
        CustomerEntity customer = repository.findCustomerById(id)
            .orElseThrow(() -> new EntityNotFoundException("Customer [" + id + "] not found"));

        CarEntity carEntity = repository.findCarById(car.id())
            .orElseThrow(() -> new EntityNotFoundException("Car [" + car.id() + "] not found"));

        repository.saveRental(new RentalEntity(customer, carEntity, from, to));
    }

    @Override
    public void returnCar(Car car) {
        RentalEntity entity = repository
            .findOpenRentalByCustomerAndCar(id, car.id())
            .orElseThrow(() -> new EntityNotFoundException("Open rental not found"));

        entity.setReturnedAt(LocalDate.now());
        repository.save(entity);
    }

    @Override
    public void print(Media media) {
        CustomerEntity entity = repository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Customer [" + id + "] not found"));

        media.with("customerId", entity.getId())
             .with("name",       entity.getName())
             .with("email",      entity.getEmail());
    }
}
```

### Step 5: Compose at the Root

```java
// PersistentCustomerPool.java — in customer/
public class PersistentCustomerPool implements CustomerPool {

    private final CustomerRepository customerRepository;
    private final PaymentRepository  paymentRepository;
    private final RentalRepository   rentalRepository;
    private final CacheManager       cache;
    private final Logger             log;
    private final KafkaTemplate<String, String> kafka;
    private final StripeClient  stripe;
    private final PayPalClient  paypal;

    @Override
    public Customer customerOf(String customerId) {
        Customer customer = new PersistentCustomer(customerId, customerRepository);
        customer = new CachedCustomer(customer, cache);
        customer = new LoggedCustomer(customer, log);
        customer = new PublishedCustomer(customer, kafka);
        customer = new ValidCustomer(customer);
        return customer;
    }

    /**
     * Returns a PayableCustomer — the horizontal extension —
     * for endpoints that need to charge a rental.
     * The vertical decorator chain is reused; payRental() is added on top.
     */
    public PayableCustomer PayableCustomerOf(String customerId) {
        Payments payments = new PersistentPayments(
            customerId, paymentRepository, stripe, paypal
        );
        return new PayableCustomer(
            customerOf(customerId),   // reuse the full vertical chain
            payments,
            rentalRepository
        );
    }
}

// SpringCarrentalApp.java — composition root
@Configuration
@SpringBootApplication
@ComponentScan(basePackages = {"com.company.**"})
public class SpringCarrentalApp implements CarrentalApp {

    private final CarRepository      carRepository;
    private final CustomerRepository customerRepository;
    private final PaymentRepository  paymentRepository;
    private final RentalRepository   rentalRepository;
    private final CacheManager       cache;
    private final Logger             log;
    private final KafkaTemplate<String, String> kafka;
    private final StripeClient  stripe;
    private final PayPalClient  paypal;

    public static void main(String[] args) {
        SpringApplication.run(SpringCarrentalApp.class, args);
    }

    @Bean
    @Override
    public CarFleet carFleet() {
        return new PersistentCarFleet(carRepository, cache, log, kafka);
    }

    @Bean
    @Override
    public CustomerPool customerPool() {
        return new PersistentCustomerPool(
            customerRepository, paymentRepository, rentalRepository,
            cache, log, kafka, stripe, paypal
        );
    }
}
```

### Step 6: Use It in the REST Controller

```java
// ServedRentals.java — in rental/
@RestController
@RequestMapping("/api/rentals")
public class ServedRentals {

    @Autowired private CarFleet            carFleet;
    @Autowired private PersistentCustomerPool customerPool;

    // ── POST /api/rentals  — rent a car ─────────────────────────────────────

    @PostMapping
    public ResponseEntity<String> rentCar(@RequestBody RentCarRequest request) {

        Customer customer = customerPool.customerOf(request.getCustomerId());
        Car car = carFleet.carOf(request.getCarId());

        // One line executes: validate → persist → cache → log → publish
        customer.rentCar(car, request.getRentFrom(), request.getRentTo());

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

    // ── DELETE /api/rentals/{carId}  — return a car ─────────────────────────

    @DeleteMapping("/{carId}")
    public ResponseEntity<String> returnCar(@PathVariable String carId,
                                            @RequestBody ReturnCarRequest request) {
        Customer customer = customerPool.customerOf(request.getCustomerId());
        Car car = carFleet.carOf(carId);

        customer.returnCar(car);

        Media customerMedia = new JsonMedia();
        customer.print(customerMedia);

        return ResponseEntity.ok()
            .contentType(MediaType.APPLICATION_JSON)
            .body(String.format("{\"success\":true,\"customer\":%s}", customerMedia.json()));
    }

    // ── POST /api/rentals/{rentalId}/payment  — pay an open rental ──────────
    //
    // PayableCustomer is used explicitly here because payRental() is a
    // horizontal extension — not part of the Customer interface.
    // Exactly as BufferedReader is used explicitly when readLine() is needed.

    @PostMapping("/{rentalId}/payment")
    public ResponseEntity<String> payRental(@PathVariable String rentalId,
                                            @RequestBody PayRentalRequest request) {

        // Horizontal decorator — holds the concrete type deliberately
        PayableCustomer PayableCustomer =
            customerPool.PayableCustomerOf(request.getCustomerId());

        // Resolve the open rental — fully encapsulated behind Rentals
        Rentals rentals = new PersistentRentals(
            request.getCustomerId(), rentalRepository
        );
        Rental rental = rentals.rentalOf(rentalId);

        // payRental: preferred payment charged, rental marked as paid
        PayableCustomer.payRental(rental);

        Media rentalMedia = new JsonMedia();
        rental.print(rentalMedia);

        Media customerMedia = new JsonMedia();
        PayableCustomer.print(customerMedia);

        String response = String.format(
            "{\"success\":true,\"customer\":%s,\"rental\":%s}",
            customerMedia.json(), rentalMedia.json()
        );

        return ResponseEntity.ok()
            .contentType(MediaType.APPLICATION_JSON)
            .body(response);
    }
}
```

**What happens when `POST /api/rentals/{rentalId}/payment` is called?**

```
POST /api/rentals/{rentalId}/payment
    ↓
ServedRentals.payRental()
    ↓
customerPool.payerOf(customerId)
    │  ↳ builds the vertical chain: ValidCustomer → ... → PersistentCustomer
    │  ↳ wraps it in PayableCustomer (horizontal layer)
    ↓
rentals.rentalOf(rentalId)
    ↳ PersistentRentals  →  SimpleRental (price encapsulated)
    ↓
payer.payRental(rental)
    ├─ payments.preferred()                → PersistentPayments resolves provider
    │   └─ ValidPayment(StripePayment)     → validates amount, then charges Stripe
    ├─ payment.charge(rental.price())      → Stripe charged
    └─ rentalRepository.markAsPaid(rental) → persisted
    ↓
rental.print(new JsonMedia())              → Rental prints itself
payer.print(new JsonMedia()) → Customer prints itself
    ↓
HTTP 200 (JSON) — no getters used
```

---

## God Service vs. Decorators

### ❌ The God Service (Traditional)

```java
public class CarRentalService {

    public void rentCar(String carId, Customer customer,
                        LocalDate from, LocalDate to) {
        if (customer == null) throw new IllegalArgumentException("...");
        if (from.isBefore(LocalDate.now())) throw new IllegalArgumentException("...");

        CarEntity entity = repository.findById(carId).orElseThrow();
        cache.evict("cars", carId);
        log.info("Renting car {} to customer {}", carId, customer.getId());
        entity.setRented(true);
        repository.save(entity);
        kafka.send("car-events", new CarRentedEvent(carId));
        log.info("Car {} successfully rented", carId);
    }

    public void returnCar(String carId, Customer customer) { /* ... */ }

    public void payRental(String rentalId, Customer customer) {
        RentalEntity rental = rentalRepository.findById(rentalId).orElseThrow();
        PaymentEntity payment = paymentRepository.findPreferredBy(customer.getId()).orElseThrow();

        // provider hardcoded — swap Stripe for PayPal = edit this service
        long days = rental.getRentFrom().until(rental.getRentTo(), ChronoUnit.DAYS);
        BigDecimal amount = rental.getDailyRate().multiply(BigDecimal.valueOf(days));
        stripe.charge(payment.getToken(), amount);

        rental.setPaid(true);
        rentalRepository.save(rental);
    }
}
```

🛑 Testing requires mocking everything  
🛑 Swap Stripe for PayPal? Edit the same class as logging and validation  
🛑 Concerns cannot be reused independently  
🛑 Violates all SOLID principles  
🛑 Getters break encapsulation everywhere

### ✅ Decorators + Printers (Business-Driven)

```java
// Vertical chain — each class: ONE responsibility, NO getters
Customer customer = new ValidCustomer(
    new PersistentCustomer(
        new CachedCustomer(
            new LoggedCustomer(
                new PublishedCustomer(baseCustomer, queue),
                log
            ),
            cache
        ),
        repository
    )
);
customer.rentCar(car, from, to);    // full chain
customer.returnCar(car);            // same chain, other operation
customer.print(new JsonMedia());    // prints itself

// Horizontal extension — add payRental() without touching Customer
PayableCustomer payer =
    new PayableCustomer(customer, payments, rentalRepository);

Payment payment = payments.preferred();  // Payments resolves provider
payer.payRental(rental);   // charges + marks as paid
```

✅ Test each concern in complete isolation  
✅ Swap payment providers by changing one class  
✅ Reuse decorators in different compositions  
✅ Code structure reveals the business process  
✅ Respects all SOLID principles  
✅ Perfect encapsulation — no getters

---

## When to Use Decorators

**Perfect for:**
- Cross-cutting concerns (logging, caching, validation, metrics, payment)
- Business processes with clear, ordered stages
- Systems requiring flexible composition
- Code that must be highly testable
- Domain-driven design implementations

**Not ideal for:**
- Simple CRUD with no cross-cutting concerns
- Tight performance constraints (decorator chains add minimal but non-zero overhead)
- Teams unfamiliar with composition patterns (requires onboarding)

---

## Key Takeaways

**Decorators ≠ Technical Pattern** — They are the business process made executable.

**Vertical vs. Horizontal** — Vertical decorators wrap the same interface. Horizontal decorators add new methods, like `java.io.BufferedReader.readLine()`. Use horizontal extension when a capability belongs at a specific enrichment level, not in the base contract.

**Code Structure = Domain Context** — Package by business concept (`carfleet/`, `customer/`, `payment/`, `rental/`), not by layer (`service/`, `repository/`).

**One Decorator = One Concern** — `ValidCustomer` validates. `LoggedCustomer` logs. `PersistentCustomer` persists. `PayableCustomer` charges. Period.

**Composition > Inheritance** — Build complex behaviour by composing simple decorators.

**Printers > Getters** — Objects print themselves to media. Perfect encapsulation + flexible output formats (JSON, XML, HTML).

**SOLID Principles Emerge Naturally** — SRP, OCP, DIP all respected by default.

---

## The Bottom Line

🛑 Stop writing *God Classes* that do everything.  
🛑 Stop hiding business logic in *technical layers*.

**Start thinking in decorators:**

- *"Where's the validation?"* → **ValidCustomer**
- *"Where's the caching?"* → **CachedCustomer**
- *"How does payment work?"* → **PayableCustomer** + **PersistentPayments**
- *"How does the rental process work?"* → show the **decorator chain**

Our code becomes self-documenting. Our architecture becomes business-driven. Our tests become trivial.

> That's the power of the Decorator Pattern.

---

## Resources

- [Happy Packaging](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/) — Robert Bräutigam
- [Composition Root Pattern](https://blog.ploeh.dk/2011/07/28/CompositionRoot/) — Mark Seemann
- [Printers Instead of Getters](https://www.yegor256.com/2016/04/05/printers-instead-of-getters.html) — Yegor Bugayenko
- [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html) — Yegor Bugayenko
- [Composable Decorators vs. Imperative Utility Methods](https://www.yegor256.com/2015/02/26/composable-decorators.html) — Yegor Bugayenko
- [Defensive Programming via Validating Decorators](https://www.yegor256.com/2016/01/26/defensive-programming.html) — Yegor Bugayenko
- [Maintaining Model Integrity](https://www.vzurauskas.com/2018/07/24/maintaining-model-integrity) — Vytautas Žurauskas
- Gang of Four — *Design Patterns* (1994)
- Robert C. Martin — *Clean Code*
- Yegor Bugayenko — *Elegant Objects*, Vol. 1 & 2

---

*What patterns have transformed your code structure? Have you tried the "Printers Instead of Getters" approach? Feel free to comment.*
