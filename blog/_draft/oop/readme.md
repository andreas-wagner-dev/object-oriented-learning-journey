# Business Context-Driven Code Structure 
**State: Draft!!!**
> *The Missing Link Between Business and Code*


## 1. **The Problem:** The Translation Gap

*Why your code structure should be your Business-Context Diagram?*

Imagine: A Business Analyst shows you a System Context Diagram of a **Car Rental System**, integrated with **Payment** providers (`PayPal`/`Stripe`), a **Customer** database and **User** booking interface.

```
                       User
                    (Booking)
                        │
                        ▼
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
 │ Payment │    │                 │    │  Customer   │
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │
 │         │    │  (Application)  │    │             │
 └─────────┘    └─────────────────┘    └─────────────┘
                        │
                        ▼
                ┌───────────────┐
                │               │
                │   Car Pool    │
                │  (Inventory)  │
                └───────────────┘
```

Then you open the code and find one of the following structures:

**Example 1:** Classic Layered Architecture
```
com.company.carrental
├── controller/
├── service/
├── repository/
├── dto/
├── entity/
└── config/
```
**Example 2:** Clean Architecture (Robert C. Martin)
```
com.company.carrental
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
**Example 3:** DDD Structure (Eric Evans)
```
com.company.carrental
├── aggregate/
├── entity/
├── valueobject/
├── repository/
├── service/
├── factory/
└── specification/
```
**Example 4:** Hexagonal Architecture (Alistair Cockburn's)
```
com.company.carrental
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
**Example 5:** Vertical Slices Architecture (Jimmy Bogard's)
```
com.company.carrental
├── features/
│   ├── rent-car/
│   ├── return-car/
│   └── manage-customers/
├── shared/
└── infrastructure/
```

**Where is Car?** - **Where is Customer?** - **Where is Payment?** - **Where is Booking from the Context Diagram?**

**You must mentally translate:**

* **Car** entity is probably hidden in the `service` package? - **Layered**
* Or maybe in `infrastructure`? - **Clean Architecture**
* Could also be in `domain` or `application`? - **DDD**
* Probably in `adapter` or `port`? - **Hexagonal Architecture**
* Maybe in `shared`? or `features/rent-car`? - **Vertical Slices**

Even modern patterns like Vertical Slices often focus primarily on technical functionalities and process logic (**verbs** like `rent-car`, `return-car`), instead of directly mapping domain objects (**things** like `Car`, `Customer`) and bounded contexts as central components to the package structure.

## 2. The Solution: Context-Driven Packaging

**The solution is surprisingly simple:** Your package structure should primarily reflect the Context Diagram.

```
carrental/
├── booking/
├── carpool/
├── customer/
└── payment/
```

**Screaming Architecture:** —> means your package structure IS your business context diagram. 

When someone asks: 
*	"Where is the car logic?" → `carpool/`
*	"Where is payment processing?" → `payment/`
*	"Where is customer data?" → `customer/`
*	"Where is the user booking?" → `booking/`
  
**This is a crucial strategic aspect of software architecture:** By directly mapping bounded contexts in code, we consistently apply the Ubiquitous Language at every level of system organization.

**Benefits**

**1. Code Navigation**
- Before: 1-2 minutes to locate feature
- After: 10-30 seconds
- Improvement: ~80-90% less time

**2. Onboarding New Developers**
- Before: "Where is the car logic?" → 5 minutes explanation
- After: "Look in the `carpool/` package" → 5-10 seconds
- Improvement: ~90% less explanation effort

**3. Business-Developer Communication**
- Before: Business says "PayPal problem" → Developer mentally translates to technical packages
- After: Business says "PayPal problem" → Developer goes directly to `payment/`
- Improvement: No translation overhead

---




--
## 3. The Strategic Part: Design

### 3.1 Object-Oriented Domain-Driven Analysis

**Business Capability Mapping (Top-Down)**

In modular monolithic structure, **Business Capability Mapping** is the primary method for context analysis and the definition of **Domain Responsibility**. It examines an organization’s capabilities by **focusing on the "what"** of the business rather than the "how." These identified capabilities are organized as top-level packages within the monolith, ensuring that the software architecture directly mirrors the business domain.

**Ubiquitous Language**

The  **Ubiquitous Language analysis** serves to fine-tune these boundaries and define the **Key Logic:** Whenever a single term assumes different meanings across departments, it signals the need for separate Bounded Contexts.

A car rental company must manage vehicles (**Carpool**), support customers (**Customer**), process payments (**Payment**), and manage reservations of users (**Booking**).

A striking example of linguistic differentiation can be found in the concept of the customer:

* In the **Customer** context, the customer is treated as a legal entity with master data and history.
* In the **Booking** context, however, the same customer acts as a user, performing interactions on the platform and making reservations.

These different perspectives lead to the "customer" being represented by different models (`Customer` vs. `User`) depending on the context, in order to clearly separate the respective business logic.

**Key Logic Example:** The distinction becomes critical when defining business rules. For instance, the logic for "Deleting a Customer" varies significantly:
* In the **Customer Context**, a "Delete" might trigger a check for legal data retention periods or active contracts.
* In the **Booking Context**, the same "Delete" action simply means invalidating an active web session or clearing a temporary resevation of a cart.

**Technical Boundary (Anti-Corruption Layer)**

The following structure implements an Anti-Corruption Layer (ACL) as concept to strictly decouple the core domain logic from external subsystems and technical infrastructure concerns. 

By employing an **Adapter/Bridge or Decorator-based approach**, this layer (centralized in the `exchange/` directory) acts  as a translator between different semantics. It ensures that the internal domain remains "pure" and is never compromised by the data structures or technical constraints of external dependencies, such as legacy databases, third-party APIs (e.g., PayPal), or messaging systems (e.g., Kafka).

### 3.2 Object-Oriented Domain-Driven Design (OO-DDD)

Based on the provided System Context Diagram and stratigic analysis, we can identify four distinct **Bounded Contexts**. Each represents a *specific linguistic* and *functional* **boundary** within the Car Rental System. It is important to note that a Bounded Context is not merely a database table, but a dedicated business area of responsibility. The identified Bounded Contexts are as follows:


## 3.1 The monolithic project structure

--

### 3.2 Identification and Definition of Bounded Contexts

In monolithic project structures, **Business Capability Mapping** is a key method for context analysis. It examines a company's organizational capabilities, focusing on the "what" of the business rather than the "how." The business capabilities identified in this way are organized as overarching packages within the monolithic structure. This ensures that the software architecture reflects the business domain.

Based on the provided System Context Diagram and business capabilities, we can identify four distinct **Bounded Contexts**. Each represents a *specific linguistic* and *functional* **boundary** within the Car Rental System. It is important to note that a Bounded Context is not merely a database table, but a dedicated business area of responsibility. The identified Bounded Contexts are as follows:

**1. Car Pool Context (Fleet Management)**

This is the most developed context in the structure, represented by the `carpool/` package.
   
* **Responsibility:** Managing the lifecycle and state of physical vehicles.
* **Key Logic:** Validation (`ValidCar`), Caching (`CachedCar`), and Inventory management (SimpleCar).
* **Technical Boundary:** It handles its own REST exposure (`ServedCarPool`) and Event Messaging (PublishedCar) by using DTOs from `exchange/resource/` and `exchange/messaging/`.

**2. Customer Context**

Represented by the `customer/` package.

* **Responsibility:** Managing renter profiles and communication.
* **Key Logic:** Persistence of customer data (`StoredCustomer`) and automated notifications.
* **Technical Boundary:** It integrates with the mailing system (`NotifiedCustomer`) using the SMTP protocols defined in `exchange/mailing/`.

**3. Payment Context**

Represented by the `payment/` package.

* **Responsibility:** Abstracting financial transactions.
* **Key Logic:** Processing payments via different providers like `PayPal` or `CreditCard`.
* **Technical Boundary:** It acts as a Gateway that consumes the specialized API clients and DTOs located in `exchange/paypal/`.

**4. Booking & Reservation - User Context**

Represented by the `booking/` package. This is a "user" context.

* **Responsibility:** Handling Booking & user sessions and server-side UI rendering.
* **Key Logic:** Managing the user Reservation and visual layout (`layout/`), UI components (`control/`), and web pages (`page/`).
* **Technical Boundary:** It manages the WebUser (session-based) and StoredUser (DB-based) identities.


The **Ubiquitous Language Analysis** serves to fine-tune the boundaries: As soon as the same term takes on a different meaning in different departments, this is a clear indication of separate contexts.

**Application:** A car rental company must manage vehicles (Carpool), support customers (Customer), process payments (Payment), and manage reservations (Booking).

A striking example of linguistic differentiation can be found in the concept of the customer:

* In the **Customer** context, the customer is treated as a legal entity with master data and history.
* In the **Booking** context, however, the same customer acts as a user, performing interactions on the platform and making reservations.

These different perspectives lead to the "customer" being represented by different models (`Customer` vs. `User`) depending on the context, in order to clearly separate the respective business logic.




The packages of an object-oriented system are based on clear OO-Design principles. There are **no layers** in the traditional sense of Clean Architecture. Instead, **packages are hierarchically organized** according to domain concepts.

```
carrental/
├── application/
│   ├── CarRentalApp.cs          ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs      ← Kafka Configuration
├── carpool/
│   ├── CachedCar.cs             ← Cache Decorator
│   ├── CachedCarPool.cs         ← Cache Decorator
│   ├── LoggedCar.cs             ← Logging Decorator
│   ├── SimpleCar.cs             ← Default Decorator (Factory of cars in DDD)
│   ├── StoredCar.cs             ← Database Decorator (use DTO's from exchange/storage/)
│   ├── StoredCarPool.cs         ← Database Decorator (use DTO's from exchange/storage/)
│   ├── ServedCarPool.cs         ← REST service for cars (use DTO's from exchange/resource/)
│   ├── PublishedCar.cs          ← Kafka Producer of Events (use DTO's from exchange/messaging/)
│   ├── ReceivedCar.cs           ← Kafka Consumer of Message (use DTO's from exchange/messaging/)
│   └── ValidCar.cs              ← Validation Decorator
├── customer/
│   ├── StoredCustomer.cs        ← Database Decorator
│   ├── StoredCustomers.cs       ← Database Decorator
│   ├── NotifiedCustomer.cs      ← Email Decorator (use SmtpsEmail from exchange/mailing/)
│   └── ...cs
├── exchange/                    → Anti-Corruption Layer
│   ├── paypal/                  → e.g. Paypal REST library, HTTP endpoint classes JSON/XML DTOs
│   │   ├── Paypal.cs            ← API client
│   │   ├── PaypalRequest.cs     ← Request DTO
│   │   └── PaypalResponse.cs    ← Response DTO
│   ├── resource/                → JSON/XML DTOs for REST service classes
│   │   ├── CarRequest.cs        ← Request DTO as JSON-Object  
│   │   ├── CarResoure.cs        ← Response DTO as JSON-Object 
│   │   └── CarResoures.cs       ← Response DTO as JSON-Array
│   ├── storage/                 ← EF Core           
│   │   ├── CarEntity.cs         ← EF Core DTO
│   │   ├── CarDbContext.cs      ← EF Core DAO
│   │   └── ...cs
│   ├── mailing/                 → Email: SMTPS, IMAPS or POP3S Protocol  
│   │    └── SmtpsEmail.cs       ← SMTPS Email
│   ├── messaging/               ← Queues like Kafka
│   │    └── CarRentedEvent.cs   ← AVRO DTOs
│   │   
│   └── .../
├── payment/                     ← (uses exchange/paypal/)
│   ├── PaypalPayment.cs         ← Paypal API as Decorator
│   ├── CardPayment.cs           ← Credit card Decorator
│   └── ...cs
├── booking/    
│   ├── user/                        ← user contepts and server side UI rendering
│   │   ├── control/                 ← UI common controls (used in page/)
│   │   ├── layout/                  ← UI Layout/Style components (used in page/)
│   │   ├── page/                    ← UI pages of the application
│   │   ├── Control.cs               ← abstract Control
│   │   ├── Layout.cs                ← abstract Layout (Helper/Util)
│   │   ├── Page.cs                  ← abstract Page extends Control
│   │   ├── StoredUser.cs            ← Db Decorator
│   │   └── WebUser.cs               ← Web/Session Decorator
│   ├── IUser.cs                     ← Domain Interface
│   ├── IUsers.cs
│   ├── IUserReservations.cs     ← Domain Collection Interface
├── CarNumber.cs                 ← Shared/Util/Helper for all packages
├── ICar.cs                      ← Domain Interface
├── ICarPool.cs                  ← Collection Interface
├── ICustomer.cs
├── ICustomers.cs
├── IReservation.cs              ← Domain Interface
├── IReservations.cs             ← Collection Interface
├── ICarRentalApp.cs             ← Composition Root Interface
└── ...
```

* When business says *“there’s a problem with car rentals”*, you go to **carpool/**
* When they say *“payment processing is slow”*, you go to **payment/**

The above structure employs a Decorator-based approach to rigorously decouple core domain logic from technical infrastructure concerns (located in the `exchange/` directory). 

## 3. The Technical Part: Implementation
---

## 4. Key Principles of Packaging and Naming Conventions

### 1) Packages Should Never Depend on Sub-Packages

Root package = Domain core, independent of everything.
- e.g., `ICar`, `ICustomer` as interfaces or abstract classes

Sub-packages = Implementations (adapters), dependent on core.
- e.g., `carpool/ValidCar.cs` implements `ICar`

### 2) Sub-Packages Don't Introduce New Concepts, Only Details

`car/StoredCar.cs` = detail of car persistence.

No new business concepts in sub-packages that don't exist as interfaces in root.

The `application/` package provide main method + (DI) injections of technical infrastructure.

### 3) Packages and Classes Reflect Business Concepts, Not Technical Roles

✅ **Recommended: Package names from Context Diagram**
- `payment/`, `inventory/`, `shipping/` (business concepts or external systems)
- `user/` (GUI interfaces or REST interfaces for GUI e.g. React)
- `exchange/` (everything that requires data exchange with external systems HTTP / REST / DB /...)

Each package forms a logical unit that encapsulates a domain concept and hide implementation details.

✅ **Recommended: Classes names are Nouns (things) with descriptive prefixes (RESULT oriented)**
- `CachedCar`, `StoredCar`, `ValidCar`
- `PayPalPayment`, `StripePayment`, `PayPal` (use HttpClient), `Stripe` (...Http)
- `customer/StoredCustomer`, `customer/ValidCustomer`
- `InMemoryCar`, `StoredCar`, `CachedCar`, `LoggedCar`, `ValidCar` (prefixes describe WHAT)
- `PublishedCar` (send Kafka messages/events), `ReceivedCar` (receive Kafka messages/events)
- `ICarRentalApp` interface in root, `CarRentalApp` in `application/`

Only what the business customer says - with result oriented prefixes.

❌ **Avoid: Technical package names — very Bad (it is a SHAME)**
- `service/`, `repository/`, `controller/`, `presentation/`, `persistence/`
- `common/`, `shared/`, `util/`, `helper/`
- `adapter/`, `client/`, `wrapper/`, `facade/`, `usecases/`, `interactors/`

Avoid technical package names for grouping by architecture patterns.

❌ **Avoid: Classes names - Verbs or technical suffixes — very Bad (it is a SHAME)**
- `CarProcessor`, `CarManager`, `CarClient`
- `PaymentRepository`, `PaymentService` (architecture pattern)
- `CustomerHandler`, `CustomerValidator`
- `CarService`, `CarManager`, `CarHandler` (verbs/technical suffixes of architecture pattern)
- `CarRepository`, `CarValidator` (technical roles)
- `CarDTO`, `CarModel` (technical classification of architecture pattern)
- `MailHelper`, `CarNumberUtil` (technical waste)
- `CarConsumer`, `CarProducer` (use Received/Published prefix instead)

Avoid meaning of technical things and suffixes of architecture patterns.

---
## 5. Step by Step - Implementation

### 5.1 Domain Interfaces in Root Package

The **most important concepts and ideas** should be at the beginning - **in the top-level package** of the software. 
This ensures *conceptual integrity*, preserving the *abstract identity* of the system before technical details distort it.

```
carrental/
├── .../
├── ICar.cs          ← Never knows about technical details
├── ICarRental.cs
├── ....cs
```


E.g. `ICar.cs` - Domain Interface (ROOT!)
```csharp

namespace CarRental;

public interface ICar
{
    void Rent(ICustomer customer, DateTime from, DateTime to);
    void Return();
    bool IsAvailable();
    decimal CalculatePrice(DateTime from, DateTime to);
}
```

E.g. `ICarPool.cs` - Collection Domain Interface (ROOT!)

```csharp
namespace CarRental;

public interface ICarPool
{
    ICar CarOf(CarId carId);

    // other methods ...
}
```

### 5.2 Composition Root Pattern

By placing the initial system class (as an interface/abstract class) at level "0" of the project structure, we clearly indicate the beginning of the story to the reader.

```
carrental/
├── .../
├── ICar.cs          
├── ICarRental.cs
├── ICarRentalApp.cs  ← Composition Root
```

The class `ICarRentalApp.cs` enables access via the collection classes e.g. `ICarPool.cs` to single **domain interfaces** like `ICar.cs`. 

```csharp
// ICarRentalApp.cs - Composition Root Interface (ROOT!)
namespace CarRental;

public interface ICarRentalApp
{
    ICarPool CarPool();
    ICustomers Customers();
}
```

---

### 5.3 Detail Implementations with Decorators - `carpool/`

Business logic as code – the radical idea behind the Decorator pattern.  
This means that your code structure should precisely reflect your business process.

E.g. in a car rental business, when you rent a car, you:
1. Validate the rental request
2. Persist it to storage
3. Cache it for performance
4. Log it for audit
5. Publish events for other systems

Example of default implementation of `carpool/SimpleCar.cs` - instead of Factory building block from DDD.

```csharp

namespace CarRental.CarPool;

public class SimpleCar : ICar
{
    private readonly string _id;
    private readonly string _model;
    private readonly decimal _dailyRate;
    private bool _isRented;

    public SimpleCar(string id, string model, decimal dailyRate)
    {
        _id = id;
        _model = model;
        _dailyRate = dailyRate;
    }

    public void Rent(ICustomer customer, DateTime from, DateTime to) => _isRented = true;
    public void Return() => _isRented = false;
    public bool IsAvailable() => !_isRented;
    public decimal CalculatePrice(DateTime from, DateTime to) => (to - from).Days * _dailyRate;
}

```

Example of `carpool/StoredCar.cs` as Database Decorator using EF-Core classes from `exchange/storage/` package.

```csharp
using CarRental.Exchange.Storage;

namespace CarRental.CarPool;

public class StoredCar : ICar
{
    private readonly ICar _origin;
    private readonly CarDbContext _dbContext;
    private readonly string _carId;

    public StoredCar(ICar origin, CarDbContext dbContext, string carId)
    {
        _origin = origin;
        _dbContext = dbContext;
        _carId = carId;
    }

    public void Rent(ICustomer customer, DateTime from, DateTime to)
    {
        _origin.Rent(customer, from, to);
        var entity = _dbContext.Cars.Find(_carId);
        if (entity != null)
        {
            entity.IsRented = true;
            entity.CurrentCustomerId = customer.Id();
            _dbContext.SaveChanges();
        }
    }

    public void Return()
    {
        _origin.Return();
        var entity = _dbContext.Cars.Find(_carId);
        if (entity != null)
        {
            entity.IsRented = false;
            _dbContext.SaveChanges();
        }
    }

    public bool IsAvailable() => _origin.IsAvailable();
    public decimal CalculatePrice(DateTime from, DateTime to) => _origin.CalculatePrice(from, to);
}

```

```
...more other decorators: ValidCar, CachedCar, LoggedCar, PublishedCar...
```

**Decorator Composition:**

```
  → ValidCar (Validation)
    → StoredCar (Persistence)
      → CachedCar (Caching)
        → LoggedCar (Logging)
          → PublishedCar (Events)
```

**The Core Message: Decorators Are Your Business Logic**

Here's the paradigm shift:

* **Traditional thinking:** "Decorators are a technical pattern for adding functionality"
* **Business-driven thinking:** "Decorators ARE the business process, made executable"

Let's us look at this decorator chain again:

```
Validation → Events → Logging → Caching → Persistence
    ↓          ↓        ↓         ↓          ↓
 Business   Domain    Audit   Performance   Data
  Rules     Events    Trail   Optimization  Storage
```
This isn't technical plumbing. This is our business.

* When Product Owner says "we need to validate rental dates," you add **ValidCar**. 
* When Compliance says "we need audit trails," you add **LoggedCar**. 
* When Operations says "we need event-driven architecture," you add **PublishedCar**.

Each business requirement = one decorator. *Clear*. *Traceable*. *Maintainable*.

---

### 5.4 Implementation of Composition Root in `application/`

The Composition Root is an application infrastructure component.
> Only applications should have Composition Roots. Libraries and frameworks shouldn't.
> The Composition Root can be implemented with DI Pure DI, but is also the (only) appropriate place to use a DI Container.
> A DI Container should only be referenced from the Composition Root. All other modules should have no reference to the container.

```
carrental/
├── application/
│   └── CarRentalApp.cs    ← application's entry point.
├── ICar.cs          
├── ICarRental.cs
├── ICarRentalApp.cs       ← Composition Root
```

The package `application/` can contain various implementations of the application's entry point.

```csharp
using CarRental.CarPool;
using CarRental.Exchange.Storage;
using Microsoft.Extensions.Caching.Memory;
using Microsoft.Extensions.DependencyInjection;

namespace CarRental.Application;

public class CarRentalApp : ICarRentalApp
{
    private readonly IServiceProvider _services;

    // Framework Integration
    public CarRentalApp(IServiceProvider services) => _services = services;

    public static void Main(string[] args) 
    {
        // ASP.NET Core Setup & DI Configuration
    }
    
    public ICarPool CarPool() 
    {
        // Implementation of ICarPool using collection decorators
        // Composition logic: CachedCarPool(StoredCarPool(InMemoryCars))
        return new CachedCarPool(
            new StoredCarPool(
                _services.GetRequiredService<CarDbContext>()
            ),
            _services.GetRequiredService<IMemoryCache>()
        );
    }

    public ICustomers Customers() 
    {   // Composition logic: CachedCustomers(StoredCustomers(_services.GetRequiredService<CustomerDbContext>()))
        // Returns the collection implementation for customers
        return ...;
    }
}
```

---

### 5.5 Isolation of Frameworks and Libraries 

Anti-Corruption Layer

**Option 1) Ideally**, technical aspects implemented through frameworks or libraries should be outsourced to **separate projects** and integrated into the main project as dependencies.

```
carrental                     → depends on: -endpoint, -resource, -storage, -... 

carrental-endpoint            → HTTP classes JSON/XML DTOs
carrental-resource            → REST classes JSON/XML DTOs
carrental-storage             ← EF Core ...DbContexts/Entities
carrental-paypal              ← Paypal REST client JSON/XML DTOs
carrental-mailing             ← EMAIL sending by SMTPS/IMAPS/POP3S 
carrental-messaging           ← QUEUES integration like Kafka with AVRO classes
carrental-pdf                 ← PDF library with DTOs/helper classes
carrental-text                ← Textformatting library with DTOs/helper classes
carrental-...                 ← other framework or library
```

The classes in these technical projects can then be used in the business packages of **carrental** project - starting at the first level.

For example, if you are using ORMs like EF Core, isolate them in a **separate project** `storage` and then use EF classes in the package `carpool/` behind a class like `StoredCar.cs`, which is designed as a `Decorator`, `Bridge` or `Adapter` pattern.

**Important:** The domain interfaces and classes in the root package of **carrental** project should never use classes technical of projects.

**Option 2) Alternative:** suitable for projects with small codebases.
3.3 The Exchange Package: 
Isolate all technical aspects (everything that requires data exchange with external systems)

* into a dedicated package `exchange/`

followed by further subpackages for each aspect, such as:

* `endpoint/`   → HTTP classes for WEB access with helper classes
* `resource/`   → HTTP classes for REST and JSON/XML DTOs with helper classes
* `storage/`    → ORM classes for DB access with helper classes
* `paypal/`     → Paypal REST library
* `mailing/`    → SMTPS/IMAPS/POP3S for EMAIL sending and server integration with helper classes
* `messaging/`  → AVRO classes for Kafka integration with helper classes
* `text/`       → Textformatting library with helper classes
* `pdf/`        → PDF library with helper classes
* `other.../`   → ... library/classes ...

The classes in these packages can then be used in the business packages starting at the first level.  
E. g. when using ORMs like EF Core, isolate them in the `exchange/storage/` package.

```
carrental/
├── application/ 
├── carpool/            
│   └── StoredCar.cs         ← Uses exchange/storage/ for persistence
├── exchange/
│   ├── endpoint/            → HTTP classes JSON/XML DTOs
│   ├── resource/            → REST classes JSON/XML DTOs
│   ├── storage/             ← EF Core Entity       
│   │   ├── CarEntity.cs     ← EF Core Entity
│   │   ├── CarDbContext.cs  ← EF Core DbContext
│   │   ├── Db....cs         ← EF Core common Utils or Helper only for this package
│   │   └── ...
│   ├── paypal/              → Paypal: REST library
│   ├── mailing/             → Email SMTPS, IMAPS or POP3S Protocol   
│   ├── messaging/           → Queues Apache Avro or Protocol Buffers DTOs
│   └── ...  
├── .../    
└── ICar.cs                  ← Never knows about EF Core
```

**Important:**
The domain interfaces and classes in the root package should never depends on technical DTO classes.
* All ORM classes (Entity, DbContext) live in `exchange/storage/` package
* The package `exchange/storage/` can be used by `carpool/`, `payment/` not otherwise
* Domain adapters (like `StoredCar` in `carpool/`) depends on DTOs of `exchange/storage/` package

This ensures framework independence and clean dependency flow.

**E.g. Integration of ORM Layer** -  `exchange/storage/ ` 

```csharp
// exchange/storage/CarEntity.cs
namespace CarRental.Exchange.Storage;

public class CarEntity
{
    public string Id { get; set; } = string.Empty;
    public string Model { get; set; } = string.Empty;
    public decimal DailyRate { get; set; }
    public bool IsRented { get; set; }
    public string? CurrentCustomerId { get; set; }
    public DateTime? RentedFrom { get; set; }
    public DateTime? RentedTo { get; set; }
    public DateTime CreatedAt { get; set; }
}
```

```csharp
// exchange/storage/CarDbContext.cs
using Microsoft.EntityFrameworkCore;

namespace CarRental.Exchange.Storage;

public class CarDbContext : DbContext
{
    public CarDbContext(DbContextOptions<CarDbContext> options) 
        : base(options)
    {
    }

    public DbSet<CarEntity> Cars { get; set; } = null!;

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<CarEntity>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Model).IsRequired().HasMaxLength(200);
            entity.Property(e => e.DailyRate).HasPrecision(18, 2);
        });
    }
}
```


---





---

## 6. Architectural Evolution Path

A structure should evolve with the business and architectual needs. This section outlines a proven three-phase approach.

### 6.1 Phase 1: Monolith

**Start Here:** Single deployable artifact with all bounded contexts in one package.

```
carrental/          ← Single assembly
├── application/
├── carpool/
├── customer/
├── exchange/
├── payment/
└── booking/
```

**When to use:**
•	Starting a new project
•	Team size < 10 developers
•	Business boundaries are still evolving
•	Simple deployment requirements

---
### 6.2 Modulith Artifacts (Phase 2)

A modular structurce of code is NOT an obvious next step, but a conscious decision to combat increasing entropy. It makes sense when:

* **The team is growing:** With around 4-5 developers, natural areas of responsibility begin to emerge. Modules allow these boundaries to be defined in the code, so developers are less likely to "poach" on each other's code.
* **Cognitive overload:** If a developer has to understand half the system to make a small change to the payment system, encapsulation has failed. The modular architecture restores the mental map.
* **Exploding test times:** If the entire test suite runs for every minor change and takes more than 5-10 minutes, modularization helps create test slices that can be validated independently.
* **Preparing for microservices:** A modular architecture is the best insurance against the "distributed monolith." Only when the functional interfaces within the modular architecture are stable is the physical transition to microservices safe.



#### 6.2.2. Revised Structure & Strategic Decoupling

This step begins with a single monolithic artifact, which is successively decomposed into autonomous modules. 
The functional boundaries of Bounded Contexts serve as the primary guideline for this modularization.

```
carrental-service              ← Deployable Unit
├── [carrental]                ← RECOMMENDED: To be eliminated (Shared Kernel)
├── carrental-application      ← ASP.NET Core Entry Point & DI Configuration (Composition Root)
├── carrental-carpool          ← Bounded Context: Fleet Management
├── carrental-customer         ← Bounded Context: CRM / Identity
├── carrental-payment          ← Bounded Context: Billing & Transactions
└── carrental-booking-client   ← Frontend / API Gateway Logic
```

**Strategic Note on Decoupling**

**"Better duplication than the wrong abstraction."** - Sandi Metz (The Wall of Coding Wisdom)

Ideally, the shared module `carrental` should be completely eliminated. This is achieved by duplicating necessary *Value Objects*, such as `CarId`, `CustomerId`, and `PaymentId`, directly within their respective contexts. 

**Why avoid the Shared Kernel or Common module?**
* **Autonomy:** Each *bounded context* remains truly independent and can evolve its data structures without side effects on others.
* **Preventing Bloat:** It prevents a `common module` from becoming an uncontrolled *"dumping ground"* for unrelated logic.
* **Semantic Precision:** A `CustomerId` in `Payment` might require different validation rules than in `Customer` Support.

#### FLAT Project Structure (WITHOUT Shared Kernel)

```
carrental-carpool             ← project of customer bounded context 
├── carpool/                  → depends on: carpool-endpoint, *-resource, *-storage, *-mailing
│   ├── CachedCarPool.cs      ← Cache Decorator
│   ├── LoggedCar.cs          ← Logging Decorator
├   ...
├── CustomerId.cs             ← Value Object
├── PaymentId.cs              ← Value Object
├── ICar.cs                   ← Domain Interface
├── ICarPool.cs               ← Collection Interface
...
carrental-carpool-endpoint    ← Http Clients with JSON DTOs
carrental-carpool-resource    ← REST Services with JSON DTOs
carrental-carpool-storage     ← ORM Entity DTOs with Repositories
carrental-carpool-messaging   ← AVRO Schema generation of DTOs
```

```
carrental-customer             ← project of customer bounded context 
├── customer/                  → depends on: customer-endpoint, *-resource, *-storage, -*-mailing
│   ├── StoredCustomer.cs      ← Database Decorator (use EF-Core and -Entities from ...-*-storage project)
│   ├── StoredCustomers.cs     ← Database Decorator (use EF-Core and -Entities from ...-*-storage project)
│   ├── NotifiedCustomer.cs    ← Email Decorator (use SmtpsEmail from ...-customer-mailing project)
│   └── ...cs
├── CarId.cs                   ← Value Object
├── PaymentId.cs               ← Value Object
├── ICustomer.cs               ← Domain Interface
├── ICustomers.cs              ← Collection Interface
...

carrental-customer-endpoint    ← Http Clients with JSON DTOs
carrental-customer-resource    ← REST Services with JSON DTOs
carrental-customer-storage     ← ORM Entity DTOs with Repositories
carrental-customer-mailing     ← Email: SMTPS, IMAPS or POP3S Protocol
```

```
carrental-payment
├── paypal/                    
│   └── ...cs                  ← Payment Decorator (uses Paypal endpoint)
├── Payment.cs                 ← Domain Interface
...

carrental-payment-paypal       ← endpoint of Paypal: REST library
```

```
carrental-booking
...

```

```
carrental                     ← deployable module-composition of all projects, main setup & DI
├── application/              → depends on: carrental-carpool, *-customer, *-payment, *-booking  
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs   ← Kafka Configuration
...                              
```


#### HIERARCHICAL Project Structure (WITHOUT Shared Kernel)

If the number of projects becomes too unwieldy, the technical aspects can be encapsulated within the context modules, instead of creating a separate top-level module for each aspect:

```
carrental                     ← Root Module-Composition of all Projects
├── application/              → depends on: carrental-carpool, *-customer, *-payment, *-booking  
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs   ← Kafka Configuration
...

carrental-carpool             → Module-Group - Parent Project
├── carpool                   → Bounded Context Module
├── carpool-endpoint
├── carpool-resource
├── carpool-storage
└── carpool-messaging

carrental-customer            → Module-Group - Parent Project
├── customer                  → Bounded Context Module
├── customer-endpoint
├── customer-resource
├── customer-storage
└── customer-mailing

carrental-payment
├── payment                    
└── payment-paypal


carrental-booking
├── user                 
└── ...

```
---

### Phase 3: Microservices

Microservices are NOT an automatic next step. They bring significant complexity. Only consider microservices if:

**Organizational triggers:**
* Multiple autonomous teams
* Teams require independent deployment cycles
* Different technology stacks are needed
* Clear ownership boundaries are established

**Technical triggers:**
* Different scaling requirements (Payment needs 5 times more instances than Customer)
* Individual contexts cause system-wide outages
* Deployment process takes > minutes
* Modules are already cleanly separated and stable
  
**Business triggers:**
* Compliance requirements (e.g., isolating payment data)
* Multi-tenancy with context-specific isolation
* Different SLAs for different contexts

**Warning:** If you have fewer than 15 developers or your modules are not yet stable, stick with the modular monolith!

The following division is the logical consequence of Business Context-Driven structure. It separates  Bounded Contexts along with their technical infrastructure dependencies to individual microservices. Each service is structured like the **Mono or Modulith** artifact (in Phase 1 or 2).

```
carrental-gateway-service   ← artifact (build as deployable .dll)

carrental-carpool-service   ← artifact (build as deployable  .dll)
carrental-customer-service  ← artifact (build as deployable .dll)
carrental-payment-service   ← artifact (build as deployable .dll)

carrental-booking-service   ← artifact frontend (build as deployable  .dll)
```
**Note:** Each service maintains the SAME internal structure as the monolith/modulith. Only deployment boundaries change.

### Frontend as a Standalone Microservice (BFF Pattern)

In modern cloud-native architectures, it is often beneficial to treat the frontend not merely as a static asset, a collection of files (such as .js, .css, .html), but as a standalone "Backend-for-Frontend" (BFF) service. The BFF functions as a customized user interface precisely tailored to the needs of a specific client (e.g., web, mobile, or smart device). This enables a strict separation of presentation logic and business interfaces.

```
carrental-booking-client             → Frontend Project / BFF Service Project 
├── src/
│   ├── application/                  → startup: composition root pure JS / Node.js
│   │   ├── NativeCarrentalApp.js     → decorator pure JS: main entry point
│   │   ├── ServedCarrentalApp.js     → decorator Node.js: main entry point
│   │   ...
│   ├── exchange/                     → access of external resources
│   │   ├── database/                 → database / schema generation
│   │   │   ├── UserDb
│   │   │   ...
│   │   ├── endpoint/                 → HTTP clients for business services
│   │   │   ├── CarPoolApi.js
│   │   │   ├── CustomerApi.js
│   │   │   ├── PaymentApi.js
│   │   ...
│   ├── control/                 → custom UI elements / external plugins (jquery ui)
│   │   ├── InputGroup.js        → composite container
│   │   ├── TextInput.js         → input field
│   │   ├── TextLabel.js         → output field
│   │   ├── Form.js              → abstract Form extends Control  
│   │   ├── Page.js              → abstract Page extends Control  
│   │   ├── List.js              → abstract List extends Control 
│   │   ├── Table.js             → abstract Table extends Control  
│   │   ├── Menu.js              → abstract Menu extends Control  
│   │   ├── Navigation.js        → abstract Rooting extends Control
│   │   ...
│   ├── layout/                  → CSS styles / pictures / layouts 
│   │   ├── icon/                → icons of application
│   │   ├── image/               → images of application
│   │   │   ├── background.png
│   │   │   ...
│   │   ├── layout.css           → CSS common rules for layouts
│   │   ├── Layout.js            → layout as helper class
│   │ ...
│   ├── page/                    → HTML sites or JS page components
│   │   ├── admin-form.js
│   │   ├── car-details.js    
│   │   ├── carpool-list.js  
│   │   ├── payment-form.js  
│   │   ├── user-profile.js
│   │   ├── main-form.js  
│   │   ├── main-menu.js  
│   │  ...
│   ├── customer/                → implementation of domain logic of Person
│   │   ├── ValidAddress.js      → decorator for validation
│   │   ├── ValidCustomer.js     → decorator for validation
│   │   ...                      → other decorators (Logged*, Cashed*)
│   ├── Address.js               → abstract domain class/interface
│   ├── Control.js               → abstract UI component class (composite-pattern)
│   ├── Car.js                   → abstract domain class/interface
│   ├── CarPool.js               → abstract domain class/interface
│   ├── Customer.js              → abstract domain class/interface
│   ├── CarrentalApp.js          → abstract main class for composition root
│   ├── UserProfile.js           → domain class extends Person
│   ...
├── test/                        → Unit and integration tests
├── .gitignore                   → Environment variables
├── .environment
├── readme.md
├── package.json
│   ...
```


### 7. Conclusion: Screaming Architecture

There are **3 golden Rules** - to achieves the next level of readability:
 
1. Packages should never depend on sub-packages. 
2. Sub-packages should not introduce new concepts, just more details. 
3. Packages and classes should reflect business-concepts, not technical ones.

so that the code tells a story.

**Screaming Architecture:** —> means the structure screams the business domain at you. No translation layer. No mental mapping. Just direct, obvious correspondence between business concepts and code structure.


---
## 8. References and Further Reading

* Java Dev Guy, [Happy-Packaging (2017)](https://javadevguy.wordpress.com/2017/12/18/happy-packaging/)
* Philipp Hauer, [Package by Feature (2020)](https://phauer.com/2020/package-by-feature/)
* Mark Seemann, [Composition Root Pattern (2011)](https://blog.ploeh.dk/2011/07/28/CompositionRoot/)
* Mark Seemann, [Poor Man's DI Pattern (2012)](https://blog.ploeh.dk/2012/11/06/WhentouseaDIContainer/)
* Yegor Bugayenko, [Vertical and Horizontal Decorating](https://www.yegor256.com/2015/10/01/vertical-horizontal-decorating.html)
* Mihai A. RODEGBFR, [My Take On Object Naming (2018)](https://amihaiemil.com/2018/01/07/my-take-on-object-naming.html)
* Yegor Bugayenko, [Seven Virtues of a Good Object](https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html)
* Yegor Bugayenko, [Prefixed Naming](https://www.yegor256.com/2020/03/03/prefixed-naming.html)
* Yegor Bugayenko, [Don't Create Objects That End With -ER](https://www.yegor256.com/2015/03/09/objects-end-with-er.html)
* Yegor Bugayenko, [A Compound Name Is a Code Smell](https://www.yegor256.com/2015/01/12/compound-name-is-code-smell.html)
* Yegor Bugayenko, [Evil Suffix For Object Names](https://www.yegor256.com/2017/09/12/evil-object-name-suffix-client.html)



