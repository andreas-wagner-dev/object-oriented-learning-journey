# Business Context-Driven Code Structure 

> *The Missing Link Between Business and Code*


## 1. **The Problem:** The Translation Gap

*Why your code structure should be your Business-Context Diagram?*

**Imagine:** 

A Business Analyst shows you a System Context Diagram of a **Car Rental System**, integrated with `Payment` providers (`PayPal`/`Stripe`), a `Customer` database, and `User` interfaces.

```
                      User
                        │
                        ▼
 ┌─────────┐    ┌─────────────────┐    ┌─────────────┐
 │ Payment │    │                 │    │  Customer   │
 │ (PayPal)│────│   Car Rental    │────│ (Database)  │
 │         │    │                 │    │             │
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

* **Where is Car?**
* **Where is Customer?**
* **Where is Payment?**
* **Where is User from the Context Diagram?**

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
├── carpool/
├── customer/
├── payment/
└── user/
```

**This is a crucial strategic aspect of software architecture:** By directly mapping business contexts in code, we consistently apply the *Ubiquitous Language* at every level of system organization.

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

## 3. Business Context-Driven Project Structure - Car-Rental

The packages of an object-oriented system are based on clear OO-Design principles. There are **no layers** in the traditional sense of Clean Architecture or DDD. Instead, **packages are hierarchically organized** according to domain concepts.

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
├── exchange/
│   ├── endpoint/                → HTTP client classes JSON/XML DTOs
│   │   ├── Paypal.cs            ← API client
│   │   ├── PaypalRequest.cs     ← Request DTO
│   │   └── PaypalResponse.cs    ← Response DTO
│   ├── resource/                → JSON/XML DTOs for REST service classes
│   │   ├── CarResoure.cs        ← Resoure DTO
│   │   └── CarResoures.cs       ← Resoure DTO
│   ├── storage/                 ← EF Core           
│   │   ├── CarEntity.cs         ← EF Core DTO
│   │   ├── CarDbContext.cs      ← EF Core DAO
│   │   └── ...cs
│   ├── mailing/                 → Email: SMTPS, IMAPS or POP3S Protocol  
│   │    └── SmtpsEmail.cs       ← AVRO DTOs
│   ├── messaging/               ← Queues like Kafka
│   │    └── CarRentedEvent.cs   ← AVRO DTOs
│   │   
│   └── .../            
├── user/                        ← user contepts and server side UI rendering
│   ├── control/                 ← UI common controls (used in page/)
│   ├── layout/                  ← UI Layout/Style components (used in page/)
│   ├── page/                    ← UI pages of the application
│   ├── Control.cs               ← abstract Control
│   ├── Layout.cs                ← abstract Layout (Helper/Util)
│   ├── Page.cs                  ← abstract Page extends Control
│   ├── StoredUser.cs            ← Db Decorator
│   └── WebUser.cs               ← Web/Session Decorator
├── CarNumber.cs                 ← Shared/Util/Helper for all packages
├── ICar.cs                      ← Domain Interface
├── ICarPool.cs                  ← Collection Interface
├── ICustomer.cs
├── ICustomers.cs
├── IUser.cs                     ← Domain Interface
├── IUsers.cs                    ← Collection Interface
├── ICarRentalApp.cs             ← Composition Root Interface
└── ...
```

* When business says *“there’s a problem with car rentals”*, you go to **carpool/**
* When they say *“payment processing is slow”*, you go to **payment/**

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

✅ **Recommended: Classes names are Nouns (things) with descriptive prefixes (RESULT oriented)**
- `CachedCar`, `StoredCar`, `ValidCar`
- `PayPalPayment`, `StripePayment`, `PayPal` (use HttpClient), `Stripe` (...Http)
- `customer/StoredCustomer`, `customer/ValidCustomer`
- `InMemoryCar`, `StoredCar`, `CachedCar`, `LoggedCar`, `ValidCar` (prefixes describe WHAT)
- `PublishedCar` (send Kafka messages/events), `ReceivedCar` (receive Kafka messages/events)
- `ICarRentalApp` interface in root, `CarRentalApp` in `application/`

Only what the business customer says - with result oriented prefixes.

✅ **Recommended: Package names from Context Diagram**
- `payment/`, `inventory/`, `shipping/` (business concepts or external systems)
- `user/` (GUI interfaces or REST interfaces for GUI e.g. React)
- `exchange/` (everything that requires data exchange with external systems HTTP / REST / DB /...)

Each package forms a logical unit that encapsulates a domain concept and hide implementation details.

❌ **Avoid: Classes names - Verbs or technical suffixes — very Bad (it is a SHAME)**
- `carpool/CarProcessor`, `carpool/CarManager`, `carpool/CarClient`
- `payment/PaymentRepository`, `payment/PaymentService` (architecture pattern)
- `customer/CustomerHandler`, `customer/CustomerValidator`
- `CarService`, `CarManager`, `CarHandler` (verbs/technical suffixes of architecture pattern)
- `CarRepository`, `CarValidator` (technical roles)
- `CarDTO`, `CarModel` (technical classification of architecture pattern)
- `MailHelper`, `CarNumberUtil` (technical waste)
- `CarConsumer`, `CarProducer` (use Received/Published prefix instead)

Avoid meaning of technical things and suffixes of architecture patterns.

❌ **Avoid: Technical package names — very Bad (it is a SHAME)**
- `service/`, `repository/`, `controller/`, `presentation/`, `persistence/`
- `common/`, `shared/`, `util/`, `helper/`
- `adapter/`, `client/`, `wrapper/`, `facade/`, `usecases/`, `interactors/`

Avoid technical package names for grouping by architecture patterns.

---
## 5. Step by Step - Implementation

### Domain Interfaces in Root Package

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

### Composition Root Pattern

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

### Detail Implementations with Decorators - `carpool/`

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

### Implementation of Composition Root in `application/`

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

### Isolation of Frameworks and Libraries 

**1) Ideally**, technical aspects implemented through frameworks or libraries should be outsourced to **separate projects** and integrated into the main project as dependencies.

```
carrental                     → depends on: -endpoint, -resource, -storage, -... 

carrental-endpoint            → HTTP classes JSON/XML DTOs
carrental-resource            → REST classes JSON/XML DTOs
carrental-storage             ← EF Core ...DbContexts/Entities
carrental-mailing             ← EMAIL sending by SMTPS/IMAPS/POP3S 
carrental-messaging           ← QUEUES integration like Kafka with AVRO classes
carrental-pdf                 ← PDF library with DTOs/helper classes
carrental-text                ← Textformatting library with DTOs/helper classes
carrental-...                 ← other framework or library
```

The classes in these technical projects can then be used in the business packages of **carrental** project - starting at the first level.

For example, if you are using ORMs like EF Core, isolate them in a **separate project** `storage` and then use EF classes in the package `carpool/` behind a class like `StoredCar.cs`, which is designed as a `Decorator`, `Bridge` or `Adapter` pattern.

**Important:** The domain interfaces and classes in the root package of **carrental** project should never use classes technical of projects.

**2) Alternative:** suitable for projects with small codebases.

Isolate all technical aspects (everything that requires data exchange with external systems)

* into a dedicated package `exchange/`

followed by further subpackages for each aspect, such as:

* `endpoint/`   → HTTP classes for WEB access with helper classes
* `resource/`   → HTTP classes for REST and JSON/XML DTOs with helper classes
* `storage/`    → ORM classes for DB access with helper classes
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
│   ├── mailing/             → Email: SMTPS, IMAPS or POP3S Protocol   
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

## 6. Evolution Path

### 6.1 Mono Artifact (Phase 1)
```
carrental/
├── application/
├── carpool/
├── customer/
├── payment/
└── user/
```

---
### 6.2 Modulith Artifacts (Phase 2)

Modular architectures are NOT an obvious next step, but a conscious decision to combat increasing entropy. It makes sense when:

* **The team is growing:** With around 4-5 developers, natural areas of responsibility begin to emerge. Modules allow these boundaries to be defined in the code, so developers are less likely to "poach" on each other's code.
* **Cognitive overload:** If a developer has to understand half the system to make a small change to the payment system, encapsulation has failed. The modular architecture restores the mental map.
* **Exploding test times:** If the entire test suite runs for every minor change and takes more than 5-10 minutes, modularization helps create test slices that can be validated independently.
* **Preparing for microservices:** A modular architecture is the best insurance against the "distributed monolith." Only when the functional interfaces within the modular architecture are stable is the physical transition to microservices safe.

#### Revised Structure & Strategic Decoupling

This step begins with a single monolithic artifact, which is successively decomposed into autonomous modules. The functional boundaries of Bounded Contexts serve as the primary guideline for this modularization.

```
carrental-service              ← Deployable Unit
├── [carrental]                ← RECOMMENDED: To be eliminated (Shared Kernel)
├── carrental-application      ← ASP.NET Core Entry Point & DI Configuration (Composition Root)
├── carrental-carpool          ← Bounded Context: Fleet Management
├── carrental-customer         ← Bounded Context: CRM / Identity
├── carrental-payment          ← Bounded Context: Billing & Transactions
└── carrental-user-client      ← Frontend / API Gateway Logic
```

**Strategic Note on Decoupling**

**"Better duplication than the wrong abstraction."** - Sandi Metz (The Wall of Coding Wisdom)

Ideally, the shared module `carrental` should be completely eliminated. This is achieved by duplicating necessary *Value Objects*, such as `CarId`, `CustomerId`, and `PaymentId`, directly within their respective contexts. 

**Why avoid the Shared Kernel or common module?**
* **Autonomy:** Each *bounded context* remains truly independent and can evolve its data structures without side effects on others.
* **Preventing Bloat:** It prevents a `common module` from becoming an uncontrolled *"dumping ground"* for unrelated logic.
* **Semantic Precision:** A `CustomerId` in `Payment` might require different validation rules than in `Customer` Support.

#### FLAT Project Structure (WITHOUT Shared Kernel)

```
carrental                     ← deployable module-composition of all projects, main setup & DI
├── application/              → depends on: core carrental and carrental-carpool, carrental-customer 
│   ├── CarRentalApp.cs       ← ASP.NET Core Main + DI
│   └── KafkaQueueConfig.cs   ← Kafka Configuration
...                              

carrental-carpool             ← Business (Bounded) Context
├── carpool/                  → depends on: core carrental and -endpoint, -resource, -storage, -...
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


carrental-customer             ← Business (Bounded) Context 
├── customer/                  → depends on: core carrental and -endpoint, -resource, -storage, -...
│   ├── StoredCustomer.cs      ← Database Decorator (use EF-Core and -Entities from ...-customer-storage project)
│   ├── StoredCustomers.cs     ← Database Decorator (use EF-Core and -Entities from ...-customer-storage project)
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


carrental-payment
...


carrental-user-client
...

```

#### HIERARCHICAL Project Structure (WITHOUT Shared Kernel)

If the number of projects becomes too unwieldy, the technical aspects can be encapsulated within the context modules, instead of creating a separate top-level module for each aspect:

```
carrental                     ← Root Module-Composition of all Projects
├── application/              → depends on: core carrental and carrental-carpool, carrental-customer 
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

....

```

---

### 6.3 Microservices (Phase 3)

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

Split at package/module boundaries.
Each service is structured like the **Mono or Modulith** artifact (in Phase 1 or 2)
```
carrental-gateway-service   ← artifact (build as deployable .dll)
carrental-client-service    ← artifact frontend (build as deployable  .dll)
carrental-carpool-service   ← artifact (build as deployable  .dll)
carrental-customer-service  ← artifact (build as deployable .dll)
carrental-payment-service   ← artifact (build as deployable .dll)
```

Each phase maintains the same principles - only deployment boundaries change.

**Screaming Architecture:** —> means your package structure IS your business context diagram. 

No *mental* translation or mapping needed.

---
## 7. References

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
* Wikipedia, [Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)

